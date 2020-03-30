---
title: 'Vytvoření připojení mezi virtuálními sítěmi: klasický: Portál Azure'
description: Propojte virtuální sítě Azure pomocí PowerShellu a portálu Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 63c6329ad62289cd127902c1438073b28fc8683e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201845"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurace připojení virtuální sítě k virtuální síti (klasické)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Tento článek vám pomůže vytvořit připojení brány VPN mezi virtuálními sítěmi. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných. Kroky v tomto článku platí pro klasický model nasazení a portál Azure. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagram připojení virtuální sítě k virtuální síti](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>Informace o propojeních VNet-to-VNet

Připojení virtuální sítě k jiné virtuální síti (Virtuální síť k virtuální síti) v klasickém modelu nasazení pomocí brány VPN je podobné připojení virtuální sítě k místnímu umístění lokality. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE.

Virtuální sítě, které připojíte, můžou být v různých předplatných a různých oblastech. Komunikaci virtuální sítě s virtuální sítí můžete kombinovat s konfiguracemi s více lokalitami. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí.

![Připojení virtuální sítě k virtuální síti](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Proč propojovat virtuální sítě?

Virtuální sítě může být vhodné propojit z následujících důvodů:

* **Geografická redundance a geografická přítomnost mezi oblastmi**

  * Můžete nastavit vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením bez procházení koncovými body připojenými k internetu.
  * S Azure Load Balancer a Microsoft nebo clustering technologie třetích stran, můžete nastavit vysoce dostupné úlohy s geografickou redundanci ve více oblastech Azure. Jedním z důležitých příkladů je nastavení technologie SQL Always On se skupinami dostupnosti nad několika oblastmi Azure.
* **Regionální vícevrstvé aplikace se silnou hranicí izolace**

  * V rámci stejné oblasti můžete nastavit vícevrstvé aplikace s více virtuálními sítěmi propojenými se silnou izolací a zabezpečenou mezivrstvou komunikací.
* **Křížové předplatné, meziorganizacení komunikace v Azure**

  * Pokud máte více předplatných Azure, můžete bezpečně připojit úlohy z různých předplatných mezi virtuálními sítěmi.
  * Pro podniky nebo poskytovatele služeb můžete povolit komunikaci mezi organizacemi se zabezpečenou technologií VPN v rámci Azure.

Další informace o propojeních VNet-to-VNet najdete v části [Aspekty propojení VNet-to-VNet](#faq) na konci tohoto článku.

### <a name="working-with-azure-powershell"></a><a name="powershell"></a>Práce s Azure PowerShellem

Portál používáme pro většinu kroků, ale k vytvoření připojení mezi virtuálními sítěmi je nutné použít prostředí PowerShell. Připojení nelze vytvořit pomocí portálu Azure. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>Krok 1: Plánování rozsahů IP adres

Je důležité rozhodnout rozsahy, které budete používat ke konfiguraci virtuálních sítí. Pro tuto konfiguraci se musíte ujistit, že se žádná z rozsahů virtuální sítě nepřekrývá mezi sebou nebo s žádnou z místních sítí, ke které se připojují.

V následující tabulce je uveden příklad, jak definovat virtuální sítě. Rozsahy používejte pouze jako vodítko. Poznamenejte si rozsahy pro vaše virtuální sítě. Tyto informace potřebujete pro pozdější kroky.

**Příklad**

| Virtual Network | Adresní prostor | Region (Oblast) | Připojení k lokalitě místní sítě |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA – východ |Virtuální síť 4Místní<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA – západ |Virtuální síť 1 Místní<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>Krok 2 – Vytvoření virtuálních sítí

Vytvořte dvě virtuální sítě na [webu Azure Portal](https://portal.azure.com). Postup vytvoření klasických virtuálních sítí najdete [v tématu Vytvoření klasické virtuální sítě](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Při použití portálu k vytvoření klasické virtuální sítě, musíte přejít na stránku virtuální sítě pomocí následujících kroků, jinak možnost vytvořit klasickou virtuální síť se nezobrazí:

1. Kliknutím na '+' otevřete stránku "Nový".
2. Do pole Hledat na trhu zadejte "Virtuální síť". Pokud místo toho vyberete síť -> virtuální síť, nebudete mít možnost vytvořit klasickou virtuální síť.
3. Vyhledejte "Virtuální síť" ze vrácené ho seznamu a klepnutím na ni otevřete stránku Virtuální síť. 
4. Na stránce virtuální sítě vyberte "Classic", chcete-li vytvořit klasickou virtuální síť. 

Pokud používáte tento článek jako cvičení, můžete použít následující příklad hodnoty:

**Hodnoty pro TestVNet1**

Název: TestVnet1<br>
Adresní prostor: 10.11.0.0/16, 10.12.0.0/16 (volitelně)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.11.0.1/24<br>
Skupina prostředků: ClassicRG<br>
Umístění: USA – východ<br>
Podsíť brány: 10.11.1.0/27

**Hodnoty pro TestVNet4**

Název: TestVNet4<br>
Adresní prostor: 10.41.0.0/16, 10.42.0.0/16 (volitelně)<br>
Název podsítě: výchozí<br>
Rozsah adres podsítě: 10.41.0.1/24<br>
Skupina prostředků: ClassicRG<br>
Umístění: USA – západ<br>
Podsíť brány: 10.41.1.0/27

**Při vytváření virtuálních sítí mějte na paměti následující nastavení:**

* **Adresní prostory virtuální sítě** – Na stránce Adresní prostory virtuální sítě zadejte rozsah adres, který chcete použít pro virtuální síť. Jedná se o dynamické IP adresy, které budou přiřazeny k virtuálním počítačům a dalším instancím rolí, které nasadíte do této virtuální sítě.<br>Adresní prostory, které vyberete, se nemohou překrývat s adresními prostory pro žádné jiné virtuální sítě nebo místní umístění, ke kterým se tato virtuální síť připojí.

* **Umístění** – při vytváření virtuální sítě ji přidružíte k umístění Azure (oblast). Pokud například chcete, aby vaše virtuální počítače, které jsou nasazené do vaší virtuální sítě, byly fyzicky umístěné v západní USA, vyberte toto umístění. Po vytvoření virtuální sítě nelze změnit umístění přidružené k vaší virtuální síti.

**Po vytvoření virtuálních sítí můžete přidat následující nastavení:**

* **Adresní prostor** – pro tuto konfiguraci není vyžadován další adresní prostor, ale po vytvoření virtuální sítě můžete přidat další adresní prostor.

* **Podsítě** – pro tuto konfiguraci nejsou vyžadovány další podsítě, ale můžete chtít mít virtuální počítače v podsíti, která je oddělená od ostatních instancí role.

* **Servery DNS** – Zadejte název serveru DNS a adresu IP. Toto nastavení neslouží k vytvoření serveru DNS. Umožňuje určit server DNS, který chcete použít pro překlad názvů pro tuto virtuální síť.

V této části nakonfigurujete typ připojení, místní lokalitu a vytvoříte bránu.

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>Krok 3 – Konfigurace místního webu

Azure používá nastavení zadaná v každé lokalitě místní sítě k určení, jak směrovat provoz mezi virtuálními sítěmi. Každá virtuální síť musí ukazovat na příslušnou místní síť, do které chcete směrovat provoz. Určíte název, který chcete použít k odkazování na jednotlivé lokality místní sítě. Nejlepší je použít něco popisného.

Například TestVNet1 se připojí k lokalitě místní sítě, kterou vytvoříte s názvem "VNet4Local". Nastavení pro VNet4Local obsahují předpony adresy pro TestVNet4.

Místní lokalita pro každou virtuální síť je další virtuální síť. Pro naši konfiguraci se používají následující ukázkové hodnoty:

| Virtual Network | Adresní prostor | Region (Oblast) | Připojení k lokalitě místní sítě |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |USA – východ |Virtuální síť 4Místní<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |USA – západ |Virtuální síť 1 Místní<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Vyhledejte TestVNet1 na webu Azure Portal. V části **Připojení VPN** na stránce klikněte na **Gateway**.

    ![Žádná brána](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na stránce **Nové připojení VPN** vyberte Možnost **Site-to-Site**.
3. Kliknutím na **Místní web** otevřete stránku Místní web a nakonfigurujte nastavení.
4. Na stránce **Místní web** pojmenujte místní web. V našem příkladu pojmenujeme místní web "VNet4Local".
5. Pro **IP adresu brány VPN**můžete použít libovolnou IP adresu, pokud je v platném formátu. Obvykle byste použili skutečnou externí IP adresu pro zařízení VPN. Ale pro klasickou konfiguraci virtuální sítě k virtuální síti použijete veřejnou IP adresu, která je přiřazená k bráně pro vaši virtuální síť. Vzhledem k tomu, že jste ještě nevytvořili bránu virtuální sítě, zadáte jako zástupný symbol libovolnou platnou veřejnou IP adresu.<br>Nenechávejte to toto prázdné – pro tuto konfiguraci není volitelné. V pozdějším kroku se vrátíte do těchto nastavení a nakonfigurujete je s odpovídajícími IP adresami brány virtuální sítě, jakmile je Azure vygeneruje.
6. Pro **adresní prostor klienta**použijte adresní prostor jiné virtuální sítě. Podívejte se na příklad plánování. Kliknutím na **OK** uložte nastavení a vraťte se zpět na stránku **Nové připojení VPN.**

    ![místní web](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>Krok 4 – Vytvoření brány virtuální sítě

Každá virtuální síť musí mít bránu virtuální sítě. Brána virtuální sítě směruje a šifruje provoz.

1. Na stránce **Nové připojení VPN** zaškrtněte políčko **Vytvořit bránu hned**.
2. Klepněte na **položku Podsíť, velikost a typ směrování**. Na stránce **Konfigurace brány** klikněte na **Podsíť**.
3. Název podsítě brány je vyplněn automaticky s požadovaným názvem GatewaySubnet. **Rozsah Adresa** obsahuje IP adresy, které jsou přiděleny službám brány VPN. Některé konfigurace umožňují podsíť brány /29, ale je nejlepší použít /28 nebo /27 pro budoucí konfigurace, které mohou vyžadovat více IP adres pro služby brány. V našem příkladu nastavení používáme 10.11.1.0/27. Upravte adresní prostor a klepněte na tlačítko **OK**.
4. Nakonfigurujte **velikost brány**. Toto nastavení odkazuje na [skladovou položku brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Nakonfigurujte **typ směrování**. Typ směrování pro tuto konfiguraci musí být **Dynamický**. Typ směrování nelze později změnit, pokud bránu nestrhnete a nevytvoříte novou.
6. Klikněte na tlačítko **OK**.
7. Na stránce **Nové připojení VPN** klikněte na **OK** a začněte vytvářet bránu virtuální sítě. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>Krok 5 – Konfigurace nastavení testu VNet4

Opakujte kroky k [vytvoření místní lokality](#localsite) a [vytvoření brány virtuální sítě](#gw) pro konfiguraci testvnet4, v případě potřeby nahradit hodnoty. Pokud to provádíte jako cvičení, použijte [příklad hodnoty](#vnetvalues).

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>Krok 6 – Aktualizace místních webů

Po vytvoření bran virtuální sítě pro obě virtuální sítě je nutné upravit hodnoty **IP adres brány brány VPN** v místních lokalitách.

|Název virtuální sítě|Připojená lokalita|IP adresa brány|
|:--- |:--- |:--- |
|TestVNet1|Virtuální síť 4Místní|IP adresa brány VPN pro TestVNet4|
|TestVNet4|Virtuální síť 1 Místní|IP adresa brány VPN pro TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Část 1 – Získání veřejné IP adresy brány virtuální sítě

1. Vyhledejte virtuální síť na webu Azure Portal.
2. Klepnutím otevřete stránku **Přehled** virtuální sítě. Na stránce můžete v **připojení VPN**zobrazit IP adresu brány virtuální sítě.

   ![Veřejná IP adresa](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Zkopírujte adresu IP. Budete jej používat v další části.
4. Opakujte tyto kroky pro TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Část 2 - Změna místních webů

1. Vyhledejte virtuální síť na webu Azure Portal.
2. Na stránce **Přehled** virtuální sítě klikněte na místní web.

   ![Místní web vytvořen](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na stránce **Připojení VPN mezi lokalitami** klikněte na název místního webu, který chcete upravit.

   ![Otevřít místní web](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Klikněte na **místní web,** který chcete upravit.

   ![upravit web](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Aktualizujte **IP adresu brány VPN** a kliknutím na **OK** nastavení uložte.

   ![IP brána](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zavřete ostatní stránky.
7. Opakujte tyto kroky pro TestVNet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>Krok 7 – Načtení hodnot z konfiguračního síťového souboru

Když na webu Azure Portal vytvoříte klasické virtuální sítě, název, který si zobrazíte, není úplný název, který používáte pro PowerShell. Například virtuální síť, která se na portálu jmenuje **TestVNet1,** může mít v konfiguračním souboru sítě mnohem delší název. Název může vypadat podobně: **Skupina ClassicRG TestVNet1**. Při vytváření připojení je důležité použít hodnoty, které se zobrazují v konfiguračním souboru sítě.

V následujících krocích se připojíte ke svému účtu Azure a stáhnete a zobrazíte konfigurační soubor sítě, abyste získali hodnoty, které jsou pro vaše připojení vyžadovány.

1. Stáhněte a nainstalujte nejnovější verzi rutin prostředí PowerShell pro správu služeb Azure (SM). Další informace najdete [v tématu Práce s Azure PowerShell](#powershell).

2. Otevřete konzolu PowerShell se zvýšenými právy. Použijte následující příklady, které vám pomohou připojit. Tyto příkazy je nutné spustit místně pomocí modulu správy služeb prostředí PowerShell. Chcete-li přepnout na správu služeb, použijte tento příkaz:

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
6. Exportaje a zobrazí konfigurační soubor sítě. Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu je konfigurační síťový soubor exportován do **sítě C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Otevřete soubor s textovým editorem a zobrazte názvy virtuálních sítí a webů. Tyto názvy budou názvy, které použijete při vytváření připojení.<br>Názvy virtuálních sítí jsou uvedeny jako **název webu VirtualNetworkSite =**<br>Názvy webů jsou uvedeny jako **název LocalNetworkSiteRef =**

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>Krok 8 – Vytvoření připojení brány VPN

Po dokončení všech předchozích kroků můžete nastavit předsdílené klíče IPsec/IKE a vytvořit připojení. Tato sada kroků používá Prostředí PowerShell. Připojení virtuální sítě k virtuální síti pro klasický model nasazení nelze nakonfigurovat na webu Azure Portal.

V příkladech všimněte si, že sdílený klíč je přesně stejný. Sdílený klíč se musí vždy shodovat. Nezapomeňte nahradit hodnoty v těchto příkladech přesnými názvy virtuálních sítí a lokalit místní sítě.

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
3. Počkejte na inicializaci připojení. Po inicializování brány je stav "Úspěšný".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>Důležité informace o virtuální síti pro virtuální sítě pro klasické virtuální sítě
* Virtuální sítě může být ve stejném nebo různých předplatných.
* Virtuální sítě se můžou nacházet ve stejné oblasti (umístění) Azure nebo v různých oblastech.
* Cloudová služba nebo koncový bod vyrovnávání zatížení se nemůže rozprostírat napříč virtuálními sítěmi, i když jsou propojeny.
* Připojení více virtuálních sítí dohromady nevyžaduje žádná zařízení VPN.
* Virtuální síť k virtuální síti podporuje připojení virtuálních sítí Azure. Nepodporuje připojení virtuálních počítačů nebo cloudových služeb, které nejsou nasazeny do virtuální sítě.
* Síť virtuální sítě k virtuální síti vyžaduje dynamické brány směrování. Statické směrovací brány Azure nejsou podporovány.
* Možnost připojení k virtuální síti je možné využívat současně se sítěmi VPN s více servery. Existuje maximálně 10 tunelových propojení VPN pro bránu VIRTUÁLNÍ SÍTĚ VPN, která se připojuje k jiným virtuálním sítím nebo místním webům.
* Adresní prostory virtuálních sítí a místních serverů místních sítí se nesmějí překrývat. Překrývající se adresní prostory způsobí selhání vytváření virtuálních sítí nebo nahrávání konfiguračních souborů netcfg.
* Redundantní tunelová propojení mezi dvěma virtuálními sítěmi nejsou podporována.
* Všechny tunely VPN pro virtuální síť, včetně VPN P2S, sdílejí dostupnou šířku pásma pro bránu VPN a stejnou dostupnost dostupnosti brány VPN v Azure.
* Provoz virtuální sítě na virtuální síť se pohybuje přes páteřní síť Azure.

## <a name="next-steps"></a>Další kroky
Ověřte stav připojení. Viz [Ověření připojení brány VPN](vpn-gateway-verify-connection-resource-manager.md).
