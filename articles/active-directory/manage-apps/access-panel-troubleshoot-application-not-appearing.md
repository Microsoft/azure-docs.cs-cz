---
title: Přiřazená aplikace se nezobrazuje na přístupovém panelu | Microsoft Docs
description: Řešení potíží s tím, proč se aplikace nezobrazuje na přístupovém panelu
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
ms.date: 09/09/2018
ms.author: kenwith
ms.reviwer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69f5196484b841e8f0de72ce52ae48e00963f6f5
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760991"
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Přiřazená aplikace se na přístupovém panelu nezobrazuje.

Přístupový panel je webový portál, který umožňuje uživateli s pracovním nebo školním účtem ve službě Azure Active Directory (Azure AD) zobrazovat a spouštět cloudové aplikace, kterým správce Azure AD udělil přístup k systému. Tyto aplikace jsou nakonfigurované jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurována a přiřazena uživateli nebo skupině, které je uživatel členem, aby bylo možné aplikaci zobrazit na přístupovém panelu.

Typ aplikací, které může uživatel zobrazit, spadají do následujících kategorií:

-   Aplikace Office 365

-   Aplikace Microsoftu a aplikace třetích stran nakonfigurované pomocí jednotného přihlašování založeného na federaci

-   Aplikace jednotného přihlašování založené na heslech

-   Aplikace s existujícími řešeními jednotného přihlašování

## <a name="general-issues-to-check-first"></a>Obecné problémy k první kontrole

-   Pokud se aplikace právě přidala uživateli, zkuste se znovu přihlásit a přejít na přístupový panel uživatele, aby se zjistilo, jestli se aplikace přidala.

-   Pokud byla licence pouze odebrána od uživatele nebo skupiny, které je uživatel členem této služby, může trvat dlouhou dobu v závislosti na velikosti a složitosti skupiny, aby byly provedeny změny. Před přihlášením k přístupovému panelu povolte dodatečnou dobu.

## <a name="problems-related-to-application-configuration"></a>Problémy související s konfigurací aplikace

V přístupovém panelu uživatele se aplikace nemusí zobrazovat, protože není správně nakonfigurovaná. Níže jsou uvedeny některé způsoby, jak můžete řešit problémy související s konfigurací aplikace:

-   [Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo galerii](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Jak nakonfigurovat aplikaci pro jednotné přihlašování pomocí hesla pro aplikaci Galerie Azure AD](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Jak nakonfigurovat aplikaci jednotného přihlašování k heslům pro aplikaci mimo galerii](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD

Všechny aplikace v galerii Azure AD povolené s možností podnikového jednotného přihlašování mají k dispozici podrobný kurz. Můžete získat přístup k [seznamu kurzů, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) pro podrobné pokyny.

Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   [Přidání aplikace z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace hodnot metadat aplikace v Azure AD (přihlašovací adresa URL, identifikátor, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat a certifikátů Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurace hodnot metadat Azure AD v aplikaci (přihlašovací adresa URL, vydavatel, adresa URL pro odhlášení a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce** .

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  Do textového pole **Zadejte název** z části **Přidat z Galerie** zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název z textového pole **název** .

9.  Kliknutím na tlačítko **Přidat** přidejte aplikaci.

Po krátké době se bude moci zobrazit podokno konfigurace aplikace.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurace jednotného přihlašování pro aplikaci z Galerie Azure AD

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. V rozevíracím seznamu **režim** vyberte možnost **přihlašování založené na SAML** .

9. Zadejte požadované hodnoty do pole **doména a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Chcete-li nakonfigurovat aplikaci jako jednotné přihlašování iniciované pomocí nástroje SP, přihlašovací adresa URL je požadovaná hodnota. U některých aplikací je identifikátor také povinná hodnota.

   2. Pokud chcete nakonfigurovat aplikaci jako IdP jednotného přihlašování, adresa URL odpovědi je požadovaná hodnota. U některých aplikací je identifikátor také povinná hodnota.

10. **Volitelné:** Pokud chcete zobrazit nepotřebné hodnoty, klikněte na **Zobrazit upřesňující nastavení adresy URL** .

11. V části **atributy uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** .

12. **Volitelné:** kliknutím na **Zobrazit a upravit všechny ostatní atributy uživatele** upravíte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

    2. klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

13. Kliknutím na **Konfigurovat &lt; název &gt; aplikace** získáte přístup k dokumentaci týkající se konfigurace jednotného přihlašování v aplikaci. K nastavení jednotného přihlašování v aplikaci máte také k dispozici adresy URL a certifikát metadat.

14. Kliknutím na **Uložit** uložte konfiguraci.

15. Přiřaďte uživatele k aplikaci.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.

Chcete-li vybrat identifikátor uživatele nebo přidat atributy uživatele, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, u které jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. V části **atributy uživatele** vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** . Aby bylo možné uživatele ověřit, musí vybraná možnost odpovídat očekávané hodnotě v aplikaci.

   >[!NOTE] 
   >Azure AD vybere formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu, který vyžádala aplikace v AuthRequest SAML. Další informace najdete v článku [protokol SAML jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy.
   >
   >

9. Pokud chcete přidat atributy uživatele, klikněte na **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

   2. klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátů Azure AD

Pokud chcete stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte podle následujících kroků:

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo galerii

Chcete-li nakonfigurovat aplikaci mimo galerii, je nutné mít službu Azure AD Premium a aplikace podporuje SAML 2,0. Další informace o verzích Azure AD najdete na stránce [ceny Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Konfigurace hodnot metadat aplikace v Azure AD (přihlašovací adresa URL, identifikátor, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat a certifikátů Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurace hodnot metadat Azure AD v aplikaci (přihlašovací adresa URL, vydavatel, adresa URL pro odhlášení a certifikát)](#configure-the-application-for-password-single-sign-on-1)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurace hodnot metadat aplikace v Azure AD (přihlašovací adresa URL, identifikátor, adresa URL odpovědi)

Pokud chcete nakonfigurovat jednotné přihlašování pro aplikaci, která není v galerii Azure AD, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6. v části **Přidat vlastní aplikaci** klikněte na **jinou aplikaci než Galerie** .

7. Do textového pole **název** zadejte název aplikace.

8. Kliknutím na tlačítko **Přidat** přidejte aplikaci.

9. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

10. V rozevíracím seznamu **režim** vyberte možnost **přihlašování založené na SAML** .

11. Zadejte požadované hodnoty do pole **doména a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

    1. Pokud chcete aplikaci nakonfigurovat jako IdP jednotné přihlašování, zadejte adresu URL odpovědi a identifikátor.

    2.  **Volitelné:** Chcete-li nakonfigurovat aplikaci jako jednotné přihlašování iniciované pomocí nástroje SP, přihlašovací adresa URL je požadovaná hodnota.

12. V části **atributy uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** .

13. **Volitelné:** kliknutím na **Zobrazit a upravit všechny ostatní atributy uživatele** upravíte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

    2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

14. Kliknutím na **Konfigurovat &lt; název &gt; aplikace** získáte přístup k dokumentaci týkající se konfigurace jednotného přihlašování v aplikaci. K dispozici jsou také adresy URL a certifikáty Azure AD, které aplikace vyžaduje.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.

Chcete-li vybrat identifikátor uživatele nebo přidat atributy uživatele, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, u které jste nakonfigurovali jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. V části **atributy uživatele** vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** . Aby bylo možné uživatele ověřit, musí vybraná možnost odpovídat očekávané hodnotě v aplikaci.

   >[!NOTE] 
   >Azure AD vybere formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu, který vyžádala aplikace v AuthRequest SAML. Další informace najdete v článku [protokol SAML jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy.
   >
   >

9. Pokud chcete přidat atributy uživatele, klikněte na **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

   2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátů Azure AD

Pokud chcete stáhnout metadata aplikace nebo certifikát z Azure AD, postupujte podle následujících kroků:

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci Galerie Azure AD

Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   [Přidání aplikace z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace aplikace pro jednotné přihlašování k heslům](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce** .

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  Do textového pole **Zadejte název** z části **Přidat z Galerie** zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název z textového pole **název** .

9.  Kliknutím na tlačítko **Přidat** přidejte aplikaci.

Po krátké době uvidíte podokno konfigurace aplikace.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. Vyberte možnost režim **přihlašování založené na heslech.**

9. [Přiřaďte uživatele k aplikaci](#how-to-assign-a-user-to-an-application-directly).

10. Kromě toho můžete přihlašovací údaje jménem uživatele zadat také tak, že vyberete řádky uživatelů a kliknete na **Aktualizovat přihlašovací údaje** a zadáte uživatelské jméno a heslo jménem uživatele. V opačném případě se uživatelům zobrazí výzva k zadání přihlašovacích údajů sami při spuštění.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci mimo galerii

Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   [Přidat aplikaci mimo galerii](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro jednotné přihlašování k heslům](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo galerii

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce**.

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  klikněte na **aplikaci mimo galerii.**

7.  Do textového pole **název** zadejte název vaší aplikace. Vyberte **Přidat.**

Po krátké době se bude moci zobrazit podokno konfigurace aplikace.

#### <a name="configure-the-application-for-password-single-sign-on"></a><a name="configure-the-application-for-password-single-sign-on-1"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

    1.  Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6.  Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8.  Vyberte možnost režim **přihlašování založené na heslech.**

9.  Zadejte **přihlašovací adresu URL**. Toto je adresa URL, kam uživatelé zadají své uživatelské jméno a heslo, aby se mohli přihlásit. Ujistěte se, že jsou přihlašovací pole na adrese URL viditelná.

10. [Přiřaďte uživatele k aplikaci](#how-to-assign-a-user-to-an-application-directly).

11. Kromě toho můžete přihlašovací údaje jménem uživatele zadat také tak, že vyberete řádky uživatelů a kliknete na **Aktualizovat přihlašovací údaje** a zadáte uživatelské jméno a heslo jménem uživatele. V opačném případě se uživatelům zobrazí výzva k zadání přihlašovacích údajů sami při spuštění.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problémy související s přiřazováním aplikací uživatelům

Uživatel nemusí zobrazit aplikaci na svém přístupovém panelu, protože není přiřazen k aplikaci. Níže jsou uvedeny některé způsoby, jak ověřit:

-   [Zjistit, jestli je uživatel přiřazený k aplikaci](#check-if-a-user-is-assigned-to-the-application)

-   [Postup přiřazení uživatele k aplikaci přímo](#how-to-assign-a-user-to-an-application-directly)

-   [Zjistit, jestli je uživatel přiřazený k licenci týkající se aplikace](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Přiřazení licence uživateli](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Zjistit, jestli je uživatel přiřazený k aplikaci

Pokud chcete zjistit, jestli je uživatel přiřazený k aplikaci, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

6. **Vyhledejte** název příslušné aplikace.

7. klikněte na **Uživatelé a skupiny**.

8. Zkontrolujte, jestli je váš uživatel přiřazený k aplikaci.

   * Pokud není, postupujte podle kroků v části "jak přiřadit uživatele k aplikaci přímo".

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Postup přiřazení uživatele k aplikaci přímo

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce**.

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ze které chcete uživatele přiřadit.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9. v podokně **Přidat přiřazení** klikněte na selektor **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte jméno a **příjmení** nebo **e-mailovou adresu** uživatele, kterého se zajímáte o přiřazení.

11. Pokud chcete zobrazit **zaškrtávací políčko**, najeďte myší na **uživatele** v seznamu. Kliknutím na zaškrtávací políčko vedle profilové fotografie nebo loga uživatele přidáte uživatele do **vybraného** seznamu.

12. **Volitelné:** Pokud chcete **Přidat více než jednoho uživatele**, zadejte do vyhledávacího pole **jméno nebo e-mailová** adresa další **jméno** nebo **e-mailovou adresu** a kliknutím na zaškrtávací políčko přidejte tohoto uživatele do **vybraného** seznamu.

13. Po dokončení výběru uživatelů klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** kliknutím na výběr **role** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Kliknutím na tlačítko **přiřadit** aplikaci přiřadíte vybraným uživatelům.

Po krátké době uživatelé, které jste vybrali, budou moci spouštět tyto aplikace na přístupovém panelu.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Zjistit, jestli se uživatel nachází v licenci týkající se aplikace

Chcete-li ověřit přiřazené licence uživatele, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **Uživatelé a skupiny** .

5. klikněte na **Všichni uživatelé**.

6. **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7. Kliknutím na **licence** zobrazíte licence, které uživatel aktuálně přiřadil.

   * Pokud je uživatel přiřazen k licenci Office, umožní to, aby se na přístupovém panelu uživatele zobrazovaly aplikace Office pro první stranu.

### <a name="how-to-assign-a-user-a-license"></a>Přiřazení licence uživateli 

Pokud chcete přiřadit licenci uživateli, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **Všichni uživatelé**.

6.  **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  Kliknutím na **licence** zobrazíte licence, které uživatel aktuálně přiřadil.

8.  klikněte na tlačítko **přiřadit** .

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** kliknutím na položku **možnosti přiřazení** podrobně přiřaďte produkty. Po dokončení klikněte na **OK** .

11. Pro přiřazení těchto licencí tomuto uživateli klikněte na tlačítko **přiřadit** .

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problémy související s přiřazováním aplikací do skupin

Uživatel může zobrazit aplikaci na svém přístupovém panelu, protože je součástí skupiny, které byla aplikace přiřazena. Níže jsou uvedeny některé způsoby, jak ověřit:

-   [Ověřit členství uživatele ve skupinách](#check-a-users-group-memberships)

-   [Postup přiřazení aplikace ke skupině přímo](#how-to-assign-an-application-to-a-group-directly)

-   [Zkontroluje, jestli je uživatel členem skupiny přiřazené k licenci.](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Přiřazení licence ke skupině](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Ověřit členství uživatele ve skupinách

Pokud chcete ověřit členství ve skupině, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **Uživatelé a skupiny** .

5. klikněte na **Všichni uživatelé**.

6. **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7. klikněte na **skupiny**.

8. Zkontrolujte, jestli je váš uživatel součástí skupiny přiřazené k aplikaci.

   * Pokud chcete uživatele odebrat ze skupiny, **klikněte na řádek** skupiny a vyberte Odstranit.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Postup přiřazení aplikace ke skupině přímo

Chcete-li přiřadit jednu nebo více skupin k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce**.

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Ze seznamu vyberte aplikaci, ze které chcete uživatele přiřadit.

7. Po načtení aplikace klikněte na **Uživatelé a skupiny** v nabídce navigace na levé straně aplikace.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9. v podokně **Přidat přiřazení** klikněte na selektor **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte **úplný název skupiny** , které vás zajímá.

11. Pokud chcete zobrazit **zaškrtávací políčko**, najeďte myší na **skupinu** v seznamu. Kliknutím na zaškrtávací políčko vedle profilové fotografie nebo loga skupiny přidáte uživatele do **vybraného** seznamu.

12. **Volitelné:** Chcete-li **Přidat více než jednu skupinu**, zadejte do pole **Hledat podle jména nebo e-mailové adresy** další **název celé skupiny** a kliknutím na zaškrtávací políčko přidejte tuto skupinu do **vybraného** seznamu.

13. Po dokončení výběru skupin klikněte na tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** Pokud chcete vybrat roli, kterou chcete přiřadit vybraným skupinám, klikněte v podokně **Přidat přiřazení** na výběr **role vybrat** .

15. Kliknutím na tlačítko **přiřadit** aplikaci přiřadíte k vybraným skupinám.

Po krátké době uživatelé, které jste vybrali, budou moci spouštět tyto aplikace na přístupovém panelu.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Zkontroluje, jestli je uživatel členem skupiny přiřazené k licenci.

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce klikněte na **Uživatelé a skupiny** .

5. klikněte na **Všichni uživatelé**.

6. **Vyhledejte** uživatele, kterého vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7. klikněte na **skupiny**.

8. klikněte na řádek konkrétní skupiny.

9. Kliknutím na **licence** zobrazíte licence, ke kterým se skupina přiřadí.

   * Pokud je skupina přiřazená k licenci na Office, může se stát, že se některé aplikace Office, které se na přístupovém panelu zobrazují, zobrazí na uživatelském přístupovém panelu.

### <a name="how-to-assign-a-license-to-a-group"></a>Přiřazení licence ke skupině

Pokud chcete přiřadit licenci ke skupině, postupujte podle následujících kroků:

1.  Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce klikněte na **Uživatelé a skupiny** .

5.  klikněte na **všechny skupiny**.

6.  **Vyhledejte** skupinu, kterou vás zajímá, a **klikněte na řádek** , který chcete vybrat.

7.  Kliknutím na **licence** zobrazíte licence, které skupina aktuálně přiřadila.

8.  klikněte na tlačítko **přiřadit** .

9.  Vyberte **jeden nebo více produktů** ze seznamu dostupných produktů.

10. **Volitelné** kliknutím na položku **možnosti přiřazení** podrobně přiřaďte produkty. Po dokončení klikněte na **OK** .

11. Kliknutím na tlačítko **přiřadit** jim přiřadíte tyto licence k této skupině. V závislosti na velikosti a složitosti skupiny to může trvat dlouhou dobu.

>[!NOTE]
>Chcete-li to provést rychleji, zvažte možnost dočasného přiřazení licence přímo uživateli. 
>
>

## <a name="next-steps"></a>Další kroky
[Přidání nových uživatelů do služby Azure Active Directory](./../fundamentals/add-users-azure-active-directory.md)

