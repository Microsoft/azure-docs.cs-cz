---
title: Kurz – vytvoření tenanta Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak připravit pro registraci vaší aplikace tak, že vytvoříte tenanta služby Azure Active Directory B2C na webu Azure portal.
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7571e5f4d95320ab92fa3b69b0ea1f05ff9c771f
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47408398"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Kurz: Vytvoření klienta služby Azure Active Directory B2C

Předtím, než aplikace můžete pracovat s Azure Active Directory (Azure AD) B2C, musí být zaregistrovaná v tenantovi, která spravujete.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojte svého tenanta se svým předplatným

Zjistíte, jak zaregistrovat aplikaci v dalším kurzu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje vaše předplatné po kliknutí **filtr adresářů a předplatných** v horní nabídce a výběrem adresáři, který jej obsahuje. Toto je jiný adresář než ten, který bude obsahovat vašeho tenanta Azure AD B2C.

    ![Přepnout na adresář předplatného](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Zvolte **vytvořit prostředek** v levém horním rohu webu Azure portal.
4. Vyhledejte a vyberte **Active Directory B2C**a potom klikněte na tlačítko **vytvořit**.
5. Zvolte **vytvořit nového Tenanta Azure AD B2C**, zadejte název organizace a počáteční název domény, který se používá v názvu tenanta, vyberte zemi (ho není možné později změnit) a potom klikněte na tlačítko **vytvořit**.

    ![Vytvoření tenanta](./media/tutorial-create-tenant/create-tenant.png)

    V tomto příkladu je název tenanta contoso0926Tenant.onmicrosoft.com

6. Na **vytvořit nového Tenanta B2C nebo propojit s Tenantem stávající** zvolte **Tenanta odkaz stávající službou Azure AD B2C s mým předplatným Azure**, vyberte tenanta, který jste vytvořili, vyberte své předplatné, klikněte na **Vytvořit nový** a zadejte název pro skupinu prostředků, která bude obsahovat tenanta, vyberte umístění a potom klikněte na **vytvořit**.
7. Pokud chcete začít používat nového tenanta, ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáři, který jej obsahuje.

    ![Přepnout do tenanta adresáře](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojte svého tenanta se svým předplatným

> [!div class="nextstepaction"]
> [Povolení ověřování účtů webové aplikace](active-directory-b2c-tutorials-web-app.md)