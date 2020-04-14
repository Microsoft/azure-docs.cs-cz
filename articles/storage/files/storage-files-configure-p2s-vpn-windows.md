---
title: Konfigurace sítě VPN point-to-site (P2S) v systému Windows pro použití se soubory Azure | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat VPN point-to-site (P2S) v systému Windows pro použití se soubory Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95386af4522adca1d65e04b01c2a349a80e9ab8a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273473"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Konfigurace sítě VPN point-to-site (P2S) v systému Windows pro použití se soubory Azure
Připojení VPN z bodu na webu (P2S) můžete použít k připojení sdílených složek Azure přes SMB mimo Azure, aniž byste museli otevřít port 445. Připojení VPN bodu k webu je připojení VPN mezi Azure a jednotlivým klientem. Chcete-li použít připojení P2S VPN se soubory Azure, připojení P2S VPN bude muset být nakonfigurováno pro každého klienta, který se chce připojit. Pokud máte mnoho klientů, kteří se potřebují připojit ke sdíleným složekm Azure z místní sítě, můžete místo připojení typu Point-to-Site pro každého klienta použít připojení VPN site-to-site (Site-to-Site). Další informace najdete [v tématu Konfigurace sítě VPN mezi lokalitami pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).

Důrazně doporučujeme, abyste si [přečetli aspekty sítě pro přímý přístup ke sdílené složce Azure,](storage-files-networking-overview.md) než budete pokračovat v tomto článku pro úplnou diskusi o možnostech sítě, které jsou k dispozici pro soubory Azure.

V článku jsou podrobně kroky konfigurace sítě VPN s bodem na místo v systému Windows (klient Windows a Windows Server) pro připojení sdílených složek Azure přímo místně. Pokud chcete směrovat provoz Azure File Sync přes SÍŤ VPN, přečtěte si prosím [konfiguraci nastavení proxy serveru Azure File Sync a brány firewall](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Požadavky
- Nejnovější verze modulu Azure PowerShell. Další informace o tom, jak nainstalovat Azure PowerShell, najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) a vyberte operační systém. Pokud dáváte přednost použití azure cli v systému Windows, můžete, ale níže uvedené pokyny jsou uvedeny pro Azure PowerShell.

- Sdílená složka Azure, kterou chcete připojit místně. Sdílené složky Azure se nasazují v rámci účtů úložiště, což jsou konstrukce správy, které představují sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek a další prostředky úložiště, jako jsou kontejnery objektů blob nebo fronty. Další informace o nasazení sdílených složek a úložiště Azure najdete v [části Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).

- Soukromý koncový bod pro účet úložiště obsahující sdílenou složku Azure, kterou chcete připojit místně. Další informace o tom, jak vytvořit privátní koncový bod, najdete [v tématu Konfigurace síťových koncových bodů Souborů Azure](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Nasazení virtuální sítě
Chcete-li získat přístup ke sdílené složce Azure a dalším prostředkům Azure z místní sítě prostřednictvím sítě VPN s bodem na webu, musíte vytvořit virtuální síť nebo virtuální síť. Připojení Vpn P2S, které automaticky vytvoříte, je mostem mezi místním počítačem se systémem Windows a touto virtuální sítí Azure.

Následující PowerShell vytvoří virtuální síť Azure se třemi podsítěmi: jednu pro koncový bod služby vašeho účtu úložiště, jednu pro soukromý koncový bod vašeho účtu úložiště, která je vyžadována pro přístup k účtu úložiště v místním prostředí bez vytvoření vlastního směrování pro veřejnou IP adresu účtu úložiště, která se může změnit, a pro bránu virtuální sítě, která poskytuje službu VPN. 

Nezapomeňte nahradit `<region>` `<resource-group>`, `<desired-vnet-name>` a příslušné hodnoty pro vaše prostředí.

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>Vytvoření kořenového certifikátu pro ověřování pomocí sítě VPN
Aby bylo možné ověřit připojení VPN z místních počítačů se systémem Windows pro přístup k vaší virtuální síti, musíte vytvořit dva certifikáty: kořenový certifikát, který bude poskytnut bráně virtuálního počítače, a klientský certifikát, který bude podepsán kořenovým certifikátem. Následující prostředí PowerShell vytvoří kořenový certifikát. klientský certifikát se vytvoří po vytvoření brány virtuální sítě Azure s informacemi z brány. 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>Nasazení brány virtuální sítě
Brána virtuální sítě Azure je služba, ke které se připojí vaše místní počítače s Windows. Nasazení této služby vyžaduje dvě základní součásti: veřejnou IP adresu, která identifikuje bránu pro vaše klienty, ať jsou kdekoli na světě, a kořenový certifikát, který jste vytvořili dříve a který bude použit k ověření vašich klientů.

Nezapomeňte nahradit `<desired-vpn-name-here>` název, který chcete pro tyto prostředky.

> [!Note]  
> Nasazení brány virtuální sítě Azure může trvat až 45 minut. Během nasazování tohoto prostředku bude tento skript prostředí PowerShell blokovat pro dokončení nasazení. To se očekává.

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>Vytvořit klientský certifikát
Klientský certifikát je vytvořen pomocí identifikátoru URI brány virtuální sítě. Tento certifikát je podepsán kořenovým certifikátem, který jste vytvořili dříve.

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>Konfigurace klienta VPN
Brána virtuální sítě Azure vytvoří balíček ke stažení s konfiguračními soubory potřebnými k inicializaci připojení VPN v místním počítači s Windows. Připojení VPN nakonfigurujeme pomocí funkce [Always On VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) systému Windows 10/Windows Server 2016+. Tento balíček také obsahuje spustitelné balíčky, které budou v případě potřeby konfigurovat starší klienta Windows VPN. Tato příručka používá vždy na VPN spíše než starší klient a windows VPN jako vždy na VPN klient umožňuje koncovým uživatelům připojit nebo odpojit od Azure VPN bez oprávnění správce k jejich počítači. 

Následující skript nainstaluje klientský certifikát potřebný pro ověřování proti bráně virtuální sítě, stáhne a nainstaluje balíček VPN. Nezapomeňte nahradit `<computer1>` `<computer2>` a požadované počítače. Tento skript můžete spustit na tolika počítačích, kolik chcete `$sessions` přidáním dalších relací prostředí PowerShell do pole. Váš účet použití musí být správcem každého z těchto počítačů. Pokud jeden z těchto počítačů je místní počítač, ze které skript spouštěte, je nutné spustit skript z relace prostředí PowerShell se zvýšenými oprávněními. 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>Připojení sdílené složky Azure
Teď, když jste nastavili vpn bodu na místo, můžete ji použít k připojení sdílené složky Azure v počítačích, které nastavíte prostřednictvím prostředí PowerShell. Následující příklad připojí sdílenou složku, zobrazí kořenový adresář sdílené složky, aby se prokázalo, že sdílená složka je skutečně připojena, a odpojit sdílenou složku. Bohužel není možné trvale připojit sdílenou složku přes vzdálené předání prostředí PowerShell. Pokud se chcete trvale připojit, přečtěte si část [Použití sdílené složky Azure ve Windows](storage-how-to-use-files-windows.md). 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>Viz také
- [Důležité informace o vytváření sítí pro přímý přístup ke sdílené složce Azure](storage-files-networking-overview.md)
- [Konfigurace vpn point-to-site (P2S) na Linuxu pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md)
- [Konfigurace sítě VPN site-to-site (S2S) pro použití se soubory Azure](storage-files-configure-s2s-vpn.md)
