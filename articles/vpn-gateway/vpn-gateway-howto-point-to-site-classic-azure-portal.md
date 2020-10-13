---
title: 'Připojení počítače k virtuální síti s použitím ověřování typu Point-to-site a certifikátu: Azure Portal Classic | Microsoft Docs'
description: Vytvoříte připojení classic k bráně VPN typu Point-to-Site pomocí portálu Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: bf0618c120a7fe572aa55b423d36dce3ef5656da
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876188"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Konfigurace připojení typu Point-to-site pomocí ověřování certifikátů (Classic)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

V tomto článku se dozvíte, jak vytvořit virtuální síť s připojením Point-to-site. Tuto virtuální síť vytvoříte pomocí modelu nasazení Classic pomocí Azure Portal. Tato konfigurace používá certifikáty k ověření připojujícího se klienta, buď podepsané svým držitelem (self-signed certificate), nebo vydané certifikační autoritou. Tuto konfiguraci můžete také vytvořit pomocí jiného nástroje pro nasazení nebo modelu pomocí možností, které jsou popsány v následujících článcích:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Bránu VPN typu Point-to-Site (P2S) můžete použít k vytvoření zabezpečeného připojení k virtuální síti z jednotlivých klientských počítačů. Připojení VPN typu Point-to-site jsou užitečná, když se chcete připojit k virtuální síti ze vzdáleného umístění. Pokud máte jen několik klientů, kteří se potřebují připojit k virtuální síti, P2S VPN je užitečné řešení, které použijete místo VPN typu Site-to-site. Připojení VPN P2S se vytváří jeho zahájením z klientského počítače.

> [!IMPORTANT]
> Model nasazení Classic podporuje pouze klienty VPN systému Windows a používá protokol SSTP (Secure Socket Tunneling Protocol), což je protokol VPN založený na protokolu SSL. Aby bylo možné podporovat klienty VPN jiných výrobců než Windows, je nutné vytvořit virtuální síť pomocí modelu nasazení Správce prostředků. Model nasazení Resource Manager podporuje kromě protokolu SSTP i IKEv2 VPN. Další informace najdete v tématu [Informace o připojeních typu Point-to-Site](point-to-site-about.md).
>
>

![Diagram Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Požadavky

Připojení pro ověřování certifikátů Point-to-site vyžadují následující požadavky:

* Dynamickou bránu VPN.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. Tento klíč se považuje za důvěryhodný certifikát a používá se k ověřování.
* Klientský certifikát vygenerovaný z kořenového certifikátu a nainstalovaný na každém klientském počítači, který se bude připojovat. Tento certifikát se používá k ověřování klienta.
* Na každém klientském počítači, který se bude připojovat, musí být vygenerován a nainstalován balíček pro konfiguraci klienta VPN. Konfigurační balíček klienta nakonfiguruje nativního klienta VPN, který už je v operačním systému, a potřebuje informace pro připojení k virtuální síti.

Připojení typu Point-to-site nevyžadují zařízení VPN ani místní veřejnou IP adresu. Připojení VPN se vytváří přes protokol SSTP (Secure Socket Tunneling Protocol). Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2. 

Další informace o připojeních Point-to-site najdete v tématu [Nejčastější dotazy k Point-to-site](#point-to-site-faq).

### <a name="example-settings"></a>Příklad nastavení

K vytvoření testovacího prostředí použijte následující hodnoty, nebo tyto hodnoty můžete použít k lepšímu pochopení příkladů v tomto článku:

- **Vytvořit nastavení virtuální sítě (klasické)**
   - **Název**: zadejte *VNet1*.
   - **Adresní prostor**: zadejte *192.168.0.0/16*. V tomto příkladu používáme pouze jeden adresní prostor. Pro svoji virtuální síť můžete mít více adresních prostorů, jak ukazuje diagram.
   - **Název podsítě**: zadejte *front-end*.
   - **Rozsah adres podsítě**: zadejte *192.168.1.0/24*.
   - **Předplatné**: vyberte předplatné ze seznamu dostupných předplatných.
   - **Skupina prostředků**: zadejte *TestRG*. Vyberte **vytvořit novou**, pokud skupina prostředků neexistuje.
   - **Umístění**: v seznamu vyberte **východní USA** .

  - **Nastavení připojení VPN**
    - **Typ připojení**: vyberte **Point-to-site**.
    - **Adresní prostor klienta**: zadejte *172.16.201.0/24*. Klienti VPN, kteří se připojují k virtuální síti pomocí tohoto připojení Point-to-site, obdrží IP adresu ze zadaného fondu.

- **Nastavení podsítě konfigurace brány**
   - **Název**: vyplní se *GatewaySubnet*.
   - **Rozsah adres**: zadejte *192.168.200.0/24*. 

- **Nastavení konfigurace brány**:
   - **Velikost**: vyberte SKU brány, kterou chcete použít.
   - **Typ směrování**: vyberte **Dynamická**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Vytvoření virtuální sítě a brány VPN

Než začnete, ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Část 1: Vytvoření virtuální sítě

Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Část 2: Vytvoření podsítě brány a brány dynamického směrování

V tomto kroku vytvoříte podsíť brány a bránu dynamického směrování. V Azure Portal pro model nasazení Classic vytvoříte podsíť brány a bránu přes stejné konfigurační stránky. Použijte podsíť brány jenom pro služby brány. Nikdy nenasazujte nic (například virtuální počítače nebo jiné služby) přímo do podsítě brány.

1. V Azure Portal otevřete virtuální síť, pro kterou chcete vytvořit bránu.

2. Na stránce pro virtuální síť vyberte **Přehled**a v části **připojení VPN** vyberte **Brána**.

   ![Vyberte, pokud chcete vytvořit bránu.](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na stránce **Nové připojení VPN** vyberte **Point-to-Site**.

   ![Připojení typu Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. V případě **adresního prostoru klienta**přidejte rozsah IP adres, ze kterého klienti VPN při připojování obdrží IP adresu. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, nebo s virtuální sítí, ke které se připojujete. Rozsah automatického vyplňování můžete přepsat rozsahem privátních IP adres, které chcete použít. V tomto příkladu se zobrazuje rozsah automatického vyplňování. 

   ![Klientský adresní prostor](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Vyberte možnost **vytvořit bránu hned**a pak vyberte **volitelná konfigurace brány** a otevřete stránku **Konfigurace brány** .

   ![Vybrat volitelnou konfiguraci brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Na stránce **Konfigurace brány** vyberte **podsíť** a přidejte podsíť brány. Je možné vytvořit podsíť brány s malým označením/29. Doporučujeme však vytvořit větší podsíť, která bude obsahovat více adres, a to tak, že vyberete aspoň/28 nebo/27. Tím umožníte dostatek adres pro přizpůsobení možných dalších konfigurací, které v budoucnu budete chtít. Při práci s podsítěmi brány nepřidružujte skupinu zabezpečení sítě (NSG) k podsíti brány. Přidružení skupiny zabezpečení sítě k této podsíti může způsobit, že vaše brána VPN nebude fungovat podle očekávání. Kliknutím na **tlačítko OK** toto nastavení uložte.

   ![Přidání podsítě brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Vyberte **velikost** brány. Velikost je skladová položka brány pro vaši bránu virtuální sítě. V Azure Portal je výchozí SKU **výchozí**. Další informace o SKU brány najdete v tématu [informace o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Velikost brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Vyberte pro bránu **typ směrování**. Konfigurace P2S vyžadují **dynamický** typ směrování. Po dokončení konfigurace této stránky vyberte **OK** .

   ![Konfigurace typu směrování](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Na stránce **nové připojení VPN** vyberte **OK** v dolní části stránky a začněte vytvářet bránu virtuální sítě. Dokončení brány VPN může trvat až 45 minut, v závislosti na vybrané skladové jednotce brány.
 
## <a name="create-certificates"></a><a name="generatecerts"></a>Vytvoření certifikátů

Azure používá certifikáty k ověřování klientů VPN pro sítě VPN typu Point-to-site. Nahrajete do Azure informace o veřejném klíči kořenového certifikátu. Veřejný klíč se pak považuje za *důvěryhodný*. Klientské certifikáty musí být vygenerovány z důvěryhodného kořenového certifikátu a poté nainstalovány do každého klientského počítače v úložišti certifikátů Certificates-Current User\Personal\Certificates. Certifikát se používá k ověření klienta při připojení k virtuální síti. 

Pokud používáte certifikáty podepsané svým držitelem, musí se vytvořit pomocí určitých parametrů. Certifikát podepsaný svým držitelem můžete vytvořit pomocí pokynů pro [PowerShell a Windows 10](vpn-gateway-certificates-point-to-site.md)nebo [Makecert](vpn-gateway-certificates-point-to-site-makecert.md). Je důležité postupovat podle kroků v těchto pokynech při použití kořenových certifikátů podepsaných svým držitelem a k vygenerování klientských certifikátů z kořenového certifikátu podepsaného svým držitelem. V opačném případě certifikáty, které vytvoříte, nebudou kompatibilní s připojeními P2S a obdržíte chybu připojení.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Získání veřejného klíče (. cer) pro kořenový certifikát

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Vygenerování klientského certifikátu

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Nahrání souboru .cer kořenového certifikátu

Po vytvoření brány nahrajte soubor. cer (obsahující informace o veřejném klíči) důvěryhodného kořenového certifikátu do serveru Azure. Nenahrávat privátní klíč pro kořenový certifikát. Po nahrání certifikátu ho Azure používá k ověřování klientů, kteří mají nainstalovaný klientský certifikát vygenerovaný z důvěryhodného kořenového certifikátu. Později můžete v případě potřeby nahrávat další soubory důvěryhodných kořenových certifikátů (až 20).  

1. V části **připojení VPN** na stránce vaší virtuální sítě vyberte grafiku klienti a otevřete stránku **připojení VPN typu Point-to-site** .

   ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na stránce **připojení VPN typu Point-to-site** vyberte **Spravovat certifikát** a otevřete stránku **certifikáty** .

   ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Na stránce **certifikáty** vyberte **Odeslat** a otevřete stránku **Odeslat certifikát** .

    ![Stránka pro nahrání certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Vyberte grafiku složky a vyhledejte soubor. cer. Vyberte soubor a pak vyberte **OK**. Nahraný certifikát se zobrazí na stránce **certifikáty** .

   ![Nahrání certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Konfigurace klienta

Aby bylo možné se připojit k virtuální síti pomocí sítě VPN typu Point-to-site, musí každý klient nainstalovat balíček pro konfiguraci nativního klienta VPN systému Windows. Konfigurační balíček konfiguruje nativního klienta VPN ve Windows pomocí nastavení potřebných pro připojení k virtuální síti.

V každém klientském počítači můžete použít stejný konfigurační balíček klienta VPN za předpokladu, že jeho verze odpovídá architektuře klienta. Seznam podporovaných klientských operačních systémů najdete v tématu [Nejčastější dotazy k připojením typu Point-to-site](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generování a instalace balíčku pro konfiguraci klienta VPN

1. V Azure Portal na stránce **Přehled** pro vaši virtuální síť v části **připojení VPN**Vyberte grafiku klienta, abyste otevřeli stránku **připojení VPN typu Point-to-site** .

2. Na stránce **připojení VPN typu Point-to-site** vyberte balíček ke stažení, který odpovídá operačnímu systému klienta, ve kterém je nainstalovaný:

   * U 64bitových klientů vyberte **Klient VPN (64bitový)**.
   * U 32bitových klientů vyberte **Klient VPN (32bitový)**.

   ![Stažení balíčku pro konfiguraci klienta VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Po vygenerování balíčku ho stáhněte a nainstalujte do klientského počítače. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace**a pak vyberte **přesto spustit**. Můžete také balíček uložit k instalaci na další klientské počítače.

### <a name="install-a-client-certificate"></a>Instalace klientského certifikátu

Pokud chcete vytvořit připojení P2S z jiného klientského počítače, než který jste použili pro vygenerování klientských certifikátů, nainstalujte certifikát klienta. Při instalaci klientského certifikátu budete potřebovat heslo, které bylo vytvořeno při exportu klientského certifikátu. Certifikát můžete obvykle nainstalovat tak, že na něj dvakrát kliknete. Další informace najdete v tématu [Instalace exportovaného klientského certifikátu](vpn-gateway-certificates-point-to-site.md#install).


## <a name="connect-to-your-vnet"></a>Připojení k síti VNet

>[!NOTE]
>Na klientském počítači, že kterého se připojujete, musíte mít oprávnění správce.
>
>

1. Pokud se chcete připojit k virtuální síti, v klientském počítači přejděte na **připojení VPN** v Azure Portal a vyhledejte připojení VPN, které jste vytvořili. Připojení VPN má stejný název jako vaše virtuální síť. Vyberte **Připojit**. Pokud se zobrazí automaticky otevíraná zpráva o certifikátu, vyberte **pokračovat** a používejte zvýšená oprávnění.

2. Na stránce Stav **připojení** vyberte **připojit** a spusťte připojení. Pokud se zobrazí obrazovka **Vybrat certifikát** , ověřte, zda je zobrazený klientský certifikát správný. Pokud ne, vyberte v rozevíracím seznamu správný certifikát a pak vyberte **OK**.

3. Pokud je připojení úspěšné, zobrazí se **připojené** oznámení.


### <a name="troubleshooting-p2s-connections"></a>Řešení potíží s připojeními P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Ověření připojení VPN

1. Ověřte, že je připojení VPN aktivní. Na klientském počítači otevřete příkazový řádek se zvýšenými oprávněními a spusťte příkaz **ipconfig/all**.
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

## <a name="connect-to-a-virtual-machine"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="add-or-remove-trusted-root-certificates"></a>Přidání a odebrání důvěryhodných kořenových certifikátů

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Když odeberete kořenový certifikát, klienti s certifikátem vygenerovaným z tohoto kořenového adresáře už nebudou moct ověřit a připojit. Aby se klienti mohli znovu ověřit a připojit, musíte nainstalovat nový klientský certifikát vygenerovaný z kořenového certifikátu, který je důvěryhodný pro Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů .cer s důvěryhodnými kořenovými certifikáty. Pokyny najdete v tématu nahrání souboru. cer kořenového certifikátu.

### <a name="to-remove-a-trusted-root-certificate"></a>Odebrání důvěryhodného kořenového certifikátu

1. V části **připojení VPN** na stránce vaší virtuální sítě vyberte grafiku klienti a otevřete stránku **připojení VPN typu Point-to-site** .

   ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na stránce **připojení VPN typu Point-to-site** vyberte **Spravovat certifikát** a otevřete stránku **certifikáty** .

   ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Na stránce **certifikáty** vyberte tři tečky vedle certifikátu, který chcete odebrat, a pak vyberte **Odstranit**.

   ![Odstranění kořenového certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Odvolání klientského certifikátu

V případě potřeby můžete odvolat klientský certifikát. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-Site na základě jednotlivých klientských certifikátů. Tato metoda se liší od odebrání důvěryhodného kořenového certifikátu. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování pro připojení typu Point-to-Site další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

### <a name="to-revoke-a-client-certificate"></a>Odvolání klientského certifikátu

Klientský certifikát můžete odvolat tím, že přidáte jeho kryptografický otisk do seznamu odvolaných certifikátů.

1. Načtěte kryptografický otisk klientského certifikátu. Další informace najdete v tématu [Postup: Načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).
2. Zkopírujte informace do textového editoru a odstraňte mezery, aby se jedná o souvislý řetězec.
3. Přejít na klasickou virtuální síť. Vyberte **připojení VPN typu Point-to-site**a pak výběrem **možnosti spravovat certifikát** otevřete stránku **certifikáty** .
4. Vyberte **seznam odvolaných certifikátů** a otevřete stránku **seznam odvolaných certifikátů** . 
5. Výběrem **Přidat certifikát** otevřete stránku **Přidat certifikát do seznamu odvolaných** certifikátů.
6. V části **kryptografický otisk**vložte kryptografický otisk certifikátu jako jeden souvislý řádek textu bez mezer. Kliknutím na **tlačítko OK** dokončete.

Po dokončení aktualizace už nebude možné certifikát použít k připojení. Klientům, kteří se pokusí připojit pomocí tohoto certifikátu, se zobrazí zpráva s informací o tom, že certifikát již není platný.

## <a name="point-to-site-faq"></a>Nejčastější dotazy týkající se připojení Point-to-Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Další kroky

- Po dokončení připojení můžete virtuální počítače přidat do svých virtuálních sítí. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/). 

- Další informace o síťových a virtuálních počítačích se systémem Linux najdete v tématu [Přehled sítě virtuálních počítačů Azure a Linux](../virtual-machines/linux/network-overview.md).

- Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
