---
title: Nejčastější dotazy týkající se stavu prostředků Azure
description: Přehled stavu prostředků Azure
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: 7459a29dca01dc186d75b4545f89068569975607
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985027"
---
# <a name="azure-resource-health-faq"></a>Nejčastější dotazy týkající se stavu prostředků Azure
Seznamte se s odpověďmi na běžné otázky týkající se stavu prostředků Azure.

## <a name="what-is-azure-resource-health"></a>Co je stav prostředků Azure?
Resource Health pomáhá při diagnostice a získání podpory v případě, že potíže s Azure ovlivňují vaše prostředky. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc při potížích se službami Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>K čemu je zdroj Health určen?
Jakmile byl zjištěn problém s prostředkem, může vám stav prostředků pomoci diagnostikovat hlavní příčinu. Poskytuje pomoc ke zmírnění problému a technické podpory, když potřebujete další pomoc s problémy se službami Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Jaké kontroly stavu provádějí stav prostředků?
Stav prostředků provádí různé kontroly na základě [typu prostředku](resource-health-checks-resource-types.md). Tyto kontroly jsou navrženy tak, aby implementovaly tři typy problémů: 
- Neplánované události, například neočekávané restartování hostitele
- Plánované události, jako jsou plánované aktualizace hostitelského operačního systému
- Události spouštěné akcemi uživatelů, například uživatel restartující virtuální počítač

## <a name="what-does-each-of-the-health-status-mean"></a>Co znamená každý zdravotní stav?
Existují tři různé stavy:
- K dispozici: Na platformě Azure nejsou žádné známé problémy, které by mohly mít vliv na tento prostředek.
- Není k dispozici: Stav prostředků zjistil problémy, které mají vliv na prostředek.
- Neznámý: Stav prostředku nemůže určit stav prostředku, protože o něm přestal přijímat informace. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Co znamená neznámý stav? Je něco v nepořádku s mým zdrojem?
Stav je nastaven na neznámý, když stav prostředku přestane přijímat informace o určitém prostředku. Zatímco tento stav není definitivní označení stavu prostředku, v případech, kdy dochází k problémům, může znamenat, že je problém Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Jak lze získat nápovědu pro prostředek, který není k dispozici?
Můžete odeslat žádost o podporu z okna Stav prostředků. K otevření požadavku, když prostředek není k dispozici, protože události platformy nejsou k dispozici, nepotřebujete se společností Microsoft smlouvu o podpoře se společností Microsoft.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Rozlišuje stav prostředků mezi nedostupností způsobenou problémy s platformou a něčím, co jsem udělal?
Ano, pokud prostředek není k dispozici, stav prostředků identifikuje hlavní příčinu v jedné z těchto kategorií: 
-   Akce iniciovaná uživatelem
-   Plánovaná událost 
-   Neplánovaná událost

Na portálu jsou akce iniciované uživatelem zobrazeny pomocí modré ikony oznámení, zatímco plánované a neplánované události jsou zobrazeny pomocí červené výstražné ikony. Další podrobnosti jsou uvedeny v [přehledu stav zdrojů](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Mohu integrovat resource health s mými monitorovacími nástroji?
Stav prostředků má [podporu náhledu](resource-health-alert-arm-template-guide.md) pro výstrahy založené na protokolu aktivit. Výstrahy protokolu aktivit používají [skupiny akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) k upozornění uživatelů, že byla aktivována výstraha. Skupiny akcí podporují různé kanály oznámení, jako je e-mail, SMS, webhook a akce ITSM.

## <a name="where-do-i-find-resource-health"></a>Kde najdu zdraví zdrojů?
Po přihlášení k portálu Azure existuje několik způsobů, jak získat přístup ke stavu prostředků:
- Přejděte na svůj prostředek. V levém navigačním panelu vyberte **Stav prostředků.**
- Přejděte do okna Azure Service Health.  V levém navigačním panelu vyberte **položku Stav prostředků**.
- Otevřete okno **Nápověda + Podpora** tak, že v pravém horním rohu portálu vyberete otazník a potom vyberete **nápovědu + podporu**. Po otevření okna vyberte **Stav prostředků**

K získání informací o stavu prostředků můžete také použít rozhraní API pro stav prostředků.

## <a name="is-resource-health-available-for-all-resource-types"></a>Je stav prostředků k dispozici pro všechny typy prostředků?
Seznam kontrol stavu a typů prostředků podporovaných prostřednictvím zdroje zdraví naleznete [zde](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Co mám dělat, když se můj zdroj zobrazuje k dispozici, ale věřím, že tomu tak není?"
Při kontrole stavu prostředku můžete přímo pod stavem kliknout na **nahlásit nesprávný stav**. Před odesláním sestavy máte možnost poskytnout další podrobnosti o tom, proč se domníváte, že aktuální stav je nesprávný.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Je stav prostředků k dispozici pro všechny oblasti Azure? 
Stav prostředků je k dispozici ve všech geografických službách Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Jak se stav prostředků liší od stavu Azure nebo řídicího panelu stavu služby?
Informace poskytované službou Resource Health jsou konkrétnější než informace poskytované stavem Azure nebo řídicím panelem stavu služby.

Vzhledem k [tomu, že stav Azure](https://status.azure.com) a řídicí panel Stavu služby vás informují o problémech se službami, které ovlivňují širokou sadu zákazníků (například oblast Azure), stav prostředků zveřejňuje podrobnější události, které jsou relevantní pouze pro konkrétní prostředek. Například pokud hostitel neočekávaně restartuje, resource health upozorní pouze ty zákazníky, jejichž virtuální počítače byly spuštěny na tomto hostiteli.

Je důležité si všimnout, že k zajištění úplné viditelnosti událostí, které mají vliv na vaše prostředky, stav prostředků také zobrazí události publikované v řídicím panelu Stav služby.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Musím aktivovat stav prostředků pro každý zdroj?
Ne, informace o stavu jsou k dispozici pro všechny typy prostředků, které jsou k dispozici prostřednictvím stavu prostředků. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Musíme povolit stav prostředků pro mou organizaci?
Ne.  Azure Resource Health je přístupný na webu Azure Portal bez jakýchkoli požadavků na nastavení.

## <a name="is-resource-health-available-free-of-charge"></a>Je zdraví zdrojů k dispozici zdarma?
Ano.  Azure Resource Health je zdarma.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Jaká doporučení poskytuje health zdrojů?
Na základě stavu, Resource Health poskytuje doporučení s cílem zkrátit čas, který jste strávili řešení potíží. U dostupných zdrojů se doporučení zaměřují na řešení nejčastějších problémů, se kterými se zákazníci setkávají. Pokud prostředek není k dispozici z důvodu neplánované události Azure, bude důraz na pomoc během a po procesu obnovení. 

## <a name="next-steps"></a>Další kroky

Další informace o stavu zdrojů:
-  [Přehled stavu prostředků Azure](Resource-health-overview.md)
-  [Typy prostředků a kontroly stavu dostupné prostřednictvím služby Azure Resource Health](resource-health-checks-resource-types.md)
