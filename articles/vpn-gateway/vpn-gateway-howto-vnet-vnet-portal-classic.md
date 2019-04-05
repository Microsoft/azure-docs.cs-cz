---
title: 'Vytvořte připojení mezi virtuálními sítěmi: classic: Azure portal | Dokumentace Microsoftu'
description: Připojení virtuálních sítí Azure pomocí Powershellu a webu Azure portal.
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
ms.openlocfilehash: e323a8d71bbffd1d29ad793dff7b5b4a072b6979
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046118"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurace připojení typu VNet-to-VNet (classic)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Tento článek vám pomůže vytvořit připojení brány VPN mezi virtuálními sítěmi. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných. Postup v tomto článku se vztahuje k modelu nasazení classic a webu Azure portal. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [portál Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure portal (classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagram připojení k virtuální síti se virtuální síť](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-vnet-to-vnet-connections"></a>Informace o propojeních VNet-to-VNet

Propojení virtuální sítě s jinou virtuální sítí (VNet-to-VNet) v modelu nasazení classic pomocí VPN gateway je podobné propojení virtuální sítě s místním serverem. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE.

Virtuální sítě, ke kterým se připojujete, může být v různých předplatných a různých oblastech. Připojení typu VNet můžete kombinovat komunikaci VNet s konfiguracemi Multi-Site. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí.

![Připojení mezi virtuálními sítěmi](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Proč propojovat virtuální sítě?

Virtuální sítě může být vhodné propojit z následujících důvodů:

* **Geografická redundance a geografická přítomnost mezi oblastmi**

  * Můžete nastavit vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením bez procházení koncovými body připojenými k internetu.
  * Azure Load Balancer a Microsoft nebo třetích stran technologie clusteringu můžete nastavit úlohy s vysokou dostupností s geografickou redundancí nad několika oblastmi Azure. Jedním z důležitých příkladů je nastavení technologie SQL Always On se skupinami dostupnosti nad několika oblastmi Azure.
* **Regionální vícevrstvé aplikace s silné oddělovací hranice**

  * V rámci stejné oblasti můžete nastavit vícevrstvé aplikace s více virtuálními sítěmi připojené společně s silnou izolaci a zabezpečenou komunikaci mezi vrstvy.
* **Různé předplatného, komunikace mezi organizace v Azure**

  * Pokud máte více předplatných Azure, můžete se připojit úlohy z různých předplatných společně bezpečně mezi virtuálními sítěmi.
  * Pro podniky a poskytovatelé služeb můžete povolit komunikaci mezi organizacemi pomocí zabezpečené sítě VPN technologii v Azure.

Další informace o propojeních VNet-to-VNet najdete v části [Aspekty propojení VNet-to-VNet](#faq) na konci tohoto článku.

### <a name="before-you-begin"></a>Před zahájením

Před zahájením tohoto cvičení, stáhněte a nainstalujte nejnovější verzi rutin Powershellu pro Azure Service Management (SM). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Používáme pro většinu kroků na portálu, ale musíte použít PowerShell k vytvoření připojení mezi virtuálními sítěmi. Nelze vytvořit připojení pomocí webu Azure portal.

## <a name="plan"></a>Krok 1: Plánování rozsahů IP adres

Je důležité určit rozsahy, které budete používat ke konfiguraci virtuální sítě. Pro tuto konfiguraci Ujistěte se, že překrývat žádné z rozsahů virtuálních sítí mezi sebou, případně s některým z místní sítě, které se připojují.

Následující tabulka ukazuje příklad toho, jak definovat virtuální sítě. Používejte jako vodítko pouze oblasti. Zapište si oblastí pro vaše virtuální sítě. Tyto informace budete potřebovat pro pozdější kroky.

**Příklad:**

| Virtual Network | Adresní prostor | Oblast | Se připojí k místní síťové lokality |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA – východ |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Západní USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Krok 2: vytvoření virtuální sítě

Vytvoření dvou virtuálních sítí v [webu Azure portal](https://portal.azure.com). Postup vytvoření klasických virtuálních sítích najdete v tématu [vytvořit klasickou virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Při použití portálu k vytvoření klasickou virtuální síť, je nutné přejít na stránku pro virtuální sítě pomocí následujících kroků, jinak se nezobrazí možnost vytvořit klasickou virtuální síť:

1. Klikněte '+' otevřete stránku 'New'.
2. V poli na marketplace vyhledejte text "Virtuální síť". Pokud místo toho vyberte síť -> virtuální síť, nebude mít možnost vytvořit klasickou virtuální síť.
3. Vyhledejte "Virtuální síť" z vráceném seznamu a klikněte na něj a otevřete stránku virtuální sítě. 
4. Na stránce virtuální sítě vyberte možnost "Klasickém" vytvořit klasickou virtuální síť. 

Pokud používáte tento článek jako cvičení, můžete použít následující ukázkové hodnoty:

**Hodnoty pro virtuální síť TestVNet1**

Název: TestVNet1<br>
Adresní prostor: 10.11.0.0/16 10.12.0.0/16 (volitelné)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.11.0.1/24<br>
Skupina zdrojů: ClassicRG<br>
Umístění: USA – východ<br>
GatewaySubnet: 10.11.1.0/27

**Hodnoty pro virtuální síť TestVNet4**

Název: TestVNet4<br>
Adresní prostor: 10.41.0.0/16 10.42.0.0/16 (volitelné)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.41.0.1/24<br>
Skupina zdrojů: ClassicRG<br>
Umístění: Západní USA<br>
GatewaySubnet: 10.41.1.0/27

**Při vytváření virtuální sítě, mějte na paměti následující nastavení:**

* **Adresní prostory virtuální sítě** – na stránce adresní prostory virtuální sítě adres zadejte rozsah adres, který chcete použít pro vaši virtuální síť. Toto jsou dynamické IP adresy, které se přiřadí virtuální počítače a dalších instancí rolí, které nasadíte do této virtuální sítě.<br>S adresními prostory pro některý z jiných virtuálních sítí nebo v místním umístění, která tuto virtuální síť se připojí k nemůže překrývat adresní prostory, které vyberete.

* **Umístění** – při vytvoření virtuální sítě, přiřaďte ji k umístění (oblast) služby Azure. Pokud chcete virtuální počítače, které jsou nasazeny do vaší virtuální sítě byly fyzicky umístěné v oblasti západní USA, vyberte toto umístění. Umístění přidružené k vaší virtuální sítě po vytvoření nelze změnit.

**Po vytvoření virtuální sítě, můžete přidat následující nastavení:**

* **Adresní prostor** – další adresní prostor se nevyžaduje pro tuto konfiguraci, ale po vytvoření virtuální sítě můžete přidat další adresní prostor.

* **Podsítě** – dodatečné podsítě nejsou vyžadovány pro tuto konfiguraci, ale můžete chtít mít virtuální počítače v podsíti oddělené od dalších instancí rolí.

* **Servery DNS** – zadejte název serveru DNS a IP adresu. Toto nastavení neslouží k vytvoření serveru DNS. Umožňuje určit server DNS, který chcete použít pro překlad názvů pro tuto virtuální síť.

V této části nakonfigurujete typ připojení webu místní a vytvořte bránu.

## <a name="localsite"></a>Krok 3: Konfigurace místní lokality

Azure používá nastavení uvedená v každé lokalitě místní sítě k určení, jak směrovat provoz mezi virtuálními sítěmi. Každá virtuální síť musí odkazovat na příslušné místní sítě, které chcete směrovat provoz do. Určíte název, který chcete použít k odkazování na každé lokalitě místní sítě. Je nejvhodnější použít něco popisného.

Například virtuální síť TestVNet1 připojí k místní síťovou lokalitu, kterou vytvoříte s názvem "VNet4Local". Nastavení pro VNet4Local obsahovat předpony adres pro virtuální síť TestVNet4.

Místní lokalita pro každou virtuální síť je druhá virtuální síť. Následující ukázkové hodnoty se používají pro naše konfigurace:

| Virtual Network | Adresní prostor | Oblast | Se připojí k místní síťové lokality |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA – východ |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Západní USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Na webu Azure Portal vyhledejte virtuální síť TestVNet1. V **připojení k síti VPN** části stránky klikněte na tlačítko **brány**.

    ![Žádná brána](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na **nové připojení VPN** stránce **Site-to-Site**.
3. Klikněte na tlačítko **místní lokalita** k otevření stránky místní lokalitě a nakonfigurujte nastavení.
4. Na **místní lokalita** stránky, zadejte název vaší místní lokalitě. V našem příkladu používáme název místní lokality "VNet4Local".
5. Pro **IP adresu brány VPN**, IP adresu, která chcete, můžete použít, dokud je v platném formátu. Obvykle použijete skutečná externí IP adresu zařízení VPN. Ale pro klasické konfigurace připojení typu VNet-to-VNet, použijte veřejnou IP adresu, která je přiřazena brány pro vaši virtuální síť. Vzhledem k tomu, že dosud jste vytvořili bránu virtuální sítě, zadáte všechny platnou veřejnou IP adresu jako zástupný symbol.<br>Není toto pole nechat prázdné – není pro tuto konfiguraci volitelný. V pozdějším kroku vraťte se do těchto nastavení a konfigurace pomocí odpovídající IP adresy brány virtuální sítě, jakmile ho generuje Azure.
6. Pro **klientský adresní prostor**, použijte adresní prostor z druhé virtuální sítě. Odkazovat na plánování příklad. Klikněte na tlačítko **OK** uložte nastavení a vrátit zpět **nové připojení VPN** stránky.

    ![místní lokalita](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Krok 4 – vytvoření brány virtuální sítě

Každá virtuální síť musí mít bránu virtuální sítě. Brána virtuální sítě trasy a šifruje provoz.

1. Na stránce **Nové připojení VPN** zaškrtněte políčko **Vytvořit bránu hned**.
2. Klikněte na tlačítko **podsíť, velikost a typ směrování**. Na **konfigurace brány** klikněte na **podsítě**.
3. Název podsítě brány je automaticky vyplněno s požadovaným názvem "GatewaySubnet". **Rozsah adres** obsahuje IP adresy, které jsou přiděleny služba brány VPN. Některé konfigurace povolit podsíť brány s minimální velikostí/29, ale je nejvhodnější použít pro případné budoucí konfigurace, které mohou vyžadovat další IP adresy pro služby brány o velikosti/28 nebo/27. V našem příkladu nastavení použijeme 10.11.1.0/27. Upravte adresní prostor a pak klikněte na **OK**.
4. Konfigurace **velikost brány**. Toto nastavení znamená [skladové jednotce brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurace **typ směrování**. Směrování typu pro tuto konfiguraci musí být **dynamické**. Typ směrování nelze později změnit, pokud dovolí bránu a vytvořte novou.
6. Klikněte na **OK**.
7. Na **nové připojení VPN** klikněte na **OK** zahajte proces vytváření brány virtuální sítě. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

## <a name="vnet4settings"></a>Krok 5: Konfigurace nastavení virtuální sítě TestVNet4

Zopakováním těchto kroků [vytvořit místní lokalita](#localsite) a [vytvořit bránu virtuální sítě](#gw) konfigurace virtuální sítě TestVNet4, nahraďte hodnoty, pokud je to nezbytné. Pokud se to jako cvičení, použijte [ukázkové hodnoty](#vnetvalues).

## <a name="updatelocal"></a>Krok 6: aktualizujte místní lokality

Po vytvoření vaší brány virtuální sítě pro obě virtuální sítě, je nutné upravit místní lokality **IP adresu brány VPN** hodnoty.

|Název virtuální sítě|Připojené lokality|IP adresa brány|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|IP adresa brány VPN pro virtuální síť TestVNet4|
|TestVNet4|VNet1Local|IP adresa brány VPN pro virtuální síť TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Část 1 – získejte veřejnou IP adresu brány virtuální sítě

1. Vyhledejte virtuální sítě na webu Azure Portal.
2. Kliknutím otevřete virtuální síť **přehled** stránky. Na stránce v **připojení k síti VPN**, můžete zobrazit IP adresu brány virtuální sítě.

   ![Veřejná IP adresa](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Zkopírujte IP adresu. Použijete ho v další části.
4. Tento postup opakujte pro virtuální síť TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Část 2 – upravit místní lokality

1. Vyhledejte virtuální sítě na webu Azure Portal.
2. Ve virtuální síti **přehled** stránky, klikněte na místní lokalitu.

   ![Místní web vytvořený pouze](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na **připojení Site-to-Site VPN** klikněte na název místní lokality, kterou chcete upravit.

   ![Otevřít místní web](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klikněte na tlačítko **místní lokalita** , kterou chcete upravit.

   ![Upravit web](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Aktualizace **IP adresu brány VPN** a klikněte na tlačítko **OK** uložte nastavení.

   ![Brána IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zavřete na stránkách.
7. Tento postup opakujte pro virtuální síť TestVNet4.

## <a name="getvalues"></a>Krok 7: načtení hodnoty z konfiguračního souboru sítě

Při vytváření klasických virtuálních sítích na webu Azure Portal, které můžete zobrazit název není úplný název, který používáte pro prostředí PowerShell. Například virtuální síť, která se zobrazí s názvem **virtuální sítě TestVNet1** na portálu může mít mnohem déle, název v konfiguračním souboru sítě. Název může vypadat podobně jako: **Seskupit virtuální sítě ClassicRG TestVNet1**. Když vytvoříte připojení, je potřeba použít hodnoty, které se zobrazí v konfiguračním souboru sítě.

V následujících krocích se připojit ke svému účtu Azure a stáhnout a zobrazit soubor konfigurace sítě a získat tak hodnoty, které jsou požadovány pro připojení.

1. Stáhněte si a nainstalujte nejnovější verzi rutin Powershellu pro Azure Service Management (SM). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

2. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

   ```powershell
   Connect-AzAccount
   ```

   Zkontrolujte předplatná pro příslušný účet.

   ```powershell
   Get-AzSubscription
   ```

   Máte-li více předplatných, vyberte předplatné, které chcete použít.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

   V dalším kroku použijte následující rutinu k vašemu předplatnému Azure přidat do prostředí PowerShell pro model nasazení classic.

   ```powershell
   Add-AzureAccount
   ```
3. Vyexportovat a zobrazit soubor konfigurace sítě. Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu se soubor konfigurace sítě exportuje do **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
4. V textovém editoru otevřete soubor a vidět názvy pro vaše virtuální sítě a serverů. Toto bude název, který používáte při vytváření připojení.<br>Názvy virtuální sítě jsou uvedené jako **VirtualNetworkSite name =**<br>Názvy lokalit jsou uvedeny jako **LocalNetworkSiteRef název =**

## <a name="createconnections"></a>Krok 8: vytvoření připojení brány VPN

Po dokončení předchozích kroků můžete nastavit předsdílené klíče protokolu IPsec/IKE a vytvořte připojení. Tuto sadu kroků používá PowerShell. Připojení VNet-to-VNet pro model nasazení classic nelze nastavit na webu Azure Portal.

V příkladech Všimněte si, že sdílený klíč je přesně tatáž. Sdílený klíč musí vždy odpovídat. Nezapomeňte nahradit hodnoty v těchto příkladech přesné názvy virtuálních sítí a místní síťové lokality.

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
3. Počkejte připojení k inicializaci. Jakmile se brána byla inicializována, je ve stavu "Úspěšný".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>Důležité informace o připojení typu VNet-to-VNet pro klasické virtuální sítě
* Virtuální sítě může být v jednom nebo několika předplatných.
* Virtuální sítě se můžou nacházet ve stejné oblasti (umístění) Azure nebo v různých oblastech.
* Cloudová služba ani koncový bod vyrovnávání zatížení nemůžou pracovat nad více virtuálními sítěmi ani v případě, že jsou propojeny.
* Propojení více virtuálních sítí, nevyžaduje se žádná zařízení VPN.
* Připojení typu VNet-to-VNet podporuje propojování virtuálních sítí Azure. Nepodporuje propojování virtuálních počítačů ani cloudových služeb, které nejsou nasazeni ve virtuální síti.
* Připojení typu VNet-to-VNet vyžaduje brány s dynamickým směrováním. Brány statického směrování Azure nejsou podporovány.
* Možnost připojení k virtuální síti je možné využívat současně se sítěmi VPN s více servery. Je maximálně 10 tunelů VPN pro bránu VPN virtuální sítě, připojení k jiné virtuální sítě, nebo místními umístěními.
* Adresní prostory virtuálních sítí a místních serverů místních sítí se nesmějí překrývat. Překrývající se adresní prostory, způsobí vytvoření virtuální sítě nebo ukládání konfiguračních souborů netcfg selhání.
* Redundantní tunelová propojení mezi dvěma virtuálními sítěmi nejsou podporována.
* Pro virtuální síť, včetně sítí VPN P2S, všechny tunely VPN sdílejí dostupnou šířku pásma pro VPN gateway a stejné dostupnost brány VPN smlouvy SLA v Azure.
* Provoz VNet-to-VNet se přenáší prostřednictvím páteřní síť Azure.

## <a name="next-steps"></a>Další postup
Ověřte stav připojení. Zobrazit [ověření připojení brány VPN](vpn-gateway-verify-connection-resource-manager.md).
