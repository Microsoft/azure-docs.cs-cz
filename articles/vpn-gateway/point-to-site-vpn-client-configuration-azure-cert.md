---
title: 'Vytvoření & instalace konfiguračních souborů klienta VPN P2S: ověření certifikátu'
titleSuffix: Azure VPN Gateway
description: Vytvoření a instalace konfiguračních souborů klienta VPN systému Windows, Linux, Linux (klient strongswan) a macOS X pro ověřování certifikátů P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/11/2020
ms.author: cherylmc
ms.openlocfilehash: c7b186aa1a6f63b1bc3e9dbefa5001faac967762
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94556096"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Vytvoření a instalace konfiguračních souborů klienta VPN pro konfigurace připojení typu point-to-site s využitím nativního ověřování Azure pomocí certifikátů

Konfigurační soubory klienta VPN jsou obsaženy v souboru ZIP. Konfigurační soubory poskytují nastavení požadovaná pro nativní klienty Windows, Mac IKEv2 VPN nebo Linux pro připojení k virtuální síti přes připojení typu Point-to-site, která používají nativní ověřování certifikátů Azure.

Konfigurační soubory klienta jsou specifické pro konfiguraci sítě VPN pro virtuální síť. Pokud v konfiguraci sítě VPN typu Point-to-site dojde ke změnám po vygenerování konfiguračních souborů klienta VPN, jako je například typ protokolu sítě VPN nebo typ ověřování, nezapomeňte vygenerovat nové konfigurační soubory klienta VPN pro vaše uživatelská zařízení.

* Další informace o připojení Point-to-Site najdete v tématu věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).
* Pokyny pro OpenVPN najdete v tématech [Konfigurace OpenVPN pro P2S](vpn-gateway-howto-openvpn.md) a [Konfigurace klientů OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generování konfiguračních souborů klienta VPN

Než začnete, ujistěte se, že všichni připojující se uživatelé mají na zařízení uživatele nainstalovaný platný certifikát. Další informace o instalaci klientského certifikátu najdete v tématu [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

Konfigurační soubory klienta můžete vygenerovat pomocí PowerShellu nebo pomocí Azure Portal. Kterákoli z metod vrátí stejný soubor zip. Rozbalte soubor pro zobrazení následujících složek:

* **WindowsAmd64** a **WindowsX86**, které obsahují instalační balíčky Windows 32-bit a 64, v uvedeném pořadí. Balíček Instalační služby **WindowsAmd64** je pro všechny podporované 64 klienty Windows, ne jenom pro procesory AMD.
* **Obecné**, který obsahuje obecné informace, které se používají k vytvoření vlastní konfigurace klienta VPN. Obecná složka je k dispozici, pokud byla v bráně nakonfigurována IKEv2 nebo SSTP + IKEv2. Je-li nakonfigurován pouze protokol SSTP, není obecná složka k dispozici.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Generovat soubory pomocí Azure Portal

1. V Azure Portal přejděte do brány virtuální sítě pro virtuální síť, ke které se chcete připojit.
1. Na stránce Brána virtuální sítě vyberte **Konfigurace Point-to-site**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/download-client.png" alt-text="Stažení klienta VPN":::
1. V horní části stránky konfigurace typu Point-to-site vyberte možnost **stáhnout klienta VPN**. Generování konfiguračního balíčku klienta může trvat několik minut.
1. V prohlížeči se zobrazí zpráva, že je k dispozici soubor zip konfigurace klienta. Má název stejný jako brána. Rozbalte soubor, aby se zobrazily složky.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generování souborů pomocí PowerShellu

1. Při generování konfiguračních souborů klienta VPN je hodnota pro "-AuthenticationMethod" EapTls ". Pomocí následujícího příkazu vygenerujte konfigurační soubory klienta VPN:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```

1. Zkopírujte adresu URL do prohlížeče a Stáhněte soubor zip a potom rozbalte soubor, abyste viděli složky.

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Windows instructions](../../includes/vpn-gateway-p2s-client-configuration-windows.md)]

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Na každém počítači Mac, který se bude připojovat k Azure, je potřeba ručně nakonfigurovat nativního klienta IKEv2 VPN. Azure neposkytuje soubor mobileconfig pro nativní ověřování certifikátů Azure. **Obecné** obsahuje všechny informace, které potřebujete pro konfiguraci. Pokud složku Generic mezi staženými soubory nevidíte, pravděpodobně jste jako typ tunelu nevybrali IKEv2. Upozorňujeme, že základní skladová položka služby VPN Gateway nepodporuje IKEv2. Jakmile vyberete IKEv2, znovu vygenerujte soubor zip a načtěte složku Generic.<br>Složka Generic obsahuje následující soubory:

* **VpnSettings.xml**, která obsahuje důležitá nastavení jako adresa serveru a typ tunelového propojení. 
* **VpnServerRoot. cer**, který obsahuje kořenový certifikát nutný k ověření služby Azure VPN Gateway během nastavení připojení P2S.

Pomocí následujících kroků můžete nakonfigurovat nativního klienta VPN na Macu pro ověřování certifikátů. Tyto kroky je potřeba provést na všech počítačích Mac, které se budou připojovat k Azure:

1. Importujte kořenový certifikát **VpnServerRoot** do počítače Mac. To se dá udělat tak, že soubor zkopírujete do počítače Mac a dvakrát na něj kliknete. Vyberte **Přidat** pro import.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-certificate.png" alt-text="Snímek obrazovky zobrazující stránku certifikátů":::
  
    >[!NOTE]
    >Dvojím kliknutím na certifikát se nemusí zobrazit dialog **Přidat** , ale certifikát je nainstalovaný ve správném úložišti. Certifikát můžete vyhledat v přihlašovacím řetězci klíčů v kategorii certifikáty.
    >
  
1. Ověřte, že máte nainstalovaný certifikát klienta, který byl vydán kořenovým certifikátem, který jste nahráli do Azure, když jste nakonfigurovali nastavení P2S. To se liší od VPNServerRoot, který jste nainstalovali v předchozím kroku. Certifikát klienta se používá pro ověřování a je povinný. Další informace o generování certifikátů najdete v tématu věnovaném [generování certifikátů](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informace o instalaci klientského certifikátu najdete v tématu [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).
1. Otevřete dialogové okno **síť** v části **Předvolby sítě** a vyberte **+** a vytvořte nový profil připojení klienta VPN pro připojení P2S k virtuální síti Azure.

   Hodnota **rozhraní** je VPN a **Typ VPN Type** je IKEv2. V poli **název služby** zadejte název profilu a pak vyberte **vytvořit** a vytvořte profil připojení klienta VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/network.png" alt-text="Snímek obrazovky se zobrazí okno síť s možností vybrat rozhraní, vybrat typ sítě VPN a zadat název služby.":::
1. V **Obecné** složce v souboru **VpnSettings.xml** Zkopírujte hodnotu značky **VpnServer** . Vložte tuto hodnotu do polí **Adresa serveru** a **vzdálený identifikátor** v profilu.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/server.png" alt-text="Snímek obrazovky ukazuje informace o serveru.":::
1. Vyberte **nastavení ověřování** a vyberte **certifikát**. V **Catalina** vyberte **žádné** a pak na **certifikát**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/authentication-settings.png" alt-text="Snímek obrazovky se zobrazí nastavení ověřování.":::

   V případě Catalina vyberte možnost **žádná** a pak **certifikát**. **Vyberte** správný certifikát:
   
   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png" alt-text="Snímek obrazovky se zobrazí okno síť s možnostmi žádný vybraný pro nastavení ověřování a vybraný certifikát.":::

1. Klikněte na **Vybrat...** pro výběr klientského certifikátu, který chcete použít pro ověřování. Toto je certifikát, který jste nainstalovali v kroku 2.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png" alt-text="Snímek obrazovky se zobrazí okno síť s nastavením ověřování, kde můžete vybrat certifikát.":::
1. **Volba identity** zobrazí seznam certifikátů, ze kterých si můžete vybrat. Vyberte vhodný certifikát a pak vyberte **pokračovat**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/identity.png" alt-text="Snímek obrazovky se zobrazí dialogové okno zvolit identitu, kde můžete vybrat vhodný certifikát.":::

1. Do pole **místní ID** zadejte název certifikátu (z kroku 6). V tomto příkladu je to `ikev2Client.com`. Pak vyberte **použít** a uložte změny.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/apply-connect.png" alt-text="Zobrazí se obrazovka použít.":::
1. V dialogovém okně **síť** vyberte **použít** a uložte všechny změny. Pak vyberte **připojit** a spusťte připojení P2S k virtuální síti Azure.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (grafické rozhraní klient strongswan)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Nainstalovat klient strongswan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Generování certifikátů

Pokud jste ještě vygenerovali certifikáty, použijte následující postup:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Instalace a konfigurace

V Ubuntu 18.0.4 byly vytvořeny následující pokyny. Ubuntu 16.0.10 nepodporuje grafické rozhraní klient strongswan. Pokud chcete použít Ubuntu 16.0.10, budete muset použít [příkazový řádek](#linuxinstallcli). Níže uvedené příklady nemusí odpovídat obrazovkám, které vidíte v závislosti na vaší verzi systému Linux a klient strongswan.

1. Otevřete **terminál** pro instalaci **klient strongswan** a jeho správce sítě spuštěním příkazu v příkladu.

   ```
   sudo apt install network-manager-strongswan
   ```
1. Vyberte **Nastavení** a pak vybrat **síť**. Vyberte **+** tlačítko pro vytvoření nového připojení.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/edit-connections.png" alt-text="Snímek obrazovky se zobrazí stránka síťová připojení.":::

1. V nabídce vyberte **IPSec/IKEv2 (klient strongswan)** a dvakrát klikněte na tlačítko.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/add-connection.png" alt-text="Snímek obrazovky se zobrazí stránka Přidat síť VPN.":::

1. Na stránce **Přidat síť VPN** přidejte název připojení k síti VPN.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/choose-type.png" alt-text="Snímek obrazovky znázorňující výběr typu připojení.":::
1. Otevřete soubor **VpnSettings.xml** z **Obecné** složky obsažené ve stažených konfiguračních souborech klienta. Vyhledejte značku s názvem **VpnServer** a zkopírujte název začínající na ' azuregateway ' a končící na '. cloudapp.NET '.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/vpn-server.png" alt-text="Snímek obrazovky ukazuje kopírování dat.":::
1. Do pole **adresa** nového připojení k síti VPN v části **Brána** vložte název. V dalším kroku vyberte ikonu složky na konci pole **certifikát** , přejděte do složky **Obecné** a vyberte soubor **VpnServerRoot** .
1. V části **klient** tohoto připojení vyberte pro **ověřování** **certifikát/privátní klíč**. V části **certifikát** a **privátní klíč** vyberte certifikát a privátní klíč, který jste vytvořili dříve. V **Možnosti** vyberte možnost **požádat o vnitřní IP adresu**. Pak vyberte **Přidat**.

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/ip-request.png" alt-text="Snímek obrazovky s požadavkem zobrazí vnitřní IP adresu.":::

1. Zapněte připojení **.**

   :::image type="content" source="./media/point-to-site-vpn-client-configuration-azure-cert/turn-on.png" alt-text="Snímek obrazovky znázorňující kopii":::

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (rozhraní příkazového řádku klient strongswan)

### <a name="install-strongswan"></a>Nainstalovat klient strongswan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generování certifikátů

Pokud jste ještě vygenerovali certifikáty, použijte následující postup:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalace a konfigurace

1. Stáhněte si balíček VPNClient z Azure Portal.
1. Soubor extrahujte.
1. Z **Obecné** složky zkopírujte nebo přesuňte **VpnServerRoot. cer** do **/etc/IPSec.d/cacerts**.
1. Zkopírujte nebo přesuňte **CP Client. p12** na **/etc/IPSec.d/Private/**. Tento soubor je klientským certifikátem pro bránu VPN.
1. Otevřete soubor **VpnSettings.xml** a zkopírujte `<VpnServer>` hodnotu. Tuto hodnotu použijete v dalším kroku.
1. Upravte hodnoty v následujícím příkladu a pak přidejte příklad do konfigurace **/etc/IPSec.conf** .
  
   ```
   conn azure
         keyexchange=ikev2
         type=tunnel
         leftfirewall=yes
         left=%any
         leftauth=eap-tls
         leftid=%client # use the DNS alternative name prefixed with the %
         right= Enter the VPN Server value here# Azure VPN gateway address
         rightid=% # Enter the VPN Server value here# Azure VPN gateway FQDN with %
         rightsubnet=0.0.0.0/0
         leftsourceip=%config
         auto=add
   ```
1. Do **/etc/IPSec.Secrets** přidejte následující hodnoty.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

1. Spusťte následující příkazy:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Další kroky

Vraťte se k původnímu článku, ze kterého jste pracovali, a pak dokončete konfiguraci P2S.

* [Kroky konfigurace PowerShellu](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Postup konfigurace Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).
