---
title: Řešení Azure VMware (AVS) – konfigurace sítě VPN mezi místním prostředím a privátním cloudem služby AVS
description: Popisuje, jak nakonfigurovat připojení typu Site-to-site nebo VPN typu Point-to-site mezi vaší místní sítí a Vaším privátním cloudem služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8188fac270eadb6e09cc3561ddefb05aa59ba661
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087126"
---
# <a name="configure-a-vpn-connection-to-your-avs-private-cloud"></a>Konfigurace připojení VPN k privátnímu cloudu služby AVS

Brány VPN umožňují vzdálené připojení k síti funkce AVS z místní sítě a z klientského počítače. V tomto článku najdete informace o nastavení bran sítě VPN z portálu služby AVS. Připojení VPN mezi vaší místní sítí a vaší sítí služby AVS zajišťuje přístup k vCenter a úlohám v privátním cloudu služby AVS. AVS podporuje brány VPN typu Point-to-Site VPN a Site-to-site.

## <a name="vpn-gateway-types"></a>Typy bran VPN

* Připojení **VPN typu Point-to-site** je nejjednodušší způsob, jak se připojit k privátnímu cloudu služby AVS z počítače. Použijte připojení VPN typu Point-to-site pro vzdálené připojení k privátnímu cloudu služby AVS.
* Připojení **VPN typu Site-to-site** umožňuje nastavit vaše úlohy privátního cloudu služby AVS pro přístup k místním službám. Místní službu Active Directory můžete použít také jako zdroj identity pro ověřování do privátního cloudu vCenter služby AVS. V současné době je podporován typ **sítě VPN založený na zásadách** .

V oblasti můžete vytvořit jednu bránu VPN typu Site-to-site a jednu bránu VPN typu Point-to-site.

## <a name="point-to-site-vpn"></a>Point-to-Site VPN

Pokud chcete vytvořit bránu VPN typu Point-to-site, přečtěte si téma [Vytvoření brány VPN typu Point-to-site](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Připojení k funkci AVS pomocí sítě VPN typu Point-to-site

Klient VPN je potřebný pro připojení k funkci AVS z počítače. Stáhněte si [klienta OpenVPN](https://openvpn.net/community-downloads/) pro Windows nebo [viskozitu](https://www.sparklabs.com/viscosity/download/) pro MacOS a OS X.

1. Spusťte portál AVS a vyberte **síť**.
2. Vyberte **VPN Gateway**.
3. V seznamu bran VPN klikněte na bránu VPN typu Point-to-site.
4. Vyberte možnost **Uživatelé**.
5. Klikněte na **Stáhnout konfiguraci sítě VPN** .

    ![Stažení konfigurace zařízení VPN](media/download-p2s-vpn-configuration.png)

6. Import konfigurace na klienta VPN

    * Pokyny pro [Import konfigurace na klienta Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Pokyny pro [Import konfigurace v MacOS nebo OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Připojte se k bráně VPN typu AVS.

Níže uvedený příklad ukazuje Import připojení pomocí **klienta viskozity**.

#### <a name="import-connection-on-viscosity-client"></a>Importovat připojení v klientovi viskozity

1. Extrahuje obsah konfigurace sítě VPN ze staženého souboru. zip.

2. V počítači otevřete viskozitu.

3. Klikněte na ikonu **+** a vyberte **importovat připojení** > **ze souboru**.

    ![Importovat konfiguraci sítě VPN ze souboru](media/import-p2s-vpn-config.png)

4. Vyberte konfigurační soubor OpenVPN (. ovpn) pro protokol, který chcete použít, a klikněte na **otevřít**.

    ![Síť VPN](media/import-p2s-vpn-config-choose-ovpn.png)

Připojení se nyní zobrazí v nabídce viskozita.

#### <a name="connect-to-the-vpn"></a>Připojte se k síti VPN.

Pokud se chcete připojit k síti VPN pomocí klienta viskozita OpenVPN, vyberte připojení z nabídky. Ikona nabídky se aktualizuje, aby označovala, že je připojení navázáno.

![Síť VPN](media/vis03.png)

### <a name="connecting-to-multiple-avs-private-clouds"></a>Připojení k více privátním cloudům pro funkci AVS

Připojení VPN typu Point-to-site řeší názvy DNS prvního privátního cloudu služby AVS, který vytvoříte. Pokud chcete získat přístup k jiným privátním cloudům služby AVS, musíte aktualizovat server DNS na svém klientovi VPN.

1. Spusťte [portál AVS](access-cloudsimple-portal.md).

2. Přejděte k **prostředkům** > **privátní cloudy funkce AVS** a vyberte privátní cloud, ke kterému se chcete připojit.

3. Na stránce **Souhrn** v privátním cloudu služby AVS zkopírujte IP adresu serveru DNS privátního cloudu pro službu AVS v části **základní informace**.

    ![Servery DNS privátního cloudu služby AVS](media/private-cloud-dns-server.png)

4. Klikněte pravým tlačítkem myši na ikonu viskozity v hlavním panelu systému a vyberte možnost **Předvolby**.

    ![Síť VPN](media/vis00.png)

5. Vyberte připojení k síti VPN ve službě AVS.

    ![Připojení k síti VPN](media/viscosity-client.png)

6. Klikněte na **Upravit** a změňte vlastnosti připojení.

    ![Upravit připojení VPN](media/viscosity-edit-connection.png)

7. Klikněte na kartu **sítě** a zadejte IP adresy privátního cloudu služby AVS, které jsou oddělené čárkou nebo mezerou a doménu jako ```az.cloudsimple.io```. Vyberte **Ignorovat nastavení DNS odesílaná serverem VPN**.

    ![Sítě VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Pokud se chcete připojit k prvnímu privátnímu cloudu služby AVS, odeberte tato nastavení a připojte se k serveru VPN.

## <a name="site-to-site-vpn"></a>Site-to-Site VPN

Chcete-li vytvořit bránu VPN typu Site-to-site, přečtěte si téma [Vytvoření brány VPN typu Site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway). Tyto výhody přináší připojení VPN typu Site-to-Site z vaší místní sítě k vašemu privátnímu cloudu služby AVS. 

* Přístupnost vašeho privátního cloudu služby AVS z jakékoli pracovní stanice v místní síti
* Použití místní služby Active Directory jako zdroje identity vCenter
* Pohodlný přenos šablon virtuálních počítačů, soubory ISO a dalších souborů z místních prostředků do vašeho privátního cloudu služby AVS
* Přístupnost úloh běžících na privátním cloudu služby AVS z vaší místní sítě

Pokud chcete nastavit místní bránu VPN v režimu vysoké dostupnosti, přečtěte si téma [Konfigurace připojení VPN s vysokou dostupností](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Na zařízení VPN nastavte připojení TCP MSS na 1200. Nebo pokud vaše zařízení VPN nepodporují svorky MSS, můžete místo toho nastavit jednotku MTU v rozhraní tunelu na 1240 bajtů.
> 2. Po nastavení sítě VPN typu Site-to-site předejte žádosti DNS pro *. cloudsimple.io na servery DNS privátního cloudu služby AVS. Postupujte podle pokynů v [místní instalaci DNS](on-premises-dns-setup.md).
