---
title: Zdroje pro migraci aplikací do Azure Active Directory | Dokumentace Microsoftu
description: Prostředky vám pomůžeme s migrací aplikace přístup a ověřování do Azure Active Directory (Azure AD).
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2018
ms.author: barbkess
ms.reviewer: baselden
ms.openlocfilehash: d2ede423a92fe9e0d45155d9470d63689bc8d281
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165529"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Zdroje pro migraci aplikací do Azure Active Directory

Prostředky vám pomůžeme s migrací aplikace přístup a ověřování do Azure Active Directory (Azure AD). Tento krátký dotazník (https://aka.ms/AppsMigrationFeedback) poskytnout zpětnou vazbu na prostředí pro migraci aplikací do služby Azure AD (včetně problémy blokující migraci, třeba pro nástroje / pokyny nebo důvody pro zachování svého zprostředkovatele identity v místním prostředí). 

| Prostředek  | Popis  |
|:-----------|:-------------|
|[Migrace aplikací do služby Azure AD](https://aka.ms/migrateapps/whitepaper) | Tento dokument white paper nabízí výhody migrace a popisuje, jak naplánovat migraci ve čtyřech fázích, jasně uvedených: zjišťování, klasifikaci, migrace a průběžnou správu. Vás provedeme vás jak uvažovat o procesu a rozdělit projekt do částí, snadno využívat. V celém dokumentu jsou odkazy na důležité prostředky, které vám pomůžou na cestě. |
|[Průvodce řešením: Migrace aplikací ze služby Active Directory Federation Services (AD FS) do služby Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Tento průvodce vás provede stejné čtyři fáze plánování a provádění migrace projektu aplikace je popsáno na vyšší úrovni v dokumentu White Paper migrace. V této příručce se dozvíte postupy platí tyto fáze pro specifické cílem přesun aplikace z Azure Directory Federated Services (AD FS) do služby Azure AD.|
| [Nástroj: Active Directory Federation Services migrace připravenosti skript](https://aka.ms/migrateapps/adfstools) | To je skript můžete spustit na serveru místní služby Active Directory Federation Services (AD FS) Chcete-li určit připravenost aplikací pro migraci do Azure AD.|
| [Plán nasazení: migrace ze služby AD FS na synchronizace hodnot hash hesel](https://aka.ms/ADFSTOPHSDPDownload) | Pomocí synchronizace hodnot hash hesel hodnoty hash hesel uživatelů synchronizované z místní služby Active Directory do služby Azure AD. To umožňuje službě Azure AD k ověřování uživatelů bez interakci s místním Active Directory.| 
| [Plán nasazení: migrace ze služby AD FS na předávací ověřování.](https://aka.ms/ADFSTOPTADPDownload)|Předávací ověřování služby Azure AD umožňuje uživatelům přihlašovat se k místním i cloudovým aplikacím pomocí stejné heslo. Tato funkce poskytuje uživatelům lepší, protože mají jeden menší heslo mějte na paměti. Také snižuje náklady na IT helpdesk, protože uživatelé jsou méně pravděpodobné, že zapomenete jak se přihlásit, když potřebují jenom jeden heslo si zapamatujte. Když se uživatelé přihlásí pomocí Azure AD, tato funkce ověří jejich hesla přímo v místní službě Active Directory.|
| [Plán nasazení: povolení jednotného přihlašování pro SaaS aplikace s Azure AD](https://aka.ms/SSODPDownload) | Jednotné přihlašování (SSO) pomáhá přístup všechny aplikace a prostředky, které potřebujete k práci při stačí, když pomocí jediného uživatelského účtu. Například po se uživatel přihlásil, uživatel přesunout z aplikace Microsoft Office pro SalesForce, do pole bez ověřování (například zadáním hesla) podruhé. 
| [Plán nasazení: rozšíření aplikace do služby Azure AD pomocí Proxy aplikace](https://aka.ms/AppProxyDPDownload)| Poskytování přístupu z přenosné počítače zaměstnanců a dalších zařízení pro místní aplikace má obvykle zahrnuta virtuální privátní sítě (VPN) nebo demilitarizovaná zóny (zóny DMZ). Tato řešení jsou nejen složitá a obtížně zabezpečitelná, ale jsou také nákladná na nastavení a správu. Proxy aplikací Azure AD usnadňuje přístup k místním aplikacím. |
| [Plány nasazení](../fundamentals/active-directory-deployment-plans.md) | Najdete další plány nasazení pro nasazení funkce, jako je Vícefaktorové ověřování, podmíněného přístupu, zřizování uživatelů, bezproblémové jednotné přihlašování, samoobslužné resetování hesla a další! |


