---
title: Traffic Manager typy koncových bodů | Microsoft Docs
description: Tento článek popisuje různé typy koncových bodů, které je možné používat s Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051223"
---
# <a name="traffic-manager-endpoints"></a>Koncové body Traffic Manageru

Microsoft Azure Traffic Manager vám umožní řídit způsob distribuce síťového provozu do nasazení aplikací spuštěných v různých datových centrech. V Traffic Manageru nakonfigurujete každé nasazení aplikace jako koncový bod. Když Traffic Manager obdrží žádost DNS, zvolí dostupný koncový bod, který vrátí v odpovědi DNS. Traffic Manager vychází z výběru aktuálního stavu koncového bodu a metody směrování provozu. Další informace najdete v tématu [jak Traffic Manager funguje](traffic-manager-how-it-works.md).

Traffic Manager podporuje tři typy koncových bodů:

* **Koncové body Azure** se používají pro služby hostované v Azure.
* **Externí koncové body** se používají pro adresy IPv4/IPv6, plně kvalifikované názvy domény nebo služby hostované mimo Azure. Služby následujících mohou být buď místní, nebo s jiným poskytovatelem hostingu.
* **Vnořené koncové body** slouží ke kombinování profilů Traffic Manageru pro účely vytváření pružnějších schémat směrování provozu, která podporují potřeby větších a složitějších nasazení.

Neexistuje žádné omezení na to, jak jsou koncové body různých typů kombinovány v jednom profilu Traffic Manager. Každý profil může obsahovat libovolnou kombinaci typů koncových bodů.

Následující části popisují jednotlivé typy koncových bodů podrobněji.

## <a name="azure-endpoints"></a>Koncové body Azure

Koncové body Azure se používají pro služby založené na Azure v Traffic Manager. Podporují se tyto typy prostředků Azure:

* PaaS cloudové služby.
* Web Apps
* Sloty webové aplikace
* Prostředky PublicIPAddress (které se dají připojit k virtuálním počítačům přímo nebo prostřednictvím Azure Load Balancer) PublicIpAddress musí mít přiřazený název DNS, aby ho bylo možné použít v profilu Traffic Manager.

Prostředky PublicIPAddress jsou Azure Resource Manager prostředky. Neexistují v klasickém modelu nasazení a jsou podporované jenom v prostředích Azure Resource Manager v Traffic Manager. Ostatní typy koncových bodů jsou podporovány prostřednictvím Správce prostředků i modelu nasazení Classic.

Při použití koncových bodů Azure Traffic Manager detekuje, když je webová aplikace zastavená a spuštěná. Tento stav se projeví ve stavu koncového bodu. Podrobnosti najdete v tématu [monitorování koncového bodu Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status) . Pokud je podkladová služba zastavena, Traffic Manager neprovádí kontroly stavu koncových bodů ani přímý provoz do koncového bodu. U zastavené instance nejsou k dispozici žádné Traffic Manager fakturační události. Po restartování služby se vyúčtování obnoví a koncový bod má nárok na příjem provozu. Tato detekce se nevztahuje na koncové body PublicIpAddress.

## <a name="external-endpoints"></a>Externí koncové body

Externí koncové body se používají pro adresy IPv4/IPv6, plně kvalifikované názvy domény nebo pro služby mimo Azure. Použití koncových bodů adresy IPv4/IPv6 umožňuje Traffic Manageru kontrolovat stav koncových bodů bez vyžadování názvu DNS. V důsledku toho Traffic Manager možné reagovat na dotazy pomocí záznamů A/AAAA při vracení tohoto koncového bodu v odpovědi. Služby mimo Azure můžou zahrnovat službu hostovanou místně nebo s jiným poskytovatelem. Externí koncové body lze použít individuálně nebo v kombinaci s Koncové body Azure ve stejném profilu Traffic Manager. Výjimkou jsou pro koncové body, které jsou zadány jako adresy IPv4 nebo IPv6, které mohou být pouze externí koncové body. Kombinování koncových bodů Azure s externími koncovými body umožňuje různé scénáře:

* Zajištění vyšší redundance stávající místní aplikace v modelu aktivní-aktivní nebo aktivní-pasivní převzetí služeb při selhání pomocí Azure. 
* Směrování provozu do koncových bodů, které nemají přidružené názvy DNS. Také snižuje latenci vyhledávání DNS tím, že odebráním nutnosti spustit druhý dotaz DNS, který získá IP adresu vráceného názvu DNS.
* Snižte latenci aplikací pro uživatele po celém světě a rozšíříte stávající místní aplikaci do jiných geografických umístění v Azure. Další informace najdete v tématu [Traffic Manager směrování provozu výkonu](traffic-manager-routing-methods.md#performance).
* Poskytněte větší kapacitu pro existující místní aplikaci, ať už nepřetržitě nebo jako řešení typu "shluk-to-Cloud", aby splňovala špičku v poptávce pomocí Azure.

V některých případech je vhodné použít externí koncové body, které odkazují na služby Azure. Příklady najdete v [nejčastějších dotazech](traffic-manager-faqs.md#traffic-manager-endpoints) . Kontroly stavu se účtují podle kurzu koncových bodů Azure, ne podle sazby externích koncových bodů. Na rozdíl od koncových bodů Azure když zastavíte nebo odstraníte základní službu, bude fakturace kontroly stavu pokračovat. Účtování se zastaví, když v Traffic Manager zakážete nebo odstraníte koncový bod.

## <a name="nested-endpoints"></a>Vnořené koncové body

Vnořené koncové body kombinují více profilů Traffic Manager pro vytváření flexibilních technologických směrovacích schémat pro podporu potřeb větších a složitých nasazení. U vnořených koncových bodů se jako koncový bod k nadřazenému profilu přidá profil "podřízený". Podřízené i nadřazené profily mohou obsahovat jiné koncové body libovolného typu, včetně jiných vnořených profilů. 

Další informace najdete v tématu [vnořené Traffic Manager profily](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Web Apps jako koncové body

Při konfiguraci Web Apps jako koncových bodů v Traffic Manager platí další informace:

1. Pro použití s Traffic Manager mají nárok pouze Web Apps v SKU Standard nebo vyšších. Pokusy o přidání webové aplikace s nižší jednotkou SKU selžou. Výsledkem snížení objemu skladové jednotky existující webové aplikace Traffic Manager tím, že už neodesílají provoz do této webové aplikace. Další informace o podporovaných plánech najdete v tématu [plány App Service](https://azure.microsoft.com/pricing/details/app-service/plans/) .
2. Když koncový bod přijme požadavek HTTP, použije v žádosti hlavičku hosta k určení, která webová aplikace by měla požadavek obsluhovat. Hlavička hostitele obsahuje název DNS, který se používá ke spuštění požadavku, například ' contosoapp.azurewebsites.net '. Pokud chcete pro webovou aplikaci použít jiný název DNS, musí být název DNS registrovaný jako vlastní název domény pro aplikaci. Při přidávání koncového bodu webové aplikace jako koncového bodu Azure se název DNS profilu Traffic Manager pro aplikaci automaticky zaregistruje. Tato registrace je automaticky odebrána při odstranění koncového bodu.
3. Každý profil Traffic Manager může mít maximálně jeden koncový bod webové aplikace z každé oblasti Azure. Pokud chcete toto omezení obejít, můžete nakonfigurovat webovou aplikaci jako externí koncový bod. Další informace najdete v [nejčastějších dotazech](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Povolování a zakazování koncových bodů

Zakázání koncového bodu v Traffic Manager může být užitečné pro dočasné odebrání provozu z koncového bodu, který je v režimu údržby nebo který se znovu nasazuje. Po opětovném spuštění koncového bodu je možné ho znovu povolit.

Koncové body můžete povolit nebo zakázat prostřednictvím portálu Traffic Manager, PowerShellu, rozhraní příkazového řádku nebo REST API.

> [!NOTE]
> Zakázání koncového bodu Azure nemá nic dělat se stavem nasazení v Azure. Služba Azure (například virtuální počítač nebo webová aplikace zůstane spuštěná a může přijímat provoz i v případě, že je v Traffic Manager zakázaná). Provoz je možné adresovat přímo do instance služby, a ne prostřednictvím názvu DNS profilu Traffic Manager. Další informace najdete v tématu [jak Traffic Manager funguje](traffic-manager-how-it-works.md).

Aktuální způsobilost každého koncového bodu pro příjem provozu závisí na následujících faktorech:

* Stav profilu (povoleno/zakázáno)
* Stav koncového bodu (povoleno/zakázáno)
* Výsledky kontrol stavu pro daný koncový bod

Podrobnosti najdete v tématu [Traffic Manager monitorování koncového bodu](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Vzhledem k tomu, že Traffic Manager funguje na úrovni DNS, nemůže mít vliv na existující připojení ke koncovému bodu. Pokud koncový bod není k dispozici, Traffic Manager směruje nová připojení k jinému dostupnému koncovému bodu. Hostitel za zakázaným nebo nefunkčním koncovým bodem ale může dál přijímat přenosy přes existující připojení, dokud se tyto relace neukončí. Aplikace by měly omezit dobu trvání relace, aby bylo možné provoz z existujících připojení vyprázdnit.

Pokud jsou všechny koncové body v profilu zakázané, nebo pokud je samotný profil zakázaný, Traffic Manager odešle odpověď "NXDOMAIN" novému dotazu DNS.

## <a name="faqs"></a>Nejčastější dotazy

* [Můžu použít Traffic Manager s koncovými body z více předplatných?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [Můžu použít Traffic Manager s testovacími sloty cloudové služby?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Podporuje Traffic Manager koncové body IPv6?](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [Můžu použít Traffic Manager s více než jednou webovou aplikací ve stejné oblasti?](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Návody přesunout koncové body Azure profilu Traffic Manager do jiné skupiny prostředků?](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, [jak Traffic Manager funguje](traffic-manager-how-it-works.md).
* Přečtěte si o Traffic Manager [monitorování koncového bodu a automatické převzetí služeb při selhání](traffic-manager-monitoring.md)
* Přečtěte si informace o [metodách směrování provozu](traffic-manager-routing-methods.md)Traffic Manager.