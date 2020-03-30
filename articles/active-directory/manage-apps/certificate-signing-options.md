---
title: Pokročilé možnosti podepisování certifikátů tokenů SAML pro aplikace Azure AD
description: Zjistěte, jak používat pokročilé možnosti podepisování certifikátů v tokenu SAML pro předem integrované aplikace ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: mimart
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc911ff06208b1fd0af7651c8274a45c958bf0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159195"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Pokročilé možnosti podepisování certifikátů v tokenu SAML pro galerijní aplikace ve službě Azure Active Directory

Azure Active Directory (Azure AD) dnes podporuje tisíce předintegrovaných aplikací v Galerii aplikací Azure Active Directory. Více než 500 aplikací podporuje jednotné přihlašování pomocí protokolu [SAML (Security Assertion Markup Language)](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) 2.0, například aplikace [NetSuite.](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) Když se zákazník ověří do aplikace prostřednictvím Azure AD pomocí SAML, Azure AD odešle token do aplikace (prostřednictvím HTTP POST). Aplikace pak ověří a použije token k přihlášení zákazníka namísto výzvy k zadání uživatelského jména a hesla. Tyto tokeny SAML jsou podepsány pomocí jedinečného certifikátu, který je generován ve službě Azure AD a konkrétnístandardní algoritmy.

Azure AD používá některá výchozí nastavení pro aplikace galerie. Výchozí hodnoty jsou nastaveny na základě požadavků aplikace.

Ve službě Azure AD můžete nastavit možnosti podepisování certifikátů a algoritmus podepisování certifikátů.

## <a name="certificate-signing-options"></a>Možnosti podpisu certifikátů

Azure AD podporuje tři možnosti podepisování certifikátů:

* **Podepište kontrolní výraz SAML**. Tato výchozí možnost je nastavena pro většinu aplikací galerie. Pokud vyberete tuto možnost, Azure AD jako zprostředkovatel identity (IdP) podepíše kontrolní výraz SAML a certifikát s certifikátem [X.509](https://wikipedia.org/wiki/X.509) aplikace.

* **Podepsat odpověď SAML**. Pokud vyberete tuto možnost, Azure AD jako IdP podepíše odpověď SAML s certifikátem X.509 aplikace.

* **Podepsat odpověď a kontrolní výraz SAML**. Pokud vyberete tuto možnost, Azure AD jako IdP podepíše celý token SAML s certifikátem X.509 aplikace.

## <a name="certificate-signing-algorithms"></a>Algoritmy podepisování certifikátů

Azure AD podporuje dva algoritmy podepisování nebo zabezpečené algoritmy hash (SH), k podepsání odpovědi SAML:

* **SHA-256**. Azure AD používá tento výchozí algoritmus k podepsání odpovědi SAML. Je to nejnovější algoritmus a je bezpečnější než SHA-1. Většina aplikací podporuje algoritmus SHA-256. Pokud aplikace podporuje pouze SHA-1 jako podpisový algoritmus, můžete jej změnit. V opačném případě doporučujeme použít algoritmus SHA-256 pro podepsání odpovědi SAML.

* **SHA-1**. Tento algoritmus je starší a je považován za méně bezpečný než SHA-256. Pokud aplikace podporuje pouze tento algoritmus podepisování, můžete tuto možnost vybrat v rozevíracím seznamu **Algoritmus podepisování.** Azure AD pak podepíše odpověď SAML pomocí algoritmu SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Změna možností podepisování certifikátů a algoritmu podpisu

Chcete-li změnit možnosti podepisování certifikátů SAML aplikace a algoritmu s podpisem certifikátu, vyberte dotyčnou aplikaci:

1. Na [portálu Azure Active Directory](https://aad.portal.azure.com/)se přihlaste ke svému účtu. Zobrazí se stránka **Centra pro správu Služby Azure Active Directory.**
1. V levém podokně vyberte **Podnikové aplikace**. Zobrazí se seznam podnikových aplikací ve vašem účtu.
1. Vyberte aplikaci. Zobrazí se stránka s přehledem aplikace.

   ![Příklad: Stránka s přehledem aplikací](./media/certificate-signing-options/application-overview-page.png)

Dále změňte možnosti podepisování certifikátů v tokenu SAML pro tuto aplikaci:

1. V levém podokně stránky přehledu aplikace vyberte **Možnost Jednotné přihlašování**.
1. Pokud se zobrazí stránka **Nastavit jednotné přihlašování pomocí saml – náhled,** přejděte ke kroku 5.
1. Pokud se stránka **Vybrat metodu jednotného přihlašování** nezobrazí, vyberte **Změnit režimy jednotného přihlašování,** aby se tato stránka zobrazila.
1. Na stránce **Vybrat metodu jednotného přihlašování** vyberte **možnost SAML,** pokud je k dispozici. (Pokud **SAML** není k dispozici, aplikace nepodporuje SAML a můžete ignorovat zbytek tohoto postupu a článku.)
1. Na stránce **Nastavit jednotné přihlašování pomocí saml – náhled** najděte záhlaví **podpisového certifikátu SAML** a vyberte ikonu **Upravit** (tužku). Zobrazí se stránka **podpisového certifikátu SAML.**

   ![Příklad: Stránka podpisového certifikátu SAML](./media/certificate-signing-options/saml-signing-page.png)

1. V rozevíracím seznamu **Možnost podepisování** zvolte **Podepsat odpověď SAML**, **Podepsat kontrolní výraz SAML**nebo **Podepsat odpověď a kontrolní výraz SAML**. Popisy těchto možností se zobrazí dříve v tomto článku v [možnostech podepisování certifikátů](#certificate-signing-options).
1. V rozevíracím seznamu **Algoritmus podepisování** zvolte **SHA-1** nebo **SHA-256**. Popisy těchto možností se zobrazí dříve v tomto článku v části [Algoritmy podepisování certifikátů.](#certificate-signing-algorithms)
1. Pokud jste se svými volbami spokojeni, vyberte **Uložit a** použijte nové nastavení podpisového certifikátu SAML. V opačném případě vyberte **x** zahodit změny.

## <a name="next-steps"></a>Další kroky

* [Konfigurace jednotného přihlášení k aplikacím, které nejsou v Galerii aplikací Služby Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Poradce připotíží k jednotnému přihlašování na saml](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
