---
title: Publikování aplikace LUIS | Dokumentace Microsoftu
description: Po sestavení a testování vaší aplikace s použitím služby Language Understanding (LUIS), můžete ji publikujte jako webovou službu v Azure.
services: cognitive-services
titleSuffix: Azure
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: d579ba8913eeb6ce22e277dab454ba7e3b628703
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171928"
---
# <a name="publish-your-trained-app"></a>Publikování trénovaného aplikace
Po dokončení vytváření a testování vaší aplikace LUIS, publikujte ho. Po publikování aplikace na stránce publikování se zobrazí všechny přidružené HTTP [koncové body](luis-glossary.md#endpoint). Tyto koncové body za [oblasti](luis-reference-regions.md) a za [klíč](luis-how-to-manage-keys.md), pak jsou integrované do libovolné aplikace klienta, chatovací robot nebo back-endu. 

Vždy můžete [testování](luis-interactive-test.md) vaši aplikaci před publikováním. 

## <a name="production-and-staging-slots"></a>Produkční a přípravné sloty
Můžete publikovat aplikaci tak, aby **přípravný slot** nebo **produkčního slotu**. Pomocí dvou publikování sloty díky tomu budete mít dvě různé verze s koncovými body publikované nebo stejnou verzi na dvou různých koncových bodů. 

<!-- TBD: what is the technical difference? log files, endpoint quota? -->

## <a name="settings-configuration-requires-publishing-model"></a>Konfigurace nastavení vyžaduje publikování modelu
Publikování do koncového bodu po změny následujícího nastavení. 

## <a name="external-services-settings"></a>Nastavení externí služby
Nastavení externí služby zahrnují **[analýzu subjektivního hodnocení](#enable-sentiment-analysis)** a  **[řeči Příprava](#enable-speech-priming)**.

### <a name="enable-sentiment-analysis"></a>Povolit analýzu subjektivního hodnocení
V **externí služby nastavení**, **povolit analýzu subjektivního hodnocení** LUIS můžete integrovat s umožňuje zaškrtávací políčko [rozhraní Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) poskytnout zabarvení a klíčové fráze analýzy. Není potřeba zadat klíč pro analýzu textu a nic neplatíte fakturace pro tuto službu ke svému účtu Azure. Když zaškrtnete toto nastavení, je trvalé. 

Je skóre mezi 1 a 0 označující pozitivní mínění data (blíže 1) ani na zápornou (blíže 0) mínění data.

Další informace o odpovědi JSON koncového bodu s analýzou mínění, naleznete v tématu [analýzu subjektivního hodnocení](luis-concept-data-extraction.md#sentiment-analysis)

### <a name="enable-speech-priming"></a>Povolit dočištění řeči 
V **externí služby nastavení**, **povolit Příprava řeči** umožňuje zaškrtávací políčko, abyste měli jeden koncový bod zajistit mluvené slovo utterance chatovací robot nebo LUIS volající aplikace a přijímat LUIS předpověď odpovědi. Rozpoznávání řeči dočištění používá služby Cognitive Services [rozhraní Speech API](../Speech-Service/rest-apis.md). 

![Obrázek řeči dočištění potvrzovací dialogové okno](./media/luis-how-to-publish-app/speech-prime-modal.png)

Jakmile je tato funkce povolena, publikujte aplikaci. Při publikování aplikace LUIS modelu aplikace posílá službě řeči vymazat Speech service. Informace o modelu **není** použít mimo vlastních služeb. 

Aby bylo možné dokončit použití dočištění řeči, budete potřebovat následující informace pro použití v [sadou SDK pro řeč](../speech-service/speech-sdk-reference.md):
* Klíč koncového bodu služby LUIS.
* ID aplikace LUIS
* Doménu koncového bodu označovány jako "Hostname" v sadě SDK řeči, jako je například "westus.api.cognitive.microsoft.com", kde první subdoménu je oblast, ve kterém je aplikace publikována.

Další informace najdete v tématu [řeč na záměr](http://aka.ms/speechsdk) vzorku.

Při odstranění aplikace LUIS nebo při odstranění služby řeči, vaše data modelu se odeberou. 

## <a name="endpoint-url-settings"></a>Nastavení adresy URL koncového bodu
Zahrnout nastavení služby koncového bodu adresy URL **[časové pásmo](#set-timezone-offset)** odsazení,  **[všechny předpovědět záměru skóre](#include-all-predicted-intent-scores)**, a  **[ Kontrola pravopisu Bingu](#enable-bing-spell-checker)**.

### <a name="set-timezone-offset"></a>Nastavit posun časového pásma 
Volba slot je výběr časového pásma. Toto nastavení časového pásma umožňuje LUIS [alter](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) kdykoli předem připravených datetimeV2 hodnoty během předpovědi tak, aby byla data vrácená entity správné podle vybraného časového pásma. 

### <a name="include-all-predicted-intent-scores"></a>Zahrnout všechny predikovaného hodnocení záměru
**Zahrnout všechny předpovědět záměru skóre** odpověď na dotaz koncový bod zahrnout skóre předpovědi pro každý záměru umožňuje zaškrtávací políčko. 

Toto nastavení umožňuje chatovací robot nebo LUIS volající aplikace pro programové rozhodnutí podle skóre vrácené záměry. Horní dva příkazy jsou obecně zajímá nejvíce. Pokud hlavní skóre již není žádný záměru, že váš robot můžete položit dotaz zpracování zajistíte, aby konečná možností volby mezi záměru žádný a vysokým skóre záměr. 

Příkazů a jejich výsledky jsou také zahrnuté protokoly koncového bodu. Je možné [exportovat](luis-how-to-start-new-app.md#export-app) tyto protokoly a analyzovat výsledky. 

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

### <a name="enable-bing-spell-checker"></a>Povolit kontrolu pravopisu Bingu 
V **nastavení adresy url koncového bodu**, **kontrolu pravopisu Bingu povolit** LUIS opravovat slova s překlepem před předpovědi umožňuje zaškrtávací políčko. Vytvoření  **[kontrolu pravopisu Bingu klíč](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. Po vytvoření klíče jsou přidány dva parametry řetězce dotazu na URL koncového bodu na stránce publikovat. 

Přidat **kontrola pravopisu = true** parametr querystring a **Bingu – pravopisu – kontrola subscription-key = {YOUR_BING_KEY_HERE}** . Nahradit `{YOUR_BING_KEY_HERE}` vaším klíčem kontrolu pravopisu Bingu.

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

## <a name="publish-your-trained-app-to-an-http-endpoint"></a>Publikovat aplikaci trénované na koncový bod HTTP
Otevření aplikace po kliknutí na jeho název **Moje aplikace** stránce a potom klikněte na tlačítko **publikovat** v horním panelu. 

![Publikovat stránku –](./media/luis-how-to-publish-app/publish-to-production.png)
 
Pokud vaše aplikace se úspěšně publikuje, zobrazí se upozornění zelené úspěch v horní části stránky prohlížeče. 

* Zvolte, jestli se má publikovat **produkční** nebo **pracovní** tak, že vyberete rozevírací nabídky v části **vyberte slot**. 

## <a name="assign-key"></a>Přiřazení klíče

Pokud chcete použít klíče jiných než bezplatné Starter_Key zobrazí, klikněte na tlačítko **přidat klíč** tlačítko. Tato akce otevře dialogové okno, které vám umožní vybrat existující klíč koncového bodu přiřadit k aplikaci. Další informace o tom, jak vytvořit a přidat do aplikace LUIS klíče koncového bodu, najdete v části [spravovat klíče](luis-how-to-manage-keys.md).

Koncové body a klíče přidružené k další oblasti zobrazíte pomocí přepínací tlačítka můžete přepínat oblastech. Každý řádek **prostředky a klíče** tabulce jsou uvedeny prostředky Azure, které jsou spojené s vaším účtem a koncový bod klíče přidružené k tomuto prostředku.

## <a name="endpoint-url-construction"></a>Konstrukce adresu URL koncového bodu
Adresa URL koncového bodu odpovídající oblast Azure, které jsou přidružené k klíče koncového bodu.

Tato tabulka zobrazuje pohodlně publikování konfigurace koncového bodu adresy URL s možností trasy a hodnot řetězce dotazu. Pokud váš koncový bod adresy URL se sestavením pro vaši aplikaci LUIS volání, ujistěte se, že tyto hodnoty jsou nastavené pro koncový bod tras a řetězec dotazu použít – pokud je chcete nastavit.

Adresa URL trasy je vytvořený pomocí oblast a ID aplikace. Pokud publikujete v jiných oblastech nebo s jinými aplikacemi, adresu URL koncového bodu lze sestavit tak, že změníte hodnoty ID oblasti a aplikace. 

* Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat). Při publikování bude úspěšné, použijte adresu URL zobrazené koncový bod pro přístup k aplikaci LUIS. 

### <a name="optional-query-string-parameters"></a>Parametry řetězce dotazu volitelné
Následující parametry řetězce dotazu jde použít s adresu URL koncového bodu:

<!-- TBD: what about speech priming? -->

|Řetězec dotazu|Typ|Příklad hodnoty|Účel|
|--|--|--|--|
|verbose|Boolean|true (pravda)|Zahrnout [všechny záměru skóre](#include-all-predicted-intent-scores) pro utterance|
|timezoneOffset|číslo (jednotka je minut)|60|Nastavte [posun časového pásma](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) pro [datetimeV2 předem připravených entit](luis-reference-prebuilt-datetimev2.md)|
|Kontrola pravopisu|Boolean|true (pravda)|[Opravte pravopis](#enable-bing-spell-checker) z utterance - použít ve spojení s parametrem řetězce dotazu Bingu – pravopisu – kontrola subscription-key|
|Bing pravopisu – kontrola subscription-key|ID předplatného||použít ve spojení s parametrem řetězce dotazu kontrola pravopisu|
|pracovní|Boolean|false (nepravda)|Vyberte pracovní nebo produkční koncový bod|
|protokol|Boolean|true (pravda)|Přidání dotazu a výsledky do protokolu|


## <a name="test-your-published-endpoint-in-a-browser"></a>Testování publikované koncový bod v prohlížeči
Testování publikované koncový bod tak, že vyberete na adresu URL v **koncový bod** sloupce. Výchozí prohlížeč otevře se vygenerovala adresa URL. Nastavte parametr adresy URL "& q" do dotazu testu. Například připojte `&q=Book me a flight to Boston on May 4` na adresu URL a potom stiskněte klávesu Enter. Prohlížeč zobrazí odpovědi JSON koncového bodu protokolu HTTP. 

![Odpověď JSON od publikované koncový bod HTTP](./media/luis-how-to-publish-app/luis-publish-app-json-response.png)

## <a name="next-steps"></a>Další postup

* Zobrazit [spravovat klíče](./luis-how-to-manage-keys.md) přidat klíče aplikace LUIS, a další informace o mapování klíčů do oblasti.
* Zobrazit [trénování a testování vaší aplikace](luis-interactive-test.md) pokyny o tom, jak v testovací konzole testování publikované aplikace.
