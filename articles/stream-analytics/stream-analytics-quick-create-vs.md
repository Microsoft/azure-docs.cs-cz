---
title: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics tools for Visual Studio
description: V tomto rychlém startu se dozvíte, jak začít vytvořením úlohy Stream Analytics, konfigurací vstupů a výstupů a definicí dotazu pomocí sady Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 1a72e2874e28a2aa5b69866bd959743707ea9d99
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021913"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-stream-analytics-tools-for-visual-studio"></a>Rychlý start: Vytvoření úlohy Stream Analytics pomocí Azure Stream Analytics tools for Visual Studio

V tomto rychlém startu se dozvíte, jak vytvořit a spustit úlohu Stream Analytics pomocí Azure Stream Analytics Tools for Visual Studio. Úlohy příklad přečte streamovaná data ze služby IoT Hub device. Můžete definovat úlohu, která vypočítá průměrná teplota, při více než 27° a zapíše výsledný výstup události do nového souboru v úložišti objektů blob.

## <a name="before-you-begin"></a>Před zahájením

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

* Nainstalujte sadu Visual Studio 2017, Visual Studio 2015 nebo Visual Studio 2013 Update 4. Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Edice Express se nepodporuje.

* Postupujte podle [pokynů k instalaci](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) a nainstalujte Stream Analytics Tools for Visual Studio.

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Než začnete definovat úlohu Stream Analytics, měli byste data, která je novější nakonfigurovaný jako vstup úlohy. Chcete-li připravíte vstupní data vyžadovaná úlohou, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.

3. V **služby IoT Hub** podokně zadejte následující informace:
   
   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Předplatné  | \<Vaše předplatné\> |  Vyberte předplatné Azure, které chcete použít. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte**Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. |
   |Oblast  |  \<Vyberte oblast nejbližší vašim uživatelům.\> | Vyberte zeměpisné umístění, kde je možné hostovat služby IoT Hub. Použijte umístění co nejblíže vašim uživatelům. |
   |Název IoT Hubu  | MyASAIoTHub  |   Vyberte název centra IoT.   |

   ![Vytvořit IoT Hub](./media/stream-analytics-quick-create-vs/create-iot-hub.png)

4. Vyberte **Další: Nastavení velikosti a měřítka**.

5. Zvolte si **Úroveň ceny a škálování**. Pro účely tohoto rychlého startu vyberte **F1 – Free** vrstvy, pokud je stále k dispozici v rámci předplatného. Pokud není k dispozici na úrovni free, zvolte na nejnižší úrovni k dispozici. Další informace najdete v tématu [ceny služby IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Velikosti a škálování služby IoT Hub](./media/stream-analytics-quick-create-vs/iot-hub-size-and-scale.png)

6. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte informace o službě IoT Hub a klikněte na tlačítko **vytvořit**. Služby IoT Hub může trvat několik minut pro vytvoření. Průběh můžete sledovat v podokně **Oznámení**.

7. V navigační nabídce služby IoT Hub, klikněte na tlačítko **přidat** pod **zařízení IoT**. Přidat **ID zařízení** a klikněte na tlačítko **Uložit**.

   ![Přidání zařízení do služby IoT Hub](./media/stream-analytics-quick-create-vs/add-device-iot-hub.png)

8. Po vytvoření zařízení ho otevřete z **zařízení IoT** seznamu. Kopírovat **připojovací řetězec – primární klíč** a uložte ho do poznámkového bloku pro pozdější použití.

   ![Zkopírujte připojovací řetězec služby IoT Hub zařízení](./media/stream-analytics-quick-create-vs/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Vytvořit úložiště objektů blob

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Úložiště** > **Účet úložiště**.

2. V **vytvořit účet úložiště** podokně zadejte skupinu název, umístění a prostředek účtu úložiště. Zvolte stejné umístění a skupině prostředků jako centra IoT, které jste vytvořili. Pak klikněte na tlačítko **revize + vytvořit** k vytvoření účtu.

   ![Vytvoření účtu úložiště](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Po vytvoření účtu úložiště, vyberte **objekty BLOB** na dlaždici **přehled** podokně.

   ![Přehled účtu úložiště](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Z **služby Blob Service** stránce **kontejneru** a zadejte název kontejneru, jako třeba *container1*. Nechte **úroveň veřejného přístupu** jako **privátní (bez anonymního přístupu)** a vyberte **OK**.

   ![Vytvoření kontejneru objektů blob](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Vytvoření projektu Stream Analytics

1. Spusťte Visual Studio.

2. Vyberte **Soubor > Nový projekt**.  

3. V seznamu šablon na levé straně vyberte **Stream Analytics** a pak vyberte **Aplikace Azure Stream Analytics**.  

4. Zadejte **Název** projektu, **Umístění** a **Název řešení** a vyberte **OK**.

   ![Vytvoření projektu Stream Analytics](./media/stream-analytics-quick-create-vs/create-stream-analytics-project.png)

Všimněte si prvků obsažených v projektu Azure Stream Analytics.

   <img src="./media/stream-analytics-quick-create-vs/stream-analytics-project.png" alt="Azure Stream Analytics project elements" width="300px"/>


## <a name="choose-the-required-subscription"></a>Výběr požadovaného předplatného

1. V sadě Visual Studio v nabídce **Zobrazení** vyberte **Průzkumník serveru**.

2. Klikněte pravým tlačítkem na **Azure**, vyberte **Připojit k předplatnému Microsoft Azure** a pak se přihlaste pomocí svého účtu Azure.

## <a name="define-input"></a>Definice vstupu

1. V **Průzkumníku řešení** rozbalte uzel **Vstupy** a dvakrát klikněte na soubor **Input.json**.

2. Vyplňte okno **Konfigurace vstupu Stream Analytics** následujícími hodnotami:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**   |
   |---------|---------|---------|
   |Alias pro vstup  |  Vstup   |  Zadejte název pro identifikaci vstupu úlohy.   |
   |Typ zdroje   |  Datový proud |  Zvolte odpovídající vstupní zdroj: Datový Stream nebo referenčními daty.   |
   |Zdroj  |  IoT Hub |  Vyberte odpovídající vstupní zdroj.   |
   |Prostředek  | Zvolit zdroj dat z aktuálního účtu | Zvolte, jestli chcete data zadat ručně, nebo vyberte existující účet.   |
   |Předplatné  |  \<Vaše předplatné\>   | Vyberte předplatné Azure, který má centra IoT, které jste vytvořili.   |
   |IoT Hub  |  MyASAIoTHub   |  Vyberte nebo zadejte název služby IoT Hub. Služby IoT Hub názvy jsou automaticky zjišťují, pokud jsou vytvořené v rámci stejného předplatného.   |
   
3. U ostatních možností ponechejte výchozí hodnoty a výběrem možnosti **Uložit** uložte nastavení.  

   ![Konfigurace vstupních dat](./media/stream-analytics-quick-create-vs/stream-analytics-vs-input.png)

## <a name="define-output"></a>Definice výstupu

1. V **Průzkumníku řešení** rozbalte uzel **Výstupy** a dvakrát klikněte na soubor **Output.json**.

2. Vyplňte okno **Konfigurace výstupu Stream Analytics** následujícími hodnotami:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**   |
   |---------|---------|---------|
   |Alias pro výstup  |  Výstup   |  Zadejte název pro identifikaci výstupu úlohy.   |
   |Jímka   |  Blob Storage |  Zvolte odpovídající jímku.    |
   |Prostředek  |  Zadat nastavení zdroje dat ručně |  Zvolte, jestli chcete data zadat ručně, nebo vyberte existující účet.   |
   |Předplatné  |  \<Vaše předplatné\>   | Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného.   |
   |Účet úložiště  |  asaquickstartstorage   |  Vyberte nebo zadejte název účtu úložiště. Pokud jsou názvy vytvořeny v rámci stejného předplatného, zjišťují se automaticky.   |
   |Kontejner  |  container1   |  Vyberte existující kontejner, který jste vytvořili v účtu úložiště.   |
   |Vzor cesty  |  output   |  Zadejte název cesty k souboru, která se vytvoří v rámci kontejneru.   |
   
3. U ostatních možností ponechejte výchozí hodnoty a výběrem možnosti **Uložit** uložte nastavení.  

   ![Konfigurace výstupních dat](./media/stream-analytics-quick-create-vs/stream-analytics-vs-output.png)

## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

1. V **Průzkumníku řešení** v sadě Visual Studio otevřete soubor **Script.asaql**.

2. Přidejte následující dotaz:

   ```sql
   SELECT *
   INTO BlobOutput
   FROM IoTHubInput
   HAVING Temperature > 27
   ```

## <a name="submit-a-stream-analytics-query-to-azure"></a>Odeslání dotazu Stream Analytics do Azure

1. V **Editoru dotazů** vyberte **Odeslat do Azure**.

2. Vyberte **Vytvořit novou úlohu Azure Stream Analytics** a zadejte **Název úlohy**. Zvolte **Předplatné**, **Skupinu prostředků** a **Umístění**, které jste použili na začátku tohoto rychlého startu.

   ![Odeslání úlohy do Azure](./media/stream-analytics-quick-create-vs/stream-analytics-job-to-azure.png)

## <a name="run-the-iot-simulator"></a>Spustit simulátor IoT

1. Otevřít [Online simulátor Raspberry Pi Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/) v nové záložce prohlížeče nebo okno.

2. Nahraďte zástupný text v řádku 15 připojovací řetězec služby Azure IoT Hub zařízení, které jste si uložili v předchozí části.

3. Klikněte na **Run** (Spustit). Výstup by měl zobrazit data ze senzorů a zprávy odeslané do služby IoT Hub.

   ![Online simulátor Raspberry Pi Azure IoT](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

1. Po vytvoření úlohy se automaticky otevře zobrazení úlohy. Výběrem tlačítka se zelenou šipkou spusťte úlohu.

   ![Spuštění úlohy Stream Analytics](./media/stream-analytics-quick-create-vs/start-stream-analytics-job-vs.png)

2. Změnit **režim spuštění výstupu úlohy** k **JobStartTime** a vyberte **Start**.

   ![Konfigurace spuštění úlohy](./media/stream-analytics-quick-create-vs/stream-analytics-start-configuration.png)

3. Všimněte si, že se stav úlohy změnil na **Spuštěno** a zobrazují se vstupní a výstupní události. Může to trvat několik minut.

   ![Spuštění úlohy Stream Analytics](./media/stream-analytics-quick-create-vs/stream-analytics-job-running.png)

4. Pokud chcete zobrazit výsledky, v nabídce **Zobrazení** vyberte **Průzkumník cloudu** a přejděte do účtu úložiště ve vaší skupině prostředků. V části **Kontejnery objektů blob** dvakrát klikněte na kontejner **container1** a pak na cestu k **výstupnímu** souboru.

   ![Zobrazení výsledků](./media/stream-analytics-quick-create-vs/stream-analytics-vs-results.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste pomocí sady Visual Studio nasadili jednoduchou úlohu Stream Analytics. Úlohy Stream Analytics můžete nasadit také pomocí webu [Azure Portal](stream-analytics-quick-create-portal.md) a [PowerShellu](stream-analytics-quick-create-powershell.md). 

Další informace o Azure Stream Analytics tools for Visual Studio, pokračujte v následujícím článku:

> [!div class="nextstepaction"]
> [Chcete-li zobrazit úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-vs-tools.md)
