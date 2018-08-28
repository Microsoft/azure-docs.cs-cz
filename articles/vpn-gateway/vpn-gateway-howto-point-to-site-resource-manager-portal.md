---
title: 'Připojení počítače k virtuální síti Azure typu Point-to-Site s použitím nativního ověřování certifikátů Azure: Azure Portal | Dokumentace Microsoftu'
description: Připojíte zabezpečeně klienty Windows a Mac OS X k virtuální síti Azure pomocí P2S (Point-to-Site) a certifikátů podepsaných svým držitelem (self-signed certificate) nebo vydaných certifikační autoritou. V tomto článku se používá Azure Portal.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: a15ad327-e236-461f-a18e-6dbedbf74943
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2018
ms.author: cherylmc
ms.openlocfilehash: 4fd82c110590c3aa5ede729b317fb722e58cc406
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "41918621"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Konfigurace připojení typu Point-to-Site k virtuální síti s použitím nativního ověřování certifikátů Azure: Azure Portal

Tento článek vám pomůže zabezpečeně připojit jednotlivé klienty se systémem Windows nebo Mac OS X k virtuální síti Azure. Připojení VPN typu Point-to-Site jsou užitečná, když se chcete ke své virtuální síti připojit ze vzdáleného umístění, například při práci z domova nebo z místa konání konference. Místo sítě VPN Site-to-Site můžete také použít P2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Připojení typu Point-to-Site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol) nebo protokolu IKEv2. Další informace o síti VPN Point-to-Site najdete v článku věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).

![Připojení počítače k virtuální síti Azure – diagram připojení Point-to-Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)


## <a name="architecture"></a>Architektura

Nativní připojení Azure typu Point-to-Site k ověřování certifikátů používají následující položky, které nakonfigurujete v tomto cvičení:

* Bránu VPN typu RouteBased.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. Jakmile je certifikát nahraný, považuje za důvěryhodný certifikát a používá se k ověřování.
* Klientský certifikát, který se generuje z kořenového certifikátu. Klientský certifikát nainstalovaný na každém klientském počítači, který se bude připojovat k virtuální síti. Tento certifikát se používá k ověřování klienta.
* Konfigurace klienta VPN. Konfigurační soubory klienta VPN obsahují informace potřebné pro připojení klienta k virtuální síti. Soubory konfigurují stávajícího klienta VPN nativního pro příslušný operační systém. Každý klient, který se připojuje, musí být nakonfigurovaný pomocí nastavení v konfiguračních souborech.

#### <a name="example"></a>Příklady hodnot

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku:

* **Název virtuální sítě:** VNet1
* **Adresní prostor:** 192.168.0.0/16<br>V tomto příkladu používáme pouze jeden adresní prostor. Pro svoji virtuální síť můžete mít více adresních prostorů.
* **Název podsítě:** FrontEnd
* **Rozsah adres podsítě:** 192.168.1.0/24
* **Předplatné:** Ujistěte se, že máte správné předplatné, pokud máte více než jedno.
* **Skupina prostředků:** TestRG
* **Umístění:** Východní USA
* **Podsíť brány:** 192.168.200.0/24<br>
* **Server DNS:** (volitelné) IP adresa serveru DNS, který chcete používat pro překlad názvů.
* **Název brány virtuální sítě:** VNet1GW
* **Typ brány:** Síť VPN
* **Typ sítě VPN:** Založená na trasách
* **Název veřejné IP adresy:** VNet1GWpip
* **Typ připojení:** Point-to-Site
* **Fond adres klienta:** 172.16.201.0/24<br>Klienti VPN, kteří se budou k virtuální síti připojovat pomocí tohoto připojení typu Point-to-Site, získají IP adresu ze zadaného fondu adres klienta.

## <a name="createvnet"></a>1. Vytvoření virtuální sítě

Než začnete, ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-p2s-vnet-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>2. Přidání podsítě brány

Před připojením virtuální sítě k bráně musíte nejdříve vytvořit podsíť brány pro virtuální síť, ke které se chcete připojit. Služby brány používají IP adresy určené v podsíti brány. Pokud je to možné, vytvořte podsíť brány s použitím bloku CIDR /28 nebo /27, aby byl k dispozici dostatek IP adres pro plnění dalších požadavků na konfiguraci v budoucnu.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-p2s-rm-portal-include.md)]

## <a name="dns"></a>3. Určení serveru DNS (volitelné)

Po vytvoření virtuální sítě můžete přidat IP adresu serveru DNS, aby bylo možné zpracovávat překlad názvů. Server DNS je pro tuto konfiguraci volitelný, ale vyžaduje se, pokud chcete překlad názvů. Zadání hodnoty nevytvoří nový server DNS. Server DNS, jehož IP adresu zadáte, by měl být server DNS, který dokáže přeložit názvy pro prostředky, ke kterým se připojujete. V tomto příkladu jsme použili privátní IP adresu, ale je pravděpodobné, že to není IP adresa vašeho serveru DNS. Je potřeba, abyste použili svoje vlastní hodnoty. Hodnotu, kterou zadáte, použijí prostředky, které nasadíte do virtuální sítě, a ne připojení P2S nebo klient VPN.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="creategw"></a>4. Vytvoření brány virtuální sítě

[!INCLUDE [create-gateway](../../includes/vpn-gateway-add-gw-p2s-rm-portal-include.md)]

>[!NOTE]
>Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS.
>

## <a name="generatecert"></a>5. Generování certifikátů

Azure používá certifikáty k ověřování klientů, kteří se připojují k virtuální síti přes připojení VPN typu Point-to-Site. Jakmile získáte kořenový certifikát, [nahrajete](#uploadfile) do Azure informace o veřejném klíči. Azure pak kořenový certifikát považuje za důvěryhodný pro připojení k virtuální síti přes Point-to-Site. Z kořenového certifikátu také generujete klientské certifikáty, které pak nainstalujete na každém klientském počítači. Klientský certifikát se používá k ověřování klienta při zahájení připojení k virtuální síti. 

### <a name="getcer"></a>1. Získání souboru .cer pro kořenový certifikát

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generateclientcert"></a>2. Vygenerování klientského certifikátu

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="addresspool"></a>6. Přidání fondu adres klienta

Fond adres klienta je rozsah privátních IP adres, který zadáte. Klienti připojující se přes síť VPN typu Point-to-Site dynamicky obdrží IP adresu z tohoto rozsahu. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, ani s virtuální sítí, ke které se chcete připojit.

1. Jakmile je brána virtuální sítě vytvořená, přejděte do části **Nastavení** na stránce brány virtuální sítě. V části **Nastavení** klikněte na **Konfigurace Point-to-site (P2S)**.

  ![Stránka Point-to-Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/gatewayblade.png) 
2. Klikněte na tlačítko **Nakonfigurovat**, aby se otevřela stránka s konfigurací.

  ![Nakonfigurovat](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configurenow.png)
3. Na stránce konfigurace **Point-to-Site** přidejte do pole **Fond adres** rozsah privátních IP adres, který chcete použít. Klienti VPN dynamicky obdrží IP adresu z rozsahu, který zadáte. Kliknutím na **Uložit** ověřte a uložte nastavení.

  ![Fond adres klienta](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/addresspool.png)

  >[!NOTE]
  >Pokud se na této stránce portálu nezobrazí typ tunelu nebo typ ověřování, vaše brána používá skladovou položku Basic. Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS.
  >

## <a name="tunneltype"></a>7. Konfigurace typu tunelového propojení

Můžete vybrat typ tunelového propojení. Dostupné jsou dvě možnosti, SSTP a IKEv2. Klient strongSwan v Androidu a Linuxu a nativní klient IKEv2 VPN v iOSu a OSX budou pro připojení používat jenom tunel IKEv2. Klienti Windows nejdřív vyzkoušejí IKEv2 a pokus se nepřipojí, přejdou zpátky k SSTP. Můžete si vybrat, jestli povolíte jednu z nich nebo obě. Zaškrtněte políčka, která vyžaduje vaše řešení.

![Typ tunelového propojení](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunneltype.png)

## <a name="authenticationtype"></a>8. Konfigurace typu ověřování

Vyberte **certifikát Azure**.

  ![Typ tunelového propojení](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authenticationtype.png)

## <a name="uploadfile"></a>9. Nahrání dat veřejného klíče kořenového certifikátu

Můžete nahrát další důvěryhodné kořenové certifikáty až do celkového počtu 20. Jakmile jsou data veřejného certifikátu nahraná, Azure ho může použít k ověřování klientů s nainstalovaným klientským certifikátem vygenerovaným z důvěryhodného kořenového certifikátu. Nahrajte do Azure informace o veřejném klíči pro kořenový certifikát.

1. Certifikáty se přidávají na stránce **Konfigurace Point-to-Site** v části **Kořenový certifikát**.
2. Ujistěte se, že jste kořenový certifikát vyexportovali jako soubor .cer X.509 s kódováním Base-64. Je nutné certifikát exportovat v tomto formátu, abyste ho mohli otevřít v textovém editoru.
3. Otevřete certifikát v textovém editoru, například v Poznámkovém bloku. Při kopírování dat certifikátu se ujistěte, že kopírujete text jako jeden souvislý řádek bez konců řádků nebo odřádkování. Pokud chcete zobrazit konce řádků a odřádkování, může být nutné upravit zobrazení v textovém editoru na Zobrazit symbol / Zobrazit všechny znaky. Zkopírujte pouze tuto část jako jeden souvislý řádek:

  ![Data certifikátu](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png)
4. Vložte data certifikátu do pole **Data veřejného certifikátu**. Zadejte **Název** certifikátu a pak klikněte na tlačítko **Uložit**. Můžete přidat až 20 důvěryhodných kořenových certifikátů.

  ![Nahrání certifikátu](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png)
5. Uložte všechna konfigurační nastavení kliknutím na **Uložit** v horní části stránky.

  ![Uložení](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png)

## <a name="installclientcert"></a>10. Instalace exportovaného klientského certifikátu

Pokud chcete vytvořit připojení P2S z jiného klientského počítače, než který jste použili k vytvoření klientských certifikátů, budete muset klientský certifikát nainstalovat. Při instalaci klientského certifikátu budete potřebovat heslo, které bylo vytvořeno při jeho exportu.

Zkontrolujte, že se klientský certifikát vyexportoval jako soubor .pfx spolu s celým řetězem certifikátů (to je výchozí nastavení). Jinak informace o kořenovém certifikátu na klientském počítači nejsou a klient se nebude moct správně ověřit.

Postup instalace najdete v tématu věnovaném [instalaci klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="clientconfig"></a>11. Vygenerování a instalace konfiguračního balíčku klienta VPN

Konfigurační soubory klienta VPN obsahují nastavení pro konfiguraci zařízení, která umožňuje připojit se k virtuální síti přes připojení P2S. Pokyny pro generování a instalaci konfiguračních souborů klienta VPN najdete v tématu věnovaném [vytvoření a instalaci konfiguračních souborů klienta VPN pro konfigurace PS2 s nativním ověřováním certifikátů Azure](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="connect"></a>12. Připojení k Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Připojení z klienta VPN systému Windows

>[!NOTE]
>Na klientském počítači s Windows, že kterého se připojujete, musíte mít oprávnění správce.
>
>

1. Chcete-li se připojit ke své síti VNet, přejděte na klientském počítači na připojení VPN a vyhledejte připojení VPN, které jste vytvořili. Bude mít stejný název jako vaše virtuální síť. Klikněte na **Připojit**. Může se zobrazit místní zpráva týkající se použití certifikátu. Klikněte na **Pokračovat** pro použití zvýšených oprávnění.

2. Připojení spustíte kliknutím na tlačítko **Připojit** na stavové stránce **Připojení**. Pokud uvidíte obrazovku **Výběr certifikátu**, ujistěte se, že zobrazený klientský certifikát je ten, který chcete pro připojení použít. Pokud není, vyberte pomocí šipky rozevíracího seznamu správný certifikát, a poté klikněte na **OK**.

  ![Připojení klienta VPN k Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png)
3. Vaše připojení bylo vytvořeno.

  ![Vytvořené připojení](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png)

#### <a name="troubleshoot-windows-p2s-connections"></a>Řešení potíží s připojeními P2S v systému Windows

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Připojení z klienta VPN systému Mac

V dialogovém okně Síť vyhledejte klientský profil, který chcete použít, zadejte nastavení ze souboru [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac) a pak klikněte na **Připojit**.

Podrobné pokyny najdete v části [Instalace – Mac (OS X)](https://docs.microsoft.com/en-us/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac).

  ![Připojení v systému Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="verify"></a>Ověření stavu připojení

Tyto pokyny platí pro klienty se systémem Windows.

1. Chcete-li ověřit, zda je připojení VPN aktivní, v příkazovém řádku se zvýšenými oprávněními spusťte příkaz *ipconfig/all*.
2. Zkontrolujte výsledky. Všimněte si, že IP adresa, kterou jste obdrželi, je jedna z adres z fondu adres klienta VPN připojení Point-to-Site, který jste určili během konfigurace. Výsledky jsou podobné tomuto příkladu:

  ```
  PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
  ```

## <a name="connectVM"></a>Připojení k virtuálnímu počítači

Tyto pokyny platí pro klienty se systémem Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="add"></a>Přidání a odebrání důvěryhodných kořenových certifikátů

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Když odeberete kořenový certifikát, klienti s certifikátem vygenerovaným z tohoto kořenového certifikátu se nebudou moci ověřit a proto ani připojit. Pokud chcete, aby se klient mohl i nadále ověřovat a připojovat, je nutné nainstalovat nový klientský certifikát vygenerovaný z kořenového certifikátu, který Azure považuje za důvěryhodný (je do Azure nahraný).

### <a name="to-add-a-trusted-root-certificate"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů .cer s důvěryhodnými kořenovými certifikáty. Pokyny najdete v části [Nahrání důvěryhodného kořenového certifikátu](#uploadfile) v tomto článku.

### <a name="to-remove-a-trusted-root-certificate"></a>Odebrání důvěryhodného kořenového certifikátu

1. Pokud chcete odebrat důvěryhodný kořenový certifikát, přejděte na stránku **Konfigurace Point-to-Site** pro vaši bránu virtuální sítě.
2. V části stránky **Kořenový certifikát** vyhledejte certifikát, který chcete odebrat.
3. Klikněte na tři tečky vedle certifikátu a potom klikněte na Odebrat.

## <a name="revokeclient"></a>Odvolání klientského certifikátu

Certifikáty klientů lze odvolat. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-Site na základě jednotlivých klientských certifikátů. To se liší od odebrání důvěryhodného kořenového certifikátu. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

### <a name="revoke-a-client-certificate"></a>Odvolání klientského certifikátu

Klientský certifikát můžete odvolat tím, že přidáte jeho kryptografický otisk do seznamu odvolaných certifikátů.

1. Načtěte kryptografický otisk klientského certifikátu. Další informace najdete v tématu [Postup načtení kryptografického otisku certifikátu](https://msdn.microsoft.com/library/ms734695.aspx).
2. Zkopírujte údaje do textového editoru a smažte všechny mezery, aby vznikl souvislý řetězec.
3. Přejděte na stránku **Konfigurace Point-to-Site** brány virtuální sítě. To je stejná stránka, kterou jste použili k [nahrání důvěryhodného kořenového certifikátu](#uploadfile).
4. V části **Odvolané certifikáty** zadejte popisný název certifikátu (nemusí to být CN certifikátu).
5. Zkopírujte řetězec kryptografického otisku a vložte jej do pole **Kryptografický otisk**.
6. Kryptografický otisk se ověří a automaticky přidá do seznamu odvolaných certifikátů. Na obrazovce se zobrazí zpráva informující o probíhající aktualizaci seznamu. 
7. Po dokončení aktualizace už nebude možné certifikát použít k připojení. Klientům, kteří se pokusí připojit pomocí tohoto certifikátu, se zobrazí zpráva s informací o neplatnosti certifikátu.

## <a name="faq"></a>Nejčastější dotazy týkající se připojení Point-to-Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Další kroky
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/#pivot=services&panel=Compute). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/azure-vm-network-overview.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
