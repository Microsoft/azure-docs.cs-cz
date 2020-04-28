---
title: Problémy s přihlášením k aplikaci z přístupového panelu | Microsoft Docs
description: Řešení potíží s přístupem k aplikaci z přístupového panelu Microsoft Azure AD na adrese myapps.microsoft.com
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
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "74082146"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problémy s přihlášením k aplikaci z přístupového panelu

Přístupový panel je webový portál, který umožňuje uživateli s pracovním nebo školním účtem ve službě Azure Active Directory (Azure AD) zobrazovat a spouštět cloudové aplikace, kterým správce Azure AD udělil přístup k systému. 

Tyto aplikace jsou nakonfigurované jménem uživatele na portálu Azure AD. Aplikace musí být správně nakonfigurována a přiřazena uživateli nebo skupině, které je uživatel členem, aby bylo možné aplikaci zobrazit na přístupovém panelu.

Typ aplikací, které může uživatel zobrazit, spadají do následujících kategorií:

-   Aplikace Office 365

-   Aplikace Microsoftu a aplikace třetích stran nakonfigurované pomocí jednotného přihlašování založeného na federaci

-   Aplikace jednotného přihlašování založené na heslech

-   Aplikace s existujícími řešeními jednotného přihlašování

## <a name="general-issues-to-check-first"></a>Obecné problémy k první kontrole

-   Ujistěte se, že používáte **prohlížeč** , který splňuje minimální požadavky na přístupovém panelu.

-   Ujistěte se, že prohlížeč uživatele přidal adresu URL aplikace do jejích **důvěryhodných webů**.

-   Přesvědčte se, zda je aplikace správně **nakonfigurována** .

-   Ujistěte se, že je **povolený** účet uživatele pro přihlášení.

-   Ujistěte se, že účet uživatele není **uzamčený.**

-   Ujistěte se, že **heslo uživatele není prošlé nebo zapomenuté.**

-   Ujistěte se, že **Multi-Factor Authentication** neblokuje přístup uživatelů.

-   Zajistěte, aby **zásady podmíněného přístupu** nebo zásady **ochrany identit** neblokovaly přístup uživatelů.

-   Ujistěte se, že **kontaktní údaje pro ověření** uživatele jsou aktuální, aby bylo možné vyhovět Multi-Factor Authentication nebo zásadám podmíněného přístupu.

-   Nezapomeňte taky vymazat soubory cookie v prohlížeči a zkusit se znovu přihlásit.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Požadavky prohlížeče schůzky na přístupový panel

Přístupový panel vyžaduje prohlížeč, který podporuje jazyk JavaScript a je povolený pomocí šablon stylů CSS. Pokud chcete na přístupovém panelu použít jednotné přihlašování založené na heslech (SSO), musí se v prohlížeči uživatele nainstalovat rozšíření přístupového panelu. Toto rozšíření se stáhne automaticky, když uživatel vybere aplikaci, která je nakonfigurovaná pro jednotné přihlašování založené na heslech.

V případě jednotného přihlašování založeného na heslech můžou být prohlížeče koncového uživatele:

-   Internet Explorer 8, 9, 10, 11 – v systému Windows 7 nebo novějším

-   Microsoft Edge ve Windows 10 výročí Edition nebo novějších verzích

-   Chrome – v systému Windows 7 nebo novějším a na MacOS X nebo novějším

-   Firefox 26,0 nebo novější – v systému Windows XP SP2 nebo novějším a v Mac OS X 10,6 nebo novějším

## <a name="how-to-install-the-access-panel-browser-extension"></a>Postup instalace rozšíření prohlížeče přístupového panelu

Chcete-li nainstalovat rozšíření prohlížeče přístupového panelu, postupujte podle následujících kroků:

1.  Otevřete [přístupový panel](https://myapps.microsoft.com) v jednom z podporovaných prohlížečů a přihlaste se jako **uživatel** ve službě Azure AD.

2.  Na přístupovém panelu klikněte na **aplikaci pro jednotné přihlašování pomocí hesla** .

3.  Na příkazovém řádku s výzvou k instalaci softwaru vyberte **nainstalovat hned**.

4.  Na základě vašeho prohlížeče směrovaného na odkaz ke stažení. **Přidejte** rozšíření do prohlížeče.

5.  Pokud se v prohlížeči zobrazí dotaz, vyberte možnost **Povolit** nebo **Povolit** rozšíření.

6.  Po instalaci **restartujte** relaci prohlížeče.

7.  Přihlaste se na přístupový panel a podívejte se, jestli můžete **spouštět** aplikace pro jednotné přihlašování k heslům.

Můžete si také stáhnout rozšíření pro Chrome a Microsoft Edge z přímých odkazů níže:

-   [Rozšíření přístupového panelu Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Rozšíření Microsoft Edge Access panel](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci Galerie Azure AD

Všechny aplikace v galerii Azure AD povolené s možností podnikového jednotného přihlašování mají k dispozici podrobný kurz. Můžete získat přístup k [seznamu kurzů, jak integrovat aplikace SaaS s Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) pro podrobné pokyny.

Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   Přidání aplikace z Galerie Azure AD

-   [Konfigurace hodnot metadat aplikace v Azure AD (přihlašovací adresa URL, identifikátor, adresa URL odpovědi)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat a certifikátů Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurace hodnot metadat Azure AD v aplikaci (přihlašovací adresa URL, vydavatel, adresa URL pro odhlášení a certifikát)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Přiřazení uživatelů k aplikaci

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

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

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurace jednotného přihlašování pro aplikaci z Galerie Azure AD

Chcete-li nakonfigurovat jednotné přihlašování pro aplikaci, postupujte podle následujících kroků:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**

2. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5. Kliknutím na **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud se vám nezobrazí aplikace, kterou chcete zobrazit, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace** a nastavte možnost **Zobrazit** pro **všechny aplikace.**

6. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

8. V rozevíracím seznamu **režim** vyberte možnost **přihlašování založené na SAML** .

9. Zadejte požadované hodnoty do pole **doména a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

   1. Chcete-li nakonfigurovat aplikaci jako jednotné přihlašování, přihlašovací adresa URL je požadovaná hodnota. U některých aplikací je identifikátor také povinná hodnota.

   2. Chcete-li nakonfigurovat aplikaci jako IdP jednotného přihlašování, adresa URL odpovědi je požadovaná hodnota. U některých aplikací je identifikátor také povinná hodnota.

10. **Volitelné:** Pokud chcete zobrazit nepotřebné hodnoty, klikněte na **Zobrazit upřesňující nastavení adresy URL** .

11. V části **atributy uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** .

12. **Volitelné:** kliknutím na **Zobrazit a upravit všechny ostatní atributy uživatele** upravíte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

    2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

13. Kliknutím **na &lt;konfigurovat název&gt; aplikace** získáte přístup k dokumentaci týkající se konfigurace jednotného přihlašování v aplikaci. K nastavení jednotného přihlašování v aplikaci máte také k dispozici adresy URL a certifikát metadat.

14. Kliknutím na **Uložit** uložte konfiguraci.

15. Přiřaďte uživatele k aplikaci.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.

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
   >Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu, který požaduje aplikace v AuthRequest SAML. Další informace najdete v článku [protokol SAML jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy.
   >
   >

9. Pokud chcete přidat atributy uživatele, klikněte na **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

   2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátů Azure AD

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

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat federované jednotné přihlašování pro aplikaci mimo galerii

Chcete-li nakonfigurovat aplikaci mimo galerii, je nutné mít službu Azure AD Premium a aplikace podporuje SAML 2,0. Další informace o verzích Azure AD najdete na stránce [ceny Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   Konfigurace hodnot metadat aplikace v Azure AD (přihlašovací adresa URL, identifikátor, adresa URL odpovědi)

-   [Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Načtení metadat a certifikátů Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurace hodnot metadat Azure AD v aplikaci (přihlašovací adresa URL, vydavatel, adresa URL pro odhlášení a certifikát)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurace hodnot metadat aplikace v Azure AD (přihlašovací adresa URL, identifikátor, adresa URL odpovědi)

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

10. V rozevíracím seznamu **režim** vyberte **přihlašování založené na SAML** .

11. Zadejte požadované hodnoty do pole **doména a adresy URL.** Tyto hodnoty byste měli získat od dodavatele aplikace.

    1. Pokud chcete aplikaci nakonfigurovat jako IdP jednotné přihlašování, zadejte adresu URL odpovědi a identifikátor.

    2. **Volitelné:** Chcete-li nakonfigurovat aplikaci jako jednotné přihlašování, přihlašovací adresa URL je požadovaná hodnota.

12. V části **atributy uživatele**vyberte jedinečný identifikátor pro uživatele v rozevíracím seznamu **identifikátor uživatele** .

13. **Volitelné:** kliknutím na **Zobrazit a upravit všechny ostatní atributy uživatele** upravíte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

    Přidání atributu:

    1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

    2. Klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

14. Kliknutím **na &lt;konfigurovat název&gt; aplikace** získáte přístup k dokumentaci týkající se konfigurace jednotného přihlašování v aplikaci. K dispozici jsou také adresy URL a certifikáty Azure AD, které aplikace vyžaduje.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Vyberte identifikátor uživatele a přidejte atributy uživatele, které se mají odeslat do aplikace.

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
   >Azure AD vyberte formát pro atribut NameID (identifikátor uživatele) na základě vybrané hodnoty nebo formátu, který požaduje aplikace v AuthRequest SAML. Další informace najdete v článku [protokol SAML jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) v části NameIDPolicy.
   >
   >

9. Pokud chcete přidat atributy uživatele, klikněte na **Zobrazit a upravit všechny ostatní atributy uživatele** a upravte atributy, které se mají odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1. klikněte na **Přidat atribut**. Zadejte **název** a vyberte **hodnotu** z rozevíracího seznamu.

   2 klikněte na **Uložit.** V tabulce se zobrazí nový atribut.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Stažení metadat nebo certifikátů Azure AD

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

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci Galerie Azure AD

Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   Přidání aplikace z Galerie Azure AD

-   Konfigurace aplikace pro jednotné přihlašování k heslům

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce** .

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  Do textového pole **Zadejte název** z části **Přidat z Galerie** zadejte název aplikace.

7.  Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název z textového pole **název** .

9.  Kliknutím na tlačítko **Přidat** přidejte aplikaci.

Po krátké době uvidíte podokno konfigurace aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

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

9. Přiřaďte uživatele k aplikaci.

10. Kromě toho můžete přihlašovací údaje jménem uživatele zadat také tak, že vyberete řádky uživatelů a kliknete na **Aktualizovat přihlašovací údaje** a zadáte uživatelské jméno a heslo jménem uživatele. V opačném případě se uživatelům zobrazí výzva k zadání přihlašovacích údajů sami při spuštění.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci mimo galerii

Ke konfiguraci aplikace z Galerie Azure AD potřebujete:

-   [Přidat aplikaci mimo galerii](#add-a-non-gallery-application)

-   [Konfigurace aplikace pro jednotné přihlašování k heslům](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Přidat aplikaci mimo galerii

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle následujících kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com) a přihlaste se jako **globální správce** nebo **spolusprávce** .

2.  Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.

3.  Do vyhledávacího pole filtru zadejte **"Azure Active Directory**" a vyberte položku **Azure Active Directory** .

4.  v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .

5.  klikněte na tlačítko **Přidat** v pravém horním rohu podokna **podnikové aplikace** .

6.  klikněte na **aplikaci mimo galerii.**

7.  Do textového pole **název** zadejte název vaší aplikace. Vyberte **Přidat.**

Po krátké době se bude moci zobrazit podokno konfigurace aplikace.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

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

9. Zadejte **přihlašovací adresu URL**. Toto je adresa URL, kam uživatelé zadají své uživatelské jméno a heslo, aby se mohli přihlásit. Ujistěte se, že jsou přihlašovací pole na adrese URL viditelná.

10. Přiřaďte uživatele k aplikaci.

11. Kromě toho můžete přihlašovací údaje jménem uživatele zadat také tak, že vyberete řádky uživatelů a kliknete na **Aktualizovat přihlašovací údaje** a zadáte uživatelské jméno a heslo jménem uživatele. V opačném případě se uživatelům zobrazí výzva k zadání přihlašovacích údajů sami při spuštění.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Postup přiřazení uživatele k aplikaci přímo

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce.**

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

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Pokud tyto kroky pro řešení potíží nevyřešily problém

Otevřete lístek podpory s následujícími informacemi, pokud jsou k dispozici:

-   ID chyby korelace

-   UPN (uživatelská e-mailová adresa)

-   TenantID

-   Typ prohlížeče

-   Časové pásmo a čas/časový rámec při výskytu chyby

-   Fiddler trasování

## <a name="next-steps"></a>Další kroky
[Zajištění jednotného přihlašování k aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md)

