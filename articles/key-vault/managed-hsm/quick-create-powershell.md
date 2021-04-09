---
title: Vytvoření a načtení atributů spravovaného klíče v Azure Key Vault – Azure PowerShell
description: Rychlý Start ukazující, jak nastavit a načíst spravovaný klíč z Azure Key Vault pomocí Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/26/2021
ms.author: mbaldwin
ms.openlocfilehash: 943555e9f7a26530a075aee27dd310d96974e652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99072909"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Rychlý Start: nastavení a načtení spravovaného klíče z Azure Key Vault pomocí prostředí PowerShell

V tomto rychlém startu vytvoříte Trezor klíčů v Azure Key Vault s Azure PowerShell. Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md). Azure PowerShell slouží k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. Po dokončení této akce uložíte klíč.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz Azure PowerShell verze modulu 1.0.0 nebo novější. `$PSVersionTable.PSVersion`Pro nalezení verze zadejte. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí rutiny Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *westus* . 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Vaše ID objektu zabezpečení bude vráceno ve formátu "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx".

## <a name="create-a-managed-hsm"></a>Vytvoření spravovaného modulu HSM

Pomocí rutiny Azure PowerShell [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) vytvořte nový Key Vault spravovaný modul HSM. Budete muset zadat několik informací:

- Spravovaný název HSM: řetězec o 3 až 24 znaků, který může obsahovat jenom číslice (0-9), písmena (A-z, A-Z) a spojovníky (-).

  > [!Important]
  > Každý spravovaný modul HSM musí mít jedinečný název. V následujících příkladech nahraďte <jedinečným-HSM-název> s názvem spravovaného HSM.

- Název skupiny prostředků: **myResourceGroup**.
- Umístění: **EastUS**.
- Vaše ID objektu zabezpečení: předejte ID objektu zabezpečení Azure Active Directory, které jste získali v poslední části, do parametru "Administrator". 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

Výstup této rutiny zobrazuje vlastnosti nově vytvořeného spravovaného modulu HSM. Poznamenejte si hodnoty dvou vlastností uvedených níže:

- **Spravovaný název HSM**: název, který jste zadali do parametru--name výše.
- **Identifikátor URI trezoru**: v tomto příkladu je to https:// &lt; Your-Unique-Managed-HSM-name &gt; . Vault.Azure.NET/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.

## <a name="activate-your-managed-hsm"></a>Aktivace spravovaného modulu HSM

Všechny příkazy roviny dat jsou zakázané, dokud se neaktivuje modul HARDWAROVÉho zabezpečení. Nebudete moci vytvářet klíče ani role přiřadit. Modul hardwarového zabezpečení (HSM) mohou aktivovat pouze určení správci, kteří byli přiřazeni během příkazu CREATE. Chcete-li aktivovat modul HSM, musíte si stáhnout [doménu zabezpečení](security-domain.md).

Chcete-li aktivovat modul HARDWAROVÉho zabezpečení, který potřebujete:
- Minimální 3 páry klíčů RSA (maximum 10)
- Zadejte minimální počet klíčů nutný k dešifrování domény zabezpečení (kvorum).

Pokud chcete aktivovat modul hardwarového zabezpečení (HSM), odešlete aspoň 3 (maximum 10) veřejných klíčů RSA do modulu HSM. Modul HARDWAROVÉho zabezpečení zabezpečuje pomocí těchto klíčů doménu zabezpečení a odesílá je zpátky. Po úspěšném dokončení této domény zabezpečení je váš modul HARDWAROVÉho zabezpečení připravený k použití. Je také nutné zadat kvorum, což je minimální počet privátních klíčů vyžadovaných k dešifrování domény zabezpečení.

Níže uvedený příklad ukazuje, jak použít `openssl` (k dispozici [](https://slproweb.com/products/Win32OpenSSL.html)pro Windows) k vygenerování 3 certifikátu podepsaného svým vlastníkem.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Vytvořte a bezpečně uložte páry klíčů RSA a soubor domény zabezpečení vygenerované v tomto kroku.

K stažení domény zabezpečení a aktivaci spravovaného modulu HSM použijte rutinu Azure PowerShell [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) . Níže uvedený příklad používá 3 páry klíčů RSA (pro tento příkaz jsou nutné pouze veřejné klíče) a nastavuje kvorum na 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Uložte prosím zabezpečený soubor domény zabezpečení a páry klíčů RSA. Budete je potřebovat pro zotavení po havárii nebo pro vytvoření jiného spravovaného modulu HSM, který sdílí stejnou doménu zabezpečení, takže může sdílet klíče.

Po úspěšném stažení domény zabezpečení bude modul HSM v aktivním stavu a bude připravený k použití.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a uložili do něj certifikát. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si referenční informace pro [rutiny Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Přečtěte si [Přehled zabezpečení Key Vault](../general/security-overview.md)
