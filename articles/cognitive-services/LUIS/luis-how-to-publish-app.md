---
title: Publikování aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Po dokončení vytváření a testování aplikace LUIS aktivní, zpřístupní ji pro klientské aplikace ji publikujete do koncového bodu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: f92776072038c5684e9334d2dda1690ebb7bcaa8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257786"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikujte svou aktivní, školenou aplikaci do pracovního nebo produkčního koncového bodu.

Po dokončení vytváření a testování aplikace LUIS aktivní, zpřístupní ji pro klientské aplikace ji publikujete do koncového bodu. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publikování

1. Chcete-li publikovat koncový bod, vyberte **publikovat** v horní části, klikněte pravým tlačítkem myši panelu. 

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

Například pro aplikaci vytvořenou v [www.Luis.AI](https://www.luis.ai), pokud vytvoříte prostředek Luis ve dvou oblastech, **westus** a **eastus**a přidáte je do aplikace jako prostředky, aplikace se publikuje v obou oblastech. Další informace o oblastech LUIS, naleznete v tématu [oblastech](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Konfigurace nastavení publikování

Po výběru slotu nakonfigurujte nastavení publikování pro:

* Analýza mínění
* Oprava pravopisu
* Neprojevení řeči 

Po publikování jsou tato nastavení dostupná ke kontrole na stránce **Správa** **Nastavení publikování** oddílu. Nastavení můžete změnit při každém publikování. Pokud zrušíte publikování, všechny změny, které jste provedli během publikování, se také zruší. 

### <a name="when-your-app-is-published"></a>Při publikování aplikace

Pokud vaše aplikace se úspěšně publikuje, zobrazí se upozornění zelené úspěch v horní části stránky prohlížeče. Zelená oznamovací pruh také obsahuje odkaz na koncové body. 

![Publikování automaticky otevíraného okna s odkazem na koncový bod](./media/luis-how-to-publish-app/publish-success.png)

Pokud budete potřebovat adresu URL koncového bodu, vyberte odkaz. K adresám URL koncových bodů se můžete dostat taky tak, že v horní nabídce vyberete **Spravovat** a v nabídce vlevo vyberete **prostředky Azure** . 

## <a name="sentiment-analysis"></a>Analýza mínění

<a name="enable-sentiment-analysis"></a>

Analýza subjektivního hodnocení umožňuje LUIS můžete integrovat s [rozhraní Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) poskytnout frázi analýzu subjektivního hodnocení a klíč. 

Není potřeba zadat klíč pro analýzu textu a nic neplatíte fakturace pro tuto službu ke svému účtu Azure. 

Je skóre mezi 1 a 0 označující pozitivní mínění data (blíže 1) ani na zápornou (blíže 0) mínění data. Popisek `positive`mínění, `neutral`a `negative` je na podporovanou jazykovou verzi. V současné době podporuje popisky mínění jenom angličtina. 

Další informace o odpovědi JSON koncového bodu s analýzou mínění, naleznete v tématu [analýzu subjektivního hodnocení](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="spelling-correction"></a>Oprava pravopisu

Opravy pro kontrolu pravopisu se provádí před předvídáním LUIS uživatele utterance. V odpovědi můžete zobrazit všechny změny v původních utterance, včetně pravopisu.

## <a name="speech-priming"></a>Neprojevení řeči

Dopřed konverze textu na řeč je proces použití funkce posílání LUIS modelu na hlasové služby před převodem textu na řeč. Díky tomu může služba Speech Service pro váš model přesněji poskytovat převod řeči. To umožňuje, aby se v jednom volání LUIS robot a žádosti a odpovědi vyvolaly jedno volání řeči a vrátilo zpět odpověď LUIS. Poskytuje méně latence celkově.

## <a name="next-steps"></a>Další kroky

* Zobrazit [spravovat klíče](./luis-how-to-azure-subscription.md) přidání klíče do předplatného Azure klíč k LUIS a jak nastavit kontrolu pravopisu Bingu klíče a zahrnout všechny příkazy ve výsledcích.
* Zobrazit [trénování a testování vaší aplikace](luis-interactive-test.md) pokyny o tom, jak v testovací konzole testování publikované aplikace.

