---
title: Co je Azure AD Connect synchronizace cloudu. | Microsoft Docs
description: Popisuje Azure AD Connect synchronizaci cloudu.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: af4eaa5912cdf7463c81f501d71b69e934f8febb
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306003"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>Co je Azure AD Connect synchronizace cloudu?
Azure AD Connect Cloud Sync je nová nabídka od Microsoftu navržená tak, aby splňovala vaše hybridní cíle identity pro synchronizaci uživatelů, skupin a kontaktů do Azure AD.  To to provede pomocí agenta zřizování cloudu Azure AD místo aplikace Azure AD Connect.  Dá se ale použít společně Azure AD Connect synchronizaci a přináší následující výhody:
    
- Podpora synchronizace s klientem služby Azure AD z prostředí s odpojenou doménovou strukturou služby Active Directory s více doménovými strukturami: mezi běžné scénáře patří akvizice & fúze (kde jsou izolované doménové struktury služby AD získanou společností z doménových struktur AD nadřazené společnosti) a společnosti, které mají historicky více doménových struktur služby AD.
- Zjednodušená instalace s lehkými zřizovacími agenty: agenti působí jako most ze služby AD do služby Azure AD a všechny konfigurace synchronizace spravované v cloudu. 
- K zjednodušení nasazení s vysokou dostupností je možné použít víc agentů zřizování, zvláště důležité pro organizace, které se spoléhají na synchronizaci hodnot hash hesel ze služby AD do Azure AD.
- Podpora velkých skupin s 50 tis členy. Při synchronizaci velkých skupin se doporučuje použít jenom filtr oboru organizačních jednotek.


![Co je služba Azure AD Connect](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>Jak se Azure AD Connect synchronizace cloudu liší od Azure AD Connect synchronizace?
Díky Azure AD Connect synchronizaci cloudu se zřizování ze služby AD do služby Azure AD orchestruje v online službách Microsoftu. Organizace potřebuje nasadit, ve svém místním nebo IaaSm prostředí, což je agent s lehkým zatížením, který funguje jako most mezi Azure AD a AD. Konfigurace zřizování je uložená v Azure AD a spravovaná jako součást služby.

## <a name="azure-ad-connect-cloud-sync-video"></a>Video synchronizace Azure AD Connect cloudu
Následující krátké video poskytuje vynikající přehled Azure AD Connect synchronizace cloudu:

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Porovnání mezi Azure AD Connect a cloudovou synchronizací

Následující tabulka poskytuje srovnání mezi Azure AD Connect a Azure AD Connect cloudovou synchronizací:

| Funkce | Azure Active Directory Connect synchronizace| Synchronizace Azure Active Directory Connect cloudu |
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
| Synchronizovat atributy Exchange Online |● |● |
| Synchronizovat atributy rozšíření 1-15 |● |● |
| Synchronizace atributů AD definovaných zákazníkem (rozšíření adresáře) |● | |
| Podpora synchronizace hodnot hash hesel |●|●|
| Podpora pro ověřování Pass-Through |●||
| Podpora pro federaci |●|●|
| Bezproblémové jednotné přihlašování|● |●|
| Podporuje instalaci na řadič domény |● |● |
| Podpora pro Windows Server 2016|● |● |
| Filtrování domén/organizačních jednotek/skupin |● |● |
| Filtrovat hodnoty atributů objektů |● | |
| Povolení minimální sady atributů, které mají být synchronizovány (MinSync) |● |● |
| Povolení odebrání atributů z toku ze služby AD do služby Azure AD |● |● |
| Povolení upřesňujících úprav pro toky atributů |● | |
| Podpora zpětného zápisu (hesla, zařízení, skupiny) |● | |
| Podpora Azure AD Domain Services|● | |
| [Hybridní zpětný zápis systému Exchange](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Podpora až 150 000 objektů na doménu AD |● |● |
| Podpora velkých skupin – skupiny s až 50 000 členy |● |● |
| Odkazy mezi doménami|● | |
| Zřizování na vyžádání| |● |

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Instalace synchronizace cloudu](how-to-install.md)
