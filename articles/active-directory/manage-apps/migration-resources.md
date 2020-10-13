---
title: Prostředky pro migraci aplikací na Azure Active Directory | Microsoft Docs
description: Materiály, které vám pomůžou migrovat přístup k aplikacím a ověřování pro Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/29/2020
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: 327e59d3fee6f23d2635b846e9fb5ab422e0139e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87429034"
---
# <a name="resources-for-migrating-applications-to-azure-active-directory"></a>Prostředky pro migraci aplikací na Azure Active Directory

Materiály, které vám pomůžou migrovat přístup k aplikacím a ověřování pro Azure Active Directory (Azure AD).

| Prostředek  | Popis  |
|:-----------|:-------------|
|[Migrace aplikací do Azure AD](https://aka.ms/migrateapps/whitepaper) | Tento dokument White Paper přináší výhody migrace a popisuje, jak naplánovat migraci ve čtyřech jasně uvedených fázích: zjišťování, klasifikace, migrace a průběžná správa. Provedete vás postupem, jak se zamyslet na proces a rozdělte projekt na snadno ovladatelné části. V celém dokumentu jsou odkazy na důležité prostředky, které vám pomůžou na cestě. |
|[Průvodce řešením: migrace aplikací z Active Directory Federation Services (AD FS) (AD FS) do služby Azure AD](https://aka.ms/migrateapps/adfssolutionguide) | Tento průvodce řešením vás provede stejnými čtyřmi fázemi plánování a provádění projektu migrace aplikace popsaného na vyšší úrovni v dokumentu White paper pro migraci. V této příručce se dozvíte, jak tyto fáze použít na konkrétní cíl přesunutí aplikace ze služby Azure Directory federovaným Services (AD FS) do Azure AD.|
| [Nástroj: Active Directory Federation Services (AD FS) skript připravenosti migrace](https://aka.ms/migrateapps/adfstools) | Tento skript můžete spustit na místním serveru Active Directory Federation Services (AD FS) (AD FS) a určit připravenost aplikací pro migraci do služby Azure AD.|
| [Plán nasazení: migrace z AD FS na synchronizaci hodnot hash hesel](https://aka.ms/ADFSTOPHSDPDownload) | Díky synchronizaci hodnot hash hesel jsou hash hesla uživatelů synchronizovaná z místní služby Active Directory do Azure AD. Díky tomu může Azure AD ověřovat uživatele bez interakce s místní službou Active Directory.| 
| [Plán nasazení: migrace z AD FS do předávacího ověřování](https://aka.ms/ADFSTOPTADPDownload)|Předávací ověřování Azure AD pomáhá uživatelům přihlašovat se pomocí stejného hesla k místním i cloudovým aplikacím. Tato funkce poskytuje vašim uživatelům lepší možnosti, protože mají jedno méně hesla, než aby si pamatovali. Také snižuje náklady na helpdesk IT, protože uživatelé mají méně pravděpodobný způsob, jak se přihlásit, když potřebují pamatovat pouze jedno heslo. Když se uživatelé přihlásí pomocí Azure AD, tato funkce ověří jejich hesla přímo v místní službě Active Directory.|
| [Plán nasazení: povolení jednotného přihlašování k aplikaci SaaS pomocí Azure AD](https://aka.ms/SSODPDownload) | Jednotné přihlašování (SSO) vám pomůže přistupovat ke všem aplikacím a prostředkům, které potřebujete k podnikání, a současně se přihlašujete pomocí jediného uživatelského účtu. Například po přihlášení uživatele může uživatel přesunout z systém Microsoft Office, do SalesForce, do boxu bez ověřování (například zadání hesla) podruhé. 
| [Plán nasazení: rozšíření aplikací do Azure AD s využitím proxy aplikací](https://aka.ms/AppProxyDPDownload)| Poskytnutí přístupu z přenosných počítačů a jiných zařízení od zaměstnanců k místním aplikacím tradičně zahrnuje virtuální privátní sítě (VPN) nebo zóny demilitarizovaná (zóny DMZ). Tato řešení jsou nejen složitá a obtížně zabezpečitelná, ale jsou také nákladná na nastavení a správu. Azure Proxy aplikací služby AD usnadňuje přístup k místním aplikacím. |
| [Plány nasazení](../fundamentals/active-directory-deployment-plans.md) | Seznamte se s dalšími plány nasazení pro nasazování funkcí, jako je Multi-Factor Authentication, podmíněný přístup, zřizování uživatelů, bezproblémové jednotné přihlašování, Samoobslužné resetování hesla a další. |


