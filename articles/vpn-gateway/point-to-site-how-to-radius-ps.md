---
title: 'Připojení počítače k virtuální síti pomocí Point-to-Site a ověřování pomocí protokolu RADIUS: Prostředí PowerShell | Azure'
description: Připojování klientů Windows a Mac OS X k virtuální síti pomocí ověřování P2S a protokolu RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: cherylmc
ms.openlocfilehash: bd74aca180d291042e597ba6893009c38aa22555
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200900"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurace připojení typu Point-to-Site k virtuální síti s použitím ověřování pomocí protokolu RADIUS: PowerShell

Tento článek ukazuje, jak vytvořit virtuální síť s připojením Point-to-Site, která používá ověřování pomocí protokolu RADIUS. Tato konfigurace dostupná jenom pro model nasazení Resource Manager.

Brána VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení k síti VPN Point-to-Site jsou užitečné, pokud chcete pro připojení k virtuální síti ze vzdáleného umístění, například při práci z domova nebo z místa konání konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN Site-to-Site, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti.

Připojení VPN typu P2S se zahájí ze zařízení se systémem Windows nebo Mac. Klienti při připojování mohou využít následující metody ověřování:

* Server RADIUS
* Brána VPN nativního ověřování certifikátů

Tento článek pomůže vám nakonfigurovat konfiguraci P2S s ověřováním pomocí protokolu RADIUS server. Pokud chcete ověřit, místo toho použít certifikáty generované a VPN gateway nativního ověřování certifikátů, přečtěte si téma [konfigurace připojení typu Point-to-Site k virtuální síti pomocí sítě VPN gateway nativního ověřování certifikátů](vpn-gateway-howto-point-to-site-rm-ps.md).

![Diagram připojení - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Připojení typu Point-to-Site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol) nebo protokolu IKEv2.

* SSTP je tunel VPN založený na SSL, který se podporuje jenom na klientských platformách Windows. Může proniknout branami firewall a díky tomu je ideální možností pro připojení k Azure odkudkoli. Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2.

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).

Připojení typu Point-to-Site vyžadují:

* Bránu VPN typu RouteBased. 
* Server RADIUS pro zpracování ověření uživatele. RADIUS server může být nasazená místně, nebo ve virtuální síti Azure.
* Konfiguračního balíčku klienta VPN pro zařízení Windows, které se připojují k virtuální síti. Konfigurační balíček klienta VPN obsahuje požadované nastavení pro klienta VPN pro připojení přes P2S.

## <a name="aboutad"></a>Informace o ověřování v doméně služby Active Directory (AD) pro P2S VPN

Doménové ověřování AD umožňuje uživatelům přihlášení do Azure pomocí svých firemních přihlašovacích údajů domény. Vyžaduje server RADIUS, která se integruje se serverem AD. Organizace také můžete využít své stávající nasazení pomocí protokolu RADIUS.
 
RADIUS server může být místní, nebo ve vaší virtuální síti Azure. Během ověřování VPN gateway funguje jako předávací a předávání zpráv ověřování vpřed a zpět mezi serverem RADIUS a připojení zařízení. Je důležité pro VPN gateway bude moct připojit k serveru RADIUS. Pokud je server protokolu RADIUS v místním, vyžaduje se připojení VPN Site-to-Site z Azure do místní lokality.

Kromě služby Active Directory server protokolu RADIUS můžete integrovat také s jinými systémy pro externí identity. Otevře spoustu možnosti ověřování pro připojení Point-to-Site VPN, včetně možnosti MFA. Kontrola protokolu RADIUS serveru dokumentace od příslušného dodavatele zobrazíte seznam systémů identit, které se integruje se službou.

![Diagram připojení - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Pouze připojení VPN typu Site-to-Site lze použít pro připojení k serveru pomocí protokolu RADIUS v místním prostředí. Připojení ExpressRoute nejde použít.
>
>

## <a name="before"></a>Než začnete

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="sign-in"></a>Přihlášení

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

### <a name="example"></a>Příklady hodnot

Příklady hodnot můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku. Můžete buď využít kroky jako podrobný postup a převzít hodnoty beze jejich změny, nebo je změnit tak, aby odpovídaly vašemu prostředí.

* **Jméno: Ze sítě VNet1**
* **Adresní prostor: 192.168.0.0/16** a **10.254.0.0/16**<br>V tomto příkladu se používá více adresních prostorů k ilustraci, že tato konfigurace funguje s více adresními prostory. Více adresních prostorů pro ni ale není potřeba.
* **Název podsítě: Front-endu**
  * **Rozsah adres podsítě: 192.168.1.0/24**
* **Název podsítě: Back-endu**
  * **Rozsah adres podsítě: 10.254.1.0/24**
* **Název podsítě: Podsíť brány**<br>Název podsítě *GatewaySubnet* je pro správnou funkci brány VPN Gateway povinný.
  * **Rozsah adres podsítě brány: 192.168.200.0/24** 
* **Fond adres klienta VPN: 172.16.201.0/24**<br>Klienti VPN, kteří se budou k síti VNet připojovat pomocí tohoto připojení Point-to-Site, dostanou IP adresu ze zadaného fondu adres klienta VPN.
* **Předplatné:** Pokud máte více než jedno předplatné, ověřte, že používáte tu správnou.
* **Skupina prostředků: TestRG**
* **Umístění: USA – východ**
* **DNS Server: IP adresa** serveru DNS, který chcete použít pro překlad názvů pro vaši virtuální síť. (volitelné)
* **Název brány: Brány Vnet1GW**
* **Název veřejné IP: VNet1GWPIP**
* **Typ sítě VPN: RouteBased** 

## 1. <a name="vnet"></a>Vytvořte skupinu prostředků, virtuální sítě a veřejné IP adresy

Následující postup vytvořte skupinu prostředků a virtuální sítě ve skupině prostředků se třemi podsítěmi. Při nahrazování hodnot je důležité vždy pojmenovat podsítě brány speciálně "GatewaySubnet". Pokud jste jiný název, vytvoření brány selže.

1. Vytvořte skupinu prostředků.

  ```azurepowershell-interactive
  New-AzureRmResourceGroup -Name "TestRG" -Location "East US"
  ```
2. Vytvořte konfigurace podsítí pro virtuální síť, podsítě pojmenujte *FrontEnd*, *BackEnd* a *GatewaySubnet*. Tyto předpony musí být součástí adresního prostoru virtuální sítě deklarovaného výše.

  ```azurepowershell-interactive
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
  ```
3. Vytvořte virtuální síť.

  V tomto příkladu je parametr serveru -DnsServer volitelný. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se ze své virtuální sítě připojujete. V tomto příkladu jsme použili privátní IP adresu, ale je pravděpodobné, že to není IP adresa vašeho serveru DNS. Je potřeba, abyste použili svoje vlastní hodnoty. Hodnota, kterou zadáte, používá prostředky, které nasadíte do virtuální sítě, ne pomocí připojení P2S.

  ```azurepowershell-interactive
  New-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Brána VPN musí mít veřejnou IP adresu. Nejprve si vyžádáte prostředek IP adresy a pak na něj budete odkazovat při vytváření brány virtuální sítě. IP adresa se dynamicky přiřadí k prostředku po vytvoření brány VPN. Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. Nemůžete si vyžádat statické přiřazení IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

  Zadejte proměnné pro žádost o dynamicky přidělovanou veřejnou IP adresu.

  ```azurepowershell-interactive
  $vnet = Get-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
  $pip = New-AzureRmPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
  ```

## 2. <a name="radius"></a>Nastavení serveru protokolu RADIUS

Před vytvořením a konfigurace brány virtuální sítě, musí být serveru RADIUS, správně nakonfigurovaný pro ověřování.

1. Pokud nemáte server RADIUS nasazené, nasaďte jednu. Kroky nasazení najdete v Průvodci instalací poskytované dodavatelem RADIUS.  
2. Konfigurace brány VPN jako klienta protokolu RADIUS na POLOMĚR. Při přidávání tohoto klienta RADIUS, zadejte virtuální síť podsíť brány, kterou jste vytvořili. 
3. Po nastavení serveru protokolu RADIUS se získáte IP adresu serveru RADIUS a sdílený tajný klíč RADIUS klienti měli používat ke komunikaci se serverem RADIUS. Pokud RADIUS server je ve virtuální síti Azure, použijte certifikační Autority IP adresa serveru RADIUS, virtuálního počítače.

[Server NPS (Network Policy Server)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) článek obsahuje pokyny týkající se konfigurace Windows RADIUS server (NPS) pro ověřování v doméně AD.

## 3. <a name="creategw"></a>Vytvoření brány VPN

Nakonfigurujte a vytvořte bránu VPN pro vaši virtuální síť.

* Parametr-GatewayType musí být "Vpn" a parametr-VpnType musí být "RouteBased".
* Brány VPN může trvat až 45 minut, v závislosti na tom [skladové položky brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku) vyberete.

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"></a>Přidání fondu adres serveru a klienta protokolu RADIUS
 
* RadiusServer – je možné zadat pomocí názvu nebo podle IP adresy. Pokud zadáte název a server se nachází v místním, nemusí být schopni vyřešit název brány sítě VPN. Pokud je to tento případ, je lepší zadejte IP adresu serveru. 
* -RadiusSecret by měl odpovídat, co je nakonfigurováno na serveru RADIUS.
* -VpnCientAddressPool je oblast, ze kterého dostanou připojovaní klienti VPN IP adresu. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se budete připojovat, nebo s virtuální sítí, ke které se chcete připojit. Ujistěte se, že máte fond dostatečně velký adres, nakonfigurována.  

1. Vytvořte zabezpečený řetězec poloměru tajného kódu.

  ```azurepowershell-interactive
  $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
  ```

2. Zobrazí se výzva k zadání tajný klíč RADIUS. Znaky, které zadáte, se nezobrazí a místo toho bude nahrazen "*" znak.

  ```azurepowershell-interactive
  RadiusSecret:***
  ```
3. Přidejte fond adres klienta VPN a informace o serveru RADIUS.

  Pro SSTP konfigurace:

    ```azurepowershell-interactive
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Pro IKEv2 konfigurace:

    ```azurepowershell-interactive
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Pro SSTP a IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"></a>Stažení konfiguračního balíčku klienta VPN a nastavení klienta VPN

Konfigurace klienta VPN umožňuje připojení k virtuální síti přes připojení P2S zařízením. Generování konfiguračního balíčku klienta VPN a nastavení klienta VPN najdete v tématu [vytvořit konfiguraci klienta VPN pro ověřování pomocí protokolu RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Připojení k Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Připojení z klienta VPN systému Windows

1. Chcete-li se připojit ke své síti VNet, přejděte na klientském počítači na připojení VPN a vyhledejte připojení VPN, které jste vytvořili. Bude mít stejný název jako vaše virtuální síť. Zadejte svoje přihlašovací údaje domény a klikněte na tlačítko "Připojit". Zobrazí se místní zpráva požadující zvýšenými oprávněními. Přijmout a zadejte přihlašovací údaje.

  ![Připojení klienta VPN k Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Vaše připojení bylo vytvořeno.

  ![Vytvořené připojení](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Připojení z klienta VPN pro Mac

V dialogovém okně Síť vyhledejte klientský profil, který chcete použít, a potom klikněte na **Připojit**.

  ![Připojení v systému Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Ověření stavu připojení

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

Řešení potíží s připojení P2S, naleznete v tématu [připojení point-to-site řešení potíží s Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>Nejčastější dotazy

Tyto nejčastější dotazy se vztahuje na P2S pomocí ověřování pomocí protokolu RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další postup

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/azure-vm-network-overview.md).
