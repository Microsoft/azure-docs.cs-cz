---
title: Připojte se ke konfiguraci a aktivovat Azure Data Box Edge na webu Azure portal | Dokumentace Microsoftu
description: Třetí kurz k nasazení hrany pole dat dává pokyn k připojení a nastavení a aktivovat fyzické zařízení.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 83bc1d81eaa930fc16c895f4e3b8b9bf1b1ad28c
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48832170"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-edge-preview"></a>Kurz: Připojení, nastavit, aktivovat Azure Data Box Edge (Preview) 

Tento kurz popisuje, jak se připojit k nastavení a aktivaci zařízení Data Box Edge pomocí místního webového uživatelského rozhraní. 

Proces instalace a aktivace může trvat přibližně 20 minut na dokončení. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připojte se k fyzickým zařízením
> * Nastavit a aktivovat fyzické zařízení

> [!IMPORTANT]
> Data Box Edge je ve verzi preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="prerequisites"></a>Požadavky

Než začnete konfigurovat a nastavit hranici pole dat, ujistěte se, že:

* Fyzické zařízení nainstalujete podle popisu v [nainstalovat okraj pole Data](data-box-edge-deploy-install.md).
* Máte aktivační klíč ze služby Data Box Edge, který jste vytvořili pro správu zařízení Data Box Edge. Další informace najdete v části [Příprava na nasazení Azure Data Box Edge](data-box-edge-deploy-prep.md).

## <a name="connect-to-the-local-web-ui-setup"></a>Připojte se k nastavení místního webového uživatelského rozhraní 

1. Nakonfigurujte adaptér sítě Ethernet na počítači, který používáte pro připojení k hraniční zařízení pomocí statické IP adresy 192.168.100.5 a podsítě 255.255.255.0.
2. Připojte počítač k portu 1 na vašem zařízení. 
3. Otevřete okno prohlížeče a přístup k místní webové uživatelské rozhraní zařízení, u https://192.168.100.10. Tato akce může trvat několik minut po zapnutí zařízení. 
4. Zobrazí chybu nebo upozornění, že dojde k problému s certifikátem zabezpečení webu. Klikněte na tlačítko **pokračovat na tuto webovou stránku**. (Tyto kroky může lišit podle prohlížeče použitého.)
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

2. Přihlaste se k webu uživatelského rozhraní vašeho zařízení. Výchozí heslo je *Heslo1*. 
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

3. Budete vyzváni ke změně hesla správce zařízení. Zadejte nové heslo, který obsahuje délku 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číselné a speciální znaky.

Teď jste na **řídicí panel** vašeho zařízení.

## <a name="set-up-and-activate-the-physical-device"></a>Nastavit a aktivovat fyzické zařízení
 
1. Z řídicího panelu můžete přejít na různá nastavení, které jsou potřeba ke konfiguraci a registraci fyzického zařízení ve službě Data Box Edge. **Název zařízení**, **nastavení sítě**, **nastavení proxy webu**, a **nastavení času** jsou volitelné. Pouze požadované nastavení, musí být **nastavení cloudu**.
   
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

2. V **název zařízení** proveďte popisný název zařízení. Popisný název mohou být dlouhé 1 až 15 znaků a může obsahovat písmena, číslice a pomlčky.

    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

3. (Volitelně) konfigurovat vaše **nastavení sítě**. Na fyzické zařízení uvidíte šest síťových rozhraní. PORT 1 a PORT 2 jsou 1 GB/s síťové rozhraní. PORT 3, PORT 4, PORT 5 a 6 portu jsou všechna síťová rozhraní 25 GB/s. PORT 1 se automaticky nakonfiguruje jako jen pro správu portu PORT 2 až 6 portu jsou všechny porty data. **Nastavení sítě** stránce, jak je znázorněno níže.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    Při konfiguraci nastavení sítě, mějte na paměti:

    - Pokud je ve vašem prostředí povolený protokol DHCP, síťová rozhraní se nakonfigurují automaticky. IP adresa, podsíť, brána a DNS jsou automaticky přiřazeny.
    - Pokud není povolený DHCP, můžete přiřadit statické IP adresy v případě potřeby.
    - Síťové rozhraní můžete nakonfigurovat jako pro protokol IPv4.
   
4. (Volitelně) nakonfigurujte váš webový proxy server. Přestože konfigurace webového proxy serveru je volitelné, pokud používáte webový proxy server, můžete konfigurovat pouze ho tady.
   
   ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   V **webového proxy serveru** stránky:
   
   1. Zadat **webová adresa URL proxy serveru** v tomto formátu: `http://host-IP address or FDQN:Port number`. Adresy URL HTTPS nejsou podporovány.
   2. Zadejte **ověřování** jako **základní** nebo **žádný**.
   3. Pokud používáte ověřování, bude také potřeba zadat **uživatelské jméno** a **heslo**.
   4. Klikněte na tlačítko **použít** na ověřit a použít nastavení nakonfigurované webového proxy serveru.

5. (Volitelně) nakonfigurujte nastavení času pro vaše zařízení, jako je časové pásmo a primární a sekundární server NTP. Servery NTP jsou požadovány, protože zařízení musí synchronizovat čas tak, aby ho mohli ověřit poskytovatelé cloudových služeb.
    
    ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    V **nastavení času** stránky:
    
    1. Z rozevíracího seznamu, vyberte **časové pásmo** na základě geografického umístění, ve kterém se nasazuje zařízení. Výchozí časové pásmo pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.
    2. Zadejte **primárního serveru NTP** pro vaše zařízení nebo přijměte výchozí hodnotu time.windows.com. Ujistěte se, že vaše síť umožňuje přenos dat NTP z vašeho datového centra na internet.
    3. Volitelně můžete zadat **sekundární NTP server** pro vaše zařízení.
    4. Klikněte na tlačítko **použít** k ověření a nastavení nakonfigurovaná doba.

6. V **nastavení cloudu** stránce, aktivujte zařízení ve službě Data Box Edge na webu Azure portal.
    
    1. Zadejte **aktivační kód** , které jste získali [získat aktivační klíč](data-box-edge-deploy-prep.md#get-the-activation-key) Edge Data Box.

    2. Klikněte na tlačítko **Použít**. 
       
         ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    3. Jakmile se zařízení úspěšně aktivuje, máte na výběr možnosti režim připojení. Tato nastavení jsou konfigurována, pokud potřebujete pro práci s zařízení v režimu částečně odpojený nebo odpojené. 

        ![](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

Dokončení nastavení zařízení. Teď můžete přidávat sdílené složky na vašem zařízení.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech okraj pole Data, jako:

> [!div class="checklist"]
> * Připojte se k fyzickým zařízením
> * Nastavit a aktivovat fyzické zařízení


Přejděte k dalšímu kurzu, kde se naučíte, jak přenést data s hranici pole Data.

> [!div class="nextstepaction"]
> [Přenos dat pomocí služby Data Box Edge](./data-box-edge-deploy-add-shares.md).