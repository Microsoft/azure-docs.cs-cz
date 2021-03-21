---
title: Prozkoumat doporučení zabezpečení
description: Prozkoumejte bezpečnostní doporučení pomocí služby Defender for IoT Security.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: shhazam
ms.openlocfilehash: 0e902db38e4145bf94ab6a235bc1210b520327a1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99809180"
---
# <a name="quickstart-investigate-security-recommendations"></a>Rychlý Start: zkoumání doporučení zabezpečení


Aktuální analýza a zmírnění doporučení v programu Defender pro IoT je nejlepší způsob, jak vylepšit stav zabezpečení a omezit tak možnosti útoku v rámci vašeho řešení IoT.

V tomto rychlém startu prozkoumáme informace, které jsou k dispozici v jednotlivých doporučeních zabezpečení IoT, a vysvětlete, jak přejít k podrobnostem a použít podrobnosti o jednotlivých doporučeních a souvisejících zařízeních, abyste snížili riziko.

Tak se do toho pusťme.

## <a name="investigate-new-recommendations"></a>Prozkoumat nová doporučení

Seznam doporučení IoT Hub obsahuje všechna agregovaná doporučení zabezpečení pro vaši IoT Hub.

1.  V Azure Portal otevřete **IoT Hub** chcete prozkoumat nová doporučení.

1.  V nabídce **zabezpečení** vyberte **doporučení**. Zobrazí se všechna doporučení zabezpečení pro IoT Hub a doporučení s **novým** příznakem označují doporučení za posledních 24 hodin. 

    :::image type="content" source="media/quickstart/investigate-security-recommendations-expanded.png#lightbox" alt-text="Prozkoumat doporučení zabezpečení pomocí ASC pro IoT] (Media/rychlý Start/investigate-security-recommendations-inline.png)":::


1.  Výběrem a otevřením všech doporučení ze seznamu otevřete podrobnosti doporučení a přejdete k podrobnostem o konkrétní možnosti.

## <a name="security-recommendation-details"></a>Podrobnosti o doporučení zabezpečení

Otevřete všechna agregovaná doporučení pro zobrazení podrobného popisu doporučení, nápravných kroků, ID zařízení pro každé zařízení, které vyvolalo doporučení. Zobrazuje také závažnost doporučení a přímý přístup k šetření pomocí Log Analytics.

1.  V seznamu **IoT Hub**  >    >  **doporučení** zabezpečení vyberte a otevřete jakékoli doporučení zabezpečení.

1.  Přečtěte si **Popis** doporučení, **závažnost**, **Podrobnosti o zařízení** všech zařízení, která toto doporučení vystavila v rámci agregovaného období. 

1.  Po kontrole specifických doporučení použijte pokyny k **Ruční nápravě** , které vám pomůžou opravit a vyřešit problém, který toto doporučení způsobil. 

    :::image type="content" source="media/quickstart/remediate-security-recommendations-inline.png" alt-text="Náprava doporučení zabezpečení pomocí ASC pro IoT" lightbox="media/quickstart/remediate-security-recommendations-expanded.png":::

1.  V podrobnostech o doporučeních pro konkrétní zařízení vyberte požadované zařízení na stránce s podrobnostmi o prozkoumání.

    :::image type="content" source="media/quickstart/explore-security-recommendation-detail-inline.png" alt-text="Prozkoumat konkrétní doporučení zabezpečení pro zařízení pomocí ASC pro IoT" lightbox="media/quickstart/explore-security-recommendation-detail-expanded.png":::

1.  Pokud se vyžaduje další šetření, **Prozkoumejte doporučení v Log Analytics** pomocí odkazu. 

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak vytvářet vlastní výstrahy...

> [!div class="nextstepaction"]
> [Vytváření vlastních výstrah](quickstart-create-custom-alerts.md)
