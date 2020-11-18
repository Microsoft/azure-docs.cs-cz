---
title: 'Připojení počítače k virtuální síti pomocí P2S: ověřování certifikátu: Azure Portal Classic'
titleSuffix: Azure VPN Gateway
description: Vytvořte klasické připojení typu Point-to-Site VPN Gateway pomocí Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657070"
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

## <a name="settings-and-requirements"></a>Nastavení a požadavky

### <a name="requirements"></a>Požadavky

Připojení pro ověřování certifikátů Point-to-site vyžadují následující položky. V tomto článku jsou uvedené kroky, které vám pomůžou je vytvořit.

* Dynamickou bránu VPN.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. Tento klíč se považuje za důvěryhodný certifikát a používá se k ověřování.
* Klientský certifikát vygenerovaný z kořenového certifikátu a nainstalovaný na každém klientském počítači, který se bude připojovat. Tento certifikát se používá k ověřování klienta.
* Na každém klientském počítači, který se bude připojovat, musí být vygenerován a nainstalován balíček pro konfiguraci klienta VPN. Konfigurační balíček klienta nakonfiguruje nativního klienta VPN, který už je v operačním systému, a potřebuje informace pro připojení k virtuální síti.

Připojení typu Point-to-site nevyžadují zařízení VPN ani místní veřejnou IP adresu. Připojení VPN se vytváří přes protokol SSTP (Secure Socket Tunneling Protocol). Na straně serveru podporujeme SSTP verze 1.0, 1.1 a 1.2. Klient rozhodne, která verze se má použít. Pro Windows 8.1 a novější se standardně používá SSTP verze 1.2.

Další informace najdete v tématech [o připojeních Point-to-site](point-to-site-about.md) a [Nejčastější dotazy](#faq).

### <a name="example-settings"></a>Příklad nastavení

K vytvoření testovacího prostředí použijte následující hodnoty, nebo tyto hodnoty můžete použít k lepšímu pochopení příkladů v tomto článku:

* **Skupina prostředků:** TestRG
* **Název virtuální sítě:** VNet1
* **Adresní prostor:** 192.168.0.0/16 <br>V tomto příkladu používáme pouze jeden adresní prostor. Pro svoji virtuální síť můžete mít více adresních prostorů.
* **Název podsítě:** FrontEnd
* **Rozsah adres podsítě:** 192.168.1.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Oblast:** (US) východní USA
* **Adresní prostor klienta:** 172.16.201.0/24 <br> Klienti VPN, kteří se připojují k virtuální síti pomocí tohoto připojení Point-to-site, obdrží IP adresu ze zadaného fondu.
* **Typ připojení**: vyberte **Point-to-site**.
* **Rozsah adres GatewaySubnet (blok CIDR):** 192.168.200.0/24

Než začnete, ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Vytvoření virtuální sítě

Pokud již máte virtuální síť vytvořenou, ověřte, zda jsou nastavení kompatibilní s vaším návrhem brány VPN. Věnujte zvláštní pozornost všem podsítím, které by se mohly překrývat s jinými sítěmi.

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>Vytvoření brány VPN

1. Přejděte do virtuální sítě, kterou jste vytvořili.
1. Na stránce virtuální síť vyberte v části nastavení možnost **Brána**. Na stránce **Brána** si můžete zobrazit bránu pro virtuální síť. Tato virtuální síť ještě nemá bránu. Klikněte na poznámku, která uvádí, **jak chcete přidat připojení a bránu, kliknutím sem**.
1. Na stránce **Konfigurace připojení k síti VPN a brány** vyberte následující nastavení:

   * Typ připojení: Point-to-Site
   * Adresní prostor klienta: přidejte rozsah IP adres, ze kterého klienti VPN dostanou IP adresu při připojování. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, nebo s virtuální sítí, ke které se připojujete.
1. V tomto okamžiku nechejte políčko pro možnost **Nekonfigurovat bránu** . Vytvoříme bránu.
1. V dolní části stránky vyberte **Další: brána >**.
1. Na kartě **Brána** vyberte následující hodnoty:

   * **Velikost:** Velikost je SKU brány pro vaši bránu virtuální sítě. V Azure Portal je výchozí SKU **výchozí**. Další informace o SKU brány najdete v tématu [informace o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Typ směrování:** Pro konfiguraci Point-to-site je nutné vybrat možnost **Dynamická** . Statické směrování nebude fungovat.
   * **Podsíť brány:** Toto pole je již vyplněné. Nemůžete změnit název. Pokud se pokusíte změnit název pomocí PowerShellu nebo jakýmkoli jiným způsobem, brána nebude správně fungovat.
   * **Rozsah adres (blok CIDR):** I když je možné vytvořit podsíť brány s minimální velikostí/29, doporučujeme vytvořit větší podsíť, která bude obsahovat více adres, a to tak, že vyberete aspoň/28 nebo/27. Tím umožníte dostatek adres pro přizpůsobení možných dalších konfigurací, které v budoucnu budete chtít. Při práci s podsítěmi brány nepřidružujte skupinu zabezpečení sítě (NSG) k podsíti brány. Přidružení skupiny zabezpečení sítě k této podsíti může způsobit, že vaše brána VPN nebude fungovat podle očekávání.
1. Vyberte **zkontrolovat + vytvořit** a ověřte nastavení.
1. Jakmile ověření projde, vyberte **vytvořit**. Dokončení brány VPN může trvat až 45 minut, v závislosti na vybrané skladové jednotce brány.

## <a name="create-certificates"></a><a name="generatecerts"></a>Vytvoření certifikátů

Azure používá certifikáty k ověřování klientů VPN pro sítě VPN typu Point-to-site. Nahrajete do Azure informace o veřejném klíči kořenového certifikátu. Veřejný klíč se pak považuje za *důvěryhodný*. Klientské certifikáty musí být vygenerovány z důvěryhodného kořenového certifikátu a poté nainstalovány do každého klientského počítače v úložišti certifikátů Certificates-Current User\Personal\Certificates. Certifikát se používá k ověření klienta při připojení k virtuální síti. 

Pokud používáte certifikáty podepsané svým držitelem, musí se vytvořit pomocí určitých parametrů. Certifikát podepsaný svým držitelem můžete vytvořit pomocí pokynů pro [PowerShell a Windows 10](vpn-gateway-certificates-point-to-site.md)nebo [Makecert](vpn-gateway-certificates-point-to-site-makecert.md). Je důležité postupovat podle kroků v těchto pokynech při použití kořenových certifikátů podepsaných svým držitelem a k vygenerování klientských certifikátů z kořenového certifikátu podepsaného svým držitelem. V opačném případě certifikáty, které vytvoříte, nebudou kompatibilní s připojeními P2S a obdržíte chybu připojení.

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>Získání veřejného klíče (. cer) pro kořenový certifikát

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>Vygenerování klientského certifikátu

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>Nahrání souboru .cer kořenového certifikátu

Po vytvoření brány nahrajte soubor. cer (obsahující informace o veřejném klíči) důvěryhodného kořenového certifikátu do serveru Azure. Nenahrávat privátní klíč pro kořenový certifikát. Po nahrání certifikátu ho Azure používá k ověřování klientů, kteří mají nainstalovaný klientský certifikát vygenerovaný z důvěryhodného kořenového certifikátu. Později můžete v případě potřeby nahrávat další soubory důvěryhodných kořenových certifikátů (až 20).

1. Přejděte do virtuální sítě, kterou jste vytvořili.
1. V části **Nastavení** vyberte **připojení Point-to-site**.
1. Vyberte **Spravovat certifikát**.
1. Vyberte **Nahrát**.
1. V podokně **nahrát certifikát** vyberte ikonu složky a přejděte k certifikátu, který chcete nahrát.
1. Vyberte **Nahrát**.
1. Po úspěšném nahrání certifikátu ho můžete zobrazit na stránce Spravovat certifikát. Možná budete muset vybrat **aktualizovat** , aby se zobrazil certifikát, který jste právě Nahráli.

## <a name="configure-the-client"></a>Konfigurace klienta

Aby bylo možné se připojit k virtuální síti pomocí sítě VPN typu Point-to-site, musí každý klient nainstalovat balíček pro konfiguraci nativního klienta VPN systému Windows. Konfigurační balíček konfiguruje nativního klienta VPN ve Windows pomocí nastavení potřebných pro připojení k virtuální síti.

V každém klientském počítači můžete použít stejný konfigurační balíček klienta VPN za předpokladu, že jeho verze odpovídá architektuře klienta. Seznam podporovaných klientských operačních systémů najdete v tématu [o připojeních typu Point-to-site](point-to-site-about.md) a [Nejčastější dotazy](#faq).

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>Generování a instalace balíčku pro konfiguraci klienta VPN

1. Přejděte do nastavení **připojení Point-to-site** pro vaši virtuální síť.
1. V horní části stránky vyberte balíček ke stažení, který odpovídá operačnímu systému klienta, na který se bude instalovat:

   * Pro 64 klientů vyberte **klient VPN (64-bit)**.
   * Pro 32 klientů vyberte **klient VPN (32-bit)**.

1. Azure vytvoří balíček s konkrétním nastavením, které klient vyžaduje. Pokaždé, když provedete změny virtuální sítě nebo brány, potřebujete stáhnout nový balíček pro konfiguraci klienta a nainstalovat ho do klientských počítačů.
1. Po vygenerování balíčku vyberte **Stáhnout**.
1. Nainstalujte balíček pro konfiguraci klienta do klientského počítače. Pokud se při instalaci zobrazí automaticky otevírané okno SmartScreen s informacemi o tom, že systém Windows chráněný vaším počítačem, vyberte **Další informace** a pak vyberte **přesto spustit**. Můžete také balíček uložit k instalaci na další klientské počítače.

### <a name="install-a-client-certificate"></a>Instalace klientského certifikátu

Pro toto cvičení byla při vygenerování klientského certifikátu automaticky nainstalována do počítače. Chcete-li vytvořit připojení P2S z jiného klientského počítače, než který je použit pro vygenerování klientských certifikátů, je nutné na tento počítač nainstalovat vygenerovaný klientský certifikát.

Při instalaci klientského certifikátu budete potřebovat heslo, které bylo vytvořeno při exportu klientského certifikátu. Certifikát můžete obvykle nainstalovat tak, že na něj dvakrát kliknete. Další informace najdete v tématu [Instalace exportovaného klientského certifikátu](vpn-gateway-certificates-point-to-site.md#install).

## <a name="connect-to-your-vnet"></a>Připojení k síti VNet

>[!NOTE]
>Na klientském počítači, že kterého se připojujete, musíte mít oprávnění správce.
>

1. V klientském počítači přejít na nastavení sítě VPN.
1. Vyberte síť VPN, kterou jste vytvořili. Pokud jste použili příklad nastavení, připojení bude označeno jako **Skupina TestRG VNet1**.
1. Vyberte **Connect** (Připojit).
1. V poli Windows Azure Virtual Network vyberte **připojit**. Pokud se zobrazí automaticky otevíraná zpráva o certifikátu, vyberte **pokračovat** , pokud chcete přijímat změny konfigurace, a **Ano** .
1. Po úspěšném připojení se zobrazí **připojené** oznámení.

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>Ověření připojení VPN

1. Ověřte, že je připojení VPN aktivní. Na klientském počítači otevřete příkazový řádek se zvýšenými oprávněními a spusťte příkaz **ipconfig/all**.
1. Zkontrolujte výsledky. Všimněte si, že IP adresa, kterou jste obdrželi, je jedna z adres z rozsahu adres připojení typu Point-to-Site, který jste určili během vytváření vaší virtuální sítě. Výsledek by se měl podobat tomuto příkladu:

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

## <a name="to-connect-to-a-virtual-machine"></a>Připojení k virtuálnímu počítači

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>Přidání a odebrání důvěryhodných kořenových certifikátů

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Když odeberete kořenový certifikát, klienti s certifikátem vygenerovaným z tohoto kořenového adresáře už nebudou moct ověřit a připojit. Aby se klienti mohli znovu ověřit a připojit, musíte nainstalovat nový klientský certifikát vygenerovaný z kořenového certifikátu, který je důvěryhodný pro Azure.

### <a name="add-a-trusted-root-certificate"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů. cer s důvěryhodným kořenovým certifikátem, a to pomocí stejného procesu, který jste použili k přidání prvního důvěryhodného kořenového certifikátu.

### <a name="remove-a-trusted-root-certificate"></a>Odebrání důvěryhodného kořenového certifikátu

1. V části **připojení typu Point-to-site** stránky vaší virtuální sítě vyberte **Spravovat certifikát**.
1. Vyberte tři tečky vedle certifikátu, který chcete odebrat, a pak vyberte **Odstranit**.

## <a name="to-revoke-a-client-certificate"></a>Odvolání klientského certifikátu

V případě potřeby můžete odvolat klientský certifikát. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-Site na základě jednotlivých klientských certifikátů. Tato metoda se liší od odebrání důvěryhodného kořenového certifikátu. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování pro připojení typu Point-to-Site další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

Klientský certifikát můžete odvolat tím, že přidáte jeho kryptografický otisk do seznamu odvolaných certifikátů.

1. Načtěte kryptografický otisk klientského certifikátu. Další informace najdete v tématu [Postup: Načtení kryptografického otisku certifikátu](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Zkopírujte informace do textového editoru a odstraňte mezery, aby se jedná o souvislý řetězec.
1. Přejděte na **připojení VPN typu Point-to-site** a pak vyberte **Spravovat certifikát**.
1. Vyberte **seznam odvolaných certifikátů** a otevřete stránku **seznam odvolaných certifikátů** .
1. V části **kryptografický otisk** vložte kryptografický otisk certifikátu jako jeden souvislý řádek textu bez mezer.
1. Vyberte **+ Přidat k seznamu** a přidejte tak kryptografický otisk do seznamu odvolaných certifikátů (CRL).

Po dokončení aktualizace už nebude možné certifikát použít k připojení. Klientům, kteří se pokusí připojit pomocí tohoto certifikátu, se zobrazí zpráva s informací o tom, že certifikát již není platný.

## <a name="faq"></a><a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>Další kroky

* Po dokončení připojení můžete virtuální počítače přidat do svých virtuálních sítí. Další informace najdete v tématu [Virtuální počítače](../index.yml).

* Další informace o síťových a virtuálních počítačích se systémem Linux najdete v tématu [Přehled sítě virtuálních počítačů Azure a Linux](../virtual-machines/network-overview.md).

* Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).