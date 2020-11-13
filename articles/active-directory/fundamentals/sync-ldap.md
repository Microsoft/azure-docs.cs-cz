---
title: Synchronizace protokolu LDAP s Azure Active Directory
description: Pokyny pro architekturu k dosažení synchronizace LDAP s Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e617d7ccc14e65c18eb86877b1c7fb1aeef74cd0
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578888"
---
# <a name="ldap-synchronization-with-azure-active-directory"></a>Synchronizace protokolu LDAP s Azure Active Directory

Protokol LDAP (Lightweight Directory Access Protocol) je protokol adresářové služby, který je spuštěný v zásobníku protokolu TCP/IP. Poskytuje mechanismus, který slouží k připojení k internetovým adresářům, hledání a úpravám. Adresářová služba LDAP je založena na modelu klient-server a její funkci je povolit přístup k existujícímu adresáři. Řada společností závisí na místních serverech LDAP na ukládání uživatelů a skupin pro důležité obchodní aplikace. 

Azure Active Directory (Azure AD) může nahradit synchronizaci LDAP pomocí Azure AD Connect. Služba Azure AD Connect Synchronization provádí všechny operace týkající se synchronizace dat identity mezi místními prostředími a službou Azure AD. 

## <a name="use-when"></a>Kdy použít

Je potřeba synchronizovat data identity mezi místními adresáři LDAP v3 a Azure AD. 

![Diagram architektury](./media/authentication-patterns/ldap-sync.png)

## <a name="components-of-system"></a>Součásti systému

* **Uživatel** : přistupuje k aplikaci, která spoléhá na použití adresáře LDAP V3 k řazení uživatelů a hesel.

* **Webový prohlížeč** : součást, se kterou uživatel komunikuje s přístupem k externí adrese URL aplikace

* **Webová aplikace** : aplikace se závislostmi v adresářích LDAP v3.

* **Azure AD** : Azure AD synchronizuje informace o identitě (uživatelé, skupiny, hesla) z místních adresářů LDAP v organizaci prostřednictvím Azure AD Connect. 

* **Azure AD Connect** : je nástroj pro připojení místních infrastruktur identity k Microsoft Azure AD. Průvodce a v prostředí s asistencí usnadňuje nasazení a konfiguraci požadavků a komponent vyžadovaných pro připojení. 

* **Vlastní konektor** : obecný konektor LDAP umožňuje integrovat službu Azure AD Connect Synchronization Service se serverem LDAP v3. Je umístěný na Azure AD Connect.

* **Active Directory** : Služba Active Directory je adresářová služba obsažená ve většině operačních systémů Windows Server. Servery se službou Active Directory Directory Services se nazývají řadiče domény a ověřují a autorizují všechny uživatele a počítače v doméně systému Windows.

* **Server LDAP V3** : adresář kompatibilní s protokolem LDAP, který ukládá firemní uživatele a hesla používaná pro ověřování adresářových služeb.

## <a name="implement-ldap-synchronization-with-azure-ad"></a>Implementace synchronizace LDAP s Azure AD

* [Nástroje pro integraci adresáře Hybrid identity](https://docs.microsoft.com/azure/active-directory/hybrid/plan-hybrid-identity-design-considerations-tools-comparison) 

* [Průvodce instalací Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-roadmap) 

* [Přehled a Vytvoření konektoru LDAP](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap) 

   > [!NOTE]
   > Konektory LDAP jsou pokročilou konfigurací, která vyžaduje určitou znalost s nástrojem Forefront Identity Manager nebo Microsoft Identity Manager. Pokud se tato konfigurace používá v produkčním prostředí, doporučuje se k ní klást dotazy [Premier Support](https://support.microsoft.com/premier) nebo Microsoft Partner Network.

 
