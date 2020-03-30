---
title: Řešení Azure VMware od CloudSimple – konfigurace sítě VPN mezi místním a privátním cloudem
description: Popisuje, jak nakonfigurovat připojení VPN site-to-site nebo point-to-site mezi místní sítí a privátním cloudem CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d000d8390375466232c7daac2a4a056ef424be79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087126"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Konfigurace připojení VPN k privátnímu cloudu CloudSimple

Brány VPN umožňují připojení k síti CloudSimple z místní sítě a z klientského počítače vzdáleně.  V tomto článku najdete informace o nastavení bran VPN z portálu CloudSimple.  Připojení VPN mezi místní sítí a sítí CloudSimple poskytuje přístup k virtuálnímu centru a úlohám ve vašem privátním cloudu. CloudSimple podporuje brány VPN point-to-site i brány VPN site-to-site.

## <a name="vpn-gateway-types"></a>Typy brány VPN

* Připojení **VPN z bodu na místo** je nejjednodušší způsob, jak se připojit k privátnímu cloudu z počítače. Pro vzdálené připojení k privátnímu cloudu použijte připojení VPN z bodu na místo.
* Připojení **VPN site-to-site** umožňuje nastavit úlohy privátního cloudu pro přístup k místním službám. Místní službu Active Directory můžete také použít jako zdroj identity pro ověřování v programu Private Cloud vCenter.  V současné době je podporován typ **VPN založený na zásadách.**

V oblasti můžete vytvořit jednu bránu VPN site-to-site a jednu bránu VPN point-to-site.

## <a name="point-to-site-vpn"></a>Point-to-site VPN

Informace o vytvoření brány VPN bodu na web naleznete v [tématu Vytvoření brány VPN bodu na web](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Připojení k CloudSimple pomocí technologie Point-to-Site VPN

Klient VPN je potřebný pro připojení k CloudSimple z vašeho počítače.  Stáhněte si [klienta OpenVPN](https://openvpn.net/community-downloads/) pro Windows nebo [Viskozitu](https://www.sparklabs.com/viscosity/download/) pro macOS a OS X.

1. Spusťte portál CloudSimple a vyberte **možnost Síť**.
2. Vyberte **bránu VPN**.
3. V seznamu bran VPN klikněte na bránu VPN point-to-site.
4. Vyberte **Uživatelé**.
5. Klikněte na **Stáhnout konfiguraci VPN**

    ![Stažení konfigurace zařízení VPN](media/download-p2s-vpn-configuration.png)

6. Import konfigurace v klientovi VPN

    * Pokyny pro [import konfigurace v klientovi systému Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Pokyny pro [import konfigurace v macOS nebo OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Připojte se k bráně CloudSimple VPN.

Příklad níže ukazuje import připojení pomocí **Klienta viskozity**.

#### <a name="import-connection-on-viscosity-client"></a>Import připojení na klienta Viskozity

1. Extrahujte obsah konfigurace VPN ze staženého souboru ZIP.

2. Otevřete v počítači viskozitu.

3. Klepněte **+** na ikonu a vyberte **importpřipojení** > **ze souboru**.

    ![Import konfigurace VPN ze souboru](media/import-p2s-vpn-config.png)

4. Vyberte konfigurační soubor OpenVPN (.ovpn) pro protokol, který chcete použít, a klepněte na tlačítko **Otevřít**.

    ![Síť VPN](media/import-p2s-vpn-config-choose-ovpn.png)

Připojení se nyní zobrazí v nabídce Viskozita.

#### <a name="connect-to-the-vpn"></a>Připojte se k síti VPN.

Chcete-li se připojit k VPN pomocí klienta Viscosity OpenVPN, vyberte připojení z nabídky. Ikona nabídky se aktualizuje, což znamená, že je navázáno připojení.

![Síť VPN](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Připojení k více privátním cloudům

Připojení VPN bodu na místo řeší názvy DNS prvního privátního cloudu, který vytvoříte. Pokud chcete získat přístup k jiným privátním cloudům, je nutné aktualizovat server DNS v klientovi VPN.

1. Spusťte [portál CloudSimple](access-cloudsimple-portal.md).

2. Přejděte do**privátního cloudu** **Resources** > a vyberte privátní cloud, ke kterému se chcete připojit.

3. Na stránce **Souhrn** privátního cloudu zkopírujte IP adresu IP serveru PRIVATE Cloud DNS v části **Základní informace**.

    ![Privátní cloudové servery DNS](media/private-cloud-dns-server.png)

4. Klepněte pravým tlačítkem myši na ikonu Viskozity na hlavním panelu počítače a vyberte **položku Předvolby**.

    ![Síť VPN](media/vis00.png)

5. Vyberte připojení CloudSimple VPN.

    ![Připojení k síti VPN](media/viscosity-client.png)

6. Kliknutím na **Upravit** změňte vlastnosti připojení.

    ![Upravit připojení VPN](media/viscosity-edit-connection.png)

7. Klepněte na kartu **Síť** a zadejte IP adresy serveru PRIVATE Cloud DNS ```cloudsimple.io```oddělené čárkou nebo mezerou a doménu jako .  Vyberte **Ignorovat nastavení DNS odeslaná serverem VPN**.

    ![Síť VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Chcete-li se připojit k prvnímu privátnímu cloudu, odeberte tato nastavení a připojte se k serveru VPN.

## <a name="site-to-site-vpn"></a>Site-to-site VPN

Informace o vytvoření brány VPN site-to-site najdete v tématu [Vytvoření brány VPN mezi lokalitami](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  Tyto výhody poskytuje připojení VPN site-to-site z místní sítě do privátního cloudu.  

* Dostupnost vašeho private cloudu vCenter z libovolné pracovní stanice v místní síti
* Použití místní služby Active Directory jako zdroje identity vCenter
* Pohodlný přenos šablon virtuálních zařízení, ISO a dalších souborů z místních prostředků do vašeho privátního cloudu vCenter
* Dostupnost úloh spuštěných ve vašem privátním cloudu z místní sítě

Informace o nastavení místní brány VPN v režimu vysoké dostupnosti najdete v [tématu Konfigurace připojení VPN s vysokou dostupností](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Nastavte tcp MSS upnutí na 1200 na vašem zařízení VPN. Nebo pokud vaše zařízení VPN nepodporují upínání MSS, můžete alternativně nastavit MTU na rozhraní tunelu na 1240 bajtů.
> 2. Po nastavení sítě VPN site-to-site přepošlete požadavky DNS pro *.cloudsimple.io na servery DNS privátního cloudu.  Postupujte podle pokynů v [místním nastavení DNS](on-premises-dns-setup.md).
