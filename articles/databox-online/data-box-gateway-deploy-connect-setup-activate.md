---
title: Připojte se ke konfiguraci a aktivovat Azure Data Box brány na webu Azure portal | Dokumentace Microsoftu
description: Třetí kurz, abyste mohli nasadit bránu pole dat dává pokyn k připojení a nastavení a aktivaci vašeho virtuálního zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: de2ef4908260a62acf28a270dda6ad738a1760b9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402322"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Kurz: Připojení, nastavit, aktivovat Azure Data Box brány

## <a name="introduction"></a>Úvod

Tento kurz popisuje, jak se připojit k, nastavení a aktivovat vaše zařízení Data Box brány pomocí místního webového uživatelského rozhraní. 

Proces instalace a aktivace může trvat přibližně 10 minut na dokončení. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojte se k virtuálnímu zařízení
> * Nastavit a aktivovat virtuálního zařízení

## <a name="prerequisites"></a>Požadavky

Než začnete konfigurovat a nastavit bránu dat pole, ujistěte se, že:

* Jste zřídili virtuální zařízení a získat adresu URL připojených k němu podle popisu v [bránu Data Gateway pole v Hyper-V zřídit](data-box-gateway-deploy-provision-hyperv.md) nebo [zřízení bránu Data Gateway pole v prostředí VMware](data-box-gateway-deploy-provision-vmware.md).
* Máte aktivační klíč ze služby Data Box brány, kterou jste vytvořili pro správu zařízení Data Box brány. Další informace najdete v části [Příprava na nasazení Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Připojte se k nastavení místního webového uživatelského rozhraní 

1. Otevřete okno prohlížeče a přístup k místní webové uživatelské rozhraní zařízení na:
   
   [https://ip-address-of-network-interface](https://ip-address-of-network-interface)
   
   Pomocí adresy URL pro připojení, které jste si poznamenali v předchozím kurzu. Zobrazí chybu nebo upozornění, že dojde k problému s certifikátem zabezpečení webu.

2. Vyberte **pokračovat na tuto webovou stránku**. Tyto kroky může lišit v závislosti na prohlížeči, které používáte.
   
    ![Zpráva chyby certifikátu zabezpečení webu](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Přihlaste se do webového uživatelského rozhraní vašeho virtuálního zařízení. Výchozí heslo je *Heslo1*. 
   
    ![Přihlaste se k místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Do příkazového řádku změňte heslo zařízení. Nové heslo musí obsahovat 8 až 16 znaků. Musí obsahovat 3 z následujících možností: velká písmena, malá písmena, číselné a speciální znaky.

    ![Změna hesla zařízení](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Teď jste na **řídicí panel** vašeho zařízení.

## <a name="set-up-and-activate-the-virtual-device"></a>Nastavit a aktivovat virtuálního zařízení
 
Řídicí panel zobrazuje různá nastavení, které jsou potřeba ke konfiguraci a registraci virtuálního zařízení ve službě Data Box brány. **Název zařízení**, **nastavení sítě**, **nastavení proxy webu**, a **nastavení času** jsou volitelné. Pouze požadované nastavení, musí být **nastavení cloudu**.
   
![Místního webového uživatelského rozhraní "Panel" stránky](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. V levém podokně vyberte **název zařízení**a potom zadejte popisný název pro vaše zařízení. Popisný název musí mít délku 1 až 15 znaků a obsahovat písmeno, číslice a pomlčky.

    ![Stránka "Název zařízení" místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Volitelné) V levém podokně vyberte **nastavení sítě** a pak proveďte konfiguraci nastavení. Na svém virtuálním zařízení zobrazí se vám alespoň jedno síťové rozhraní a provádění dalších akcí v závislosti na tom, kolik jste nakonfigurovali v základní virtuální počítač. **Nastavení sítě** stránce pro virtuální zařízení s jedním síťovým rozhraním povoleno je znázorněno níže.
    
    ![Stránka "Nastavení sítě" místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Při konfiguraci nastavení sítě, mějte na paměti:

    - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. IP adresa, podsíť, brána a DNS jsou automaticky přiřazeny.
    - Pokud není povolený DHCP, můžete přiřadit statické IP adresy v případě potřeby.
    - Síťové rozhraní můžete nakonfigurovat jako pro protokol IPv4.

     >[!NOTE] 
     > Doporučujeme přepnutí místní IP adresa síťového rozhraní ze statické na protokol DHCP, pokud nemáte jinou IP adresu pro připojení k zařízení. Pokud pomocí některé síťové rozhraní a můžete přepnout na DHCP, by existovat žádný způsob, jak určit adresu ze serveru DHCP. Pokud chcete změnit adresu DHCP, počkat, dokud se zařízení zaregistruje ve službě a potom změňte. Pak můžete zobrazit IP adresy všech adaptérů v **vlastnosti zařízení** na webu Azure Portal pro vaši službu.

3. Volitelně konfigurujte proxy server. Přestože konfigurace webového proxy serveru je volitelné, pokud používáte webový proxy server, můžete ho nakonfigurovat jenom na této stránce.
   
   ![Stránka "Nastavení webového proxy serveru" místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na **webového proxy serveru** stránce, postupujte takto:
   
   1. V **webová adresa URL proxy serveru** zadejte adresu URL v tomto formátu: `http://&lt;host-IP address or FQDN&gt;:Port number`. Adresy URL HTTPS nejsou podporovány.
   2. V části **ověřování**vyberte **žádný** nebo **NTLM**.
   3. Pokud používáte ověřování, zadejte **uživatelské jméno** a **heslo**.
   4. Chcete-li ověřit a použít nastavení nakonfigurované webového proxy serveru, vyberte **použít**.

4. (Volitelné) V levém podokně vyberte **nastavení času**a potom nakonfigurujte časové pásmo a primární a sekundární server NTP pro vaše zařízení. 

    Servery NTP jsou požadovány, protože zařízení musí synchronizovat čas tak, aby ho mohli ověřit poskytovatelé cloudových služeb.
    
    ![Stránka "Nastavení času" místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    V **nastavení času** stránce, postupujte takto:
    
    1. V **časové pásmo** rozevíracího seznamu vyberte časové pásmo, která odpovídá na geografické umístění, ve kterém je nasazený zařízení.
        Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.

    2. Zadejte **primárního serveru NTP** pro vaše zařízení nebo přijměte výchozí hodnotu `time.windows.com`.   
        Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.

    3. Volitelně **sekundární NTP server** zadejte sekundární server pro vaše zařízení.

    4. Chcete-li ověřit a použít nastavení nakonfigurovaném čase, vyberte **použít**.

6. V levém podokně vyberte **nastavení cloudu**a poté aktivovat vaše zařízení do služby Data Box brány na webu Azure portal.
    
    1. V **aktivační kód** zadejte **aktivační kód** , které jste získali [získat aktivační klíč](data-box-gateway-deploy-prep.md#get-the-activation-key) pro bránu dat pole.

    2. Vyberte **aktivovat**.
       
         ![Stránka "Nastavení cloudu" místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Zařízení se aktivuje a kritických aktualizací, pokud je k dispozici, se automaticky provedou. Zobrazí se příslušná oznámení. Sledujte průběh aktualizace přes Azure portal.

        ![Stránka "Nastavení cloudu" místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Dialogové okno má také obnovovací klíč, který by měl zkopírovat a uložit na bezpečném místě. Tento klíč se používá k obnovení dat v případě, že zařízení se nedá spustit.**


    4. Budete muset počkat několik minut, než se aktualizace úspěšně dokončit. Po instalaci aktualizace se dokončí, přihlaste se k zařízení. **Nastavení cloudu** stránka se aktualizuje a indikuje, že se zařízení úspěšně aktivuje.

        ![Aktualizovat stránku pro "Nastavení cloudu" místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Dokončení nastavení zařízení. Teď můžete přidávat sdílené složky na vašem zařízení.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Připojte se k virtuálnímu zařízení
> * Nastavit a aktivovat virtuálního zařízení

Zjistěte, jak přenos dat pomocí brány dat pole, najdete v tématech:

> [!div class="nextstepaction"]
> [Přenos dat pomocí brány dat pole](./data-box-gateway-deploy-add-shares.md).
