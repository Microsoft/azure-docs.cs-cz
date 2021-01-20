---
title: Azure AD Connect podporované topologie a scénáře cloudové synchronizace
description: Přečtěte si o různých topologiích místní a Azure Active Directory (Azure AD), které používají cloudovou synchronizaci Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613244"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Azure AD Connect podporované topologie a scénáře cloudové synchronizace
Tento článek popisuje různé místní a Azure Active Directory (Azure AD) topologie, které používají cloudovou synchronizaci Azure AD Connect. Tento článek obsahuje jenom podporované konfigurace a scénáře.

> [!IMPORTANT]
> Microsoft nepodporuje úpravu ani provozní Azure AD Connect cloudové synchronizace mimo konfigurace nebo akce, které jsou formálně zdokumentované. Kterákoli z těchto konfigurací nebo akcí může způsobit nekonzistentní nebo nepodporovaný stav Azure AD Connect synchronizace cloudu. V důsledku toho společnost Microsoft nemůže poskytnout technickou podporu pro taková nasazení.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Co je třeba si uvědomit o všech scénářích a topologiích
V následující části najdete seznam informací, které je potřeba vzít v úvahu při výběru řešení.

- Uživatelé a skupiny musí být jednoznačně identifikováni ve všech doménových strukturách.
- K porovnání mezi doménovými strukturami nedochází při synchronizaci cloudu.
- Uživatel nebo skupina musí být ve všech doménových strukturách zastoupená jenom jednou.
- Zdrojové ukotvení pro objekty je vybráno automaticky.  Používá MS-DS-ConsistencyGuid, pokud je k dispozici, v opačném případě se používá identifikátor ObjectGUID.
- Nemůžete změnit atribut, který se používá pro zdrojové ukotvení.

## <a name="single-forest-single-azure-ad-tenant"></a>Jedna doménová struktura, jeden tenant Azure AD
![Diagram, který zobrazuje topologii pro jednu doménovou strukturu a jednoho tenanta.](media/tutorial-single-forest/diagram-2.png)

Nejjednodušší topologie je jediná místní doménová struktura s jednou nebo více doménami a jedním tenanta Azure AD.  Příklad tohoto scénáře najdete v tématu [kurz: jediná doménová struktura s jedním klientem služby Azure AD.](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Více doménových struktur, jeden tenant Azure AD
![Topologie pro více doménovou strukturu a jednoho tenanta](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

Společná topologie je několik doménových struktur AD, s jednou nebo více doménami a s jedním tenanta Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Existující doménová struktura s Azure AD Connect, Nová doménová struktura s zřizováním cloudu
![Diagram, který zobrazuje topologii pro existující doménovou strukturu a novou doménovou strukturu.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

Tento scénář je podobný scénáři s více doménovými strukturami, ale ta zahrnuje existující prostředí Azure AD Connect a potom se do nové doménové struktury pomocí Azure AD Connect synchronizace cloudu.  Příklad tohoto scénáře najdete v tématu [kurz: existující doménová struktura s jedním klientem služby Azure AD.](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Pilotní nasazení Azure AD Connect cloudové synchronizace v existující hybridní doménové struktuře AD
![Topologie pro jednu doménovou strukturu a jediného tenanta ](media/tutorial-migrate-aadc-aadccp/diagram-2.png) : pilotní scénář zahrnuje existenci Azure AD Connect i Azure AD Connect cloudové synchronizace ve stejné doménové struktuře a odpovídajícím způsobem určování oboru uživatelů a skupin. Poznámka: objekt by měl být v oboru pouze v jednom z nástrojů. 

Příklad tohoto scénáře najdete v tématu [kurz: pilotní Azure AD Connect cloudové synchronizace v existující synchronizované doménové struktuře AD.](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)

