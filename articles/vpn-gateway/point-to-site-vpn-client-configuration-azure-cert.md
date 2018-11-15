---
title: 'Vytvoření a instalace konfiguračních souborů klienta P2S VPN pro ověřování certifikátů Azure: Azure | Dokumentace Microsoftu'
description: Vytvoření a instalace Windows, Linux, systém Linux (strongSwan) a Mac OS X VPN konfiguračních souborů klienta pro ověřování P2S certifikátu.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/05/2018
ms.author: cherylmc
ms.openlocfilehash: 11d23102ca807ab1ddf41f1d0e72aed8a8513ac8
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636641"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-p2s-configurations"></a>Vytvoření a instalace konfiguračních souborů klienta VPN pro ověřování P2S konfigurace nativního certifikátu Azure

Konfiguračních souborů klienta VPN jsou obsaženy v souboru zip. Konfigurační soubory poskytují nastavením požadovaným pro nativní klienty Windows, Mac IKEv2 VPN nebo Linuxem pro připojení k virtuální síti pomocí připojení Point-to-Site, které používají nativní ověřování certifikátů Azure. Další informace o připojení Point-to-Site najdete v tématu věnovaném [síti VPN typu Point-to-Site](point-to-site-about.md).

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

>[!NOTE]
>Konfigurační soubory klienta jsou specifické pro konfiguraci sítě VPN pro virtuální síť. Pokud existují změny v konfiguraci Point-to-Site VPN od vygenerování konfiguračních souborů klienta VPN, jako je protokol typu sítě VPN nebo typ ověřování, je nutné generovat nové soubory konfigurace klienta VPN pro uživatele zařízení.
>
>

## <a name="generate"></a>Generování souborů konfigurace klienta VPN

Než začnete, ujistěte se, že všechny připojujících se uživatelů mají platný certifikát nainstalovaný na zařízení uživatele. Další informace o instalaci klientského certifikátu naleznete v tématu [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

Můžete generovat konfiguračních souborů klienta pomocí Powershellu, nebo pomocí webu Azure portal. Některé z metod vrací stejný soubor zip. Rozbalte soubor zobrazíte následující složky:

  * **WindowsAmd64** a **WindowsX86**, které obsahují Instalační služby systému Windows 32bitové a 64bitové balíčky, v uvedeném pořadí. **WindowsAmd64** pro všechny podporované klienty Windows 64-bit, ne jenom Amd je balíček Instalační služby.
  * **Obecný**, který obsahuje obecné informace použité k vytvoření vlastní konfigurace klienta VPN. Pokud byly nakonfigurované IKEv2 nebo SSTP + IKEv2 v bráně je k dispozici obecné složce. Pokud je nakonfigurována SSTP, pouze není obecné složce k dispozici.

### <a name="zipportal"></a>Generování souborů pomocí webu Azure portal

1. Na webu Azure Portal přejděte k bráně virtuální sítě pro virtuální síť, kterou chcete připojit k.
2. Na stránce brány virtuální sítě, klikněte na tlačítko **KonfiguracePoint-to-site**.
3. V horní části na stránce konfigurace Point-to-site, klikněte na tlačítko **stáhnout klienta VPN**. Trvá několik minut, než klient konfigurační balíček pro generování.
4. Váš prohlížeč označuje, že konfigurační soubor zip klienta je k dispozici. Jmenuje se stejným názvem jako bránu. Rozbalte tento soubor k zobrazení složky.

### <a name="zipps"></a>Generování souborů pomocí Powershellu

1. Při generování konfigurace klienta VPN souborů, hodnota pro ' – AuthenticationMethod "je"EapTls". Generovat konfiguračních souborů klienta VPN pomocí následujícího příkazu:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Zkopírujte adresu URL do prohlížeče a stáhněte soubor zip a rozbalte soubor k zobrazení složky.

## <a name="installwin"></a>Windows

Na každém klientském počítači s Windows, můžete použít stejný konfigurační balíček klienta VPN, za předpokladu, že jeho verze odpovídá architektuře klienta. Seznam klientských operačních systémů, které jsou podporované, najdete v části Point-to-Site [VPN Gateway – nejčastější dotazy](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Musí mít oprávnění správce v klientském počítači Windows, ze kterého chcete připojit.
>
>

Použijte následující postup ke konfiguraci nativního klienta VPN ve Windows pro ověření certifikátu:

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. V případě 64bitové architektury procesoru zvolte instalační balíček VpnClientSetupAmd64. V případě 32bitové architektury procesoru zvolte instalační balíček VpnClientSetupX86. 
2. Dvakrát klikněte na balíček a nainstalujte ho. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace** a potom na **Přesto spustit**.
3. Na klientském počítači přejděte do části **Nastavení sítě** a klikněte na možnost **Síť VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje. 
4. Než se pokusíte o připojení, ověřte, že jste na klientském počítači nainstalovali klientský certifikát. Klientský certifikát se vyžaduje k ověřování při použití typu nativního ověřování certifikátů Azure. Další informace o generování certifikátů najdete v tématu [vygenerovat certifikáty](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informace o postupu při instalaci klientského certifikátu naleznete v tématu [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Mac (OS X)

 Na každém počítači Mac, který se bude připojovat k Azure, je potřeba ručně nakonfigurovat nativního klienta IKEv2 VPN. Azure neposkytuje soubor mobileconfig pro nativní ověřování certifikátů Azure. **Obecný** obsahuje všechny informace, které potřebujete pro konfiguraci. Pokud složku Generic mezi staženými soubory nevidíte, pravděpodobně jste jako typ tunelu nevybrali IKEv2. Jakmile vyberete IKEv2, znovu vygenerujte soubor zip a načtěte složku Generic.<br>Složka Generic obsahuje následující soubory:

* **VpnSettings.xml**, která obsahuje důležité nastavení, jako je typ serveru adresu a tunelové propojení. 
* **VpnServerRoot.cer**, který obsahuje kořenový certifikát, který je nutné k ověření Azure VPN Gateway během instalace připojení P2S.

Pomocí následujících kroků konfigurace nativního klienta VPN v systému Mac pro ověření certifikátu. Je nutné provést tento postup na každý Mac, který se připojí k Azure:

1. Import **VpnServerRoot** kořenového certifikátu do vašeho macu. To můžete udělat tak, že soubor překopírovat na počítači Mac a dvojitým kliknutím na ni.  
Klikněte na tlačítko **přidat** k importu.

  ![Přidání certifikátu](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Dvojitým kliknutím na certifikátu nemusí být zobrazeny **přidat** dialogového okna, ale certifikát je nainstalovaný ve správném úložišti. Můžete vyhledat certifikát v klíčence přihlášení v rámci kategorie certifikáty.
    >
  
2. Ověřte, zda jste nainstalovali klientský certifikát, který byl vydán kořenový certifikát, který jste nahráli do Azure, když jste nakonfigurovali nastavení P2S. Tím se liší od VPNServerRoot, který jste nainstalovali v předchozím kroku. Klientský certifikát se používá k ověřování a je povinný. Další informace o generování certifikátů najdete v tématu [vygenerovat certifikáty](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informace o postupu při instalaci klientského certifikátu naleznete v tématu [instalace klientského certifikátu](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Otevřít **sítě** dialogového okna v části **sítě Předvolby** a klikněte na tlačítko **'+'** k vytvoření nového profilu připojení klienta VPN pro připojení P2S k virtuální síti Azure.

  **Rozhraní** hodnota je "VPN" a **typ sítě VPN** hodnota je "IKEv2". Zadejte název profilu, který **název služby** pole a potom klikněte na **vytvořit** k vytvoření profilu připojení klienta VPN.

  ![Sítě](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. V **obecný** složky, ze **VpnSettings.xml** soubor, zkopírujte **VpnServer** hodnota značky. Vložte tuto hodnotu v **adresa serveru** a **vzdáleného ID** pole profilu.

  ![informace o serveru](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klikněte na tlačítko **nastavení ověřování** a vyberte **certifikát**. 

  ![nastavení ověřování](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Klikněte na tlačítko **vyberte...** Vybrat klientský certifikát, který chcete použít pro ověřování. Jedná se o certifikát, který jste nainstalovali v kroku 2.

  ![certifikát](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Zvolte Identity** zobrazí seznam certifikáty, abyste lze vybírat. Vyberte příslušný certifikát a potom klikněte na **pokračovat**.

  ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. V **místní ID** uveďte název certifikátu (z kroku 6). V tomto příkladu je "ikev2Client.com". Potom klikněte na **použít** tlačítko Uložit změny.

  ![použít](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Na **sítě** dialogového okna, klikněte na tlačítko **použít** uložte všechny změny. Potom klikněte na **připojit** spustit připojení typu P2S k virtuální síti Azure.

## <a name="linuxgui"></a>Linux (strongSwan grafického uživatelského rozhraní)

### <a name="extract-the-key-and-certificate"></a>Extrahovat klíčů a certifikátů

Pro strongSwan musíte extrahovat klíč a certifikát z klientského certifikátu (soubor .pfx) a uloží je do jednotlivých .pem souborů.
Postupujte následovně:

1. Stáhněte a nainstalujte OpenSSL z [OpenSSL](https://www.openssl.org/source/).
2. Otevřete okno příkazového řádku a přejděte do adresáře, kam nainstalovat OpenSSL, například "c:\OpenSLL-Win64\bin\'.
3. Spusťte následující příkaz k extrakci privátní klíč a uložte ho do nového souboru s názvem 'privatekey.pem' z klientského certifikátu:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
  ```
4.  Nyní spusťte následující příkaz k extrakci veřejného certifikátu a uložte ho do nového souboru:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
  ```

### <a name="install"></a>Instalace a konfigurace

Prostřednictvím strongSwan 5.5.1 na Ubuntu 17.0.4 byly vytvořeny podle následujících pokynů. Ubuntu 16.0.10 nepodporuje strongSwan grafického uživatelského rozhraní. Pokud chcete použít Ubuntu 16.0.10, budete muset použít [příkazového řádku](#linuxinstallcli). Následující příklady nemusí odpovídat obrazovky, které se zobrazí, v závislosti na vaší verzi operačních systémů Linux a strongSwan.

1. Otevřít **terminálu** instalace **strongSwan** a jeho správce sítě spuštěním příkazu v příkladu. Pokud se zobrazí chyba, která souvisí s *moduly plug-in libcharon navíc*, nahraďte ji metodou "strongswan-plugin-eap-mschapv2".

  ```
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Vyberte **správce sítě** ikonu (-šipku/seznam šipka nahoru), pak vyberte **upravit připojení**.

  ![Upravit připojení](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Klikněte na tlačítko **přidat** pro vytvoření nového připojení.

  ![Přidání připojení](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Vyberte **protokolu IPsec nebo IKEv2 (strongswan)** z rozevírací nabídky a pak klikněte na tlačítko **vytvořit**. Připojení v tomto kroku můžete přejmenovat.

  ![Vyberte typ připojení](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Otevřít **VpnSettings.xml** soubor **obecný** složka součástí staženého klienta konfigurační soubory. Požadovanou značku, volá **VpnServer** a zkopírujte název, počínaje "azuregateway" a končící na ". cloudapp.net".

  ![Kopírovat název](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Vložte tento název do **adresu** pole nové připojení k síti VPN v **brány** oddílu. Potom vyberte ikonu složky na konci **certifikát** pole, přejděte na **obecný** a pak zvolte položku **VpnServerRoot** souboru.
7. V **klienta** části připojení pro **ověřování**vyberte **certifikátu a privátního klíče**. Pro **certifikát** a **privátní klíč**, zvolte certifikát a privátní klíč, které byly dříve vytvořeny. V **možnosti**vyberte **vnitřní IP adresu požadavku**. Potom klikněte na **přidat**.

  ![požadavek vnitřní IP adresa](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Klikněte na tlačítko **správce sítě** ikona (šipka/dolů – šipku nahoru) a podržte ukazatel myši nad **připojení k síti VPN**. Zobrazí se připojení VPN, které jste vytvořili. Klikněte na tlačítko pro navázání připojení.

## <a name="linuxinstallcli"></a>Linux (strongSwan rozhraní příkazového řádku)

### <a name="install-strongswan"></a>Nainstalujte strongSwan

Můžete použít následující příkazy rozhraní příkazového řádku nebo postupujte podle kroků strongSwan v [grafickým uživatelským rozhraním](#install) strongSwan instalace.

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

### <a name="install-and-configure"></a>Instalace a konfigurace

1. Stažení balíčku klienta VPN z webu Azure portal.
2. Extrahujte soubor.
3. Z **obecný** složky, zkopírovat nebo přesunout VpnServerRoot.cer /etc/ipsec.d/cacerts.
4. Zkopírovat nebo přesunout cp client.p12 /etc/ipsec.d/private/. Tento soubor je klientský certifikát pro službu Azure VPN Gateway.
5. Otevřete soubor VpnSettings.xml a zkopírujte <VpnServer> hodnotu. Tuto hodnotu použijete v dalším kroku.
6. Upravit hodnoty v následujícím příkladu a pak přidejte příklad /etc/ipsec.conf konfigurace.
  
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
6. Přidejte následující text do */etc/ipsec.secrets*.

  ```
  : P12 client.p12 'password' # key filename inside /etc/ipsec.d/private directory
  ```

7. Spusťte následující příkazy:

  ```
  # ipsec restart
  # ipsec up azure
  ```

## <a name="next-steps"></a>Další postup

Vraťte se do článku do [dokončete konfiguraci P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Řešení potíží s připojeními P2S, najdete v následujících článcích:

  * [Řešení potíží s Azure připojení point-to-site](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)
  * [Řešení potíží s připojeními VPN z klientů se systémem Mac OS X VPN](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md)
