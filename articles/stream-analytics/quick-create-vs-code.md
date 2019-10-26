---
title: Vytvoření cloudové úlohy Azure Stream Analytics v Visual Studio Code
description: V tomto rychlém startu se dozvíte, jak začít vytvořením Stream Analytics úlohy, konfigurací vstupů, výstupů a definováním dotazu pomocí Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 35e2711bbb3cd6dc0662146a566014dd65d879bc
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935092"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Rychlý Start: vytvoření cloudové úlohy Azure Stream Analytics v Visual Studio Code (Preview)

V tomto rychlém startu se dozvíte, jak vytvořit a spustit úlohu Stream Analytics pomocí rozšíření Azure Stream Analytics pro Visual Studio Code. Ukázková úloha načte streamovaná data z IoT Hubho zařízení. Nadefinujete úlohu, která vypočítá průměrnou teplotu při více než 27 ° a zapíše výsledné výstupní události do nového souboru v úložišti objektů BLOB.

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se na web [Azure Portal](https://portal.azure.com/).

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-extension"></a>Instalace rozšíření Azure Stream Analytics

1. Otevřete Visual Studio Code.

2. V nabídce **rozšíření** v levém podokně vyhledejte **Stream Analytics** a v rozšíření **Azure Stream Analytics** vyberte **instalovat** .

3. Po instalaci rozšíření ověřte, že se v **povolených rozšířeních**zobrazují **Azure Stream Analytics nástroje** .

   ![Azure Stream Analytics nástroje v části povolená rozšíření v Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Aktivace rozšíření Azure Stream Analytics

1. Na řádku VS Code aktivity vyberte ikonu **Azure** . **Stream Analytics** se zobrazí na bočním panelu. V části **Stream Analytics**vyberte **Přihlásit se k Azure**. 

   ![Přihlaste se k Azure v Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Když jste přihlášení, váš název účtu Azure se zobrazí na stavovém řádku v levém dolním rohu okna VS Code.

> [!NOTE]
> Pokud se odhlásíte, Azure Stream Analytics nástroje se automaticky přihlásí. Pokud má váš účet dvojúrovňové ověřování, doporučujeme místo kódu PIN používat ověřování pomocí telefonu.
> Pokud máte problémy se seznamem prostředků, odhlášení a opětovné přihlášení obvykle pomáhá. Pokud se chcete odhlásit, zadejte příkaz `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Před definováním Stream Analytics úlohy byste měli připravit data, která jsou později nakonfigurovaná jako vstup úlohy. Chcete-li připravit vstupní data požadovaná úlohou, proveďte následující kroky:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).

2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.

3. V podokně **IoT Hub** zadejte následující informace:
   
   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Předplatné  | \<Vaše předplatné\> |  Vyberte předplatné Azure, které chcete použít. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte**Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. |
   |Oblast  |  \<Vyberte oblast nejbližší vašim uživatelům.\> | Vyberte zeměpisnou polohu, kde můžete IoT Hub hostovat. Použijte umístění, které je nejblíže vašim uživatelům. |
   |Název IoT Hub  | MyASAIoTHub  |   Vyberte název IoT Hub.   |

   ![Vytvořit IoT Hub](./media/quick-create-vs-code/create-iot-hub.png)

4. Vyberte **Další: nastavit velikost a měřítko**.

5. Zvolte si **Úroveň ceny a škálování**. Pro tento rychlý Start vyberte úroveň **F1** , pokud je stále k dispozici ve vašem předplatném. Pokud úroveň Free není k dispozici, vyberte nejnižší dostupnou úroveň. Další informace najdete v tématu [IoT Hub ceny](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Velikost a škálování IoT Hub](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte informace o IoT Hub a klikněte na **vytvořit**. Vytvoření IoT Hub může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení**.

7. V navigační nabídce IoT Hub klikněte na **Přidat** v části **zařízení IoT**. Přidejte **ID zařízení** a klikněte na **Uložit**.

   ![Přidání zařízení do IoT Hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Po vytvoření zařízení otevřete zařízení ze seznamu **zařízení IoT** . Zkopírujte **připojovací řetězec – primární klíč** a uložte ho do poznámkového bloku a použijte ho později.

   ![Kopírovat připojovací řetězec IoT Hub zařízení](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Vytvoření úložiště objektů BLOB

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Úložiště** > **Účet úložiště**.

2. V podokně **vytvořit účet úložiště** zadejte název účtu úložiště, umístění a skupinu prostředků. Vyberte stejné umístění a skupinu prostředků jako IoT Hub, který jste vytvořili. Pak klikněte na tlačítko **zkontrolovat + vytvořit** a vytvořte účet.

   ![Vytvoření účtu úložiště](./media/quick-create-vs-code/create-storage-account.png)

3. Po vytvoření účtu úložiště vyberte dlaždici **objektů BLOB** v podokně **Přehled** .

   ![Přehled účtu úložiště](./media/quick-create-vs-code/blob-storage.png)

4. Na stránce **BLOB Service** vyberte **kontejner** a zadejte název svého kontejneru, například *container1*. Ponechte **úroveň veřejného přístupu** jako **soukromou (bez anonymního přístupu)** a vyberte **OK**.

   ![Vytvoření kontejneru objektů blob](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Vytvoření projektu Stream Analytics

1. V Visual Studio Code stisknutím **kombinace kláves CTRL + SHIFT + P** otevřete paletu příkazů. Pak zadejte **ASA** a vyberte **ASA: vytvořit nový projekt**.

   ![Vytvoření nového projektu](./media/quick-create-vs-code/create-new-project.png)

2. Zadejte název projektu, například **myASAproj** , a vyberte složku pro svůj projekt.

    ![Vytvořit název projektu](./media/quick-create-vs-code/create-project-name.png)

3. Nový projekt se přidá do vašeho pracovního prostoru. Projekt ASA se skládá ze skriptu dotazu **(*. asaql)** , souboru **JobConfig. JSON** a konfiguračního souboru **asaproj. JSON** .

   ![Stream Analytics soubory projektu v VS Code](./media/quick-create-vs-code/asa-project-files.png)

4. Konfigurační soubor **asaproj. JSON** obsahuje informace o vstupech, výstupech a konfiguračních souborech úloh potřebných k odeslání Stream Analytics úlohy do Azure.

   ![Konfigurační soubor úlohy Stream Analytics v VS Code](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Při přidávání vstupů a výstupů z palety příkazů budou do **asaproj. JSON** automaticky přidány odpovídající cesty. Pokud přidáváte nebo odebíráte vstupy nebo výstupy přímo na disku, musíte je ručně přidat nebo odebrat z **asaproj. JSON**. Vstupy a výstupy si můžete umístit na jednom místě a pak je odkazovat v různých úlohách zadáním cest v každém **asaproj. JSON**.

## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

1. Otevřete **myASAproj. asaql** z vaší složky projektu.

2. Přidejte následující dotaz:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```
## <a name="test-with-sample-data"></a>Testování s ukázkovými daty
Před spuštěním dotazu v cloudu můžete otestovat dotaz s místními ukázkovými daty a ověřit logiku dotazu.

Postupujte podle pokynů v části [test s ukázkovými daty](vscode-local-run.md) , kde najdete další podrobnosti. 

 ![Testování s ukázkovými daty v VS Code](./media/quick-create-vs-code/vscode-localrun.gif)

## <a name="define-an-input"></a>Definování vstupu

1. Vyberte **CTRL + SHIFT + P** a otevřete paletu příkazů a zadejte **ASA: přidat vstup**.

   ![Přidat vstup Stream Analytics v VS Code](./media/quick-create-vs-code/add-input.png)

2. Jako typ vstupu vyberte **IoT Hub** .

   ![Vybrat možnost IoT Hub jako vstup](./media/quick-create-vs-code/iot-hub.png)

3. Vyberte skript dotazu ASA, který bude používat vstup. Měl by se automaticky naplnit cestou k souboru **myASAproj. asaql**.

   ![Vyberte skript ASA v Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Zadejte název vstupního souboru jako **IotHub**.

5. Upravte **IoTHub. JSON** s následujícími hodnotami. Ponechat výchozí hodnoty pro pole, která nejsou uvedena níže. Pomocí CodeLens můžete zadat řetězec, vybrat ho v rozevíracím seznamu nebo změnit text přímo v souboru.

   |Nastavení|Navrhovaná hodnota|Popis|
   |-------|---------------|-----------|
   |Name (Název)|Vstup|Zadejte název pro identifikaci vstupu úlohy.|
   |IotHubNamespace|MyASAIoTHub|Vyberte nebo zadejte název IoT Hub. Názvy IoT Hub jsou automaticky zjištěny, pokud jsou vytvořeny ve stejném předplatném.|
   |sharedAccessPolicyName|iothubowner| |

   ![Konfigurace vstupu v Visual Studio Code](./media/quick-create-vs-code/configure-input.png)



## <a name="define-an-output"></a>Definování výstupu

1. Kliknutím na **CTRL + SHIFT + P** otevřete paletu příkazů. Pak zadejte **ASA: Přidat výstup**.

   ![Přidat výstup Stream Analytics v VS Code](./media/quick-create-vs-code/add-output.png)

2. Jako typ jímky vyberte **BLOB Storage** .

3. Vyberte skript dotazu ASA, který bude používat tento vstup.

4. Jako **BlobStorage**zadejte název výstupního souboru.

5. Upravte **BlobStorage** s následujícími hodnotami. Ponechat výchozí hodnoty pro pole, která nejsou uvedena níže. Použijte CodeLens, který vám pomůžete vybrat z rozevíracího seznamu nebo zadat řetězec. 

   |Nastavení|Navrhovaná hodnota|Popis|
   |-------|---------------|-----------|
   |Name (Název)|Výstup| Zadejte název pro identifikaci výstupu úlohy.|
   |Účet úložiště|asaquickstartstorage|Vyberte nebo zadejte název svého účtu úložiště. Pokud jsou názvy vytvořeny v rámci stejného předplatného, zjišťují se automaticky.|
   |Kontejner|container1|Vyberte existující kontejner, který jste vytvořili v účtu úložiště.|
   |Vzor cesty|output|Zadejte název cesty k souboru, která se vytvoří v rámci kontejneru.|

 ![Konfigurace výstupu v Visual Studio Code](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Zkompilujte skript

Kompilace skriptu provádí dvě věci: kontroluje syntaxi a generuje šablony Azure Resource Manager pro autodeployment.

Existují dva způsoby, jak spustit kompilaci skriptu:

1. Vyberte skript z pracovního prostoru a potom z palety příkazů Spusťte kompilaci. 

   ![Použití palety příkazů VS Code ke kompilaci skriptu](./media/quick-create-vs-code/compile-script1.png)

2. Klikněte pravým tlačítkem na skript a vyberte **ASA: kompilovat skript**.

    ![Klikněte pravým tlačítkem na skript ASA ke kompilaci.](./media/quick-create-vs-code/compile-script2.png)

3. Po kompilaci můžete najít dvě vygenerované Azure Resource Manager šablony ve složce **Deploy** v projektu. Tyto dva soubory se používají pro autodeployment.

    ![Šablony nasazení Stream Analytics v Průzkumníkovi souborů](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Odeslání úlohy Stream Analytics do Azure

1. V okně Editor skriptů Visual Studio Code vyberte **vybrat z vašich předplatných**.

   ![Výběr z textu vašich předplatných v editoru skriptů](./media/quick-create-vs-code/select-subscription.png)

2. V rozevíracím seznamu vyberte své předplatné.

3. Vyberte úlohu * *. Pak zvolte vytvořit novou úlohu.

4. Zadejte název své úlohy, **myASAjob** a podle pokynů zvolte skupinu prostředků a umístění.

5. Vyberte **Odeslat do Azure**. Protokoly najdete v okně výstup. 

6. Když je vaše úloha vytvořená, můžete ji zobrazit v **průzkumníkovi Stream Analytics**.

![Vypsat úlohu v Průzkumníkovi Stream Analytics](./media/quick-create-vs-code/list-job.png)


## <a name="run-the-iot-simulator"></a>Spuštění simulátoru IoT

1. Otevřete [online simulátor Azure IoT PI](https://azure-samples.github.io/raspberry-pi-web-simulator/) na nové kartě nebo v okně prohlížeče.

2. Zástupný text na řádku 15 nahraďte připojovacím řetězcem zařízení Azure IoT Hub, který jste uložili v předchozí části.

3. Klikněte na **Run** (Spustit). Výstup by měl ukazovat data a zprávy snímače, které se odesílají do vašeho IoT Hub.

   ![Simulátor Azure IoT online pro maliny PI](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

1. Otevřete **Stream Analytics Explorer** v Visual Studio Code a najděte svoji úlohu **myASAJob**.

2. Klikněte pravým tlačítkem myši na název úlohy. Pak v místní nabídce vyberte možnost **Spustit** .

   ![Spustit úlohu Stream Analytics v VS Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. V místním okně vyberte **nyní** a spusťte úlohu.

4. Všimněte si, že stav úlohy se změnil na **spuštěno**. Kliknutím pravým tlačítkem myši na název úlohy a výběrem možnosti **Otevřít zobrazení úlohy na portálu** Zobrazte metriky vstupní a výstupní události. Tato akce může trvat několik minut.

5. Chcete-li zobrazit výsledky, otevřete úložiště objektů BLOB v rozšíření Visual Studio Code nebo v Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchou Stream Analyticsovou úlohu pomocí Visual Studio Code. Stream Analytics úlohy můžete nasadit také pomocí [Azure Portal](stream-analytics-quick-create-portal.md), [PowerShellu](stream-analytics-quick-create-powershell.md)a sady Visual Studio (Stream-Analytics-Quick-Create-vs.MD). 

Další informace o nástrojích Azure Stream Analytics Tools for Visual Studio najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Použití sady Visual Studio k zobrazení Azure Stream Analyticsch úloh](stream-analytics-vs-tools.md)
