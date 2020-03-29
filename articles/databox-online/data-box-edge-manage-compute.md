---
title: Správa výpočetních prostředků Azure Data Box Edge | Dokumenty společnosti Microsoft
description: Popisuje, jak spravovat nastavení edge výpočetních prostředků, jako je aktivační událost, moduly, zobrazit konfiguraci výpočetních prostředků, odebrat konfiguraci přes portál Azure na Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: a9daf1d59b03d283be999aaab559c6d60f6405dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65953120"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Správa výpočetních prostředků na Azure Data Box Edge

Tento článek popisuje, jak spravovat výpočetní prostředky na Azure Data Box Edge. Výpočetní prostředky můžete spravovat prostřednictvím portálu Azure nebo prostřednictvím místního webového uživatelského prostředí. Na webu Azure Portal můžete spravovat moduly, aktivační události a konfiguraci výpočetních prostředků a místní webové uživatelské rozhraní ke správě nastavení výpočetních prostředků.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Správa aktivačních událostí
> * Správa konfigurace výpočetních prostředků


## <a name="manage-triggers"></a>Správa aktivačních událostí

Události jsou věci, které se dějí ve vašem cloudovém prostředí nebo na vašem zařízení, které můžete chtít provést akci. Například při vytvoření souboru ve sdílené složce se jedná o událost. Aktivační události vyvolávají události. Pro váš data box edge, aktivační události mohou být v reakci na události souboru nebo plán.

- **Soubor**: Tyto aktivační události jsou v reakci na události souboru, jako je vytvoření souboru, změna souboru.
- **Naplánované**: Tyto aktivační události jsou v reakci na plán, který můžete definovat pomocí počátečního data, času zahájení a intervalu opakování.


### <a name="add-a-trigger"></a>Přidání triggeru

Pomocí následujících kroků na webu Azure Portal vytvořte aktivační událost.

1. Na webu Azure Portal přejděte na prostředek Edge datové schránky a přejděte na **edge výpočetní > trigger**. Vyberte **+ Přidat aktivační událost** na panelu příkazů.

    ![Vybrat přidat aktivační událost](media/data-box-edge-manage-compute/add-trigger-1.png)

2. V **okně Přidat spouštěcí** okno zadejte jedinečný název aktivační události.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Vyberte **typ** aktivační události. Zvolte **Soubor,** pokud je aktivační událost v reakci na událost souboru. Vyberte **Naplánované,** pokud chcete, aby aktivační událost začínala v definovaném čase a spouštěla se v zadaném intervalu opakování. V závislosti na vašem výběru je zobrazena jiná sada možností.

    - **Aktivační událost souboru** – Z rozevíracího seznamu vyberte připojenou sdílenou složku. Při aktivaci události souboru v této sdílené složce aktivační událost by vyvolat funkci Azure.

        ![Přidání sdílené složky SMB](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Plánovaná aktivační událost** – Zadejte počáteční datum a čas a interval opakování v hodinách, minutách nebo sekundách. Zadejte také název tématu. Téma vám poskytne flexibilitu pro směrování aktivační události do modulu nasazeného v zařízení.

        Příklad řetězce trasy `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`je: .

        ![Přidání sdílené složky systému souborů NFS](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Chcete-li vytvořit aktivační událost, vyberte **Přidat.** Oznámení ukazuje, že probíhá vytváření aktivační události. Po vytvoření aktivační události se okno aktualizuje tak, aby odráželo novou aktivační událost.
 
    ![Aktualizovaný seznam aktivačních událostí](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Odstranění aktivační události

Pomocí následujících kroků na webu Azure Portal odstraňte aktivační událost.

1. Ze seznamu aktivačních událostí vyberte aktivační událost, kterou chcete odstranit.

    ![Vybrat aktivační událost](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Klepněte pravým tlačítkem myši a vyberte **příkaz Odstranit**.

    ![Vybrat odstranit](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

    ![Potvrzení odstranění](media/data-box-edge-manage-compute/add-trigger-1.png)

Seznam aktivačních událostí aktualizuje tak, aby odrážely odstranění.

## <a name="manage-compute-configuration"></a>Správa konfigurace výpočetních prostředků

Na webu Azure Portal můžete zobrazit konfiguraci výpočetních prostředků, odebrat existující konfiguraci výpočetních prostředků nebo aktualizovat konfiguraci výpočetních prostředků za účelem synchronizace přístupových klíčů pro zařízení IoT a zařízení IoT Edge pro edge datové schránky.

### <a name="view-compute-configuration"></a>Zobrazit konfiguraci výpočetních prostředků

Postupujte podle následujících kroků na webu Azure Portal a zobrazte konfiguraci výpočetních prostředků pro vaše zařízení.

1. Na webu Azure Portal přejděte na prostředek Edge datové schránky a pak přejděte na **Edge compute > Modules**. Na panelu příkazů vyberte **Zobrazit výpočetní výkon.**

    ![Vybrat Zobrazit výpočetní výkon](media/data-box-edge-manage-compute/view-compute-1.png)

2. Poznamenejte si konfiguraci výpočetních prostředků na vašem zařízení. Při konfiguraci výpočetních prostředků jste vytvořili prostředek centra IoT Hub. V rámci tohoto prostředku ioT hubu jsou nakonfigurované zařízení IoT a zařízení IoT Edge. Ke spuštění na zařízení IoT Edge se podporují jenom linuxové moduly.

    ![Zobrazit konfiguraci](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Odebrat konfiguraci výpočetních prostředků

Na webu Azure Portal odeberte existující výpočetní konfiguraci Edge pro vaše zařízení následujícím postupem.

1. Na webu Azure Portal přejděte na prostředek Edge datové schránky a přejděte na **edge výpočetní > Začínáme**. Na panelu příkazů vyberte **Odebrat výpočetní výkon.**

    ![Vybrat Odebrat výpočetní prostředky](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Pokud odeberete konfiguraci výpočetních prostředků, budete muset zařízení překonfigurovat pro případ, že budete muset výpočetní prostředky znovu použít. Po zobrazení výzvy k potvrzení vyberte **možnost Ano**.

    ![Vybrat Odebrat výpočetní prostředky](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synchronizace zařízení IoT a přístupových kláves zařízení IoT Edge

Když nakonfigurujete výpočetní prostředky na okraji datové schránky, vytvoří se zařízení IoT a zařízení IoT Edge. Těmto zařízením jsou automaticky přiřazeny symetrické přístupové klávesy. Jako osvědčený postup zabezpečení se tyto klíče pravidelně střídají prostřednictvím služby IoT Hub.

Chcete-li tyto klíče otočit, můžete přejít na službu Služby IoT Hub, kterou jste vytvořili, a vybrat zařízení IoT nebo zařízení IoT Edge. Každé zařízení má primární přístupový klíč a sekundární přístupové klíče. Přiřaďte primární přístupový klíč k sekundárnímu přístupovému klíči a potom obnovte primární přístupový klíč.

Pokud vaše zařízení IoT a klíče zařízení IoT Edge byly otočené, pak je potřeba aktualizovat konfiguraci na okraji datové schránky, abyste získali nejnovější přístupové klávesy. Synchronizace pomáhá zařízení získat nejnovější klíče pro vaše zařízení IoT a zařízení IoT Edge. Data Box Edge používá pouze primární přístupové klíče.

Na webu Azure Portal prosynchronizujte přístupové klíče pro vaše zařízení následujícím postupem.

1. Na webu Azure Portal přejděte na prostředek Edge datové schránky a přejděte na **edge výpočetní > Začínáme**. Na panelu příkazů vyberte **Aktualizovat konfiguraci.**

    ![Vybrat konfiguraci aktualizace](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Po zobrazení výzvy k potvrzení vyberte možnost **Ano.**

     ![Po zobrazení výzvy vyberte možnost Ano.](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. Po dokončení synchronizace zavřete dialogové okno.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat výpočetní síť Edge přes Azure Portal](data-box-edge-extend-compute-access-modules.md).
