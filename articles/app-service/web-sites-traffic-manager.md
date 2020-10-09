---
title: Řízení provozu pomocí Traffic Manager
description: Vyhledá osvědčené postupy pro konfiguraci Azure Traffic Manager při integraci s Azure App Service.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 040f84288c66f4506919e775b9ea41324b617cfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80437898"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Řízení provozu Azure App Service s využitím Azure Traffic Manager
> [!NOTE]
> Tento článek poskytuje souhrnné informace o Microsoft Azure Traffic Manager v souvislosti s Azure App Service. Další informace o samotném Azure Traffic Manager najdete na odkazech na konci tohoto článku.
> 
> 

## <a name="introduction"></a>Úvod
Pomocí služby Azure Traffic Manager můžete řídit způsob distribuce požadavků z webových klientů do aplikací ve službě Azure App Service. Pokud do profilu služby Azure Traffic Manager přidáte koncové body služby App Service, služba Azure Traffic Manager bude sledovat stav vašich aplikací App Service (spuštěné, zastavené nebo odstraněné), aby se mohla rozhodnout, do kterého z těchto koncových bodů se má směrovat provoz.

## <a name="routing-methods"></a>Metody směrování
Azure Traffic Manager používá čtyři různé metody směrování. Tyto metody jsou popsány v následujícím seznamu, protože se vztahují k Azure App Service.

* ** [Priorita](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** použijte primární aplikaci pro veškerý provoz a poskytněte zálohy pro případ, že primární nebo záložní aplikace nejsou k dispozici.
* ** [Vážená](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribuujte provoz napříč sadou aplikací, a to buď rovnoměrně, nebo podle vah, které definujete.
* ** [Výkon](../traffic-manager/traffic-manager-routing-methods.md#performance):** Pokud máte aplikace v různých geografických umístěních, použijte "nejbližší" aplikaci z hlediska nejnižší latence sítě.
* ** [Geografické](../traffic-manager/traffic-manager-routing-methods.md#geographic):** přímé uživatele na konkrétní aplikace na základě toho, ze kterého geografického umístění dotaz DNS pocházejí. 

Další informace najdete v tématu [metody směrování Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Profily App Service a Traffic Manager
Pokud chcete nakonfigurovat řízení provozu aplikace App Service, vytvořte v Azure Traffic Manager profil, který používá jednu ze čtyř výše popsaných metod Vyrovnávání zatížení, a pak přidejte koncové body (v tomto případě App Service), pro které chcete řídit provoz do profilu. Stav vaší aplikace (spuštěné, zastavené nebo odstraněné) se pravidelně komunikuje s profilem, aby služba Azure Traffic Manager mohla odpovídajícím způsobem směrovat provoz.

Při používání Azure Traffic Manager s Azure mějte na paměti následující body:

* Pro nasazení jenom aplikace v rámci stejné oblasti už App Service v rámci stejné oblasti poskytuje funkce převzetí služeb při selhání a kruhové dotazování bez ohledu na režim aplikace.
* Pro nasazení ve stejné oblasti, která používá App Service ve spojení s jinou cloudovou službou Azure, můžete kombinovat oba typy koncových bodů a povolit tak hybridní scénáře.
* V profilu můžete zadat jenom jeden App Service koncový bod na oblast. Když vyberete aplikaci jako koncový bod pro jednu oblast, zbývající aplikace v této oblasti nebudou k dispozici pro výběr tohoto profilu.
* Koncové body App Service, které zadáte v profilu Azure Traffic Manager, se zobrazí v části **názvy domén** na stránce konfigurace pro aplikaci v profilu, ale zde se nedají konfigurovat.
* Když přidáte aplikaci do profilu, **Adresa URL webu** na řídicím panelu na stránce portálu aplikace zobrazí adresu URL vlastní domény aplikace, pokud jste ji nastavili. V opačném případě se zobrazí adresa URL profilu Traffic Manager (například `contoso.trafficmanager.net` ). Přímý název domény aplikace i adresa URL Traffic Manager jsou viditelné na stránce konfigurace aplikace v části **názvy domén** .
* Vaše vlastní názvy domén pracují podle očekávání, ale kromě jejich přidání do aplikací musíte také nakonfigurovat mapu DNS tak, aby odkazovala na adresu URL Traffic Manager. Informace o tom, jak nastavit vlastní doménu pro aplikaci App Service, najdete v tématu [Konfigurace vlastního názvu domény v Azure App Service s Traffic Manager Integration](configure-domain-traffic-manager.md).
* Do profilu Azure Traffic Manager můžete přidat jenom aplikace, které jsou v režimu Standard nebo Premium.
* Přidání aplikace do profilu Traffic Manager způsobí, že se aplikace restartuje.

## <a name="next-steps"></a>Další kroky
Koncepční a technický přehled služby Azure Traffic Manager najdete v tématu [přehled Traffic Manager](../traffic-manager/traffic-manager-overview.md).


