---
title: Konfigurace sítě VPN typu Point-to-Site (P2S) ve Windows pro použití se soubory Azure | Microsoft Docs
description: Jak nakonfigurovat síť VPN typu Point-to-Site (P2S) ve Windows pro použití se soubory Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6253deb53229172cd499a6aa14b8d8f19bc07b63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629253"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>Konfigurace sítě VPN typu Point-to-Site (P2S) ve Windows pro použití se soubory Azure
Pomocí připojení VPN typu Point-to-Site (P2S) můžete připojit sdílené složky Azure přes protokol SMB mimo Azure bez nutnosti otevírat port 445. Připojení VPN typu Point-to-site je připojení VPN mezi Azure a jednotlivými klienty. Pokud chcete použít připojení VPN P2S se soubory Azure, bude nutné nakonfigurovat připojení VPN P2S pro každého klienta, který se chce připojit. Pokud máte mnoho klientů, kteří se potřebují připojit ke sdíleným složkám Azure ze své místní sítě, můžete místo připojení typu Point-to-site pro každého klienta použít připojení VPN typu Site-to-Site (S2S). Další informace najdete v tématu [Konfigurace sítě Site-to-Site VPN pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).

Důrazně doporučujeme, abyste si přečetli [požadavky na síť pro přímý přístup ke sdílené složce Azure](storage-files-networking-overview.md) , než budete pokračovat v tomto článku, kde najdete kompletní informace o možnostech sítě, které jsou dostupné pro soubory Azure.

Tento článek podrobně popisuje postup konfigurace sítě VPN typu Point-to-site v systému Windows (klient Windows a Windows Server) pro připojení sdílených složek Azure přímo v místním prostředí. Pokud chcete směrovat Synchronizace souborů Azure provoz přes síť VPN, přečtěte si téma [Konfigurace nastavení proxy serveru synchronizace souborů Azure a brány firewall](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Požadavky
- Nejnovější verze modulu Azure PowerShell. Další informace o tom, jak nainstalovat Azure PowerShell, najdete v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps) a výběr operačního systému. Pokud dáváte přednost použití Azure CLI v systému Windows, může se stát, že následující pokyny jsou Azure PowerShell.

- Sdílená složka Azure, kterou byste chtěli místně připojit. Sdílené složky Azure se nasazují v rámci účtů úložiště, což jsou konstrukce správy, které představují sdílený fond úložiště, ve kterém můžete nasazovat víc sdílených složek a další prostředky úložiště, jako jsou kontejnery nebo fronty objektů BLOB. Další informace o tom, jak nasadit sdílené složky Azure a účty úložiště, najdete v tématu [Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).

- Privátní koncový bod pro účet úložiště, který obsahuje sdílenou složku Azure, kterou chcete místně připojit. Další informace o tom, jak vytvořit privátní koncový bod, najdete v tématu [Konfigurace koncových bodů sítě Azure Files](storage-files-networking-endpoints.md?tabs=azure-powershell). 

## <a name="deploy-a-virtual-network"></a>Nasazení virtuální sítě
Abyste měli přístup ke sdílené složce Azure a dalším prostředkům Azure z místního prostředí prostřednictvím sítě VPN typu Point-to-site, musíte vytvořit virtuální síť nebo virtuální síť. P2S připojení VPN, které vytvoříte automaticky, je most mezi místním počítačem s Windows a touto virtuální sítí Azure.

Následující prostředí PowerShell vytvoří virtuální síť Azure se třemi podsítěmi: jednu pro koncový bod služby účtu úložiště, jednu pro privátní koncový bod účtu úložiště, který je vyžadován pro přístup k místnímu účtu úložiště bez vytváření vlastního směrování pro veřejnou IP adresu účtu úložiště, který se může změnit, a druhý pro bránu virtuální sítě, která poskytuje službu VPN. 

Nezapomeňte nahradit `<region>` , `<resource-group>` a `<desired-vnet-name>` s odpovídajícími hodnotami pro vaše prostředí.

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

## <a name="create-root-certificate-for-vpn-authentication"></a>Vytvoření kořenového certifikátu pro ověřování sítě VPN
Aby bylo možné pro přístup k virtuální síti ověřit připojení VPN z místních počítačů s Windows, musíte vytvořit dva certifikáty: kořenový certifikát, který se poskytne bráně virtuálního počítače, a klientský certifikát, který bude podepsaný kořenovým certifikátem. Následující PowerShell vytvoří kořenový certifikát; certifikát klienta se vytvoří po vytvoření brány virtuální sítě Azure s informacemi z brány. 

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

## <a name="deploy-virtual-network-gateway"></a>Nasadit bránu virtuální sítě
Brána virtuální sítě Azure je služba, ke které se budou připojovat vaše místní počítače s Windows. Nasazení této služby vyžaduje dvě základní komponenty: veřejnou IP adresu, která bude bránu klientům označovat bez ohledu na to, kde jsou na světě, a kořenový certifikát, který jste vytvořili dříve a který se použije k ověření vašich klientů.

Nezapomeňte nahradit `<desired-vpn-name-here>` názvem, který byste chtěli pro tyto prostředky.

> [!Note]  
> Nasazení brány virtuální sítě Azure může trvat až 45 minut. Během nasazování tohoto prostředku se tento skript PowerShellu zablokuje, aby se nasazení dokončilo. To se očekává.

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
Certifikát klienta se vytvoří s identifikátorem URI brány virtuální sítě. Tento certifikát je podepsán pomocí kořenového certifikátu, který jste vytvořili dříve.

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
Brána virtuální sítě Azure vytvoří balíček ke stažení s konfiguračními soubory potřebnými k inicializaci připojení VPN na místním počítači s Windows. Nakonfigurujeme připojení VPN pomocí funkce [Always On VPN](/windows-server/remote/remote-access/vpn/always-on-vpn/) systému Windows 10/Windows Server 2016 +. Tento balíček také obsahuje spustitelné balíčky, které nakonfigurují starší verzi klienta VPN v systému Windows, pokud je to potřeba. Tato příručka používá rozhraní VPN Always On, nikoli starší klient VPN, protože klient VPN Always On umožňuje koncovým uživatelům připojit se k Azure VPN a odpojit se od něj bez oprávnění správce ke svému počítači. 

Následující skript nainstaluje klientský certifikát vyžadovaný k ověřování na bráně virtuální sítě, stáhne a nainstaluje balíček VPN. Nezapomeňte nahradit `<computer1>` a `<computer2>` pomocí požadovaných počítačů. Tento skript můžete spustit na tolik počítačů, kolik jich budete chtít, přidáním dalších relací PowerShellu do `$sessions` pole. Váš účet použití musí být správce na každém z těchto počítačů. Pokud je jedním z těchto počítačů místní počítač, ze kterého spouštíte skript, musíte spustit skript z relace PowerShellu se zvýšenými oprávněními. 

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
Teď, když jste nastavili síť VPN typu Point-to-site, můžete ji použít k připojení sdílené složky Azure na počítačích, které nastavíte prostřednictvím PowerShellu. Následující příklad připojí sdílenou složku, vypíše kořenový adresář sdílené složky, aby prokáže, že sdílená složka je skutečně připojena, a odpojte sdílenou složku. Sdílenou složku bohužel není možné trvale připojit přes vzdálenou komunikaci PowerShellu. Chcete-li se připojit trvale, přečtěte si téma [použití sdílené složky Azure v systému Windows](storage-how-to-use-files-windows.md). 

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
- [Požadavky na sítě pro přímý přístup ke sdíleným složkám Azure](storage-files-networking-overview.md)
- [Konfigurace sítě VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure](storage-files-configure-p2s-vpn-linux.md)
- [Konfigurace sítě VPN typu Site-to-Site (S2S) pro použití se soubory Azure](storage-files-configure-s2s-vpn.md)