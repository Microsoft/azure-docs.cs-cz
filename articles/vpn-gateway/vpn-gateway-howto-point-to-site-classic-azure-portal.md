---
title: 'Připojení počítače k virtuální síti typu Point-to-Site s použitím ověření certifikátu: Portál Azure Classic | Dokumentace Microsoftu'
description: Vytvoříte připojení classic k bráně VPN typu Point-to-Site pomocí portálu Azure Portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: 65e14579-86cf-4d29-a6ac-547ccbd743bd
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: fe460113441933d655b183e87cceefee4dd24d24
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
ms.locfileid: "29464265"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-certificate-authentication-classic-azure-portal"></a>Konfigurace připojení typu Point-to-Site k virtuální síti s použitím ověření certifikátu (Classic): Azure Portal

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Tento článek ukazuje postup vytvoření virtuální sítě s připojením typu Point-to-Site v modelu nasazení Classic pomocí webu Azure Portal. Tato konfigurace používá certifikáty k ověření připojujícího se klienta, buď podepsané svým držitelem (self-signed certificate), nebo vydané certifikační autoritou. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Brána VPN typu Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení VPN typu Point-to-Site jsou užitečná, když se chcete ke své virtuální síti připojit ze vzdáleného umístění, například při práci z domova nebo z místa konání konference. Síť VPN P2S je také užitečným řešením nahrazujícím síť VPN Site-to-Site, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Připojení VPN P2S se vytváří jeho zahájením z klientského počítače.

> [!IMPORTANT]
> Model nasazení Classic podporuje pouze klienty VPN systému Windows a používá protokol SSTP (Secure Socket Tunneling Protocol), což je protokol VPN založený na protokolu SSL. Aby vaše virtuální síť podporovala jiné klienty než klienty VPN systému Windows, musí být vytvořená prostřednictvím modelu nasazení Resource Manager. Model nasazení Resource Manager podporuje kromě protokolu SSTP i IKEv2 VPN. Další informace najdete v tématu [Informace o připojeních typu Point-to-Site](point-to-site-about.md).
>
>

![Diagram Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)


Připojení typu Point-to-Site s ověřováním certifikátů vyžadují následující:

* Dynamickou bránu VPN.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. Ten se považuje za důvěryhodný certifikát a používá se k ověřování.
* Klientský certifikát vygenerovaný z kořenového certifikátu a nainstalovaný na každém klientském počítači, který se bude připojovat. Tento certifikát se používá k ověřování klienta.
* Na každém klientském počítači, který se bude připojovat, musí být vygenerován a nainstalován balíček pro konfiguraci klienta VPN. Balíček pro konfiguraci klienta konfiguruje nativního klienta VPN, který již je v operačním systému, s použitím informací potřebných pro připojení k virtuální síti.

Připojení typu Point-to-Site nevyžadují zařízení VPN ani místní veřejnou IP adresu. Připojení VPN se vytváří přes protokol SSTP (Secure Socket Tunneling Protocol). Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2. 

Další informace o připojení Point-to-Site najdete v části [Nejčastější dotazy týkající se připojení Point-to-Site](#faq) na konci tohoto článku.

### <a name="example-settings"></a>Příklad nastavení

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku:

* **Název: VNet1**
* **Adresní prostor: 192.168.0.0/16**<br>V tomto příkladu používáme pouze jeden adresní prostor. Pro svoji virtuální síť můžete mít více adresních prostorů, jak ukazuje diagram.
* **Název podsítě: FrontEnd**
* **Rozsah adres podsítě: 192.168.1.0/24**
* **Předplatné:** Ujistěte se, že máte správné předplatné, pokud máte více než jedno.
* **Skupina prostředků: TestRG**
* **Umístění: Východní USA**
* **Typ připojení: Point-to-Site**
* **Klientský adresní prostor: 172.16.201.0/24**. Klienti VPN, kteří se budou k síti VNet připojovat pomocí tohoto připojení Point-to-Site, dostanou IP adresu ze zadaného fondu.
* **Podsíť brány: 192.168.200.0/24**. Podsíť brány musí používat název GatewaySubnet.
* **Velikost:** Vyberte SKU brány, kterou chcete použít.
* **Typ směrování: Dynamické**

## <a name="vnetvpn"></a>1. Vytvoření virtuální sítě a brány VPN

Než začnete, ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="createvnet"></a>Část 1: Vytvoření virtuální sítě

Pokud ještě nemáte virtuální síť, vytvořte si ji. Snímky obrazovek slouží jen jako příklady. Nezapomeňte hodnoty nahradit vlastními. Pokud chcete vytvořit virtuální síť přes Azure Portal, použijte následující postup:

1. V prohlížeči přejděte na portál [Azure Portal](http://portal.azure.com) a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na **Vytvořit prostředek** > **Sítě** > **Virtuální síť**. 
3. U dolního okraje stránky Virtuální síť v seznamu **Vybrat model nasazení** vyberte **Classic** a potom klikněte na **Vytvořit**.

  ![Výběr modelu nasazení](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/selectmodel.png)
4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Na této stránce přidáte první adresní prostor a jeden rozsah adres podsítě. Po dokončení vytváření sítě VNet můžete přejít zpět a přidat další podsítě a adresní prostory.

  ![Stránka pro vytvoření virtuální sítě](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)
5. V rozevíracím seznamu **Předplatné** zkontrolujte, jestli je vybrané správné předplatné. Předplatná můžete měnit prostřednictvím rozevíracího seznamu.
6. Klikněte na **Skupina prostředků** a vyberte existující skupinu nebo zadáním názvu nové skupiny prostředků vytvořte novou skupinu prostředků. Pokud vytváříte novou skupinu prostředků, nazvěte skupinu prostředků v souladu s hodnotami plánované konfigurace. Další informace o skupinách prostředků najdete v článku [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Potom vyberte nastavení **Umístění** sítě VNet. Umístění určuje, kde budou uloženy prostředky nasazené v této síti VNet.
8. Pokud chcete mít k síti VNet snadný přístup na řídicím panelu, vyberte možnost **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

  ![Připnutí na řídicí panel](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/pintodashboard150.png)
9. Po kliknutí na Vytvořit se na řídicím panelu zobrazí dlaždice s informacemi o průběhu vytváření sítě VNet. Obsah dlaždice se v průběhu vytváření sítě VNet mění.

  ![dlaždice Vytváří se virtuální síť](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deploying150.png)
10. Po vytvoření virtuální sítě se zobrazí **Vytvořeno**.
11. Přidejte server DNS (volitelné). Po vytvoření virtuální sítě můžete přidat IP adresu serveru DNS pro překlad IP adres. Zadaná IP adresa serveru DNS by měla být adresa serveru DNS, který dokáže přeložit názvy pro prostředky ve vaší virtuální síti.<br>Pokud chcete přidat server DNS, otevřete nastavení pro virtuální síť, klikněte na servery DNS a přidejte IP adresu serveru DNS, který chcete použít.

### <a name="gateway"></a>Část 2: Vytvoření podsítě brány a brány dynamického směrování

V tomto kroku vytvoříte podsíť brány a bránu dynamického směrování. Na webu Azure Portal pro model nasazení Classic je možné vytvořit podsíť brány a bránu na stejných stránkách konfigurace. Podsíť brány se používá jenom pro služby brány. Nikdy nenasazujte nic (například virtuální počítače nebo jiné služby) přímo do podsítě brány.

1. Na portálu přejděte na virtuální síť, pro kterou chcete vytvořit bránu.
2. Na stránce pro virtuální síť klikněte na stránce **Přehled** v části pro připojení VPN na **Brána**.

  ![Kliknutím vytvoříte podsíť brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na stránce **Nové připojení VPN** vyberte **Point-to-Site**.

  ![Připojení typu Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Do pole **Klientský adresní prostor** přidejte rozsah IP adres. Z tohoto rozsahu dostanou klienti VPN IP adresu při svém připojení. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se budete připojovat, nebo s virtuální sítí, ke které se chcete připojit. Automaticky vyplněný rozsah můžete odstranit a pak přidat rozsah privátních IP adres, který chcete použít. Tento příklad ukazuje automaticky vyplněný rozsah. Odstraňte ho a přidejte požadovanou hodnotu.

  ![Klientský adresní prostor](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Zaškrtněte políčko **Vytvořit bránu hned**. Kliknutím na **Volitelná konfigurace brány** otevřete stránku **Konfigurace brány**.

  ![Kliknutí na tlačítko Volitelná konfigurace brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)
6. Klikněte na **Podsíť – Konfigurovat požadované nastavení** a přidejte požadovanou **podsíť brány**. I když je možné vytvořit podsíť brány s minimální velikostí /29, doporučujeme vytvořit větší podsíť, která pojme více adres, tzn. vybrat velikost aspoň /28 nebo /27. Tím vznikne dostatečný prostor pro adresy, který umožní nastavení případných dalších konfigurací v budoucnu. Při práci s podsítěmi brány nepřidružujte skupinu zabezpečení sítě (NSG) k podsíti brány. Pokud byste k této podsíti přidružili skupinu zabezpečení sítě, brána sítě VPN by mohla přestat fungovat podle očekávání.

  ![Přidání podsítě brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Vyberte **velikost** brány. Velikost je skladová položka brány pro vaši bránu virtuální sítě. Na portálu je výchozí SKU **Basic**. Další informace o SKU brány najdete v tématu [Informace o nastavení VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Velikost brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Vyberte pro bránu **typ směrování**. Konfigurace P2S vyžadují **dynamický** typ směrování. Až dokončíte konfiguraci této stránky, klikněte na **OK**.

  ![Konfigurace typu směrování](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)
9. V dolní části stránky **Nové připojení VPN** klikněte na **OK** – zahájí se vytváření brány virtuální sítě. Dokončení brány VPN může trvat až 45 minut v závislosti na vybrané skladové jednotce brány.

## <a name="generatecerts"></a>2. Vytvoření certifikátů

Azure používá certifikáty k ověřování klientů VPN pro sítě VPN Point-to-Site. Nahrajete do Azure informace o veřejném klíči kořenového certifikátu. Veřejný klíč se pak bude považovat za důvěryhodný. Klientské certifikáty musí být vygenerované z důvěryhodného kořenového certifikátu a pak nainstalované na každém klientském počítači v úložišti certifikátů v adresáři Certificates-Current User/Personal. Tento certifikát se používá k ověřování klienta při zahájení připojení k virtuální síti. 

Pokud používáte certifikáty podepsané svým držitelem, musí se vytvořit pomocí konkrétních parametrů. Certifikát podepsaný svým držitelem můžete vytvořit podle pokynů pro [PowerShell a Windows 10](vpn-gateway-certificates-point-to-site.md) nebo [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Je důležité, abyste při práci s kořenovými certifikáty podepsanými svým držitelem a generování klientských certifikátů z kořenových certifikátů podepsaných svým držitelem postupovali podle pokynů. Jinak certifikáty, které vytvoříte, nebudou kompatibilní s připojeními typu Point-to-Site a zobrazí se chyba připojení.

### <a name="cer"></a>Část 1: Získání veřejného klíče (.cer) pro kořenový certifikát

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="genclientcert"></a>Část 2: Vygenerování klientského certifikátu

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload"></a>3. Nahrání souboru .cer kořenového certifikátu

Po vytvoření brány můžete nahrát soubor .cer (obsahující informace o veřejném klíči) důvěryhodného kořenového certifikátu do Azure. Privátní klíč kořenového certifikátu nebudete nahrávat do Azure. Jakmile je soubor .cer nahraný, Azure ho může použít k ověřování klientů s nainstalovaným klientským certifikátem vygenerovaným z důvěryhodného kořenového certifikátu. Později můžete podle potřeby nahrát další soubory s důvěryhodnými kořenovými certifikáty – celkem až 20.  

1. V části **Připojení VPN** na stránce pro vaši virtuální síť kliknutím na grafiku **klienti** otevřete stránku **Připojení VPN typu Point-to-Site**.

  ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Na stránce **Připojení VPN typu Point-to-Site** kliknutím na **Správa certifikátů** otevřete stránku **Certifikáty**.<br>

  ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Na stránce **Certifikáty** kliknutím na **Nahrát** otevřete stránku **Nahrát certifikát**.<br>

    ![Stránka pro nahrání certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)<br>
4. Klikněte na obrázek složky a vyhledejte soubor .cer. Vyberte soubor a potom klikněte na **OK**. Aktualizujte stránku, aby se nahraný certifikát zobrazil na stránce **Certifikáty**.

  ![Nahrání certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)<br>

## <a name="vpnclientconfig"></a>4. Konfigurace klienta

Pokud se chcete připojit k virtuální síti pomocí sítě VPN typu Point-to-Site, musí mít každý klient nainstalovaný balíček pro konfiguraci nativního klienta VPN ve Windows. Konfigurační balíček konfiguruje nativního klienta VPN ve Windows pomocí nastavení potřebných pro připojení k virtuální síti.

V každém klientském počítači můžete použít stejný konfigurační balíček klienta VPN za předpokladu, že jeho verze odpovídá architektuře klienta. Seznam podporovaných klientských operačních systémů naleznete v části [Nejčastější dotazy o připojení Point-to-Site](#faq) na konci tohoto článku.

### <a name="generateconfigpackage"></a>Část 1: Vygenerování a instalace konfiguračního balíčku klienta VPN

1. Na webu Azure Portal klikněte na stránce **Přehled** pro vaši virtuální síť na grafiku „klienti“ v poli **Připojení VPN**. Otevře se stránka **Připojení VPN typu Point-to-Site**.
2. V horní části stránky **Připojení VPN typu Point-to-Site** klikněte na balíček ke stažení odpovídající operačnímu systému klienta, na který se bude instalovat:

  * U 64bitových klientů vyberte **Klient VPN (64bitový)**.
  * U 32bitových klientů vyberte **Klient VPN (32bitový)**.

  ![Stažení balíčku pro konfiguraci klienta VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)<br>
3. Jakmile je balíček vygenerovaný, stáhněte ho a nainstalujte na klientský počítač. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace** a potom na **Přesto spustit**. Můžete také balíček uložit k instalaci na další klientské počítače.

### <a name="installclientcert"></a>Část 2: Instalace klientského certifikátu

Pokud chcete vytvořit připojení P2S z jiného klientského počítače, než který jste použili k vytvoření klientských certifikátů, budete muset klientský certifikát nainstalovat. Při instalaci klientského certifikátu budete potřebovat heslo, které bylo vytvořeno při jeho exportu. Obvykle stačí jenom dvakrát kliknout na certifikát a nainstalovat ho. Další informace najdete v tématu [Instalace exportovaného klientského certifikátu](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect"></a>5. Připojení k Azure

### <a name="connect-to-your-vnet"></a>Připojení k síti VNet

>[!NOTE]
>Na klientském počítači, že kterého se připojujete, musíte mít oprávnění správce.
>
>

1. Chcete-li se připojit ke své síti VNet, přejděte na klientském počítači na připojení VPN a vyhledejte připojení VPN, které jste vytvořili. Bude mít stejný název jako vaše virtuální síť. Klikněte na **Připojit**. Může se zobrazit místní zpráva týkající se použití certifikátu. Pokud k tomu dojde, klikněte na možnost **Pokračovat**, abyste použili zvýšená oprávnění.
2. Připojení spustíte kliknutím na tlačítko **Připojit** na stavové stránce **Připojení**. Pokud uvidíte obrazovku **Výběr certifikátu**, ujistěte se, že zobrazený klientský certifikát je ten, který chcete pro připojení použít. Pokud není, vyberte pomocí šipky rozevíracího seznamu správný certifikát, a poté klikněte na **OK**.

  ![Připojení klienta VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientconnect.png)
3. Vaše připojení bylo vytvořeno.

  ![Vytvořené připojení](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/connected.png)

#### <a name="troubleshooting-p2s-connections"></a>Řešení potíží s připojeními P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="verifyvpnconnect"></a>Ověření připojení VPN

1. Pokud chcete ověřit, jestli je připojení VPN aktivní, z příkazového řádku se zvýšenými oprávněními v klientském počítači spusťte příkaz *ipconfig/all*.
2. Zkontrolujte výsledky. Všimněte si, že IP adresa, kterou jste obdrželi, je jedna z adres z rozsahu adres připojení typu Point-to-Site, který jste určili během vytváření vaší virtuální sítě. Výsledek by se měl podobat tomuto příkladu:

  ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add"></a>Přidání a odebrání důvěryhodných kořenových certifikátů

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Když odeberete kořenový certifikát, klienti s certifikátem vygenerovaným z tohoto kořenového certifikátu se nebudou moci ověřit a proto ani připojit. Pokud chcete, aby se klient mohl i nadále ověřovat a připojovat, je nutné nainstalovat nový klientský certifikát vygenerovaný z kořenového certifikátu, který Azure považuje za důvěryhodný (je do Azure nahraný).

### <a name="addtrustedroot"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů .cer s důvěryhodnými kořenovými certifikáty. Pokyny najdete v [části 3 – Nahrání souboru .cer kořenového certifikátu](#upload).

### <a name="removetrustedroot"></a>Odebrání důvěryhodného kořenového certifikátu

1. V části **Připojení VPN** na stránce pro vaši virtuální síť kliknutím na grafiku **klienti** otevřete stránku **Připojení VPN typu Point-to-Site**.

  ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)
2. Na stránce **Připojení VPN typu Point-to-Site** kliknutím na **Správa certifikátů** otevřete stránku **Certifikáty**.<br>

  ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)<br><br>
3. Na stránce **Certifikáty** klikněte na tři tečky vedle certifikátu, který chcete odebrat, a potom klikněte na **Odstranit**.

  ![Odstranění kořenového certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)<br>

## <a name="revokeclient"></a>Odvolání klientského certifikátu

Certifikáty klientů lze odvolat. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-Site na základě jednotlivých klientských certifikátů. To se liší od odebrání důvěryhodného kořenového certifikátu. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování pro připojení typu Point-to-Site další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

### <a name="revokeclientcert"></a>Odvolání klientského certifikátu

Klientský certifikát můžete odvolat tím, že přidáte jeho kryptografický otisk do seznamu odvolaných certifikátů.

1. Načtěte kryptografický otisk klientského certifikátu. Další informace najdete v tématu [Postup: Načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).
2. Zkopírujte údaje do textového editoru a smažte všechny mezery, aby vznikl souvislý řetězec.
3. Přejděte na stránku **název_klasické_virtuální_sítě > Připojení VPN typu Point-to-Site > Certifikáty** a klikněte na **Seznam odvolaných certifikátů**. Otevře se stránka Seznam odvolaných certifikátů. 
4. Na stránce **Seznam odvolaných certifikátů** kliknutím na **+Přidat certifikát** otevřete stránku **Přidat certifikát do seznamu odvolaných certifikátů**.
5. Na stránce **Přidat certifikát do seznamu odvolaných certifikátů** vložte kryptografický otisk certifikátu jako souvislý řádek textu bez mezer. Klikněte na **OK** v dolní části stránky.
6. Po dokončení aktualizace už nebude možné certifikát použít k připojení. Klientům, kteří se pokusí připojit pomocí tohoto certifikátu, se zobrazí zpráva s informací o neplatnosti certifikátu.

## <a name="faq"></a>Nejčastější dotazy týkající se připojení Point-to-Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Další kroky
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/azure-vm-network-overview.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).