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
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986687"
---
## <a name="windows-clients"></a><a name="windows"></a>Klienti systému Windows

1. Stáhněte a nainstalujte klienta OpenVPN (verze 2.4 nebo vyšší) z [oficiálních webových stránek OpenVPN](https://openvpn.net/index.php/open-source/downloads.html).
2. Stáhněte si profil sítě VPN pro bránu. To lze provést z karty konfigurace point-to-site na webu na webu nebo "New-AzVpnClientConfiguration" v Prostředí PowerShell.
3. Rozbalte profil. Dále otevřete konfigurační soubor *vpnconfig.ovpn* ze složky OpenVPN pomocí poznámkového bloku.
4. Exportujte klientský certifikát typu point-to-site, který jste vytvořili a nahráli do konfigurace P2S na bráně. Použijte následující odkazy na článek:

   * Pokyny [brány VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport)
   
   * [Virtuální](../articles/virtual-wan/certificates-point-to-site.md#clientexport) pokyny wan
5. Extrahujte soukromý klíč a kryptografický otisk base64 z *.pfx*. To lze provést několika způsoby. Použití OpenSSL na vašem počítači je jedním ze způsobů. Soubor *profileinfo.txt* obsahuje soukromý klíč a kryptografický otisk certifikační autority a klientského certifikátu. Ujistěte se, že používáte kryptografický otisk klientského certifikátu.

   ```
   openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
   ```
6. Otevřete *soubor profileinfo.txt* v poznámkovém bloku. Chcete-li získat kryptografický otisk klientského (podřízeného) certifikátu, vyberte text (včetně a mezi)"-----BEGIN CERTIFICATE-----" a "-----END CERTIFICATE-----" pro podřízený certifikát a zkopírujte jej. Dětský certifikát můžete identifikovat tak, že se podíváte na předmět=/ řádek.
7. Přepněte na soubor *vpnconfig.ovpn,* který jste otevřeli v poznámkovém bloku od kroku 3. Najděte níže uvedenou část a vyměňte vše mezi "cert" a "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otevřete *soubor profileinfo.txt* v poznámkovém bloku. Chcete-li získat soukromý klíč, vyberte text (včetně a mezi) "-----BEGIN PRIVATE KEY-----" a "-----END PRIVATE KEY-----" a zkopírujte jej.
9. Vraťte se do souboru vpnconfig.ovpn v poznámkovém bloku a najděte tuto sekci. Vložte soukromý klíč, který nahradí vše mezi a "klíč" a "/klíč".

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

## <a name="mac-clients"></a><a name="mac"></a>Mac klienti

1. Stáhněte a nainstalujte klienta OpenVPN, například [TunnelBlick](https://tunnelblick.net/downloads.html). 
2. Stáhněte si profil sítě VPN pro bránu. To lze provést z karty konfigurace point-to-site na webu na webu nebo pomocí "New-AzVpnClientConfiguration" v Prostředí PowerShell.
3. Rozbalte profil. Otevřete konfigurační soubor vpnconfig.ovpn ze složky OpenVPN v textovém editoru.
4. V části klientského certifikátu P2S vyplňte veřejný klíč klientského certifikátu P2S v kódování Base-64. V případě certifikátu s formátem PEM stačí otevřít soubor .cer a zkopírovat klíč Base-64 uvedený mezi hlavičkami certifikátu. Pomocí následujících odkazů na článek naleznete informace o exportu certifikátu pro získání kódovaného veřejného klíče:

   * Pokyny [brány VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * [Virtuální](../articles/virtual-wan/certificates-point-to-site.md#cer) pokyny wan
5. V části privátního klíče vyplňte privátní klíč klientského certifikátu P2S v kódování Base-64. Informace o tom, jak extrahovat soukromý klíč, naleznete v tématu [Export vašeho soukromého klíče](https://openvpn.net/community-resources/how-to/#pki) na webu OpenVPN.
6. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
7. Poklepáním na soubor profilu vytvořte profil v tunnelblicku.
8. Spusťte Tunnelblick ze složky aplikace.
9. Klikněte na ikonu Tunnelblick v systémové liště a vyberte připojit.

> [!IMPORTANT]
>Protokolem OpenVPN jsou podporovány pouze iOS 11.0 a vyšší a MacOS 10.13 a vyšší.
>
## <a name="ios-clients"></a><a name="iOS"></a>Klienti iOS

1. Nainstalujte klienta OpenVPN (verze 2.4 nebo vyšší) z Obchodu s aplikacemi.
2. Stáhněte si profil sítě VPN pro bránu. To lze provést z karty konfigurace point-to-site na webu na webu nebo pomocí "New-AzVpnClientConfiguration" v Prostředí PowerShell.
3. Rozbalte profil. Otevřete konfigurační soubor vpnconfig.ovpn ze složky OpenVPN v textovém editoru.
4. V části klientského certifikátu P2S vyplňte veřejný klíč klientského certifikátu P2S v kódování Base-64. V případě certifikátu s formátem PEM stačí otevřít soubor .cer a zkopírovat klíč Base-64 uvedený mezi hlavičkami certifikátu. Pomocí následujících odkazů na článek naleznete informace o exportu certifikátu pro získání kódovaného veřejného klíče:

   * Pokyny [brány VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#cer) 
   
   * [Virtuální](../articles/virtual-wan/certificates-point-to-site.md#cer) pokyny wan
5. V části privátního klíče vyplňte privátní klíč klientského certifikátu P2S v kódování Base-64. Informace o tom, jak extrahovat soukromý klíč, naleznete v tématu [Export soukromého klíče](https://openvpn.net/community-resources/how-to/#pki) na webu OpenVPN.
6. Ostatní pole ponechte beze změny.
7. E-mail soubor profilu (.ovpn) na váš e-mailový účet, který je nakonfigurován v e-mailové aplikaci na vašem iPhone. 
8. Otevřete e-mail v e-mailové aplikaci na iPhonu a klepněte na připojený soubor.

    ![Otevřít e-mail](./media/vpn-gateway-vwan-config-openvpn-clients/ios2.png)

9. Klepněte na **další,** pokud nevidíte možnost **Kopírovat do OpenVPN**

    ![Víc](./media/vpn-gateway-vwan-config-openvpn-clients/ios3.png)

10. Klepněte na **Kopírovat do OpenVPN** 

    ![Kopírovat do OpenVPN](./media/vpn-gateway-vwan-config-openvpn-clients/ios4.png)

11. Klepněte na **PŘIDAT** na stránce **Importovat profil**

    ![Přidat](./media/vpn-gateway-vwan-config-openvpn-clients/ios5.png)

12. Klepněte na **PŘIDAT** na stránce **Importovaný profil.**

    ![Klepněte na PŘIDAT.](./media/vpn-gateway-vwan-config-openvpn-clients/ios6.png)

13. Spusťte aplikaci OpenVPN a posuňte přepínač na stránce **Profil** vpravo pro připojení

    ![Připojení](./media/vpn-gateway-vwan-config-openvpn-clients/ios8.png)


## <a name="linux-clients"></a><a name="linux"></a>Linuxoví klienti

1. Otevřete novou relaci terminálu. Novou relaci můžete otevřít stisknutím klávesctrl + alt + t současně.
2. Chcete-li nainstalovat potřebné součásti, zadejte následující příkaz:

   ```
   sudo apt-get install openvpn
   sudo apt-get -y install network-manager-openvpn
   sudo service network-manager restart
   ```
3. Stáhněte si profil sítě VPN pro bránu. To lze provést z karty konfigurace point-to-site na webu na webu.
4. Exportujte klientský certifikát P2S, který jste vytvořili a nahráli do konfigurace P2S na bráně. Použijte následující odkazy na článek:

   * Pokyny [brány VPN](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md#clientexport) 
   
   * [Virtuální](../articles/virtual-wan/certificates-point-to-site.md#clientexport) pokyny wan
5. Extrahujte soukromý klíč a kryptografický otisk base64 z .pfx. To lze provést několika způsoby. Použití OpenSSL v počítači je jedním ze způsobů.

    ```
    openssl pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
   Soubor *profileinfo.txt* bude obsahovat soukromý klíč a kryptografický otisk certifikační autority a klientský certifikát. Ujistěte se, že používáte kryptografický otisk klientského certifikátu.

6. Otevřete *soubor profileinfo.txt* v textovém editoru. Chcete-li získat kryptografický otisk klientského (podřízeného) certifikátu, vyberte text včetně a mezi "-----BEGIN CERTIFICATE-----" a "-----END CERTIFICATE-----" pro podřízený certifikát a zkopírujte jej. Dětský certifikát můžete identifikovat tak, že se podíváte na předmět=/ řádek.

7. Otevřete soubor *vpnconfig.ovpn* a najděte níže uvedenou sekci. Vyměňte vše mezi a a "cert" a "/cert".

   ```
   # P2S client certificate
   # please fill this field with a PEM formatted cert
   <cert>
   $CLIENTCERTIFICATE
   </cert>
   ```
8. Otevřete soubor profileinfo.txt v textovém editoru. Chcete-li získat soukromý klíč, vyberte text včetně a mezi ----- "BEGIN PRIVATE KEY-----" a "-----END PRIVATE KEY-----" a zkopírujte jej.

9. Otevřete soubor vpnconfig.ovpn v textovém editoru a najděte tuto sekci. Vložte soukromý klíč, který nahradí vše mezi a "klíč" a "/klíč".

   ```
   # P2S client root certificate private key
   # please fill this field with a PEM formatted key
   <key>
   $PRIVATEKEY
   </key>
   ```

10. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
11. Chcete-li se připojit pomocí příkazového řádku, zadejte následující příkaz:
  
    ```
    sudo openvpn --config <name and path of your VPN profile file>&
    ```
12. Chcete-li se připojit pomocí grafického uživatelského rozhraní, přejděte na nastavení systému.
13. Klepnutím **+** přidáte nové připojení VPN.
14. V části **Přidat VPN**vyberte **Importovat ze souboru...**
15. Přejděte do souboru profilu a poklepejte nebo vyberte **Otevřít**.
16. V okně **Přidat VPN** klikněte na **Přidat.**
  
    ![Importovat ze souboru](./media/vpn-gateway-vwan-config-openvpn-clients/import.png)
17. Připojit se můžete zapnutím sítě VPN **na** stránce **Nastavení sítě** nebo pod ikonou sítě na hlavním panelu systému.
