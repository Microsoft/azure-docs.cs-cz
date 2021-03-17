---
title: Vytvoření & odstranění Azure AD B2C uživatelských účtů spotřebitele v Azure Portal
description: Naučte se používat Azure Portal k vytváření a odstraňování uživatelů uživatelů v adresáři Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ace0ccb8372ff21a2d3e8721baf09bab539846c2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033656"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Použití Azure Portal k vytváření a odstraňování uživatelů spotřebitelů v Azure AD B2C

Můžou nastat scénáře, ve kterých chcete ručně vytvořit uživatelské účty v adresáři Azure Active Directory B2C (Azure AD B2C). I když se uživatelům, kteří se přihlásí k používání jedné z vašich aplikací, často vytvářejí účty uživatelů Azure AD B2C, můžete je vytvořit prostřednictvím kódu programu a pomocí Azure Portal. Tento článek se zaměřuje na metodu Azure Portal vytvoření a odstranění uživatele.

Pokud chcete přidat nebo odstranit uživatele, musí mít váš účet přiřazenou roli *správce uživatele* nebo *globální správce* .

## <a name="types-of-user-accounts"></a>Typy uživatelských účtů

Jak je popsáno v tématu [Přehled uživatelských účtů v Azure AD B2C](user-overview.md), existují tři typy uživatelských účtů, které se dají vytvořit v Azure AD B2Cm adresáři:

* Práce
* Host
* Příjemce

Tento článek se zaměřuje na práci s **uživatelskými účty** v Azure Portal. Informace o vytváření a odstraňování pracovních a hostovaných účtů najdete v tématu [Přidání nebo odstranění uživatelů pomocí Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Vytvoření uživatele spotřebitele

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Uživatelé**.
1. Vyberte **Nový uživatel**.
1. Vyberte **vytvořit Azure AD B2C uživatele**.
1. Vyberte **způsob přihlášení** a zadejte **e-mailovou** adresu nebo **uživatelské jméno** pro nového uživatele. Vámi zvolená metoda přihlašování se musí shodovat s nastavením, které jste zadali pro poskytovatele identity *místního účtu* klienta Azure AD B2C (viz **Správa**  >  **zprostředkovatelů identity** ve vašem tenantovi Azure AD B2C).
1. Zadejte **jméno** uživatele. Obvykle se jedná o celé jméno (zadané a příjmení) uživatele.
1. Volitelné Přihlášení můžete **zablokovat** , pokud chcete odložit možnost přihlášení uživatele. Přihlášení můžete povolit později úpravou **profilu** uživatele v Azure Portal.
1. Vyberte možnost **generovat heslo** nebo chci **vytvořit heslo**.
1. Zadejte **jméno** a **příjmení** uživatele.
1. Vyberte **Vytvořit**.

Pokud jste nevybrali možnost přihlásit se k **bloku**, uživatel se teď může přihlásit pomocí metody přihlašování (e-mailu nebo uživatelského jména), kterou jste zadali.

## <a name="reset-a-users-password"></a>Resetování hesla uživatele

Jako správce můžete resetovat heslo uživatele, pokud uživatel zapomene heslo. Když resetujete heslo uživatele, automaticky se vygeneruje dočasné heslo pro uživatele. Dočasné heslo je platné i po vypršení platnosti. Až se uživatel příště přihlásí, heslo bude pořád fungovat bez ohledu na to, kolik času uplynulo od vygenerování dočasného hesla. Uživatel pak musí heslo resetovat na trvalé. 

> [!IMPORTANT]
> Než resetujete heslo uživatele, [nastavte v Azure Active Directory B2C vynucený tok resetování hesla](force-password-reset.md), jinak se uživatel nebude moci přihlásit.

Resetování hesla uživatele:

1. V adresáři Azure AD B2C vyberte možnost **Uživatelé** a potom vyberte uživatele, jehož heslo chcete resetovat.
1. Vyhledejte a vyberte uživatele, který vyžaduje resetování, a pak vyberte **resetovat heslo**.

    Zobrazí se stránka **Alain Charon-Profile** s možností **resetovat heslo** .

    ![Stránka profilu uživatele s zvýrazněnou možností resetovat heslo](media/manage-users-portal/user-profile-reset-password-link.png)

1. Na stránce **resetovat heslo** vyberte **resetovat heslo**.
1. Zkopírujte heslo a poskytněte ho uživateli. Uživatel bude muset změnit heslo při příštím procesu přihlašování.


## <a name="delete-a-consumer-user"></a>Odstranění uživatele spotřebitele

1. V adresáři Azure AD B2C vyberte možnost **Uživatelé** a potom vyberte uživatele, kterého chcete odstranit.
1. Vyberte **Odstranit** a potvrďte odstranění potvrzením kliknutím na **Ano** .

Podrobnosti o obnovení uživatele během prvních 30 dnů po odstranění nebo při trvalém odstranění uživatele najdete v tématu [obnovení nebo odebrání nedávno odstraněného uživatele pomocí Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Další kroky

V případě automatizovaných scénářů správy uživatelů, například migrace uživatelů z jiného zprostředkovatele identity do adresáře Azure AD B2C, přečtěte si téma [Azure AD B2C: Migrace uživatelů](user-migration.md).
