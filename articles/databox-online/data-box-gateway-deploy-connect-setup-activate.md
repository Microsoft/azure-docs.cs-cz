---
title: Připojení, konfigurace a aktivace Azure Data Box Gateway v Azure Portal
description: Třetí kurz nasazení Data Box Gateway vás provede pokyny k připojení, nastavení a aktivaci virtuálního zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91741954"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Kurz: připojení, nastavení, aktivace Azure Data Box Gateway

## <a name="introduction"></a>Úvod

V tomto kurzu se dozvíte, jak se připojit k zařízení Data Box Gateway pomocí místního webového uživatelského rozhraní a jak je nastavit a aktivovat. 

Dokončení procesu instalace a aktivace může trvat přibližně 10 minut. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k virtuálnímu zařízení
> * Nastavení a aktivace virtuálního zařízení

## <a name="prerequisites"></a>Předpoklady

Než nakonfigurujete a nastavíte Data Box Gateway, ujistěte se, že:

* Zřídili jste virtuální zařízení a získali připojenou adresu URL, jak je popsáno v části [zřízení data box Gateway v Hyper-v](data-box-gateway-deploy-provision-hyperv.md) , nebo [zřízení data box Gateway ve VMware](data-box-gateway-deploy-provision-vmware.md).
* Máte aktivační klíč ze služby Data Box Gateway, kterou jste vytvořili pro správu Data Box Gateway zařízení. Další informace najdete na webu [Příprava na nasazení Azure Data box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského rozhraní 

1. Otevřete okno prohlížeče a přejděte k místnímu webovému uživatelskému rozhraní zařízení v:
   
   https: \/ /IP-address-of-Network-Interface
   
   Použijte adresu URL připojení, kterou jste si poznamenali v předchozím kurzu. Zobrazí se vám chyba nebo upozornění indikující problém s certifikátem zabezpečení webu.

2. Vyberte možnost **pokračovat na tuto webovou stránku**. Tyto kroky se můžou lišit v závislosti na použitém prohlížeči.
   
    ![Chybová zpráva certifikátu zabezpečení webu](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Přihlaste se k webovému uživatelskému rozhraní virtuálního zařízení. Výchozí heslo je *Heslo1*. 
   
    ![Přihlaste se k místnímu webovému uživatelskému rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. V příkazovém řádku změňte heslo zařízení. Nové heslo musí obsahovat 8 až 16 znaků. Musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky.

    ![Změna hesla zařízení](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Nyní jste na **řídicím panelu** zařízení.

## <a name="set-up-and-activate-the-virtual-device"></a>Nastavení a aktivace virtuálního zařízení
 
Řídicí panel zobrazuje různá nastavení, která jsou nutná ke konfiguraci a registraci virtuálního zařízení ve službě Data Box Gateway. **Název zařízení**, **nastavení sítě**, nastavení **webového proxy serveru**a **Nastavení času** jsou volitelné. Jediná požadovaná nastavení jsou **Nastavení cloudu**.
   
![Stránka místního webového uživatelského rozhraní "řídicí panel"](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. V levém podokně vyberte **název zařízení**a zadejte popisný název zařízení. Popisný název musí mít délku 1 až 15 znaků a obsahovat písmena, číslice a spojovníky. 

    ![Stránka místní webové uživatelské rozhraní "název zařízení"](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. Volitelné V levém podokně vyberte **nastavení sítě** a pak nakonfigurujte nastavení. Na virtuálním zařízení se zobrazí aspoň jedno síťové rozhraní a další v závislosti na tom, kolik jste nakonfigurovali v podkladovém virtuálním počítači. Stránka **nastavení sítě** pro virtuální zařízení s jedním síťovým rozhraním je povolena, jak je uvedeno níže.
    
    ![Stránka místní webové uživatelské rozhraní "nastavení sítě"](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Při konfiguraci nastavení sítě Pamatujte na toto:

    - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. IP adresa, podsíť, brána a DNS se přiřadí automaticky.
    - Pokud není protokol DHCP povolený, můžete v případě potřeby přiřadit statické IP adresy.
    - Síťové rozhraní můžete nakonfigurovat jako IPv4.

     >[!NOTE] 
     > Doporučujeme, abyste místní IP adresu síťového rozhraní nepnuli z hodnoty static na DHCP, pokud nemáte jinou IP adresu pro připojení k zařízení. Pokud používáte jedno síťové rozhraní a přepnete na adresu DHCP, adresu DHCP nebude možné určit. Pokud chcete změnit adresu na adresu DHCP, počkejte na dokončení registrace zařízení ve službě a pak proveďte změnu. Potom můžete zobrazit IP adresy všech adaptérů ve **vlastnostech zařízení** v Azure Portal pro vaši službu.

3. Volitelně konfigurujte proxy server. I když je konfigurace webového proxy serveru volitelná, pokud používáte webový proxy server, můžete ho nakonfigurovat jenom na této stránce.
   
   ![Stránka nastavení webového proxy serveru v místním webovém prostředí](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na stránce **webový proxy server** proveďte následující kroky:
   
   1. Do pole **Adresa URL webového proxy serveru** zadejte adresu URL v tomto formátu: `http://&lt;host-IP address or FQDN&gt;:Port number` . Adresy URL protokolu HTTPS se nepodporují.
   2. V části **ověřování**vyberte **žádné** nebo **NTLM**.
   3. Pokud používáte ověřování, zadejte **uživatelské jméno** a **heslo**.
   4. Pokud chcete ověřit a použít nakonfigurovaná nastavení webového proxy serveru, vyberte **použít**.

   > [!NOTE]
   > Soubory automatické konfigurace proxy serveru (PAC) nejsou podporovány. Soubor PAC definuje způsob, jakým můžou webové prohlížeče a další uživatelské agenti automaticky zvolit odpovídající proxy server (přístupovou metodu) pro načtení dané adresy URL.
   > Proxy servery, které se pokoušejí zachytit a číst veškerý provoz (a následně vše znovu podepsat vlastním certifikátem), nejsou kompatibilní, protože certifikáty takových proxy serverů nejsou důvěryhodné.
   > Obvykle transparentní proxy servery dobře fungují s Azure Data Box Gateway.

4. Volitelné V levém podokně vyberte **Nastavení času**a pak nakonfigurujte časové pásmo a primární a sekundární servery NTP pro vaše zařízení. 

    Jsou vyžadovány servery NTP, protože vaše zařízení musí synchronizovat čas, aby se mohl ověřit u vašich poskytovatelů cloudových služeb.
    
    ![Stránka nastavení času místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na stránce **Nastavení času** proveďte následující:
    
    1. V rozevíracím seznamu **časové pásmo** vyberte časové pásmo, které odpovídá geografickému umístění, ve kterém je zařízení nasazené.
        Výchozím časovým pásmem pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.

    2. Zadejte **primární server NTP** pro vaše zařízení nebo přijměte výchozí hodnotu `time.windows.com` .   
        Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.

    3. Případně můžete v poli **sekundární server NTP** zadat sekundární server pro vaše zařízení.

    4. Chcete-li ověřit a použít nakonfigurované nastavení času, vyberte **použít**.

6. V levém podokně vyberte **Nastavení cloudu**a potom aktivujte zařízení ve službě Data Box Gateway v Azure Portal.
    
    1. Do pole **aktivační klíč** zadejte **aktivační** klíč, který jste získali v [části získání aktivačního klíče](data-box-gateway-deploy-prep.md#get-the-activation-key) pro data box Gateway.

    2. Vyberte **aktivovat**.
       
         ![Stránka nastavení cloudu místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Zařízení je aktivované a důležité aktualizace, pokud jsou k dispozici, se automaticky aplikují. K tomuto účelu se zobrazí oznámení. Sledujte průběh aktualizace prostřednictvím Azure Portal.

        ![Místní webové uživatelské rozhraní "nastavení cloudu" Stránka 2](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Dialog má také obnovovací klíč, který byste měli zkopírovat a uložit v bezpečném umístění. Tento klíč se používá k obnovení dat v případě, že se zařízení nedá spustit.**


    4. Možná budete muset počkat několik minut, než se aktualizace úspěšně dokončí. Až se aktualizace dokončí, přihlaste se k zařízení. Stránka **Nastavení cloudu** se aktualizuje, aby označovala, že se zařízení úspěšně aktivovalo.

        ![Stránka nastavení cloudu místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Instalace zařízení je dokončená. V zařízení teď můžete přidat sdílené složky.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Připojení k virtuálnímu zařízení
> * Nastavení a aktivace virtuálního zařízení

Informace o tom, jak přenést data s vaším Data Box Gateway, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přenos dat pomocí data box Gateway](./data-box-gateway-deploy-add-shares.md).
