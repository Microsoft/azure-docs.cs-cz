---
title: Co je zřizování cloudu Azure AD Connect. | Dokumentace Microsoftu
description: Popisuje zřizování cloudu Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a86d34fca9a88b0df601533a0f3de1cc97ad1a2f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050602"
---
# <a name="what-is-azure-ad-connect-cloud-provisioning"></a>Co je zřízení cloudu Azure AD Connect?
Azure AD Connect cloud zřizování je nový agent Microsoft usplnění a dosažení vašich cílů hybridní identity pro synchronizaci uživatelů, skupin a kontaktů do Azure AD.  Lze jej použít společně se synchronizací Azure AD Connect a poskytuje následující výhody:
    
- Podpora pro synchronizaci s tenantem Azure AD z vícedoménového prostředí doménové struktury služby Active Directory: Mezi běžné scénáře patří fúze & akvizice, kde jsou doménové struktury služby AD získané společnosti izolovány od doménových struktur služby AD nadřazené společnosti a společností, které měly v minulosti více doménových struktur služby AD.
- Zjednodušená instalace s agenty pro zřizování s lehkou hmotností: Agenti fungují jako most ze služby AD do služby Azure AD se všemi konfiguracemi synchronizace spravovanými v cloudu. 
- Více zřizovacích agentů lze použít ke zjednodušení nasazení s vysokou dostupností, zvláště důležité pro organizace, které spoléhají na synchronizaci hodnot hash hesla ze služby AD do služby Azure AD.


![Co je služba Azure AD Connect](media/what-is-cloud-provisioning/architecture.png)

## <a name="how-is-azure-ad-connect-cloud-provisioning-different-from-azure-ad-connect-sync"></a>Jak se zřizování cloudu Azure AD Connect liší od synchronizace Azure AD Connect?
Díky zřizování cloudu Azure AD Connect se zřizování ze služby AD do Azure AD řídí ve službách Microsoft Online Services. Organizace potřebuje pouze nasadit, v jejich místní a IaaS hostované prostředí, lehký agent, který funguje jako most mezi Azure AD a AD. Konfigurace zřizování je uložená ve službě Azure AD a spravována jako součást služby.

Následující tabulka obsahuje porovnání mezi Azure AD Connect a Azure AD Connect zřizování cloudu:

| Funkce | Synchronizace služby Azure Active Directory Connect| Zřizování cloudu služby Azure Active Directory Connect |
|:--- |:---:|:---:|
|Připojení k jedné místní doménové struktuře AD|● |● |
| Připojení k několika místním doménovým strukturám AD |● |● |
| Připojení k více odpojeným místním doménovým strukturám služby AD | |● |
| Model instalace s lehkým agentem | |● |
| Více aktivních agentů pro vysokou dostupnost | |● |
| Připojení k adresářům LDAP|●| | 
| Podpora uživatelských objektů |● |● |
| Podpora skupinových objektů |● |● |
| Podpora kontaktních objektů |● |● |
| Podpora objektů zařízení |● | |
| Povolit základní přizpůsobení pro toky atributů |● |● |
| Sychronize Exchange online atributy |● |● |
| Synchronizovat atributy rozšíření 1-15 |● |● |
| Synchronizace atributů AD definovaných zákazníkem (rozšíření adresářů) |● | |
| Podpora synchronizace hash hesel |●|●|
| Podpora předcházejícího ověřování |●||
| Podpora federace |●|●|
| Bezproblémové jednotné přihlašování|● |●|
| Podporuje instalaci na řadič domény |● |● |
| Podpora pro Windows Server 2012 a Windows Server 2012 R2 |● |● |
| Filtrování domén/vu/skupin |● |● |
| Filtrování hodnot atributů objektů |● | |
| Povolení minimální sady atributů, které mají být synchronizovány (MinSync) |● |● |
| Povolení odebrání atributů z toku ze služby AD do služby Azure AD |● |● |
| Povolení upřesňujících úprav pro toky atributů |● | |
| Podpora zpětného zápisu (hesla, zařízení, skupiny) |● | |
| Podpora doménových služeb Azure AD|● | |
| [Exchange hybridní zpětný zápis](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Podpora více než 50 000 objektů na doménu služby AD |● | |

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Instalace zřizování cloudu](how-to-install.md)
