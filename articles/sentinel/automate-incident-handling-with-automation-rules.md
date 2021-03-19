---
title: Automatizace zpracování incidentů ve službě Azure Sentinel | Microsoft Docs
description: Tento článek vysvětluje, jak používat pravidla automatizace k automatizaci zpracování incidentů, aby se maximalizovala efektivita SOC a efektivita v reakci na bezpečnostní hrozby.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/14/2021
ms.author: yelevin
ms.openlocfilehash: 1ff9fbbb6cd4b8827555a6cb1b222ed4eb0a5299
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609364"
---
# <a name="automate-incident-handling-in-azure-sentinel-with-automation-rules"></a>Automatizace zpracování incidentů ve službě Azure Sentinel s pravidly automatizace

V tomto článku se dozvíte, jaká pravidla Automatizace Azure Sentinel jsou a jak se dají používat k implementaci operací orchestrace, automatizace a odezvy (společnosti) zabezpečení, zvýšení efektivity vaší SOC a úspory času a prostředků.

> [!IMPORTANT]
>
> - Funkce **pravidel automatizace** je aktuálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

## <a name="what-are-automation-rules"></a>Co jsou pravidla automatizace?

Pravidla automatizace jsou novým konceptem v rámci Azure Sentinel. Tato funkce umožňuje uživatelům centrálně spravovat automatizaci zpracování incidentů. Kromě toho, že vám umožní přiřazovat playbooky incidentům (ne jen k výstrahám jako dřív), pravidla automatizace také umožňují automatizovat odpovědi na více pravidel analýzy najednou, automaticky označovat, přiřazovat nebo uzavřít incidenty bez nutnosti playbooky a řídit pořadí akcí, které se spustí. Pravidla automatizace zjednodušují použití automatizace v Azure Sentinel a umožní vám zjednodušit složité pracovní postupy pro procesy orchestrace incidentů.

## <a name="components"></a>Komponenty

Pravidla automatizace se skládají z několika součástí:

### <a name="trigger"></a>Trigger

Pravidla automatizace se spouštějí vytvořením incidentu. 

Pro kontrolu – incidenty se vytvářejí z výstrah podle pravidel analýzy, z nichž existují čtyři typy, jak je vysvětleno v kurzu [zjišťování hrozeb pomocí integrovaných analytických pravidel v Azure Sentinel](tutorial-detect-threats-built-in.md).

### <a name="conditions"></a>Podmínky

Komplexní sady podmínek lze definovat, aby se řídily, kdy by měly být spuštěny akce (viz níže). Tyto podmínky jsou obvykle založeny na stavech nebo hodnotách atributů incidentů a jejich entit a mohou obsahovat `AND` / `OR` / `NOT` / `CONTAINS` operátory.

### <a name="actions"></a>Akce

Akce lze definovat tak, aby se spouštěly při splnění podmínek (viz výše). V pravidle můžete definovat mnoho akcí a můžete zvolit pořadí, ve kterém se budou spouštět (viz níže). Následující akce lze definovat pomocí pravidel automatizace bez nutnosti [pokročilých funkcí PlayBook](automate-responses-with-playbooks.md):

- Změna stavu incidentu a udržování aktuálního pracovního postupu.

  - Když se změní na uzavřeno, určí se [důvod ukončení](tutorial-investigate-cases.md#closing-an-incident) a přidá se komentář. Díky tomu můžete sledovat výkon a efektivitu a doladit je, abyste snížili falešně pozitivní výsledky.

- Změna závažnosti incidentu – můžete znovu vyhodnotit a změnit prioritu na základě přítomnosti, nepřítomnosti, hodnot nebo atributů entit zapojených do incidentu.

- Přiřazení incidentu k vlastníkovi – to vám pomůže směrovat typy incidentů pracovníkům, které jsou nejvhodnější pro práci s nimi, nebo k nejvíce dostupným pracovníkům.

- Přidání značky k incidentu – to je užitečné pro klasifikaci incidentů podle předmětu, útočníka nebo jakéhokoli jiného společného jmenovatele.

Také můžete definovat akci spuštění [PlayBook](tutorial-respond-threats-playbook.md), aby bylo možné provádět složitější akce reakce, včetně těch, které zahrnují externí systémy. V pravidlech automatizace jsou k dispozici pouze playbooky aktivované [triggerem incidentu](automate-responses-with-playbooks.md#azure-logic-apps-basic-concepts) . Můžete definovat akci pro zahrnutí více playbooky nebo kombinace playbooky a dalších akcí a pořadí, ve kterém budou spouštěny.

### <a name="expiration-date"></a>Datum vypršení platnosti

Můžete definovat datum vypršení platnosti pravidla automatizace. Po tomto datu bude pravidlo zakázané. To je užitečné pro zpracování incidentů "hluku", které způsobily plánované, časově omezené aktivity, jako je testování průniku.

### <a name="order"></a>Objednávka

Můžete definovat pořadí, ve kterém se pravidla automatizace spustí. Později pravidla automatizace vyhodnotí podmínky incidentu podle jeho stavu poté, co je zpracuje předchozí pravidla automatizace.

Například pokud "první pravidlo automatizace" změnila závažnost incidentu z středně na nízkou a "druhé pravidlo automatizace" je definováno tak, aby se spouštělo pouze u incidentů se střední nebo vyšší závažností, nebude na tomto incidentu běžet.

## <a name="common-use-cases-and-scenarios"></a>Běžné případy použití a scénáře

### <a name="incident-triggered-automation"></a>Automatizace aktivovaná incidenty

Až do této chvíle mohli spustit automatizovanou reakci pomocí playbooky jenom výstrahy. Pomocí pravidel pro automatizaci teď můžou incidenty aktivovat automatické řetězy odpovědí, které můžou zahrnovat nové playbooky aktivované incidenty ([vyžaduje se zvláštní oprávnění](#permissions-for-automation-rules-to-run-playbooks)), když se vytvoří incident. 

### <a name="trigger-playbooks-for-microsoft-providers"></a>Aktivovat playbooky pro poskytovatele Microsoftu

Pravidla automatizace poskytují způsob, jak automatizovat zpracování výstrah zabezpečení od Microsoftu, a to použitím těchto pravidel na incidenty vytvořené z výstrah. Pravidla automatizace můžou volat playbooky ([vyžadují se speciální oprávnění](#permissions-for-automation-rules-to-run-playbooks)) a předávat incidenty do nich se všemi jejich podrobnostmi, včetně výstrah a entit. Obecně platí, že osvědčené postupy pro službu Azure Sentinel nastavují použití fronty incidentů jako ústředního bodu operací zabezpečení.

Mezi výstrahy zabezpečení Microsoftu patří následující:

- Microsoft Cloud App Security (MCAS)
- Azure AD Identity Protection
- Azure Defender (ASC)
- Defender pro IoT (dříve ASC pro IoT)
- Microsoft Defender for Office 365 (dříve Office 365 ATP)
- Microsoft Defender pro koncový bod (dříve MDATP)
- Microsoft Defender pro identity (dříve Azure ATP)

### <a name="multiple-sequenced-playbooksactions-in-a-single-rule"></a>Více sekvencované playbooky/akcí v jednom pravidle

Teď můžete mít téměř úplnou kontrolu nad pořadím provádění akcí a playbooky v rámci jednoho pravidla automatizace. Můžete také řídit pořadí spouštění pravidel automatizace. To vám umožňuje významně zjednodušit playbooky, snižovat je na jednu úlohu nebo malou, jednoduchou sekvenci úloh a kombinovat tyto malé playbookye v různých kombinacích v různých pravidlech automatizace.

### <a name="assign-one-playbook-to-multiple-analytics-rules-at-once"></a>Přiřazení jednoho PlayBook k několika pravidlům analýzy najednou

Pokud máte úkol, který chcete automatizovat pro všechna vaše pravidla analýzy – řekněme vytvoření lístku podpory v externím systému pro lístkování – můžete použít jeden PlayBook pro všechna nebo všechna pravidla analýzy – včetně všech budoucích pravidel – v jednom snímku. Díky tomu budou jednoduché, ale opakující se úlohy údržby a údržbuy mnohem méně chore.

### <a name="automatic-assignment-of-incidents"></a>Automatické přiřazování incidentů

Automaticky můžete přiřadit incidenty správnému vlastníkovi. Pokud má váš SOC analytika, který se specializuje na konkrétní platformu, mohou být všechny incidenty týkající se této platformy automaticky přiřazeny k tomuto analytikovi.

### <a name="incident-suppression"></a>Potlačení incidentu

Pravidla můžete použít k automatickému vyřešení incidentů, které jsou známy falešně pozitivní nebo neškodné kladné hodnoty bez použití playbooky. Například při spuštění testů průniku, provádění plánované údržby nebo upgradu nebo testování postupů automatizace může být vytvořeno mnoho falešně pozitivních incidentů, které SOC chce ignorovat. Časově omezené pravidlo automatizace může tyto incidenty automaticky uzavřít při jejich vytváření a při jejich označování způsobit popisovač příčiny jejich generování.

### <a name="time-limited-automation"></a>Automatizace časově omezená

Pro pravidla automatizace můžete přidat data vypršení platnosti. Mohou nastat jiné případy než potlačení incidentů, které zaručuje časově omezená automatizaci. Konkrétní typ incidentu můžete pro konkrétního uživatele (například interně nebo konzultanta) přiřadit konkrétnímu časovému období. Pokud je časový rámec předem známý, můžete efektivně způsobit, že se pravidlo na konci jeho relevanci zakáže, aniž byste se museli zapamatovat.

### <a name="automatically-tag-incidents"></a>Automaticky označovat incidenty

Můžete automaticky přidat volné značky k incidentům k seskupení nebo jejich klasifikaci podle libovolných kritérií, která zvolíte.

## <a name="automation-rules-execution"></a>Spuštění pravidel automatizace

Pravidla automatizace se spouštějí postupně podle pořadí, které určíte. Každé pravidlo automatizace se spustí po dokončení jeho spuštění. V rámci pravidla automatizace jsou všechny akce spouštěny postupně v pořadí, ve kterém jsou definovány.

Pro akce PlayBook je mezi začátkem akce PlayBook a další akcí v seznamu prodleva dvě minuty.

### <a name="permissions-for-automation-rules-to-run-playbooks"></a>Oprávnění pro spuštění playbooky pro pravidla automatizace

Když pravidlo služby Azure Sentinel Automation spustí PlayBook, použije speciální účet služby Azure Sentinel, který je konkrétně autorizovaný pro tuto akci. Použití tohoto účtu (na rozdíl od vašeho uživatelského účtu) zvyšuje úroveň zabezpečení služby.

Aby pravidlo automatizace spustilo PlayBook, musí mít tento účet udělená explicitní oprávnění skupině prostředků, ve které se nachází PlayBook. V tomto okamžiku bude jakékoli pravidlo automatizace moci v této skupině prostředků spustit všechny PlayBook.

Když konfigurujete pravidlo automatizace a přidáte akci **Spustit PlayBook** , zobrazí se rozevírací seznam playbooky. Playbooky, ke kterým má Azure Sentinel oprávnění, se zobrazí jako nedostupná (šedě vydaná). Kliknutím na odkaz **Spravovat PlayBook oprávnění** můžete udělit skupině prostředků playbooky na místě oprávnění Azure Sentinel.

> [!NOTE]
> **Oprávnění v architektuře s více klienty**
>
> Pravidla automatizace plně podporují nasazení mezi pracovními prostory a více klienty (v případě víceklientské služby s využitím [Azure Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)).
>
> Proto platí, že pokud vaše nasazení služby Azure Sentinel používá víceklientské architektury (Pokud jste například MSSP), můžete mít pravidlo automatizace v jednom tenantovi, kde je spuštěný PlayBook, který žije v jiném tenantovi, ale oprávnění Sentinelu pro spuštění playbooky musí být definované v tenantovi, kde jsou definované pravidla automatizace.

## <a name="creating-and-managing-automation-rules"></a>Vytváření a Správa pravidel automatizace

Můžete vytvářet a spravovat pravidla automatizace z různých bodů v prostředí Sentinel Azure, podle konkrétní potřeby a případu použití.

- **Okno automatizace**

    Pravidla automatizace se dají centrálně spravovat v novém okně **Automatizace** (což nahrazuje okno **playbooky** ) na kartě **pravidla automatizace** . (v tomto okně teď můžete playbooky spravovat taky na kartě **playbooky** .) Odtud můžete vytvořit nová pravidla automatizace a upravit stávající. Můžete také přetáhnout pravidla automatizace a změnit pořadí provádění a povolit nebo zakázat.

    V okně **Automatizace** uvidíte všechna pravidla, která jsou definována v pracovním prostoru, spolu se stavem (povoleno/zakázáno) a která pravidla analýzy jsou aplikována na.

    Pokud potřebujete pravidlo automatizace, které se bude vztahovat na mnoho pravidel analýzy, vytvořte ho přímo v okně **Automatizace** . V horní nabídce klikněte na **vytvořit** a **přidejte nové pravidlo**, které otevře panel **vytvořit nové pravidlo automatizace** . Tady máte ucelenou flexibilitu při konfiguraci pravidla: můžete ho použít pro všechna analytická pravidla (včetně budoucích) a definovat nejširší rozsah podmínek a akcí.

- **Průvodce analytickým pravidlem**

    Na kartě **automatizovaná odpověď** v průvodci analytickým pravidlem můžete zobrazit, spravovat a vytvořit pravidla automatizace, která se vztahují na konkrétní analytické pravidlo, které se v průvodci vytvoří nebo upraví.

    Když v horní nabídce v okně **Analýza** kliknete na **vytvořit** a jeden z typů pravidel (**pravidlo plánovaného dotazu** nebo **pravidlo vytváření incidentu Microsoftu**), nebo pokud vyberete existující pravidlo analýzy a kliknete na **Upravit**, otevře se Průvodce pravidly. Po výběru karty **automatizovaná odpověď** se zobrazí část s názvem **Automatizace incidentů**, pod kterou se budou zobrazovat pravidla automatizace, která se aktuálně vztahují k tomuto pravidlu. Můžete vybrat existující pravidlo automatizace, které chcete upravit, nebo kliknutím na **Přidat nový** vytvořit nový.

    Všimněte si, že když vytvoříte pravidlo automatizace z tohoto místa, na panelu **vytvořit nové pravidlo automatizace** se zobrazí podmínka **pravidlo analýzy** jako nedostupná, protože toto pravidlo je už nastavené na použití jenom pro pravidlo analýzy, které upravujete v průvodci. Všechny ostatní možnosti konfigurace jsou stále k dispozici.

- **Okno incidenty**

    Můžete také vytvořit pravidlo automatizace z okna **incidenty** , aby bylo možné reagovat na jeden opakovaný incident. To je užitečné při vytváření [pravidla potlačení](#incident-suppression) pro automatické uzavírání incidentů "s vysokou úrovní". Vyberte incident z fronty a v horní nabídce klikněte na **vytvořit pravidlo automatizace** .

    Všimněte si, že na panelu **vytvořit nové pravidlo automatizace** se naplní všechna pole s hodnotami z incidentu. Toto pravidlo pojmenuje stejný název jako incident, použije ho pro pravidlo analýzy, které incident vygenerovalo, a použije všechny dostupné entity v incidentu jako podmínky pravidla. Ve výchozím nastavení také navrhne akci potlačení (zavření) a navrhne datum vypršení platnosti pravidla. Můžete přidat nebo odebrat podmínky a akce a změnit datum vypršení platnosti, jak chcete.

## <a name="auditing-automation-rule-activity"></a>Aktivita pravidla automatizace auditování

Je možné, že vás zajímá, co se stalo s daným incidentem a jaké pravidlo pro určité pravidlo automatizace může nebo nemusí být provedeno. V tabulce *SecurityIncident* v okně **logs** máte k dispozici úplný záznam o Chronicles incidentu. Pro zobrazení všech aktivit pravidla automatizace použijte následující dotaz:

```kusto
SecurityIncident
| where ModifiedBy contains "Automation"
```

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat pravidla automatizace ke správě fronty incidentů Sentinel v Azure a implementovat základní automatizaci zpracování incidentů.

- Další informace o rozšířených možnostech automatizace najdete v tématu [Automatizace reakce na hrozby pomocí playbooky ve službě Azure Sentinel](automate-responses-with-playbooks.md).
- Nápovědu k implementaci pravidel automatizace a playbooky najdete v tématu [kurz: použití playbooky k automatizaci odpovědí na hrozby v Azure Sentinel](tutorial-respond-threats-playbook.md).
