# bayes2024hazi

**1.1 Írj következtetésre olyan ComplexModel1-et, ami ilyen premisszákat generál véletlenszerűen: Panni "könyvtáros/tanár" "és/vagy" "csendes/cserfes" és ebből logikailag helyesen következtetne olyan konklúzióra, hogy "Panni csendes/cserfes", DE! sajnos diszlexiás az ágens és a "csendes/cserfes"-t valamilyen valószínűséggel felcseréli.**

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




**1.2 Írj következtetésre olyan ComplexModel2-t, ami ilyen premisszákat tartalmaz: Panni "könyvtáros/tanár" "és/vagy" "csendes/cserfes" és ebből logikailag helyesen következtetne olyan konklúzióra, hogy "Panni csendes/cserfes" ill. "Panni könyvtáros/tanár", DE! az "és"-t 95%-ban a klasszikus logikának megfelelően használja, de elég gyakran, 80%-os valószínűséggel néha a vagy-ot és-nek olvassa. Vö.: "Jaj! valami ördög... vagy ha nem, hát... kis nyúl!".**

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


