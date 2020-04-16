---
title: 'Úvodní příručka: Nastavení a načtení certifikátu z trezoru klíčů Azure'
description: Úvodní příručka o tom, jak nastavit a načíst certifikát z Azure Key Vault pomocí Azure PowerShellu
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: ecb9262a96d1c9857283de00224950d9bc7a583f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424710"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Úvodní příručka: Nastavení a načtení certifikátu z Azure Key Vault pomocí Azure PowerShellu

V tomto rychlém startu vytvoříte trezor klíčů v azure key vaultu s Azure PowerShellem. Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md). Azure PowerShell se používá k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. Jakmile to dokončíte, uložíte certifikát.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Chcete-li najít verzi, zadejte. `$PSVersionTable.PSVersion` Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure s [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

Dále vytvoříte službu Key Vault. K provedení tohoto kroku potřebujete několik informací:

Přestože používáme "Contoso KeyVault2" jako název pro náš trezor klíčů v celém tomto rychlém startu, musíte použít jedinečný název.

- **Název trezoru:** Contoso-Vault2
- **Název skupiny prostředků** ContosoResourceGroup.
- **Umístění** Východní USA.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Výstup této rutiny zobrazuje vlastnosti nově vytvořeného trezoru klíčů. Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru:** V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další rutiny Key Vault.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://Contoso-Vault2.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Po vytvoření trezoru bude jakékoli operace s tímto novým trezorem moct provádět pouze váš účet Azure.

![Výstup po dokončení příkazu pro vytvoření trezoru klíčů](../media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="add-a-certificate-to-key-vault"></a>Přidání certifikátu do trezoru klíčů

Chcete-li přidat certifikát do trezoru, stačí provést několik dalších kroků. Tento certifikát může být použit aplikací. 

Zadejte níže uvedené příkazy a vytvořte certifikát podepsaný svým držitelem se zásadou **examplecertificate** :

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal
Add-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Nyní můžete odkazovat na tento certifikát, který jste přidali do trezoru klíčů Azure pomocí jeho URI. Slouží **https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate** k získání aktuální verze. 

Zobrazení dříve uloženého certifikátu:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "Contoso-Vault2" -Name "ExampleCertificate"
```

Nyní jste vytvořili trezor klíčů, uložili certifikát a načetli jej.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Pokud již není potřeba, můžete použít [příkaz Odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) k odebrání skupiny prostředků a všech souvisejících prostředků. Prostředky můžete odstranit následujícím způsobem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili trezor klíčů a uložili do něj certifikát. Chcete-li se dozvědět více o trezoru klíčů a o tom, jak jej integrovat s aplikacemi, pokračujte v následujících článcích.

- Přečtěte si [přehled trezoru klíčů Azure](../general/overview.md)
- Podívejte se na odkaz na [rutiny trezoru klíčů Azure PowerShell](/powershell/module/az.keyvault/)
- Kontrola [doporučených postupů azure key vaultu](../general/best-practices.md)
