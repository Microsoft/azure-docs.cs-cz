---
title: 'Připojení počítače k virtuální síti typu Point-to-Site s použitím ověření certifikátu: Portál Azure Classic | Dokumentace Microsoftu'
description: Vytvoříte připojení classic k bráně VPN typu Point-to-Site pomocí portálu Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: 5d80cb2f2ed844126d1e9311151e6c53fcb11840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244573"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>Konfigurace připojení typu Point-to-Site pomocí ověřování certifikátu (klasické)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Tento článek ukazuje, jak vytvořit virtuální síť s připojením point-to-site. Tento virtuální síť vytvoříte pomocí klasického modelu nasazení pomocí portálu Azure. Tato konfigurace používá certifikáty k ověření připojujícího se klienta, buď podepsané svým držitelem (self-signed certificate), nebo vydané certifikační autoritou. Tuto konfiguraci můžete také vytvořit pomocí jiného nástroje nebo modelu nasazení pomocí možností popsaných v následujících článcích:

> [!div class="op_single_selector"]
> * [Portál Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

Bránu VPN point-to-site (P2S) slouží k vytvoření zabezpečeného připojení k virtuální síti z jednotlivých klientských počítačů. Připojení VPN point-to-site jsou užitečná, když se chcete připojit k virtuální síti ze vzdáleného umístění. Pokud máte jenom několik klientů, kteří se potřebují připojit k virtuální síti, je p2s VPN užitečným řešením, které se používá místo sítě VPN site-to-Site. Připojení VPN P2S se vytváří jeho zahájením z klientského počítače.

> [!IMPORTANT]
> Model nasazení Classic podporuje pouze klienty VPN systému Windows a používá protokol SSTP (Secure Socket Tunneling Protocol), což je protokol VPN založený na protokolu SSL. Chcete-li podporovat klienty vpn než Windows, musíte vytvořit virtuální síť s modelem nasazení Správce prostředků. Model nasazení Resource Manager podporuje kromě protokolu SSTP i IKEv2 VPN. Další informace najdete v tématu [Informace o připojeních typu Point-to-Site](point-to-site-about.md).
>
>

![Diagram Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="prerequisites"></a>Požadavky

Připojení ověřování certifikátů typu Point-to-Site vyžadují následující požadavky:

* Dynamickou bránu VPN.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. Tento klíč je považován za důvěryhodný certifikát a používá se k ověřování.
* Klientský certifikát vygenerovaný z kořenového certifikátu a nainstalovaný na každém klientském počítači, který se bude připojovat. Tento certifikát se používá k ověřování klienta.
* Na každém klientském počítači, který se bude připojovat, musí být vygenerován a nainstalován balíček pro konfiguraci klienta VPN. Konfigurační balíček klienta konfiguruje nativního klienta VPN, který je už v operačním systému, s potřebnými informacemi pro připojení k virtuální síti.

Připojení z bodu na web nevyžadují zařízení VPN ani místní veřejnou IP adresu. Připojení VPN se vytváří přes protokol SSTP (Secure Socket Tunneling Protocol). Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2. 

Další informace o připojení chodna k webu naleznete v [tématu Nejčastější dotazy k bodu na pracoviště](#point-to-site-faq).

### <a name="example-settings"></a>Příklad nastavení

Použijte následující hodnoty k vytvoření testovacího prostředí nebo se podívejte na tyto hodnoty, abyste lépe porozuměli příkladům v tomto článku:

- **Vytvoření nastavení virtuální sítě (klasické)**
   - **Název**: Zadejte *virtuální síť 1*.
   - **Adresní prostor**: Zadejte *192.168.0.0/16*. V tomto příkladu používáme pouze jeden adresní prostor. Pro svoji virtuální síť můžete mít více adresních prostorů, jak ukazuje diagram.
   - **Název podsítě**: Zadejte *frontend*.
   - **Rozsah adres podsítě**: Zadejte *192.168.1.0/24*.
   - **Předplatné**: Vyberte předplatné ze seznamu dostupných předplatných.
   - **Skupina prostředků**: Zadejte *TestRG*. Vyberte **Vytvořit nový**, pokud skupina prostředků neexistuje.
   - **Umístění**: Ze seznamu vyberte **možnost Východ USA.**

  - **Nastavení připojení VPN**
    - **Typ připojení**: Vyberte **možnost Point-to-site**.
    - **Adresní prostor klienta**: Zadejte *172.16.201.0/24*. Klienti VPN, kteří se připojují k virtuální síti pomocí tohoto připojení point-to-site, obdrží ip adresu ze zadaného fondu.

- **Nastavení konfigurační podsítě brány**
   - **Název**: Automaticky vyplněno *programem GatewaySubnet*.
   - **Rozsah adres**: Zadejte *192.168.200.0/24*. 

- **Nastavení konfigurace brány**:
   - **Velikost**: Vyberte skladovou položku brány, kterou chcete použít.
   - **Typ směrování**: Vyberte **možnost Dynamické**.

## <a name="create-a-virtual-network-and-a-vpn-gateway"></a>Vytvoření virtuální sítě a brány VPN

Než začnete, ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="part-1-create-a-virtual-network"></a>Část 1: Vytvoření virtuální sítě

Pokud ještě nemáte virtuální síť (VNet), vytvořte ji. Snímky obrazovek slouží jen jako příklady. Hodnoty na obrázcích nahraďte vlastními hodnotami. Pokud chcete vytvořit virtuální síť přes Azure Portal, použijte následující postup:

1. V nabídce [Portál Azure](https://portal.azure.com) nebo na **domovské** stránce vyberte **Vytvořit prostředek**. Otevře se **nová** stránka.

2. Do pole **Hledat na tržišti** zadejte *virtuální síť* a ze vrácenýho seznamu vyberte **Virtuální síť.** Otevře se stránka **Virtuální síť.**

3. V seznamu **Vybrat model nasazení** vyberte **Klasické**a pak vyberte **Vytvořit**. Otevře se stránka **Vytvořit virtuální síť.**

4. Na stránce **Vytvořit virtuální síť** nakonfigurujte nastavení virtuální sítě. Na této stránce přidáte první adresní prostor a jeden rozsah adres podsítě. Po dokončení vytváření sítě VNet můžete přejít zpět a přidat další podsítě a adresní prostory.

   ![Stránka pro vytvoření virtuální sítě](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/vnet125.png)

5. V rozevíracím seznamu vyberte **předplatné,** které chcete použít.

6. Vyberte existující **skupinu prostředků**. Nebo vytvořte novou skupinu prostředků tak, že vyberete **Vytvořit nový** a zadáte název. Pokud vytváříte novou skupinu prostředků, pojmenujte skupinu prostředků podle plánovaných hodnot konfigurace. Další informace o skupinách prostředků najdete [v tématu Přehled Správce prostředků Azure](../azure-resource-manager/management/overview.md#resource-groups).

7. Vyberte **umístění** pro virtuální síť. Toto nastavení určuje geografické umístění prostředků, které nasadíte do této virtuální sítě.

8. Vyberte **Vytvořit,** chcete-li virtuální síť vytvořit. Na stránce **Oznámení** se zobrazí zpráva **O probíhajícím nasazení.**

8. Po vytvoření virtuální sítě byla zpráva na stránce **Oznámení** **úspěšná**. Pokud chcete virtuální síť snadno najít na řídicím panelu, vyberte **Připnout na řídicí panel.** 

10. Přidejte server DNS (volitelné). Po vytvoření virtuální sítě můžete přidat IP adresu serveru DNS pro překlad IP adres. Zadaná IP adresa serveru DNS by měla být adresa serveru DNS, který dokáže přeložit názvy pro prostředky ve vaší virtuální síti.

    Pokud chcete přidat DNS server, vyberte **servery DNS** ze stránky virtuální sítě. Potom zadejte adresu IP serveru DNS, který chcete použít, a vyberte **uložit**.

### <a name="part-2-create-a-gateway-subnet-and-a-dynamic-routing-gateway"></a>Část 2: Vytvoření podsítě brány a dynamické brány směrování

V tomto kroku vytvoříte podsíť brány a dynamickou bránu směrování. Na portálu Azure pro klasický model nasazení vytvoříte podsíť brány a bránu prostřednictvím stejných konfiguračních stránek. Podsíť brány používejte pouze pro služby brány. Nikdy nenasazujte nic (například virtuální počítače nebo jiné služby) přímo do podsítě brány.

1. Na webu Azure Portal přejděte do virtuální sítě, pro kterou chcete vytvořit bránu.

2. Na stránce virtuální sítě vyberte **Přehled**a v části **Připojení VPN** vyberte **Brána**.

   ![Výběrem vytvoříte bránu.](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/beforegw125.png)
3. Na stránce **Nové připojení VPN** vyberte **Point-to-Site**.

   ![Připojení typu Point-to-Site](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/newvpnconnect.png)
4. V **části Klientský adresní prostor**přidejte rozsah IP adres, ze kterého klienti VPN při připojování obdrží adresu IP. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, nebo s virtuální sítí, ke které se připojujete. Rozsah automatického plnění můžete přepsat rozsahem privátních IP adres, který chcete použít. Tento příklad ukazuje rozsah automatického plnění. 

   ![Klientský adresní prostor](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clientaddress.png)
5. Vyberte **Vytvořit bránu okamžitě**a pak vyberte **Volitelná konfigurace brány,** abyste otevřeli **konfigurační** stránku Brány.

   ![Výběr volitelné konfigurace brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/optsubnet125.png)

6. Na stránce **konfigurace brány** vyberte **Podsíť** a přidejte podsíť brány. Je možné vytvořit podsíť brány tak malé jako /29. Doporučujeme však vytvořit větší podsíť, která obsahuje více adres výběrem alespoň /28 nebo /27. To umožní dostatek adres pro případné další konfigurace, které budete chtít v budoucnu. Při práci s podsítěmi brány nepřidružujte skupinu zabezpečení sítě (NSG) k podsíti brány. Připojování skupiny zabezpečení sítě k této podsíti může způsobit, že brána VPN nebude fungovat očekávaným způsobem. Chcete-li toto nastavení uložit, vyberte **ok.**

   ![Přidání podsítě brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsubnet125.png)
7. Vyberte **velikost** brány. Velikost je skladová položka brány pro vaši bránu virtuální sítě. Na webu Azure Portal je výchozí skladová položka **výchozí**. Další informace o su položkách brány naleznete v tématu [O nastavení brány VPN](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

   ![Velikost brány](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/gwsize125.png)
8. Vyberte pro bránu **typ směrování**. Konfigurace P2S vyžadují **dynamický** typ směrování. Po dokončení konfigurace této stránky vyberte **OK.**

   ![Konfigurace typu směrování](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/routingtype125.png)

9. Na stránce **Nové připojení VPN** vyberte **ok** v dolní části stránky a začněte vytvářet bránu virtuální sítě. Dokončení brány VPN může trvat až 45 minut v závislosti na vybrané skladové jednotce brány.
 
## <a name="create-certificates"></a><a name="generatecerts"></a>Vytvoření certifikátů

Azure používá certifikáty k ověřování klientů VPN pro VPN typu Point-to-Site. Nahrajete do Azure informace o veřejném klíči kořenového certifikátu. Veřejný klíč je pak považován za *důvěryhodný*. Klientské certifikáty musí být generovány z důvěryhodného kořenového certifikátu a poté nainstalovány v každém klientském počítači v úložišti certifikátů Current User\Personal\Certificates. Certifikát se používá k ověření klienta při připojení k virtuální síti. 

Pokud používáte certifikáty podepsané svým držitelem, musí být vytvořeny pomocí specifických parametrů. Certifikát podepsaný svým držitelem můžete vytvořit pomocí pokynů pro [Prostředí PowerShell a Windows 10](vpn-gateway-certificates-point-to-site.md)nebo [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Při použití kořenových certifikátů podepsaných svým držitelem a generování klientských certifikátů z kořenového certifikátu podepsaného svým držitelem je důležité postupovat podle těchto kroků. V opačném případě nebudou certifikáty, které vytvoříte, kompatibilní s připojeními P2S a zobrazí se chyba připojení.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Získání veřejného klíče (.cer) pro kořenový certifikát

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Vygenerování klientského certifikátu

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Nahrání souboru .cer kořenového certifikátu

Po vytvoření brány nahrajte soubor CER (který obsahuje informace o veřejném klíči) pro důvěryhodný kořenový certifikát na server Azure. Nenahrávejte soukromý klíč pro kořenový certifikát. Po nahrání certifikátu ho Azure použije k ověření klientů, kteří nainstalovali klientský certifikát generovaný z důvěryhodného kořenového certifikátu. Později můžete v případě potřeby nahrát další důvěryhodné soubory kořenových certifikátů (až 20).  

1. V části **Připojení VPN** na stránce virtuální sítě vyberte grafiku klientů a otevřete stránku připojení VPN **typu Point-to-site.**

   ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na stránce **připojení VPN typu Point-to-site** vyberte **Spravovat certifikát,** chcete-li otevřít stránku **Certifikáty.**

   ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

1. Na stránce **Certifikáty** vyberte **Nahrát,** abyste otevřeli stránku **Nahrát certifikát.**

    ![Stránka pro nahrání certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/uploadcerts.png)

4. Vyberte grafiku složky, kterou chcete vyhledat soubor CER. Vyberte soubor a pak vyberte **OK**. Nahraný certifikát se zobrazí na stránce **Certifikáty.**

   ![Nahrání certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/upload.png)


## <a name="configure-the-client"></a>Konfigurace klienta

Chcete-li se připojit k virtuální síti pomocí sítě VPN s bodem na lokalitu, musí každý klient nainstalovat balíček pro konfiguraci nativního klienta VPN systému Windows. Konfigurační balíček konfiguruje nativního klienta VPN ve Windows pomocí nastavení potřebných pro připojení k virtuální síti.

V každém klientském počítači můžete použít stejný konfigurační balíček klienta VPN za předpokladu, že jeho verze odpovídá architektuře klienta. Seznam podporovaných klientských operačních systémů naleznete v [nejčastějších dotazech k připojení mj.](#point-to-site-faq)

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generování a instalace konfiguračního balíčku klienta VPN

1. Na webu Azure Portal na stránce **Přehled** pro vaši virtuální síť **vyberte**v připojení VPN grafiku klienta a otevřete stránku **připojení VPN typu Point-to-site.**

2. Na stránce **připojení VPN bodu na místo** vyberte balíček pro stahování, který odpovídá klientském operačnímu systému, ve kterém je nainstalován:

   * U 64bitových klientů vyberte **Klient VPN (64bitový)**.
   * U 32bitových klientů vyberte **Klient VPN (32bitový)**.

   ![Stažení balíčku pro konfiguraci klienta VPN](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/dlclient.png)

3. Po generování balíčku jej stáhněte a nainstalujte do klientského počítače. Pokud se zobrazí vyskakovací okno SmartScreen, vyberte **Další informace**a pak **stejně**vyberte Spustit . Můžete také balíček uložit k instalaci na další klientské počítače.

### <a name="install-a-client-certificate"></a>Instalace klientského certifikátu

Chcete-li vytvořit připojení P2S z jiného klientského počítače, než který se používá ke generování klientských certifikátů, nainstalujte klientský certifikát. Při instalaci klientského certifikátu potřebujete heslo, které bylo vytvořeno při exportu klientského certifikátu. Certifikát můžete obvykle nainstalovat pouhým poklepáním. Další informace najdete v tématu [Instalace exportovaného klientského certifikátu](vpn-gateway-certificates-point-to-site.md#install).


## <a name="connect-to-your-vnet"></a>Připojení k síti VNet

>[!NOTE]
>Na klientském počítači, že kterého se připojujete, musíte mít oprávnění správce.
>
>

1. Chcete-li se připojit k virtuální síti, v klientském počítači přejděte na **připojení VPN** na webu Azure Portal a vyhledejte připojení VPN, které jste vytvořili. Připojení VPN má stejný název jako vaše virtuální síť. Vyberte **Connect** (Připojit). Pokud se zobrazí vyskakovací zpráva o certifikátu, vyberte **Pokračovat v** používání zvýšených oprávnění.

2. Na stránce **Stav připojení** vyberte **Připojit** a spusťte připojení. Pokud se zobrazí obrazovka **Vybrat certifikát,** ověřte, zda je zobrazený klientský certifikát správný. Pokud ne, vyberte správný certifikát z rozevíracího seznamu a pak vyberte **OK**.

3. Pokud je připojení úspěšné, zobrazí se **oznámení O připojení.**


### <a name="troubleshooting-p2s-connections"></a>Řešení potíží s připojeními P2S

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Ověření připojení VPN

1. Ověřte, zda je připojení VPN aktivní. Otevřete příkazový řádek se zvýšenými oprávněními v klientském počítači a spusťte **ipconfig/all**.
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

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Při odebrání kořenového certifikátu se klienti, kteří mají certifikát generovaný z tohoto kořenového adresáře, již nemohou ověřovat a připojovat. Aby se tito klienti měli znovu ověřovat a připojovat, musíte nainstalovat nový klientský certifikát generovaný z kořenového certifikátu, který je důvěryhodný pro Azure.

### <a name="to-add-a-trusted-root-certificate"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů .cer s důvěryhodnými kořenovými certifikáty. Pokyny naleznete v tématu Nahrání kořenového souboru CER certifikátu.

### <a name="to-remove-a-trusted-root-certificate"></a>Odebrání důvěryhodného kořenového certifikátu

1. V části **Připojení VPN** na stránce virtuální sítě vyberte grafiku klientů a otevřete stránku připojení VPN **typu Point-to-site.**

   ![Klienti](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/clients125.png)

2. Na stránce **připojení VPN typu Point-to-site** vyberte **Spravovat certifikát,** chcete-li otevřít stránku **Certifikáty.**

   ![Stránka certifikátů](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/ptsmanage.png)

3. Na stránce **Certifikáty** vyberte tři tečky vedle certifikátu, který chcete odebrat, a pak vyberte **Odstranit**.

   ![Odstranění kořenového certifikátu](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/deleteroot.png)

## <a name="revoke-a-client-certificate"></a>Odvolání klientského certifikátu

V případě potřeby můžete odvolat klientský certifikát. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-Site na základě jednotlivých klientských certifikátů. Tato metoda se liší od odebrání důvěryhodného kořenového certifikátu. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování pro připojení typu Point-to-Site další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

### <a name="to-revoke-a-client-certificate"></a>Odvolání klientského certifikátu

Klientský certifikát můžete odvolat tím, že přidáte jeho kryptografický otisk do seznamu odvolaných certifikátů.

1. Načtěte kryptografický otisk klientského certifikátu. Další informace naleznete v [tématu How to: Retrieve the Thumbprint of a Certificate](https://msdn.microsoft.com/library/ms734695.aspx).
2. Zkopírujte informace do textového editoru a odeberte jejich mezery tak, aby se znějích propojovalsouvisle.
3. Přejděte do klasické virtuální sítě. Vyberte **připojení VPN typu Point-to-site**, pak vyberte **Spravovat certifikát** a otevřete stránku **Certifikáty.**
4. Výběrem **seznamu odvolání** otevřete stránku **seznamu Odvolání.** 
5. Výběrem **možnosti Přidat certifikát** otevřete stránku **seznamu Přidat certifikát k odvolání.**
6. V **kryptografickém potisku**vložte kryptografický otisk certifikátu jako jeden souvislý řádek textu bez mezer. Chcete-li dokončit, vyberte **možnost OK.**

Po dokončení aktualizace už nebude možné certifikát použít k připojení. Klienti, kteří se pokusí připojit pomocí tohoto certifikátu, obdrží zprávu, že certifikát již není platný.

## <a name="point-to-site-faq"></a>Nejčastější dotazy týkající se připojení Point-to-Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Další kroky

- Po dokončení připojení můžete do virtuálních sítí přidat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/). 

- Další informace o síťových a linuxových virtuálních počítačích najdete v [tématu Přehled sítě virtuálních počítačů Azure a Linuxu](../virtual-machines/linux/network-overview.md).

- Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
