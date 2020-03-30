---
title: 'Vytvoření & instalaci konfiguračních souborů klienta P2S VPN: ověřování certifikátů'
titleSuffix: Azure VPN Gateway
description: Vytvořte a nainstalujte konfigurační soubory klienta Windows, Linux, Linux (strongSwan) a Mac OS X VPN pro ověřování certifikátů P2S.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/04/2020
ms.author: cherylmc
ms.openlocfilehash: d15efee635e131d658cd650b7f80eb9e670a0dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279413"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Vytvoření a instalace konfiguračních souborů klienta VPN pro nativní konfigurace P2S ověřování certifikátů Azure

Konfigurační soubory klienta VPN jsou obsaženy v souboru ZIP. Konfigurační soubory poskytují nastavení vyžadované pro nativní klienty Windows, Mac IKEv2 VPN nebo Linux, aby se připojili k virtuální síti přes připojení typu Point-to-Site, která používají nativní ověřování certifikátů Azure.

Konfigurační soubory klienta jsou specifické pro konfiguraci sítě VPN pro virtuální síť. Pokud po vygenerování konfiguračních souborů klienta VPN, jako je typ protokolu VPN nebo typ ověřování, dojde ke změnám konfigurace sítě POINT-TO-Site, nezapomeňte pro uživatelská zařízení vygenerovat nové konfigurační soubory klienta VPN. 

* Další informace o připojení Point-to-Site najdete v tématu věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).
* Pokyny pro OpenVPN [najdete v tématech Konfigurace OpenVPN pro Klienty P2S](vpn-gateway-howto-openvpn.md) a [Konfigurace OpenVPN](vpn-gateway-howto-openvpn-clients.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generovat konfigurační soubory klienta VPN

Než začnete, ujistěte se, že všichni připojující se uživatelé mají na zařízení uživatele nainstalován platný certifikát. Další informace o instalaci klientského certifikátu naleznete [v tématu Instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

Konfigurační soubory klientů můžete generovat pomocí PowerShellu nebo pomocí portálu Azure. Obě metody vrátí stejný soubor zip. Chcete-li zobrazit následující složky, rozbalte soubor:

  * **Systémy WindowsAmd64** a **WindowsX86**, které obsahují 32bitové a 64bitové instalační balíčky systému Windows. Instalační balíček **systému WindowsAmd64** je určen pro všechny podporované 64bitové klienty systému Windows, nikoli pouze pro službu Amd.
  * **Obecný**, který obsahuje obecné informace použité k vytvoření vlastní konfigurace klienta VPN. Obecná složka je k dispozici, pokud iKEv2 nebo SSTP + IKEv2 byl nakonfigurován na bráně. Pokud je nakonfigurován pouze SSTP, pak obecná složka není k dispozici.

### <a name="generate-files-using-the-azure-portal"></a><a name="zipportal"></a>Generování souborů pomocí portálu Azure

1. Na webu Azure Portal přejděte na bránu virtuální sítě pro virtuální síť, ke které se chcete připojit.
2. Na stránce brány virtuální sítě klikněte na **Konfigurace bodu na pracoviště**.

   ![stáhnout klientský portál](./media/point-to-site-vpn-client-configuration-azure-cert/client-configuration-portal.png)
3. V horní části stránky konfigurace point-to-site klikněte na **Stáhnout klienta VPN**. Generování konfiguračního balíčku klienta trvá několik minut.
4. Prohlížeč označuje, že je k dispozici soubor zip konfigurace klienta. Má stejný název jako brána. Chcete-li zobrazit složky, rozbalte soubor.

### <a name="generate-files-using-powershell"></a><a name="zipps"></a>Generování souborů pomocí PowerShellu


1. Při generování konfiguračních souborů klienta VPN je hodnota pro metodu AuthenticationMethod "EapTls". Vygenerujte konfigurační soubory klienta VPN pomocí následujícího příkazu:

   ```azurepowershell-interactive
   $profile=New-AzVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

   $profile.VPNProfileSASUrl
   ```
2. Zkopírujte adresu URL do prohlížeče a stáhněte soubor ZIP a potom soubor rozbalte a zobrazte složky.

## <a name="windows"></a><a name="installwin"></a>Windows

Můžete použít stejný balíček konfigurace klienta VPN v každém klientském počítači se systémem Windows, pokud verze odpovídá architektuře pro klienta. Seznam podporovaných klientských operačních systémů naleznete v části Point-to-Site v [nejčastějších dotazech k bráně VPN](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>V klientském počítači se systémem Windows, ze kterého se chcete připojit, musíte mít práva správce.
>
>

Pomocí následujících kroků nakonfigurujte nativního klienta VPN systému Windows pro ověřování certifikátů:

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. V případě 64bitové architektury procesoru zvolte instalační balíček VpnClientSetupAmd64. V případě 32bitové architektury procesoru zvolte instalační balíček VpnClientSetupX86. 
2. Dvakrát klikněte na balíček a nainstalujte ho. Pokud se zobrazí vyskakovací okno SmartScreen, klikněte na **Další informace**a **stejně spustit**.
3. Na klientském počítači přejděte do části **Nastavení sítě** a klikněte na možnost **Síť VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje. 
4. Než se pokusíte o připojení, ověřte, že jste na klientském počítači nainstalovali klientský certifikát. Klientský certifikát se vyžaduje k ověřování při použití typu nativního ověřování certifikátů Azure. Další informace o generování certifikátů naleznete v [tématu Generování certifikátů](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informace o instalaci klientského certifikátu naleznete v [tématu Instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="mac-os-x"></a><a name="installmac"></a>Mac (OS X)

 Na každém počítači Mac, který se bude připojovat k Azure, je potřeba ručně nakonfigurovat nativního klienta IKEv2 VPN. Azure neposkytuje soubor mobileconfig pro nativní ověřování certifikátů Azure. **Obecný** obsahuje všechny informace, které potřebujete pro konfiguraci. Pokud složku Generic mezi staženými soubory nevidíte, pravděpodobně jste jako typ tunelu nevybrali IKEv2. Všimněte si, že základní skladová položka brány VPN nepodporuje iKEv2. Jakmile vyberete IKEv2, znovu vygenerujte soubor zip a načtěte složku Generic.<br>Složka Generic obsahuje následující soubory:

* **Soubor VPNSettings.xml**, který obsahuje důležitá nastavení, jako je adresa serveru a typ tunelového propojení. 
* **VpnServerRoot.cer**, který obsahuje kořenový certifikát potřebný k ověření brány Azure VPN během nastavení připojení P2S.

Pomocí následujících kroků nakonfigurujte nativního klienta VPN na Macu pro ověřování certifikátů. Tyto kroky musíte provést na každém Macu, který se připojí k Azure:

1. Importujte kořenový certifikát **VpnServerRoot** do macu. To lze provést zkopírováním souboru do počítače Mac a dvojitým kliknutím na něj. Kliknutím na **Přidat** importujte.

   ![přidat certifikát](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Poklepánína na certifikát nemusí zobrazit dialogové okno **Přidat,** ale certifikát je nainstalován ve správném úložišti. Certifikát můžete zkontrolovat v přihlašovací klíčence v kategorii certifikátů.
    >
  
2. Ověřte, že jste nainstalovali klientský certifikát vydaný kořenovým certifikátem, který jste nahráli do Azure při konfiguraci nastavení P2S. To se liší od kořenové služby VPNServerRoot, který jste nainstalovali v předchozím kroku. Klientský certifikát se používá k ověřování a je vyžadován. Další informace o generování certifikátů naleznete v [tématu Generování certifikátů](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informace o instalaci klientského certifikátu naleznete v [tématu Instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Otevřete dialogové okno **Síť** v části **Předvolby sítě** a kliknutím na **tlačítko +vytvořte** nový profil připojení klienta VPN pro připojení P2S k virtuální síti Azure.

   Hodnota **rozhraní** je "VPN" a hodnota **typu VPN** je IKEv2. Do pole **Název služby** zadejte název profilu a kliknutím na **Vytvořit** vytvořte profil připojení klienta VPN.

   ![network](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. V **obecné** složce zkopírujte ze souboru **VpnSettings.xml** hodnotu značky **VpnServer.** Vložte tuto hodnotu do polí **Adresa serveru** a **Vzdálené ID** profilu.

   ![informace o serveru](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klepněte na **položku Nastavení ověřování** a vyberte **možnost Certifikát**.V **části Catalina**klikněte na **Žádný** a potom na **certifikát.**

   ![nastavení ověřování](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)

   * V části Catalina vyberte **možnost Žádný** a potom **certifikát**. **Vyberte** správný certifikát:
   
   ![Catalina](./media/point-to-site-vpn-client-configuration-azure-cert/catalina.png)

6. Klikněte na **Vybrat...** zvolte klientský certifikát, který chcete použít pro ověřování. Toto je certifikát, který jste nainstalovali v kroku 2.

   ![certifikát](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Zvolte Identita** zobrazí seznam certifikátů, ze kterých si můžete vybrat. Vyberte správný certifikát a klepněte na tlačítko **Pokračovat**.

   ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. Do pole **Místní ID** zadejte název certifikátu (z kroku 6). V tomto příkladu je "ikev2Client.com". Potom kliknutím na **tlačítko Použít** změny uložte.

   ![apply](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. V dialogovém okně **Síť** klikněte na **Použít** a uložte všechny změny. Potom kliknutím na **Připojit** spusťte připojení P2S k virtuální síti Azure.

## <a name="linux-strongswan-gui"></a><a name="linuxgui"></a>Linux (strongSwan GUI)

### <a name="install-strongswan"></a><a name="installstrongswan"></a>Nainstalujte strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a><a name="genlinuxcerts"></a>Generování certifikátů

Pokud jste ještě nevygenerovali certifikáty, postupujte takto:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a><a name="install"></a>Instalace a konfigurace

Následující pokyny byly vytvořeny na Ubuntu 18.0.4. Ubuntu 16.0.10 nepodporuje silnéSwan GUI. Pokud chcete používat Ubuntu 16.0.10, budete muset použít [příkazový řádek](#linuxinstallcli). Níže uvedené příklady nemusí odpovídat obrazovky, které vidíte, v závislosti na verzi Linuxu a strongSwan.

1. Otevřete **terminál** pro instalaci **strongSwan** a jeho Network Manager spuštěním příkazu v příkladu.

   ```
   sudo apt install network-manager-strongswan
   ```
2. Vyberte **Nastavení**a pak vyberte **možnost Síť**.

   ![úpravy připojení](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Kliknutím **+** na tlačítko vytvořte nové připojení.

   ![přidání připojení](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. V nabídce vyberte **IPsec/IKEv2 (strongSwan)** a poklepejte. V tomto kroku můžete pojmenovat připojení.

   ![výběr typu připojení](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Otevřete soubor **VpnSettings.xml** ze **složky Obecné** obsažené ve stažených konfiguračních souborech klienta. Najděte značku s názvem **VpnServer** a zkopírujte název, počínaje 'azuregateway' a končící '.cloudapp.net'.

   ![kopírovat název](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Vložte tento název do pole **Adresa** nového připojení VPN v části **Brána.** Dále vyberte ikonu složky na konci pole **Certifikát,** přejděte do **obecné** složky a vyberte soubor **VpnServerRoot.**
7. V části **Klient** připojení v části **Ověřování**vyberte **možnost Certifikát/soukromý klíč**. V **případě certifikátu** a **soukromého klíče**zvolte certifikát a soukromý klíč, které byly vytvořeny dříve. V **části Možnosti**vyberte **možnost Požadovat vnitřní adresu IP**. Potom klepněte na tlačítko **Přidat**.

   ![požádat o vnitřní IP adresu](./media/point-to-site-vpn-client-configuration-azure-cert/turnon.png)
8. Zapněte **připojení**.

## <a name="linux-strongswan-cli"></a><a name="linuxinstallcli"></a>Linux (strongSwan CLI)

### <a name="install-strongswan"></a>Nainstalujte strongSwan

[!INCLUDE [install strongSwan](../../includes/vpn-gateway-strongswan-install-include.md)]

### <a name="generate-certificates"></a>Generování certifikátů

Pokud jste ještě nevygenerovali certifikáty, postupujte takto:

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

### <a name="install-and-configure"></a>Instalace a konfigurace

1. Stáhněte si balíček VPNClient z webu Azure Portal.
2. Extrahujte soubor.
3. Ze **složky Generic** zkopírujte nebo přesuňte soubor VpnServerRoot.cer na /etc/ipsec.d/cacerts.
4. Zkopírujte nebo přesuňte cp client.p12 na /etc/ipsec.d/private/. Tento soubor je klientský certifikát pro azure vpn gateway.
5. Otevřete soubor VpnSettings.xml `<VpnServer>` a zkopírujte hodnotu. Tuto hodnotu použijete v dalším kroku.
6. Upravte hodnoty v příkladu níže a přidejte příklad do konfigurace /etc/ipsec.conf.
  
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
6. Přidejte následující do */etc/ipsec.secrets*.

   ```
   : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
   ```

7. Spusťte následující příkazy:

   ```
   # ipsec restart
   # ipsec up azure
   ```

## <a name="next-steps"></a>Další kroky

Vraťte se do článku a [dokončete konfiguraci P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Informace o řešení potíží s připojeními P2S naleznete v následujících článcích:

  * [Poradce při potížích s připojením Azure point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Poradce při potížích s připojením VPN z klientů MAC OS X VPN](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
