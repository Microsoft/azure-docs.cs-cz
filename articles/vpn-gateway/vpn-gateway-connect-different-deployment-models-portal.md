---
title: 'Propojení klasických virtuálních sítí s Azure Resource Manager virtuální sítě: Portal | Microsoft Docs'
description: Postup připojení klasického virtuální sítěu k Správce prostředků virtuální sítě pomocí VPN Gateway a portálu
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 9d31bcaad01b9b762e57bd619d45c1f53ffb201e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376798"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Propojení virtuálních sítí z různých modelů nasazení pomocí portálu

V tomto článku se dozvíte, jak připojit klasický virtuální sítě k Správce prostředků virtuální sítě, abyste umožnili vzájemné komunikaci prostředků umístěných v různých modelech nasazení. Postup v tomto článku primárně používá Azure Portal, ale tuto konfiguraci můžete také vytvořit pomocí prostředí PowerShell tak, že ho vyberete v tomto seznamu.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Připojení klasické virtuální sítě k virtuální síti Správce prostředků je podobné připojení virtuální sítě k místnímu umístění lokality. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Můžete vytvořit propojení mezi virtuální sítě, která jsou v různých předplatných a v různých oblastech. Můžete taky připojit virtuální sítě, které už mají připojení k místním sítím, pokud je brána, se kterou je nakonfigurovaná, dynamická nebo založená na směrování. Další informace o propojeních VNet-to-VNet najdete v části [Nejčastější dotazy týkající se propojení VNet-to-VNet](#faq) na konci tohoto článku. 

Pokud ještě nemáte bránu virtuální sítě a nechcete ji vytvořit, možná budete chtít místo toho připojit virtuální sítě pomocí partnerského vztahu VNet. Partnerské vztahy virtuálních sítí nepoužívají bránu VPN. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Než začnete



* Tyto kroky předpokládají, že oba virtuální sítě již byly vytvořeny. Pokud tento článek používáte jako cvičení a nemáte virtuální sítě, existují odkazy na postupy, které vám pomůžou je vytvořit.
* Ověřte, že se rozsahy adres pro virtuální sítě nemění mezi sebou, nebo se překrývají s žádným z rozsahů pro další připojení, ke kterým je možné brány připojit.
* Nainstalujte nejnovější rutiny PowerShellu pro Správce prostředků i pro správu služeb (Classic). V tomto článku používáme jak Azure Portal, tak i PowerShell. K vytvoření připojení z klasické virtuální sítě do virtuální sítě Správce prostředků je potřeba PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/). 

### <a name="example-settings"></a><a name="values"></a>Příklad nastavení

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

**Klasická virtuální síť**

Název virtuální sítě = ClassicVNet <br>
Adresní prostor = 10.0.0.0/24 <br>
Název podsítě = podsíť-1 <br>
Rozsah adres podsítě = 10.0.0.0/27 <br>
Předplatné = předplatné, které chcete použít <br>
Skupina prostředků = ClassicRG <br>
Location = Západní USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Místní web = RMVNetLocal <br>

**Správce prostředků VNet**

Název virtuální sítě = RMVNet <br>
Adresní prostor = 192.168.0.0/16 <br>
Skupina prostředků = RG1 <br>
Location = Východní USA <br>
Název podsítě = podsíť-1 <br>
Rozsah adres = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Název brány virtuální sítě = RMGateway <br>
Typ brány = VPN <br>
Typ sítě VPN = směrování založené na trasách <br>
SKU = VpnGw1 <br>
Location = Východní USA <br>
Virtuální síť = RMVNet <br> (přidružte bránu VPN k této virtuální síti) První konfigurace IP adresy = rmgwpip <br> (veřejná IP adresa brány) Brána místní sítě = ClassicVNetLocal <br>
Název připojení = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Přehled připojení

Pro tuto konfiguraci vytvoříte připojení brány sítě VPN přes tunelové propojení IPsec/IKE mezi virtuálními sítěmi. Ujistěte se, že se žádný z rozsahů virtuální sítě mezi sebou nepřekrývá, nebo pomocí kterékoli z místních sítí, ke kterým se připojují.

V následující tabulce je uveden příklad, jak jsou definovány příklady virtuální sítě a místní lokality:

| Virtual Network | Adresní prostor | Oblast | Připojí se k místní síťové lokalitě. |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA – západ | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Oddíl 1 – Konfigurace nastavení klasické virtuální sítě

V této části vytvoříte klasickou virtuální síť, místní síť (místní lokalitu) a bránu virtuální sítě. Snímky obrazovek slouží jen jako příklady. Nezapomeňte hodnoty nahradit vlastními, nebo použijte [ukázkové](#values) hodnoty.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"></a> vytvoření virtuální sítě Classic

Pokud nemáte klasickou virtuální síť a spouštíte tyto kroky jako cvičení, můžete vytvořit virtuální síť pomocí [tohoto článku](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) a výše uvedené [Příklady](#values) hodnot nastavení.

Pokud již máte virtuální síť s bránou sítě VPN, ověřte, zda je brána dynamická. Pokud je statická, musíte nejdřív odstranit bránu VPN, než budete pokračovat [v konfiguraci místní lokality](#local).

1. Otevřete [Azure Portal](https://ms.portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Kliknutím na **+ vytvořit prostředek** otevřete stránku nový.
3. Do pole Hledat na Marketplace zadejte ' Virtual Network '. Pokud místo toho vyberte síť – > Virtual Network, nebudete mít možnost vytvořit klasickou virtuální síť.
4. Vyhledejte ' Virtual Network ' ze seznamu vrácených a kliknutím na něj otevřete stránku Virtual Network. 
5. Na stránce virtuální síť vyberte Classic a vytvořte klasickou virtuální síť. Pokud zde použijete výchozí nastavení, budete místo toho mít k disSprávce prostředků virtuální síť.

### <a name="2-configure-the-local-site"></a>2. <a name="local"></a> Konfigurace místní lokality

1. Přejděte na **všechny prostředky** a vyhledejte v seznamu **ClassicVNet** .
2. Klikněte na **Brána** v části **Nastavení** v nabídce a pak kliknutím na banner vytvořte bránu.
  ![Konfigurace brány VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Konfigurace brány VPN")
3. Na stránce **nové připojení VPN** pro **Typ připojení** vyberte **site-to-site**.
4. U možnosti **místní lokalita** klikněte na **konfigurovat požadovaná nastavení**. Tím se otevře stránka **místní web** .
5. Na stránce **místní lokalita** vytvořte název, který bude odkazovat na správce prostředků virtuální síť. Například ' RMVNetLocal '.
6. Pokud brána VPN pro Správce prostředků virtuální síť již má veřejnou IP adresu, použijte hodnotu pro pole **IP adresa brány VPN** . Pokud provedete tyto kroky jako cvičení nebo ještě nemáte bránu virtuální sítě pro vaši virtuální síť Správce prostředků, můžete vytvořit zástupnou IP adresu. Ujistěte se, že zástupný symbol IP adresa používá platný formát. Později nahraďte zástupnou IP adresu veřejnou IP adresou brány Správce prostředků virtuální sítě.
7. Pro **adresní prostor klienta** použijte [hodnoty](#connectoverview) pro adresní prostory IP adres virtuální sítě pro virtuální síť správce prostředků. Toto nastavení slouží k zadání adresních prostorů pro směrování do Správce prostředků virtuální sítě. V tomto příkladu používáme pro RMVNet rozsah adres 192.168.0.0/16.
8. Kliknutím na tlačítko **OK** uložte hodnoty a vraťte se na stránku **nové připojení k síti VPN** .

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. Vytvoření brány virtuální sítě

1. Na stránce **nové připojení VPN** zaškrtněte políčko **vytvořit bránu hned** .
2. Kliknutím na **Volitelná konfigurace brány** otevřete stránku **Konfigurace brány**.

   ![Otevřít stránku konfigurace brány](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Otevřít stránku konfigurace brány")
3. Kliknutím na **podsíť – konfigurovat požadované nastavení** otevřete stránku **Přidat podsíť** . **Název** je již nakonfigurován s požadovanou hodnotou: **GatewaySubnet**.
4. **Rozsah adres** odkazuje na rozsah pro podsíť brány. I když můžete vytvořit podsíť brány s rozsahem adres/29 (3 adresy), doporučujeme vytvořit podsíť brány, která bude obsahovat víc IP adres. To bude vyhovovat budoucím konfiguracím, které mohou vyžadovat více dostupných IP adres. Pokud je to možné, použijte/27 nebo/28. Používáte-li tyto kroky jako cvičení, můžete odkazovat na [Ukázkové hodnoty](#values). V tomto příkladu používáme 10.0.0.32/28. Kliknutím na **OK** vytvořte podsíť brány.
5. Na stránce **Konfigurace brány** se **Velikost** vztahuje na SKU brány. Vyberte SKU brány pro vaši bránu VPN.
6. Ověřte, zda je **typ směrování** **dynamický**, a kliknutím na tlačítko **OK** se vraťte na stránku **nové připojení k síti VPN** .
7. Na stránce **nové připojení VPN** klikněte na **OK** a začněte vytvářet bránu VPN. Dokončení vytváření brány sítě VPN může trvat až 45 minut.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. Zkopírujte veřejnou IP adresu brány virtuální sítě.

Po vytvoření brány virtuální sítě si můžete zobrazit IP adresu brány. 

1. Přejděte na klasickou virtuální síť a klikněte na **Přehled**.
2. Kliknutím na **připojení VPN** otevřete stránku připojení VPN. Na stránce připojení VPN můžete zobrazit veřejnou IP adresu. Toto je veřejná IP adresa přiřazená vaší bráně virtuální sítě. Poznamenejte si IP adresu. Použijete ji v pozdějších krocích při práci s nastavením konfigurace brány místní sítě Správce prostředků. 
3. Můžete zobrazit stav připojení brány. Všimněte si, že místní síťová lokalita, kterou jste vytvořili, je uvedena jako "připojování". Po vytvoření připojení se stav změní. Po skončení zobrazení stavu můžete tuto stránku zavřít.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Oddíl 2 – konfigurace nastavení Správce prostředků VNet

V této části vytvoříte bránu virtuální sítě a bránu místní sítě pro vaši virtuální síť Správce prostředků. Snímky obrazovek slouží jen jako příklady. Nezapomeňte hodnoty nahradit vlastními, nebo použijte [ukázkové](#values) hodnoty.

### <a name="1-create-a-virtual-network"></a>1. vytvoření virtuální sítě

**Příklady hodnot:**

* Název virtuální sítě = RMVNet <br>
* Adresní prostor = 192.168.0.0/16 <br>
* Skupina prostředků = RG1 <br>
* Location = Východní USA <br>
* Název podsítě = podsíť-1 <br>
* Rozsah adres = 192.168.1.0/24 <br>

Pokud nemáte Správce prostředků virtuální síť a spouštíte tyto kroky jako cvičení, vytvořte virtuální síť pomocí kroků v části [vytvoření virtuální sítě](../virtual-network/quick-create-portal.md)s použitím ukázkových hodnot.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Vytvoření brány virtuální sítě

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Příklady hodnot:**

* Název brány virtuální sítě = RMGateway <br>
* Typ brány = VPN <br>
* Typ sítě VPN = směrování založené na trasách <br>
* SKU = VpnGw1 <br>
* Location = Východní USA <br>
* Virtuální síť = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* První konfigurace IP adresy = rmgwpip <br>

[!INCLUDE [Add gateway](../../includes/vpn-gateway-add-gw-rm-portal-empty.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. Vytvoření brány místní sítě

**Příklady hodnot:** Brána místní sítě = ClassicVNetLocal

| Virtual Network | Adresní prostor | Oblast | Připojí se k místní síťové lokalitě. |Veřejná IP adresa brány|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |USA – západ | RMVNetLocal (192.168.0.0/16) |Veřejná IP adresa, která je přiřazená bráně ClassicVNet|
| RMVNet | (192.168.0.0/16) |East US |ClassicVNetLocal (10.0.0.0/24) |Veřejná IP adresa, která je přiřazená bráně RMVNet.|

Brána místní sítě určuje rozsah adres a veřejnou IP adresu přidruženou k vaší klasické virtuální síti a její bráně virtuální sítě. Pokud provedete tyto kroky jako cvičení, přečtěte si příklady hodnot.

[!INCLUDE [Add local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-ip-empty.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Oddíl 3 – Změna nastavení místního webu virtuální sítě

V této části nahradíte zástupnou IP adresu, kterou jste použili při zadávání nastavení místních lokalit, s Správce prostředků IP adresou brány VPN. V této části se používají rutiny prostředí PowerShell Classic (SM).

1. V Azure Portal přejděte do klasické virtuální sítě.
2. Na stránce vaší virtuální sítě klikněte na **Přehled**.
3. V části **připojení k síti VPN** klikněte na název místní lokality na obrázku.

   ![VPN – připojení](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Připojení VPN")
4. Na stránce **připojení VPN typu Site-to-site** klikněte na název webu.

   ![Název webu](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Název místního webu")
5. Na stránce připojení pro místní web klikněte na název místní lokality a otevřete stránku **místní lokalita** .

   ![Otevřít – místní-lokalita](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Otevřít místní lokalitu")
6. Na stránce **místní lokalitu** nahraďte **IP adresu brány VPN** IP adresou brány správce prostředků.

   ![Brána – IP adresa](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "IP adresa brány")
7. Kliknutím na tlačítko **OK** aktualizujte IP adresu.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Oddíl 4 – Vytvoření Správce prostředků pro klasické připojení

V těchto krocích nakonfigurujete připojení z Správce prostředků VNet na klasickou virtuální síť pomocí Azure Portal.

1. V části **všechny prostředky** vyhledejte bránu místní sítě. V našem příkladu je brána místní sítě **ClassicVNetLocal**.
2. Klikněte na **Konfigurace** a ověřte, jestli je hodnota IP adresa bránou sítě VPN pro klasickou virtuální síť. V případě potřeby aktualizujte a pak klikněte na **Uložit**. Stránku zavřete.
3. V části **všechny prostředky** klikněte na bránu místní sítě.
4. Kliknutím na **připojení** otevřete stránku připojení.
5. Na stránce **připojení** klikněte na **+** Přidat připojení.
6. Na stránce **Přidat připojení zadejte** název připojení. Například ' RMtoClassic '.
7. **Site-to-site** je již na této stránce vybrán.
8. Vyberte bránu virtuální sítě, kterou chcete přidružit k tomuto webu.
9. Vytvořte **sdílený klíč**. Tento klíč se používá také v připojení, které vytvoříte z klasické virtuální sítě do virtuální sítě Správce prostředků. Můžete vytvořit klíč nebo ho vytvořit znovu. V našem příkladu používáme "abc123", ale můžete (a měli byste) používat něco složitějšího.
10. Kliknutím na tlačítko **OK** vytvořte připojení.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Oddíl 5 – Vytvoření klasického připojení k Správce prostředků

V těchto krocích nakonfigurujete připojení z klasické virtuální sítě k virtuální síti Správce prostředků. Tyto kroky vyžadují prostředí PowerShell. Toto připojení nemůžete vytvořit na portálu. Ujistěte se, že jste stáhli a nainstalovali rutiny prostředí PowerShell pro Classic (SM) a Správce prostředků (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Připojte se k účtu Azure

Otevřete konzolu PowerShellu se zvýšenými právy a přihlaste se ke svému účtu Azure. Po přihlášení se stáhne nastavení účtu, aby bylo možné Azure PowerShell. Následující rutina vás vyzve k zadání přihlašovacích údajů pro váš účet Azure pro model nasazení Správce prostředků:

```powershell
Connect-AzAccount
```

Načtěte seznam předplatných Azure.

```powershell
Get-AzSubscription
```

Pokud máte více než jedno předplatné, zadejte předplatné, které chcete použít.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Pak se přihlaste k používání klasických rutin PowerShellu (Správa služeb). Pomocí následujícího příkazu přidejte svůj účet Azure pro model nasazení Classic:

```powershell
Add-AzureAccount
```

Získejte seznam předplatných. Tento krok může být nutný při přidávání rutin služby Service Management v závislosti na instalaci modulu Azure.

```powershell
Get-AzureSubscription
```

Pokud máte více než jedno předplatné, zadejte předplatné, které chcete použít.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Prohlédněte si hodnoty konfiguračního souboru sítě.

Když v Azure Portal vytvoříte virtuální síť, úplný název, který Azure používá, není viditelný v Azure Portal. Například virtuální síť, která se zdá mít název "ClassicVNet" v Azure Portal může mít v souboru konfigurace sítě mnohem delší název. Název může vypadat nějak takto: "Group ClassicRG ClassicVNet". V těchto krocích stáhnete konfigurační soubor sítě a zobrazíte hodnoty.

Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otevřete soubor pomocí textového editoru a zobrazte název své klasické virtuální sítě. Při spouštění rutin PowerShellu použijte názvy v souboru konfigurace sítě.

- Názvy virtuální sítě jsou uvedené jako **VirtualNetworkSite název =**
- Názvy webů jsou uvedené jako **LocalNetworkSite název =**

### <a name="3-create-the-connection"></a>3. vytvoření připojení

Nastavte sdílený klíč a vytvořte připojení z klasické virtuální sítě k virtuální síti Správce prostředků. Pomocí portálu nemůžete nastavit sdílený klíč. Pokud jste se přihlásili pomocí klasické verze rutin PowerShellu, ujistěte se, že tyto kroky spustíte. Uděláte to tak, že použijete **příkaz Add-AzureAccount**. V opačném případě nebudete moci nastavit '-AzureVNetGatewayKey '.

- V tomto příkladu **– VNetName** je název klasické virtuální sítě, jak se nachází v konfiguračním souboru vaší sítě. 
- Parametr **-LocalNetworkSiteName** je název, který jste zadali pro místní lokalitu, jak se nachází v konfiguračním souboru vaší sítě.
- **-SharedKey** je hodnota, kterou vygenerujete a zadáte. V tomto příkladu jsme použili *abc123*, ale můžete vygenerovat něco složitějšího. Důležité je, aby hodnota, kterou zde zadáte, měla stejnou hodnotu, kterou jste zadali při vytváření Správce prostředků k klasickému připojení.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Oddíl 6 – ověření připojení

Připojení můžete ověřit pomocí Azure Portal nebo PowerShellu. Při ověřování může být nutné počkat minutu nebo dvě při vytváření připojení. Po úspěšném připojení se stav připojení změní z ' připojování ' na ' připojeno '.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Ověření připojení z vaší klasické virtuální sítě k vaší Správce prostředků virtuální síti

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Ověření připojení z vaší virtuální sítě Správce prostředků k vaší klasické virtuální síti

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
