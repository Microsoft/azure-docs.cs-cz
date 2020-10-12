---
title: Azure AD Connect a federace | Microsoft Docs
description: Tato stránka je centrálním umístěním pro veškerou dokumentaci týkající se AD FSch operací, které používají Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f9107cf5-0131-499a-9edf-616bf3afef4d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7d61a3dd995efd1f433c2e862c4b7a59d31f79a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660848"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect a federace
Služba Azure Active Directory (Azure AD) Connect umožňuje konfigurovat federaci s místními Active Directory Federation Services (AD FS) (AD FS) a Azure AD. Pomocí federačního přihlášení můžete uživatelům povolit, aby se přihlásili ke službám založeným na službě Azure AD pomocí místních hesel – a v podnikové síti bez nutnosti zadávat hesla znovu. Pomocí možnosti federace s AD FS můžete nasadit novou instalaci AD FS nebo můžete zadat existující instalaci v serverové farmě Windows Serveru 2012 R2.

Toto téma je domovkou pro informace o funkcích týkajících se federace pro Azure AD Connect. Obsahuje odkazy na všechna související témata. Odkazy na Azure AD Connect najdete v tématu [Integrace místních identit s Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: témata federace
| Téma | Co pokrývá a kdy se má přečíst |
|:--- |:--- |
| **Možnosti přihlášení uživatele Azure AD Connect** | |
| [Pochopení možností přihlašování uživatelů](plan-connect-user-signin.md) |Přečtěte si o různých možnostech přihlašování uživatelů a o tom, jak mají vliv na uživatelské prostředí pro přihlašování do Azure. |
| **Instalace AD FS pomocí Azure AD Connect** | |
| [Požadavky](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Projděte si požadavky na úspěšnou AD FS instalaci prostřednictvím Azure AD Connect. |
| [Konfigurace farmy AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Nainstalujte novou AD FSovou farmu pomocí Azure AD Connect. |
| [Federovat se službou Azure AD s použitím alternativního přihlašovacího ID](how-to-connect-fed-management.md#alternateid) | Konfigurace federace pomocí alternativního přihlašovacího ID  |
| **Úprava konfigurace AD FS** | |
| [Opravit vztah důvěryhodnosti](how-to-connect-fed-management.md#repairthetrust) |Opravte aktuální vztah důvěryhodnosti mezi místními AD FS a Microsoft 365/Azure. |
| [Přidat nový server AD FS](how-to-connect-fed-management.md#addadfsserver) |Po počáteční instalaci rozbalte farmu AD FS s dalším AD FS serverem. |
| [Přidat nový AD FS Server WAP](how-to-connect-fed-management.md#addwapserver) |Po počáteční instalaci rozbalte farmu AD FS s dalším serverem proxy webových aplikací (WAP). |
| [Přidat novou federované doménu](how-to-connect-fed-management.md#addfeddomain) |Přidejte další doménu, kterou chcete federovaným pomocí Azure AD. |
| [Aktualizace certifikátu TLS/SSL](how-to-connect-fed-ssl-update.md)| Aktualizujte certifikát TLS/SSL pro AD FS farmu. |
| [Obnovení federačních certifikátů pro Microsoft 365 a Azure AD](how-to-connect-fed-o365-certs.md)|Prodlužte si platnost certifikátu O365 pomocí Azure AD.|
| **Jiná konfigurace federace** | |
| [Vytvoření federace několika instancí Azure AD s jednou instancí AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federovat více Azure AD s jednou AD FS farmou| 
| [Přidání vlastního loga nebo obrázku vlastní společnosti](how-to-connect-fed-management.md#customlogo) |Určete vlastní logo, které se zobrazí na přihlašovací stránce AD FS, a upravte prostředí pro přihlašování. |
| [Přidat popis přihlášení](how-to-connect-fed-management.md#addsignindescription) |Změňte přihlašovací popis na přihlašovací stránce AD FS. |
| [Upravit pravidla deklarace AD FS](how-to-connect-fed-management.md#modclaims) |Upravte nebo přidejte pravidla deklarace identity v AD FS, která odpovídají konfiguraci Azure AD Connect synchronizace. |


## <a name="additional-resources"></a>Další zdroje
* [Federování dvě služby Azure AD s jedním AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Nasazení služby AD FS v Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs)
* [Vysoce dostupná nasazení mezi geografickými AD FSy v Azure s využitím Azure Traffic Manager](/windows-server/identity/ad-fs/deployment/active-directory-adfs-in-azure-with-azure-traffic-manager)