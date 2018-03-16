---
title: "Microsoft Azure Active Directory jednu přihlašování modulů plug-in – nejčastější dotazy | Microsoft Docs"
description: "Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Microsoft Azure Active Directory jednotné přihlašování pro JIRA."
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
ms.openlocfilehash: 571fbd5078f66375f6e81cba2a790121366f9d60
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="microsoft-azure-active-directory-single-sign-on-plugin-faq"></a>Microsoft Azure Active Directory jednu přihlašování modulů plug-in – nejčastější dotazy 

## <a name="1-whats-the-microsoft-sso-add-on"></a>1. Co je doplněk Microsoft Jednotné přihlašování?

Toto rozšíření poskytuje možnost jednotného přihlašování na Atlassian JIRA (včetně JIRA jádra, JIRA Software, JIRA technickou podporu) a soutoku místní software. Doplněk spolupracuje se službou Azure AD jako poskytovatel identity.

## <a name="2-add-on-works-with-which-atlassian-products"></a>2. Rozšíření funguje s produktů, které Atlassian?

Od nyní doplňku pracuje s místní verzí JIRA a soutoku.

## <a name="3-does-this-add-on-work-on-cloud-version"></a>3. Funguje toto rozšíření na cloudovou verzi?

Ne. Jsou podporovány pouze místní verze JIRA a soutoku.

## <a name="4-which-versions-of-jira-and-confluence-are-supported"></a>4. Jaké verze JIRA a soutoku jsou podporovány?

Níže je seznam verzí, které jsou podporovány:

* Základní JIRA a Software: 6.0 na 7.2.2 
* JIRA technickou podporu: 3.2 k 3.0 
* Soutoku: 5.0 pro 5.10

## <a name="5-is-this-add-on-free-or-paid"></a>5. Je toto rozšíření volné nebo zaplacenou?

To představuje bezplatné rozšíření a lze nainstalovat z Atlassian Marketplace.

## <a name="6-do-i-need-to-restart-jiraconfluence-once-i-deploy-the-add-on"></a>6. Je potřeba znovu JIRA/soutoku až nasadit doplňku

Restart není požadovaná post rozšíření nasazení. Můžete začít používat okamžitě po nasazení doplňku.

## <a name="7-how-do-i-get-support-for-the-add-on"></a>7. Jak získat podporu pro doplňku?

Oslovení nám na: <email> . Odešleme do <> hodin. Můžete taky zvýšit lístek podpory se společností Microsoft prostřednictvím portálu Azure kanálu. Můžete taky kontaktovat nás telefonicky na: <Number> mezi <> mě <> pm ve všední dny.

## <a name="8-would-this-add-on-work-on-mac-or-ubuntu-installation-of-jira-and-confluence"></a>8. By tato rozšíření fungovat na Mac nebo Ubuntu instalace JIRA a soutoku?

Toto rozšíření jenom na 64bitová verze Windows serveru instalace JIRA a soutoku jsme testovali.

## <a name="9-does-this-add-on-work-with-other-idps-than-azure-ad"></a>9. Funguje tento doplněk pomocí jiných IdPs než Azure AD?

Ne. Rozšíření funguje pouze s Azure AD.

## <a name="10-what-version-of-saml-does-the-add-on-work-with"></a>10. Jaká verze SAML doplňku funguje s?

Rozšíření pracuje s SAML 2.0.

## <a name="11-does-the-add-on-do-use-provisioning-as-well"></a>11. Nepodporuje doplňku používat také zřizování?

Ne. Od nyní rozšíření poskytuje že jenom SAML 2.0 na základě jednotné přihlašování. Uživatel musí být zřízené v aplikaci před jednotného přihlášení.

## <a name="12-are-cluster-versions-of-jira-and-confluence-supported-by-add-on"></a>12. Jsou verze clusteru JIRA a soutoku nepodporuje rozšíření?

Ne. Doplněk pracuje s místní verzí JIRA a soutoku.

## <a name="13-would-this-plugin-work-with-http-version-of-jira-and-confluence"></a>13. By tento modul plug-in fungovat s verzí HTTP JIRA a soutoku?

Ne. Rozšíření funguje s protokolem HTTPS povoleno pouze u instalací.

## <a name="14-do-i-need-to-buy-license-of-the-add-on"></a>14. Potřebuji k zakoupení licence doplňku?

Je bezplatné rozšíření.