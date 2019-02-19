---
title: Přehled správy Azure – Azure zásad správného řízení
description: Přehled oblastí správy pro aplikace Azure a prostředky s odkazy na obsah na nástroje pro správu Azure.
author: DCtheGeek
manager: carmonm
ms.service: governance
ms.topic: article
ms.date: 12/06/2018
ms.author: dacoulte
ms.openlocfilehash: c38d19b8528399ae09d92e48ed7dfc17c18938b7
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338066"
---
# <a name="management-in-azure"></a>Správa v Azure

Zásady správného řízení v Azure je jeden z aspektů správy Azure. Tento článek popisuje různé oblasti správy pro nasazení a údržbu vašich prostředků v Azure.

Termín správa označuje úlohy a procesy nezbytné pro udržování obchodních aplikací a prostředků, které je podporují. Azure nabízí mnoho služeb a nástrojů, které společně poskytují kompletní správu. Tyto služby nejsou pouze pro prostředky v Azure, ale také v ostatních cloudech a v místním prostředí. Pochopení různých nástrojů a jak spolu fungují je prvním krokem při navrhování kompletního prostředí pro správu.

Následující diagram ukazuje různé oblasti správy, které jsou nutné k údržbě libovolné aplikace nebo prostředku. Tyto různé oblasti lze považovat za životní cyklus. Každou oblast, kterou je potřeba průběžné postupně během životnosti prostředku. Tento prostředek životní cyklus začíná počáteční nasazení prostřednictvím nepřetržitý provoz a nakonec po vyřazení.

![Správa oborů](../monitoring/media/management-overview/management-capabilities.png)

Žádná samostatná služba Azure nesplňuje kompletně požadavky konkrétní oblasti správy. Místo toho každá se provádí několik služeb společně. Některé služby, jako jsou Application Insights, poskytují cílené funkce monitorování pro webové aplikace. Jiné, jako jsou protokoly Azure monitoru, ukládat data správy pro jiné služby. Tato funkce umožňuje analyzovat data různých typů shromážděná různými službami.

Následující části stručně popisují různé oblasti správy a poskytují odkazy na podrobný obsah věnovaný hlavním službám Azure, které jsou určené k jejich řešení.

## <a name="monitor"></a>Monitorování

Monitorování je shromažďování a analýza dat auditování výkonu, stavu a dostupnosti vašich prostředků. Efektivní strategie monitorování vám pomůže porozumět provozu součásti a prodloužit dobu provozu Díky oznámením. Přečtěte si přehled o monitorování, která zahrnuje různé služby používané v [aplikací a prostředků Monitoring Azure](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Konfigurace

Konfigurace označuje počáteční nasazení a konfigurace prostředků a průběžnou péči.
Automatizace těchto úloh umožňuje eliminovat redundance, minimalizovat čas a úsilí a zvýšit přesnost a efektivitu. [Azure Automation](../automation/automation-intro.md) poskytuje převážnou část služeb pro automatizaci úloh konfigurace. Při zpracování sady runbook automatizace procesů, konfigurace a Správa aktualizací pomáhají při správě konfigurace.

## <a name="govern"></a>Řízení

Zásady správného řízení poskytují mechanismy a postupy pro zajištění kontroly nad vašimi aplikacemi a prostředky v Azure. Zahrnují plánování iniciativ a nastavení strategických priorit.
Zásady správného řízení v Azure jsou primárně implementované pomocí dvou služeb. [Služba Azure Policy](./policy/overview.md) umožňuje vytvářet, přiřazovat a spravovat definice zásad a vynucovat pravidla pro vaše prostředky. Tato funkce zajišťuje těchto prostředků v souladu s firemními standardy. [Azure Cost Management by Cloudyn](../cost-management/overview.md) umožňuje sledovat využití cloudu a výdaje za prostředky Azure a dalších poskytovatelů cloudu.

## <a name="secure"></a>Zabezpečení

Správa zabezpečení vašich prostředků a data. Program zabezpečení zahrnuje posouzení hrozeb, shromažďování a analýza dat a dodržování předpisů u vašich aplikací a prostředků. Analýza hrozeb a monitorování zabezpečení jsou k dispozici v [Azure Security Center](../security-center/security-center-intro.md), která poskytuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Zobrazit [Úvod do zabezpečení Azure](../security/azure-security.md) komplexní informace a doprovodné materiály k zabezpečení prostředků Azure.

## <a name="protect"></a>Ochrana

Ochrana znamená udržování vašich aplikací a dat, které jsou k dispozici, i s výpadky, které jsou mimo vaši kontrolu. Ochranu v Azure zajišťují dvě služby. [Azure Backup](../backup/backup-introduction-to-azure-backup.md)zajišťuje zálohování a obnovení vašich dat v cloudu nebo místně. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) poskytuje obchodní kontinuity a okamžitého obnovení při havárii.

## <a name="migrate"></a>Migrace

Migrace označuje přenos úloh, které jsou aktuálně spuštěné v místním prostředí, do cloudu Azure.
[Azure Migrate](../migrate/migrate-overview.md) je služba, která vám pomůže vyhodnotit vhodnost migrace místních virtuálních počítačů do Azure. Azure Site Recovery migrovat virtuální počítače [z místního](../site-recovery/migrate-tutorial-on-premises-azure.md) nebo [z Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Azure Database Migration](../dms/dms-overview.md) vám pomůže při migraci databázových zdrojů na datové platformy Azure.