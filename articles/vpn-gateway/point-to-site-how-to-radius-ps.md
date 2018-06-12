---
title: 'Připojení počítače k virtuální síti pomocí ověřování Point-to-Site a protokolu RADIUS: prostředí PowerShell | Azure'
description: Zabezpečené připojení klientů systému Windows a Mac OS X k virtuální netowrk pomocí ověřování P2S a protokolu RADIUS.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: anzaman
ms.openlocfilehash: df7afe9324831ffb8e79d7320f2c716ed18a7b4f
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267345"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Konfigurace připojení typu Point-to-Site k virtuální síti pomocí ověřování protokolu RADIUS: prostředí PowerShell

Tento článek ukazuje, jak vytvořit virtuální síť s připojením Point-to-Site, která používá ověřování pomocí protokolu RADIUS. Tato konfigurace je dostupná jenom pro model nasazení Resource Manager.

Brána VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení point-to-Site VPN jsou užitečné, pokud se chcete připojit k virtuální síti ze vzdáleného umístění, například když se jsou telefonicky z domova nebo z konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN Site-to-Site, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti.

Připojení VPN typu P2S se zahájí ze zařízení se systémem Windows nebo Mac. Klienti při připojování mohou využít následující metody ověřování:

* Server RADIUS
* Ověřování pomocí certifikátu nativní brána sítě VPN

Tento článek vám pomůže nakonfigurovat P2S konfigurace ověřování pomocí protokolu RADIUS serveru. Pokud chcete provést ověření pomocí generovaného certifikátů a ověřování nativní certifikát brány VPN místo toho najdete v tématu [konfigurace připojení typu Point-to-Site k virtuální síti VPN gateway nativní ověřování pomocí certifikátů](vpn-gateway-howto-point-to-site-rm-ps.md).

![Diagram připojení - RADIUS](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Připojení typu Point-to-Site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol) nebo protokolu IKEv2.

* SSTP je tunel VPN založený na SSL, který se podporuje jenom na klientských platformách Windows. Může proniknout branami firewall a díky tomu je ideální možností pro připojení k Azure odkudkoli. Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2.

* IKEv2 VPN, řešení IPsec VPN založené na standardech. IKEv2 VPN je možné použít k připojení ze zařízení se systémem Mac (OSX verze 10.11 a vyšší).

Připojení typu Point-to-Site vyžadují:

* Bránu VPN typu RouteBased. 
* Server RADIUS pro zpracování ověření uživatele. RADIUS server lze nasadit místně, nebo ve virtuální síti Azure.
* Balíček konfigurace klienta VPN pro zařízení Windows, která se budou připojovat k virtuální síti. Balíček konfigurace klienta VPN obsahuje nastavením požadovaným pro připojení přes P2S klienta VPN.

## <a name="aboutad"></a>O ověřování v doméně služby Active Directory (AD) pro sítě VPN P2S

Ověřování v doméně AD umožňuje uživatelům přihlášení do Azure pomocí svých přihlašovacích údajů domény organizace. To vyžaduje server RADIUS, který se integruje s serveru služby AD. Organizace můžete využít i jejich stávající nasazení protokolu RADIUS.
 
RADIUS server může být spuštěn místně nebo ve vaší virtuální síti Azure. Při ověřování služby VPN gateway funguje jako předávací a předávání zpráv ověřování mezi serverem RADIUS a zařízení pro připojení. Je důležité pro bránu VPN, moct připojit k serveru RADIUS. Pokud RADIUS server se nacházejí na místních, připojení VPN typu Site-to-Site z Azure do místní lokality není potřeba.

Kromě služby Active Directory můžete RADIUS server také integrovat s jinými systémy externí identity. Otevře se hodně možnosti ověřování pro sítě Point-to-Site VPN, včetně možnosti vícefaktorového ověřování. Podívejte se do dokumentace dodavatele serveru RADIUS získat seznam systémů identit, které se integruje.

![Diagram připojení - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Pouze připojení VPN typu Site-to-Site lze použít pro připojení k serveru protokolu RADIUS na místě. Připojení typu ExpressRoute nelze použít.
>
>

## <a name="before"></a>Před zahájením

* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

* Nainstalujte nejnovější verzi rutin PowerShellu pro Azure Resource Manager. Další informace o instalaci rutin prostředí PowerShell najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

### <a name="log-in"></a>Přihlásit se

[!INCLUDE [Log in](../../includes/vpn-gateway-ps-login-include.md)]

### <a name="example"></a>Příklady hodnot

Příklady hodnot můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku. Můžete buď využít kroky jako podrobný postup a převzít hodnoty beze jejich změny, nebo je změnit tak, aby odpovídaly vašemu prostředí.

* **Název: VNet1**
* **Adresní prostor: 192.168.0.0/16** a **10.254.0.0/16**<br>V tomto příkladu se používá více adresních prostorů k ilustraci, že tato konfigurace funguje s více adresními prostory. Více adresních prostorů pro ni ale není potřeba.
* **Název podsítě: FrontEnd**
  * **Rozsah adres podsítě: 192.168.1.0/24**
* **Název podsítě: BackEnd**
  * **Rozsah adres podsítě: 10.254.1.0/24**
* **Název podsítě: GatewaySubnet**<br>Název podsítě *GatewaySubnet* je pro správnou funkci brány VPN Gateway povinný.
  * **Rozsah adres podsítě brány: 192.168.200.0/24** 
* **Fond adres klienta VPN: 172.16.201.0/24**<br>Klienti VPN, kteří se budou k síti VNet připojovat pomocí tohoto připojení Point-to-Site, dostanou IP adresu ze zadaného fondu adres klienta VPN.
* **Předplatné:** Ujistěte se, že máte správné předplatné, pokud máte více než jedno.
* **Skupina prostředků: TestRG**
* **Umístění: Východní USA**
* **Serveru DNS: IP adresa** serveru DNS, který chcete použít pro překlad názvů pro virtuální síť. (volitelné)
* **Název brány: Vnet1GW**
* **Název veřejné IP adresy: VNet1GWPIP**
* **Typ sítě VPN: RouteBased** 

## 1. <a name="vnet"></a>Vytvoření skupiny prostředků, virtuální síť a veřejnou IP adresu

Následující postup vytvořte skupinu prostředků a virtuální síť ve skupině prostředků s tři podsítě. Při nahrazování hodnot je důležité vždy pojmenovat podsítě brány konkrétně GatewaySubnet. Pokud jste jiný název, vytvoření brány se nezdaří;

1. Vytvořte skupinu prostředků.

  ```powershell
  New-AzureRmResourceGroup -Name "TestRG" -Location "East US"
  ```
2. Vytvořte konfigurace podsítí pro virtuální síť, podsítě pojmenujte *FrontEnd*, *BackEnd* a *GatewaySubnet*. Tyto předpony musí být součástí adresního prostoru virtuální sítě deklarovaného výše.

  ```powershell
  $fesub = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
  $besub = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
  $gwsub = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
  ```
3. Vytvořte virtuální síť.

  V tomto příkladu je parametr serveru -DnsServer volitelný. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se ze své virtuální sítě připojujete. V tomto příkladu jsme použili privátní IP adresu, ale je pravděpodobné, že to není IP adresa vašeho serveru DNS. Je potřeba, abyste použili svoje vlastní hodnoty. Prostředky, které nasadíte do virtuální sítě, ne podle připojení P2S používá hodnotu, kterou jste určili.

  ```powershell
  New-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
  ```
4. Brána VPN musí mít veřejnou IP adresu. Nejprve si vyžádáte prostředek IP adresy a pak na něj budete odkazovat při vytváření brány virtuální sítě. IP adresa se dynamicky přiřadí k prostředku po vytvoření brány VPN. Služba VPN Gateway aktuálně podporuje pouze *dynamické* přidělení veřejné IP adresy. Nemůžete si vyžádat statické přiřazení IP adresy. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN bude měnit. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. V případě změny velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN se nezmění.

  Určete proměnné, k vyžádání dynamicky přiřazenou veřejnou IP adresu.

  ```powershell
  $vnet = Get-AzureRmVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
  $pip = New-AzureRmPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
  $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
  ```

## 2. <a name="radius"></a>Nastavit server protokolu RADIUS

Před vytvoření a konfiguraci brány virtuální sítě, by měl být serveru RADIUS, správně nakonfigurovaná pro ověřování.

1. Pokud nemáte server RADIUS nasazené, nasaďte jednu. Kroky nasazení naleznete v Průvodci instalací poskytované dodavatelem protokolu RADIUS.  
2. Nakonfigurujte bránu VPN jako klienta protokolu RADIUS na POLOMĚR. Při přidávání tohoto klienta RADIUS, zadejte virtuální síť GatewaySubnet, kterou jste vytvořili. 
3. RADIUS server je nastaveno, získáte IP adresu serveru RADIUS a sdílený tajný klíč, který by měl klientů RADIUS použít ke komunikaci se serverem RADIUS. Pokud RADIUS server ve virtuální síti Azure, použijte certifikační Autority IP ze serveru RADIUS virtuálních počítačů.

[Server NPS (Network Policy Server)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) článek obsahuje pokyny týkající se konfigurace Windows RADIUS serveru (NPS Server) pro ověřování v doméně AD.

## 3. <a name="creategw"></a>Vytvoření brány VPN

Nakonfigurujte a vytvořte bránu VPN pro virtuální síť.

* Parametr-GatewayType musí být 'Vpn, a parametr - VpnType musí být 'RouteBased'.
* Brána sítě VPN může trvat až 45 minut na dokončení, v závislosti na [skladová položka brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku) vyberete.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## 4. <a name="addradius"></a>Přidejte fond adres serveru a klienta protokolu RADIUS
 
* -RadiusServer lze zadat pomocí názvu nebo podle IP adresy. Pokud zadáte název a server se nachází v místě, nemusí být schopni vyřešit název brány sítě VPN. Pokud je to tento případ, je lepší zadejte IP adresu serveru. 
* -RadiusSecret by měl odpovídat, co je nakonfigurované na serveru RADIUS.
* -VpnCientAddressPool je oblast, odkud přijímat připojení klienti VPN IP adresu. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se budete připojovat, nebo s virtuální sítí, ke které se chcete připojit. Ujistěte se, že máte nakonfigurovaný fond dostatečně velké na adresu.  

1. Vytvořte zabezpečený řetězec pro POLOMĚR tajný.

  ```powershell
  $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
  ```

2. Zobrazí se výzva k zadání tajného klíče protokolu RADIUS. Znaky, které zadáte, se nezobrazí a místo toho budou nahrazeny "*" znak.

  ```powershell
  RadiusSecret:***
  ```
3. Přidejte fond adres klienta VPN a informace o serveru RADIUS.

  Pro SSTP konfigurace:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Pro IKEv2 konfigurace:

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

  Pro SSTP a IKEv2

    ```powershell
    $Gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

## 5. <a name="vpnclient"></a>Stáhněte balíček konfigurace klienta VPN a nastavení klienta VPN

Konfigurace klienta VPN umožňuje připojení k virtuální síti přes připojení P2S zařízením. Generovat balíček konfigurace klienta VPN a nastavení klienta VPN najdete v tématu [vytvořit konfiguraci klienta VPN pro ověřování pomocí protokolu RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="connect"></a>6. Připojení k Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Připojení z klienta VPN systému Windows

1. Chcete-li se připojit ke své síti VNet, přejděte na klientském počítači na připojení VPN a vyhledejte připojení VPN, které jste vytvořili. Bude mít stejný název jako vaše virtuální síť. Zadejte své přihlašovací údaje domény a klikněte na tlačítko "Připojit". Se zobrazí automaticky otevírané okno zprávy, která žádá zvýšenými oprávněními. Jejich přijetí a zadejte přihlašovací údaje.

  ![Připojení klienta VPN k Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Vaše připojení bylo vytvořeno.

  ![Vytvořené připojení](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Připojení z klienta Mac VPN

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

K vyřešení připojení P2S najdete v tématu [připojení point-to-site řešení potíží s Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="faq"></a>Nejčastější dotazy

Tyto nejčastější dotazy se vztahuje na P2S pomocí ověřování protokolu RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Další postup

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/azure-vm-network-overview.md).
