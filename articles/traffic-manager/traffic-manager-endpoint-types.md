---
title: Typy koncových bodů správce provozu | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje různé typy koncových bodů, které lze použít s Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rohink
ms.openlocfilehash: 3d8f899a7899243129d31c2620a51dc764a8e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250930"
---
# <a name="traffic-manager-endpoints"></a>Koncové body Traffic Manageru

Microsoft Azure Traffic Manager umožňuje řídit, jak se síťový provoz distribuuje do nasazení aplikací spuštěných v různých datových centrech. V Traffic Manageru nakonfigurujete každé nasazení aplikace jako koncový bod. Když Traffic Manager obdrží žádost DNS, zvolí dostupný koncový bod, který vrátí v odpovědi DNS. Traffic Manager zakládá volbu na aktuálním stavu koncového bodu a metodě směrování provozu. Další informace naleznete v tématu [Jak funguje traffic manager](traffic-manager-how-it-works.md).

Traffic Manager podporuje tři typy koncových bodů:

* **Koncové body Azure** se používají pro služby hostované v Azure.
* **Externí koncové body** se používají pro adresy IPv4/IPv6, plně kvalifikované názvy domén nebo pro služby hostované mimo Azure, které mohou být buď místní, nebo u jiného poskytovatele hostingu.
* **Vnořené koncové body** slouží ke kombinování profilů Traffic Manageru pro účely vytváření pružnějších schémat směrování provozu, která podporují potřeby větších a složitějších nasazení.

Kombinování různých typů koncových bodů v jednom profilu Traffic Manageru není nijak omezeno. Každý profil může obsahovat libovolnou kombinaci typů koncových bodů.

Následující části popisují každý typ koncového bodu do větší hloubky.

## <a name="azure-endpoints"></a>Koncové body Azure

Koncové body Azure se používají pro služby založené na Azure ve Správci provozu. Podporovány jsou následující typy prostředků Azure:

* Cloudové služby PaaS.
* Web Apps
* Sloty pro webové aplikace
* PublicIPAddress prostředky (které lze připojit k virtuálním počítačům buď přímo, nebo prostřednictvím Azure Load Balancer). Adresa publicIpAddress musí mít přiřazen ý název DNS pro použití v profilu traffic manageru.

Prostředky PublicIPAddress jsou prostředky Azure Resource Manager. Neexistují v modelu klasickénasazení. Proto jsou podporovány jenom v prostředí správce prostředků Azure Správce provozu. Ostatní typy koncových bodů jsou podporovány prostřednictvím Správce prostředků a klasického modelu nasazení.

Při použití koncových bodů Azure Traffic Manager zjistí, kdy je webová aplikace zastavena a spuštěna. Tento stav se projeví ve stavu koncového bodu. Podrobnosti najdete v [tématu Sledování koncového bodu Traffic Manageru.](traffic-manager-monitoring.md#endpoint-and-profile-status) Při zastavení základní služby Traffic Manager neprovádí kontroly stavu koncového bodu nebo přímý provoz na koncový bod. Pro zastavenou instanci nedochází k žádným fakturačním událostem Traffic Manageru. Po restartování služby se obnoví fakturace a koncový bod je způsobilý přijímat provoz. Toto zjišťování se nevztahuje na koncové body PublicIpAddress.

## <a name="external-endpoints"></a>Externí koncové body

Externí koncové body se používají buď pro adresy IPv4/IPv6, vícenežnové oblasti nebo pro služby mimo Azure. Použití koncových bodů adresy IPv4/IPv6 umožňuje správci provozu zkontrolovat stav koncových bodů bez nutnosti jejich názvu DNS. V důsledku toho Traffic Manager můžete reagovat na dotazy se záznamy A/AAAA při vrácení tohoto koncového bodu v odpovědi. Služby mimo Azure mohou zahrnovat službu hostovoji místně nebo s jiným poskytovatelem. Externí koncové body lze použít jednotlivě nebo v kombinaci s koncovými body Azure ve stejném profilu Traffic Manager s výjimkou koncových bodů určených jako Adresy IPv4 nebo IPv6, které mohou být pouze externí koncové body. Kombinace koncových bodů Azure s externími koncovými body umožňuje různé scénáře:

* Poskytněte zvýšenou redundanci pro existující místní aplikaci v modelu převzetí služeb při selhání aktivní nebo pasivní pomocí Azure. 
* Směrovat provoz do koncových bodů, které nemají název DNS s nimi spojené. Kromě toho snižte celkovou latenci vyhledávání DNS odebráním nutnosti spustit druhý dotaz DNS, abyste získali vrácenou IP adresu názvu DNS.
* Snižte latenci aplikací pro uživatele po celém světě a rozšiřte stávající místní aplikaci do dalších geografických umístění v Azure. Další informace naleznete v tématu [Traffic Manager 'Performance' traffic routing](traffic-manager-routing-methods.md#performance).
* Poskytněte další kapacitu pro existující místní aplikaci, a to buď nepřetržitě, nebo jako řešení "burst-to-cloud", abyste pomocí Azure uspokojili špičku v poptávce.

V některých případech je užitečné použít externí koncové body k odkazování na služby Azure (například viz [nejčastější dotazy).](traffic-manager-faqs.md#traffic-manager-endpoints) V takovém případě se kontroly stavu účtují sazbou koncových bodů Azure, nikoli sazbou externích koncových bodů. Pokud však na rozdíl od koncových bodů Azure zastavíte nebo odstraníte základní službu, fakturace kontroly stavu bude pokračovat, dokud nezakážete nebo neodstraníte koncový bod ve Správci provozu.

## <a name="nested-endpoints"></a>Vnořené koncové body

Vnořené koncové body kombinují více profilů Traffic Manageru a vytvářejí flexibilní schémata směrování provozu a podporují potřeby větších a složitých nasazení. S vnořené koncové body je profil podřízené ho přidá jako koncový bod do profilu nadřazený. Podřízený i nadřazený profil může obsahovat další koncové body libovolného typu, včetně jiných vnořených profilů. Další informace naleznete v [vnořených profilech traffic manageru](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Webové aplikace jako koncové body

Některé další důležité informace platí při konfiguraci webových aplikací jako koncových bodů ve Správci provozu:

1. Pouze webové aplikace na skladové místě standardu nebo vyšší jsou způsobilé pro použití s Traffic Manager. Pokusy o přidání webové aplikace nižší skladové položky se nezdaří. Snížení počtu skladových položk existující webové aplikace vede k tomu, že Traffic Manager již neodesílá provoz do této webové aplikace. Další informace o podporovaných plánech najdete v [tématu Plány služby App Service](https://azure.microsoft.com/pricing/details/app-service/plans/)
2. Když koncový bod obdrží požadavek HTTP, použije hlavičku hostitele v požadavku k určení, která webová aplikace by měla požadavek obsluhovat. Hlavička hostitele obsahuje název DNS použitý k zahájení požadavku, například "contosoapp.azurewebsites.net". Chcete-li ve webové aplikaci použít jiný název DNS, musí být název DNS zaregistrován jako vlastní název domény aplikace. Při přidávání koncového bodu Webové aplikace jako koncového bodu Azure se pro aplikaci automaticky zaregistruje název DNS profilu Traffic Manageru. Tato registrace je automaticky odebrána při odstranění koncového bodu.
3. Každý profil Traffic Manageru může mít maximálně jeden koncový bod webové aplikace z každé oblasti Azure. Chcete-li toto omezení obejít, můžete nakonfigurovat webovou aplikaci jako externí koncový bod. Další informace naleznete v [nejčastějších dotazech](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Povolení a zakázání koncových bodů

Zakázání koncového bodu ve Správci provozu může být užitečné pro dočasné odebrání přenosů z koncového bodu, který je v režimu údržby nebo je znovu nasazen. Jakmile je koncový bod znovu spuštěn, může být znovu povolen.

Koncové body lze povolit a zakázat prostřednictvím portálu Traffic Manager, PowerShellu, ROZHRANÍ CLI nebo ROZHRANÍ REST API.

> [!NOTE]
> Zakázání koncového bodu Azure nemá nic společného se stavem nasazení v Azure. Služba Azure (například virtuální počítač nebo webová aplikace zůstane spuštěná a schopná přijímat provoz, i když je ve Správci provozu zakázána. Provoz lze adresovat přímo instanci služby, nikoli prostřednictvím názvu DNS profilu Traffic Manageru. Další informace naleznete v tématu [Jak Traffic Manager funguje](traffic-manager-how-it-works.md).

Aktuální způsobilost každého koncového bodu pro příjem provozu závisí na následujících faktorech:

* Stav profilu (povolen/zakázán)
* Stav koncového bodu (povolen/zakázán)
* Výsledky kontrol stavu pro tento cílový parametr

Podrobnosti naleznete v tématu [Sledování koncového bodu Traffic Manageru](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Vzhledem k tomu, že Traffic Manager pracuje na úrovni DNS, nemůže ovlivnit existující připojení k libovolnému koncovému bodu. Pokud koncový bod není k dispozici, Traffic Manager směruje nová připojení do jiného dostupného koncového bodu. Hostitel za zakázaným nebo nefunkčním koncovým bodem však může nadále přijímat přenosy prostřednictvím existujících připojení, dokud nebudou tyto relace ukončeny. Aplikace by měly omezit dobu trvání relace, aby přenosy mohly být vyčerpány z existujících připojení.

Pokud jsou zakázány všechny koncové body v profilu nebo pokud je zakázán samotný profil, odešle Traffic Manager odpověď NXDOMAIN na nový dotaz DNS.

## <a name="faqs"></a>Nejčastější dotazy

* [Můžu použít Traffic Manager s koncovými body z více předplatných?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Mohu použít Traffic Manager s 'Staging' sloty cloudové služby?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Podporuje Traffic Manager koncové body IPv6?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-ipv6-endpoints)

* [Můžu používat Traffic Manager s více než jednou webovou aplikací ve stejné oblasti?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Jak můžu přesunout koncové body profilu Azure svého profilu Traffic Manageru do jiné skupiny prostředků?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Další kroky

* Přečtěte [si, jak Traffic Manager funguje](traffic-manager-how-it-works.md).
* Informace o [sledování koncového bodu Traffic Manageru a automatickém převzetí služeb při selhání](traffic-manager-monitoring.md).
* Informace o [metodách směrování provozu](traffic-manager-routing-methods.md)traffic manageru .
