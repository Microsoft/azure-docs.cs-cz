---
title: Azure AD Connect podporované topologie a scénáře zřizování cloudu
description: Přečtěte si o různých topologiích místní a Azure Active Directory (Azure AD), které používají zřizování cloudu Azure AD Connect.
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
ms.openlocfilehash: d442c980ad5bbe3b56eae127b6e9eaeddf380a4e
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526846"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect podporované topologie a scénáře zřizování cloudu
Tento článek popisuje různé místní a Azure Active Directory (Azure AD) topologie, které používají zřizování cloudu Azure AD Connect. Tento článek obsahuje jenom podporované konfigurace a scénáře.

> [!IMPORTANT]
> Společnost Microsoft nepodporuje úpravu ani Azure AD Connect provozní zřizování cloudu mimo konfigurace nebo akce, které jsou podrobně dokumentovány. Kterákoli z těchto konfigurací nebo akcí může způsobit nekonzistentní nebo nepodporovaný stav zřizování cloudu Azure AD Connect. Microsoft proto nemůže pro taková nasazení poskytovat technickou podporu.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Co je třeba si uvědomit o všech scénářích a topologiích
V následující části najdete seznam informací, které je potřeba vzít v úvahu při výběru řešení.

- Uživatelé a skupiny musí být jednoznačně identifikováni ve všech doménových strukturách.
- Při zřizování cloudu nedochází k shodě mezi doménovými strukturami
- Uživatel nebo skupina musí být ve všech doménových strukturách zastoupená jenom jednou.
- Zdrojové ukotvení pro objekty je vybráno automaticky.  Používá MS-DS-ConsistencyGuid, pokud je k dispozici, v opačném případě se používá identifikátor ObjectGUID.
- Nemůžete změnit atribut, který se používá pro zdrojové ukotvení.

## <a name="single-forest-single-azure-ad-tenant"></a>Jedna doménová struktura, jeden tenant Azure AD
![Topologie pro jednu doménovou strukturu a jednoho tenanta](media/plan-cloud-provisioning-topologies/single-forest.png)

Nejjednodušší topologie je jediná místní doménová struktura s jednou nebo více doménami a jedním tenanta Azure AD.  Příklad tohoto scénáře najdete v tématu [kurz: jediná doménová struktura s jedním klientem služby Azure AD.](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Více doménových struktur, jeden tenant Azure AD
![Topologie pro více doménovou strukturu a jednoho tenanta](media/plan-cloud-provisioning-topologies/multi-forest.png)

Společná topologie je několik doménových struktur AD, s jednou nebo více doménami a s jedním tenanta Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Existující doménová struktura s Azure AD Connect, Nová doménová struktura s zřizováním cloudu
![Topologie pro jednu doménovou strukturu a jednoho tenanta](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Tento scénář je podobný scénáři s více doménovými strukturami, ale ta zahrnuje existující prostředí Azure AD Connect a pak se přinášejí do nové doménové struktury pomocí Azure AD Connect zřizování cloudu.  Příklad tohoto scénáře najdete v tématu [kurz: existující doménová struktura s jedním klientem služby Azure AD.](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Pilotní nasazení Azure AD Connect zřízení cloudu v existující hybridní doménové struktuře AD
![Topologie pro jednu doménovou strukturu a jediného tenanta ](media/plan-cloud-provisioning-topologies/migrate.png) , který pilotní scénář zahrnuje, je existence Azure AD Connect i Azure AD Connectho zřizování cloudu ve stejné doménové struktuře a určení oboru uživatelů a skupin. Poznámka: objekt by měl být v oboru pouze v jednom z nástrojů. 

Příklad tohoto scénáře najdete [v tématu Kurz: zřízení pilotního nasazení cloud Azure AD Connect v existující synchronizované doménové struktuře AD.](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)

