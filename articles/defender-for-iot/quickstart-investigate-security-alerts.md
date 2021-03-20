---
title: 'Rychlý Start: Prozkoumejte výstrahy zabezpečení'
description: Seznamte se s výstrahami zabezpečení IoT na zařízeních IoT a prozkoumejte je v programu Defender.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: 172ae82288c2cb948839b69955b9491715eb4690
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90946819"
---
# <a name="quickstart-investigate-security-alerts"></a>Rychlý Start: Prozkoumejte výstrahy zabezpečení

Toto je nejlepší způsob, jak zajistit dodržování předpisů a ochranu v rámci řešení IoT, a naplánovaných šetření a nápravy výstrah vydaných programem Defender pro IoT

V tomto rychlém startu prozkoumáme informace, které jsou dostupné v každé výstraze zabezpečení IoT, a vysvětlete, jak přejít k podrobnostem a použít podrobnosti o jednotlivých výstrahách a souvisejících zařízeních, abyste mohli reagovat a opravit. 

Tak se do toho pusťme. 


## <a name="investigate-new-security-alerts"></a>Prozkoumat nové výstrahy zabezpečení

Seznam výstrah zabezpečení IoT Hub zobrazuje všechny agregované výstrahy zabezpečení pro vaši IoT Hub. 

1. V Azure Portal otevřete **IoT Hub** chcete prozkoumat nové výstrahy.
1. V nabídce **zabezpečení** vyberte **výstrahy**. Zobrazí se všechny výstrahy zabezpečení pro IoT Hub a výstrahy s **novým** příznakem označují výstrahy za posledních 24 hodin.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Prozkoumat nové výstrahy zabezpečení IoT pomocí nového příznaku výstrahy":::
1. Výběrem a otevřením jakékoli výstrahy ze seznamu otevřete podrobnosti výstrahy a přejdete k podrobnostem týkajícím se výstrah. 

## <a name="security-alert-details"></a>Podrobnosti výstrahy zabezpečení

Při otevření každé agregované výstrahy se zobrazí podrobný popis výstrahy, postup nápravy, ID zařízení pro každé zařízení, které aktivovalo výstrahu, a také Závažnost výstrahy a přístup k přímému šetření pomocí Log Analytics. 

1. Vyberte a otevřete libovolnou výstrahu zabezpečení ze   >    >  seznamu **výstrahy** zabezpečení IoT Hub. 
1. Zkontrolujte **Popis** výstrahy, **závažnost**, **zdroj detekce**, **Podrobnosti o zařízení** , která tato výstraha vystavila v agregačním období.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Přechod k podrobnostem a kontrola podrobností jednotlivých zařízení v agregované výstraze "::: 
1. Po kontrole specifických výstrah použijte pokyny k **Ruční nápravě** , které vám pomůžou opravit a vyřešit problém, který výstrahu způsobil. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Postupujte podle kroků ruční nápravy, které vám pomůžou vyřešit nebo opravit výstrahy zabezpečení zařízení.":::

1. Pokud se vyžaduje další šetření, **Prozkoumejte výstrahy v Log Analytics** pomocí odkazu. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="K dalšímu prověření výstrahy použijte odkaz prozkoumat pomocí Log Analytics, který je k dispozici na obrazovce.":::

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte víc o typech výstrah a možných úpravách v programu Defender...

> [!div class="nextstepaction"]
> [Principy výstrah zabezpečení IoT](concept-security-alerts.md)
