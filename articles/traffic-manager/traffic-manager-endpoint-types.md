---
title: Typy koncových bodů Traffic Manageru | Dokumentace Microsoftu
description: Tento článek vysvětluje různé typy koncových bodů, které lze použít s Azure Traffic Managerem
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 4e506986-f78d-41d1-becf-56c8516e4d21
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: kumud
ms.openlocfilehash: 115511d15bc2366e49f6b3d1b89b513ea0ee5e90
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398024"
---
# <a name="traffic-manager-endpoints"></a>Koncové body Traffic Manageru
Microsoft Azure Traffic Manager umožňuje řídit, jak je zatížení sítě distribuováno do nasazení aplikace spuštěné v různých datových centrech. Konfigurace nasazení každé aplikace jako "koncový bod' v Traffic Manageru. Když Traffic Manager obdrží žádost o DNS, vybere dostupný koncový bod vrátit v odpovědi DNS. Volba Traffic Manageru založen na aktuální stav koncového bodu a metodu směrování provozu. Další informace najdete v tématu [jak funguje Traffic Manager](traffic-manager-how-it-works.md).

Existují tři typy koncových bodů, které Traffic Manager podporuje:
* **Koncové body Azure** jsou používané pro služby hostované v Azure.
* **Externí koncové body** jsou používány pro služby hostované mimo Azure, ať už místní, nebo pomocí jiného poskytovatele hostitelských služeb.
* **Vnořené koncové body** umožňují kombinovat profily Traffic Manageru k vytvoření více flexibilní schémata směrování provozu pro podporu potřeb větší a složitější nasazení.

Neexistuje žádné omezení jak jsou koncové body různých typů kombinovat v jednom profilu Traffic Manageru. Jednotlivé profily mohou obsahovat libovolné kombinaci různých typů koncový bod.

Každý typ koncového bodu podrobněji v následujících částech.

## <a name="azure-endpoints"></a>Koncové body Azure

Koncové body Azure jsou používané pro služby založené na Azure ve službě Traffic Manager. Jsou podporovány následující typy prostředků Azure:

* "Klasickém" virtuální počítače IaaS a PaaS cloudové služby.
* Web Apps
* Prostředků PublicIPAddress (který jde připojit k virtuální počítače přímo nebo prostřednictvím služby Azure Load Balancer). PublicIpAddress musí mít název DNS přiřadit pro použití v profilu služby Traffic Manager.

Prostředků PublicIPAddress jsou prostředky Azure Resource Manageru. Neexistují v modelu nasazení classic. Proto jsou pouze podporované v Traffic Manageru na Azure Resource Manageru prostředí. Jiné typy koncových bodů jsou podporované prostřednictvím modelu nasazení classic i Resource Manager.

Při používání koncových bodů Azure Traffic Manager rozpozná virtuálních počítačů IaaS "Klasickém", cloudovou službu nebo webové aplikace je zastavena a spuštěna. Tento stav se projeví v stav koncového bodu. Zobrazit [monitorování koncových bodů Traffic Manageru](traffic-manager-monitoring.md#endpoint-and-profile-status) podrobnosti. Při zastavení základní služby Traffic Manager neprovádí kontroly stavu koncových bodů nebo směrovat přenos dat do koncového bodu. Pro zastavené instanci dojít k žádné události fakturace Traffic Manageru. Při restartování služby fakturačního obnoví a koncový bod je způsobilá k přijímání provozu. Tato detekce se nevztahují na koncové body PublicIpAddress.

## <a name="external-endpoints"></a>Externí koncové body

Externí koncové body jsou používané pro služby mimo Azure. Například služba hostovaný místně nebo pomocí jiného poskytovatele. Externí koncové body můžete použít jednotlivě nebo společně s koncovými body Azure v jednom profilu Traffic Manageru. Kombinování koncové body s externími koncovými body Azure umožňuje různé scénáře:

* V obou převzetí služeb při selhání aktivní aktivní nebo aktivní pasivní modelu poskytují zvýšené redundance pro existující místní aplikaci pomocí Azure.
* Snížení latence aplikace pro uživatele po celém světě, rozšiřte existující místní aplikaci do dalších geografických umístěních v Azure. Další informace najdete v tématu [Traffic Manageru "Výkonu" směrování provozu](traffic-manager-routing-methods.md#performance).
* Použití Azure při poskytování další kapacitu pro existující místní aplikace, průběžně nebo jako "burst-to-cloud" řešení, které vyhoví špičky v poptávce.

V některých případech je vhodné použít externí koncové body odkazovat služeb Azure (příklady najdete v tématu [nejčastější dotazy k](traffic-manager-faqs.md#traffic-manager-endpoints)). V takovém případě kontroly stavu se účtují sazbou koncové body Azure, ne míry externí koncové body. Ale na rozdíl od koncových bodů Azure, pokud zastavíte nebo odstraníte službu základní kontroly stavu fakturace pokračuje, dokud zakázat nebo odstranit koncový bod v Traffic Manageru.

## <a name="nested-endpoints"></a>Vnořené koncových bodů

Vnořené koncové body kombinovat více profily Traffic Manageru k vytvoření schémat flexibilního směrování provozu a vyhověl potřebám větší a komplexní nasazení. Pomocí koncových bodů vnořené "podřízený" profil se přidá jako koncový bod do profilu "nadřazený". Podřízenými a nadřazenými profily mohou obsahovat jiné koncové body libovolného typu, včetně jiných vnořených profilů. Další informace najdete v tématu [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Webové aplikace jako koncové body

Několik dalších důležitých informací platí při konfiguraci webových aplikací jako koncových bodů v Traffic Manageru:

1. Jsou vhodné pro použití s Traffic Managerem jenom u Web Apps v SKU "Standard" nebo novější. Pokusy o přidání webovou aplikaci z nižší SKU selžou. Downgradování SKU existující webové aplikace výsledků v Traffic Manageru už odesílání provozu do příslušné webové aplikace.
2. Když koncový bod přijme požadavek HTTP, používá "hostitel" hlavičky v požadavku k určení by měl zpracovat požadavek, která sada webových aplikací. Hlavička hostitele obsahuje název DNS použitý k provedení požadavku, například contosoapp.azurewebsites.net. Pokud chcete použít jiný název DNS s vaší webovou aplikací, musí název DNS zaregistrovat jako název vlastní domény pro aplikaci. Při přidávání koncový bod webové aplikace jako koncový bod Azure, se automaticky registruje název DNS Traffic Manageru profil pro aplikaci. Tuto registrace automaticky odstraní při odstranění koncového bodu.
3. Každý profil služby Traffic Manager může mít maximálně jednu webovou aplikaci koncového bodu z každé oblasti Azure. Pro toto omezení obejít, můžete nakonfigurovat webovou aplikaci jako externí koncový bod. Další informace najdete v tématu [nejčastější dotazy k](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Povolení a zakázání koncových bodů

Zakázání koncového bodu ve službě Traffic Manager může být užitečné k dočasnému odstranění provoz z koncového bodu, který je v režimu údržby nebo který je znovu nasazován. Po opětovném zprovoznění koncový bod, můžete ho znova zapnout.

Koncové body můžete povolené a zakázané přes Traffic Manageru, Powershellu, rozhraní příkazového řádku nebo rozhraní REST API, které jsou podporovány v modelu nasazení classic i Resource Manager.

> [!NOTE]
> Zakázání koncový bod Azure nemá nic společného s jeho stavem nasazení v Azure. Služba Azure (například virtuální počítač nebo webová aplikace zůstává spuštěný a může přijímat provoz i v případě, že je v Traffic Manageru zakázán. Přenosy lze řešit přímo do instance služby, nikoli prostřednictvím názvu DNS profilu Traffic Manageru. Další informace najdete v tématu [jak funguje Traffic Manager](traffic-manager-how-it-works.md).

Aktuální existence nároku každého koncového bodu pro příjem provozu, závisí na následujících faktorech:

* Stav profilu (povolený/zakázaný)
* Stav koncového bodu (povolený/zakázaný)
* Výsledky stavu kontroluje tohoto koncového bodu

Podrobnosti najdete v tématu [monitorování koncových bodů Traffic Manageru](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Vzhledem k tomu, že Traffic Manager pracuje na úrovni DNS, se nemůže ovlivnit existující připojení pro libovolný koncový bod. Pokud koncový bod není k dispozici, Traffic Manager přesměruje nových připojení na jiný koncový bod k dispozici. Hostitele za koncový bod zakázané nebo není v pořádku však může i nadále přijímat provoz přes existující připojení, dokud tyto relace budou ukončeny. Aplikace by měla omezit doba trvání relace, která umožňují přenosy na vyprazdňování z existující připojení.

Pokud jsou zakázané všechny koncové body v profilu nebo samotném profilu je zakázaná, Traffic Manager odešle odpověď "NXDOMAIN" nový dotaz DNS.


## <a name="next-steps"></a>Další postup

* Přečtěte si [jak funguje Traffic Manager](traffic-manager-how-it-works.md).
* Přečtěte si o Traffic Manageru [koncový bod monitorování a automatickému převzetí služeb při selhání](traffic-manager-monitoring.md).
* Přečtěte si o Traffic Manageru [metody směrování provozu](traffic-manager-routing-methods.md).
