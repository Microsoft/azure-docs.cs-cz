---
title: Přehled správy Azure – zásady správného řízení Azure
description: Přehled oblastí správy aplikací a prostředků Azure s odkazy na obsah v nástrojích pro správu Azure.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75980903"
---
# <a name="overview-of-management-services-in-azure"></a>Přehled služeb správy v Azure

Zásadsprávné řízení v Azure je jedním aspektem Azure Management. Tento článek popisuje různé oblasti správy pro nasazení a údržbu prostředků v Azure.

Termín správa označuje úlohy a procesy nezbytné pro udržování obchodních aplikací a prostředků, které je podporují. Azure má mnoho služeb a nástrojů, které spolupracují na zajištění kompletní správy. Tyto služby nejsou jenom pro prostředky v Azure, ale také v jiných cloudech a v místním prostředí. Pochopení různých nástrojů a jejich spolupráce je prvním krokem při navrhování kompletního prostředí pro správu.

Následující diagram ukazuje různé oblasti správy, které jsou nutné k údržbě libovolné aplikace nebo prostředku. Tyto různé oblasti lze považovat za životní cyklus. Každá oblast je vyžadována v nepřetržitém sledu po celou dobu životnosti zdroje. Tento životní cyklus prostředků začíná počátečnínasazení, prostřednictvím pokračování operace a nakonec při vyřazení.

![Disciplíny správy v Azure](../monitoring/media/management-overview/management-capabilities.png)

Žádná jediná služba Azure zcela nevyplňuje požadavky na určitou oblast správy. Místo toho je každý realizován několika službami, které spolupracují. Některé služby, například Application Insights, poskytují cílené funkce monitorování webových aplikací. Jiné, jako jsou protokoly Azure Monitor, ukládají data správy pro jiné služby. Tato funkce umožňuje analyzovat data různých typů shromážděných různými službami.

Následující části stručně popisují různé oblasti správy a poskytují odkazy na podrobný obsah věnovaný hlavním službám Azure, které jsou určené k jejich řešení.

## <a name="monitor"></a>Monitorování

Monitorování je shromažďování a analýza dat pro audit výkonu, stavu a dostupnosti vašich prostředků. Efektivní strategie monitorování vám pomůže pochopit provoz komponent a zvýšit dobu provozus oznámeními. Přečtěte si přehled monitorování, který zahrnuje různé služby používané v [monitorování aplikací a prostředků Azure](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Konfigurace

Konfigurace odkazuje na počáteční nasazení a konfiguraci prostředků a průběžnou údržbu.
Automatizace těchto úloh umožňuje eliminovat redundanci, minimalizovat čas a úsilí a zvýšit přesnost a efektivitu. [Azure Automation](../automation/automation-intro.md) poskytuje převážnou část služeb pro automatizaci úloh konfigurace. Zatímco sady Runbook zpracovávají automatizaci procesů, pomáhají při správě konfigurace při správě konfigurace.

## <a name="govern"></a>Řízení

Zásady správného řízení poskytují mechanismy a postupy pro zajištění kontroly nad vašimi aplikacemi a prostředky v Azure. Zahrnují plánování iniciativ a nastavení strategických priorit.
Zásady správného řízení v Azure jsou primárně implementované pomocí dvou služeb. [Azure Policy](./policy/overview.md) umožňuje vytvářet, přiřazovat a spravovat definice zásad k vynucení pravidel pro vaše prostředky. Tato funkce udržuje tyto prostředky v souladu s vašimi podnikovými standardy. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) umožňuje sledovat využití cloudu a výdaje pro vaše prostředky Azure a další poskytovatele cloudu.

## <a name="secure"></a>Zabezpečení

Spravujte zabezpečení svých prostředků a dat. Bezpečnostní program zahrnuje hodnocení hrozeb, shromažďování a analýzu dat a dodržování předpisů vašich aplikací a prostředků. Monitorování zabezpečení a analýzu hrozeb poskytuje [Azure Security Center](../security-center/security-center-intro.md), který zahrnuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Najdete [v tématu Úvod do zabezpečení Azure](../security/fundamentals/overview.md) pro komplexní informace a pokyny k zabezpečení prostředků Azure.

## <a name="protect"></a>Ochrana

Ochrana se týká udržování aplikací a dat k dispozici, a to i při výpadcích, které jsou mimo vaši kontrolu. Ochranu v Azure zajišťují dvě služby. [Azure Backup](../backup/backup-introduction-to-azure-backup.md) zajišťuje zálohování a obnovení vašich dat, a to v cloudu i v místním prostředí. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) poskytuje kontinuitu podnikání a okamžité obnovení během havárie.

## <a name="migrate"></a>Migrace

Migrace označuje přenos úloh, které jsou aktuálně spuštěné v místním prostředí, do cloudu Azure.
[Azure Migrate](../migrate/migrate-overview.md) je služba, která vám pomůže posoudit vhodnost migrace místních virtuálních počítačů do Azure. Azure Site Recovery migruje virtuální počítače [z místních](../site-recovery/migrate-tutorial-on-premises-azure.md) nebo [z Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Migrace databáze Azure](../dms/dms-overview.md) vám pomůže při migraci databázových zdrojů na datové platformy Azure.
