---
title: Vytvoření & odstranění Azure AD B2C uživatelských účtů spotřebitele v Azure Portal
description: Naučte se používat Azure Portal k vytváření a odstraňování uživatelů uživatelů v adresáři Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d6d6169f8662c9b973fb7f624a590322f62b0b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387520"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Použití Azure Portal k vytváření a odstraňování uživatelů spotřebitelů v Azure AD B2C

Můžou nastat scénáře, ve kterých chcete ručně vytvořit uživatelské účty v adresáři Azure Active Directory B2C (Azure AD B2C). I když se uživatelům, kteří se přihlásí k používání jedné z vašich aplikací, často vytvářejí účty uživatelů Azure AD B2C, můžete je vytvořit prostřednictvím kódu programu a pomocí Azure Portal. Tento článek se zaměřuje na metodu Azure Portal vytvoření a odstranění uživatele.

Pokud chcete přidat nebo odstranit uživatele, musí mít váš účet přiřazenou roli *správce uživatele* nebo *globální správce* .

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Typy uživatelských účtů

Jak je popsáno v tématu [Přehled uživatelských účtů v Azure AD B2C](user-overview.md), existují tři typy uživatelských účtů, které se dají vytvořit v Azure AD B2Cm adresáři:

* Práce
* Host
* Příjemce

Tento článek se zaměřuje na práci s **uživatelskými účty** v Azure Portal. Informace o vytváření a odstraňování pracovních a hostovaných účtů najdete v tématu [Přidání nebo odstranění uživatelů pomocí Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Vytvoření uživatele spotřebitele

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Uživatelé**.
1. Vyberte **Nový uživatel**.
1. Vyberte **vytvořit Azure AD B2C uživatele**.
1. Vyberte **způsob přihlášení** a zadejte **e-mailovou** adresu nebo **uživatelské jméno** pro nového uživatele. Vámi zvolená metoda přihlašování se musí shodovat s nastavením, které jste zadali pro poskytovatele identity *místního účtu* klienta Azure AD B2C (viz **Správa**  >  **zprostředkovatelů identity** ve vašem tenantovi Azure AD B2C).
1. Zadejte **jméno** uživatele. Obvykle se jedná o celé jméno (zadané a příjmení) uživatele.
1. Volitelné Přihlášení můžete **zablokovat** , pokud chcete odložit možnost přihlášení uživatele. Přihlášení můžete povolit později úpravou **profilu** uživatele v Azure Portal.
1. Vyberte možnost **automaticky generovat heslo** nebo si **dejte vytvořit heslo**.
1. Zadejte **jméno** a **příjmení**uživatele.
1. Vyberte **Vytvořit**.

Pokud jste nevybrali možnost přihlásit se k **bloku**, uživatel se teď může přihlásit pomocí metody přihlašování (e-mailu nebo uživatelského jména), kterou jste zadali.

## <a name="delete-a-consumer-user"></a>Odstranění uživatele spotřebitele

1. V adresáři Azure AD B2C vyberte možnost **Uživatelé**a potom vyberte uživatele, kterého chcete odstranit.
1. Vyberte **Odstranit**a potvrďte odstranění potvrzením kliknutím na **Ano** .

Podrobnosti o obnovení uživatele během prvních 30 dnů po odstranění nebo při trvalém odstranění uživatele najdete v tématu [obnovení nebo odebrání nedávno odstraněného uživatele pomocí Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Další kroky

V případě automatizovaných scénářů správy uživatelů, například migrace uživatelů z jiného zprostředkovatele identity do adresáře Azure AD B2C, přečtěte si téma [Azure AD B2C: Migrace uživatelů](user-migration.md).
