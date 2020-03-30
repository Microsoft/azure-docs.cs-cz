---
title: Konfigurace aplikací SaaS pro spolupráci B2B – Azure AD
description: Kód a ukázky PowerShellu pro spolupráci B2B služby Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c73a14c2a8cd063672bd0998368ca660f52cd5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272956"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurace aplikací SaaS pro spolupráci B2B

Spolupráce b2B služby Azure Active Directory (Azure AD) funguje s většinou aplikací, které se integrují s Azure AD. V této části procházíme pokyny pro konfiguraci některých oblíbených aplikací SaaS pro použití s Azure AD B2B.

Než se podíváte na pokyny pro konkrétní aplikace, tady jsou některá pravidla:

* U většiny aplikací se nastavení uživatele musí uskutečnit ručně. To znamená, že uživatelé musí být vytvořeny ručně v aplikaci také.

* Pro aplikace, které podporují automatické nastavení, jako je Dropbox, se z nich vytvářejí samostatné pozvánky. Uživatelé musí být jisti, že přijmout každou pozvánku.

* Chcete-li zmírnit případné problémy s pošanitým diskem profilu uživatele (UPD) u uživatelů typu Host, nastavte vždy **identifikátor uživatele** na **soubor user.mail**.


## <a name="dropbox-business"></a>Dropbox podnikání

Chcete-li uživatelům povolit přihlášení pomocí svého účtu organizace, musíte ručně nakonfigurovat Dropbox Business tak, aby používal Azure AD jako zprostředkovatele identity s výrčitek zabezpečení (SAML). Pokud Dropbox Business nebyl nakonfigurován tak, že nemůže vyzvat nebo jinak povolit uživatelům přihlásit pomocí Azure AD.

1. Pokud chcete přidat aplikaci Dropbox Business do Azure AD, v levém podokně vyberte **podnikové aplikace** a klikněte na **Přidat**.

   ![Tlačítko "Přidat" na stránce Podnikové aplikace](media/configure-saas-apps/add-dropbox.png)

2. V okně **Přidat aplikaci** zadejte do vyhledávacího pole **rozevírací políčko** a v seznamu výsledků vyberte **Dropbox pro firmy.**

   ![Hledání "dropbox" na stránce Přidat aplikaci](media/configure-saas-apps/add-app-dialog.png)

3. Na stránce **Jednotné přihlašování** vyberte v levém podokně **jednotné přihlašování** a zadejte **user.mail** do pole **Identifikátor uživatele.** (Ve výchozím nastavení je nastavenjako UPN.)

   ![Konfigurace jednotného přihlášení pro aplikaci](media/configure-saas-apps/configure-app-sso.png)

4. Chcete-li stáhnout certifikát, který chcete použít pro konfiguraci Dropboxu, vyberte **konfigurovat DropBox**a v seznamu vyberte **adresu URL služby jednotného přihlašování SAML.**

   ![Stažení certifikátu pro konfiguraci Dropboxu](media/configure-saas-apps/download-certificate.png)

5. Přihlaste se do Dropboxu pomocí přihlašovací adresy URL na stránce **jednotného přihlášení.**

   ![Snímek obrazovky s přihlašovací stránkou Dropboxu](media/configure-saas-apps/sign-in-to-dropbox.png)

6. V nabídce vyberte **Admin Console**.

   ![Odkaz "Admin Console" v nabídce Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. V dialogovém okně **Ověřování** vyberte **Další**, nahrajte certifikát a potom do pole **Přihlašovací adresa URL** zadejte adresu URL jednotného přihlášení SAML.

   ![Odkaz Další ve sbaleném dialogovém okně Ověřování](media/configure-saas-apps/dropbox-auth-01.png)

   ![Přihlašovací adresa URL v rozbaleném dialogovém okně Ověřování](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Pokud chcete nakonfigurovat automatické nastavení uživatele na webu Azure Portal, v levém podokně vyberte **Zřizování,** vyberte **Automaticky** v poli **Režim zřizování** a pak vyberte **Autorizovat**.

   ![Konfigurace automatického zřizování uživatelů na webu Azure Portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

Poté, co byli uživatelé hosta nebo člena nastaveni v aplikaci Dropbox, obdrží od Dropboxu samostatnou pozvánku. Chcete-li používat jednotné přihlašování dropboxu, musí pozvaní přijmout pozvánku kliknutím na odkaz v něm.

## <a name="box"></a>Box
Můžete povolit uživatelům ověřování uživatelů typu Host box u jejich účtu Azure AD pomocí federace, která je založena na protokolu SAML. V tomto postupu nahrajete metadata do Box.com.

1. Přidejte aplikaci Box z podnikových aplikací.

2. Nakonfigurujte jednotné přihlašování v následujícím pořadí:

   ![Snímek obrazovky s nastavením konfigurace jednotného přihlášení](media/configure-saas-apps/configure-box-sso.png)

   a. V poli **Přihlásit se na adresu URL** se ujistěte, že přihlašovací adresa URL je správně nastavená pro Box na webu Azure Portal. Tato adresa URL je adresou URL Box.com tenanta. Měl by se *https://.box.com*řídit konvencí pojmenování .  
   **Identifikátor** se na tuto aplikaci nevztahuje, ale stále se zobrazuje jako povinné pole.

   b. Do pole **Identifikátor uživatele** zadejte **user.mail** (pro spřibíka pro účty hosta).

   c. V části **PODPISOVÝ Certifikát SAML**klepněte na tlačítko **Vytvořit nový certifikát**.

   d. Chcete-li začít konfigurovat Box.com tenanta pro použití Azure AD jako zprostředkovatele identity, stáhněte soubor metadat a uložte ho na místní disk.

   e. Přepošlete soubor metadat týmu podpory boxu, který pro vás nakonfiguruje jednotné přihlašování.

3. Pro automatické nastavení uživatele Azure AD v levém podokně vyberte **Zřizování**a pak vyberte **Autorizovat**.

   ![Autorizace Azure AD pro připojení k Boxu](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Stejně jako pozvaní uživatelé Dropboxu musí pozvaní boxu uplatnit pozvánku z aplikace Box.

## <a name="next-steps"></a>Další kroky

Podívejte se na následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Dynamické skupiny a b2B spolupráce](use-dynamic-groups.md)
- [Mapování deklarací identity uživatelů spolupráce B2B](claims-mapping.md)
- [Externí sdílení Office 365](o365-external-user.md)

