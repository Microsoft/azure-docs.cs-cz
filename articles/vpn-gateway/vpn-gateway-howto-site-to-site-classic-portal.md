---
title: 'Připojení místní sítě k virtuální síti Azure: Síť VPN typu Site-to-Site (Classic): Portál| Dokumentace Microsoftu'
description: Vytvořte připojení IPsec z vaší místní sítě k virtuální síti Azure Classic přes veřejný internet.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cherylmc
ms.openlocfilehash: e386e5fc9c4d62266e0ca23869bf30ccaffeb91d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244443"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Vytvoření připojení typu Site-to-Site pomocí webu Azure Portal (Classic)


Tento článek ukazuje, jak pomocí webu Azure Portal vytvořit připojení brány VPN typu Site-to-Site z místní sítě k virtuální síti. Kroky v tomto článku platí pro klasický model nasazení a nevztahují se na aktuální model nasazení Resource Manager. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Cli](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o bránách VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagram připojení VPN Gateway typu Site-to-Site mezi různými místy](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Ujistěte se, že chcete pracovat v modelu nasazení Classic. Pokud chcete pracovat v modelu nasazení Resource Manager, přečtěte si téma [Vytvoření připojení typu Site-to-Site (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Pokud je to možné, doporučujeme vám použít model nasazení Resource Manager.
* Ujistěte se, že máte kompatibilní zařízení VPN a někoho, kdo jej umí nakonfigurovat. Další informace o kompatibilních zařízeních VPN a konfiguraci zařízení najdete v tématu [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).
* Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN.
* Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže. Při vytváření této konfigurace musíte zadat předpony rozsahu IP adres, které bude Azure směrovat do vašeho místního umístění. Žádná z podsítí vaší místní sítě se nesmí překrývat s podsítěmi virtuální sítě, ke kterým se chcete připojit.
* PowerShell je nutné zadat sdílený klíč a vytvořit připojení brány VPN. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Ukázkové hodnoty konfigurace pro toto cvičení

V příkladech v tomto článku se používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

* **Název virtuální sítě:** TestVNet1
* **Adresní prostor:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (volitelné pro toto cvičení)
* **Podsítě:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (volitelné pro toto cvičení)
* **Podsíť brány:** 10.11.255.0/27
* **Skupina prostředků:** TestRG1
* **Umístění:** Východní USA
* **Server DNS:** 10.11.0.3 (volitelné pro toto cvičení)
* **Název místní lokality:** Site2
* **Klientský adresní prostor:** Adresní prostor umístěný ve vaší místní lokalitě.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Vytvoření virtuální sítě

Pokud vytváříte virtuální síť pro použití k připojení typu Site-to-Site, budete se muset ujistit, že se adresní prostory, které zadáváte, nepřekrývají s žádnými klientskými adresními prostory pro místní lokality, ke kterým se chcete připojit. Pokud se podsítě překrývají, připojení nebude fungovat správně.

* Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi. 

* Pokud ještě nemáte virtuální síť, vytvořte si ji. Snímky obrazovek slouží jen jako příklady. Hodnoty na obrázcích nahraďte vlastními hodnotami.

### <a name="to-create-a-virtual-network"></a>Chcete-li vytvořit virtuální síť

1. V prohlížeči přejděte na portál [Azure Portal](https://portal.azure.com) a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na **+Vytvořit zdroj*. Do pole **Hledat na trhu** zadejte "Virtuální síť". Vyhledejte **virtuální síť** ze vrácený seznam a klepnutím otevřete stránku **Virtuální síť.**
3. klepněte na tlačítko **(změnit na klasický)** a potom klepněte na **tlačítko Vytvořit**.
4. Na stránce **Vytvořit virtuální síť (Classic)** nakonfigurujte nastavení virtuální sítě. Na této stránce přidáte první adresní prostor a jeden rozsah adres podsítě. Po vytvoření virtuální sítě se můžete vrátit zpět a přidat další podsítě a adresní prostory.

   ![Stránka pro vytvoření virtuální sítě](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Stránka pro vytvoření virtuální sítě")
5. V rozevíracím seznamu **Předplatné** zkontrolujte, jestli je vybrané správné předplatné. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
6. Klikněte na **Skupina prostředků** a vyberte existující skupinu prostředků nebo zadáním názvu vytvořte novou. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../azure-resource-manager/management/overview.md#resource-groups).
7. Potom vyberte nastavení **Umístění** sítě VNet. Umístění určuje, kde budou uloženy prostředky nasazené v této síti VNet.
8. Kliknutím na **Vytvořit** vytvořte virtuální síť.
9. Po kliknutí na Vytvořit se na řídicím panelu zobrazí dlaždice, která zobrazuje postup vaší virtuální sítě. Obsah dlaždice se v průběhu vytváření sítě VNet mění.

## <a name="2-add-additional-address-space"></a><a name="additionaladdress"></a>2. Přidání dalšího adresního prostoru

Po vytvoření virtuální sítě můžete přidat další adresní prostor. Přidání dalšího adresního prostoru se nevyžaduje jako součást konfigurace Site-to-Site, pokud ale potřebujete více adresních prostorů, postupujte následovně:

1. Vyhledejte virtuální síť na portálu.
2. Na stránce pro virtuální síť v části **Nastavení** klikněte na **Adresní prostor**.
3. Na stránce Adresní prostor klikněte na **+Přidat** a zadejte další adresní prostor.

## <a name="3-specify-a-dns-server"></a><a name="dns"></a>3. Určení serveru DNS

Nastavení DNS se nevyžaduje jako součást konfigurace Site-to-Site, ale pokud chcete překlad IP adres, server DNS je nezbytný. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se připojujete. Pro příklad nastavení jsme použili privátní IP adresu. IP adresa, kterou používáme, pravděpodobně není IP adresa vašeho serveru DNS. Je potřeba, abyste použili svoje vlastní hodnoty.

Po vytvoření virtuální sítě můžete přidat IP adresu serveru DNS, aby bylo možné zpracovávat překlad názvů. Otevřete nastavení pro virtuální síť, klikněte na servery DNS a přidejte IP adresu serveru DNS, který chcete použít pro překlad IP adres.

1. Vyhledejte virtuální síť na portálu.
2. Na stránce pro virtuální síť v části **Nastavení** klikněte na **Servery DNS**.
3. Přidejte server DNS.
4. Uložte nastavení kliknutím na **Uložit** v horní části stránky.

## <a name="4-configure-the-local-site"></a><a name="localsite"></a>4. Konfigurace místního webu

Místní lokalita obvykle odkazuje na vaše místní umístění. Obsahuje IP adresu zařízení VPN, ke kterému vytvoříte připojení, a rozsah IP adres, které budou do tohoto zařízení VPN směrovány přes bránu VPN.

1. Na stránce virtuální sítě klikněte v části **Nastavení**na **Diagram**.
1. Na stránce **Připojení VPN** klikněte na Nemáte žádná existující **připojení VPN. Chcete-li začít, klepněte sem**.
1. U **typu Připojení**ponechejte vybrané informace o **webu.**
4. Kliknutím na **Místní lokalita – Konfigurovat požadované nastavení** otevřete stránku **Místní lokalita**. Nakonfigurujte nastavení a potom kliknutím na **OK** nastavení uložte.
   - **Název:** Vytvořte název pro místní lokalitu, abyste ji mohli snadno identifikovat.
   - **IP adresa brány VPN:** Toto je veřejná IP adresa zařízení VPN pro vaši místní síť. Zařízení VPN vyžaduje veřejnou IP adresu IPv4. Zadejte platnou veřejnou IP adresu pro zařízení VPN, ke kterému se chcete připojit. Musí být dosažitelný pomocí Azure. Pokud neznáte IP adresu zařízení VPN, pořád můžete použít zástupnou hodnotu (pokud je ve formátu platné veřejné IP adresy) a změnit ji později.
   - **Klientský adresní prostor:** Vypište rozsahy IP adres, které chcete přes tuto bránu směrovat do místní sítě. Můžete přidat více různých rozsahů adres. Ujistěte se, že se zde zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se vaše virtuální síť připojuje, nebo s rozsahy adres samotné virtuální sítě.

   ![Místní web](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Konfigurace místní lokality")

Klepnutím na **tlačítko OK** zavřete stránku Místní web. **Neklepejte na tlačítko OK, chcete-li zavřít stránku Nové připojení VPN**.

## <a name="5-configure-the-gateway-subnet"></a><a name="gatewaysubnet"></a>5. Konfigurace podsítě brány

Pro bránu VPN je nutné vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které bude používat služba brány VPN.


1. Na stránce **Nové připojení VPN** zaškrtněte políčko **Vytvořit bránu hned**. Zobrazí se stránka Volitelná konfigurace brány. Pokud políčko nezaškrtnete, stránka, na které můžete konfigurovat podsíť brány, se nezobrazí.

   ![Konfigurace brány – podsíť, velikost, typ směrování](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Konfigurace brány – podsíť, velikost, typ směrování")
2. Stránku **Konfigurace brány** otevřete kliknutím na **Volitelná konfigurace brány – Podsíť, velikost a typ směrování**.
3. Na stránce **Konfigurace brány** kliknutím na **Podsíť – Konfigurovat požadované nastavení** otevřete stránku **Přidat podsíť**. Po dokončení konfigurace těchto nastavení klepněte na tlačítko **OK**.

   ![Konfigurace brány – podsíť brány](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Konfigurace brány – podsíť brány")
4. Na stránce **Přidat podsíť** přidejte podsíť brány. Velikost podsítě brány, kterou zadáte, závisí na konfiguraci brány VPN, kterou chcete vytvořit. Přestože je možné vytvořit tak malou podsíť brány, jako je /29, doporučujeme použít /27 nebo /28. Vytvoří se tak vetší podsíť zahrnující více adres. Použitím větší podsítě brány zajistíte dostatek IP adres pro případné další konfigurace.

   ![Přidání podsítě brány](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Přidání podsítě brány")

## <a name="6-specify-the-sku-and-vpn-type"></a><a name="sku"></a>6. Zadejte typ skladové položky a sítě VPN

1. Vyberte **velikost** brány. Jde o hodnotu SKU, se kterou vytvoříte bránu virtuální sítě. Brány VPN Classic používají staré (starší) skladové položky brány. Další informace o starších skladových položkách brány najdete v tématu [Práce se skladovými položkami bran virtuálních sítí (staré skladové položky)](vpn-gateway-about-skus-legacy.md).

   ![Vyberte typ SKUL a VPN.](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Vybrat skladovou položku a typ VPN")
2. Vyberte pro bránu **typ směrování**. Označuje se také jako typ sítě VPN. Je důležité vybrat správný typ, protože bránu nelze převést z jednoho typu na jiný. Zařízení VPN musí být kompatibilní s typem směrování, který zvolíte. Další informace o typu směrování naleznete v [tématu O nastavení brány VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Můžete narazit na články, které odkazují na typy sítě VPN RouteBased a PolicyBased. Typ Dynamická odpovídá RouteBased a Statická odpovídá PolicyBased.
3. Kliknutím na **OK** uložte nastavení.
4. Na stránce **Nové připojení VPN** klikněte v dolní části stránky na **OK** a začněte nasazovat bránu virtuální sítě. V závislosti na vybrané skladové položce může vytvoření brány virtuální sítě trvat až 45 minut.

## <a name="7-configure-your-vpn-device"></a><a name="vpndevice"></a>7. Konfigurace zařízení VPN

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. V tomto kroku nakonfigurujete zařízení VPN. Při konfiguraci zařízení VPN potřebujete následující:

- Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
- Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="8-create-the-connection"></a><a name="CreateConnection"></a>8. Vytvořte připojení
V tomto kroku nastavíte sdílený klíč a vytvoříte připojení. Klíč, který nastavíte, musí být stejný jako klíč, který jste použili v konfiguraci zařízení VPN.

> [!NOTE]
> Tento krok v současné době není k dispozici na webu Azure Portal. Musíte použít verzi rutin Azure PowerShellu pro správu služeb. Informace o instalaci těchto rutin naleznete [v tématu Before you Begin.](#before)
>

### <a name="step-1-connect-to-your-azure-account"></a>Krok 1. Připojení k účtu Azure

Tyto příkazy je nutné spustit místně pomocí modulu správy služeb prostředí PowerShell. 

1. Otevřete konzolu PowerShell se zvýšenými právy. Chcete-li přepnout na správu služeb, použijte tento příkaz:

   ```powershell
   azure config mode asm
   ```
2. Připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

   ```powershell
   Add-AzureAccount
   ```
3. Zkontrolujte předplatná pro příslušný účet.

   ```powershell
   Get-AzureSubscription
   ```
4. Máte-li více předplatných, vyberte předplatné, které chcete použít.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Krok 2. Nastavení sdíleného klíče a vytvoření připojení

Když na portálu vytvoříte klasickou virtuální síť (nepoužíváte PowerShell), Azure přidá název skupiny prostředků ke krátkému názvu. Například podle Azure je název virtuální sítě, kterou jste vytvořili pro toto cvičení "Skupina TestRG1 TestVNet1", ne "TestVNet1". Prostředí PowerShell vyžaduje úplný název virtuální sítě, nikoli krátký název, který se zobrazí na portálu. Dlouhý název není na portálu viditelný. Následující kroky vám pomohou exportovat konfigurační soubor sítě a získat přesné hodnoty pro název virtuální sítě. 

1. Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Otevřete soubor konfigurace sítě v editoru XML a zkontrolujte hodnoty „LocalNetworkSite name“ (Název místní síťové lokality) a „VirtualNetworkSite name“ (Název virtuální síťové lokality). Upravte příklad pro toto cvičení tak, aby odrážel hodnoty ve formátu XML. Pokud zadáváte název, který obsahuje mezery, zadejte hodnotu v jednoduchých uvozovkách.

3. Nastavte sdílený klíč a vytvořte připojení. Hodnota, kterou generujete a zadáváte, je „-SharedKey“. V příkladu jsme použili „abc123“, ale můžete (a měli byste) vygenerovat něco složitějšího. Důležité je, aby hodnota, kterou zde zadáte, byla stejná jako hodnota, kterou jste zadali při konfiguraci zařízení VPN.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   Jakmile se připojení vytvoří, výsledkem bude: **Stav: Úspěch**.

## <a name="9-verify-your-connection"></a><a name="verify"></a>9. Ověření připojení

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Pokud máte potíže s připojením, podívejte se do části **Řešení potíží** obsahu v levém podokně.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Resetování brány VPN

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Pokyny najdete v tématu [Resetování brány VPN](vpn-gateway-resetgw-classic.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Změna skladové položky brány

Postup pro změnu skladové položky brány najdete v tématu [Změna velikosti skladové položky brány](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Další kroky

* Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/).
* Informace o vynuceném tunelování naleznete v tématu [O vynuceném tunelování](vpn-gateway-about-forced-tunneling.md).
