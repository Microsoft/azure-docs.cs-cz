---
title: 'Kurz: Azure Active Directory integrace jednotného přihlašování se softwarem Maxientu pro správu softwaru | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Maxient software správce.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: 4b57b3fbb338774eb8d66fc4d3c0c817e19aff4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92458196"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>Kurz: Azure Active Directory integrace jednotného přihlašování pomocí softwaru správce Maxient

V tomto kurzu se dozvíte, jak integrovat software správce Maxient pomocí služby Azure Active Directory (Azure AD). Když integrujete software správce Maxient pomocí služby Azure AD, můžete provádět tyto akce:

* Využijte Azure AD k ověřování uživatelů pro software Maxient reorganizovat.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Maxienti softwaru správce pomocí svých účtů Azure AD.


Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Maxient provádí odběr s povoleným jednotným přihlašováním (SSO) pro software Manager.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete službu Azure AD pro použití se softwarem Maxient a nástrojem.


* Software správce Maxiente podporuje **aktualizace SP a IDP,** iniciované jednotné přihlašování

> [!NOTE]
> Identifikátorem této aplikace je pevná řetězcová hodnota, takže v jednom tenantovi může být nakonfigurovaná jenom jedna instance.

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>Přidání softwaru správce Maxient do galerie

Pokud chcete nakonfigurovat integraci Maxientho softwaru správce do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Maxient program Správce chování z galerie.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Maxient software Manager** .
1. Na panelu výsledků vyberte **Maxient software Manager** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Maxient software Manager

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Maxientho softwaru správce. Aby jednotné přihlašování fungovalo, musíte navázat spojení mezi službou Azure AD a softwarem Maxientu pro správu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Maxient softwaru správce, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům ověřování pro použití se softwarem Maxient a software Manager
    1. **[Přiřaďte všem uživatelům, aby používali Maxient](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** – k tomu, aby mohli všichni pracovníci vaší instituce ověřovat.
1. **[Otestujte nastavení Azure AD pomocí Maxient](#test-with-maxient)** – ověřte, jestli konfigurace funguje a že se uvolňují správné atributy.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace **Maxientch softwarových** aplikací pro správu, najděte část **Správa** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. V **základním oddílu konfigurace SAML** je aplikace předem nakonfigurovaná v režimu iniciované **IDP** a nezbytné adresy URL už jsou předem naplněné pomocí Azure. Uživatel musí konfiguraci uložit kliknutím na tlačítko **Uložit** .

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > Hodnota není reálné číslo. Aktualizujte hodnotu skutečnou přihlašovací adresou URL. Pokud chcete získat hodnotu, spolupracujte se svým Maxient implementací nebo podporou.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.  K této adrese URL budete muset zadat Maxient implementaci/zástupce podpory.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>Přiřazení všech uživatelů, kteří budou moci ověřit software správce Maxient

V této části udělíte přístup pro všechny účty k ověřování pomocí systému Azure pro Maxient software Manager.  Je důležité si uvědomit, že tento krok je **nutný** , aby Maxient správně fungoval.  Maxient využívá váš systém Azure AD k *ověřování* uživatelů. *Autorizace* uživatelů se provádí v systému Maxient pro konkrétní funkci, kterou se pokouší provést. Maxient nepoužívá k provedení těchto rozhodnutí atributy z vašeho adresáře.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Maxient software správce**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte všichni uživatelé (nebo příslušné skupiny) a **přiřaďte** je, abyste je mohli ověřit pomocí Maxient.

## <a name="test-with-maxient"></a>Testování pomocí Maxient 

Pokud se lístek podpory ještě neotevřel s Maxient implementací nebo pracovníkem podpory, pošlete e-mail na [support@maxient.com](mailto:support@maxient.com) téma "ověřování na základě areálu/nastavení Azure- \<\<School Name\> \> ". Do těla e-mailu zadejte **adresu URL federačních metadat aplikace**. Maxient zaměstnanci budou reagovat s odkazem na testování a ověřit, jestli jsou vydávány správné atributy.  
    
## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Maxient software pro správu pomocí Azure AD](https://aad.portal.azure.com/)