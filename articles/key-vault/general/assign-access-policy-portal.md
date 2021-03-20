---
title: Přiřazení zásad Azure Key Vault přístupu (portál)
description: Jak použít Azure Portal k přiřazení zásady přístupu Key Vault k instančnímu objektu nebo identitě aplikace.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 910b8dae10036cc2e396be13495fd28363dc971d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934556"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Přiřazení zásady přístupu Key Vault pomocí Azure Portal

Zásada přístupu Key Vault určuje, jestli daný instanční objekt, konkrétně aplikace nebo skupina uživatelů, může provádět různé operace s Key Vault [tajné klíče](../secrets/index.yml), [klíče](../keys/index.yml)a [certifikáty](../certificates/index.yml). Zásady přístupu můžete přiřadit pomocí Azure Portal (Tento článek), rozhraní příkazového [řádku Azure](assign-access-policy-cli.md)nebo [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Další informace o vytváření skupin v Azure Active Directory prostřednictvím Azure Portal najdete v tématu [Vytvoření základní skupiny a přidání členů](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) .

## <a name="assign-an-access-policy"></a>Přiřazení zásad přístupu

1.  V [Azure Portal](https://portal.azure.com)přejděte na prostředek Key Vault. 

1.  V části **Nastavení** vyberte **zásady přístupu** a pak vyberte **Přidat zásady přístupu**:

    ![Vyberte zásady přístupu, vyberte přidat přiřazení role.](../media/authentication/assign-policy-portal-01.png)

1.  Vyberte požadovaná oprávnění v části **oprávnění certifikátu**, **oprávnění klíče** a **tajná oprávnění**. Můžete také vybrat šablonu obsahující společné kombinace oprávnění:

    ![Určení oprávnění zásad přístupu](../media/authentication/assign-policy-portal-02.png)

1. V části **Vybrat objekt zabezpečení** vyberte odkaz **žádné vybrané** a otevřete tak podokno výběru **objektu zabezpečení** . Do vyhledávacího pole zadejte název aplikace nebo instančního objektu, vyberte příslušný výsledek a pak zvolte **Vybrat**.

    ![Výběr objektu služby pro zásady přístupu](../media/authentication/assign-policy-portal-03.png)

    Pokud pro aplikaci používáte spravovanou identitu, vyhledejte a vyberte název samotné aplikace. (Další informace o spravované identitě a instančních objektech naleznete v tématu [Key Vault Authentication-identity aplikace a instanční objekty](authentication.md#app-identity-and-security-principals).)
 
1.  Zpátky v podokně **Přidat zásady přístupu** vyberte **Přidat** a uložte zásady přístupu.

    ![Přidání zásad přístupu k přiřazenému objektu služby](../media/authentication/assign-policy-portal-04.png)

1. Zpátky na stránce **zásady přístupu** ověřte, že zásady přístupu jsou uvedené v části **aktuální zásady přístupu**, a pak vyberte **Uložit**. Zásady přístupu se nepoužijí, dokud je neuložíte.

    ![Ukládají se změny zásad přístupu.](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Další kroky

- [Azure Key Vault zabezpečení: Správa identit a přístupu](security-overview.md#identity-management)
- [Zabezpečte svůj Trezor klíčů](secure-your-key-vault.md).
- [Azure Key Vault příručka pro vývojáře](developers-guide.md)