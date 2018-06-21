---
title: Kurz – vytvoření klienta Azure Active Directory B2C | Microsoft Docs
description: Zjistěte, jak připravit pro registraci aplikace tak, že vytvoříte klienta služby Azure Active Directory B2C pomocí portálu Azure.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/19/2018
ms.author: davidmu
ms.openlocfilehash: 04f3dbbe461bfe0f07b6930a92bdd8a721e55098
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296096"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Kurz: Vytvoření klienta Azure Active Directory B2C

Předtím, než je vaše aplikace mohou komunikovat s Azure Active Directory (Azure AD) B2C, musí být zaregistrován v klientovi, které spravujete.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Odkaz vašeho klienta do vašeho předplatného

Zjistíte, jak zaregistrovat aplikaci v dalším kurzu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

1. Zvolte **vytvořit prostředek** v levém horním rohu portálu Azure.
2. Do vyhledávacího pole výše v seznamu prostředků Azure Marketplace vyhledejte a vyberte **Active Directory B2C**a potom klikněte na **vytvořit**.
3. Zvolte **vytvořit nového klienta Azure AD B2C**, zadejte název organizace a počáteční název domény, který je používán název klienta, vyberte zemi a pak klikněte na tlačítko **vytvořit**. Ujistěte se, země klienta vzhledem k tomu, že není možné později změnit.

    ![Vytvoření tenanta](./media/tutorial-create-tenant/create-tenant.png)

    V tomto příkladu je název klienta contoso0522Tenant.onmicrosoft.com

Abyste mohli začít spravovat nového klienta, klikněte na slovo **sem** kde uvádí **klikněte sem, ke správě nový adresář**. Zobrazí se **Poradce při potížích** zpráva, že potřebujete předplatné propojit k novému klientovi. 

## <a name="link-your-tenant-to-your-subscription"></a>Odkaz vašeho klienta do vašeho předplatného

Potřebujete připojit k předplatnému Azure povolit všechny funkce a platit poplatky za používání klienta služby Azure AD B2C. Pokud váš klient není odkaz na vaše předplatné, vaše aplikace nebude fungovat správně.

1. Ujistěte se, že používáte adresáři, který obsahuje předplatné, které chcete přidružit k novému klientovi podle přepínání adresáře v pravém horním rohu portálu Azure.

    ![Přepnout adresáře](./media/tutorial-create-tenant/switch-directories.png)

    A pak vyberte adresář, který obsahuje vaše předplatné.

    ![Výběr adresáře](./media/tutorial-create-tenant/select-directory.png)

2. Zvolte **vytvořit prostředek** v levém horním rohu portálu Azure.
3. Do vyhledávacího pole výše v seznamu prostředků Azure Marketplace vyhledejte a vyberte **Active Directory B2C**a potom klikněte na **vytvořit**.
4. Zvolte **odkaz existující Azure AD B2C klienta pro Moje předplatné**vyberte klienta, který jste vytvořili, vyberte předplatné, zadejte *myContosoTenantRG* pro název skupiny prostředků, přijměte umístění a pak klikněte na tlačítko **vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Odkaz vašeho klienta do vašeho předplatného

> [!div class="nextstepaction"]
> [Povolit webových aplikací k ověření s účty](active-directory-b2c-tutorials-web-app.md)