---
title: Konfigurace sítě VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure | Microsoft Docs
description: Jak nakonfigurovat síť VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b988435fc6928d52321cb427e2412e7ca81680d2
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73141742"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Konfigurace sítě VPN typu Point-to-Site (P2S) na platformě Linux pro použití se soubory Azure
Pomocí připojení VPN typu Point-to-Site (P2S) můžete připojit sdílené složky Azure přes protokol SMB mimo Azure bez nutnosti otevírat port 445. Připojení VPN typu Point-to-site je připojení VPN mezi Azure a jednotlivými klienty. Pokud chcete použít připojení VPN P2S se soubory Azure, bude nutné nakonfigurovat připojení VPN P2S pro každého klienta, který se chce připojit. Pokud máte mnoho klientů, kteří se potřebují připojit ke sdíleným složkám Azure ze své místní sítě, můžete místo připojení typu Point-to-site pro každého klienta použít připojení VPN typu Site-to-Site (S2S). Další informace najdete v tématu [Konfigurace sítě Site-to-Site VPN pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).

Důrazně doporučujeme, abyste si přečetli [Přehled sítě Azure Files](storage-files-networking-overview.md) , než budete pokračovat v tomto článku, abyste mohli získat úplnou diskusi o možnostech sítě dostupných pro soubory Azure.

Tento článek podrobně popisuje postup konfigurace sítě VPN typu Point-to-site v systému Linux pro připojení sdílených složek Azure přímo v místním prostředí. Pokud chcete směrovat Azure File Sync provoz přes síť VPN, přečtěte si téma [Konfigurace nastavení proxy serveru Azure File Sync a brány firewall](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Předpoklady
- Nejnovější verze rozhraní příkazového řádku Azure CLI. Další informace o tom, jak nainstalovat rozhraní příkazového řádku Azure, najdete v tématu [instalace rozhraní příkazového řádku Azure PowerShell](https://docs.microsoft.com/cli/azure/install-azure-cli) a výběr operačního systému. Pokud upřednostňujete použití modulu Azure PowerShell v systému Linux, může se stát, že následující pokyny jsou k dispozici pro rozhraní příkazového řádku Azure CLI.

- Sdílená složka Azure, kterou byste chtěli místně připojit. K síti VPN typu Point-to-site můžete použít buď službu Azure [Standard](storage-how-to-create-file-share.md) , nebo [Premium](storage-how-to-create-premium-fileshare.md) .

## <a name="install-required-software"></a>Nainstalovat požadovaný software
Brána virtuální sítě Azure může poskytovat připojení k síti VPN pomocí několika protokolů VPN, včetně protokolu IPsec a OpenVPN. V této příručce se dozvíte, jak používat protokol IPsec a pomocí balíčku klient strongswan poskytovat podporu pro Linux. 

> Ověřeno s Ubuntu 18,10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Nasazení virtuální sítě 
Abyste měli přístup ke sdílené složce Azure a dalším prostředkům Azure z místního prostředí prostřednictvím sítě VPN typu Point-to-site, musíte vytvořit virtuální síť nebo virtuální síť. P2S připojení VPN, které vytvoříte automaticky, je most mezi místním počítačem se systémem Linux a touto virtuální sítí Azure.

Následující skript vytvoří virtuální síť Azure se třemi podsítěmi: jednu pro koncový bod služby účtu úložiště, jednu pro privátní koncový bod vašeho účtu úložiště, která je nutná pro přístup k místnímu účtu úložiště bez vytvoření vlastní. směrování pro veřejnou IP adresu účtu úložiště, která se může změnit, a jednu pro bránu virtuální sítě, která poskytuje službu VPN. 

Nezapomeňte nahradit `<region>`, `<resource-group>`a `<desired-vnet-name>` odpovídajícími hodnotami pro vaše prostředí.

```bash
region="<region>"
resourceGroupName="<resource-group>"
virtualNetworkName="<desired-vnet-name>"

virtualNetwork=$(az network vnet create \
    --resource-group $resourceGroupName \
    --name $virtualNetworkName \
    --location $region \
    --address-prefixes "192.168.0.0/16" \
    --query "newVNet.id" | tr -d '"')

serviceEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "ServiceEndpointSubnet" \
    --address-prefixes "192.168.0.0/24" \
    --service-endpoints "Microsoft.Storage" \
    --query "id" | tr -d '"')

privateEndpointSubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "PrivateEndpointSubnet" \
    --address-prefixes "192.168.1.0/24" \
    --query "id" | tr -d '"')

gatewaySubnet=$(az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $virtualNetworkName \
    --name "GatewaySubnet" \
    --address-prefixes "192.168.2.0/24" \
    --query "id" | tr -d '"')
```

## <a name="restrict-the-storage-account-to-the-virtual-network"></a>Omezení účtu úložiště na virtuální síť
Ve výchozím nastavení se při vytváření účtu úložiště k němu dostanete odkudkoli na světě, pokud máte prostředky k ověření vaší žádosti (například s vaší identitou služby Active Directory nebo s klíčem účtu úložiště). Pokud chcete omezit přístup k tomuto účtu úložiště na virtuální síť, kterou jste právě vytvořili, musíte vytvořit sadu síťových pravidel, která umožňuje přístup v rámci virtuální sítě a všem ostatním přístupům odepřít.

Omezení účtu úložiště na virtuální síť vyžaduje použití koncového bodu služby. Koncový bod služby je konstrukce sítě, pomocí které se veřejná IP adresa DNS nebo veřejná IP adresa dá použít jenom v rámci virtuální sítě. Vzhledem k tomu, že veřejná IP adresa není zaručená, že by měla zůstat stejná, doporučujeme pro účet úložiště použít místo koncového bodu služby privátní koncový bod, ale nemůžete tento účet úložiště omezit, pokud není taky vystavený koncový bod služby.

Nezapomeňte nahradit `<storage-account-name>` účtem úložiště, ke kterému chcete získat přístup.

```bash
storageAccountName="<storage-account-name>"

az storage account network-rule add \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --subnet $serviceEndpointSubnet > /dev/null

az storage account update \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --bypass "AzureServices" \
    --default-action "Deny" > /dev/null
```

## <a name="create-a-private-endpoint-preview"></a>Vytvoření privátního koncového bodu (Preview)
Vytvoření privátního koncového bodu pro váš účet úložiště poskytuje účtu úložiště IP adresu v adresním prostoru IP adres vaší virtuální sítě. Když připojíte sdílenou složku Azure z místní sítě pomocí této privátní IP adresy, pravidla směrování automaticky definovaná při instalaci sítě VPN směrují vaši žádost o připojení k účtu úložiště přes síť VPN. 

```bash
zoneName="privatelink.file.core.windows.net"

storageAccount=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "id" | tr -d '"')

az resource update \
    --ids $privateEndpointSubnet \
    --set properties.privateEndpointNetworkPolicies=Disabled > /dev/null

az network private-endpoint create \
    --resource-group $resourceGroupName \
    --name "$storageAccountName-PrivateEndpoint" \
    --location $region \
    --subnet $privateEndpointSubnet \
    --private-connection-resource-id $storageAccount \
    --group-ids "file" \
    --connection-name "privateEndpointConnection" > /dev/null

az network private-dns zone create \
    --resource-group $resourceGroupName \
    --name $zoneName > /dev/null

az network private-dns link vnet create \
    --resource-group $resourceGroupName \
    --zone-name $zoneName \
    --name "$virtualNetworkName-link" \
    --virtual-network $virtualNetworkName \
    --registration-enabled false > /dev/null

networkInterfaceId=$(az network private-endpoint show \
    --name "$storageAccountName-PrivateEndpoint" \
    --resource-group $resourceGroupName \
    --query 'networkInterfaces[0].id' | tr -d '"')
 
storageAccountPrivateIP=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query "properties.ipConfigurations[0].properties.privateIPAddress" | tr -d '"')

fqdnQuery="properties.ipConfigurations[0].properties.privateLinkConnectionProperties.fqdns[0]"
fqdn=$(az resource show \
    --ids $networkInterfaceId \
    --api-version 2019-04-01 \
    --query $fqdnQuery | tr -d '"')

az network private-dns record-set a create \
    --name $storageAccountName \
    --zone-name $zoneName \
    --resource-group $resourceGroupName > /dev/null
```

## <a name="create-certificates-for-vpn-authentication"></a>Vytvoření certifikátů pro ověřování sítě VPN
Aby bylo možné pro přístup k virtuální síti ověřit připojení VPN z místních počítačů se systémem Linux, je nutné vytvořit dva certifikáty: kořenový certifikát, který bude k dispozici pro bránu virtuálního počítače, a klientský certifikát, který bude Podepsáno kořenovým certifikátem. Následující skript vytvoří požadované certifikáty.

```bash
rootCertName="P2SRootCert"
username="client"
password="1234"

mkdir temp
cd temp

sudo ipsec pki --gen --outform pem > rootKey.pem
sudo ipsec pki --self --in rootKey.pem --dn "CN=$rootCertName" --ca --outform pem > rootCert.pem

rootCertificate=$(openssl x509 -in rootCert.pem -outform der | base64 -w0 ; echo)

sudo ipsec pki --gen --size 4096 --outform pem > "clientKey.pem"
sudo ipsec pki --pub --in "clientKey.pem" | \
    sudo ipsec pki \
        --issue \
        --cacert rootCert.pem \
        --cakey rootKey.pem \
        --dn "CN=$username" \
        --san $username \
        --flag clientAuth \
        --outform pem > "clientCert.pem"

openssl pkcs12 -in "clientCert.pem" -inkey "clientKey.pem" -certfile rootCert.pem -export -out "client.p12" -password "pass:$password"
```

## <a name="deploy-virtual-network-gateway"></a>Nasadit bránu virtuální sítě
Brána virtuální sítě Azure je služba, ke které se budou připojovat vaše místní počítače se systémem Linux. Nasazení této služby vyžaduje dvě základní komponenty: veřejná IP adresa, která bude bránu klientům označovat bez ohledu na to, kde se nachází na světě, a kořenový certifikát, který jste vytvořili dříve, který se použije k ověřování klientů.

Nezapomeňte nahradit `<desired-vpn-name-here>` názvem, který chcete pro tyto prostředky.

> [!Note]  
> Nasazení brány virtuální sítě Azure může trvat až 45 minut. I když se tento prostředek nasazuje, tento skript skriptu bash se zablokuje, aby se nasazení dokončilo. To se očekává.

```bash
vpnName="<desired-vpn-name-here>"
publicIpAddressName="$vpnName-PublicIP"

publicIpAddress=$(az network public-ip create \
    --resource-group $resourceGroupName \
    --name $publicIpAddressName \
    --location $region \
    --sku "Basic" \
    --allocation-method "Dynamic" \
    --query "publicIp.id" | tr -d '"')

az network vnet-gateway create \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --vnet $virtualNetworkName \
    --public-ip-addresses $publicIpAddress \
    --location $region \
    --sku "VpnGw1" \
    --gateway-typ "Vpn" \
    --vpn-type "RouteBased" \
    --address-prefixes "172.16.201.0/24" \
    --client-protocol "IkeV2" > /dev/null

az network vnet-gateway root-cert create \
    --resource-group $resourceGroupName \
    --gateway-name $vpnName \
    --name $rootCertName \
    --public-cert-data $rootCertificate \
    --output none
```

## <a name="configure-the-vpn-client"></a>Konfigurace klienta VPN
Brána virtuální sítě Azure vytvoří balíček ke stažení s konfiguračními soubory potřebnými k inicializaci připojení VPN na místním počítači se systémem Linux. Následující skript umístí certifikáty, které jste vytvořili na správném místě, a nakonfiguruje soubor `ipsec.conf` se správnými hodnotami z konfiguračního souboru v balíčku ke stažení.

```bash
vpnClient=$(az network vnet-gateway vpn-client generate \
    --resource-group $resourceGroupName \
    --name $vpnName \
    --authentication-method EAPTLS | tr -d '"')

curl $vpnClient --output vpnClient.zip
unzip vpnClient.zip

vpnServer=$(xmllint --xpath "string(/VpnProfile/VpnServer)" Generic/VpnSettings.xml)
vpnType=$(xmllint --xpath "string(/VpnProfile/VpnType)" Generic/VpnSettings.xml | tr '[:upper:]' '[:lower:]')
routes=$(xmllint --xpath "string(/VpnProfile/Routes)" Generic/VpnSettings.xml)

sudo cp "${installDir}ipsec.conf" "${installDir}ipsec.conf.backup"
sudo cp "Generic/VpnServerRoot.cer" "${installDir}ipsec.d/cacerts"
sudo cp "${username}.p12" "${installDir}ipsec.d/private" 

echo -e "\nconn $virtualNetworkName" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tkeyexchange=$vpnType" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\ttype=tunnel" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftfirewall=yes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleft=%any" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftauth=eap-tls" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftid=%client" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tright=$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightid=%$vpnServer" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\trightsubnet=$routes" | sudo tee -a "${installDir}ipsec.conf" > /dev/null
echo -e "\tleftsourceip=%config" | sudo tee -a "${installDir}ipsec.conf" > /dev/null 
echo -e "\tauto=add" | sudo tee -a "${installDir}ipsec.conf" > /dev/null

echo ": P12 client.p12 '$password'" | sudo tee -a "${installDir}ipsec.secrets" > /dev/null

sudo ipsec restart
sudo ipsec up $virtualNetworkName 
```

## <a name="mount-azure-file-share"></a>Připojení sdílené složky Azure
Teď, když jste nastavili síť VPN typu Point-to-site, můžete připojit sdílenou složku Azure. Následující příklad připojí sdílenou složku, která není trvalá. Chcete-li se připojit trvale, přečtěte si téma [použití sdílené složky Azure se systémem Linux](storage-how-to-use-files-linux.md). 

```bash
fileShareName="myshare"

mntPath="/mnt/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

smbPath="//$storageAccountPrivateIP/$fileShareName"
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

## <a name="see-also"></a>Další informace najdete v tématech
- [Přehled sítě Azure Files](storage-files-networking-overview.md)
- [Konfigurace sítě VPN typu Point-to-Site (P2S) ve Windows pro použití se soubory Azure](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurace sítě VPN typu Site-to-Site (S2S) pro použití se soubory Azure](storage-files-configure-s2s-vpn.md)