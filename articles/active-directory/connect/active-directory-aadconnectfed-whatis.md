---
title: Azure AD Connect a federační | Microsoft Docs
description: Tato stránka je centrálního umístění pro veškerá dokumentace týkající se provozu služby AD FS, které používají Azure AD Connect.
services: active-directory
documentationcenter: ''
author: anandyadavmsft
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
ms.author: anandy
ms.openlocfilehash: 6c14b673bd9664aad54ea8002175999470661e34
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593849"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect a federace
Azure Active Directory (Azure AD) připojit umožňuje konfiguraci federace pomocí místní služby Active Directory Federation Services (AD FS) a Azure AD. Federační přihlášení můžete povolit uživatelům přihlášení k Azure AD na základě služby pomocí jejich místních hesel – a v podnikové síti, aniž by museli znovu zadejte hesla. Pomocí možnosti federační službou AD FS můžete nasadit nové instalace služby AD FS, nebo můžete zadat existující instalace ve farmě Windows serveru 2012 R2.

Toto téma je domovská stránka pro informace o souvisejících s federační funkce Azure AD Connect. Vypíše odkazy na všechny související témata. Odkazy na Azure AD Connect, najdete v části [integrace místních identit s Azure Active Directory](active-directory-aadconnect.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: témata federace
| Téma | Co pokrývá a kdy k jeho čtení |
|:--- |:--- |
| **Azure AD Connect uživatelské možnosti přihlášení** | |
| [Pochopení možností přihlášení uživatele](active-directory-aadconnect-user-signin.md) |Další informace o různých uživatel přihlašovací možnosti a jejich vliv na prostředí Azure přihlášení uživatele. |
| **Instalace služby AD FS pomocí Azure AD Connect** | |
| [Požadavky](active-directory-aadconnect-get-started-custom.md#ad-fs-configuration-pre-requisites) |Viz požadavky pro úspěšnou instalaci služby AD FS pomocí Azure AD Connect. |
| [Konfigurovat farmu služby AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) |Nainstalujte novou farmu služby AD FS pomocí Azure AD Connect. |
| [Vytvořit federaci s Azure AD pomocí alternativního přihlašovacího ID ](active-directory-aadconnect-federation-management.md#alternateid) | Konfigurace federace pomocí alternativního přihlašovacího ID  |
| **Upravte konfiguraci služby AD FS** | |
| [Opravit vztah důvěryhodnosti](active-directory-aadconnect-federation-management.md#repairthetrust) |Oprava aktuální vztah důvěryhodnosti mezi místní služby AD FS a Office 365 nebo Azure. |
| [Přidejte nový server služby AD FS](active-directory-aadconnect-federation-management.md#addadfsserver) |Rozbalte farmu služby AD FS s dalším serverem služby AD FS po počáteční instalaci. |
| [Přidejte nový server AD FS WAP](active-directory-aadconnect-federation-management.md#addwapserver) |Rozbalte farmu služby AD FS s dalším serverem Proxy webových aplikací (WAP) po počáteční instalaci. |
| [Přidání nové federované domény](active-directory-aadconnect-federation-management.md#addfeddomain) |Přidání další domény na federovanou se službou Azure AD. |
| [Aktualizovat certifikát SSL](active-directory-aadconnectfed-ssl-update.md)| Aktualizujte certifikát SSL pro farmu služby AD FS. |
| [Obnovení federačních certifikátů pro Office 365 a Azure AD](active-directory-aadconnect-o365-certs.md)|Obnovte certifikát O365 s Azure AD.|
| **Další konfigurace federace** | |
| [Federování několika instancí Azure AD s jednou instancí AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md) | Vytvořit federaci několika Azure AD s jedné farmy služby AD FS| 
| [Přidat vlastní firemní logo/obrázku](active-directory-aadconnect-federation-management.md#customlogo) |Upravte přihlašování uživatelů tak, že zadáte vlastní logo, které se zobrazí na přihlašovací stránce služby AD FS. |
| [Přidejte popis přihlášení](active-directory-aadconnect-federation-management.md#addsignindescription) |Změňte popis přihlášení na přihlašovací stránce služby AD FS. |
| [Upravit pravidla deklarace identity služby AD FS](active-directory-aadconnect-federation-management.md#modclaims) |Úprava nebo přidání pravidla deklarace identity ve službě AD FS, která odpovídají na konfiguraci synchronizace služby Azure AD Connect. |


## <a name="additional-resources"></a>Další zdroje informací:
* [Federaci dvě Azure AD s jedné služby AD FS](active-directory-aadconnectfed-single-adfs-multitenant-federation.md)
* [Nasazení služby AD FS v Azure](active-directory-aadconnect-azure-adfs.md)
* [Nasazení vysoké dostupnosti geografické mezi AD FS v Azure s Azure Traffic Manager](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
