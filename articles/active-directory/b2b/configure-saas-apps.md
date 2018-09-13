---
title: Konfigurace aplikací SaaS pro spolupráci B2B ve službě Azure Active Directory | Dokumentace Microsoftu
description: Ukázky kódu a prostředí PowerShell pro spolupráci Azure Active Directory s B2B
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 409cce16d256c408aaa245b97d17171669a2cf41
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642793"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurace aplikací SaaS pro spolupráci B2B

Spolupráce Azure Active Directory (Azure AD) B2B funguje se většina aplikací, které se integrují s Azure AD. V této části provedeme pokyny ke konfiguraci několik oblíbených aplikací SaaS pro použití s Azure AD B2B.

Předtím, než se podíváte na pokyny specifické pro aplikace, tady jsou některé hrubé odhady:

* Pro většinu aplikací nastavení uživatele potřeba ručně. To znamená, že uživatelé musí být vytvořeny ručně v aplikaci stejně.

* Pro aplikace, které podporují automatické nastavení, jako je například Dropbox se vytvoří samostatné pozvánky z aplikací. Uživatelé musí být potřeba přijmout každou pozvánku.

* Atributy uživatele na zmírnit problémy s disk profilu pozměnění uživatele (UPD) u uživatelů typu Host, vždy nastavte **identifikátor uživatele** k **user.mail**.


## <a name="dropbox-business"></a>Obchodní Dropboxu

Pokud chcete povolit uživatelům umožní přihlásit se pomocí svého účtu organizace, je nutné ručně nakonfigurovat Dropboxu obchodního použití služby Azure AD jako zprostředkovatele identity zabezpečení kontrolního výrazu SAML (Markup Language). Pokud obchodní Dropboxu není nakonfigurovaná k tomu, nemůže výzvu nebo jinak umožnit uživatelům přihlášení pomocí Azure AD.

1. Chcete-li přidat Dropboxu obchodní aplikace do služby Azure AD, **podnikové aplikace** v levém podokně a pak klikněte na tlačítko **přidat**.

  ![Tlačítko "Přidat" na stránce aplikace organizace](media/configure-saas-apps/add-dropbox.png)

2. V **přidat aplikaci** okno, zadejte **dropboxu** ve vyhledávacím poli a pak vyberte **Dropbox for Business** v seznamu výsledků.

  ![Vyhledejte "dropboxu" na přidání stránky aplikace](media/configure-saas-apps/add-app-dialog.png)

3. Na **jednotného přihlašování** stránce **jednotného přihlašování** v levém podokně a pak zadejte **user.mail** v **identifikátor uživatele** pole. (Je nastavena jako hlavní název uživatele ve výchozím nastavení.)

  ![Konfigurace jednotného přihlašování pro aplikaci](media/configure-saas-apps/configure-app-sso.png)

4. Chcete-li stáhnout certifikát, který chcete použít pro konfiguraci Dropboxu, vyberte **nakonfigurovat Dropboxu**a pak vyberte **SAML jednotné přihlašování na adresu URL služby** v seznamu.

  ![Stahuje se certifikát pro konfiguraci Dropboxu](media/configure-saas-apps/download-certificate.png)

5. Přihlaste se k Dropboxu adresou URL přihlašování z **jednotného přihlašování** stránky.

  ![Přihlašovací stránka Dropboxu](media/configure-saas-apps/sign-in-to-dropbox.png)

6. V nabídce vyberte **konzoly pro správu**.

  ![Odkaz "Konzoly pro správu" v nabídce Dropboxu](media/configure-saas-apps/dropbox-menu.png)

7. V **ověřování** dialogu **Další**, nahrát na server certifikát a potom na **přihlásit v adrese URL** zadejte SAML jednotné přihlašování – adresa URL.

  !["Informace" odkaz v dialogovém okně sbalený ověřování](media/configure-saas-apps/dropbox-auth-01.png)

  !["Podepsat v adrese URL" v dialogovém okně Rozšířené ověřování](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Chcete-li konfigurovat nastavení automatické uživatele na webu Azure Portal, vyberte **zřizování** v levém podokně vyberte **automatické** v **režim zřizování** a potom vyberte  **Povolit**.

  ![Konfigurace automatické zřizování uživatelů na webu Azure Portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

Po uživatelů typu Host nebo člen mít nastavení v aplikaci Dropbox, dostanou samostatnou pozvánku z Dropboxu. Pokud chcete používat Dropbox jednotného přihlašování, musí budou pozvaní uživatelé přijetí pozvánky klepnutím na odkaz v ní.

## <a name="box"></a>Box
Můžete povolit uživatele bude možné ověřit uživatele typu Host pole ke svému účtu Azure AD s použitím federace, která je založená na protokolu SAML. V tomto postupu nahrajte metadat na Box.com.

1. Přidáte aplikaci Box z podnikových aplikací.

2. Konfigurace jednotného přihlašování v následujícím pořadí:

  ![Nakonfigurujte pole jednotného přihlašování](media/configure-saas-apps/configure-box-sso.png)

 a. V **přihlašovací adresa URL** pole, ujistěte se, že přihlašovací adresa URL je nakonfigurovaná pro pole na webu Azure Portal. Tato adresa URL je adresa URL vašeho tenanta Box.com. Měla by odpovídat zásady vytváření názvů *https://.box.com*.  
 **Identifikátor** se nedá použít u této aplikace, ale stále zobrazuje jako povinné pole.

 b. V **identifikátor uživatele** zadejte **user.mail** (pro jednotné přihlašování pro účty hostů).

 c. V části **podpisový certifikát SAML**, klikněte na tlačítko **vytvořit nový certifikát**.

 d. Pokud chcete začít konfigurovat Box.com tenanta pro službu Azure AD jako zprostředkovatele identity, stažení souboru metadat a uložte ho do místního disku.

 e. Soubor metadat pro pole podporují vpřed týmu, který nakonfiguruje jednotné přihlašování pro vás.

3. Nastavení automatické uživatele Azure AD, v levém podokně vyberte **zřizování**a pak vyberte **Authorize**.

  ![Povolit Azure AD a připojit se k boxu](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Stejně jako budou pozvaní uživatelé Dropboxu pole budou pozvaní uživatelé musí uplatňovat svou pozvánku z aplikace pole.

## <a name="next-steps"></a>Další postup

Na spolupráce B2B ve službě Azure AD najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Dynamické skupiny a spolupráci B2B](use-dynamic-groups.md)
- [Mapování deklarací uživatele spolupráce B2B](claims-mapping.md)
- [Externí sdílení Office 365](o365-external-user.md)

