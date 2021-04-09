---
title: Řešení Azure VMware podle CloudSimple – konfigurace sítě VPN mezi místním a privátním cloudem
description: V této části najdete popis postupu konfigurace připojení VPN typu Site-to-site nebo Point-to-site mezi vaší místní sítí a privátním cloudem CloudSimple.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b7a119b538ee07b961011bfa98d748f92cc76f0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899214"
---
# <a name="configure-a-vpn-connection-to-your-cloudsimple-private-cloud"></a>Konfigurace připojení VPN k privátnímu cloudu CloudSimple

Brány VPN vám umožňují připojit se k CloudSimple síti z místní sítě a z klientského počítače vzdáleně.  V tomto článku najdete informace o nastavení bran VPN Gateway z portálu CloudSimple.  Připojení VPN mezi vaší místní sítí a vaší sítí CloudSimple zajišťuje přístup k vCenter a úlohám ve vašem privátním cloudu. CloudSimple podporuje sítě VPN typu Point-to-site i brány VPN typu Site-to-site.

## <a name="vpn-gateway-types"></a>Typy bran VPN

* Připojení **VPN typu Point-to-site** je nejjednodušší způsob, jak se připojit k privátnímu cloudu z vašeho počítače. Použijte připojení VPN typu Point-to-site pro vzdálené připojení k privátnímu cloudu.
* Připojení **VPN typu Site-to-site** umožňuje nastavit vaše úlohy privátního cloudu pro přístup k místním službám. K ověřování do vašeho privátního cloudu vCenter můžete použít také místní službu Active Directory jako zdroj identity.  V současné době je podporován typ **sítě VPN založený na zásadách** .

V oblasti můžete vytvořit jednu bránu VPN typu Site-to-site a jednu bránu VPN typu Point-to-site.

## <a name="point-to-site-vpn"></a>SÍŤ VPN typu Point-to-site

Pokud chcete vytvořit bránu VPN typu Point-to-site, přečtěte si téma [Vytvoření brány VPN typu Point-to-site](vpn-gateway.md#create-point-to-site-vpn-gateway).

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Připojení k CloudSimple pomocí sítě VPN typu Point-to-site

Klient VPN je potřebný pro připojení k CloudSimple z počítače.  Stáhněte si [klienta OpenVPN](https://openvpn.net/community-downloads/) pro Windows nebo [viskozitu](https://www.sparklabs.com/viscosity/download/) pro MacOS a OS X.

1. Spusťte portál CloudSimple a vyberte **síť**.
2. Vyberte **VPN Gateway**.
3. V seznamu bran VPN klikněte na bránu VPN typu Point-to-site.
4. Vyberte **Uživatelé**.
5. Klikněte na **Stáhnout konfiguraci sítě VPN** .

    ![Stažení konfigurace zařízení VPN](media/download-p2s-vpn-configuration.png)

6. Import konfigurace na klienta VPN

    * Pokyny pro [Import konfigurace na klienta Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Pokyny pro [Import konfigurace v MacOS nebo OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Připojte se k bráně VPN CloudSimple.

Níže uvedený příklad ukazuje Import připojení pomocí **klienta viskozity**.

#### <a name="import-connection-on-viscosity-client"></a>Importovat připojení v klientovi viskozity

1. Extrahuje obsah konfigurace sítě VPN ze staženého souboru. zip.

2. V počítači otevřete viskozitu.

3. Klikněte na **+** ikonu a vyberte **importovat připojení**  >  **ze souboru**.

    ![Importovat konfiguraci sítě VPN ze souboru](media/import-p2s-vpn-config.png)

4. Vyberte konfigurační soubor OpenVPN (. ovpn) pro protokol, který chcete použít, a klikněte na **otevřít**.

    ![Snímek obrazovky, který zvýrazní konfigurační soubory OpenVPN, které můžete vybrat.](media/import-p2s-vpn-config-choose-ovpn.png)

Připojení se nyní zobrazí v nabídce viskozita.

#### <a name="connect-to-the-vpn"></a>Připojte se k síti VPN.

Pokud se chcete připojit k síti VPN pomocí klienta viskozita OpenVPN, vyberte připojení z nabídky. Ikona nabídky se aktualizuje, aby označovala, že je připojení navázáno.

![Snímek obrazovky, který zobrazuje stav připojení CloudSimple VPN.](media/vis03.png)

### <a name="connecting-to-multiple-private-clouds"></a>Připojení k více privátním cloudům

Připojení VPN typu Point-to-site řeší názvy DNS prvního privátního cloudu, který vytvoříte. Pokud chcete získat přístup k jiným privátním cloudům, musíte aktualizovat server DNS na svém klientovi VPN.

1. Spusťte [portál CloudSimple](access-cloudsimple-portal.md).

2. Přejděte na **prostředky**  >  **privátní cloudy** a vyberte privátní cloud, ke kterému se chcete připojit.

3. Na stránce **Souhrn** v privátním cloudu zkopírujte IP adresu serveru DNS privátního cloudu v části **základní informace**.

    ![Servery DNS privátního cloudu](media/private-cloud-dns-server.png)

4. Klikněte pravým tlačítkem myši na ikonu viskozity v hlavním panelu systému a vyberte možnost **Předvolby**.

    ![Síť VPN](media/vis00.png)

5. Vyberte připojení VPN CloudSimple.

    ![Připojení k síti VPN](media/viscosity-client.png)

6. Klikněte na **Upravit** a změňte vlastnosti připojení.

    ![Upravit připojení VPN](media/viscosity-edit-connection.png)

7. Klikněte na kartu **sítě** a zadejte IP adresy privátního cloudového serveru DNS oddělené čárkou nebo mezerou a doménou jako ```cloudsimple.io``` .  Vyberte **Ignorovat nastavení DNS odesílaná serverem VPN**.

    ![Sítě VPN](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> Pokud se chcete připojit k prvnímu privátnímu cloudu, odeberte tato nastavení a připojte se k serveru VPN.

## <a name="site-to-site-vpn"></a>Site-to-site VPN

Chcete-li vytvořit bránu VPN typu Site-to-site, přečtěte si téma [Vytvoření brány VPN typu Site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway).  Tyto výhody poskytují připojení VPN typu Site-to-Site z vaší místní sítě do vašeho privátního cloudu.  

* Přístupnost vašeho privátního cloudu vCenter z libovolné pracovní stanice v místní síti
* Použití místní služby Active Directory jako zdroje identity vCenter
* Pohodlný přenos šablon virtuálních počítačů, soubory ISO a dalších souborů z místních prostředků do vašeho privátního cloudu vCenter
* Přístupnost úloh, které běží na vašem privátním cloudu z vaší místní sítě

Pokud chcete nastavit místní bránu VPN v režimu vysoké dostupnosti, přečtěte si téma [Konfigurace připojení VPN s vysokou dostupností](high-availability-vpn-connection.md).

> [!IMPORTANT]
>    1. Na zařízení VPN nastavte připojení TCP MSS na 1200. Nebo pokud vaše zařízení VPN nepodporují svorky MSS, můžete místo toho nastavit jednotku MTU v rozhraní tunelu na 1240 bajtů.
> 2. Po nastavení sítě VPN typu Site-to-site předejte žádosti DNS pro *. cloudsimple.io na servery DNS privátního cloudu.  Postupujte podle pokynů v [místní instalaci DNS](on-premises-dns-setup.md).
