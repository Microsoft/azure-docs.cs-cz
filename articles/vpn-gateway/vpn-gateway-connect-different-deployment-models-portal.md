---
title: 'Připojení klasických virtuálních sítí k virtuálním sítím Azure Resource Manager: Portál | Dokumenty společnosti Microsoft'
description: Kroky pro připojení klasických virtuálních sítí k virtuálním sítím Správce prostředků pomocí brány VPN a portálu
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 5e64cb2db2bd16a881334779a1c6f1ef19296da2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152019"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Připojení virtuálních sítí z různých modelů nasazení pomocí portálu

Tento článek ukazuje, jak připojit klasické virtuální sítě k virtuálním sítím Správce prostředků, aby prostředky umístěné v samostatných modelech nasazení mohly vzájemně komunikovat. Kroky v tomto článku primárně používají portál Azure, ale můžete také vytvořit tuto konfiguraci pomocí Prostředí PowerShell výběrem článku z tohoto seznamu.

> [!div class="op_single_selector"]
> * [Portál](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Připojení klasické virtuální sítě k virtuální síti Správce prostředků je podobné připojení virtuální sítě k místnímu umístění webu. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE. Můžete vytvořit připojení mezi virtuálními sítěmi, které jsou v různých předplatných a v různých oblastech. Můžete také připojit virtuální sítě, které už mají připojení k místním sítím, pokud je brána, se kterou byly nakonfigurované, dynamická nebo založená na trasách. Další informace o propojeních VNet-to-VNet najdete v části [Nejčastější dotazy týkající se propojení VNet-to-VNet](#faq) na konci tohoto článku. 

Pokud ještě nemáte bránu virtuální sítě a nechcete ji vytvořit, můžete místo toho zvážit připojení virtuálních sítí pomocí partnerského vztahu virtuální sítě. Partnerské vztahy virtuálních sítí nepoužívají bránu VPN. Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Než začnete



* Tyto kroky předpokládají, že obě virtuální sítě již byly vytvořeny. Pokud používáte tento článek jako cvičení a nemáte virtuální sítě, jsou odkazy v krocích, které vám pomohou vytvořit.
* Ověřte, zda se rozsahy adres virtuálních sítí vzájemně nepřekrývají nebo se překrývají s žádným rozsahem pro jiná připojení, ke kterým mohou být brány připojeny.
* Nainstalujte nejnovější rutiny prostředí PowerShell pro Správce prostředků i pro správu služeb (klasická). V tomto článku používáme portál Azure i PowerShell. PowerShell je potřeba k vytvoření připojení z klasické virtuální sítě k virtuální síti Správce prostředků. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). 

### <a name="example-settings"></a><a name="values"></a>Příklad nastavení

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

**Klasická virtuální síť**

Název virtuální sítě = ClassicVNet <br>
Adresní prostor = 10.0.0.0/24 <br>
Název podsítě = Podsíť-1 <br>
Rozsah adres podsítě = 10.0.0.0/27 <br>
Předplatné = předplatné, které chcete použít <br>
Skupina prostředků = ClassicRG <br>
Umístění = Západní USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Místní web = RMVNetLocal <br>

**Virtuální síť Správce prostředků**

Název virtuální sítě = RMVNet <br>
Adresní prostor = 192.168.0.0/16 <br>
Skupina prostředků = RG1 <br>
Umístění = východní USA <br>
Název podsítě = Podsíť-1 <br>
Rozsah adres = 192.168.1.0/24 <br>
Podsíť gateway = 192.168.0.0/26 <br>
Název brány virtuální sítě = RMGateway <br>
Typ brány = VPN <br>
Typ VPN = založené na trase <br>
Skladová položka = VpnGw1 <br>
Umístění = východní USA <br>
Virtuální síť = RMVNet <br> (přidružit bránu VPN k této virtuální síti) První konfigurace IP = rmgwpip <br> (veřejná IP adresa brány) Brána místní sítě = ClassicVNetLocal <br>
Název připojení = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Přehled připojení

Pro tuto konfiguraci vytvoříte připojení brány VPN přes tunel ovou síť IPsec/IKE VPN mezi virtuálními sítěmi. Ujistěte se, že žádný z vašich rozsahů virtuální sítě překrývají mezi sebou, nebo s některou z místních sítí, které se připojují k.

Následující tabulka ukazuje příklad definice příkladu virtuálních sítí a místních webů:

| Virtual Network | Adresní prostor | Region (Oblast) | Připojení k lokalitě místní sítě |
|:--- |:--- |:--- |:--- |
| Klasický Virtuální síť |(10.0.0.0/24) |USA – západ | RMVNetLocal (192.168.0.0/16) |
| Síť RMVNet | (192.168.0.0/16) |USA – východ |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Oddíl 1 – Konfigurace klasického nastavení virtuální sítě

V této části vytvoříte klasickou virtuální síť, místní síť (místní lokalitu) a bránu virtuální sítě. Snímky obrazovek slouží jen jako příklady. Nezapomeňte nahradit hodnoty vlastními hodnotami nebo použít [příkladové](#values) hodnoty.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"> </a>Vytvoření klasické virtuální sítě

Pokud nemáte klasickou virtuální síť a spouštějíte tyto kroky jako cvičení, můžete vytvořit virtuální síť pomocí [tohoto článku](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a hodnoty [nastavení příkladu](#values) shora.

Pokud už virtuální síť s bránou VPN máte, ověřte, jestli je brána dynamická. Pokud je to statické, musíte nejprve odstranit bránu VPN, než přejdete na [Konfigurovat místní web](#local).

1. Otevřete [Azure Portal](https://ms.portal.azure.com) a přihlaste se pomocí svého účtu Azure.
2. Kliknutím **na + Vytvořit zdroj** otevřete stránku Nový.
3. Do pole Hledat na trhu zadejte "Virtuální síť". Pokud místo toho vyberete síť -> virtuální síť, nebudete mít možnost vytvořit klasickou virtuální síť.
4. Vyhledejte "Virtuální síť" ze vrácené ho seznamu a klepnutím na ni otevřete stránku Virtuální síť. 
5. Na stránce virtuální sítě vyberte "Classic", chcete-li vytvořit klasickou virtuální síť. Pokud zde použijete výchozí nastavení, místo toho skončíte s virtuální sítí Správce prostředků.

### <a name="2-configure-the-local-site"></a>2. <a name="local"> </a>Konfigurace místního webu

1. Přejděte na **všechny prostředky** a vyhledejte **classicvnet** v seznamu.
2. V části **Nastavení** v nabídce klikněte na **Gateway** a kliknutím na banner vytvořte bránu.
  ![Konfigurace brány VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Konfigurace brány VPN")
3. Na stránce **Nové připojení VPN** vyberte pro typ **připojení** **položku Site-to-site**.
4. V **části Místní web**klepněte na tlačítko Konfigurovat **požadovaná nastavení**. Otevře se stránka **Místní web.**
5. Na stránce **Místní web** vytvořte název, který bude odkazovat na virtuální síť Správce prostředků. Například "RMVNetLocal".
6. Pokud brána VPN pro virtuální síť Správce prostředků už má veřejnou IP adresu, použijte hodnotu pro pole **IP adresy brány VPN.** Pokud tyto kroky provádíte jako cvičení nebo ještě nemáte bránu virtuální sítě pro virtuální síť Správce prostředků, můžete vytvořit zástupnou IP adresu. Ujistěte se, že zástupná adresa IP používá platný formát. Později nahradíte zástupnou IP adresu veřejnou IP adresou brány virtuální sítě Resource Manageru.
7. Pro **adresní prostor klienta**použijte [hodnoty](#connectoverview) pro adresní prostory IP virtuální sítě pro virtuální síť Správce prostředků. Toto nastavení slouží k určení adresových prostorů, které mají být směrovány do virtuální sítě Resource Manager. V příkladu používáme 192.168.0.0/16, rozsah adres pro RMVNet.
8. Klepnutím na **tlačítko OK** uložte hodnoty a vraťte se na stránku Nové **připojení VPN.**

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. Vytvoření brány virtuální sítě

1. Na stránce **Nové připojení VPN** zaškrtněte políčko Vytvořit **bránu okamžitě.**
2. Kliknutím na **Volitelná konfigurace brány** otevřete stránku **Konfigurace brány**.

   ![Otevřít konfigurační stránku brány](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Otevřít konfigurační stránku brány")
3. Klikněte na **Podsíť – Konfigurace požadovaných nastavení** otevřete stránku **Přidat podsíť.** **Název** je již nakonfigurován s požadovanou hodnotou: **GatewaySubnet**.
4. **Rozsah Adresa** odkazuje na rozsah podsítě brány. I když můžete vytvořit podsíť brány s rozsahem /29 adres (3 adresy), doporučujeme vytvořit podsíť brány, která obsahuje více IP adres. To bude vyhovovat budoucím konfiguracím, které mohou vyžadovat více dostupných IP adres. Pokud je to možné, použijte /27 nebo /28. Pokud používáte tyto kroky jako cvičení, můžete odkazovat na [příklad hodnoty](#values). V tomto příkladu používáme '10.0.0.32/28'. Kliknutím na **OK** vytvořte podsíť brány.
5. Na **stránce konfigurace brány** **velikost** odkazuje na skladovou položku brány. Vyberte skladovou položku brány pro bránu VPN.
6. Ověřte, zda je **typ směrování** **dynamický**, a klepnutím na **tlačítko OK** se vraťte na stránku Nové **připojení VPN.**
7. Na stránce **Nové připojení VPN** klikněte na **OK** a začněte vytvářet bránu VPN. Dokončení vytváření brány sítě VPN může trvat až 45 minut.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. Kopírování veřejné IP adresy brány virtuální sítě

Po vytvoření brány virtuální sítě můžete zobrazit IP adresu brány. 

1. Přejděte na klasickou virtuální síť a klikněte na **Přehled**.
2. Kliknutím na **připojení VPN** otevřete stránku připojení VPN. Na stránce Připojení VPN můžete zobrazit veřejnou IP adresu. Jedná se o veřejnou IP adresu přiřazenou vaší bráně virtuální sítě. Poznamenejte si IP adresu. Používáte jej v pozdějších krocích při práci s nastavením konfigurace brány místní sítě Správce prostředků. 
3. Můžete zobrazit stav připojení brány. Všimněte si, že web místní sítě, který jste vytvořili, je uveden jako "Připojení". Stav se po vytvoření připojení změní. Po dokončení zobrazení stavu můžete tuto stránku zavřít.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Oddíl 2 – Konfigurace nastavení virtuální sítě Správce prostředků

V této části vytvoříte bránu virtuální sítě a bránu místní sítě pro virtuální síť Správce prostředků. Snímky obrazovek slouží jen jako příklady. Nezapomeňte nahradit hodnoty vlastními hodnotami nebo použít [příkladové](#values) hodnoty.

### <a name="1-create-a-virtual-network"></a>1. Vytvoření virtuální sítě

**Příklady hodnot:**

* Název virtuální sítě = RMVNet <br>
* Adresní prostor = 192.168.0.0/16 <br>
* Skupina prostředků = RG1 <br>
* Umístění = východní USA <br>
* Název podsítě = Podsíť-1 <br>
* Rozsah adres = 192.168.1.0/24 <br>

Pokud nemáte virtuální síť Správce prostředků a tyto kroky spouštěte jako cvičení, vytvořte virtuální síť s kroky v [části Vytvoření virtuální sítě](../virtual-network/quick-create-portal.md)pomocí ukázkových hodnot.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Vytvoření brány virtuální sítě

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Příklady hodnot:**

* Název brány virtuální sítě = RMGateway <br>
* Typ brány = VPN <br>
* Typ VPN = založené na trase <br>
* Skladová položka = VpnGw1 <br>
* Umístění = východní USA <br>
* Virtuální síť = RMVNet <br>
* Podsíť gateway = 192.168.0.0/26 <br>
* První konfigurace IP = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. Vytvoření brány místní sítě

**Příklady hodnot:** Brána místní sítě = ClassicVNetLocal

| Virtual Network | Adresní prostor | Region (Oblast) | Připojení k lokalitě místní sítě |Veřejná IP adresa brány|
|:--- |:--- |:--- |:--- |:--- |
| Klasický Virtuální síť |(10.0.0.0/24) |USA – západ | RMVNetLocal (192.168.0.0/16) |Veřejná IP adresa přiřazená k bráně ClassicVNet|
| Síť RMVNet | (192.168.0.0/16) |USA – východ |ClassicVNetLocal (10.0.0.0/24) |Veřejná IP adresa přiřazená k bráně RMVNet.|

Brána místní sítě určuje rozsah adres a veřejnou IP adresu přidruženou k vaší klasické virtuální síti a její bráně virtuální sítě. Pokud tyto kroky provádíte jako cvičení, podívejte se na příklad hodnoty.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Oddíl 3 – Změna klasického nastavení místního webu virtuální sítě

V této části nahradíte zástupnou adresu IP, kterou jste použili při zadávání nastavení místního webu, ip adresou brány VPN správce prostředků. Tato část používá klasické (SM) Rutiny prostředí PowerShell.

1. Na webu Azure Portal přejděte na klasickou virtuální síť.
2. Na stránce virtuální sítě klikněte na **Přehled**.
3. V části **Připojení VPN** klikněte v grafice na název místního webu.

   ![Připojení VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Připojení VPN")
4. Na stránce **Připojení VPN mezi webovými servery** klikněte na název webu.

   ![Název webu](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Název místního webu")
5. Na stránce připojení místního webu otevřete stránku Místní **web** kliknutím na název místního webu.

   ![Otevřený místní web](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Otevřít místní web")
6. Na stránce **Místní web** nahraďte IP adresu **brány VPN** IP IP adresou brány Správce prostředků.

   ![Brána-ip-adresa](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "IP adresa brány")
7. Chcete-li aktualizovat adresu IP, klepněte na tlačítko **OK.**

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Oddíl 4 - Vytvořit Správce prostředků pro klasické připojení

V těchto krocích nakonfigurujete připojení z virtuální sítě Správce prostředků ke klasické virtuální síti pomocí portálu Azure.

1. V **části Všechny prostředky**vyhledejte bránu místní sítě. V našem příkladu je brána místní sítě **ClassicVNetLocal**.
2. Klikněte na **Konfigurace** a ověřte, že hodnota IP adresy je bránou VPN pro klasickou virtuální síť. V případě potřeby aktualizujte a klepněte na tlačítko **Uložit**. Stránku zavřete.
3. V **části Všechny prostředky**klepněte na bránu místní sítě.
4. Kliknutím na **Připojení** otevřete stránku Připojení.
5. Na stránce **Připojení** **+** kliknutím přidáte připojení.
6. Na stránce **Přidat připojení** pojmenujte připojení. Například 'RMtoClassic'.
7. Na této stránce je již vybrána možnost **Site-to-Site.**
8. Vyberte bránu virtuální sítě, kterou chcete přidružit k tomuto webu.
9. Vytvořte **sdílený klíč**. Tento klíč se používá také v připojení, které vytvoříte z klasické virtuální sítě do virtuální sítě Správce prostředků. Můžete vygenerovat klíč nebo si ho vytvořit. V našem příkladu používáme 'abc123', ale můžete (a měli byste) použít něco složitějšího.
10. Chcete-li vytvořit připojení, klepněte na tlačítko **OK.**

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Oddíl 5 – Vytvoření klasického připojení správce prostředků

V těchto krocích nakonfigurujete připojení z klasické virtuální sítě k virtuální síti Správce prostředků. Tyto kroky vyžadují Prostředí PowerShell. Toto připojení nelze vytvořit na portálu. Ujistěte se, že jste stáhli a nainstalovali klasické rutiny prostředí PowerShell (SM) a Resource Manager (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Připojení ke svému účtu Azure

Otevřete konzolu PowerShell se zvýšenými právy a přihlaste se ke svému účtu Azure. Po přihlášení se stáhnou nastavení vašeho účtu, aby bylo dostupné pro Azure PowerShell. Následující rutina vás vyzve k zadání přihlašovacích údajů pro váš účet Azure pro model nasazení Správce prostředků:

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

Dále se přihlaste a použijte klasické rutiny prostředí PowerShell (Správa služeb). Pomocí následujícího příkazu přidejte svůj účet Azure pro klasický model nasazení:

```powershell
Add-AzureAccount
```

Získejte seznam svých předplatných. Tento krok může být nezbytné při přidávání rutin správy služeb, v závislosti na instalaci modulu Azure.

```powershell
Get-AzureSubscription
```

Pokud máte více než jedno předplatné, zadejte předplatné, které chcete použít.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Zobrazení hodnot konfiguračního souboru sítě

Když vytvoříte virtuální síť na webu Azure Portal, celý název, který Azure používá, není na webu Azure Portal viditelný. Například virtuální síť, která se na webu Azure Portal zobrazuje s názvem "ClassicVNet", může mít v konfiguračním souboru sítě mnohem delší název. Název může vypadat nějak takto: "Group ClassicRG ClassicVNet". V těchto krocích stáhnete konfigurační soubor sítě a zobrazíte hodnoty.

Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otevřete soubor s textovým editorem a zobrazte název klasické virtuální sítě. Při spuštění rutin prostředí PowerShell použijte názvy v konfiguračním síťovém souboru.

- Názvy virtuálních sítí jsou uvedeny jako **název webu VirtualNetworkSite =**
- Názvy webů jsou uvedeny jako **název LocalNetworkSite=**

### <a name="3-create-the-connection"></a>3. Vytvořte připojení

Nastavte sdílený klíč a vytvořte připojení z klasické virtuální sítě k virtuální síti Správce prostředků. Sdílený klíč nelze nastavit pomocí portálu. Ujistěte se, že spustíte tyto kroky při přihlášení pomocí klasické verze rutin prostředí PowerShell. Chcete-li tak učinit, použijte **Add-AzureAccount**. V opačném případě nebude možné nastavit '-AzureVNetGatewayKey'.

- V tomto příkladu **-VNetName** je název klasické virtuální sítě, jak se nachází v konfiguračním souboru sítě. 
- **-LocalNetworkSiteName** je název, který jste zadali pro místní lokalitu, jak je uvedeno v konfiguračním souboru sítě.
- **-SharedKey** je hodnota, kterou generujete a určujete. V tomto příkladu jsme použili *abc123*, ale můžete vytvořit něco složitějšího. Důležité je, že hodnota, kterou zde zadáte, musí být stejná hodnota, kterou jste zadali při vytváření Správce prostředků ke klasickému připojení.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Oddíl 6 - Ověření připojení

Připojení můžete ověřit pomocí portálu Azure nebo PowerShellu. Při ověřování může být nutné počkat minutu nebo dvě při vytváření připojení. Když je připojení úspěšné, stav připojení se změní z "Připojení" na "Připojeno".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Ověření připojení z klasické virtuální sítě k virtuální síti Správce prostředků

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Ověření připojení z virtuální sítě Správce prostředků ke klasické virtuální síti

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
