---
title: Vytváření sestav posouzení rizik
description: Získejte přehled o rizikech sítě zjištěných jednotlivými senzory nebo souhrnný pohled na rizika zjištěná všemi senzory.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 853157ef1b97fefdd15785b2a71c7ccc5d06a9a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784250"
---
# <a name="risk-assessment-reporting"></a>Generování sestav posouzení rizik

## <a name="about-risk-assessment-reports"></a>O sestavách posouzení rizik

Sestavy posouzení rizik poskytují:

- Celkové skóre zabezpečení pro zařízení zjištěná v rámci organizačních senzorů.

- Bezpečnostní skóre pro každé síťové zařízení zjištěné jednotlivými senzory.

- Rozpis počtu ohrožených zařízení, zařízení, která potřebují zlepšení a zabezpečení zařízení.

-  Přehled o zabezpečení a provozních problémech:

    - Problémy s konfigurací

    - Priorita ohrožení zabezpečení zařízení podle úrovně zabezpečení

    - Problémy se zabezpečením sítě

    - Síťové provozní problémy

    - Připojení k sítím ICS

    - Připojení k Internetu

    - Průmyslové indikátory malwaru

    - Problémy s protokoly

    - Vektory útoku

### <a name="risk-mitigation"></a>Zmírnění rizik

Sestavy poskytují doporučení, která vám pomůžou vylepšit skóre zabezpečení. Například nainstalujte nejnovější aktualizace zabezpečení, upgradujte firmware na nejnovější verzi nebo sledujte výstrahy.

## <a name="about-security-scores"></a>O skóre zabezpečení

V každé sestavě je vygenerováno celkové skóre zabezpečení sítě. Skóre představuje procento z 100 procent zabezpečení. Například skóre 30% by znamenalo, že vaše síť 30% je zabezpečená.

Skóre hodnocení rizik vycházejí z informací získaných při kontrole paketů, modulech behaviorálních modulů a návrhu stavového počítače specifického pro SCADA.

**Zabezpečená zařízení** jsou zařízení se bezpečnostním skóre vyšším než 90%.

**Zařízení, která vyžadují vylepšení**: zařízení s skóre zabezpečení mezi 70% a 89%.

**Zranitelná zařízení** jsou zařízení s bezpečnostním skóre nižším než 70%.

## <a name="create-risk-assessment-reports"></a>Vytváření sestav posouzení rizik

Vytvoří sestavu posouzení rizik PDF. Název sestavy se automaticky vygeneruje jako risk-assessment-report-1.pdf. Číslo se aktualizuje pro každou novou sestavu, kterou vytvoříte.  Zobrazí se čas a den vytvoření.

### <a name="create-a-sensor-risk-assessment-report"></a>Vytvořit sestavu posouzení rizik senzorů

Vytvořte sestavu posouzení rizik na základě zjištění provedených senzorem, ke kterému jste přihlášeni.

Vytvoření sestavy:

1. Přihlaste se ke konzole senzorů.
1. V postranní nabídce vyberte **hodnocení rizik** .
1. Vyberte **Generovat sestavu**. Sestava se zobrazí v části archivovaných sestav.
1. Vyberte sestavu z části archivovaných sestav a stáhněte ji.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Zobrazení posouzení rizika.":::

Import loga společnosti:

- Vyberte **importovat logo**.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Vytvoření sestavy posouzení rizik místní konzoly pro správu

Vytvořte sestavu posouzení rizik na základě detekcí provedených všemi senzory spravovanými vaší místní konzolou pro správu. 

Vytvoření sestavy:

1. V postranní nabídce vyberte **hodnocení rizik** .

2. V rozevíracím seznamu **Vybrat senzor** vyberte snímač.

3. Vyberte **Generovat sestavu**.

4. V části **archivovaných sestav** vyberte **Stáhnout** .

Import loga společnosti:

- Vyberte **importovat logo**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Naimportujte logo pomocí zobrazení hodnocení rizik.":::

## <a name="see-also"></a>Viz také

[Vytváření sestav vektorů útoku](how-to-create-attack-vector-reports.md)

