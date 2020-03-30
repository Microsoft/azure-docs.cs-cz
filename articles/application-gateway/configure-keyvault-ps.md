---
title: Konfigurace ukončení protokolu SSL pomocí certifikátů trezoru klíčů – PowerShell
titleSuffix: Azure Application Gateway
description: Zjistěte, jak můžete integrovat Azure Application Gateway s Trezorem klíčů pro serverové certifikáty, které jsou připojené k naslouchacím líným a https povoleno.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/27/2020
ms.author: victorh
ms.openlocfilehash: 15e10d34120ab5475f241235bbebeb0c7689ca14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371230"
---
# <a name="configure-ssl-termination-with-key-vault-certificates-by-using-azure-powershell"></a>Konfigurace ukončení protokolu SSL pomocí certifikátů trezoru klíčů pomocí Azure PowerShellu

[Azure Key Vault](../key-vault/key-vault-overview.md) je úložiště tajných klíčů spravované platformou, které můžete použít k zabezpečení tajných kódů, klíčů a certifikátů SSL. Azure Application Gateway podporuje integraci s trezorem klíčů pro serverové certifikáty, které jsou připojené k naslouchací procesy s podporou PROTOKOLU HTTPS. Tato podpora je omezena na sku sku aplikace v2.

Další informace naleznete v [tématu SSL ukončení s certifikáty trezoru klíčů](key-vault-certs.md).

Tento článek ukazuje, jak pomocí skriptu Azure PowerShell integrovat trezor klíčů s vaší aplikační bránou pro certifikáty ukončení SSL.

Tento článek vyžaduje modul Azure PowerShell verze 1.0.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Chcete-li spustit příkazy v tomto článku, musíte také `Connect-AzAccount`vytvořit připojení s Azure spuštěním .

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte mít nainstalovaný modul ManagedServiceIdentity:

```azurepowershell
Install-Module -Name Az.ManagedServiceIdentity
Connect-AzAccount
Select-AzSubscription -Subscription <your subscription>
```

## <a name="example-script"></a>Ukázkový skript

### <a name="set-up-variables"></a>Nastavení proměnných

```azurepowershell
$rgname = "KeyVaultTest"
$location = "East US"
$kv = "TestKeyVaultAppGw"
$appgwName = "AppGwKVIntegration"
```

### <a name="create-a-resource-group-and-a-user-managed-identity"></a>Vytvoření skupiny prostředků a identity spravované uživatelem

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname
```

### <a name="create-a-key-vault-policy-and-certificate-to-be-used-by-the-application-gateway"></a>Vytvoření trezoru klíčů, zásad a certifikátu, které bude používat aplikační brána

```azurepowershell
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
Set-AzKeyVaultAccessPolicy -VaultName $kv -EmailAddress <your email address> -PermissionsToCertificates create,get,list
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")
```
> [!NOTE]
> Příznak -EnableSoftDelete musí být použit pro ukončení SSL správně fungovat. Pokud konfigurujete [obnovitelné odstranění trezoru klíčů prostřednictvím portálu](../key-vault/key-vault-ovw-soft-delete.md#soft-delete-behavior), musí být doba uchování zachována na 90 dnech, což je výchozí hodnota. Aplikační brána ještě nepodporuje jiné období uchovávání informací. 

### <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-virtual-ip-vip-address"></a>Vytvoření statické veřejné virtuální IP adresy (VIP)

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-front-end-ports"></a>Vytvoření bazénových a front-endových portů

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80
```

### <a name="point-the-ssl-certificate-to-your-key-vault"></a>Najeďte certifikát ssl na trezor klíčů

```azurepowershell
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId
```

### <a name="create-listeners-rules-and-autoscale"></a>Vytvoření posluchačů, pravidel a automatického škálování

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSetting -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
```

### <a name="assign-the-user-managed-identity-to-the-application-gateway"></a>Přiřazení identity spravované uživatelem k bráně aplikace

```azurepowershell
$appgwIdentity = New-AzApplicationGatewayIdentity -UserAssignedIdentityId $identity.Id
```

### <a name="create-the-application-gateway"></a>Vytvoření služby Application Gateway

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -Identity $appgwIdentity -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts @($fp01, $fp02) -HttpListeners @($listener01, $listener02) `
  -RequestRoutingRules @($rule01, $rule02) -Sku $sku `
  -SslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="next-steps"></a>Další kroky

[Další informace o ukončení SSL](ssl-overview.md)
