---
title: 'Připojení místní sítě k virtuální síti Azure: Síť VPN typu Site-to-Site (Classic): Portál| Dokumentace Microsoftu'
description: Vytvořte připojení IPsec z vaší místní sítě k virtuální síti Azure Classic přes veřejný internet.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4ad05281f13885327c855a261a3101388f38af83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878049"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Vytvoření připojení typu Site-to-Site pomocí webu Azure Portal (Classic)

Tento článek ukazuje, jak pomocí webu Azure Portal vytvořit připojení brány VPN typu Site-to-Site z místní sítě k virtuální síti. Kroky v tomto článku se vztahují na klasický model nasazení a nevztahují se na aktuální model nasazení Správce prostředků. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Rozhraní příkazového řádku](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o bránách VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagram připojení VPN Gateway typu Site-to-Site mezi různými místy](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Ujistěte se, že chcete pracovat v modelu nasazení Classic. Pokud chcete pracovat v modelu nasazení Resource Manager, přečtěte si téma [Vytvoření připojení typu Site-to-Site (Resource Manager)](./tutorial-site-to-site-portal.md). Doporučujeme použít model nasazení Správce prostředků, protože klasický model je starší verze.
* Ujistěte se, že máte kompatibilní zařízení VPN a někoho, kdo jej umí nakonfigurovat. Další informace o kompatibilních zařízeních VPN a konfiguraci zařízení najdete v tématu [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).
* Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN.
* Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže. Při vytváření této konfigurace musíte zadat předpony rozsahu IP adres, které bude Azure směrovat do vašeho místního umístění. Žádná z podsítí vaší místní sítě se nesmí překrývat s podsítěmi virtuální sítě, ke kterým se chcete připojit.
* Aby bylo možné zadat sdílený klíč a vytvořit připojení brány VPN, je nutné PowerShell. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Ukázkové hodnoty konfigurace pro toto cvičení

V příkladech v tomto článku se používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku. Při práci s hodnotami IP adres pro adresní prostor se obvykle chcete spojit se správcem sítě, aby se předešlo překrývání adresních prostorů, které mohou ovlivnit směrování. V takovém případě nahraďte hodnoty IP adresy vlastními, pokud chcete vytvořit pracovní připojení.

* **Skupina prostředků:** TestRG1
* **Název virtuální sítě:** TestVNet1
* **Adresní prostor:** 10.11.0.0/16
* **Název podsítě:** FrontEnd
* **Rozsah adres podsítě:** 10.11.0.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Oblast:** (US) východní USA
* **Název místní lokality:** Site2
* **Klientský adresní prostor:** Adresní prostor umístěný ve vaší místní lokalitě.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Vytvoření virtuální sítě

Pokud vytváříte virtuální síť pro použití k připojení typu Site-to-Site, budete se muset ujistit, že se adresní prostory, které zadáváte, nepřekrývají s žádnými klientskými adresními prostory pro místní lokality, ke kterým se chcete připojit. Pokud se podsítě překrývají, připojení nebude fungovat správně.

* Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi.

* Pokud ještě nemáte virtuální síť, vytvořte si ji. Snímky obrazovek slouží jen jako příklady. Hodnoty na obrázcích nahraďte vlastními hodnotami.

### <a name="to-create-a-virtual-network"></a>Chcete-li vytvořit virtuální síť

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>Konfigurace lokality a brány

### <a name="to-configure-the-site"></a>Konfigurace lokality

Místní lokalita obvykle odkazuje na vaše místní umístění. Obsahuje IP adresu zařízení VPN, ke kterému vytvoříte připojení, a rozsah IP adres, které budou do tohoto zařízení VPN směrovány přes bránu VPN.

1. Na stránce pro virtuální síť v části **Nastavení** vyberte **připojení Site-to-site**.
1. Na stránce připojení Site-to-site vyberte **+ Přidat**.
1. Na stránce **Konfigurovat připojení k síti VPN a bránu** pro **Typ připojení** ponechte vybranou možnost **site-to-site** . Pro účely tohoto cvičení budete muset použít kombinaci [ukázkových hodnot](#values) a vašich vlastních hodnot.

   * **IP adresa brány VPN:** Toto je veřejná IP adresa zařízení VPN pro vaši místní síť. Zařízení VPN vyžaduje veřejnou IP adresu IPv4. Zadejte platnou veřejnou IP adresu pro zařízení VPN, ke kterému se chcete připojit. Musí být dosažitelný pomocí Azure. Pokud neznáte IP adresu zařízení VPN, pořád můžete použít zástupnou hodnotu (pokud je ve formátu platné veřejné IP adresy) a změnit ji později.

   * **Klientský adresní prostor:** Vypište rozsahy IP adres, které chcete přes tuto bránu směrovat do místní sítě. Můžete přidat více různých rozsahů adres. Ujistěte se, že se zde zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se vaše virtuální síť připojuje, nebo s rozsahy adres samotné virtuální sítě.
1. V dolní části stránky nevybírejte možnost zkontrolovat + vytvořit. Místo toho vyberte **Další:>brány**.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>Konfigurace brány virtuální sítě

1. Na stránce **Brána** vyberte následující hodnoty:

   * **Velikost:** Toto je SKU brány, které použijete k vytvoření brány virtuální sítě. Brány VPN Classic používají staré (starší) skladové položky brány. Další informace o starších skladových položkách brány najdete v tématu [Práce se skladovými položkami bran virtuálních sítí (staré skladové položky)](vpn-gateway-about-skus-legacy.md). Pro toto cvičení můžete vybrat **standardní** .

   * **Typ směrování:** Vyberte typ směrování pro bránu. Označuje se také jako typ sítě VPN. Je důležité vybrat správný typ, protože bránu nemůžete převést z jednoho typu na jiný. Zařízení VPN musí být kompatibilní s typem směrování, který zvolíte. Další informace o typu směrování najdete v tématu [informace o nastaveních VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Můžete narazit na články, které odkazují na typy sítě VPN RouteBased a PolicyBased. Typ Dynamická odpovídá RouteBased a Statická odpovídá PolicyBased. Obvykle budete chtít dynamické směrování.

   * **Podsíť brány:** Velikost podsítě brány, kterou určíte, závisí na konfiguraci brány VPN, kterou chcete vytvořit. Přestože je možné vytvořit tak malou podsíť brány, jako je /29, doporučujeme použít /27 nebo /28. Vytvoří se tak vetší podsíť zahrnující více adres. Použitím větší podsítě brány zajistíte dostatek IP adres pro případné další konfigurace.

1. V dolní části stránky vyberte **zkontrolovat + vytvořit** a ověřte nastavení. Vyberte **vytvořit** k nasazení. Vytvoření brány virtuální sítě může trvat až 45 minut v závislosti na vybrané skladové jednotce brány.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>Konfigurace zařízení VPN

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. V tomto kroku nakonfigurujete zařízení VPN. Při konfiguraci zařízení VPN budete potřebovat následující hodnoty:

* Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
* Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Načíst hodnoty

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>Vytvoření připojení

> [!NOTE]
> Pro model nasazení Classic není tento krok k dispozici v Azure Portal nebo prostřednictvím Azure Cloud Shell. Je nutné použít verzi rutin Azure PowerShell Service Management (SM) místně z plochy.
>

V tomto kroku nastavíte sdílený klíč a vytvoříte připojení pomocí hodnot z předchozích kroků. Klíč, který nastavíte, musí být stejný jako klíč, který jste použili v konfiguraci zařízení VPN.

1. Nastavte sdílený klíč a vytvořte připojení.

   * Změňte hodnotu-VNetName a hodnotu-LocalNetworkSiteName. Pokud zadáváte název, který obsahuje mezery, zadejte hodnotu v jednoduchých uvozovkách.
   * '-SharedKey ' je hodnota, kterou vygenerujete a pak zadáte. V tomto příkladu jsme použili "abc123", ale můžete (a měli byste) generovat něco složitějšího. Důležité je, aby hodnota, kterou zde zadáte, byla stejná jako hodnota, kterou jste zadali při konfiguraci zařízení VPN.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. Jakmile se připojení vytvoří, výsledkem bude: **Stav: Úspěch**.

## <a name="verify-your-connection"></a><a name="verify"></a>Ověření připojení

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Pokud máte potíže s připojením, podívejte se do části **Řešení potíží** obsahu v levém podokně.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Resetování brány VPN

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Pokyny najdete v tématu [Resetování brány VPN](./reset-gateway.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Změna skladové položky brány

Postup změny SKU brány najdete v tématu [Změna velikosti skladové položky brány](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Další kroky

* Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](../index.yml).
* Informace o vynuceném tunelování najdete v tématu [o vynuceném tunelování](vpn-gateway-about-forced-tunneling.md).