---
title: Použití řešení vzdáleného monitorování k detekci problémů zařízení – Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak v řešení pro vzdálené monitorování pomocí pravidel a akcí automaticky detekovat problémy se zařízeními na základě prahových hodnot.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 695d1b1124c042b61837f0a1257ac2a201ae4860
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539397"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Kurz: Detekce problémů se zařízeními připojenými k řešení pro monitorování

V tomto kurzu nakonfigurujete akcelerátor řešení pro vzdálené monitorování tak, aby detekoval problémy s připojenými zařízeními IoT. Problémy se zařízeními můžete detekovat tak, že přidáte pravidla, která generují upozornění na řídicím panelu řešení.

V tomto kurzu se k představení pravidel a upozornění používá simulované zařízení chladiče. Tento chladič spravuje organizace Contoso a je připojený k akcelerátoru řešení pro vzdálené monitorování. Společnost Contoso už má pravidlo, které generuje kritické upozornění, když tlak v chladiči překročí 298 PSI. Jako operátor ve společnosti Contoso chcete identifikovat chladící zařízení, která můžou mít problematické senzory. Za tímto účelem budete hledat počáteční prudká zvýšení tlaku. Taková zařízení identifikujete prostřednictvím přidání pravidla, které generuje varovné upozornění, když tlak v chladiči překročí 150 PSI.

Také jste byli požádáni, abyste pro chladič vytvořili kritické upozornění v případě, že během posledních pěti minut byla průměrná vlhkost v zařízení vyšší než 80 % a teplota zařízení vyšší než 75 stupňů Fahrenheita.

V tomto kurzu jste:

>[!div class="checklist"]
> * Zobrazení pravidel v řešení
> * Vytvoření pravidla
> * Vytvoření pravidla s několika podmínkami
> * Úprava existujícího pravidla
> * Zapnutí a vypnutí pravidel

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>Kontrola existujících pravidel

Na stránce **Pravidla** v akcelerátoru řešení se zobrazí seznam všech aktuálních pravidel:

[![Stránka pravidla](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Pokud chcete zobrazit pouze pravidla, která se týkají chladících zařízení, použijte filtr. Výběrem pravidla v seznamu můžete zobrazit další informace o pravidle a upravit ho:

[![Zobrazit podrobnosti pravidla](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Vytvoření pravidla

Pokud chcete vytvořit pravidlo, které generuje upozornění, když tlak v chladícím zařízení překročí 150 PSI, klikněte na **Nové pravidlo**. K vytvoření pravidla použijte následující hodnoty:

| Nastavení          | Hodnota                                 |
| ---------------- | ------------------------------------- |
| Název pravidla        | Upozornění chladiče                       |
| Popis      | Tlak v chladiči překročil 150 PSI |
| Skupina zařízení     | Skupina zařízení **Chladiče**             |
| Výpočet      | Okamžitě                               |
| Pole podmínky 1| tlak                              |
| Operátor podmínky 1 | Je větší než                      |
| Hodnota podmínky 1    | 150                               |
| Úroveň závažnosti  | Upozornění                               |

[![Vytvořit pravidlo upozornění](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Nové pravidlo uložíte kliknutím na **Použít**.

Aktivaci pravidla můžete zobrazit na stránce **Pravidla** nebo na stránce **Řídicí panel**:

[![Pravidlo upozornění aktivované](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-an-advanced-rule"></a>Vytvoření pokročilého pravidla

Pokud chcete vytvořit pravidlo s několika podmínkami, které generuje kritické upozornění v případě, že je u chladícího zařízení během posledních pěti minut průměrná vlhkost vyšší než 80 % a průměrná teplota vyšší než 75 stupňů Fahrenheita, klikněte na **Nové pravidlo**. K vytvoření pravidla použijte následující hodnoty:

| Nastavení          | Hodnota                                 |
| ---------------- | ------------------------------------- |
| Název pravidla        | Kritická vlhkost a teplota chladiče    |
| Popis      | Úrovně vlhkosti a teploty jsou kritické |
| Skupina zařízení     | Skupina zařízení **Chladiče**             |
| Výpočet      | Průměr                               |
| Časové období      | 5                                     |
| Pole podmínky 1| vlhkost                              |
| Operátor podmínky 1 | Je větší než                      |
| Hodnota podmínky 1    | 80                                |
| Úroveň závažnosti  | Kritické                              |

[![Vytvořit více částí pravidla podmínky 1](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Druhou podmínku přidáte kliknutím na + Přidat podmínku. Pro novou podmínku použijte následující hodnoty:

| Nastavení          | Hodnota                                 |
| ---------------- | ------------------------------------- |
| Pole podmínky 2| teplota                           |
| Operátor podmínky 2 | Je větší než                      |
| Hodnota podmínky 2    | 75                                |

[![Vytvoření více částí pravidla podmínky 2](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Nové pravidlo uložíte kliknutím na **Použít**.

Aktivaci pravidla můžete zobrazit na stránce **Pravidla** nebo na stránce **Řídicí panel**:

[![Bylo aktivováno pravidlo vícenásobné podmínky.](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Úprava existujícího pravidla

Pokud chcete provést změnu existujícího pravidla, vyberte ho v seznamu pravidel a klikněte na **Upravit**:

[![Upravit pravidlo](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Zákaz pravidla

Pokud chcete pravidlo dočasně vypnout, můžete ho zakázat v seznamu pravidel. Vyberte pravidlo, které chcete zakázat, a pak zvolte **Zakázat**. **Stav** pravidla v seznamu se změní a bude značit, že je teď pravidlo zakázané. Stejným postupem můžete znovu povolit pravidlo, které jste předtím zakázali.

[![Zakázat pravidlo](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Pokud v seznamu vyberete několik pravidel, můžete povolit a zakázat několik pravidel najednou.

## <a name="delete-a-rule"></a>Odstranění pravidla

Pokud chcete trvale odstranit pravidlo, můžete ho odstranit ze seznamu pravidel. Vyberte pravidlo, které chcete odstranit, a pak zvolte **Odstranit**.

[![Snímek obrazovky zobrazující stránku pravidla s zvýrazněným oknem odstranit pravidlo](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-expanded.png#lightbox)

Jakmile potvrdíte, že chcete pravidlo odstranit, budete mít možnost odstranit všechna upozornění související s tímto pravidlem na stránce **Údržba**.

[![Odstranit pravidlo](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-expanded.png#lightbox)

Najednou můžete odstranit pouze jedno pravidlo.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak pomocí stránky **Pravidla** v akcelerátoru řešení pro vzdálené monitorování vytvářet a spravovat pravidla, která v řešení aktivují upozornění. Informace o tom, jak pomocí akcelerátoru řešení spravovat a konfigurovat připojená zařízení, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Konfigurace a správa zařízení připojených k řešení pro monitorování](iot-accelerators-remote-monitoring-manage.md)