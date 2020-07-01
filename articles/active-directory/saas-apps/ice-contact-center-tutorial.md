---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování pomocí centra kontaktů Ice | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a centrem kontaktů Ice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85c964d8-7c0f-458d-8df5-b7d0768f298d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/15/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37f06bbc91fef0f0d8c1c646082bd95452c7e518
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85605793"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ice-contact-center"></a>Kurz: Azure Active Directory integraci jednotného přihlašování pomocí centra kontaktů Ice

V tomto kurzu se dozvíte, jak integrovat centrum kontaktů Ice pomocí Azure Active Directory (Azure AD). Když integrujete centrum kontaktů Ice s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k centru kontaktů Ice.
* Umožněte, aby se vaši uživatelé automaticky přihlásili do centra kontaktů Ice pomocí svých účtů Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* předplatné s povoleným jednotným přihlašováním (SSO) pro kontaktování softwaru Ice

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kontaktní centrum Ice podporuje jednotné přihlašování v **SP** .
* Po nakonfigurování centra kontaktů Ice můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-ice-contact-center-from-the-gallery"></a>Přidání centra kontaktů Ice z Galerie

Pokud chcete nakonfigurovat integraci kontaktního centra ICE do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat kontaktní centrum Ice z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **ICE Contact Center** .
1. Vyberte **centrum kontaktů Ice** z panelu výsledků a pak přidejte aplikaci. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ice-contact-center"></a>Konfigurace a testování jednotného přihlašování Azure AD pro centrum kontaktů Ice

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí centra kontaktů Ice pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v centru kontaktů Ice.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí centra kontaktů Ice, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Konfigurace služby Ice kontaktní centrum SSO](#configure-ice-contact-center-sso)** – ke konfiguraci nastavení jednotného přihlašování na straně aplikace
    1. **[Vytvořit testovacího uživatele centra kontaktů Ice](#create-ice-contact-center-test-user)** – Chcete-li mít protějšek B. Simon ve službě Ice Contact Center, která je propojená se zastoupením uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce pro integraci aplikace **centra kontaktů Ice** najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V části **základní konfigurace SAML** zadejte hodnoty pro následující pole:

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<TENANT>.iceuc.com/iceManager`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí jednoho z následujících vzorů:

        ```https
        https://<TENANT>-imrpool.icescape365.com:PORT/identity
        https://<TENANT>-imrpool.icescape.com:PORT/identity
        https://<TENANT>-imrpool.iceuc.com:PORT/identity
        ```

    c. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí jednoho z následujících vzorů:

        ```https
        https://<TENANT>-imrpool.icescape365.com:PORT/identity
        https://<TENANT>-imrpool.icescape.com:PORT/identity
        https://<TENANT>-imrpool.iceuc.com:PORT/identity
    ```

    > [!NOTE]
    > These values are not real. Update these values with the actual Sign on URL, Identifier and Reply URL. Contact [ice Contact Center Client support team](mailto:support@computer-talk.com) to get these values. You can also refer to the patterns shown in the **Basic SAML Configuration** section in the Azure portal.

1. On the **Set up single sign-on with SAML** page, In the **SAML Signing Certificate** section, click copy button to copy **App Federation Metadata Url** and save it on your computer.

    ![The Certificate download link](common/copy-metadataurl.png)

### Create an Azure AD test user

In this section, you'll create a test user in the Azure portal called B.Simon.

1. From the left pane in the Azure portal, select **Azure Active Directory**, select **Users**, and then select **All users**.
1. Select **New user** at the top of the screen.
1. In the **User** properties, follow these steps:
   1. In the **Name** field, enter `B.Simon`.  
   1. In the **User name** field, enter the username@companydomain.extension. For example, `B.Simon@contoso.com`.
   1. Select the **Show password** check box, and then write down the value that's displayed in the **Password** box.
   1. Click **Create**.

### Assign the Azure AD test user

In this section, you'll enable B.Simon to use Azure single sign-on by granting access to ice Contact Center.

1. In the Azure portal, select **Enterprise Applications**, and then select **All applications**.
1. In the applications list, select **ice Contact Center**.
1. In the app's overview page, find the **Manage** section and select **Users and groups**.

   ![The "Users and groups" link](common/users-groups-blade.png)

1. Select **Add user**, then select **Users and groups** in the **Add Assignment** dialog.

    ![The Add User link](common/add-assign-user.png)

1. In the **Users and groups** dialog, select **B.Simon** from the Users list, then click the **Select** button at the bottom of the screen.
1. If you're expecting any role value in the SAML assertion, in the **Select Role** dialog, select the appropriate role for the user from the list and then click the **Select** button at the bottom of the screen.
1. In the **Add Assignment** dialog, click the **Assign** button.

## Configure ice Contact Center SSO

To configure single sign-on on **ice Contact Center** side, you need to send the **App Federation Metadata Url** to [ice Contact Center support team](mailto:support@computer-talk.com). They set this setting to have the SAML SSO connection set properly on both sides.

### Create ice Contact Center test user

In this section, you create a user called Britta Simon in ice Contact Center. Work with [ice Contact Center support team](mailto:support@computer-talk.com) to add the users in the ice Contact Center platform. Users must be created and activated before you use single sign-on.

## Test SSO 

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the ice Contact Center tile in the Access Panel, you should be automatically signed in to the ice Contact Center for which you set up SSO. For more information about the Access Panel, see [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## Additional resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [What is conditional access in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Try ice Contact Center with Azure AD](https://aad.portal.azure.com/)

- [What is session control in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [How to protect ice Contact Center with advanced visibility and controls](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

