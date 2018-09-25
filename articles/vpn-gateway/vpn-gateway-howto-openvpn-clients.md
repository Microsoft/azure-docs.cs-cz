---
title: Postup konfigurace klientů OpenVPN pro Azure VPN Gateway | Dokumentace Microsoftu
description: Postup konfigurace klientů OpenVPN pro Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 141f29ea1c7ebdd8208dda6bc39a60f7873dab7d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977838"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway-preview"></a>Konfigurace klientů OpenVPN pro Azure VPN Gateway (Preview)

Tento článek vám pomůže nakonfigurovat OpenVPN klientů.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Ověřte, že jste dokončili postup pro konfiguraci OpenVPN pro bránu VPN. Podrobnosti najdete v tématu [OpenVPN konfigurace pro službu Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

## <a name="windows"></a>Klienti Windows

1. Stažení a instalace klienta OpenVPN z oficiální [OpenVPN webu](https://openvpn.net/index.php/open-source/downloads.html).
2. Stáhněte profil sítě VPN pro bránu. To můžete udělat na kartě Konfigurace Point-to-site na webu Azure Portal, nebo ' New-AzureRmVpnClientConfiguration "v prostředí PowerShell.
3. Rozbalte profilu. Pak otevřete konfigurační soubor vpnconfig.ovpn ze složky OpenVPN v poznámkovém bloku.
4. Zadejte části certifikátu klienta P2S s P2S klienta veřejný klíč certifikátu v kódování base64. V certifikátu PEM formátovaný můžete jednoduše otevřete soubor .cer a kopírování přes klíč ve formátu base64 mezi záhlaví certifikátu. Tady uvidíte exportování certifikátu se získat kódované veřejný klíč.
5. Zadejte privátní klíče oddílu s P2S klienta privátní klíč certifikátu v kódování base64. Informace o tom, jak extrahovat privátní klíč najdete v tématu [exportovat klíč](vpn-gateway-certificates-point-to-site.md#clientexport).
6. Neměňte žádná pole. Použijte vyplněné v konfiguraci ve vstupu klienta pro připojení k síti VPN.
7. Zkopírujte soubor vpnconfig.ovpn do složky C:\Program Files\OpenVPN\config.
8. Klikněte pravým tlačítkem na ikonu OpenVPN na hlavním panelu a klikněte na připojit.

## <a name="mac"></a>Klienti se systémem Mac

1. Stáhnout a nainstalovat OpenVPN klienta, jako například [TunnelBlik](https://tunnelblick.net/downloads.html). 
2. Stáhněte profil sítě VPN pro bránu. To můžete udělat na kartě Konfigurace point-to-site na webu Azure Portal nebo pomocí "New-AzureRmVpnClientConfiguration" v prostředí PowerShell.
3. Rozbalte profilu. Otevřete konfigurační soubor vpnconfig.ovpn ze složky OpenVPN v poznámkovém bloku.
4. Zadejte části certifikátu klienta P2S s P2S klienta veřejný klíč certifikátu v kódování base64. V certifikátu PEM formátovaný můžete jednoduše otevřete soubor .cer a kopírování přes klíč ve formátu base64 mezi záhlaví certifikátu. Zobrazit [Export veřejného klíče](vpn-gateway-certificates-point-to-site.md#cer) informace o exportování certifikátu se získat kódované veřejný klíč.
5. Zadejte privátní klíče oddílu s P2S klienta privátní klíč certifikátu v kódování base64. Zobrazit [exportovat soukromý klíč](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) informace o tom, jak extrahovat privátní klíč.
6. Neměňte žádná pole. Použijte vyplněné v konfiguraci ve vstupu klienta pro připojení k síti VPN.
7. Poklikejte na soubor profilu a vytvořte profil v tunnelblik.
8. Spusťte Tunnelblik ze složky aplikace.
9. Klikněte na ikonu Tunneblik na hlavním panelu systému a připojit výběru.

## <a name="linux"></a>Klienti Linux

1. Otevřete novou relaci terminálu. Ve stejnou dobu můžete otevřít novou relaci stisknutím klávesy "Ctrl + Alt + t.
2. Zadejte následující příkaz pro instalaci potřebných součástí:

  ```
  sudo apt-get install openvpn
  sudo apt-get -y install network-manager-openvpn
  sudo service network-manager restart
  ```
3. Stáhněte profil sítě VPN pro bránu. To můžete udělat na kartě Konfigurace Point-to-site na webu Azure Portal nebo pomocí "New-AzureRmVpnClientConfiguration" v prostředí PowerShell.
4. Zadejte privátní klíče oddílu s P2S klienta privátní klíč certifikátu v kódování base64. Zobrazit [exportovat soukromý klíč](https://www.geotrust.eu/en/support/manuals/microsoft/all+windows+servers/export+private+key+or+certificate/) informace o tom, jak extrahovat privátní klíč.
5. Připojení pomocí příkazového řádku, zadejte následující příkaz:
  
  ```
  Sudo openvpn –config <name and path of your VPN profile file>
  ```
5. Chcete-li připojit pomocí grafického uživatelského rozhraní, přejděte na nastavení systému.
6. Klikněte na tlačítko **+** přidat nové připojení VPN.
7. V části **přidat VPN**, vyberte **importovat ze souboru...**
8. Vyhledejte soubor profilu a poklikáním nebo vyberte **otevřít**
9. Klikněte na tlačítko **přidat** na **přidat VPN** okna.
  
  ![Importovat ze souboru](./media/vpn-gateway-howto-openvpn-clients/importfromfile.png)
10. Můžete připojit síť VPN zapnete **ON** na **nastavení sítě** stránky, nebo na ikonu sítě v oznamovací oblasti.

## <a name="next-steps"></a>Další postup

Pokud chcete, aby klienti VPN bude mít přístup k prostředkům v jiné virtuální síti (produkční), postupujte podle pokynů [připojení typu Vnet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) článku nastavte připojení typu vnet-to-vnet. Nezapomeňte povolit protokol BGP na brány a připojení, jinak nebude tok provozu.
