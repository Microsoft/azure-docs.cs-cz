---
title: Kurz – vytvoření tenanta Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak připravit pro registraci vaší aplikace tak, že vytvoříte tenanta služby Azure Active Directory B2C na webu Azure portal.
services: B2C
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fe86541ed64d780c094eeb761053c76028b38862
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063604"
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

Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant. 

![Přepnutí na tenanta Azure AD B2C](./media/tutorial-create-tenant/switch-directories.png)

1. Zvolte **vytvořit prostředek** v levém horním rohu na webu Azure portal.
2. Do vyhledávacího pole výše v seznamu prostředků Azure Marketplace, vyhledejte a vyberte **Active Directory B2C**a potom klikněte na tlačítko **vytvořit**.
3. Zvolte **Tenanta odkaz stávající službou Azure AD B2C s mým předplatným Azure**, vyberte tenanta, který jste vytvořili, vyberte své předplatné, zadejte *myContosoTenantRG* pro název skupiny prostředků, přijměte umístění a pak klikněte na tlačítko **vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojte svého tenanta se svým předplatným

> [!div class="nextstepaction"]
> [Povolení ověřování účtů webové aplikace](active-directory-b2c-tutorials-web-app.md)