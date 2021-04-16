---
title: Rychlý Start – Nastavení & zobrazení Azure Key Vault certifikátů pomocí Azure PowerShell
description: Rychlý Start ukazující, jak nastavit a načíst certifikát z Azure Key Vault pomocí Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 11f463ab0ae60f489fd6b10d06402b6d27fc9930
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502249"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Rychlý Start: nastavení a načtení certifikátu z Azure Key Vault pomocí Azure PowerShell

V tomto rychlém startu vytvoříte Trezor klíčů v Azure Key Vault s Azure PowerShell. Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md). Azure PowerShell slouží k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. Po dokončení této akce uložíte certifikát.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz Azure PowerShell verze modulu 1.0.0 nebo novější. `$PSVersionTable.PSVersion`Pro nalezení verze zadejte. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Přidat certifikát pro Key Vault

Pokud chcete do trezoru přidat certifikát, stačí provést několik dalších kroků. Tento certifikát může používat aplikace. 

Zadáním níže uvedených příkazů vytvořte certifikát podepsaný svým držitelem se zásadami s názvem **ExampleCertificate** :

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Nyní můžete odkazovat na tento certifikát, který jste přidali do Azure Key Vault pomocí jeho identifikátoru URI. Pokud chcete získat aktuální verzi, použijte **https://<pro název trezoru klíčů>. Vault.Azure.NET/Certificates/ExampleCertificate.** 

Postup zobrazení dříve uloženého certifikátu:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

Nyní jste vytvořili Key Vault, uložili certifikát a načetli jste ho.

**Řešení potíží**:

Operace vrátila neplatný stavový kód zakázáno.

Pokud se zobrazí tato chyba, účet přistupující k Azure Key Vault nemá správná oprávnění k vytváření certifikátů.

Chcete-li přiřadit správná oprávnění, spusťte následující příkaz Azure PowerShell:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> -ObjectId <AzureObjectID> -PermissionsToCertificates get,list,update,create
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a uložili do něj certifikát. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si referenční informace pro [rutiny Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Přečtěte si [Přehled zabezpečení Key Vault](../general/security-overview.md)
