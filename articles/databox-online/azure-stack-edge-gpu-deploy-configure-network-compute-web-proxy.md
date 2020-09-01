---
title: Kurz konfigurace nastavení sítě pro zařízení Azure Stack Edge pomocí GPU v Azure Portal | Microsoft Docs
description: Kurz nasazení Azure Stack hraničního procesoru vám dává pokyn ke konfiguraci sítě, výpočetní sítě a nastavení webového proxy serveru pro vaše fyzické zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 6e7dbc2b96a53d220554e07228a5e30857d12d9c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262970"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-with-gpu"></a>Kurz: konfigurace sítě pro Azure Stack Edge pomocí GPU

V tomto kurzu se dozvíte, jak nakonfigurovat síť pro zařízení Azure Stack Edge pomocí integrovaného GPU pomocí místního webového uživatelského rozhraní.

Dokončení procesu připojení může trvat přibližně 20 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Konfigurace sítě
> * Povolit výpočetní síť
> * Konfigurace webového proxy serveru


## <a name="prerequisites"></a>Požadavky

Před konfigurací a nastavením Azure Stack hraničního zařízení pomocí GPU se ujistěte, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
* Připojili jste se k místnímu webovému uživatelskému rozhraní zařízení, jak je popsáno v části [připojení k Azure Stack Edge](azure-stack-edge-gpu-deploy-connect.md) .


## <a name="configure-network"></a>Konfigurace sítě

Stránka **Začínáme** zobrazuje různá nastavení, která jsou nutná ke konfiguraci a registraci fyzického zařízení ve službě Azure Stack Edge. 

Pomocí těchto kroků nakonfigurujete síť pro vaše zařízení.

1. V místním webovém uživatelském rozhraní zařízení, **navštivte stránku Začínáme** . 

2. Na dlaždici **síť** vyberte **Konfigurovat**.  
    
    ![Dlaždice nastavení sítě místního webového uživatelského rozhraní](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)

    Na fyzickém zařízení existuje šest síťových rozhraní. PORTY 1 a 2 jsou síťová rozhraní s rychlostí 1 GB/s. PORT 3, PORT 4, PORT 5 a PORT 6 jsou všechna síťová rozhraní s 25 GB/s, která mohou sloužit také jako síťová rozhraní s rychlostí 10 GB/s. PORT 1 je automaticky nakonfigurován jako port jen pro správu a PORT 2 na PORT 6 jsou všechny datové porty. Pro nové zařízení je stránka **nastavení sítě** uvedená níže.
    
    ![Stránka místní webové uživatelské rozhraní "nastavení sítě"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2a.png)


   
3. Chcete-li změnit nastavení sítě, vyberte port a v pravém podokně, které se zobrazí, upravte IP adresu, podsíť, bránu, primární DNS a sekundární DNS. 

    - Pokud vyberete port 1, uvidíte, že je předem nakonfigurovaný jako statický. 

        ![Místní webové uživatelské rozhraní "port 1 – nastavení sítě"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-3.png)

    - Pokud vyberete port 2, port 3, port 4 nebo port 5, všechny tyto porty budou ve výchozím nastavení nakonfigurovány jako DHCP.

        ![Místní webové uživatelské rozhraní "port 3 nastavení sítě"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-4.png)

    Při konfiguraci nastavení sítě Pamatujte na toto:

   * Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. Automaticky se přiřadí IP adresa, podsíť, brána a DNS.
   * Pokud není protokol DHCP povolený, můžete v případě potřeby přiřadit statické IP adresy.
   * Síťové rozhraní můžete nakonfigurovat jako IPv4.
   * V rozhraních s 25 GB/s můžete nastavit režim RDMA (Remote Direct Access Memory) na iWarp nebo RoCE (RDMA přes sblíženou síť Ethernet). Pokud jsou nízké latence zásadním požadavkem a škálovatelností není obavou, použijte RoCE. Když je latence klíčovým požadavkem, ale lepším využitím a škálovatelností jsou i vysoké priority, iWARP je nejlepší kandidát.
   * Sériové číslo pro libovolný port odpovídá sériovému číslu uzlu.

    Po nakonfigurování sítě zařízení se stránka aktualizuje, jak je uvedeno níže.

    ![Stránka místní webové uživatelské rozhraní "nastavení sítě"](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-2.png)


     >[!NOTE]
     >
     > * Doporučujeme, abyste místní IP adresu síťového rozhraní nepnuli z static na DCHP, pokud nemáte jinou IP adresu pro připojení k zařízení. Pokud používáte jedno síťové rozhraní a přepnete na protokol DHCP, tak nemusíte určit adresu DHCP. Pokud chcete přejít na adresu DHCP, počkejte na to, až se zařízení aktivuje se službou, a pak změňte. Potom můžete zobrazit IP adresy všech adaptérů ve **vlastnostech zařízení** v Azure Portal pro vaši službu.


    Po nakonfigurování a použití nastavení sítě se vraťte na **začátek**.

## <a name="enable-compute-network"></a>Povolit výpočetní síť

Pomocí těchto kroků povolíte výpočetní prostředky a nakonfigurujete výpočetní síť.

1. Přejít na stránku **Začínáme** v místním webovém uživatelském rozhraní vašeho zařízení. Na dlaždici **síť** vyberte **výpočetní síť**.  

    ![Stránka COMPUTE v místním uživatelském rozhraní](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-1.png)

2. Na stránce **COMPUTE** vyberte síťové rozhraní, které chcete povolit pro výpočetní výkon. 

    ![Stránka COMPUTE v místním uživatelském rozhraní](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)

2. V dialogovém okně **nastavení sítě** vyberte **Povolit**. Pokud povolíte výpočetní prostředky, na zařízení se na tomto síťovém rozhraní vytvoří virtuální přepínač. Virtuální přepínač se používá pro výpočetní infrastrukturu na zařízení. 
    
3. Přiřaďte **IP adresy uzlu Kubernetes**. Tyto statické IP adresy jsou pro výpočetní virtuální počítač. 

    U *n*-Node zařízení se pro výpočetní virtuální počítač, který používá počáteční a KONCOVá IP adresa, nabízí souvislý rozsah minimálně *n + 1* IPv4 adres (nebo více). Daná Azure Stack Edge je zařízení s jedním uzlem, je k dispozici minimálně 2 souvislé adresy IPv4. 

4. Přiřaďte **IP adresy externích služeb Kubernetes**. Toto jsou také IP adresy vyrovnávání zatížení. Tyto souvislé IP adresy jsou pro služby, které chcete zveřejnit mimo cluster Kubernetes, a určete rozsah statických IP adres v závislosti na počtu zveřejněných služeb. 
    
    > [!IMPORTANT]
    > Důrazně doporučujeme zadat minimálně jednu IP adresu pro službu Azure Stack Edge hub pro přístup k výpočetním modulům. Volitelně můžete zadat další IP adresy pro jiné služby nebo moduly IoT Edge (1 na službu/modul), ke kterým je potřeba mít pøístup z vnějšku clusteru. IP adresy služby se dají aktualizovat později. 
    
5. Vyberte **Použít**.

    ![Stránka COMPUTE v místním uživatelském rozhraní](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-3.png)


## <a name="configure-web-proxy"></a>Konfigurace webového proxy serveru

Toto je volitelná konfigurace.

> [!IMPORTANT]
> * Pokud povolíte výpočetní prostředí a použijete IoT Edge modul na zařízení Azure Stack Edge, doporučujeme, abyste nastavili ověřování webového proxy serveru jako **žádné**. Protokol NTLM není podporován.
>* Soubory automatické konfigurace proxy serveru (PAC) nejsou podporovány. Soubor PAC definuje způsob, jakým můžou webové prohlížeče a další uživatelské agenti automaticky zvolit odpovídající proxy server (přístupovou metodu) pro načtení dané adresy URL. Proxy servery, které se pokoušejí zachytit a načíst veškerý provoz (pak se znovu podepisuje vše s vlastní certifikací) nejsou kompatibilní, protože certifikát proxy serveru není důvěryhodný. Obvykle transparentní proxy servery dobře fungují s Azure Stack Edge. Netransparentní webové proxy servery nejsou podporovány.

1. Přejít na stránku **Začínáme** v místním webovém uživatelském rozhraní vašeho zařízení.
2. Na dlaždici **síť** nakonfigurujte nastavení webového proxy server. I když je konfigurace webového proxy serveru volitelná, pokud používáte webový proxy server, můžete ho nakonfigurovat jenom na této stránce.

   ![Stránka nastavení webového proxy serveru v místním webovém prostředí](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

3. Na stránce **nastavení webového proxy serveru** proveďte následující kroky:

    1. Do pole **Adresa URL webového proxy serveru** zadejte adresu URL v tomto formátu: `http://host-IP address or FQDN:Port number` . Adresy URL protokolu HTTPS nejsou podporovány.

    2. V části **ověřování**vyberte **žádné** nebo **NTLM**. Pokud povolíte výpočetní prostředí a použijete IoT Edge modul na zařízení Azure Stack Edge, doporučujeme, abyste nastavili ověřování webového proxy serveru na **žádné**. **Protokol NTLM** není podporován.

    3. Pokud používáte ověřování, zadejte uživatelské jméno a heslo.

    4. Pokud chcete ověřit a použít nakonfigurovaná nastavení webového proxy serveru, vyberte **použít**.
    
   ![Stránka nastavení webového proxy serveru v místním webovém prostředí](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

4. Po použití nastavení se **vraťte na začátek**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Konfigurace sítě
> * Povolit výpočetní síť
> * Konfigurace webového proxy serveru


Informace o tom, jak nastavit Azure Stack hraniční zařízení, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace nastavení zařízení](./azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
