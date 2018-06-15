---
title: Publikování aplikace LEOŠ | Microsoft Docs
description: Po vytvoření a testování aplikace s použitím jazyka Principy (LEOŠ), můžete jej publikujte jako webovou službu v Azure.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: 056608e7843c8feab28359de5f2762164287f09d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343933"
---
# <a name="publish-your-trained-app"></a>Publikování vyškolení aplikace
Po dokončení vytváření a testování vaší aplikace LEOŠ ji publikujte. Po publikování aplikace, na stránce publikování se zobrazí všechny související HTTP [koncové body](luis-glossary.md#endpoint). Tyto koncové body na [oblast](luis-reference-regions.md) a za [klíč](Manage-Keys.md), pak jsou integrované do libovolné aplikace klienta, chatbot nebo back-end. 

Vždy můžete [testování](train-test.md) aplikace před publikováním. 

## <a name="production-and-staging-slots"></a>Produkční a pracovní sloty
Můžete publikovat aplikaci, kterou chcete **pracovní pozici** nebo **produkční Slot**. Pomocí dvou publikování sloty, můžete tak mít dvě různé verze pomocí publikovaných koncových bodů nebo stejnou verzi na dva různé koncové body. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>Nastavení konfigurace vyžaduje publikování modelu
Po provedení změn následujícího nastavení publikování ke koncovému bodu. 

## <a name="external-services-settings"></a>Nastavení externích služeb
Zahrnout nastavení služby externí **[postojích Analysis](#enable-sentiment-analysis)** a  **[řeči Priming](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Povolit postojích analýzy
V **externí služby nastavení**, **povolit Analysis postojích** políčko umožňuje LEOŠ k integraci s [Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) zajistit postojích a klíče fráze analýza. Není nutné zadat klíč Analýza textu a je bezplatná fakturace pro tuto službu ke svému účtu Azure. Jakmile zaškrtnete toto nastavení, je trvalé. 

Postojích dat je skóre mezi 1 a 0 označující pozitivní (blíže 1) ani na zápornou (blíže 0) postojích data.

Další informace o odpovědi JSON koncového bodu postojích analýzy najdete v tématu [postojích analýzy](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Povolit dočištění řeči 
V **externí služby nastavení**, **povolit Priming řeči** zaškrtávacího políčka můžete mít jeden koncový bod získat mluvené utterance z chatbot nebo LEOŠ volání aplikace a přijímat LEOŠ odpověď předpovědi. Rozpoznávání řeči dočištění používá službu kognitivní [rozhraní API pro rozpoznávání řeči](../Speech-Service/rest-apis.md). 

![Obrázek dialogového okna potvrzení dočištění řeči](./media/luis-how-to-publish-app/speech-prime-modal.png)

Po povolení této funkce publikování aplikace. Při publikování aplikace LEOŠ modelu aplikace je odeslán služby řeči pro primární řeči služby. Informace o modelu **není** použít mimo vaši vlastní službu. 

Aby bylo možné dokončit použití dočištění rozpoznávání řeči, potřebujete následující informace pro použití v [řeči SDK](../speech-service/speech-sdk-reference.md):
* Klíč předplatného LEOŠ.
* ID LEOŠ aplikace.
* Doméně koncový bod označuje jako "Název hostitele" v sadě SDK pro rozpoznávání řeči, jako je například "westus.api.cognitive.microsoft.com", kde je první subdomény oblasti, kde je publikovaná aplikace.

Další informace najdete v tématu [převod řeči na záměr](http://aka.ms/speechsdk) ukázka.

Pokud vaše aplikace LEOŠ odstraněna nebo službu řeči je odstranit, odeberou se data modelu. 

## <a name="endpoint-url-settings"></a>Nastavení adresy URL koncového bodu
Zahrnout nastavení služby adresy URL koncového bodu **[časové pásmo](#set-timezone-offset)** posun,  **[všechny předpovědět záměrné skóre](#include-all-predicted-intent-scores)**, a  **[ Kontrola pravopisu v Bingu](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Nastavte posun časového pásma 
Součást volba slotu je výběru časového pásma. Toto nastavení časového pásma umožňuje LEOŠ k [alter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) kdykoli předem datetimeV2 hodnoty během předpovědi, aby byla data vrácenou entitu správné podle vybraného časového pásma. 

### <a name="include-all-predicted-intent-scores"></a>Zahrnout všechny předpokládaných záměrné skóre
**Zahrnout všechny předpovědět záměrné skóre** políčko umožňuje odpovědi na dotaz koncový bod zahrnout předpovědi skóre pro každý záměr. 

Toto nastavení umožňuje chatbot nebo LEOŠ volání aplikace k programovým rozhodnutí podle skóre vrácený tříd Intent. Obecně jsou nejvíce zajímavé nejvyšší dvou tříd Intent. Pokud hlavní skóre je záměrné, že vaše chatbot můžete k zadání následné dotazu, který poskytuje spolehlivý možnost výběru mezi záměrné žádné a vysokým skóre záměr None. 

Tříd Intent a jejich výsledky jsou také zahrnuté v protokolech koncový bod. Můžete [exportovat](create-new-app.md#export-app) tyto protokoly a analyzovat skóre. 

```
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Povolit kontrolu pravopisu v Bingu 
V **nastavení adresy url koncového bodu**, **kontrola pravopisu povolit Bing** políčko umožňuje LEOŠ opravit slovo obsahující chyby před předpovědi. To vyžaduje, abyste vytvořili  **[kontrola pravopisu Bing klíč](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. Po vytvoření klíče dva parametry řetězce dotazu se přidají do adresu URL koncového bodu na stránce publikovat. 

Pokud jsou vytváření vlastní adresy URL pro aplikaci LEOŠ volání, zkontrolujte **kontrola pravopisu = true** parametr řetězce dotazu a **bing pravopisu – kontrola subscription-key = {YOUR_BING_KEY_HERE}**. Nahraďte `{YOUR_BING_KEY_HERE}` váš klíč pro kontrolu pravopisu Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Publikujte aplikaci vyškolení koncový bod HTTP
Otevřete aplikaci klikněte na jeho název **Moje aplikace** a pak klikněte na tlačítko **publikovat** v horním panelu. 

![Publikovat stránku –](./media/luis-how-to-publish-app/publish-to-production.png)
 
Pokud vaše aplikace je úspěšně publikována, zobrazí se upozornění zelená úspěch v horní části prohlížeče. 

* Vyberte, zda chcete publikovat do **produkční** nebo **pracovní** výběrem z rozevírací nabídky v části **vyberte slotu**. 

## <a name="assign-key"></a>Přiřazení klíče

Pokud chcete používat klíč jiné než volné Starter_Key zobrazí, klikněte na **přidat klíč** tlačítko. Tím se otevře dialogové okno, které vám umožní vybrat existující klíč koncového bodu přiřadit k aplikaci. Další informace o tom, jak vytvořit a přidat koncový bod klíče do aplikace LEOŠ najdete v tématu [spravovat klíče](Manage-Keys.md).

Koncové body a klíče přidružené jiných oblastí najdete pomocí přepínačů, chcete-li přepnout oblasti. Každý řádek **prostředky a klíče** tabulce jsou uvedeny prostředky Azure, které jsou přidružené k účtu a klíče koncový bod přidružené k danému prostředku.

## <a name="endpoint-url-construction"></a>Konstrukce adresu URL koncového bodu
Adresa URL koncového bodu odpovídá oblast Azure, které jsou přidružené ke klíči koncový bod.

Tato tabulka pohodlně odráží publikování konfigurace adresy URL koncového bodu se volby trasy a hodnoty řetězců dotazu. Pokud jsou vytváření váš koncový bod adresy URL pro aplikaci LEOŠ volání, zajistěte, aby tyto hodnoty jsou nastavené pro koncový bod stejné trasy a řetězce dotazu – Pokud je chcete nastavit.

Trasu adresy URL je vytvořený pomocí oblast a ID aplikace. Při publikování v jiných oblastech nebo s jinými aplikacemi, konstruovat adresu URL koncového bodu změnou hodnoty ID oblasti a aplikace. 

* Vyberte produkční slot a **publikovat** tlačítko. Při publikování úspěšné, použijte adresu URL zobrazené koncový bod pro přístup k aplikaci LEOŠ. 

### <a name="optional-query-string-parameters"></a>Parametrů řetězce dotazu volitelné
Následující parametrů řetězce dotazu lze použít s adresou URL koncového bodu:

<!-- TBD: what about speech priming? -->

|Řetězec dotazu|Typ|Příklad hodnoty|Účel|
|--|--|--|--|
|Verbose|Boolean|true (pravda)|Zahrnout [všechny záměrné skóre](#include-all-predicted-intent-scores) pro utterance|
|timezoneOffset|číslo (jednotka je minut)|60|Nastavit [posun časového pásma](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) pro [datetimeV2 předem entity](luis-reference-prebuilt-entities.md#builtindatetimev2)|
|Kontrola pravopisu|Boolean|true (pravda)|[Opravte pravopis](#enable-bing-spell-checker) z utterance – používá ve spojení s parametr řetězce dotazu bing pravopisu – kontrola subscription-key|
|Bing pravopisu – kontrola subscription-key|ID předplatného||používá ve spojení s parametr řetězce dotazu kontrola pravopisu|
|Pracovní|Boolean|false (nepravda)|Vyberte pracovním nebo produkčním koncový bod|
|Protokolu|Boolean|true (pravda)|Přidání dotazu a výsledky do protokolu|


## <a name="test-your-published-endpoint-in-a-browser"></a>Testování publikované koncový bod v prohlížeči
Testování publikované koncový bod výběrem adresu URL v **koncový bod** sloupce. Výchozí prohlížeč otevře s vygenerovaná adresa URL. Nastavte parametr adresy URL, "& q" do testovací dotazu. Například připojit `&q=Book me a flight to Boston on May 4` na adresu URL a potom stiskněte klávesu Enter. Prohlížeč zobrazí odpověď JSON váš koncový bod HTTP. 

![Odpověď JSON z publikovaných koncový bod HTTP](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>Další postup

* V tématu [Správa klíčů](./Manage-Keys.md) přidání klíče do LEOŠ aplikace a další informace o mapování klíče do oblasti.
* V tématu [Train a testování aplikace](Train-Test.md) pokyny k testování publikované aplikace v konzole nástroje test.
