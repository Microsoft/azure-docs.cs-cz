---
title: Přiřazení zásad Azure Key Vaultho přístupu (CLI)
description: Jak pomocí rozhraní příkazového řádku Azure můžete přiřadit zásady přístupu Key Vault k instančnímu objektu nebo identitě aplikace.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 32a323e8cc56a8c45b18737bb55ebe19d4be27c0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89380816"
---
# <a name="assign-a-key-vault-access-policy"></a>Přiřazení zásady přístupu Key Vault

Zásada přístupu Key Vault určuje, jestli daný instanční objekt, konkrétně aplikace nebo skupina uživatelů, může provádět různé operace s Key Vault [tajné klíče](../secrets/index.yml), [klíče](../keys/index.yml)a [certifikáty](../certificates/index.yml). Zásady přístupu můžete přiřadit pomocí [Azure Portal](assign-access-policy-portal.md), Azure CLI (Tento článek) nebo [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Další informace o vytváření skupin v Azure Active Directory pomocí rozhraní příkazového řádku Azure CLI najdete v tématu [AZ AD Group Create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) a [AZ AD Group member Add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Konfigurace Azure CLI a přihlášení

1. Pokud chcete spustit příkazy rozhraní příkazového řádku Azure v místním počítači, nainstalujte [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).
 
    Pokud chcete spouštět příkazy přímo v cloudu, použijte [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Pouze místní CLI: Přihlaste se k Azure pomocí `az login` :

    ```bash
    az login
    ```

    `az login`Příkaz otevře okno prohlížeče pro shromažďování přihlašovacích údajů v případě potřeby.

## <a name="acquire-the-object-id"></a>Získat ID objektu

Určete ID objektu aplikace, skupiny nebo uživatele, ke kterému chcete přiřadit zásady přístupu:

- Aplikace a další instanční objekty: pomocí příkazu [AZ AD SP list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) načtěte objekty služby. Zkontrolujte výstup příkazu a určete ID objektu zabezpečení, ke kterému chcete přiřadit zásady přístupu.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Skupiny: použijte příkaz [AZ AD Group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) , který filtruje výsledky s `--display-name` parametrem:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Uživatelé: použijte příkaz [AZ AD User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) a předáním e-mailové adresy uživatele v `--id` parametru:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Přiřazení zásad přístupu
    
Pomocí příkazu [AZ klíčů trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) přiřaďte požadovaná oprávnění:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Nahraďte `<object-id>` ID objektu vašeho objektu služby.

Potřebujete pouze zahrnout `--secret-permissions` , `--key-permissions` a `--certificate-permissions` při přiřazování oprávnění těmto konkrétním typům. Přípustné hodnoty pro, a `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` jsou uvedeny v dokumentaci [AZ pro trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

## <a name="next-steps"></a>Další kroky

- [Azure Key Vault zabezpečení: Správa identit a přístupu](overview-security.md#identity-and-access-management)
- [Zabezpečte svůj Trezor klíčů](secure-your-key-vault.md).
- [Azure Key Vault příručka pro vývojáře](developers-guide.md)
- [Azure Key Vault osvědčené postupy](best-practices.md)
