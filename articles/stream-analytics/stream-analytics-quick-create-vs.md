---
title: Rychlý Start – vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio
description: V tomto rychlém startu se dozvíte, jak začít vytvořením úlohy Stream Analytics, konfigurací vstupů a výstupů a definicí dotazu pomocí sady Visual Studio.
author: mamccrea
ms.author: mamccrea
ms.date: 06/11/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.openlocfilehash: aff9bf83795043e0176d7a3f155844c8dbe0281a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87337465"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-visual-studio"></a>Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio

V tomto rychlém startu se dozvíte, jak vytvořit a spustit úlohu Stream Analytics pomocí Azure Stream Analytics Tools for Visual Studio. Ukázková úloha načte streamovaná data z IoT Hubho zařízení. Nadefinujete úlohu, která vypočítá průměrnou teplotu při více než 27 ° a zapíše výsledné výstupní události do nového souboru v úložišti objektů BLOB.

> [!NOTE]
> Sady Visual Studio a nástroje pro Visual Studio Code nepodporují úlohy v oblastech Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod.

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se na web [Azure Portal](https://portal.azure.com/).

* Nainstalujte Visual Studio 2019, Visual Studio 2015 nebo Visual Studio 2013 Update 4. Podporují se edice Enterprise (Ultimate nebo Premium), Professional a Community. Edice Express se nepodporuje.

* Postupujte podle [pokynů k instalaci](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) a nainstalujte Stream Analytics Tools for Visual Studio.

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Před definováním Stream Analytics úlohy byste měli připravit data, která jsou později nakonfigurovaná jako vstup úlohy. Chcete-li připravit vstupní data požadovaná úlohou, proveďte následující kroky:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

2. Vyberte **vytvořit prostředek**  >  **Internet věcí**  >  **IoT Hub**.

3. V podokně **IoT Hub** zadejte následující informace:
   
   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Předplatné  | \<Your subscription\> |  Vyberte předplatné Azure, které chcete použít. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte **Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. |
   |Oblast  |  \<Select the region that is closest to your users\> | Vyberte zeměpisnou polohu, kde můžete IoT Hub hostovat. Použijte umístění, které je nejblíže vašim uživatelům. |
   |Název IoT Hub  | MyASAIoTHub  |   Vyberte název IoT Hub.   |

   ![Vytvořit IoT Hub](./media/stream-analytics-quick-create-vs/create-iot-hub.png)

4. Vyberte **Další: nastavit velikost a měřítko**.

5. Zvolte si **Úroveň ceny a škálování**. Pro tento rychlý Start vyberte úroveň **F1** , pokud je stále k dispozici ve vašem předplatném. Pokud úroveň Free není k dispozici, vyberte nejnižší dostupnou úroveň. Další informace najdete v tématu [IoT Hub ceny](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Velikost a škálování IoT Hub](./media/stream-analytics-quick-create-vs/iot-hub-size-and-scale.png)

6. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte informace o IoT Hub a klikněte na **vytvořit**. Vytvoření IoT Hub může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.

7. V navigační nabídce IoT Hub klikněte na **Přidat** v části **zařízení IoT**. Přidejte **ID zařízení** a klikněte na **Uložit**.

   ![Přidání zařízení do IoT Hub](./media/stream-analytics-quick-create-vs/add-device-iot-hub.png)

8. Po vytvoření zařízení otevřete zařízení ze seznamu **zařízení IoT** . Zkopírujte **připojovací řetězec – primární klíč** a uložte ho do poznámkového bloku a použijte ho později.

   ![Kopírovat připojovací řetězec IoT Hub zařízení](./media/stream-analytics-quick-create-vs/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Vytvoření úložiště objektů BLOB

1. V levém horním rohu Azure Portal vyberte **vytvořit prostředek**úložiště  >  **Storage**  >  **účet**úložiště.

2. V podokně **vytvořit účet úložiště** zadejte název účtu úložiště, umístění a skupinu prostředků. Vyberte stejné umístění a skupinu prostředků jako IoT Hub, který jste vytvořili. Pak klikněte na tlačítko **zkontrolovat + vytvořit** a vytvořte účet.

   ![Vytvoření účtu úložiště](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Po vytvoření účtu úložiště vyberte dlaždici **objektů BLOB** v podokně **Přehled** .

   ![Přehled účtu úložiště](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Na stránce **BLOB Service** vyberte **kontejner** a zadejte název svého kontejneru, například *container1*. Ponechte **úroveň veřejného přístupu** jako **soukromou (bez anonymního přístupu)** a vyberte **OK**.

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
   |Typ zdroje   |  Datový proud |  Zvolte odpovídající vstupní zdroj: datový proud nebo referenční data.   |
   |Zdroj  |  IoT Hub |  Vyberte odpovídající vstupní zdroj.   |
   |Prostředek  | Zvolit zdroj dat z aktuálního účtu | Zvolte, jestli chcete data zadat ručně, nebo vyberte existující účet.   |
   |Předplatné  |  \<Your subscription\>   | Vyberte předplatné Azure, které obsahuje IoT Hub, které jste vytvořili.   |
   |IoT Hub  |  MyASAIoTHub   |  Vyberte nebo zadejte název IoT Hub. Názvy IoT Hub jsou automaticky zjištěny, pokud jsou vytvořeny ve stejném předplatném.   |
   
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
   |Předplatné  |  \<Your subscription\>   | Zadejte předplatné Azure vytvořeného účtu úložiště. Účet úložiště můžete využívat v rámci stejného, ale i jiného předplatného. V tomto příkladu se předpokládá, že jste účet vytvořili v rámci stejného předplatného.   |
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
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="submit-a-stream-analytics-query-to-azure"></a>Odeslání dotazu Stream Analytics do Azure

1. V **Editoru dotazů** vyberte **Odeslat do Azure**.

2. Vyberte **Vytvořit novou úlohu Azure Stream Analytics** a zadejte **Název úlohy**. Zvolte **Předplatné**, **Skupinu prostředků** a **Umístění**, které jste použili na začátku tohoto rychlého startu.

   ![Odeslání úlohy do Azure](./media/stream-analytics-quick-create-vs/stream-analytics-job-to-azure.png)

## <a name="run-the-iot-simulator"></a>Spuštění simulátoru IoT

1. Otevřete [online simulátor Azure IoT PI](https://azure-samples.github.io/raspberry-pi-web-simulator/) na nové kartě nebo v okně prohlížeče.

2. Zástupný text na řádku 15 nahraďte připojovacím řetězcem zařízení Azure IoT Hub, který jste uložili v předchozí části.

3. Klikněte na **Spustit**. Výstup by měl ukazovat data a zprávy snímače, které se odesílají do vašeho IoT Hub.

   ![Online simulátor Raspberry Pi Azure IoT](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

1. Po vytvoření úlohy se automaticky otevře zobrazení úlohy. Výběrem tlačítka se zelenou šipkou spusťte úlohu.

   ![Spuštění úlohy Stream Analytics](./media/stream-analytics-quick-create-vs/start-stream-analytics-job-vs.png)

2. Změňte **počáteční režim výstupu úlohy** na **JobStartTime** a vyberte **Spustit**.

   ![Konfigurace spuštění úlohy](./media/stream-analytics-quick-create-vs/stream-analytics-start-configuration.png)

3. Všimněte si, že se stav úlohy změnil na **Spuštěno** a zobrazují se vstupní a výstupní události. Může to trvat několik minut.

   ![Spuštění úlohy Stream Analytics](./media/stream-analytics-quick-create-vs/stream-analytics-job-running.png)

4. Pokud chcete zobrazit výsledky, v nabídce **Zobrazení** vyberte **Průzkumník cloudu** a přejděte do účtu úložiště ve vaší skupině prostředků. V části **Kontejnery objektů blob** dvakrát klikněte na kontejner **container1** a pak na cestu k **výstupnímu** souboru.

   ![Zobrazení výsledků](./media/stream-analytics-quick-create-vs/stream-analytics-vs-results.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí sady Visual Studio nasadili jednoduchou úlohu Stream Analytics. Úlohy Stream Analytics můžete nasadit také pomocí webu [Azure Portal](stream-analytics-quick-create-portal.md) a [PowerShellu](stream-analytics-quick-create-powershell.md). 

Další informace o nástrojích Azure Stream Analytics Tools for Visual Studio najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)
