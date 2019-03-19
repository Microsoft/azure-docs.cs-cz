---
title: Správa Azure Data Box Edge výpočetních | Dokumentace Microsoftu
description: Popisuje, jak spravovat nastavení Edge výpočetní prostředky, jako je aktivační událost, moduly, výpočetní prostředky konfigurace zobrazení, odeberte konfiguraci prostřednictvím portálu Azure na hraničních zařízeních váš Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/13/2019
ms.author: alkohli
ms.openlocfilehash: 8128afa7078c396156d2cbffb47effeb7de68a0b
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2019
ms.locfileid: "58005056"
---
# <a name="manage-compute-on-your-azure-data-box-edge"></a>Správa výpočetních služeb v Azure Data Box hranici

Tento článek popisuje, jak spravovat výpočetní prostředí na hranici vaší Azure Data Box. Můžete spravovat tak výpočetní prostředky prostřednictvím portálu Azure portal nebo přes místní webové uživatelské rozhraní. Pomocí webu Azure portal ke správě modulů, aktivační události a výpočetní konfigurace a místního webového uživatelského rozhraní ke správě nastavení výpočetní prostředky.

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Správa aktivační události
> * Spravovat konfiguraci výpočtů


## <a name="manage-triggers"></a>Správa aktivační události

Události jsou možnosti, ke kterým dochází ve vašem cloudovém prostředí nebo na zařízení, která může být vhodné provést určitou akci u. Například když se vytvoří soubor ve sdílené složce, je událost. Aktivační události jsou odpovědi na tyto události. Aktivační události může pomoct provést funkci pokaždé, když se aktivuje událost. Pro hranici pole dat může být aktivační události v reakci na události souboru nebo plánu.

- **Soubor**: Tyto triggery jsou v reakci na události souboru, jako je například vytvoření souboru, úpravy souboru.
- **Naplánované**: Tyto triggery jsou v reakci na plán, který můžete definovat s počátečním datem, počáteční čas a interval opakování.


### <a name="add-a-trigger"></a>Přidat trigger

Pomocí následujících kroků na webu Azure Portal k vytvoření aktivační události.

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **hrany výpočetní > aktivační událost**. Vyberte **+ přidat trigger** na panelu příkazů.

    ![Výběr možnosti Přidat aktivační události](media/data-box-edge-manage-compute/add-trigger-1.png)

2. V **aktivační událost přidat** okno, zadejte jedinečný název pro trigger.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Vyberte **typ** aktivační události. Zvolte **souboru** po triggeru v reakci na události souborů. Vyberte **naplánované** Pokud má aktivační událost spuštění ve stanovenou dobu a spuštění v zadaném intervalu opakování. V závislosti na vašem výběru se zobrazí jinou sadu možností.

    - **Aktivační událost souboru** – z rozevíracího seznamu zvolte připojené sdílené složky. Když soubor událost se aktivuje v této sdílené složce, by měl aktivační událost vyvolat funkci Azure functions.

        ![Přidání sdílené složky SMB](media/data-box-edge-manage-compute/add-file-trigger.png)

    - **Naplánovanou aktivační událost** – zadejte počáteční datum a čas a interval opakování v řádu hodin, minut nebo sekund. Také zadejte název tématu. Téma poskytují flexibilitu pro směrování aktivační události v modulu nasazený na zařízení.

        Příklad postupu řetězce je: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"`.

        ![Přidání sdílené složky systému souborů NFS](media/data-box-edge-manage-compute/add-scheduled-trigger.png)

4. Vyberte **přidat** vytvořit aktivační událost. Oznámení ukazuje, že v průběhu vytváření aktivační události. Po vytvoření aktivační události v okně se aktualizuje a projeví se nová aktivační událost.
 
    ![Seznam spouštěčů aktualizované](media/data-box-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Odstranit aktivační události

Pomocí následujících kroků na webu Azure Portal k odstranění aktivační procedury.

1. Ze seznamu triggerů vyberte trigger, který chcete odstranit.

    ![Vybrat trigger](media/data-box-edge-manage-compute/add-trigger-1.png)

2. Klikněte pravým tlačítkem a pak vyberte **odstranit**.

    ![Vyberte odstranit](media/data-box-edge-manage-compute/add-trigger-1.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

    ![Potvrzení odstranění](media/data-box-edge-manage-compute/add-trigger-1.png)

Seznam se aktualizuje tak, aby odrážely odstranění aktivační události.

## <a name="manage-compute-configuration"></a>Spravovat konfiguraci výpočtů

Pomocí webu Azure portal, chcete-li zobrazit informace o konfiguraci výpočetních, odeberte stávající konfiguraci výpočtů nebo aktualizovat konfiguraci výpočtů synchronizovat klíče pro přístup pro zařízení IoT a zařízení IoT Edge pro vaše Data Box Edge.

### <a name="view-compute-configuration"></a>Výpočetní prostředky konfigurace zobrazení

Pomocí následujících kroků na webu Azure Portal zobrazit informace o konfiguraci výpočetní prostředky pro vaše zařízení.

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **hrany výpočetní > moduly**. Vyberte **zobrazit výpočetní** na panelu příkazů.

    ![Vyberte výpočetní zobrazení](media/data-box-edge-manage-compute/view-compute-1.png)

2. Poznamenejte si konfiguraci výpočtů na vašem zařízení. Při konfiguraci výpočetních vytvoří prostředek služby IoT Hub. V části prostředek služby IoT Hub jsou nakonfigurované zařízení IoT a zařízení IoT Edge. Pro spuštění na zařízení IoT Edge jsou podporovány pouze moduly systému Linux.

    ![Zobrazit konfiguraci](media/data-box-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Odebrat konfiguraci výpočtů

Pomocí následujících kroků na webu Azure Portal odebrat existující konfiguraci hraniční výpočetní prostředky pro vaše zařízení.

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **hrany výpočetní > Začínáme**. Vyberte **odebrat výpočetní** na panelu příkazů.

    ![Zvolte odebrat compute](media/data-box-edge-manage-compute/remove-compute-1.png)

2. Pokud odeberete konfiguraci výpočtů, je potřeba překonfigurovat zařízení v případě, že budete muset znovu použít výpočetní prostředky. Po zobrazení výzvy k potvrzení, vyberte **Ano**.

    ![Zvolte odebrat compute](media/data-box-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>Synchronizovat zařízení IoT a zařízení IoT Edge přístupové klíče

Při konfiguraci výpočetních na hranici pole dat, vytvoří se zařízení IoT a zařízení IoT Edge. Tato zařízení jsou automaticky přiřazeny symetrický přístupové klíče. Z hlediska zabezpečení je nejvhodnější tyto klíče se pravidelně otočí prostřednictvím služby IoT Hub.

Obměna těchto klíčů, můžete přejít do služby IoT Hub, kterou jste vytvořili a vyberte zařízení IoT nebo zařízení IoT Edge. Každé zařízení má primární přístupový klíč a sekundární přístupové klíče. Přiřadit primární přístupový klíč k sekundární přístupový klíč a potom znovu vygenerovat primární přístupový klíč.

Pokud jste otočením vašeho zařízení IoT a klíče zařízení IoT Edge, budete muset aktualizovat konfiguraci na hranici pole dat. Chcete-li získat nejnovější přístupové klíče. Synchronizace pomáhá zařízení získat nejnovější klíče pro zařízení IoT a zařízení IoT Edge. Data Box Edge používá pouze primární přístupové klíče.

Pomocí následujících kroků na webu Azure Portal k synchronizaci přístupových klíčů pro vaše zařízení.

1. Na webu Azure Portal, přejděte na váš prostředek okraj pole Data a potom přejděte ke **hrany výpočetní > Začínáme**. Vyberte **aktualizace konfigurace** na panelu příkazů.

    ![Vyberte položku Aktualizace konfigurace](media/data-box-edge-manage-compute/refresh-configuration-1.png)

2. Vyberte **Ano** po zobrazení výzvy k potvrzení.

     ![Po zobrazení výzvy vyberte Ano](media/data-box-edge-manage-compute/refresh-configuration-2.png)

3. Po dokončení synchronizace zavřete dialogové okno.


## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [spravovat uživatele pomocí webu Azure Portal](data-box-edge-manage-users.md).
