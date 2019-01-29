---
title: Kurz – vytvoření tenanta Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak připravit pro registraci vaší aplikace tak, že vytvoříte tenanta služby Azure Active Directory B2C na webu Azure portal.
services: B2C
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b8878eccb079bf78c45ff9c1e4040659d109b1ab
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152922"
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
2. Ujistěte se, že používáte adresáře, který obsahuje vaše předplatné po kliknutí **filtr adresářů a předplatných** v horní nabídce a výběrem adresáři, který jej obsahuje. Tento adresář je jiný než ten, který bude obsahovat vašeho tenanta Azure AD B2C.

    ![Přepnout na adresář předplatného](./media/tutorial-create-tenant/switch-directory-subscription.png)

3. Zvolte **vytvořit prostředek** v levém horním rohu webu Azure portal.
4. Vyhledejte a vyberte **Active Directory B2C**a potom klikněte na tlačítko **vytvořit**.
5. Zvolte **vytvořit nového Tenanta Azure AD B2C**, zadejte název organizace a počáteční název domény, který se používá v názvu tenanta, vyberte zemi (ho není možné později změnit) a potom klikněte na tlačítko **vytvořit**.

    ![Vytvoření tenanta](./media/tutorial-create-tenant/create-tenant.png)

    V tomto příkladu je název tenanta contoso0926Tenant.onmicrosoft.com

6. Na **vytvořit nového Tenanta B2C nebo propojit s Tenantem stávající** zvolte **Tenanta odkaz stávající službou Azure AD B2C s mým předplatným Azure**, vyberte tenanta, který jste vytvořili, vyberte své předplatné a pak Klikněte na tlačítko **vytvořit nový**.
7. Zadejte název pro skupinu prostředků, která bude obsahovat tenanta, vyberte umístění a potom klikněte na **vytvořit**.
8. Pokud chcete začít používat nového tenanta, ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáři, který jej obsahuje.

    ![Přepnout do tenanta adresáře](./media/tutorial-create-tenant/switch-directories.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojte svého tenanta se svým předplatným

> [!div class="nextstepaction"]
> [Registrace aplikace](tutorial-register-applications.md)
