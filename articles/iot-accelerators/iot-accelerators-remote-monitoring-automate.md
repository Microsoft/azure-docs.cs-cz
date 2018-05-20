---
title: Rozpoznat problémy s zařízení v řešení vzdáleného monitorování - Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak používat pravidla a akce automaticky rozpoznat problémy s zařízení na základě prahové hodnoty v řešení vzdáleného monitorování.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 461490a312cbcfda50f4b2e9db39c40250d716fd
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="detect-issues-using-threshold-based-rules"></a>Zjistit problémy při použití pravidla na základě prahové hodnoty

Tento kurz ukazuje možnosti stroj pravidel v řešení vzdáleného monitorování. Zavádět tyto možnosti, tento kurz používá scénáři v aplikaci Contoso IoT.

Contoso má pravidlo, které generuje kritickou výstrahu v případě hlášené tlak **chladič** zařízení překračuje 250 PSI. Jako operátor, který chcete označit **chladič** zařízení, které mohou mít problematické senzorů tak, že vyhledá počáteční přetížení špičky. K identifikaci těchto zařízení, vytvoříte pravidlo pro vygenerovat upozornění, když tlak překračuje 150 PSI.

Můžete také byla slyšeli, že kritickou výstrahu, je potřeba při aktivaci průměrná vlhkost **chladič** je větší než 80 % a teplota zařízení za posledních 5 minut **chladič** zařízení v je větší než 75 stupních Fahrenheita za posledních 5 minut.

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Zobrazení pravidla ve vašem řešení
> * Vytvořit nové pravidlo
> * Vytvořit nové pravidlo s několika podmínek
> * Úprava existujícího pravidla
> * Odstranění pravidla

## <a name="prerequisites"></a>Požadavky

Chcete-li v tomto kurzu, je třeba nasazené instanci řešení vzdáleného monitorování ve vašem předplatném Azure.

Pokud jste nenasadili řešení vzdáleného monitorování ještě by se měla Dokončit [nasazení vzdálené monitorování akcelerátoru řešení](iot-accelerators-remote-monitoring-deploy.md) kurzu.

## <a name="view-the-rules-in-your-solution"></a>Zobrazení pravidla ve vašem řešení

**Pravidla** stránky v řešení zobrazí seznam všech aktuální pravidla:

![Stránka pravidla a akce](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2.png)

Chcete-li zobrazit pouze pravidla, která se týkají **chladič** zařízení, použít filtr:

![Filtrovat seznam pravidel](./media/iot-accelerators-remote-monitoring-automate/rulesactionsfilter_v2.png)

Můžete zobrazit další informace o pravidlo a upravit ho, když ji vyberete v seznamu:

![Zobrazení podrobností pravidla](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2.png)

K zakázání, povolení nebo odstranění jednoho nebo více pravidel, vyberte v seznamu více pravidel:

![Vyberte více pravidel](./media/iot-accelerators-remote-monitoring-automate/rulesactionsmultiselect_v2.png)

## <a name="create-a-new-rule"></a>Vytvořit nové pravidlo

Chcete-li přidat nové pravidlo, které generuje upozornění při přetížení v **chladič** zařízení překračuje 150 PSI, zvolte **nové pravidlo**:

![Vytvořit pravidlo](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2.png)

Vytvoření pravidla, použijte následující hodnoty:

| Nastavení          | Hodnota                                 |
| ---------------- | ------------------------------------- |
| Název pravidla        | Chladič upozornění                       |
| Popis      | Přetížení chladič překročil 150 PSI |
| Skupiny zařízení     | **Dochlazovače** skupiny zařízení             |
| Výpočet      | Pro rychlé                               |
| Podmínka 1 pole| pressure                              |
| Operator podmínku 1 | Více než                      |
| Hodnota podmínky 1    | 150                               |
| Úroveň Serverity  | Upozornění                               |

Chcete-li uložit nové pravidlo, zvolte **použít**.

Chcete-li pravidlo je spuštěno na Zobrazit **pravidla** stránky nebo na **řídicí panel** stránky.

## <a name="create-a-new-rule-with-multiple-conditions"></a>Vytvořit nové pravidlo s několika podmínek

Chcete-li vytvořit nové pravidlo s více podmínek, které generuje ke kritické výstrahy při průměrná vlhkost **chladič** je větší než 80 % a teplota zařízení za posledních 5 minut **chladič** je větší než 75 stupních Fahrenheita zařízení za posledních 5 minut, zvolte **nové pravidlo**:

![Vytvoření pravidla mult](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2.png)

Vytvoření pravidla, použijte následující hodnoty:

| Nastavení          | Hodnota                                 |
| ---------------- | ------------------------------------- |
| Název pravidla        | Chladič vlhkosti a temp kritické    |
| Popis      | Je důležité vlhkosti a teploty |
| Skupiny zařízení     | **Dochlazovače** skupiny zařízení             |
| Výpočet      | Průměr                               |
| Časové období      | 5                                     |
| Podmínka 1 pole| vlhkosti                              |
| Operator podmínku 1 | Více než                      |
| Hodnota podmínky 1    | 80                               |
| Úroveň Serverity  | Důležité                              |

Pokud chcete přidat druhou podmínku, klikněte na "+ Přidat podmínku".

![Vytvořit podmínku 2](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2.png)

Použijte následující hodnoty na novou podmínku:

| Nastavení          | Hodnota                                 |
| ---------------- | ------------------------------------- |
| Pole podmínku 2| Teplotní                           |
| Operator podmínku 2 | Více než                      |
| Hodnota podmínky 2    | 75                                |

Chcete-li uložit nové pravidlo, zvolte **použít**.

Chcete-li pravidlo je spuštěno na Zobrazit **pravidla** stránky nebo na **řídicí panel** stránky.

## <a name="edit-an-existing-rule"></a>Úprava existujícího pravidla

Chcete-li provést změnu do existujícího pravidla, vyberte ho v seznamu pravidel.

![Upravit pravidlo](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2.png)

<!--## Disable a rule

To temporarily switch off a rule, you can disable it in the list of rules. Choose the rule to disable, and then choose **Disable**. The **Status** of the rule in the list changes to indicate the rule is now disabled. You can re-enable a rule that you previously disabled using the same procedure.

![Disable rule](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable.png)

You can enable and disable multiple rules at the same time if you select multiple rules in the list.-->

<!--## Delete a rule

To permanently delete a rule, choose the rule in the list of rules and then choose **Delete**.

You can delete multiple rules at the same time if you select multiple rules in the list.-->

## <a name="next-steps"></a>Další postup

V tomto kurzu ukázal, jak na:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Zobrazení pravidla ve vašem řešení
> * Vytvořit nové pravidlo
> * Úprava existujícího pravidla
> * Odstranění pravidla

Teď, když jste se naučili k zjištění problémy při použití pravidla na základě prahové hodnoty, navrhované další kroky jsou další postup:

* [Správa a konfigurace zařízení](./../iot-suite/iot-suite-remote-monitoring-manage.md).
* [Řešení potíží a opravám problémů zařízení](./../iot-suite/iot-suite-remote-monitoring-maintain.md).
* [Testování řešení s Simulovaná zařízení](../iot-suite/iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->