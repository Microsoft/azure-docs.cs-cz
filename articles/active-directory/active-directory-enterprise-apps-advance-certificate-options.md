---
title: Podepsání možnosti v tokenu SAML pro předběžně integrované aplikace v Azure Active Directory rozšířené certifikátu | Microsoft Docs
description: Další informace o použití pokročilý certifikát Podepisování možnosti v tokenu SAML pro předběžně integrované aplikace v Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: cec377ad92e1491413f9d3ebc953abb154b83f01
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Rozšířené možnosti v tokenu SAML pro galerii aplikací v Azure Active Directory podepsání certifikátu
Dnes Azure Active Directory (Azure AD) podporuje tisícům předem integrovaných aplikací v galerii aplikací Azure Active Directory. Tato hodnota zahrnuje více než 500 aplikace, které podporují jednotné přihlašování pomocí protokolu SAML 2.0. Když se uživatel přihlásí k aplikaci prostřednictvím služby Azure AD pomocí SAML, Azure AD odešle token do aplikace (přes HTTP POST). Aplikace pak ověří a použije token k přihlášení uživatele místo výzvy k zadání uživatelského jména a hesla. Tyto tokeny SAML jsou podepsané jedinečný certifikát, který je vytvořen ve službě Azure AD a konkrétní standardních algoritmů.

Azure AD se využívá část výchozí nastavení pro aplikace, galerie. Výchozí hodnoty jsou nastaveny podle požadavky na aplikace.

Azure AD podporuje pokročilý certifikát Podepisování nastavení. Pokud chcete vybrat tyto možnosti, nejprve vyberte **zobrazit upřesňující nastavení podpisový certifikát** políčko:

![Zobrazit upřesňující nastavení podepisování certifikátu](./media/active-directory-enterprise-apps-advance-certificate-options/saml-advance-certificate.png)

Po výběru tohoto zaškrtávacího políčka, můžete nastavit možnosti podepsání certifikátu a certifikát podpisový algoritmus.

## <a name="certificate-signing-options"></a>Možnosti podepsání certifikátu

Azure AD podporuje tři možnosti podpisový certifikát:

* **Podepsat kontrolního výrazu SAML**. Toto výchozí nastavení je pro většinu aplikací galerie. Pokud je vybraná tato možnost, Azure AD jako IdP podepisuje kontrolního výrazu SAML a certifikát s X509 certifikát aplikace. Také používá podpisový algoritmus, který je vybraný v **podpisový algoritmus** rozevíracího seznamu.

* **Podepsat odpověď SAML**. Pokud je vybraná tato možnost, Azure AD jako IdP podepisuje odpověď SAML se X509 certifikát aplikace. Také používá podpisový algoritmus, který je vybraný v **podpisový algoritmus** rozevíracího seznamu.

* **Podepisování odpověď SAML a assertion**. Pokud je vybraná tato možnost, Azure AD jako IdP podepíše celý tokenu SAML s X509 certifikát aplikace. Také používá podpisový algoritmus, který je vybraný v **podpisový algoritmus** rozevíracího seznamu.

    ![Možnosti podepsání certifikátu](./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-options.png)

## <a name="certificate-signing-algorithms"></a>Algoritmy podepsání certifikátu

Azure AD podporuje dva podpisový algoritmy k podepsání odpověď SAML:

* **ALGORITMUS SHA-256**. Azure AD tento výchozí algoritmus používá k podepisování odpověď SAML. Je nejnovější algoritmus a je považován za další bezpečné než SHA-1. Většina aplikací podporují algoritmus SHA-256. Pokud aplikace podporuje pouze SHA-1 jako podpisový algoritmus, můžete ji změnit. Jinak doporučujeme použít algoritmus SHA-256 k podepisování odpověď SAML.

    ![Podepsání algoritmus SHA-256 certifikátu](./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha256.png)

* **SHA-1**. Toto je starší algoritmus a je považován za méně bezpečné než SH-256. Pokud aplikace podporuje pouze tento podpisový algoritmus, můžete vybrat tuto možnost v **podpisový algoritmus** rozevíracího seznamu. Azure AD poté podepisuje odpověď SAML s algoritmus SHA-1.

    ![Podepsání algoritmus SHA-1 certifikátu](./media/active-directory-enterprise-apps-advance-certificate-options/saml-signing-algo-sha1.png)

## <a name="next-steps"></a>Další postup
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](active-directory-apps-index.md)
* [Konfigurovat jednotné přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Řešení potíží s na základě SAML jednotné přihlašování](develop/active-directory-saml-debugging.md)


