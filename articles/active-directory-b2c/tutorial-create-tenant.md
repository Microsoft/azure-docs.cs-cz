---
title: Kurz – vytvoření tenanta Azure Active Directory B2C
description: Naučte se připravit registraci aplikací vytvořením klienta Azure Active Directory B2C pomocí Azure Portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 57aed26fe2f2f03dca347836f21d1cad14a07642
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063351"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Kurz: Vytvoření tenanta Azure Active Directory B2C

Než můžou vaše aplikace pracovat s Azure Active Directory B2C (Azure AD B2C), musí být zaregistrované v tenantovi, které spravujete.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojení tenanta s předplatným

Naučíte se, jak zaregistrovat aplikaci v dalším kurzu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vaše předplatné. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vaše předplatné. Tento adresář je jiný než ten, který bude obsahovat vašeho tenanta Azure AD B2C.

    ![Filtr adresářů a předplatných s vybraným klientem předplatného](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .
4. Vyhledejte a vyberte **Active Directory B2C**a pak klikněte na **vytvořit**.
5. Vyberte **vytvořit nového tenanta Azure AD B2C** a zadejte název organizace a počáteční název domény. Vyberte zemi nebo oblast (nedá se změnit později) a pak klikněte na **vytvořit**.

    Jako součást názvu tenanta se používá počáteční název domény. V tomto příkladu je název tenanta *contoso0926Tenant.onmicrosoft.com*:

    ![Stránka pro vytvoření tenanta B2C v Azure Portal](./media/tutorial-create-tenant/create-tenant.PNG)

6. Na stránce **vytvořit nového TENANTA B2C nebo propojit s existujícím klientem** vyberte možnost **propojit existujícího tenanta Azure AD B2C s předplatným Azure**.

    Vyberte tenanta, kterého jste vytvořili, a vyberte své předplatné.

    V případě skupiny prostředků vyberte **vytvořit novou**. Zadejte název skupiny prostředků, která bude obsahovat tenanta, vyberte umístění a pak klikněte na **vytvořit**.
1. Pokud chcete začít používat nového tenanta, ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který ho obsahuje.

    Pokud se v seznamu nezobrazuje nový tenant Azure B2C, aktualizujte okno prohlížeče a potom v horní nabídce znovu vyberte filtr **adresář + odběr** .

    ![Filtr adresářů a předplatných s vybraným klientem B2C](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojení tenanta s předplatným

V dalším kroku se dozvíte, jak zaregistrovat webovou aplikaci v novém tenantovi.

> [!div class="nextstepaction"]
> [Zaregistrujte své aplikace >](tutorial-register-applications.md)
