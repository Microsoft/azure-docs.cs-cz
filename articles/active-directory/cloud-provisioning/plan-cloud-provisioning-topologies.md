---
title: Azure AD Connect zřizování cloudu podporované topologie a scénáře
description: Toto téma popisuje předpoklady a hardwarové požadavky cloud zřizování.
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
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620872"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect zřizování cloudu podporované topologie a scénáře
Tento článek popisuje různé místní topologie azure active directory (Azure AD), které používají azure ad připojení zřizování cloudu. Tento článek obsahuje pouze podporované konfigurace a scénáře.

> [!IMPORTANT]
> Microsoft nepodporuje úpravy nebo provoz Azure AD Connect zřizování cloudu mimo konfigurace nebo akce, které jsou formálně zdokumentovány. Každá z těchto konfigurací nebo akcí může mít za následek nekonzistentní nebo nepodporovaný stav zřizování cloudu Azure AD Connect. Microsoft proto nemůže pro taková nasazení poskytovat technickou podporu.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Co si pamatovat o všech scénářích a topologii
Následuje seznam informací, které je třeba mít na paměti při výběru řešení.

- Uživatelé a skupiny musí být jednoznačně identifikováni ve všech doménových strukturách.
- Párování mezi doménovými strukturami nedochází při zřizování cloudu
- Uživatel nebo skupina musí být reprezentována pouze jednou ve všech doménových strukturách.
- Zdrojová kotva pro objekty je vybrána automaticky.  Používá ms-DS-ConsistencyGuid, pokud je k dispozici, jinak ObjectGUID se používá.
- Atribut, který se používá pro zdrojovou kotvu, nelze změnit.

## <a name="single-forest-single-azure-ad-tenant"></a>Jedna doménová struktura, jeden klient Azure AD
![Topologie pro jednu doménovou strukturu a jednoho klienta](media/plan-cloud-provisioning-topologies/single-forest.png)

Nejjednodušší topologie je jedna místní doménová struktura s jednou nebo více doménami a jedním klientem Azure AD.  Příklad tohoto scénáře najdete [v tématu Kurz: Jedna doménová struktura s jedním klientem Azure AD](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Vícedoménových doménových oborů, jeden klient Azure AD
![Topologie pro vícedoménovou strukturu a jednoho klienta](media/plan-cloud-provisioning-topologies/multi-forest.png)

Společná topologie je více doménových struktur služby AD s jednou nebo více doménami a jedním klientem Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Existující doménová struktura s Azure AD Connect, nová doménová struktura s cloudovým zřizováním
![Topologie pro jednu doménovou strukturu a jednoho klienta](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Tento scénář je topologie je podobný scénář s více doménovými strukturami, ale tento zahrnuje existující prostředí Azure AD Connect a pak přináší na nové doménové struktury pomocí Azure AD Connect zřizování cloudu.  Příklad tohoto scénáře najdete [v tématu Kurz: Existující doménová struktura s jedním klientem Azure AD](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Pilotní zabezpečení zřizování cloudu Azure AD Connect ve stávající hybridní doménové struktuře služby AD
![Topologie pro jednu doménovou](media/plan-cloud-provisioning-topologies/migrate.png) strukturu a jednoho tenanta Scénář pilotního řízení zahrnuje existenci azure ad connect a azure ad připojení cloud zřizování ve stejné doménové struktuře a vymezení uživatelů a skupin odpovídajícím způsobem. Poznámka: Objekt by měl být v oboru pouze v jednom z nástrojů. 

Příklad tohoto scénáře najdete [v tématu Kurz: Pilotní Azure AD Connect zřizování cloudu v existující synchronizované doménové struktuře služby AD](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřízení cloudu Azure AD Connect?](what-is-cloud-provisioning.md)

