---
title: Nejčastější dotazy k Azure Resource Health
description: Přehled Azure Resource Health
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: dc24688d8393b7a13e8c93d3f4d0171cce4c213c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570066"
---
# <a name="azure-resource-health-faq"></a>Nejčastější dotazy k Azure Resource Health
Přečtěte si odpovědi na nejčastější dotazy týkající se Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Co je Azure Resource Health?
Resource Health pomáhá při diagnostice a získání podpory v případě, že potíže s Azure ovlivňují vaše prostředky. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc při potížích se službami Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>K čemu Resource Health slouží?
Po zjištění problému s prostředkem vám Resource Health můžou diagnostikovat původní příčinu. Poskytuje pomoc při zmírnění potíží a technickou podporu, když potřebujete další pomoc s problémy se službami Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Jaké kontroly stavu provádí Resource Health?
Stav prostředku provádí různé kontroly na základě [typu prostředku](resource-health-checks-resource-types.md). Tyto kontroly jsou navržené tak, aby se implementovaly tři typy problémů: 
- Neplánované události, například neočekávané restartování hostitele
- Plánované události, jako jsou plánované aktualizace operačního systému hostitele
- Události aktivované akcemi uživatele, například uživatel, který restartuje virtuální počítač

## <a name="what-does-each-of-the-health-status-mean"></a>Co znamená každý stav?
Existují tři různé stavy stavu:
- K dispozici: na platformě Azure nejsou žádné známé problémy, které by mohly mít vliv na tento prostředek.
- Nedostupné: stav prostředků zjistil problémy, které mají vliv na prostředek.
- Neznámé: stav prostředku nemůže určit stav prostředku, protože přestal přijímat informace o něm. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Co znamená neznámý stav? Je něco v mém prostředku špatné?
Stav je nastaven na neznámý, pokud Resource Health zastaví příjem informací o konkrétním prostředku. I když tento stav není konečným náznakem stavu prostředku, v případech, kdy dochází k problémům, může to znamenat, že došlo k potížím s Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Jak získám pomoc pro prostředek, který není k dispozici?
Žádost o podporu můžete odeslat z okna Resource Health. K otevření žádosti, když je prostředek nedostupný, protože události platformy, nepotřebujete smlouvu o podpoře Microsoftu.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Rozlišuje Resource Health rozdíl mezi nedostupností způsobenou problémy s platformou a něčím?
Ano, pokud prostředek není k dispozici, Resource Health identifikuje hlavní příčinu v rámci jedné z těchto kategorií: 
-   Akce zahájené uživatelem
-   Plánovaná událost 
-   Neplánovaná událost

Na portálu se uživatelem iniciované akce zobrazují pomocí modré ikony oznámení, zatímco plánované a neplánované události se zobrazují pomocí červené výstražné ikony. Další podrobnosti najdete v [přehledu Resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Můžu Resource Health integrovat s nástroji pro monitorování?
Resource Health [podporuje](resource-health-alert-arm-template-guide.md) výstrahy založené na protokolu aktivit. Výstrahy protokolu aktivit používají [skupiny akcí](../azure-monitor/alerts/action-groups.md) k upozornění uživatelů na aktivaci výstrahy. Skupiny akcí podporují různé kanály oznámení, jako jsou e-maily, SMS, Webhooky a ITSM akce.

## <a name="where-do-i-find-resource-health"></a>Kde najdu Resource Health?
Až se přihlásíte k Azure Portal, máte k dispozici několik způsobů, jak získat přístup k Resource Health:
- Přejděte k prostředku. V levém navigačním panelu vyberte **Resource Health** .
- Otevřete okno Azure Service Health.  V levém navigačním panelu vyberte **stav prostředků**.

K získání informací o stavu vašich prostředků můžete použít také rozhraní Resource Health API.

## <a name="is-resource-health-available-for-all-resource-types"></a>Je Resource Health k dispozici pro všechny typy prostředků?
Seznam kontrol stavu a typů prostředků podporovaných prostřednictvím Resource Health najdete [tady](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Co mám dělat, když můj zdroj zobrazuje k dispozici, ale jsem přesvědčen, že není? "
Když kontrolujete stav prostředku, napravo pod stavem stav můžete kliknout na **sestava nesprávný stav stavu**. Před odesláním sestavy máte možnost poskytnout další podrobnosti o tom, proč se domníváte, že aktuální stav je nesprávný.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Je Resource Health k dispozici pro všechny oblasti Azure? 
Stav prostředku je k dispozici ve všech zeměpisných oblastech Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Jak se Resource Health liší od stavu Azure nebo řídicího panelu Service Health?
Informace, které poskytuje Resource Health, jsou konkrétnější, než je poskytované stavem Azure nebo řídicím panelem Service Health.

Vzhledem k tomu, že [stav Azure](https://status.azure.com) a řídicí panel Service Health informují o problémech se službami, které mají vliv na širokou škálu zákazníků (například oblast Azure), Resource Health zveřejňuje podrobnější události, které jsou relevantní jenom pro konkrétní prostředek. Například pokud se hostitel neočekávaně restartuje, Resource Health upozorní pouze zákazníky, jejichž virtuální počítače byly na tomto hostiteli spuštěny.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Potřebuji u každého prostředku aktivovat Resource Health?
Ne, informace o stavu jsou k dispozici pro všechny typy prostředků, které jsou k dispozici prostřednictvím Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Musíme Resource Health pro moji organizaci povolit?
No.  Azure Resource Health je k dispozici v Azure Portal bez jakýchkoli požadavků na instalaci.

## <a name="is-resource-health-available-free-of-charge"></a>Je Resource Health k dispozici zdarma?
Ano.  Azure Resource Health je bezplatná.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Jaká doporučení Resource Health poskytuje?
Na základě stavu Resource Health poskytuje doporučení s cílem zkrátit čas strávený odstraňováním potíží. V případě dostupných prostředků se v doporučeních zaměřujeme na řešení nejběžnějších problémů, ke kterým se zákazníci setkávají. Pokud prostředek není k dispozici z důvodu neplánované události Azure, zaměřte se na vás během procesu obnovení a po něm. 

## <a name="next-steps"></a>Další kroky

Další informace o Resource Health:
-  [Přehled Azure Resource Health](Resource-health-overview.md)
-  [Typy prostředků a kontroly stavu dostupné prostřednictvím služby Azure Resource Health](resource-health-checks-resource-types.md)
