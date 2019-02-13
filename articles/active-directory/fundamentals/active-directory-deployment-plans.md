---
title: Plány nasazení – Azure Active Directory | Dokumentace Microsoftu
description: Začátku do konce pokyny o tom, jak nasadit řadu funkcí Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a2ec578fcc51519cb6794ea87f8e4236d6b1d96
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198078"
---
# <a name="azure-active-directory-deployment-plans"></a>Plány nasazení Azure Active Directory
Hledáte kompletní pokyny pro nasazení některých funkcí Azure Active Directory (Azure AD)? Následující plány nasazení popisují obchodní hodnotu, důležité informace o plánování, návrh a provozní postupy potřebné k úspěšnému zavedení několika běžnějších funkcí Azure AD. 

V dokumentech najdete šablony e-mailů, diagramy architektur systémů, běžné testovací případy a další. 

Velmi bychom uvítali váš názor na tyto dokumenty. Vyplňte tento krátký [dotazník](https://aka.ms/deploymentplanfeedback) o vašich zkušenostech s dokumenty. 

|Scénář |Popis |
|-|-|
|[Multi-Factor Authentication](https://aka.ms/MFADPDownload)|Azure Multi-Factor Authentication (MFA) je řešení dvoustupňového ověřování od Microsoftu. Použitím metod ověřování schválených správcem pomáhá Azure MFA chránit přístup k datům a aplikacím a současně splňuje požadavky na jednoduchý proces přihlašování.|
|[Podmíněný přístup](https://aka.ms/CADPDownload)|S využitím podmíněného přístupu můžete implementovat automatizované rozhodování ohledně řízení přístupu a na základě podmínek určit, kdo bude mít přístup k vašim cloudovým aplikacím.|
|[Samoobslužné resetování hesla](https://aka.ms/SSPRDPDownload)|Samoobslužné resetování hesla pomáhá uživatelům tím, že si kdykoli a kdekoli můžou v případě potřeby resetovat heslo bez zásahu správce.|
|[Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)|Azure AD Privileged Identity Management (PIM) pomáhá spravovat privilegované role správců ve službě Azure AD se prostředky Azure a dalších Microsoft Online Services. PIM poskytuje řešení, jako jsou just-in-time, pracovní postupy schválení žádostí a plně integrované přístup kontroly, abyste mohli identifikovat odkrýt a zabránit škodlivých aktivit privilegovaných rolí v reálném čase.|
|[Jednotné přihlašování](https://aka.ms/SSODPDownload)|Jednotné přihlašování pomáhá získat přístup ke všem aplikacím a prostředkům, které potřebujete ke své obchodní činnosti, prostřednictvím jediného přihlášení s použitím jednoho uživatelského účtu. Po přihlášení můžete přecházet například mezi Microsoft Office, SalesForce a Boxem, aniž by se po vás vyžadovalo další ověření (například zadáním hesla).|
|[Bezproblémové jednotné přihlašování](https://aka.ms/SeamlessSSODPDownload)|Bezproblémové jednotné přihlašování Azure Active Directory (bezproblémové jednotné přihlašování Azure AD) automaticky přihlašuje uživatele, kteří zrovna používají svá podniková zařízení připojená k vaší podnikové síti. Po zapnutí této funkce uživatelé nebudou muset kvůli přihlášení do Azure AD zadávat heslo a obvykle nebudou muset zadávat ani své uživatelské jméno. Tato funkce poskytuje uživatelům snadný přístup k vašim cloudovým aplikacím bez potřeby dalších místních komponent.|
|[Přístupový panel](https://aka.ms/AccessPanelDPDownload)|Nabízejí uživatelům jednoduchý hub ke zjištění a přístupu k aplikacím. Mohly být produktivnější díky samoobslužné funkce, jako je například umožňuje žádat o přístup k nové aplikace a skupiny, nebo spravovat přístup k těmto prostředkům jménem jiných uživatelů.|
|[ADFS pro synchronizaci hodnot hash hesel](https://aka.ms/ADFSTOPHSDPDownload)|Synchronizace hodnot hash hesel zajišťuje synchronizaci hodnot hash hesel uživatelů z místní služby Active Directory do Azure AD a umožňuje službě Azure AD ověřovat uživatele bez jakékoli interakce s místní službou Active Directory.|
|[ADFS pro předávací ověřování](https://aka.ms/ADFSTOPTADPDownload)|Předávací ověřování Azure AD pomáhá uživatelům přihlašovat se k místním i cloudovým aplikacím s použitím stejných hesel. Tato funkce zlepšuje zkušenosti uživatelů, protože si nemusejí pamatovat další heslo, a snižuje náklady na helpdesk IT, protože se snižuje pravděpodobnost, že uživatelé zapomenou, jak se přihlásit. Když se uživatelé přihlásí pomocí Azure AD, tato funkce ověří jejich hesla přímo v místní službě Active Directory.|
|[Proxy aplikací služby Azure AD](https://aka.ms/AppProxyDPDownload)|Dnešní zaměstnanci chtějí být produktivní, ať jsou kdekoli, a to neustále a na jakémkoli zařízení. Chtějí pracovat na vlastních zařízeních, ať už se jedná o tablety, telefony nebo přenosné počítače. Zaměstnanci navíc očekávají, že budou mít přístup ke všem svým aplikacím, až už jde o aplikace SaaS v cloudu nebo místní podnikové aplikace. Poskytnutí přístupu k místním aplikacím tradičně zahrnovalo virtuální privátní sítě (VPN) nebo demilitarizované zóny (DMZ). Tato řešení jsou nejen složitá a obtížně zabezpečitelná, ale jsou také nákladná na nastavení a správu. Existuje však lepší způsob. – Proxy aplikací služby Azure AD|
|[Zřizování uživatelů](https://aka.ms/UserProvisioningDPDownload)|Azure AD pomáhá automatizovat vytváření, údržbu a odebírání identit uživatelů v cloudových aplikacích (SaaS), jako jsou Dropbox, Salesforce, ServiceNow a další.|
|[Zřizování WORKDAY řízené příchozí uživatelů](https://aka.ms/WorkdayDeploymentPlan)|Řízené WORKDAY příchozího zřizování uživatelů služby Active Directory vytvoří základ pro zásady správného řízení identit probíhající a zvyšuje kvalitu obchodních procesů, které jsou závislé na datech autoritativní identity. Pomocí této funkce můžete bez problémů spravovat životní cyklus identity zaměstnanců a zaměstnanců contingent tím, že nakonfigurujete pravidla, která mapují spojovač. funkci Leaver procesů (např. nové zařazení, ukončení, přenos) pro zřizování akce IT (jako je například vytvoření, povolit, Zakázat, odstraňovat účty).|
