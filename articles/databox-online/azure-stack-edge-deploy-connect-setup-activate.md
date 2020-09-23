---
title: Kurz pro připojení, konfiguraci, aktivaci Azure Stack Edge pro zařízení v Azure Portal | Microsoft Docs
description: Kurz nasazení Azure Stack Edge pro vám dává pokyn k připojení, nastavení a aktivaci fyzického zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 5a89125edf6082bbbcbaf139a648c9edc2debf81
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894251"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge-pro"></a>Kurz: připojení, nastavení a aktivace Azure Stack Edge pro 

V tomto kurzu se dozvíte, jak se můžete připojit k zařízení Azure Stack Edge pro, nastavit a aktivovat pomocí místního webového uživatelského rozhraní.

Dokončení procesu instalace a aktivace může trvat přibližně 20 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Připojení k fyzickému zařízení
> * Nastavení a aktivace fyzického zařízení

## <a name="prerequisites"></a>Požadavky

Před konfigurací a nastavením zařízení Azure Stack Edge pro se ujistěte, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro](azure-stack-edge-deploy-install.md).
* Máte aktivační klíč ze služby Azure Stack Edge, kterou jste vytvořili pro správu zařízení Azure Stack Edge pro. Další informace najdete na webu [Příprava nasazení Azure Stack Edge pro](azure-stack-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského rozhraní

1. Nakonfigurujte adaptér Ethernet v počítači pro připojení k zařízení Azure Stack Edge pro se statickou IP adresou 192.168.100.5 a podsítí 255.255.255.0.

2. Připojte počítač k portu 1 na svém zařízení. Pomocí následující ilustrace Identifikujte PORT 1 na vašem zařízení.

    ![Propojovací rozhraní zařízení se zapojenou kabeláží](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

3. Otevřete okno prohlížeče a přejděte do místního webového uživatelského rozhraní zařízení na adrese `https://192.168.100.10`.  
    Po zapnutí zařízení může tato akce několik minut trvat.

    Zobrazí se vám chyba nebo upozornění indikující problém s certifikátem zabezpečení webu.
   
    ![Chybová zpráva certifikátu zabezpečení webu](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Vyberte možnost **pokračovat na tuto webovou stránku**.  
    Tyto kroky se můžou lišit v závislosti na použitém prohlížeči.

5. Přihlaste se k webovému uživatelskému rozhraní vašeho zařízení. Výchozí heslo je *Heslo1*. 
   
    ![Přihlašovací stránka pro zařízení Azure Stack Edge pro](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. V příkazovém řádku změňte heslo správce zařízení.  
    Nové heslo musí obsahovat 8 až 16 znaků. Musí obsahovat tři z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky.

Nyní jste na řídicím panelu zařízení.

## <a name="set-up-and-activate-the-physical-device"></a>Nastavení a aktivace fyzického zařízení
 
Řídicí panel zobrazuje různá nastavení, která jsou nutná ke konfiguraci a registraci fyzického zařízení ve službě Azure Stack Edge. **Název zařízení**, **nastavení sítě**, nastavení **webového proxy serveru**a **Nastavení času** jsou volitelné. Jediná požadovaná nastavení jsou **Nastavení cloudu**.
   
![Stránka místního webového uživatelského rozhraní "řídicí panel"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. V levém podokně vyberte **název zařízení**a zadejte popisný název zařízení.  
    Popisný název musí být v rozmezí od 1 do 15 znaků a obsahovat písmena, číslice a spojovníky.

    ![Stránka místní webové uživatelské rozhraní "název zařízení"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. Volitelné V levém podokně vyberte **nastavení sítě** a pak nakonfigurujte nastavení.  
    Na fyzickém zařízení je šest síťových rozhraní. Porty 1 a 2 představují síťová rozhraní s rychlostí 1 GB/s. PORT 3, PORT 4, PORT 5 a PORT 6 jsou všechna síťová rozhraní s 25 GB/s, která mohou sloužit také jako síťová rozhraní s rychlostí 10 GB/s. PORT 1 je automaticky nakonfigurován jako port jen pro správu a PORT 2 na PORT 6 jsou všechny datové porty. Stránka **nastavení sítě** je uvedená níže.
    
    ![Stránka místní webové uživatelské rozhraní "nastavení sítě"](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Při konfiguraci nastavení sítě mějte na paměti následující:

   - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. IP adresa, podsíť, brána a DNS se přiřadí automaticky.
   - Pokud není protokol DHCP povolený, můžete v případě potřeby přiřadit statické IP adresy.
   - Síťové rozhraní můžete nakonfigurovat jako IPv4.

     >[!NOTE] 
     > Doporučujeme neměnit místní IP adresu síťového rozhraní ze statické adresy na adresu DHCP, pokud nemáte další IP adresu pro připojení k zařízení. Pokud používáte jedno síťové rozhraní a přepnete na adresu DHCP, adresu DHCP nebude možné určit. Pokud chcete změnit adresu na adresu DHCP, počkejte na dokončení registrace zařízení ve službě a pak proveďte změnu. Potom můžete zobrazit IP adresy všech adaptérů ve **vlastnostech zařízení** v Azure Portal pro vaši službu.

3. Volitelné V levém podokně vyberte **nastavení webového proxy serveru**a pak nakonfigurujte svůj web proxy server. I když je konfigurace webového proxy serveru volitelná, pokud používáte webový proxy server, můžete ho nakonfigurovat jenom na této stránce.
   
   ![Stránka nastavení webového proxy serveru v místním webovém prostředí](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na stránce **nastavení webového proxy serveru** postupujte takto:
   
   a. Do pole **Adresa URL webového proxy serveru** zadejte adresu URL v tomto formátu: `http://host-IP address or FQDN:Port number` . Adresy URL protokolu HTTPS se nepodporují.

   b. V části **ověřování**vyberte **žádné** nebo **NTLM**. Pokud povolíte výpočetní prostředí a použijete modul IoT Edge na zařízení Azure Stack Edge pro, doporučujeme, abyste nastavili ověřování webového proxy serveru na **žádné**. **Protokol NTLM** není podporován.

   c. Pokud používáte ověřování, zadejte uživatelské jméno a heslo.

   d. Pokud chcete ověřit a použít nakonfigurovaná nastavení webového proxy serveru, vyberte **použít nastavení**.

   > [!NOTE]
   > Soubory automatické konfigurace proxy serveru (PAC) nejsou podporovány. Soubor PAC definuje způsob, jakým můžou webové prohlížeče a další uživatelské agenti automaticky zvolit odpovídající proxy server (přístupovou metodu) pro načtení dané adresy URL.
   > Proxy servery, které se pokoušejí zachytit a číst veškerý provoz (a následně vše znovu podepsat vlastním certifikátem), nejsou kompatibilní, protože certifikáty takových proxy serverů nejsou důvěryhodné.
   > Obvykle transparentní proxy servery dobře fungují s Azure Stack Edge pro.

4. Volitelné V levém podokně vyberte **Nastavení času**a pak nakonfigurujte časové pásmo a primární a sekundární servery NTP pro vaše zařízení.  
    Jsou vyžadovány servery NTP, protože vaše zařízení musí synchronizovat čas, aby se mohl ověřit u vašich poskytovatelů cloudových služeb.
       
    Na stránce **Nastavení času** proveďte následující:
    
    1. V rozevíracím seznamu **časové pásmo** vyberte časové pásmo, které odpovídá geografickému umístění, ve kterém se zařízení nasazuje.
        Výchozím časovým pásmem pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.

    2. V poli **primární server NTP** zadejte primární server pro vaše zařízení nebo přijměte výchozí hodnotu Time.Windows.com.  
        Ujistěte se, že vaše síť umožňuje předávat data NTP z vašeho datacentra na Internet.

    3. Případně můžete v poli **sekundární server NTP** zadat sekundární server pro vaše zařízení.

    4. Pokud chcete ověřit a použít nakonfigurované nastavení času, vyberte **použít nastavení**.

        ![Stránka nastavení času místního webového uživatelského rozhraní](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. Volitelné V levém podokně vyberte **Nastavení úložiště** a nakonfigurujte odolnost úložiště v zařízení. Tato funkce je aktuálně ve verzi Preview. Ve výchozím nastavení není úložiště v zařízení odolné a při selhání datového disku v zařízení dojde ke ztrátě dat. Pokud povolíte možnost odolnosti, dojde k překonfigurování úložiště v zařízení a zařízení bude moci odolat selhání jednoho datového disku bez ztráty dat. Pokud úložiště nakonfigurujete jako odolné, sníží se použitelná kapacita vašeho zařízení.

    > [!IMPORTANT] 
    > Odolnost proti chybám se dá nakonfigurovat jenom před aktivací zařízení. 

    ![Stránka nastavení úložiště místního webového uživatelského rozhraní](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. V levém podokně vyberte **Nastavení cloudu**a potom v Azure Portal aktivujte zařízení pomocí služby Azure Stack Edge.
    
    1. Do pole **aktivační klíč** zadejte aktivační klíč, který jste získali v [části získání aktivačního klíče](azure-stack-edge-deploy-prep.md#get-the-activation-key) pro Azure Stack Edge pro.
    2. Vyberte **Použít**.
       
        ![Stránka nastavení cloudu místního webového uživatelského rozhraní](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Nejdřív se zařízení aktivuje. Zařízení pak vyhledá důležité aktualizace a pokud jsou k dispozici, aktualizace se automaticky aplikují. K tomuto účelu se zobrazí oznámení.

        Dialog má také obnovovací klíč, který byste měli zkopírovat a uložit na bezpečném místě. Tento klíč se používá k obnovení dat v případě, že se zařízení nedá spustit.

        ![Stránka nastavení cloudu místního webového uživatelského rozhraní](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Po úspěšném dokončení aktualizace možná budete muset počkat několik minut. Stránka se aktualizuje, aby označovala, že se zařízení úspěšně aktivovalo.

        ![Stránka nastavení cloudu místního webového uživatelského rozhraní](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Instalace zařízení je dokončená. V zařízení teď můžete přidat sdílené složky.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Připojení k fyzickému zařízení
> * Nastavení a aktivace fyzického zařízení

Informace o tom, jak přenést data pomocí zařízení Azure Stack Edge pro, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přenos dat pomocí Azure Stack Edge pro](./azure-stack-edge-deploy-add-shares.md).
