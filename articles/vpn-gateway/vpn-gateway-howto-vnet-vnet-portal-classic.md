---
title: 'Vytvoření připojení mezi virtuální sítě: Classic: Azure Portal'
description: Propojení virtuálních sítí Azure s využitím PowerShellu a Azure Portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: bdd27645045195016b7a563787470bf6f2187115
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84985471"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurace připojení typu VNet-to-VNet (Classic)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Tento článek vám pomůže vytvořit připojení brány VPN mezi virtuálními sítěmi. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných. Postup v tomto článku se týká modelu nasazení Classic a Azure Portal. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagram připojení virtuální sítě k virtuální síti](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>Informace o propojeních VNet-to-VNet

Připojení virtuální sítě k jiné virtuální síti (VNet-to-VNet) v modelu nasazení Classic pomocí brány VPN se podobá připojení virtuální sítě k místnímu umístění lokality. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE.

Virtuální sítě, které se připojíte, může být v různých předplatných a různých oblastech. Můžete zkombinovat virtuální síť k síti VNet s konfiguracemi s více lokalitami. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí.

![Připojení VNet-to-VNet](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

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

### <a name="working-with-azure-powershell"></a><a name="powershell"></a>Práce s Azure PowerShell

Portál používáme pro většinu kroků, ale k vytvoření připojení mezi virtuální sítě je potřeba použít PowerShell. Připojení nemůžete vytvořit pomocí Azure Portal. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>Krok 1: Plánování rozsahů IP adres

Je důležité určit rozsahy, které budete používat ke konfiguraci virtuálních sítí. V případě této konfigurace se ujistěte, že se žádný z rozsahů virtuální sítě nepřekrývá mezi sebou nebo pomocí kterékoli z místních sítí, ke kterým se připojují.

Následující tabulka ukazuje příklad definování virtuální sítě. Rozsahy používejte jenom jako základní pravidla. Zapište rozsahy pro virtuální sítě. Tyto informace budete potřebovat pro pozdější kroky.

**Příklad**

| Virtual Network | Adresní prostor | Oblast | Připojí se k místní síťové lokalitě. |
|:--- |:--- |:--- |:--- |
| Virtuální sítě testvnet1 |Virtuální sítě testvnet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| Virtuální sítě testvnet4 |Virtuální sítě testvnet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA – západ |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>Krok 2 – Vytvoření virtuálních sítí

Vytvořte v [Azure Portal](https://portal.azure.com)dvě virtuální sítě. Postup vytvoření klasických virtuálních sítí najdete v tématu [Vytvoření klasické virtuální sítě](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Při použití portálu k vytvoření klasické virtuální sítě musíte přejít na stránku virtuální síť pomocí následujících kroků, jinak se nezobrazí možnost vytvořit klasický virtuální síť:

1. Kliknutím na ' + ' otevřete stránku ' nový '.
2. Do pole Hledat na Marketplace zadejte ' Virtual Network '. Pokud místo toho vyberte síť – > Virtual Network, nebudete mít možnost vytvořit klasickou virtuální síť.
3. Vyhledejte ' Virtual Network ' ze seznamu vrácených a kliknutím na něj otevřete stránku Virtual Network. 
4. Na stránce virtuální síť vyberte Classic a vytvořte klasickou virtuální síť. 

Pokud používáte tento článek jako cvičení, můžete použít následující příklady hodnot:

**Hodnoty pro virtuální sítě testvnet1**

Název: virtuální sítě testvnet1<br>
Adresní prostor: 10.11.0.0/16, 10.12.0.0/16 (volitelné)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.11.0.1/24<br>
Skupina prostředků: ClassicRG<br>
Umístění: USA – východ<br>
GatewaySubnet: 10.11.1.0/27

**Hodnoty pro virtuální sítě testvnet4**

Název: virtuální sítě testvnet4<br>
Adresní prostor: 10.41.0.0/16, 10.42.0.0/16 (volitelné)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.41.0.1/24<br>
Skupina prostředků: ClassicRG<br>
Umístění: USA – západ<br>
GatewaySubnet: 10.41.1.0/27

**Při vytváření virtuální sítě Pamatujte na následující nastavení:**

* **Virtual Network adresních prostorů** – na stránce Virtual Network adresní prostory zadejte rozsah adres, který chcete použít pro virtuální síť. Jedná se o dynamické IP adresy, které se přiřadí virtuálním počítačům a dalším instancím rolí, které nasadíte do této virtuální sítě.<br>Adresní prostory, které vyberete, se nemůžou překrývat s adresními prostory pro žádné jiné virtuální sítě nebo místní lokality, ke kterým se tato síť VNet připojí.

* **Umístění** – při vytváření virtuální sítě ji přidružíte k umístění Azure (oblast). Například pokud chcete, aby byly virtuální počítače, které jsou nasazené do vaší virtuální sítě, fyzicky umístěné v Západní USA, vyberte toto umístění. Po vytvoření už nemůžete změnit umístění přidružené k vaší virtuální síti.

**Po vytvoření virtuální sítě můžete přidat následující nastavení:**

* **Adresní prostor** – pro tuto konfiguraci není potřeba další adresní prostor, ale po vytvoření virtuální sítě můžete přidat další adresní prostor.

* **Podsítě** – pro tuto konfiguraci se nevyžadují další podsítě, ale možná budete chtít mít virtuální počítače v podsíti, která je oddělená od ostatních instancí rolí.

* **DNS servery** – zadejte název a IP adresu serveru DNS. Toto nastavení neslouží k vytvoření serveru DNS. Umožňuje určit server DNS, který chcete použít pro překlad názvů pro tuto virtuální síť.

V této části nakonfigurujete typ připojení, místní lokalitu a vytvoříte bránu.

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>Krok 3 – Konfigurace místní lokality

Azure používá nastavení zadaná v každé místní síťové lokalitě k určení způsobu směrování provozu mezi virtuální sítě. Každá virtuální síť musí ukazovat na příslušnou místní síť, do které chcete směrovat provoz. Určíte název, který chcete použít k odkazování na jednotlivé místní síťové lokality. Je nejvhodnější použít něco popisného.

Například virtuální sítě testvnet1 se připojí k místní síťové lokalitě, kterou vytvoříte s názvem "VNet4Local". Nastavení pro VNet4Local obsahují předpony adres pro virtuální sítě testvnet4.

Místní lokalita pro každou virtuální síť je druhá virtuální síť. Následující příklady hodnot se používají pro naši konfiguraci:

| Virtual Network | Adresní prostor | Oblast | Připojí se k místní síťové lokalitě. |
|:--- |:--- |:--- |:--- |
| Virtuální sítě testvnet1 |Virtuální sítě testvnet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |East US |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| Virtuální sítě testvnet4 |Virtuální sítě testvnet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA – západ |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Vyhledejte virtuální sítě testvnet1 v Azure Portal. V části **připojení VPN** na stránce klikněte na **Brána**.

    ![Žádná brána](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na stránce **nové připojení VPN** vyberte **site-to-site**.
3. Kliknutím na **místní lokalitu** otevřete stránku místní lokality a nakonfigurujte nastavení.
4. Na stránce **místní lokalita** pojmenujte místní lokalitu. V našem příkladu pojmenuje místní lokalitu ' VNet4Local '.
5. Pro **IP adresu brány VPN**můžete použít libovolnou IP adresu, kterou požadujete, pokud je v platném formátu. Obvykle byste použili skutečnou externí IP adresu pro zařízení VPN. Pro klasickou konfiguraci VNet-to-VNet ale použijete veřejnou IP adresu, která je přiřazená bráně pro vaši virtuální síť. Vzhledem k tom, že jste ještě nevytvořili bránu virtuální sítě, zadáte jako zástupný symbol jakoukoli platnou veřejnou IP adresu.<br>Nenechávejte toto pole prázdné – pro tuto konfiguraci není volitelné. V pozdějším kroku se vrátíte do těchto nastavení a nakonfigurujete je s odpovídajícími IP adresami brány virtuální sítě, jakmile ji Azure vygeneruje.
6. Pro **adresní prostor klienta**použijte adresní prostor druhé virtuální sítě. Podívejte se na příklad vašeho plánování. Kliknutím na **OK** uložte nastavení a vraťte se zpátky na stránku **nové připojení k síti VPN** .

    ![místní lokalita](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>Krok 4 – Vytvoření brány virtuální sítě

Každá virtuální síť musí mít bránu virtuální sítě. Brána virtuální sítě směruje a šifruje provoz.

1. Na stránce **Nové připojení VPN** zaškrtněte políčko **Vytvořit bránu hned**.
2. Klikněte na **podsíť, velikost a typ směrování**. Na stránce **Konfigurace brány** klikněte na **podsíť**.
3. Název podsítě brány se vyplní automaticky s požadovaným názvem ' GatewaySubnet '. **Rozsah adres** obsahuje IP adresy, které jsou přiděleny službám brány VPN. Některé konfigurace umožňují podsíť brány/29, ale je vhodné použít a/28 nebo/27, aby vyhovovaly budoucím konfiguracím, které mohou vyžadovat více IP adres pro služby brány. V našem příkladu nastavení používáme 10.11.1.0/27. Upravte adresní prostor a pak klikněte na **OK**.
4. Nakonfigurujte **Velikost brány**. Toto nastavení odkazuje na [SKU brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Nakonfigurujte **typ směrování**. Typ směrování pro tuto konfiguraci musí být **dynamický**. Typ směrování nemůžete později změnit, pokud nechcete bránu odtrhnout a vytvořit novou.
6. Klikněte na **OK**.
7. Na stránce **nové připojení VPN** klikněte na **OK** a začněte vytvářet bránu virtuální sítě. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>Krok 5 – Konfigurace nastavení virtuální sítě testvnet4

Zopakováním kroků [vytvořte místní lokalitu](#localsite) a [vytvořte bránu virtuální sítě](#gw) pro konfiguraci virtuální sítě testvnet4 a v případě potřeby nahraďte hodnoty. Pokud tento postup provádíte jako cvičení, použijte [Ukázkové hodnoty](#vnetvalues).

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>Krok 6 – aktualizace místních lokalit

Až budou brány virtuální sítě vytvořené pro obě virtuální sítě, musíte upravit hodnoty **IP adresy místní lokality brány VPN** .

|Název virtuální sítě|Připojený Web|IP adresa brány|
|:--- |:--- |:--- |
|Virtuální sítě testvnet1|VNet4Local|IP adresa brány VPN pro virtuální sítě testvnet4|
|Virtuální sítě testvnet4|VNet1Local|IP adresa brány VPN pro virtuální sítě testvnet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Část 1 – získání veřejné IP adresy brány virtuální sítě

1. Vyhledejte virtuální síť v Azure Portal.
2. Kliknutím otevřete stránku s **přehledem** virtuální sítě. Na stránce v části **připojení VPN**můžete zobrazit IP adresu brány virtuální sítě.

   ![Veřejná IP adresa](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Zkopírujte IP adresu. Budete ho používat v další části.
4. Opakujte tyto kroky pro virtuální sítě testvnet4

### <a name="part-2---modify-the-local-sites"></a>Část 2 – Změna místních lokalit

1. Vyhledejte virtuální síť v Azure Portal.
2. Na stránce **Přehled** virtuální sítě klikněte na místní lokalitu.

   ![Vytvořila se místní lokalita](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na stránce **připojení VPN typu Site-to-site** klikněte na název místní lokality, kterou chcete upravit.

   ![Otevřít místní lokalitu](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klikněte na **místní lokalitu** , kterou chcete upravit.

   ![Upravit web](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Aktualizujte **IP adresu brány VPN** a kliknutím na **OK** nastavení uložte.

   ![IP adresa brány](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zavřete ostatní stránky.
7. Opakujte tyto kroky pro virtuální sítě testvnet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>Krok 7 – načtení hodnot z konfiguračního souboru sítě

Když v Azure Portal vytvoříte klasický virtuální sítě, název, který si zobrazíte, není úplný název, který používáte pro PowerShell. Například virtuální síť, která se na portálu zdá být pojmenovaná **virtuální sítě testvnet1** , může mít v konfiguračním souboru sítě mnohem delší název. Název může vypadat nějak takto: **Group ClassicRG virtuální sítě testvnet1**. Při vytváření připojení je důležité použít hodnoty, které vidíte v souboru konfigurace sítě.

V následujících krocích se připojíte ke svému účtu Azure a stáhnete a zobrazíte soubor konfigurace sítě, abyste získali hodnoty požadované pro vaše připojení.

1. Stáhněte a nainstalujte si nejnovější verzi rutin PowerShellu pro správu služeb Azure (SM). Další informace najdete v tématu [práce s Azure PowerShell](#powershell).

2. Otevřete konzolu PowerShellu se zvýšenými právy. Použijte následující příklady, které vám pomůžou se připojit. Tyto příkazy musíte spustit místně pomocí modulu pro správu služby PowerShell. Chcete-li přepnout na správu služeb, použijte tento příkaz:

   ```powershell
   azure config mode asm
   ```
3. Připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

   ```powershell
   Add-AzureAccount
   ```
4. Zkontrolujte předplatná pro příslušný účet.

   ```powershell
   Get-AzureSubscription
   ```
5. Máte-li více předplatných, vyberte předplatné, které chcete použít.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
6. Exportujte a zobrazte konfigurační soubor sítě. Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu je konfigurační soubor sítě exportován do **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Otevřete soubor pomocí textového editoru a zobrazte názvy pro virtuální sítě a weby. Tyto názvy budou názvy, které použijete při vytváření připojení.<br>Názvy virtuální sítě jsou uvedené jako **VirtualNetworkSite název =**<br>Názvy webů jsou uvedené jako **LocalNetworkSiteRef název =**

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>Krok 8 – vytvoření připojení brány VPN Gateway

Po dokončení všech předchozích kroků můžete nastavit předsdílené klíče IPsec/IKE a vytvořit připojení. Tato sada kroků používá PowerShell. Připojení typu VNet-to-VNet pro model nasazení Classic nelze konfigurovat v Azure Portal.

V příkladech si všimněte, že je sdílený klíč přesně stejný. Sdílený klíč musí vždy odpovídat. Nezapomeňte hodnoty v těchto příkladech nahradit přesnými názvy pro virtuální sítě a místní síťové lokality.

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
3. Počkejte na inicializaci připojení. Po inicializaci brány se jedná o stav "úspěch".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>Požadavky VNet-to-VNet pro klasický virtuální sítě
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
