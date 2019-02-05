---
title: 'Připojení místní sítě ke službě Azure virtual network: Síť Site-to-Site VPN (classic): Portál | Dokumentace Microsoftu'
description: Vytvořte připojení IPsec z vaší místní sítě k virtuální síti Azure Classic přes veřejný internet.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: b0fa60d709c2fa6c286e44797d53e8a4a8d47d00
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695600"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Vytvoření připojení typu Site-to-Site pomocí webu Azure Portal (Classic)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Tento článek ukazuje, jak pomocí webu Azure Portal vytvořit připojení brány VPN typu Site-to-Site z místní sítě k virtuální síti. Postupy v tomto článku se týkají modelu nasazení Classic. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Rozhraní příkazového řádku](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Připojení brány VPN typu Site-to-Site slouží k připojení místní sítě k virtuální síti Azure přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o bránách VPN najdete v tématu [Informace o službě VPN Gateway](vpn-gateway-about-vpngateways.md).

![Diagram připojení VPN Gateway typu Site-to-Site mezi různými místy](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Před zahájením

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Ujistěte se, že chcete pracovat v modelu nasazení Classic. Pokud chcete pracovat v modelu nasazení Resource Manager, přečtěte si téma [Vytvoření připojení typu Site-to-Site (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Pokud je to možné, doporučujeme vám použít model nasazení Resource Manager.
* Ujistěte se, že máte kompatibilní zařízení VPN a někoho, kdo jej umí nakonfigurovat. Další informace o kompatibilních zařízeních VPN a konfiguraci zařízení najdete v tématu [Informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).
* Ověřte, že máte veřejnou IPv4 adresu pro vaše zařízení VPN. Tato IP adresa nesmí být umístěná za překladem adres (NAT).
* Pokud neznáte rozsahy IP adres v konfiguraci vaší místní sítě, budete se muset spojit s někým, kdo vám s tím pomůže. Při vytváření této konfigurace musíte zadat předpony rozsahu IP adres, které bude Azure směrovat do vašeho místního umístění. Žádná z podsítí vaší místní sítě se nesmí překrývat s podsítěmi virtuální sítě, ke kterým se chcete připojit.
* V současné době je se k zadání sdíleného klíče a vytvoření připojení brány VPN vyžaduje PowerShell. Nainstalujte nejnovější verzi rutin PowerShellu pro správu služeb Azure. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Při práci s prostředím PowerShell pro tuto konfiguraci se ujistěte, že jej spouštíte jako správce. 

### <a name="values"></a>Ukázkové hodnoty konfigurace pro toto cvičení

V příkladech v tomto článku se používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku.

* **Název virtuální sítě:** TestVNet1
* **Adresní prostor:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (volitelné pro toto cvičení)
* **Podsítě:**
  * Front-endu: 10.11.0.0/24
  * Back-endu: 10.12.0.0/24 (volitelné pro toto cvičení)
* **GatewaySubnet:** 10.11.255.0/27
* **Skupina prostředků:** TestRG1
* **Umístění:** USA – východ
* **DNS Server:** 10.11.0.3 (volitelné pro toto cvičení)
* **Název místní lokality:** Site2
* **Klientský adresní prostor:** Adresní prostor, který je umístěný ve vaší místní lokalitě.

## <a name="CreatVNet"></a>1. Vytvoření virtuální sítě

Pokud vytváříte virtuální síť pro použití k připojení typu Site-to-Site, budete se muset ujistit, že se adresní prostory, které zadáváte, nepřekrývají s žádnými klientskými adresními prostory pro místní lokality, ke kterým se chcete připojit. Pokud se podsítě překrývají, připojení nebude fungovat správně.

* Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi. 

* Pokud ještě nemáte virtuální síť, vytvořte si ji. Snímky obrazovek slouží jen jako příklady. Nezapomeňte hodnoty nahradit vlastními.

### <a name="to-create-a-virtual-network"></a>Chcete-li vytvořit virtuální síť

1. V prohlížeči přejděte na portál [Azure Portal](http://portal.azure.com) a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na **+**. Do pole **Hledat na Marketplace** zadejte text „Virtuální síť“. Ve vráceném seznamu vyhledejte položku **Virtuální síť** a kliknutím otevřete stránku **Virtuální síť**.

  ![Stránka pro vyhledání virtuální sítě](./media/vpn-gateway-howto-site-to-site-classic-portal/newvnetportal700.png)
3. U dolního okraje stránky Virtuální síť v rozevíracím seznamu **Vybrat model nasazení** vyberte **Classic** a potom klikněte na **Vytvořit**.

  ![Výběr modelu nasazení](./media/vpn-gateway-howto-site-to-site-classic-portal/selectmodel.png)
4. Na stránce **Vytvořit virtuální síť (Classic)** nakonfigurujte nastavení virtuální sítě. Na této stránce přidáte první adresní prostor a jeden rozsah adres podsítě. Po dokončení vytváření sítě VNet můžete přejít zpět a přidat další podsítě a adresní prostory.

  ![Stránka pro vytvoření virtuální sítě](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Stránka pro vytvoření virtuální sítě")
5. V rozevíracím seznamu **Předplatné** zkontrolujte, jestli je vybrané správné předplatné. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
6. Klikněte na **Skupina prostředků** a vyberte existující skupinu prostředků nebo zadáním názvu vytvořte novou. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Potom vyberte nastavení **Umístění** sítě VNet. Umístění určuje, kde budou uloženy prostředky nasazené v této síti VNet.
8. Pokud chcete mít k síti VNet snadný přístup na řídicím panelu, vyberte **Připnout na řídicí panel**. Kliknutím na **Vytvořit** vytvořte virtuální síť.

  ![Připnout na řídicí panel](./media/vpn-gateway-howto-site-to-site-classic-portal/pintodashboard150.png "Připnout na řídicí panel")
9. Po kliknutí na Vytvořit se na řídicím panelu zobrazí dlaždice, která zobrazuje postup vaší virtuální sítě. Obsah dlaždice se v průběhu vytváření sítě VNet mění.

  ![Dlaždice Vytváří se virtuální síť](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png "Vytváří se virtuální síť")

## <a name="additionaladdress"></a>2. Přidání dalšího adresního prostoru

Po vytvoření virtuální sítě můžete přidat další adresní prostor. Přidání dalšího adresního prostoru se nevyžaduje jako součást konfigurace Site-to-Site, pokud ale potřebujete více adresních prostorů, postupujte následovně:

1. Vyhledejte virtuální sítě na portálu.
2. Na stránce pro virtuální síť v části **Nastavení** klikněte na **Adresní prostor**.
3. Na stránce Adresní prostor klikněte na **+Přidat** a zadejte další adresní prostor.

## <a name="dns"></a>3. Určení serveru DNS

Nastavení DNS se nevyžaduje jako součást konfigurace Site-to-Site, ale pokud chcete překlad IP adres, server DNS je nezbytný. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se připojujete. Pro příklad nastavení jsme použili privátní IP adresu. IP adresa, kterou používáme, pravděpodobně není IP adresa vašeho serveru DNS. Je potřeba, abyste použili svoje vlastní hodnoty.

Po vytvoření virtuální sítě můžete přidat IP adresu serveru DNS, aby bylo možné zpracovávat překlad názvů. Otevřete nastavení pro virtuální síť, klikněte na servery DNS a přidejte IP adresu serveru DNS, který chcete použít pro překlad IP adres.

1. Vyhledejte virtuální sítě na portálu.
2. Na stránce pro virtuální síť v části **Nastavení** klikněte na **Servery DNS**.
3. Přidejte server DNS.
4. Uložte nastavení kliknutím na **Uložit** v horní části stránky.

## <a name="localsite"></a>4. Konfigurace místní lokality

Místní lokalita obvykle odkazuje na vaše místní umístění. Obsahuje IP adresu zařízení VPN, ke kterému vytvoříte připojení, a rozsah IP adres, které budou do tohoto zařízení VPN směrovány přes bránu VPN.

1. Na portálu přejděte na virtuální síť, pro kterou chcete vytvořit bránu.
2. Na stránce pro virtuální síť klikněte na stránce **Přehled** v části pro připojení VPN na **Brána**. Otevře se stránka **Nové připojení VPN**.

  ![Kliknutím nakonfigurujte nastavení brány](./media/vpn-gateway-howto-site-to-site-classic-portal/beforegw125.png "Kliknutím nakonfigurujte nastavení brány")
3. Na stránce **Nové připojení VPN** vyberte **Site-to-Site**.
4. Kliknutím na **Místní lokalita – Konfigurovat požadované nastavení** otevřete stránku **Místní lokalita**. Nakonfigurujte nastavení a potom kliknutím na **OK** nastavení uložte.
  - **Jméno:** Vytvořte název pro místní lokalitu, aby byl snadno identifikovat.
  - **IP adresa brány VPN:** Toto je veřejnou IP adresu zařízení VPN pro vaši místní síť. Zařízení VPN vyžaduje veřejnou IP adresu IPv4. Zadejte platnou veřejnou IP adresu pro zařízení VPN, ke kterému se chcete připojit. IP adresa nemůže být za serverem NAT a musí být dostupná pro Azure. Pokud neznáte IP adresu zařízení VPN, pořád můžete použít zástupnou hodnotu (pokud je ve formátu platné veřejné IP adresy) a změnit ji později.
  - **Klientský adresní prostor:** Seznam rozsahů IP adres, které chcete směrovat do místní sítě přes tuto bránu. Můžete přidat více různých rozsahů adres. Ujistěte se, že se zde zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se vaše virtuální síť připojuje, nebo s rozsahy adres samotné virtuální sítě.

  ![Místní lokalita](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Konfigurace místní lokality")

## <a name="gatewaysubnet"></a>5. Konfigurace podsítě brány

Pro bránu VPN je nutné vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které bude používat služba brány VPN.

1. Na stránce **Nové připojení VPN** zaškrtněte políčko **Vytvořit bránu hned**. Zobrazí se stránka Volitelná konfigurace brány. Pokud políčko nezaškrtnete, stránka, na které můžete konfigurovat podsíť brány, se nezobrazí.

  ![Konfigurace brány – Podsíť, velikost, typ směrování](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Konfigurace brány – Podsíť, velikost, typ směrování")
2. Stránku **Konfigurace brány** otevřete kliknutím na **Volitelná konfigurace brány – Podsíť, velikost a typ směrování**.
3. Na stránce **Konfigurace brány** kliknutím na **Podsíť – Konfigurovat požadované nastavení** otevřete stránku **Přidat podsíť**.

  ![Konfigurace brány – podsíť brány](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Konfigurace brány – podsíť brány")
4. Na stránce **Přidat podsíť** přidejte podsíť brány. Velikost podsítě brány, kterou zadáte, závisí na konfiguraci brány VPN, kterou chcete vytvořit. Přestože je možné vytvořit tak malou podsíť brány, jako je /29, doporučujeme použít /27 nebo /28. Vytvoří se tak vetší podsíť zahrnující více adres. Použitím větší podsítě brány zajistíte dostatek IP adres pro případné další konfigurace.

  ![Přidání podsítě brány](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Přidání podsítě brány")

## <a name="sku"></a>6. Zadání SKU a typu sítě VPN

1. Vyberte **velikost** brány. Jde o hodnotu SKU, se kterou vytvoříte bránu virtuální sítě. Na portálu je výchozí SKU **Basic**. Brány VPN Classic používají staré (starší) skladové položky brány. Další informace o starších skladových položkách brány najdete v tématu [Práce se skladovými položkami bran virtuálních sítí (staré skladové položky)](vpn-gateway-about-skus-legacy.md).

  ![Výběr SKU a typu sítě VPN](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Výběr SKU a typu sítě VPN")
2. Vyberte pro bránu **typ směrování**. Označuje se také jako typ sítě VPN. Je důležité vybrat správný typ brány, protože typ brány nelze měnit. Zařízení VPN musí být kompatibilní s typem směrování, který zvolíte. Další informace o typu sítě VPN najdete v tématu [Informace o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Můžete narazit na články, které odkazují na typy sítě VPN RouteBased a PolicyBased. Typ Dynamická odpovídá RouteBased a Statická odpovídá PolicyBased.
3. Kliknutím na **OK** uložte nastavení.
4. V dolní části stránky **Nové připojení VPN** klikněte na **OK** – zahájí se vytváření brány virtuální sítě. V závislosti na vybrané skladové položce může vytvoření brány virtuální sítě trvat až 45 minut.

## <a name="vpndevice"></a>7. Konfigurace zařízení VPN

Připojení Site-to-Site k místní síti vyžadují zařízení VPN. V tomto kroku nakonfigurujete zařízení VPN. Při konfiguraci zařízení VPN potřebujete následující:

- Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáváte při vytváření připojení VPN Site-to-Site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
- Veřejnou IP adresu vaší brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>8. Vytvoření připojení
V tomto kroku nastavíte sdílený klíč a vytvoříte připojení. Klíč, který nastavíte, musí být stejný jako klíč, který jste použili v konfiguraci zařízení VPN.

> [!NOTE]
> Tento krok v současné době není k dispozici na webu Azure Portal. Musíte použít verzi rutin Azure PowerShellu pro správu služeb.
>

### <a name="step-1-connect-to-your-azure-account"></a>Krok 1. Připojení k účtu Azure

1. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

  ```powershell
  Add-AzureAccount
  ```
2. Zkontrolujte předplatná pro příslušný účet.

  ```powershell
  Get-AzureSubscription
  ```
3. Máte-li více předplatných, vyberte předplatné, které chcete použít.

  ```powershell
  Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
  ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Krok 2. Nastavení sdíleného klíče a vytvoření připojení

Při práci s PowerShellem a modelem nasazení Classic někdy názvy prostředků na portálu neodpovídají názvům, které Azure při použití PowerShellu očekává. Následující kroky vám pomůžou exportovat soubor konfigurace sítě a získat tak přesné hodnoty pro tyto názvy.

1. Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu se soubor konfigurace sítě exportuje do adresáře C:\AzureNet.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
2. Otevřete soubor konfigurace sítě v editoru XML a zkontrolujte hodnoty „LocalNetworkSite name“ (Název místní síťové lokality) a „VirtualNetworkSite name“ (Název virtuální síťové lokality). Upravte příklad tak, aby odpovídal potřebným hodnotám. Pokud zadáváte název, který obsahuje mezery, zadejte hodnotu v jednoduchých uvozovkách.

3. Nastavte sdílený klíč a vytvořte připojení. Hodnota, kterou generujete a zadáváte, je „-SharedKey“. V příkladu jsme použili „abc123“, ale můžete (a měli byste) vygenerovat něco složitějšího. Důležité je, aby hodnota, kterou zde zadáte, byla stejná jako hodnota, kterou jste zadali při konfiguraci zařízení VPN.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
  -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
  ```
Když se připojení vytvoří, výsledkem je: **Stav: Úspěšné**.

## <a name="verify"></a>9. Ověření stavu připojení

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Pokud máte potíže s připojením, podívejte se do části **Řešení potíží** obsahu v levém podokně.

## <a name="reset"></a>Resetování brány VPN

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Pokyny najdete v tématu [Resetování brány VPN](vpn-gateway-resetgw-classic.md).

## <a name="changesku"></a>Změna skladové položky brány

Postup pro změnu skladové položky brány najdete v tématu [Změna velikosti skladové položky brány](vpn-gateway-about-SKUS-legacy.md).

## <a name="next-steps"></a>Další postup

* Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/).
* Informace o vynuceném tunelování najdete v tématu [Informace o vynuceném tunelování](vpn-gateway-about-forced-tunneling.md).
