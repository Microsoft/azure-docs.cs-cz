---
title: 'Vytvoření a instalace souborů konfigurace klienta P2S VPN pro ověření certifikátu Azure: prostředí PowerShell: Azure | Microsoft Docs'
description: Vytvoření a instalace systému Windows, Linux (strongSwan) a Mac OS X VPN souborů konfigurace klienta pro ověřování pomocí certifikátu P2S.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2018
ms.author: cherylmc
ms.openlocfilehash: 2ae16d76dce01488403e9f13684e719f92eb80c5
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235827"
---
# <a name="create-and-install-vpn-client-configuration-files-for-native-azure-certificate-authentication-point-to-site-configurations"></a>Vytvoření a instalace VPN konfigurace Point-to-Site ověřování certifikátu klienta konfigurační soubory pro nativní Azure

Soubory konfigurace klienta VPN jsou obsaženy v souboru zip. Konfigurační soubory, zadejte požadované nastavení pro nativní klient systému Windows, Mac IKEv2 VPN nebo Linux se připojit k virtuální síti přes připojení Point-to-Site, která používá ověřování certifikátem. nativní Azure.

>[!IMPORTANT]
>[!INCLUDE [TLS](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="workflow"></a>Pracovní postup P2S

  1. Nastaví bránu Azure VPN pro připojení P2S.
  2. Generování kořenového certifikátu a klientské certifikáty. Veřejný klíč certifikátu kořenové nahrát do Azure a nainstalujte klientské certifikáty na klientských zařízeních. Certifikáty se používají pro ověřování připojování uživatelů.
  3. Získání konfigurace klienta VPN pro možnost ověřování podle svého výběru a použít ho k nastavení klienta VPN na zařízení s Windows a Mac. To vám umožní připojit se k virtuálním sítím Azure prostřednictvím připojení Point-to-Site.

>[!NOTE]
>Konfigurační soubory klienta jsou specifické pro konfiguraci sítě VPN pro virtuální sítě. Je-li vygenerovat soubory konfigurace klienta VPN, třeba protokol typ sítě VPN nebo typ ověřování, se změny v konfiguraci Point-to-Site VPN je nutné vygenerovat nové soubory konfigurace klienta VPN pro uživatele zařízení.
>
>

## <a name="generate"></a>Generování souborů konfigurace klienta VPN

Než začnete, ujistěte se, že všechny připojování uživatelé mají platný certifikát nainstalovaný na zařízení uživatele. Další informace o instalaci certifikátu klienta najdete v tématu [nainstalovat certifikát klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

Můžete vygenerovat konfigurační soubory klienta pomocí prostředí PowerShell, nebo pomocí portálu Azure. Buď metoda vrátí stejné souboru zip. Rozbalte soubor zobrazíte následující složky:

  * **WindowsAmd64** a **WindowsX86**, které obsahují balíčky Instalační služby systému Windows 32-bit a 64-bit, v uvedeném pořadí. **WindowsAmd64** je balíček Instalační služby pro všechny podporované klienty Windows 64-bit, nikoli pouze Amd.
  * **Obecné**, který obsahuje obecné informace použít k vytvoření vlastní konfigurace klienta VPN. Obecné složce je zadán, pokud byla nakonfigurována IKEv2 nebo SSTP + IKEv2 v bráně. Pokud je nakonfigurována SSTP, pouze není obecné složce přítomen.

### <a name="zipportal"></a>Generování souborů pomocí portálu Azure

1. Na portálu Azure přejděte do bránu virtuální sítě pro virtuální síť, která chcete připojit.
2. Na stránce brány virtuální sítě klikněte na **KonfiguracePoint-to-site**.
3. V horní části stránky konfigurace Point-to-site, klikněte na tlačítko **klienta VPN Stáhnout**. Trvá několik minut pro klienta konfigurační balíček ke generování.
4. Váš prohlížeč označuje, že konfigurační soubor zip klienta je k dispozici. Je název stejný název jako bránu. Rozbalte soubor k zobrazení složky.

### <a name="zipps"></a>Generování souborů pomocí prostředí PowerShell

1. Při generování konfigurace klienta VPN pomocí souborů, hodnota pro '-AuthenticationMethod' je 'EapTls'. Generovat soubory konfigurace klienta VPN pomocí následujícího příkazu:

  ```powershell
  $profile=New-AzureRmVpnClientConfiguration -ResourceGroupName "TestRG" -Name "VNet1GW" -AuthenticationMethod "EapTls"

  $profile.VPNProfileSASUrl
  ```
2. Zkopírujte adresu URL do prohlížeče ke stažení souboru zip a potom rozbalte soubor k zobrazení složky.

## <a name="installwin"></a>Install - Windows

Na každém klientském počítači s Windows, můžete použít stejný balíček konfigurace klienta VPN, tak dlouho, dokud verze odpovídá architektuře pro klienta. Seznam klientské operační systémy, které jsou podporované, najdete v části Point-to-Site [VPN Gateway – nejčastější dotazy](vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Musí mít oprávnění správce v klientském počítači Windows, ze kterého se chcete připojit.
>
>

Pomocí následujícího postupu můžete nakonfigurovat Nativní klient VPN ve Windows pro ověření certifikátu:

1. Vyberte konfigurační soubory klienta VPN, které odpovídá architektuře počítače Windows. Pro 64bitový procesor architekturu vyberte balíček Instalační služby 'VpnClientSetupAmd64'. Pro architekturu procesoru 32-bit vyberte balíček Instalační služby 'VpnClientSetupX86'. 
2. Dvakrát klikněte na balíček k její instalaci. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace** a potom na **Přesto spustit**.
3. Na klientském počítači přejděte do části **Nastavení sítě** a klikněte na možnost **Síť VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje. 
4. Před dalším pokusem o připojení, ověřte, zda jste nainstalovali klientský certifikát na klientském počítači. Klientský certifikát je potřeba pro ověřování, při použití nativní Azure certifikát typ ověřování. Další informace o generování certifikátů najdete v tématu [generovat certifikáty](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informace o tom, jak nainstalovat certifikát klienta najdete v tématu [nainstalovat certifikát klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="installmac"></a>Instalace – Mac (OS X)

Azure neposkytuje mobileconfig soubor pro ověření certifikátu nativní Azure. Budete muset ručně nakonfigurovat Nativní klient IKEv2 VPN v každé Mac, který se připojí k Azure. **Obecné** složka obsahuje všechny informace, které je třeba ji nakonfigurovat. Pokud nevidíte obecné složce v stahování, je pravděpodobné, že IKEv2 nebyl vybrán jako typ tunelového propojení. Pokud je vybraná IKEv2, generujte souboru zip a načtěte obecné složce. Obecné složce obsahuje následující soubory:

* **VpnSettings.xml**, která obsahuje důležité nastavení, jako je typ serveru adresu a tunelové propojení. 
* **VpnServerRoot.cer**, který obsahuje kořenový certifikát, který je nutné k ověření služby Azure VPN Gateway během instalace připojení P2S.

Použijte následující postup ke konfiguraci Nativní klient VPN v systému Mac pro ověřování pomocí certifikátu. Je nutné provést tento postup na každý Mac, který se připojí k Azure:

1. Import **VpnServerRoot** kořenového certifikátu k vaší Mac. To lze provést tak, že kopírování souboru přes do počítače Mac a dvakrát kliknete na soubor na něm.  
Klikněte na tlačítko **přidat** k importu.

  ![Přidání certifikátu](./media/point-to-site-vpn-client-configuration-azure-cert/addcert.png)
  
    >[!NOTE]
    >Dvojitým kliknutím na certifikátu nemusí být zobrazeny **přidat** dialogové okno, ale certifikát je nainstalovaný do správného úložiště. Můžete zkontrolovat pro certifikát v řetězci klíčů přihlášení v kategorii certifikáty.
  
2. Ověřte, že jste nainstalovali klientský certifikát, který byl vydán kořenový certifikát, který jste nahráli do Azure, když jste nakonfigurovali nastavení P2S. To se liší od VPNServerRoot, který jste nainstalovali v předchozím kroku. Klientský certifikát slouží k ověřování a je vyžadován. Další informace o generování certifikátů najdete v tématu [generovat certifikáty](vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert). Informace o tom, jak nainstalovat certifikát klienta najdete v tématu [nainstalovat certifikát klienta](point-to-site-how-to-vpn-client-install-azure-cert.md).
3. Otevřete **sítě** dialogové okno pod **sítě Předvolby** a klikněte na tlačítko **'+'** k vytvoření nového profilu připojení VPN klienta pro připojení P2S k virtuální síti Azure.

  **Rozhraní** hodnota je "VPN" a **typ sítě VPN** hodnota je 'IKEv2'. Zadejte název pro profil v **název služby** pole a pak klikněte na **vytvořit** k vytvoření profilu připojení klienta VPN.

  ![Sítě](./media/point-to-site-vpn-client-configuration-azure-cert/network.png)
4. V **Obecné** složky, z **VpnSettings.xml** souboru, zkopírujte **VpnServer** hodnota značky. Vložte tuto hodnotu v **adresu serveru** a **vzdáleného ID** pole profilu.

  ![informace o serveru](./media/point-to-site-vpn-client-configuration-azure-cert/server.png)
5. Klikněte na tlačítko **nastavení ověřování** a vyberte **certifikát**. 

  ![nastavení ověřování](./media/point-to-site-vpn-client-configuration-azure-cert/authsettings.png)
6. Klikněte na tlačítko **vyberte...** a vyberte certifikát klienta, který chcete použít pro ověřování. To je certifikát, který jste nainstalovali v kroku 2.

  ![certifikát](./media/point-to-site-vpn-client-configuration-azure-cert/certificate.png)
7. **Vyberte Identity** zobrazí seznam certifikátů můžete vybírat. Vybrat vhodný certifikát a potom klikněte na **pokračovat**.

  ![identity](./media/point-to-site-vpn-client-configuration-azure-cert/identity.png)
8. V **místní ID** pole, zadejte název certifikátu (z kroku 6). V tomto příkladu je "ikev2Client.com". Potom klikněte na **použít** tlačítko a uložte změny.

  ![použít](./media/point-to-site-vpn-client-configuration-azure-cert/applyconnect.png)
9. Na **sítě** dialogové okno, klikněte na tlačítko **použít** uložit všechny změny. Potom klikněte na **Connect** spustit připojení P2S do virtuální sítě Azure.

## <a name="installlinux"></a>Nainstalovat - Linux (strongSwan)

### <a name="extract-the-key-and-certificate"></a>Extrahování klíče a certifikátu

Pro strongSwan musíte extrahovat klíč a certifikát z klientského certifikátu (soubor .pfx) a uložte je do jednotlivých .pem soubory.
Postupujte následovně:

1. Stáhněte a nainstalujte OpenSSL z [OpenSSL](https://www.openssl.org/source/).
2. Otevřete okno příkazového řádku a přejděte do adresáře, kam jste nainstalovali OpenSSL, například "c:\OpenSLL-Win64\bin\'.
3. Spusťte následující příkaz k extrakci privátní klíč a uložit ho do nového souboru s názvem 'privatekey.pem' z klientský certifikát:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nocerts -out privatekey.pem -nodes
  ```
4.  Nyní spusťte následující příkaz k extrakci veřejný certifikát a uložte ho do nového souboru:

  ```
  C:\ OpenSLL-Win64\bin> openssl pkcs12 -in clientcert.pfx -nokeys -out publiccert.pem -nodes
  ```

### <a name="install"></a>Instalace

Prostřednictvím strongSwan 5.5.1 na Ubuntu 17.0.4 byly vytvořeny následující pokyny. Ubuntu 16.0.10 nepodporuje strongSwan grafickým uživatelským rozhraním. Pokud chcete použít Ubuntu 16.0.10, je nutné použít příkaz. Následující příklady nemusí odpovídat obrazovky, které se zobrazí, v závislosti na vaší verzí systémů Linux a strongSwan.

1. Otevřete **Terminálové** k instalaci **strongSwan** a jeho správce sítě spuštěním příkazu v příkladu. Pokud obdržíte chybu, která souvisí s *modulů plug-in libcharon navíc*, nahraďte ji metodou 'strongswan-plugin-eap-mschapv2'.

  ```
  sudo apt-get install strongswan libcharon-extra-plugins moreutils iptables-persistent network-manager-strongswan
  ```
2. Vyberte **správce sítě** ikona (až šipku nebo šipku dolů), pak vyberte **upravit připojení**.

  ![Upravit připojení](./media/point-to-site-vpn-client-configuration-azure-cert/editconnections.png)
3. Klikněte **přidat** tlačítko vytvořte nové připojení.

  ![Přidat připojení](./media/point-to-site-vpn-client-configuration-azure-cert/addconnection.png)
4. Vyberte **protokolu IPsec nebo IKEv2 (strongswan)** z rozevírací nabídky a pak klikněte na tlačítko **vytvořit**. Můžete přejmenovat připojení v tomto kroku.

  ![Vyberte typ připojení](./media/point-to-site-vpn-client-configuration-azure-cert/choosetype.png)
5. Otevřete **VpnSettings.xml** souboru z **Obecné** složky, které jsou součástí staženého klienta konfigurační soubory. Nalézt značku názvem **VpnServer** a zkopírujte název, počínaje "azuregateway" a končící '. cloudapp.net'.

  ![název kopie](./media/point-to-site-vpn-client-configuration-azure-cert/vpnserver.png)
6. Vložte tento název do **adresu** pole nové připojení VPN v **brány** části. Potom vyberte ikonu složky na konci **certifikát** pole, přejděte na **Obecné** složky a vyberte **VpnServerRoot** souboru.
7. V **klienta** části připojení, pro **ověřování**, vyberte **certifikátu a privátního klíče**. Pro **certifikát** a **privátní klíč**, zvolte certifikát a privátní klíč, které byly dříve vytvořeny. V **možnosti**, vyberte **vyžádání vnitřní IP adresy**. Potom klikněte na **přidat**.

  ![žádost o informacích o vnitřní IP adresu](./media/point-to-site-vpn-client-configuration-azure-cert/inneripreq.png)
8. Klikněte **správce sítě** ikona (šipka nebo nižší šipku nahoru) a hover přes **připojení k síti VPN**. Najdete v části připojení k síti VPN, který jste vytvořili. Kliknutím zahájíte připojení.

## <a name="next-steps"></a>Další postup

Vrátit do článku do [dokončit konfiguraci P2S](vpn-gateway-howto-point-to-site-rm-ps.md).

Informace o odstraňování potíží P2S [připojení point-to-site řešení potíží s Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) a [řešení VPN připojení z klientů se systémem Mac OS X VPN](vpn-gateway-troubleshoot-point-to-site-osx-ikev2.md).