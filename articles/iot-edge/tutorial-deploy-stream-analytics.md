---
title: Kurz – Stream Analytics na hraničních zařízeních pomocí Azure IoT Edge
description: V tomto kurzu nasadíte Azure Stream Analytics jako modul pro IoT Edge zařízení.
author: kgremban
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 323973b7646acee07a0c4dbc59834e0aceca75ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462044"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Kurz: nasazení Azure Stream Analytics jako modulu IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Mnoho řešení IoT používá analytické služby k získání přehledu o datech při jejich doručování do cloudu ze zařízení IoT. Díky Azure IoT Edge můžete logiku služby [Azure Stream Analytics](../stream-analytics/index.yml) přesunout do samotného zařízení. Zpracováním telemetrických streamů na hraničních zařízeních můžete snížit objem nahrávaných dat a zkrátit dobu reakce na přehledy umožňující provádět různé akce.

Azure IoT Edge a Azure Stream Analytics jsou integrované pro zjednodušení vývoje úloh. V Azure Portal můžete vytvořit úlohu Azure Stream Analytics a pak ji nasadit jako modul IoT Edge bez dalšího kódu.  

Azure Stream Analytics poskytuje bohatě strukturovaný syntax dotazu pro analýzu dat, jak v cloudu, tak na IoT Edgech zařízeních. Další informace najdete v [dokumentaci Azure Stream Analytics](../stream-analytics/stream-analytics-edge.md).

Modul Stream Analytics v tomto kurzu počítá průměrnou teplotu modulu v 30sekundových intervalech. Jakmile průměrná teplota dosáhne hodnoty 70, modul pošle upozornění do zařízení, aby provedlo akci. V tomto případě akce spočívá v tom, že se resetuje čidlo simulované teploty. V produkčním prostředí můžete tuto funkci například využít k vypnutí přístroje nebo jako preventivní opatření pro případ, kdy teplota dosáhne nebezpečné úrovně.

V tomto kurzu se naučíte:
> [!div class="checklist"]
>
> * Vytvořit úlohu Azure Stream Analytics, která zpracuje data na hraničním zařízení
> * Připojit se k nové úloze Azure Stream Analytics pomocí jiných modulů IoT Edge
> * Nasadit úlohu Azure Stream Analytics na zařízení IoT Edge z portálu Azure Portal

<center>

![Diagram – architektura kurzu: fáze a nasazení ASA úlohy](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Pomocí postupu v rychlém startu pro zařízení se systémem [Linux](quickstart-linux.md) nebo [Windows](quickstart.md)můžete použít virtuální počítač Azure jako zařízení IoT Edge.

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.

## <a name="create-an-azure-stream-analytics-job"></a>Vytvoření úlohy Azure Stream Analytics

V této části vytvoříte úlohu Azure Stream Analytics, která provede následující kroky:

* Příjem dat ze zařízení IoT Edge.
* Dotazování na data telemetrie pro hodnoty mimo nastavený rozsah.
* Provede na zařízení IoT Edge akci na základě výsledků dotazu.

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Když vytvoříte úlohu Azure Stream Analytics, která se má spouštět na zařízení IoT Edge, je potřeba ji uložit tak, aby ji bylo možné volat ze zařízení. Můžete použít existující účet Azure Storage nebo vytvořit nový.

1. V Azure Portal klikněte na **vytvořit prostředek**  >  **úložiště**  >  **účet úložiště**.

1. Zadejte následující hodnoty pro vytvoření účtu úložiště:

   | Pole | Hodnota |
   | ----- | ----- |
   | Předplatné | Zvolte stejné předplatné jako pro vaše centrum IoT. |
   | Skupina prostředků | Doporučujeme použít stejnou skupinu prostředků pro všechny vaše testovací prostředky pro IoT Edge rychlý Start a kurzy. Například **IoTEdgeResources**. |
   | Name | Zadejte jedinečný název účtu úložiště. |
   | Umístění | Zvolte umístění, které je blízko vás. |

1. Pro ostatní pole ponechte výchozí hodnoty a vyberte **zkontrolovat + vytvořit**.

1. Zkontrolujte nastavení a pak vyberte **vytvořit**.

### <a name="create-a-new-job"></a>Vytvoření nové úlohy

1. V Azure Portal klikněte na **vytvořit prostředek**  >  **Internet věcí**  >  **Stream Analytics úlohu**.

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

   ![Azure Stream Analytics – přidat vstup](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. V rozevíracím seznamu zvolte **Edge Hub**.

1. V podokně **Nový vstup** zadejte **teplota** jako alias pro vstup.

1. V ostatních polích nechte výchozí hodnoty a vyberte **Uložit**.

1. V části **Topologie úlohy** otevřete **Výstupy** a pak vyberte **Přidat**.

   ![Azure Stream Analytics – přidat výstup](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. V rozevíracím seznamu zvolte **Edge Hub**.

1. V podokně **Nový výstup** zadejte **upozornění** jako alias pro výstup.

1. V ostatních polích nechte výchozí hodnoty a vyberte **Uložit**.

1. V části **topologie úlohy** vyberte možnost **dotaz**.

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

1. Vyberte **Uložit dotaz**.

### <a name="configure-iot-edge-settings"></a>Konfigurace nastavení IoT Edge

Pokud chcete úlohu Stream Analytics připravit k nasazení na zařízení IoT Edge, musíte úlohu přidružit ke kontejneru v účtu úložiště. Až přejdete k nasazení úlohy, definice úlohy se exportuje do kontejneru úložiště.

1. V části **Konfigurovat** vyberte **Nastavení účtu úložiště** a pak vyberte **Přidat účet úložiště**.

   ![Azure Stream Analytics – přidat účet úložiště](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Z rozevírací nabídky vyberte **účet úložiště** , který jste vytvořili na začátku tohoto kurzu.

1. V poli **Kontejner** vyberte **Vytvořit nový** a zadejte název kontejneru úložiště.

1. Vyberte **Uložit**.

## <a name="deploy-the-job"></a>Nasazení úlohy

Nyní jste připraveni nasadit úlohu Azure Stream Analytics na zařízení IoT Edge.

V této části pomocí průvodce **Nastavení modulů** na webu Azure Portal vytvoříte *manifest nasazení*. Manifest nasazení je soubor JSON popisující všechny moduly, které se nasadí do zařízení, registry kontejnerů, které uchovávají image modulů, způsob správy modulů a způsob vzájemné komunikace mezi moduly. Vaše zařízení IoT Edge načte svůj manifest nasazení ze služby IoT Hub a pak pomocí informací v něm obsažených nasadí a nakonfiguruje všechny přiřazené moduly.

Pro účely tohoto kurzu nasadíte dva moduly. První je **SimulatedTemperatureSensor**, což je modul, který simuluje teplotu a senzor vlhkosti. Druhým modulem je vaše úloha Stream Analytics. Modul snímače poskytuje datový proud, který bude analyzovat dotaz vaší úlohy.

1. Na webu Azure Portal přejděte do svého centra IoT.

1. Přejít na **IoT Edge** a pak otevřete stránku podrobností pro vaše zařízení IoT Edge.

1. Vyberte **Set modules** (Nastavit moduly).  

1. Pokud jste na tomto zařízení dřív nasadili modul SimulatedTemperatureSensor, může se automaticky vyplnit. Pokud ne, přidejte ho následujícím postupem:

   1. Klikněte na **Přidat** a vyberte **Modul IoT Edge**.
   1. Jako název zadejte **SimulatedTemperatureSensor**.
   1. Jako identifikátor URI obrázku zadejte **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0**.
   1. Ostatní nastavení ponechte beze změny a vyberte **Přidat**.

1. Přidejte úlohu Azure Stream Analytics Edge následujícím postupem:

   1. Klikněte na **Přidat** a vyberte **Modul Azure Stream Analytics**.
   1. Vyberte předplatné a úlohu Azure Stream Analytics Edge, kterou jste vytvořili.
   1. Vyberte **Uložit**.

   Po uložení změn se do kontejneru úložiště, který jste vytvořili, publikují podrobnosti vaší Stream Analytics úlohy.

1. Když se modul Stream Analytics přidá do seznamu modulů, vyberte jeho název, abyste viděli, jak je strukturovaný, a aktualizujte jeho nastavení na stránce **aktualizovat IoT Edge modulu** .

   Karta **nastavení modulu** obsahuje **identifikátor URI obrázku** , který odkazuje na standardní Azure Stream Analytics image. Tento jeden obrázek se používá pro každý Stream Analytics modul, který se nasadí do IoT Edgeho zařízení.

   Karta **nastavení s dvojitým modulem** zobrazuje kód JSON, který definuje vlastnost Azure Stream Analytics (ASA) s názvem **ASAJobInfo**. Hodnota této vlastnosti odkazuje na definici úlohy ve vašem kontejneru úložiště. Tato vlastnost představuje způsob konfigurace Stream Analytics obrázku s konkrétními podrobnostmi o úloze.

   Ve výchozím nastavení má modul Stream Analytics stejný název jako úloha, na které je založena. Název modulu na této stránce můžete změnit, pokud chcete, ale není to nutné.

1. Vyberte **aktualizovat** nebo **Zrušit**.

1. Poznamenejte si název vašeho modulu Stream Analytics, protože ho budete potřebovat v dalším kroku. Pak pokračujte výběrem **Další: trasy** .

1. Na kartě **trasy** definujete, jak jsou zprávy předávány mezi moduly a IoT Hub. Zprávy se vytvářejí pomocí párů název/hodnota. Nahraďte výchozí `route` `upstream` hodnoty a název a hodnoty dvojicemi, které jsou uvedené v následující tabulce, následující páry název/hodnota, které nahradí instance typu _{Module}_ názvem vašeho modulu Azure Stream Analytics.

    | Name | Hodnota |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    Trasy, které zde deklarujete, definují tok dat zařízením IoT Edge. Data telemetrie z SimulatedTemperatureSensor se odesílají do IoT Hub a do **teploty** , která byla nakonfigurovaná v úloze Stream Analytics. Výstupní zprávy **výstrahy** jsou odesílány do IoT Hub a do modulu SimulatedTemperatureSensor pro aktivaci příkazu reset.

1. Vyberte **Další: zkontrolovat + vytvořit**.

1. Na kartě **Revize + vytvořit** vidíte, jak jsou informace, které jste zadali v průvodci, převedeny na manifest nasazení JSON. Až dokončíte revizi manifestu, vyberte **vytvořit**.

1. Vrátíte se na stránku s podrobnostmi o zařízení. Vyberte **aktualizovat**.  

    Měl by se zobrazit nový spuštěný modul Stream Analytics společně s moduly agenta IoT Edge a IoT Edge. Může trvat několik minut, než se tyto informace dostanou k vašemu IoT Edge zařízení a pak se nové moduly spustí. Pokud nevidíte moduly, které jsou spuštěny hned, pokračujte v aktualizaci stránky.

    ![Modul SimulatedTemperatureSensor a ASA hlášený zařízením](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Zobrazení dat

Teď můžete přejít na zařízení IoT Edge a zkontrolovat interakci mezi modulem Azure Stream Analytics a modulem SimulatedTemperatureSensor.

1. Zkontrolujte, že v Dockeru běží všechny moduly:

   ```cmd/sh
   iotedge list  
   ```

1. Zobrazte si všechny systémové protokoly a data metrik. Použijte název modulu Stream Analytics:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Zobrazit příkaz pro obnovení ovlivní SimulatedTemperatureSensor zobrazením protokolů senzoru:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   Teplotu počítače můžete sledovat postupně, dokud nedosáhne 70 stupňů po dobu 30 sekund. Potom modul Stream Analytics aktivuje resetování a teplota přístroje klesne na 21.

   ![Resetovat výstup příkazu do protokolů modulů](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

V opačném případě můžete odstranit místní konfigurace a prostředky Azure, které jste použili v tomto článku, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali úlohu Azure Streaming Analytics, aby analyzovala data ze zařízení IoT Edge. Potom jste nahráli tento modul Azure Stream Analytics na zařízení IoT Edge, aby místně zpracovával a reagoval na zvýšení teploty a posílal stream agregovaných dat do cloudu. Pokud chcete zjistit, jak Azure IoT Edge může vytvořit další řešení pro firemní využití, pokračujte dalšími kurzy.

> [!div class="nextstepaction"]
> [Nasazení modelu Azure Machine Learning jako modulu](tutorial-deploy-machine-learning.md)