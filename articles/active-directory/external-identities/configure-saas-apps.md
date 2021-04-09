---
title: Konfigurace aplikací SaaS pro spolupráci B2B – Azure AD
description: Naučte se konfigurovat aplikace SaaS pro spolupráci Azure Active Directory B2B a zobrazit další dostupné prostředky.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0db5e609a5e6b7d74809810a50f86d72ae85675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "90705506"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>Konfigurace aplikací SaaS pro spolupráci B2B

Spolupráce B2B Azure Active Directory (Azure AD) spolupracuje s většinou aplikací, které se integrují se službou Azure AD. V této části si projdeme pokyny pro konfiguraci některých oblíbených aplikací SaaS pro použití s Azure AD B2B.

Než se podíváte na pokyny specifické pro danou aplikaci, tady je několik pravidel pro palec:

* Pro většinu aplikací musí uživatelské nastavení probíhat ručně. To znamená, že uživatelé musí být vytvořeni také ručně v aplikaci.

* Pro aplikace, které podporují automatickou instalaci, jako je třeba Dropbox, se z aplikací vytvoří samostatné pozvánky. Uživatelé musí být přijměte každou pozvánku.

* Chcete-li v uživatelských atributech zmírnit problémy s pozměněným diskem s profilem uživatele (UPD) v uživatelích typu Host, vždy nastavte **identifikátor uživatele** na hodnotu **User. mail**.


## <a name="dropbox-business"></a>Dropbox Business

Pokud chcete uživatelům povolit, aby se přihlásili pomocí svého účtu organizace, musíte ručně nakonfigurovat společnost Dropbox na použití Azure AD jako zprostředkovatele identity Security Assertion Markup Language (SAML). Pokud společnost pro Dropbox není nakonfigurovaná tak, aby to provedla, nemůže se uživatelům dotázat nebo jinak povolit přihlášení pomocí Azure AD.

1. Pokud chcete obchodní aplikaci Dropboxu přidat do služby Azure AD, v levém podokně vyberte **podnikové aplikace** a pak klikněte na **Přidat**.

   ![Tlačítko Přidat na stránce podnikové aplikace](media/configure-saas-apps/add-dropbox.png)

2. V okně **Přidat aplikaci** zadejte do vyhledávacího pole **Dropbox** a potom v seznamu výsledků vyberte **Dropbox pro firmy** .

   ![Hledání "Dropboxu" na stránce Přidat aplikaci](media/configure-saas-apps/add-app-dialog.png)

3. Na stránce **jednotného přihlašování** vyberte v levém podokně **jednotné přihlašování** a potom do pole **identifikátor uživatele** zadejte **User. mail** . (Ve výchozím nastavení je nastavený jako hlavní název uživatele (UPN).)

   ![Konfigurace jednotného přihlašování pro aplikaci](media/configure-saas-apps/configure-app-sso.png)

4. Pokud chcete stáhnout certifikát, který se má použít pro konfiguraci Dropboxu, vyberte **Konfigurovat Dropbox** a pak v seznamu vyberte **adresu URL jednotného přihlašování SAML** .

   ![Stahuje se certifikát pro konfiguraci Dropboxu.](media/configure-saas-apps/download-certificate.png)

5. Přihlaste se k Dropboxu pomocí přihlašovací adresy URL na stránce **jednotného přihlašování** .

   ![Snímek obrazovky zobrazující přihlašovací stránku Dropboxu](media/configure-saas-apps/sign-in-to-dropbox.png)

6. V nabídce vyberte **Konzola správce**.

   ![Odkaz Konzola pro správu v nabídce Dropbox](media/configure-saas-apps/dropbox-menu.png)

7. V dialogovém okně **ověřování** vyberte **Další**, nahrajte certifikát a potom do pole **přihlašovací adresa URL** zadejte adresu URL jednotného přihlašování SAML.

   ![Odkaz další v dialogovém okně sbalené ověřování](media/configure-saas-apps/dropbox-auth-01.png)

   ![Přihlašovací adresa URL v dialogovém okně Rozšířené ověřování](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Chcete-li konfigurovat automatické nastavení uživatele v Azure Portal, v levém podokně vyberte možnost **zřizování** , v rozevíracím seznamu **režim zřizování** vyberte možnost **automaticky** a potom vyberte **autorizovat**.

   ![Konfigurace automatického zřizování uživatelů v Azure Portal](media/configure-saas-apps/set-up-automatic-provisioning.png)

Po nastavení uživatelů hosta nebo členů v aplikaci Dropbox obdrží samostatná Pozvánka z Dropboxu. Pokud chcete použít jednotné přihlašování Dropboxu, musí pozvánky přijmout kliknutím na odkaz v něm.

## <a name="box"></a>Box
Uživatelům můžete povolit, aby mohli k účtu Azure AD ověřovat uživatele typu host s použitím federace založeného na protokolu SAML. V tomto postupu nahrajete metadata do Box.com.

1. Přidejte aplikaci box z podnikových aplikací.

2. Konfigurace jednotného přihlašování v tomto pořadí:

   ![Snímek obrazovky zobrazující nastavení konfigurace jednotného přihlašování](media/configure-saas-apps/configure-box-sso.png)

   a. V poli **Adresa URL pro přihlášení** zkontrolujte, že je správně nastavená přihlašovací adresa URL pro pole Azure Portal. Tato adresa URL je adresa URL vašeho tenanta Box.com. Měl by následovat po konvenci pojmenování *https://.box.com* .  
   **Identifikátor** se nedá použít pro tuto aplikaci, ale pořád se zobrazuje jako povinné pole.

   b. Do pole **identifikátor uživatele** zadejte **User. mail** (pro jednotné přihlašování pro účty hosta).

   c. V části **podpisový certifikát SAML** klikněte na **vytvořit nový certifikát**.

   d. Pokud chcete začít konfigurovat tenanta Box.com, aby používal Azure AD jako zprostředkovatele identity, Stáhněte si soubor metadat a uložte ho na místní disk.

   e. Předejte soubor metadat do týmu podpory box, který nakonfiguruje jednotné přihlašování za vás.

3. Pro automatické uživatelské nastavení Azure AD v levém podokně vyberte **zřizování** a potom vyberte **autorizovat**.

   ![Autorizace Azure AD pro připojení k boxu](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

Podobně jako pozvánky na Dropbox musí pozvánky boxu uplatnit svoji pozvánku z aplikace box.

## <a name="next-steps"></a>Další kroky

Přečtěte si následující články o spolupráci Azure AD B2B:

- [Co je spolupráce B2B ve službě Azure AD?](what-is-b2b.md)
- [Dynamické skupiny a spolupráce B2B](use-dynamic-groups.md)
- [Mapování deklarací identity uživatelů spolupráce B2B](claims-mapping.md)
- [Microsoft 365 externí sdílení](o365-external-user.md)

