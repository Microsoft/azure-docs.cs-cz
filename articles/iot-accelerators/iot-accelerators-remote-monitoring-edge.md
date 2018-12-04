---
title: Zjišťovat anomálie na hraničních zařízeních v kurzu řešení Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak sledovat zařízení IoT Edge pomocí akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 51c19447e115426bd39d39fedc86193c8f091df1
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52843304"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Kurz: Detekci anomálií na hraničních zařízeních s akcelerátor řešení vzdálené monitorování

V tomto kurzu nakonfigurujete řešení vzdáleného monitorování reakce na anomálie zjištěných zařízení IoT Edge. Zařízení IoT Edge umožňuje zpracování telemetrických dat na hraničních zařízeních snížit objem telemetrická data odesílaná do řešení a umožňuje provádět rychlejší reakce na události na zařízeních. Další informace o výhodách zpracování hraničních zařízení najdete v tématu [co je Azure IoT Edge](../iot-edge/about-iot-edge.md).

Zavést edge zpracování pomocí vzdáleného monitorování, tento kurz používá simulované ropy čerpadlo jack zařízení. Tento výstup ropy čerpadlo spravuje organizace názvem Contoso a je připojen k akcelerátoru řešení vzdáleného monitorování. Senzorů v konektoru čerpadlo ropy měření teploty a tlaku. Operátory ve společnosti Contoso vědět, že abnormální nárůst teploty může způsobit konektoru ropy čerpadlo zpomalovat. Operátory ve společnosti Contoso nemusíte sledovat teploty zařízení, když je v dosahu normální.

Contoso chce nasadit do konektoru čerpadlo ropy, který detekuje anomálie teploty modul inteligentních hraničních zařízení. Jiný modul edge odesílá výstrahy k řešení vzdáleného monitorování. Po přijetí upozornění operátor společnosti Contoso provést odeslání technika údržby. Contoso také nakonfigurovat automatizovanou akci, jako je odeslání e-mailu, i když toto řešení se zobrazí upozornění.

Následující diagram ukazuje klíčové součásti v scénář tohoto kurzu:

![Přehled](media/iot-accelerators-remote-monitoring-edge/overview.png)

V tomto kurzu se naučíte:

>[!div class="checklist"]
> * Do řešení přidat zařízení IoT Edge
> * Vytvořte Edge manifest
> * Importovat do manifestu jako balíček, který definuje moduly, které chcete spustit na zařízení
> * Nasadit balíček do zařízení IoT Edge
> * Zobrazení výstrah ze zařízení

Na zařízení IoT Edge:

* Modul runtime, obdrží balíček a nainstaluje moduly.
* Modul stream analytics detekuje anomálie teploty v čerpadlo a odesílá příkazy vyřešení problému.
* Modul stream analytics předává filtrovaná data k akcelerátoru řešení.

Tento kurz používá virtuální počítač s Linuxem jako zařízení IoT Edge. Je také nainstalovat modul edge simulovat zařízení ropy čerpadlo konektoru.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Přidání zařízení IoT Edge

Existují dva kroky pro přidání zařízení IoT Edge na akcelerátor řešení vzdálené monitorování. V této části se dozvíte, jak používat:

* Přidat zařízení IoT Edge na **zařízení** stránky ve vzdálené monitorování webového uživatelského rozhraní.
* Nainstalujte modul runtime IoT Edge v Linuxu virtuálních počítačů (VM).

### <a name="add-an-iot-edge-device-to-your-solution"></a>Do řešení přidat zařízení IoT Edge

Přidání zařízení IoT Edge na akcelerátor řešení vzdálené monitorování, přejděte **zařízení** stránku ve webovém uživatelském rozhraní a klikněte na tlačítko **+ nové zařízení**.

V **nové zařízení** panelu, vyberte **zařízení IoT Edge** a zadejte **ropy čerpadlo** jako ID zařízení. Můžete ponechat výchozí hodnoty pro ostatní nastavení. Pak klikněte na **Použít**:

[![Přidat zařízení IoT Edge](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Poznamenejte si připojovací řetězec zařízení, budete potřebovat v další části tohoto kurzu.

Když registrujete zařízení prostřednictvím služby IoT hub v akcelerátoru řešení vzdáleného monitorování, nejsou na **zařízení** stránku ve webovém uživatelském rozhraní:

[![Nové zařízení IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Aby bylo snazší spravovat zařízení IoT Edge v řešení, vytvořte skupinu zařízení a přidání zařízení IoT Edge:

1. Vyberte **ropy čerpadlo** zařízení v seznamu na **zařízení** stránce a potom klikněte na tlačítko **úlohy**.

1. Vytvoření úlohy pro přidání **IsEdge** značka, které zařízení pomocí následujících nastavení:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Úloha     | Značky  |
    | Název úlohy | AddEdgeTag |
    | Klíč     | IsOilPump |
    | Hodnota   | Ano     |
    | Typ    | Text  |

    [![Přidat značku](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Klikněte na tlačítko **použít**, pak **Zavřít**.

1. Na **zařízení** klikněte na **Správa skupin zařízení**.

1. Klikněte na tlačítko **vytvořit novou skupinu zařízení**. Vytvořte novou skupinu zařízení s následujícími nastaveními:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název    | OilPumps |
    | Pole   | Tags.IsOilPump |
    | Operátor | = Rovná se |
    | Hodnota    | Ano |
    | Typ     | Text |

    [![Vytvořte skupinu zařízení](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Klikněte na **Uložit**.

Můžete zařízení IoT Edge je teď v **OilPumps** skupiny.

### <a name="install-the-edge-runtime"></a>Instalace modulu runtime Edge

Hraniční zařízení vyžaduje modulu runtime Edge k instalaci. V tomto kurzu nainstalujete modulu runtime Edge ve virtuálním počítači Azure s Linuxem k otestování tohoto scénáře. Následujícím postupem použít Azure cloud shell v instalaci a konfiguraci virtuálního počítače:

1. K vytvoření virtuálního počítače s Linuxem v Azure, spusťte následující příkazy. Můžete použít umístění blízko nacházíte:

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

1. Konfigurace modulu runtime Edge s připojovacím řetězcem zařízení, spusťte následující příkaz pomocí připojovacího řetězce zařízení, které jste si poznamenali dříve:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Nezapomeňte zahrnout připojovací řetězec do uvozovek.

Právě jste nainstalovat a nakonfigurovat modul runtime IoT Edge na zařízení s Linuxem. Později v tomto kurzu použijete řešení vzdáleného monitorování můžete nasadit moduly IoT Edge tohoto zařízení.

## <a name="create-an-edge-manifest"></a>Vytvořte Edge manifest

Simulovat zařízení čerpadlo jack ropy, budete muset přidat následující moduly do vlastního hraničního zařízení:

* Teplota simulace modulu.
* Detekce anomálií Azure Stream Analytics.

Následující kroky ukazují, jak vytvořit manifest nasazení Edge, který zahrnuje tyto moduly. Později v tomto kurzu importu manifestu jako balíček IExpress v akcelerátoru řešení vzdáleného monitorování.

### <a name="create-the-azure-stream-analytics-job"></a>Vytvoření úlohy Azure Stream Analytics

Před zabalení jako modul Edge definujete úlohu Stream Analytics na portálu.

1. Na webu Azure Portal vytvořit účet úložiště Azure pomocí výchozích možností v **IoTEdgeDevices** skupinu prostředků. Poznamenejte si název, který jste zvolili.

1. Na portálu Azure vytvořit **úlohy Stream Analytics** v **IoTEdgeDevices** skupinu prostředků. Použít následující hodnoty konfigurace:

    | Možnost | Hodnota |
    | ------ | ----- |
    | Název úlohy | EdgeDeviceJob |
    | Předplatné | Vaše předplatné Azure |
    | Skupina prostředků | IoTEdgeDevices |
    | Umístění | USA – východ |
    | Hostitelské prostředí | Edge |
    | Jednotky streamování | 1 |

1. Otevřít **EdgeDeviceJob** Stream Analytics úlohy na portálu, klikněte na vstupy a přidat **Centrum Edge** vstupu volá datový proud stream **telemetrie**.

1. V **EdgeDeviceJob** úlohy Stream Analytics na portálu klikněte na tlačítko **výstupy** a přidat **Centrum Edge** výstup nazvaný **výstup**.

1. V **EdgeDeviceJob** úlohy Stream Analytics na portálu klikněte na tlačítko **výstupy** a přidejte druhý **Centrum Edge** výstup nazvaný **výstraha**.

1. V **EdgeDeviceJob** úlohy Stream Analytics na portálu klikněte na tlačítko **dotazu** a přidejte následující **vyberte** – příkaz:

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

1. V **EdgeDeviceJob** úlohy Stream Analytics na portálu klikněte na tlačítko **nastavení účtu úložiště**. Přidání účtu úložiště, který jste přidali do **IoTEdgeDevices** skupině prostředků jako začátku této části. Vytvořit nový kontejner volá **edgeconfig**.

Následující snímek obrazovky ukazuje uložené úlohy Stream Analytics:

[![Úlohy Stream Analytics](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

Nyní jste definovali v hraničním zařízení spuštění úlohy Stream Analytics. Úloha vypočítá průměrná teplota v intervalu 5 sekund. Úloha také odešle výstrahu, pokud průměrná teplota v okně 3 sekundy překročí 400.

### <a name="create-the-iot-edge-deployment"></a>Vytvoření nasazení IoT Edge

Dále vytvořte manifest nasazení IoT Edge, který definuje moduly, které chcete spustit na hraniční zařízení. V další části importu manifestu jako balíček IExpress v řešení vzdáleného monitorování.

1. Na webu Azure Portal přejděte do služby IoT hub v řešení vzdáleného monitorování. IoT hub můžete najít ve skupině prostředků, který má stejný název jako vaše řešení vzdálené monitorování.

1. Ve službě IoT hub, klikněte na tlačítko **IoT Edge** v **Automatická správa zařízení** oddílu. Klikněte na tlačítko **přidat nasazení IoT Edge**.

1. Na **vytvořit nasazení > název a popisek** stránky, zadejte název **ropy čerpadlo zařízení**. Klikněte na **Další**.

1. Na **vytvořit nasazení > Přidat moduly** klikněte na **+ přidat**. Zvolte **modul IoT Edge**.

1. V **moduly IoT Edge vlastní** panelu, zadejte **senzor teploty** jako název, a **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** jako identifikátor URI Image. Klikněte na **Uložit**.

1. Na **vytvořit nasazení > Přidat moduly** klikněte na **+ přidat** přidat druhý modul. Zvolte **modul Azure Stream Analytics**.

1. V **hraniční nasazení** panelu, vyberte své předplatné a **EdgeDeviceJob** jste vytvořili v předchozí části. Klikněte na **Uložit**.

1. Na **vytvořit nasazení > Přidat moduly** klikněte na **Další**.

1. Na **vytvořit nasazení > Určení tras** stránce, přidejte následující kód:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Tento kód přesměruje výstup z modulu Stream Analytics na správné umístění.

    Klikněte na **Další**.

1. Na **vytvořit nasazení > zadejte metriky** klikněte na **Další**.

1. Na **vytvořit nasazení > cílová zařízení** stránky, jako je Priorita zadejte 10. Klikněte na **Další**.

1. Na **vytvořit nasazení > zkontrolujte nasazení** klikněte na **odeslat**:

    [![Zkontrolujte nasazení](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. V hlavním **IoT Edge** klikněte na **nasazení IoT Edge**. Můžete zobrazit **ropy čerpadlo zařízení** v seznamu nasazení.

1. Klikněte na tlačítko **ropy čerpadlo zařízení** nasazení a pak klikněte na tlačítko **IoT Edge stáhnout manifest**. Uložte soubor jako **ropy. čerpadlo device.json** do vhodného umístění na místním počítači. Tento soubor v další části tohoto kurzu potřebujete.

Právě jste vytvořili manifest IoT Edge, který chcete importovat do řešení vzdáleného monitorování jako balíček. Vývojáři obvykle vytvoří IoT Edge, moduly a soubor manifestu.

## <a name="import-a-package"></a>Importovat balíček

V této části importu manifestu Edge jako balíček IExpress v řešení vzdáleného monitorování.

1. Vzdálené monitorování webového uživatelského rozhraní, přejděte na **balíčky** stránky a klikněte na tlačítko **+ nový balíček pro**:

    [![Nový balíček](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Na **nový balíček** panelu, vyberte **Edge Manifest** jako typ balíčku, klikněte na tlačítko **Procházet** najít **ropy. čerpadlo device.json** souborů v váš místní počítač a klikněte na **nahrát**:

    [![Nahrání balíčku](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    Seznam balíčků teď zahrnuje **ropy. čerpadlo device.json** balíčku.

V další části můžete vytvořit nasazení, která se použije balíček do vlastního hraničního zařízení.

## <a name="deploy-a-package"></a>Nasazení balíčku

Nyní jste připraveni nasadit balíček do zařízení.

1. Vzdálené monitorování webového uživatelského rozhraní, přejděte na **nasazení** stránky a klikněte na tlačítko **+ nové nasazení**:

    [![Nové nasazení](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. V **nové nasazení** panelu, vytvořit nasazení s následujícími nastaveními:

    | Možnost | Hodnota |
    | ------ | ----- |
    | Název   | OilPumpDevices |
    | Typ balíčku | Edge Manifest |
    | Balíček | ropa. čerpadlo device.json |
    | Skupiny zařízení | OilPumps |
    | Priorita | 10 |

    [![Vytvoření nasazení](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Klikněte na tlačítko **Použít**.

Budete muset počkat několik minut, než pro balíček pro nasazení do zařízení a telemetrických dat odesílaných ze zařízení spustit.

[![Aktivní nasazení](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

**Nasazení** stránce se zobrazí následující metriky:

* **Cílem** zobrazuje počet zařízení ve skupině zařízení.
* **Použít** zobrazuje počet zařízení, která jste využili obsahu nasazení použít.
* **Úspěšné** zobrazuje počet hraničních zařízení v nasazení generování sestav úspěšnost modul runtime IoT Edge klienta.
* **Nepovedlo** zobrazuje počet hraničních zařízení v nasazení generování sestav chyby z modulu runtime IoT Edge klienta.

## <a name="monitor-the-device"></a>Monitorování zařízení

Můžete zobrazit teplotní telemetrie ze zařízení čerpadlo ropy ve vzdálené monitorování webového uživatelského rozhraní:

1. Přejděte **zařízení** stránky a vyberte své zařízení ropy čerpadla.
1. V **Telemetrie** část **podrobnosti o zařízení** panelu, klikněte na tlačítko **teploty**:

    [![Zobrazení telemetrie](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

Uvidíte jak teplota stoupne, dokud nebude dosaženo prahové hodnoty. Modul Stream Analytics Edge zjistí, že teplota dosáhne této prahové hodnoty a odešle příkaz do zařízení a omezit teplota okamžitě. Všechny tohoto zpracování se stane v zařízení bez komunikace se službou cloud.

Pokud chcete zasílat oznámení operátory, pokud bylo dosaženo prahové hodnoty, můžete vytvořit pravidlo v vzdálené monitorování webového uživatelského rozhraní:

1. Přejděte **pravidla** stránky a klikněte na tlačítko **+ nové pravidlo**.
1. Vytvořte nové pravidlo s následujícím nastavením:

    | Možnost | Hodnota |
    | ------ | ----- |
    | Název pravidla | Ropa čerpadlo teploty |
    | Popis | Ropy čerpadlo teplota překročila 300 |
    | Skupina zařízení | OilPumps |
    | Výpočet | Okamžitý |
    | Pole | teplota |
    | Operátor | > |
    | Hodnota | 300 |
    | Úroveň závažnosti | Informace |

    [![Vytvoření pravidla](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Klikněte na tlačítko **Použít**.

1. Přejděte **řídicí panel** stránky. Upozornění se zobrazí v **výstrahy** panelu teploty v **ropy čerpadlo** , co zařízení projde více než 300.

## <a name="next-steps"></a>Další postup

Tento kurz ukázal, jak přidat a nakonfigurovat zařízení IoT Edge v akcelerátoru řešení vzdáleného monitorování. Další informace o práci s IoT Edge balíčky v řešení vzdáleného monitorování, najdete v následující průvodce:

> [!div class="nextstepaction"]
> [Importovat balíček IoT Edge do akcelerátor řešení vzdálené monitorování](iot-accelerators-remote-monitoring-import-edge-package.md)

Další informace o instalaci modulu runtime IoT Edge najdete v tématu [nainstalovat modul runtime Azure IoT Edge v Linuxu (x64)](../iot-edge/how-to-install-iot-edge-linux.md).

Další informace o Azure Stream Analytics na hraničních zařízeních najdete v tématu [nasazení Azure Stream Analytics jako modulu IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md).
