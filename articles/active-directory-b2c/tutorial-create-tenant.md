---
title: Kurz – vytvoření tenanta Azure Active Directory B2C
description: V tomto kurzu se dozvíte, jak připravit registraci aplikací vytvořením klienta Azure Active Directory B2C pomocí Azure Portal.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 673e2260f358332de00a15b29729f14d62fe82a4
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518003"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Kurz: Vytvoření tenanta Azure Active Directory B2C

Než můžou vaše aplikace pracovat s Azure Active Directory B2C (Azure AD B2C), musí být zaregistrované v tenantovi, které spravujete. 

> [!NOTE]
> U každého předplatného můžete vytvořit až 20 klientů. Toto omezení pomáhá chránit před hrozbami vašich prostředků, jako jsou útoky DoS (Denial-of-Service), a je vynutilo jak Azure Portal, tak i základní rozhraní API pro vytváření tenantů. Pokud potřebujete vytvořit více než 20 tenantů, kontaktujte prosím [Podpora Microsoftu](support-options.md).
> 
> Pokud chcete znovu použít název tenanta, který jste se předtím pokusili odstranit, ale při zadání názvu domény se zobrazí chyba "už se používá v jiném adresáři", budete muset [nejprve tento tenant úplně odstranit pomocí těchto kroků](https://docs.microsoft.com/azure/active-directory-b2c/faq?tabs=app-reg-ga#how-do-i-delete-my-azure-ad-b2c-tenant). Je vyžadována role aspoň Správce předplatného. Po odstranění tenanta se možná budete muset odhlásit a znovu přihlásit a teprve potom můžete znovu použít název domény.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojení tenanta s předplatným
> * Přejděte do adresáře, který obsahuje Azure AD B2C tenanta.
> * Přidat prostředek Azure AD B2C jako **oblíbenou položku** do Azure Portal

Naučíte se, jak zaregistrovat aplikaci v dalším kurzu.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

1. Přihlaste se na [Azure Portal](https://portal.azure.com/). Přihlaste se pomocí účtu Azure, kterému byla přiřazena alespoň role [Přispěvatel](../role-based-access-control/built-in-roles.md) v rámci předplatného nebo skupiny prostředků v rámci předplatného.

1. Vyberte adresář, který obsahuje vaše předplatné.

    Na panelu nástrojů Azure Portal vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vaše předplatné. Tento adresář je jiný než ten, který bude obsahovat vašeho tenanta Azure AD B2C.

    ![Tenant předplatného, adresář a filtr předplatných vybraný tenant předplatného](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.

   ![Vyberte tlačítko vytvořit prostředek.](media/tutorial-create-tenant/create-a-resource.png)

1. Vyhledejte **Azure Active Directory B2C** a pak vyberte **vytvořit**.
2. Vyberte **Vytvořit nového tenanta Azure AD B2C**.

    ![Vytvoří nový tenant Azure AD B2C vybraný v Azure Portal.](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Na stránce **vytvořit adresář** zadejte následující:

   - **Název organizace** – zadejte název vašeho tenanta Azure AD B2C.
   - **Počáteční název domény** – zadejte název domény pro vašeho tenanta Azure AD B2C.
   - **Země nebo oblast** – ze seznamu vyberte zemi nebo oblast. Tento výběr nelze později změnit.
   - **Předplatné** – ze seznamu vyberte své předplatné.
   - **Skupina prostředků** – vyberte nebo vyhledejte skupinu prostředků, která bude obsahovat daného tenanta.

    ![Vytvořit formulář tenanta v s příklady hodnot v Azure Portal](media/tutorial-create-tenant/review-and-create-tenant.png)

1. Vyberte **Zkontrolovat a vytvořit**.
1. Zkontrolujte nastavení adresáře. Potom vyberte **Vytvořit**. Pro [řešení chyb při nasazení](../azure-resource-manager/templates/common-deployment-errors.md).

Pro účely fakturace můžete propojit více Azure AD B2C tenantů s jedním předplatným Azure. Pokud chcete propojit tenanta, musíte být správcem v Azure AD B2C tenant a mít v rámci předplatného Azure přiřazenou aspoň roli Přispěvatel. Viz [propojení klienta Azure AD B2C k předplatnému](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription).

## <a name="select-your-b2c-tenant-directory"></a>Vyberte adresář tenanta B2C

Pokud chcete začít používat nový tenant Azure AD B2C, musíte přepnout do adresáře, který obsahuje tenanta.

V horní nabídce Azure Portal vyberte filtr **adresář + odběr** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

Pokud se v seznamu nezobrazuje nový tenant Azure B2C, aktualizujte okno prohlížeče a potom v horní nabídce znovu vyberte filtr **adresář + odběr** .

![B2C je vybraný tenant, který obsahuje adresář vybraný v Azure Portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Přidat Azure AD B2C jako oblíbenou položku (volitelné)

Tento volitelný krok usnadňuje výběr klienta Azure AD B2C v následujících a všech dalších kurzech.

Místo hledání *Azure AD B2C* ve **všech službách** pokaždé, když chcete s vaším klientem pracovat, můžete místo toho prostředek přidružit. Pak ho můžete vybrat z oddílu **Oblíbené** nabídky na portálu, abyste mohli rychle přejít k vašemu Azure AD B2C tenantovi.

Tuto operaci je nutné provést pouze jednou. Před provedením tohoto postupu se ujistěte, že jste přešli do adresáře, který obsahuje Azure AD B2C tenanta, jak je popsáno v předchozí části, a [Vyberte svůj adresář TENANTA B2C](#select-your-b2c-tenant-directory).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V nabídce Azure Portal vyberte **všechny služby**.
1. Ve vyhledávacím poli **všechny služby** vyhledejte **Azure AD B2C**, najeďte myší na výsledek hledání a potom vyberte ikonu hvězdičky v popisku. **Azure AD B2C** se nyní zobrazí v Azure Portal v části **Oblíbené**.
1. Pokud chcete změnit pozici vaší nové oblíbené položky, přejděte do nabídky Azure Portal, vyberte **Azure AD B2C** a přetáhněte ji nahoru nebo dolů na požadovanou pozici.

    ![Nabídka Azure AD B2C, oblíbené položky portál Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojení tenanta s předplatným
> * Přejděte do adresáře, který obsahuje Azure AD B2C tenanta.
> * Přidat prostředek Azure AD B2C jako **oblíbenou položku** do Azure Portal

V dalším kroku se dozvíte, jak zaregistrovat webovou aplikaci v novém tenantovi.

> [!div class="nextstepaction"]
> [Zaregistrujte své aplikace >](tutorial-register-applications.md)
