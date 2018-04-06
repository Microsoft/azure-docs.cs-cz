---
title: Nejčastější dotazy pro Azure Active Directory jednotné přihlašování modulu plug-in | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se konfigurace jednotného přihlašování mezi Azure Active Directory a Jira/soutoku.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4b663047-7f88-443b-97bd-54224b232815
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2018
ms.author: jeedes
ms.openlocfilehash: b642d6de9fd9265d7f7ee11b827a36d20e5bcce7
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="faq-for-the-azure-active-directory-sso-plug-in"></a>Nejčastější dotazy pro Azure Active Directory jednotné přihlašování modulu plug-in 

## <a name="what-does-the-plug-in-do"></a>Jaké jsou modulu plug-in proveďte?

Modul plug-in poskytuje možnost jednotné přihlašování (SSO) pro Atlassian Jira (včetně Jira jádra, Jira Software, Jira technickou podporu) a soutoku místní software. Modul plug-in funguje s Azure Active Directory (Azure AD) jako zprostředkovatel identity (IdP).

## <a name="which-atlassian-products-does-the-plug-in-work-with"></a>Které produkty Atlassian podporuje modul plug-in pracují s?

Modul plug-in funguje s místní verzí Jira a soutoku.

## <a name="does-the-plug-in-work-on-cloud-versions"></a>Modul plug-in funguje ve verzích cloudu?

Ne. Modul plug-in podporuje pouze místní verze Jira a soutoku.

## <a name="which-versions-of-jira-and-confluence-does-the-plug-in-support"></a>Jaké verze Jira a soutoku podporuje modul plug-in podporu?

Modul plug-in podporuje tyto verze:

* Základní Jira a Software: 6.0 na 7.2.2 
* Jira technickou podporu: 3.2 k 3.0 
* Soutoku: 5.0 pro 5.10

## <a name="is-the-plug-in-free-or-paid"></a>Je bezplatná modul plug-in nebo placené?

Je bezplatné rozšíření. Můžete ho nainstalovat z webu Atlassian Marketplace.

## <a name="do-i-need-to-restart-jira-or-confluence-after-i-deploy-the-plug-in"></a>Je potřeba restartovat Jira nebo soutoku po nasadit modul plug-in?

Restart není vyžadován. Můžete začít používat okamžitě modulu plug-in.

## <a name="how-do-i-get-support-for-the-plug-in"></a>Jak získat podporu pro modul plug-in?

Oslovení [týmu Integrace Azure AD jednotného přihlašování k](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Tým odpovídá v 24 48 pracovní dobu. 

Můžete taky zvýšit lístek podpory se společností Microsoft prostřednictvím portálu Azure kanálu.

## <a name="would-the-plug-in-work-on-a-mac-or-ubuntu-installation-of-jira-and-confluence"></a>Modul plug-in práce na instalaci Mac nebo Ubuntu Jira a soutoku by?

Jsme testovali modul plug-in pouze v 64bitových instalacích systému Windows Server Jira a soutoku.

## <a name="does-the-plug-in-work-with-idps-other-than-azure-ad"></a>Modul plug-in funguje s IdPs než Azure AD?

Ne. Funguje pouze s Azure AD.

## <a name="what-version-of-saml-does-the-plug-in-work-with"></a>Jaká verze SAML podporuje modul plug-in pracují s?

Funguje s SAML 2.0.

## <a name="does-the-plug-in-do-user-provisioning"></a>Modul plug-in dělá zřizování uživatelů?

Ne. Modul plug-in poskytuje pouze na základě SAML 2.0 SSO. Uživatel má zřídit v aplikaci před přihlášením jednotné přihlašování.

## <a name="does-the-plug-in-support-cluster-versions-of-jira-and-confluence"></a>Podporuje verze modulu plug-in podpora clusteru Jira a soutoku?

Ne. Modul plug-in funguje s místní verzí Jira a soutoku.

## <a name="does-the-plug-in-work-with-http-versions-of-jira-and-confluence"></a>Modul plug-in funguje s verzemi HTTP Jira a soutoku?

Ne. Modul plug-in funguje s povoleným protokolem HTTPS instalace pouze.