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
