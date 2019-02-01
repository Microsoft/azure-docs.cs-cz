---
title: 'Připojení klasických virtuálních sítí k virtuálním sítím Azure Resource Manageru: Portál | Dokumentace Microsoftu'
description: Postup připojení klasických virtuálních sítí k virtuálním sítím Resource Manageru pomocí VPN Gateway a na portálu
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: 4d2edeaf7423d3a46becf386294d2dd8c46e9ab7
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508330"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Připojení virtuálních sítí z různých modelů nasazení pomocí portálu

Tento článek ukazuje, jak připojení klasických virtuálních sítí k virtuálním sítím Resource Manageru tak, aby prostředky umístěné v samostatné nasazení modelů komunikovat mezi sebou. Kroky v tomto článku použijte primárně na webu Azure portal, ale můžete také vytvořit tuto konfiguraci pomocí prostředí PowerShell tak, že vyberete článek z tohoto seznamu.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Připojení klasické virtuální sítě k virtuální síti správce prostředků je podobné propojení virtuální sítě s místním serverem. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Můžete vytvořit propojení virtuálních sítí patřících do různých předplatných a v různých oblastech. Můžete také připojit virtuální sítě, které už máte připojení k místním sítím, jako je brána, které byly nakonfigurovány pomocí dynamické nebo založené na směrování. Další informace o propojeních VNet-to-VNet najdete v části [Nejčastější dotazy týkající se propojení VNet-to-VNet](#faq) na konci tohoto článku. 

Pokud ještě nemáte bránu virtuální sítě a nechcete, aby si ji vytvořit, můžete místo toho zvážit připojení vašich virtuálních sítí pomocí VNet Peering. Partnerské vztahy virtuálních sítí nepoužívají bránu VPN. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

### <a name="before"></a>Než začnete

* Tento postup předpokládá, že jste již vytvořili obou virtuálních sítích. Pokud používáte v tomto článku jako cvičení a nemáte virtuální sítě, existují odkazy v kroky k jejich vytváření.
* Ověřte, zda rozsahy adres pro virtuální sítě není překrývat mezi sebou, nebo se překrývají s žádným z rozsahů pro další připojení, které brány může být připojen k.
* Nainstalujte nejnovější rutiny Powershellu pro Resource Manager a správa služeb (klasické). V tomto článku používáme webu Azure portal a PowerShell. Prostředí PowerShell se vyžaduje k vytvoření připojení z klasické virtuální sítě k virtuální síti správce prostředků. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). 

### <a name="values"></a>Příklady nastavení

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

**Klasické virtuální sítě**

Název virtuální sítě = ClassicVNet <br>
Adresní prostor = 10.0.0.0/24 <br>
Název podsítě Subnet-1 = <br>
Rozsah adres podsítě = 10.0.0.0/27 <br>
Předplatné = předplatné, které chcete použít <br>
Skupina prostředků = ClassicRG <br>
Umístění = USA – západ <br>
GatewaySubnet = 10.0.0.32/28 <br>
Místní lokalita = RMVNetLocal <br>

**Virtuální síť Resource Manageru**

Název virtuální sítě = RMVNet <br>
Adresní prostor = 192.168.0.0/16 <br>
Resource Group = RG1 <br>
Umístění = USA – východ <br>
Název podsítě Subnet-1 = <br>
Rozsah adres = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Název brány virtuální sítě = RMGateway <br>
Typ brány sítě VPN = <br>
Typ sítě VPN = trasové <br>
SKU = VpnGw1 <br>
Umístění = USA – východ <br>
Virtuální síť = RMVNet <br> (přidružení VPN gateway, která tuto virtuální síť) První konfigurace IP adresy = rmgwpip <br> (veřejná IP adresa brány) Brána místní sítě = ClassicVNetLocal <br>
Název připojení = RMtoClassic

### <a name="connectoverview"></a>Přehled připojení

Pro tuto konfiguraci vytvořit připojení brány VPN přes tunel VPN IPsec/IKE mezi virtuálními sítěmi. Ujistěte se, že překrývat žádné z rozsahů virtuálních sítí mezi sebou, případně s některým z místní sítě, které se připojují.

Následující tabulka uvádí příklad, jak jsou definované příklad virtuálních sítí a místní servery:

| Virtual Network | Adresní prostor | Oblast | Se připojí k místní síťové lokality |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Západní USA | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |USA – východ |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Oddíl 1 – konfigurace nastavení klasické virtuální sítě

V této části vytvoříte klasickou virtuální síť, místní síti (místní lokality) a bránu virtuální sítě. Snímky obrazovek slouží jen jako příklady. Nezapomeňte nahradit hodnoty vlastními a použijte [příklad](#values) hodnoty.

### 1. <a name="classicvnet"></a>Vytvořit klasickou virtuální síť

Pokud nemáte klasické virtuální sítě a jsou spuštěné tyto kroky jako cvičení, můžete vytvořit virtuální síť pomocí [v tomto článku](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a [příklad](#values) hodnoty nastavení výše.

Pokud již máte virtuální síť s bránou VPN, ověřte, že brána je dynamická. Pokud je statická, musíte nejprve odstranit bránu VPN předtím, než přejdete k [konfigurace místní lokality](#local).

1. Otevřete [Azure Portal](https://ms.portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Klikněte na tlačítko **+ vytvořit prostředek** otevře se stránka "New".
3. V poli na marketplace vyhledejte text "Virtuální síť". Pokud místo toho vyberte síť -> virtuální síť, nebude mít možnost vytvořit klasickou virtuální síť.
4. Vyhledejte "Virtuální síť" z vráceném seznamu a klikněte na něj a otevřete stránku virtuální sítě. 
5. Na stránce virtuální sítě vyberte možnost "Klasickém" vytvořit klasickou virtuální síť. Pokud zvolíte výchozí, můžete se ukončí s virtuální sítí Resource Manager místo.

### 2. <a name="local"></a>Konfigurace místní lokality

1. Přejděte do **všechny prostředky** a vyhledejte **ClassicVNet** v seznamu.
2. Na **přehled** stránku, **připojení k síti VPN** klikněte na tlačítko **brány** k vytvoření brány.
  ![Konfigurace brány VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "konfigurace brány VPN")
3. Na **nové připojení VPN** stránky, pro **typ připojení**vyberte **Site-to-site**.
4. Pro **místní lokalita**, klikněte na tlačítko **konfigurovat požadované nastavení**. Tím se otevře **místní lokalita** stránky.
5. Na **místní lokalita** stránce, vytvoření názvu k odkazování na virtuální síť Resource Manageru. Například "RMVNetLocal".
6. Pokud bránu VPN pro virtuální síť Resource Manageru již má veřejnou IP adresu, použijte hodnotu pro **IP adresu brány VPN** pole. Pokud jsou to tyto kroky jako cvičení, nebo ještě nemají bránu virtuální sítě pro vaši virtuální síť Resource Manageru, které tvoří zástupnou IP adresu. Ujistěte se, že zástupnou IP adresu používá platný formát. Později můžete nahradit zástupnou IP adresu veřejnou IP adresu brány virtuální sítě Resource Manageru.
7. Pro **klientský adresní prostor**, použijte [hodnoty](#connectoverview) pro IP adresu virtuální sítě adresní prostory pro virtuální síť Resource Manageru. Toto nastavení slouží k určení adresní prostory pro směrování do virtuální sítě Resource Manageru. V tomto příkladu používáme 192.168.0.0/16, rozsah adres RMVNet.
8. Klikněte na tlačítko **OK** uložte příslušné hodnoty a návrat na **nové připojení VPN** stránky.

### <a name="classicgw"></a>3. Vytvoření brány virtuální sítě

1. Na **nové připojení VPN** stránky, vyberte **vytvořit bránu hned** zaškrtávací políčko.
2. Kliknutím na **Volitelná konfigurace brány** otevřete stránku **Konfigurace brány**.

  ![Stránka Konfigurace brány otevřete](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "brány otevřete stránku konfigurace")
3. Klikněte na tlačítko **podsíť – konfigurovat požadované nastavení** otevřít **přidat podsíť** stránky. **Název** už má nakonfigurovanou požadovaná hodnota: **GatewaySubnet**.
4. **Rozsah adres** odkazuje na oblast pro podsíť brány. Přestože je možné vytvořit podsíť brány s je/29 (3 adresy), rozsah adres doporučujeme vytvořit podsíť brány, který obsahuje další IP adresy. To bude případné budoucí konfigurace, které mohou vyžadovat další dostupné IP adresy. Pokud je to možné použijte možnost/27 nebo/28. Pokud používáte tyto kroky jako cvičení, můžete použít [ukázkové hodnoty](#values). V tomto příkladu používáme "10.0.0.32/28". Klikněte na tlačítko **OK** vytvořit podsíť brány.
5. Na **konfigurace brány** stránce **velikost** odkazuje na SKU brány. Vyberte SKU brány pro VPN gateway.
6. Ověřte **směrování typu** je **dynamické**, pak klikněte na tlačítko **OK** se vraťte do **nové připojení VPN** stránky.
7. Na **nové připojení VPN** klikněte na **OK** zahajte proces vytváření brány VPN. Dokončení vytváření brány sítě VPN může trvat až 45 minut.

### <a name="ip"></a>4. Zkopírujte veřejnou IP adresu brány virtuální sítě

Po vytvoření brány virtuální sítě, můžete zobrazit IP adresu brány. 

1. Přejděte do klasické virtuální sítě a klikněte na tlačítko **přehled**.
2. Klikněte na tlačítko **připojení k síti VPN** otevřete stránku připojení VPN. Na stránce připojení VPN můžete zobrazit veřejnou IP adresu. Toto je veřejná IP adresa přiřazená brány virtuální sítě. Poznamenejte si IP adresu. Použijete ho v dalších krocích při práci s nastavení konfigurace brány místní sítě Resource Manageru. 
3. Můžete zobrazit stav připojení brány. Všimněte si, že na místní síťovou lokalitu, že kterou jste vytvořili, je uveden jako "Připojení". Stav se změní po vytvoření připojení. Tuto stránku můžete zavřít, až budete hotovi, zobrazení stavu.

## <a name="rmvnet"></a>Oddíl 2 – konfigurace nastavení virtuální sítě Resource Manageru

V této části vytvoříte bránu virtuální sítě a bránu místní sítě pro vaši virtuální síť Resource Manageru. Snímky obrazovek slouží jen jako příklady. Nezapomeňte nahradit hodnoty vlastními a použijte [příklad](#values) hodnoty.

### <a name="1-create-a-virtual-network"></a>1. Vytvoření virtuální sítě

**Ukázkové hodnoty:**

* Název virtuální sítě = RMVNet <br>
* Adresní prostor = 192.168.0.0/16 <br>
* Resource Group = RG1 <br>
* Umístění = USA – východ <br>
* Název podsítě Subnet-1 = <br>
* Rozsah adres = 192.168.1.0/24 <br>


Pokud nemáte virtuální síť Resource Manageru a jsou spuštěné tyto kroky jako cvičení, vytvořte virtuální síť s kroky v [vytvoření virtuální sítě](../virtual-network/quick-create-portal.md), pomocí ukázkové hodnoty.

### <a name="2-create-a-gateway-subnet"></a>2. Vytvoření podsítě brány

**Příklad hodnoty:** GatewaySubnet = 192.168.0.0/26

Před vytvořením brány virtuální sítě, musíte nejprve vytvořit podsíť brány. Vytvořit podsíť brány s počtem CIDR/28 nebo větší (/ 27, / 26, atd.). Pokud vytváříte jako součást cvičení, můžete použít ukázkové hodnoty.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="creategw"></a>3. Vytvoření brány virtuální sítě

**Ukázkové hodnoty:**

* Název brány virtuální sítě = RMGateway <br>
* Typ brány sítě VPN = <br>
* Typ sítě VPN = trasové <br>
* SKU = VpnGw1 <br>
* Umístění = USA – východ <br>
* Virtuální síť = RMVNet <br>
* První konfigurace IP adresy = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4. Vytvoření brány místní sítě

**Ukázkové hodnoty:** Brána místní sítě = ClassicVNetLocal

| Virtual Network | Adresní prostor | Oblast | Se připojí k místní síťové lokality |Veřejné IP adresy brány|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Západní USA | RMVNetLocal (192.168.0.0/16) |Veřejnou IP adresu, která je přiřazena ClassicVNet brány|
| RMVNet | (192.168.0.0/16) |USA – východ |ClassicVNetLocal (10.0.0.0/24) |Veřejnou IP adresu, která je přiřazena RMVNet brány.|

Brána místní sítě určuje rozsah adres a veřejnou IP adresu přidružené k vaší klasickou virtuální sítí a svou bránu virtuální sítě. Pokud byste tyto kroky jako cvičení, podívejte se na ukázkové hodnoty.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Oddíl 3 – upravte nastavení místní lokality klasické virtuální sítě

V této části můžete nahradit zástupnou IP adresu, který jste použili při zadávání nastavení místní sítě s IP adresou brány VPN Resource Manageru. Tato část používá klasické rutiny Powershellu.

1. Na webu Azure Portal přejděte k položce klasická virtuální síť.
2. Na stránce vaší virtuální sítě, klikněte na tlačítko **přehled**.
3. V **připojení k síti VPN** klikněte na název vaší místní síti na obrázku.

  ![Připojení VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "připojení k síti VPN")
4. Na **připojení Site-to-site VPN** klikněte na název lokality.

  ![Název lokality](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "název místní lokality")
5. Na stránce připojení pro místní lokalitu, klikněte na název místní lokality, chcete-li otevřít **místní lokalita** stránky.

  ![Otevřít místní lokality](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "otevřít místní web")
6. Na **místní lokalita** stránka, nahraďte **IP adresu brány VPN** s IP adresou brány Resource Manageru.

  ![Adresa ip brány](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "IP adresa brány")
7. Klikněte na tlačítko **OK** k aktualizaci IP adresy.

## <a name="RMtoclassic"></a>Oddíl 4 – vytvoření připojení classic k Resource Manageru

V následujícím postupu nakonfigurujte připojení z virtuální sítě Resource Manageru pro klasické virtuální sítě pomocí webu Azure portal.

1. V **všechny prostředky**, vyhledejte bránu místní sítě. V našem příkladu je brána místní sítě **ClassicVNetLocal**.
2. Klikněte na tlačítko **konfigurace** a ověřte, zda je hodnota IP adresu brány sítě VPN pro klasické virtuální sítě. V případě potřeby aktualizujte a potom klikněte na **Uložit**. Zavřete stránku.
3. V **všechny prostředky**, klikněte na bránu místní sítě.
4. Klikněte na tlačítko **připojení** otevřete stránku připojení.
5. Na **připojení** klikněte na **+** můžete přidat připojení.
6. Na **přidat připojení** stránky, zadejte název připojení. Například "RMtoClassic".
7. **Site-to-Site** je již na této stránce vybrali.
8. Vyberte bránu virtuální sítě, kterou chcete přidružit k této lokalitě.
9. Vytvoření **sdílený klíč**. Tento klíč se také používá ve spojení, které vytvoříte z klasické virtuální sítě k virtuální síti správce prostředků. Můžete generovat klíč nebo si ho vymyslet. V našem příkladu používáme "abc123", ale které můžete (a měli byste) používat něco složitějšího.
10. Klikněte na tlačítko **OK** k vytvoření připojení.

## <a name="classictoRM"></a>Část 5 – vytvoření z modelu classic do Resource Manageru připojení

V následujícím postupu nakonfigurujte připojení z klasické virtuální sítě k virtuální síti správce prostředků. Tyto kroky vyžadují prostředí PowerShell. Toto připojení nelze vytvořit na portálu. Ujistěte se, že jste stáhli a nainstalovali classic (lu) a rutiny Powershellu pro správce prostředků (SV).

### <a name="1-connect-to-your-azure-account"></a>1. Připojení k účtu Azure

Otevřete konzolu Powershellu se zvýšenými oprávněními a připojte se k účtu Azure. Po přihlášení se stáhnou nastavení svého účtu tak, aby byly k dispozici pro prostředí Azure PowerShell. Následující rutina vás vyzve k zadání přihlašovacích údajů pro váš účet Azure pro model nasazení Resource Manager:

```powershell
Connect-AzureRmAccount
```

Načtěte seznam předplatných Azure.

```powershell
Get-AzureRmSubscription
```

Pokud máte více než jedno předplatné, zadejte předplatné, pro kterou chcete použít.

```powershell
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```

Přihlaste se dále používat klasické rutiny prostředí PowerShell (Správa služeb). Použijte následující příkaz pro přidání účtu Azure pro model nasazení classic:

```powershell
Add-AzureAccount
```

Získá seznam vašich předplatných. Tento krok může být nutné při přidávání rutiny správy služeb v závislosti na Azure modul nainstalovat.

```powershell
Get-AzureSubscription
```

Pokud máte více než jedno předplatné, zadejte předplatné, pro kterou chcete použít.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Zobrazit hodnoty soubor konfigurace sítě

Při vytváření virtuální sítě na webu Azure Portal, celý název, který používá Azure není viditelný na webu Azure Portal. Například virtuální síť, která se zobrazí s názvem "ClassicVNet" na webu Azure Portal může pojmenovaný mnohem déle, v konfiguračním souboru sítě. Název může vypadat podobně jako: Seskupit ClassicRG ClassicVNet. V následujícím postupu stáhněte si soubor konfigurace sítě a zobrazit hodnoty.

Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

V textovém editoru otevřete soubor a podívejte se na název pro klasické virtuální sítě. Při spuštění rutiny Powershellu, použijte názvy v konfiguračním souboru sítě.

- Názvy virtuální sítě jsou uvedené jako **VirtualNetworkSite name =**
- Názvy lokalit jsou uvedeny jako **LocalNetworkSite name =**

### <a name="3-create-the-connection"></a>3. Vytvoření připojení

Nastavte sdílený klíč a vytvoříte připojení z klasické virtuální sítě k virtuální síti správce prostředků. Nelze nastavit sdílený klíč pomocí portálu. Ujistěte se, že spustíte tyto kroky, když jste přihlášení pomocí klasické verze rutin Powershellu. Chcete-li tak učinit, použijte **Add-AzureAccount**. V opačném případě nebude možné nastavit "-AzureVNetGatewayKey".

- V tomto příkladu **- VNetName** je název klasickou virtuální síť jak se nachází v konfiguračním souboru sítě. 
- **- LocalNetworkSiteName** název zadaný pro místní lokalitu, jako je nalezena v konfiguračním souboru sítě.
- **- SharedKey** je hodnota, kterou generujete a zadáváte. V tomto příkladu jsme použili *abc123*, ale dají vygenerovat něco složitějšího. Důležité je, že hodnota, kterou zde zadáte, musí být stejné výhody, které jste zadali při vytváření váš správce prostředků classic připojení.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="verify"></a>Část 6 – ověření připojení

Ověření připojení pomocí webu Azure portal nebo Powershellu. Při ověřování, možná budete muset počkat jednu minutu nebo dvě jako připojení se vytváří. Pokud je připojení úspěšné, stav připojení se změní z "Připojení" na "Připojeno".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Chcete-li ověřit připojení z klasické virtuální sítě k virtuální síti správce prostředků

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Chcete-li ověřit připojení z vaší virtuální sítě Resource Manageru pro klasické virtuální sítě

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
