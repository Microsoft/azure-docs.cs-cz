---
title: 'Připojení k virtuální síti pomocí P2S VPN & ověřování certifikátů: portál'
titleSuffix: Azure VPN Gateway
description: Připojte klienty Windows, macOS a Linux bezpečně ke službě Azure Virtual Network pomocí certifikátů P2S a certifikátů podepsaných držitelem nebo certifikační autority. V tomto článku se používá Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: 0b2fa06bc04bdb584367312b1e89939ed386b4f2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952844"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-native-azure-certificate-authentication-azure-portal"></a>Konfigurace připojení VPN typu Point-to-site k virtuální síti s použitím nativního ověřování certifikátů Azure: Azure Portal

Tento článek vám pomůže bezpečně připojit jednotlivé klienty se systémem Windows, Linux nebo macOS k virtuální síti Azure. Připojení VPN typu Point-to-Site jsou užitečná, když se chcete ke své virtuální síti připojit ze vzdáleného umístění, například při práci z domova nebo z místa konání konference. Místo sítě VPN Site-to-Site můžete také použít P2S, pokud máte pouze několik klientů, kteří se potřebují připojit k virtuální síti. Připojení typu Point-to-Site nevyžadují zařízení VPN ani veřejnou IP adresu. P2S vytvoří připojení VPN prostřednictvím protokolu SSTP (Secure Socket Tunneling Protocol) nebo protokolu IKEv2. Další informace o síti VPN Point-to-Site najdete v článku věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).

:::image type="content" source="./media\vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Připojení počítače k virtuální síti Azure – diagram připojení typu Point-to-site":::

Další informace o síti VPN typu Point-to-site najdete v tématu věnovaném [síti VPN typu Point-to-site](point-to-site-about.md). Pokud chcete tuto konfiguraci vytvořit pomocí Azure PowerShell, přečtěte si téma [Konfigurace sítě VPN typu Point-to-site pomocí Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

[!INCLUDE [P2S basic architecture](../../includes/vpn-gateway-p2s-architecture.md)]

## <a name="prerequisites"></a>Požadavky

Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial).

### <a name="example-values"></a><a name="example"></a>Příklady hodnot

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

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

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

### <a name="generate-a-root-certificate"></a><a name="getcer"></a>Generování kořenového certifikátu

[!INCLUDE [root-certificate](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-client-certificates"></a><a name="generateclientcert"></a>Generovat klientské certifikáty

[!INCLUDE [generate-client-cert](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="4-add-the-client-address-pool"></a><a name="addresspool"></a>4. přidejte fond adres klienta.

Fond adres klienta je rozsah privátních IP adres, který zadáte. Klienti připojující se přes síť VPN typu Point-to-Site dynamicky obdrží IP adresu z tohoto rozsahu. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, ani s virtuální sítí, ke které se chcete připojit. Pokud nakonfigurujete více protokolů a SSTP je jedním z protokolů, nakonfigurované fondy adres se rovnoměrně rozdělí mezi nakonfigurované protokoly.

1. Jakmile je brána virtuální sítě vytvořená, přejděte do části **Nastavení** na stránce brány virtuální sítě. V **Nastavení** vyberte **Konfigurace Point-to-site**. Kliknutím na **Konfigurovat nyní** otevřete stránku konfigurace.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Stránka Konfigurace Point-to-site" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Na stránce **Konfigurace typu Point-to-site** můžete nakonfigurovat celou řadu nastavení. Pokud na této stránce nevidíte Typ tunelového propojení nebo typ ověřování, vaše brána používá základní SKU. Skladová položka Basic nepodporuje ověřování IKEv2 ani RADIUS. Pokud chcete použít tato nastavení, musíte bránu odstranit a znovu vytvořit pomocí jiné SKU brány.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/address-pool.png" alt-text="Zadat fond adres" lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/address-pool.png":::
1. Do pole **fond adres** přidejte rozsah privátních IP adres, který chcete použít. Klienti VPN dynamicky obdrží IP adresu z rozsahu, který zadáte. Minimální maska podsítě je 29 bitů pro aktivní/pasivní a 28 bitů pro konfiguraci aktivní/aktivní.
1. Pro konfiguraci typu tunelu použijte následující oddíl.

## <a name="5-configure-tunnel-type"></a><a name="tunneltype"></a>5. konfigurace typu tunelu

Vyberte typ tunelu. Možnosti tunelu jsou OpenVPN, SSTP a IKEv2.

* Klient strongSwan v Androidu a Linuxu a nativní klient IKEv2 VPN v iOSu a OSX budou pro připojení používat jenom tunel IKEv2.
* Klienti Windows vyzkouší IKEv2 jako první a pokud se to nepřipojí, vrátí se k SSTP.
* Klienta OpenVPN můžete použít pro připojení k typu tunelu OpenVPN.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/tunnel-ike.png" alt-text="Typ tunelového propojení":::

## <a name="6-configure-authentication-type"></a><a name="authenticationtype"></a>6. konfigurace typu ověřování

Jako **typ ověřování** vyberte **certifikát Azure**.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/azure-certificate.png" alt-text="Typ ověřování":::

## <a name="7-upload-the-root-certificate-public-certificate-data"></a><a name="uploadfile"></a>7. Nahrajte data veřejného certifikátu kořenového certifikátu.

Můžete nahrát další důvěryhodné kořenové certifikáty až do celkového počtu 20. Jakmile jsou data veřejného certifikátu nahraná, Azure ho může použít k ověřování klientů s nainstalovaným klientským certifikátem vygenerovaným z důvěryhodného kořenového certifikátu. Nahrajte do Azure informace o veřejném klíči pro kořenový certifikát.

1. Certifikáty se přidávají na stránce **Konfigurace Point-to-Site** v části **Kořenový certifikát**.
1. Ujistěte se, že jste kořenový certifikát vyexportovali jako soubor .cer X.509 s kódováním Base-64. Je nutné certifikát exportovat v tomto formátu, abyste ho mohli otevřít v textovém editoru.
1. Otevřete certifikát v textovém editoru, například v Poznámkovém bloku. Při kopírování dat certifikátu se ujistěte, že kopírujete text jako jeden souvislý řádek bez konců řádků nebo odřádkování. Pokud chcete zobrazit konce řádků a odřádkování, může být nutné upravit zobrazení v textovém editoru na Zobrazit symbol / Zobrazit všechny znaky. Zkopírujte pouze tuto část jako jeden souvislý řádek:

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/notepadroot.png" alt-text="Data certifikátu" border="false":::
1. Vložte data certifikátu do pole **Data veřejného certifikátu**. **Pojmenujte** certifikát a pak vyberte **Uložit**. Můžete přidat až 20 důvěryhodných kořenových certifikátů.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/uploaded.png" alt-text="Vložit data certifikátu" border="false":::
1. V horní části stránky vyberte **Uložit** a uložte všechna nastavení konfigurace.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/save.png" alt-text="Uložit konfiguraci" border="false":::

## <a name="8-install-an-exported-client-certificate"></a><a name="installclientcert"></a>8. instalace exportovaného klientského certifikátu

Pokud chcete vytvořit připojení P2S z jiného klientského počítače, než který jste použili k vytvoření klientských certifikátů, budete muset klientský certifikát nainstalovat. Při instalaci klientského certifikátu budete potřebovat heslo, které bylo vytvořeno při jeho exportu.

Zkontrolujte, že se klientský certifikát vyexportoval jako soubor .pfx spolu s celým řetězem certifikátů (to je výchozí nastavení). Jinak informace o kořenovém certifikátu na klientském počítači nejsou a klient se nebude moct správně ověřit.

Postup instalace najdete v tématu věnovaném [instalaci klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="9-generate-and-install-the-vpn-client-configuration-package"></a><a name="clientconfig"></a>9. vygenerujte a nainstalujte konfigurační balíček klienta VPN.

Konfigurační soubory klienta VPN obsahují nastavení pro konfiguraci zařízení, která umožňuje připojit se k virtuální síti přes připojení P2S. Pokyny pro generování a instalaci konfiguračních souborů klienta VPN najdete v tématu věnovaném [vytvoření a instalaci konfiguračních souborů klienta VPN pro konfigurace PS2 s nativním ověřováním certifikátů Azure](point-to-site-vpn-client-configuration-azure-cert.md).

## <a name="10-connect-to-azure"></a><a name="connect"></a>10. připojení k Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Připojení z klienta VPN systému Windows

[!INCLUDE [Connect from a Windows client](../../includes/vpn-gateway-p2s-connect-windows-client.md)]

[!INCLUDE [verifies client certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

### <a name="to-connect-from-a-mac-vpn-client"></a>Připojení z klienta VPN systému Mac

V dialogovém okně síť Najděte profil klienta, který chcete použít, zadejte nastavení z [VpnSettings.xml](point-to-site-vpn-client-configuration-azure-cert.md#installmac)a pak vyberte **připojit**.

Podrobné pokyny najdete v tématu [install-Mac (OS X)](./point-to-site-vpn-client-configuration-azure-cert.md#installmac) . Pokud máte potíže s připojením, ověřte, že brána virtuální sítě nepoužívá základní SKU. Základní SKU není pro klienty Mac podporováno.

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png" alt-text="Připojení klienta k síti VPN pro Mac" border="false":::

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

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Ověřte, že se po zadání IP adres serveru DNS pro virtuální síť vygeneroval balíček pro konfiguraci klienta VPN. Pokud jste aktualizovali IP adresy serveru DNS, vygenerujte a nainstalujte nový balíček pro konfiguraci klienta VPN.

* Pomocí příkazu ipconfig zkontrolujte IPv4 adresu přiřazenou adaptéru Ethernet na počítači, ze kterého se připojujete. Pokud je IP adresa v rámci rozsahu adres virtuální sítě, ke které se připojujete, nebo v rámci rozsahu adres VPNClientAddressPool, tato situace se označuje jako překrývající se adresní prostor. Když se adresní prostor tímto způsobem překrývá, síťový provoz nemá přístup do Azure a zůstane v místní síti.

## <a name="to-add-or-remove-trusted-root-certificates"></a><a name="add"></a>Přidání a odebrání důvěryhodných kořenových certifikátů

Důvěryhodný kořenový certifikát můžete do Azure přidat nebo ho z Azure odebrat. Když odeberete kořenový certifikát, klienti s certifikátem vygenerovaným z tohoto kořenového certifikátu se nebudou moci ověřit a proto ani připojit. Pokud chcete, aby se klient mohl i nadále ověřovat a připojovat, je nutné nainstalovat nový klientský certifikát vygenerovaný z kořenového certifikátu, který Azure považuje za důvěryhodný (je do Azure nahraný).

### <a name="to-add-a-trusted-root-certificate"></a>Přidání důvěryhodného kořenového certifikátu

Do Azure můžete přidat až 20 souborů .cer s důvěryhodnými kořenovými certifikáty. Pokyny najdete v části [Nahrání důvěryhodného kořenového certifikátu](#uploadfile) v tomto článku.

### <a name="to-remove-a-trusted-root-certificate"></a>Odebrání důvěryhodného kořenového certifikátu

1. Pokud chcete odebrat důvěryhodný kořenový certifikát, přejděte na stránku **Konfigurace Point-to-Site** pro vaši bránu virtuální sítě.
1. V části stránky **Kořenový certifikát** vyhledejte certifikát, který chcete odebrat.
1. Vyberte tři tečky vedle certifikátu a pak vyberte Remove (odebrat).

## <a name="to-revoke-a-client-certificate"></a><a name="revokeclient"></a>Odvolání klientského certifikátu

Certifikáty klientů lze odvolat. Seznam odvolaných certifikátů umožňuje selektivně odepřít připojení Point-to-Site na základě jednotlivých klientských certifikátů. To se liší od odebrání důvěryhodného kořenového certifikátu. Pokud odeberete z Azure důvěryhodný kořenový certifikát v souboru .cer, dojde k odvolání přístupu pro všechny klientské certifikáty, které byly tímto odvolaným kořenovým certifikátem vygenerovány nebo podepsány. Odvolání klientského certifikátu namísto kořenového certifikátu umožňuje používat k ověřování další certifikáty vygenerované z kořenového certifikátu.

Běžnou praxí je použití kořenového certifikátu pro řízení přístupu na úrovni týmu nebo organizace, přičemž odvolání klientských certifikátů slouží pro detailní kontrolu přístupu jednotlivých uživatelů.

### <a name="revoke-a-client-certificate"></a>Odvolání klientského certifikátu

Klientský certifikát můžete odvolat tím, že přidáte jeho kryptografický otisk do seznamu odvolaných certifikátů.

1. Načtěte kryptografický otisk klientského certifikátu. Další informace najdete v tématu [Postup načtení kryptografického otisku certifikátu](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate).
1. Zkopírujte údaje do textového editoru a smažte všechny mezery, aby vznikl souvislý řetězec.
1. Přejděte na stránku **Konfigurace Point-to-Site** brány virtuální sítě. To je stejná stránka, kterou jste použili k [nahrání důvěryhodného kořenového certifikátu](#uploadfile).
1. V části **Odvolané certifikáty** zadejte popisný název certifikátu (nemusí to být CN certifikátu).
1. Zkopírujte řetězec kryptografického otisku a vložte jej do pole **Kryptografický otisk**.
1. Kryptografický otisk se ověří a automaticky přidá do seznamu odvolaných certifikátů. Na obrazovce se zobrazí zpráva informující o probíhající aktualizaci seznamu. 
1. Po dokončení aktualizace už nebude možné certifikát použít k připojení. Klientům, kteří se pokusí připojit pomocí tohoto certifikátu, se zobrazí zpráva s informací o neplatnosti certifikátu.

## <a name="point-to-site-faq"></a><a name="faq"></a>Nejčastější dotazy týkající se připojení Point-to-Site

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Další kroky
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v tématu [Virtuální počítače](../index.yml). Bližší informace o sítích a virtuálních počítačích najdete v tématu s [přehledem sítě virtuálních počítačů s Linuxem v Azure](../virtual-machines/network-overview.md).

Informace o odstraňování potíží s P2S najdete v článku [Poradce při potížích s připojeními Azure typu point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
