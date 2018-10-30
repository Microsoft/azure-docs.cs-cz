---
title: Správa koncových bodů v Azure Traffic Manageru | Dokumentace Microsoftu
description: Tento článek vám pomůže při přidávání, odebírání, povolování a zakazování koncových bodů v Azure Traffic Manageru.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: kumud
ms.openlocfilehash: 35c3027f2b592f6b24075005829ae9376a7fca82
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987030"
---
# <a name="add-disable-enable-or-delete-endpoints"></a>Přidávání, zakazování, povolování nebo odstraňování koncových bodů

Funkce Web Apps v rámci služby Azure App Service již poskytuje funkce směrování provozu s převzetím služeb při selhání a s kruhovým dotazováním pro weby v datovém centru bez ohledu na režim příslušného webu. Azure Traffic Manager umožňuje určit směrování provozu s převzetím služeb při selhání a s kruhovým dotazováním pro weby a služby Cloud Services v různých datových centrech. Jako první krok pro poskytnutí těchto funkcí je třeba přidat příslušný koncový bod služby Cloud Services nebo webu do Traffic Manageru.

Můžete také zakázat jednotlivé koncové body, které jsou součástí profilu Traffic Manageru. Zakázaný koncový bod zůstává součástí profilu, profil však pracuje, jako by příslušný koncový bod neobsahoval. Tato akce je užitečná pro dočasné odebrání koncového bodu, který se nachází v režimu údržby nebo který se znovu nasazuje. Jakmile je koncový bod znovu spuštěn a pracuje, může být povolen.

> [!NOTE]
> Zakázání koncového bodu nijak nesouvisí s jeho stavem nasazení v Azure. Funkční koncový bod zůstane v provozu a může přijímat provoz i poté, co je v Traffic Manageru zakázán. Zakázání koncový bod v jednom profilu navíc nemá vliv na jeho stav v jiném profilu.

## <a name="to-add-a-cloud-service-or-an-app-service-endpoint-to-a-traffic-manager-profile"></a>Přidání cloudové služby nebo koncového bodu služby App Service do profilu služby Traffic Manager

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com).
2. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body**.
4. V zobrazeném okně **Koncové body** klikněte na **Přidat**.
5. Okno **Přidat koncový bod** vyplňte následujícím způsobem:
    1. V části **Typ** klikněte na **Koncový bod Azure**.
    2. Zadejte **Název**, podle kterého tento koncový bod rozpoznáte.
    3. V části **Typ cílového prostředku** zvolte z rozevírací nabídky vhodný typ prostředku.
    4. V části **Cílový prostředek** klikněte na selektor **Zvolit...** a v **okně Prostředky** se zobrazí výpis prostředků v rámci stejného předplatného. V zobrazeném okně **Prostředek** vyberte službu, kterou chcete přidat jako první koncový bod.
    5. V části **Priorita** vyberte **1**. Výsledkem bude, že veškerý provoz bude přicházet do tohoto koncového bodu, pokud bude v pořádku.
    6. Políčko **Přidat jako zakázaný** ponechte nezaškrtnuté.
    7. Klikněte na tlačítko **OK**.
6.  Opakujte kroky 4 a 5 a přidejte další koncový bod Azure. Nezapomeňte ho přidat s hodnotou **Priorita** nastavenou na **2**.
7.  Po přidání se oba koncové body zobrazí v okně **Profil služby Traffic Manager** a jejich stav monitorování bude **Online**.

> [!NOTE]
> Po přidání nebo odebrání koncového bodu z profilu metodou směrování provozu *Převzetí služeb při selhání* nemusí být seznam priorit přebírání služeb seřazen tak, jak si představujete. Pořadí tohoto seznamu priorit můžete upravit na stránce Konfigurace. Další informace najdete v části [Konfigurování směrování provozu s převzetím služeb při selhání](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Zakázání koncového bodu

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com).
2. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body**. 
4. Klikněte na koncový bod, který chcete zakázat, a v zobrazeném okně **Koncový bod** klikněte na **Upravit**.
5. V okně **Koncový bod** změňte stav koncového bodu na **Zakázáno** a pak klikněte na **Uložit**.
6. Klienti budou dál posílat data na koncový bod po dobu odpovídající hodnotě Time-to-Live (TTL). Hodnotu TTL můžete změnit na stránce Konfigurace profilu Traffic Manageru.

## <a name="to-enable-an-endpoint"></a>Povolení koncového bodu

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com).
2. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body**. 
4. Klikněte na koncový bod, který chcete zakázat, a v zobrazeném okně **Koncový bod** klikněte na **Upravit**.
5. V okně **Koncový bod** změňte stav koncového bodu na **Povoleno** a pak klikněte na **Uložit**.
6. Klienti budou dál posílat data na koncový bod po dobu odpovídající hodnotě Time-to-Live (TTL). Hodnotu TTL můžete změnit na stránce Konfigurace profilu Traffic Manageru.

## <a name="to-delete-an-endpoint"></a>Odstranění koncového bodu

1. V prohlížeči se přihlaste k webu [Azure Portal](http://portal.azure.com).
2. Na panelu hledání na portálu vyhledejte název **profilu služby Traffic Manager**, který chcete upravit, a pak na tento profil služby Traffic Manager klikněte v zobrazených výsledcích.
3. V okně **Profil služby Traffic Manager** v části **Nastavení** klikněte na **Koncové body**. 
4. Klikněte na koncový bod, který chcete zakázat, a v zobrazeném okně **Koncový bod** klikněte na **Upravit**.
5. V okně **Koncový bod** změňte stav koncového bodu na **Povoleno** a pak klikněte na **Uložit**.


## <a name="next-steps"></a>Další kroky

* [Správa profilů Traffic Manager](traffic-manager-manage-profiles.md)
* [Konfigurace metod směrování](traffic-manager-configure-routing-method.md)
* [Řešení potíží při sníženém výkonu Traffic Manageru](traffic-manager-troubleshooting-degraded.md)
* [Důležité informace o výkonu nástroje Traffic Manager](traffic-manager-performance-considerations.md)
* [Operace v Traffic Manageru (referenční informace k rozhraní API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

