---
title: 'Připojení počítače k virtuální síť Point-to-Site s použitím ověření certifikátu: Azure Portal classic | Dokumentace Microsoftu'
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
ms.date: 12/11/2018
ms.author: cherylmc
ms.openlocfilehash: 6aedfa3f0ef65fdb1b663db8637b648e685e31be
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321473"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Konfigurace připojení typu Point-to-Site s použitím ověření certifikátu (classic)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Tento článek ukazuje, jak vytvořit virtuální síť s připojením Point-to-Site. Tuto virtuální síť pomocí modelu nasazení classic vytvoříte pomocí webu Azure portal. Tato konfigurace používá certifikáty k ověření připojujícího se klienta, buď podepsané svým držitelem (self-signed certificate), nebo vydané certifikační autoritou. Tuto konfiguraci můžete také vytvořit pomocí jiného nástroje nasazení nebo model s použitím možností, které jsou popsány v následujících článcích:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Bránu sítě VPN Point-to-Site (P2S) umožňuje vytvořit zabezpečené připojení k virtuální síti z jednotlivých klientských počítačů. Připojení k síti VPN Point-to-Site jsou užitečná, pokud chcete pro připojení k virtuální síti ze vzdáleného umístění. Pokud máte pouze několik klientů, které je potřeba připojit k virtuální síti, sítě VPN P2S je užitečným řešením nahrazujícím síť VPN Site-to-Site. Připojení VPN P2S se vytváří jeho zahájením z klientského počítače.

> [!IMPORTANT]
> Model nasazení Classic podporuje pouze klienty VPN systému Windows a používá protokol SSTP (Secure Socket Tunneling Protocol), což je protokol VPN založený na protokolu SSL. Pro podporu klientů Windows VPN, musíte vytvořit virtuální síť pomocí modelu nasazení Resource Manageru. Model nasazení Resource Manager podporuje kromě protokolu SSTP i IKEv2 VPN. Další informace najdete v tématu [Informace o připojeních typu Point-to-Site](point-to-site-about.md).
>
>

![Diagram Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Požadavky

Ověření připojení point-to-Site certifikátů vyžadují následující součásti:

* Dynamickou bránu VPN.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. Tento klíč se považuje za důvěryhodný certifikát a používá se pro ověřování.
* Klientský certifikát vygenerovaný z kořenového certifikátu a nainstalovaný na každém klientském počítači, který se bude připojovat. Tento certifikát se používá k ověřování klienta.
* Na každém klientském počítači, který se bude připojovat, musí být vygenerován a nainstalován balíček pro konfiguraci klienta VPN. Balíček pro konfiguraci klienta konfiguruje nativního klienta VPN, který je již v operačním systému s použitím informací potřebných pro připojení k virtuální síti.

Připojení point-to-Site nevyžadují zařízení VPN ani místní veřejnou IP adresu. Připojení VPN se vytváří přes protokol SSTP (Secure Socket Tunneling Protocol). Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2. 

Další informace o připojení Point-to-Site najdete v tématu [Point-to-Site – nejčastější dotazy](#point-to-site-faq).

### <a name="example-settings"></a>Příklad nastavení

K vytvoření testovacího prostředí, nebo si přečtěte tyto hodnoty k lepšímu pochopení příkladů v tomto článku použijte následující hodnoty:

- **Vytvoření virtuální sítě (classic) nastavení**
   - **Název**: Zadejte *ze sítě VNet1*.
   - **Adresní prostor**: Zadejte *192.168.0.0/16*. V tomto příkladu používáme pouze jeden adresní prostor. Pro svoji virtuální síť můžete mít více adresních prostorů, jak ukazuje diagram.
   - **Název podsítě**: Zadejte *front-endu*.
   - **Rozsah adres podsítě**: Zadejte *192.168.1.0/24*.
   - **Předplatné**: Vyberte předplatné, ze seznamu dostupných předplatných.
   - **Skupina prostředků**: Zadejte *TestRG*. Vyberte **vytvořit nový**, pokud skupina prostředků neexistuje.
   - **Umístění**: Vyberte **USA – východ** ze seznamu.

 - **Nastavení připojení VPN**
   - **Typ připojení**: Vyberte **Point-to-site**.
   - **Klientský adresní prostor**: Zadejte *172.16.201.0/24*. Klienti VPN, které se připojují k virtuální síti pomocí tohoto připojení Point-to-Site obdrží IP adresu ze zadaného fondu.

- **Nastavení konfigurace podsítě brány**
   - **Název**: Autofilled s *GatewaySubnet*.
   - **Rozsah adres**: Zadejte *192.168.200.0/24*. 

- **Nastavení konfigurace brány**:
   - **Velikost**: Vyberte SKU brány, kterou chcete použít.
   - **Typ směrování**: Vyberte **dynamické**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Vytvoření virtuální sítě a brány VPN

Než začnete, ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Část 1: Vytvoření virtuální sítě

Pokud ještě nemáte virtuální síť (VNet), vytvořte si ho. Snímky obrazovek slouží jen jako příklady. Nezapomeňte hodnoty nahradit vlastními. Pokud chcete vytvořit virtuální síť přes Azure Portal, použijte následující postup:

1. Přihlaste se k [webu Azure portal](http://portal.azure.com) a vyberte **vytvořit prostředek**. **Nový** otevře se stránka. 

2. V **Hledat na marketplace** zadejte *virtuální sítě* a vyberte **virtuální síť** z vráceném seznamu. **Virtuální síť** otevře se stránka.

3. Z **vybrat model nasazení** vyberte **Classic**a pak vyberte **vytvořit**. **Vytvořit virtuální síť** otevře se stránka.

4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Na této stránce přidáte první adresní prostor a jeden rozsah adres podsítě. Po dokončení vytváření sítě VNet můžete přejít zpět a přidat další podsítě a adresní prostory.

   ![Stránka pro vytvoření virtuální sítě](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. Vyberte **předplatné** chcete použít z rozevíracího seznamu.

6. Vyberte existující **skupiny prostředků**. Nebo vytvořte novou skupinu prostředků tak, že vyberete **vytvořit nový** a zadáním názvu. Pokud vytváříte novou skupinu prostředků, nazvěte skupinu prostředků v souladu s hodnotami plánované konfigurace. Další informace o skupinách prostředků najdete v tématu [Přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md#resource-groups).

7. Vyberte **umístění** pro vaši virtuální síť. Toto nastavení určuje zeměpisnou polohu prostředky, které nasadíte do této virtuální síti.

8. Vyberte **vytvořit** vytvořte síť VNet. Z **oznámení** stránce, zobrazí se vám **probíhá nasazení** zprávy.

8. Po vaší virtuální sítě vytvořená, zprávy na **oznámení** změn stránky **nasazení bylo úspěšné**. Vyberte **připnout na řídicí panel** Pokud chcete snadno najít virtuální síť na řídicím panelu. 

10. Přidejte server DNS (volitelné). Po vytvoření virtuální sítě můžete přidat IP adresu serveru DNS pro překlad IP adres. Zadaná IP adresa serveru DNS by měla být adresa serveru DNS, který dokáže přeložit názvy pro prostředky ve vaší virtuální síti.

    Chcete-li přidat DNS server, **servery DNS** z stránce vaší virtuální sítě. Zadejte IP adresu serveru DNS, který chcete použít a vyberte **Uložit**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Část 2: Vytvoření podsítě brány a bránu dynamického směrování

V tomto kroku vytvoříte podsíť brány a bránu dynamického směrování. Na webu Azure portal pro model nasazení classic vytvoříte podsíť brány a bránu na stejné stránkách konfigurace. Podsíť brány použijte pouze na služby brány. Nikdy nenasazujte nic (například virtuální počítače nebo jiné služby) přímo do podsítě brány.

1. Na webu Azure Portal přejděte na virtuální síť, pro kterou chcete vytvořit bránu.

2. Na stránce vaší virtuální sítě, vyberte **přehled**a **připojení k síti VPN** vyberte **brány**.

  ![Vytvořit bránu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na stránce **Nové připojení VPN** vyberte **Point-to-Site**.

  ![Připojení typu Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. Pro **klientský adresní prostor**, přidat rozsah IP adres, ze kterého dostanou klienti VPN IP adresu při připojování. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého připojovat, nebo s virtuální sítí, se připojujete k. Rozsah autofilled můžete přepsat rozsah privátních IP adres, který chcete použít. Tento příklad ukazuje autofilled rozsahu. 

  ![Klientský adresní prostor](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Vyberte **vytvořit bránu hned**a pak vyberte **volitelná konfigurace brány** otevřít **konfigurace brány** stránky.

  ![Vyberte volitelná konfigurace brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Z **konfigurace brány** stránce **podsítě** přidáte podsíť brány. Je možné vytvořit podsíť brány s minimální velikostí/29. Ale doporučujeme vytvořit větší podsíť, která pojme více adres výběru velikosti alespoň/28 nebo/27. To vám umožní dostatek adres pro případné další konfigurace, které můžete chtít v budoucnu. Při práci s podsítěmi brány nepřidružujte skupinu zabezpečení sítě (NSG) k podsíti brány. Přidružení skupiny zabezpečení sítě s touto podsítí může způsobit, že vaše brána nebude fungovat podle očekávání. Vyberte **OK** uložit toto nastavení.

  ![Přidání podsítě brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Vyberte **velikost** brány. Velikost je skladová položka brány pro vaši bránu virtuální sítě. Na webu Azure Portal, výchozí SKU je **výchozí**. Další informace o SKU brány najdete v tématu [informace o službě VPN gateway nastavení](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

  ![Velikost brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Vyberte pro bránu **typ směrování**. Konfigurace P2S vyžadují **dynamický** typ směrování. Vyberte **OK** až dokončíte konfiguraci této stránky.

  ![Konfigurace typu směrování](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Na **nové připojení VPN** stránce **OK** v dolní části stránky zahájíte vytváření brány virtuální sítě. Brány VPN může trvat až 45 minut v závislosti na vybrané skladové jednotce brány.
 
## <a name="generatecerts"></a>Vytvoření certifikátů

Azure používá certifikáty k ověřování klientů VPN pro sítě Point-to-Site VPN. Nahrajete do Azure informace o veřejném klíči kořenového certifikátu. Veřejný klíč se považovat za *důvěryhodné*. Klientské certifikáty musí být vygenerované z důvěryhodného kořenového certifikátu a nainstaluje na každý klientský počítač v úložišti certifikátů Certificates-Current User\Personal\Certificates. Certifikát se používá k ověřování klienta při připojení k virtuální síti. 

Pokud používáte certifikáty podepsané svým držitelem, musí se vytvořit pomocí konkrétních parametrů. Můžete vytvořit certifikát podepsaný svým držitelem podle pokynů pro [prostředí PowerShell a Windows 10](vpn-gateway-certificates-point-to-site.md), nebo [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Je důležité postupovat podle kroků v těchto pokynech, když pomocí možnosti kořenovými certifikáty podepsanými svým držitelem a generování klientských certifikátů z certifikátu podepsaného svým držitelem. Jinak certifikáty, které vytvoříte, nebudou kompatibilní s připojeními typu P2S a zobrazí chyba připojení.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Získání veřejného klíče (.cer) pro kořenový certifikát

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Vygenerování klientského certifikátu

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Nahrání souboru .cer kořenového certifikátu

Po vytvoření brány, nahrání souboru .cer (obsahující informace o veřejném klíči) pro důvěryhodného kořenového certifikátu na Azure server. Nenahrávejte privátní klíč pro kořenový certifikát. Po odeslání certifikátu v Azure používá k ověřování klientů s nainstalovaným klientským certifikátem vygenerovaným z důvěryhodného kořenového certifikátu. Později můžete nahrát další důvěryhodné kořenové soubory certifikátů (až na 20), v případě potřeby.  

1. Na **připojení k síti VPN** části na stránce pro vaši virtuální síť, vyberte obrázek klienti otevřete **Point-to-site VPN připojení** stránky.

  ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na **Point-to-site VPN připojení** stránce **Správa certifikátu** otevřít **certifikáty** stránky.

  ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Na **certifikáty** stránce **nahrát** otevřít **nahrát certifikát** stránky.

    ![Stránka pro nahrání certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Vyberte obrázek složky a vyhledejte soubor .cer. Vyberte soubor a pak vyberte **OK**. Nahraný certifikát zobrazí na **certifikáty** stránky.

  ![Nahrání certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Konfigurace klienta

Pro připojení k virtuální síti pomocí sítě VPN Point-to-Site, musí každý klient nainstalovaný balíček ke konfiguraci nativního klienta VPN ve Windows. Konfigurační balíček konfiguruje nativního klienta VPN ve Windows pomocí nastavení potřebných pro připojení k virtuální síti.

V každém klientském počítači můžete použít stejný konfigurační balíček klienta VPN za předpokladu, že jeho verze odpovídá architektuře klienta. Seznam klientských operačních systémů, které jsou podporovány, naleznete v tématu [NejčastějšídotazyopřipojeníPoint-to-Site](#point-to-site-faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Vygenerování a instalace konfiguračního balíčku klienta VPN

1. Na webu Azure Portal v **přehled** stránce vaší virtuální sítě, v **připojení k síti VPN**, vyberte grafiku, otevřete **Point-to-site VPN připojení** stránky.

2. Z **Point-to-site VPN připojení** vyberte balíček ke stažení, který odpovídá operačnímu systému klienta, kde je nainstalovaný:

  * U 64bitových klientů vyberte **Klient VPN (64bitový)**.
  * U 32bitových klientů vyberte **Klient VPN (32bitový)**.

  ![Stažení balíčku pro konfiguraci klienta VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Po vygeneruje balíček stáhněte ho a nainstalujte ho na klientském počítači. Pokud se zobrazí místní okno filtru SmartScreen, vyberte **informace**a pak vyberte **přesto spustit**. Můžete také balíček uložit k instalaci na další klientské počítače.

### <a name="install-a-client-certificate"></a>Instalace klientského certifikátu

Chcete-li vytvořit připojení P2S z jiného klienta počítače, než jaký se používá k vytvoření klientských certifikátů, instalace klientského certifikátu. Při instalaci klientského certifikátu budete potřebovat heslo, který byl vytvořen při klientský certifikát vyexportoval. Obvykle můžete nainstalovat certifikát podle stačí na něj kliknuli dvakrát. Další informace najdete v tématu [Instalace exportovaného klientského certifikátu](vpn-gateway-certificates-point-to-site.md#install).


## <a name="connect-to-your-vnet"></a>Připojení k síti VNet

>[!NOTE]
>Na klientském počítači, že kterého se připojujete, musíte mít oprávnění správce.
>
>

1. Pro připojení k virtuální síti, v klientském počítači přejděte do **připojení k síti VPN** na webu Azure Portal a vyhledejte právě vytvořené připojení VPN. Připojení k síti VPN má stejný název jako vaše virtuální síť. Vyberte **Connect** (Připojit). Pokud se zobrazí místní zpráva o certifikát, vyberte **pokračovat** pro použití zvýšených oprávnění.

2. Na **připojení** stavové stránce vyberte **připojit** spustit připojení. Pokud se zobrazí **vybrat certifikát** obrazovky, ověřte, že zobrazený klientský certifikát je správný. Pokud ne, vyberte z rozevíracího seznamu správný certifikát a pak vyberte **OK**.

3. Pokud je připojení úspěšné, zobrazí se vám **připojeno** oznámení.


### <a name="troubleshooting-p2s-connections"></a>Řešení potíží s připojeními P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Ověření připojení VPN

1. Ověřte, že je aktivní připojení k síti VPN. Otevřete příkazový řádek se zvýšenými oprávněními v klientském počítači a spusťte **ipconfig/all**.
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

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Když odeberete kořenový certifikát, klienti, kteří mají s certifikátem vygenerovaným z tohoto kořenového už ověřili a připojili. Pro tyto klienty k ověření a znovu připojit musíte nainstalovat nový klientský certifikát vygenerovaný z kořenového certifikátu, která je důvěryhodná pro Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů .cer s důvěryhodnými kořenovými certifikáty. Pokyny najdete v tématu [nahrání souboru .cer kořenového certifikátu](#upload-the-root-certificate-.cer-file).

### <a name="to-remove-a-trusted-root-certificate"></a>Odebrání důvěryhodného kořenového certifikátu

1. Na **připojení k síti VPN** části na stránce pro vaši virtuální síť, vyberte obrázek klienti otevřete **Point-to-site VPN připojení** stránky.

   ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na **Point-to-site VPN připojení** stránce **Správa certifikátu** otevřít **certifikáty** stránky.

   ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Na **certifikáty** stránky, vyberte tři tečky vedle certifikátu, který chcete odebrat a pak vyberte **odstranit**.

   ![Odstranění kořenového certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Odvolání klientského certifikátu

V případě potřeby můžete odvolat klientský certifikát. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-Site na základě jednotlivých klientských certifikátů. Tato metoda se liší od odebrání důvěryhodného kořenového certifikátu. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování pro připojení typu Point-to-Site další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

### <a name="to-revoke-a-client-certificate"></a>Odvolání klientského certifikátu

Klientský certifikát můžete odvolat tím, že přidáte jeho kryptografický otisk do seznamu odvolaných certifikátů.

1. Načtěte kryptografický otisk klientského certifikátu. Další informace najdete v tématu [jak: Načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).
2. Zkopírujte údaje do textového editoru a odebrat jeho mezery tak, aby se souvislý řetězec.
3. Přejděte k položce klasická virtuální síť. Vyberte **Point-to-site VPN připojení**a pak vyberte **Správa certifikátu** otevřít **certifikáty** stránky.
4. Vyberte **seznamu odvolaných certifikátů** otevřít **seznamu odvolaných certifikátů** stránky. 
5. Vyberte **přidat certifikát** otevřít **přidat certifikát do seznamu odvolaných certifikátů** stránky.
6. V **kryptografický otisk**, vložte kryptografický otisk certifikátu jako souvislý řádek textu bez mezer. Vyberte **OK** na dokončení.

Po dokončení aktualizace už nebude možné certifikát použít k připojení. Klientům, kteří se pokusí připojit pomocí tohoto certifikátu zobrazí zpráva s informacemi o tom, že certifikát již není platný.

## <a name="point-to-site-faq"></a>Nejčastější dotazy k Point-to-Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Další postup

- Po dokončení připojení můžete přidat virtuální počítače k virtuálním sítím. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). 

- Informace o sítích a virtuálních počítačů s Linuxem najdete v tématu [přehled sítě Azure a virtuální počítač s Linuxem](../virtual-machines/linux/network-overview.md).

- Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).