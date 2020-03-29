---
title: Publikovat aplikaci – LUIS
titleSuffix: Azure Cognitive Services
description: Po dokončení vytváření a testování aktivní aplikace LUIS, zpřístupnit ji pro klientskou aplikaci publikováním do koncového bodu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: c91a3ca73d70dd5fd2848bed0f43f14a817087d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053440"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikování aktivní, trénované aplikace do pracovního nebo produkčního koncového bodu

Po dokončení vytváření, školení a testování aktivní aplikace LUIS, zpřístupnit ji pro klientskou aplikaci publikováním do koncového bodu. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="publishing"></a>Publikování

1. Pokud chcete publikovat do koncového bodu, vyberte **Publikovat** v horním pravém panelu. 

    ![Tlačítko Publikovat v horním pravém panelu navirtuálním panelu](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Vyberte nastavení pro publikovaný koncový bod předpovědi a pak vyberte **Publikovat**.

    ![Vyberte nastavení publikování a vyberte tlačítko Publikovat.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Publikování slotů

Při zobrazení vyskakovacího okna vyberte správnou patici: 

* Příprava
* Výroba 

Pomocí obou slotů pro publikování to umožňuje mít dvě různé verze aplikace k dispozici na publikovaných koncových bodech nebo stejnou verzi na dvou různých koncových bodech. 

### <a name="publishing-regions"></a>Publikování oblastí

Aplikace se publikuje do všech oblastí přidružených ke koncovým bodům předpovědi LUIS, které jsou přidány na portálu LUIS ze stránky **Spravovat** -> **[prostředky Azure.](luis-how-to-azure-subscription.md#assign-a-resource-to-an-app)** 

Například pro aplikaci vytvořenou na [www.luis.ai](https://www.luis.ai), pokud vytvoříte prostředek LUIS ve dvou oblastech, **westus** a **eastus**a přidáte je do aplikace jako prostředky, aplikace se publikuje v obou oblastech. Další informace o oblastech služby LUIS naleznete v [tématu Oblasti](luis-reference-regions.md).

> [!TIP]
> Existují 3 vývojové oblasti. V oblasti, do které chcete publikovat, je nutné vytvořit autora. Pokud potřebujete publikovat do všech oblastí, musíte spravovat proces vytváření a výsledný trénovaný model ve všech 3 oblastech vytváření. 


## <a name="configuring-publish-settings"></a>Konfigurace nastavení publikování

Po výběru slotu nakonfigurujte nastavení publikování pro:

* Analýza mínění
* Oprava pravopisu - pouze koncový bod predikce v2
* Řečové priming 

Po publikování jsou tato nastavení k dispozici ke kontrole na stránce **Nastavení publikování** v části **Správa.** Nastavení můžete změnit při každém publikování. Pokud publikování zrušíte, budou zrušeny také všechny změny provedené během publikování. 

### <a name="when-your-app-is-published"></a>Při publikování aplikace

Po úspěšném publikování aplikace se v horní části prohlížeče zobrazí oznámení o úspěchu. Oznámení také obsahuje odkaz na koncové body. 

Pokud potřebujete adresu URL koncového bodu, vyberte odkaz. K adresám URL koncového bodu se taky dostanete tak, že v horní nabídce **vyberete Spravovat** a v levé nabídce vyberete **Prostředky Azure.** 

## <a name="sentiment-analysis"></a>Analýza mínění

<a name="enable-sentiment-analysis"></a>

Analýza mínění umožňuje luis integrovat s [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) poskytovat analýzu mínění a klíčové fráze. 

Není třeba zadat klíč Analýzy textu a za tuto službu se účtu Azure neúčtuje žádná fakturační účtová faktura. 

Data mínění je skóre mezi 1 a 0 označující kladné (blíže k 1) nebo negativní (blíže k 0) mínění dat. Popisek mínění `positive` `neutral`, `negative` , a je podle podporované jazykové verze. V současné době pouze angličtina podporuje popisky mínění. 

Další informace o odezvě koncového bodu JSON s analýzou mínění naleznete v [tématu Analýza mínění](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Oprava pravopisu

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

Opravy pravopisu jsou provedeny před předpovědí utterance uživatele LUIS. V odpovědi můžete zobrazit všechny změny původního utterance, včetně pravopisu.

## <a name="speech-priming"></a>Řečové priming

Priming řeči je proces odesílání modelu LUIS na řeči služby před převodem textu na řeč. To umožňuje službě řeči poskytovat převod řeči přesněji pro váš model. To umožňuje bot řeči a LUIS požadavky a odpovědi v jednom volání tím, že jeden volání řeči a získání zpět odpověď LUIS. Poskytuje celkově menší latenci.

## <a name="next-steps"></a>Další kroky

* Viz [Správa klíčů](./luis-how-to-azure-subscription.md) pro přidání klíčů do klíče předplatného Azure luis a jak nastavit klíč kontrola pravopisu Bingu a zahrnout všechny záměry ve výsledcích.
* Pokyny k testování publikované aplikace v testovací konzoli najdete v tématu [Trénování a testování aplikace.](luis-interactive-test.md)

