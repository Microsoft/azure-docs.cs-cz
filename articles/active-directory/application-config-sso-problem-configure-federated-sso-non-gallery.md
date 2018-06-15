---
title: Problém konfigurace federované jednotné přihlašování pro aplikace bez Galerie | Microsoft Docs
description: Adresa některé běžné problémy, ke kterým může dojít při konfiguraci federované jednotné přihlašování k vaší vlastní aplikaci SAML, která není uvedena v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 1990f6595be86d7f3a50f6c764a855aedf2cbcbf
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
ms.locfileid: "34052554"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problém konfigurace federované jednotné přihlašování pro aplikace bez Galerie

Pokud dojde k potížím při konfiguraci aplikace. Ověřte jste postupovali podle pokynů v článku [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Nelze přidat jiná instance aplikace

Přidat druhou instanci aplikace, musíte být schopni:

-   Jedinečný identifikátor pro druhou instanci nakonfigurujte. Nelze nakonfigurovat pro první instanci používá stejný identifikátor.

-   Nakonfigurujte jiný certifikát než ten, který používá pro první instanci.

Pokud aplikace nepodporuje žádné z předchozí, nelze nakonfigurovat druhou instanci.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Kde lze nastavit formát EntityID (identifikátor uživatele)

Nelze vybrat formát EntityID (identifikátor uživatele), který odešle aplikaci v odpovědi po ověření uživatele Azure AD.

Azure AD vybere formát požadovanou aplikaci v SAML AuthRequest nebo formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty. Další informace najdete v článku [protokolu SAML přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) části NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Kde získat metadata aplikace nebo certifikát z Azure AD

Chcete-li stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte takto:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

   * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, které jste nakonfigurovali jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Přejděte na **SAML podpisový certifikát** části a pak klikněte na **Stáhnout** hodnota sloupce. V závislosti na tom, jaké aplikace vyžaduje konfiguraci jednotné přihlašování uvidíte buď možnost Stáhnout soubor XML s metadaty nebo certifikát.

Azure AD neposkytuje adresu URL se získat metadata. Metadata mohou být načteny pouze jako soubor XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nemusíte vědět, jak přizpůsobit SAML deklarace identity odeslat do aplikace

Naučte se přizpůsobovat deklarací identity atributu SAML odeslaných do vaší aplikace, najdete v tématu [deklarací mapování v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Další informace.

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](manage-apps/what-is-application-management.md)
