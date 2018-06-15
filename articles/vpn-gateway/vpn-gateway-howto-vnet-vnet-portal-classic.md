---
title: 'Vytvořte připojení mezi virtuálními sítěmi: classic: portálu Azure | Microsoft Docs'
description: Připojte virtuální sítě Azure pomocí prostředí PowerShell a portálu Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: d9766afefa793baf66ea5218843f06031b1b364c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601052"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurace připojení typu VNet-to-VNet (klasické)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Tento článek vám pomůže vytvořit připojení k bráně VPN mezi virtuálními sítěmi. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných. Postup v tomto článku se vztahuje k modelu nasazení classic a portálu Azure. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Virtuální sítě do diagramu připojení virtuální sítě](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Informace o propojeních VNet-to-VNet

Propojení virtuální sítě s jinou virtuální sítí (VNet-to-VNet) v modelu nasazení classic s použitím brány VPN je podobné propojení virtuální sítě do umístění místního webu. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE.

Virtuální sítě, ke kterým se připojujete, může být v různých předplatných a různých oblastech. Virtuální síť můžete kombinovat komunikaci VNet s konfigurací s více servery. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí.

![Virtuální síť připojení virtuální sítě](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Proč propojovat virtuální sítě?

Virtuální sítě může být vhodné propojit z následujících důvodů:

* **Geografická redundance napříč oblastmi a geografická přítomnost**

  * Můžete nastavit vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením bez procházení koncovými body připojenými k internetu.
  * S nástroj pro vyrovnávání zatížení Azure a Microsoft nebo třetích stran clustering technologií můžete nastavit úlohy s vysokou dostupností s geografickou redundancí nad několika oblastmi Azure. Jedním z důležitých příkladů je nastavení technologie SQL Always On se skupinami dostupnosti nad několika oblastmi Azure.
* **Regionální vícevrstvé aplikace s silnou izolaci hranic**

  * V rámci stejné oblasti můžete nastavit vícevrstvé aplikace s více virtuálních sítí připojené společně s silnou izolaci a zabezpečenou komunikaci mezi vrstvy.
* **Mezi předplatného, komunikace mezi organizace v Azure**

  * Pokud máte víc předplatných Azure, můžete se připojit úlohy z různých předplatných společně bezpečně mezi virtuálními sítěmi.
  * Pro podniky a poskytovatelé služeb můžete povolit komunikaci mezi organizace s zabezpečené technologie VPN v rámci Azure.

Další informace o propojeních VNet-to-VNet najdete v části [Aspekty propojení VNet-to-VNet](#faq) na konci tohoto článku.

### <a name="before-you-begin"></a>Než začnete

Před zahájením tohoto cvičení, stáhněte a nainstalujte nejnovější verzi rutin prostředí PowerShell Azure Service Management (SM). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Pro většinu kroků používáme portálu, ale musíte použít PowerShell k vytvoření připojení mezi virtuální sítě. Nelze vytvořit připojení pomocí portálu Azure.

## <a name="plan"></a>Krok 1: Plánování rozsahů IP adres

Je důležité určit rozsahy, které budete používat ke konfiguraci virtuálních sítí. Pro tuto konfiguraci musíte zkontrolovat, že žádný z vaší virtuální sítě rozsahů nepřekrývá mezi sebou, nebo s žádným z místní sítě, které se připojují k.

Následující tabulka ukazuje příklad toho, jak definovat vaší virtuální sítě. Rozsahy použijte jako vodítko jenom. Poznamenejte si rozsahy virtuálních sítí. Je třeba tyto informace pro pozdější kroky.

**Příklad**

| Virtual Network | Adresní prostor | Oblast | Připojí se k místnímu síťovému webu |
|:--- |:--- |:--- |:--- |
| Virtuální síť TestVNet1 |Virtuální síť TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Východ USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| Virtuální síť TestVNet4 |Virtuální síť TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Západní USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Krok 2 – vytvoření virtuální sítě

Vytvoření dvou virtuálních sítí v [portál Azure](https://portal.azure.com). Kroky pro vytvoření klasické virtuální sítě najdete v tématu [vytvoření klasické virtuální sítě](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Při použití portálu k vytvoření klasické virtuální sítě, je nutné přejít na stránku virtuální sítě pomocí následujících kroků, jinak se nezobrazí možnost vytvoření klasické virtuální sítě:

1. Klikněte '+' otevřít stránku 'New'.
2. V poli "vyhledávání na webu marketplace, zadejte"Virtuální sítě". Pokud místo toho vyberte sítě -> virtuální sítě, nebude získáte možnost vytvoření klasické virtuální sítě.
3. Vyhledejte "virtuální sítě, ze seznamu vrácených a klikněte na něj chcete otevřít stránku virtuální sítě. 
4. Na stránce virtuální sítě vyberte možnost "Klasickém" k vytvoření klasické virtuální sítě. 

Pokud používáte tento článek jako cvičení, můžete použít následující ukázkové hodnoty:

**Hodnoty pro virtuální síť TestVNet1**

Název: TestVNet1<br>
Adresní prostor: 10.11.0.0/16, 10.12.0.0/16 (volitelné)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.11.0.1/24<br>
Skupina prostředků: ClassicRG<br>
Umístění: Východní USA<br>
GatewaySubnet: 10.11.1.0/27

**Hodnoty pro virtuální síť TestVNet4**

Název: virtuální sítě TestVNet4<br>
Adresní prostor: 10.41.0.0/16, 10.42.0.0/16 (volitelné)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.41.0.1/24<br>
Skupina prostředků: ClassicRG<br>
Umístění: Západní USA<br>
GatewaySubnet: 10.41.1.0/27

**Při vytváření vaší virtuální sítě, mějte na paměti následující nastavení:**

* **Adresní prostory virtuální sítě** – na stránce virtuální síť adresní prostory, zadejte rozsah adres, který chcete použít pro vaši virtuální síť. Toto jsou dynamické IP adresy, které budou přiřazené pro virtuální počítače a další instance rolí, které nasadíte do této virtuální sítě.<br>Adresní prostory, které vyberete se nesmí překrývat s adresní prostory pro některý z jiných virtuálních sítí nebo na místní umístění, která této virtuální sítě se budou připojovat k.

* **Umístění** – když vytvoříte virtuální síť, přiřaďte ji k Azure umístěním (oblastí). Například pokud chcete, aby vaše virtuální počítače, které jsou nasazeny k virtuální síti, aby byly fyzicky umístěné ve západní USA, vyberte toto umístění. Nelze změnit umístění přidruženou k virtuální síti po jejím vytvoření.

**Po vytvoření vaší virtuální sítě, můžete přidat následující nastavení:**

* **Adresní prostor** – další adresní prostor se nevyžaduje pro tuto konfiguraci, ale můžete přidat další adresní prostor po vytvoření virtuální sítě.

* **Podsítě** – dodatečné podsítě nejsou vyžadovány pro tuto konfiguraci, ale můžete chtít mít virtuální počítače v podsíti oddělené od dalších instancí rolí.

* **Servery DNS** – zadejte název serveru DNS a IP adresu. Toto nastavení neslouží k vytvoření serveru DNS. Umožňuje určit server DNS, který chcete použít pro překlad názvů pro tuto virtuální síť.

V této části nakonfigurujete typu připojení místní lokality a vytvoření brány.

## <a name="localsite"></a>Krok 3: Konfigurace místní lokalitě.

Azure používá nastavení zadané v každé lokalitě místní sítě určit, jak směrovat provoz mezi virtuální sítě. Každý virtuální síť musí odkazovat na příslušné místní síť, kterou chcete směrovat provoz. Můžete určit název, který chcete použít k odkazování na každé lokalitě místní sítě. Je nejvhodnější použít popisný text.

Například virtuální sítě TestVNet1 připojí k místní síťové lokality, který můžete vytvořit s názvem 'VNet4Local'. Nastavení pro VNet4Local obsahovat předpony adres pro virtuální síť TestVNet4.

Místní lokalita pro každý virtuální síť je jiné virtuální sítě. Následující ukázkové hodnoty se používají pro naše konfigurace:

| Virtual Network | Adresní prostor | Oblast | Připojí se k místnímu síťovému webu |
|:--- |:--- |:--- |:--- |
| Virtuální síť TestVNet1 |Virtuální síť TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Východ USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| Virtuální síť TestVNet4 |Virtuální síť TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Západní USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Najděte virtuální síť TestVNet1 na portálu Azure. V **připojení k síti VPN** části stránky klikněte na tlačítko **brány**.

    ![Žádná brána](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na **nové připojení VPN** vyberte **Site-to-Site**.
3. Klikněte na tlačítko **místní lokality** otevřít stránku místního webu a nakonfigurovat nastavení.
4. Na **místní lokality** stránky, název vaší místní lokalitě. V našem příkladu jsme názvu místního serveru 'VNet4Local'.
5. Pro **IP adresa brány VPN**, každou IP adresu, která chcete, můžete použít, dokud je v platném formátu. Obvykle byste použili skutečné externí IP adresu pro zařízení VPN. Ale pro konfiguraci classic VNet-to-VNet, použijte veřejnou IP adresu, která je přiřazena k bráně pro vaši virtuální síť. Vzhledem k tomu, že zatím jste vytvořili bránu virtuální sítě, je třeba zadat libovolný platný veřejnou IP adresu jako zástupný znak.<br>Nemáte nechte pole prázdné, – není pro tuto konfiguraci volitelné. Později přejděte zpět do těchto nastavení a konfigurace pomocí odpovídající IP adresy brány virtuální sítě, jakmile ho generuje Azure.
6. Pro **klienta adresní prostor**, použít adresní prostor jiné sítě vnet. Naleznete plánování příkladu. Klikněte na tlačítko **OK** a uložíte své nastavení vrátit zpět **nové připojení VPN** stránky.

    ![místní lokalita](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Krok 4 – vytvoření brány virtuální sítě

Každá virtuální síť musí mít bránu virtuální sítě. Brána virtuální sítě tras a zašifruje provoz.

1. Na stránce **Nové připojení VPN** zaškrtněte políčko **Vytvořit bránu hned**.
2. Klikněte na tlačítko **podsíť, velikost a typ směrování**. Na **konfigurace brány** klikněte na tlačítko **podsítě**.
3. Název podsítě brány je automaticky vyplněno s požadovaným názvem "GatewaySubnet". **Rozsahu adres** obsahuje IP adresy, které jsou přiděleny služby brány VPN. Některé konfigurace povolit podsíť brány /29, ale je nejvhodnější použít pro přizpůsobení budoucí konfigurace, které mohou vyžadovat další IP adresy pro služby brány o velikosti/28 nebo /27. V našem příkladu nastavení použijeme 10.11.1.0/27. Upravit adresní prostor a potom klikněte na **OK**.
4. Konfigurace **velikost brány**. Toto nastavení znamená [skladová položka brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurace **typ směrování**. Směrování zadejte pro tuto konfiguraci musí být **dynamické**. Typ směrování nelze později změnit, pokud přerušit brány a vytvořte novou.
6. Klikněte na **OK**.
7. Na **nové připojení VPN** klikněte na tlačítko **OK** zahajte proces vytváření brány virtuální sítě. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

## <a name="vnet4settings"></a>Krok 5: Konfigurace nastavení virtuální sítě TestVNet4

Opakováním kroků [vytvořte místního webu](#localsite) a [vytvořit bránu virtuální sítě](#gw) konfigurace virtuální sítě TestVNet4, nahraďte hodnoty, pokud je to nezbytné. Pokud to dělají jako cvičení, použijte [ukázkové hodnoty](#vnetvalues).

## <a name="updatelocal"></a>Krok 6 – aktualizovat místní lokality

Po vytvoření vaší brány virtuální sítě pro obě virtuální sítě, je nutné upravit místní lokality **IP adresa brány VPN** hodnoty.

|Název virtuální sítě|Připojená lokalita|IP adresa brány|
|:--- |:--- |:--- |
|Virtuální síť TestVNet1|VNet4Local|IP adresa brány VPN pro virtuální síť TestVNet4|
|Virtuální síť TestVNet4|VNet1Local|IP adresa brány VPN pro virtuální síť TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Část 1 - Get veřejnou IP adresu brány virtuální sítě

1. Najděte virtuální síť na portálu Azure.
2. Kliknutím otevřete síť VNet **přehled** stránky. Na stránce v **připojení k síti VPN**, můžete zobrazit IP adresu pro bránu virtuální sítě.

  ![Veřejná IP adresa](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Zkopírujte adresu IP. Budete ho používat v další části.
4. Opakujte tyto kroky pro virtuální síť TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Část 2 – upravit místní lokality

1. Najděte virtuální síť na portálu Azure.
2. Na síť VNet **přehled** klikněte na místní lokalitě.

  ![Místní web vytvořený pouze](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na **připojení Site-to-Site VPN** klikněte na název místního serveru, který chcete upravit.

  ![Otevřete místní lokality](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klikněte **místní lokality** , kterou chcete upravit.

  ![Upravit web](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Aktualizace **IP adresa brány VPN** a klikněte na tlačítko **OK** uložte nastavení.

  ![IP brány](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zavřete dalších stránek.
7. Opakujte tyto kroky pro virtuální síť TestVNet4.

## <a name="getvalues"></a>Krok 7: načtení hodnoty z konfiguračního souboru sítě

Když vytvoříte virtuální sítě classic na portálu Azure, není název, který zobrazí úplný název, který používáte pro prostředí PowerShell. Například virtuální sítě, který se zobrazí s názvem **virtuální sítě TestVNet1** na portálu, může mít mnoho delší název v souboru konfigurace sítě. Název může vypadat podobně jako: **skupiny ClassicRG virtuální sítě TestVNet1**. Při vytváření připojení, je důležité použít hodnoty, které se zobrazí v konfiguračním souboru na síti.

V následujících krocích se připojit ke svému účtu Azure a stáhnout a zobrazit konfiguračního souboru sítě k získání hodnot, které jsou požadovány pro připojení.

1. Stáhněte a nainstalujte nejnovější verzi rutin prostředí PowerShell Azure Service Management (SM). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

2. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

  ```powershell
  Connect-AzureRmAccount
  ```

  Zkontrolujte předplatná pro příslušný účet.

  ```powershell
  Get-AzureRmSubscription
  ```

  Máte-li více předplatných, vyberte předplatné, které chcete použít.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

  Potom použijte následující rutinu k předplatnému Azure přidat do prostředí PowerShell pro model nasazení classic.

  ```powershell
  Add-AzureAccount
  ```
3. Exportujte a v souboru konfigurace sítě. Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu konfiguračního souboru sítě se exportují do **C:\AzureNet**.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
4. Otevřete soubor v textovém editoru a zobrazení názvů pro virtuální sítě a lokality. Budou to název, který použijete při vytváření připojení.<br>Názvy virtuální sítě jsou uvedeny jako **VirtualNetworkSite name =**<br>Názvy lokalit jsou uvedeny jako **LocalNetworkSiteRef name =**

## <a name="createconnections"></a>Krok 8 – vytvoření brány připojení k síti VPN

Když byly dokončeny všechny předchozí kroky, můžete nastavit, aby předsdíleného klíče protokolu IPsec/IKE a vytvoření připojení. Tato sada kroků používá prostředí PowerShell. Připojení VNet-to-VNet pro model nasazení classic nelze konfigurovat na portálu Azure.

V příkladech Všimněte si, že se sdílený klíč se přesně shodují. Sdílený klíč musí vždy odpovídat. Nezapomeňte nahradit hodnoty v těchto příkladech se přesný názvy virtuálních sítí a místní síťové lokality.

1. Vytvořte připojení virtuální sítě TestVNet1 k virtuální síti TestVNet4.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
  -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
  ```
2. Vytvořte připojení virtuální sítě TestVNet4 k virtuální síti TestVNet1.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
  -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
  ```
3. Počkejte, než pro připojení k chybě při inicializaci. Jakmile se brána byla inicializována, je stav 'bylo úspěšné.

  ```
  Error          :
  HttpStatusCode : OK
  Id             :
  Status         : Successful
  RequestId      :
  StatusCode     : OK
  ```

## <a name="faq"></a>Aspekty VNet-to-VNet pro virtuální sítě classic
* Virtuální sítě může být ve stejné nebo různých předplatných.
* Virtuální sítě se můžou nacházet ve stejné oblasti (umístění) Azure nebo v různých oblastech.
* Cloudová služba ani koncový bod vyrovnávání zatížení nemůžou pracovat nad více virtuálními sítěmi ani v případě, že jsou propojeny.
* Propojováním více virtuálních sítí nevyžaduje žádné zařízení VPN.
* VNet-to-VNet podporují propojování virtuálních sítí Azure. Ji nepodporuje propojování virtuálních počítačů nebo cloudových služeb, které nejsou nasazené virtuální sítě.
* VNet-to-VNet vyžaduje brány s dynamickým směrováním. Azure brány se statickým směrováním nejsou podporovány.
* Možnost připojení k virtuální síti je možné využívat současně se sítěmi VPN s více servery. Je maximálně 10 tunelů VPN pro bránu virtuální sítě VPN, připojení k buď jiné virtuální sítě, nebo místními servery.
* Adresní prostory virtuálních sítí a místních serverů místních sítí se nesmějí překrývat. Překrývající se adresní prostory způsobí, že vytvoření virtuální sítě nebo odesílání netcfg konfigurační soubory k selhání.
* Redundantní tunelová propojení mezi dvěma virtuálními sítěmi nejsou podporována.
* Všechna tunelová propojení sítě VPN pro virtuální síť, včetně P2S VPN, sdílet dostupnou šířku pásma pro bránu sítě VPN a stejné dostupnost brány VPN SLA v Azure.
* Provoz VNet-to-VNet se přenáší přes páteřní strukturu Azure.

## <a name="next-steps"></a>Další postup
Ověřte stav připojení. V tématu [ověření připojení VPN Gateway](vpn-gateway-verify-connection-resource-manager.md).
