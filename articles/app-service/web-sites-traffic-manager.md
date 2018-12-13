---
title: Řízení provozu pomocí nástroje Traffic Manager – Azure App Service
description: Tento článek obsahuje souhrnné informace pro službu Azure Traffic Manager, protože se týká služby Azure App Service.
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
ms.custom: seodec18
ms.openlocfilehash: 85405a96d141188203ddc88410c96654667fa83a
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270106"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Řízení provozu služby Azure App Service pomocí Azure Traffic Manageru
> [!NOTE]
> Tento článek obsahuje souhrnné informace pro Microsoft Azure Traffic Manager, protože se týká služby Azure App Service. Stačí navštívit odkazech na konci tohoto článku najdete další informace o Azure Traffic Manageru, samotného.
> 
> 

## <a name="introduction"></a>Úvod
Azure Traffic Manager umožňuje řídit, jak se distribuují požadavky z webových klientů pro aplikace ve službě Azure App Service. Pokud do profilu služby Azure Traffic Manager přidáte koncové body služby App Service, služba Azure Traffic Manager bude sledovat stav vašich aplikací App Service (spuštěné, zastavené nebo odstraněné), aby se mohla rozhodnout, do kterého z těchto koncových bodů se má směrovat provoz.

## <a name="routing-methods"></a>Metody směrování
Azure Traffic Manager používá čtyři různé metody směrování. Tyto metody jsou popsány v následujícím seznamu, podle kterých se týkají služby Azure App Service.

* **[Priorita](../traffic-manager/traffic-manager-routing-methods.md#priority):** používal pro veškeré přenosy primární aplikace a poskytují zálohování v případě, že nejsou k dispozici primární nebo aplikacím pro zálohování.
* **[Váha](../traffic-manager/traffic-manager-routing-methods.md#weighted):** distribuování síťového provozu mezi sadu aplikací, rovnoměrně nebo podle váhy, které definujete.
* **[Výkon](../traffic-manager/traffic-manager-routing-methods.md#performance):** až budete mít aplikace v různých geografických lokalitách, použijte "nejbližší" aplikace z hlediska nejnižší síťovou latencí.
* **[Geografické](../traffic-manager/traffic-manager-routing-methods.md#geographic):** uživatelé na konkrétní aplikace založené na jaké geografické umístění jejich dotaz DNS pochází. 

Další informace najdete v tématu [metody směrování Traffic Manageru](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service a profily Traffic Manageru
Ke konfiguraci řízení provozu aplikace služby App Service, vytvořte profil ve, že používá, jeden ze tří načíst vyrovnávání metod popsaných dříve službě Azure Traffic Manager a pak přidat koncové body (v tomto případě služby App Service) pro které chcete řídit provoz do profil. Stav vaší aplikace (spuštění, zastavení nebo odstranění) je pravidelně předávají do profilu tak, aby Azure Traffic Manager může směrovat provoz odpovídajícím způsobem.

Při použití Azure Traffic Manageru s Azure, mějte na paměti následující body:

* Pro pouze nasazení aplikací v rámci stejné oblasti služby App Service již poskytuje převzetí služeb při selhání a kruhové dotazování funkce bez ohledu na režim aplikace.
* Pro nasazení ve stejné oblasti, které používají ve spojení s jiné cloudové služby Azure App Service můžete kombinovat oba typy koncových bodů povolit hybridní scénáře.
* V profilu můžete určit pouze jeden koncový bod služby App Service v jedné oblasti. Když vyberete aplikaci jako koncový bod pro jednu oblast, stát zbývající aplikace v této oblasti k dispozici pro výběr pro tento profil.
* Koncové body služby App Service, které jste zadali v profilu Azure Traffic Manageru se zobrazí v části **názvy domén** části na stránce konfigurace pro aplikace v profilu, ale není konfigurovatelné.
* Po přidání aplikace do profilu **adresa URL webu** na řídicím panelu portálu stránky aplikace zobrazí adresy URL vlastní domény aplikace, pokud jste nastavili jeden. V opačném případě se zobrazí adresa URL profilu Traffic Manageru (například `contoso.trafficmanager.net`). Název přímé domény adresy URL Traffic Manageru a aplikace jsou viditelné na stránce konfigurace aplikace v rámci **názvy domén** oddílu.
* Názvy vlastních domén fungovat podle očekávání, ale kromě jejich přidání do svých aplikací, musíte také nakonfigurovat mapy DNS tak, aby odkazoval na adresu URL Traffic Manageru. Informace o tom, jak nastavit vlastní doménu pro aplikaci služby App Service najdete v tématu [mapování existujícího vlastního názvu DNS na Azure Web Apps](app-service-web-tutorial-custom-domain.md).
* Mohli byste přidávat pouze aplikace, které jsou v režimu standard nebo premium k profilu Azure Traffic Manageru.

## <a name="next-steps"></a>Další kroky
Koncepční a technický přehled Azure Traffic Manageru, najdete v článku [Traffic Manager – Přehled](../traffic-manager/traffic-manager-overview.md).

Další informace o službě App Service pomocí Traffic Manageru, najdete v blogových příspěvků [pomocí Azure Traffic Manageru s weby Azure](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) a [Azure Traffic Manager umožňuje integrovat s weby Azure](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/).

