---
title: 'Funkce: akce a kontext – přizpůsobování'
titleSuffix: Azure Cognitive Services
description: Přizpůsobení používá funkce, informace o akcích a kontextu k zajištění lepšího hodnocení návrhů. Funkce mohou být velmi obecné nebo specifické pro položku.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: edd1549ddabef0ae1ba37150ad75a371ac6e6d85
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365512"
---
# <a name="features-are-information-about-actions-and-context"></a>Funkce jsou informace o akcích a kontextu.

Služba pro přizpůsobování funguje tak, že se naučí, co by aplikace měla zobrazit uživatelům v daném kontextu.

Přizpůsobení používá **funkce** , které jsou informace o **aktuálním kontextu** pro výběr nejlepší **Akce**. Tyto funkce reprezentují všechny informace, které si myslíte, abyste mohli dosáhnout vyšší ceny. Funkce mohou být velmi obecné nebo specifické pro položku. 

Můžete mít například **funkci** o:

* _Uživatel_ , jako je například `Sports_Shopper` . Nemělo by to být individuální ID uživatele. 
* _Obsah_ , jako je například, pokud se jedná o, a, `Documentary` `Movie` `TV Series` nebo zda je položka maloobchodního prodeje v obchodě k dispozici.
* _Aktuální_ časové období, jako je například den v týdnu.

Přizpůsobené aplikace nepředepisuje, neomezuje ani neopravují funkce, které můžete odeslat pro akce a kontext:

* Některé funkce můžete pro některé akce Odeslat, a ne pro ostatní, pokud je nemáte. Například televizní série může mít atributy filmy.
* Některé funkce mohou být k dispozici pouze několikrát. Například mobilní aplikace může poskytovat další informace než webová stránka. 
* V průběhu času můžete přidat nebo odebrat funkce týkající se kontextu a akcí. Přizpůsobování se dál učí od dostupných informací.
* Musí existovat alespoň jedna funkce pro daný kontext. Přizpůsobení nepodporuje prázdný kontext. Pokud zadáte pouze pevný kontext pokaždé, přichycení zvolí akci pro hodnocení pouze ohledně funkcí v akcích.
* V případě funkcí kategorií nemusíte definovat možné hodnoty a nemusíte předem definovat rozsahy pro číselné hodnoty.

## <a name="supported-feature-types"></a>Podporované typy funkcí

Přizpůsobení podporuje funkce typu String, numeric a Boolean.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Jak volba typu funkce ovlivní Machine Learning v přizpůsobování

* **Řetězce** : u typů řetězců každá kombinace klíče a hodnoty vytváří novou váhu v modelu strojového učení pro přizpůsobení. 
* **Číselná** hodnota: je třeba použít číselné hodnoty, pokud má číslo proporcionálně ovlivnit výsledek přizpůsobení. To je velmi závislé na scénáři. Ve zjednodušeném příkladu, například při přizpůsobení maloobchodního prostředí, může být NumberOfPetsOwned funkce, která je numerická, protože může chtít, aby lidé se dvěma nebo 3 domácími osobami ovlivnili výsledek přizpůsobení dvakrát nebo třikrát, a to v rozsahu 1 PET. Funkce, které jsou založené na číselných jednotkách, ale u kterých není význam lineární – například stáří, teplota nebo výška osoby – jsou nejlépe kódované jako řetězce a kvalita funkcí se může obvykle zlepšit pomocí rozsahů. Například stáří může být kódováno jako "stáří": "0-5", "stáří": "6-10" atd.
* **Logické** hodnoty odeslané s hodnotou false fungují jako v případě, že jste byly odeslány vůbec.

Funkce, které nejsou k dispozici, by měly být z požadavku vynechány. Vyhněte se posílání funkcí s hodnotou null, protože se při výuce modelu zpracuje jako stávající a s hodnotou null.

## <a name="categorize-features-with-namespaces"></a>Kategorizace funkcí s obory názvů

Přizpůsobování přebírá funkce uspořádané do oborů názvů. V aplikaci určíte, zda jsou obory názvů použity a co mají být. Obory názvů slouží k seskupení funkcí podobného tématu nebo funkcí, které pocházejí z určitého zdroje.

Následují příklady oborů názvů funkcí používaných aplikacemi:

* User_Profile_from_CRM
* Čas
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Počasí
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

Obory názvů funkcí můžete pojmenovat podle vlastních konvencí, pokud jsou platné klíče JSON. Obory názvů slouží k uspořádání funkcí do různých sad a k jednoznačnému využití funkcí s podobnými názvy. Obory názvů si můžete představit jako předponu, která je přidána do názvů funkcí. Obory názvů nemůžou být vnořené.


V následujících JSON,, `user` `state` a `device` jsou obory názvů funkcí. 

> [!Note]
> V současné době důrazně doporučujeme používat názvy pro obory názvů funkcí, které jsou založené na kódování UTF-8 a začínají s různými písmeny. Například,, `user` `state` a `device` začněte s, a `u` `s` `d` . V současné době by obory názvů se stejnými prvními znaky mohly způsobit kolizi v indexech použitých pro strojové učení.

Objekty JSON můžou zahrnovat vnořené objekty JSON a jednoduché vlastnosti nebo hodnoty. Pole lze zahrnout pouze v případě, že položky pole jsou čísla. 

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

### <a name="restrictions-in-character-sets-for-namespaces"></a>Omezení ve znakových sadách pro obory názvů

Řetězec, který použijete pro pojmenování oboru názvů, musí splňovat určitá omezení: 
* Nemůže to být Unicode.
* Můžete použít některé z tisknutelných symbolů s kódy < 256 pro názvy oborů názvů. 
* Nemůžete použít symboly s kódy < 32 (netisknutelný), 32 (Space), 58 (dvojtečka), 124 (pipe) a 126 – 140.
* Nesmí začínat podtržítkem (_), jinak se tato funkce bude ignorovat.

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Jak nastavit efektivnější nastavení funkcí pro přizpůsobení

Dobrá sada funkcí pomáhá přizpůsobovat, jak předpovědět akci, která bude řídit nejvyšší odměnu. 

Zvažte odeslání funkcí rozhraní API pro řazení přizpůsobeného rozhraní, které se řídí těmito doporučeními:

* K dispozici je dostatek funkcí pro přizpůsobení. Přesnější cílení obsahu vyžaduje, aby bylo potřeba víc funkcí.

* Existuje dostatek funkcí různých *hustot*. Funkce je *zhuštěná* , pokud je v několika intervalech seskupeno mnoho položek. Například tisíce videí mohou být klasifikovány jako "Long" (více než 5 minut dlouhé) a "krátká" (méně než 5 minut). Toto je *velmi zhuštěná* funkce. Na druhé straně stejné tisíce položek mohou mít atribut nazvaný "title", který téměř nikdy nebude mít stejnou hodnotu z jedné položky do druhé. Toto je velmi nezhuštěná nebo *zhuštěná* funkce.  

Funkce vysoké hustoty pomáhá přizpůsobování odvodit z jedné položky do druhé. Pokud ale existuje jenom několik funkcí a jsou moc husté, přizpůsobené aplikace se pokusí přesně cílit na obsah, který se dá vybrat jenom v několika intervalech.

### <a name="improve-feature-sets"></a>Vylepšení sad funkcí 

Proveďte analýzu chování uživatele provedením vyhodnocení offline. To vám umožní podívat se na minulá data a zjistit, jaké funkce jsou silně přispívající k kladným rozdílům, které přispívají méně. Můžete se podívat, jaké funkce vám pomáhají, a bude až do vaší aplikace, abyste našli lepší funkce pro odeslání do přizpůsobeného nástroje, aby bylo možné ještě víc zlepšit výsledky.

Tyto části jsou běžné postupy pro vylepšení funkcí odesílaných na přizpůsobení.

#### <a name="make-features-more-dense"></a>Udělejte větší zhuštěné funkce

Je možné vylepšit sady funkcí jejich úpravou, aby byly větší a více nebo méně zhuštěné.

Například časové razítko dolů na druhou je velmi zhuštěná funkce. Je možné, že je možné provést více hustě (v platnosti) tím, že rozklasifikujete časy do "ráno", "poledne", "odpoledne" atd.

Informace o poloze také obvykle těží z vytváření širších klasifikací. Například Latitude-Longitude souřadnici, jako je například lat: 47,67402 ° N, Long: 122,12154 ° W, je příliš přesný a vynutí, aby se model dozvěděl o zeměpisné šířce a délce jako odlišné rozměry. Při pokusu o přizpůsobení na základě informací o poloze pomáhá seskupovat informace o poloze ve větších sektorech. Snadným způsobem, jak to provést, je zvolit vhodnou přesnost zaokrouhlení pro Lat-Long čísla a seskupit zeměpisnou šířku a délku do "oblastí" tím, že je provedete do jednoho řetězce. Dobrým způsobem, jak vyjádřit 47,67402 ° N, Long: 122,12154 ° W v oblastech, přibližně pár kilometrů na šířku by bylo "umístění": "34.3, 12,1".


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Rozbalení sad funkcí s použitím extrapolace informací

Můžete také získat více funkcí tak, že si myslíte neprozkoumatelné atributy, které mohou být odvozeny z informací, které už máte. Například v fiktivním přizpůsobení seznamu filmů je možné, že víkendový vs v týdnu zjistí různé chování od uživatelů? Čas se dá rozšířit tak, aby měl atribut "víkend" nebo "Weekday". Máte pozor na konkrétní typy filmů národní kulturní svátky? Například atribut "Halloweenem" je užitečný v místech, kde je relevantní. Je možné, že povětrnostní počasí má výrazný dopad na výběr filmu pro mnoho lidí? V čase a na místě může služba počasí poskytnout tyto informace a můžete ji přidat jako další funkci. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Rozbalení sad funkcí s umělou logikou a rozpoznáváním služeb

Umělá logika a Cognitive Services připravená ke spuštění můžou být velmi výkonným doplňkem k přizpůsobenému nástroji. 

Díky předzpracování vašich položek pomocí umělých analytických služeb můžete automaticky extrahovat informace, které jsou pravděpodobně relevantní pro přizpůsobení.

Příklad:

* Můžete spustit filmový soubor prostřednictvím [video indexer](https://azure.microsoft.com/services/media-services/video-indexer/) k extrakci elementů scény, text, mínění a mnoha dalších atributů. Tyto atributy je pak možné odrážet tak, aby odrážely vlastnosti, které původní metadata položky neobsahovaly. 
* Image je možné spouštět pomocí detekce objektů, plošek až po mínění atd.
* Informace v textu lze rozšířit extrahováním entit, mínění, rozbalováním entit pomocí nástroje Bing Knowledge Graph atd.

Můžete použít několik dalších [Cognitive Services Azure](https://www.microsoft.com/cognitive-services), například

* [Entity Linking](../text-analytics/index.yml)
* [Analýza textu](../text-analytics/overview.md)
* [Emoce](../face/overview.md)
* [Počítačové zpracování obrazu](../computer-vision/overview.md)

## <a name="actions-represent-a-list-of-options"></a>Akce reprezentují seznam možností.

Každá akce:

* Má ID _události_ . Pokud již máte ID události, měli byste je odeslat. Pokud nemáte ID události, neodešlete ho, přizpůsobování ho pro vás vytvoří a vrátí ho v reakci na požadavek Rank. ID je přidruženo k události pořadí, nikoli k uživateli. Vytvoříte-li ID, bude nejlépe fungovat GUID. 
* Obsahuje seznam funkcí.
* Seznam funkcí může být velký (stovky), ale doporučujeme vyhodnotit efektivitu funkcí a odebrat funkce, které nepřispívají k získání neprospěchu. 
* Funkce v **akcích** mohou nebo nemusí mít žádnou korelaci s funkcemi v **kontextu** , který používá přizpůsobený modul.
* V některých akcích se můžou vyskytovat funkce pro akce, které ne ostatní. 
* Funkce pro konkrétní ID akce mohou být k dispozici po jednom dni, ale později v ní nejsou k dispozici. 

Algoritmy strojového učení pro přizpůsobení budou lepší, když budou existovat stabilní sady funkcí, ale pokud se sada funkcí změní v průběhu času, volání pořadí se nezdaří.

Neodesílat při akcích řazení více než 50 akcí. Může se jednat o stejné 50 akce pokaždé, když se můžou změnit. Například pokud máte katalog produktů s 10 000 položkami pro aplikaci pro elektronické obchodování, můžete použít modul pro vyfiltrování doporučení nebo filtrování k určení největšího 40 zákazníka, a pomocí přizpůsobeného nástroje najít ten, který vygeneruje největší odměnu (například uživatel bude pro aktuální kontext přidat do košíku).


### <a name="examples-of-actions"></a>Příklady akcí

Akce, které zadáte do rozhraní API řazení, budou záviset na tom, co se snažíte přizpůsobit.

Tady je několik příkladů:

|Účel|Akce|
|--|--|
|Přizpůsobení článku, který je zvýrazněný na webu příspěvky|Každá akce je potenciálně novinkou.|
|Optimalizujte umístění reklamy na webu.|Každá akce bude rozložení nebo pravidla, která vytvoří rozložení pro reklamy (například nahoře, malý obrázek a velké obrázky).|
|Zobrazení individuálního hodnocení doporučených položek na nákupním webu.|Každá akce je konkrétní produkt.|
|Navrhněte prvky uživatelského rozhraní, jako jsou filtry, které se mají použít pro konkrétní fotografii.|Každá akce může být jiný filtr.|
|Vyberte odpověď robota pro chat k objasnění záměru uživatele nebo navrhnout akci.|Každá akce je možností, jak interpretovat odpověď.|
|Vyberte, co se má zobrazit na začátku seznamu výsledků hledání.|Každá akce je jedním z nejdůležitějších výsledků hledání.|


### <a name="examples-of-features-for-actions"></a>Příklady funkcí pro akce

Níže jsou uvedeny dobré příklady funkcí pro akce. Ty budou záviset na každé aplikaci.

* Funkce s charakteristikou akcí. Například jde o film nebo televizní seriál?
* Funkce, jak uživatelé mohli s touto akcí pracovat v minulosti. Například tento film je většinou vidět lidem v demografických údajích A nebo B, obvykle se nehraje více než jednou.
* Funkce týkající se vlastností, jak uživatel *uvidí* akce Například obrázek plakátu pro film zobrazený v miniatuře obsahuje obličeje, automobily nebo krajinu?

### <a name="load-actions-from-the-client-application"></a>Načíst akce z klientské aplikace

K funkcím z akcí může typicky docházet ze systémů správy obsahu, katalogů a doporučených systémů. Vaše aplikace zodpovídá za načítání informací o akcích z příslušných databází a systémů, které máte. Pokud se vaše akce nezmění ani se nenačte, když má nepotřebný dopad na výkon, můžete do své aplikace přidat logiku pro ukládání těchto informací do mezipaměti.

### <a name="prevent-actions-from-being-ranked"></a>Zabránit seřazení akcí

V některých případech existují akce, které nechcete zobrazovat uživatelům. Nejlepším způsobem, jak zabránit řazení akce v pořadí podle nejvyšší úrovně, není jejich zahrnutí v seznamu akcí do rozhraní API řazení na prvním místě.

V některých případech se dá určit jenom později v obchodní logice, pokud by se uživateli zobrazila výsledná _Akce_ volání rozhraní API řazení. V těchto případech byste měli používat _neaktivní události_.

## <a name="json-format-for-actions"></a>Formát JSON pro akce

Při volání Rank budete odesílat několik akcí, které si můžete vybrat:

Objekty JSON můžou zahrnovat vnořené objekty JSON a jednoduché vlastnosti nebo hodnoty. Pole lze zahrnout pouze v případě, že položky pole jsou čísla. 

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

## <a name="examples-of-context-information"></a>Příklady informací o kontextu

Informace pro _kontext_ závisí na každé aplikaci a v případu použití, ale obvykle mohou zahrnovat například tyto informace:

* Demografické a profilové informace o uživateli.
* Informace extrahované z hlaviček protokolu HTTP, jako je například Agent pro uživatele, nebo odvozené z informací HTTP, jako jsou reverzní geografické vyhledávání na základě IP adres.
* Informace o aktuálním čase, jako je například den v týdnu, víkend nebo not, ráno nebo odpoledne, svátky atd.
* Informace extrahované z mobilních aplikací, jako je například poloha, pohyb nebo úroveň baterie.
* Historické agregované hodnoty chování uživatelů – například to, jaké jsou filmové žánry, které tento uživatel zobrazil nejvíc.

Vaše aplikace zodpovídá za načítání informací o kontextu z příslušných databází, senzorů a systémů, které jste pravděpodobně nastavili. Pokud se informace o kontextu nezmění, můžete do své aplikace přidat logiku pro ukládání těchto informací do mezipaměti, než ji odešlete do rozhraní API pro řazení.

## <a name="json-format-for-context"></a>Formát JSON pro kontext 

Kontext je vyjádřen jako objekt JSON, který se odesílá do rozhraní API řazení:

Objekty JSON můžou zahrnovat vnořené objekty JSON a jednoduché vlastnosti nebo hodnoty. Pole lze zahrnout pouze v případě, že položky pole jsou čísla. 

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