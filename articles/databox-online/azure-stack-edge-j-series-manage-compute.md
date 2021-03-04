---
title: Správa výpočtů GPU pro procesory Azure Stack Edge Microsoft Docs
description: Popisuje, jak spravovat nastavení hraničních výpočtů, jako jsou triggery, moduly, zobrazit výpočetní konfiguraci, odebrat konfiguraci prostřednictvím Azure Portal v grafickém procesoru Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/27/2021
ms.author: alkohli
ms.openlocfilehash: bd49edcfaca781ac3d36fbf871ec146b32c64ae3
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733410"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Správa výpočetních prostředků na GPU Azure Stack Edge pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Tento článek popisuje, jak spravovat výpočetní prostředky prostřednictvím služby IoT Edge na zařízení GPU Azure Stack Edge pro. Výpočetní prostředky můžete spravovat prostřednictvím Azure Portal nebo prostřednictvím místního webového uživatelského rozhraní. Pomocí Azure Portal můžete spravovat moduly, triggery a konfiguraci IoT Edge a místní webové uživatelské rozhraní pro správu výpočetních nastavení sítě.



## <a name="manage-triggers"></a>Spravovat triggery

Události jsou něco, co se děje v rámci cloudového prostředí nebo na zařízení, na co byste mohli chtít reagovat určitou akcí. Událostí je například vytvoření souboru ve sdílené složce. Události vyvolávají triggery. Pro Azure Stack Edge pro se triggery můžou nacházet v reakci na události souboru nebo plán.

- **Soubor**: tyto triggery jsou v reakci na události souboru, jako je vytváření souboru, úprava souboru.
- **Naplánované**: tyto triggery jsou v reakci na plán, který můžete definovat s počátečním datem, časem spuštění a intervalem opakování.


### <a name="add-a-trigger"></a>Přidání triggeru

Pokud chcete vytvořit Trigger, proveďte následující kroky v Azure Portal.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **IoT Edge**. Přejít na **triggery** a na panelu příkazů vyberte **+ Přidat Trigger** .

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

## <a name="manage-iot-edge-configuration"></a>Správa konfigurace IoT Edge

Pomocí Azure Portal můžete zobrazit konfiguraci výpočtů, odebrat existující výpočetní konfiguraci nebo aktualizovat výpočetní konfiguraci pro synchronizaci přístupových klíčů pro zařízení IoT a zařízení IoT Edge pro Azure Stack Edge pro.

### <a name="view-iot-edge-configuration"></a>Zobrazit konfiguraci IoT Edge

Chcete-li zobrazit konfiguraci IoT Edge pro vaše zařízení, proveďte následující kroky v Azure Portal.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **IoT Edge**. Po povolení služby IoT Edge v zařízení se na stránce Přehled zobrazí zpráva, že služba IoT Edge je spuštěná správně.

    ![Vybrat zobrazení COMPUTE](media/azure-stack-edge-j-series-manage-compute/view-compute-1.png)

2. Přejít na **vlastnosti** a zobrazit konfiguraci IoT Edge na vašem zařízení. Když jste nakonfigurovali výpočetní prostředky, vytvořili jste prostředek IoT Hub. V rámci tohoto IoT Hub prostředku se nakonfigurují zařízení IoT a IoT Edge zařízení. Pro spuštění na IoT Edgeovém zařízení jsou podporovány pouze moduly Linux.

    ![Zobrazit konfiguraci](media/azure-stack-edge-j-series-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>Odebrat službu IoT Edge

Chcete-li odebrat existující konfiguraci IoT Edge pro vaše zařízení, proveďte následující kroky v Azure Portal.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **IoT Edge**. Přejít na **Přehled** a na panelu příkazů vyberte **Odebrat** .

    ![Vyberte odebrat výpočetní prostředky.](media/azure-stack-edge-j-series-manage-compute/remove-compute-1.png)

2. Odeberete-li službu IoT Edge, je akce nevratná a nelze ji vrátit zpět. Moduly a triggery, které jste vytvořili, budou také odstraněny. Pro případ, že budete muset IoT Edge znovu použít, budete muset zařízení znovu nakonfigurovat. Po zobrazení výzvy k potvrzení vyberte **OK**.

    ![Vyberte odebrat výpočetní prostředky 2.](media/azure-stack-edge-j-series-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synchronizace přístupových klíčů zařízení IoT a IoT Edge

Když nakonfigurujete výpočetní prostředí na Azure Stack Edge pro, vytvoří se zařízení IoT a IoT Edge zařízení. Těmto zařízením jsou automaticky přiřazeny symetrické přístupové klíče. Z hlediska zabezpečení je nejvhodnější tyto klíče pravidelně střídat prostřednictvím služby IoT Hub.

Pokud chcete tyto klíče otočit, můžete přejít na službu IoT Hub, kterou jste vytvořili, a vybrat zařízení IoT nebo IoT Edge zařízení. Každé zařízení má primární přístupový klíč a sekundární přístupové klíče. Přiřaďte primární přístupový klíč k sekundárnímu přístupovému klíči a pak znovu vygenerujte primární přístupový klíč.

Pokud jsou vaše zařízení IoT a IoT Edge klíče zařízení otočené, musíte aktualizovat konfiguraci na Azure Stack Edge pro, abyste získali nejnovější přístupové klávesy. Synchronizace pomáhá zařízení získat nejnovější klíče pro zařízení IoT a zařízení IoT Edge. Azure Stack Edge pro používá jenom primární přístupové klíče.

V Azure Portal proveďte následující kroky, které synchronizují přístupové klíče pro vaše zařízení.

1. V Azure Portal přejdete na prostředek Azure Stack Edge a pak přejdete na **IoT Edge COMPUTE**. Přejít na **Přehled** a na panelu příkazů vyberte **aktualizovat konfiguraci** .

    ![Vybrat aktualizaci konfigurace](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-1.png)

2. Po zobrazení výzvy k potvrzení vyberte **Ano** .

    ![Po zobrazení výzvy vyberte Ano.](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-2.png)

3. Po dokončení synchronizace zavřete dialogové okno.

## <a name="change-external-service-ips-for-containers"></a>Změna IP adres externích služeb pro kontejnery

IP adresy externích služeb Kubernetes se používají pro přístup ke službám, které jsou přístupné mimo cluster Kubernetes. Po aktivaci zařízení můžete nastavit nebo upravit IP adresy externích služeb pro kontejnerové úlohy pro vaše zařízení tím, že přistupujete k místnímu uživatelskému rozhraní.


1. V místním uživatelském rozhraní zařízení přejít na **COMPUTE**.
1. Vyberte port, jehož síť je nakonfigurovaná pro výpočetní výkon. V okně, které se otevře, zadejte (nové) nebo upravte (pokud existuje) IP adresy externích služeb Kubernetes. Tyto IP adresy se používají pro všechny služby, které je potřeba zveřejnit mimo cluster Kubernetes. 
    - Pro `edgehub` službu, která běží na vašem zařízení, potřebujete minimálně jednu IP adresu služby, kterou používají IoT Edge moduly. 
    - Pro každý další IoT Edge modul nebo kontejner, který máte v úmyslu nasadit, budete potřebovat IP adresu. 
    - Jedná se o statické a souvislé IP adresy.

    ![Změna IP adres služby Kubernetes](media/azure-stack-edge-j-series-manage-compute/change-service-ips-1.png)

1. Vyberte **Použít**. Po použití IP adres zařízení nepotřebuje restartování ani restartování. Nové IP adresy se projeví okamžitě.


## <a name="next-steps"></a>Další kroky

- Naučte se [řešit potíže s Azure Stack Edge pro](azure-stack-edge-gpu-troubleshoot.md).
