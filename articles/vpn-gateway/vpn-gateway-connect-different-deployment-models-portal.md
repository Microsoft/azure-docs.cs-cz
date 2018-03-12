---
title: "Připojení klasické virtuální sítě k virtuálním sítím Azure Resource Manager: portál | Microsoft Docs"
description: "Vytvoření připojení VPN mezi klasické virtuální sítě a virtuální sítě Resource Manager pomocí brány sítě VPN a portálu"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/13/2018
ms.author: cherylmc
ms.openlocfilehash: 90a3c51fa9acbcd9701d4c2b135688ef5148bbf8
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Připojit virtuální sítě z různé modely nasazení pomocí portálu

Tento článek ukazuje, jak se připojit virtuální sítě classic do Resource Manager virtuální sítě tak, aby prostředky, které jsou umístěné v samostatné nasazení modely pro komunikaci mezi sebou. Kroky v tomto článku především pomocí portálu Azure, ale můžete také vytvořit této konfigurace pomocí prostředí PowerShell tak, že vyberete článek z tohoto seznamu.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Připojení klasické virtuální sítě k virtuální síti Resource Manager je podobné propojení virtuální sítě do umístění místního webu. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Můžete vytvořit připojení mezi virtuální sítě, které jsou v různých předplatných a v různých oblastech. Virtuální sítě, které už máte připojení k místní sítě, můžete také připojit, pokud je brány, které byly nakonfigurovány k dynamické nebo založené na trasách. Další informace o propojeních VNet-to-VNet najdete v části [Nejčastější dotazy týkající se propojení VNet-to-VNet](#faq) na konci tohoto článku. 

Pokud ještě není bránu virtuální sítě a nechcete, aby si ji vytvořit, můžete místo toho zvažte připojení vaší virtuální sítě pomocí virtuální sítě partnerský vztah. Partnerské vztahy virtuálních sítí nepoužívají bránu VPN. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

### <a name="before"></a>Než začnete

* Tento postup předpokládá, že jste již vytvořili obě virtuální sítě. Pokud používáte tento článek jako cvičení a nemáte virtuálních sítí, existují odkazy v krocích vám pomůže vytvořit je.
* Ověřte, zda rozsahy adres pro virtuální sítě není navzájem překrývají, nebo se překrývají s žádným z rozsahů pro další připojení, které brány může být připojen k.
* Nainstalujte nejnovější rutiny prostředí PowerShell pro Resource Manager a Service Management (klasické). V tomto článku používáme portál Azure a prostředí PowerShell. Prostředí PowerShell je potřeba vytvořit připojení z klasické virtuální sítě do virtuální sítě Resource Manager. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). 

### <a name="values"></a>Příklady nastavení

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

**Klasické virtuální sítě**

Název virtuální sítě = ClassicVNet <br>
Adresní prostor = 10.0.0.0/24 <br>
Název podsítě Subnet-1 = <br>
Rozsah adres podsítě = 10.0.0.0/27 <br>
Předplatné = předplatné, které chcete použít <br>
Skupina prostředků = ClassicRG <br>
Umístění = západní USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Místní lokalita = RMVNetLocal <br>

**Virtuální sítě Resource Manageru**

Název virtuální sítě = RMVNet <br>
Adresní prostor = 192.168.0.0/16 <br>
Skupina prostředků = RG1 <br>
Umístění = východní USA <br>
Název podsítě Subnet-1 = <br>
Rozsah adres = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Název brány virtuální sítě = RMGateway <br>
Typ brány sítě VPN = <br>
Typ sítě VPN = trasové <br>
SKU = VpnGw1 <br>
Umístění = východní USA <br>
Virtuální sítě = RMVNet <br> (přidružte bránu VPN do této virtuální sítě) První konfigurace protokolu IP = rmgwpip <br> (veřejná IP adresa brány) Brána místní sítě = ClassicVNetLocal <br>
Název připojení = RMtoClassic

### <a name="connectoverview"></a>Připojení – přehled

Pro tuto konfiguraci vytvořit připojení k bráně VPN přes tunelové propojení protokolu IPsec/IKE VPN mezi virtuálními sítěmi. Ujistěte se, že žádný z vaší virtuální sítě rozsahů nepřekrývá mezi sebou, nebo s žádným z místní sítě, které se připojují k.

Následující tabulka uvádí příklad, jak jsou definovány příklad virtuálních sítí a místních webů:

| Virtual Network | Adresní prostor | Oblast | Připojí se k místnímu síťovému webu |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Západní USA | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |Východ USA |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Oddíl 1 – konfigurace classic nastavení sítě VNet

V této části vytvoříte klasické virtuální síti, místní sítě (místní lokality) a Brána virtuální sítě. Snímky obrazovek slouží jen jako příklady. Nezapomeňte nahradit hodnoty vlastními a použijte [příklad](#values) hodnoty.

### 1. <a name="classicvnet"></a>Vytvoření klasické virtuální sítě

Pokud nemáte klasické virtuální síti a jsou spuštěné tyto kroky jako cvičení, můžete vytvořit virtuální síť pomocí [v tomto článku](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a [příklad](#values) hodnoty nastavení z výše.

Pokud již máte virtuální síť, brána sítě VPN, ověřte, zda brána dynamické. Pokud je statická, musíte nejprve odstranit bránu VPN dřív než přejdete k [nakonfigurovat místní lokality](#local).

1. Otevřete [Azure Portal](https://ms.portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Klikněte na tlačítko **+ vytvořit prostředek** chcete otevřít stránku 'New'.
3. V poli "vyhledávání na webu marketplace, zadejte"Virtuální sítě". Pokud místo toho vyberte sítě -> virtuální sítě, nebude získáte možnost vytvoření klasické virtuální sítě.
4. Vyhledejte "virtuální sítě, ze seznamu vrácených a klikněte na něj chcete otevřít stránku virtuální sítě. 
5. Na stránce virtuální sítě vyberte možnost "Klasickém" k vytvoření klasické virtuální sítě. Pokud pořídíte výchozí nastavení, můžete se dostal s virtuální sítě Resource Manager místo.

### 2. <a name="local"></a>Nakonfigurujte místní lokalitě.

1. Přejděte na **všechny prostředky** a najděte **ClassicVNet** v seznamu.
2. Na **přehled** stránky v **připojení k síti VPN** klikněte na tlačítko **brány** k vytvoření brány.
  ![Konfigurovat bránu VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "konfigurovat bránu VPN")
3. Na **nové připojení VPN** stránky, pro **typ připojení**, vyberte **Site-to-site**.
4. Pro **místní lokality**, klikněte na tlačítko **konfigurovat požadované nastavení**. Tím se otevře **místní lokality** stránky.
5. Na **místní lokality** stránky, vytvořte název, který bude odkazovat na virtuální sítě Resource Manageru. Například 'RMVNetLocal'.
6. Pokud brána VPN pro virtuální sítě Resource Manageru již má veřejnou IP adresu, použijte hodnotu pro **IP adresa brány VPN** pole. Pokud jsou to tyto kroky jako cvičení, nebo nemáte ještě bránu virtuální sítě pro virtuální sítě Resource Manager, můžete nastavit až na zástupný symbol IP adresu. Ujistěte se, že IP adresa zástupný symbol používá platný formát. Později nahraďte zástupný symbol IP adresu s veřejnou IP adresu brány virtuální sítě Resource Manager.
7. Pro **klienta adresní prostor**, použijte [hodnoty](#connectoverview) pro IP adresu virtuální sítě adresní prostory pro virtuální sítě Resource Manageru. Toto nastavení slouží k určení adresní prostory směrovat do virtuální sítě Resource Manager. V příkladu používáme 192.168.0.0/16, rozsah adres pro RMVNet.
8. Klikněte na tlačítko **OK** uložit hodnoty a vrátíte se do **nové připojení VPN** stránky.

### <a name="classicgw"></a>3. Vytvoření brány virtuální sítě

1. Na **nové připojení VPN** vyberte **vytvořit bránu hned** zaškrtávací políčko.
2. Kliknutím na **Volitelná konfigurace brány** otevřete stránku **Konfigurace brány**.

  ![Stránka Konfigurace otevřít bránu](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "stránku konfigurace otevřete brány")
3. Klikněte na tlačítko **podsíť – nakonfigurujte požadovaná nastavení** otevřete **přidat podsíť** stránky. **Název** již nastavena požadovaná hodnota: **GatewaySubnet**.
4. **Rozsahu adres** odkazuje na oblast pro podsíť brány. I když můžete vytvořit podsíť brány s/29 adres rozsah (3 adresy), doporučujeme vytvořit podsíť brány, který obsahuje víc IP adres. To je dostatečná pro budoucí konfigurace, které mohou vyžadovat další dostupné IP adresy. Pokud je to možné použijte/27 nebo velikosti/28. Pokud používáte tyto kroky jako cvičení, můžete se podívat do [ukázkové hodnoty](#values). V tomto příkladu používáme '10.0.0.32/28'. Klikněte na tlačítko **OK** vytvořit podsíť brány.
5. Na **konfigurace brány** stránky, **velikost** odkazuje na SKU brány. Vyberte skladová položka brány pro bránu sítě VPN.
6. Ověřte **typ směrování** je **dynamické**, pak klikněte na tlačítko **OK** se vraťte do **nové připojení VPN** stránky.
7. Na **nové připojení VPN** klikněte na tlačítko **OK** zahájíte vytváření brány VPN. Vytvoření brány VPN může trvat až 45 minut na dokončení.

### <a name="ip"></a>4. Zkopírujte veřejnou IP adresu brány virtuální sítě

Po vytvoření brány virtuální sítě, můžete zobrazit IP adresu brány. 

1. Přejděte na klasické virtuální síti a klikněte na tlačítko **přehled**.
2. Klikněte na tlačítko **připojení k síti VPN** otevře se stránka připojení VPN. Na stránce připojení sítě VPN můžete zobrazit veřejnou IP adresu. Toto je veřejnou IP adresu přiřadit k bráně virtuální sítě. Poznamenejte si IP adresy. Použijete jej v dalších krocích při práci s nastavení konfigurace brány místní sítě Resource Manager. 
3. Můžete zobrazit stav připojení brány. Všimněte si, že na místní síťový web, který jste vytvořili je uveden jako "Připojení". Stav se změní po vytvoření připojení. Tuto stránku můžete zavřít, když jste hotovi, zobrazení stavu.

## <a name="rmvnet"></a>Oddíl 2 – nakonfigurujte nastavení virtuální sítě Resource Manageru

V této části vytvoříte bránu virtuální sítě a brány místní sítě pro virtuální sítě Resource Manager. Snímky obrazovek slouží jen jako příklady. Nezapomeňte nahradit hodnoty vlastními a použijte [příklad](#values) hodnoty.

### <a name="1-create-a-virtual-network"></a>1. Vytvoření virtuální sítě

**Ukázkové hodnoty:**

* Název virtuální sítě = RMVNet <br>
* Adresní prostor = 192.168.0.0/16 <br>
* Skupina prostředků = RG1 <br>
* Umístění = východní USA <br>
* Název podsítě Subnet-1 = <br>
* Rozsah adres = 192.168.1.0/24 <br>


Pokud nemáte virtuální sítě Resource Manageru a běží tyto kroky jako cvičení, vytvořte virtuální síť s kroky v [vytvořit virtuální síť](../virtual-network/quick-create-portal.md), pomocí ukázkové hodnoty.

### <a name="2-create-a-gateway-subnet"></a>2. Vytvoření podsítě brány

**Příklad hodnoty:** GatewaySubnet = 192.168.0.0/26

Před vytvořením brány virtuální sítě, musíte nejprve vytvořit podsíť brány. Vytvořit podsíť brány s počtem CIDR/28 nebo větší (/ 27, / 26, atd.). Pokud vytváříte jako součást cvičení, můžete použít ukázkové hodnoty.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="creategw"></a>3. Vytvoření brány virtuální sítě

**Ukázkové hodnoty:**

* Název brány virtuální sítě = RMGateway <br>
* Typ brány sítě VPN = <br>
* Typ sítě VPN = trasové <br>
* SKU = VpnGw1 <br>
* Umístění = východní USA <br>
* Virtuální sítě = RMVNet <br>
* První konfigurace protokolu IP = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4. Vytvoření brány místní sítě

**Ukázkové hodnoty:** brány místní sítě = ClassicVNetLocal

| Virtual Network | Adresní prostor | Oblast | Připojí se k místnímu síťovému webu |Veřejná IP adresa brány|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Západní USA | RMVNetLocal (192.168.0.0/16) |Veřejnou IP adresu, která je přiřazena k bráně ClassicVNet|
| RMVNet | (192.168.0.0/16) |Východ USA |ClassicVNetLocal (10.0.0.0/24) |Veřejnou IP adresu, která je přiřazena k bráně RMVNet.|

Místní síťová brána určuje rozsah adres a veřejné IP adresy přidružené k vaší klasické virtuální sítě a jeho brány virtuální sítě. Pokud byste tyto kroky jako cvičení, podívejte se na ukázkové hodnoty.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Oddíl 3 - upravte nastavení místní lokality klasické virtuální sítě

V této části nahraďte zástupný symbol IP adresu, kterou jste použili při zadávání nastavení místní lokality s IP adresou brány VPN Resource Manager. Tato část používá classic rutin prostředí PowerShell (SM).

1. Na portálu Azure přejděte do klasické virtuální sítě.
2. Na stránce pro vaši virtuální síť, klikněte na **přehled**.
3. V **připojení k síti VPN** klikněte na název vaší místní sítě na obrázku.

  ![Připojení VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "připojení k síti VPN")
4. Na **připojení Site-to-site VPN** klikněte na název lokality.

  ![Název lokality](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "název místního webu")
5. Na stránce připojení místní lokality, klikněte na název místní lokalitě. Chcete-li otevřít **místní lokality** stránky.

  ![Otevřete místní site](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "otevřete místní lokality")
6. Na **místní lokality** stránky, nahraďte **IP adresa brány VPN** s IP adresou brány Resource Manager.

  ![Adresa ip brány](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "IP adresa brány")
7. Klikněte na tlačítko **OK** aktualizovat IP adresu.

## <a name="RMtoclassic"></a>Oddíl 4 – vytvoření Resource Manager classic připojení

V těchto kroků můžete nakonfigurovat připojení z virtuální sítě Resource Manageru k klasické virtuální sítě pomocí portálu Azure.

1. V **všechny prostředky**, vyhledejte bránu místní sítě. V našem příkladu bránu místní sítě je **ClassicVNetLocal**.
2. Klikněte na tlačítko **konfigurace** a ověřte, zda je hodnota IP adresu brány VPN pro klasické virtuální sítě. Aktualizovat, v případě potřeby a pak klikněte na **Uložit**. Zavřete stránku.
3. V **všechny prostředky**, klikněte na tlačítko brána místní sítě.
4. Klikněte na tlačítko **připojení** chcete otevřít stránku připojení.
5. Na **připojení** klikněte na tlačítko  **+**  přidat připojení.
6. Na **přidat připojení** stránky, název připojení. Například 'RMtoClassic'.
7. **Site-to-Site** je již vybrána na této stránce.
8. Vyberte bránu virtuální sítě, který chcete přidružit k této lokalitě.
9. Vytvoření **sdílený klíč**. Tento klíč se také používá pro připojení, které vytvořit z klasické virtuální sítě pro virtuální sítě Resource Manageru. Můžete vygenerovat klíč nebo si ho vymyslet. V našem příkladu používáme 'abc123', ale můžete (a měli) používáte něco složitější.
10. Klikněte na tlačítko **OK** k vytvoření připojení.

## <a name="classictoRM"></a>Část 5 – Vytvoření klasického správce prostředků připojení

V těchto kroků můžete nakonfigurovat připojení z klasické virtuální sítě do virtuální sítě Resource Manager. Tyto kroky vyžadují prostředí PowerShell. Toto připojení nelze vytvořit na portálu. Zajistěte, aby byly staženy a nainstalovány classic (SM) i rutiny prostředí PowerShell Resource Manager (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Připojení k účtu Azure

Otevřete konzolu prostředí PowerShell se zvýšenými oprávněními a přihlaste se k účtu Azure. Po přihlášení, se stáhnou nastavení svého účtu, aby byly k dispozici pro prostředí Azure PowerShell. Následující rutina vás vyzve k zadání přihlašovací údaje pro účet Azure pro model nasazení Resource Manager:

```powershell
Login-AzureRmAccount
```

Načtěte seznam předplatných Azure.

```powershell
Get-AzureRmSubscription
```

Pokud máte více než jedno předplatné, určete předplatné, které chcete použít.

```powershell
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```

Přihlaste se dále používat classic rutin prostředí PowerShell (správou služeb). Použijte následující příkaz pro přidání účtu Azure pro model nasazení classic:

```powershell
Add-AzureAccount
```

Získejte seznam předplatných. Tento krok může být nutné při přidávání rutiny pro správu služby, v závislosti na modulu Azure nainstalovat.

```powershell
Get-AzureSubscription
```

Pokud máte více než jedno předplatné, určete předplatné, které chcete použít.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Zobrazit hodnoty souboru konfigurace sítě

Když vytvoříte virtuální síť na portálu Azure, úplný název, který používá Azure není zobrazená v portálu Azure. Například virtuální sítě, který se zobrazí s názvem 'ClassicVNet' na portálu Azure může mít mnoho delší název v souboru konfigurace sítě. Název může vypadat podobně jako: 'Skupiny ClassicRG ClassicVNet'. V následujícím postupu stáhněte konfiguračního souboru sítě a zobrazovat hodnoty.

Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otevřete soubor v textovém editoru a zobrazit název pro klasické virtuální síti. Použije názvy v konfiguračním souboru sítě při spuštění vaše rutiny prostředí PowerShell.

- Názvy virtuální sítě jsou uvedeny jako **VirtualNetworkSite name =**
- Názvy lokalit jsou uvedeny jako **LocalNetworkSite name =**

### <a name="3-create-the-connection"></a>3. Vytvoření připojení

Nastavit sdílený klíč a vytvoříte připojení z klasické virtuální sítě do virtuální sítě Resource Manager. Nelze nastavit sdílený klíč pomocí portálu. Ujistěte se, že spustíte tyto kroky při přihlašování pomocí classic verze rutin prostředí PowerShell. Chcete-li to provést, použijte **Add-AzureAccount**. Jinak nebudete moct nastavit '-AzureVNetGatewayKey'.

- V tomto příkladu **- VNetName** je název klasické virtuální síti jak se nachází v souboru konfigurace sítě. 
- **- LocalNetworkSiteName** se název zadaný pro místní lokality jako nachází v souboru konfigurace vaší sítě.
- **- SharedKey** je hodnota, která můžete vygenerovat a zadat. V tomto příkladu jsme použili *abc123*, ale můžete vygenerovat něco složitější. Důležité je, že hodnota, kterou tady zadáte, musí být stejnou hodnotou, kterou jste zadali při vytváření vašeho správce prostředků classic připojení.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

##<a name="verify"></a>Část 6 - ověřte připojení

Připojení můžete ověřit pomocí portálu Azure nebo prostředí PowerShell. Při ověřování, budete muset Počkejte minutu nebo dvě jako připojení je právě vytvářena. Pokud je připojení úspěšné, stav připojení změní z "Připojení" "Připojena".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Chcete-li ověřit připojení z vaší klasické virtuální sítě k virtuální síti Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Chcete-li ověřit připojení z vaší virtuální sítě Resource Manager k vaší klasické virtuální sítě

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
