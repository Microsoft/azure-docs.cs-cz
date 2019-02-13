---
title: Potíže s konfigurací federované jednotné přihlašování pro aplikaci mimo Galerii | Dokumentace Microsoftu
description: Vyřešit některé běžné problémy, na které můžete narazit při konfigurování federovaného jednotného přihlašování do vlastní aplikace SAML, která není uvedená v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c3e0716d24681dbbdf961bbd6df469a1b77f92
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188575"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Potíže s konfigurací federované jednotné přihlašování pro aplikaci mimo Galerii

Pokud narazíte na problém při konfiguraci aplikace. Ověření, jste provedli všechny kroky v následujícím článku [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Nelze přidat další instanci aplikace

Přidání druhé instance aplikace, musíte být schopni:

-   Nakonfigurujte jedinečný identifikátor pro druhou instanci. Nelze konfigurovat stejný identifikátor pro první instanci.

-   Nakonfigurujte jiným certifikátem, než jaký se používá pro první instanci.

Pokud aplikace nepodporuje některé z předchozích, nelze nakonfigurovat druhou instanci.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Kde můžu nastavit formát EntityID (identifikátor uživatele)

Nelze vybrat formát EntityID (identifikátor uživatele), který Azure AD se odesílá do aplikace v odpovědi po ověření uživatele.

Azure AD vybere ve formátu NameID atributu (identifikátor uživatele), na základě hodnoty vybrané nebo formátu požadovaná aplikací v SAML AuthRequest. Další informace najdete v článku [protokol jednotné přihlašování SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Kde získat metadata aplikace nebo certifikát z Azure AD

Chcete-li stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte takto:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikace, které jste nakonfigurovali jednotného přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

8.  Přejděte na **podpisový certifikát SAML** části a pak klikněte na **Stáhnout** hodnota ve sloupci. V závislosti na tom, jaké aplikace vyžaduje konfiguraci jednotného přihlašování se zobrazí buď možnost stahovat kód XML metadat nebo certifikát.

Azure AD neposkytuje adresu URL získat metadata. Metadata se dá načíst jenom jako soubor XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nevím, jak k přizpůsobení deklarací identity SAML odesílá se do aplikace

Informace o přizpůsobení deklarací identity atributu SAML odeslán do vaší aplikace najdete v tématu [mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Další informace.

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
