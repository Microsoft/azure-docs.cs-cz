---
title: 'Funkce: Akce a kontextu - Personalizer'
titleSuffix: Azure Cognitive Services
description: Personalizer používá funkce, informace o akce a kontextu, a nabízí lepší návrhy hodnocení. Funkce mohou být velmi obecná nebo specifické položky.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: concept
ms.date: 06/24/2019
ms.author: edjez
ms.openlocfilehash: 2353b8c735602aff0386f44cc29d2be5eb9f90c4
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340893"
---
# <a name="features-are-information-about-actions-and-context"></a>Funkce jsou informace o akce a kontextu

Služba Personalizer funguje tak, že učení, co vaše aplikace by se zobrazit uživatelům v daném kontextu.

Používá personalizer **funkce**, což je informace o **aktuálního kontextu** zvolit nejlepší **akce**. Funkce představují všechny informace, které si myslíte, že může pomoct přizpůsobit, abyste dosáhli vyšší odměny. Funkce mohou být velmi obecná nebo specifické položky. 

Například můžete mít **funkce** o:

* _Uživatele_ například `UserID`. 
* _Obsah_ třeba když se na video `Documentary`, `Movie`, nebo `TV Series`, nebo zda maloobchodního prodeje položek je k dispozici v úložišti.
* _Aktuální_ období dobu, jako je například den v týdnu je.

Personalizer neuloží, omezit nebo opravit, jaké funkce můžete odeslat akce a kontextu:

* Některé funkce pro některé akce a ne pro ostatní uživatele, můžete odeslat, pokud je nemáte. Například seriálu může mít atributy, které nemají videa.
* Může mít některé funkce, které jsou k dispozici pouze v některých případech. Mobilní aplikace může například poskytují další informace, než na webové stránce. 
* V průběhu času mohou přidávat a odebírat funkce o kontextu a akce. Personalizer i nadále poslechněte si dostupné informace.
* Musí existovat alespoň jedna funkce pro daný kontext. Personalizer prázdnému kontextu nepodporuje. Při odeslání pouze dlouhodobý kontextu pokaždé, když, zvolí Personalizer akce pro hodnocení pouze týkající se funkcí v akcích. 
* Personalizer se pokusí vybrat akce, které nejlépe vyhovuje všem uživatelům v každém okamžiku.

## <a name="supported-feature-types"></a>Typy podporované funkce

Personalizer podporuje funkce řetězec, číselných a logických typů.

### <a name="how-choice-of-feature-type-affects-machine-learning-in-personalizer"></a>Jak ovlivňuje Machine Learning v Personalizer vybrat typ funkce

* **Řetězce**: Pro typy řetězců vytvoří pro každou kombinaci klíč a hodnotu nový váhy v Personalizer modelu strojového učení. 
* **Číselné**: V případě počet by měl proporcionálně ovlivňují výsledek individuálního nastavení, měli byste použít číselné hodnoty. To je velmi scénář závislé. V zjednodušený příklad například při přizpůsobení maloobchodní prostředí, NumberOfPetsOwned může být funkce, která jsou číselná chcete ovlivnit přizpůsobení výsledek dvakrát nebo třikrát co s 1 pet osobám s mazlíčci 2 nebo 3. Funkce, které jsou založeny na číselná jednotek, ale pokud není lineární – například věk, teplota nebo osoba vyska - význam jsou nejlépe kódovány jako řetězce a kvalitu funkce lze obvykle vylepšit použití rozsahů. Například věk může být zakódován jako "Age": "0-5", "Age": "6-10" atd.
* **Logická** hodnoty odeslané s hodnotou "false" act, jako kdyby jejich nebyl odeslán vůbec.

Funkce, které nejsou k dispozici vyloučeny z požadavku. Vyhněte se funkce s hodnotou null pro odesílání, protože ho budou zpracovány jako existující a s hodnotou "null" při cvičení modelu.

## <a name="categorize-features-with-namespaces"></a>Kategorizace funkce s obory názvů

Personalizer přijímá funkce, které jsou uspořádány do oborů názvů. Můžete určit, v aplikaci, pokud se používají obory názvů a co by měl být. Obory názvů slouží k seskupení funkce podobné tématu nebo funkce, které pocházejí z určitého zdroje.

Následují příklady funkce obory názvů používané aplikacemi:

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

Funkce obory názvů vlastních konvencemi jako jsou platný klíčů JSON můžete pojmenovat.

V následujícím kódu JSON `user`, `state`, a `device` jsou funkce obory názvů.

Objekty JSON může obsahovat vnořené objekty JSON a jednoduché vlastnosti a hodnoty. Pole může obsahovat pouze v případě, že jsou položky pole čísel. 

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug",
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

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>Jak se funkce nastaví pro Personalizer zefektivnit?

Sada dobré funkcí pomáhá Personalizer zjistěte, jak předvídat akce, které vyvolají potřebu nejvyšší. 

Vezměte v úvahu odesílání funkce do rozhraní API pořadí Personalizer, postupujte podle následujících doporučení:

* Nejsou k dispozici dostatek funkcí, které jednotky přizpůsobení. Čím více přesně obsahu musí být cílem, jsou potřeba další funkce.

* Existuje dostatek funkcí z různých *hustoty*. Funkce je *dense* Pokud mnoho položek jsou seskupené v několika kontejnerů. Například můžete být tisíce videa jsou klasifikovány jako "Dlouhý" (více než 5 minut dlouhé) a "Krátký" (v části 5 minut dlouhé). Jde *velmi dense* funkce. Stejné tisíců položek na druhé straně může mít atribut s názvem "Title", který téměř nikdy má stejnou hodnotu z jedné položky do jiného. Jedná se velmi bez bohatém nebo *zhuštěné* funkce.  

Funkce s vysokou hustotou pomůže Personalizer potom údaje extrapolovat učení z jedné položky do jiného. Ale pokud existuje pouze několik funkcí, jsou příliš dense Personalizer se pokusí přesně cíl obsahu s pouze několika kontejnerů lze vybírat.

### <a name="improve-feature-sets"></a>Vylepšení sady funkcí 

Analýza chování uživatele tímto způsobem zkušební verzi v režimu Offline. Můžete se podívat na posledních dat a zjistit, co funkce silně přispíváte do kladnou odměny a ty, které přispívají menší. Můžete zjistit, co funkce pomáhají a bude až vás a vaše aplikace lépe funkcích odesílat Personalizer a dosáhnout ještě lepších výsledků.

Tyto následující části jsou běžné postupy pro zlepšení odesílat Personalizer funkce.

#### <a name="make-features-more-dense"></a>Ujistěte se, funkce více dense

Je možné ke zlepšení vaší sady funkcí tak, že upravíte, aby byly větší a víc nebo míň dense.

Například na druhé časové razítko je velmi zhuštěné funkce. Ji může nastavit na hustém zefektivnit (?) tříděním časy do "ráno", "poledne", "odpoledne" atd.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>Rozbalte sady funkcí extrapolované informacemi

Můžete také získat další funkce podle přemýšlet o neprozkoumaných atributy, které mohou být odvozeny z informací, které už máte. Například v seznamu individuálního nastavení fiktivní film, je možné, který pracovní den víkendu vs elicits různé chování uživatelů? Čas může mít atribut "víkendu" nebo "den v týdnu" rozšířit. Státních svátků v kulturní jednotka pozornost k určitým typům film? Například "Halloweenem" atribut je užitečné v místech, kde je relevantní. Je možné, že destivo počasí má významný dopad na řadu videa pro mnoho uživatelů? V čase a místě může poskytovat služby weather, informace a můžete ho přidat jako další funkce. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>Rozbalte sady funkcí pomocí umělé inteligence a cognitive services

Umělá inteligence a Cognitive Services připravené ke spuštění může být velmi účinné přidání do Personalizer. 

Předběžného zpracování položek pomocí služby umělé inteligence, můžete automaticky extrahovat informace, které by mohla být relevantní pro přizpůsobení.

Příklad:

* Můžete spustit film prostřednictvím [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/) extrahovat prvky scény, text, mínění a mnoho dalších atributů. Tyto atributy lze pak provést více hustému tak, aby odrážely charakteristiky, které nebyly k dispozici původní metadata položky. 
* Image je možné spustit prostřednictvím objektu zjišťování, tváří prostřednictvím mínění, atd.
* Informace v textu se dají rozšířit tak, že extrahují entity, mínění, rozbalení entity, které Bing knowledge graphu a tak dál.

Můžete použít několik dalších [Azure Cognitive Services](https://www.microsoft.com/cognitive-services), třeba

* [Rozhraní entity Linking](../entitylinking/home.md)
* [Analýza textu](../text-analytics/overview.md)
* [Rozpoznávání emocí](../emotion/home.md)
* [Počítačové zpracování obrazu](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>Akce představují seznam možností

Každá akce:

* Nemá identifikátor.
* Obsahuje seznam funkcí.
* Seznam funkcí může být velký (stovky), ale doporučujeme vyhodnotit funkce efektivitu odebrat funkce, které nejsou přispět k získání odměny. 
* Funkce **akce** může nebo nemusí být jakákoli korelace s funkcemi v **kontextu** používané Personalizer.
* Funkce pro akce mohou být přítomny v některé akce ale ne pro jiné. 
* Funkce pro určité ID akce může být k dispozici jeden den, ale později přestanou být dostupné. 

Společnosti personalizer algoritmů strojového učení provede lépe, když jsou sady funkcí stabilní, ale pořadí volání nebude selhat, pokud tuto funkci nastavit změn v čase.

Neodesílat ve více než 50 akcí při hodnocení akce. Může se jednat stejné akce 50 pokaždé, když nebo může změnit. Například pokud máte 10 000 položek pro aplikaci elektronického obchodování katalog produktů, můžete použít doporučení nebo filtrování engine k určení horní 40 zákazník může a použít Personalizer a vyberte ten, který se bude generovat většina reward (například bude uživatel přidat do košíku) pro aktuální kontext.


### <a name="examples-of-actions"></a>Příklady akcí

Akce, které odesíláte do rozhraní API pořadí bude záviset na co se pokoušíte přizpůsobit.

Následuje několik příkladů:

|Účel|Akce|
|--|--|
|Přizpůsobení, něž je zvýrazněn na webu zpráv.|Každá akce je potenciální zpravodajskému článku.|
|Optimalizujte umístění ad na webu.|Každá akce bude rozložení nebo pravidla pro vytváření rozložení pro reklamu (například v horní části na správné, malé obrázky, velké obrázky).|
|Zobrazte přizpůsobené hodnocení Doporučené položky na nákupního webu.|Každá akce je konkrétní produkt.|
|Navrhněte prvky uživatelského rozhraní, jako jsou filtry, které platí pro konkrétní fotografie.|Každá akce může být jiný filtr.|
|Zvolte chatovací robot odpovědi pro upřesnění záměru uživatele nebo v něm navrhnout akci.|Každá akce je možnost, jak interpretovat odpověď.|
|Zvolte, co chcete zobrazit v horní části seznamu výsledků hledání|Každá akce je jedním z nejlepší několik výsledky hledání.|


### <a name="examples-of-features-for-actions"></a>Příklady funkcí pro akce

Následující jsou dobrým příkladem funkcí pro akce. Toto bude záviset mnohem na každou aplikaci.

* Funkce s vlastnostmi akce. Například je filmu nebo řady tv?
* Funkce, o jak uživatelé mohou mít interakci s touto akcí v minulosti. Například toto video je ve většině případů vidět lidí v demografické údaje A nebo B, je obvykle hraní více než jednou.
* Funkce charakteristiky jak uživatel *vidí* akce. Například se plakát pro video ukazuje miniatur zahrnout tváří, autech nebo prostředí?

### <a name="load-actions-from-the-client-application"></a>Načíst akce z klientské aplikace

Funkce z akcí může obvykle pocházejí z systémy správy obsahu, katalogy a doporučené systémy. Aplikace zodpovídá za načítání informace o akcích z příslušných databází a systémů, na které máte. Pokud vaše akce neměnit nebo znova načíst pokaždé, když má zbytečné dopad na výkon, můžete přidat logiky v aplikaci pro ukládání do mezipaměti tyto informace.

### <a name="prevent-actions-from-being-ranked"></a>Chránit proti akcím z řazené

V některých případech jsou akce, které nechcete zobrazit uživatelům. Nejlepší způsob, jak zabránit akci z řazené podle nejvyššího je nechcete zahrnout do seznamu akcí pořadí rozhraní API na prvním místě.

V některých případech je určen pouze později v obchodní logiky Pokud výsledném _akce_ API pořadí volání je zobrazený uživateli. Pro tyto případy, byste měli použít _neaktivní události_.

## <a name="json-format-for-actions"></a>Formát JSON pro akce

Při volání metody pořadí, odešlete několik akcí na výběr:

Objekty JSON může obsahovat vnořené objekty JSON a jednoduché vlastnosti a hodnoty. Pole může obsahovat pouze v případě, že jsou položky pole čísel. 

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

Informace o _kontextu_ závisí na každý případ použití, ale obvykle může obsahovat informace, jako:

* Profil a demografické informace o uživateli.
* Informace o extrahují z hlavičky protokolu HTTP, jako je například uživatelského agenta nebo odvozený z HTTP informace, jako je reverzní geografické vyhledávání na základě IP adres.
* Informace o aktuální čas, jako je například den v týdnu, víkendu nebo Ne, dopoledne nebo odpoledne svátcích nebo Ne, atd.
* Informace získané z mobilních aplikací, jako je například umístění, přesouvání nebo stav baterie.
* Historické agregace chování uživatelů – například jaké jsou žánry film tohoto uživatele nezobrazil na maximum.

Aplikace zodpovídá za načítání informací o kontextu z příslušných databází, senzorů a systémů, které máte uzavřeny. Pokud nedojde ke změně informace o kontextu, můžete přidat logiky v aplikaci pro ukládání do mezipaměti tyto informace před odesláním do rozhraní API pro řazení.

## <a name="json-format-for-context"></a>Formát JSON pro kontext 

Kontext je vyjádřena jako objekt JSON, který je odeslán do rozhraní API pořadí:

Objekty JSON může obsahovat vnořené objekty JSON a jednoduché vlastnosti a hodnoty. Pole může obsahovat pouze v případě, že jsou položky pole čísel. 

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

## <a name="next-steps"></a>Další postup

[Zpětnovazebnému učení](concepts-reinforcement-learning.md) 
