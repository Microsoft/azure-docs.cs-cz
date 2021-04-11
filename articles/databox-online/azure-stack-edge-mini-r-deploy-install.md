---
title: Kurz instalace fyzického zařízení se zkráceným R Azure Stack Edge | Microsoft Docs
description: Druhý kurz o instalaci Azure Stack hraničního připojení zařízení R zahrnuje způsob zapojení fyzického zařízení do elektrické sítě.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 8b154dabd6f672c6fdaf77c5f8d48f80fb40d5d8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060104"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Kurz: instalace Azure Stack hraničního konektoru R

V tomto kurzu se dozvíte, jak nainstalovat Azure Stack fyzického zařízení na hraničních zařízeních na Mini R. Postup instalace zahrnuje kabely zařízení.

Dokončení instalace může trvat přibližně 30 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola zařízení
> * Zapojení kabeláže zařízení

## <a name="prerequisites"></a>Požadavky

Požadavky pro instalaci fyzického zařízení jsou následující:

### <a name="for-the-azure-stack-edge-resource"></a>Pro prostředek Azure Stack Edge

Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v části [Příprava na nasazení Azure Stack hraniční Mini R](azure-stack-edge-mini-r-deploy-prep.md).
    * Vytvořili jste prostředek Azure Stack Edge pro nasazení zařízení.
    * Vygenerovali jste aktivační klíč pro aktivaci zařízení pomocí Azure Stackho hraničního prostředku.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>Pro fyzické zařízení Azure Stack hraničního konektoru R

Před nasazením zařízení:

- Ujistěte se, že zařízení funguje bezpečně na plochém, stabilním a úrovni pracovního prostoru.
- Ověřte, že lokalita, kterou chcete nastavit, disponuje jedním z následujícího:
    - Standardní AC výkon z nezávislého zdroje-nebo-
    - Jednotka PDU napájení stojanu. 
    

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru

Než začnete:

- Projděte si požadavky na síť pro nasazení Azure Stack hraničních zařízení Mini R a nakonfigurujte síť datacenter podle požadavků. Další informace najdete v tématu [Azure Stack požadavky na síť Edge](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements).

- Ujistěte se, že minimální šířka pásma internetu je 20 MB/s pro optimální fungování zařízení. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Kontrola zařízení

Toto zařízení se dodává jako jediná jednotka. Zařízení rozbalíte pomocí následujících kroků.

1. Umístěte krabici na rovný povrch.
2. Zkontrolujte případná poškození v případě zařízení. Otevřete případ a zkontrolujte zařízení. Pokud se zdá, že se jedná o poškozený případ nebo zařízení, kontaktujte podpora Microsoftu, abyste pomohli posoudit, jestli je zařízení v dobrém pořadí.
3. Po otevření případ se ujistěte, že máte následující:
    - Jedno přenosné Azure Stack hraniční zařízení v R s připojenými postranními nárazníky
    - Jedna baterie a zadní kryta připojená k zařízení. 
    - Jeden napájecí kabel k připojení baterie ke zdroji napájení 

Pokud jste neobdrželi všechny uvedené položky, obraťte se na podporu Azure Stack Edge. Dalším krokem je zapojení zařízení do kabelu.


## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

Následující postupy vysvětlují, jak Azure Stack hraniční zařízení pro napájení a síť kabelem.

Než začnete kabelovat vaše zařízení, budete potřebovat následující:

- Azure Stack fyzického zařízení na hraničních zařízeních na pracovišti na instalačním webu.
- Jeden napájecí kabel.
- Alespoň jeden síťový kabel 1 GbE RJ-45 pro připojení k rozhraní pro správu. Na zařízení jsou dvě síťová rozhraní 1 GbE – jedno pro správu a druhé pro data.
- 1 10-GbE SFP + Copper kabel pro každé rozhraní datové sítě, které se má nakonfigurovat. Aspoň jedno rozhraní datové sítě z portu 3 nebo 4 musí být připojené k Internetu (s připojením k Azure).  
- Přístup k jedné jednotce pro distribuci napájení (doporučeno)

> [!NOTE]
> - Pokud se připojujete pouze k jednomu síťovému rozhraní, doporučujeme, abyste pro posílání dat do Azure používali síťové rozhraní s 10 GbE, jako je PORT 3 nebo PORT 4. 
> - Pro zajištění nejlepšího výkonu a zpracování velkých objemů dat zvažte připojení všech datových portů.
> - Azure Stack hraniční zařízení by se mělo připojit k síti datacenter, aby mohl ingestovat data ze serverů zdrojů dat. <!-- engg TBC -->

Na Azure Stack hraniční zařízení:

- Přední panel má dopravce SSD. 

    - Zařízení má v patici 1 disk SSD. 
    - Zařízení má také kartu CFx, která slouží jako úložiště pro disk operačního systému.
    
- Přední panel má síťová rozhraní a přístup k Wi-Fi.

    - 2 X 1 GbE 45 síťová rozhraní. Jedná se o PORT 1 a PORT 2 v místním uživatelském rozhraní zařízení.
    - 2 X 10 GbE SFP + síťová rozhraní. Jedná se o PORT 3 a PORT 4 v místním uživatelském rozhraní zařízení. 
    - Jeden Wi-Fi port s připojeným vysílačem Wi-Fi.

- Přední panel obsahuje také tlačítko napájení. 

- Zadní panel zahrnuje baterii a krytu, které jsou v zařízení nainstalované. 


Proveďte následující kroky, které zařízení zapojte do sítě pro napájení a síť.

1. Identifikujte různé součásti sítě a úložiště v přední rovině zařízení.

    ![Rozhraní sítě a úložiště na zařízení](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. Vyhledejte tlačítko napájení v levém dolním rohu přední části zařízení. 

    ![Přední rovina zařízení s tlačítkem napájení v zařízení](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. Baterie je připojená k zadní rovině vašeho zařízení. Identifikujte druhé tlačítko napájení umístěné na baterii. 

    ![Přední rovina zařízení s tlačítkem napájení na baterii](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Připojte jeden konec napájecí šňůry k baterii a druhému do elektrické zásuvky. 

    ![Připojení napájecího kabelu](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    Při spuštění jenom na baterii (baterie není připojené ke zdroji napájení) by se měl přepínač napájení na přední straně a přepínač na baterii přepnout na místo. Když je baterie připojená ke zdroji napájení, měla by se na pozici zapnout jenom tlačítko napájení na přední straně zařízení. 

4. Stisknutím tlačítka napájení v přední rovině zapněte zařízení. 
    
    > [!NOTE]
    > Chcete-li zapnout nebo vypnout napájení zařízení, je nutné stisknutí černého tlačítka myši na tlačítku napájení a potom přepnout tlačítko napájení na pozici na pozici nebo mimo něj. 

5. Pokud na tomto zařízení konfigurujete Wi-Fi, použijte následující diagram kabeláže:

    ![Kabeláž pro Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - Připojte PORT 1 síťového rozhraní 1-GbE k počítači, který se používá ke konfiguraci fyzického zařízení. PORT 1 představuje vyhrazené rozhraní pro správu.


    Pokud pro toto zařízení používáte konfiguraci pevné sítě, použijte následující diagram:
     
    ![Kabeláž pro kabelový](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - Připojte PORT 1 síťového rozhraní 1-GbE k počítači, který se používá ke konfiguraci fyzického zařízení. PORT 1 představuje vyhrazené rozhraní pro správu.
    - Připojte jednu nebo více portů 2, PORT 3 nebo PORT 4 k síti datacenter nebo Internetu.
    
        - Pokud připojujete PORT 2, použijte síťový kabel RJ-45.
        - Pro síťová rozhraní 10 GbE použijte kabely SFP + Copper.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se seznámili s tématy Azure Stack Edge, jako je například:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Zapojení kabeláže zařízení

V dalším kurzu se dozvíte, jak připojit, nastavit a aktivovat zařízení.

> [!div class="nextstepaction"]
> [Připojení a nastavení Azure Stack Edge](./azure-stack-edge-mini-r-deploy-connect.md)
