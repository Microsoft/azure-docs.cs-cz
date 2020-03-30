---
title: Azure AD Connect a federace | Dokumenty společnosti Microsoft
description: Tato stránka je centrální umístění pro veškerou dokumentaci týkající se operací služby AD FS, které používají Azure AD Connect.
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
ms.topic: conceptual
ms.date: 10/09/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0518c58abf156c718ee083ffadb0ef8e0a590252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331543"
---
# <a name="azure-ad-connect-and-federation"></a>Azure AD Connect a federace
Azure Active Directory (Azure AD) Connect umožňuje konfigurovat federaci s místní službou AD FS (AD FS) a Azure AD. Díky přihlašování federace můžete uživatelům povolit přihlášení ke službám založeným na Azure AD pomocí jejich místních hesel – a v podnikové síti, aniž by museli znovu zadávat svá hesla. Pomocí možnosti federace se službou AD FS můžete nasadit novou instalaci služby AD FS nebo můžete určit existující instalaci ve farmě systému Windows Server 2012 R2.

Toto téma je domovem pro informace o funkcích souvisejících s federací pro Azure AD Connect. Obsahuje odkazy na všechna související témata. Odkazy na Azure AD Connect najdete [v tématu Integrace místních identit pomocí Služby Azure Active Directory](whatis-hybrid-identity.md).

## <a name="azure-ad-connect-federation-topics"></a>Azure AD Connect: témata federace
| Téma | Co pokrývá a kdy si ji přečíst |
|:--- |:--- |
| **Možnosti přihlášení uživatele Azure AD Connect** | |
| [Principy možností přihlášení uživatele](plan-connect-user-signin.md) |Přečtěte si o různých možnostech přihlášení uživatelů a o tom, jak ovlivňují uživatelské prostředí Azure. |
| **Instalace služby AD FS pomocí služby Azure AD Connect** | |
| [Požadavky](how-to-connect-install-custom.md#ad-fs-configuration-pre-requisites) |Podívejte se na předpoklady pro úspěšnou instalaci služby AD FS prostřednictvím služby Azure AD Connect. |
| [Konfigurace farmy služby AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs) |Nainstalujte novou farmu služby AD FS pomocí služby Azure AD Connect. |
| [Federate s Azure AD pomocí alternativní přihlašovací ID](how-to-connect-fed-management.md#alternateid) | Konfigurace federace pomocí alternativního přihlašovacího ID  |
| **Změna konfigurace služby AD FS** | |
| [Oprava vztahu důvěryhodnosti](how-to-connect-fed-management.md#repairthetrust) |Opravte aktuální vztah důvěryhodnosti mezi místní službou AD FS a Office 365/Azure. |
| [Přidání nového serveru služby AD FS](how-to-connect-fed-management.md#addadfsserver) |Po počáteční instalaci rozbalte farmu služby AD FS o další server služby AD FS. |
| [Přidání nového wap serveru služby AD FS](how-to-connect-fed-management.md#addwapserver) |Po počáteční instalaci rozbalte farmu služby AD FS o další server proxy webové aplikace (WAP). |
| [Přidání nové federované domény](how-to-connect-fed-management.md#addfeddomain) |Přidejte další doménu, která má být federována pomocí Azure AD. |
| [Aktualizace certifikátu TLS/SSL](how-to-connect-fed-ssl-update.md)| Aktualizujte certifikát TLS/SSL pro farmu služby AD FS. |
| [Obnovení federačních certifikátů pro Office 365 a Azure AD](how-to-connect-fed-o365-certs.md)|Obnovte svůj certifikát O365 pomocí Azure AD.|
| **Jiná konfigurace federace** | |
| [Vytvoření federace několika instancí Azure AD s jednou instancí AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md) | Federate více Azure AD s jednou farmou AD FS| 
| [Přidání vlastního firemního loga/ilustrace](how-to-connect-fed-management.md#customlogo) |Upravte přihlašovací prostředí zadáním vlastního loga, které se zobrazí na přihlašovací stránce službě AD FS. |
| [Přidání popisu přihlášení](how-to-connect-fed-management.md#addsignindescription) |Změňte popis přihlášení na přihlašovací stránce služby AD FS. |
| [Úprava pravidel deklarací ad FS](how-to-connect-fed-management.md#modclaims) |Upravte nebo přidejte pravidla deklarací ve službě AD FS, která odpovídají konfiguraci synchronizace Azure AD Connect. |


## <a name="additional-resources"></a>Další zdroje
* [Federace dvou Azure AD s jedním AD FS](how-to-connect-fed-single-adfs-multitenant-federation.md)
* [Nasazení služby AD FS v Azure](how-to-connect-fed-azure-adfs.md)
* [Vysoce dostupnost mezigeografické nasazení služby AD FS v Azure pomocí Azure Traffic Manageru](../active-directory-adfs-in-azure-with-azure-traffic-manager.md)
