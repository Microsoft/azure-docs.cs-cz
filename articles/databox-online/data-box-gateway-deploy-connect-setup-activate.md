---
title: Připojení, konfigurace a aktivace služby Azure Data Box Gateway na webu Azure Portal
description: Třetí kurz nasazení brány datové schránky vás instruuje k připojení, nastavení a aktivaci virtuálního zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: b3cf4fd958202c28586b7c15932dc88a21d7c60f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686867"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Kurz: Připojení, nastavení, aktivace brány datové schránky Azure

## <a name="introduction"></a>Úvod

Tento kurz popisuje, jak se připojit, nastavit a aktivovat zařízení Brány datové schránky pomocí místního webového uživatelského rozhraní. 

Proces instalace a aktivace může trvat přibližně 10 minut. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojení k virtuálnímu zařízení
> * Nastavení a aktivace virtuálního zařízení

## <a name="prerequisites"></a>Požadavky

Než nakonfigurujete a nastavíte bránu datové schránky, ujistěte se, že:

* Zřídíte virtuální zařízení a získali jste k němu připojenou adresu URL, jak je podrobně uvedeno v [části Zřídit bránu datové schránky v technologii Hyper-V](data-box-gateway-deploy-provision-hyperv.md) nebo [Zřídit bránu datové schránky ve společnosti VMware](data-box-gateway-deploy-provision-vmware.md).
* Aktivační klíč máte ze služby Brána datové schránky, kterou jste vytvořili ke správě zařízení brány datové schránky. Další informace najdete v části [Příprava na nasazení brány Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Připojení k místnímu nastavení webového uživatelského prostředí 

1. Otevřete okno prohlížeče a získejte přístup k místnímu webovému uživatelskému rozhraní zařízení na adrese:
   
   https:\//ip-address-of-network-interface
   
   Použijte adresu URL připojení uvedenou v předchozím kurzu. Zobrazí se chyba nebo upozornění, že došlo k potížím s certifikátem zabezpečení webu.

2. Vyberte **Pokračovat na tuto webovou stránku**. Tyto kroky se mohou lišit v závislosti na prohlížeči, který používáte.
   
    ![Chybová zpráva o certifikátu zabezpečení webu](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Přihlaste se k webovému uživatelskému rozhraní svého virtuálního zařízení. Výchozí heslo je *Password1*. 
   
    ![Přihlášení k místnímu webovému uživatelskému uživatelskému prostředí](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. Na výzvu změňte heslo zařízení. Nové heslo musí obsahovat 8 až 16 znaků. Musí obsahovat 3 z následujících znaků: velká, malá, číselná a speciální znatá.

    ![Změna hesla zařízení](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Nyní jste na **řídicím panelu** vašeho zařízení.

## <a name="set-up-and-activate-the-virtual-device"></a>Nastavení a aktivace virtuálního zařízení
 
Řídicí panel zobrazuje různá nastavení, která jsou nutná ke konfiguraci a registraci virtuálního zařízení pomocí služby Brána datové schránky. **Název zařízení**, **Nastavení sítě**, Nastavení **webového proxy serveru**a Nastavení **času** jsou volitelné. Jediným požadovaným **nastavením**jsou nastavení cloudu .
   
![Stránka "Řídicí panel" místního webového uživatelského panelu](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. V levém podokně vyberte **Název zařízení**a zadejte popisný název zařízení. Popisný název musí obsahovat 1 až 15 znaků a musí obsahovat písmena, čísla a pomlčky.

    ![Stránka "Název zařízení" místního webového uživatelského rozhraní](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. (Nepovinné) V levém podokně vyberte **Nastavení sítě** a pak nastavení nakonfigurujte. Na virtuálním zařízení uvidíte alespoň jedno síťové rozhraní a další v závislosti na tom, kolik jste nakonfigurovali v podkladovém virtuálním počítači. Stránka **Nastavení sítě** pro virtuální zařízení s jedním síťovým rozhraním je uvedena níže.
    
    ![Stránka Místní webové uživatelské tlačítko "Nastavení sítě"](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    Při konfiguraci nastavení sítě mějte na paměti:

    - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. Automaticky se přiřadí IP adresa, podsíť, brána a DNS.
    - Pokud není povolena služba DHCP, můžete v případě potřeby přiřadit statické IP adresy.
    - Síťové rozhraní můžete nakonfigurovat jako protokol IPv4.

     >[!NOTE] 
     > Doporučujeme nepřepínat místní IP adresu síťového rozhraní ze statického na DHCP, pokud nemáte jinou ADRESU IP pro připojení k zařízení. Pokud používáte jedno síťové rozhraní a přepnete na server DHCP, nebude možné určit adresu DHCP. Pokud chcete změnit na adresu DHCP, počkejte, dokud se zařízení nezaregistruje u služby, a poté změňte. Potom můžete zobrazit IP adresy všech adaptérů ve **vlastnostech Zařízení** na webu Azure Portal pro vaši službu.

3. Volitelně konfigurujte proxy server. Přestože je konfigurace webového proxy serveru volitelná, pokud používáte webový proxy server, můžete jej nakonfigurovat pouze na této stránce.
   
   ![Stránka Nastavení webového proxy uživatelského panelu v místním webovém uživatelském prostředí](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   Na stránce **webového proxy** serveru postupujte takto:
   
   1. Do pole **Adresa URL webového proxy** serveru `http://&lt;host-IP address or FQDN&gt;:Port number`zadejte adresu URL v tomto formátu: . Adresy URL protokolu HTTPS nejsou podporovány.
   2. V části **Ověřování**vyberte **možnost Žádný** nebo **NTLM**.
   3. Pokud používáte ověřování, zadejte **uživatelské jméno** a **heslo**.
   4. Chcete-li ověřit a použít nakonfigurované nastavení webového proxy serveru, vyberte **použít**.

   > [!NOTE]
   > Proxy-auto config (PAC) soubory nejsou podporovány. Soubor PAC definuje, jak mohou webové prohlížeče a další uživatelští agenti automaticky zvolit příslušný proxy server (metodu přístupu) pro načtení dané adresy URL.
   > Proxy, které se pokoušejí zachytit a číst veškerý provoz (pak znovu podepsat vše s vlastní certifikací) nejsou kompatibilní, protože certifikát proxy není důvěryhodný.
   > S Azure Data Box Gateway obvykle fungují transparentní proxy servery.

4. (Nepovinné) V levém podokně vyberte **nastavení času**a pak nakonfigurujte časové pásmo a primární a sekundární servery NTP pro vaše zařízení. 

    Servery NTP jsou vyžadovány, protože zařízení musí synchronizovat čas, aby se mohlo ověřit u poskytovatelů cloudových služeb.
    
    ![Stránka "Nastavení času" místního webového uživatelského prostředí](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Na stránce **Nastavení času** postupujte takto:
    
    1. V rozevíracím seznamu **Časové pásmo** vyberte časové pásmo, které odpovídá geografické poloze, ve které je zařízení nasazeno.
        Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.

    2. Zadejte **primární server NTP** pro vaše zařízení `time.windows.com`nebo přijměte výchozí hodnotu aplikace .   
        Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.

    3. Volitelně můžete do pole **Sekundární server NTP** zadat sekundární server pro vaše zařízení.

    4. Chcete-li ověřit a použít nakonfigurované nastavení času, vyberte **použít**.

6. V levém podokně vyberte **Nastavení cloudu**a pak aktivujte zařízení pomocí služby Brána datových schronů na webu Azure Portal.
    
    1. Do pole **aktivační klíč** zadejte **aktivační klíč,** který jste dostali, [v části Získat aktivační klíč](data-box-gateway-deploy-prep.md#get-the-activation-key) pro bránu datové schránky.

    2. Vyberte **Aktivovat**.
       
         ![Stránka Místní webové uživatelské tlačítko "Nastavení cloudu"](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Zařízení je aktivováno a jsou automaticky použity důležité aktualizace, pokud jsou k dispozici. Zobrazí se oznámení v tomto smyslu. Sledujte průběh aktualizace prostřednictvím portálu Azure.

        ![Stránka Místní webové uživatelské tlačítko "Nastavení cloudu"](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **Dialogové okno má také obnovovací klíč, který byste měli zkopírovat a uložit na bezpečném místě. Tento klíč se používá k obnovení dat v případě, že se zařízení nemůže spustit.**


    4. Bude pravděpodobně nutné počkat několik minut, než bude aktualizace úspěšně dokončena. Po dokončení aktualizace se přihlaste k zařízení. Stránka **Nastavení cloudu** se aktualizuje a označuje, že zařízení bylo úspěšně aktivováno.

        ![Místní webové uživatelské tlačítko "Nastavení cloudu" stránka aktualizována](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Nastavení zařízení je dokončeno. Nyní můžete do zařízení přidávat sdílené složky.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Připojení k virtuálnímu zařízení
> * Nastavení a aktivace virtuálního zařízení

Informace o přenosu dat pomocí brány datové schránky najdete v tématu:

> [!div class="nextstepaction"]
> [Přenos dat pomocí brány datové schránky](./data-box-gateway-deploy-add-shares.md).
