---
title: Azure AD Connect a federace | Dokumentace Microsoftu
description: Tato stránka je centrální umístění pro veškerou dokumentaci týkající se služby AD FS operace, které používají Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: ce6b889d23959e9d04b95ec2bc5847340b596448
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915832"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect a federace
Konfigurace federace se službou Azure Active Directory (Azure AD) Connect umožňuje místní služby Active Directory Federation Services (AD FS) a Azure AD. S federací přihlášení můžete povolit uživatelům umožní přihlásit k Azure AD službám pomocí jejich místních hesel – a v podnikové síti, aniž byste museli znovu zadávat hesla. Pomocí možnosti federační službou AD FS, můžete nasadit nové instalace služby AD FS, nebo můžete zadat existující instalace ve farmě systému Windows Server 2012 R2.

Toto téma je domovská stránka pro informace o funkcím souvisejícím s federací Azure AD Connect. Vypíše odkazy na všechny související témata. Odkazy na Azure AD Connect, naleznete v části [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Služby Azure AD Connect: témata federace
| Téma | Co obsahuje a kdy k jeho čtení |
|:--- |:--- |
| **Azure AD Connect uživatelské možnosti přihlášení** | |
| [Vysvětlení možností přihlášení uživatele](active-directory-aadconnect-user-signin.md) |Další informace o různých uživatelské možnosti přihlášení a způsob, jakým ovlivňují činnost koncového uživatele Azure přihlášení. |
| **Instalace služby AD FS pomocí služby Azure AD Connect** | |
| [Požadavky](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Viz předpoklady pro úspěšnou instalaci služby AD FS pomocí služby Azure AD Connect. |
| [Konfigurovat farmu služby AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Instalace nové farmě služby AD FS pomocí služby Azure AD Connect. |
| [Provést federaci se službou Azure AD pomocí alternativního přihlašovacího ID ](active-directory-aadconnect-federation-management.md#alternateid) | Konfigurovat federaci pomocí alternativního přihlašovacího ID  |
| **Změny konfigurace služby AD FS** | |
| [Opravit důvěryhodnost](active-directory-aadconnect-federation-management.md#repairthetrust) |Oprava aktuální vztah důvěryhodnosti mezi místní služby AD FS a Office 365 nebo Azure. |
| [Přidání nového serveru služby AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Rozbalte farmu služby AD FS s dalším serverem služby AD FS po počáteční instalaci. |
| [Přidejte nový server AD FS WAP](active-directory-aadconnect-federation-management.md#addwapserver) |Rozbalte farmu služby AD FS s dalším serverem Proxy webových aplikací (WAP) po počáteční instalaci. |
| [Přidání nové federované domény](active-directory-aadconnect-federation-management.md#addfeddomain) |Přidejte jinou doménu k federaci s Azure AD. |
| [Aktualizace certifikátu SSL](active-directory-aadconnectfed-ssl-update.md)| Aktualizace certifikátu SSL pro farmu služby AD FS. |
| [Obnovení federačních certifikátů pro Office 365 a Azure AD](active-directory-aadconnect-o365-certs.md)|Obnovení certifikátu O365 s Azure AD.|
| **Další konfigurace federace** | |
| [Federování několika instancí Azure AD s jednou instancí AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Vytvořit federaci několik služeb Azure AD pomocí jedné farmy služby AD FS| 
| [Přidat vlastní firemní logo a obrázek](active-directory-aadconnect-federation-management.md#customlogo) |Upravte prostředí přihlášení tak, že zadáte vlastní logo, které se zobrazí na přihlašovací stránku služby AD FS. |
| [Přidání popisu přihlášení](active-directory-aadconnect-federation-management.md#addsignindescription) |Změňte popis přihlášení na přihlašovací stránku služby AD FS. |
| [Upravit pravidla deklarací identity služby AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Úprava nebo přidání pravidel deklarací identity ve službě AD FS, které odpovídají konfiguraci synchronizace Azure AD Connect. |


## <a name="additional-resources"></a>Další zdroje informací:
* [Federování dvě služby Azure AD s jednou službou AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Nasazení služby AD FS v Azure](active-directory-aadconnect-azure-adfs.md)
* [Nasazení vysoká dostupnost mezi geografickými AD FS v Azure pomocí Azure Traffic Manageru](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
