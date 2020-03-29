---
title: 'Nabízeno: Akce a kontext - Personalizátář'
titleSuffix: Azure Cognitive Services
description: Personalizár používá funkce, informace o akcích a kontextu, aby lépe hodnocení návrhy. Funkce mohou být velmi obecné nebo specifické pro položku.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 408501232891a7971d03c89acc647d9ed19609b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77026145"
---
# <a name="features-are-information-about-actions-and-context"></a>Funkce jsou informace o akcích a kontextu.

Služba Personalizace funguje tak, že se dozví, co by měla vaše aplikace zobrazovat uživatelům v daném kontextu.

Personalizátor používá **funkce**, což jsou informace o **aktuálním kontextu,** aby zvolil nejlepší **akci**. Funkce představují všechny informace, o kterých si myslíte, že mohou pomoci přizpůsobit se dosažení vyšších odměn. Funkce mohou být velmi obecné nebo specifické pro položku. 

Můžete mít například **funkci** týkající se:

* _Uživatel persona,_ například `Sports_Shopper`. To by nemělo být individuální ID uživatele. 
* _Obsah,_ například pokud je `Documentary`video `Movie`, `TV Series`, nebo , nebo zda je maloobchodní položka k dispozici v obchodě.
* _Aktuální_ časové období, například který den v týdnu je.

Personalizár nepředepisuje, neomezuje ani neopravuje funkce, které můžete odesílat pro akce a kontext:

* Některé funkce můžete odeslat pro některé akce a ne pro jiné, pokud je nemáte. Televizní seriál může mít například atributy, které filmy nemají.
* Některé funkce můžete mít k dispozici pouze někdy. Mobilní aplikace může například poskytnout více informací než webová stránka. 
* V průběhu času můžete přidávat a odebírat funkce týkající se kontextu a akcí. Personalizátor se stále učí z dostupných informací.
* Musí existovat alespoň jeden prvek pro kontext. Personalizátor nepodporuje prázdný kontext. Pokud pokaždé odešlete pouze pevný kontext, personalizátor zvolí akci pro hodnocení pouze pokud jde o funkce v akcích.
* U kategorických prvků není nutné definovat možné hodnoty a není nutné předem definovat rozsahy pro číselné hodnoty.

## <a name="supported-feature-types"></a>Podporované typy funkcí

Personalizákátor podporuje funkce řetězcových, číselných a logických typů.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Vliv volby typu funkce na strojové učení v personalizátoru

* **Řetězce**: Pro typy řetězců každá kombinace klíče a hodnoty vytvoří nové váhy v modelu personalizovaného strojového učení. 
* **Číselné**: Číselné hodnoty byste měli použít, pokud by číslo mělo úměrně ovlivnit výsledek individuálního nastavení. To je velmi závislé na scénáři. Ve zjednodušeném příkladu, například při personalizaci maloobchodní zkušenosti, NumberOfPetsOwned může být funkce, která je numerická, jak můžete chtít, aby lidé s 2 nebo 3 domácí zvířata ovlivnit personalizaci výsledek dvakrát nebo třikrát tolik jako s 1 pet. Funkce, které jsou založeny na číselných jednotkách, ale kde význam není lineární - například věk, teplota nebo výška osoby - jsou nejlépe kódovány jako řetězce a kvalita prvku může být obvykle vylepšena pomocí rozsahů. Například Věk může být kódován jako "Věk":"0-5", "Věk":"6-10", atd.
* Logické hodnoty **odeslané** s hodnotou "false" fungují, jako by nebyly odeslány vůbec.

Funkce, které nejsou k dispozici by měly být vynechány z požadavku. Vyhněte se odesílání funkcí s hodnotou null, protože budou zpracovány jako existující a s hodnotou "null" při trénování modelu.

## <a name="categorize-features-with-namespaces"></a>Kategorizace funkcí pomocí oborů názvů

Personalizátor přijímá funkce uspořádané do oborů názvů. Můžete určit, ve vaší aplikaci, pokud jsou použity obory názvů a jaké by měly být. Obory názvů se používají k seskupení funkcí o podobném tématu nebo funkcích, které pocházejí z určitého zdroje.

Následují příklady oborů názvů funkcí používaných aplikacemi:

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* VideoŘešení
* Informace o uživatelském zařízení
* Weather
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Obory názvů funkcí můžete pojmenovat podle vlastních konvencí, pokud se jedná o platné klíče JSON. Obory názvů se používají k uspořádání funkcí do různých sad a k rozdvojení funkcí s podobnými názvy. Obory názvů si můžete myslet jako "předponu", která je přidána k názvům funkcí. Obory názvů nelze vnořit.


V následujícím `user`JSON, `state`, `device` a jsou obory názvů funkcí. 

> [!Note]
> V současné době důrazně doporučujeme používat názvy pro obory názvů funkcí, které jsou založeny na UTF-8 a začínají různými písmeny. Například `user`, `state`, `device` a `u` `s`začínat na , , a `d`. V současné době mají obory názvů se stejnými prvními znaky může mít za následek kolize v indexech používaných pro strojové učení.

JSON objekty mohou obsahovat vnořené objekty JSON a jednoduché vlastnosti/hodnoty. Pole lze zahrnout pouze v případě, že položky pole jsou čísla. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "profileType":"AnonymousUser",
                "latlong": [47.6, -122.1]
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

### <a name="restrictions-in-character-sets-for-namespaces"></a>Omezení znakových sad pro obory názvů

Řetězec, který používáte pro pojmenování oboru názvů, musí dodržovat určitá omezení: 
* Nemůže to být unicode.
* Pro názvy oborů názvů můžete použít některé tisknutelné symboly s kódy < 256. 
* Nelze použít symboly s kódy < 32 (nelze tisknout), 32 (mezera), 58 (dvojtečka), 124 (potrubí) a 126–140.
* Nemělby začínat podtržítkem "_" nebo bude funkce ignorována.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Jak zefektivnit sady funkcí pro personalistu

Dobrá sada funkcí pomáhá personalistovi naučit se předvídat akci, která bude řídit nejvyšší odměnu. 

Zvažte odeslání funkcí do rozhraní PERSONALIZER Rank API, které se řídí těmito doporučeními:

* Existuje dostatek funkcí pro přizpůsobení. Čím přesněji je obsah cílený, tím více funkcí je zapotřebí.

* Existuje dostatek funkcí různých *hustot*. Funkce je *hustá,* pokud je mnoho položek seskupeno do několika bloků. Například tisíce videí lze klasifikovat jako "dlouhé" (více než 5 minut dlouhé) a "Krátké" (pod 5 minut). Jedná se o *velmi hustou* vlastnost. Na druhou stranu stejné tisíce položek mohou mít atribut s názvem "Název", který téměř nikdy nebude mít stejnou hodnotu z jedné položky do druhé. Jedná se o velmi nehustý nebo *řídkou* funkci.  

S funkcemi s vysokou hustotou pomáhá personalista extrapolovat učení z jedné položky do druhé. Ale pokud existuje jen několik funkcí a jsou příliš husté, personalizáta se pokusí přesně zacílit obsah s několika kbelíky, ze kterých si můžete vybrat.

### <a name="improve-feature-sets"></a>Vylepšete sady funkcí 

Analyzujte chování uživatelů pomocí offline vyhodnocení. To vám umožní podívat se na minulé údaje, abyste zjistili, jaké funkce výrazně přispívají k pozitivním odměnám oproti těm, které přispívají méně. Můžete vidět, jaké funkce pomáhají, a bude na vás a vaší aplikaci, abyste našli lepší funkce, které můžete poslat personalistovi, abyste ještě zlepšili výsledky.

Tyto následující části jsou běžné postupy pro zlepšení funkcí odeslaných personalistovi.

#### <a name="make-features-more-dense"></a>Zhustší funkce

Sady funkcí je možné vylepšit jejich úpravou tak, aby byly větší a více či méně husté.

Například časové razítko až do druhého je velmi řídká funkce. To by mohlo být hustší (efektivní) klasifikací časů do "ráno", "poledne", "odpoledne", atd.

Informace o poloze také obvykle těží z vytváření širších klasifikací. Například souřadnice Zeměpisné délky, například Lat: 47.67402° S. N, Dlouhá: 122.12154° W, je příliš přesná a nutí model učit se zeměpisné šířce a délky jako odlišné rozměry. Pokud se pokoušíte přizpůsobit informace o poloze na základě informací o poloze, pomáhá seskupit informace o poloze ve větších sektorech. Snadný způsob, jak to udělat, je zvolit vhodnou přesnost zaokrouhlení pro lat-long čísla a kombinovat zeměpisnou šířku a délku do "oblastí" tím, že je do jednoho řetězce. Například dobrý způsob, jak reprezentovat 47.67402° S. N, Long: 122.12154° W v oblastech o šířce přibližně několik kilometrů by byl "umístění":"34.3 , 12.1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Rozbalit sady funkcí s extrapolovanými informacemi

Můžete také získat další funkce tím, že přemýšlíte o neprozkoumaných atributech, které lze odvodit z informací, které již máte. Například ve fiktivním seznamu filmů personalizace, je možné, že víkend vs všední den vyvolává odlišné chování od uživatelů? Čas může být rozšířen tak, aby měl atribut "víkend" nebo "den v týdnu". Do národní kulturní svátky řídit pozornost některých typů filmů? Například atribut "Halloween" je užitečný v místech, kde je relevantní. Je možné, že deštivé počasí má významný vliv na výběr filmu pro mnoho lidí? S časem a místem, může meteorologická služba poskytnout tyto informace a můžete přidat jako další funkci. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Rozšiřte sady funkcí o umělou inteligenci a kognitivní služby

Umělá inteligence a služby Cognitive Services připravené ke spouštění mohou být velmi výkonným doplňkem personalistu. 

Předzpracováním položek pomocí služeb umělé inteligence můžete automaticky extrahovat informace, které budou pravděpodobně relevantní pro přizpůsobení.

Například:

* Můžete spustit soubor filmu přes [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) extrahovat prvky scény, text, sentiment a mnoho dalších atributů. Tyto atributy pak mohou být hustší, aby odrážely vlastnosti, které původní metadata položky neměla. 
* Obrázky lze spouštět prostřednictvím detekce objektů, tváří prostřednictvím mínění atd.
* Informace v textu lze rozšířit extrahováníentity, mínění, rozšiřující entity s grafem znalostí Bing, atd.

Můžete použít několik dalších [služeb Azure Cognitive Services](https://www.microsoft.com/cognitive-services), jako je

* [Propojení entit](../entitylinking/home.md)
* [Analýza textu](../text-analytics/overview.md)
* [Emoce](../emotion/home.md)
* [Počítačové vidění](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Akce představují seznam možností

Každá akce:

* Má ID _události._ Pokud již máte ID události, měli byste ji odeslat. Pokud nemáte ID události, neposílejte ho, personalizátář ho vytvoří a vrátí v odpovědi na požadavek pořadí. ID je přidruženo k události Rank, nikoli k uživateli. Pokud vytvoříte ID, identifikátor GUID funguje nejlépe. 
* Má seznam funkcí.
* Seznam funkcí může být velký (stovky), ale doporučujeme vyhodnotit efektivitu funkcí, aby se odstranily funkce, které nepřispívají k získání odměn. 
* Funkce v **akcích** mohou nebo nemusí mít žádnou korelaci s funkcemi v **kontextu** používaném personalistou.
* Funkce akcí mohou být přítomny v některých akcích a ne v jiných. 
* Funkce pro určité ID akce mohou být k dispozici jeden den, ale později budou nedostupné. 

Algoritmy strojového učení personalizátů budou fungovat lépe, pokud existují stabilní sady funkcí, ale volání ranku neselže, pokud se sada funkcí v průběhu času změní.

Při hodnocení akcí neposílejte více než 50 akcí. Může se jedná o stejných 50 akcí pokaždé, nebo se mohou změnit. Máte-li například katalog produktů 10 000 položek pro aplikaci elektronického obchodování, můžete použít k určení 40 nejlepších položek, které se zákazníkovi mohou líbit, a pomocí personalistu najít ten, který vygeneruje největší odměnu (například , uživatel přidá do košíku) pro aktuální kontext.


### <a name="examples-of-actions"></a>Příklady akcí

Akce, které odešlete do rozhraní API hodnocení, budou záviset na tom, co se pokoušíte přizpůsobit.

Zde je několik příkladů:

|Účel|Akce|
|--|--|
|Přizpůsobte si, který článek je zvýrazněn na zpravodajském webu.|Každá akce je potenciální zpravodajský článek.|
|Optimalizujte umístění reklam na webu.|Každá akce bude rozložení nebo pravidla pro vytvoření rozložení pro reklamy (například nahoře, vpravo, malé obrázky, velké obrázky).|
|Zobrazení personalizovaného hodnocení doporučených položek na nákupním webu.|Každá akce je specifický produkt.|
|Navrhněte prvky uživatelského rozhraní, jako jsou filtry, které se použijí na konkrétní fotografii.|Každá akce může být jiný filtr.|
|Zvolte odpověď chatovacího robota, abyste objasnili záměr uživatele nebo navrhli akci.|Každá akce je možnost, jak interpretovat odpověď.|
|Volba, co se má zobrazit v horní části seznamu výsledků hledání|Každá akce je jedním z několika nejlepších výsledků hledání.|


### <a name="examples-of-features-for-actions"></a>Příklady funkcí akcí

Následují dobré příklady funkcí akcí. Ty budou záviset hodně na každé aplikaci.

* Funkce s charakteristikami akcí. Například, je to film nebo televizní seriál?
* Funkce o tom, jak uživatelé mohli v minulosti pracovat s touto akcí. Například, tento film je většinou vidět lidé v demografii A nebo B, to je obvykle hrál ne více než jednou.
* Funkce o charakteristikách, jak uživatel *vidí* akce. Obsahuje například plakát filmu zobrazený v miniatuře tváře, auta nebo krajiny?

### <a name="load-actions-from-the-client-application"></a>Načíst akce z klientské aplikace

Funkce z akcí mohou obvykle pocházet ze systémů pro správu obsahu, katalogů a doporučujících systémů. Vaše aplikace je zodpovědná za načítání informací o akcích z příslušných databází a systémů, které máte. Pokud se vaše akce nezmění nebo jejich načtení pokaždé, když má zbytečný dopad na výkon, můžete přidat logiku ve vaší aplikaci do mezipaměti těchto informací.

### <a name="prevent-actions-from-being-ranked"></a>Zabránit seřazení akcí

V některých případech existují akce, které nechcete zobrazit uživatelům. Nejlepší způsob, jak zabránit akci seřazené jako nejvyšší je nezahrnout ji do seznamu akcí na rank API na prvním místě.

V některých případech lze určit pouze později v obchodní logiky, pokud výsledná _akce_ rank API volání má být zobrazena uživateli. V těchto případech byste měli použít _neaktivní události_.

## <a name="json-format-for-actions"></a>Formát JSON pro akce

Při volání rank, budete posílat více akcí na výběr:

JSON objekty mohou obsahovat vnořené objekty JSON a jednoduché vlastnosti/hodnoty. Pole lze zahrnout pouze v případě, že položky pole jsou čísla. 

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium",
          "grams": [400,800]
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [150, 300, 450]
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none",
          "grams": [300, 600, 900]
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low",
          "grams": [300, 600]
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>Příklady kontextových informací

Informace pro _kontext_ závisí na každé aplikaci a případu použití, ale obvykle může obsahovat informace, jako jsou:

* Demografické a profilové informace o uživateli.
* Informace extrahované ze záhlaví protokolu HTTP, například uživatelského agenta, nebo odvozené z informací protokolu HTTP, jako jsou například reverzní geografická vyhledávání založená na adresách IP.
* Informace o aktuálním čase, jako je den v týdnu, víkend nebo ne, ráno nebo odpoledne, prázdninnebo ne, atd.
* Informace získané z mobilních aplikací, jako je umístění, pohyb nebo úroveň baterie.
* Historické agregáty chování uživatelů - například jaké jsou filmové žánry, které tento uživatel viděl nejvíce.

Vaše aplikace je zodpovědná za načítání informací o kontextu z příslušných databází, senzorů a systémů, které můžete mít. Pokud se informace o kontextu nezmění, můžete do aplikace přidat logiku pro ukládání těchto informací do mezipaměti před jejich odesláním do rozhraní RANK API.

## <a name="json-format-for-context"></a>Formát JSON pro kontext 

Kontext je vyjádřen jako objekt JSON, který je odeslán do rozhraní API pořadí:

JSON objekty mohou obsahovat vnořené objekty JSON a jednoduché vlastnosti/hodnoty. Pole lze zahrnout pouze v případě, že položky pole jsou čísla. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true,
                "screensize": [1680,1050]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky

[Zpětnovazební učení](concepts-reinforcement-learning.md) 
