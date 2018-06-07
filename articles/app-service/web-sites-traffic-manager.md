---
title: Kontrole přenosů dat v Azure App Service pomocí nástroje Azure Traffic Manager
description: Tento článek obsahuje souhrnné informace pro Azure Traffic Manager, protože se týká služby Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.openlocfilehash: 92ab7bf64445ff772f33a18e7f7946a7e0be333a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824036"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Kontrole přenosů dat v Azure App Service pomocí nástroje Azure Traffic Manager
> [!NOTE]
> Tento článek obsahuje souhrnné informace pro Microsoft Azure Traffic Manager, protože se týká služby Azure App Service. Další informace o Azure Traffic Manager, sám, může navštívením odkazy na konci tohoto článku.
> 
> 

## <a name="introduction"></a>Úvod
Azure Traffic Manageru můžete použít k řízení rozdělení požadavky od klientů webové aplikace v Azure App Service. Když koncové body služby App Service jsou přidány do profilu Azure Traffic Manager, Azure Traffic Manager uchovává informace o stavu aplikace služby App Service (spuštěna, zastavena nebo odstraněné) tak, aby se můžete rozhodnout, která z těchto koncových bodů musí přijímat přenosy.

## <a name="routing-methods"></a>Metody směrování
Azure Traffic Manager používá čtyři různé metody směrování. Tyto metody jsou popsané v následujícím seznamu, podle kterých se týkají služby Azure App Service.

* **[Priorita](../traffic-manager/traffic-manager-routing-methods.md#priority):** používal pro veškeré přenosy primární aplikace a zadejte zálohování v případě, že primární nebo aplikace pro zálohování jsou k dispozici.
* **[Vážené](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribuci přenosů mezi sadu aplikací, rovnoměrně nebo podle váhy, které definujete.
* **[Výkon](../traffic-manager/traffic-manager-routing-methods.md#performance):** v různých geografických polohách. máte aplikace, použijte "nejbližší" aplikaci z hlediska nejnižší latenci sítě.
* **[Zeměpisná](../traffic-manager/traffic-manager-routing-methods.md#geographic):** uživatelé na konkrétní aplikace na základě, na které zeměpisné umístění svého dotazu DNS pochází z. 

Další informace najdete v tématu [metody směrování Traffic Manageru](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>Služba App Service a profily Traffic Manageru
Konfigurace řízení provozu aplikace služby App Service, vytvořte profil v, používá, jeden ze tří zatížení vyrovnávání metody popsané programem Azure Traffic Manager a poté přidejte koncových bodů (v tomto případě App Service) pro které chcete řídit provoz profil. Stav vaší aplikace (spuštěna, zastavena nebo odstranit) je pravidelně oznamovat v profilu tak, aby Azure Traffic Manager může směrovat přenosy odpovídajícím způsobem.

Pokud používáte Azure Traffic Manageru službou Azure, mějte na paměti následující body:

* Pro pouze nasazení aplikací v rámci stejné oblasti služby App Service již poskytuje převzetí služeb při selhání a funkce kruhového dotazování bez ohledu na režim aplikace.
* Pro nasazení ve stejné oblasti, které používají ve spojení s jiné cloudové služby Azure App Service můžete kombinovat oba typy koncových bodů povolit hybridní scénáře.
* V profilu můžete určit pouze jeden koncový bod služby App Service na oblast. Když vyberete aplikaci jako koncový bod pro jednu oblast, zbývající aplikace v této oblasti k dispozici pro výběr pro tento profil.
* Koncové body služby App Service, které zadáte v profilu Azure Traffic Manager se zobrazí pod **názvy domén** části na stránce konfigurace pro aplikaci v profilu, ale není konfigurovatelné.
* Po přidání aplikace do profilu, **adresa URL webu** na řídicím panelu portálu stránku aplikace zobrazí adresu URL vlastní doménu aplikace, pokud jste nastavili jednu. Jinak, zobrazí adresu URL profilu Traffic Manageru (například `contoso.trafficmanager.net`). Název domény přímé aplikaci a adresu URL Traffic Manager jsou viditelné na stránku konfigurace aplikace v rámci **názvy domén** části.
* Názvy vlastních domén fungovat podle očekávání, ale kromě jejich přidáním do vaší aplikace, je nutné také nakonfigurovat mapu DNS a přejděte na adresu URL správce provozu. Informace o tom, jak nastavit vlastní doménu pro aplikaci služby App Service najdete v tématu [mapovat existující vlastní název DNS pro službu Azure Web Apps](app-service-web-tutorial-custom-domain.md).
* Můžete přidat pouze aplikace, které jsou v režimu standard nebo premium do profilu Azure Traffic Manager.

## <a name="next-steps"></a>Další kroky
Koncepční a technický přehled o Azure Traffic Manager, najdete v části [Traffic Manager Přehled](../traffic-manager/traffic-manager-overview.md).

Další informace o používání Traffic Manageru službou App Service najdete v příspěvcích na blogu [pomocí Azure Traffic Manager s weby Azure](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) a [Azure Traffic Manager teď můžete integrovat s weby Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

