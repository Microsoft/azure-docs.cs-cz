---
title: Vytvoření & odstranění uživatelských účtů azure ad b2c na webu Azure Portal
description: Zjistěte, jak pomocí portálu Azure vytvářet a odstraňovat spotřebitele uživatelů v adresáři Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187215"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Vytvoření a odstranění uživatelů spotřebitelských služeb ve Službě Azure AD B2C pomocí portálu Azure Portal

Mohou existovat scénáře, ve kterých chcete ručně vytvořit spotřebitelské účty v adresáři Azure Active Directory B2C (Azure AD B2C). I když spotřebitelské účty v adresáři Azure AD B2C se nejčastěji vytvářejí, když se uživatelé zaregistrují k použití jedné z vašich aplikací, můžete je vytvořit programově a pomocí portálu Azure. Tento článek se zaměřuje na metodu Azure portal vytváření a odstraňování uživatelů.

Chcete-li přidat nebo odstranit uživatele, musí být vašemu účtu přiřazena role *Správce uživatele* nebo *Globální správce.*

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>Typy uživatelských účtů

Jak je popsáno v [přehledu uživatelských účtů v Azure AD B2C](user-overview.md), existují tři typy uživatelských účtů, které lze vytvořit v adresáři Azure AD B2C:

* Práce
* Host
* Spotřebitele

Tento článek se zaměřuje na práci s **účty spotřebitelů** na webu Azure Portal. Informace o vytváření a odstraňování účtů Práce a Host najdete v tématu [Přidání nebo odstranění uživatelů pomocí služby Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Vytvoření spotřebitele uživatele

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. V části **Spravovat** vyberte **Uživatele**.
1. Vyberte **nový uživatel**.
1. Vyberte **Vytvořit uživatele Azure AD B2C**.
1. Zvolte **metodu Přihlášení** a zadejte **e-mailovou** adresu nebo **uživatelské jméno** pro nového uživatele. Metoda přihlášení, kterou zde vyberete, se musí shodovat s nastavením, které jste zadali pro místní poskytovatele identity *účtu* vašeho klienta Azure AD B2C (viz **Správa** > **poskytovatelů identit y** ve vašem tenantovi Azure AD B2C).
1. Zadejte **název** uživatele. Obvykle se jedná o celé jméno (uvedené a příjmení) uživatele.
1. (Nepovinné) Pokud chcete uživatele odložit možnost přihlášení, můžete **se přihlásit.** Přihlášení můžete povolit později úpravou **profilu** uživatele na webu Azure Portal.
1. Zvolte **Automaticky generovat heslo** nebo **Dovolte mi vytvořit heslo**.
1. Zadejte **jméno** uživatele a **příjmení**.
1. Vyberte **Vytvořit**.

Pokud jste nevybrali **možnost Blokovat přihlášení**, uživatel se teď může přihlásit pomocí přihlašovací metody (e-mailu nebo uživatelského jména), kterou jste zadali.

## <a name="delete-a-consumer-user"></a>Odstranění spotřebitele

1. V adresáři Azure AD B2C vyberte **Uživatelé**a vyberte uživatele, kterého chcete odstranit.
1. Chcete-li odstranění potvrdit, vyberte **možnost Odstranit**a potom **ano.**

Podrobnosti o obnovení uživatele během prvních 30 dnů po odstranění nebo o trvalém odstranění uživatele naleznete v [tématu Obnovení nebo odebrání naposledy odstraněného uživatele pomocí služby Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Další kroky

Scénáře automatizované správy uživatelů, například migrace uživatelů z jiného poskytovatele identity do adresáře Azure AD B2C, najdete [v tématu Azure AD B2C: Migrace uživatelů](user-migration.md).
