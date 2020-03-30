---
title: Kurz – vytvoření klienta Služby Azure Active Directory B2C
description: Zjistěte, jak se připravit na registraci aplikací vytvořením klienta Azure Active Directory B2C pomocí portálu Azure.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186399"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Kurz: Vytvoření tenanta Azure Active Directory B2C

Než vaše aplikace můžou pracovat s Azure Active Directory B2C (Azure AD B2C), musí být registrované v tenantovi, který spravujete.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojení klienta s předplatným
> * Přepnutí do adresáře obsahujícího vašeho klienta Azure AD B2C
> * Přidání prostředku Azure AD B2C jako **oblíbeného** na webu Azure Portal

Dozvíte se, jak zaregistrovat aplikaci v dalším kurzu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C

1. Přihlaste se k [portálu Azure](https://portal.azure.com/). Přihlaste se pomocí účtu Azure, který byl přiřazen alespoň roli [přispěvatele](../role-based-access-control/built-in-roles.md) v rámci předplatného nebo skupiny prostředků v rámci předplatného.

1. Vyberte adresář, který obsahuje vaše předplatné.

    Na panelu nástrojů Portálu Azure vyberte ikonu **Adresář + předplatné** a pak vyberte adresář, který obsahuje vaše předplatné. Tento adresář se liší od adresáře, který bude obsahovat vašeho klienta Azure AD B2C.

    ![Tenant předplatného, filtr Adresář + Odběr s vybraným klientem předplatného](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
1. Vyhledejte **službu Azure Active Directory B2C**a vyberte **příkaz Vytvořit**.
1. Vyberte **Vytvořit nového tenanta Azure AD B2C**.

    ![Vytvoření nového klienta Azure AD B2C vybraného na webu Azure Portal](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Zadejte **název organizace** a počáteční název **domény**. Vyberte **zemi nebo oblast** (nelze ji později změnit) a pak vyberte **Vytvořit**.

    Název domény se používá jako součást celého názvu domény klienta. V tomto příkladu je název klienta *contosob2c.onmicrosoft.com*:

    ![Vytvoření formuláře klienta pomocí ukázkových hodnot na webu Azure Portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. Po dokončení vytvoření klienta vyberte **vytvořit nového klienta B2C nebo odkaz na existující nájemce** odkaz v horní části stránky vytvoření klienta.

    ![Odkaz na odkaz popis cesty klienta zvýrazněný na webu Azure Portal](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Vyberte **Propojit existujícího klienta Azure AD B2C s mým předplatným Azure**.

   ![Propojení existujícího výběru předplatného na webu Azure Portal](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Vyberte **klienta Azure AD B2C,** který jste vytvořili, a vyberte **předplatné**.

    V poli **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte **název** skupiny prostředků, která bude obsahovat klienta, vyberte **umístění skupiny prostředků**a pak vyberte **Vytvořit**.

    ![Propojit formulář nastavení předplatného na webu Azure Portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    Můžete propojit více klientů Azure AD B2C s jedním předplatným Azure pro účely fakturace.

## <a name="select-your-b2c-tenant-directory"></a>Vyberte adresář klienta B2C.

Chcete-li začít používat nové hospo- klienta Azure AD B2C, musíte přepnout do adresáře, který obsahuje klienta.

Vyberte filtr **Directory + předplatné** v horní nabídce portálu Azure a vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.

Pokud se váš nový tenant Azure B2C v seznamu zpočátku nezobrazuje, aktualizujte okno prohlížeče a v horní nabídce znovu vyberte filtr **Directory + předplatného.**

![Adresář obsahující klienta B2C vybraný na webu Azure Portal](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Přidání Azure AD B2C jako oblíbené (volitelné)

Tento volitelný krok usnadňuje výběr klienta Azure AD B2C v následujících a všech následujících kurzech.

Místo hledání *Azure AD B2C* ve **všech službách** pokaždé, když chcete pracovat s tenantem, můžete místo toho oblíbené prostředek. Potom ji můžete vybrat z části **Oblíbené** nabídky portálu a rychle přejít do tenanta Azure AD B2C.

Tuto operaci je třeba provést pouze jednou. Před provedením těchto kroků se ujistěte, že jste přepnuli do adresáře obsahujícího klienta Azure AD B2C, jak je popsáno v předchozí části [Vyberte adresář klienta B2C](#select-your-b2c-tenant-directory).

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V nabídce Portál Azure vyberte **Všechny služby**.
1. Ve vyhledávacím poli **Všechny služby** vyhledejte **Azure AD B2C**, najeďte na výsledek hledání a vyberte ikonu hvězdičky v popisku. **Azure AD B2C** se teď zobrazí na webu Azure portal v části **Oblíbené položky**.
1. Pokud chcete změnit pozici nové oblíbené položky, přejděte do nabídky portálu Azure, vyberte **Azure AD B2C**a přetáhněte ji nahoru nebo dolů na požadovanou pozici.

    ![Azure AD B2C, nabídka Oblíbené položky, portál Microsoft Azure](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili:

> [!div class="checklist"]
> * Vytvoření tenanta Azure AD B2C
> * Propojení klienta s předplatným
> * Přepnutí do adresáře obsahujícího vašeho klienta Azure AD B2C
> * Přidání prostředku Azure AD B2C jako **oblíbeného** na webu Azure Portal

Dále se dozvíte, jak zaregistrovat webovou aplikaci v novém tenantovi.

> [!div class="nextstepaction"]
> [Zaregistrujte své žádosti >](tutorial-register-applications.md)
