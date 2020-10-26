---
title: 'Připojení počítače k virtuální síti s použitím ověřování typu Point-to-site a ověřování RADIUS: PowerShell | Azure'
description: Připojte klienty Windows a OS X bezpečně k virtuální síti pomocí ověřování P2S a RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: bce381ba4916bc58d2c7acf8d69b323dbdf972aa
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544779"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurace připojení typu Point-to-site k virtuální síti pomocí ověřování RADIUS: PowerShell

V tomto článku se dozvíte, jak vytvořit virtuální síť s připojením Point-to-site, které používá ověřování pomocí protokolu RADIUS. Tato konfigurace je k dispozici pouze pro model nasazení Správce prostředků.

Brána VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení VPN typu Point-to-site jsou užitečná, když se chcete připojit k virtuální síti ze vzdáleného umístění, například při práci z domova nebo z konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN Site-to-Site, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti.

Připojení VPN typu P2S se zahájí ze zařízení se systémem Windows nebo Mac. Klienti při připojování mohou využít následující metody ověřování: 

* Server RADIUS
* VPN Gateway ověřování nativního certifikátu
* Nativní ověřování Azure Active Directory (jenom Windows 10)

Tento článek vám pomůže nakonfigurovat konfiguraci P2S s ověřováním pomocí serveru RADIUS. Pokud se chcete ověřit pomocí vygenerovaných certifikátů a nativního ověřování certifikátů pomocí služby VPN Gateway, přečtěte si téma [Konfigurace připojení typu Point-to-site k virtuální síti pomocí ověřování nativního certifikátu pomocí brány VPN](vpn-gateway-howto-point-to-site-rm-ps.md) nebo [Vytvoření klienta Azure Active Directory pro připojení protokolu P2S OpenVPN](openvpn-azure-ad-tenant.md) pro ověřování Azure Active Directory.

![Diagram, který zobrazuje konfiguraci P2S s ověřováním pomocí serveru protokolu RADIUS.](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Připojení typu Point-to-Site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol), OpenVPN nebo IKEv2.

* SSTP je tunelové propojení sítě VPN založené na protokolu TLS, které je podporováno pouze na klientských platformách systému Windows. Může proniknout branami firewall a díky tomu je ideální možností pro připojení k Azure odkudkoli. Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2.

* Protokol OpenVPN®, protokol VPN založený na protokolu SSL/TLS. Řešení TLS VPN může proniknout do brány firewall, protože většina bran firewall otevírá odchozí port TCP 443, který používá protokol TLS. OpenVPN se dá použít k připojení z Androidu, iOS (verze 11,0 a vyšší), Windows, Linux a Mac (OSX verze 10,13 a vyšší).

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).

Připojení typu Point-to-Site vyžadují:

* Bránu VPN typu RouteBased. 
* Server RADIUS pro zpracování ověření uživatele. Server RADIUS se dá nasadit místně nebo ve virtuální síti Azure. Pro zajištění vysoké dostupnosti můžete také nakonfigurovat dva servery RADIUS.
* Konfigurační balíček klienta VPN pro zařízení s Windows, která se budou připojovat k virtuální síti. Konfigurační balíček klienta VPN poskytuje nastavení potřebné pro připojení klienta VPN přes P2S.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Ověřování domény služby Active Directory (AD) pro sítě VPN P2S

Ověřování domény AD umožňuje uživatelům přihlásit se k Azure pomocí svých přihlašovacích údajů domény organizace. Vyžaduje server RADIUS, který se integruje se serverem AD. Organizace můžou využít i stávající nasazení RADIUS.
 
Server RADIUS se může nacházet v místním prostředí nebo ve vaší virtuální síti Azure. Při ověřování funguje brána VPN jako předávací a přesměruje ověřovací zprávy mezi serverem RADIUS a připojeným zařízením. Pro bránu VPN je důležité, aby se mohl připojit k serveru RADIUS. Pokud se server RADIUS nachází v místním prostředí, vyžaduje se připojení VPN typu Site-to-Site z Azure k místní lokalitě.

Kromě služby Active Directory se může server RADIUS integrovat i s dalšími externími systémy identit. Tím se otevře dostatek možností ověřování pro sítě VPN typu Point-to-site, včetně možností MFA. Seznam systémů identit, se kterými se integruje, najdete v dokumentaci dodavatele serveru RADIUS.

![Diagram připojení – RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>K místnímu připojení k serveru RADIUS se dá použít jenom připojení VPN typu Site-to-site. Připojení ExpressRoute není možné použít.
>
>

## <a name="before-beginning"></a><a name="before"></a>Před zahájením

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Příklady hodnot

Příklady hodnot můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku. Můžete buď využít kroky jako podrobný postup a převzít hodnoty beze jejich změny, nebo je změnit tak, aby odpovídaly vašemu prostředí.

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
* **Server DNS: IP adresa** serveru DNS, který chcete použít pro překlad názvů vaší virtuální sítě. (volitelné)
* **Název brány: Vnet1GW**
* **Název veřejné IP adresy: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. nastavení proměnných

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

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"></a> Vytvoření skupiny prostředků, virtuální sítě a veřejné IP adresy

Následující kroky vytvoří skupinu prostředků a virtuální síť ve skupině prostředků se třemi podsítěmi. Při nahrazování hodnot je důležité vždycky pojmenovat podsíť brány konkrétně "GatewaySubnet". Pokud ho pojmenovat něco jiného, vytvoření brány se nepovede.

1. Vytvořte skupinu prostředků.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Vytvořte konfigurace podsítí pro virtuální síť, podsítě pojmenujte *FrontEnd* , *BackEnd* a *GatewaySubnet* . Tyto předpony musí být součástí adresního prostoru virtuální sítě deklarovaného výše.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Vytvořte virtuální síť.

   V tomto příkladu je parametr serveru -DnsServer volitelný. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se ze své virtuální sítě připojujete. V tomto příkladu jsme použili privátní IP adresu, ale je pravděpodobné, že to není IP adresa vašeho serveru DNS. Je potřeba, abyste použili svoje vlastní hodnoty. Hodnota, kterou zadáte, se používá v prostředcích, které nasazujete do virtuální sítě, nikoli prostřednictvím připojení P2S.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Brána VPN musí mít veřejnou IP adresu. Nejprve si vyžádáte prostředek IP adresy a pak na něj budete odkazovat při vytváření brány virtuální sítě. IP adresa se dynamicky přiřadí k prostředku po vytvoření brány VPN. Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. Nemůžete si vyžádat statické přiřazení IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

   Zadejte proměnné pro vyžádání dynamicky přiřazené veřejné IP adresy.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"></a> nastavení serveru protokolu RADIUS

Před vytvořením a konfigurací brány virtuální sítě by měl být server RADIUS správně nakonfigurovaný pro ověřování.

1. Pokud nemáte nasazený server RADIUS, nasaďte ho. Postup nasazení najdete v příručce k instalaci poskytované vaším dodavatelem protokolu RADIUS.  
2. Nakonfigurujte bránu VPN jako klienta RADIUS na protokolu RADIUS. Při přidávání tohoto klienta protokolu RADIUS zadejte GatewaySubnet virtuální sítě, kterou jste vytvořili. 
3. Po nastavení serveru RADIUS Získejte IP adresu serveru RADIUS a sdílený tajný klíč, který by klienti RADIUS měli používat ke komunikaci se serverem RADIUS. Pokud je server RADIUS ve virtuální síti Azure, použijte IP adresu certifikační autority virtuálního počítače serveru RADIUS.

Článek [NPS (Network Policy Server)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) poskytuje pokyny ke konfiguraci serveru NPS (Windows RADIUS Server) pro ověřování domény AD.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"></a> Vytvoření brány VPN

Nakonfigurujte a vytvořte bránu VPN pro virtuální síť.

* Vlastnost-GatewayType musí mít hodnotu VPN a vlastnost-VpnType musí mít hodnotu RouteBased.
* Dokončení brány VPN může trvat až 45 minut, v závislosti na vybrané [skladové jednotce brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku)   .

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"></a> přidejte server protokolu RADIUS a fond adres klienta
 
* Parametr-RadiusServer lze zadat podle názvu nebo podle IP adresy. Pokud zadáte název a server se nachází v místním prostředí, brána sítě VPN nemusí být schopná tento název přeložit. Pokud je to tento případ, je vhodnější zadat IP adresu serveru. 
* RadiusSecret by se měl shodovat s tím, co je nakonfigurováno na serveru protokolu RADIUS.
* VpnClientAddressPool je rozsah, ze kterého se připojující klienti VPN dostanou IP adresu. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se budete připojovat, nebo s virtuální sítí, ke které se chcete připojit. Ujistěte se, že máte nakonfigurovaný dostatečně velký fond adres.  

1. Vytvořte zabezpečený řetězec pro tajný klíč protokolu RADIUS.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. Zobrazí se výzva k zadání tajného kódu protokolu RADIUS. Znaky, které zadáte, nebudou zobrazeny a místo toho budou nahrazeny znakem "*".

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Přidejte informace o fondu adres klienta VPN a serveru RADIUS.

   Pro konfigurace SSTP:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Pro OpenVPN® konfigurace:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   Konfigurace IKEv2:

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

   Chcete-li zadat **dva** servery RADIUS **(verze Preview)** , použijte následující syntaxi. Upravte hodnotu **-VpnClientProtocol** podle potřeby.

    ```azurepowershell-interactive
    $radiusServer1 = New-AzRadiusServer -RadiusServerAddress 10.1.0.15 -RadiusServerSecret $radiuspd -RadiusServerScore 30
    $radiusServer2 = New-AzRadiusServer -RadiusServerAddress 10.1.0.16 -RadiusServerSecret $radiuspd -RadiusServerScore 1

    $radiusServers = @( $radiusServer1, $radiusServer2 )

    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $actual -VpnClientAddressPool 201.169.0.0/16 -VpnClientProtocol "IkeV2" -RadiusServerList $radiusServers
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"></a> Stáhněte si konfigurační balíček klienta VPN a nastavte klienta VPN.

Konfigurace klienta VPN umožňuje zařízením připojit se k virtuální síti přes připojení P2S. Pokud chcete vygenerovat konfigurační balíček klienta VPN a nastavit klienta VPN, přečtěte si téma [Vytvoření konfigurace klienta VPN pro ověřování RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. připojení k Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Připojení z klienta VPN systému Windows

1. Chcete-li se připojit ke své síti VNet, přejděte na klientském počítači na připojení VPN a vyhledejte připojení VPN, které jste vytvořili. Bude mít stejný název jako vaše virtuální síť. Zadejte své přihlašovací údaje do domény a klikněte na připojit. Zobrazí se automaticky otevíraná zpráva požadující zvýšená oprávnění. Přijměte a zadejte přihlašovací údaje.

   ![Připojení klienta VPN k Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Vaše připojení bylo vytvořeno.

   ![Vytvořené připojení](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Připojení z klienta VPN Mac

V dialogovém okně Síť vyhledejte klientský profil, který chcete použít, a potom klikněte na **Připojit** .

  ![Připojení v systému Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Ověření stavu připojení

1. Chcete-li ověřit, zda je připojení VPN aktivní, v příkazovém řádku se zvýšenými oprávněními spusťte příkaz *ipconfig/all* .
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

Řešení potíží s připojením k P2S najdete v tématu řešení potíží s připojením typu [Point-to-site v Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Ověřte, že se po zadání IP adres serveru DNS pro virtuální síť vygeneroval balíček pro konfiguraci klienta VPN. Pokud jste aktualizovali IP adresy serveru DNS, vygenerujte a nainstalujte nový balíček pro konfiguraci klienta VPN.

* Pomocí příkazu ipconfig zkontrolujte IPv4 adresu přiřazenou adaptéru Ethernet na počítači, ze kterého se připojujete. Pokud je IP adresa v rámci rozsahu adres virtuální sítě, ke které se připojujete, nebo v rámci rozsahu adres VPNClientAddressPool, tato situace se označuje jako překrývající se adresní prostor. Když se adresní prostor tímto způsobem překrývá, síťový provoz nemá přístup do Azure a zůstane v místní síti.

## <a name="faq"></a><a name="faq"></a>Nejčastější dotazy

Tyto nejčastější dotazy platí pro P2S pomocí ověřování RADIUS.

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/azure-vm-network-overview.md).
