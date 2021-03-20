---
title: 'Azure ExpressRoute: Konfigurace MACsec'
description: Tento článek vám pomůže nakonfigurovat MACsec pro zabezpečení připojení mezi hraničními směrovači a hraničními směrovači Microsoftu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.openlocfilehash: 8ee7f42a4b05def7c2239c7a1e5bcef54f6f7e50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89395770"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Konfigurace MACsec na portech ExpressRoute Direct

Tento článek vám pomůže nakonfigurovat MACsec k zabezpečení připojení mezi hraničními směrovači a hraničními směrovači Microsoftu pomocí PowerShellu.

## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací, potvrďte následující:

* Porozumíte [přímým pracovním postupům zřizování ExpressRoute](expressroute-erdirect-about.md).
* Vytvořili jste [prostředek portu ExpressRoute Direct](expressroute-howto-erdirect.md).
* Pokud chcete spustit prostředí PowerShell místně, ověřte, zda je v počítači nainstalována nejnovější verze Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Přihlaste se a vyberte správné předplatné.

Pokud chcete spustit konfiguraci, přihlaste se ke svému účtu Azure a vyberte předplatné, které chcete použít.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. vytvoření Azure Key Vault, tajných kódů MACsec a identity uživatele

1. Vytvořte instanci Key Vault pro uložení tajných kódů MACsec do nové skupiny prostředků.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Pokud už máte Trezor klíčů nebo skupinu prostředků, můžete je znovu použít. Je ale důležité, abyste [funkci **obnovitelného odstranění**](../key-vault/general/soft-delete-overview.md) povolili ve svém stávajícím trezoru klíčů. Pokud není povolené obnovitelné odstranění, můžete k tomu použít následující příkazy:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Vytvořte identitu uživatele.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Pokud New-AzUserAssignedIdentity není rozpoznaná jako platná rutina prostředí PowerShell, nainstalujte následující modul (v režimu správce) a znovu spusťte výše uvedený příkaz.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Vytvořte klíč přidružení připojení (CAK) a název klíče přidružení připojení (CKN) a uložte je do trezoru klíčů.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Přiřaďte identitě uživatele oprávnění GET.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Tato identita teď může získat tajné kódy, například CAK a CKN, z trezoru klíčů.
5. Nastavte tuto identitu uživatele pro použití v ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. konfigurace MACsec na přímých portech ExpressRoute

### <a name="to-enable-macsec"></a>Povolení MACsec

Každá instance ExpressRoute Direct má dva fyzické porty. Můžete se rozhodnout povolit MACsec na obou portech současně nebo povolit MACsec na jednom portu v daném okamžiku. Po jednom portu (přepnutím provozu na aktivní port při obsluhování druhého portu) může doejít přerušení, pokud je už Služba ExpressRoute Direct v provozu.

1. Nastavte MACsec tajných klíčů a šifr a přidružte identitu uživatele k portu, aby kód správy ExpressRoute mohl v případě potřeby získat přístup k tajným tajným klíčům MACsec.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. Volitelné Pokud jsou porty v administrativním stavu, můžete tyto porty vyvolat spuštěním následujících příkazů.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    V tuto chvíli je MACsec povolená na ExpressRoute přímých portech na straně Microsoftu. Pokud jste na hraničních zařízeních nenakonfigurovali, můžete je nakonfigurovat se stejnými tajnými klíči a šifrou MACsec.

### <a name="to-disable-macsec"></a>Zakázání MACsec

Pokud MACsec už není na vaší ExpressRoute přímé instanci žádoucí, můžete pro jeho zakázání spustit následující příkazy.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

V tomto okamžiku je MACsec v přímých portech ExpressRoute na straně Microsoftu zakázaný.

### <a name="test-connectivity"></a>Test připojení
Po nakonfigurování MACsec (včetně aktualizace klíče MACsec) na portech ExpressRoute Direct [Ověřte](expressroute-troubleshooting-expressroute-overview.md) , jestli jsou relace BGP okruhů v provozu. Pokud na těchto portech ještě nemáte žádný okruh, vytvořte si nejdřív jednu z nich a nastavte privátní partnerský vztah Azure nebo partnerský vztah Microsoftu pro okruh. Pokud je MACsec nesprávně nakonfigurovaný, včetně neshody klíčů MACsec, mezi síťovými zařízeními a síťovými zařízeními Microsoftu, na úrovni 2 a ve vrstvě 3 se rozlišení ARP nezobrazuje. Pokud je vše nakonfigurované správně, měli byste vidět správné trasy protokolu BGP v obou směrech a toku dat aplikací přes ExpressRoute.

## <a name="next-steps"></a>Další kroky
1. [Vytvoření okruhu ExpressRoute na ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Propojení okruhu ExpressRoute k virtuální síti Azure](expressroute-howto-linkvnet-arm.md)
3. [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
