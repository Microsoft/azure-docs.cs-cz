---
title: Publikování aplikace
titleSuffix: Azure Cognitive Services
description: Po dokončení vytváření a testování aplikace LUIS aktivní, zpřístupní ji pro klientské aplikace ji publikujete do koncového bodu.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: 027e1f3c7aa7eb19a40d2c4202cb4661bd3f41ae
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208218"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publikování aplikace aktivní, trénované na pracovní nebo produkční koncový bod

Po dokončení vytváření a testování aplikace LUIS aktivní, zpřístupní ji pro klientské aplikace ji publikujete do koncového bodu. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publikování

Chcete-li publikovat koncový bod, vyberte **publikovat** v horní části, klikněte pravým tlačítkem myši panelu. 

![Horní, pravé navigačního panelu](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

Jakmile se zobrazí v automaticky otevíraném okně vyberte správné slotu: pracovní nebo produkční prostředí. Pomocí dvou publikování sloty díky tomu budete mít dvě různé verze s koncovými body publikované nebo stejnou verzi na dvou různých koncových bodů. 

Publikování aplikace do všech oblastí, které jsou spojené s prostředky služby LUIS přidat na portálu služby LUIS. Například aplikace vytvořená v [www.luis.ai](https://www.luis.ai), pokud vytvoříte prostředek LUIS v **westus** a přidejte ho do aplikace jako prostředek, publikování aplikace v dané oblasti. Další informace o oblastech LUIS, naleznete v tématu [oblastech](luis-reference-regions.md).
 
![Publikování automaticky otevíraném okně](./media/luis-how-to-publish-app/publish-pop-up.png)

Pokud vaše aplikace se úspěšně publikuje, zobrazí se upozornění zelené úspěch v horní části stránky prohlížeče. Zelená oznamovací pruh také obsahuje odkaz na koncové body. 

![Publikování automaticky otevírané okno s odkazem na koncový bod](./media/luis-how-to-publish-app/publish-success.png)

Pokud budete potřebovat adresu URL koncového bodu, vyberte odkaz. Získáte taky k adresám URL, koncový bod tak, že vyberete **spravovat** v horní nabídce vyberte **klíče a koncových bodů** v levé nabídce. 

## <a name="configuring-publish-settings"></a>Konfigurace nastavení publikování

Konfigurace nastavení publikování tak, že vyberete **spravovat** v horní části, klikněte pravým tlačítkem myši navigaci a poté vyberete **nastavení publikování**. 

![Nastavení publikování](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>Publikování po povolení analýzy mínění

<a name="enable-sentiment-analysis"></a>

Analýza subjektivního hodnocení umožňuje LUIS můžete integrovat s [rozhraní Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) poskytnout frázi analýzu subjektivního hodnocení a klíč. 

Není potřeba zadat klíč pro analýzu textu a nic neplatíte fakturace pro tuto službu ke svému účtu Azure. Když zaškrtnete toto nastavení, je trvalé. 

Je skóre mezi 1 a 0 označující pozitivní mínění data (blíže 1) ani na zápornou (blíže 0) mínění data. Popisek mínění `positive`, `neutral`, a `negative` se děje u každého podporované jazykové verze. V současné době podporuje jenom v angličtině mínění popisky. 

Další informace o odpovědi JSON koncového bodu s analýzou mínění, naleznete v tématu [analýzu subjektivního hodnocení](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="next-steps"></a>Další postup

* Zobrazit [spravovat klíče](./luis-how-to-azure-subscription.md) přidání klíče do předplatného Azure klíč k LUIS a jak nastavit kontrolu pravopisu Bingu klíče a zahrnout všechny příkazy ve výsledcích.
* Zobrazit [trénování a testování vaší aplikace](luis-interactive-test.md) pokyny o tom, jak v testovací konzole testování publikované aplikace.

