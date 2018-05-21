---
title: Získání klienta Azure AD | Dokumentace Microsoftu
description: Získání klienta Azure Active Directory pro registraci a vytváření aplikací.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/23/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 051078bd3220c8d0f6b78c834cdf3628c2039e8e
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Získání klienta Azure Active Directory

V Azure Active Directory (Azure AD) představuje [klient](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) zástupce organizace. Je to vyhrazená instance služby Azure AD, kterou organizace obdrží a vlastní, když vytvoří partnerství se společností Microsoft – například tím, že se zaregistruje ke cloudové službě společnosti Microsoft, jako je třeba Azure, Microsoft Intune nebo Office 365. Každý klient Azure AD se odlišuje a je oddělený od ostatních klientů Azure AD. 

V tenantovi se nachází uživatelé ve společnosti a informace o nich – hesla, data uživatelského profilu, oprávnění atd. Obsahuje také skupiny, aplikace a další informace týkající se organizace a jejího zabezpečení.

Pokud chcete uživatelům Azure AD povolit přihlášení do aplikace, je třeba aplikaci zaregistrovat do vašeho klienta. Vytvoření tenanta Azure AD a publikování aplikace v tomto tenantovi je **zcela bezplatné**, můžete se však rozhodnout platit ve vašem tenantovi za prémiové funkce. Ve skutečnosti si mnoho vývojářů vytváří několik tenantů a aplikací pro účely experimentování, vývoje, přípravy a testování.

## <a name="use-an-existing-azure-ad-tenant"></a>Použití existujícího tenanta Azure AD

Mnoho vývojářů už má tenanty prostřednictvím služeb nebo předplatných spojených s tenanty Azure AD, jako jsou například předplatná Office 365 nebo Azure. Pokud si chcete ověřit, jestli už tenanta máte, přihlaste se na portál [Azure Portal](https://portal.azure.com) pomocí účtu, který chcete použít ke správě vaší aplikace, a podívejte se do pravého horního rohu, kde jsou zobrazeny informace o vašem účtu. Pokud tenanta máte, budete k němu automaticky přihlášeni a název tenanta se zobrazí přímo pod názvem vašeho účtu. Pokud najedete myší na název vašeho účtu v pravém horním rohu webu Azure Portal, zobrazí se vaše jméno, e-mail, ID adresáře a tenanta (GUID) a vaše doména. Pokud je k vašemu účtu přidruženo více tenantů, můžete výběrem názvu vašeho účtu otevřít nabídku, ve které můžete mezi jednotlivými tenanty přepínat. Každý tenant má vlastní ID tenanta.

> [!TIP]
> Pokud potřebujete zjistit ID tenanta, můžete tuto informaci získat několika způsoby. ID tenanta můžete získat najetím myší na název vašeho účtu nebo výběrem **Azure Active Directory > Vlastnosti > ID adresáře** na webu Azure Portal.

Pokud existujícího tenanta přidruženého k účtu nemáte, zobrazí se pod názvem vašeho účtu GUID a než si [vytvoříte nového tenanta](#create-a-new-azure-ad-tenant), nebudete moct provádět akce, jako jsou třeba registrace aplikací.

## <a name="create-a-new-azure-ad-tenant"></a>Vytvoření nového tenanta Azure AD

Pokud ještě tenanta Azure AD nemáte nebo chcete vytvořit nového, můžete to udělat pomocí [prostředí pro vytváření adresářů](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) na portálu [Azure Portal](https://portal.azure.com). Tento proces trvá přibližně minutu a na konci se zobrazí výzva, abyste přešli do nově vytvořeného tenanta.
