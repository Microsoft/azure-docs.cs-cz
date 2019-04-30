---
title: Připojte se ke konfiguraci a aktivovat Azure Data Box hraničního zařízení na portálu Azure portal | Dokumentace Microsoftu
description: Třetí kurz k nasazení hrany pole dat dává pokyn k připojení a nastavení a aktivovat fyzické zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: b97334ae60715f021cce387f9d73b5ea69eea7fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758511"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Kurz: Připojení, nastavení a aktivovat Azure Data Box Edge 

Tento kurz popisuje, jak připojit ke službě, nastavení a aktivovat vaše zařízení Azure Data Box Edge pomocí místního webového uživatelského rozhraní.

Proces instalace a aktivace může trvat přibližně 20 minut na dokončení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k fyzické zařízení
> * Nastavit a aktivovat fyzické zařízení

## <a name="prerequisites"></a>Požadavky

Než začnete konfigurovat a nastavit vaše zařízení Data Box Edge, ujistěte se, že:

* Fyzické zařízení nainstalujete podle popisu v [nainstalovat okraj pole Data](data-box-edge-deploy-install.md).
* Máte aktivační klíč ze služby Data Box Edge, který jste vytvořili pro správu zařízení Data Box Edge. Další informace najdete v části [Příprava na nasazení Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Připojte se k nastavení místního webového uživatelského rozhraní 

1. Nakonfigurujte adaptér sítě Ethernet na počítači pro připojení k zařízení Data Box Edge pomocí statické IP adresy 192.168.100.5 a podsítě 255.255.255.0.

2. Připojte počítač k portu 1 na vašem zařízení. Na následujícím obrázku slouží k identifikaci portů 1 na vašem zařízení.

    ![Propojovací rozhraní zařízení se zapojenou kabeláží](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Otevřete okno prohlížeče a přístup k místní webové uživatelské rozhraní zařízení, u https://192.168.100.10.  
    Tato akce může trvat několik minut poté, co jste zapnuli zařízení. 

    Zobrazí chybu nebo upozornění, že dojde k problému s certifikátem zabezpečení webu. 
   
    ![Zpráva chyby certifikátu zabezpečení webu](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Vyberte **pokračovat na tuto webovou stránku**.  
    Tyto kroky může lišit v závislosti na prohlížeči, které používáte.

5. Přihlaste se k webu uživatelského rozhraní vašeho zařízení. Výchozí heslo je *Heslo1*. 
   
    ![Data Box hraniční zařízení přihlašovací stránky](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. Do příkazového řádku změňte heslo správce zařízení.  
    Nové heslo musí obsahovat 8 až 16 znaků. Musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číselné a speciální znaky.

Teď jste na řídicím panelu vašeho zařízení.

## <a name="set-up-and-activate-the-physical-device"></a>Nastavit a aktivovat fyzické zařízení
 
Řídicí panel zobrazuje různá nastavení, které jsou potřeba ke konfiguraci a registraci fyzického zařízení ve službě Data Box Edge. **Název zařízení**, **nastavení sítě**, **nastavení proxy webu**, a **nastavení času** jsou volitelné. Pouze požadované nastavení, musí být **nastavení cloudu**.
   
![Místního webového uživatelského rozhraní "Panel" stránky](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. V levém podokně vyberte **název zařízení**a potom zadejte popisný název pro vaše zařízení.  
    Popisný název musí mít délku 1 až 15 znaků a obsahovat písmena, číslice a pomlčky.

    ![Stránka "Název zařízení" místního webového uživatelského rozhraní](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Volitelné) V levém podokně vyberte **nastavení sítě** a pak proveďte konfiguraci nastavení.  
    Na fyzické zařízení existuje šest síťových rozhraní. PORT 1 a PORT 2 jsou 1 GB/s síťové rozhraní. PORT 3, PORT 4, PORT 5 a 6 portu jsou všechna síťová rozhraní 25 GB/s, které může sloužit také jako 10 GB/s síťové rozhraní. PORT 1 se automaticky nakonfiguruje jako jen pro správu port a PORT 2 až 6 portu jsou všechny porty data. **Nastavení sítě** stránka je uvedeno dále.
    
    ![Stránka "Nastavení sítě" místního webového uživatelského rozhraní](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Při konfiguraci nastavení sítě, mějte na paměti:

   - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. IP adresa, podsíť, brána a DNS jsou automaticky přiřazeny.
   - Pokud není povolený DHCP, můžete přiřadit statické IP adresy v případě potřeby.
   - Síťové rozhraní můžete nakonfigurovat jako pro protokol IPv4.

     >[!NOTE] 
     > Doporučujeme přepnutí místní IP adresa síťového rozhraní ze statické na server, pokud nemáte jinou IP adresu pro připojení k zařízení. Pokud pomocí některé síťové rozhraní a můžete přepnout na DHCP, by existovat žádný způsob, jak určit adresu ze serveru DHCP. Pokud chcete změnit adresu DHCP, počkat, dokud se zařízení zaregistruje ve službě a potom změňte. Pak můžete zobrazit IP adresy všech adaptérů v **vlastnosti zařízení** na webu Azure Portal pro vaši službu.

3. (Volitelné) V levém podokně vyberte **nastavení proxy webu**a potom nakonfigurovat váš webový proxy server. Přestože konfigurace webového proxy serveru je volitelné, pokud používáte webový proxy server, můžete ho nakonfigurovat na této stránce pouze.
   
   ![Stránka "Nastavení webového proxy serveru" místního webového uživatelského rozhraní](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na **nastavení proxy webu** stránce, postupujte takto:
   
   a. V **webová adresa URL proxy serveru** zadejte adresu URL v tomto formátu: `http://host-IP address or FQDN:Port number`. Adresy URL HTTPS nejsou podporovány.

   b. V části **ověřování**vyberte **žádný** nebo **NTLM**.

   c. Pokud používáte ověřování, zadejte uživatelské jméno a heslo.

   d. Chcete-li ověřit a použít nastavení nakonfigurované webového proxy serveru, vyberte **použít nastavení**.

4. (Volitelné) V levém podokně vyberte **nastavení času**a potom nakonfigurujte časové pásmo a primární a sekundární server NTP pro vaše zařízení.  
    Servery NTP jsou požadovány, protože zařízení musí synchronizovat čas tak, aby ho mohli ověřit poskytovatelé cloudových služeb.
       
    Na **nastavení času** stránce, postupujte takto:
    
    1. V **časové pásmo** rozevíracího seznamu vyberte časové pásmo, která odpovídá na geografické umístění, ve kterém se nasazuje zařízení.
        Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.

    2. V **primárního serveru NTP** pole, zadejte primární server pro vaše zařízení nebo přijměte výchozí hodnotu time.windows.com.  
        Ujistěte se, že vaše síť povoluje dat NTP z vašeho datového centra k Internetu.

    3. Volitelně **sekundární NTP server** zadejte sekundární server pro vaše zařízení.

    4. Chcete-li ověřit a použít nastavení nakonfigurovaném čase, vyberte **použít nastavení**.

        ![Stránka "Nastavení času" místního webového uživatelského rozhraní](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Volitelné) V levém podokně vyberte **nastavení úložiště** ke konfiguraci úložiště odolnost proti chybám na vašem zařízení. Tato funkce je aktuálně ve verzi Preview. Ve výchozím nastavení není odolné úložiště na zařízení a je ztrátu dat, pokud datový disk selže na zařízení. Když povolíte možnost odolné, se mění její konfigurace úložiště na zařízení a zařízení můžete odolat selhání bez ztráty jedním datovým diskem. Konfigurace úložiště jako odolný sníží využitelné kapacity vašeho zařízení.

    > [!IMPORTANT] 
    > Odolnost proti chybám se dá nakonfigurovat jenom před aktivací zařízení. 

    ![Stránka "Nastavení" místního webového uživatelského rozhraní](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. V levém podokně vyberte **nastavení cloudu**a poté aktivovat vaše zařízení do služby Data Box Edge na webu Azure Portal.
    
    1. V **aktivační kód** zadejte aktivační kód, který jste získali v [získat aktivační klíč](data-box-edge-deploy-prep.md#get-the-activation-key) Edge Data Box.
    2. Vyberte **Použít**.
       
        ![Stránka "Nastavení cloudu" místního webového uživatelského rozhraní](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Nejprve se aktivuje. Zařízení se pak hledá všechny kritické aktualizace a pokud je k dispozici aktualizace jsou automaticky použity. Zobrazí se příslušná oznámení.

        Dialogové okno má také obnovovací klíč, že by měl zkopírujte a uložte ho na bezpečném místě. Tento klíč se používá k obnovení dat v případě, že zařízení se nedá spustit.

        ![Aktualizovat stránku pro "Nastavení cloudu" místního webového uživatelského rozhraní](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Budete muset počkat několik minut, po úspěšném dokončení aktualizace. Stránka se aktualizuje a indikuje, že se zařízení úspěšně aktivuje.

        ![Aktualizovat stránku pro "Nastavení cloudu" místního webového uživatelského rozhraní](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Dokončení nastavení zařízení. Teď můžete přidávat sdílené složky na vašem zařízení.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Připojení k fyzické zařízení
> * Nastavit a aktivovat fyzické zařízení

Zjistěte, jak přenést data s vaším zařízením Data Box Edge, najdete v tématech:

> [!div class="nextstepaction"]
> [Přenos dat pomocí služby Data Box Edge](./data-box-edge-deploy-add-shares.md).
