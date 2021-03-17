---
title: 'Vytvoření připojení mezi virtuální sítě: Classic: Azure Portal'
description: Propojení virtuálních sítí Azure s využitím PowerShellu a Azure Portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103216"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurace připojení typu VNet-to-VNet (Classic)

Tento článek vám pomůže vytvořit připojení brány VPN mezi virtuálními sítěmi. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="Diagram znázorňující klasickou architekturu VNet-to-VNet":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Postup v tomto článku se týká modelu nasazení Classic a Azure Portal. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Klasický](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Připojení virtuální sítě v různých modelech nasazení](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>Informace o propojeních VNet-to-VNet

Připojení virtuální sítě k jiné virtuální síti (VNet-to-VNet) v modelu nasazení Classic pomocí brány VPN se podobá připojení virtuální sítě k místnímu umístění lokality. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE.

Virtuální sítě, které se připojíte, může být v různých předplatných a různých oblastech. Můžete zkombinovat virtuální síť k síti VNet s konfiguracemi s více lokalitami. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="Diagram znázorňující klasickou architekturu VNet-to-VNet":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Proč propojovat virtuální sítě?

Virtuální sítě může být vhodné propojit z následujících důvodů:

* **Geografická redundance a geografická přítomnost mezi oblastmi**

  * Můžete nastavit vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením bez procházení koncovými body připojenými k internetu.
  * Díky Azure Load Balancer a technologii clusteringu od Microsoftu nebo třetích stran můžete nastavit úlohu s vysokou dostupností s geografickou redundancí napříč několika oblastmi Azure. Jedním z důležitých příkladů je nastavení technologie SQL Always On se skupinami dostupnosti nad několika oblastmi Azure.
* **Regionální vícevrstvé aplikace s hranici silné izolace**

  * V rámci stejné oblasti můžete nastavit vícevrstvé aplikace s několika virtuální sítěy propojenými společně se silnou izolací a zabezpečenou mezivrstvou komunikace.
* **Vzájemné předplatné, komunikace mezi organizacemi v Azure**

  * Pokud máte více předplatných Azure, můžete mezi virtuálními sítěmi bezpečně propojit úlohy z různých předplatných.
  * Pro podniky nebo poskytovatele služeb můžete povolit komunikaci mezi organizacemi pomocí zabezpečené technologie VPN v rámci Azure.

Další informace o propojeních VNet-to-VNet najdete v části [Aspekty propojení VNet-to-VNet](#faq) na konci tohoto článku.

## <a name="prerequisites"></a>Předpoklady

Portál používáme pro většinu kroků, ale k vytvoření připojení mezi virtuální sítě je potřeba použít PowerShell. Nemůžete vytvořit připojení pomocí Azure Portal, protože na portálu neexistuje žádný způsob, jak zadat sdílený klíč. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>Plánování

Je důležité určit rozsahy, které budete používat ke konfiguraci virtuálních sítí. V případě této konfigurace se ujistěte, že se žádný z rozsahů virtuální sítě nepřekrývá mezi sebou nebo pomocí kterékoli z místních sítí, ke kterým se připojují.

### <a name="vnets"></a><a name="vnet"></a>Virtuální sítě

Pro toto cvičení používáme následující příklady hodnot:

**Hodnoty pro virtuální sítě testvnet1**

Název: virtuální sítě testvnet1<br>
Adresní prostor: 10.11.0.0/16, 10.12.0.0/16 (volitelné)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.11.0.0/24<br>
Skupina prostředků: ClassicRG<br>
Umístění: USA – východ<br>
GatewaySubnet: 10.11.1.0/27

**Hodnoty pro virtuální sítě testvnet4**

Název: virtuální sítě testvnet4<br>
Adresní prostor: 10.41.0.0/16, 10.42.0.0/16 (volitelné)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.41.0.0/24<br>
Skupina prostředků: ClassicRG<br>
Umístění: USA – západ<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>Připojení

V následující tabulce je uveden příklad, jak budete připojovat své virtuální sítě. Rozsahy používejte jenom jako základní pravidla. Zapište rozsahy pro virtuální sítě. Tyto informace budete potřebovat pro pozdější kroky.

V tomto příkladu se virtuální sítě testvnet1 připojuje k místní síťové lokalitě, kterou vytvoříte s názvem "VNet4Local". Nastavení pro VNet4Local obsahují předpony adres pro virtuální sítě testvnet4.
Místní lokalita pro každou virtuální síť je druhá virtuální síť. Následující příklady hodnot se používají pro naši konfiguraci:

**Příklad**

| Virtual Network | Adresní prostor | Umístění | Připojí se k místní síťové lokalitě. |
|:--- |:--- |:--- |:--- |
| Virtuální sítě testvnet1 |Virtuální sítě testvnet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| Virtuální sítě testvnet4 |Virtuální sítě testvnet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA – západ |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>Vytvoření virtuálních sítí

V tomto kroku vytvoříte dvě klasické virtuální sítě, virtuální sítě testvnet1 a virtuální sítě testvnet4. Pokud používáte tento článek jako cvičení, použijte [příklady hodnot](#vnet).

**Při vytváření virtuální sítě Pamatujte na následující nastavení:**

* **Virtual Network adresních prostorů** – na stránce Virtual Network adresní prostory zadejte rozsah adres, který chcete použít pro virtuální síť. Jedná se o dynamické IP adresy, které se přiřadí virtuálním počítačům a dalším instancím rolí, které nasadíte do této virtuální sítě.<br>Adresní prostory, které vyberete, se nemůžou překrývat s adresními prostory pro žádné jiné virtuální sítě nebo místní lokality, ke kterým se tato síť VNet připojí.

* **Umístění** – při vytváření virtuální sítě ji přidružíte k umístění Azure (oblast). Například pokud chcete, aby byly virtuální počítače, které jsou nasazené do vaší virtuální sítě, fyzicky umístěné v Západní USA, vyberte toto umístění. Po vytvoření už nemůžete změnit umístění přidružené k vaší virtuální síti.

**Po vytvoření virtuální sítě můžete přidat následující nastavení:**

* **Adresní prostor** – pro tuto konfiguraci není potřeba další adresní prostor, ale po vytvoření virtuální sítě můžete přidat další adresní prostor.

* **Podsítě** – pro tuto konfiguraci se nevyžadují další podsítě, ale možná budete chtít mít virtuální počítače v podsíti, která je oddělená od ostatních instancí rolí.

* **DNS servery** – zadejte název a IP adresu serveru DNS. Toto nastavení neslouží k vytvoření serveru DNS. Umožňuje určit server DNS, který chcete použít pro překlad názvů pro tuto virtuální síť.

### <a name="to-create-a-classic-virtual-network"></a>Vytvoření klasické virtuální sítě

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>Konfigurace lokalit a bran

Azure používá nastavení zadaná v každé místní síťové lokalitě k určení způsobu směrování provozu mezi virtuální sítě. Každá virtuální síť musí ukazovat na příslušnou místní síť, do které chcete směrovat provoz. Určíte název, který chcete použít k odkazování na jednotlivé místní síťové lokality. Je nejvhodnější použít něco popisného.

Například virtuální sítě testvnet1 se připojí k místní síťové lokalitě, kterou vytvoříte s názvem "VNet4Local". Nastavení pro VNet4Local obsahují předpony adres pro virtuální sítě testvnet4.

Mějte na paměti, že místní lokalita pro každou virtuální síť je druhá virtuální síť.

| Virtual Network | Adresní prostor | Umístění | Připojí se k místní síťové lokalitě. |
|:--- |:--- |:--- |:--- |
| Virtuální sítě testvnet1 |Virtuální sítě testvnet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| Virtuální sítě testvnet4 |Virtuální sítě testvnet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA – západ |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>Konfigurace lokality

Místní lokalita obvykle odkazuje na vaše místní umístění. Obsahuje IP adresu zařízení VPN, ke kterému vytvoříte připojení, a rozsah IP adres, které budou do tohoto zařízení VPN směrovány přes bránu VPN.

1. Na stránce pro virtuální síť v části **Nastavení**vyberte **připojení Site-to-site**.
1. Na stránce připojení Site-to-site vyberte **+ Přidat**.
1. Na stránce **Konfigurovat připojení k síti VPN a bránu** pro **Typ připojení**ponechte vybranou možnost **site-to-site** .

   * **IP adresa brány VPN:** Toto je veřejná IP adresa zařízení VPN pro vaši místní síť. Pro toto cvičení můžete umístit zástupnou adresu, protože ještě nemáte IP adresu pro bránu VPN pro druhý Web. Například 5.4.3.2. Později, až nakonfigurujete bránu pro jinou virtuální síť, můžete tuto hodnotu upravit.

   * **Adresní prostor klienta:** Vypíše rozsahy IP adres, které chcete směrovat do jiné virtuální sítě prostřednictvím této brány. Můžete přidat více různých rozsahů adres. Ujistěte se, že se zde zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se vaše virtuální síť připojuje, nebo s rozsahy adres samotné virtuální sítě.
1. V dolní části stránky nevybírejte možnost zkontrolovat + vytvořit. Místo toho vyberte **Další:>brány **.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>Konfigurace brány virtuální sítě

1. Na stránce **Brána** vyberte následující hodnoty:

   * **Velikost:** Toto je SKU brány, které použijete k vytvoření brány virtuální sítě. Brány VPN Classic používají staré (starší) skladové položky brány. Další informace o starších skladových položkách brány najdete v tématu [Práce se skladovými položkami bran virtuálních sítí (staré skladové položky)](vpn-gateway-about-skus-legacy.md). Pro toto cvičení můžete vybrat **standardní** .

   * **Typ směrování:** Vyberte typ směrování pro bránu. Označuje se také jako typ sítě VPN. Je důležité vybrat správný typ, protože bránu nemůžete převést z jednoho typu na jiný. Zařízení VPN musí být kompatibilní s typem směrování, který zvolíte. Další informace o typu směrování najdete v tématu [informace o nastaveních VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Můžete narazit na články, které odkazují na typy sítě VPN RouteBased a PolicyBased. Typ Dynamická odpovídá RouteBased a Statická odpovídá PolicyBased. Pro tuto konfiguraci vyberte **Dynamická**.

   * **Podsíť brány:** Velikost podsítě brány, kterou určíte, závisí na konfiguraci brány VPN, kterou chcete vytvořit. Přestože je možné vytvořit tak malou podsíť brány, jako je /29, doporučujeme použít /27 nebo /28. Vytvoří se tak vetší podsíť zahrnující více adres. Použitím větší podsítě brány zajistíte dostatek IP adres pro případné další konfigurace.

1. V dolní části stránky vyberte **zkontrolovat + vytvořit** a ověřte nastavení. Vyberte **vytvořit** k nasazení. Vytvoření brány virtuální sítě může trvat až 45 minut v závislosti na vybrané skladové jednotce brány.
1. Při vytváření této brány můžete začít pokračovat k dalšímu kroku.

### <a name="configure-testvnet4-settings"></a>Konfigurace nastavení virtuální sítě testvnet4

Opakujte postup pro [vytvoření lokality a brány](#localsite) pro konfiguraci virtuální sítě testvnet4 a nahrazování hodnot v případě potřeby. Pokud tento postup provádíte jako cvičení, použijte [Ukázkové hodnoty](#planning).

## <a name="update-local-sites"></a><a name="updatelocal"></a>Aktualizace místních lokalit

Až budou brány virtuální sítě vytvořené pro obě virtuální sítě, musíte upravit vlastnosti místní lokality pro **IP adresu brány VPN**.

|Název virtuální sítě|Připojený Web|IP adresa brány|
|:--- |:--- |:--- |
|Virtuální sítě testvnet1|VNet4Local|IP adresa brány VPN pro virtuální sítě testvnet4|
|Virtuální sítě testvnet4|VNet1Local|IP adresa brány VPN pro virtuální sítě testvnet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Část 1 – získání veřejné IP adresy brány virtuální sítě

1. Přejděte do této virtuální sítě tak, že přejdete do **skupiny prostředků** a vyberete virtuální síť.
1. Na stránce pro virtuální síť v podokně **základy** na pravé straně vyhledejte **IP adresu brány** a zkopírujte do schránky.

### <a name="part-2---modify-the-local-site-properties"></a>Část 2 – Změna vlastností místní lokality

1. V části připojení Site-to-site vyberte připojení. Například SiteVNet4.
1. Na stránce **vlastnosti** pro připojení Site-to-site vyberte **upravit místní lokalitu**.
1. Do pole **IP adresa brány VPN** vložte IP adresu brány VPN, kterou jste zkopírovali v předchozí části.
1. Vyberte **OK**.
1. Pole je aktualizováno v systému. Tuto metodu můžete také použít k přidání další IP adresy, kterou chcete směrovat na tento webový server.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>Část 3 – opakování kroků pro druhou virtuální síť

Opakujte kroky pro virtuální sítě testvnet4.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>Načtení hodnot konfigurace

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>Vytvoření připojení

Po dokončení všech předchozích kroků můžete nastavit předsdílené klíče IPsec/IKE a vytvořit připojení. Tato sada kroků používá PowerShell. Připojení typu VNet-to-VNet pro model nasazení Classic Nejde nakonfigurovat v Azure Portal, protože sdílený klíč nejde zadat na portálu.

V příkladech si všimněte, že je sdílený klíč přesně stejný. Sdílený klíč musí vždy odpovídat. Nezapomeňte hodnoty v těchto příkladech nahradit přesnými názvy pro virtuální sítě a místní síťové lokality.

1. Vytvořte připojení virtuální sítě TestVNet1 k virtuální síti TestVNet4. Ujistěte se, že jste hodnoty změnili.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. Vytvořte připojení virtuální sítě TestVNet4 k virtuální síti TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. Počkejte na inicializaci připojení. Po inicializaci brány se jedná o stav "úspěch".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>Nejčastější dotazy a požadavky

Tyto požadavky platí pro klasické virtuální sítě a brány klasických virtuálních sítí.

* Virtuální sítě mohou být ve stejném nebo jiném předplatném.
* Virtuální sítě se můžou nacházet ve stejné oblasti (umístění) Azure nebo v různých oblastech.
* Cloudová služba nebo koncový bod pro vyrovnávání zatížení nemůžou být napříč virtuálními sítěmi, i když se vzájemně spojí.
* Propojení více virtuálních sítí dohromady nevyžaduje žádná zařízení VPN.
* VNet-to-VNet podporuje propojení virtuálních sítí Azure. Nepodporuje připojení virtuálních počítačů ani cloudových služeb, které nejsou nasazené do virtuální sítě.
* VNet-to-VNet vyžaduje brány dynamického směrování. Brány statického směrování Azure se nepodporují.
* Možnost připojení k virtuální síti je možné využívat současně se sítěmi VPN s více servery. Pro bránu VPN virtuální sítě je k dispozici maximálně 10 tunelů VPN, které se připojují k jiným virtuálním sítím nebo místním webům.
* Adresní prostory virtuálních sítí a místních serverů místních sítí se nesmějí překrývat. Překrývající se adresní prostory způsobí, že vytváření virtuálních sítí nebo nahrání konfiguračních souborů nástroje Netcfg selže.
* Redundantní tunelová propojení mezi dvěma virtuálními sítěmi nejsou podporována.
* Všechny tunely VPN pro virtuální síť, včetně VPN P2S, sdílejí dostupnou šířku pásma pro bránu VPN a stejnou smlouvu SLA služby VPN Gateway v Azure.
* Provoz VNet-to-VNet prochází přes páteřní síť Azure.

## <a name="next-steps"></a>Další kroky

Ověřte stav připojení. Viz [ověření VPN Gatewayho připojení](vpn-gateway-verify-connection-resource-manager.md).
