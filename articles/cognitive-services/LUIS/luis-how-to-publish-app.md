---
title: Publikování aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Až dokončíte sestavování a testování vaší aktivní aplikace LUIS, zpřístupněte ji klientské aplikaci tím, že ji publikujete do koncového bodu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 0d3a413249cb9058e4098f2836131494670a1727
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491331"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikujte svou aktivní, školenou aplikaci do pracovního nebo produkčního koncového bodu.

Až dokončíte sestavování a testování vaší aktivní aplikace LUIS, zpřístupněte ji klientské aplikaci tím, že ji publikujete do koncového bodu. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publikování

1. Pokud chcete publikovat do koncového bodu, vyberte **publikovat** v horním a pravém panelu. 

    ![Tlačítko publikovat v horním a pravém navigačním panelu](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Vyberte nastavení pro publikovaný koncový bod předpovědi a pak vyberte **publikovat**.

    ![Vyberte publikovat nastavení a pak vyberte tlačítko publikovat.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Sloty publikování

Po zobrazení automaticky otevíraného okna vyberte správný slot: 

* Přednastavení.
* Produkční. 

Pomocí obou slotů pro publikování vám to umožňuje mít v publikovaných koncových bodech k dispozici dvě různé verze aplikace nebo stejnou verzi ve dvou různých koncových bodech. 

### <a name="publishing-regions"></a>Publikování oblastí

Aplikace je publikovaná ve všech oblastech přidružených k prostředkům koncového bodu předpovědi LUIS přidaným na portálu LUIS. 

Například pro aplikaci vytvořenou v [www.Luis.AI](https://www.luis.ai), pokud vytvoříte prostředek Luis ve dvou oblastech, **westus** a **eastus**a přidáte je do aplikace jako prostředky, aplikace se publikuje v obou oblastech. Další informace o oblastech LUIS najdete v tématu [oblasti](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Konfigurace nastavení publikování

Po výběru slotu nakonfigurujte nastavení publikování pro:

* Analýza subjektivního hodnocení
* Oprava pravopisu
* Neprojevení řeči 

Po publikování jsou tato nastavení dostupná ke kontrole na stránce **Správa** **Nastavení publikování** oddílu. Nastavení můžete změnit při každém publikování. Pokud zrušíte publikování, všechny změny, které jste provedli během publikování, se také zruší. 

### <a name="when-your-app-is-published"></a>Při publikování aplikace

Po úspěšném publikování vaší aplikace se v horní části prohlížeče zobrazí zelená zpráva o úspěchu. Zelený oznamovací pruh obsahuje také odkaz na koncové body. 

![Publikování automaticky otevíraného okna s odkazem na koncový bod](./media/luis-how-to-publish-app/publish-success.png)

Pokud potřebujete adresu URL koncového bodu, vyberte odkaz. K adresám URL koncových bodů se můžete dostat taky tak, že v horní nabídce vyberete **Spravovat** a v nabídce vlevo vyberete **prostředky Azure** . 

## <a name="sentiment-analysis"></a>Analýza subjektivního hodnocení

<a name="enable-sentiment-analysis"></a>

Analýza mínění umožňuje integraci LUIS s [Analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/) , aby poskytovala mínění a analýzu klíčových frází. 

Nemusíte zadávat Analýza textu klíč a za tuto službu se neúčtují žádné poplatky za váš účet Azure. 

Mínění data jsou skóre mezi 1 a 0 značící kladné (blíže k 1) nebo záporné (navýšení na 0) mínění dat. Popisek mínění `positive`, `neutral`a `negative` je na podporovanou jazykovou verzi. V současné době podporuje popisky mínění jenom angličtina. 

Další informace o odpovědích koncových bodů JSON s analýzou mínění najdete v tématu [Analýza mínění](luis-concept-data-extraction.md#sentiment-analysis) .

## <a name="spelling-correction"></a>Oprava pravopisu

Opravy pro kontrolu pravopisu se provádí před předvídáním LUIS uživatele utterance. V odpovědi můžete zobrazit všechny změny v původních utterance, včetně pravopisu.

## <a name="speech-priming"></a>Neprojevení řeči

Dopřed konverze textu na řeč je proces použití funkce posílání LUIS modelu na hlasové služby před převodem textu na řeč. Díky tomu může služba Speech Service pro váš model přesněji poskytovat převod řeči. To umožňuje, aby se v jednom volání LUIS robot a žádosti a odpovědi vyvolaly jedno volání řeči a vrátilo zpět odpověď LUIS. Poskytuje méně latence celkově.

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [Správa klíčů](./luis-how-to-azure-subscription.md) a přidání klíčů do klíče předplatného Azure do Luis a jak nastavit klíč kontrola pravopisu Bingu a zahrnout všechny záměry do výsledků.
* Pokyny k otestování publikované aplikace v konzole testu najdete v tématu [výuka a testování vaší aplikace](luis-interactive-test.md) .

