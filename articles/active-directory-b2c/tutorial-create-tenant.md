---
title: Kurz – vytvoření tenanta Azure Active Directory B2C
description: Zjistěte, jak připravit pro registraci vaší aplikace tak, že vytvoříte tenanta služby Azure Active Directory B2C na webu Azure portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 711b9152f9f3fa1b3573e39d1950f18b628c268a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056316"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Kurz: Vytvoření tenanta Azure Active Directory B2C

Předtím, než aplikace můžete pracovat s Azure Active Directory (Azure AD) B2C, musí být zaregistrovaná v tenantovi, která spravujete.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojte svého tenanta se svým předplatným

Zjistíte, jak zaregistrovat aplikaci v dalším kurzu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje vaše předplatné. Klikněte na tlačítko **filtr adresářů a předplatných** v horní nabídce vyberte adresář, který obsahuje vaše předplatné. Tento adresář je jiný než ten, který bude obsahovat vašeho tenanta Azure AD B2C.

    ![Přepnout na adresář předplatného](./media/tutorial-create-tenant/switch-directory-subscription.PNG)

3. Zvolte **vytvořit prostředek** v levém horním rohu webu Azure portal.
4. Vyhledejte a vyberte **Active Directory B2C**a potom klikněte na tlačítko **vytvořit**.
5. Zvolte **vytvořit nového Tenanta Azure AD B2C** a zadejte název organizace a počáteční název domény. Vyberte zemi nebo oblast (ho není možné později změnit) a potom klikněte na tlačítko **vytvořit**.

    Název domény se používá jako součást název vašeho klienta. V tomto příkladu je název tenanta *contoso0926Tenant.onmicrosoft.com*:

    ![Vytvoření tenanta](./media/tutorial-create-tenant/create-tenant.PNG)

6. Na **vytvořit nového Tenanta B2C nebo propojit existujícího Tenanta** zvolte **Tenanta odkaz stávající službou Azure AD B2C s mým předplatným Azure**.

    Vyberte tenanta, který jste vytvořili a vyberte své předplatné.

    Pro skupinu prostředků, vyberte **vytvořit nový**. Zadejte název pro skupinu prostředků, která bude obsahovat tenanta, vyberte umístění a potom klikněte na **vytvořit**.
1. Pokud chcete začít používat nového tenanta, ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáři, který jej obsahuje.

    Pokud se zpočátku vašeho nového tenanta Azure B2C v seznamu nevidíte, aktualizujte okno prohlížeče a pak vyberte **filtr adresářů a předplatných** znovu v horní nabídce.

    ![Přepnout do tenanta adresáře](./media/tutorial-create-tenant/switch-directories.PNG)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojte svého tenanta se svým předplatným

Dále se naučíte zaregistrovat webovou aplikaci do nového tenanta.

> [!div class="nextstepaction"]
> [Registrace aplikace >](tutorial-register-applications.md)
