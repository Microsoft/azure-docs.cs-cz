---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: c7be10e14daf3ee769e86d51f648cc6b656a416a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89302124"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Ověření IoT Edge CI/CD pomocí kanálů sestavení a vydání

Chcete-li spustit úlohu sestavení, můžete buď Odeslat potvrzení do úložiště zdrojového kódu, nebo jej ručně aktivovat. V této části ručně aktivujete kanál CI/CD, abyste otestovali, že funguje. Pak ověřte, že nasazení proběhlo úspěšně.

1. V nabídce v levém podokně vyberte **kanály** a otevřete kanál sestavení, který jste vytvořili na začátku tohoto článku.

2. Můžete aktivovat úlohu sestavení v kanálu sestavení výběrem tlačítka **Spustit kanál** v pravém horním rohu.

    ![Ruční aktivace kanálu sestavení pomocí tlačítka spustit kanál](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Zkontrolujte nastavení **kanálu spuštění** . Pak vyberte **Spustit**.

    ![Zadejte možnosti kanálu spuštění a vyberte spustit.](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. Vyberte **úlohu agenta 1** a sledujte průběh běhu. Výběrem úlohy můžete zkontrolovat protokoly výstupu úlohy. 

    ![Kontrola výstupu protokolu úlohy](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. Pokud je kanál sestavení úspěšně dokončen, aktivuje vydání **vývojové** fáze. Úspěšná verze **dev** vytvoří nasazení IoT Edge pro cílení IoT Edge zařízení.

    ![Vydání pro vývoj](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. Kliknutím na **vývojové** fáze zobrazíte protokoly verzí.

    ![Protokoly vydaných verzí](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)