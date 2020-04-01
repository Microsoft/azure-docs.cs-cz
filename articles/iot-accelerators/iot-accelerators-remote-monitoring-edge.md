---
title: Detekce anomálií na hraničních zařízeních v kurzu řešení – Azure | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak sledovat vaše zařízení IoT Edge pomocí akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a812155474b244682613b38b9b9379fa6cdcdcd8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "66117588"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Kurz: Detekce anomálií na hraničních zařízeních pomocí akcelerátoru řešení vzdáleného monitorování

V tomto kurzu nakonfigurujete řešení vzdáleného monitorování tak, aby reagovalo na anomálie zjištěné zařízením IoT Edge. Zařízení IoT Edge umožňují zpracovávat telemetrii na hraničních zařízeních, abyste snížili objem telemetrie odeslané do řešení a povolili rychlejší odezvy na události na zařízeních. Další informace o výhodách zpracování hraničních zařízení najdete v tématu [Co je Azure IoT Edge](../iot-edge/about-iot-edge.md).

Chcete-li zavést zpracování hran se vzdáleným monitorováním, tento kurz používá simulované zařízení konektoru olejového čerpadla. Tento konektor olejového čerpadla je řízen organizací contoso a je připojen k urychlovači řešení vzdáleného monitorování. Senzory na zvedáku olejového čerpadla měří teplotu a tlak. Operátoři společnosti Contoso vědí, že abnormální zvýšení teploty může způsobit zpomalení konektoru olejového čerpadla. Operátoři společnosti Contoso nemusí sledovat teplotu zařízení, když je v normálním rozsahu.

Contoso chce nasadit inteligentní hraniční modul do konektoru olejového čerpadla, který detekuje teplotní anomálie. Jiný hraniční modul odesílá výstrahy do řešení vzdáleného monitorování. Po přijetí výstrahy může operátor společnosti Contoso vyslat technika údržby. Společnost Contoso může také nakonfigurovat automatickou akci, například odeslání e-mailu, ke spuštění, když řešení obdrží výstrahu.

Následující diagram znázorňuje klíčové součásti v ukázkovém scénáři:

![Přehled](media/iot-accelerators-remote-monitoring-edge/overview.png)

V tomto kurzu jste:

>[!div class="checklist"]
> * Přidání zařízení IoT Edge do řešení
> * Vytvoření manifestu Edge
> * Import manifestu jako balíčku, který definuje moduly pro spuštění na zařízení
> * Nasazení balíčku do zařízení IoT Edge
> * Zobrazení upozornění ze zařízení

Na zařízení IoT Edge:

* Modul runtime obdrží balíček a nainstaluje moduly.
* Modul analýzy datového proudu detekuje teplotní anomálie v čerpadle a odesílá příkazy vyřešit problém.
* Modul analýzy datového proudu předává filtrovaná data do akcelerátoru řešení.

Tento kurz používá virtuální počítač Linux jako zařízení IoT Edge. Můžete také nainstalovat okrajový modul pro simulaci zařízení konektoru olejového čerpadla.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Přidání zařízení IoT Edge

Existují dva kroky, jak přidat zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování. V této části se můžete naučit používat:

* Přidejte zařízení IoT Edge na stránku **Průzkumník zařízení** ve webovém uživatelském rozhraní vzdáleného monitorování.
* Nainstalujte runtime IoT Edge do virtuálního počítače s Linuxem (VM).

### <a name="add-an-iot-edge-device-to-your-solution"></a>Přidání zařízení IoT Edge do vašeho řešení

Chcete-li přidat zařízení IoT Edge do akcelerátoru řešení vzdáleného monitorování, přejděte na stránku **Průzkumníkzařízení** ve webovém uživatelském rozhraní a klepněte na tlačítko **+ Nové zařízení**.

V panelu **Nové zařízení** zvolte **zařízení IoT Edge** a zadejte **olejové čerpadlo** jako ID zařízení. Výchozí hodnoty pro ostatní nastavení můžete ponechat. Pak klikněte na **Použít**:

[![Přidání zařízení IoT Edge](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Poznamenejte si připojovací řetězec zařízení, potřebujete jej v další části tohoto kurzu.

Když zaregistrujete zařízení pomocí služby IoT hub v akcelerátoru řešení vzdáleného monitorování, je uvedeno na stránce **Průzkumníkzařízení** ve webovém uživatelském rozhraní:

[![Nové zařízení IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Chcete-li usnadnit správu zařízení IoT Edge v řešení, vytvořte skupinu zařízení a přidejte zařízení IoT Edge:

1. V seznamu na stránce **Průzkumník zařízení** vyberte zařízení **olejové čerpadlo** a klepněte na tlačítko **Úlohy**.

1. Vytvořte úlohu pro přidání značky **IsEdge** do zařízení pomocí následujících nastavení:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Úloha     | Značky  |
    | Název úlohy | Značka AddEdgeTag |
    | Klíč     | IsoilPump |
    | Hodnota   | Ano     |
    | Typ    | Text  |

    [![Přidat značku](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Klepněte na tlačítko **Použít**a potom **na tlačítko Zavřít**.

1. Na stránce **Průzkumník zařízení** klikněte na **Spravovat skupiny zařízení**.

1. Klikněte na **Vytvořit novou skupinu zařízení**. Vytvořte novou skupinu zařízení s následujícím nastavením:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Name (Název)    | Olejová čerpadla |
    | Pole   | Tagy.IsoilPump |
    | Operátor | = Rovná se |
    | Hodnota    | Ano |
    | Typ     | Text |

    [![Vytvořit skupinu zařízení](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Klikněte na **Uložit**.

Zařízení IoT Edge je nyní ve skupině **OilPumps.**

### <a name="install-the-edge-runtime"></a>Instalace runtime Edge

Zařízení Edge vyžaduje, aby byl nainstalován runtime Edge. V tomto kurzu nainstalujete edge runtime v virtuálním počítači Azure Linux k testování scénáře. Následující kroky používají cloudové prostředí Azure při instalaci a konfiguraci virtuálního počítače:

1. Pokud chcete vytvořit virtuální počítač s Linuxem v Azure, spusťte následující příkazy. Můžete použít umístění v blízkosti místa, kde se nacházíte:

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

1. Chcete-li nakonfigurovat runtime Edge pomocí připojovacího řetězce zařízení, spusťte následující příkaz pomocí připojovacího řetězce zařízení, který jste si dříve poznamenali:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Nezapomeňte zahrnout připojovací řetězec do dvojunabídek.

Teď jste nainstalovali a nakonfigurovali runtime IoT Edge na zařízení s Linuxem. Později v tomto kurzu použijete řešení vzdáleného monitorování k nasazení modulů IoT Edge do tohoto zařízení.

## <a name="create-an-edge-manifest"></a>Vytvoření manifestu Edge

Chcete-li simulovat zařízení čerpadla olejové zásuvky, musíte do zařízení Edge přidat následující moduly:

* Modul simulace teploty.
* Azure Stream Analytics detekce anomálií.

Následující kroky ukazují, jak vytvořit manifest nasazení Edge, který obsahuje tyto moduly. Později v tomto kurzu importovat tento manifest jako balíček v akcelerátoru řešení vzdáleného monitorování.

### <a name="create-the-azure-stream-analytics-job"></a>Vytvoření úlohy Azure Stream Analytics

Úlohu Stream Analytics definujete na portálu před jeho zabalením jako moduledge.

1. Na webu Azure Portal vytvořte účet úložiště Azure pomocí výchozích možností ve skupině prostředků **IoTEdgeDevices.** Poznamenejte si jméno, které jste si vybrali.

1. Na webu Azure Portal vytvořte **úlohu Analýzy datových proudů** ve skupině prostředků **IoTEdgeDevices.** Použijte následující hodnoty konfigurace:

    | Možnost | Hodnota |
    | ------ | ----- |
    | Název úlohy | EdgeDeviceJob |
    | Předplatné | Vaše předplatné Azure |
    | Skupina prostředků | Zařízení IoTEdge |
    | Umístění | USA – východ |
    | Hostitelské prostředí | Edge |
    | Jednotky streamování | 1 |

1. Otevřete úlohu **EdgeDeviceJob** Stream Analytics na portálu, klikněte na Vstupy a přidejte vstup datového proudu **Edge Hub** s názvem **telemetrie**.

1. V úloze **EdgeDeviceJob** Stream Analytics na portálu klikněte na **Výstupy** a přidejte výstup **Edge Hub** s názvem **výstup**.

1. V úloze **EdgeDeviceJob** Stream Analytics na portálu klikněte na **Výstupy** a přidejte druhý výstup **Edge Hub** s názvem **výstraha**.

1. V úloze **EdgeDeviceJob** Stream Analytics na portálu klikněte na **Dotaz** a přidejte následující **příkaz select:**

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

1. V úloze **EdgeDeviceJob** Stream Analytics na portálu klikněte na **Nastavení účtu úložiště**. Jako začátek této části přidejte účet úložiště, který jste přidali do skupiny prostředků **IoTEdgeDevices.** Vytvořte nový kontejner s názvem **edgeconfig**.

Následující snímek obrazovky ukazuje uloženou úlohu Stream Analytics:

[![Úloha Stream Analytics](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Nyní jste definovali úlohu Stream Analytics, která se má spouštět na hraničním zařízení. Úloha vypočítá průměrnou teplotu v okně 5 sekund. Úloha také odešle výstrahu, pokud průměrná teplota v okně 3 sekund přesahuje 400.

### <a name="create-the-iot-edge-deployment"></a>Vytvoření nasazení IoT Edge

Dále vytvoříte manifest nasazení IoT Edge, který definuje moduly, které mají být spuštěny na vašem zařízení Edge. V další části importujete tento manifest jako balíček v řešení vzdáleného monitorování.

1. Na webu Azure Portal přejděte do služby IoT hub ve vašem řešení vzdáleného monitorování. Centrum IoT najdete ve skupině prostředků, která má stejný název jako řešení vzdáleného monitorování.

1. V centru IoT klikněte v části **Automatická správa zařízení** na **IoT Edge.** Klikněte **na Přidat nasazení IoT Edge**.

1. Na stránce **Vytvořit název > nasazení a popisek** zadejte název **olejové čerpadlo- zařízení**. Klikněte na **Další**.

1. Na stránce **Vytvořit nasazení > Přidat moduly** klikněte na + **Přidat**. Zvolte **Modul IoT Edge**.

1. V panelu **Vlastní moduly IoT Edge** zadejte **temperatureSensor** jako název a **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** jako identifikátor URI obrázku. Klikněte na **Uložit**.

1. Na stránce **Vytvořit nasazení > přidat moduly** klikněte na + **Přidat** a přidejte druhý modul. Zvolte **Modul Azure Stream Analytics**.

1. V panelu **nasazení Edge** vyberte předplatné a **EdgeDeviceJob,** které jste vytvořili v předchozí části. Klikněte na **Uložit**.

1. Na stránce **Vytvořit nasazení > přidat moduly** klikněte na **další**.

1. Na stránce **Vytvořit nasazení > určit trasy** přidejte následující kód:

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

    Klikněte na **Další**.

1. Na stránce **Vytvořit > zadat metriky** klikněte na **Další**.

1. Na stránce **Vytvořit nasazení > cílová zařízení** zadejte jako prioritu hodnotu 10. Klikněte na **Další**.

1. Na stránce **Vytvořit nasazení > nasazení recenze** klikněte na **Odeslat**:

    [![Zkontrolovat nasazení](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Na hlavní stránce **IoT Edge** klikněte na **Nasazení IoT Edge**. V seznamu nasazení můžete vidět **zařízení olejového čerpadla.**

1. Klikněte na nasazení **zařízení olejového čerpadla** a potom klikněte na **stáhnout manifest IoT Edge**. Uložte soubor jako **olej-čerpadlo device.json** na vhodné místo v místním počítači. Tento soubor potřebujete v další části tohoto kurzu.

Nyní jste vytvořili manifest IoT Edge pro import do řešení vzdáleného monitorování jako balíček. Vývojář obvykle vytvoří moduly IoT Edge a soubor manifestu.

## <a name="import-a-package"></a>Import balíčku

V této části importujete manifest Edge jako balíček v řešení vzdáleného monitorování.

1. Ve webovém uživatelském uživatelském uživatelském uživatelském tlačítkě Vzdálené monitorování přejděte na stránku **Balíčky** a klikněte na **+ Nový balíček**:

    [![Nový balíček](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Na panelu **Nový balíček** zvolte Jako typ balíčku **manifest hrany,** klikněte na **Procházet** a vyhledejte soubor **olejové pumpy device.json** v místním počítači a klikněte na **Nahrát**:

    [![Nahrát balíček](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    Seznam balíčků nyní obsahuje balíček **olej-čerpadlo device.json.**

V další části vytvoříte nasazení, které použije balíček pro vaše zařízení Edge.

## <a name="deploy-a-package"></a>Nasazení balíčku

Nyní jste připraveni nasadit balíček do zařízení.

1. Ve webovém uživatelském uživatelském uživatelském tlačítkě Vzdálené monitorování přejděte na stránku **Nasazení** a klikněte na **+ Nové nasazení**:

    [![Nové nasazení](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. V panelu **Nové nasazení** vytvořte nasazení s následujícím nastavením:

    | Možnost | Hodnota |
    | ------ | ----- |
    | Name (Název)   | Zařízení OilPumpDevices |
    | Typ balíčku | Manifest okrajů |
    | Balíček | olej-čerpadlo-device.json |
    | Skupina zařízení | Olejová čerpadla |
    | Priorita | 10 |

    [![Vytvoření nasazení](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Klikněte na **Použít**.

Musíte počkat několik minut, než se balíček nasadí do vašeho zařízení a telemetrie začne proudit ze zařízení.

[![Nasazení je aktivní](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

Stránka **Nasazení** zobrazuje následující metriky:

* **Cílený** zobrazuje počet zařízení ve skupině zařízení.
* **Použité** zobrazuje počet zařízení, která měla použitý obsah nasazení.
* **Úspěšný** zobrazuje počet zařízení Edge v nasazení hlášení úspěchu z runtime klienta IoT Edge.
* **Neúspěšný** zobrazuje počet zařízení Edge v selhání hlášení nasazení z runtime klienta IoT Edge.

## <a name="monitor-the-device"></a>Sledování zařízení

Telemetrii teploty můžete zobrazit ze zařízení s olejovým čerpadlem ve webovém uživatelském rozhraní vzdáleného monitorování:

1. Přejděte na stránku **Průzkumník zařízení** a vyberte zařízení s olejovým čerpadlem.
1. V části **Telemetrie** na panelu **Podrobnosti o zařízení** klikněte na **Teplota**:

    [![Zobrazení telemetrických dat](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Můžete vidět, jak teplota stoupá, dokud nedosáhne prahové hodnoty. Modul Stream Analytics Edge detekuje, kdy teplota dosáhne této prahové hodnoty, a odešle do zařízení příkaz, aby okamžitě snížil teplotu. Veškeré toto zpracování probíhá na zařízení bez komunikace s cloudem.

Pokud chcete operátorům upozorňovat, když bylo dosaženo prahové hodnoty, můžete vytvořit pravidlo v uživatelském uživatelském uživatelském zařízení vzdáleného monitorování:

1. Přejděte na stránku **Pravidla** a klepněte na tlačítko **+ Nové pravidlo**.
1. Vytvořte nové pravidlo s následujícím nastavením:

    | Možnost | Hodnota |
    | ------ | ----- |
    | Název pravidla | Teplota olejového čerpadla |
    | Popis | Teplota olejového čerpadla překročila 300 |
    | Skupina zařízení | Olejová čerpadla |
    | Výpočet | Okamžitý |
    | Pole | teplota |
    | Operátor | > |
    | Hodnota | 300 |
    | Úroveň závažnosti | Informace |

    [![Vytvořit pravidlo](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Klikněte na **Použít**.

1. Přejděte na stránku **Řídicí panel.** Výstraha se zobrazí v panelu **Upozornění,** když teplota v zařízení **olejového čerpadla** přejde na více než 300.

## <a name="next-steps"></a>Další kroky

Tento kurz vám ukázal, jak přidat a nakonfigurovat zařízení IoT Edge v akcelerátoru řešení vzdáleného monitorování. Další informace o práci s balíčky IoT Edge v řešení vzdáleného monitorování najdete v následujících návodech:

> [!div class="nextstepaction"]
> [Import balíčku IoT Edge do akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-import-edge-package.md)

Další informace o instalaci runtime IoT Edge najdete [v tématu Instalace runtime Azure IoT Edge na Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md).

Další informace o Azure Stream Analytics na zařízeních Edge najdete v tématu [Nasazení Azure Stream Analytics jako modulu IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md).
