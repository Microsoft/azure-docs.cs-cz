---
title: Přiřazení zásad Azure Key Vault přístupu (portál)
description: Jak použít Azure Portal k přiřazení zásady přístupu Key Vault k objektu zabezpečení nebo identitě aplikace.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: a64a91e6f41f3fba7584630380ffb878979b4389
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968760"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Přiřazení zásady přístupu Key Vault pomocí Azure Portal

Zásada přístupu Key Vault určuje, jestli daný objekt zabezpečení, konkrétně uživatel, aplikace nebo skupina uživatelů, může provádět různé operace s Key Vault [tajné klíče](../secrets/index.yml), [klíče](../keys/index.yml)a [certifikáty](../certificates/index.yml). Zásady přístupu můžete přiřadit pomocí Azure Portal (Tento článek), rozhraní příkazového [řádku Azure](assign-access-policy-cli.md)nebo [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Další informace o vytváření skupin v Azure Active Directory prostřednictvím Azure Portal najdete v tématu [Vytvoření základní skupiny a přidání členů](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) .

## <a name="assign-an-access-policy"></a>Přiřazení zásad přístupu

1.  V [Azure Portal](https://portal.azure.com)přejděte na prostředek Key Vault. 

1.  V části **Nastavení** vyberte **zásady přístupu** a pak vyberte **Přidat zásady přístupu**:

    ![Vyberte zásady přístupu, vyberte přidat přiřazení role.](../media/authentication/assign-policy-portal-01.png)

1.  Vyberte požadovaná oprávnění v části **oprávnění certifikátu**, **oprávnění klíče** a **tajná oprávnění**. Můžete také vybrat šablonu obsahující společné kombinace oprávnění:

    ![Určení oprávnění zásad přístupu](../media/authentication/assign-policy-portal-02.png)

1. V části **Vybrat objekt zabezpečení** vyberte odkaz **žádné vybrané** a otevřete tak podokno výběru **objektu zabezpečení** . Do vyhledávacího pole zadejte jméno uživatele, aplikace nebo instančního objektu, vyberte příslušný výsledek a pak zvolte **Vybrat**.

    ![Výběr objektu zabezpečení pro zásady přístupu](../media/authentication/assign-policy-portal-03.png)

    Pokud pro aplikaci používáte spravovanou identitu, vyhledejte a vyberte název samotné aplikace. (Další informace o spravované identitě a instančních objektech naleznete v tématu [Key Vault Authentication-identity aplikace a instanční objekty](authentication.md#app-identity-and-security-principals).)
 
1.  Zpátky v podokně **Přidat zásady přístupu** vyberte **Přidat** a uložte zásady přístupu.

    ![Přidávání zásad přístupu s přiřazeným objektem zabezpečení](../media/authentication/assign-policy-portal-04.png)

1. Zpátky na stránce **zásady přístupu** ověřte, že zásady přístupu jsou uvedené v části **aktuální zásady přístupu**, a pak vyberte **Uložit**. Zásady přístupu se nepoužijí, dokud je neuložíte.

    ![Ukládají se změny zásad přístupu.](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Další kroky

- [Azure Key Vault zabezpečení: Správa identit a přístupu](security-overview.md#identity-management)
- [Zabezpečte svůj Trezor klíčů](secure-your-key-vault.md).
- [Azure Key Vault příručka pro vývojáře](developers-guide.md)