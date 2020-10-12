---
title: 'Vytvoření & instalace konfiguračních souborů klienta VPN P2S: ověření certifikátu'
titleSuffix: Azure VPN Gateway
description: Vytvoření a instalace konfiguračních souborů klienta VPN systému Windows, Linux, Linux (klient strongswan) a macOS X pro ověřování certifikátů P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 3a7a9711f04a4b54459d57a05ae65b4eedf1d6b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986635"
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
2. Na stránce Brána virtuální sítě klikněte na **Konfigurace Point-to-site**.

   ![Stáhnout klientský portál](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. V horní části stránky konfigurace typu Point-to-site klikněte na **stáhnout klienta VPN**. Generování konfiguračního balíčku klienta může trvat několik minut.
4. V prohlížeči se zobrazí zpráva, že je k dispozici soubor zip konfigurace klienta. Má název stejný jako brána. Rozbalte soubor, aby se zobrazily složky.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generování souborů pomocí PowerShellu


1. Při generování konfiguračních souborů klienta VPN je hodnota pro "-AuthenticationMethod" EapTls ". Pomocí následujícího příkazu vygenerujte konfigurační soubory klienta VPN:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Zkopírujte adresu URL do prohlížeče a Stáhněte soubor zip a potom rozbalte soubor, abyste viděli složky.

## <a name="windows"></a><a name="installwin"></a>Windows

Stejný konfigurační balíček klienta VPN můžete použít na každém klientském počítači s Windows, pokud verze odpovídá architektuře pro klienta. Seznam podporovaných klientských operačních systémů najdete v části [Nejčastější dotazy k VPN Gateway](vpn-gateway-vpn-faq.md#P2S)v části Point-to-site.

>[!NOTE]
>Musíte mít oprávnění správce na klientském počítači s Windows, ze kterého se chcete připojit.
>
>

Pomocí následujících kroků nakonfigurujte nativního klienta VPN systému Windows pro ověřování certifikátů:

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. V případě 64bitové architektury procesoru zvolte instalační balíček VpnClientSetupAmd64. V případě 32bitové architektury procesoru zvolte instalační balíček VpnClientSetupX86. 
2. Dvakrát klikněte na balíček a nainstalujte ho. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace**a potom na **přesto spustit**.
3. Na klientském počítači přejděte do části **Nastavení sítě** a klikněte na možnost **Síť VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje. 
4. Než se pokusíte o připojení, ověřte, že jste na klientském počítači nainstalovali klientský certifikát. Klientský certifikát se vyžaduje k ověřování při použití typu nativního ověřování certifikátů Azure. Další informace o generování certifikátů najdete v tématu věnovaném [generování certifikátů](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informace o instalaci klientského certifikátu najdete v tématu [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Na každém počítači Mac, který se bude připojovat k Azure, je potřeba ručně nakonfigurovat nativního klienta IKEv2 VPN. Azure neposkytuje soubor mobileconfig pro nativní ověřování certifikátů Azure. **Obecné** obsahuje všechny informace, které potřebujete pro konfiguraci. Pokud složku Generic mezi staženými soubory nevidíte, pravděpodobně jste jako typ tunelu nevybrali IKEv2. Upozorňujeme, že základní skladová položka služby VPN Gateway nepodporuje IKEv2. Jakmile vyberete IKEv2, znovu vygenerujte soubor zip a načtěte složku Generic.<br>Složka Generic obsahuje následující soubory:

* **VpnSettings.xml**, která obsahuje důležitá nastavení jako adresa serveru a typ tunelového propojení. 
* **VpnServerRoot. cer**, který obsahuje kořenový certifikát nutný k ověření služby Azure VPN Gateway během nastavení připojení P2S.

Pomocí následujících kroků můžete nakonfigurovat nativního klienta VPN na Macu pro ověřování certifikátů. Tyto kroky je potřeba provést na všech počítačích Mac, které se budou připojovat k Azure:

1. Importujte kořenový certifikát **VpnServerRoot** do počítače Mac. To se dá udělat tak, že soubor zkopírujete do počítače Mac a dvakrát na něj kliknete. Pro import klikněte na **Přidat** .

   ![Přidat certifikát](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Dvojím kliknutím na certifikát se nemusí zobrazit dialog **Přidat** , ale certifikát je nainstalovaný ve správném úložišti. Certifikát můžete vyhledat v přihlašovacím řetězci klíčů v kategorii certifikáty.
    >
  
2. Ověřte, že máte nainstalovaný certifikát klienta, který byl vydán kořenovým certifikátem, který jste nahráli do Azure, když jste nakonfigurovali nastavení P2S. To se liší od VPNServerRoot, který jste nainstalovali v předchozím kroku. Certifikát klienta se používá pro ověřování a je povinný. Další informace o generování certifikátů najdete v tématu věnovaném [generování certifikátů](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informace o instalaci klientského certifikátu najdete v tématu [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Otevřete dialogové okno **síť** v části **Předvolby sítě** a kliknutím na **+** vytvořte nový profil připojení klienta VPN pro připojení P2S k virtuální síti Azure.

   Hodnota **rozhraní** je VPN a **Typ VPN Type** je IKEv2. Do pole **název služby** zadejte název profilu a potom kliknutím na **vytvořit** vytvořte profil připojení klienta VPN.

   ![Snímek obrazovky se zobrazí okno síť s možností vybrat rozhraní, vybrat typ sítě VPN a zadat název služby.](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. V **Obecné** složce v souboru **VpnSettings.xml** Zkopírujte hodnotu značky **VpnServer** . Vložte tuto hodnotu do polí **Adresa serveru** a **vzdálený identifikátor** v profilu.

   ![informace o serveru](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klikněte na **nastavení ověřování** a vyberte **certifikát**.V **Catalina**klikněte na **žádná** a pak na **certifikát** .

   ![nastavení ověřování](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * V případě Catalina vyberte možnost **žádná** a pak **certifikát**. **Vyberte** správný certifikát:
   
   ![Snímek obrazovky se zobrazí okno síť s možnostmi žádný vybraný pro nastavení ověřování a vybraný certifikát.](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Klikněte na **Vybrat...** pro výběr klientského certifikátu, který chcete použít pro ověřování. Toto je certifikát, který jste nainstalovali v kroku 2.

   ![Snímek obrazovky se zobrazí okno síť s nastavením ověřování, kde můžete vybrat certifikát.](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Volba identity** zobrazí seznam certifikátů, ze kterých si můžete vybrat. Vyberte vhodný certifikát a potom klikněte na **pokračovat**.

   ![Snímek obrazovky se zobrazí dialogové okno zvolit identitu, kde můžete vybrat vhodný certifikát.](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Do pole **místní ID** zadejte název certifikátu (z kroku 6). V tomto příkladu je to "ikev2Client.com". Potom kliknutím na tlačítko **použít** uložte změny.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. V dialogovém okně **síť** klikněte na **použít** a uložte všechny změny. Pak klikněte na **připojit** a spusťte připojení P2S k virtuální síti Azure.

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
2. Vyberte **Nastavení**a pak vybrat **síť**.

   ![Upravit připojení](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Kliknutím na **+** tlačítko vytvořte nové připojení.

   ![Přidat připojení](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. V nabídce vyberte **IPSec/IKEv2 (klient strongswan)** a dvakrát klikněte na tlačítko. V tomto kroku můžete své připojení pojmenovat.

   ![zvolit typ připojení](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Otevřete soubor **VpnSettings.xml** z **Obecné** složky obsažené ve stažených konfiguračních souborech klienta. Vyhledejte značku s názvem **VpnServer** a zkopírujte název začínající na ' azuregateway ' a končící na '. cloudapp.NET '.

   ![Kopírovat název](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Tento název vložte do pole **adresa** nového připojení k síti VPN v části **Brána** . V dalším kroku vyberte ikonu složky na konci pole **certifikát** , přejděte do složky **Obecné** a vyberte soubor **VpnServerRoot** .
7. V části **klient** tohoto připojení vyberte pro **ověřování** **certifikát/privátní klíč**. V části **certifikát** a **privátní klíč**vyberte certifikát a privátní klíč, který jste vytvořili dříve. V **Možnosti**vyberte možnost **požádat o vnitřní IP adresu**. Pak klikněte na tlačítko **Přidat**.

   ![požádat o vnitřní IP adresu](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Zapněte připojení **.**

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (rozhraní příkazového řádku klient strongswan)

### <a name="install-strongswan"></a>Nainstalovat klient strongswan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generování certifikátů

Pokud jste ještě vygenerovali certifikáty, použijte následující postup:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalace a konfigurace

1. Stáhněte si balíček VPNClient z Azure Portal.
2. Extrahujte soubor.
3. Z **Obecné** složky zkopírujte nebo přesuňte VpnServerRoot. cer do/etc/IPSec.d/cacerts..
4. Kopírovat nebo přesunout klienta CP Client. p12 na/etc/IPSec.d/Private/. Tento soubor je klientský certifikát pro Azure VPN Gateway.
5. Otevřete soubor VpnSettings.xml a zkopírujte `<VpnServer>` hodnotu. Tuto hodnotu použijete v dalším kroku.
6. Upravte hodnoty v následujícím příkladu a pak přidejte příklad do konfigurace/etc/IPSec.conf.
  
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
6. Do */etc/IPSec.Secrets*přidejte následující.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Spusťte následující příkazy:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Další kroky

Vraťte se k článku a [dokončete konfiguraci P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Řešení potíží s připojením P2S naleznete v následujících článcích:

  * [Řešení potíží s připojením Point-to-Site z Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Řešení potíží s připojením VPN od klientů VPN macOS X](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
