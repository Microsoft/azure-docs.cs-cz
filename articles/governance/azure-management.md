---
title: Přehled správy Azure – zásady správného řízení Azure
description: Přehled oblastí správy pro aplikace a prostředky Azure s odkazy na obsah v nástrojích pro správu Azure.
ms.date: 07/06/2020
ms.topic: overview
ms.openlocfilehash: 81d655db706a7330fc541724d490a4885cc2fe8b
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86041910"
---
# <a name="what-are-the-azure-management-areas"></a>Co jsou oblasti správy Azure?

Zásady správného řízení v Azure jsou jedním aspektem správy Azure. Tento článek popisuje různé oblasti správy pro nasazení a údržbu vašich prostředků v Azure.

Termín správa označuje úlohy a procesy nezbytné pro udržování obchodních aplikací a prostředků, které je podporují. Azure má spoustu služeb a nástrojů, které spolupracují za účelem zajištění úplné správy. Tyto služby nejsou jenom prostředky v Azure, ale také v jiných cloudech i v místním prostředí. V rámci navrhování kompletního prostředí pro správu je třeba pochopit různé nástroje a jejich spolupráci.

Následující diagram ukazuje různé oblasti správy, které jsou nutné k údržbě libovolné aplikace nebo prostředku. Tyto různé oblasti si můžete představit jako životní cyklus. Každá oblast je požadována při nepřetržitém pokusům o životnosti prostředku. Tento životní cyklus prostředků začíná počátečním nasazením, prostřednictvím pokračování operace a nakonec po vyřazení.

:::image type="content" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Disciplíny správy v Azure" border="false":::

Žádná jediná služba Azure zcela neplní požadavky určité oblasti správy. Místo toho jsou všechny realizovány několika službami současně. Některé služby, například Application Insights, poskytují cílené funkce monitorování webových aplikací. Ostatní, jako jsou protokoly Azure Monitor, ukládají data správy pro další služby. Tato funkce umožňuje analyzovat data různých typů shromažďovaných různými službami.

Následující části stručně popisují různé oblasti správy a poskytují odkazy na podrobný obsah věnovaný hlavním službám Azure, které jsou určené k jejich řešení.

## <a name="monitor"></a>Monitorování

Monitorování je proces shromažďování a analýzy dat pro audit výkonu, stavu a dostupnosti vašich prostředků. Efektivní strategie monitorování vám pomůže pochopit fungování komponent a zvýšit dobu provozu pomocí oznámení. Přečtěte si přehled monitorování, které pokrývá různé služby používané při [monitorování aplikací a prostředků Azure](../azure-monitor/overview.md).

## <a name="configure"></a>Konfigurace

Konfigurace odkazuje na počáteční nasazení a konfiguraci prostředků a průběžnou údržbu.
Automatizace těchto úloh vám umožní eliminovat redundanci, minimalizovat čas a úsilí a zvýšit přesnost a efektivitu. [Azure Automation](../automation/automation-intro.md) poskytuje převážnou část služeb pro automatizaci úloh konfigurace. Zatímco Runbooky zpracovávají automatizaci procesů, konfiguraci a správu aktualizací pomáhají při správě konfigurace.

## <a name="govern"></a>Řízení

Zásady správného řízení poskytují mechanismy a postupy pro zajištění kontroly nad vašimi aplikacemi a prostředky v Azure. Zahrnují plánování iniciativ a nastavení strategických priorit.
Zásady správného řízení v Azure jsou primárně implementované pomocí dvou služeb. [Azure Policy](./policy/overview.md) umožňuje vytvářet, přiřazovat a spravovat definice zásad pro vymáhání vašich prostředků.
Tato funkce udržuje tyto prostředky v souladu s vašimi podnikovými standardy.
[Azure cost management](../cost-management-billing/cost-management-billing-overview.md) vám umožňuje sledovat využití cloudu a výdaje za prostředky Azure a další cloudové poskytovatele.

## <a name="secure"></a>Zabezpečení

Spravujte zabezpečení svých prostředků a dat. Bezpečnostní program zahrnuje hodnocení hrozeb, shromažďování a analýzy dat a kompatibilitu vašich aplikací a prostředků. Monitorování zabezpečení a analýza hrozeb poskytuje [Azure Security Center](../security-center/security-center-intro.md), což zahrnuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudy. Podrobné informace a pokyny k zabezpečení prostředků Azure najdete v tématu [Úvod do zabezpečení Azure](../security/fundamentals/overview.md) .

## <a name="protect"></a>Ochrana

Ochrana znamená, že vaše aplikace a data budou k dispozici, a to i s výpadky, které jsou mimo vaši kontrolu. Ochranu v Azure zajišťují dvě služby. [Azure Backup](../backup/backup-overview.md) zajišťuje zálohování a obnovení vašich dat, a to v cloudu i v místním prostředí. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) zajišťuje kontinuitu podnikových aplikací a okamžité obnovení při havárii.

## <a name="migrate"></a>Migrace

Migrace označuje přenos úloh, které jsou aktuálně spuštěné v místním prostředí, do cloudu Azure.
[Azure Migrate](../migrate/migrate-services-overview.md) je služba, která vám pomůže vyhodnotit vhodnost migrace místních virtuálních počítačů do Azure. Azure Site Recovery migruje virtuální počítače [z místního](../site-recovery/migrate-tutorial-on-premises-azure.md) prostředí nebo [z Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Azure Database Migration](../dms/dms-overview.md) vám pomůže při migraci zdrojů databáze na datové platformy Azure.

## <a name="next-steps"></a>Další kroky

Další informace o správě Azure najdete v těchto článcích:

- Podívejte se na [Centrum zásad správného řízení Azure](./index.yml).
- Viz zásady [správného řízení v rozhraní cloudu pro přijetí Azure](/azure/cloud-adoption-framework/govern/) .
