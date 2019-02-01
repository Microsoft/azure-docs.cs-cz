---
title: Postup konfigurace klientů OpenVPN pro Azure VPN Gateway | Dokumentace Microsoftu
description: Postup konfigurace klientů OpenVPN pro Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 1/15/2019
ms.author: cherylmc
ms.openlocfilehash: cd84948b5895a628fc94cc2946156d9fa2284369
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55508670"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Konfigurace klientů OpenVPN pro Azure VPN Gateway (Preview)

Tento článek vám pomůže nakonfigurovat OpenVPN klientů.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Před zahájením

Ověřte, že jste dokončili postup pro konfiguraci OpenVPN pro bránu VPN. Podrobnosti najdete v tématu [OpenVPN konfigurace pro službu Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Klienti Windows

1. Stažení a instalace klienta OpenVPN z oficiální [OpenVPN webu](https://openvpn.net/index.php/open-source/downloads.html).
2. Stáhněte si profil sítě VPN pro bránu. To můžete udělat na kartě Konfigurace Point-to-site na webu Azure Portal, nebo 'New-AzureRmVpnClientConfiguration"v prostředí PowerShell.
3. Rozbalte profil. Dále otevřete *vpnconfig.ovpn* konfigurační soubor ze složky OpenVPN pomocí poznámkového bloku.
4. [Export](vpn-gateway-certificates-point-to-site.md#clientexport) P2S klientský certifikát můžete vytvořit a nahrát do vaší konfigurace P2S k bráně.
5. Extrahování privátním klíčem a kryptografického otisku base64 z *.pfx*. To lze provést několika způsoby. Pomocí OpenSSL na vašem počítači je jedním ze způsobů. *Profileinfo.txt* soubor obsahuje privátní klíč a kryptografický otisk certifikační Autority a certifikát klienta. Nezapomeňte použít kryptografický otisk klientského certifikátu.

  ```
  openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
  ```
6. Otevřít *profileinfo.txt* v poznámkovém bloku. Pokud chcete získat kryptografický otisk certifikátu klienta (podřízený), vyberte text (včetně a mezi) "---BEGIN CERTIFICATE---" a "---konec certifikát---" pro podřízené certifikát a zkopírujte ho. Certifikát podřízené můžete identifikovat podle předmětu = / řádek.
7. Přepněte *vpnconfig.ovpn* soubor otevřený v poznámkovém bloku z kroku 3. Část uvedenou níže najít a nahradit všechno mezi "cert" a "/ certifikátu".

  ```
  # P2S client certificate
  # please fill this field with a PEM formatted cert
  <cert>
  $CLIENTCERTIFICATE
  </cert>
  ```
8.  Otevřít *profileinfo.txt* v poznámkovém bloku. Pokud chcete získat privátní klíč, vyberte text (včetně a mezi) "---BEGIN PRIVATE KEY---" a "---BEGIN PRIVATE KEY---" a zkopírujte ho.
9.  Vraťte se do souboru vpnconfig.ovpn v programu Poznámkový blok a najděte v této části. Vložte privátní klíč, který nahrazuje všechno mezi a "klíče" a "/ klíčů".

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

## <a name="mac"></a>Klienti se systémem Mac

1. Stáhnout a nainstalovat OpenVPN klienta, jako například [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Stáhněte si profil sítě VPN pro bránu. To můžete udělat na kartě Konfigurace point-to-site na webu Azure Portal nebo pomocí "New-AzureRmVpnClientConfiguration" v prostředí PowerShell.
3. Rozbalte profil. Otevřete konfigurační soubor vpnconfig.ovpn ze složky OpenVPN v poznámkovém bloku.
4. V části klientského certifikátu P2S vyplňte veřejný klíč klientského certifikátu P2S v kódování Base-64. V případě certifikátu s formátem PEM stačí otevřít soubor .cer a zkopírovat klíč Base-64 uvedený mezi hlavičkami certifikátu. Zobrazit [Export veřejného klíče](vpn-gateway-certificates-point-to-site.md#cer) informace o exportování certifikátu se získat kódované veřejný klíč.
5. V části privátního klíče vyplňte privátní klíč klientského certifikátu P2S v kódování Base-64. Zobrazit [exportovat soukromý klíč](https://openvpn.net/community-resources/how-to/#pki) informace o tom, jak extrahovat privátní klíč.
6. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
7. Poklikejte na soubor profilu a vytvořte profil v tunnelblik.
8. Spusťte Tunnelblik ze složky aplikace.
9. Klikněte na ikonu Tunnelblik na hlavním panelu systému a připojit výběru.

> [!IMPORTANT]
>Jenom iOS 11.0 a vyšší a MacOS 10.13 a novější podporují OpenVPN protokolu.
>

## <a name="linux"></a>Klienti Linux

1. Otevřete novou relaci terminálu. Nová relace můžete otevřít stisknutím klávesy "Ctrl + Alt + t" ve stejnou dobu.
2. Zadejte následující příkaz pro instalaci potřebných součástí:

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. Stáhněte si profil sítě VPN pro bránu. To můžete udělat na kartě Konfigurace Point-to-site na webu Azure Portal.
4.  [Export](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-certificates-point-to-site#clientexport) P2S klientský certifikát můžete vytvořit a nahrát do vaší konfigurace P2S k bráně. 
5. Extrahování privátní klíč a kryptografický otisk base64 z soubor .pfx. To lze provést několika způsoby. Pomocí OpenSSL ve vašem počítači je jedním ze způsobů.

    ```
    openssl.exe pkcs12 -in "filename.pfx" -nodes -out "profileinfo.txt"
    ```
  *Profileinfo.txt* soubor bude obsahovat privátní klíč a kryptografický otisk certifikační Autority a certifikát klienta. Nezapomeňte použít kryptografický otisk klientského certifikátu.

6. Otevřít *profileinfo.txt* v textovém editoru. Pokud chcete získat kryptografický otisk certifikátu klienta (podřízený), vyberte text, včetně a mezi "---začátek certifikát---" a "---konec certifikát---" pro podřízené certifikát a zkopírujte ho. Certifikát podřízené můžete identifikovat podle předmětu = / řádek.

7.  Otevřít *vpnconfig.ovpn* souborů a vyhledejte část uvedenou níže. Nahradit vše mezi a "cert" a "/ certifikátu".

    ```
    # P2S client certificate
    # please fill this field with a PEM formatted cert
    <cert>
    $CLIENTCERTIFICATE
    </cert>
    ```
8.  Otevřete profileinfo.txt v textovém editoru. Pokud chcete získat privátní klíč, vyberte text, včetně a mezi "---začátek PRIVÁTNÍ klíč---" a "---BEGIN PRIVATE KEY---" a zkopírujte ho.

9.  Otevřete soubor vpnconfig.ovpn v textovém editoru a najděte v této části. Vložte privátní klíč, který nahrazuje všechno mezi a "klíče" a "/ klíčů".

    ```
    # P2S client root certificate private key
    # please fill this field with a PEM formatted key
    <key>
    $PRIVATEKEY
    </key>
    ```

10. Ostatní pole ponechte beze změny. S použitím vyplněné konfigurace ve vstupu klienta se připojte k síti VPN.
11. Připojení pomocí příkazového řádku, zadejte následující příkaz:
  
  ```
  sudo openvpn –-config <name and path of your VPN profile file>
  ```
12. Chcete-li připojit pomocí grafického uživatelského rozhraní, přejděte na nastavení systému.
13. Klikněte na tlačítko **+** přidat nové připojení VPN.
14. V části **přidat VPN**, vyberte **importovat ze souboru...**
15. Vyhledejte soubor profilu a poklikáním nebo vyberte **otevřít**.
16. Klikněte na tlačítko **přidat** na **přidat VPN** okna.
  
  ![Importovat ze souboru](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
17. Můžete připojit síť VPN zapnete **ON** na **nastavení sítě** stránky, nebo na ikonu sítě v oznamovací oblasti.

## <a name="next-steps"></a>Další postup

Pokud chcete, aby klienti VPN bude mít přístup k prostředkům v jiné virtuální síti (produkční), postupujte podle pokynů [připojení typu VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) článku nastavte připojení typu vnet-to-vnet. Je nutné povolit protokol BGP na brány a připojení, jinak nebude tok provozu.
