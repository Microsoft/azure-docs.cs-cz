---
title: 'Připojení k virtuální síti pomocí P2S VPN & ověřování certifikátů: portál'
titleSuffix: Azure VPN Gateway
description: Připojte klienty Windows, Mac OS X a Linux bezpečně ke službě Azure Virtual Network pomocí P2S a certifikátů podepsaných držitelem nebo vydaných certifikační autoritou. V tomto článku se používá Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 03dbc481950ed2a020a26dc3af8668c516b66115
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89435997"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Konfigurace připojení VPN typu Point-to-site k virtuální síti s použitím nativního ověřování certifikátů Azure: Azure Portal

Tento článek vám pomůže bezpečně připojit jednotlivé klienty se systémem Windows, Linux nebo Mac OS X k virtuální síti Azure. Připojení VPN typu Point-to-Site jsou užitečná, když se chcete ke své virtuální síti připojit ze vzdáleného umístění, například při práci z domova nebo z místa konání konference. Místo sítě VPN Site-to-Site můžete také použít P2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Připojení typu Point-to-Site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol) nebo protokolu IKEv2. Další informace o síti VPN Point-to-Site najdete v článku věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).

![Připojení počítače k virtuální síti Azure – diagram připojení Point-to-Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/p2snativeportal.png)

## <a name="architecture"></a>Architektura

Nativní připojení Azure typu Point-to-Site k ověřování certifikátů používají následující položky, které nakonfigurujete v tomto cvičení:

* Bránu VPN typu RouteBased.
* Veřejný klíč (soubor .cer) pro kořenový certifikát nahraný do Azure. Jakmile je certifikát nahraný, považuje za důvěryhodný certifikát a používá se k ověřování.
* Klientský certifikát, který se generuje z kořenového certifikátu. Klientský certifikát nainstalovaný na každém klientském počítači, který se bude připojovat k virtuální síti. Tento certifikát se používá k ověřování klienta.
* Konfigurace klienta VPN. Konfigurační soubory klienta VPN obsahují informace potřebné pro připojení klienta k virtuální síti. Soubory konfigurují stávajícího klienta VPN nativního pro příslušný operační systém. Každý klient, který se připojuje, musí být nakonfigurovaný pomocí nastavení v konfiguračních souborech.

#### <a name="example-values"></a><a name="example"></a>Příklady hodnot

Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů v tomto článku:

* **Název virtuální sítě:** VNet1
* **Adresní prostor:** 10.1.0.0/16<br>V tomto příkladu používáme pouze jeden adresní prostor. Pro svoji virtuální síť můžete mít více adresních prostorů.
* **Název podsítě:** FrontEnd
* **Rozsah adres podsítě:** 10.1.0.0/24
* **Předplatné:** Ujistěte se, že máte správné předplatné, pokud máte více než jedno.
* **Skupina prostředků:** TestRG1
* **Umístění:** Východní USA
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Název brány virtuální sítě:** VNet1GW
* **Typ brány:** S2S
* **Typ sítě VPN:** Založené na trasách
* **Název veřejné IP adresy:** VNet1GWpip
* **Typ připojení:** Point-to-site
* **Fond adres klienta:** 172.16.201.0/24<br>Klienti VPN, kteří se budou k virtuální síti připojovat pomocí tohoto připojení typu Point-to-Site, získají IP adresu ze zadaného fondu adres klienta.

## <a name="1-create-a-virtual-network"></a><a name="createvnet"></a>1. vytvoření virtuální sítě

Než začnete, ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).
[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Vytvoření brány virtuální sítě

V tomto kroku vytvoříte bránu virtuální sítě pro svou virtuální síť. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové jednotce (SKU) brány.

>[!NOTE]
>SKU brány úrovně Basic nepodporuje ověřování IKEv2 nebo RADIUS. Pokud plánujete, že se klienti se systémem Mac připojí k vaší virtuální síti, nepoužívejte základní SKU.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="3-generate-certificates"></a><a name="generatecert"></a>3. generování certifikátů

Azure používá certifikáty k ověřování klientů, kteří se připojují k virtuální síti přes připojení VPN typu Point-to-Site. Jakmile získáte kořenový certifikát, [nahrajete](#uploadfile) do Azure informace o veřejném klíči. Azure pak kořenový certifikát považuje za důvěryhodný pro připojení k virtuální síti přes Point-to-Site. Z kořenového certifikátu také generujete klientské certifikáty, které pak nainstalujete na každém klientském počítači. Klientský certifikát se používá k ověřování klienta při zahájení připojení k virtuální síti. 

### <a name="1-obtain-the-cer-file-for-the-root-certificate"></a><a name="getcer"></a>1. získání souboru. cer pro kořenový certifikát

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="2-generate-a-client-certificate"></a><a name="generateclientcert"></a>2. vygenerujte klientský certifikát.

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. přidejte fond adres klienta.

Fond adres klienta je rozsah privátních IP adres, který zadáte. Klienti připojující se přes síť VPN typu Point-to-Site dynamicky obdrží IP adresu z tohoto rozsahu. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, ani s virtuální sítí, ke které se chcete připojit. Pokud nakonfigurujete více protokolů a SSTP je jedním z protokolů, nakonfigurované fondy adres se rovnoměrně rozdělí mezi nakonfigurované protokoly.

1. Jakmile je brána virtuální sítě vytvořená, přejděte do části **Nastavení** na stránce brány virtuální sítě. V části **Nastavení** vyberte **Konfigurace Point-to-site**. Kliknutím na **Konfigurovat nyní** otevřete stránku konfigurace.

   ![Stránka Point-to-Site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-configure.png "Konfigurace Point-to-site teď")
2. Na stránce **Konfigurace typu Point-to-site** můžete nakonfigurovat celou řadu nastavení. Pokud na této stránce nevidíte Typ tunelového propojení nebo typ ověřování, vaše brána používá základní SKU. Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS. Pokud chcete použít tato nastavení, musíte bránu odstranit a znovu vytvořit pomocí jiné SKU brány.

   [![Stránka Konfigurace Point-to-site](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-address.png "zadat fond adres")](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/certificate-settings-expanded.png#lightbox)
3. Do pole **fond adres** přidejte rozsah privátních IP adres, který chcete použít. Klienti VPN dynamicky obdrží IP adresu z rozsahu, který zadáte. Minimální maska podsítě je 29 bitů pro aktivní/pasivní a 28 bitů pro konfiguraci aktivní/aktivní.
4. Chcete-li nakonfigurovat typ tunelu, přejděte k další části.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. konfigurace typu tunelu

Můžete vybrat typ tunelového propojení. Možnosti tunelu jsou OpenVPN, SSTP a IKEv2.

* Klient strongSwan v Androidu a Linuxu a nativní klient IKEv2 VPN v iOSu a OSX budou pro připojení používat jenom tunel IKEv2.
* Klienti Windows vyzkouší IKEv2 jako první a pokud se to nepřipojí, vrátí se k SSTP.
* Klienta OpenVPN můžete použít pro připojení k typu tunelu OpenVPN.

![Typ tunelového propojení](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel.png "zadat typ tunelu")

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. konfigurace typu ověřování

Jako **typ ověřování**vyberte **certifikát Azure**.

  ![Typ ověřování](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/authentication-type.png "zadat typ ověřování")

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. Nahrajte data veřejného certifikátu kořenového certifikátu.

Můžete nahrát další důvěryhodné kořenové certifikáty až do celkového počtu 20. Jakmile jsou data veřejného certifikátu nahraná, Azure ho může použít k ověřování klientů s nainstalovaným klientským certifikátem vygenerovaným z důvěryhodného kořenového certifikátu. Nahrajte do Azure informace o veřejném klíči pro kořenový certifikát.

1. Certifikáty se přidávají na stránce **Konfigurace Point-to-Site** v části **Kořenový certifikát**.
2. Ujistěte se, že jste kořenový certifikát vyexportovali jako soubor .cer X.509 s kódováním Base-64. Je nutné certifikát exportovat v tomto formátu, abyste ho mohli otevřít v textovém editoru.
3. Otevřete certifikát v textovém editoru, například v Poznámkovém bloku. Při kopírování dat certifikátu se ujistěte, že kopírujete text jako jeden souvislý řádek bez konců řádků nebo odřádkování. Pokud chcete zobrazit konce řádků a odřádkování, může být nutné upravit zobrazení v textovém editoru na Zobrazit symbol / Zobrazit všechny znaky. Zkopírujte pouze tuto část jako jeden souvislý řádek:

   ![Data certifikátu](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png "kopírovat data kořenového certifikátu")
4. Vložte data certifikátu do pole **Data veřejného certifikátu**. **Pojmenujte** certifikát a pak vyberte **Uložit**. Můžete přidat až 20 důvěryhodných kořenových certifikátů.

   ![Vložit data certifikátu](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png "Vložit data certifikátu")
5. V horní části stránky vyberte **Uložit** a uložte všechna nastavení konfigurace.

   ![Uložit konfiguraci](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png "Uložit konfiguraci")

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. instalace exportovaného klientského certifikátu

Pokud chcete vytvořit připojení P2S z jiného klientského počítače, než který jste použili k vytvoření klientských certifikátů, budete muset klientský certifikát nainstalovat. Při instalaci klientského certifikátu budete potřebovat heslo, které bylo vytvořeno při jeho exportu.

Zkontrolujte, že se klientský certifikát vyexportoval jako soubor .pfx spolu s celým řetězem certifikátů (to je výchozí nastavení). Jinak informace o kořenovém certifikátu na klientském počítači nejsou a klient se nebude moct správně ověřit.

Postup instalace najdete v tématu věnovaném [instalaci klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. vygenerujte a nainstalujte konfigurační balíček klienta VPN.

Konfigurační soubory klienta VPN obsahují nastavení pro konfiguraci zařízení, která umožňuje připojit se k virtuální síti přes připojení P2S. Pokyny pro generování a instalaci konfiguračních souborů klienta VPN najdete v tématu věnovaném [vytvoření a instalaci konfiguračních souborů klienta VPN pro konfigurace PS2 s nativním ověřováním certifikátů Azure](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. připojení k Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Připojení z klienta VPN systému Windows

>[!NOTE]
>Na klientském počítači s Windows, že kterého se připojujete, musíte mít oprávnění správce.
>
>

1. Chcete-li se připojit ke své síti VNet, přejděte na klientském počítači na připojení VPN a vyhledejte připojení VPN, které jste vytvořili. Bude mít stejný název jako vaše virtuální síť. Vyberte **Připojit**. Může se zobrazit místní zpráva týkající se použití certifikátu. Pokud chcete používat zvýšená oprávnění, vyberte **pokračovat** .

2. Na stránce Stav **připojení** vyberte **připojit** a spusťte připojení. Pokud uvidíte obrazovku **Výběr certifikátu**, ujistěte se, že zobrazený klientský certifikát je ten, který chcete pro připojení použít. Pokud není, vyberte pomocí šipky rozevíracího seznamu správný certifikát a pak vyberte **OK**.

   ![Připojení klienta VPN k Azure](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/clientconnect.png "připojit")
3. Vaše připojení bylo vytvořeno.

   ![Vytvořené připojení](./media/vpn-gateway-howto-point-to-site-resource-manager-portal/connected.png "navázáno připojení")

#### <a name="troubleshoot-windows-p2s-connections"></a>Řešení potíží s připojeními P2S v systému Windows

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Připojení z klienta VPN systému Mac

V dialogovém okně síť Najděte profil klienta, který chcete použít, zadejte nastavení z [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac)a pak vyberte **připojit**.

Podrobné pokyny najdete v tématu [install-Mac (OS X)](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-vpn-client-configuration-azure-cert#installmac) . Pokud máte potíže s připojením, ověřte, že brána virtuální sítě nepoužívá základní SKU. Základní SKU není pro klienty Mac podporováno.

  ![Připojení v systému Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png "Připojit")

## <a name="to-verify-your-connection"></a><a name="verify"></a>Ověření stavu připojení

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

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Připojení k virtuálnímu počítači

Tyto pokyny platí pro klienty se systémem Windows.

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Přidání a odebrání důvěryhodných kořenových certifikátů

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Když odeberete kořenový certifikát, klienti s certifikátem vygenerovaným z tohoto kořenového certifikátu se nebudou moci ověřit a proto ani připojit. Pokud chcete, aby se klient mohl i nadále ověřovat a připojovat, je nutné nainstalovat nový klientský certifikát vygenerovaný z kořenového certifikátu, který Azure považuje za důvěryhodný (je do Azure nahraný).

### <a name="to-add-a-trusted-root-certificate"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů .cer s důvěryhodnými kořenovými certifikáty. Pokyny najdete v části [Nahrání důvěryhodného kořenového certifikátu](#uploadfile) v tomto článku.

### <a name="to-remove-a-trusted-root-certificate"></a>Odebrání důvěryhodného kořenového certifikátu

1. Pokud chcete odebrat důvěryhodný kořenový certifikát, přejděte na stránku **Konfigurace Point-to-Site** pro vaši bránu virtuální sítě.
2. V části stránky **Kořenový certifikát** vyhledejte certifikát, který chcete odebrat.
3. Vyberte tři tečky vedle certifikátu a pak vyberte Remove (odebrat).

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Odvolání klientského certifikátu

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

## <a name="point-to-site-faq"></a><a name="faq"></a>Nejčastější dotazy týkající se připojení Point-to-Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Další kroky
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](https://docs.microsoft.com/azure/). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/azure-vm-network-overview.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
