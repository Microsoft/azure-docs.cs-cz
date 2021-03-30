---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 55fa01d100c60c6411774373428ff4bbd9a56822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "80986687"
---
## <a name="windows-clients"></a><a name="windows"></a>Klienti Windows

1. Stáhněte si a nainstalujte klienta OpenVPN (verze 2,4 nebo vyšší) z oficiálního [webu OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Stáhněte si profil sítě VPN pro bránu. To se dá udělat na kartě Konfigurace typu Point-to-site v Azure Portal, nebo v PowerShellu New-AzVpnClientConfiguration.
3. Rozbalte profil. Pak otevřete konfigurační soubor *vpnconfig. ovpn* ze složky OpenVPN pomocí poznámkového bloku.
4. Exportujte certifikát klienta Point-to-site, který jste vytvořili a nahráli do konfigurace P2S v bráně. Použijte následující odkazy článků:

   * Pokyny pro [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * Pokyny pro [virtuální síť WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extrahujte privátní klíč a kryptografický otisk Base64 z formátu *. pfx*. To lze provést několika způsoby. Použití OpenSSL na vašem počítači je jedním ze způsobů. *profileinfo.txt* soubor obsahuje privátní klíč a kryptografický otisk pro certifikační autoritu a klientský certifikát. Nezapomeňte použít kryptografický otisk klientského certifikátu.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Otevřete *profileinfo.txt* v programu Poznámkový blok. Chcete-li získat kryptografický otisk certifikátu klienta (podřízeného), vyberte text (včetně a mezi) "-----BEGIN CERTIFICATE-----" a "-----END CERTIFICATE-----" pro podřízený certifikát a zkopírujte jej. Podřízený certifikát můžete identifikovat tak, že prohlížíte předmět =/řádek.
7. V kroku 3 Přepněte na soubor *vpnconfig. ovpn* , který jste otevřeli v programu Poznámkový blok. Najděte níže uvedenou část a nahraďte vše mezi "CERT" a "/CERT".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otevřete *profileinfo.txt* v programu Poznámkový blok. Pokud chcete získat privátní klíč, vyberte text (včetně a mezi) "-----zahájit privátní klíč-----" a "-----konec PRIVÁTNÍho klíče-----" a zkopírujte ho.
9. V programu Poznámkový blok vraťte se do souboru vpnconfig. ovpn a najděte tuto část. Vložte privátní klíč, který nahrazuje vše mezi a "klíč" a "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```
10. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
11. Zkopírujte soubor vpnconfig.ovpn do složky C:\Program Files\OpenVPN\config.
12. Klikněte pravým tlačítkem na ikonu OpenVPN na hlavním panelu a klikněte na Připojit.

## <a name="mac-clients"></a><a name="mac"></a>Klienti Mac

1. Stáhněte a nainstalujte klienta OpenVPN, jako je například [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Stáhněte si profil sítě VPN pro bránu. To se dá udělat na kartě Konfigurace typu Point-to-site v Azure Portal nebo pomocí rutiny New-AzVpnClientConfiguration v PowerShellu.
3. Rozbalte profil. Otevřete konfigurační soubor vpnconfig. ovpn ze složky OpenVPN v textovém editoru.
4. V části klientského certifikátu P2S vyplňte veřejný klíč klientského certifikátu P2S v kódování Base-64. V případě certifikátu s formátem PEM stačí otevřít soubor .cer a zkopírovat klíč Base-64 uvedený mezi hlavičkami certifikátu. Pomocí následujících odkazů najdete informace o tom, jak vyexportovat certifikát pro získání kódovaného veřejného klíče:

   * Pokyny pro [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Pokyny pro [virtuální síť WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. V části privátního klíče vyplňte privátní klíč klientského certifikátu P2S v kódování Base-64. Informace o postupu při extrakci privátního klíče najdete v části [export privátního klíče](https://openvpn.net/community-resources/how-to/#pki) na webu OpenVPN.
6. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
7. Dvojím kliknutím na soubor profilu vytvořte profil v Tunnelblick.
8. Spusťte Tunnelblick ze složky aplikace.
9. Na hlavním panelu systému klikněte na ikonu Tunnelblick a vyberte připojit.

> [!IMPORTANT]
>Protokol OpenVPN podporuje jenom iOS 11,0 a vyšší a MacOS 10,13 a novější.
>
## <a name="ios-clients"></a><a name="iOS"></a>klienti iOS

1. Nainstalujte klienta OpenVPN (verze 2,4 nebo vyšší) z App Storu.
2. Stáhněte si profil sítě VPN pro bránu. To se dá udělat na kartě Konfigurace typu Point-to-site v Azure Portal nebo pomocí rutiny New-AzVpnClientConfiguration v PowerShellu.
3. Rozbalte profil. Otevřete konfigurační soubor vpnconfig. ovpn ze složky OpenVPN v textovém editoru.
4. V části klientského certifikátu P2S vyplňte veřejný klíč klientského certifikátu P2S v kódování Base-64. V případě certifikátu s formátem PEM stačí otevřít soubor .cer a zkopírovat klíč Base-64 uvedený mezi hlavičkami certifikátu. Pomocí následujících odkazů najdete informace o tom, jak vyexportovat certifikát pro získání kódovaného veřejného klíče:

   * Pokyny pro [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * Pokyny pro [virtuální síť WAN](../articles/virtual-wan/certificates-point-to-site.md#cer)
5. V části privátního klíče vyplňte privátní klíč klientského certifikátu P2S v kódování Base-64. Informace o postupu při extrakci privátního klíče najdete v tématu [export privátního klíče](https://openvpn.net/community-resources/how-to/#pki) na webu OpenVPN.
6. Ostatní pole ponechte beze změny.
7. Pošlete soubor profilu (. ovpn) na váš e-mailový účet, který je nakonfigurovaný v e-mailové aplikaci na iPhonu. 
8. Otevřete e-mail v aplikaci Mail na iPhonu a klepněte na přiložený soubor.

    ![Otevřít e-mail](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Pokud nevidíte možnost **Kopírovat do OpenVPN** , klepněte na **Další** .

    ![Víc](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Klepněte na **Kopírovat do OpenVPN** 

    ![Kopírovat do OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Klepněte na **Přidat** na stránce **Importovat profil** .

    ![Přidání](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Klepněte na **Přidat** na stránce **importovaný profil** .

    ![Klepněte na Přidat](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Spusťte aplikaci OpenVPN a posuňte přepínač na stránce **profil** vpravo a připojte se.

    ![Připojit](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Klienti Linux

1. Otevřete novou relaci terminálu. Novou relaci můžete otevřít stisknutím kombinace kláves CTRL + ALT + t ve stejnou dobu.
2. Zadejte následující příkaz pro instalaci potřebných součástí:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Stáhněte si profil sítě VPN pro bránu. To se dá udělat na kartě Konfigurace Point-to-site v Azure Portal.
4. Exportujte certifikát klienta P2S, který jste vytvořili a nahráli do konfigurace P2S v bráně. Použijte následující odkazy článků:

   * Pokyny pro [VPN Gateway](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * Pokyny pro [virtuální síť WAN](../articles/virtual-wan/certificates-point-to-site.md#clientexport)
5. Extrahujte privátní klíč a kryptografický otisk Base64 z formátu. pfx. To lze provést několika způsoby. Použití OpenSSL v počítači je jednosměrné.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Soubor *profileinfo.txt* bude obsahovat privátní klíč a kryptografický otisk pro certifikační autoritu a klientský certifikát. Nezapomeňte použít kryptografický otisk klientského certifikátu.

6. Otevřete *profileinfo.txt* v textovém editoru. Chcete-li získat kryptografický otisk certifikátu klienta (podřízeného), vyberte text včetně a mezi "-----BEGIN CERTIFICATE-----" a "-----END CERTIFICATE-----" pro podřízený certifikát a zkopírujte jej. Podřízený certifikát můžete identifikovat tak, že prohlížíte předmět =/řádek.

7. Otevřete soubor *vpnconfig. ovpn* a vyhledejte část uvedenou níže. Nahraďte vše mezi hodnotami a "CERT" a "/CERT".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otevřete profileinfo.txt v textovém editoru. Pokud chcete získat privátní klíč, vyberte text včetně a mezi "-----zahájit-----PRIVÁTNÍho klíče" a "-----konec PRIVÁTNÍho klíče-----" a zkopírujte ho.

9. V textovém editoru otevřete soubor vpnconfig. ovpn a vyhledejte tento oddíl. Vložte privátní klíč, který nahrazuje vše mezi a "klíč" a "/Key".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
11. Pokud se chcete připojit pomocí příkazového řádku, zadejte následující příkaz:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Pokud se chcete připojit pomocí grafického uživatelského rozhraní, použijte možnost nastavení systému.
13. Kliknutím **+** můžete přidat nové připojení k síti VPN.
14. V části **Přidat síť VPN** vyberte **Importovat ze souboru...**
15. Přejděte k souboru profilu a dvakrát klikněte nebo vyberte **otevřít**.
16. V okně **Přidat síť VPN** klikněte na **Přidat** .
  
    ![Importovat ze souboru](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Můžete se připojit zapnutím sítě VPN na stránce **nastavení sítě** **nebo pomocí ikony** síť v oznamovací oblasti na hlavním panelu systému.
