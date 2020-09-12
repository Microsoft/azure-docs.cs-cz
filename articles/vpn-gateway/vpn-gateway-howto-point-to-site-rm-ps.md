---
title: 'Připojení k virtuální síti z počítače – P2S VPN a nativní ověřování certifikátů Azure: PowerShell'
description: Připojíte zabezpečeně klienty Windows a Mac OS X k virtuální síti Azure pomocí P2S (Point-to-Site) a certifikátů podepsaných svým držitelem (self-signed certificate) nebo vydaných certifikační autoritou. Tento článek používá PowerShell.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 2b7522e4c1074c3c52e62453e815cce859a86148
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435758"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-powershell"></a>Konfigurace připojení VPN typu Point-to-site k virtuální síti s použitím nativního ověřování certifikátů Azure: PowerShell

Tento článek vám pomůže bezpečně připojit jednotlivé klienty se systémem Windows, Linux nebo Mac OS X k virtuální síti Azure. Připojení VPN typu Point-to-Site jsou užitečná, když se chcete ke své virtuální síti připojit ze vzdáleného umístění, například při práci z domova nebo z místa konání konference. Místo sítě VPN Site-to-Site můžete také použít P2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Připojení typu Point-to-Site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol) nebo protokolu IKEv2. Další informace o síti VPN Point-to-Site najdete v článku věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).

![Připojení počítače k virtuální síti Azure – diagram připojení Point-to-Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Architektura

Nativní připojení Azure typu Point-to-Site k ověřování certifikátů používají následující položky, které nakonfigurujete v tomto cvičení:

* Bránu VPN typu RouteBased.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. Jakmile je certifikát nahraný, považuje za důvěryhodný certifikát a používá se k ověřování.
* Klientský certifikát, který se generuje z kořenového certifikátu. Klientský certifikát nainstalovaný na každém klientském počítači, který se bude připojovat k virtuální síti. Tento certifikát se používá k ověřování klienta.
* Konfigurace klienta VPN. Konfigurační soubory klienta VPN obsahují informace potřebné pro připojení klienta k virtuální síti. Soubory konfigurují stávajícího klienta VPN nativního pro příslušný operační systém. Každý klient, který se připojuje, musí být nakonfigurovaný pomocí nastavení v konfiguračních souborech.

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

>[!NOTE]
> Většina kroků v tomto článku může Azure Cloud Shell použít. Chcete-li však odeslat veřejný klíč kořenového certifikátu, je nutné buď použít PowerShell místně, nebo Azure Portal.
>

### <a name="example-values"></a><a name="example"></a>Příklady hodnot

Příklady hodnot můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku. Proměnné se nastaví v části [1](#declare) článku. Můžete buď využít kroky jako podrobný postup a převzít hodnoty beze jejich změny, nebo je změnit tak, aby odpovídaly vašemu prostředí.

* **Název: VNet1**
* **Adresní prostor: 192.168.0.0/16** a **10.254.0.0/16**<br>V tomto příkladu se používá více adresních prostorů k ilustraci, že tato konfigurace funguje s více adresními prostory. Více adresních prostorů pro ni ale není potřeba.
* **Název podsítě: front-end**
  * **Rozsah adres podsítě: 192.168.1.0/24**
* **Název podsítě: BackEnd**
  * **Rozsah adres podsítě: 10.254.1.0/24**
* **Název podsítě: GatewaySubnet**<br>Název podsítě *GatewaySubnet* je pro správnou funkci brány VPN Gateway povinný.
  * **Rozsah adres podsítě brány: 192.168.200.0/24** 
* **Fond adres klienta VPN: 172.16.201.0/24**<br>Klienti VPN, kteří se budou k síti VNet připojovat pomocí tohoto připojení Point-to-Site, dostanou IP adresu ze zadaného fondu adres klienta VPN.
* **Předplatné:** Ujistěte se, že máte správné předplatné, pokud máte více než jedno.
* **Skupina prostředků: TestRG**
* **Umístění: USA – východ**
* **Server DNS: IP adresa** serveru DNS, který chcete použít pro překlad názvů. (volitelné)
* **Název brány: Vnet1GW**
* **Název veřejné IP adresy: VNet1GWPIP**
* **VpnType: RouteBased** 

## <a name="1-sign-in-and-set-variables"></a><a name="declare"></a>1. přihlášení a nastavení proměnných

V této části se přihlašujete a deklarujete hodnoty používané pro tuto konfiguraci. Deklarované hodnoty jsou použity v ukázkových skriptech. Změňte hodnoty tak, aby odpovídaly vašemu prostředí. Můžete také použít deklarované hodnoty a projít kroky jako cvičení.

### <a name="sign-in"></a>Přihlášení

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="declare-variables"></a>Deklarace proměnných

Deklarujte proměnné, které chcete použít. Použijte následující příklad a dle potřeby nahraďte v něm uvedené hodnoty vlastními. Pokud během cvičení zavřete relaci PowerShell/Cloud Shell, stačí zkopírovat hodnoty a vložit je znovu, aby se proměnné znovu deklarovaly.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-configure-a-vnet"></a><a name="ConfigureVNet"></a>2. konfigurace virtuální sítě

1. Vytvořte skupinu prostředků.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG -Location $Location
   ```
2. Vytvořte konfigurace podsítí pro virtuální síť, podsítě pojmenujte *FrontEnd*, *BackEnd* a *GatewaySubnet*. Tyto předpony musí být součástí adresního prostoru virtuální sítě deklarovaného výše.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name $FESubName -AddressPrefix $FESubPrefix
   $besub = New-AzVirtualNetworkSubnetConfig -Name $BESubName -AddressPrefix $BESubPrefix
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name $GWSubName -AddressPrefix $GWSubPrefix
   ```
3. Vytvořte virtuální síť.

   V tomto příkladu je parametr serveru -DnsServer volitelný. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se ze své virtuální sítě připojujete. V tomto příkladu se používá privátní IP adresa, ale je pravděpodobné, že to není IP adresa vašeho serveru DNS. Je potřeba, abyste použili svoje vlastní hodnoty. Hodnotu, kterou zadáte, použijí prostředky, které nasadíte do virtuální sítě, a ne připojení P2S nebo klient VPN.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG -Location $Location -AddressPrefix $VNetPrefix1,$VNetPrefix2 -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Určete proměnné pro virtuální síť, kterou jste vytvořili.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   ```
5. Brána VPN musí mít veřejnou IP adresu. Nejprve si vyžádáte prostředek IP adresy a pak na něj budete odkazovat při vytváření brány virtuální sítě. IP adresa se dynamicky přiřadí k prostředku po vytvoření brány VPN. Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. Nemůžete si vyžádat statické přiřazení IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

   Požádejte o dynamicky přidělovanou veřejnou IP adresu.

   ```azurepowershell-interactive
   $pip = New-AzPublicIpAddress -Name $GWIPName -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-create-the-vpn-gateway"></a><a name="creategateway"></a>3. Vytvoření brány VPN

Nakonfigurujte a vytvořte bránu virtuální sítě pro svou virtuální síť.

* Parametr -GatewayType musí mít hodnotu **Vpn** a parametr -VpnType musí mít hodnotu **RouteBased**.
* -VpnClientProtocol se používá k zadání typů tunelů, které chcete povolit. Možnosti tunelu jsou **OpenVPN, SSTP** a **IKEv2**. Můžete zvolit, že se má povolit jedna z nich, nebo libovolná podporovaná kombinace. Pokud chcete povolit více typů, zadejte názvy oddělené čárkou. OpenVPN a SSTP nelze současně povolit. Klient strongSwan v Androidu a Linuxu a nativní klient IKEv2 VPN v iOS a OS X budou pro připojení používat jenom tunel IKEv2. Klienti Windows nejdřív vyzkoušejí IKEv2 a pokus se nepřipojí, přejdou zpátky k SSTP. Klienta OpenVPN můžete použít pro připojení k typu tunelu OpenVPN.
* SKU "Basic" pro bránu virtuální sítě nepodporuje ověřování IKEv2, OpenVPN ani RADIUS. Pokud plánujete, že se klienti se systémem Mac připojí k vaší virtuální síti, nepoužívejte základní SKU.
* Dokončení brány VPN může trvat až 45 minut v závislosti na vybrané [skladové jednotce brány](vpn-gateway-about-vpn-gateway-settings.md). Tento příklad používá IKEv2.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1 -VpnClientProtocol "IKEv2"
```

## <a name="4-add-the-vpn-client-address-pool"></a><a name="addresspool"></a>4. přidejte fond adres klienta VPN.

Po dokončení vytváření brány VPN můžete přidat fond adres klienta VPN. Fond adres klienta VPN je rozsah, ze kterého dostanou klienti VPN IP adresu při svém připojení. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, ani s virtuální sítí, ke které se chcete připojit. V tomto příkladu je fond adres klienta VPN deklarován jako [proměnná](#declare) v kroku 1.

```azurepowershell-interactive
$Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientAddressPool $VPNClientAddressPool
```

## <a name="5-generate-certificates"></a><a name="Certificates"></a>5. generování certifikátů

Azure používá certifikáty k ověřování klientů VPN pro sítě VPN Point-to-Site. Nahrajete do Azure informace o veřejném klíči kořenového certifikátu. Veřejný klíč se pak bude považovat za důvěryhodný. Klientské certifikáty musí být vygenerované z důvěryhodného kořenového certifikátu a pak nainstalované na každém klientském počítači v úložišti certifikátů v adresáři Certificates-Current User/Personal. Tento certifikát se používá k ověřování klienta při zahájení připojení k virtuální síti. 

Pokud používáte certifikáty podepsané svým držitelem, musí se vytvořit pomocí konkrétních parametrů. Certifikát podepsaný svým držitelem můžete vytvořit podle pokynů pro [PowerShell a Windows 10](vpn-gateway-certificates-point-to-site.md). Pokud nemáte Windows 10, můžete použít [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Je důležité, abyste při generování kořenových certifikátů podepsaných svým držitelem a klientských certifikátů postupovali podle pokynů. Jinak certifikáty, které vytvoříte, nebudou kompatibilní s připojeními typu P2S a zobrazí se chyba připojení.

### <a name="1-obtain-the-cer-file-for-the-root-certificate"></a><a name="cer"></a>1. získání souboru. cer pro kořenový certifikát

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]


### <a name="2-generate-a-client-certificate"></a><a name="generate"></a>2. vygenerujte klientský certifikát.

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="6-upload-the-root-certificate-public-key-information"></a><a name="upload"></a>6. Nahrajte informace o veřejném klíči kořenového certifikátu.

Ověřte, že se dokončilo vytváření brány VPN. Po dokončení můžete nahrát soubor .cer (obsahující informace o veřejném klíči) důvěryhodného kořenového certifikátu do Azure. Jakmile je soubor .cer nahraný, Azure ho může použít k ověřování klientů s nainstalovaným klientským certifikátem vygenerovaným z důvěryhodného kořenového certifikátu. Později můžete podle potřeby nahrát další soubory s důvěryhodnými kořenovými certifikáty – celkem až 20.

>[!NOTE]
> Soubor. cer nelze nahrát pomocí Azure Cloud Shell. Můžete buď použít PowerShell místně na vašem počítači, nebo můžete použít [Azure Portal kroky](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
>

1. Deklarujte proměnnou pro název certifikátu a nahraďte hodnotu vlastní hodnotou.

   ```azurepowershell
   $P2SRootCertName = "P2SRootCert.cer"
   ```
2. Cestu k souboru nahraďte vlastní cestou a potom spusťte rutiny.

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64 = [system.convert]::ToBase64String($cert.RawData)
   $p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64
   ```
3. Nahrajte informace o veřejném klíči do Azure. Po nahrání informací o certifikátu považuje Azure za důvěryhodného kořenového certifikátu. Při nahrávání se ujistěte, že na počítači běží místně PowerShell, nebo místo toho můžete použít [Azure Portal kroky](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile). Pomocí Azure Cloud Shell nemůžete nahrávat.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64
   ```

## <a name="7-install-an-exported-client-certificate"></a><a name="clientcertificate"></a>7. Nainstalujte exportovaný certifikát klienta.

Pokud chcete vytvořit připojení P2S z jiného klientského počítače, než který jste použili k vytvoření klientských certifikátů, budete muset klientský certifikát nainstalovat. Při instalaci klientského certifikátu budete potřebovat heslo, které bylo vytvořeno při jeho exportu.

Zkontrolujte, že se klientský certifikát vyexportoval jako soubor .pfx spolu s celým řetězem certifikátů (to je výchozí nastavení). Jinak informace o kořenovém certifikátu na klientském počítači nejsou a klient se nebude moct správně ověřit. 

Postup instalace najdete v tématu věnovaném [instalaci klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="8-configure-the-native-vpn-client"></a><a name="clientconfig"></a>8. konfigurace nativního klienta VPN

Konfigurační soubory klienta VPN obsahují nastavení pro konfiguraci zařízení, která umožňuje připojit se k virtuální síti přes připojení P2S. Pokyny pro generování a instalaci konfiguračních souborů klienta VPN najdete v tématu věnovaném [vytvoření a instalaci konfiguračních souborů klienta VPN pro konfigurace PS2 s nativním ověřováním certifikátů Azure](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="9-connect-to-azure"></a><a name="connect"></a>9. připojení k Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Připojení z klienta VPN systému Windows

>[!NOTE]
>Na klientském počítači s Windows, že kterého se připojujete, musíte mít oprávnění správce.
>
>

1. Chcete-li se připojit ke své síti VNet, přejděte na klientském počítači na připojení VPN a vyhledejte připojení VPN, které jste vytvořili. Bude mít stejný název jako vaše virtuální síť. Klikněte na **Připojit**. Může se zobrazit místní zpráva týkající se použití certifikátu. Klikněte na **Pokračovat** pro použití zvýšených oprávnění. 
2. Připojení spustíte kliknutím na tlačítko **Připojit** na stavové stránce **Připojení**. Pokud uvidíte obrazovku **Výběr certifikátu**, ujistěte se, že zobrazený klientský certifikát je ten, který chcete pro připojení použít. Pokud není, vyberte pomocí šipky rozevíracího seznamu správný certifikát, a poté klikněte na **OK**.

   ![Připojení klienta VPN k Azure](./media/vpn-gateway-howto-point-to-site-rm-ps/clientconnect.png)
3. Vaše připojení bylo vytvořeno.

   ![Vytvořené připojení](./media/vpn-gateway-howto-point-to-site-rm-ps/connected.png)

#### <a name="troubleshooting-windows-client-p2s-connections"></a>Řešení potíží s připojeními P2S u klientů se systémem Windows

[!INCLUDE [client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Připojení z klienta VPN systému Mac

V dialogovém okně Síť vyhledejte klientský profil, který chcete použít, a potom klikněte na **Připojit**.
Podrobné pokyny najdete v tématu [install-Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) . Pokud máte potíže s připojením, ověřte, že brána virtuální sítě nepoužívá základní SKU. Základní SKU není pro klienty Mac podporováno.

  ![Připojení v systému Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Ověření stavu připojení

Tyto pokyny platí pro klienty se systémem Windows.

1. Chcete-li ověřit, zda je připojení VPN aktivní, v příkazovém řádku se zvýšenými oprávněními spusťte příkaz *ipconfig/all*.
2. Zkontrolujte výsledky. Všimněte si, že IP adresa, kterou jste obdrželi, je jedna z adres z fondu adres klienta VPN připojení Point-to-Site, který jste určili během konfigurace. Výsledky jsou podobné tomuto příkladu:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Připojení k virtuálnímu počítači

Tyto pokyny platí pro klienty se systémem Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="to-add-or-remove-a-root-certificate"></a><a name="addremovecert"></a>Přidání nebo odebrání kořenového certifikátu

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Když odeberete kořenový certifikát, klienti s certifikátem vygenerovaným z kořenového certifikátu se nebudou moc ověřit ani připojit. Pokud chcete, aby se klient mohl i nadále ověřovat a připojovat, je nutné nainstalovat nový klientský certifikát vygenerovaný z kořenového certifikátu, který Azure považuje za důvěryhodný (je do Azure nahraný).

### <a name="to-add-a-trusted-root-certificate"></a><a name="addtrustedroot"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů .cer s kořenovými certifikáty. Kořenový certifikát můžete přidat provedením následujících kroků:

#### <a name="method-1"></a><a name="certmethod1"></a>Metoda 1


Tato metoda představuje nejúčinnější způsob, jak nahrát kořenový certifikát. Vyžaduje, aby byly rutiny Azure PowerShell nainstalované místně na vašem počítači (ne Azure Cloud Shell).

1. Připravte soubor .cer k nahrání:

   ```azurepowershell
   $filePathForCert = "C:\cert\P2SRootCert3.cer"
   $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($filePathForCert)
   $CertBase64_3 = [system.convert]::ToBase64String($cert.RawData)
   $p2srootcert = New-AzVpnClientRootCertificate -Name $P2SRootCertName -PublicCertData $CertBase64_3
   ```
2. Nahrajte soubor. Najednou můžete nahrát jenom jeden soubor.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $CertBase64_3
   ```

3. Ověřte nahrání souboru certifikátu:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

#### <a name="method-2---azure-portal"></a><a name="certmethod2"></a>Metoda 2 – Azure Portal

Tato metoda zahrnuje více kroků než metoda 1, ale má stejný výsledek. Přikládáme ji pro případ, že potřebujete zobrazit data certifikátu. Vyžaduje, aby byly rutiny Azure PowerShell nainstalované místně na vašem počítači (ne Azure Cloud Shell).

1. Vytvořte a připravte nový kořenový certifikát, který chcete přidat do Azure. Exportujte veřejný klíč ve formátu X.509, kódování Base-64 (CER) a otevřete jej v textovém editoru. Zkopírujte hodnoty, jak je znázorněno v následujícím příkladu:

   ![certifikát](./media/vpn-gateway-howto-point-to-site-rm-ps/copycert.png)

   > [!NOTE]
   > Při kopírování dat certifikátu se ujistěte, že kopírujete text jako jeden souvislý řádek bez konců řádků nebo odřádkování. Pokud chcete zobrazit konce řádků a odřádkování, může být nutné upravit zobrazení v textovém editoru na Zobrazit symbol / Zobrazit všechny znaky.
   >
   >

2. Zadejte název certifikátu a informace o klíči jako proměnnou. Nahraďte tyto informace svými vlastními, jak je ukázáno na následujícím příkladu:

   ```azurepowershell
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```
3. Přidejte nový kořenový certifikát. Nelze přidat více certifikátů současně.

   ```azurepowershell
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayname "VNet1GW" -ResourceGroupName "TestRG" -PublicCertData $MyP2SCertPubKeyBase64_2
   ```
4. Pomocí následujícího příkladu můžete ověřit, zda byl nový certifikát přidán správně:

   ```azurepowershell
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

### <a name="to-remove-a-root-certificate"></a><a name="removerootcert"></a>Odebrání kořenového certifikátu

1. Deklarujte proměnné.

   ```azurepowershell-interactive
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   $P2SRootCertName2 = "ARMP2SRootCert2.cer"
   $MyP2SCertPubKeyBase64_2 = "MIIC/zCCAeugAwIBAgIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAMBgxFjAUBgNVBAMTDU15UDJTUm9vdENlcnQwHhcNMTUxMjE5MDI1MTIxWhcNMzkxMjMxMjM1OTU5WjAYMRYwFAYDVQQDEw1NeVAyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAyjIXoWy8xE/GF1OSIvUaA0bxBjZ1PJfcXkMWsHPzvhWc2esOKrVQtgFgDz4ggAnOUFEkFaszjiHdnXv3mjzE2SpmAVIZPf2/yPWqkoHwkmrp6BpOvNVOpKxaGPOuK8+dql1xcL0eCkt69g4lxy0FGRFkBcSIgVTViS9wjuuS7LPo5+OXgyFkAY3pSDiMzQCkRGNFgw5WGMHRDAiruDQF1ciLNojAQCsDdLnI3pDYsvRW73HZEhmOqRRnJQe6VekvBYKLvnKaxUTKhFIYwuymHBB96nMFdRUKCZIiWRIy8Hc8+sQEsAML2EItAjQv4+fqgYiFdSWqnQCPf/7IZbotgQIDAQABo00wSzBJBgNVHQEEQjBAgBAkuVrWvFsCJAdK5pb/eoCNoRowGDEWMBQGA1UEAxMNTXlQMlNSb290Q2VydIIQKazxzFjMkp9JRiX+tkTfSzAJBgUrDgMCHQUAA4IBAQA223veAZEIar9N12ubNH2+HwZASNzDVNqspkPKD97TXfKHlPlIcS43TaYkTz38eVrwI6E0yDk4jAuPaKnPuPYFRj9w540SvY6PdOUwDoEqpIcAVp+b4VYwxPL6oyEQ8wnOYuoAK1hhh20lCbo8h9mMy9ofU+RP6HJ7lTqupLfXdID/XevI8tW6Dm+C/wCeV3EmIlO9KUoblD/e24zlo3YzOtbyXwTIh34T0fO/zQvUuBqZMcIPfM1cDvqcqiEFLWvWKoAnxbzckye2uk1gHO52d8AVL3mGiX8wBJkjc/pMdxrEvvCzJkltBmqxTM6XjDJALuVh16qFlqgTWCIcb7ju"
   ```
2. Odeberte certifikát.

   ```azurepowershell-interactive
   Remove-AzVpnClientRootCertificate -VpnClientRootCertificateName $P2SRootCertName2 -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -PublicCertData $MyP2SCertPubKeyBase64_2
   ```
3. Pomocí následujícího příkladu můžete ověřit, zda byl certifikát úspěšně odebrán.

   ```azurepowershell-interactive
   Get-AzVpnClientRootCertificate -ResourceGroupName "TestRG" `
   -VirtualNetworkGatewayName "VNet1GW"
   ```

## <a name="to-revoke-a-client-certificate"></a><a name="revoke"></a>Odvolání klientského certifikátu

Certifikáty klientů lze odvolat. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-Site na základě jednotlivých klientských certifikátů. To se liší od odebrání důvěryhodného kořenového certifikátu. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

### <a name="revoke-a-client-certificate"></a><a name="revokeclientcert"></a>Odvolání klientského certifikátu

1. Načtěte kryptografický otisk klientského certifikátu. Další informace najdete v tématu [Postup načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).
2. Zkopírujte údaje do textového editoru a smažte všechny mezery, aby vznikl souvislý řetězec. Řetězec je v dalším kroku deklarován jako proměnná.
3. Deklarujte proměnné. Ujistěte se, že deklarujete kryptografický otisk, který jste získali v předchozím kroku.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```
4. Přidejte kryptografický otisk do seznamu odvolaných certifikátů. Po přidání kryptografického otisku se zobrazí zpráva „Úspěch“.

   ```azurepowershell-interactive
   Add-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG `
   -Thumbprint $RevokedThumbprint1
   ```
5. Zkontrolujte, zda byl daný kryptografický otisk přidán do seznamu odvolaných certifikátů.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```
6. Po přidání kryptografického otisku už nebude možné certifikát použít k připojení. Klientům, kteří se pokusí připojit pomocí tohoto certifikátu, se zobrazí zpráva s informací o neplatnosti certifikátu.

### <a name="to-reinstate-a-client-certificate"></a><a name="reinstateclientcert"></a>Obnovení klientského certifikátu

Klientský certifikát lze obnovit odebráním jeho kryptografického otisku ze seznamu odvolaných klientských certifikátů.

1. Deklarujte proměnné. Ujistěte se, že deklarujete správný kryptografický otisk pro certifikát, který chcete obnovit.

   ```azurepowershell-interactive
   $RevokedClientCert1 = "NameofCertificate"
   $RevokedThumbprint1 = "‎51ab1edd8da4cfed77e20061c5eb6d2ef2f778c7"
   $GWName = "Name_of_virtual_network_gateway"
   $RG = "Name_of_resource_group"
   ```
2. Odeberte kryptografický otisk certifikátu ze seznamu odvolaných certifikátů.

   ```azurepowershell-interactive
   Remove-AzVpnClientRevokedCertificate -VpnClientRevokedCertificateName $RevokedClientCert1 `
   -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG -Thumbprint $RevokedThumbprint1
   ```
3. Zkontrolujte, za byl kryptografický otisk ze seznamu odebrán.

   ```azurepowershell-interactive
   Get-AzVpnClientRevokedCertificate -VirtualNetworkGatewayName $GWName -ResourceGroupName $RG
   ```

## <a name="point-to-site-faq"></a><a name="faq"></a>Nejčastější dotazy týkající se připojení Point-to-Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Další kroky
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/azure-vm-network-overview.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích: Problémy s připojením Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
