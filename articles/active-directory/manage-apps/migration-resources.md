---
title: Prostředky pro migraci aplikací do služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Prostředky, které vám pomohou migrovat přístup k aplikacím a ověřování do služby Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/29/2020
ms.author: mimart
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: b30469858a5dd83f7f5f707f74466302b3000510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968725"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Prostředky pro migraci aplikací do služby Azure Active Directory

Prostředky, které vám pomohou migrovat přístup k aplikacím a ověřování do služby Azure Active Directory (Azure AD). Udělejte sihttps://aka.ms/AppsMigrationFeedback) tento krátký průzkum (chcete-li poskytnout zpětnou vazbu o vašich zkušenostech s migrací aplikací do Azure AD (včetně blokátorů migrace, potřeby nástrojů / pokynů nebo důvodů pro zachování místního IDP). 

| Prostředek  | Popis  |
|:-----------|:-------------|
|[Migrace aplikací do Azure AD](https://aka.ms/migrateapps/whitepaper) | Tato bílá kniha představuje výhody migrace a popisuje, jak plánovat migraci ve čtyřech jasně nastíněných fázích: zjišťování, klasifikace, migrace a průběžné řízení. Budete vedeni tím, jak přemýšlet o procesu a rozdělit svůj projekt na snadno konzumovat kusy. V celém dokumentu jsou odkazy na důležité zdroje, které vám pomohou na cestě. |
|[Průvodce řešením: Migrace aplikací ze služby AD FS (AD FS) do Služby Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Toto řešení vás provede stejnými čtyřmi fázemi plánování a provádění projektu migrace aplikací popsaného na vyšší úrovni v dokumentu white paper migrace. V této příručce se dozvíte, jak použít tyto fáze na konkrétní cíl přesunutí aplikace z Azure Directory Federated Services (AD FS) do Azure AD.|
| [Nástroj: Skript připravenosti migrace služby Active Directory Federation Services](https://aka.ms/migrateapps/adfstools) | Toto je skript, který můžete spustit na místním serveru Služby AD FS (Active Directory Federation Services) a určit připravenost aplikací pro migraci do Služby Azure AD.|
| [Plán nasazení: Migrace ze služby AD FS na synchronizaci hash hesel](https://aka.ms/ADFSTOPHSDPDownload) | Při synchronizaci hodnot hash hesel se hashy uživatelských hesel synchronizují z místní služby Active Directory do služby Azure AD. To umožňuje Azure AD k ověření uživatelů bez interakce s místní službou Active Directory.| 
| [Plán nasazení: Migrace ze služby AD FS na předávací ověřování](https://aka.ms/ADFSTOPTADPDownload)|Předávací ověřování Azure AD pomáhá uživatelům přihlásit se k místním i cloudovým aplikacím pomocí stejného hesla. Tato funkce poskytuje uživatelům lepší zážitek, protože mají o jedno heslo méně k zapamatování. Snižuje také náklady na it helpdesk, protože uživatelé jsou méně pravděpodobné, že zapomenete, jak se přihlásit, když potřebují pamatovat pouze jedno heslo. Když se uživatelé přihlásí pomocí Azure AD, tato funkce ověří jejich hesla přímo v místní službě Active Directory.|
| [Plán nasazení: Povolení jednotného přihlašování k aplikaci SaaS pomocí Azure AD](https://aka.ms/SSODPDownload) | Jednotné přihlašování (SSO) vám pomůže získat přístup ke všem aplikacím a prostředkům, které potřebujete k podnikání, při přihlášení pouze jednou pomocí jednoho uživatelského účtu. Například poté, co se uživatel přihlásí, může se uživatel přesunout z Microsoft Office do SalesForce do boxu bez ověření (například zadání hesla) podruhé. 
| [Plán nasazení: Rozšíření aplikací do Azure AD pomocí proxy aplikace](https://aka.ms/AppProxyDPDownload)| Poskytování přístupu z přenosných počítačů zaměstnanců a dalších zařízení k místním aplikacím tradičně zahrnuje virtuální privátní sítě (VNU) nebo demilitarizované zóny (DMZ). Tato řešení jsou nejen složitá a obtížně zabezpečitelná, ale jsou také nákladná na nastavení a správu. Azure AD Proxy aplikace usnadňuje přístup k místním aplikacím. |
| [Plány nasazení](../fundamentals/active-directory-deployment-plans.md) | Najděte další plány nasazení pro nasazení funkcí, jako je vícefaktorové ověřování, podmíněný přístup, zřizování uživatelů, bezproblémové jednotné přihlašování, samoobslužné resetování hesla a další! |


