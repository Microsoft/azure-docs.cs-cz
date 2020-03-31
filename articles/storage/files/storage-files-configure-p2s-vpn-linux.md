---
title: Konfigurace sítě VPN point-to-site (P2S) v Linuxu pro použití se soubory Azure | Dokumenty společnosti Microsoft
description: Jak nakonfigurovat VPN point-to-site (P2S) na Linuxu pro použití se soubory Azure
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cfff05ed52258ee448d83a521b99dca7d356a0f9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061049"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-linux-for-use-with-azure-files"></a>Konfigurace vpn point-to-site (P2S) na Linuxu pro použití se soubory Azure
Připojení VPN z bodu na webu (P2S) můžete použít k připojení sdílených složek Azure přes SMB mimo Azure, aniž byste museli otevřít port 445. Připojení VPN bodu k webu je připojení VPN mezi Azure a jednotlivým klientem. Chcete-li použít připojení P2S VPN se soubory Azure, připojení P2S VPN bude muset být nakonfigurováno pro každého klienta, který se chce připojit. Pokud máte mnoho klientů, kteří se potřebují připojit ke sdíleným složekm Azure z místní sítě, můžete místo připojení typu Point-to-Site pro každého klienta použít připojení VPN site-to-site (Site-to-Site). Další informace najdete [v tématu Konfigurace sítě VPN mezi lokalitami pro použití se soubory Azure](storage-files-configure-s2s-vpn.md).

Důrazně doporučujeme, abyste si přečetli [přehled sítí Azure Files,](storage-files-networking-overview.md) než budete pokračovat v tomto článku pro úplnou diskusi o možnostech sítě, které jsou k dispozici pro soubory Azure.

Článek podrobně popisuje kroky konfigurace vpn point-to-site na Linuxu pro připojení sdílených složek Azure přímo místně. Pokud chcete směrovat provoz Azure File Sync přes SÍŤ VPN, přečtěte si prosím [konfiguraci nastavení proxy serveru Azure File Sync a brány firewall](storage-sync-files-firewall-and-proxy.md).

## <a name="prerequisites"></a>Požadavky
- Nejnovější verze azure cli. Další informace o tom, jak nainstalovat příkazový příkaz k příkazu Kandidata Azure, najdete [v tématu Instalace příkazového příkazového příkazu k prostředí Azure PowerShell](https://docs.microsoft.com/cli/azure/install-azure-cli) a výběr operačního systému. Pokud dáváte přednost použití modulu Azure PowerShell na Linuxu, můžete, ale níže uvedené pokyny jsou uvedeny pro Azure CLI.

- Sdílená složka Azure, kterou chcete připojit místně. Sdílené složky Azure se nasazují v rámci účtů úložiště, což jsou konstrukce správy, které představují sdílený fond úložiště, ve kterém můžete nasadit více sdílených složek a další prostředky úložiště, jako jsou kontejnery objektů blob nebo fronty. Další informace o nasazení sdílených složek a úložiště Azure najdete v [části Vytvoření sdílené složky Azure](storage-how-to-create-file-share.md).

- Soukromý koncový bod pro účet úložiště obsahující sdílenou složku Azure, kterou chcete připojit místně. Další informace o tom, jak vytvořit privátní koncový bod, najdete [v tématu Konfigurace síťových koncových bodů Souborů Azure](storage-files-networking-endpoints.md?tabs=azure-cli). 

## <a name="install-required-software"></a>Instalace požadovaného softwaru
Brána virtuální sítě Azure může poskytovat připojení VPN pomocí několika protokolů VPN, včetně IPsec a OpenVPN. Tato příručka ukazuje, jak používat IPsec a používá balíček strongSwan k poskytování podpory na Linuxu. 

> Ověřeno s Ubuntu 18.10.

```bash
sudo apt install strongswan strongswan-pki libstrongswan-extra-plugins curl libxml2-utils cifs-utils

installDir="/etc/"
```

### <a name="deploy-a-virtual-network"></a>Nasazení virtuální sítě 
Chcete-li získat přístup ke sdílené složce Azure a dalším prostředkům Azure z místní sítě prostřednictvím sítě VPN s bodem na webu, musíte vytvořit virtuální síť nebo virtuální síť. Připojení Vpn P2S, které automaticky vytvoříte, je mostem mezi místním počítačem s Linuxem a touto virtuální sítí Azure.

Následující skript vytvoří virtuální síť Azure se třemi podsítěmi: jeden pro koncový bod služby vašeho účtu úložiště, jeden pro váš účet úložiště je soukromý koncový bod, který je nutný pro přístup k účtu úložiště v místním prostředí bez vytváření vlastních směrování pro veřejnou IP adresu účtu úložiště, který se může změnit, a pro bránu virtuální sítě, která poskytuje službu VPN. 

Nezapomeňte nahradit `<region>` `<resource-group>`, `<desired-vnet-name>` a příslušné hodnoty pro vaše prostředí.

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

## <a name="create-certificates-for-vpn-authentication"></a>Vytvoření certifikátů pro ověřování pomocí sítě VPN
Aby bylo možné ověřit připojení VPN z místních počítačů s Linuxem pro přístup k vaší virtuální síti, musíte vytvořit dva certifikáty: kořenový certifikát, který bude poskytnut bráně virtuálního počítače, a klientský certifikát, který bude podepsána kořenovým certifikátem. Následující skript vytvoří požadované certifikáty.

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

## <a name="deploy-virtual-network-gateway"></a>Nasazení brány virtuální sítě
Brána virtuální sítě Azure je služba, ke které se připojí vaše místní počítače s Linuxem. Nasazení této služby vyžaduje dvě základní součásti: veřejnou IP adresu, která identifikuje bránu pro vaše klienty, ať jsou kdekoli na světě, a kořenový certifikát, který jste vytvořili dříve a který bude použit k ověření vašich klientů.

Nezapomeňte nahradit `<desired-vpn-name-here>` název, který chcete pro tyto prostředky.

> [!Note]  
> Nasazení brány virtuální sítě Azure může trvat až 45 minut. Během nasazování tohoto prostředku bude tento skript bash blokovat pro dokončení nasazení. To se očekává.

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
Brána virtuální sítě Azure vytvoří balíček ke stažení s konfiguračními soubory potřebnými k inicializaci připojení VPN na místním počítači s Linuxem. Následující skript umístí certifikáty, které jste vytvořili, `ipsec.conf` na správné místo a nakonfiguruje soubor se správnými hodnotami z konfiguračního souboru v balíčku ke stažení.

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
Teď, když jste nastavili vpn bodu na webu, můžete připojit sdílenou složku Azure. Následující příklad připojí sdílenou složku non-persistently. Pokud se chcete trvale připojovat, přečtěte si část [Použití sdílené složky Azure s Linuxem](storage-how-to-use-files-linux.md). 

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

## <a name="see-also"></a>Viz také
- [Přehled sítí Azure Files](storage-files-networking-overview.md)
- [Konfigurace sítě VPN point-to-site (P2S) v systému Windows pro použití se soubory Azure](storage-files-configure-p2s-vpn-windows.md)
- [Konfigurace sítě VPN site-to-site (S2S) pro použití se soubory Azure](storage-files-configure-s2s-vpn.md)