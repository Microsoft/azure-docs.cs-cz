---
title: 'Připojení k virtuální síti z počítače – P2S VPN a nativní ověřování certifikátů Azure: PowerShell'
description: Připojíte zabezpečeně klienty Windows a Mac OS X k virtuální síti Azure pomocí P2S (Point-to-Site) a certifikátů podepsaných svým držitelem (self-signed certificate) nebo vydaných certifikační autoritou. Tento článek používá PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 5d2902222dea3e84ebed04d80d7349167f83cae1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076013"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Konfigurace připojení VPN typu Point-to-site k virtuální síti s použitím nativního ověřování certifikátů Azure: PowerShell

Tento článek vám pomůže bezpečně připojit jednotlivé klienty se systémem Windows, Linux nebo Mac OS X k virtuální síti Azure. Připojení VPN typu Point-to-site jsou užitečná, když se chcete připojit k virtuální síti ze vzdáleného umístění, například při práci z domova nebo z konference. Místo sítě VPN Site-to-Site můžete také použít P2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Připojení typu Point-to-site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol) nebo protokolu IKEv2.

:::image type="content" source="./media/vpn-gateway-how-to-point-to-site-rm-ps/point-to-site-diagram.png" alt-text="Připojení počítače k virtuální síti Azure – diagram připojení typu Point-to-site":::

Další informace o síti VPN typu Point-to-site najdete v tématu věnovaném [síti VPN typu Point-to-site](point-to-site-about.md). Pokud chcete tuto konfiguraci vytvořit pomocí Azure Portal, přečtěte si téma [Konfigurace sítě VPN typu Point-to-site pomocí Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

## <a name="architecture"></a>Architektura

Nativní připojení k ověřování pomocí certifikátů Azure Point-to-site používají následující položky, které v tomto cvičení nakonfigurujete:

* Bránu VPN typu RouteBased.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. Jakmile je certifikát nahraný, považuje za důvěryhodný certifikát a používá se k ověřování.
* Klientský certifikát, který se generuje z kořenového certifikátu. Klientský certifikát nainstalovaný na každém klientském počítači, který se bude připojovat k virtuální síti. Tento certifikát se používá k ověřování klienta.
* Konfigurace klienta VPN. Konfigurační soubory klienta VPN obsahují informace potřebné pro připojení klienta k virtuální síti. Soubory konfigurují stávajícího klienta VPN nativního pro příslušný operační systém. Každý klient, který se připojuje, musí být nakonfigurovaný pomocí nastavení v konfiguračních souborech.

## <a name="prerequisites"></a>Předpoklady

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

>[!IMPORTANT]
> Mnohé z kroků v tomto článku můžou použít Azure Cloud Shell. Pro generování certifikátů ale nemůžete použít Cloud Shell. Kromě toho, pokud chcete odeslat veřejný klíč kořenového certifikátu, musíte použít Azure PowerShell lokálně nebo Azure Portal.
>

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-sign-in"></a><a name="signin"></a>1. přihlášení

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="2-declare-variables"></a><a name="declare"></a>2. deklarace proměnných

Pro tento článek používáme proměnné, abyste mohli snadno změnit hodnoty tak, aby se daly použít na vlastní prostředí, aniž byste museli měnit příklady samotného. Deklarujte proměnné, které chcete použít. V případě potřeby můžete použít následující ukázku a nahradit hodnoty vlastními hodnotami. Pokud během cvičení zavřete relaci PowerShell/Cloud Shell, stačí zkopírovat hodnoty a vložit je znovu, aby se proměnné znovu deklarovaly.

```azurepowershell-interactive
$VNetName  = "VNet1"
$FESubName = "FrontEnd"
$GWSubName = "GatewaySubnet"
$VNetPrefix = "10.1.0.0/16"
$FESubPrefix = "10.1.0.0/24"
$GWSubPrefix = "10.1.255.0/27"
$VPNClientAddressPool = "172.16.201.0/24"
$RG = "TestRG1"
$Location = "EastUS"
$GWName = "VNet1GW"
$GWIPName = "VNet1GWpip"
$GWIPconfName = "gwipconf"
$DNS = "10.2.1.4"
```

## <a name="3-configure-a-vnet"></a><a name="ConfigureVNet"></a>3. konfigurace virtuální sítě

1. Vytvořte skupinu prostředků.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```

1. Vytvořte konfigurace podsítě pro virtuální síť a pojmenujte je *front-end* a *GatewaySubnet* . Tyto předpony musí být součástí adresního prostoru virtuální sítě deklarovaného výše.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```

1. Vytvořte virtuální síť.

   V tomto příkladu je parametr serveru -DnsServer volitelný. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se ze své virtuální sítě připojujete. V tomto příkladu se používá privátní IP adresa, ale je pravděpodobné, že to není IP adresa vašeho serveru DNS. Je potřeba, abyste použili svoje vlastní hodnoty. Hodnotu, kterou zadáte, použijí prostředky, které nasadíte do virtuální sítě, a ne připojení P2S nebo klient VPN.

   ```azurepowershell-interactive
       New-AzVirtualNetwork `
      -ResourceGroupName $RG `
      -Location $Location `
      -Name $VNetName `
      -AddressPrefix $VNetPrefix `
      -Subnet $fesub, $gwsub `
      -DnsServer $DNS
   ```

1. Určete proměnné pro virtuální síť, kterou jste vytvořili.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```

1. Brána VPN musí mít veřejnou IP adresu. Nejprve si vyžádáte prostředek IP adresy a pak na něj budete odkazovat při vytváření brány virtuální sítě. IP adresa se dynamicky přiřadí k prostředku po vytvoření brány VPN. Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. Nemůžete si vyžádat statické přiřazení IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

   Požádejte o dynamicky přidělovanou veřejnou IP adresu.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="4-create-the-vpn-gateway"></a><a name="creategateway"></a>4. Vytvoření brány VPN

V tomto kroku nakonfigurujete a vytvoříte bránu virtuální sítě pro vaši virtuální síť.

* Parametr -GatewayType musí mít hodnotu **Vpn** a parametr -VpnType musí mít hodnotu **RouteBased** .
* -VpnClientProtocol se používá k zadání typů tunelů, které chcete povolit. Možnosti tunelu jsou **OpenVPN, SSTP** a **IKEv2** . Můžete zvolit, že se má povolit jedna z nich, nebo libovolná podporovaná kombinace. Pokud chcete povolit více typů, zadejte názvy oddělené čárkou. OpenVPN a SSTP nelze současně povolit. Klient strongSwan v Androidu a Linuxu a nativní klient IKEv2 VPN v iOS a OS X budou pro připojení používat jenom tunel IKEv2. Klienti Windows nejdřív vyzkoušejí IKEv2 a pokus se nepřipojí, přejdou zpátky k SSTP. Klienta OpenVPN můžete použít pro připojení k typu tunelu OpenVPN.
* SKU "Basic" pro bránu virtuální sítě nepodporuje ověřování IKEv2, OpenVPN ani RADIUS. Pokud plánujete, že se klienti se systémem Mac připojí k vaší virtuální síti, nepoužívejte základní SKU.
* Dokončení brány VPN může trvat až 45 minut v závislosti na vybrané [skladové jednotce brány](vpn-gateway-about-vpn-gateway-settings.md). Tento příklad používá IKEv2.

1. Nakonfigurujte a vytvořte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány bude trvat přibližně 45 minut.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
   -Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
   -VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
   ```

1. Po vytvoření brány ji můžete zobrazit pomocí následujícího příkladu. Pokud jste zavřeli PowerShell nebo vypršel časový limit při vytváření vaší brány, můžete [proměnné deklarovat](#declare) znovu.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroup $RG
   ```

## <a name="5-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>5. přidejte fond adres klienta VPN.

Po dokončení vytváření brány VPN můžete přidat fond adres klienta VPN. Fond adres klienta VPN je rozsah, ze kterého dostanou klienti VPN IP adresu při svém připojení. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, ani s virtuální sítí, ke které se chcete připojit.

V tomto příkladu je fond adres klienta VPN deklarovaný jako [Proměnná](#declare) v předchozím kroku.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="6-generate-certificates"></a><a name="Certificates"></a>6. generování certifikátů

>[!IMPORTANT]
> Nemůžete generovat certifikáty pomocí Azure Cloud Shell. Je nutné použít jednu z metod popsaných v této části. Pokud chcete používat PowerShell, musíte ho nainstalovat lokálně.
>

Služba Azure používá certifikáty k ověřování klientů VPN pro sítě VPN typu Point-to-site. Nahrajete do Azure informace o veřejném klíči kořenového certifikátu. Veřejný klíč se pak bude považovat za důvěryhodný. Klientské certifikáty musí být vygenerované z důvěryhodného kořenového certifikátu a pak nainstalované na každém klientském počítači v úložišti certifikátů v adresáři Certificates-Current User/Personal. Tento certifikát se používá k ověřování klienta při zahájení připojení k virtuální síti. 

Pokud používáte certifikáty podepsané svým držitelem, musí se vytvořit pomocí konkrétních parametrů. Certifikát podepsaný svým držitelem můžete vytvořit podle pokynů pro [PowerShell a Windows 10](vpn-gateway-certificates-point-to-site.md). Pokud nemáte Windows 10, můžete použít [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Je důležité, abyste při generování kořenových certifikátů podepsaných svým držitelem a klientských certifikátů postupovali podle pokynů. Jinak certifikáty, které vytvoříte, nebudou kompatibilní s připojeními typu P2S a zobrazí se chyba připojení.

### <a name="root-certificate"></a><a name="cer"></a>Kořenový certifikát

1. [!INCLUDE [Root certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

1. Po vytvoření kořenového certifikátu [exportujte](vpn-gateway-certificates-point-to-site.md#cer) data veřejného certifikátu (ne privátní klíč) jako soubor X. 509. cer kódovaný v kódování Base64.

### <a name="client-certificate"></a><a name="generate"></a>Certifikát klienta

1. [!INCLUDE [Generate a client certificate](../../includes/vpn-gateway-p2s-clientcert-include.md)]

1. Po vytvoření klientského certifikátu ho [exportujte](vpn-gateway-certificates-point-to-site.md#clientexport) . Klientský certifikát bude distribuován do klientských počítačů, které se budou připojovat.

## <a name="7-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>7. Nahrajte informace o veřejném klíči kořenového certifikátu.

Ověřte, že se dokončilo vytváření brány VPN. Po dokončení můžete nahrát soubor .cer (obsahující informace o veřejném klíči) důvěryhodného kořenového certifikátu do Azure. Jakmile je soubor .cer nahraný, Azure ho může použít k ověřování klientů s nainstalovaným klientským certifikátem vygenerovaným z důvěryhodného kořenového certifikátu. Později můžete podle potřeby nahrát další soubory s důvěryhodnými kořenovými certifikáty – celkem až 20.

>[!NOTE]
> Soubor. cer nelze nahrát pomocí Azure Cloud Shell. Můžete buď použít PowerShell místně na vašem počítači, nebo můžete použít [Azure Portal kroky](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
>

1. Deklarujte proměnnou pro název certifikátu a nahraďte hodnotu vlastní hodnotou.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```

1. Cestu k souboru nahraďte vlastní cestou a potom spusťte rutiny.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Nahrajte informace o veřejném klíči do Azure. Po nahrání informací o certifikátu považuje Azure za důvěryhodného kořenového certifikátu. Při nahrávání se ujistěte, že na počítači běží místně PowerShell, nebo místo toho můžete použít [Azure Portal kroky](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Pomocí Azure Cloud Shell nemůžete nahrávat.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64
   ```

## <a name="8-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>8. instalace exportovaného klientského certifikátu

Následující kroky vám pomůžou nainstalovat na klienta Windows. Další klienty a další informace najdete v tématu [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

Zkontrolujte, že se klientský certifikát vyexportoval jako soubor .pfx spolu s celým řetězem certifikátů (to je výchozí nastavení). Jinak informace o kořenovém certifikátu na klientském počítači nejsou a klient se nebude moct správně ověřit.

## <a name="9-configure-the-vpn-client"></a><a name="clientconfig"></a>9. konfigurace klienta VPN

V této části nakonfigurujete nativního klienta pro váš počítač pro připojení k bráně virtuální sítě. Pokud například přejdete na nastavení sítě VPN v počítači s Windows, můžete přidat připojení VPN. Připojení typu Point-to-site vyžaduje konkrétní nastavení konfigurace. Tyto kroky vám pomůžou vytvořit balíček s konkrétním nastavením, který váš nativní klient VPN potřebuje k připojení k virtuální síti přes připojení typu Point-to-site.

K vygenerování a instalaci balíčku pro konfiguraci klienta můžete použít následující rychlé příklady. Další informace o obsahu balíčku a dalších pokynech k vygenerování a instalaci konfiguračních souborů klienta VPN najdete v tématu [Vytvoření a instalace konfiguračních souborů](point-to-site-vpn-client-configuration-azure-cert.md)klienta VPN.

Pokud budete chtít proměnné znovu deklarovat, najdete je [tady](#declare).

### <a name="to-generate-configuration-files"></a>Generování konfiguračních souborů

```azurepowershell-interactive
$profile=New-AzVpnClientConfiguration -ResourceGroupName $RG -Name $GWName -AuthenticationMethod "EapTls"

$profile.VPNProfileSASUrl
```

### <a name="to-install-the-client-configuration-package"></a>Instalace balíčku pro konfiguraci klienta

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. připojení k Azure

### <a name="windows-vpn-client"></a>Klient VPN systému Windows

[!INCLUDE [Connect from Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [Client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="mac-vpn-client"></a>Klient VPN pro Mac

V dialogovém okně Síť vyhledejte klientský profil, který chcete použít, a potom klikněte na **Připojit** .
Podrobné pokyny najdete v tématu [install-Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) . Pokud máte potíže s připojením, ověřte, že brána virtuální sítě nepoužívá základní SKU. Základní SKU není pro klienty Mac podporováno.

  ![Připojení v systému Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-a-connection"></a><a name="verify"></a>Ověření připojení

Tyto pokyny platí pro klienty se systémem Windows.

1. Chcete-li ověřit, zda je připojení VPN aktivní, v příkazovém řádku se zvýšenými oprávněními spusťte příkaz *ipconfig/all* .
2. Zkontrolujte výsledky. Všimněte si, že IP adresa, kterou jste obdrželi, je jednou z adres v rámci fondu adres klienta VPN typu Point-to-site, který jste zadali v konfiguraci. Výsledky jsou podobné tomuto příkladu:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.13(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Připojení k virtuálnímu počítači

Tyto pokyny platí pro klienty se systémem Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Ověřte, že se po zadání IP adres serveru DNS pro virtuální síť vygeneroval balíček pro konfiguraci klienta VPN. Pokud jste aktualizovali IP adresy serveru DNS, vygenerujte a nainstalujte nový balíček pro konfiguraci klienta VPN.

* Pomocí příkazu ipconfig zkontrolujte IPv4 adresu přiřazenou adaptéru Ethernet na počítači, ze kterého se připojujete. Pokud je IP adresa v rámci rozsahu adres virtuální sítě, ke které se připojujete, nebo v rámci rozsahu adres VPNClientAddressPool, tato situace se označuje jako překrývající se adresní prostor. Když se adresní prostor tímto způsobem překrývá, síťový provoz nemá přístup do Azure a zůstane v místní síti.

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Přidání nebo odebrání kořenového certifikátu

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Když odeberete kořenový certifikát, klienti s certifikátem vygenerovaným z kořenového certifikátu se nebudou moc ověřit ani připojit. Pokud chcete, aby se klient mohl i nadále ověřovat a připojovat, je nutné nainstalovat nový klientský certifikát vygenerovaný z kořenového certifikátu, který Azure považuje za důvěryhodný (je do Azure nahraný). Tyto kroky vyžadují, aby byly rutiny Azure PowerShell nainstalované místně na vašem počítači (ne Azure Cloud Shell). K přidání kořenových certifikátů můžete použít taky Azure Portal.

**Přidat:**

Do Azure můžete přidat až 20 souborů .cer s kořenovými certifikáty. Následující kroky vám pomůžou přidat kořenový certifikát. 

1. Připravte soubor .cer k nahrání:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   ```

1. Nahrajte soubor. Najednou můžete nahrát jenom jeden soubor.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG1" -PublicCertData $CertBase64_3
   ```

1. Ověřte nahrání souboru certifikátu:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

**Pro odebrání:**

1. Deklarujte proměnné. Upravte proměnné v příkladu tak, aby odpovídaly certifikátu, který chcete odebrat.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```

1. Odeberte certifikát.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```

1. Pomocí následujícího příkladu můžete ověřit, zda byl certifikát úspěšně odebrán.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG1" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-or-reinstate-a-client-certificate"></a><a name="revoke"></a>Odvolat nebo obnovit klientský certifikát

Certifikáty klientů lze odvolat. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-site na základě individuálních klientských certifikátů. To se liší od odebrání důvěryhodného kořenového certifikátu. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

**Odvolat:**

1. Načtěte kryptografický otisk klientského certifikátu. Další informace najdete v tématu [Postup načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).

1. Zkopírujte údaje do textového editoru a smažte všechny mezery, aby vznikl souvislý řetězec. Řetězec je v dalším kroku deklarován jako proměnná.

1. Deklarujte proměnné. Ujistěte se, že deklarujete kryptografický otisk, který jste získali v předchozím kroku.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Přidejte kryptografický otisk do seznamu odvolaných certifikátů. Po přidání kryptografického otisku se zobrazí zpráva „Úspěch“.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```

1. Zkontrolujte, zda byl daný kryptografický otisk přidán do seznamu odvolaných certifikátů.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

1. Po přidání kryptografického otisku už nebude možné certifikát použít k připojení. Klientům, kteří se pokusí připojit pomocí tohoto certifikátu, se zobrazí zpráva s informací o neplatnosti certifikátu.

**Obnovit:**

Klientský certifikát lze obnovit odebráním jeho kryptografického otisku ze seznamu odvolaných klientských certifikátů.

1. Deklarujte proměnné. Ujistěte se, že deklarujete správný kryptografický otisk pro certifikát, který chcete obnovit.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```

1. Odeberte kryptografický otisk certifikátu ze seznamu odvolaných certifikátů.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```

1. Zkontrolujte, za byl kryptografický otisk ze seznamu odebrán.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Nejčastější dotazy týkající se připojení Point-to-Site

Další informace o bodech k pracovišti najdete v tématu [Nejčastější dotazy k VPN Gateway Point-to-site](vpn-gateway-vpn-faq.md#P2S) .

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/azure-vm-network-overview.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích: Problémy s připojením Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
