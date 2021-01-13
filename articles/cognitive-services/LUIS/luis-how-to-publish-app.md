---
title: Publikování aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Až dokončíte sestavování a testování vaší aktivní aplikace LUIS, zpřístupněte ji klientské aplikaci tím, že ji publikujete do koncového bodu.
services: cognitive-services
author: aahill
manager: nitinme
ms.author: aahi
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 8db0f5fa39c7f489db0e30e98ee2684c74eee7e8
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98180026"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikujte svou aktivní, školenou aplikaci do pracovního nebo produkčního koncového bodu.

Po dokončení sestavování, školení a testování vaší aktivní aplikace LUIS zpřístupněte ji klientské aplikaci tím, že ji publikujete do koncového bodu.

## <a name="publishing"></a>Publikování
1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Pokud chcete publikovat do koncového bodu, vyberte **publikovat** v horním a pravém panelu.

    ![Tlačítko publikovat v horním a pravém navigačním panelu](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Vyberte nastavení pro publikovaný koncový bod předpovědi a pak vyberte **publikovat**.

    ![Vyberte publikovat nastavení a pak vyberte tlačítko publikovat.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Sloty publikování

Po zobrazení automaticky otevíraného okna vyberte správný slot:

* Příprava
* Provoz

Pomocí obou slotů pro publikování vám to umožňuje mít v publikovaných koncových bodech k dispozici dvě různé verze aplikace nebo stejnou verzi ve dvou různých koncových bodech.

### <a name="publishing-regions"></a>Publikování oblastí

Aplikace se publikuje do všech oblastí přidružených k prostředkům koncového bodu předpovědi Luis přidaným na portálu Luis ze stránky **Správa**  ->  **[prostředků Azure](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** .

Například pro aplikaci vytvořenou v [www.Luis.AI](https://www.luis.ai), pokud vytvoříte prostředek Luis ve dvou oblastech, **westus** a **eastus** a přidáte je do aplikace jako prostředky, aplikace se publikuje v obou oblastech. Další informace o oblastech LUIS najdete v tématu [oblasti](luis-reference-regions.md).

> [!TIP]
> Existují tři oblasti vytváření obsahu. Musíte vytvořit oblast, do které chcete publikovat. Pokud potřebujete publikovat ve všech oblastech, je nutné spravovat proces vytváření obsahu a výsledný model vyškolený ve všech třech oblastech vytváření obsahu.


## <a name="configuring-publish-settings"></a>Konfigurace nastavení publikování

Po výběru slotu nakonfigurujte nastavení publikování pro:

* Analýza mínění
* [Oprava pravopisu](luis-tutorial-bing-spellcheck.md)
* Neprojevení řeči

Po publikování jsou tato nastavení dostupná ke kontrole na stránce **Správa** **Nastavení publikování** oddílu. Nastavení můžete změnit při každém publikování. Pokud zrušíte publikování, všechny změny, které jste provedli během publikování, se také zruší.

### <a name="when-your-app-is-published"></a>Při publikování aplikace

Po úspěšném publikování vaší aplikace se v horní části prohlížeče zobrazí oznámení o úspěchu. Oznámení také obsahuje odkaz na koncové body.

Pokud potřebujete adresu URL koncového bodu, vyberte odkaz. K adresám URL koncových bodů se můžete dostat taky tak, že v horní nabídce vyberete **Spravovat** a v nabídce vlevo vyberete **prostředky Azure** .

## <a name="sentiment-analysis"></a>Analýza mínění

<a name="enable-sentiment-analysis"></a>

Analýza mínění umožňuje integraci LUIS s [Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) , aby poskytovala mínění a analýzu klíčových frází.

Nemusíte zadávat Analýza textu klíč a za tuto službu se neúčtují žádné poplatky za váš účet Azure.

Mínění data jsou skóre mezi 1 a 0 značící kladné (blíže k 1) nebo záporné (navýšení na 0) mínění dat. Popisek mínění `positive` , `neutral` a `negative` je na podporovanou jazykovou verzi. V současné době podporuje popisky mínění jenom angličtina.

Další informace o odpovědích koncových bodů JSON s analýzou mínění najdete v tématu [Analýza mínění](luis-reference-prebuilt-sentiment.md) .

## <a name="spelling-correction"></a>Oprava pravopisu

Prediktivní rozhraní API V3 teď podporuje rozhraní API pro kontrolu pravopisu Bingu. Do své aplikace můžete přidat kontrolu pravopisu, a to tak, že do hlavičky vašich požadavků přidáte klíč do svého prostředku vyhledávání Bingu. Můžete použít existující prostředek Bingu, pokud už vlastníte, nebo [vytvořit nový](https://portal.azure.com/#create/Microsoft.BingSearch) , abyste mohli tuto funkci používat. 

|Klíč záhlaví|Hodnota hlavičky|
|--|--|
|`mkt-bing-spell-check-key`|Klíče nalezené v okně **klíče a koncový bod** vašeho prostředku|

Příklad výstupu předpovědi pro nesprávně napsaný dotaz:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

Opravy pro kontrolu pravopisu se provádí před předvídáním LUIS uživatele utterance. V odpovědi můžete zobrazit všechny změny v původních utterance, včetně pravopisu.

## <a name="speech-priming"></a>Neprojevení řeči

Dopřed konverze textu na řeč je proces použití funkce posílání LUIS modelu na hlasové služby před převodem textu na řeč. Díky tomu může služba Speech Service pro váš model přesněji poskytovat převod řeči. To umožňuje, aby se v jednom volání LUIS robot a žádosti a odpovědi vyvolaly jedno volání řeči a vrátilo zpět odpověď LUIS. Poskytuje méně latence celkově.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [Správa klíčů](./luis-how-to-azure-subscription.md) a přidání klíčů do klíče předplatného Azure do Luis a jak nastavit klíč kontrola pravopisu Bingu a zahrnout všechny záměry do výsledků.
* Pokyny k otestování publikované aplikace v konzole testu najdete v tématu [výuka a testování vaší aplikace](luis-interactive-test.md) .

