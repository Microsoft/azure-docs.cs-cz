---
title: Kurz – vytvoření tenanta Azure Active Directory B2C
description: Naučte se připravit registraci aplikací vytvořením klienta Azure Active Directory B2C pomocí Azure Portal.
services: B2C
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: de29929c8fda476fe276f91d4a68ce4d632503fd
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345209"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Kurz: Vytvoření tenanta Azure Active Directory B2C

Než můžou vaše aplikace pracovat s Azure Active Directory B2C (Azure AD B2C), musí být zaregistrované v tenantovi, které spravujete.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojení tenanta s předplatným
> * Přejděte do adresáře, který obsahuje Azure AD B2C tenanta.
> * Přidat prostředek Azure AD B2C jako *oblíbenou položku* do Azure Portal

Naučíte se, jak zaregistrovat aplikaci v dalším kurzu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vaše předplatné.

    V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vaše předplatné. Tento adresář je jiný než ten, který bude obsahovat vašeho tenanta Azure AD B2C.

    ![Adresář a filtr předplatných vybraný tenant předplatného](media/tutorial-create-tenant/portal-01-select-directory.png)

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek** .
1. Vyhledejte a vyberte **Active Directory B2C**a pak vyberte **vytvořit**.
1. Vyberte **Vytvořit nového tenanta Azure AD B2C**.

    ![Vytvoří nový tenant Azure AD B2C vybraný v Azure Portal.](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Zadejte **název organizace** a **počáteční název domény**. Vyberte **zemi nebo oblast** (nedá se změnit později) a pak vyberte **vytvořit**.

    Název domény se používá jako součást úplného názvu domény tenanta. V tomto příkladu je název tenanta *contosob2c.onmicrosoft.com*:

    ![Vytvořit formulář tenanta v s příklady hodnot v Azure Portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Po vytvoření tenanta vyberte odkaz **vytvořit nového TENANTA B2C nebo odkaz na existující tenanta** v horní části stránky pro vytvoření tenanta.

    ![Odkaz na odkaz s popisem klienta v Azure Portal zvýrazněn](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Vyberte **propojit existujícího tenanta Azure AD B2C s předplatným Azure**.

   ![Propojit existující výběr předplatného v Azure Portal](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Vyberte **klienta Azure AD B2C** , kterého jste vytvořili, a pak vyberte své **předplatné**.

    V poli **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte **název** skupiny prostředků, která bude obsahovat tenanta, vyberte **umístění skupiny prostředků**a pak vyberte **vytvořit**.

    ![Odkaz na formulář nastavení předplatného v Azure Portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

## <a name="select-your-b2c-tenant-directory"></a>Vyberte adresář tenanta B2C

Pokud chcete začít používat nový tenant Azure AD B2C, musíte přepnout do adresáře, který obsahuje tenanta.

V horní nabídce Azure Portal vyberte filtr **adresář + odběr** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

Pokud se v seznamu nezobrazuje nový tenant Azure B2C, aktualizujte okno prohlížeče a potom v horní nabídce znovu vyberte filtr **adresář + odběr** .

![B2C je vybraný tenant, který obsahuje adresář vybraný v Azure Portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Přidat Azure AD B2C jako oblíbenou položku (volitelné)

Tento volitelný krok usnadňuje výběr klienta Azure AD B2C v následujících a všech dalších kurzech.

Místo hledání "Azure AD B2C" ve **všech službách** pokaždé, když chcete ve svém tenantovi pracovat, můžete místo toho prostředek přidružit. Pak ho můžete vybrat **v nabídce na levé straně a** rychle přejít k vašemu Azure AD B2C tenantovi.

Tuto operaci je nutné provést pouze jednou. Před provedením tohoto postupu se ujistěte, že jste přešli do adresáře, který obsahuje Azure AD B2C tenanta, jak je popsáno v předchozí části, a [Vyberte svůj adresář TENANTA B2C](#select-your-b2c-tenant-directory).

1. V levé nabídce [Azure Portal](https://portal.azure.com) vyberte **všechny služby** .
1. Do textového pole Hledat zadejte *Azure AD B2C*
1. Výběr **hvězdičky** pro přidání Azure AD B2C k oblíbeným položkám
1. *Azure AD B2C* se nyní zobrazí v **nabídce vlevo.** Pokud chcete, můžete vybrat a přetáhnout ho v seznamu nahoru, jak ukazuje následující obrázek:

![Postup přidání Azure AD B2C jako oblíbené položky v Azure Portal](media/tutorial-create-tenant/portal-08-favorite-b2c.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojení tenanta s předplatným
> * Přejděte do adresáře, který obsahuje Azure AD B2C tenanta.
> * Přidat prostředek Azure AD B2C jako *oblíbenou položku* do Azure Portal

V dalším kroku se dozvíte, jak zaregistrovat webovou aplikaci v novém tenantovi.

> [!div class="nextstepaction"]
> [Zaregistrujte své aplikace >](tutorial-register-applications.md)
