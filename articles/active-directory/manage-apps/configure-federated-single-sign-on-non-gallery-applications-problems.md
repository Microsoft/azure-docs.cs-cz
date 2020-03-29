---
title: Problém s konfigurací federovaného jednotného přihlašování pro aplikaci mimo galerii | Dokumenty společnosti Microsoft
description: Řešení některých běžných problémů, se kterými se můžete setkat při konfiguraci federovaného jednotného přihlášení k vlastní aplikaci SAML, která není uvedená v Galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7894bfada4d363e89f526280e2925b4f4c6180a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76711888"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problém s konfigurací federovaného jednotného přihlašování pro aplikaci mimo galerii

Pokud narazíte na problém při konfiguraci aplikace. Ověřte, zda jste postupovali podle všech kroků v článku [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory.](configure-federated-single-sign-on-non-gallery-applications.md)

## <a name="cant-add-another-instance-of-the-application"></a>Nelze přidat další instanci aplikace

Chcete-li přidat druhou instanci aplikace, musíte být schopni:

-   Nakonfigurujte jedinečný identifikátor pro druhou instanci. Nelze nakonfigurovat stejný identifikátor, který byl použit pro první instanci.

-   Nakonfigurujte jiný certifikát, než který byl použit pro první instanci.

Pokud aplikace nepodporuje žádné z předchozích, nelze nakonfigurovat druhou instanci.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Kde mám nastavit formát EntityID (Identifikátor uživatele)

Nelze vybrat EntityID (Identifikátor uživatele) formát, který Azure AD odešle do aplikace v odpovědi po ověření uživatele.

Azure AD vybere formát atributu NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu požadovaného aplikací v SAML AuthRequest. Další informace naleznete v článku [Protokol SAML jednotného přihlášení](../develop/single-sign-on-saml-protocol.md#authnrequest) v části NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Kde získám metadata nebo certifikát aplikace z Azure AD

Pokud chcete stáhnout metadata nebo certifikát aplikace z Azure AD, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální **správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na Všechny **služby** v horní části hlavní nabídky navigace vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Služby Azure Active Directory.**

4. V levé navigační nabídce Azure Active Directory klikněte na **Podnikové aplikace.**

5. Kliknutím na **Všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud zde nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace** a nastavte možnost **Zobrazit** na **všechny aplikace.**

6. Vyberte aplikaci, kterou jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte **na jednotné přihlašování** z levé navigační nabídky aplikace.

8. Přejděte do části **Podpisový certifikát SAML** a klikněte na **Stáhnout** hodnotu sloupce. V závislosti na tom, co aplikace vyžaduje konfiguraci jednotného přihlášení, se zobrazí možnost stažení xml metadat nebo certifikátu.

Azure AD neposkytuje adresu URL získat metadata. Metadata lze načíst pouze jako soubor XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Nevíte, jak přizpůsobit deklarace SAML odeslané do aplikace

Další informace najdete v tématu [Mapování deklarací identity ve službě Azure Active Directory.](../develop/active-directory-claims-mapping.md)

## <a name="next-steps"></a>Další kroky
[Správa aplikací pomocí služby Azure Active Directory](what-is-application-management.md)
