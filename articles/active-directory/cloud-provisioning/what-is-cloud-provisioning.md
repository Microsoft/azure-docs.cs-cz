---
title: Co je zřizování cloudu Azure AD Connect. | Microsoft Docs
description: Popisuje Azure AD Connect zřízení cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e88f6f344c77109ee01cd6b0332138518f70270d
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793780"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Co je zřizování cloudu Azure AD Connect?
Zřizování cloudu Azure AD Connect je nový agent Microsoftu navržený tak, aby splnil a splnil vaše hybridní cíle identity pro synchronizaci uživatelů, skupin a kontaktů do Azure AD.  Dá se použít společně Azure AD Connect synchronizaci a nabízí následující výhody:
    
- Podpora synchronizace do tenanta Azure AD z prostředí s odpojenou doménovou strukturou služby Active Directory s více doménovými strukturami: běžné scénáře zahrnují & akvizici, kde jsou doménové struktury služby AD získané společnosti izolované od služby AD nadřazené společnosti. doménové struktury a společnosti, které mají historicky více doménových struktur služby AD.
- Zjednodušená instalace s lehkými zřizovacími agenty: agenti působí jako most ze služby AD do služby Azure AD a všechny konfigurace synchronizace spravované v cloudu. 
- K zjednodušení nasazení s vysokou dostupností je možné použít víc agentů zřizování, zvláště důležité pro organizace, které se spoléhají na synchronizaci hodnot hash hesel ze služby AD do Azure AD.


![Co je služba Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Jak se Azure AD Connect cloudové zřizování liší od Azure AD Connect synchronizace?
Při zřizování cloudu Azure AD Connect se zřizování ze služby AD do služby Azure AD orchestruje v online službách Microsoftu. Organizace potřebuje nasadit, ve svém místním a IaaS prostředí, což je jednoduchý agent, který funguje jako most mezi Azure AD a AD. Konfigurace zřizování je uložená v Azure AD a spravovaná jako součást služby.

Následující tabulka poskytuje srovnání mezi Azure AD Connect a Azure AD Connectm zřizováním cloudu:

| Funkce | Azure Active Directory Connect synchronizace| Zřizování cloudu Azure Active Directory Connect |
|:--- |:---:|:---:|
|Připojení k jedné místní doménové struktuře AD|● |● |
| Připojení k několika místním doménovým strukturám AD |● |● |
| Připojení k několika odpojeným místním doménovým strukturám AD | |● |
| Model instalace pro odlehčené agenty | |● |
| Víc aktivních agentů pro vysokou dostupnost | |● |
| Připojit k adresářům LDAP|●| | 
| Podpora uživatelských objektů |● |● |
| Podpora pro objekty skupiny |● |● |
| Podpora pro objekty kontaktů |● |● |
| Podpora pro objekty zařízení |● | |
| Umožňuje základní přizpůsobení toků atributů. |● |● |
| Synchronizace atributů AD definovaných zákazníkem (rozšíření adresáře) |● | |
| Podpora synchronizace hodnot hash hesel |●|●|
| Podpora předávacího ověřování |●||
| Podpora pro federaci |●|●|
| Bezproblémové jednotné přihlašování|● |●|
| Podporuje instalaci na řadič domény |● |● |
| Podpora pro Windows Server 2012 a Windows Server 2012 R2 |● |● |
| Filtrování domén/organizačních jednotek/skupin |● |● |
| Filtrování v rámci hodnot atributů objektů |● | |
| Povolení minimální sady atributů, které mají být synchronizovány (MinSync) |● |● |
| Povolení odebrání atributů z toku ze služby AD do služby Azure AD |● |● |
| Povolení upřesňujících úprav pro toky atributů |● | |
| Podpora zpětného zápisu (hesla, zařízení, skupiny) |● | |

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je zřizování cloudu Azure AD Connect?](what-is-cloud-provisioning.md)
