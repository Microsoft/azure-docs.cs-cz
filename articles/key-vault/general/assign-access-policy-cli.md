---
title: Přiřazení zásad Azure Key Vaultho přístupu (CLI)
description: Jak pomocí rozhraní příkazového řádku Azure můžete přiřadit zásady přístupu Key Vault k objektu zabezpečení nebo identitě aplikace.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: a9dc03f776ac430072e456332955cbfc75d73bf2
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968845"
---
# <a name="assign-a-key-vault-access-policy"></a>Přiřazení zásady přístupu Key Vault

Zásada přístupu Key Vault určuje, jestli daný objekt zabezpečení, konkrétně uživatel, aplikace nebo skupina uživatelů, může provádět různé operace s Key Vault [tajné klíče](../secrets/index.yml), [klíče](../keys/index.yml)a [certifikáty](../certificates/index.yml). Zásady přístupu můžete přiřadit pomocí [Azure Portal](assign-access-policy-portal.md), Azure CLI (Tento článek) nebo [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Další informace o vytváření skupin v Azure Active Directory pomocí rozhraní příkazového řádku Azure CLI najdete v tématu [AZ AD Group Create](/cli/azure/ad/group#az-ad-group-create) a [AZ AD Group member Add](/cli/azure/ad/group/member#az-ad-group-member-add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Konfigurace Azure CLI a přihlášení

1. Pokud chcete spustit příkazy rozhraní příkazového řádku Azure v místním počítači, nainstalujte [Azure CLI](/cli/azure/install-azure-cli).
 
    Pokud chcete spouštět příkazy přímo v cloudu, použijte [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Pouze místní CLI: Přihlaste se k Azure pomocí `az login` :

    ```bash
    az login
    ```

    `az login`Příkaz otevře okno prohlížeče pro shromažďování přihlašovacích údajů v případě potřeby.

## <a name="acquire-the-object-id"></a>Získat ID objektu

Určete ID objektu aplikace, skupiny nebo uživatele, ke kterému chcete přiřadit zásady přístupu:

- Aplikace a další instanční objekty: pomocí příkazu [AZ AD SP list](/cli/azure/ad/sp#az-ad-sp-list) načtěte objekty služby. Zkontrolujte výstup příkazu a určete ID objektu zabezpečení, ke kterému chcete přiřadit zásady přístupu.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Skupiny: použijte příkaz [AZ AD Group list](/cli/azure/ad/group#az-ad-group-list) , který filtruje výsledky s `--display-name` parametrem:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Uživatelé: použijte příkaz [AZ AD User show](/cli/azure/ad/user#az-ad-user-show) a předáním e-mailové adresy uživatele v `--id` parametru:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Přiřazení zásad přístupu
    
Pomocí příkazu [AZ klíčů trezor set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) přiřaďte požadovaná oprávnění:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Nahraďte `<object-id>` ID objektu vašeho objektu zabezpečení.

Potřebujete pouze zahrnout `--secret-permissions` , `--key-permissions` a `--certificate-permissions` při přiřazování oprávnění těmto konkrétním typům. Přípustné hodnoty pro, a `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` jsou uvedeny v dokumentaci [AZ pro trezor set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) .

## <a name="next-steps"></a>Další kroky

- [Azure Key Vault zabezpečení: Správa identit a přístupu](security-overview.md#identity-management)
- [Zabezpečte svůj Trezor klíčů](secure-your-key-vault.md).
- [Azure Key Vault příručka pro vývojáře](developers-guide.md)