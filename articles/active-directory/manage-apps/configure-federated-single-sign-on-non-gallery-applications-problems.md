---
title: Problém s konfigurací federovaného jednotného přihlašování pro aplikaci mimo galerii | Microsoft Docs
description: Vyřešte některé běžné problémy, se kterými se můžete setkat při konfiguraci federovaného jednotného přihlašování k vlastní aplikaci SAML, která není uvedená v galerii aplikací Azure AD.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84a8ee8ca29046d26d200aaf853a3efe25f15768
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763563"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problém s konfigurací federovaného jednotného přihlašování pro aplikaci mimo galerii

Pokud narazíte na problém při konfiguraci aplikace. Ověřte, že jste postupovali podle všech kroků v článku [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory.](configure-federated-single-sign-on-non-gallery-applications.md)

## <a name="cant-add-another-instance-of-the-application"></a>Nejde přidat další instanci aplikace.

Chcete-li přidat druhou instanci aplikace, je nutné mít tyto možnosti:

-   Nakonfigurujte jedinečný identifikátor pro druhou instanci. Nemůžete konfigurovat stejný identifikátor, který se používá pro první instanci.

-   Nakonfigurujte jiný certifikát než ten, který se používá pro první instanci.

Pokud aplikace nepodporuje žádný z předchozích verzí, nelze nakonfigurovat druhou instanci.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Kde mám nastavit formát EntityID (uživatelský identifikátor)

Nemůžete vybrat formát EntityID (uživatelský identifikátor), který Azure AD pošle do aplikace v reakci po ověření uživatele.

Azure AD vybere formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu, který vyžádala aplikace v AuthRequest SAML. Další informace najdete v článku [protokol SAML jednotného přihlašování](../develop/single-sign-on-saml-protocol.md#authnrequest) v části NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Kde získám metadata aplikace nebo certifikát z Azure AD

Pokud chcete stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte takto:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, u které jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. Přejděte do části **podpisový certifikát SAML** a pak klikněte na **Stáhnout** sloupec hodnota. V závislosti na tom, co aplikace vyžaduje konfiguraci jednotného přihlašování, se zobrazí buď možnost stáhnout metadata XML nebo certifikát.

Azure AD neposkytuje adresu URL pro získání metadat. Metadata lze načíst pouze jako soubor XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nevím, jak přizpůsobit deklarace SAML odeslané do aplikace.

Další informace o tom, jak přizpůsobit deklarace identity atributů SAML odeslané do vaší aplikace, najdete v tématu [mapování deklarací v Azure Active Directory](../develop/active-directory-claims-mapping.md) .

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
