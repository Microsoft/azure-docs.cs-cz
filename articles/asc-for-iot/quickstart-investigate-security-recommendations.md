---
title: 'Rychlý Start: zkoumání doporučení zabezpečení'
description: Prozkoumejte bezpečnostní doporučení pomocí Azure Security Center služby zabezpečení IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2020
ms.author: mlottner
ms.openlocfilehash: aa241a9108be32f88357065732c6f283d312be6c
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2020
ms.locfileid: "88144205"
---
# <a name="quickstart-investigate-security-recommendations"></a>Rychlý Start: zkoumání doporučení zabezpečení


Azure Security Center pro IoT je nejlepší způsob, jak vylepšit stav zabezpečení a omezit tak možnosti útoku v rámci vašeho řešení IoT, je včasné analýza a zmírnění doporučení.

V tomto rychlém startu prozkoumáme informace, které jsou k dispozici v jednotlivých doporučeních zabezpečení IoT, a vysvětlete, jak přejít k podrobnostem a použít podrobnosti o jednotlivých doporučeních a souvisejících zařízeních, abyste snížili riziko.

Pusťme se do toho.

## <a name="investigate-new-recommendations"></a>Prozkoumat nová doporučení

Seznam doporučení IoT Hub obsahuje všechna agregovaná doporučení zabezpečení pro vaši IoT Hub.

1.  V Azure Portal otevřete **IoT Hub**chcete   prozkoumat nová doporučení.

1.  V nabídce **zabezpečení**   Vyberte **doporučení**. Zobrazí se všechna doporučení zabezpečení pro IoT Hub a doporučení s **novým**   příznakem označují doporučení za posledních 24 hodin. 

    [![Prozkoumejte doporučení zabezpečení pomocí ASC pro IoT](media/quickstart/investigate-security-recommendations-inline.png)](media/quickstart/investigate-security-recommendations-expanded.png#lightbox)


1.  Výběrem a otevřením všech doporučení ze seznamu otevřete podrobnosti doporučení a přejdete k podrobnostem o konkrétní možnosti.

## <a name="security-recommendation-details"></a>Podrobnosti o doporučení zabezpečení

Otevřete všechna agregovaná doporučení pro zobrazení podrobného popisu doporučení, nápravných kroků, ID zařízení pro každé zařízení, které vyvolalo doporučení. Zobrazuje také závažnost doporučení a přímý přístup k šetření pomocí Log Analytics.

1.  V seznamu **IoT Hub**   \>  **Security**   \>  **doporučení**zabezpečení vyberte a otevřete jakékoli doporučení zabezpečení   .

1.  Přečtěte si **Popis**doporučení, **závažnost**, **Podrobnosti o zařízení**   všech zařízení, která toto doporučení vystavila v rámci agregovaného období. 

1.  Po kontrole specifických doporučení použijte pokyny k **Ruční nápravě**, které   vám pomůžou opravit a vyřešit problém, který toto doporučení způsobil. 

    [![Náprava doporučení zabezpečení pomocí ASC pro IoT](media/quickstart/remediate-security-recommendations-inline.png)](media/quickstart/remediate-security-recommendations-expanded.png#lightbox)


1.  V podrobnostech o doporučeních pro konkrétní zařízení vyberte požadované zařízení na stránce s podrobnostmi o prozkoumání.

    [![Prozkoumat konkrétní doporučení zabezpečení pro zařízení pomocí ASC pro IoT](media/quickstart/explore-security-recommendation-detail-inline.png)](media/quickstart/explore-security-recommendation-detail-expanded.png#lightbox)


1.  Pokud se vyžaduje další šetření, **Prozkoumejte doporučení v Log Analytics**   pomocí odkazu. 


## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak vytvářet vlastní výstrahy...

> [!div class="nextstepaction"]
> [Vytváření vlastních výstrah](quickstart-create-custom-alerts.md)
