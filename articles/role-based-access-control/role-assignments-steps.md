---
title: Postup přiřazení role Azure – Azure RBAC
description: Naučte se, jak přiřadit role Azure uživatelům, skupinám, instančním objektům nebo spravovaným identitám pomocí řízení přístupu na základě role Azure (RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 081335779ffc4b3a6ddf09e56b773c6d34b210be
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100556032"
---
# <a name="steps-to-assign-an-azure-role"></a>Postup přiřazení role Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Tento článek popisuje kroky vysoké úrovně k přiřazení rolí Azure pomocí [Azure Portal](role-assignments-portal.md), [Azure PowerShell](role-assignments-powershell.md), rozhraní příkazového [řádku Azure](role-assignments-cli.md)nebo [REST API](role-assignments-rest.md).

## <a name="step-1-determine-who-needs-access"></a>Krok 1: určení toho, kdo potřebuje přístup

Nejdřív je potřeba určit, kdo potřebuje přístup. Roli můžete přiřadit uživateli, skupině, objektu služby nebo spravované identitě. Označuje se taky jako *objekt zabezpečení*.

![Objekt zabezpečení pro přiřazení role](./media/shared/rbac-security-principal.png)

- Uživatel – jednotlivec, který má profil ve službě Azure Active Directory. Můžete také přiřadit role uživatelům v jiných tenantech. Informace o uživatelích v jiných organizacích najdete v článku o [B2B ve službě Azure Active Directory](../active-directory/external-identities/what-is-b2b.md).
- Skupina – skupina uživatelů vytvořená ve službě Azure Active Directory. Když přiřadíte roli skupině, budou mít danou roli všichni její uživatelé. 
- Instanční objekt – identita zabezpečení, kterou používají aplikace nebo služby pro přístup ke konkrétním prostředkům Azure. Můžete si ji představit jako *identitu uživatele* (uživatelské jméno a heslo nebo certifikát) pro aplikaci.
- Spravovaná identita – identita v Azure Active Directory, kterou automaticky spravuje Azure. [Spravované identity](../active-directory/managed-identities-azure-resources/overview.md) obvykle používáte při vývoji cloudových aplikací pro správu přihlašovacích údajů pro ověřování ve službách Azure.

## <a name="step-2-select-the-appropriate-role"></a>Krok 2: vyberte příslušnou roli.

Oprávnění se seskupují do *definice role*. Obvykle se nazývá jenom *role*. Můžete vybrat ze seznamu několika předdefinovaných rolí. Pokud předdefinované role nesplňují konkrétní požadavky vaší organizace, můžete si vytvořit vlastní role.

![Definice role pro přiřazení role](./media/shared/rbac-role-definition.png)

V následujícím seznamu najdete čtyři základní předdefinované role. První tři se vztahují ke všem typům prostředků.

- [Vlastník](built-in-roles.md#owner) – má plný přístup ke všem prostředkům, včetně práva delegovat přístup na ostatní uživatele.
- [Přispěvatel](built-in-roles.md#contributor) – může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním.
- [Čtenář](built-in-roles.md#reader) – může zobrazit existující prostředky Azure.
- [Správce uživatelských přístupů](built-in-roles.md#user-access-administrator) – může spravovat uživatelský přístup k prostředkům Azure.

Zbývající předdefinované role umožňují správu konkrétních prostředků Azure. Role [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) například uživateli umožňuje vytvářet a spravovat virtuální počítače.

1. Začněte úplným článkem, [integrovanými rolemi Azure](built-in-roles.md). Tabulka v horní části článku je indexem podrobností dále v článku.

1. V tomto článku přejděte do kategorie služby (například výpočetní prostředky, úložiště a databáze) pro prostředek, ke kterému chcete udělit oprávnění. Nejjednodušší způsob, jak najít hledání, je obvykle vyhledat na stránce příslušné klíčové slovo, jako je například "blob", "virtuální počítač" atd.

1. Zkontrolujte role uvedené pro kategorii služby a určete konkrétní operace, které potřebujete. Znovu vždy zahajte s největším omezením role.

    Pokud například objekt zabezpečení potřebuje číst objekty BLOB v účtu služby Azure Storage, ale nepotřebuje přístup pro zápis, pak místo [přispěvatele dat objektu BLOB](built-in-roles.md#storage-blob-data-contributor) úložiště (a jednoznačně ne role [vlastníka dat objektu BLOB úložiště](built-in-roles.md#storage-blob-data-owner) na úrovni správce) zvolte možnost [čtecí modul úložiště dat objektů](built-in-roles.md#storage-blob-data-reader) BLOB. Přiřazení rolí můžete kdykoli aktualizovat později podle potřeby.

1. Pokud nenajdete vhodnou roli, můžete vytvořit [vlastní roli](custom-roles.md).

## <a name="step-3-identify-the-needed-scope"></a>Krok 3: určení potřebného oboru

*Obor* je sada prostředků, na které se vztahuje přístup. V Azure můžete zadat obor na čtyřech úrovních: [skupina pro správu](../governance/management-groups/overview.md), předplatné, [Skupina prostředků](../azure-resource-manager/management/overview.md#resource-groups)a prostředek. Obory jsou strukturovány ve vztahu nadřazený-podřízený obor. Jednotlivé úrovně hierarchie usnadňují rozsah konkrétního rozsahu. Role můžete přiřadit na kterékoli z těchto úrovní rozsahu. Úroveň, kterou vyberete, určuje, jak široce je role použita. Nižší úrovně dědí oprávnění role z vyšších úrovní. 

![Obor přiřazení role](./media/shared/rbac-scope.png)

Když přiřadíte roli v nadřazeném oboru, budou tato oprávnění děděna do podřízených oborů. Příklad:

- Pokud přiřadíte roli [Čtenář](built-in-roles.md#reader) k uživateli v oboru skupiny pro správu, může tento uživatel číst vše ve všech předplatných ve skupině pro správu.
- Pokud přiřadíte roli [Čtenář fakturace](built-in-roles.md#billing-reader) skupině v oboru předplatného, členové této skupiny mohou číst fakturační data pro každou skupinu prostředků a prostředek v rámci předplatného.
- Pokud přiřadíte roli [Přispěvatel](built-in-roles.md#contributor) aplikaci v oboru skupiny prostředků, může aplikace spravovat prostředky všech typů v dané skupině prostředků, ale už ne žádné jiné skupiny prostředků v předplatném.

 Další informace najdete v tématu [vysvětlení oboru](scope-overview.md).

## <a name="step-4-check-your-prerequisites"></a>Krok 4: Ověřit požadavky

Chcete-li přiřadit role, musíte být přihlášeni pomocí uživatele, kterému je přiřazena role s oprávněním k zápisu, jako je [vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatele](built-in-roles.md#user-access-administrator) v oboru, ke kterému se snažíte přiřadit roli. Podobně pokud chcete odebrat přiřazení role, musíte mít oprávnění odstranit přiřazení rolí.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Pokud váš uživatelský účet nemá oprávnění k přiřazení role v rámci vašeho předplatného, zobrazí se chybová zpráva, že váš účet nemá oprávnění k provedení akce Microsoft. Authorization/roleAssignments/Write. V takovém případě kontaktujte správce předplatného, aby k němu mohli přiřazovat oprávnění vaším jménem.

## <a name="step-5-assign-role"></a>Krok 5. Přiřazení role

Jakmile budete znát objekt zabezpečení, roli a rozsah, můžete roli přiřadit. Role můžete přiřadit pomocí Azure Portal, Azure PowerShell, Azure CLI, Azure SDK nebo rozhraní REST API. V každém předplatném můžete mít až **2000** přiřazení rolí. Toto omezení zahrnuje přiřazení rolí v rámci předplatného, skupiny prostředků a oborů prostředků. V každé skupině pro správu můžete mít až **500** přiřazení rolí.

Podrobné pokyny k přiřazení rolí najdete v následujících článcích.

- [Přiřazení rolí Azure pomocí Azure Portal](role-assignments-portal.md)
- [Přiřazení rolí Azure pomocí Azure PowerShell](role-assignments-powershell.md)
- [Přiřazení rolí Azure pomocí Azure CLI](role-assignments-cli.md)
- [Přiřazení rolí Azure pomocí REST API](role-assignments-rest.md)

## <a name="next-steps"></a>Další kroky

- [Kurz: udělení přístupu uživatele k prostředkům Azure pomocí Azure Portal](quickstart-assign-role-user-portal.md)