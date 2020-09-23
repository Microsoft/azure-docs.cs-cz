---
title: Zjištění anomálií na hraničních zařízeních v kurzu řešení – Azure | Microsoft Docs
description: V tomto kurzu se naučíte monitorovat zařízení IoT Edge pomocí akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 43ba14845765230b9a54c2b34dbc7ccd53af950b
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90969992"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Kurz: zjištění anomálií na hraničních zařízeních pomocí akcelerátoru řešení vzdáleného monitorování

V tomto kurzu nakonfigurujete řešení vzdáleného monitorování tak, aby odpovídalo anomáliím zjištěným IoT Edge zařízením. IoT Edge zařízení umožňují zpracovávat telemetrie na hranici, aby se snížil objem telemetrie odeslaný do řešení a povolovaly rychlejší odezvy na události v zařízeních. Další informace o výhodách zpracování Edge najdete v tématu [co je Azure IoT Edge](../iot-edge/about-iot-edge.md).

K zavedení hraničního zpracování se vzdáleným monitorováním používá tento kurz simulované zařízení s olejovou pumpou. Tato zdířka čerpací stanice je spravovaná organizací s názvem contoso a je připojená k akcelerátoru řešení vzdáleného monitorování. Senzory měření konektoru olejového čerpadla na teplotu a tlak. Operátoři ve společnosti Contoso ví, že abnormální nárůst teploty může způsobit zpomalení konektoru čerpacího čerpadla. Operátory ve společnosti Contoso nepotřebují monitorovat teplotu zařízení, když se nachází v normálním rozsahu.

Společnost Contoso chce nasadit modul Intelligent Edge do zásuvky čerpací stanice, která detekuje anomálie teploty. Jiný modul Edge odesílá výstrahy do řešení vzdáleného monitorování. Při přijetí výstrahy může operátor společnosti Contoso odeslat technik údržby. Společnost Contoso může také nakonfigurovat automatizovanou akci, například odeslání e-mailu, která se spustí, když řešení obdrží výstrahu.

Následující diagram znázorňuje klíčové komponenty v scénáři kurzu:

![Diagram zobrazuje zdířka čerpacího čerpadla připojeného k I výstupům modulu Stream Analytics v zařízení I o Edge pro telemetrii a příkazy. Filtrovaná telemetrie přechází na hraniční zařízení I o T v akcelerátoru řešení vzdáleného monitorování v cloudu. Cloud také obsahuje nasazení a balíček. Nasazení nasadí do zařízení modul runtime I o T Edge.](media/iot-accelerators-remote-monitoring-edge/overview.png)

V tomto kurzu jste:

>[!div class="checklist"]
> * Přidání zařízení IoT Edge do řešení
> * Vytvoření manifestu Edge
> * Importujte manifest jako balíček definující moduly, které se mají na zařízení spouštět.
> * Nasazení balíčku do zařízení IoT Edge
> * Zobrazit výstrahy ze zařízení

Na IoT Edge zařízení:

* Modul runtime přijme balíček a nainstaluje moduly.
* Modul Stream Analytics detekuje anomálie teploty v pumpe a odesílá příkazy k vyřešení problému.
* Modul Stream Analytics přesměruje filtrovaná data do akcelerátoru řešení.

Tento kurz používá virtuální počítač Linux jako zařízení IoT Edge. Nainstalujete také hraniční modul pro simulaci čerpacího zařízení olejového čerpadla.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Přidání zařízení IoT Edge

Existují dva kroky, jak přidat zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování. V této části se dozvíte, jak používat:

* Přidejte zařízení IoT Edge na stránce **Device Explorer** ve webovém uživatelském rozhraní vzdáleného monitorování.
* Nainstalujte modul runtime IoT Edge na virtuálním počítači se systémem Linux (VM).

### <a name="add-an-iot-edge-device-to-your-solution"></a>Přidání zařízení IoT Edge do řešení

Pokud chcete přidat zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování, přejděte na stránku **Device Explorer** ve webovém uživatelském rozhraní a klikněte na **+ nové zařízení**.

Na panelu **nové zařízení** vyberte **IoT Edge zařízení** a jako ID zařízení zadejte **olejovou pumpi** . Můžete ponechat výchozí hodnoty pro ostatní nastavení. Pak klikněte na **Použít**:

[![Přidat IoT Edge zařízení](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Poznamenejte si připojovací řetězec zařízení, budete ho potřebovat v další části tohoto kurzu.

Když zaregistrujete zařízení ve službě IoT Hub v akcelerátoru řešení vzdáleného monitorování, zobrazí se na stránce **Device Explorer** ve webovém uživatelském rozhraní:

[![Nové zařízení IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Aby bylo snazší spravovat IoT Edge zařízení v řešení, vytvořte skupinu zařízení a přidejte IoT Edge zařízení:

1. V seznamu na stránce **Device Explorer** vyberte zařízení **ropného čerpadla** a pak klikněte na **úlohy**.

1. Vytvořte úlohu pro přidání značky the **Edge** do zařízení pomocí následujících nastavení:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Úloha     | Značky  |
    | Název úlohy | AddEdgeTag |
    | Klíč     | IsOilPump |
    | Hodnota   | Y     |
    | Typ    | Text  |

    [![Přidat značku](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Klikněte na **použít**a pak na **Zavřít**.

1. Na stránce **Device Explorer** klikněte na možnost **Spravovat skupiny zařízení**.

1. Klikněte na **vytvořit novou skupinu zařízení**. Vytvořte novou skupinu zařízení s následujícím nastavením:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název    | OilPumps |
    | Pole   | Tags. IsOilPump |
    | Operátor | = Equals |
    | Hodnota    | Y |
    | Typ     | Text |

    [![Vytvořit skupinu zařízení](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Klikněte na **Uložit**.

IoT Edge zařízení je teď ve skupině **OilPumps** .

### <a name="install-the-edge-runtime"></a>Instalace modulu runtime Edge

Hraniční zařízení vyžaduje instalaci hraničního modulu runtime. V tomto kurzu nainstalujete modul runtime Edge do virtuálního počítače Azure Linux a otestujete scénář. Následující postup použijte v části instalace a konfigurace virtuálního počítače Azure Cloud Shell:

1. Pokud chcete vytvořit virtuální počítač se systémem Linux v Azure, spusťte následující příkazy. Můžete použít umístění blízko, kde jste:

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Pokud chcete nakonfigurovat modul runtime Edge pomocí připojovacího řetězce zařízení, spusťte následující příkaz pomocí připojovacího řetězce zařízení, který jste si poznamenali dříve:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Nezapomeňte přidat připojovací řetězec do dvojitých uvozovek.

Nyní jste nainstalovali a nakonfigurovali IoT Edge runtime na zařízení se systémem Linux. Později v tomto kurzu nasadíte IoT Edge moduly do tohoto zařízení pomocí řešení vzdáleného monitorování.

## <a name="create-an-edge-manifest"></a>Vytvoření manifestu Edge

K simulaci čerpacího zařízení v oleji musíte do hraničního zařízení přidat následující moduly:

* Modul simulace teploty
* Azure Stream Analytics detekci anomálií.

Následující kroky ukazují, jak vytvořit manifest nasazení Edge, který obsahuje tyto moduly. Později v tomto kurzu naimportujete tento manifest jako balíček v akcelerátoru řešení vzdáleného monitorování.

### <a name="create-the-azure-stream-analytics-job"></a>Vytvoření úlohy Azure Stream Analytics

Úlohu Stream Analytics definujete na portálu před jejím zabalením jako modul Edge.

1. V Azure Portal vytvořte účet úložiště Azure pomocí výchozích možností ve skupině prostředků **IoTEdgeDevices** . Poznamenejte si název, který jste zvolili.

1. V Azure Portal vytvořte **úlohu Stream Analytics** ve skupině prostředků **IoTEdgeDevices** . Použijte následující hodnoty konfigurace:

    | Možnost | Hodnota |
    | ------ | ----- |
    | Název úlohy | EdgeDeviceJob |
    | Předplatné | Vaše předplatné Azure |
    | Skupina prostředků | IoTEdgeDevices |
    | Umístění | USA – východ |
    | Hostitelské prostředí | Edge |
    | Jednotky streamování | 1 |

1. Na portálu otevřete úlohu **EdgeDeviceJob** Stream Analytics, klikněte na vstupy a přidejte vstup datového proudu **centra Edge** s názvem **telemetrie**.

1. V úloze **EdgeDeviceJob** Stream Analytics na portálu klikněte na možnost **výstupy** a přidejte výstup **hraničního centra** s názvem **Output**.

1. V úloze **EdgeDeviceJob** Stream Analytics na portálu klikněte na **výstupy** a přidejte další výstup **centra hran** s názvem **Alert**.

1. V úloze **EdgeDeviceJob** Stream Analytics na portálu klikněte na **dotaz** a přidejte následující příkaz **Select** :

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. V úloze **EdgeDeviceJob** Stream Analytics na portálu klikněte na **Nastavení účtu úložiště**. Jako začátek této části přidejte účet úložiště, který jste přidali do skupiny prostředků **IoTEdgeDevices** . Vytvořte nový kontejner s názvem **edgeconfig**.

Následující snímek obrazovky ukazuje uloženou úlohu Stream Analytics:

[![Stream Analytics úlohy](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Nyní jste definovali úlohu Stream Analytics, která se má spustit na hraničním zařízení. Úloha vypočítá průměrnou teplotu v intervalu 5 sekund. Tato úloha také pošle výstrahu v případě, že průměrná teplota v rámci 3 druhého okna překročí 400.

### <a name="create-the-iot-edge-deployment"></a>Vytvoření nasazení IoT Edge

Dále vytvoříte manifest nasazení IoT Edge, který definuje moduly, které mají být spuštěny na hraničním zařízení. V další části importujete tento manifest jako balíček v řešení vzdáleného monitorování.

1. V Azure Portal přejděte do služby IoT Hub v řešení vzdáleného monitorování. Centrum IoT můžete najít ve skupině prostředků, která má stejný název jako řešení vzdáleného monitorování.

1. Ve službě IoT Hub klikněte na **IoT Edge** v části **Automatická správa zařízení** . Klikněte na  **přidat IoT Edge nasazení**.

1. Na stránce **vytvořit > název a popisek pro nasazení** zadejte název **oleje – pumpa zařízení**. Klikněte na **Next** (Další).

1. Na stránce **vytvořit nasazení > přidat moduly** klikněte na **+ Přidat**. Vyberte **modul IoT Edge**.

1. Na panelu **IoT Edge vlastní moduly** zadejte **senzor teploty** jako název a **asaedgedockerhubtest/ASA-Edge-test-Module: senzor-AD-Linux-amd64** jako identifikátor URI image. Klikněte na **Uložit**.

1. Na stránce **vytvořit nasazení > přidat moduly** klikněte na **+ Přidat** a přidejte druhý modul. Vyberte **modul Azure Stream Analytics**.

1. Na panelu **nasazení Edge** vyberte vaše předplatné a **EdgeDeviceJob** , které jste vytvořili v předchozí části. Klikněte na **Uložit**.

1. Na stránce **vytvořit nasazení > přidat moduly** klikněte na **Další**.

1. Na stránce **vytvořit nasazení > zadat trasy** přidejte následující kód:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Tento kód směruje výstup z modulu Stream Analytics do správných umístění.

    Klikněte na **Next** (Další).

1. Na stránce **vytvořit nasazení > určit metriky** klikněte na **Další**.

1. Na stránce **vytvořit nasazení > cílová zařízení** zadejte jako prioritu 10. Klikněte na **Next** (Další).

1. Na stránce **vytvořit nasazení > kontrola nasazení** klikněte na **Odeslat**:

    [![Zkontrolovat nasazení](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Na hlavní **IoT Edge** stránce klikněte na **IoT Edge nasazení**. V seznamu nasazení si můžete prohlédnout **zařízení v olejovém pumpě** .

1. Klikněte na kartu **olej-pumpa zařízení** a pak klikněte na **Stáhnout IoT Edge manifest**. Uložte soubor jako **oil-pump-device.js** do vhodného umístění v místním počítači. Tento soubor budete potřebovat v další části tohoto kurzu.

Nyní jste vytvořili manifest IoT Edge pro import do řešení vzdáleného monitorování jako balíček. Vývojář obvykle vytváří IoT Edge moduly a soubor manifestu.

## <a name="import-a-package"></a>Importovat balíček

V této části importujete manifest Edge jako balíček do řešení vzdáleného monitorování.

1. Ve webovém uživatelském rozhraní vzdáleného monitorování přejděte na stránku **balíčky** a klikněte na **+ nový balíček**:

    [![Nový balíček](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Na panelu **nový balíček** jako typ balíčku vyberte **manifest Edge** , klikněte na **Procházet** , vyhledejte **oil-pump-device.jsv** souboru na místním počítači a klikněte na **nahrát**:

    [![Nahrát balíček](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    Seznam balíčků teď obsahuje **oil-pump-device.jsv** balíčku.

V další části vytvoříte nasazení, které balíček aplikuje na hraniční zařízení.

## <a name="deploy-a-package"></a>Nasazení balíčku

Teď jste připraveni nasadit balíček do svého zařízení.

1. Ve webovém uživatelském rozhraní vzdáleného monitorování přejděte na stránku **nasazení** a klikněte na **+ nové nasazení**:

    [![Nové nasazení](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Na **novém panelu nasazení** vytvořte nasazení s následujícími nastaveními:

    | Možnost | Hodnota |
    | ------ | ----- |
    | Název   | OilPumpDevices |
    | Typ balíčku | Hraniční manifest |
    | Balíček | oil-pump-device.jsna |
    | Skupina zařízení | OilPumps |
    | Priorita | 10 |

    [![Vytvoření nasazení](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Klikněte na **Použít**.

Než začnete s nasazením balíčku do vašeho zařízení, musíte počkat několik minut, než se zahájí tok ze zařízení.

[![Nasazení je aktivní](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

Na stránce **nasazení** se zobrazují následující metriky:

* **Cílová** hodnota zobrazuje počet zařízení ve skupině zařízení.
* **Použito** : zobrazuje počet zařízení s použitým obsahem nasazení.
* **Úspěšně** se zobrazuje počet hraničních zařízení v průběhu generování sestav nasazení z modulu runtime klienta IoT Edge.
* **Neúspěšné** : zobrazuje počet hraničních zařízení v selhání hlášení nasazení z modulu runtime klienta IoT Edge.

## <a name="monitor-the-device"></a>Monitorování zařízení

V rámci webového uživatelského rozhraní vzdáleného monitorování můžete zobrazit telemetrii o teplotě z čerpacího zařízení v oleji:

1. Přejděte na stránku **Device Explorer** a vyberte zařízení ropného čerpadla.
1. V části **telemetrie** na panelu **Podrobnosti o zařízení** klikněte na tlačítko **teplota**:

    [![Zobrazení telemetrických dat](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Můžete vidět, jak se teplota zvyšuje, dokud nedosáhne prahové hodnoty. Modul Stream Analytics Edge detekuje, když teplota dosáhne této prahové hodnoty, a pošle příkaz do zařízení, aby se teplota okamžitě snížila. K tomuto zpracování dochází v zařízení bez komunikace s cloudem.

Pokud chcete po dosažení prahové hodnoty oznamovat operátory, můžete vytvořit pravidlo ve webovém uživatelském rozhraní vzdáleného monitorování:

1. Přejděte na stránku **pravidla** a klikněte na **+ nové pravidlo**.
1. Vytvořte nové pravidlo s následujícím nastavením:

    | Možnost | Hodnota |
    | ------ | ----- |
    | Název pravidla | Teplota olejového čerpadla |
    | Description | Teplota olejového čerpadla překročila 300. |
    | Skupina zařízení | OilPumps |
    | Výpočet | Okamžitě |
    | Pole | teplota |
    | Operátor | > |
    | Hodnota | 300 |
    | Úroveň závažnosti | Informace |

    [![Vytvořit pravidlo](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Klikněte na **Použít**.

1. Přejděte na stránku **řídicí panel** . Výstraha se zobrazí na panelu **výstrahy** , když teplota v **olejovém** zařízení překročí 300.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste si ukázali, jak přidat a nakonfigurovat zařízení IoT Edge v akcelerátoru řešení vzdáleného monitorování. Další informace o práci s balíčky IoT Edge v řešení vzdáleného monitorování najdete v následujících příručkách:

> [!div class="nextstepaction"]
> [Import balíčku IoT Edge do akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-import-edge-package.md)

Další informace o instalaci modulu runtime IoT Edge najdete v tématu [Instalace modulu runtime Azure IoT Edge v systému Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md).

Další informace o Azure Stream Analytics na hraničních zařízeních najdete v tématu [nasazení Azure Stream Analytics jako IoT Edge modul](../iot-edge/tutorial-deploy-stream-analytics.md).
