---
title: Správa výpočtů GPU pro procesory Azure Stack Edge Microsoft Docs
description: Popisuje, jak spravovat nastavení hraničních výpočtů, jako jsou triggery, moduly, zobrazit výpočetní konfiguraci, odebrat konfiguraci prostřednictvím Azure Portal v grafickém procesoru Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 58e199cc3c09e6b4ff333fb53c047598b1ec9b5f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890590"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Správa výpočetních prostředků na GPU Azure Stack Edge pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak spravovat výpočetní prostředky na Azure Stack Edge pro. Výpočetní prostředky můžete spravovat prostřednictvím Azure Portal nebo prostřednictvím místního webového uživatelského rozhraní. Pomocí Azure Portal můžete spravovat moduly, triggery a výpočetní konfiguraci a místní webové uživatelské rozhraní pro správu výpočetních nastavení.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Spravovat triggery
> * Spravovat výpočetní konfiguraci


## <a name="manage-triggers"></a>Spravovat triggery

Události jsou něco, co se děje v rámci cloudového prostředí nebo na zařízení, na co byste mohli chtít reagovat určitou akcí. Událostí je například vytvoření souboru ve sdílené složce. Události vyvolávají triggery. Pro Azure Stack Edge pro se triggery můžou nacházet v reakci na události souboru nebo plán.

- **Soubor**: tyto triggery jsou v reakci na události souboru, jako je vytváření souboru, úprava souboru.
- **Naplánované**: tyto triggery jsou v reakci na plán, který můžete definovat s počátečním datem, časem spuštění a intervalem opakování.


### <a name="add-a-trigger"></a>Přidání triggeru

Pokud chcete vytvořit Trigger, proveďte následující kroky v Azure Portal.

1. V Azure Portal přejdete do svého prostředku Azure Stack Edge a pak přejdete na **> Trigger COMPUTE COMPUTE pro Edge**. Na panelu příkazů vyberte **+ Přidat aktivační událost** .

    ![Vyberte Přidat aktivační událost.](media/azure-stack-edge-j-series-manage-compute/add-trigger-1m.png)

2. V okně **Přidat aktivační událost** zadejte jedinečný název aktivační události.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Vyberte **typ** triggeru. Vyberte **soubor** , když je Trigger v reakci na událost souboru. Vyberte možnost **naplánováno** , pokud chcete, aby se aktivační událost spouštěla v definovaném čase a běžela v zadaném intervalu opakování. V závislosti na výběru se zobrazí jiná sada možností.

    - **Aktivační událost souboru** – v rozevíracím seznamu vyberte připojenou sdílenou složku. Při vyvolání události souboru v této sdílené složce vyvolá Trigger funkci Azure Function.

        ![Přidání sdílené složky SMB](media/azure-stack-edge-j-series-manage-compute/add-file-trigger.png)

    - **Naplánované triggery** – zadejte počáteční datum a čas a interval opakování v hodinách, minutách nebo sekundách. Zadejte také název tématu. Téma vám poskytne flexibilitu při směrování triggeru na modul nasazený na zařízení.

        Vzorový řetězec trasy: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` .

        ![Přidání sdílené složky systému souborů NFS](media/azure-stack-edge-j-series-manage-compute/add-scheduled-trigger.png)

4. Vyberte **Přidat** a vytvořte Trigger. Oznámení ukazuje, že probíhá vytváření aktivační události. Po vytvoření triggeru se okno aktualizuje a projeví se nová aktivační událost.
 
    ![Seznam aktualizovaných triggerů](media/azure-stack-edge-j-series-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Odstranění triggeru

Chcete-li odstranit aktivační událost, proveďte následující kroky v Azure Portal.

1. V seznamu triggerů vyberte aktivační událost, kterou chcete odstranit.

    ![Vybrat aktivační událost](media/azure-stack-edge-j-series-manage-compute/delete-trigger-1.png)

2. Klikněte pravým tlačítkem a pak vyberte **Odstranit**.

    ![Vybrat odstranit](media/azure-stack-edge-j-series-manage-compute/delete-trigger-2.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

    ![Potvrzení odstranění](media/azure-stack-edge-j-series-manage-compute/delete-trigger-3.png)

Seznam triggerů se aktualizuje tak, aby odrážel odstranění.

## <a name="manage-compute-configuration"></a>Spravovat výpočetní konfiguraci

Pomocí Azure Portal můžete zobrazit konfiguraci výpočtů, odebrat existující výpočetní konfiguraci nebo aktualizovat výpočetní konfiguraci pro synchronizaci přístupových klíčů pro zařízení IoT a zařízení IoT Edge pro Azure Stack Edge pro.

### <a name="view-compute-configuration"></a>Zobrazit konfiguraci výpočtů

Proveďte následující kroky v Azure Portal, abyste zobrazili výpočetní konfiguraci pro vaše zařízení.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **Edge compute > moduly**. Na panelu příkazů vyberte **zobrazení COMPUTE** .

    ![Vybrat zobrazení COMPUTE](media/azure-stack-edge-j-series-manage-compute/view-compute-1.png)

2. Poznamenejte si konfiguraci výpočtů na svém zařízení. Když jste nakonfigurovali výpočetní prostředky, vytvořili jste prostředek IoT Hub. V rámci tohoto IoT Hub prostředku se nakonfigurují zařízení IoT a IoT Edge zařízení. Pro spuštění na IoT Edgeovém zařízení jsou podporovány pouze moduly Linux.

    ![Zobrazit konfiguraci](media/azure-stack-edge-j-series-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Odebrat výpočetní konfiguraci

Proveďte následující kroky v Azure Portal k odebrání existující konfigurace hraničních výpočtů pro vaše zařízení.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **Edge compute > Začínáme**. Na panelu příkazů vyberte **Odebrat výpočetní** prostředky.

    ![Vyberte odebrat výpočetní prostředky.](media/azure-stack-edge-j-series-manage-compute/remove-compute-1.png)

2. Pokud odeberete konfiguraci výpočtů, budete muset zařízení znovu nakonfigurovat pro případ, že budete potřebovat výpočetní výkon znovu použít. Po zobrazení výzvy k potvrzení vyberte **Ano**.

    ![Vyberte odebrat výpočetní prostředky.](media/azure-stack-edge-j-series-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synchronizace přístupových klíčů zařízení IoT a IoT Edge

Když nakonfigurujete výpočetní prostředí na Azure Stack Edge pro, vytvoří se zařízení IoT a IoT Edge zařízení. Těmto zařízením jsou automaticky přiřazeny symetrické přístupové klíče. Z hlediska zabezpečení je nejvhodnější tyto klíče pravidelně střídat prostřednictvím služby IoT Hub.

Pokud chcete tyto klíče otočit, můžete přejít na službu IoT Hub, kterou jste vytvořili, a vybrat zařízení IoT nebo IoT Edge zařízení. Každé zařízení má primární přístupový klíč a sekundární přístupové klíče. Přiřaďte primární přístupový klíč k sekundárnímu přístupovému klíči a pak znovu vygenerujte primární přístupový klíč.

Pokud jsou vaše zařízení IoT a IoT Edge klíče zařízení otočené, musíte aktualizovat konfiguraci na Azure Stack Edge pro, abyste získali nejnovější přístupové klávesy. Synchronizace pomáhá zařízení získat nejnovější klíče pro zařízení IoT a zařízení IoT Edge. Azure Stack Edge pro používá jenom primární přístupové klíče.

V Azure Portal proveďte následující kroky, které synchronizují přístupové klíče pro vaše zařízení.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **Edge compute > Začínáme**. Na panelu příkazů vyberte **aktualizovat konfiguraci** .

    ![Vybrat aktualizaci konfigurace](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-1.png)

2. Po zobrazení výzvy k potvrzení vyberte **Ano** .

    ![Po zobrazení výzvy vyberte Ano.](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-2.png)

3. Po dokončení synchronizace zavřete dialogové okno.

## <a name="next-steps"></a>Další kroky

- Naučte se [řešit potíže s Azure Stack Edge pro](azure-stack-edge-gpu-troubleshoot.md).
