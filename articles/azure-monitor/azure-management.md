---
title: Správa Azure a Operations Management Suite (OMS) | Microsoft Docs
description: Přehled oblastí správy pro prostředky a aplikace Azure s odkazy na obsah věnovaný nástrojům Azure pro správu, které byly dřív součástí sady Operations Management Suite (OMS)
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2018
ms.author: bwren
ms.openlocfilehash: e0ad6b212e328612d2934562e1292b5c36546478
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990251"
---
# <a name="azure-management---monitoring"></a>Správa Azure – monitorování

Monitorování v Azure je jedním z aspektů správy Azure.  Tento článek stručně popisuje různé oblasti správy, které jsou nezbytné pro nasazení a údržbu vašich aplikací a prostředků v Azure, a obsahuje odkazy na dokumentaci, která vám umožní začít.

## <a name="management-in-azure"></a>Správa v Azure

Termín správa označuje úlohy a procesy nezbytné pro udržování obchodních aplikací a prostředků, které je podporují.  Azure má několik služeb a nástrojů, které společně poskytují kompletní správu pro vaše aplikace spuštěné nejen v Azure, ale také v ostatních cloudech a v místním prostředí.  Pochopení různých nástrojů, které jsou k dispozici, a jejich současného využití při zpracování celé řady scénářů správy je prvním krokem návrhu při kompletního prostředí pro správu.

Následující diagram ukazuje různé oblasti správy, které jsou nutné k údržbě libovolné aplikace nebo prostředku.  Na tyto různé oblasti lze nahlížet z pohledu životního cyklu, v jehož rámci se postupně po sobě vyžadují během životnosti prostředku.  Začíná to počátečním nasazením, přes průběžný provoz až po vyřazení z provozu.

![Možnosti správy](media/management-overview/management-capabilities.png)


Následující části stručně popisují různé oblasti správy a poskytují odkazy na podrobný obsah věnovaný hlavním službám Azure, které jsou určené k jejich řešení.

## <a name="monitor"></a>Monitorování
Monitorování je shromažďování a analýza dat s cílem určit výkon, stav a dostupnost vaší obchodní aplikace a prostředků, na kterých závisí. Efektivní strategie monitorování vám pomůže porozumět provozu jednotlivých komponent vaší aplikace a prodloužit dobu provozu díky proaktivnímu upozorňování na zásadní potíže, abyste je mohli vyřešit předtím, než začnou způsobovat problémy. Monitorování v Azure provádí primárně služba [Azure Monitor](../azure-monitor/overview.md), která poskytuje běžná úložiště pro ukládání dat monitorování, více zdrojů dat pro shromažďování dat z různých vrstev podporujících vaši aplikaci a funkce pro analýzy a odpovědi na shromážděná data.

## <a name="configure"></a>Konfigurace
Konfigurace označuje počáteční nasazení a konfiguraci aplikací a prostředků a jejich průběžnou údržbu pomocí oprav a aktualizací.  Automatizace těchto úloh pomocí skriptů a zásad umožňuje eliminovat redundance, minimalizovat čas a úsilí a zvýšit přesnost a efektivitu.  [Azure Automation](../automation/automation-intro.md) poskytuje převážnou část služeb pro automatizaci úloh konfigurace.  Kromě runbooků pro automatizaci procesů poskytuje správu aktualizací a konfigurací, která pomáhá při správě konfigurací prostřednictvím zásad a při identifikaci a nasazování aktualizací.

## <a name="govern"></a>Řízení
Zásady správného řízení poskytují mechanismy a postupy pro zajištění kontroly nad vašimi aplikacemi a prostředky v Azure.  Zahrnují plánování iniciativ a nastavení strategických priorit.  Zásady správného řízení v Azure jsou primárně implementované pomocí dvou služeb.  [Azure Policy](../governance/policy/overview.md) pomáhá vytvářet, přiřazovat a spravovat definice zásad, které u vašich prostředků vynucují různá pravidla a akce, aby tyto prostředky i nadále odpovídaly vašim firemním standardům a smlouvám o úrovni služeb. [Azure Cost Management by Cloudyn](../cost-management/overview.md) umožňuje sledovat využití cloudu a výdaje za prostředky Azure a dalších poskytovatelů cloudu, včetně AWS a Googlu.

## <a name="secure"></a>Zabezpečení
Správa zabezpečení vašich aplikací, prostředků a dat zahrnuje kombinaci vyhodnocování hrozeb, shromažďování a analýzy dat zabezpečení a zajištění, že vaše aplikace a prostředky jsou navržené a nakonfigurované zabezpečeným způsobem.  Monitorování zabezpečení a analýzu hrozeb zajišťuje služba [Azure Security Center](../security-center/security-center-intro.md), která poskytuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami.  Měli byste si také projít [úvod do zabezpečení Azure](../security/azure-security.md), kde najdete kompletní informace o zabezpečení v Azure a pokyny pro zabezpečenou konfiguraci prostředků Azure.


## <a name="protect"></a>Ochrana
Ochrana znamená zajištění, že vaše aplikace a data jsou vždycky k dispozici, a to i v případě výpadků mimo vaši kontrolu.  Ochranu v Azure zajišťují dvě služby.  [Azure Backup](../backup/backup-introduction-to-azure-backup.md) zajišťuje zálohování a obnovení vašich dat, a to v cloudu i v místním prostředí.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md) poskytuje vysokou dostupnost vašich aplikací díky zajištění obchodní kontinuity a okamžitého obnovení v případě havárie.

## <a name="migrate"></a>Migrace 
Migrace označuje přenos úloh, které jsou aktuálně spuštěné v místním prostředí, do cloudu Azure.  [Azure Migrate](../migrate/migrate-overview.md) je služba, která pomáhá vyhodnotit vhodnost migrace místních virtuálních počítačů do Azure, včetně odhadu rozsahu na základě výkonu a souvisejících nákladů.  Azure Site Recovery vám pomůže s vlastní migrací virtuálních počítačů [z místního prostředí](../site-recovery/migrate-tutorial-on-premises-azure.md) nebo [z Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  [Azure Database Migration](../dms/dms-overview.md) vám pomůže s migrací několika databázových zdrojů na datové platformy Azure.

