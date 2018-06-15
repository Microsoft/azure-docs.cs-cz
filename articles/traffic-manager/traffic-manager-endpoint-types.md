---
title: Typy koncových bodů Traffic Manageru | Microsoft Docs
description: Tento článek vysvětluje různé typy koncových bodů, které lze použít s Azure Traffic Manager
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
ms.openlocfilehash: 792712e3e529d77ff20a7603b5fbf028ca60f8c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877514"
---
# <a name="traffic-manager-endpoints"></a>Koncové body Traffic Manageru
Microsoft Azure Traffic Manager umožňuje řídit, jak je síťový provoz distribuován do nasazení aplikace spuštěné v různých datových centrech. Konfigurace nasazení každé aplikace jako "koncový bod, v Traffic Manageru. Když Traffic Manager obdrží žádost o DNS, vybere v odpovědi DNS vrátit k dispozici koncového bodu. Správce provozu základny volba na aktuální stav koncového bodu a metodu směrování provozu. Další informace najdete v tématu [jak Traffic Manager funguje](traffic-manager-how-traffic-manager-works.md).

Existují tři typy podporované Traffic Managerem koncového bodu:
* **Koncové body Azure** se používají pro služby hostované v Azure.
* **Externí koncové body** se používají pro služby hostované mimo Azure, místně nebo pomocí jiného poskytovatele hostitelských služeb.
* **Vnořené koncové body** se používají k zkombinovat profily Traffic Manageru k vytvoření flexibilnější směrování provozu schémata pro podporu potřebám rozsáhlejších, složitějších nasazení.

Neexistuje žádné omezení na tom, jak jsou koncové body různých typů kombinaci v jednom profilu Traffic Manageru. Každý profil může obsahovat libovolné kombinace typy koncových bodů.

Následující části popisují každý typ koncového bodu podrobněji.

## <a name="azure-endpoints"></a>Koncové body Azure

Koncové body Azure používají služeb založených na Azure Traffic Manager. Jsou podporovány následující typy prostředků Azure:

* "Classic, virtuální počítače IaaS a PaaS cloudových služeb.
* Web Apps
* PublicIPAddress prostředky (který může být připojen k virtuální počítače přímo nebo prostřednictvím Vyrovnávání zatížení Azure). PublicIpAddress musí mít název DNS přiřazené pro použití v profilu Traffic Manageru.

PublicIPAddress prostředky jsou prostředky Azure Resource Manager. Neexistují v modelu nasazení classic. Proto jsou pouze podporované v Traffic Manageru na Azure Resource Manager prostředí. Ostatní typy koncových bodů jsou podporované pomocí modelu nasazení classic i Resource Manager.

Při použití koncové body Azure, Traffic Manager zjistí, že virtuální počítač IaaS "Klasickém", Cloudová služba nebo webové aplikace je zastavena a spuštěna. Tento stav se projeví ve stavu koncový bod. V tématu [monitorování koncového bodu Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status) podrobnosti. Pokud je základní služba zastavená, Traffic Manager nebude provádět kontroly stavu koncového bodu nebo přímé přenosy ke koncovému bodu. Žádné události fakturace Traffic Manager dojít k zastaven instance. Při restartování služby nemá oprávnění přijímat provoz fakturace obnoví a koncový bod. Toto zjišťování se nevztahuje na PublicIpAddress koncové body.

## <a name="external-endpoints"></a>Externí koncové body

Externí koncové body se používají pro služby mimo Azure. Například služby hostované v místě nebo s jiným zprostředkovatelem. Externí koncové body můžete použít jednotlivě nebo v kombinaci s koncovými body Azure v jednom profilu Traffic Manageru. Kombinování koncové body Azure s externí koncové body umožňuje různé scénáře:

* V obou aktivní aktivní nebo aktivní – pasivní převzetí služeb při selhání model zajistit vyšší redundance pro stávající místní aplikace použijte Azure.
* Ke snížení latencí aplikací pro uživatele po celém světě, rozšiřte stávající místní aplikace na dalších geografické polohy v Azure. Další informace najdete v tématu [Traffic Manager, výkon, směrování provozu](traffic-manager-routing-methods.md#performance).
* Použití Azure poskytnout další kapacitu pro stávající místní aplikace, nepřetržitě nebo jako "shluků to-cloud, řešení ke splnění Špička při vyžádání.

V některých případech je užitečné používat externí koncové body k odkazování služby Azure (příklady najdete v tématu [– nejčastější dotazy](traffic-manager-faqs.md#traffic-manager-endpoints)). V takovém případě kontroly stavu fakturují se za koncové body Azure rychlost, není míra externí koncové body. Na rozdíl od koncové body Azure, zastavení a odstranění základní služby stavu Zkontrolujte však fakturace pokračuje, dokud není zakázat nebo odstranit koncový bod služby Traffic Manager.

## <a name="nested-endpoints"></a>Vnořené koncové body

Vnořené koncové body kombinovat více profily Traffic Manageru k vytvoření flexibilní směrování provozu schémata a podporovala potřeby větší a složité nasazení. S koncovými body vnořené profil "podřízený" přidat jako koncový bod do profilu "nadřazená". Profily podřízenými a nadřazenými může obsahovat další koncové body libovolného typu, včetně jiných vnořených profilů. Další informace najdete v tématu [vnořené profily Traffic Manageru](traffic-manager-nested-profiles.md).

## <a name="web-apps-as-endpoints"></a>Webové aplikace jako koncové body.

Při konfiguraci webové aplikace jako koncové body v Traffic Manageru, platí některé další aspekty:

1. Jsou vhodné pro použití s nástrojem Traffic Manager pouze webové aplikace v "Standard" SKU nebo vyšší. Neúspěšné pokusy o přidání webové aplikace z nižší SKU. Přechod na starší verzi SKU existující webové aplikace výsledků v Traffic Manager už odesílat tak přenosy do této webové aplikace.
2. Když koncový bod přijme požadavek HTTP, používá hlavičku "hostitele" v žádosti k určení by měl obsloužit požadavek, které webové aplikace. Hlavička hostitele obsahuje název DNS, které se použilo k zahájení požadavku, například contosoapp.azurewebsites.net. Pokud chcete použít jiný název DNS s vaší webové aplikace, musí být název DNS zaregistrovat jako název vlastní domény pro aplikaci. Při přidávání koncový bod webové aplikace jako koncový bod Azure, název DNS profilu Správce provozu se automaticky registruje pro aplikaci. Tato registrace je automaticky odstraněna při odstranění koncového bodu.
3. Každý profil služby Traffic Manager může mít maximálně jednu webovou aplikaci endpoint z každé oblasti Azure. Pro toto omezení obejít, můžete nakonfigurovat webovou aplikaci jako externí koncový bod. Další informace najdete v tématu [– nejčastější dotazy](traffic-manager-faqs.md#traffic-manager-endpoints).

## <a name="enabling-and-disabling-endpoints"></a>Povolování a zakazování koncových bodů

Zakázání koncového bodu v Traffic Manager může být užitečné k provozu dočasně odebrání koncový bod, který je v režimu údržby nebo který je znovu nasazován. Jakmile se koncový bod je znovu spustit, může být znovu zapnout.

Koncové body lze povolit a zakázat prostřednictvím portálu Traffic Manager, prostředí PowerShell, rozhraní příkazového řádku nebo REST API, které jsou podporovány v modelu nasazení classic i Resource Manager.

> [!NOTE]
> Zakázání Azure koncového bodu nijak nesouvisí s jeho stavem nasazení v Azure. Služba Azure (například virtuálního počítače nebo webovou aplikaci zůstane spuštěný a může přijímat provoz i v případě, že v Traffic Manageru zakázán. Přenosy lze řešit přímo na instanci služby a nikoli prostřednictvím DNS název profilu Traffic Manageru. Další informace najdete v tématu [fungování Traffic Manager](traffic-manager-how-traffic-manager-works.md).

Aktuální vhodnost každý koncový bod pro příjem přenosů závisí na následujících faktorech:

* Stav profilu (povoleno nebo zakázáno)
* Stav koncového bodu (povoleno nebo zakázáno)
* Výsledky stav vyhledává tohoto koncového bodu

Podrobnosti najdete v tématu [monitorování koncového bodu Traffic Manager](traffic-manager-monitoring.md#endpoint-and-profile-status).

> [!NOTE]
> Vzhledem k tomu, že Traffic Manager funguje na úrovni DNS, není schopen ovlivnit existující připojení pro libovolný koncový bod. Když koncový bod není k dispozici, Traffic Manager určí, že nová připojení k jiným koncovým bodem k dispozici. Hostitel za koncový bod zakázáno nebo není v pořádku, ale mohou i nadále přijímat přenosy přes existující připojení, dokud tyto relace jsou ukončeny. Aplikace měli omezit dobu trvání relace tak, aby umožnila přenosy na vyprazdňování z existující připojení.

Pokud jsou zakázány všechny koncové body v profilu, nebo pokud je zakázán profil sám sebe, Traffic Manager odešle odpověď 'NXDOMAIN' nový dotaz DNS.


## <a name="next-steps"></a>Další kroky

* Další informace [fungování Traffic Manager](traffic-manager-how-traffic-manager-works.md).
* Další informace o Traffic Manager [koncového bodu monitorování a automatické převzetí služeb při selhání](traffic-manager-monitoring.md).
* Další informace o Traffic Manager [metodách směrování provozu](traffic-manager-routing-methods.md).
