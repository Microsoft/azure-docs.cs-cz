---
title: Řízení provozu pomocí Traffic Manageru
description: Najděte doporučené postupy pro konfiguraci Azure Traffic Manageru při jeho integraci se službou Azure App Service.
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.topic: article
ms.date: 02/25/2016
ms.custom: seodec18
ms.openlocfilehash: 200effab70b369d69b4e89b1901578ecfe1a1b87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684107"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Řízení provozu služby Azure App Service pomocí Azure Traffic Manageru
> [!NOTE]
> Tento článek obsahuje souhrnné informace pro Microsoft Azure Traffic Manager, pokud jde o Azure App Service. Další informace o azure traffic managersám lze nalézt na odkazech na konci tohoto článku.
> 
> 

## <a name="introduction"></a>Úvod
Pomocí služby Azure Traffic Manager můžete řídit způsob distribuce požadavků z webových klientů do aplikací ve službě Azure App Service. Pokud do profilu služby Azure Traffic Manager přidáte koncové body služby App Service, služba Azure Traffic Manager bude sledovat stav vašich aplikací App Service (spuštěné, zastavené nebo odstraněné), aby se mohla rozhodnout, do kterého z těchto koncových bodů se má směrovat provoz.

## <a name="routing-methods"></a>Metody směrování
Azure Traffic Manager používá čtyři různé metody směrování. Tyto metody jsou popsány v následujícím seznamu, protože se jedná o službu Azure App Service.

* ** [Priorita:](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)** Použijte primární aplikaci pro veškerý provoz a poskytněte zálohy v případě, že primární nebo zálohovací aplikace nejsou k dispozici.
* ** [Vážené](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribuovat provoz mezi sadu aplikací, rovnoměrně nebo podle hmotnosti, které definujete.
* ** [Výkon](../traffic-manager/traffic-manager-routing-methods.md#performance):** Pokud máte aplikace v různých geografických lokalitách, použijte "nejbližší" aplikaci z hlediska nejnižší latence sítě.
* ** [Geografické:](../traffic-manager/traffic-manager-routing-methods.md#geographic)** nasměrujte uživatele na konkrétní aplikace podle toho, odkud jejich dotaz DNS pochází. 

Další informace naleznete v [tématu Traffic Manager metody směrování](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Profily služby App Service a traffic manageru
Chcete-li nakonfigurovat řízení provozu aplikací služby App Service, vytvořte profil ve Správci provozu Azure, který používá jednu ze čtyř metod vyrovnávání zatížení popsaných dříve, a pak přidejte koncové body (v tomto případě App Service), pro které chcete řídit provoz do Profil. Stav aplikace (spuštěný, zastavený nebo odstraněný) se pravidelně sděluje profilu, aby Azure Traffic Manager mohl podle toho směrovat provoz.

Při používání Azure Traffic Manageru s Azure mějte na paměti následující body:

* Pro nasazení pouze aplikace ve stejné oblasti App Service již poskytuje funkce převzetí služeb při selhání a kruhové dotazování bez ohledu na režim aplikace.
* Pro nasazení ve stejné oblasti, které používají službu App Service ve spojení s jinou cloudovou službou Azure, můžete kombinovat oba typy koncových bodů a povolit hybridní scénáře.
* Můžete zadat pouze jeden koncový bod služby App Service na oblast v profilu. Když vyberete aplikaci jako koncový bod pro jednu oblast, zbývající aplikace v této oblasti nebudou k dispozici pro výběr pro tento profil.
* Koncové body služby App Service, které zadáte v profilu Azure Traffic Manager, se zobrazí v části **Názvy domén** na stránce Konfigurovat aplikaci v profilu, ale nelze je tam konfigurovat.
* Po přidání aplikace do profilu se na **adrese URL webu** na řídicím panelu portálu aplikace zobrazí adresa URL vlastní domény aplikace, pokud jste ji nastavili. V opačném případě se zobrazí adresa URL `contoso.trafficmanager.net`profilu traffic manageru (například ). Název přímé domény aplikace i adresa URL Traffic Manageru jsou viditelné na stránce Konfigurace aplikace v části **Domain Names** .
* Vaše vlastní názvy domén fungují podle očekávání, ale kromě jejich přidání do aplikací musíte také nakonfigurovat mapu DNS tak, aby ukazovala na adresu URL Traffic Manageru. Informace o tom, jak nastavit vlastní doménu pro aplikaci App Service, najdete v [tématu Mapování existujícího vlastního názvu DNS na Službu Azure App Service](app-service-web-tutorial-custom-domain.md).
* Do profilu Azure Traffic Manageru můžete přidávat jenom aplikace, které jsou ve standardním nebo prémiovém režimu.

## <a name="next-steps"></a>Další kroky
Koncepční a technický přehled Služby Azure Traffic Manager upřena na téma [Přehled Traffic Manageru](../traffic-manager/traffic-manager-overview.md).


