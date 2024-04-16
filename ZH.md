# bayes2024hazi

1.1 Írj következtetésre olyan ComplexModel1-et, ami ilyen premisszákat generál véletlenszerűen: Panni "könyvtáros/tanár" "és/vagy" "csendes/cserfes" és ebből logikailag helyesen következtetne olyan konklúzióra, hogy "Panni csendes/cserfes", DE! sajnos diszlexiás az ágens és a "csendes/cserfes"-t valamilyen valószínűséggel felcseréli.


    var feature1 = ['könyvtáros','tanár'];
    var feature2 = ['csendes','cserfes'];
    var operator = [' és ',' vagy '];
    
    var dyslexiaProbability = 0.5; // annak valószínűsége, hogy a csendes és cserfes felcserélődik
    
    var ComplexModel1 = function() {
        var word1 = uniformDraw(feature1)
        var op = uniformDraw(operator)  
        var word2  = uniformDraw(feature2)
        print('Premissza: Panni ' + word1 + op + word2 + '.'); 
        var word3 = flip(dyslexiaProbability) 
                    ? (word2 == 'csendes' ? 'cserfes' : 'csendes') 
                    : word2;
        print('Konklúzió: Panni ' + word3 + '.'); 
        var ervenyes = (op == ' és ')
                       ? ((word2 == word3) ? 'érvényes' : 'nem érvényes') 
                       : ((word2 == word3 || word1 == word3) ? 'érvényes' : 'nem érvényes');
        print(ervenyes); 
        return ervenyes
    }
     
    var output = 
      Infer({model: ComplexModel1, method:'rejection', samples: 1})



1.2 Írj következtetésre olyan ComplexModel2-t, ami ilyen premisszákat tartalmaz: Panni "könyvtáros/tanár" "és/vagy" "csendes/cserfes" és ebből logikailag helyesen következtetne olyan konklúzióra, hogy "Panni csendes/cserfes" ill. "Panni könyvtáros/tanár", DE! az "és"-t 95%-ban a klasszikus logikának megfelelően használja, de elég gyakran, 80%-os valószínűséggel néha a vagy-ot és-nek olvassa. Vö.: "Jaj! valami ördög... vagy ha nem, hát... kis nyúl!".**

    var feature1 = ['könyvtáros', 'tanár'];
    var feature2 = ['csendes', 'cserfes'];
    var operator = [' és ', ' vagy '];
    
    var ComplexModel2 = function() {
        var word1 = uniformDraw(feature1);
        var word2 = uniformDraw(feature2);
        var conjunctionProbability = 0.95; // és klasszikus logika szerinti használata
        var logicalOperator = (flip(conjunctionProbability)) ? ' és ' : ' vagy ';    
        // annak valószínűsége, hogy a "vagy"-ot "és"-nek olvassa
        var occasionalOrProbability = 0.8;
        if (logicalOperator === ' és ' && flip(occasionalOrProbability)) {
            var logicalOperator = ' vagy ';
        }
        var premiss = 'Panni ' + word1 + logicalOperator + word2 ;
        var conclusion = 'Panni ' + word2 + '/' + word1;
        print('Premissza: ' + premiss + '.');
        print('Konklúzió: ' + conclusion + '.');
        var ervenyes = (logicalOperator === ' és ')
        ? ((word1 === word2) ? 'érvényes' : 'nem érvényes')
        : 'érvényes';
        print(ervenyes);
        return ervenyes;
    }
    
    var output =
        Infer({ model: ComplexModel2, method: 'rejection', samples: 1 });


1.3 Programozz be egy olyan modellt, ami kiszámolja, hogy mi annak a valószínűsége, hogy ha két kockával dobunk, akkor az összeg legalább 4 lesz!

    var totalOutcomes = 6 * 6; // összes
    var successfulOutcomes = 30; // kedvező 
    
    var probability = successfulOutcomes / totalOutcomes; // kedvező/összes
    
    console.log("Annak a valószínűsége, hogy ha két kockával dobunk, akkor az összeg legalább 4 lesz: " + probability);


1.4 (King-Ace Paradox, bemelegítő) Tudjuk, hogy a klasszikus logikában a "Ha ász van a kezemben, akkor király van, vagy ha nincs ász a kezemben, akkor király van a kezemben" mondatból nem következik feltétlenül, hogy király van a kezemben (miért?), sőt, ha a mondatban szereplő "vagy"-ot kizáró vagy értelemében használjuk, akkor kifejezetten az következik belőle, hogy nincs király a kezemben. Írj programot, ami a helyzetet modellezi úgy, hogy a mondatbeli "vagy" jelentése néha "és" néha "vagy" néha "kizáró vagy". Vö.: The Cambridge Handbook of Computational Psychology, ed.: Ron Sun, 2008, Cambrigde, p. 137.


    var mintaDVagy = function() {
      var veletlen = randomInteger(1, 3);
      if (veletlen === 1) {
        return 'es';
      } else if (veletlen === 2) {
        return 'vagy';
      } else {
        return 'kizaro_vagy';
      }
    };
    
    var kiralyAszParadoxon = function() {
      var vagyTipus = mintaDVagy();
      
      var vanAsz = flip(0.5);
      var vanKiraly = flip(0.5);
      
      if (vagyTipus === 'es') {
        return vanAsz && vanKiraly;
      } else if (vagyTipus === 'vagy') {
        return vanAsz || vanKiraly;
      } else {
        return vanAsz !== vanKiraly;
      }
    };
    
    var kiserlet = Infer({ method: 'enumerate' }, kiralyAszParadoxon);
    print(kiserlet);

3.1 Írj programot, amelyik kiszámolja, hogy mi annak a valószínűsége, hogy 52 lapos francia kártyából 2 kártyát választva az egyik király, a másik nem király!

    var osszeskartya = 52;
    var osszespar = osszeskartya * (osszeskartya - 1); // összes lehetséges pár
    
    var osszeskiraly = 4; // 4 király van egy pakliban
    var osszesnemkiraly = osszeskartya - osszeskiraly; // azon kártyák száma, ami nem király
    
    var kiralyesnemkiraly = osszeskiraly * osszesnemkiraly; // azok a párok, ahol az egyik lap király, a másik nem király
    
    var probability = kiralyesnemkiraly / osszespar; // annak valószínűsége, hogy 1 királyt, 1 nem királyt húzunk
    
    console.log("Annak a valószínűsége, hogy 52 lapos francia kártyából 2 kártyát választva az egyik király, a másik nem király: " + probability);

3.2 Legyen az X Y és Z valószínűség változó olyan, ami az {0, 1, 2, 3} számok közül választ egyenletes valószínűséggel. Legyen W = X + Y + Z, mi az X változó eloszlása, ha tudjuk, hogy W = 7? (Írj programot!)

    var values = [0, 1, 2, 3];
    
    var model = function() {
        var X = uniformDraw(values);
        var Y = uniformDraw(values);
        var Z = uniformDraw(values);
        var W = X + Y + Z;
      
        condition(W === 7);
      
        return X;
    };
    
    var dist = Infer({ model: model, method: 'enumerate' });
    print(JSON.stringify(dist, null, 2));

3.4 Van egy beépített emberünk a Monty Hall/vos Savant szituációban, aki 50% százalékban helyesen mutogatja el nekünk a színfalak mögül, hogy melyik ajtó mögött van az autó. Szót fogadunk neki, és azt az ajtót választjuk, amit ő javasol. Még mindig érdemes-e azután váltani, hogy Monty megmutatot egy kecskét? A választ támasszuk alá webppl programmal!

    var model = function() {
        var doors = [1, 2, 3];
        var car = uniformDraw(doors);
        var initialChoice = uniformDraw(doors);
      
        var insider = flip(0.5) ? car : uniformDraw(doors.filter(function(door) { return door !== car; }));
      
        var montyOpens = uniformDraw(doors.filter(function(door) { return door !== initialChoice && door !== insider; }));
      
        var switchChoice = doors.filter(function(door) { return door !== initialChoice && door !== montyOpens; })[0];
      
        return { "Switch Wins": switchChoice === car };
    };
    
    var dist = Infer({ model: model, method: 'enumerate' });
    print(JSON.stringify(dist, null, 2));
    
    
    // ebben az esetben így nem lesz érdemes váltani, 33,33% lesz az esélyünk megnyerni az autót


4.2 Hörcsögünk súlyának mérési adatai: 28 g, 31 g, 44 g, 29 g. Lexikonbeli adatok: átlagos súly: 32 g, szórás: 10 g. Mi lesz az adatokkal való frissítés után a hörcsög súlyának eloszlása?

    // A hörcsög súlyadatai
    var adatok = [28, 31, 44, 29];
    
    // Lexikon adatok: átlagos súly és szórás
    var lexikon_atlag = 32;
    var lexikon_szoras = 10;
    
    
    var modell = function() {
      // Előzetes tudás a hörcsög súlyáról, a lexikon alapján
      var atlag = gaussian(lexikon_atlag, lexikon_szoras);
      
      // megfigyelés után
      map(function(d) {
        observe(Gaussian({mu: atlag, sigma: lexikon_szoras}), d);
      }, adatok);
      
      // Prediktív poszterior eloszlás a hörcsög súlyáról
      var prediktiv = gaussian(atlag, lexikon_szoras);
      
      return {
        Elozetes: lexikon_atlag,
        Poszterior: atlag,
        PoszteriorPrediktiv: prediktiv
      };
    };
    
    
    var opcio = {method: 'SMC', particles: 2000, rejuvSteps: 5};
    
    
    var output = Infer(opcio, modell);
    
    // Az eloszlások ábrázolása
    viz.marginals(output);


