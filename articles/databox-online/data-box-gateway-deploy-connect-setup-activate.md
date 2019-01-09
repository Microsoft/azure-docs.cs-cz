---
title: Připojte se ke konfiguraci a aktivovat Azure Data Box brány na webu Azure portal | Dokumentace Microsoftu
description: Třetí kurz, abyste mohli nasadit bránu pole dat dává pokyn k připojení a nastavení a aktivaci vašeho virtuálního zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 5e83eb46a4d62c6aaf0862d4bb1aa046c5d64dde
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54120509"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway-preview"></a>Kurz: Připojení, nastavit, aktivovat Azure Data Box Gateway (Preview) 

## <a name="introduction"></a>Úvod

Tento kurz popisuje, jak se připojit k nastavení a aktivaci zařízení Data Box brány pomocí místního webového uživatelského rozhraní. 

Proces instalace a aktivace může trvat přibližně 10 minut na dokončení. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojte se k virtuálnímu zařízení
> * Nastavit a aktivovat virtuálního zařízení

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.


> [!IMPORTANT]
> - Data Box Gateway je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Požadavky

Než začnete konfigurovat a nastavit bránu dat pole, ujistěte se, že:

* Zřízení virtuálního zařízení a získat adresu URL připojených k němu podle popisu v [bránu Data Gateway pole v Hyper-V zřídit](data-box-gateway-deploy-provision-hyperv.md) nebo [zřízení bránu Data Gateway pole v prostředí VMware](data-box-gateway-deploy-provision-vmware.md).
* Máte aktivační klíč ze služby Data Box brány, kterou jste vytvořili pro správu zařízení Data Box brány. Další informace najdete v části [Příprava na nasazení Azure Data Box Gateway](data-box-gateway-deploy-prep.md).

<!--* If this is the second or subsequent virtual device that you are registering with an existing StorSimple Device Manager service, you should have the service data encryption key. This key was generated when the first device was successfully registered with this service. If you have lost this key, see [Get the service data encryption key](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key) for your Data Box Gateway.-->

## <a name="connect-to-the-local-web-ui-setup"></a>Připojte se k nastavení místního webového uživatelského rozhraní 

1. Otevřete okno prohlížeče a připojte se k místního webového uživatelského rozhraní. Zadejte:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Pomocí adresy URL pro připojení, které jste si poznamenali v předchozím kurzu. Můžete zobrazit chyba oznamující, že dojde k problému s certifikátem zabezpečení webu. Klikněte na tlačítko **pokračovat na tuto webovou stránku**. (Tyto kroky může lišit podle prohlížeče použitého.)
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

2. Přihlaste se do webového uživatelského rozhraní vašeho virtuálního zařízení. Výchozí heslo je *Heslo1*. 
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

3. Zobrazí se výzva ke změně hesla správce zařízení. Zadejte nové heslo, který obsahuje délku 8 až 16 znaků. Heslo musí obsahovat 3 z následujících možností: velká písmena, malá písmena, číselné a speciální znaky.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Teď jste na **řídicí panel** vašeho zařízení.

## <a name="set-up-and-activate-the-virtual-device"></a>Nastavit a aktivovat virtuálního zařízení
 
1. Z řídicího panelu můžete přejít na různá nastavení, které jsou potřeba ke konfiguraci a registraci virtuálního zařízení ve službě Data Box brány. **Nastavení sítě**, **nastavení proxy webu**, a **nastavení času** jsou volitelné. Pouze požadované nastavení, musí být **název zařízení** a **nastavení cloudu**.
   
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

2. V **název zařízení** proveďte popisný název zařízení. Popisný název mohou být dlouhé 1 až 15 znaků a může obsahovat písmena, číslice a pomlčky.

    ![](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

3. (Volitelně) konfigurovat vaše **nastavení sítě**. Uvidíte alespoň 1 síťové rozhraní a provádění dalších akcí v závislosti na tom, kolik jste nakonfigurovali v základní virtuální počítač. **Nastavení sítě** stránce pro virtuální zařízení s jedním síťovým rozhraním povoleno je znázorněno níže.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Při konfiguraci nastavení sítě, mějte následující:

    - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. Proto IP adresu, podsíť, brána a DNS jsou automaticky přiřazeny.
    - Pokud není povolený DHCP, můžete přiřadit statické IP adresy v případě potřeby.
    - Síťové rozhraní můžete nakonfigurovat jako pro protokol IPv4.

    >[!NOTE] 
    > Doporučujeme přepnutí místní IP adresu rozhraní netowrk ze statické na server, pokud nemáte jinou IP adresu pro připojení k zařízení. Pokud pomocí některé síťové rozhraní a můžete přepnout na DHCP, by existovat žádný způsob, jak určit adresu ze serveru DHCP. Pokud chcete změnit adresu DHCP, počkat, dokud se zařízení zaregistruje ve službě a potom změňte. Pak můžete zobrazit IP adresy všech adpaters v **vlastnosti zařízení** na webu Azure Portal pro vaši službu.

4. (Volitelně) nakonfigurujte váš webový proxy server. Sice volitelné konfigurace webového proxy serveru, mějte na paměti, že pokud používáte webový proxy server, můžete pouze nakonfigurovat ji sem.
   
   ![](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   V **webového proxy serveru** stránky:
   
   1. Zadat **webová adresa URL proxy serveru** v tomto formátu: *http://&lt;hostitele IP adresu nebo plně kvalifikovaného názvu domény&gt;: číslo portu*. Všimněte si, že nejsou podporovány adresy URL HTTPS.
   2. Zadejte **ověřování** jako **základní** nebo **žádný**.
   3. Pokud používáte ověřování, budete také muset poskytnout **uživatelské jméno** a **heslo**.
   4. Klikněte na tlačítko **Použít**. Bude to ověřit a použít nastavení nakonfigurované webového proxy serveru.

5. (Volitelně) nakonfigurujte nastavení času pro vaše zařízení, jako je časové pásmo a primární a sekundární server NTP. Servery NTP jsou požadovány, protože zařízení musí synchronizovat čas tak, aby ho mohli ověřit poskytovatelé cloudových služeb.
    
    ![](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    V **nastavení času** stránky:
    
    1. Z rozevíracího seznamu, vyberte **časové pásmo** na základě geografického umístění, ve kterém se nasazuje zařízení. Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
    2. Zadejte **primárního serveru NTP** pro vaše zařízení nebo přijměte výchozí hodnotu time.windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
    3. Volitelně můžete zadat **sekundární NTP server** pro vaše zařízení.
    4. Klikněte na tlačítko **Použít**. Bude to ověřit a použít nastavení nakonfigurovaném čase.

6. V **nastavení cloudu** stránce, aktivovat vaše zařízení do služby Data Box brány na webu Azure portal.
    
    1. Zadejte **aktivační kód** , které jste získali [získat aktivační klíč](data-box-gateway-deploy-prep.md#get-the-activation-key) pro bránu dat pole.

    2. Klikněte na tlačítko **aktivovat**. 
       
         ![](./media/data-box-gateway-deploy-connect-setup-activate/image10.png)
    
    3. Budete muset počkat jednu minutu, než se zařízení úspěšně aktivuje. Po aktivaci na stránce aktualizuje znamenat, že se zařízení úspěšně aktivuje.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Data Box Gateway, jako jsou:

> [!div class="checklist"]
> * Připojte se k virtuálnímu zařízení
> * Nastavit a aktivovat virtuálního zařízení


Přejděte k dalšímu kurzu, kde se naučíte, jak přenést data s bránou Data Box.

> [!div class="nextstepaction"]
> [Přenos dat pomocí brány dat pole](./data-box-gateway-deploy-add-shares.md).
