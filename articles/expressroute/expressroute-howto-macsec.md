---
title: 'Azure ExpressRoute: Konfigurace MACsec'
description: Tento článek vám pomůže nakonfigurovat sadu MACsec tak, aby zabezpečila připojení mezi hraničními směrovači a hraničními směrovači společnosti Microsoft.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083548"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Konfigurace funkce MACsec na portech ExpressRoute Direct

Tento článek vám pomůže nakonfigurovat sadu MACsec tak, aby zabezpečila připojení mezi hraničními směrovači a hraničními směrovači společnosti Microsoft pomocí prostředí PowerShell.

## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace potvrďte následující:

* Rozumíte [pracovním postupům zřizování ExpressRoute Direct](expressroute-erdirect-about.md).
* Vytvořili jste [prostředek portu ExpressRoute Direct](expressroute-howto-erdirect.md).
* Pokud chcete powershell spustit místně, ověřte, jestli je ve vašem počítači nainstalovaná nejnovější verze Prostředí Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Přihlaste se a vyberte správné předplatné

Pokud chcete spustit konfiguraci, přihlaste se ke svému účtu Azure a vyberte předplatné, které chcete použít.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Vytvoření trezoru klíčů Azure, tajných kódů MACsec a identity uživatele

1. Vytvořte instanci trezoru klíčů pro uložení tajných kódů MACsec v nové skupině prostředků.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Pokud již trezor klíčů nebo skupinu prostředků máte, můžete je znovu použít. Je však důležité povolit funkci [ **obnovitelného odstranění** ](../key-vault/key-vault-ovw-soft-delete.md) v existujícím trezoru klíčů. Pokud není povoleno obnovitelné odstranění, můžete jej povolit pomocí následujících příkazů:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Vytvořte identitu uživatele.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Pokud New-AzUserAssignedIdentity není rozpoznán jako platný rutina prostředí PowerShell, nainstalujte následující modul (v režimu správce) a znovu spusťte výše uvedený příkaz.

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

   Nyní tato identita může získat tajné klíče, například CAK a CKN, z trezoru klíčů.
5. Nastavte tuto identitu uživatele pro použití expressroute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Konfigurace macseku na portech ExpressRoute Direct

### <a name="to-enable-macsec"></a>Povolení macsec

Každá instance ExpressRoute Direct má dva fyzické porty. Můžete povolit MACsec na obou portech současně nebo povolit MACsec na jednom portu najednou. Dělat to jeden port v době (přepnutím provozu na aktivní port při údržbě druhého portu) může pomoci minimalizovat přerušení, pokud expressroute direct je již v provozu.

1. Nastavte tajné kódy MACsec a šifru a přidružte identitu uživatele k portu tak, aby kód správy ExpressRoute měl v případě potřeby přístup k tajným kódům MACsec.

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
2. (Nepovinné) Pokud jsou porty ve stavu Správce dolů, můžete spustit následující příkazy pro zvýšení portů.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    V tomto okamžiku je macsec povolena na portech ExpressRoute Direct na straně microsoftu. Pokud jste ji nenakonfigurovali na hraničních zařízeních, můžete je nakonfigurovat se stejnými tajnými kódy MACsec a šifrem.

### <a name="to-disable-macsec"></a>Zakázání funkce MACsec

Pokud již není macsec v instanci ExpressRoute Direct žádoucí, můžete ji zakázat následujícími příkazy.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

V tomto okamžiku je macsec zakázán na portech ExpressRoute Direct na straně společnosti Microsoft.

### <a name="test-connectivity"></a>Test připojení
Po konfiguraci macsec (včetně aktualizace klíče MACsec) na portech ExpressRoute Direct [zkontrolujte,](expressroute-troubleshooting-expressroute-overview.md) zda jsou relace Protokolu BGP okruhů v provozu. Pokud ještě nemáte žádný okruh na portech, vytvořte nejprve jeden a nastavte Azure Private Peering nebo Microsoft Peering okruhu. Pokud je macsec nesprávně nakonfigurován, včetně neshody klíčů MACsec, mezi síťovými zařízeními a síťovými zařízeními společnosti Microsoft, neuvidíte rozlišení ARP ve vrstvě 2 a zařízení BGP ve vrstvě 3. Pokud je vše správně nakonfigurováno, měli byste vidět trasy Protokolu BGP inzerované správně v obou směrech a tok dat aplikace odpovídajícím způsobem přes ExpressRoute.

## <a name="next-steps"></a>Další kroky
1. [Vytvoření okruhu ExpressRoute v aplikaci ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Propojení okruhu ExpressRoute s virtuální sítí Azure](expressroute-howto-linkvnet-arm.md)
3. [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
