---
title: Azure Stacková Mini R Wi-Fi Správa na hraničních zařízeních
description: Popisuje, jak používat Azure Portal ke správě Wi-Fi na Azure Stack hraničních zařízeních.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: a2cc0707c344c3ca537795666a3f60f648026596
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043763"
---
# <a name="use-the-local-web-ui-to-manage-wireless-connectivity-on-your-azure-stack-edge-mini-r"></a>Použití místního webového uživatelského rozhraní ke správě bezdrátového připojení na Azure Stack hraničních počítačích na hranici Mini R

Tento článek popisuje, jak spravovat připojení bezdrátové sítě na Azure Stack hraničních zařízeních v R. Pomocí místního webového uživatelského rozhraní na Azure Stack hraničních zařízeních v R můžete přidat, připojit a odstranit Wi-Fi profily.

## <a name="about-wi-fi"></a>O Wi-Fi

Azure Stack hraničních zařízení v jazyce R může fungovat v případě, že jsou kabelové i v síti, nebo přes bezdrátovou síť. Zařízení má port Wi-Fi, který musí být povolený, aby bylo možné zařízení připojit k bezdrátové síti. 

Vaše zařízení má pět portů, PORT 1 až PORT 4 a pátý Wi-Fi port. Tady je diagram zadní roviny v případě připojení k bezdrátové síti.

![Kabeláž pro Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)


## <a name="add-connect-to-wi-fi-profile"></a>Přidat, připojit k Wi-Fi profilu

Pokud chcete přidat profil Wi-Fi a připojit se k němu, proveďte následující kroky v místním uživatelském rozhraní vašeho zařízení.

1. Přejít na stránku **Začínáme** v místním webovém uživatelském rozhraní vašeho zařízení. Na dlaždici **síť** vyberte **Konfigurovat**.  
    
    Na fyzickém zařízení existuje pět síťových rozhraní. Porty 1 a 2 představují síťová rozhraní s rychlostí 1 GB/s. PORTY 3 a 4 jsou všechna síťová rozhraní s rychlostí 10 GB/s. Pátý port je Wi-Fi port. 

    [![Místní webové uživatelské rozhraní "Stránka 1" nastavení sítě](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)  
    
    Vyberte port Wi-Fi a nakonfigurujte nastavení portu. 
    
    > [!IMPORTANT]
    > Důrazně doporučujeme nakonfigurovat statickou IP adresu pro port Wi-Fi.  

    ![Místní webové uživatelské rozhraní "nastavení sítě" – Stránka 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Po použití nastavení Wi-Fi portu se **Síťová** stránka aktualizuje.

    ![Místní webové uživatelské rozhraní "Stránka 3" – nastavení sítě](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)

   
2. Vyberte **Přidat profil Wi-Fi** a nahrajte profil Wi-Fi. 

    ![Místní webové uživatelské rozhraní "port nastavení sítě Wi-Fi" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Profil bezdrátové sítě obsahuje identifikátor SSID (název sítě), heslo a informace o zabezpečení, aby se mohl připojit k bezdrátové síti. Můžete získat profil Wi-Fi pro vaše prostředí od správce sítě.

    Informace o přípravě profilů Wi-Fi najdete v tématu [použití profilů Wi-Fi s Azure Stack hraničních zařízení](azure-stack-edge-mini-r-use-wifi-profiles.md)v jazyce R.

    ![Místní webové uživatelské rozhraní "port nastavení sítě Wi-Fi" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Po přidání profilu se seznam profilů Wi-Fi aktualizuje tak, aby odrážel nový profil. Profil by měl zobrazovat **stav připojení** jako **Odpojeno**. 

    ![Místní webové uživatelské rozhraní "port nastavení sítě Wi-Fi" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)
    
3. Po úspěšném načtení profilu bezdrátové sítě se připojte k tomuto profilu. Vyberte **připojit k profilu Wi-Fi**. 

    ![Místní webové uživatelské rozhraní "port Wi-Fi nastavení sítě" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

4. Vyberte profil Wi-Fi, který jste přidali v předchozím kroku, a vyberte **použít**. 

    ![Místní webové uživatelské rozhraní "port Wi-Fi nastavení sítě" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **Stav připojení** by měl být aktualizován na **připojeno**. Síla signálu se aktualizuje, aby označovala kvalitu signálu. 

    ![Místní webové uživatelské rozhraní "port Wi-Fi nastavení sítě" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Pro přenos velkých objemů dat doporučujeme místo bezdrátové sítě použít kabelové připojení. 


## <a name="download-wi-fi-profile"></a>Stáhnout profil Wi-Fi

Můžete si stáhnout profil Wi-Fi, který používáte pro připojení k bezdrátové síti.

1. V místním webovém uživatelském rozhraní zařízení, klikněte na **konfigurace > síť**. 

2. V části nastavení profilu Wi-Fi vyberte **Stáhnout profil**. To by mělo stáhnout profil Wi-Fi, který aktuálně používáte.


## <a name="delete-wi-fi-profile"></a>Odstranit profil Wi-Fi

Můžete odstranit profil Wi-Fi, který používáte pro připojení k bezdrátové síti.


1. V místním webovém uživatelském rozhraní zařízení, klikněte na **konfigurace > síť**. 

2. V části nastavení profilu Wi-Fi vyberte **odstranit Wi-Fi profil**.

3. V okně **odstranit Wi-Fi profil** vyberte profil, který chcete odstranit. Vyberte **Použít**.


## <a name="configure-cisco-wi-fi-profile"></a>Konfigurace profilu Cisco Wi-Fi

Tady je několik pokynů pro správu a konfiguraci bezdrátového kontroleru Cisco a přístupového bodu na zařízení. 

### <a name="dhcp-bridging-mode"></a>Režim přemostění DHCP

Pokud chcete pro své zařízení použít bezdrátový adaptér Cisco, musíte povolit režim přemostění protokolu DHCP (Dynamic Host Configuration Protocol) na řadiči bezdrátové sítě LAN (WLC).

Další informace najdete v tématu [Režim přemostění DHCP](https://www.cisco.com/c/en/us/support/docs/wireless/4400-series-wireless-lan-controllers/110865-dhcp-wlc.html#anc9).

#### <a name="bridging-configuration-example"></a>Příklad přemostění konfigurace

Chcete-li povolit funkci přemostění DHCP na řadiči, je nutné zakázat funkci proxy serveru DHCP na řadiči. Povolení přemostění DHCP pomocí příkazového řádku:

```powershell
(Cisco Controller) > config dhcp proxy disable
(Cisco Controller) > show dhcp proxy
DHCP Proxy Behaviour: disabled
```

Pokud server DHCP neexistuje ve stejné síti protokolu L2 (Layer 2) jako klient, všesměrové vysílání by se mělo přeposílat na server DHCP v klientské bráně pomocí podpory protokolu IP. Toto je ukázka této konfigurace:

```powershell
Switch#conf t
Switch(config)#interface vlan <client vlan #>
Switch(config-if)#ip helper-address <dhcp server IP>
```

Funkce přemostění DHCP je globální nastavení, takže má vliv na všechny transakce DHCP v rámci kontroleru. Musíte přidat pomocná prohlášení protokolu IP v kabelové infrastruktuře pro všechny nezbytné virtuální místní sítě (VLAN) s na řadiči.

### <a name="enable-the-passive-client-for-wlan"></a>Povolit pasivního klienta pro síť WLAN

Povolení funkce pasivního klienta pro bezdrátovou místní síť (WLAN) na bezdrátovém řadiči Cisco:

* Rozhraní přidružené k síti WLAN musí mít povolený označování pomocí značek VLAN.
* SÍŤ VLAN vícesměrového vysílání musí být povolená pro síť WLAN.
* V WLC musí být povoleno předávání GARP.

Další informace najdete v tématu [vícesměrové vysílání VLAN informace o optimalizaci vícesměrového vysílání](https://www.cisco.com/c/en/us/td/docs/wireless/controller/8-5/config-guide/b_cg85/wlan_interfaces.html).

### <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s přidělováním IP adres na virtuálních počítačích, které běží na Azure Stack hraničních zařízeních v oblasti R, měli byste ověřit výše uvedené nastavení konfigurace v síťovém prostředí.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit Azure Stack hraničních zařízení R](azure-stack-edge-mini-r-deploy-prep.md).
