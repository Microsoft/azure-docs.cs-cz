---
title: Výuka – streamujte analýzy na hraničních zařízeních pomocí Azure IoT Edge
description: V tomto kurzu nasadíte Azure Stream Analytics jako modul na zařízení IoT Edge
author: kgremban
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: d8c3bde0f32c1df6c98f6a71f6ab830c21256903
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76906288"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Kurz: Nasazení Azure Stream Analytics jako modulu IoT Edge

Mnoho řešení IoT využívá analytické služby k získání přehledu o datech při jejich příchodu do cloudu ze zařízení IoT. Díky Azure IoT Edge můžete logiku služby [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) přesunout do samotného zařízení. Zpracováním telemetrických streamů na hraničních zařízeních můžete snížit objem nahrávaných dat a zkrátit dobu reakce na přehledy umožňující provádět různé akce.

Azure IoT Edge a Azure Stream Analytics jsou integrované, aby zjednodušily vývoj úloh. Úlohu Azure Stream Analytics můžete vytvořit na webu Azure Portal a pak ji nasadit jako modul IoT Edge bez dalšího kódu.  

Azure Stream Analytics poskytuje bohatě strukturovanou syntaxi dotazu pro analýzu dat, a to jak v cloudu, tak na zařízeních IoT Edge. Další informace najdete v [dokumentaci k Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Modul Stream Analytics v tomto kurzu počítá průměrnou teplotu modulu v 30sekundových intervalech. Jakmile průměrná teplota dosáhne hodnoty 70, modul pošle upozornění do zařízení, aby provedlo akci. V tomto případě akce spočívá v tom, že se resetuje čidlo simulované teploty. V produkčním prostředí můžete tuto funkci například využít k vypnutí přístroje nebo jako preventivní opatření pro případ, kdy teplota dosáhne nebezpečné úrovně.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vytvořit úlohu Azure Stream Analytics, která zpracuje data na hraničním zařízení
> * Připojit se k nové úloze Azure Stream Analytics pomocí jiných modulů IoT Edge
> * Nasadit úlohu Azure Stream Analytics na zařízení IoT Edge z portálu Azure Portal

<center>

![Diagram – architektura kurzu: fáze a nasazení úlohy ASA](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Virtuální počítač Azure můžete použít jako zařízení IoT Edge podle kroků v rychlém startu pro [linuxová](quickstart-linux.md) zařízení nebo [zařízení s Windows](quickstart.md).

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.

## <a name="create-an-azure-stream-analytics-job"></a>Vytvoření úlohy Azure Stream Analytics

V této části vytvoříte úlohu Azure Stream Analytics, která provede následující kroky:

* Příjem dat ze zařízení IoT Edge.
* Dotaz telemetrická data pro hodnoty mimo rozsah sady.
* Na zařízení IoT Edge provádějte akci na základě výsledků dotazu.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Když vytvoříte úlohu Azure Stream Analytics, která se má spouštět na zařízení IoT Edge, je potřeba ji uložit tak, aby ji bylo možné volat ze zařízení. Můžete použít existující účet Azure Storage nebo si teď vytvořit nový.

1. Na webu Azure Portal přejděte na Vytvoření**účtu úložiště****úložiště** >  **prostředků** > .

1. Zadejte následující hodnoty pro vytvoření účtu úložiště:

   | Pole | Hodnota |
   | ----- | ----- |
   | Předplatné | Zvolte stejné předplatné jako pro vaše centrum IoT. |
   | Skupina prostředků | Doporučujeme použít stejnou skupinu prostředků pro všechny testovací prostředky pro rychlé starty a kurzy IoT Edge. Například **IoTEdgeResources**. |
   | Name (Název) | Zadejte jedinečný název účtu úložiště. |
   | Umístění | Zvolte umístění, které je blízko vás. |

1. Zachovat výchozí hodnoty pro ostatní pole a vybrat **zkontrolovat + Vytvořit**.

1. Zkontrolujte nastavení a vyberte **Vytvořit**.

### <a name="create-a-new-job"></a>Vytvoření nové úlohy

1. Na webu Azure Portal přejděte na vytvoření úlohy**Internet of Things** > **Stream Analytics** **.** > 

1. Zadejte následující hodnoty pro vytvoření úlohy:

   | Pole | Hodnota |
   | ----- | ----- |
   | Název úlohy | Zadejte název úlohy. Například **IoTEdgeJob** |
   | Předplatné | Zvolte stejné předplatné jako pro vaše centrum IoT. |
   | Skupina prostředků | Pro všechny testovací prostředky, které vytvoříte v průběhu rychlých startů a kurzů pro IoT Edge, doporučujeme použít stejnou skupinu prostředků. Například **IoTEdgeResources**. |
   | Umístění | Zvolte umístění, které je blízko vás. |
   | Hostitelské prostředí | Vyberte **Edge**. |

1. Vyberte **Vytvořit**.

### <a name="configure-your-job"></a>Konfigurace úlohy

Po vytvoření úlohy Stream Analytics na webu Azure Portal pro ni můžete nakonfigurovat vstup, výstup a dotaz, který má spustit pro data, která jí procházejí.

V této části se s využitím těchto tří prvků (vstup, výstup a dotaz) vytvoří úloha, která přijímá data o teplotě ze zařízení IoT Edge. Úloha analyzuje tato data v 30sekundových intervalech. Pokud průměrná teplota v tomto intervalu přesáhne 70 stupňů, do zařízení IoT Edge se odešle upozornění. V další části při nasazení úlohy přesně určíte, odkud data přicházejí a kam směřují.  

1. Přejděte k vaší úloze Stream Analytics na webu Azure Portal.

1. V části **Topologie úlohy** vyberte **Vstupy** a pak **Přidat vstup streamu**.

   ![Azure Stream Analytics – přidání vstupu](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. V rozevíracím seznamu zvolte **Edge Hub**.

1. V podokně **Nový vstup** zadejte **teplota** jako alias pro vstup.

1. U ostatních polí ponechte výchozí hodnoty a vyberte **Uložit**.

1. V části **Topologie úlohy** otevřete **Výstupy** a pak vyberte **Přidat**.

   ![Azure Stream Analytics – přidání výstupu](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. V rozevíracím seznamu zvolte **Edge Hub**.

1. V podokně **Nový výstup** zadejte **upozornění** jako alias pro výstup.

1. U ostatních polí ponechte výchozí hodnoty a vyberte **Uložit**.

1. V části **Topologie úlohy** vyberte **Dotaz**.

1. Výchozí text nahraďte následujícím dotazem. Tento kód SQL odešle do výstupu úlohy příkaz k resetování, pokud průměrná teplota počítače v 30sekundovém intervalu dosáhne 70 stupňů. Příkaz k resetování je ve snímači předem naprogramovaný jako akce, kterou je možné provést.

    ```sql
    SELECT  
        'reset' AS command
    INTO
       alert
    FROM
       temperature TIMESTAMP BY timeCreated
    GROUP BY TumblingWindow(second,30)
    HAVING Avg(machine.temperature) > 70
    ```

1. Vyberte **Uložit**.

### <a name="configure-iot-edge-settings"></a>Konfigurace nastavení IoT Edge

Pokud chcete úlohu Stream Analytics připravit k nasazení na zařízení IoT Edge, musíte úlohu přidružit ke kontejneru v účtu úložiště. Až přejdete k nasazení úlohy, definice úlohy se exportuje do kontejneru úložiště.

1. V části **Konfigurovat**vyberte **nastavení účtu úložiště** a pak vyberte Přidat účet **úložiště**.

   ![Azure Stream Analytics – přidání účtu úložiště](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. V rozevírací nabídce vyberte **účet úložiště,** který jste vytvořili na začátku tohoto kurzu.

1. V poli **Kontejner** vyberte **Vytvořit nový** a zadejte název kontejneru úložiště.

1. Vyberte **Uložit**.

## <a name="deploy-the-job"></a>Nasazení úlohy

Nyní jste připraveni nasadit úlohu Azure Stream Analytics na zařízení IoT Edge.

V této části pomocí průvodce **Nastavení modulů** na webu Azure Portal vytvoříte *manifest nasazení*. Manifest nasazení je soubor JSON popisující všechny moduly, které se nasadí do zařízení, registry kontejnerů, které uchovávají image modulů, způsob správy modulů a způsob vzájemné komunikace mezi moduly. Vaše zařízení IoT Edge načte svůj manifest nasazení ze služby IoT Hub a pak pomocí informací v něm obsažených nasadí a nakonfiguruje všechny přiřazené moduly.

Pro účely tohoto kurzu nasadíte dva moduly. Prvním z nich je **SimulatedTemperatureSensor**, což je modul, který simuluje snímač teploty a vlhkosti. Druhým modulem je vaše úloha Stream Analytics. Modul snímače poskytuje datový proud, který bude analyzovat dotaz vaší úlohy.

1. Na webu Azure Portal přejděte do svého centra IoT.

1. Přejděte na **IoT Edge**a otevřete stránku podrobností pro zařízení IoT Edge.

1. Vyberte **Set modules** (Nastavit moduly).  

1. Pokud jste dříve nasadili modul SimulatedTemperatureSensor na tomto zařízení, může se automaticky naplnit. Pokud ne, přidejte ho následujícím postupem:

   1. Klikněte na **Přidat** a vyberte **Modul IoT Edge**.
   1. Pro název zadejte **SimulatedTemperatureSensor**.
   1. Jako identifikátor URI obrázku zadejte **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**.
   1. Ostatní nastavení ponechte beze změny a vyberte **Uložit**.

1. Přidejte úlohu Azure Stream Analytics Edge následujícím postupem:

   1. Klikněte na **Přidat** a vyberte **Modul Azure Stream Analytics**.
   1. Vyberte předplatné a úlohu Azure Stream Analytics Edge, kterou jste vytvořili.
   1. Vyberte **Uložit**.

   Po uložení změn se podrobnosti o úloze Služby Stream Analytics publikují do kontejneru úložiště, který jste vytvořili.

1. Když je modul Stream Analytics přidán do seznamu modulů, vyberte jeho název, abyste viděli, jak je strukturován, a aktualizujte jeho nastavení na stránce **Aktualizovat modul IoT Edge.**

   Karta **Nastavení modulu** obsahuje **identifikátor URI bitové kopie,** který odkazuje na standardní bitovou kopii Azure Stream Analytics. Tato jedna bitová kopie se používá pro každý modul Stream Analytics, který se nasadí do zařízení IoT Edge.

   Karta **Nastavení dvojčete modulu** zobrazuje json, který definuje vlastnost Azure Stream Analytics (ASA) nazvanou **ASAJobInfo**. Hodnota této vlastnosti odkazuje na definici úlohy ve vašem kontejneru úložiště. Tato vlastnost je způsob konfigurace obrázku Stream Analytics s konkrétními podrobnostmi o úloze.

   Ve výchozím nastavení má modul Stream Analytics stejný název jako úloha, na které je založen. Pokud chcete, můžete změnit název modulu na této stránce, ale není to nutné.

1. Vyberte **Zrušit** nebo **Uložit**.

1. Poznamenejte si název modulu Stream Analytics, protože ho budete potřebovat v dalším kroku, a pak vyberte **Další: Trasy,** abyste pokračovali.

1. Na kartě **Trasy** definujete, jak se zprávy předávají mezi moduly a centrem IoT Hub. Zprávy jsou konstruovány pomocí párů název/hodnota. Nahraďte `route` `upstream` výchozí a název a hodnoty dvojicemi zobrazenými v následující tabulce, následující dvojice název/hodnota a nahradí instance _{moduleName}_ názvem modulu Azure Stream Analytics.

    | Name (Název) | Hodnota |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    Trasy, které zde deklarujete, definují tok dat zařízením IoT Edge. Telemetrická data z SimulatedTemperatureSensor jsou odesílána do služby IoT Hub a na vstup **teploty,** který byl nakonfigurován v úloze Stream Analytics. Výstupní **zprávy výstrah** jsou odesílány do služby IoT Hub a do modulu SimulatedTemperatureSensor, aby se aktivoval příkaz reset.

1. Vyberte **další: Kontrola + Vytvoření**.

1. Na kartě **Revize + Vytvoření** uvidíte, jak jsou informace, které jste zadali v průvodci, převedeny na manifest nasazení JSON. Až skončíte s kontrolou manifestu, vyberte **Vytvořit**.

1. Vrátíte se na stránku podrobností o zařízení. Vyberte **Aktualizovat**.  

    Měli byste vidět nový modul Stream Analytics běží, spolu s agentem IoT Edge a moduly centra IoT Edge. Může trvat několik minut, než se informace dostanou k vašemu zařízení IoT Edge a pak se začnou spouštět nové moduly. Pokud moduly neběží hned, pokračujte v aktualizaci stránky.

    ![SimulatedTemperatureSensor a ASA modul hlášený zařízením](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Zobrazení dat

Teď můžete přejít na zařízení IoT Edge a podívat se na interakci mezi modulem Azure Stream Analytics a modulem SimulatedTemperatureSensor.

1. Zkontrolujte, že v Dockeru běží všechny moduly:

   ```cmd/sh
   iotedge list  
   ```

1. Zobrazte si všechny systémové protokoly a data metrik. Použijte název modulu Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Zobrazení příkazu reset ovlivní senzor Simulované teploty zobrazením protokolů senzoru:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Můžete sledovat, jak teplota stroje postupně stoupá, dokud nedosáhne 70 stupňů po dobu 30 sekund. Potom modul Stream Analytics aktivuje resetování a teplota přístroje klesne na 21.

   ![Obnovit výstup příkazu do protokolů modulů](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali úlohu Azure Streaming Analytics, aby analyzovala data ze zařízení IoT Edge. Potom jste nahráli tento modul Azure Stream Analytics na zařízení IoT Edge, aby místně zpracovával a reagoval na zvýšení teploty a posílal stream agregovaných dat do cloudu. Pokud chcete zjistit, jak Azure IoT Edge může vytvořit další řešení pro firemní využití, pokračujte dalšími kurzy.

> [!div class="nextstepaction"]
> [Nasazení modelu Azure Machine Learning jako modulu](tutorial-deploy-machine-learning.md)
