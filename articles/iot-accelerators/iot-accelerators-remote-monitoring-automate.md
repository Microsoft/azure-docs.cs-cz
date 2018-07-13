---
title: Detekce problémů se zařízeními v řešení vzdáleného monitorování založeném na Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak v řešení vzdáleného monitorování pomocí pravidel a akcí automaticky detekovat problémy se zařízeními na základě prahových hodnot.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 1e3eaeec1d2eae3c36f285a3e4c536657504cbb8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098477"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Kurz: Detekce problémů se zařízeními připojenými k řešení monitorování

V tomto kurzu nakonfigurujete akcelerátor řešení vzdáleného monitorování tak, aby detekoval problémy s připojenými zařízeními IoT. Problémy se zařízeními můžete detekovat tak, že přidáte pravidla, která generují upozornění na řídicím panelu řešení.

V tomto kurzu se k představení pravidel a upozornění používá simulované zařízení chladiče. Tento chladič spravuje organizace Contoso a je připojený k akcelerátoru řešení vzdáleného monitorování. Společnost Contoso už má pravidlo, které generuje kritické upozornění, když tlak v chladiči překročí 298 PSI. Jako operátor ve společnosti Contoso chcete identifikovat chladící zařízení, která můžou mít problematické senzory. Za tímto účelem budete hledat počáteční prudká zvýšení tlaku. Taková zařízení identifikujete prostřednictvím přidání pravidla, které generuje varovné upozornění, když tlak v chladiči překročí 150 PSI.

Také jste byli požádáni, abyste pro chladič vytvořili kritické upozornění v případě, že během posledních pěti minut byla průměrná vlhkost v zařízení vyšší než 80 % a teplota zařízení vyšší než 75 stupňů Fahrenheita.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Zobrazení pravidel v řešení
> * Vytvoření pravidla
> * Vytvoření pravidla s několika podmínkami
> * Úprava existujícího pravidla
> * Zapnutí a vypnutí pravidel

## <a name="prerequisites"></a>Požadavky

Abyste mohli postupovat podle tohoto kurzu, musíte ve svém předplatném Azure mít nasazenou instanci akcelerátoru řešení vzdáleného monitorování.

Pokud jste akcelerátor řešení vzdáleného monitorování ještě nenasadili, měli byste dokončit rychlý start [Nasazení cloudového řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md).

## <a name="view-the-existing-rules"></a>Zobrazení existujících pravidel

Na stránce **Pravidla** v akcelerátoru řešení se zobrazí seznam všech aktuálních pravidel:

[![Stránka Pravidla](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Pokud chcete zobrazit pouze pravidla, která se týkají chladících zařízení, použijte filtr:

[![Filtrování seznamu pravidel](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2-expanded.png#lightbox)

Výběrem pravidla v seznamu můžete zobrazit další informace o pravidle a upravit ho:

[![Zobrazení podrobností o pravidle](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

Pokud chcete zakázat nebo povolit jedno nebo více pravidel, vyberte v seznamu jedno nebo více pravidel:

[![Výběr více pravidel](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Vytvoření pravidla

Pokud chcete vytvořit pravidlo, které generuje upozornění, když tlak v chladícím zařízení překročí 150 PSI, klikněte na **Nové pravidlo**. K vytvoření pravidla použijte následující hodnoty:

| Nastavení          | Hodnota                                 |
| ---------------- | ------------------------------------- |
| Název pravidla        | Upozornění chladiče                       |
| Popis      | Tlak v chladiči překročil 150 PSI |
| Skupina zařízení     | Skupina zařízení **Chladiče**             |
| Výpočet      | Okamžitý                               |
| Pole podmínky 1| tlak                              |
| Operátor podmínky 1 | Větší než                      |
| Hodnota podmínky 1    | 150                               |
| Úroveň závažnosti  | Upozornění                               |

[![Vytvoření pravidla upozornění](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Nové pravidlo uložíte kliknutím na **Použít**.

Aktivaci pravidla můžete zobrazit na stránce **Pravidla** nebo na stránce **Řídicí panel**:

[![Aktivace pravidla upozornění](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-a-rule-with-multiple-conditions"></a>Vytvoření pravidla s několika podmínkami

Pokud chcete vytvořit pravidlo s několika podmínkami, které generuje kritické upozornění v případě, že je u chladícího zařízení během posledních pěti minut průměrná vlhkost vyšší než 80 % a průměrná teplota vyšší než 75 stupňů Fahrenheita, klikněte na **Nové pravidlo**. K vytvoření pravidla použijte následující hodnoty:

| Nastavení          | Hodnota                                 |
| ---------------- | ------------------------------------- |
| Název pravidla        | Kritická vlhkost a teplota chladiče    |
| Popis      | Úrovně vlhkosti a teploty jsou kritické |
| Skupina zařízení     | Skupina zařízení **Chladiče**             |
| Výpočet      | Průměr                               |
| Časové období      | 5                                     |
| Pole podmínky 1| vlhkost                              |
| Operátor podmínky 1 | Větší než                      |
| Hodnota podmínky 1    | 80                                |
| Úroveň závažnosti  | Kritická                              |

[![Vytvoření pravidla s několika podmínkami – první část](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Druhou podmínku přidáte kliknutím na + Přidat podmínku. Pro novou podmínku použijte následující hodnoty:

| Nastavení          | Hodnota                                 |
| ---------------- | ------------------------------------- |
| Pole podmínky 2| teplota                           |
| Operátor podmínky 2 | Větší než                      |
| Hodnota podmínky 2    | 75                                |

[![Vytvoření pravidla s několika podmínkami – druhá část](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Nové pravidlo uložíte kliknutím na **Použít**.

Aktivaci pravidla můžete zobrazit na stránce **Pravidla** nebo na stránce **Řídicí panel**:

[![Aktivace pravidla s několika podmínkami](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Úprava existujícího pravidla

Pokud chcete provést změnu existujícího pravidla, vyberte ho v seznamu pravidel a klikněte na **Upravit**:

[![Úprava pravidla](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Zákaz pravidla

Pokud chcete pravidlo dočasně vypnout, můžete ho zakázat v seznamu pravidel. Zvolte pravidlo, které chcete zakázat, a pak zvolte **Zakázat**. **Stav** pravidla v seznamu se změní a bude značit, že je teď pravidlo zakázané. Stejným postupem můžete znovu povolit pravidlo, které jste předtím zakázali.

[![Zákaz pravidla](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Pokud v seznamu vyberete několik pravidel, můžete povolit a zakázat několik pravidel najednou.

<!-- ## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se budete chtít přesunout k dalšímu kurzu, ponechte akcelerátor řešení vzdáleného monitorování nasazený. Pokud chcete snížit náklady na provoz akcelerátoru řešení, když ho nepoužíváte, na panelu nastavení můžete simulovaná zařízení zastavit:

[![Pozastavení telemetrie](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-automate/togglesimulation-expanded.png#lightbox)

Až budete připraveni začít s dalším kurzem, můžete simulovaná zařízení restartovat.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho na stránce [Zřízená řešení](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Odstranění řešení](media/iot-accelerators-remote-monitoring-automate/deletesolution.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak pomocí stránky **Pravidla** v akcelerátoru řešení vzdáleného monitorování vytvářet a spravovat pravidla, která v řešení aktivují upozornění. Informace o tom, jak pomocí akcelerátoru řešení spravovat a konfigurovat připojená zařízení, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Konfigurace a správa zařízení připojených k řešení monitorování](iot-accelerators-remote-monitoring-manage.md)