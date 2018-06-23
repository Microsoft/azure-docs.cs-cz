---
title: Spravovat federačních certifikátů ve službě Azure AD | Microsoft Docs
description: Zjistěte, jak přizpůsobit datum vypršení platnosti federačních certifikátů a jak obnovit certifikáty, jejichž platnost brzy vyprší.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2017
ms.author: barbkess
ms.reviewer: jeedes
ms.openlocfilehash: 5b4e2694a9b7e67643cb1e3b3cbc99fab940bd9d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36337797"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Spravovat certifikáty pro federované jednotné přihlašování v Azure Active Directory
Tento článek popisuje běžné otázky a informace související s certifikáty, které vytvoří Azure Active Directory (Azure AD) k vytvoření federovaného jednotného přihlašování (SSO) do aplikací SaaS. Přidáte aplikace v galerii aplikací Azure AD nebo pomocí šablony aplikace bez galerie. Konfigurace aplikace s použitím možnosti federované jednotné přihlašování.

Tento článek se týká jenom na aplikace, které jsou nakonfigurovány pro použití Azure AD jednotného přihlašování pomocí SAML federování, jak je znázorněno v následujícím příkladu:

![Azure AD jednotné přihlášení](./media/manage-certificates-for-federated-single-sign-on/saml_sso.PNG)

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automaticky vygeneruje certifikát pro galerii a bez Galerie aplikace
Při přidání nové aplikace z galerie a konfiguraci základě SAML přihlašování, Azure AD vygeneruje certifikát pro aplikaci, která je platný po dobu tří let. Tento certifikát od si můžete stáhnout **SAML podpisový certifikát** části. Pro aplikace, galerie může zobrazit v této části můžete stáhnout certifikát nebo metadata, v závislosti na požadavek na aplikace.

![Azure AD jednotné přihlašování](./media/manage-certificates-for-federated-single-sign-on/saml_certificate_download.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Přizpůsobení datum vypršení platnosti vašeho certifikátu federace a přejít do nového certifikátu
Ve výchozím nastavení jsou nastavené certifikáty vyprší po tři roky. Pomocí následujících kroků můžete různých platnosti certifikátu.
Na snímcích obrazovky používat Salesforce z důvodu příklad, ale tyto kroky provést u všech federovaných aplikací SaaS.

1. V [portál Azure](https://aad.portal.azure.com), klikněte na tlačítko **podniková aplikace** v levém podokně a pak klikněte na tlačítko **novou aplikaci** na **přehled** stránky:

   ![Otevřete průvodce Konfigurace jednotného přihlašování](./media/manage-certificates-for-federated-single-sign-on/enterprise_application_new_application.png)

2. Vyhledejte Galerie aplikace a pak vyberte aplikaci, kterou chcete přidat. Pokud nemůžete najít požadované aplikace, přidání aplikace pomocí **aplikace bez Galerie** možnost. Tato funkce je k dispozici pouze v Azure AD Premium (P1 a P2) SKU.

    ![Azure AD jednotné přihlašování](./media/manage-certificates-for-federated-single-sign-on/add_gallery_application.png)

3. Klikněte na tlačítko **jednotného přihlašování** na odkaz v levém podokně a změňte **režimu přihlašování** k **na základě SAML přihlašování**. Tento krok vygeneruje certifikát tři roky pro vaši aplikaci.

4. Chcete-li vytvořit nový certifikát, klikněte na tlačítko **vytvořit nový certifikát** na odkaz v **SAML podpisový certifikát** části.

    ![Vygenerovat nový certifikát](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

5. **Vytvořit nový certifikát** odkaz otevře ovládacího prvku kalendář. Můžete nastavit žádné datum a čas až tři roky od dnešního data. Vybrané datum a čas je nové datum vypršení platnosti a čas nový certifikát. Klikněte na **Uložit**.

    ![Stáhněte si potom odešlete certifikát](./media/manage-certificates-for-federated-single-sign-on/certifcate_date_selection.PNG)

6. Nový certifikát je nyní k dispozici ke stažení. Klikněte **certifikát** odkaz na stažení. V tomto okamžiku by váš certifikát není aktivní. Pokud chcete přejít na tento certifikát, vyberte **aktivujte nový certifikát** zaškrtávací políčko a klikněte na tlačítko **Uložit**. Od tohoto okamžiku spustí Azure AD pomocí nového certifikátu pro podepisování odpovědi.

7.  Informace o tom, na kterou odešlete certifikát k určité aplikaci SaaS, klikněte **kurz konfigurace zobrazení aplikace** odkaz.

## <a name="certificate-expiration-notification-email"></a>E-mailové oznámení pro vypršení platnosti certifikátu

Azure AD bude posílat e-mailové oznámení 60, 30 a 7 dní před vypršením platnosti certifikátu SAML. Zadání e-mailovou adresu pro kam má posílat oznámení:

- V Azure Active Directory jednu přihlašování stránky aplikace přejděte na pole e-mailové oznámení.
- Zadejte e-mailovou adresu, které by měly dostávat e-mail s oznámením vypršení platnosti certifikátu. Ve výchozím nastavení používá toto pole e-mailovou adresu správce, který přidat aplikaci.

Obdržíte e-mail s oznámením z aadnotification@microsoft.com. Abyste se vyhnuli e-mailu, přejděte do umístění nevyžádané pošty, nezapomeňte přidat tuto e-mailu k vašim kontaktům. 

## <a name="renew-a-certificate-that-will-soon-expire"></a>Obnovení certifikátu, kterému brzy vyprší
Následující kroky obnovení musí mít za následek žádné významné výpadky pro vaše uživatele. Snímky obrazovky v této části funkce Salesforce jako příklad, ale tyto kroky můžete použít na všech federovaných aplikací SaaS.

1. Na **Azure Active Directory** aplikace **jednotného přihlašování** stránky, vygenerujte nový certifikát pro vaši aplikaci. To provedete kliknutím **vytvořit nový certifikát** na odkaz v **SAML podpisový certifikát** části.

    ![Vygenerovat nový certifikát](./media/manage-certificates-for-federated-single-sign-on/create_new_certficate.png)

2. Vyberte požadovanou vypršení platnosti datum a čas nového certifikátu a klikněte na **Uložit**.

3. Stáhnout certifikát v **certifikát pro podpis SAML** možnost. Nahrajte nový certifikát na obrazovku aplikace SaaS jednotné přihlašování. Chcete-li se dozvědět, jak to provést u konkrétní aplikace SaaS, klikněte na tlačítko **kurz konfigurace zobrazení aplikace** odkaz.
   
4. Pokud chcete aktivovat nový certifikát ve službě Azure AD, vyberte **aktivujte nový certifikát** zaškrtávací políčko a klikněte na **Uložit** tlačítka v horní části stránky. To zahrnuje přes nový certifikát na straně Azure AD. Změní stav certifikátu z **nový** k **Active**. Od tohoto okamžiku spustí Azure AD pomocí nového certifikátu pro podepisování odpovědi. 
   
    ![Vygenerovat nový certifikát](./media/manage-certificates-for-federated-single-sign-on/new_certificate_download.png)

## <a name="related-articles"></a>Související články
* [Seznam kurzů k integraci aplikací SaaS v Azure Active Directory](../saas-apps/tutorial-list.md)
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Přístup k aplikaci a jednotné přihlašování s Azure Active Directory](what-is-single-sign-on.md)
* [Řešení potíží s na základě SAML jednotné přihlašování](../develop/active-directory-saml-debugging.md)
