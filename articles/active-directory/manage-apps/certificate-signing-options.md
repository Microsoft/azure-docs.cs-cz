---
title: Pokročilé možnosti v tokenu SAML pro předběžně integrované aplikace v Azure Active Directory podepisování certifikátů | Dokumentace Microsoftu
description: Zjistěte, jak použít pokročilý certifikát Podepisování možnosti v tokenu SAML pro předběžně integrované aplikace v Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a5548e7a5f60d9882fdfb4fb6eb777ab993e121
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915990"
---
# <a name="advanced-certificate-signing-options-in-the-saml-token-for-gallery-apps-in-azure-active-directory"></a>Rozšířené možnosti v tokenu SAML pro aplikace z Galerie v Azure Active Directory podepsání certifikátu

Azure Active Directory (Azure AD) ještě dnes podporuje tisíce předem integrovaných aplikací v galerii aplikací Azure Active Directory. Více než 500 aplikace podporují jednotné přihlašování s použitím [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) 2.0, protokol, jako například [NetSuite](https://azuremarketplace.microsoft.com/marketplace/apps/aad.netsuite) aplikace. Když zákazník se ověřuje aplikace prostřednictvím služby Azure AD pomocí SAML, Azure AD odešle token do aplikace (přes HTTP POST). Aplikace pak ověří a použije token pro přihlášení zákazníků místo vás vyzve k zadání uživatelského jména a hesla. Tyto tokeny SAML jsou podepsány pomocí jedinečný certifikát, který je generován ve službě Azure AD a konkrétní standardních algoritmů.

Azure AD používá některé výchozí nastavení pro aplikace Galerie. Výchozí hodnoty nastavení jsou podle požadavků vaší aplikace.

Ve službě Azure AD můžete nastavit certifikát možnosti podpisu a podpisový algoritmus.

## <a name="certificate-signing-options"></a>Možnosti podpisu certifikátů

Azure AD podporuje tři možnosti podpisový certifikát:

* **Podepsat kontrolní výraz SAML**. Toto výchozí nastavení je pro většinu z Galerie aplikací. Pokud vyberete tuto možnost, Azure AD jako zprostředkovatele Identity (IdP) podepíše kontrolní výraz SAML a certifikát se [X.509](https://wikipedia.org/wiki/X.509) certifikát aplikace.

* **Podepsat odpověď SAML**. Pokud vyberete tuto možnost, Azure AD jako identity podepíše odpověď SAML s certifikátem X.509 aplikace.

* **Podepsat odpověď SAML a kontrolní výraz**. Pokud vyberete tuto možnost, Azure AD jako identity podepíše celý tokenu SAML s certifikátem X.509 aplikace.

## <a name="certificate-signing-algorithms"></a>Algoritmy podepsání certifikátu

Azure AD podporuje dva podpisové algoritmy nebo zabezpečené hashovacích algoritmů (SHA), podepsat odpověď SAML:

* **SHA-256**. Azure AD používá tuto výchozí algoritmus podepsat odpověď SAML. Je nejnovější algoritmus a je bezpečnější než SHA-1. Většina aplikací podporuje algoritmus SHA-256. Pokud aplikace podporuje pouze SHA-1 jako podpisový algoritmus, můžete ji změnit. Jinak doporučujeme použít algoritmus SHA-256 k podepisování odpověď SAML.

* **SHA-1**. Tento algoritmus je starší a je považován za méně bezpečné než algoritmus SHA-256. Pokud aplikace podporuje pouze tento podpisový algoritmus, můžete vybrat tuto možnost v **podpisový algoritmus** rozevíracího seznamu. Azure AD pak podepíše odpověď SAML pomocí algoritmu SHA-1.

## <a name="change-the-certificate-signing-options-and-certificate-signing-algorithm"></a>Změnit možnosti podepsání certifikátu a certifikát podpisový algoritmus

Chcete-li změnit možnosti podpisu certifikát SAML aplikace a podpisový algoritmus, vyberte dané žádosti:

1. V [portálu Azure Active Directory](https://aad.portal.azure.com/), přihlaste se ke svému účtu. **Centra pro správu Azure Active Directory** se zobrazí stránka.
1. V levém podokně vyberte **Podnikové aplikace**. Zobrazí se seznam podnikových aplikací ve vašem účtu.
1. Vyberte aplikaci. Zobrazí se stránka s přehledem pro aplikaci.

   ![Stránka Přehled aplikace](./media/certificate-signing-options/application-overview-page.png)

V dalším kroku změňte možnosti v tokenu SAML pro příslušnou aplikaci podepsání certifikátu:

1. V levém podokně na stránce Přehled aplikace vyberte **jednotného přihlašování**.

2. Pokud **nastavte si jednotné přihlašování pomocí SAML - Preview** se zobrazí stránka, přejděte ke kroku 5.

3. Pokud **vybrat jedinou metodu přihlašování** stránka nezobrazuje, vyberte **změnit režim přihlašování** k zobrazení této stránky.

4. V **vybrat jedinou metodu přihlašování** stránce **SAML** Pokud je k dispozici. (Pokud **SAML** není k dispozici, aplikace nepodporuje SAML a zbývající část tohoto postupu a článek může ignorovat.)

5. V **nastavte si jednotné přihlašování pomocí SAML - Preview** stránky, vyhledejte **podpisový certifikát SAML** nadpis a vyberte **upravit** ikonu (tužky). **Podpisový certifikát SAML** se zobrazí stránka.

   ![Stránka podepisování SAML](./media/certificate-signing-options/saml-signing-page.png)

6. V **podepisování možnost** rozevíracím seznamu klikněte na položku **odpověď přihlašování SAML**, **kontrolní výraz SAML přihlašování**, nebo **přihlašování SAML odpověď a kontrolní výraz**. Popis těchto možností se zobrazí výše v tomto článku v [certifikát možnosti podpisu](#certificate-signing-options).

7. V **podpisový algoritmus** rozevíracím seznamu klikněte na položku **SHA-1** nebo **SHA-256**. Popis těchto možností se zobrazí výše v tomto článku v [certifikát Podepisování algoritmy](#certificate-signing-algorithms) oddílu.

8. Pokud jste spokojeni s vaší volby, vyberte **Uložit** použití nového podpisového nastavení certifikátu SAML. Další možností je vybrat **X** chcete zahodit změny.

## <a name="next-steps"></a>Další postup

* [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](configure-federated-single-sign-on-non-gallery-applications.md)
* [Řešení potíží s založené na SAML jednotného přihlašování](../develop/howto-v1-debug-saml-sso-issues.md)
