---
title: Konfigurace sítě VPN na Azure Stack hraničních zařízeních v R pomocí Azure PowerShell
description: Popisuje, jak nakonfigurovat síť VPN na Azure Stack hraničních zařízeních v síti R pomocí Azure PowerShell skriptu k vytváření prostředků Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 9fa4c678a04342b47601f81ede7c49ab841f42ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102630958"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>Nakonfigurujte síť VPN na Azure Stack hraničních zařízeních v R pomocí Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

Možnost sítě VPN poskytuje druhou vrstvu šifrování pro přenos dat v rámci protokolu *TLS* z Azure Stack hraničního zařízení od mini r nebo Azure Stack Edge pro R do Azure. Přes Azure Portal nebo prostřednictvím Azure PowerShell můžete nakonfigurovat síť VPN na zařízení Azure Stack hraničních připojeních R. 

Tento článek popisuje kroky potřebné ke konfiguraci sítě VPN typu Point-to-Site (P2S) na Azure Stack hraničních zařízeních v síti R pomocí Azure PowerShell skriptu pro vytvoření konfigurace v cloudu. Konfigurace na hraničním zařízení Azure Stack se provádí prostřednictvím místního uživatelského rozhraní.

## <a name="about-vpn-setup"></a>Informace o nastavení sítě VPN

Připojení brány VPN P2S vám umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů nebo zařízení s Azure Stack hraničním spojením R. Spustíte připojení P2S z klientského počítače nebo zařízení. Připojení P2S v tomto případě používá IKEv2 VPN, což je řešení IPsec VPN založené na standardech.

Typický pracovní postup zahrnuje následující kroky:

1. Konfigurace požadavků:
2. Nastavte potřebné prostředky v Azure.
    1. Vytvořte a nakonfigurujte virtuální síť a požadované podsítě. 
    2. Vytvořte a nakonfigurujte bránu Azure VPN Gateway (bránu virtuální sítě).
    3. Nastavte Azure Firewall a přidejte pravidla sítě a aplikace.
    4. Vytvořte směrovací tabulky Azure a přidejte trasy.
    5. Povolení připojení typu Point-to-site v bráně VPN Gateway.
        1. Přidejte fond adres klienta.
        2. Nakonfigurujte typ tunelu.
        3. Nakonfigurujte typ ověřování.
        4. Vytvořit certifikát.
        5. Nahrajte certifikát.
    6. Stáhněte si telefonní seznam.
3. Nastavte VPN v místním webovém uživatelském rozhraní zařízení. 
    1. Zadejte telefonní seznam.
    2. Zadejte soubor JSON (Service Tags).


Podrobné pokyny jsou uvedené v následujících částech.

## <a name="configure-prerequisites"></a>Konfigurovat požadavky

- Měli byste mít přístup k zařízení Azure Stackového Miniku R, které je nainstalované podle pokynů v tématu [instalace Azure Stack hraničního zařízení r](azure-stack-edge-mini-r-deploy-install.md). Toto zařízení bude navázat připojení P2S s Azure. 

- Měli byste mít přístup k platnému předplatnému Azure, které je povolené pro službu Azure Stack Edge v Azure. Pomocí tohoto předplatného můžete vytvořit odpovídající prostředek v Azure, abyste mohli spravovat Azure Stack hraničních zařízení v jazyce R.  

- Máte přístup k klientovi se systémem Windows, který budete používat pro přístup k zařízení s Azure Stackovým malým R. Tento klient použijete k programovému vytvoření konfigurace v cloudu.

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
        Pokud chcete nakonfigurovat VPN, zadejte název předplatného Azure, které používáte s vaším zařízením Azure Stack hraničního připojení s konektorem R.

    3. [Stáhněte si skript](https://aka.ms/ase-vpn-deployment) potřebný k vytvoření konfigurace v cloudu. Tento skript:
        
        - Vytvořte virtuální síť Azure a následující podsítě: *GatewaySubnet* a *AzureFirewallSubnet*.
        - Vytvořte a nakonfigurujte bránu Azure VPN Gateway.
        - Vytvořte a nakonfigurujte bránu místní sítě Azure.
        - Vytvořte a nakonfigurujte připojení Azure VPN mezi bránou Azure VPN a bránou místní sítě.
        - Vytvořte Azure Firewall a přidejte Síťová pravidla, pravidla aplikací.
        - Vytvořte směrovací tabulku Azure a přidejte do ní trasy.

    4. Vytvořte skupinu prostředků v Azure Portal, v rámci které chcete vytvořit prostředky Azure. Do seznamu služeb v Azure Portal vyberte možnost **Skupina prostředků** a pak vyberte **+ Přidat**. Zadejte informace o předplatném a název vaší skupiny prostředků a potom vyberte **vytvořit**. Pokud přejdete na tuto skupinu prostředků, neměla by v tuto chvíli existovat žádné prostředky.

        ![Skupina prostředků Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. Pro Azure Stack hraničního zařízení v jazyce R budete muset mít ve formátu kódování Base 64ed Certificate `.cer` . Tento certifikát by měl být nahrán do zařízení Azure Stack Edge jako `pfx` s privátním klíčem. Tento certifikát je také nutné nainstalovat do důvěryhodného kořenového adresáře úložiště v klientovi, který se pokouší vytvořit připojení P2S.

## <a name="use-the-script"></a>Použití skriptu

Nejprve `parameters-p2s.json` soubor upravte a zadejte parametry. Potom skript spustíte pomocí upraveného souboru JSON.

Každý z těchto kroků je popsán v následujících částech.

### <a name="download-service-tags-file"></a>Stáhnout soubor značek služby

Ve složce, do které `ServiceTags.json` jste stáhli skript, už možná máte soubor. Pokud ne, můžete si stáhnout soubor značek služby.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Upravit soubor parametrů

Prvním krokem je upravit `parameters-p2s.json` soubor a uložit změny. 

Pro prostředky Azure, které vytvoříte, zadejte následující názvy:

|Název parametru  |Description  |
|---------|---------|
|virtualNetworks_vnet_name    | Název Virtual Network Azure        |
|azureFirewalls_firewall_name     | Název Azure Firewall        |
|routeTables_routetable_name     | Název směrovací tabulky Azure        |
|publicIPAddresses_VNGW_public_ip_name     | Název veřejné IP adresy pro vaši bránu virtuální sítě       |
|virtualNetworkGateways_VNGW_name    | Název brány Azure VPN Gateway (Brána virtuální sítě)        |
|publicIPAddresses_firewall_public_ip_name     | Název veřejné IP adresy pro váš Azure Firewall         |
|location     |To je oblast, ve které chcete vytvořit virtuální síť. Vyberte stejnou oblast jako ta, která je přidružená k vašemu zařízení.         |
|RouteTables_routetable_onprem_name| Toto je název další směrovací tabulky, která bráně firewall může přejít zpět na Azure Stack hraniční zařízení. Skript vytvoří dvě další trasy a přidruží *výchozí* a *FirewallSubnet* s touto směrovací tabulkou.|

Zadejte následující IP adresy a adresní prostory pro prostředky Azure, které jsou vytvořené včetně virtuální sítě a přidružených podsítí (*výchozí* *Brána firewall*, *GatewaySubnet*).

|Název parametru  |Description  |
|---------|---------|
|VnetIPv4AddressSpace    | Toto je adresní prostor přidružený k vaší virtuální síti. Zadejte rozsah IP adres virtuální sítě jako rozsah privátních IP adres ( https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) .     |
|DefaultSubnetIPv4AddressSpace    |Toto je adresní prostor přidružený k `Default` podsíti vaší virtuální sítě.         |
|FirewallSubnetIPv4AddressSpace    |Toto je adresní prostor přidružený k `Firewall` podsíti vaší virtuální sítě.          |
|GatewaySubnetIPv4AddressSpace    |Toto je adresní prostor přidružený k `GatewaySubnet` vaší virtuální síti.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Toto je IP adresa, která je vyhrazená pro komunikaci protokolu BGP a vychází z adresního prostoru přidruženého k `GatewaySubnet` virtuální síti.          |
|ClientAddressPool    | Tato IP adresa se používá pro fond adres v konfiguraci P2S v Azure Portal.         |
|PublicCertData     | Data veřejného certifikátu používá VPN Gateway k ověřování klientů P2S, kteří se k němu připojují. Pokud chcete získat data certifikátu, nainstalujte kořenový certifikát. Zajistěte, aby byl certifikát Base-64 kódovaný s příponou. cer. Otevřete tento certifikát a zkopírujte text v certifikátu mezi = = BEGIN CERTIFICATE = = a = = END CERTIFICATE = = v jednom souvislém řádku.     |



### <a name="run-the-script"></a>Spuštění skriptu

Pomocí těchto kroků můžete použít upravený `parameters-p2s.json` a spustit skript k vytvoření prostředků Azure.

1. Spusťte PowerShell. Přejděte do adresáře, ve kterém se skript nachází.

3. Spusťte skript.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > V této verzi skript funguje pouze v Východní USA umístění.

    Při spuštění skriptu budete muset zadat následující informace:

    
    |Parametr  |Popis  |
    |---------|---------|
    |Umístění     |To je oblast, ve které musí být prostředky Azure vytvořeny.         |
    |AzureAppRuleFilePath     | Toto je cesta k souboru pro `appRule.json` .       |
    |AzureIPRangesFilePath     |Toto je soubor JSON značky služby, který jste si stáhli v předchozím kroku.         |
    |ResourceGroupName     | Toto je název skupiny prostředků, pod kterou se vytvoří všechny prostředky Azure.        |
    |AzureDeploymentName    |Toto je název vašeho nasazení Azure.         |
    |NetworkRuleCollectionName            | Toto je název pro kolekci všech vytvořených síťových pravidel a jejich přidání do Azure Firewall.             |
    |Priorita            | Toto je priorita přiřazená všem vytvořeným pravidlům sítě a aplikace.              |
    |AppRuleCollectionName            |Toto je název pro kolekci všech pravidel aplikací, která jsou vytvořena a přidána do Azure Firewall.                |


    Ukázkový výstup najdete níž.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - Spuštění skriptu trvá přibližně 90 minut. Nezapomeňte se před spuštěním skriptu přihlásit přímo do vaší sítě.
    > - Pokud z nějakého důvodu dojde k neúspěšné relaci se skriptem, nezapomeňte odstranit skupinu prostředků, abyste odstranili všechny prostředky, které v ní byly vytvořeny.
  
    
    Po dokončení skriptu se protokol nasazení vygeneruje ve stejné složce, ve které se nachází skript.


## <a name="verify-the-azure-resources"></a>Ověření prostředků Azure

Po úspěšném spuštění skriptu ověřte, že se v Azure vytvořily všechny prostředky. Přejít do skupiny prostředků, kterou jste vytvořili. Měli byste vidět následující zdroje informací:

![Prostředky Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>Stáhnout telefonní seznam pro profil sítě VPN

V tomto kroku stáhnete profil sítě VPN pro vaše zařízení.

1. V Azure Portal přejdete do skupiny prostředků a pak vyberete bránu virtuální sítě, kterou jste vytvořili v předchozím kroku.

    ![Brána virtuální sítě Azure](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Přejděte na **nastavení > konfigurace Point-to-site**. Vyberte **stáhnout klienta VPN**.

    ![Povolit P2S konfiguraci 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Uložte si profil zip a extrahujte ho do svého klienta Windows.

    ![Povolit P2S konfiguraci 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. Do složky *WindowsAmd64* a potom rozbalte `.exe` : *VpnClientSetupAmd64.exe*.

    ![Povolit P2S konfiguraci 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Vytvořte dočasnou cestu. Například:

    `C:\NewTemp\vnet\tmp`

4. Spusťte PowerShell a pokračujte do adresáře, kde `.exe` se nachází. Chcete-li provést `.exe` příkaz, zadejte:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. Dočasná cesta bude obsahovat nové soubory. Zde je ukázkový výstup:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. Soubor *. pbk* je telefonní seznam pro profil sítě VPN. Budete ho používat v místním uživatelském rozhraní.


## <a name="vpn-configuration-on-the-device"></a>Konfigurace sítě VPN na zařízení

Postupujte podle těchto kroků v místním uživatelském rozhraní Azure Stack hraničního zařízení.

1. V místním uživatelském rozhraní přejdete na stránku **VPN** . V části stav sítě VPN vyberte **Konfigurovat**.

    ![Konfigurace sítě VPN 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. V okně **Konfigurace sítě VPN** :
    
    1. V souboru pro nahrání telefonního seznamu ukažte na soubor. pbk, který jste vytvořili v předchozím kroku.
    2. V souboru pro nahrání konfigurace veřejného seznamu IP adres zadejte jako vstup soubor JSON rozsahu IP adres datového centra Azure. Tento soubor jste stáhli v předchozím kroku z: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
    3. Jako oblast vyberte **eastus** a vyberte **použít**.

    ![Konfigurace sítě VPN 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. V části **rozsahy IP adres, ke kterým se má přistupovat jenom pomocí sítě VPN** , zadejte rozsah adres IPv4, který jste zvolili pro Azure Virtual Network.

    ![Konfigurace sítě VPN 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>Ověřit připojení klienta

1. V Azure Portal přejít na bránu VPN.
2. Přejděte na **nastavení > konfigurace Point-to-site**. V části **přidělené IP adresy** by se měla zobrazit IP adresa vašeho zařízení Azure Stack Edge.

## <a name="validate-data-transfer-through-vpn"></a>Ověření přenosu dat prostřednictvím sítě VPN

Pokud chcete ověřit, že síť VPN funguje, zkopírujte data do sdílené složky SMB. Postupujte podle kroků v části [přidání sdílené složky](azure-stack-edge-gpu-manage-shares.md#add-a-share) na zařízení Azure Stack Edge. 

1. Zkopírujte soubor, například \data\pictures\waterfall.jpg do sdílené složky protokolu SMB, kterou jste připojili v klientském systému. 
2. Ověření, že data procházejí přes síť VPN, zatímco se kopírují data:

    1. V Azure Portal přejít na bránu VPN. 

    2. Přejít na **monitorování > metriky**.

    3. V pravém podokně vyberte **obor** jako bránu sítě VPN, **METRIKu** jako P2Sou šířku pásma brány a **agregaci** jako střední.

    4. Při kopírování dat se zobrazí zvýšení využití šířky pásma a po dokončení kopírování dat. využití šířky pásma se vynechá.

        ![Metriky Azure VPN](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Ověřte, že se tento soubor zobrazuje v účtu úložiště v cloudu.
 
## <a name="debug-issues"></a>Problémy ladění

Chcete-li ladit jakékoli problémy, použijte následující příkazy:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

Ukázkový výstup je uveden níže:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
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

[NAKONFIGURUJTE VPN přes místní uživatelské rozhraní na zařízení Azure Stack Edge](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).