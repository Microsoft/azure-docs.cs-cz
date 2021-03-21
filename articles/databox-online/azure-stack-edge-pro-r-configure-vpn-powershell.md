---
title: Konfigurace sítě VPN na zařízení Azure Stack Edge pro R pomocí Azure PowerShell
description: Popisuje konfiguraci sítě VPN na zařízení Azure Stack Edge pro R pomocí Azure PowerShell skriptu pro vytvoření prostředků Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 66edd4cad5b2f38696ef1df2030687bf4c7d9956
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634171"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>Konfigurace sítě VPN na zařízení Azure Stack Edge pro R prostřednictvím Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

Možnost VPN poskytuje druhou vrstvu šifrování pro přenos dat v rámci přenosu *TLS* ze zařízení Azure Stack Edge pro R do Azure. SÍŤ VPN můžete na zařízení Azure Stack Edge pro R nakonfigurovat přes Azure Portal nebo přes Azure PowerShell.

Tento článek popisuje kroky potřebné ke konfiguraci sítě VPN na zařízení Azure Stack Edge pro R pomocí Azure PowerShell k vytvoření konfigurace v cloudu.

## <a name="about-vpn-setup"></a>Informace o nastavení sítě VPN

Připojení VPN mezi různými místy se skládá z brány Azure VPN Gateway, místního zařízení VPN a tunelového připojení VPN S2S IPsec spojujících tyto dvě. Typický pracovní postup zahrnuje následující kroky:

- Konfigurace požadavků:
- Nastavte potřebné prostředky v Azure.
    - Vytvořte a nakonfigurujte bránu Azure VPN Gateway (bránu virtuální sítě).
    - Vytvořte a nakonfigurujte bránu místní sítě Azure, která představuje vaše místní síť a zařízení VPN.
    - Vytvořte a nakonfigurujte připojení Azure VPN mezi bránou Azure VPN a bránou místní sítě.
    - Nastavte Azure Firewall a přidejte pravidla sítě a aplikace.
    - Vytvořte směrovací tabulku Azure a přidejte trasy.
- Nastavte VPN v místním webovém uživatelském rozhraní zařízení. Nakonfigurujete místní zařízení VPN reprezentované bránou místní sítě, abyste navázali skutečný tunel VPN S2S s bránou Azure VPN.

Podrobné pokyny jsou uvedené v následujících částech.

## <a name="configure-prerequisites"></a>Konfigurovat požadavky

1. Měli byste mít přístup k zařízení Azure Stack Edge pro R, které je nainstalováno podle pokynů v tématu [instalace zařízení Azure Stack Edge pro r](azure-stack-edge-pro-r-deploy-install.md). Toto zařízení bude sloužit jako místní zařízení VPN k vytvoření připojení VPN s Azure. 

2. Vaše zařízení VPN by mělo mít statickou veřejnou IP adresu (externí). Tato adresa by neměla být NAT.

3. Měli byste mít přístup k platnému předplatnému Azure, které je povolené pro službu Azure Stack Edge v Azure. Pomocí tohoto předplatného můžete vytvořit odpovídající prostředek v Azure, abyste mohli spravovat zařízení Azure Stack Edge pro R.  

4. Máte přístup k klientovi Windows, který budete používat pro přístup k zařízení Azure Stack Edge pro R. Tento klient použijete k programovému vytvoření konfigurace v cloudu.

    1. Pokud chcete nainstalovat požadovanou verzi PowerShellu na klienta Windows, spusťte následující příkazy:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Pokud se chcete připojit ke svému účtu a předplatnému Azure, spusťte následující příkazy:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Pokud chcete nakonfigurovat síť VPN, zadejte název předplatného Azure, které používáte s vaším zařízením Azure Stack Edge pro R.

    3. [Stáhněte si skript](https://aka.ms/ase-vpn-deployment) potřebný k vytvoření konfigurace v cloudu. Tento skript:
        
        - Vytvořte virtuální síť Azure a následující podsítě: *GatewaySubnet* a *AzureFirewallSubnet*.
        - Vytvořte a nakonfigurujte bránu Azure VPN Gateway.
        - Vytvořte a nakonfigurujte bránu místní sítě Azure.
        - Vytvořte a nakonfigurujte připojení Azure VPN mezi bránou Azure VPN a bránou místní sítě.
        - Vytvořte Azure Firewall a přidejte Síťová pravidla, pravidla aplikací.
        - Vytvořte směrovací tabulku Azure a přidejte do ní trasy.


## <a name="use-the-script"></a>Použití skriptu

Nejprve `parameters.json` soubor upravte a zadejte parametry. Potom skript spustíte pomocí upraveného souboru JSON.

Každý z těchto kroků je popsán v následujících částech.

### <a name="download-service-tags-file"></a>Stáhnout soubor značek služby

Ve složce, do které `ServiceTags.json` jste stáhli skript, už možná máte soubor. Pokud ne, můžete si stáhnout soubor značek služby.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Upravit soubor parametrů

Prvním krokem je upravit `parameters.json` soubor a uložit změny. 


Pro prostředky Azure, které vytvoříte, zadejte následující názvy:

|Název parametru  |Description  |
|---------|---------|
|virtualNetworks_vnet_name    | Název Virtual Network Azure        |
|azureFirewalls_firewall_name     | Název Azure Firewall        |
|routeTables_routetable_name     | Název směrovací tabulky Azure        |
|publicIPAddresses_VNGW_public_ip_name     | Název veřejné IP adresy pro vaši bránu virtuální sítě       |
|virtualNetworkGateways_VNGW_name    | Název brány Azure VPN Gateway (Brána virtuální sítě)        |
|publicIPAddresses_firewall_public_ip_name     | Název veřejné IP adresy pro váš Azure Firewall         |
|localNetworkGateways_LNGW_name    |Název brány místní sítě Azure          |
|connections_vngw_lngw_name    | Název připojení Azure VPN. Toto je spojení mezi bránou virtuální sítě a bránou místní sítě.       |
|location     |To je oblast, ve které chcete vytvořit virtuální síť. Vyberte stejnou oblast jako ta, která je přidružená k vašemu zařízení.         |

Následující IP adresy a adresní prostory se vztahují k prostředkům Azure, které jsou vytvořeny, včetně virtuální sítě a přidružených podsítí (výchozí brána firewall, GatewaySubnet).

|Název parametru  |Description  |
|---------|---------|
|VnetIPv4AddressSpace    | Toto je adresní prostor přidružený k vaší virtuální síti.       |
|DefaultSubnetIPv4AddressSpace    |Toto je adresní prostor přidružený k `Default` podsíti vaší virtuální sítě.         |
|FirewallSubnetIPv4AddressSpace    |Toto je adresní prostor přidružený k `Firewall` podsíti vaší virtuální sítě.          |
|GatewaySubnetIPv4AddressSpace    |Toto je adresní prostor přidružený k `GatewaySubnet` vaší virtuální síti.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Toto je IP adresa, která je vyhrazená pro komunikaci protokolu BGP a vychází z adresního prostoru přidruženého k `GatewaySubnet` virtuální síti.          |

Následující IP adresy a adresní prostory se vztahují k místní síti (kde je nasazené zařízení Azure Stack Edge pro R).

|Název parametru  |Description  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Toto je adresní prostor vaší soukromé IP adresy.       |
|CustomerPublicNetworkAddressSpace    |  Toto je adresní prostor vaší veřejné IP adresy.       |
|DbeIOTNetworkAddressSpace    |Tato IP adresa je vyhrazená službou IoT. Neměňte tento parametr.        |
|AzureVPNsharedKey    |Tento sdílený klíč se používá během vytváření prostředku připojení Azure VPN. Tento klíč je také k dispozici jako sdílený tajný klíč sítě VPN během konfigurace místního webového uživatelského rozhraní sítě VPN.         |
|DBE-Gateway-IPAddress   | Veřejná IP adresa pro zařízení Azure Stack Edge pro R. To nemusí být známé a skript můžete spustit se zástupnou IP adresou. Bránu místní sítě upravte později pomocí skutečné IP adresy.     |

#### <a name="caveats-to-keep-in-mind"></a>Upozornění, která je potřeba vzít v úvahu:

- Nemáte IP adresu zařízení Azure Stack Edge pro R. K vytvoření prostředku můžete použít zástupnou IP adresu a později upravit bránu místní sítě Azure, abyste přiřadili skutečnou IP adresu zařízení a adresní prostor místní sítě pro dané zařízení.
- V závislosti na směru ze IETF na internetu Assigned Numbers Authority (IANA) použijte jakoukoli podsíť z 172.16. x. y do 172.24. z.a. Vyhrazujeme si rozsah adres 172,24 IPv4 pro síť Azure.

### <a name="run-the-script"></a>Spuštění skriptu

Pomocí těchto kroků můžete použít upravený `parameters.json` a spustit skript k vytvoření prostředků Azure.

1. Spusťte PowerShell jako správce.

2. Přejděte do adresáře, ve kterém se skript nachází.

3. Spusťte skript.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    Ukázkový výstup najdete níž.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    Spuštění skriptu trvá přibližně 90 minut. Po dokončení skriptu se protokol nasazení vygeneruje ve stejné složce, ve které se nachází skript.


## <a name="verify-the-azure-resources"></a>Ověření prostředků Azure

Po úspěšném spuštění skriptu ověřte, že se v Azure vytvořily všechny prostředky.

Dále nakonfigurujete síť VPN v místním webovém uživatelském rozhraní vašeho zařízení.


## <a name="vpn-configuration-on-the-device"></a>Konfigurace sítě VPN na zařízení

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>Ověření sítě VPN

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>Ověření přenosu dat prostřednictvím sítě VPN

Pokud chcete ověřit, že síť VPN funguje, zkopírujte data do sdílené složky SMB. Postupujte podle kroků v části [přidání sdílené složky](azure-stack-edge-gpu-manage-shares.md#add-a-share) na zařízení Azure Stack Edge pro R. 

1. Zkopírujte soubor, například \data\pictures\waterfall.jpg do sdílené složky protokolu SMB, kterou jste připojili v klientském systému. 
2. Ověřte, že se tento soubor zobrazuje v účtu úložiště v cloudu.

Ověření, že data procházejí přes síť VPN:

1. Otevřete prostředek připojení, který je součástí skupiny prostředků. 

2. Ověřte data v poli a výstupní hodnota.
 
3. Otevřete bránu Virtual Network ve vaší skupině prostředků. Prohlédněte si grafy **celkového tunelového propojení** a **celkového odchozího tunelového propojení**.
 
## <a name="debug-issues"></a>Problémy ladění

Chcete-li ladit jakékoli problémy, použijte následující příkazy:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Ukázkový výstup je uveden níže:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Další kroky

[Konfigurace sítě VPN prostřednictvím místního uživatelského rozhraní na zařízení Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
