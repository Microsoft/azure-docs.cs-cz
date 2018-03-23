---
title: Správa Key Vault v zásobníku Azure pomocí prostředí PowerShell | Microsoft Docs
description: Zjistěte, jak spravovat Key Vault v zásobníku Azure pomocí prostředí PowerShell
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: mabrigg
ms.openlocfilehash: 9dac59d74347e21bebaf7cb65d199711f45b29a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="manage-key-vault-in-azure-stack-by-using-powershell"></a>Správa Key Vault v zásobníku Azure pomocí prostředí PowerShell

Tento článek vám pomůže začít pracovat k vytváření a správě Key Vault v zásobníku Azure pomocí prostředí PowerShell. Rutiny prostředí PowerShell klíč trezoru popsané v tomto článku jsou k dispozici jako součást sady SDK Azure PowerShell. Následující části popisují rutiny prostředí PowerShell, které jsou požadovány pro:
   - Vytvoření trezoru. 
   - Ukládat a spravovat kryptografické klíče a tajné klíče. 
   - Autorizace uživatelů nebo aplikací k vyvolání operace v trezoru. 

## <a name="prerequisites"></a>Požadavky
* Musí se přihlásíte k nabídku, která zahrnuje službu Azure Key Vault.
* [Instalace prostředí PowerShell pro Azure zásobníku](azure-stack-powershell-install.md).  
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Aktivujte předplatné klienta pro operace Key Vault

Předtím, než můžete použít všechny operace u trezoru klíčů, je potřeba zajistit, že vaše předplatné klienta je povolen pro operace trezoru. Pokud chcete ověřit, že jsou povolené operace trezoru, spusťte následující příkaz:

```PowerShell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```
**Výstup**

Pokud je vaše předplatné povolená pro operace trezoru, výstup ukazuje, že "RegistrationState" je rovno "Registrovaná" pro všechny typy prostředků z trezoru klíčů.

![Stav registrace](media/azure-stack-kv-manage-powershell/image1.png)

Pokud nejsou k dispozici operace trezoru, vyvolání následující příkaz pro registraci do služby Key Vault ve vašem předplatném:

```PowerShell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

**Výstup**

Pokud registrace úspěšná, se vrátí následující výstup:

![Zaregistrovat](media/azure-stack-kv-manage-powershell/image2.png) při vyvolání příkazy trezoru klíčů, může dojde k chybě, například "předplatné není zaregistrované používání oboru názvů"Microsoft.KeyVault"." Pokud dojde k chybě, zkontrolujte, zda máte [povolen zprostředkovatel prostředků Key Vault](#enable-your-tenant-subscription-for-vault-operations) podle těchto pokynů, které bylo zmíněno dříve.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů 

Než vytvoříte trezoru klíčů, vytvořte skupinu prostředků, aby všechny zdroje související s trezoru klíčů existují ve skupině prostředků. Chcete-li vytvořit novou skupinu prostředků, použijte následující příkaz:

```PowerShell
New-AzureRmResourceGroup -Name “VaultRG” -Location local -verbose -Force

```

**Výstup**

![novou skupinu prostředků](media/azure-stack-kv-manage-powershell/image3.png)

Nyní pomocí **New-AzureRMKeyVault** příkaz pro vytvoření trezoru klíčů ve skupině prostředků, kterou jste vytvořili dříve. Tento příkaz načte tři povinné parametry: název skupiny prostředků, název trezoru klíčů a zeměpisné umístění. 

Spusťte následující příkaz pro vytvoření trezoru klíčů:

```PowerShell
New-AzureRmKeyVault -VaultName “Vault01” -ResourceGroupName “VaultRG” -Location local -verbose
```
**Výstup**

![Nový trezor klíčů](media/azure-stack-kv-manage-powershell/image4.png)

Výstup tohoto příkazu zobrazuje vlastnosti trezoru klíčů, který jste vytvořili. Když aplikace získá přístup k tomuto trezoru, použije **identifikátor URI trezoru** vlastnosti zobrazené ve výstupu. Například trezoru identifikátor URI (Uniform Resource) v tomto případě je "https://vault01.vault.local.azurestack.external". Aplikace, které se s tímto trezorem klíčů přes REST API musí používat tento identifikátor URI.

Ve službě Active Directory Federation Services (AD FS) – na základě nasazení, při vytváření klíče trezoru pomocí prostředí PowerShell, může se zobrazit upozornění, že "zásady přístupu není nastaven. Žádné uživatele nebo aplikace má přístupová oprávnění k použití tento trezor." Chcete-li vyřešit tento problém, nastavení zásad přístupu pro úložiště pomocí [Set-AzureRmKeyVaultAccessPolicy](azure-stack-kv-manage-powershell.md#authorize-an-application-to-use-a-key-or-secret) příkaz:

```PowerShell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value 

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation 
```

## <a name="manage-keys-and-secrets"></a>Správa klíčů a tajných klíčů

Po vytvoření trezoru, použijte následující postup vytvoření a Správa klíčů a tajných klíčů v trezoru.

### <a name="create-a-key"></a>Vytvoření klíče

Použití **Add-AzureKeyVaultKey** příkaz pro vytvoření nebo import softwarově chráněný klíč v trezoru klíčů. 

```PowerShell
Add-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01” -verbose -Destination Software
```
**Cílové** parametr se používá k určení, že klíč je chráněný softwaru. Po úspěšném vytvoření klíč příkaz výstupy podrobnosti vytvořený klíč.

**Výstup**

![Nový klíč](media/azure-stack-kv-manage-powershell/image5.png)

Vytvořený klíč můžete odkazovat pomocí jeho identifikátoru URI. Pokud vytvoříte nebo import klíče, který má stejný název jako existující klíč, je původní klíč aktualizované hodnoty zadané v nový klíč. Předchozí verze můžete přistupovat pomocí identifikátoru URI specifické pro verzi klíče. Příklad: 

* Použití "https://vault10.vault.local.azurestack.external:443/keys/key01" k vždy získáte aktuální verzi. 
* Použití "https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a" Chcete-li získat konkrétní verzi.

### <a name="get-a-key"></a>Získat klíč

Použití **Get-AzureKeyVaultKey** příkaz číst klíč a její podrobnosti.

```PowerShell
Get-AzureKeyVaultKey -VaultName “Vault01” -Name “Key01”
```

### <a name="create-a-secret"></a>Vytvoření tajného klíče

Použití **Set-AzureKeyVaultSecret** příkaz, který umožňuje vytvořit nebo aktualizovat tajný klíč v trezoru. Pokud již neexistuje, vytvoří se tajný klíč. Nová verze tajný klíč je vytvořen, pokud již existuje.

```PowerShell
$secretvalue = ConvertTo-SecureString “User@123” -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01” -SecretValue $secretvalue
```

**Výstup**

![Vytvoření tajného klíče](media/azure-stack-kv-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Získání tajného klíče

Použití **Get-AzureKeyVaultSecret** příkaz číst tajný klíč v trezoru klíčů. Tento příkaz může vrátit všechny nebo konkrétních verzí tajného klíče. 

```PowerShell
Get-AzureKeyVaultSecret -VaultName “Vault01” -Name “Secret01”
```

Po vytvoření klíče a tajné klíče, můžete povolit externí aplikace jejich použití.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autorizace aplikace pro použití klíče nebo tajného klíče

Použití **Set-AzureRmKeyVaultAccessPolicy** příkaz autorizace aplikace k přístupu k klíč nebo tajný klíč v trezoru klíčů.
V následujícím příkladu je název trezoru *ContosoKeyVault* a ID klienta aplikace, kterou chcete ověřit, má *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Chcete-li autorizovat aplikaci, spusťte následující příkaz. Volitelně můžete zadat **PermissionsToKeys** parametr pro nastavení oprávnění pro uživatele, aplikaci nebo skupinu zabezpečení.

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Pokud chcete autorizovat tu samou aplikaci pro čtení tajných klíčů v trezoru, spusťte následující rutinu:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Další postup
* [Nasadit virtuální počítač s heslem uložené v Key Vault](azure-stack-kv-deploy-vm-with-secret.md) 
* [Nasadit virtuální počítač s certifikát uložený v Key Vault](azure-stack-kv-push-secret-into-vm.md)

