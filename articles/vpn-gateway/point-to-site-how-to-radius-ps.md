---
title: 'Připojení počítače k virtuální síti pomocí ověřování bodu na místo a RADIUS: PowerShell | Azure'
description: Bezpečně připojte klienty Windows a Mac OS X k virtuální síti pomocí ověřování P2S a RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: 25bc25d9ec12804cc20baa558dce67fb3f8269a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77149138"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurace připojení bodu k síti k virtuální síti pomocí ověřování RADIUS: PowerShell

Tento článek ukazuje, jak vytvořit virtuální síť s připojením point-to-site, který používá ověřování RADIUS. Tato konfigurace je k dispozici pouze pro model nasazení Správce prostředků.

Brána VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení VPN z bodu na web jsou užitečná, když se chcete připojit k virtuální síti ze vzdáleného umístění, například když jste z domova nebo z konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN Site-to-Site, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti.

Připojení VPN typu P2S se zahájí ze zařízení se systémem Windows nebo Mac. Klienti při připojování mohou využít následující metody ověřování: 

* Server RADIUS
* Ověřování nativního certifikátu brány VPN

Tento článek vám pomůže nakonfigurovat konfiguraci P2S s ověřováním pomocí serveru RADIUS. Pokud se chcete ověřit pomocí generovaných certifikátů a nativního ověřování nativního certifikátu brány VPN, přečtěte si informace [o konfiguraci připojení typu Point-to-Site k virtuální síti pomocí nativního ověřování nativního certifikátu brány VPN](vpn-gateway-howto-point-to-site-rm-ps.md).

![Diagram připojení – RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Připojení typu Point-to-Site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN přes SSTP (Secure Socket Tunneling Protocol), OpenVPN nebo IKEv2.

* SSTP je tunel VPN založený na SSL, který se podporuje jenom na klientských platformách Windows. Může proniknout branami firewall a díky tomu je ideální možností pro připojení k Azure odkudkoli. Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2.

* OpenVPN® protokol, protokol VPN založený na SSL / TLS. Řešení SSL VPN může proniknout firewally, protože většina firewallů otevírá tcp port 443 odchozí, který Používá SSL. OpenVPN lze použít pro připojení z Android, iOS (verze 11.0 a vyšší), Windows, Linux a Mac zařízení (OSX verze 10.13 a vyšší).

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).

Připojení typu Point-to-Site vyžadují:

* Bránu VPN typu RouteBased. 
* Server RADIUS pro zpracování ověřování uživatelů. Server RADIUS se dá nasadit místně nebo ve virtuální síti Azure.
* Konfigurační balíček klienta VPN pro zařízení s Windows, která se připojí k virtuální síti. Konfigurační balíček klienta VPN poskytuje nastavení potřebná pro připojení klienta VPN přes P2S.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Ověřování domény služby Active Directory (AD) pro sítě Virtuální sítě P2S

Ověřování domény služby AD umožňuje uživatelům přihlásit se k Azure pomocí přihlašovacích údajů domény organizace. Vyžaduje server RADIUS, který se integruje se serverem AD. Organizace mohou také využít své stávající nasazení RADIUS.
 
Server RADIUS může být umístěn místně nebo ve vaší virtuální síti Azure. Během ověřování funguje brána VPN jako předávací a předává ověřovací zprávy tam a zpět mezi serverem RADIUS a připojujícím se zařízením. Je důležité, aby se brána VPN dostala na server RADIUS. Pokud je server RADIUS umístěn místně, je vyžadováno připojení vpn site-to-site z Azure k místnímu webu.

Kromě služby Active Directory může server RADIUS také integrovat s jinými externími identifikačními systémy. To otevírá spoustu možností ověřování pro sítě VIRTUÁLNÍ SÍTĚ point-to-site, včetně možností vícefaktorové ověřování. V dokumentaci k dodavateli serveru RADIUS získáte seznam systémů identit, se kterými se integruje.

![Diagram připojení – RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Pro připojení k místnímu serveru RADIUS lze použít pouze připojení k serveru VPN site-to-site. Nelze použít připojení ExpressRoute.
>
>

## <a name="before-beginning"></a><a name="before"></a>Před zahájením

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Příklady hodnot

Příklady hodnot můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku. Můžete buď využít kroky jako podrobný postup a převzít hodnoty beze jejich změny, nebo je změnit tak, aby odpovídaly vašemu prostředí.

* **Název: VNet1**
* **Adresní prostor: 192.168.0.0/16** a **10.254.0.0/16**<br>V tomto příkladu se používá více adresních prostorů k ilustraci, že tato konfigurace funguje s více adresními prostory. Více adresních prostorů pro ni ale není potřeba.
* **Název podsítě: FrontEnd**
  * **Rozsah adres podsítě: 192.168.1.0/24**
* **Název podsítě: BackEnd**
  * **Rozsah adres podsítě: 10.254.1.0/24**
* **Název podsítě: GatewaySubnet**<br>Název podsítě *GatewaySubnet* je pro správnou funkci brány VPN Gateway povinný.
  * **Rozsah adres podsítě brány: 192.168.200.0/24** 
* **Fond adres klientů VPN: 172.16.201.0/24**<br>Klienti VPN, kteří se budou k síti VNet připojovat pomocí tohoto připojení Point-to-Site, dostanou IP adresu ze zadaného fondu adres klienta VPN.
* **Předplatné:** Ujistěte se, že máte správné předplatné, pokud máte více než jedno.
* **Skupina prostředků: TestRG**
* **Umístění: USA – východ**
* **DNS Server: IP adresa** serveru DNS, který chcete použít pro překlad názvů pro virtuální síť. (volitelné)
* **Název brány: Vnet1GW**
* **Název veřejné IP adresy: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. Nastavte proměnné

Deklarujte proměnné, které chcete použít. Použijte následující příklad a dle potřeby nahraďte v něm uvedené hodnoty vlastními. Pokud během cvičení kdykoli zavřete relaci prostředí PowerShell/Cloud Shell, stačí znovu zkopírovat a vložit hodnoty, abyste proměnné znovu deklarovali.

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

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"> </a>Vytvoření skupiny prostředků, virtuální sítě a veřejné IP adresy

Následující kroky vytvoří skupinu prostředků a virtuální síť ve skupině prostředků se třemi podsítěmi. Při napočtení hodnot je důležité, abyste vždy pojmenovali podsíť brány specificky "GatewaySubnet". Pokud jej pojmenujete jinak, vytvoření brány se nezdaří;

1. Vytvořte skupinu prostředků.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Vytvořte konfigurace podsítí pro virtuální síť, podsítě pojmenujte *FrontEnd*, *BackEnd* a *GatewaySubnet*. Tyto předpony musí být součástí adresního prostoru virtuální sítě deklarovaného výše.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Vytvořte virtuální síť.

   V tomto příkladu je parametr serveru -DnsServer volitelný. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se ze své virtuální sítě připojujete. V tomto příkladu jsme použili privátní IP adresu, ale je pravděpodobné, že to není IP adresa vašeho serveru DNS. Je potřeba, abyste použili svoje vlastní hodnoty. Zadaná hodnota se používá prostředky, které nasadíte do virtuální sítě, nikoli připojení P2S.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Brána VPN musí mít veřejnou IP adresu. Nejprve si vyžádáte prostředek IP adresy a pak na něj budete odkazovat při vytváření brány virtuální sítě. IP adresa se dynamicky přiřadí k prostředku po vytvoření brány VPN. Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. Nemůžete si vyžádat statické přiřazení IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

   Zadejte proměnné, které chcete požádat o dynamicky přiřazenou veřejnou IP adresu.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"> </a>Nastavení serveru RADIUS

Před vytvořením a konfigurací brány virtuální sítě by měl být server RADIUS správně nakonfigurován pro ověřování.

1. Pokud nemáte nasazený server RADIUS, nasaďte ho. Postup nasazení naleznete v průvodci nastavením, který poskytuje dodavatel radius.  
2. Nakonfigurujte bránu VPN jako klienta RADIUS v radiusu. Při přidávání tohoto klienta RADIUS zadejte virtuální síť GatewaySubnet, kterou jste vytvořili. 
3. Po nastavení serveru RADIUS získáte IP adresu serveru RADIUS a sdílený tajný klíč, který by klienti RADIUS měli použít k rozhovoru se serverem RADIUS. Pokud je server RADIUS ve virtuální síti Azure, použijte IP certifikační autority virtuálního počítače serveru RADIUS.

Článek [Server síťových zásad (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) obsahuje pokyny ke konfiguraci serveru NPS systému Windows RADIUS pro ověřování domény služby AD.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"> </a>Vytvoření brány VPN

Konfigurace a vytvoření brány VPN pro vaši virtuální síť.

* -GatewayType musí být "Vpn" a -VpnType musí být RouteBased.
* Dokončení brány VPN může trvat až 45 minut v závislosti na vybrané [skladové jednotce](vpn-gateway-about-vpn-gateway-settings.md#gwsku) brány brány.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"> </a>Přidání fondu adres serveru RADIUS a klienta
 
* -RadiusServer lze zadat podle názvu nebo IP adresy. Pokud zadáte název a server je umístěn místně, brána VPN nemusí být schopna název přeložit. Pokud tomu tak je, je lepší zadat IP adresu serveru. 
* -RadiusSecret by měl odpovídat tomu, co je nakonfigurováno na serveru RADIUS.
* -VpnClientAddressPool je oblast, ze které připojující klienti VPN obdrží IP adresu.Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se budete připojovat, nebo s virtuální sítí, ke které se chcete připojit. Ujistěte se, že máte nakonfigurovaný dostatečně velký fond adres.  

1. Vytvořte zabezpečený řetězec pro tajný klíč RADIUS.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. Budete vyzváni k zadání tajného klíče RADIUS. Zadané znaky se nezobrazí a místo toho budou nahrazeny znakem "*".

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Přidejte fond adres klienta VPN a informace o serveru RADIUS.

   Pro konfigurace SSTP:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Pro konfigurace OpenVPN®:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   Pro konfigurace IKEv2:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Pro SSTP + IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"> </a>Stáhněte si konfigurační balíček klienta VPN a nastavte klienta VPN

Konfigurace klienta VPN umožňuje zařízením připojit se k virtuální síti přes připojení P2S.Informace o generování konfiguračního balíčku klienta VPN a nastavení klienta VPN naleznete [v tématu Vytvoření konfigurace klienta VPN pro ověřování RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. Připojení k Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Připojení z klienta VPN systému Windows

1. Chcete-li se připojit ke své síti VNet, přejděte na klientském počítači na připojení VPN a vyhledejte připojení VPN, které jste vytvořili. Bude mít stejný název jako vaše virtuální síť. Zadejte přihlašovací údaje domény a klikněte na tlačítko Připojit. Zobrazí se vyskakovací zpráva požadující zvýšená práva. Přijměte ji a zadejte pověření.

   ![Připojení klienta VPN k Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Vaše připojení bylo vytvořeno.

   ![Vytvořené připojení](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Připojení z klienta Mac VPN

V dialogovém okně Síť vyhledejte klientský profil, který chcete použít, a potom klikněte na **Připojit**.

  ![Připojení v systému Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Ověření stavu připojení

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

Informace o řešení potíží s připojením P2S najdete [v tématu Poradce při potížích s připojením Azure point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a><a name="faq"></a>Nejčastější dotazy

Toto nejčastější dotazy se vztahují na P2S pomocí ověřování RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/azure-vm-network-overview.md).
