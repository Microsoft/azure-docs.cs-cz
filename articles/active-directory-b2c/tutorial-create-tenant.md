---
title: Kurz – vytvoření tenanta Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak připravit pro registraci vaší aplikace tak, že vytvoříte tenanta služby Azure Active Directory B2C na webu Azure portal.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc48cd3eb40d93c26a68caf843a89f7bbfb46c6c
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236889"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Kurz: Vytvoření klienta služby Azure Active Directory B2C

Předtím, než aplikace můžete pracovat s Azure Active Directory (Azure AD) B2C, musí být zaregistrovaná v tenantovi, která spravujete.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojte svého tenanta se svým předplatným

Zjistíte, jak zaregistrovat aplikaci v dalším kurzu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

1. Zvolte **vytvořit prostředek** v levém horním rohu webu Azure portal.
2. Do vyhledávacího pole výše v seznamu prostředků Azure Marketplace, vyhledejte a vyberte **Active Directory B2C**a potom klikněte na tlačítko **vytvořit**.
3. Zvolte **vytvořit nového Tenanta Azure AD B2C**, zadejte název organizace a počáteční název domény, který se používá v názvu tenanta, vyberte zemi a potom klikněte na **vytvořit**. Vědět jistě zemi tenanta, protože není možné později změnit.

    ![Vytvoření tenanta](./media/tutorial-create-tenant/create-tenant.png)

    V tomto příkladu je název tenanta contoso0522Tenant.onmicrosoft.com

Pokud chcete začít, Správa nového tenanta, klikněte na slovo **tady** kde říká **klikněte sem, chcete-li spravovat svůj nový adresář**. Zobrazí se **Poradce při potížích** zpráva, že budete potřebovat odkázat předplatného do nového tenanta. 

## <a name="link-your-tenant-to-your-subscription"></a>Propojte svého tenanta se svým předplatným

Potřebujete propojit vašeho tenanta Azure AD B2C s předplatným Azure, aby se povolily všechny funkce a poplatky za využívání. Pokud váš tenant není propojení s předplatným, vaše aplikace nebude fungovat správně.

1. Ujistěte se, že používáte adresář obsahující předplatné, které chcete přidružit k novému klientovi přepnutím adresáře v pravém horním rohu webu Azure portal.

    ![Přepnout adresáře](./media/tutorial-create-tenant/switch-directories.png)

    A pak vyberte adresář, který obsahuje vaše předplatné.

    ![Výběr adresáře](./media/tutorial-create-tenant/select-directory.png)

2. Zvolte **vytvořit prostředek** v levém horním rohu na webu Azure portal.
3. Do vyhledávacího pole výše v seznamu prostředků Azure Marketplace, vyhledejte a vyberte **Active Directory B2C**a potom klikněte na tlačítko **vytvořit**.
4. Zvolte **Tenanta odkaz stávající službou Azure AD B2C s mým předplatným Azure**, vyberte tenanta, který jste vytvořili, vyberte své předplatné, zadejte *myContosoTenantRG* pro název skupiny prostředků, přijměte umístění a pak klikněte na tlačítko **vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojte svého tenanta se svým předplatným

> [!div class="nextstepaction"]
> [Povolení ověřování účtů webové aplikace](active-directory-b2c-tutorials-web-app.md)