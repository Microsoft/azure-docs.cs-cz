---
title: Správa federačních certifikátů ve službě Azure AD | Dokumentace Microsoftu
description: Zjistěte, jak přizpůsobit datum vypršení platnosti federačních certifikátů a jak obnovit certifikáty, které budou brzy vyprší.
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: efdcdb8dd2f7917bd1270b9da1d87be655ea675f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170993"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory
Tento článek se věnuje běžné otázky a informace související s certifikáty, které vytvoří Azure Active Directory (Azure AD) k vytvoření federovaného jednotného přihlašování (SSO) k aplikacím SaaS. Přidáte aplikace z Galerie aplikací Azure AD nebo pomocí šablony aplikace mimo galerii. Konfigurace aplikace s použitím možnosti federované jednotné přihlašování.

Tento článek je relevantní pouze pro aplikace, které jsou nakonfigurovány pro použití jednotného přihlašování k Azure AD pomocí SAML federování, jak je znázorněno v následujícím příkladu:

![Azure AD jednotné přihlašování](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automaticky generovaný certifikát pro galerie i mimo Galerii aplikace
Při přidání nové aplikace z galerie a nakonfigurovat základě SAML přihlašování Azure AD vygeneruje certifikát pro aplikaci, která je platné po dobu 3 let. Můžete si stáhnout tento certifikát od **podpisový certifikát SAML** oddílu. U aplikací, galerie může zobrazit v této části možnost stažení certifikátu nebo metadata, v závislosti na požadavku aplikace.

![Azure AD jednotného přihlašování](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Upravit datum vypršení platnosti vašeho certifikátu federace a přejít do nového certifikátu
Ve výchozím nastavení jsou nastavené certifikáty vyprší po uplynutí tří let. Provedením následujících kroků můžete různé platnosti certifikátu.
Snímky obrazovky použít Salesforce pro účely příklad, ale můžete tento postup platí pro všechny federované aplikace SaaS.

1. V [webu Azure portal](https://aad.portal.azure.com), klikněte na tlačítko **podniková aplikace** v levém podokně a pak klikněte na tlačítko **novou aplikaci** na **přehled** stránky:

   ![Otevřete průvodce Konfigurace jednotného přihlašování](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. Vyhledejte aplikaci Galerie a pak vyberte aplikaci, kterou chcete přidat. Pokud nemůžete najít požadovanou aplikaci, přidat aplikace pomocí **aplikace mimo galerii** možnost. Tato funkce je dostupná jenom v SKU úrovně Azure AD Premium (P1 a P2).

    ![Azure AD jednotného přihlašování](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. Klikněte na tlačítko **jednotného přihlašování** odkaz v levém podokně a změnit **režim jednotného přihlašování** k **přihlašování na základě SAML**. Tento krok vytvoří i certifikát tři roky pro vaši aplikaci.

4. Chcete-li vytvořit nový certifikát, klikněte na tlačítko **vytvořit nový certifikát** odkaz v **podpisový certifikát SAML** oddílu.

    ![Vygenerovat nový certifikát](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. **Vytvořte nový certifikát** odkaz otevře ovládacího prvku kalendář. Můžete nastavit libovolné datum a čas až tři roky od současného data. Vybrané datum a čas je nové datum vypršení platnosti a čas váš nový certifikát. Klikněte na **Uložit**.

    ![Stažení a nahrání certifikátu](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. Nový certifikát je nyní k dispozici ke stažení. Klikněte na tlačítko **certifikát** odkaz ke stažení je. Váš certifikát v tomto okamžiku není aktivní. Pokud chcete přejít na tento certifikát, vyberte **nastavit nový certifikát jako aktivní** zaškrtávací políčko a klikněte na tlačítko **Uložit**. Od tohoto okamžiku spuštění služby Azure AD pomocí nového certifikátu pro podepisování odpovědi.

7.  Zjistěte, jak nahrát certifikát k určité aplikaci SaaS, klikněte na tlačítko **zobrazit kurz ke konfiguraci aplikace** odkaz.

## <a name="certificate-expiration-notification-email"></a>E-mail s oznámením o vypršení platnosti certifikátu

Azure AD pošle e-mailové oznámení 60, 30 a 7 dnů před vypršením platnosti certifikátu SAML. Zadání e-mailovou adresu, kam se odeslat oznámení:

- V Azure Active Directory jednotné přihlašování stránky aplikace přejděte na pole e-mailové oznámení.
- Zadejte e-mailovou adresu, která by měla obdržíte e-mail s oznámením vypršení platnosti certifikátu. Ve výchozím nastavení používá toto pole e-mailovou adresu správce, který přidá aplikace.

Obdržíte e-mailových oznámení z aadnotification@microsoft.com. Aby se zabránilo e-mailu, že přejdete do umístění nevyžádané pošty, nezapomeňte přidat tuto e-mailu od svých kontaktů. 

## <a name="renew-a-certificate-that-will-soon-expire"></a>Obnovit certifikát, jehož platnost brzo vyprší
Následující kroky obnovení by měl mít za následek bez významnějších výpadků pro vaše uživatele. Snímky obrazovky v této části funkce Salesforce jako příklad, ale tyto kroky může platit pro všechny federované aplikace SaaS.

1. Na **Azure Active Directory** aplikace **jednotného přihlašování** stránce, generovat nový certifikát pro vaši aplikaci. To můžete provést kliknutím **vytvořit nový certifikát** odkaz v **podpisový certifikát SAML** oddílu.

    ![Vygenerovat nový certifikát](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. Vyberte požadovanou vypršení platnosti datum a čas pro váš nový certifikát a klikněte na tlačítko **Uložit**. Výběr data, která se překrývá s existující certifikát zajistí, že odstávky z důvodu vypršení platnosti certifikátu je omezené. 

3. Pokud aplikace může automaticky nespotřebujete certifikát, nastavte nový certifikát na aktivní.  Přihlásit se k aplikaci a zkontrolujte, že funguje.

4. Pokud aplikace nebude automaticky vyzvednutí nový certifikát, ale můžete popisovač více než jeden podpisový certifikát, před vypršením platnosti ten starý, nahrajte novou aplikaci, pak přejděte zpět na portál a nastavte ji aktivní certifikát. 

5. Pokud aplikace může najednou zpracovat pouze jeden certifikát, vyberte oknu prostoje, stáhněte si nový certifikát, nahrajte ho do aplikace, vraťte se na webu Azure Portal a nastavit nový certifikát jako aktivní. 
   
6. K aktivaci nového certifikátu ve službě Azure AD, vyberte **nastavit nový certifikát jako aktivní** zaškrtávací políčko a klikněte **Uložit** tlačítko v horní části stránky. Zobrazí přes nový certifikát na straně služby Azure AD. Stav certifikátu se změní z **nový** k **aktivní**. Od tohoto okamžiku spuštění služby Azure AD pomocí nového certifikátu pro podepisování odpovědi. 
   
    ![Vygenerovat nový certifikát](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>Související články
* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](../saas-apps/tutorial-list.md)
* [Správa aplikací ve službě Azure Active Directory](what-is-application-management.md)
* [Přístup k aplikaci a jednotné přihlašování s Azure Active Directory](what-is-single-sign-on.md)
* [Řešení potíží s založené na SAML jednotného přihlašování](../develop/howto-v1-debug-saml-sso-issues.md)
