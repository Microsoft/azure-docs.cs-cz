---
title: Pokročilé možnosti podepisování certifikátů tokenu SAML pro aplikace Azure AD
description: Naučte se používat rozšířené možnosti podepisování certifikátů v tokenu SAML pro předem integrované aplikace v Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bb58f59b3796311f52d78ef87c8918f9888c152
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377866"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Rozšířené možnosti podepisování certifikátů v tokenu SAML pro aplikace Galerie v Azure Active Directory

Dnešní Azure Active Directory (Azure AD) podporuje v galerii aplikací Azure Active Directory tisíce předem integrovaných aplikací. Více než 500 aplikací podporuje jednotné přihlašování pomocí protokolu [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2,0, jako je například aplikace [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) . Když se zákazník v rámci služby Azure AD ověřuje pomocí SAML, služba Azure AD pošle token do aplikace (prostřednictvím HTTP POST). Aplikace potom ověří a použije token pro přihlášení zákazníka, nikoli zobrazení výzvy k zadání uživatelského jména a hesla. Tyto tokeny SAML jsou podepsané jedinečným certifikátem vygenerovaným v Azure AD a konkrétními standardními algoritmy.

Azure AD používá pro aplikace v galerii některé výchozí nastavení. Výchozí hodnoty jsou nastavené na základě požadavků aplikace.

Ve službě Azure AD můžete nastavit možnosti podepisování certifikátů a algoritmus podepisování certifikátů.

## <a name="certificate-signing-options"></a>Možnosti podpisu certifikátů

Azure AD podporuje tři možnosti podepisování certifikátů:

* **Podepsat kontrolní výraz SAML**. Tato výchozí možnost je nastavena pro většinu aplikací galerie. Pokud vyberete tuto možnost, Azure AD jako zprostředkovatel identity (IdP) podepíše kontrolní výraz a certifikát SAML s certifikátem [X. 509](https://wikipedia.org/wiki/X.509) aplikace.

* **Podepsat odpověď SAML**. Pokud vyberete tuto možnost, Azure AD jako IdP podepíše odpověď SAML pomocí certifikátu X. 509 aplikace.

* **Podepsat odpověď a kontrolní výraz SAML**. Pokud vyberete tuto možnost, Azure AD jako IdP podepíše celý token SAML s certifikátem X. 509 aplikace.

## <a name="certificate-signing-algorithms"></a>Algoritmy podepisování certifikátů

Azure AD podporuje dva podpisové algoritmy nebo algoritmy SHA (Secure Hash Algorithm) pro podepsání odpovědi SAML:

* **SHA-256**. Azure AD používá tento výchozí algoritmus k podepsání odpovědi SAML. Je to nejnovější algoritmus a je bezpečnější než SHA-1. Většina aplikací podporuje algoritmus SHA-256. Pokud aplikace jako podpisový algoritmus podporuje pouze SHA-1, můžete ho změnit. V opačném případě doporučujeme použít algoritmus SHA-256 pro podepsání odpovědi SAML.

* **SHA-1**. Tento algoritmus je starší a je považován za méně bezpečný než SHA-256. Pokud aplikace podporuje pouze tento podpisový algoritmus, můžete tuto možnost vybrat v rozevíracím seznamu **algoritmus podepisování** . Služba Azure AD pak podepíše odpověď SAML pomocí algoritmu SHA-1.

## <a name="change-certificate-signing-options-and-signing-algorithm"></a>Změna možností podepsání certifikátu a podpisového algoritmu

Pokud chcete změnit možnosti podepisování certifikátů SAML aplikace a algoritmus podepisování certifikátů, vyberte příslušnou aplikaci:

1. Na [portálu Azure Active Directory](https://aad.portal.azure.com/)se přihlaste ke svému účtu. Zobrazí se stránka **Centrum pro správu Azure Active Directory** .
1. V levém podokně vyberte **Podnikové aplikace**. Zobrazí se seznam podnikových aplikací ve vašem účtu.
1. Vyberte aplikaci. Zobrazí se stránka s přehledem pro aplikaci.

   ![Příklad: Stránka s přehledem aplikace](./media/certificate-signing-options/application-overview-page.png)

V dalším kroku změňte možnosti podepsání certifikátu v tokenu SAML pro tuto aplikaci:

1. V levém podokně stránky přehled aplikace vyberte **jednotné přihlašování**.
1. Pokud se zobrazí stránka **nastavit jeden Sign-On se zobrazením SAML-Preview** , pokračujte krokem 5.
1. Pokud se nezobrazí stránka pro **Výběr metody jednotného přihlašování** , vyberte možnost **změnit režimy jednotného přihlašování** a tuto stránku zobrazte.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML** , pokud je k dispozici. (Pokud není **SAML** k dispozici, aplikace nepodporuje SAML, ale můžete zbývající část tohoto postupu a článku ignorovat.)
1. Na stránce **nastavit jeden Sign-On se službou SAML-Preview** Najděte hlavičku **podpisového certifikátu SAML** a vyberte ikonu pro **Úpravy** (tužka). Zobrazí se stránka **podpisový certifikát SAML** .

   ![Příklad: stránka podpisového certifikátu SAML](./media/certificate-signing-options/saml-signing-page.png)

1. V rozevíracím seznamu **možnost podepisování vyberte možnost** **podepsat odpověď SAML**, **podepsat kontrolní výraz SAML** nebo **podepsat odpověď a kontrolní výraz SAML**. Popisy těchto možností se zobrazí výše v tomto článku v části [možnosti podepisování certifikátu](#certificate-signing-options).
1. V rozevíracím seznamu **algoritmus podepisování** vyberte **SHA-1** nebo **SHA-256**. Popisy těchto možností se zobrazí výše v tomto článku v části [algoritmy podepisování certifikátů](#certificate-signing-algorithms) .
1. Pokud jste s vašimi možnostmi spokojeni, vyberte **Uložit** a použijte tak nové nastavení podpisového certifikátu SAML. V opačném případě vyberte **X** , chcete-li změny zahodit.

## <a name="next-steps"></a>Další kroky

* [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](./configure-saml-single-sign-on.md)
* [Řešení potíží s jednotným přihlašováním založeným na SAML](./debug-saml-sso-issues.md)