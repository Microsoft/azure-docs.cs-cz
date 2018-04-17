---
title: Správa Azure – monitorování | Microsoft Docs
description: Azure má několik služeb a nástrojů, které společně poskytují kompletní správu pro vaše aplikace spuštěné nejen v Azure, ale také v ostatních cloudech a v místním prostředí.  Tento článek obsahuje obecný popis různých oblastí správy a odkazy na obsah věnovaný nástrojům Azure pro správu vašich cloudových aplikací a prostředků.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2018
ms.author: bwren
ms.openlocfilehash: 36dd04be167d9e8e63ab38e4af80c5766ba55370
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-management---monitoring"></a>Správa Azure – monitorování

Monitorování v Azure je jedním z aspektů správy Azure.  Tento článek stručně popisuje různé oblasti správy, které jsou nezbytné pro nasazení a údržbu vašich aplikací a prostředků v Azure, a obsahuje odkazy na dokumentaci, která vám umožní začít.

## <a name="management-in-azure"></a>Správa v Azure

Termín správa označuje úlohy a procesy nezbytné pro udržování obchodních aplikací a prostředků, které je podporují.  Azure má několik služeb a nástrojů, které společně poskytují kompletní správu pro vaše aplikace spuštěné nejen v Azure, ale také v ostatních cloudech a v místním prostředí.  Pochopení různých nástrojů, které jsou k dispozici, a jejich současného využití při zpracování celé řady scénářů správy je prvním krokem návrhu při kompletního prostředí pro správu.

Následující diagram ukazuje různé oblasti správy, které jsou nutné k údržbě libovolné aplikace nebo prostředku.  Na tyto různé oblasti lze nahlížet z pohledu životního cyklu, v jehož rámci se postupně po sobě vyžadují během životnosti prostředku.  Začíná to počátečním nasazením, přes průběžný provoz až po vyřazení z provozu.

![Možnosti správy](media/management-overview/management-capabilities.png)


Žádná samostatná služba Azure nesplňuje kompletně požadavky konkrétní oblasti správy. Každá z nich se místo toho realizuje pomocí několika služeb společně.  Některé služby poskytují cílené funkce, například služba Application Insights, která poskytuje monitorování pro webové aplikace.  Další služby poskytují společné funkce, například služba Log Analytics, která ukládá data správy pro jiné služby a umožňuje analyzovat data různých typů shromážděná různými službami.  

Následující části stručně popisují různé oblasti správy a poskytují odkazy na podrobný obsah věnovaný hlavním službám Azure, které jsou určené k jejich řešení.

## <a name="monitor"></a>Monitorování
Monitorování je shromažďování a analýza dat s cílem určit výkon, stav a dostupnost vaší obchodní aplikace a prostředků, na kterých závisí. Efektivní strategie monitorování vám pomůže porozumět provozu jednotlivých komponent vaší aplikace a prodloužit dobu provozu díky proaktivnímu upozorňování na zásadní potíže, abyste je mohli vyřešit předtím, než začnou způsobovat problémy.  Můžete si přečíst přehled monitorování Azure, který identifikuje různé služby používané pro strategie monitorování při [monitorování aplikací a prostředků Azure](monitoring-overview.md).


## <a name="configure"></a>Konfigurace
Konfigurace označuje počáteční nasazení a konfiguraci aplikací a prostředků a jejich průběžnou údržbu pomocí oprav a aktualizací.  Automatizace těchto úloh pomocí skriptů a zásad umožňuje eliminovat redundance, minimalizovat čas a úsilí a zvýšit přesnost a efektivitu.  [Azure Automation](..\automation\automation-intro.md) poskytuje převážnou část služeb pro automatizaci úloh konfigurace.  Kromě runbooků pro automatizaci procesů poskytuje správu aktualizací a konfigurací, která pomáhá při správě konfigurací prostřednictvím zásad a při identifikaci a nasazování aktualizací.

## <a name="govern"></a>Řízení
Zásady správného řízení poskytují mechanismy a postupy pro zajištění kontroly nad vašimi aplikacemi a prostředky v Azure.  Zahrnují plánování iniciativ a nastavení strategických priorit.  Zásady správného řízení v Azure jsou primárně implementované pomocí dvou služeb.  [Azure Policy](../azure-policy/azure-policy-introduction.md) pomáhá vytvářet, přiřazovat a spravovat definice zásad, které u vašich prostředků vynucují různá pravidla a akce, aby tyto prostředky i nadále odpovídaly vašim firemním standardům a smlouvám o úrovni služeb. [Azure Cost Management by Cloudyn](../cost-management/overview.md) umožňuje sledovat využití cloudu a výdaje za prostředky Azure a dalších poskytovatelů cloudu, včetně AWS a Googlu.

## <a name="secure"></a>Zabezpečení
Správa zabezpečení vašich aplikací, prostředků a dat zahrnuje kombinaci vyhodnocování hrozeb, shromažďování a analýzy dat zabezpečení a zajištění, že vaše aplikace a prostředky jsou navržené a nakonfigurované zabezpečeným způsobem.  Monitorování zabezpečení a analýzu hrozeb zajišťuje služba [Azure Security Center](../security-center/security-center-intro.md), která poskytuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami.  Měli byste si také projít [úvod do zabezpečení Azure](../security/azure-security.md), kde najdete kompletní informace o zabezpečení v Azure a pokyny pro zabezpečenou konfiguraci prostředků Azure.


## <a name="protect"></a>Ochrana
Ochrana znamená zajištění, že vaše aplikace a data jsou vždycky k dispozici, a to i v případě výpadků mimo vaši kontrolu.  Ochranu v Azure zajišťují dvě služby.  [Azure Backup](../backup/backup-introduction-to-azure-backup.md) zajišťuje zálohování a obnovení vašich dat, a to v cloudu i v místním prostředí.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) poskytuje vysokou dostupnost vašich aplikací díky zajištění obchodní kontinuity a okamžitého obnovení v případě havárie.

## <a name="migrate"></a>Migrace 
Migrace označuje přenos úloh, které jsou aktuálně spuštěné v místním prostředí, do cloudu Azure.  [Azure Migrate](../migrate/migrate-overview.md) je služba, která pomáhá vyhodnotit vhodnost migrace místních virtuálních počítačů do Azure, včetně odhadu rozsahu na základě výkonu a souvisejících nákladů.  Azure Site Recovery vám pomůže s vlastní migrací virtuálních počítačů [z místního prostředí](../site-recovery/migrate-tutorial-on-premises-azure.md) nebo [z Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Azure Database Migration](../dms/dms-overview.md) vám pomůže s migrací několika databázových zdrojů na datové platformy Azure.


## <a name="operations-management-suite"></a>Operations Management Suite
Předchozí technická dokumentace týkající se správy Azure zahrnovala Operations Management Suite (OMS), což je sdružení následující služeb Azure pro správu:

- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

Od popisu tohoto sdružení služeb v naší technické dokumentaci upouštíme, protože kompletní správa v Azure se rozšířila a zahrnuje i další služby. Žádná ze služeb, které jsou součástí OMS, se nezměnila a všechny stále mají důležitou roli při správě aplikací a prostředků Azure. Měli byste se zaměřit na úlohy správy, které potřebujete provádět, a na různé služby Azure, které pro jednotlivé úlohy spolupracují.