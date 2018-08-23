---
title: Azure Resource Health – nejčastější dotazy | Dokumentace Microsoftu
description: Přehled Azure Resource Health
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: ''
editor: ''
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 03/27/2018
ms.author: BernardoAMunoz
ms.openlocfilehash: 1e22c62c291909bb4079ebaf389cfbd15ae9d8d5
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055633"
---
# <a name="azure-resource-health-faq"></a>Azure Resource Health – nejčastější dotazy
Přečtěte si odpovědi na běžné otázky o Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>Co je Azure Resource Health?
Resource Health pomáhá při diagnostice a získání podpory v případě, že potíže s Azure ovlivňují vaše prostředky. Informuje o aktuálním a dřívějším stavu prostředků a pomáhá zmírnit problémy. Resource Health poskytuje technickou podporu, když potřebujete pomoc při potížích se službami Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Co je Resource Health určená?
Jakmile byl zjištěn problém s prostředkem, Resource Health pomůže diagnostikovat původní příčinu. Poskytuje nápovědu ke zmírnění problému a technickou podporu, když budete potřebovat další pomoc s problémy se službami Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Doplněk pro kontroly stavu, které provádí služba Resource Health?
Služba Resource health provádí na základě různých kontrol [typ prostředku](resource-health-checks-resource-types.md). Tyto kontroly jsou navržené k implementaci tři druhy problémů: 
- Restartování v případě neplánovaných událostí, například neočekávané hostitele
- Plánované událostí, jako jsou aktualizace plánované hostitelský operační systém
- Události aktivované uživatelské akce, třeba uživatel restartování virtuálního počítače

## <a name="what-does-each-of-the-health-status-mean"></a>Co každý stav znamená?
Existují tři stavy různých stavů:
- K dispozici: Nejsou známé žádné problémy, na platformě Azure, který by mohl být ovlivňující tento prostředek
- Není k dispozici: Resource health zjistila problémy, které mají vliv na prostředek
- Neznámé: Resource health nemůže určit stav tohoto prostředku vzhledem k tomu, že se zastavila, obdrží informace o tom. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Co znamená Neznámý stav Je něco špatného Můj prostředek?
Stav je nastavena na neznámý při zastavení Resource Health obdrží informace o konkrétní prostředek. Přestože tento stav není úplným a rozhodujícím údaj o stavu prostředků v případech, kdy dochází k problému, může to znamenat, že došlo k potížím Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Jak lze získat nápovědu pro prostředek, který je k dispozici?
Můžete odeslat žádost o podporu z okna Resource Health. Není nutné podporu smlouvy s Microsoftem se otevře žádost, když je prostředek nedostupný protože události platformy.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Resource Health rozlišovat mezi nedostupnost notaci podle problémy s platformou a něco, co jsme to udělali?
Ano, pokud prostředek není k dispozici, Resource Health identifikuje původní příčinu v rámci jedné z těchto kategorií: 
-   Uživatelem iniciované akce
-   Plánované události 
-   Neplánované události

Na portálu se zobrazí uživatelem iniciované akce pomocí modré ikona během plánovaných i neplánovaných událostí jsou uvedeny pomocí červená varovná ikona. Další podrobnosti jsou uvedeny v [přehled Resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Můžu integrovat s Moje monitorovací nástroje Resource Health?
Služba Resource health je služba navržená tak, aby vám pomohou diagnostikovat a zmírnit problémy se službami Azure, které ovlivňují vaše prostředky. Při použití rozhraní API stavu prostředku můžete prostřednictvím kódu programu získat stav, doporučujeme že použít metriky pro monitorování prostředků. Po zjištění problému Resource Health vám pomůže určit původní příčinu a provede vás těmito možnostmi akcí k jejich řešení. Navštivte [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) Další informace o použití metrik ke kontrole vašich prostředků.

## <a name="where-do-i-find-resource-health"></a>Kde najdu Resource Health?
Po přihlášení k webu Azure portal, mají několik možností, dostanete Resource Health:
- Přejděte k prostředku. V levém navigačním panelu vyberte **Resource health**
- Přejděte do okna Azure Service Health.  V levém navigačním panelu vyberte **Resource health**.
- Otevřít **Nápověda a podpora** okno výběrem otazníku v pravém horním rohu portálu a pak vyberete **Nápověda a podpora**. Po otevření okna vyberte **Resource health**

Rozhraní API stavu prostředku můžete také použít k získání informací o stavu vašich prostředků.

## <a name="is-resource-health-available-for-all-resource-types"></a>Je k dispozici pro všechny typy prostředků Resource Health?
Seznam kontrol stavu a typy prostředků, které jsou podporované prostřednictvím služby Resource Health najdete [tady](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Co mám dělat, když se zobrazuje Můj prostředek k dispozici, ale věřím, že není?"
Při kontrole stavu prostředku, vpravo pod stavu můžete kliknout na **sestavy nesprávný stav**. Před odesláním do sestavy, máte možnost poskytnout další podrobnosti o proč si myslíte, že aktuální stav je nesprávný.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Resource Health je k dispozici pro všemi oblastmi Azure? 
Služba Resource health je k dispozici v ve všech zeměpisných oblastech Azure s výjimkou následujících oblastech:
- USA (Gov) – Virginia
- US Gov – Iowa
- US DoD – východ
- US DoD – střed
- Německo – střed
- Německo – severovýchod

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Čím se liší od stav služby Azure nebo řídicí panel stavu služeb Resource Health?
Služba Resource Health na základě informací poskytnutých je konkrétnější než co poskytuje stav služby Azure nebo řídicí panel stavu služeb.

Vzhledem k tomu [stav Azure](https://status.azure.com) a řídicí panel stavu služeb obsahují informace o problémech, které mají vliv široké škále zákazníků (třeba určitá oblast Azure), Resource Health poskytuje podrobnější událostí, které jsou relevantní jenom pro konkrétní prostředek. Například pokud hostitel neočekávaně restartuje, Resource Health upozorní pouze zákazníci, jehož virtuální počítače běžely na tomto hostiteli.

Je důležité si všimněte si, že poskytují kompletní přehled událostí dopadu na vaše prostředky, Resource Health poskytuje také informace o události, které jsou publikované na řídicím panelu stavu služeb.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Je potřeba aktivovat pro jednotlivé prostředky Resource Health?
Ne, informace o stavu je k dispozici pro všechny typy prostředků jsou dostupné prostřednictvím služby Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Potřebujeme pro naši organizaci povolit Resource Health?
Ne.  Azure Resource Health je dostupný na webu Azure portal bez požadavků na instalaci.

## <a name="is-resource-health-available-free-of-charge"></a>Služba Resource Health je k dispozici zdarma?
Ano.  Azure Resource Health je zdarma.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Co jsou doporučení, které poskytuje služba Resource Health?
Podle stavu, Resource Health poskytuje doporučení s cílem zkrácení doby že trvání řešení potíží. Pro prostředky dojde k výběru doporučení v tom, jak řešit nejčastější problémy zákazníků. Pokud je prostředek nedostupný z důvodu Azure neplánované události, zaměřuje bude na pomáhá během a po dokončení procesu obnovení. 

## <a name="next-steps"></a>Další postup

Další informace o službě Resource Health:
-  [Přehled Azure Resource Health](Resource-health-overview.md)
-  [Typy prostředků a kontroly stavu dostupné prostřednictvím služby Azure Resource Health](resource-health-checks-resource-types.md)
