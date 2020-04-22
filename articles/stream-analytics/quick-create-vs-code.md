---
title: Úvodní příručka – vytvoření úlohy Azure Stream Analytics v kódu Visual Studia
description: Tento rychlý start ukazuje, jak začít vytvořením úlohy Stream Analytics, konfigurací vstupů a výstupů a definováním dotazu pomocí kódu sady Visual Studio.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: fe666359b9ed2773f615ff496f4032bda5e74dc2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767767"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Úvodní příručka: Vytvoření úlohy Azure Stream Analytics v kódu Visual Studia (preview)

Tento rychlý start ukazuje, jak vytvořit a spustit úlohu Azure Stream Analytics pomocí rozšíření Nástroje Azure StreamAnalytics pro kód Visual Studia. Ukázková úloha čte streamovaná data ze zařízení Azure IoT Hub. Definujete úlohu, která vypočítá průměrnou teplotu při překročení 27° a zapíše výsledné výstupní události do nového souboru v úložišti objektů blob.

> [!NOTE]
> Nástroje Visual Studio a Visual Studio Code nepodporují úlohy v oblastech Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod.

## <a name="before-you-begin"></a>Než začnete

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

* Nainstalujte [kód sady Visual Studio](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Instalace rozšíření Nástroje Azure Stream Analytics

1. Otevřete Visual Studio Code.

2. V **rozšířeních** v levém podokně vyhledejte **Stream Analytics** a vyberte **Instalovat** v rozšíření Nástroje Azure **Stream Analytics.**

3. Po instalaci rozšíření ověřte, že **nástroje Azure Stream Analytics** jsou viditelné v **povolených rozšířeních**.

   ![Nástroje Azure Stream Analytics v povolených rozšířeních v kódu Visual Studia](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Aktivace rozšíření Nástroje Azure Stream Analytics

1. Vyberte ikonu **Azure** na panelu aktivit kódu Visual Studia. V **části Stream Analytics** na postranním panelu vyberte **Přihlásit se k Azure**.

   ![Přihlášení k Azure v kódu Visual Studia](./media/quick-create-vs-code/azure-sign-in.png)

2. Když jste přihlášení, název účtu Azure se zobrazí na stavovém řádku v levém dolním rohu okna kódu Visual Studia.

> [!NOTE]
> Rozšíření Nástroje Azure Stream Analytics vás příště automaticky podepíše, pokud se neodhlásíte. Pokud má váš účet dvoufaktorové ověřování, doporučujeme použít místo pinu telefonní ověřování.
> Pokud máte problémy se seznamem zdrojů, obvykle pomáhá odhlášení a přihlášení. Chcete-li se odhlásit, zadejte příkaz `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Před definováním úlohy Stream Analytics byste měli připravit data, která jsou později nakonfigurována jako vstup úlohy. Chcete-li připravit vstupní data, která úloha vyžaduje, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **Vytvořit prostředek** > **Internet věcí** > **IoT Hub**.

3. V podokně **IoT Hub** zadejte následující informace:

   |**Nastavení**  |**Navrhovaná hodnota**  |**Popis**  |
   |---------|---------|---------|
   |Předplatné  | \<Vaše předplatné\> |  Vyberte předplatné Azure, které chcete použít. |
   |Skupina prostředků   |   asaquickstart-resourcegroup  |   Vyberte **Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. |
   |Region (Oblast)  |  \<Vyberte oblast nejbližší vašim uživatelům.\> | Vyberte geografickou polohu, kde můžete hostovat své centrum IoT. Použijte umístění, které je vašim uživatelům nejblíže. |
   |Název centra IoT  | MyASAIoTHub  |   Vyberte název centra IoT.   |

   ![Vytvoření centra IoT](./media/quick-create-vs-code/create-iot-hub.png)

4. Vyberte **další: Nastavit velikost a měřítko**.

5. Vytvořte výběr pro **cenovou a škálovací úroveň**. Pro tento rychlý start vyberte úroveň **F1 – Free,** pokud je stále dostupná ve vašem předplatném. Pokud volná úroveň není k dispozici, zvolte nejnižší dostupnou úroveň. Další informace najdete v [tématu Azure IoT Hub ceny](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Velikost a škálování ioT hubu](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte informace o centru IoT hub a vyberte **Vytvořit**. Vytvoření centra IoT může trvat několik minut. Průběh můžete sledovat v podokně **Oznámení.**

7. V navigační nabídce služby IoT hub vyberte **přidat** v části **Zařízení IoT**. Přidejte ID pro **ID zařízení**a vyberte **Uložit**.

   ![Přidání zařízení do centra IoT hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Po vytvoření zařízení otevřete zařízení ze seznamu **zařízení IoT.** Zkopírujte řetězec v **připojovacím řetězci (primární klíč)** a uložte jej do poznámkového bloku, který chcete použít později.

   ![Kopírování připojovacího řetězce zařízení centra IoT](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Spuštění simulátoru IoT

1. Otevřete [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) v novém kartě prohlížeče nebo v okně.

2. Nahraďte zástupný symbol na řádku 15 připojovacím řetězcem zařízení ioT hubu, který jste uložili dříve.

3. Vyberte **Run** (Spustit). Výstup by měl zobrazovat data senzoru a zprávy, které jsou odesílány do centra IoT hub.

   ![Pi Online simulátor Raspberry Pi Azure IoT s výstupem](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Vytvoření úložiště objektů blob

1. V levém horním rohu portálu Azure vyberte **Vytvořit** > **účet úložiště****prostředků** > .

2. V podokně **Vytvořit účet úložiště** zadejte název účtu úložiště, umístění a skupinu prostředků. Zvolte stejné umístění a skupinu prostředků jako službu IoT hub, kterou jste vytvořili. Pak vyberte **Zkontrolovat + vytvořit** pro vytvoření účtu.

   ![Vytvoření účtu úložiště](./media/quick-create-vs-code/create-storage-account.png)

3. Po vytvoření účtu úložiště vyberte dlaždici **Objektů blob** v podokně **Přehled.**

   ![Přehled účtu úložiště](./media/quick-create-vs-code/blob-storage.png)

4. Na stránce **Služba objektu Blob** vyberte **Kontejner** a zadejte název kontejneru, například **kontejner1**. Ponechat **úroveň veřejného přístupu** jako **soukromé (bez anonymního přístupu)** a vyberte **OK**.

   ![Vytvoření kontejneru objektů blob](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Vytvoření projektu Stream Analytics

1. V kódu Visual Studia otevřete paletu příkazů **vyberte Ctrl+Shift+P.** Pak zadejte **ASA** a vyberte **ASA: Vytvořit nový projekt**.

   ![Vytvoření nového projektu](./media/quick-create-vs-code/create-new-project.png)

2. Zadejte název projektu, například **myASAproj**, a vyberte složku pro projekt.

    ![Vytvoření názvu projektu](./media/quick-create-vs-code/create-project-name.png)

3. Nový projekt je přidán do pracovního prostoru. Projekt Stream Analytics se skládá ze tří složek: **Vstupy**, **Výstupy**a **Funkce**. Má také skript dotazu **(*.asaql)**, soubor **JobConfig.json** a konfigurační soubor **asaproj.json.**

    Konfigurační soubor **asaproj.json** obsahuje informace o vstupech, výstupech a konfiguračním souboru úlohy úlohy úlohy Stream Analytics do Azure.

    ![Soubory projektu Stream Analytics v kódu Visual Studia](./media/quick-create-vs-code/asa-project-files.png)

> [!Note]
> Když přidáváte vstupy a výstupy z palety příkazů, odpovídající cesty se automaticky přidají do **asaproj.json.** Pokud přidáte nebo odeberete vstupy nebo výstupy přímo na disk, je třeba je ručně přidat nebo odebrat z **asaproj.json**. Můžete se rozhodnout umístit vstupy a výstupy na jedno místo a pak na ně odkazovat v různých úlohách zadáním cest v každém souboru **asaproj.json.**

## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

1. Otevřete **soubor myASAproj.asaql** ze složky projektu.

2. Přidejte následující dotaz:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="define-a-live-input"></a>Definování živého vstupu

1. Klikněte pravým tlačítkem myši na složku **Vstupy** v projektu Stream Analytics. Pak vyberte **ASA: Přidat vstup** z kontextové nabídky.

    ![Přidání vstupu ze složky Vstupy](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

    Nebo vyberte **Ctrl+Shift+P,** chcete-li otevřít paletu příkazů a zadat **ASA: Přidat vstup**.

   ![Přidání vstupu Stream Analytics v kódu Visual Studia](./media/quick-create-vs-code/add-input.png)

2. Zvolte **IoT Hub** pro typ vstupu.

   ![Vybrat centrum IoT jako vstupní možnost](./media/quick-create-vs-code/iot-hub.png)

3. Pokud jste přidali vstup z palety příkazů, zvolte skript dotazu Stream Analytics, který bude tento vstup používat. Měla by být automaticky naplněna cestou souboru k **myASAproj.asaql**.

   ![Výběr skriptu Stream Analytics v kódu Visual Studia](./media/quick-create-vs-code/asa-script.png)

4. Z rozbalovací nabídky zvolte **Vybrat ze svých předplatných Azure.**

    ![Výběr z předplatných](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Upravte nově generovaný soubor **IoTHub1.json** s následujícími hodnotami. Zachovat výchozí hodnoty pro pole, která zde nejsou uvedena.

   |Nastavení|Navrhovaná hodnota|Popis|
   |-------|---------------|-----------|
   |Název|Vstup|Zadejte název pro identifikaci vstupu úlohy.|
   |IotHubNamespace|MyASAIoTHub|Zvolte nebo zadejte název centra IoT hub. Názvy služby IoT hub se automaticky detekují, pokud jsou vytvořeny ve stejném předplatném.|
   |SharedAccessPolicyName|iothubowner| |

   Funkci CodeLens můžete použít k zadání řetězce, výběru z rozevíracího seznamu nebo změně textu přímo v souboru. Následující snímek obrazovky ukazuje **Vybrat z vašich předplatných** jako příklad. Pověření jsou automaticky uvedena a uložena v místním správci pověření.

   ![Konfigurace vstupu v kódu sady Visual Studio](./media/quick-create-vs-code/configure-input.png)

   ![Konfigurace vstupní hodnoty v kódu sady Visual Studio](./media/quick-create-vs-code/configure-input-value.png)

## <a name="preview-input"></a>Náhled vstupu

Z **horního** řádku vyberte náhled dat v **ioTHub1.json.** Některá vstupní data budou načtena z centra IoT hub a zobrazí se v okně náhledu. Tento proces může chvíli trvat.

 ![Náhled živého vstupu](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-an-output"></a>Definování výstupu

1. Vyberte **Ctrl+Shift+P,** chcete-li otevřít paletu příkazů. Potom zadejte **ASA: Přidat výstup**.

   ![Přidání výstupu Stream Analytics v kódu Visual Studia](./media/quick-create-vs-code/add-output.png)

2. Zvolte **úložiště objektů blob** pro typ jímky.

3. Zvolte skript dotazu Stream Analytics, který bude tento vstup používat.

4. Zadejte název výstupního souboru jako **BlobStorage**.

5. Upravte **blobStorage** pomocí následujících hodnot. Zachovat výchozí hodnoty pro pole, která zde nejsou uvedena. Pomocí funkce CodeLens můžete vybrat z rozevíracího seznamu nebo zadat řetězec.

   |Nastavení|Navrhovaná hodnota|Popis|
   |-------|---------------|-----------|
   |Název|Výstup| Zadejte název pro identifikaci výstupu úlohy.|
   |Účet úložiště|asaquickstartstorage|Zvolte nebo zadejte název svého účtu úložiště. Názvy účtů úložiště se automaticky zjistí, pokud jsou vytvořeny ve stejném předplatném.|
   |Kontejner|container1|Vyberte existující kontejner, který jste vytvořili v účtu úložiště.|
   |Vzor cesty|output|Zadejte název cesty k souboru, která se vytvoří v rámci kontejneru.|

   ![Konfigurace výstupu v kódu sady Visual Studio](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Kompilace skriptu

Kompilace skriptu kontroluje syntaxi a generuje šablony Azure Resource Manageru pro automatické nasazení.

Kompilaci skriptu lze spustit dvěma způsoby:

- Vyberte skript z pracovního prostoru a pak se zkompilujte z palety příkazů.

   ![Kompilace skriptu pomocí palety příkazů Kód sady Visual Studio](./media/quick-create-vs-code/compile-script1.png)

- Klepněte pravým tlačítkem myši na skript a vyberte **možnost ASA: Kompilovat skript**.

    ![Chcete-li zkompilovat skript Stream Analytics, klepněte pravým tlačítkem myši na skript Stream Analytics.](./media/quick-create-vs-code/compile-script2.png)

Po kompilaci můžete najít dvě generované šablony Azure Resource Manager ve složce **Nasazení** vašeho projektu. Tyto dva soubory se používají pro automatické nasazení.

![Šablony nasazení Stream Analytics v Průzkumníkovi souborů](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Odeslání úlohy Stream Analytics do Azure

1. V okně editoru skriptů skriptu skriptu vyberte **Odeslat do Azure**.

   ![Výběr z textu předplatných v editoru skriptů](./media/quick-create-vs-code/submit-job.png)

2. Vyberte předplatné z rozbalovacího seznamu.

3. Zvolte **Vybrat úlohu**. Pak zvolte **Vytvořit novou úlohu**.

4. Zadejte název své úlohy **myASAjob**. Potom podle pokynů zvolte skupinu prostředků a umístění.

5. Vyberte **Odeslat do Azure**. Protokoly najdete ve výstupním okně. 

6. Když je vaše úloha vytvořena, uvidíte ji v **Průzkumníku Analýzy streamu**.

    ![Uvedená úloha v Průzkumníku Analýzy streamu](./media/quick-create-vs-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

1. Otevřete **Průzkumníka analýzy streamu** v kódu Visual Studia a najděte svou práci, **myASAJob**.

2. Klikněte pravým tlačítkem myši na název úlohy. Potom vyberte **Spustit** z kontextové nabídky.

   ![Spuštění úlohy Stream Analytics v kódu Visual Studia](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Chcete-li úlohu spustit, zvolte **nyní** v rozbalovacím okně.

4. Všimněte si, že stav úlohy byl změněn na **Spuštěno**. Klikněte pravým tlačítkem myši na název úlohy a v yberte **Otevřít zobrazení úloh y na portálu,** abyste viděli metriky vstupních a výstupních událostí. Tato akce může trvat několik minut.

5. Chcete-li zobrazit výsledky, otevřete úložiště objektů blob v rozšíření kódu Visual Studio nebo na webu Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky. Odstranění úlohy zabrání fakturaci jednotek streamování, které úloha spotřebovává. 

Pokud plánujete tuto úlohu v budoucnu používat, můžete ji zastavit a restartovat později. Pokud tuto úlohu nebudete znovu používat, odstraňte všechny prostředky, které jste vytvořili v tomto rychlém startu, pomocí následujících kroků:

1. V levé nabídce na webu Azure Portal vyberte **skupiny prostředků** a pak vyberte název prostředku, který jste vytvořili.  

2. Na stránce vaší skupiny prostředků vyberte **Odstranit**. Do textového pole zadejte název prostředku, který chcete odstranit, a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchou úlohu Stream Analytics pomocí kódu Visual Studia. Úlohy Stream Analytics můžete nasadit také pomocí [portálu Azure](stream-analytics-quick-create-portal.md), [PowerShellu](stream-analytics-quick-create-powershell.md)a [Visual Studia](stream-analytics-quick-create-vs.md).

Další informace o nástrojích Azure Stream Analytics pro kód Visual Studia najdete v následujících článcích:

* [Test Stream Analytics dotazy místně s ukázkovými daty pomocí kódu Sady Visual Studio](visual-studio-code-local-run.md)

* [Testování úloh Azure Stream Analytics místně proti živému vstupu pomocí kódu Visual Studia](visual-studio-code-local-run-live-input.md)

* [Zobrazení úloh Azure Stream Analytics pomocí kódu Visual Studia](visual-studio-code-explore-jobs.md)

* [Nastavení kanálů CI/CD pomocí balíčku npm](setup-cicd-vs-code.md)
