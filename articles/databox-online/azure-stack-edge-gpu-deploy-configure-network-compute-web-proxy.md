---
title: Kurz konfigurace nastavení sítě pro zařízení Azure Stack Edge pro s grafickým procesorem v Azure Portal | Microsoft Docs
description: Kurz nasazení Azure Stack Edge pro grafický procesor vám dává pokyn ke konfiguraci sítě, výpočetní sítě a nastavení webového proxy serveru pro vaše fyzické zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 07a4c06b840d41455beea9be4ed0343b4946ddb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99594599"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-with-gpu"></a>Kurz: konfigurace sítě pro Azure Stack Edge pro pomocí GPU

V tomto kurzu se dozvíte, jak nakonfigurovat síť pro zařízení Azure Stack Edge pro s připojením GPU pomocí místního webového uživatelského rozhraní.

Dokončení procesu připojení může trvat přibližně 20 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Konfigurace sítě
> * Povolit výpočetní síť
> * Konfigurace webového proxy serveru


## <a name="prerequisites"></a>Požadavky

Než nakonfigurujete a nastavíte Azure Stack Edge pro zařízení s grafickým procesorem, ujistěte se, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-install.md).
* Připojili jste se k místnímu webovému uživatelskému rozhraní zařízení, jak je popsáno v části [připojení k Azure Stack Edge pro](azure-stack-edge-gpu-deploy-connect.md) .


## <a name="configure-network"></a>Konfigurace sítě

Stránka **Začínáme** zobrazuje různá nastavení, která jsou nutná ke konfiguraci a registraci fyzického zařízení ve službě Azure Stack Edge. 

Pomocí těchto kroků nakonfigurujete síť pro vaše zařízení.

1. V místním webovém uživatelském rozhraní zařízení, **navštivte stránku Začínáme** . 

2. Na dlaždici **síť** vyberte **Konfigurovat**.  
    
    ![Dlaždice nastavení sítě místního webového uživatelského rozhraní](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    Na fyzickém zařízení je šest síťových rozhraní. Porty 1 a 2 představují síťová rozhraní s rychlostí 1 GB/s. PORT 3, PORT 4, PORT 5 a PORT 6 jsou všechna síťová rozhraní s 25 GB/s, která mohou sloužit také jako síťová rozhraní s rychlostí 10 GB/s. PORT 1 je automaticky nakonfigurován jako port jen pro správu a PORT 2 na PORT 6 jsou všechny datové porty. Pro nové zařízení je stránka **nastavení sítě** uvedená níže.
    
    ![Stránka místní webové uživatelské rozhraní "nastavení sítě"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)

3. Chcete-li změnit nastavení sítě, vyberte port a v pravém podokně, které se zobrazí, upravte IP adresu, podsíť, bránu, primární DNS a sekundární DNS. 

    - Pokud vyberete port 1, uvidíte, že je předem nakonfigurovaný jako statický. 

        ![Místní webové uživatelské rozhraní "port 1 – nastavení sítě"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - Pokud vyberete port 2, port 3, port 4 nebo port 5, všechny tyto porty budou ve výchozím nastavení nakonfigurovány jako DHCP.

        ![Místní webové uživatelské rozhraní "port 3 nastavení sítě"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Při konfiguraci nastavení sítě mějte na paměti následující:

   * Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. IP adresa, podsíť, brána a DNS se přiřadí automaticky.
   * Pokud není protokol DHCP povolený, můžete v případě potřeby přiřadit statické IP adresy.
   * Síťové rozhraní můžete nakonfigurovat jako IPv4.
   * U rozhraní s 25 GB/s můžete nastavit režim RDMA (Remote Direct Access Memory) na iWarp nebo RoCE (RDMA přes sblíženou síť Ethernet). Pokud jsou nízké latence zásadním požadavkem a škálovatelností není obavou, použijte RoCE. Když je latence klíčovým požadavkem, ale lepším využitím a škálovatelností jsou i vysoké priority, iWARP je nejlepší kandidát.
   * Seskupování síťových adaptérů (NIC) nebo agregace propojení se u Azure Stack Edge nepodporují. 
   * Sériové číslo pro libovolný port odpovídá sériovému číslu uzlu.

    Po nakonfigurování sítě zařízení se stránka aktualizuje, jak je uvedeno níže.

    ![Místní webové uživatelské rozhraní "nastavení sítě" – Stránka 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     > [!NOTE]
     > Doporučujeme neměnit místní IP adresu síťového rozhraní ze statické adresy na adresu DHCP, pokud nemáte další IP adresu pro připojení k zařízení. Pokud používáte jedno síťové rozhraní a přepnete na adresu DHCP, adresu DHCP nebude možné určit. Pokud chcete přejít na adresu DHCP, počkejte na to, až se zařízení aktivuje se službou, a pak změňte. Potom můžete zobrazit IP adresy všech adaptérů ve **vlastnostech zařízení** v Azure Portal pro vaši službu.


    Po nakonfigurování a použití nastavení sítě vyberte **Další: COMPUTE** a nakonfigurujte výpočetní síť.

## <a name="enable-compute-network"></a>Povolit výpočetní síť

Pomocí těchto kroků povolíte výpočetní prostředky a nakonfigurujete výpočetní síť. 

<!--1. Go to the **Get started** page in the local web UI of your device. On the **Network** tile, select **Compute network**.  

    ![Compute page in local UI 1](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)-->

1. Na stránce **COMPUTE** vyberte síťové rozhraní, které chcete povolit pro výpočetní výkon. 

    ![Stránka COMPUTE v místním uživatelském rozhraní 2](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. V dialogovém okně **nastavení sítě** vyberte **Povolit**. Pokud povolíte výpočetní prostředky, na zařízení se na tomto síťovém rozhraní vytvoří virtuální přepínač. Virtuální přepínač se používá pro výpočetní infrastrukturu na zařízení. 
    
1. Přiřaďte **IP adresy uzlu Kubernetes**. Tyto statické IP adresy jsou pro výpočetní virtuální počítač.  

    U *n*-Node zařízení se pro výpočetní virtuální počítač, který používá počáteční a KONCOVá IP adresa, nabízí souvislý rozsah minimálně *n + 1* IPv4 adres (nebo více). Daná Azure Stack Edge je zařízení s jedním uzlem, je k dispozici minimálně 2 souvislé adresy IPv4.

    > [!IMPORTANT]
    > Kubernetes na Azure Stack Edge používá podsíť 172.27.0.0/16 pro podsíť pod a 172.28.0.0/16 pro službu. Ujistěte se, že se nepoužívají ve vaší síti. Pokud se tyto podsítě už ve vaší síti používají, můžete tyto podsítě změnit spuštěním `Set-HcsKubeClusterNetworkInfo` rutiny z rozhraní PowerShellu zařízení. Další informace najdete v tématu [Změna Kubernetes pod a podsítí služby](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Přiřaďte **IP adresy externích služeb Kubernetes**. Toto jsou také IP adresy vyrovnávání zatížení. Tyto souvislé IP adresy jsou pro služby, které chcete zveřejnit mimo cluster Kubernetes, a určete rozsah statických IP adres v závislosti na počtu zveřejněných služeb. 
    
    > [!IMPORTANT]
    > Důrazně doporučujeme zadat minimálně jednu IP adresu pro službu Azure Stack Edge pro pro přístup k výpočetním modulům. Volitelně můžete zadat další IP adresy pro jiné služby nebo moduly IoT Edge (1 na službu/modul), ke kterým je potřeba mít pøístup z vnějšku clusteru. IP adresy služby se dají aktualizovat později. 
    
1. Vyberte **Použít**.

    ![Stránka COMPUTE v místním uživatelském rozhraní 3](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Použití konfigurace trvá několik minut a může být nutné aktualizovat prohlížeč. Můžete vidět, že zadaný port je povolen pro výpočetní výkon. 
 
    ![Stránka COMPUTE v místním uživatelském rozhraní 4](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Vyberte **Další: webový proxy server** pro konfiguraci webového proxy serveru.  

  
## <a name="configure-web-proxy"></a>Konfigurace webového proxy serveru

Toto je volitelná konfigurace.

> [!IMPORTANT]
> * Pokud povolíte výpočetní prostředí a použijete modul IoT Edge na zařízení Azure Stack Edge pro, doporučujeme, abyste nastavili ověřování webového proxy serveru jako **žádné**. Protokol NTLM se nepodporuje.
> * Soubory automatické konfigurace proxy serveru (PAC) nejsou podporovány. Soubor PAC definuje způsob, jakým můžou webové prohlížeče a další uživatelské agenti automaticky zvolit odpovídající proxy server (přístupovou metodu) pro načtení dané adresy URL. 
> * Transparentní proxy servery fungují s Azure Stack Edge Pro bez problému. Pro netransparentní proxy servery, které zachycují a čtou veškerý provoz (prostřednictvím vlastních certifikátů nainstalovaných v proxy server), nahrajte veřejný klíč certifikátu proxy serveru jako podpisový řetězec na zařízení Azure Stack Edge pro. Pak můžete na svém zařízení Azure Stack Edge nakonfigurovat nastavení proxy serveru. Další informace najdete v tématech [Přineste si vlastní certifikáty a nahrajte je přes místní uživatelské rozhraní](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).  

<!--1. Go to the **Get started** page in the local web UI of your device.
2. On the **Network** tile, configure your web proxy server settings. Although web proxy configuration is optional, if you use a web proxy, you can configure it on this page only.

   ![Local web UI "Web proxy settings" page](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)-->

1. Na stránce **nastavení webového proxy serveru** proveďte následující kroky:

    1. Do pole **Adresa URL webového proxy serveru** zadejte adresu URL v tomto formátu: `http://host-IP address or FQDN:Port number` . Adresy URL protokolu HTTPS se nepodporují.

    2. V části **Ověřování** vyberte **Žádné** nebo **NTLM**. Pokud povolíte výpočetní prostředí a použijete modul IoT Edge na zařízení Azure Stack Edge pro, doporučujeme, abyste nastavili ověřování webového proxy serveru na **žádné**. **Protokol NTLM** není podporován.

    3. Pokud používáte ověřování, zadejte uživatelské jméno a heslo.

    4. Pokud chcete ověřit a použít nakonfigurovaná nastavení webového proxy serveru, vyberte **použít**.
    
   ![Místní webové uživatelské rozhraní – stránka 2 nastavení webového proxy serveru](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. Po použití nastavení vyberte **Další: zařízení**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Konfigurace sítě
> * Povolit výpočetní síť
> * Konfigurace webového proxy serveru


Informace o tom, jak nastavit Azure Stack hraniční zařízení pro, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace nastavení zařízení](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
