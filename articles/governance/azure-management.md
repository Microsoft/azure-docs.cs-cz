---
title: Správa Azure
description: Přehled oblastí správy pro aplikace Azure a prostředky s odkazy na obsah na nástroje pro správu Azure.
author: DCtheGeek
manager: carmonm
ms.service: governance
ms.topic: article
ms.date: 09/18/2018
ms.author: dacoulte
ms.openlocfilehash: 268526277dc935ccffc30120f2bb170ad3ff3ef1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978215"
---
# <a name="management-in-azure"></a>Správa v Azure

Zásady správného řízení v Azure je jeden z aspektů správy Azure. Tento článek stručně popisuje různé oblasti správy, které jsou nezbytné pro nasazení a údržbu vašich aplikací a prostředků v Azure, a obsahuje odkazy na dokumentaci, která vám umožní začít.

Termín správa označuje úlohy a procesy nezbytné pro udržování obchodních aplikací a prostředků, které je podporují. Azure má několik služeb a nástrojů, které společně poskytují kompletní správu pro vaše aplikace spuštěné nejen v Azure, ale také v ostatních cloudech a v místním prostředí. Pochopení různých nástrojů, které jsou k dispozici, a jejich současného využití při zpracování celé řady scénářů správy je prvním krokem návrhu při kompletního prostředí pro správu.

Následující diagram ukazuje různé oblasti správy, které jsou nutné k údržbě libovolné aplikace nebo prostředku. Na tyto různé oblasti lze nahlížet z pohledu životního cyklu, v jehož rámci se postupně po sobě vyžadují během životnosti prostředku. Začíná to počátečním nasazením, přes průběžný provoz až po vyřazení z provozu.

![Možnosti správy](../monitoring/media/management-overview/management-capabilities.png)

Žádná samostatná služba Azure nesplňuje kompletně požadavky konkrétní oblasti správy. Každá z nich se místo toho realizuje pomocí několika služeb společně. Některé služby poskytují cílené funkce, například služba Application Insights, která poskytuje monitorování pro webové aplikace. Další služby poskytují společné funkce, například služba Log Analytics, která ukládá data správy pro jiné služby a umožňuje analyzovat data různých typů shromážděná různými službami.

Následující části stručně popisují různé oblasti správy a poskytují odkazy na podrobný obsah věnovaný hlavním službám Azure, které jsou určené k jejich řešení.

## <a name="monitor"></a>Monitorování

Monitorování je shromažďování a analýza dat s cílem určit výkon, stav a dostupnost vaší obchodní aplikace a prostředků, na kterých závisí. Efektivní strategie monitorování vám pomůže porozumět provozu jednotlivých komponent vaší aplikace a prodloužit dobu provozu díky proaktivnímu upozorňování na zásadní potíže, abyste je mohli vyřešit předtím, než začnou způsobovat problémy. Můžete si přečíst přehled monitorování Azure, který identifikuje různé služby používané pro strategie monitorování při [monitorování aplikací a prostředků Azure](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Konfigurace

Konfigurace označuje počáteční nasazení a konfiguraci aplikací a prostředků a jejich průběžnou údržbu pomocí oprav a aktualizací. Automatizace těchto úloh pomocí skriptů a zásad umožňuje eliminovat redundance, minimalizovat čas a úsilí a zvýšit přesnost a efektivitu. [Azure Automation](..\automation\automation-intro.md) poskytuje převážnou část služeb pro automatizaci úloh konfigurace. Kromě runbooků pro automatizaci procesů poskytuje správu aktualizací a konfigurací, která pomáhá při správě konfigurací prostřednictvím zásad a při identifikaci a nasazování aktualizací.

## <a name="govern"></a>Řízení

Zásady správného řízení poskytují mechanismy a postupy pro zajištění kontroly nad vašimi aplikacemi a prostředky v Azure. Zahrnují plánování iniciativ a nastavení strategických priorit.
Zásady správného řízení v Azure jsou primárně implementované pomocí dvou služeb. [Azure Policy](../azure-policy/azure-policy-introduction.md) pomáhá vytvářet, přiřazovat a spravovat definice zásad, které u vašich prostředků vynucují různá pravidla a akce, aby tyto prostředky i nadále odpovídaly vašim firemním standardům a smlouvám o úrovni služeb. [Azure Cost Management by Cloudyn](../cost-management/overview.md) umožňuje sledovat využití cloudu a výdaje za prostředky Azure a dalších poskytovatelů cloudu, včetně AWS a Googlu.

## <a name="secure"></a>Zabezpečení

Správa zabezpečení aplikací, prostředků a dat zahrnuje kombinaci vyhodnocování hrozeb, shromažďování a analýza dat zabezpečení a zajištění, že vaše aplikace a prostředky jsou navržené a nakonfigurované zabezpečeným způsobem. Monitorování zabezpečení a analýzu hrozeb zajišťuje služba [Azure Security Center](../security-center/security-center-intro.md), která poskytuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Měli byste si také projít [úvod do zabezpečení Azure](../security/azure-security.md), kde najdete kompletní informace o zabezpečení v Azure a pokyny pro zabezpečenou konfiguraci prostředků Azure.

## <a name="protect"></a>Ochrana

Ochrana znamená zajištění, že vaše aplikace a data jsou vždycky k dispozici, a to i v případě výpadků mimo vaši kontrolu. Ochranu v Azure zajišťují dvě služby. [Azure Backup](../backup/backup-introduction-to-azure-backup.md)zajišťuje zálohování a obnovení vašich dat v cloudu nebo místně. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) poskytuje vysokou dostupnost vašich aplikací díky zajištění obchodní kontinuity a okamžitého obnovení v případě havárie.

## <a name="migrate"></a>Migrace

Migrace označuje přenos úloh, které jsou aktuálně spuštěné v místním prostředí, do cloudu Azure.
[Azure Migrate](../migrate/migrate-overview.md) je služba, která pomáhá vyhodnotit vhodnost migrace místních virtuálních počítačů do Azure, včetně odhadu rozsahu na základě výkonu a souvisejících nákladů. Azure Site Recovery vám pomůže s vlastní migrací virtuálních počítačů [z místního prostředí](../site-recovery/migrate-tutorial-on-premises-azure.md) nebo [z Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Azure Database Migration](../dms/dms-overview.md) vám pomůže s migrací několika databázových zdrojů na datové platformy Azure.