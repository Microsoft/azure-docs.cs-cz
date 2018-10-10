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
ms.date: 10/09/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 214dd95bb277053794656e1ba3dd148c085688ce
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900440"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect a federace
Konfigurace federace se službou Azure Active Directory (Azure AD) Connect umožňuje místní služby Active Directory Federation Services (AD FS) a Azure AD. S federací přihlášení můžete povolit uživatelům umožní přihlásit k Azure AD službám pomocí jejich místních hesel – a v podnikové síti, aniž byste museli znovu zadávat hesla. Pomocí možnosti federační službou AD FS, můžete nasadit nové instalace služby AD FS, nebo můžete zadat existující instalace ve farmě systému Windows Server 2012 R2.

Toto téma je domovská stránka pro informace o funkcím souvisejícím s federací Azure AD Connect. Vypíše odkazy na všechny související témata. Odkazy na Azure AD Connect, naleznete v části [integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Služby Azure AD Connect: témata federace
| Téma | Co obsahuje a kdy k jeho čtení |
|:--- |:--- |
| **Azure AD Connect uživatelské možnosti přihlášení** | |
| [Vysvětlení možností přihlášení uživatele](plan-connect-user-signin.md) |Další informace o různých uživatelské možnosti přihlášení a způsob, jakým ovlivňují činnost koncového uživatele Azure přihlášení. |
| **Instalace služby AD FS pomocí služby Azure AD Connect** | |
| [Požadavky](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Viz předpoklady pro úspěšnou instalaci služby AD FS pomocí služby Azure AD Connect. |
| [Konfigurovat farmu služby AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Instalace nové farmě služby AD FS pomocí služby Azure AD Connect. |
| [Provést federaci se službou Azure AD pomocí alternativního přihlašovacího ID ](how-to-connect-fed-management.md#alternateid) | Konfigurovat federaci pomocí alternativního přihlašovacího ID  |
| **Změny konfigurace služby AD FS** | |
| [Opravit důvěryhodnost](how-to-connect-fed-management.md#repairthetrust) |Oprava aktuální vztah důvěryhodnosti mezi místní služby AD FS a Office 365 nebo Azure. |
| [Přidání nového serveru služby AD FS](how-to-connect-fed-management.md#addadfsserver) |Rozbalte farmu služby AD FS s dalším serverem služby AD FS po počáteční instalaci. |
| [Přidejte nový server AD FS WAP](how-to-connect-fed-management.md#addwapserver) |Rozbalte farmu služby AD FS s dalším serverem Proxy webových aplikací (WAP) po počáteční instalaci. |
| [Přidání nové federované domény](how-to-connect-fed-management.md#addfeddomain) |Přidejte jinou doménu k federaci s Azure AD. |
| [Aktualizace certifikátu SSL](how-to-connect-fed-ssl-update.md)| Aktualizace certifikátu SSL pro farmu služby AD FS. |
| [Obnovení federačních certifikátů pro Office 365 a Azure AD](how-to-connect-fed-o365-certs.md)|Obnovení certifikátu O365 s Azure AD.|
| **Další konfigurace federace** | |
| [Federování několika instancí Azure AD s jednou instancí AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Vytvořit federaci několik služeb Azure AD pomocí jedné farmy služby AD FS| 
| [Přidat vlastní firemní logo a obrázek](how-to-connect-fed-management.md#customlogo) |Upravte prostředí přihlášení tak, že zadáte vlastní logo, které se zobrazí na přihlašovací stránku služby AD FS. |
| [Přidání popisu přihlášení](how-to-connect-fed-management.md#addsignindescription) |Změňte popis přihlášení na přihlašovací stránku služby AD FS. |
| [Upravit pravidla deklarací identity služby AD FS](how-to-connect-fed-management.md#modclaims) |Úprava nebo přidání pravidel deklarací identity ve službě AD FS, které odpovídají konfiguraci synchronizace Azure AD Connect. |


## <a name="additional-resources"></a>Další zdroje informací:
* [Federování dvě služby Azure AD s jednou službou AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Nasazení služby AD FS v Azure](how-to-connect-fed-azure-adfs.md)
* [Nasazení vysoká dostupnost mezi geografickými AD FS v Azure pomocí Azure Traffic Manageru](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
