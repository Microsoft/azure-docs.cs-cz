---
title: Kurz konfigurace nastavení sítě pro zařízení Azure Stackového Mini R v Azure Portal
description: V tomto kurzu nasadíte Azure Stack hraniční Mini R, abyste mohli nakonfigurovat síť, výpočetní síť a nastavení webového proxy serveru pro vaše fyzické zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
ms.openlocfilehash: 34a11679626653afd04b0cd17c77188cbc995308
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061719"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Kurz: konfigurace sítě pro Azure Stack hraniční Mini R

V tomto kurzu se dozvíte, jak nakonfigurovat síť pro Azure Stack hraničních zařízení R pomocí připojení GPU pomocí místního webového uživatelského rozhraní.

Dokončení procesu připojení může trvat přibližně 20 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Konfigurace sítě
> * Povolit výpočetní síť
> * Konfigurace webového proxy serveru


## <a name="prerequisites"></a>Požadavky

Předtím, než nakonfigurujete a nastavíte Azure Stack hraničních zařízení v jazyce R, ujistěte se, že:

* Nainstalovali jste fyzické zařízení, jak je podrobně popsáno v části [Install Azure Stack Edge Mini R](azure-stack-edge-gpu-deploy-install.md).
* Připojili jste se k místnímu webovému uživatelskému rozhraní zařízení, jak je popsáno v části [připojení k Azure Stack hraniční Mini R](azure-stack-edge-mini-r-deploy-connect.md) .


## <a name="configure-network"></a>Konfigurace sítě

Stránka **Začínáme** zobrazuje různá nastavení, která jsou nutná ke konfiguraci a registraci fyzického zařízení ve službě Azure Stack Edge. 

Pomocí těchto kroků nakonfigurujete síť pro vaše zařízení.

1. V místním webovém uživatelském rozhraní zařízení, **navštivte stránku Začínáme** . 

2. Pokud je potřeba aktualizace nulového dne, můžete to udělat tak, že nakonfigurujete datový port s kabelovým připojením. Další informace o tom, jak nastavit kabelové připojení pro toto zařízení, najdete v tématu věnovaném [kabelu zařízení](azure-stack-edge-mini-r-deploy-install.md#cable-the-device). Po dokončení aktualizace můžete kabelové připojení odebrat.

3. Vytvořte certifikáty pro Wi-Fi a podpisový řetězec. Podpisový řetězec i certifikáty Wi-Fi musí mít formát DER s příponou souboru *. cer* . Pokyny najdete v tématu [Vytvoření certifikátů](azure-stack-edge-gpu-manage-certificates.md).

4. V místním webovém uživatelském rozhraní si přečtěte web **Začínáme**. Na dlaždici **zabezpečení** vyberte **certifikáty** a pak vyberte **Konfigurovat**. 

    [![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. Vyberte **+ Přidat certifikát**. 
    
        [![Místní webové uživatelské rozhraní "Stránka 1" certifikát](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. Nahrajte podpisový řetězec a vyberte **použít**.

        ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Opakujte postup s certifikátem Wi-Fi. 

        ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. Nové certifikáty by se měly zobrazit na stránce **certifikáty** . 
    
        [![Místní uživatelské rozhraní webu "certifikáty" – Stránka 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. Vraťte se na **začátek**.

3. Na dlaždici **síť** vyberte **Konfigurovat**.  
    
    Na fyzickém zařízení existuje pět síťových rozhraní. Porty 1 a 2 představují síťová rozhraní s rychlostí 1 GB/s. PORTY 3 a 4 jsou všechna síťová rozhraní s rychlostí 10 GB/s. Pátý port je Wi-Fi port. 

    [![Místní webové uživatelské rozhraní "Stránka 1" nastavení sítě](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Vyberte port Wi-Fi a nakonfigurujte nastavení portu. 
    
    > [!IMPORTANT]
    > Důrazně doporučujeme nakonfigurovat statickou IP adresu pro port Wi-Fi.  

    ![Místní webové uživatelské rozhraní "nastavení sítě" – Stránka 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    Po použití nastavení Wi-Fi portu se **Síťová** stránka aktualizuje.

    ![Místní webové uživatelské rozhraní "Stránka 3" – nastavení sítě](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. Vyberte **Přidat profil Wi-Fi** a nahrajte profil Wi-Fi. 

    ![Místní webové uživatelské rozhraní "port nastavení sítě Wi-Fi" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    Profil bezdrátové sítě obsahuje identifikátor SSID (název sítě), heslo a informace o zabezpečení, aby se mohl připojit k bezdrátové síti. Můžete získat profil Wi-Fi pro vaše prostředí od správce sítě.

    ![Místní webové uživatelské rozhraní "port nastavení sítě Wi-Fi" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    Po přidání profilu se seznam profilů Wi-Fi aktualizuje tak, aby odrážel nový profil. Profil by měl zobrazovat **stav připojení** jako **Odpojeno**. 

    ![Místní webové uživatelské rozhraní "port nastavení sítě Wi-Fi" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. Po úspěšném načtení profilu bezdrátové sítě se připojte k tomuto profilu. Vyberte **připojit k profilu Wi-Fi**. 

    ![Místní webové uživatelské rozhraní "port Wi-Fi nastavení sítě" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Vyberte profil Wi-Fi, který jste přidali v předchozím kroku, a vyberte **použít**. 

    ![Místní webové uživatelské rozhraní "port Wi-Fi nastavení sítě" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    **Stav připojení** by měl být aktualizován na **připojeno**. Síla signálu se aktualizuje, aby označovala kvalitu signálu. 

    ![Místní webové uživatelské rozhraní "port Wi-Fi nastavení sítě" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Pro přenos velkých objemů dat doporučujeme místo bezdrátové sítě použít kabelové připojení. 

6. Odpojte PORT 1 na zařízení od přenosného počítače. 

7. Při konfiguraci nastavení sítě mějte na paměti následující:

   - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. IP adresa, podsíť, brána a DNS se přiřadí automaticky.
   - Pokud není protokol DHCP povolený, můžete v případě potřeby přiřadit statické IP adresy.
   - Síťové rozhraní můžete nakonfigurovat jako IPv4.
   - Seskupování síťových adaptérů (NIC) nebo agregace propojení se u Azure Stack Edge nepodporují.
   - Sériové číslo pro libovolný port odpovídá sériovému číslu uzlu. Pro zařízení řady n-Series se zobrazí pouze jedno sériové číslo.

     >[!NOTE] 
     > Doporučujeme neměnit místní IP adresu síťového rozhraní ze statické adresy na adresu DHCP, pokud nemáte další IP adresu pro připojení k zařízení. Pokud používáte jedno síťové rozhraní a přepnete na adresu DHCP, adresu DHCP nebude možné určit. Pokud chcete změnit adresu na adresu DHCP, počkejte na dokončení registrace zařízení ve službě a pak proveďte změnu. Potom můžete zobrazit IP adresy všech adaptérů ve **vlastnostech zařízení** v Azure Portal pro vaši službu.

Po nakonfigurování a použití nastavení sítě vyberte **Další: COMPUTE** a nakonfigurujte výpočetní síť.

## <a name="enable-compute-network"></a>Povolit výpočetní síť

Pomocí těchto kroků povolíte výpočetní prostředky a nakonfigurujete výpočetní síť. 


1. Na stránce **COMPUTE** vyberte síťové rozhraní, které chcete povolit pro výpočetní výkon. 

    ![Stránka COMPUTE v místním uživatelském rozhraní 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. V dialogovém okně **nastavení sítě** vyberte **Povolit**. Pokud povolíte výpočetní prostředky, na zařízení se na tomto síťovém rozhraní vytvoří virtuální přepínač. Virtuální přepínač se používá pro výpočetní infrastrukturu na zařízení. 
    
1. Přiřaďte **IP adresy uzlu Kubernetes**. Tyto statické IP adresy jsou pro výpočetní virtuální počítač.  

    U *n*-Node zařízení se pro výpočetní virtuální počítač, který používá počáteční a KONCOVá IP adresa, nabízí souvislý rozsah minimálně *n + 1* IPv4 adres (nebo více). Daná Azure Stack Edge je zařízení s jedním uzlem, je k dispozici minimálně 2 souvislé adresy IPv4.

    > [!IMPORTANT]
    > Kubernetes na Azure Stack Edge používá podsíť 172.27.0.0/16 pro podsíť pod a 172.28.0.0/16 pro službu. Ujistěte se, že se nepoužívají ve vaší síti. Pokud se tyto podsítě už ve vaší síti používají, můžete tyto podsítě změnit spuštěním `Set-HcsKubeClusterNetworkInfo` rutiny z rozhraní PowerShellu zařízení. Další informace najdete v tématu [Změna Kubernetes pod a podsítí služby](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. Přiřaďte **IP adresy externích služeb Kubernetes**. Jedná se také o IP adresy vyrovnávání zatížení. Tyto souvislé IP adresy jsou určené pro služby, které chcete zveřejnit mimo cluster Kubernetes, a určete rozsah statických IP adres v závislosti na počtu zveřejněných služeb. 
    
    > [!IMPORTANT]
    > Důrazně doporučujeme zadat minimálně jednu IP adresu pro službu Azure Stack Edge Mini R hub pro přístup k výpočetním modulům. Volitelně můžete zadat další IP adresy pro jiné služby nebo moduly IoT Edge (1 na službu/modul), ke kterým je potřeba mít pøístup z vnějšku clusteru. IP adresy služby se dají aktualizovat později. 
    
1. Vyberte **Použít**.

    ![Stránka COMPUTE v místním uživatelském rozhraní 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. Použití konfigurace trvá několik minut a může být nutné aktualizovat prohlížeč. Můžete vidět, že zadaný port je povolen pro výpočetní výkon. 
 
    ![Stránka COMPUTE v místním uživatelském rozhraní 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Vyberte **Další: webový proxy server** pro konfiguraci webového proxy serveru.  

  
## <a name="configure-web-proxy"></a>Konfigurace webového proxy serveru

Toto je volitelná konfigurace.

> [!IMPORTANT]
> * Pokud povolíte výpočetní prostředí a použijete IoT Edge modul na Azure Stack hraničním zařízení R, doporučujeme, abyste nastavili ověřování webového proxy serveru jako **žádné**. Protokol NTLM se nepodporuje.
>* Soubory automatické konfigurace proxy serveru (PAC) nejsou podporovány. Soubor PAC definuje způsob, jakým můžou webové prohlížeče a další uživatelské agenti automaticky zvolit odpovídající proxy server (přístupovou metodu) pro načtení dané adresy URL. Proxy servery, které se pokoušejí zachytit a načíst veškerý provoz (pak se znovu podepisuje vše s vlastní certifikací) nejsou kompatibilní, protože certifikát proxy serveru není důvěryhodný. Obvykle transparentní proxy servery dobře fungují s Azure Stackou Minikou R. netransparentní webové proxy servery nejsou podporovány.


1. Na stránce **nastavení webového proxy serveru** proveďte následující kroky:

    1. Do pole **Adresa URL webového proxy serveru** zadejte adresu URL v tomto formátu: `http://host-IP address or FQDN:Port number` . Adresy URL protokolu HTTPS se nepodporují.

    2. V části **Ověřování** vyberte **Žádné** nebo **NTLM**. Pokud povolíte výpočetní prostředí a použijete IoT Edge modul na Azure Stack hraničním zařízení R, doporučujeme, abyste nastavili ověřování webového proxy serveru na **žádné**. **Protokol NTLM** není podporován.

    3. Pokud používáte ověřování, zadejte uživatelské jméno a heslo.

    4. Pokud chcete ověřit a použít nakonfigurovaná nastavení webového proxy serveru, vyberte **použít**.
    
   ![Stránka nastavení webového proxy serveru v místním webovém prostředí](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. Po použití nastavení vyberte **Další: zařízení**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Požadavky
> * Konfigurace sítě
> * Povolit výpočetní síť
> * Konfigurace webového proxy serveru


Informace o tom, jak nastavit Azure Stack hraničních zařízení v R, najdete v tématech:

> [!div class="nextstepaction"]
> [Konfigurace nastavení zařízení](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
