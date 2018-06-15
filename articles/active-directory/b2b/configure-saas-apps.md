---
title: Konfigurace aplikací SaaS pro spolupráci B2B ve službě Azure Active Directory | Microsoft Docs
description: Ukázky kódu a prostředí PowerShell pro Azure Active Directory s B2B spolupráce
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/23/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f0f291216a3031d50d304c02b97786f23d1a6267
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267384"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurace aplikací SaaS pro spolupráci B2B

Spolupráce Azure Active Directory (Azure AD) s B2B pracuje s většinu aplikací, které se integrují s Azure AD. V této části jsme provede pokyny ke konfiguraci některých oblíbených aplikací SaaS pro použití s Azure AD s B2B.

Předtím, než se podíváte na konkrétní aplikaci pokyny, zde jsou některé zásady:

* Pro většinu aplikací musí dojít ručně nastavení uživatele. To znamená uživatelé musí být vytvořeny ručně v aplikaci také.

* Pro aplikace, které podporují automatické nastavení, jako je Dropbox jsou vytvořeny samostatné pozvánek z aplikací. Uživatelé musí být jisti tak, aby přijímal každý pozvánku.

* Atributy uživatele na zmírnit problémy s disk profilu pozměnění uživatele (UPD) v uživatele typu Host, vždy nastavte **uživatelský identifikátor** k **user.mail**.


## <a name="dropbox-business"></a>Obchodní dropbox

Pokud chcete povolit uživatelům přihlášení pomocí účtu organizace, je nutné ručně nakonfigurovat Dropbox obchodní používat Azure AD jako zprostředkovatele identity Security (Assertion Markup Language SAML). Pokud obchodní Dropbox nebyl nakonfigurován k tomu, nemůže výzvu nebo jinak umožňují uživatelům přihlásit se pomocí služby Azure AD.

1. Chcete-li přidat Dropbox obchodní aplikace do služby Azure AD, vyberte **podnikové aplikace, které** v levém podokně a pak klikněte na tlačítko **přidat**.

  ![Tlačítko "Přidat" na stránce podnikových aplikací](media/configure-saas-apps/add-dropbox.png)

2. V **přidat aplikaci** okno, zadejte **dropbox** v vyhledávacího pole a potom vyberte **Dropbox pro firmy** v seznamu výsledků.

  ![Vyhledejte "dropbox" na Přidat stránky aplikace](media/configure-saas-apps/add-app-dialog.png)

3. Na **jednotného přihlašování** vyberte **jednotného přihlašování** v levém podokně a potom zadejte **user.mail** v **uživatelský identifikátor** pole. (Je nastavený jako UPN ve výchozím nastavení.)

  ![Konfigurace jednotného přihlašování pro aplikace](media/configure-saas-apps/configure-app-sso.png)

4. Chcete-li stáhnout certifikát, který chcete použít pro konfiguraci Dropbox, vyberte **konfigurace DropBox**a potom vyberte **SAML jeden znak na adresu URL služby** v seznamu.

  ![Stáhnout certifikát pro Dropbox konfigurace](media/configure-saas-apps/download-certificate.png)

5. Přihlaste se k Dropboxu s adresou URL přihlašování z **jednotného přihlašování** stránky.

  ![Na přihlašovací stránce Dropbox](media/configure-saas-apps/sign-in-to-dropbox.png)

6. V nabídce vyberte **konzoly pro správu**.

  !["Konzoly pro správu" odkaz v nabídce Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. V **ověřování** dialogové okno, vyberte **Další**, odešlete certifikát a potom na **přihlašovací adresa URL** zadejte SAML jeden přihlašovací adresa URL.

  ![Odkaz "Informace" v dialogovém okně sbalené ověřování](media/configure-saas-apps/dropbox-auth-01.png)

  !["Podepsat v adrese URL" v dialogovém okně Rozšířená ověřování](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Chcete-li konfigurovat nastavení automatické uživatele na portálu Azure, vyberte **zřizování** v levém podokně vyberte **automatické** v **režimu zřizování** a pak vyberte **Autorizovat**.

  ![Konfiguraci zřizování automatické uživatelů na portálu Azure](media/configure-saas-apps/set-up-automatic-provisioning.png)

Po hosta nebo člen uživatelů nastavili v aplikaci Dropbox, obdrží samostatné pozvánku z Dropbox. Pokud chcete používat Dropbox jednotné přihlašování, musí pozvaným uživatelům přijmout pozvání kliknutím na odkaz v ní.

## <a name="box"></a>Box
Můžete povolit uživatelům ověřovat uživatele typu Host pole ke svému účtu Azure AD pomocí federace, která je založená na protokolu SAML. V tomto postupu nahrajte Box.com metadat.

1. Přidáte aplikaci Box z podnikové aplikace.

2. Konfigurovat jednotné přihlašování v následujícím pořadí:

  ![Konfigurace pole jednotné přihlašování](media/configure-saas-apps/configure-box-sso.png)

 a. V **přihlásit na adrese URL** pole, zkontrolujte, že adresa URL přihlašování je správně nastavená pro pole na portálu Azure. Tato adresa URL je adresa URL vaší Box.com klienta. Měl by splňovat zásady vytváření názvů *https://.box.com*.  
 **Identifikátor** nelze použít u této aplikace, ale stále zobrazuje jako povinné pole.

 b. V **uživatelský identifikátor** zadejte **user.mail** (pro jednotné přihlašování pro účet hosta).

 c. V části **SAML podpisový certifikát**, klikněte na tlačítko **vytvořit nový certifikát**.

 d. Chcete-li zahájit konfiguraci vašeho klienta Box.com používat Azure AD jako zprostředkovatele identity, stažení souboru metadat a ukládat ho do místního disku.

 e. Soubor metadat do pole dál podporovat týmu, který nakonfiguruje jednotné přihlašování pro vás.

3. Nastavení automatického uživatele Azure AD, v levém podokně, vyberte **zřizování**a potom vyberte **Authorize**.

  ![Autorizace Azure AD pro připojení k poli](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Jako budou pozvaní uživatelé Dropbox musí pozvaným uživatelům pole uplatnit jejich pozvánku z pole aplikace.

## <a name="next-steps"></a>Další postup

Na spolupráci Azure AD B2B najdete v následujících článcích:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Dynamické skupiny a spolupráci B2B](use-dynamic-groups.md)
- [Deklarace uživatele spolupráce B2B mapování](claims-mapping.md)
- [Externí sdílení Office 365](o365-external-user.md)

