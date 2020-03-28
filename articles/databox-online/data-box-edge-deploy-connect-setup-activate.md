---
title: Kurz pro připojení, konfiguraci, aktivaci zařízení Azure Data Box Edge na webu Azure Portal | Dokumenty společnosti Microsoft
description: Kurz nasazení Data Box Edge vás instruuje k připojení, nastavení a aktivaci fyzického zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 144c59c8bc24e8e10584702ec6cd48f7aa8c15c1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239053"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge"></a>Kurz: Připojení, nastavení a aktivace Azure Data Box Edge 

Tento kurz popisuje, jak se můžete připojit, nastavit a aktivovat zařízení Azure Data Box Edge pomocí místního webového uživatelského rozhraní.

Proces instalace a aktivace může trvat přibližně 20 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k fyzickému zařízení
> * Nastavení a aktivace fyzického zařízení

## <a name="prerequisites"></a>Požadavky

Než zařízení Data Box Edge nakonfigurujete a nastavíte, ujistěte se, že:

* Fyzické zařízení jste nainstalovali tak, jak je podrobně popsáno v [části Instalace datové schránky Edge](data-box-edge-deploy-install.md).
* Aktivační klíč máte ze služby Data Box Edge, kterou jste vytvořili ke správě zařízení Data Box Edge. Další informace najdete v centru [připravte se na nasazení Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského prostředí 

1. Nakonfigurujte adaptér Ethernet v počítači tak, aby se připojil k zařízení Data Box Edge se statickou ADRESOu IP 192.168.100.5 a podsítí 255.255.255.0.

2. Připojte počítač k portu port 1 v zařízení. Pomocí následujícího obrázku můžete identifikovat port port 1 v zařízení.

    ![Propojovací rozhraní zařízení se zapojenou kabeláží](./media/data-box-edge-deploy-install/backplane-cabled.png)


3. Otevřete okno prohlížeče a získejte přístup k `https://192.168.100.10`místnímu webovému uživatelskému rozhraní zařízení na adrese .  
    Tato akce může trvat několik minut po zapnutí zařízení. 

    Zobrazí se chyba nebo upozornění, že došlo k potížím s certifikátem zabezpečení webu. 
   
    ![Chybová zpráva o certifikátu zabezpečení webu](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

4. Vyberte **Pokračovat na tuto webovou stránku**.  
    Tyto kroky se mohou lišit v závislosti na prohlížeči, který používáte.

5. Přihlaste se k webovému uživatelskému rozhraní svého zařízení. Výchozí heslo je *Password1*. 
   
    ![Přihlašovací stránka zařízení Data Box Edge](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

6. Na výzvu změňte heslo správce zařízení.  
    Nové heslo musí obsahovat 8 až 16 znaků. Musí obsahovat tři z následujících znaků: velká, malá, číselná a speciální znatá.

Nyní jste na řídicím panelu zařízení.

## <a name="set-up-and-activate-the-physical-device"></a>Nastavení a aktivace fyzického zařízení
 
Řídicí panel zobrazuje různá nastavení, která jsou nutná ke konfiguraci a registraci fyzického zařízení pomocí služby Data Box Edge. **Název zařízení**, **Nastavení sítě**, Nastavení **webového proxy serveru**a Nastavení **času** jsou volitelné. Jediným požadovaným **nastavením**jsou nastavení cloudu .
   
![Stránka "Řídicí panel" místního webového uživatelského panelu](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. V levém podokně vyberte **Název zařízení**a zadejte popisný název zařízení.  
    Popisný název musí obsahovat 1 až 15 znaků a musí obsahovat písmena, číslice a pomlčky.

    ![Stránka "Název zařízení" místního webového uživatelského rozhraní](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. (Nepovinné) V levém podokně vyberte **Nastavení sítě** a pak nastavení nakonfigurujte.  
    Ve fyzickém zařízení existuje šest síťových rozhraní. PORT 1 a PORT 2 jsou síťová rozhraní 1 Gb/s. PORT 3, PORT 4, PORT 5 a PORT 6 jsou všechna síťová rozhraní 25 Gb/s, která mohou sloužit také jako síťová rozhraní 10 Gb/s. PORT 1 je automaticky konfigurován jako port pouze pro správu a port port 2 až port 6 jsou všechny datové porty. Stránka **Nastavení sítě** je uvedena níže.
    
    ![Stránka Místní webové uživatelské tlačítko "Nastavení sítě"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Při konfiguraci nastavení sítě mějte na paměti:

   - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. Automaticky se přiřadí IP adresa, podsíť, brána a DNS.
   - Pokud není povolena služba DHCP, můžete v případě potřeby přiřadit statické IP adresy.
   - Síťové rozhraní můžete nakonfigurovat jako protokol IPv4.

     >[!NOTE] 
     > Doporučujeme nepřepínat místní IP adresu síťového rozhraní ze statického na DCHP, pokud nemáte jinou IP adresu pro připojení k zařízení. Pokud používáte jedno síťové rozhraní a přepnete na server DHCP, nebude možné určit adresu DHCP. Pokud chcete změnit na adresu DHCP, počkejte, dokud se zařízení nezaregistruje u služby, a poté změňte. Potom můžete zobrazit IP adresy všech adaptérů ve **vlastnostech Zařízení** na webu Azure Portal pro vaši službu.

3. (Nepovinné) V levém podokně vyberte **nastavení webového proxy serveru**a nakonfigurujte webový proxy server. Přestože je konfigurace webového proxy serveru volitelná, pokud používáte webový proxy server, můžete jej nakonfigurovat pouze na této stránce.
   
   ![Stránka Nastavení webového proxy uživatelského panelu v místním webovém uživatelském prostředí](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   Na stránce **Nastavení webového proxy serveru** postupujte takto:
   
   a. Do pole **Adresa URL webového proxy** serveru `http://host-IP address or FQDN:Port number`zadejte adresu URL v tomto formátu: . Adresy URL protokolu HTTPS nejsou podporovány.

   b. V části **Ověřování**vyberte **možnost Žádný** nebo **NTLM**.

   c. Pokud používáte ověřování, zadejte uživatelské jméno a heslo.

   d. Chcete-li ověřit a použít nakonfigurované nastavení webového proxy serveru, vyberte **použít nastavení**.
   
   > [!NOTE]
   > Proxy-auto config (PAC) soubory nejsou podporovány. Soubor PAC definuje, jak mohou webové prohlížeče a další uživatelští agenti automaticky zvolit příslušný proxy server (metodu přístupu) pro načtení dané adresy URL.
   > Proxy, které se pokoušejí zachytit a číst veškerý provoz (pak znovu podepsat vše s vlastní certifikací) nejsou kompatibilní, protože certifikát proxy není důvěryhodný.
   > S Azure Data Box Edge obvykle fungují transparentní proxy servery.

4. (Nepovinné) V levém podokně vyberte **nastavení času**a pak nakonfigurujte časové pásmo a primární a sekundární servery NTP pro vaše zařízení.  
    Servery NTP jsou vyžadovány, protože zařízení musí synchronizovat čas, aby se mohlo ověřit u poskytovatelů cloudových služeb.
       
    Na stránce **Nastavení času** postupujte takto:
    
    1. V rozevíracím seznamu **Časové pásmo** vyberte časové pásmo, které odpovídá geografické poloze, ve které je zařízení nasazováno.
        Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.

    2. Do pole **Primární server NTP** zadejte primární server pro své zařízení nebo přijměte výchozí hodnotu time.windows.com.  
        Ujistěte se, že vaše síť umožňuje přenos ntp předat z datového centra do internetu.

    3. Volitelně můžete do pole **Sekundární server NTP** zadat sekundární server pro vaše zařízení.

    4. Chcete-li ověřit a použít nakonfigurovaná nastavení času, vyberte **použít nastavení**.

        ![Stránka "Nastavení času" místního webového uživatelského prostředí](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. (Nepovinné) V levém podokně vyberte **Nastavení úložiště** a nakonfigurujte odolnost úložiště v zařízení. Tato funkce je aktuálně ve verzi Preview. Ve výchozím nastavení není úložiště v zařízení odolné a dojde ke ztrátě dat, pokud v zařízení selže datový disk. Pokud povolíte možnost Pružná, úložiště v zařízení bude překonfigurováno a zařízení vydrží selhání jednoho datového disku bez ztráty dat. Konfigurace úložiště jako odolného sníží využitelnou kapacitu vašeho zařízení.

    > [!IMPORTANT] 
    > Odolnost proti chybám lze nakonfigurovat pouze před aktivací zařízení. 

    ![Stránka Místní webové uživatelské tlačítko "Nastavení úložiště"](./media/data-box-edge-deploy-connect-setup-activate/storage-settings.png)

6. V levém podokně vyberte **Nastavení cloudu**a pak aktivujte zařízení pomocí služby Data Box Edge na webu Azure Portal.
    
    1. Do pole **aktivační klíč** zadejte aktivační klíč, který jste dostali v části Získat [aktivační klíč](data-box-edge-deploy-prep.md#get-the-activation-key) pro Data Box Edge.
    2. Vyberte **Použít**.
       
        ![Stránka Místní webové uživatelské tlačítko "Nastavení cloudu"](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Nejprve je zařízení aktivováno. Zařízení je pak zkontrolováno na všechny důležité aktualizace a pokud jsou k dispozici, budou automaticky použity. Zobrazí se oznámení v tomto smyslu.

        Dialogové okno má také obnovovací klíč, který byste měli zkopírovat a uložit na bezpečném místě. Tento klíč se používá k obnovení dat v případě, že se zařízení nemůže spustit.

        ![Místní webové uživatelské tlačítko "Nastavení cloudu" stránka aktualizována](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Po úspěšném dokončení aktualizace může být nutné počkat několik minut. Stránka se aktualizuje a označuje, že zařízení bylo úspěšně aktivováno.

        ![Místní webové uživatelské tlačítko "Nastavení cloudu" stránka aktualizována](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Nastavení zařízení je dokončeno. Nyní můžete do zařízení přidávat sdílené složky.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Připojení k fyzickému zařízení
> * Nastavení a aktivace fyzického zařízení

Informace o přenosu dat pomocí zařízení Data Box Edge najdete v tématu:

> [!div class="nextstepaction"]
> [Přenos dat pomocí data Box Edge](./data-box-edge-deploy-add-shares.md).
