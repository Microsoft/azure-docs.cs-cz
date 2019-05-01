---
title: Vytvořit úlohu Azure Stream Analytics cloudu ve Visual Studio Code (Preview)
description: V tomto rychlém startu se dozvíte, jak začít vytvořením úlohy Stream Analytics, konfigurací vstupů a výstupů a o definici dotazu pomocí Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: dd6d527020bbf5e2fb510fa9605af408673e89dd
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514233"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Rychlý start: Vytvořit úlohu Azure Stream Analytics cloudu ve Visual Studio Code (Preview)

V tomto rychlém startu se dozvíte, jak vytvořit a spustit úlohu Stream Analytics pomocí rozšíření Azure Stream Analytics pro Visual Studio Code. Úlohy příklad přečte streamovaná data ze služby IoT Hub device. Můžete definovat úlohu, která vypočítá průměrná teplota, při více než 27° a zapíše výsledný výstup události do nového souboru v úložišti objektů blob.

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

* Nainstalujte [Visual Studio Code](https://code.visualstudio.com/).

* Stáhněte si [Azure Stream Analytics rozšíření pro VS Code](https://usqldownload.blob.core.windows.net/ext/asa/vscode-asa-0.0.2.vsix).

## <a name="install-the-azure-stream-analytics-extension"></a>Nainstalujte rozšíření Azure Stream Analytics

Nainstalujte rozšíření Azure Stream Analytics z privátní balíčku VSIX, který jste stáhli.

1. Otevřete Visual Studio Code.

2. Z **rozšíření** v levém podokně vyberte symbol tří teček **(...)**  v pravém horním rohu. Potom vyberte **instalace ze souboru VSIX**.

   ![Instalace ze souboru VSIX v aplikaci Visual Studio Code](./media/quick-create-vs-code/install-vsix.png)

3. Vyberte rozšíření, které jste stáhli jako předpoklad a vyberte **nainstalovat**.  To může trvat několik sekund.

4. Po úspěšném dokončení instalace vyberte **teď znovu načíst** v zobrazeném okně, pokud se zobrazí výzva.

5. Ověřte, že **Azure Stream Analytics Tools** je viditelná ve vaší **povolené rozšíření**.

   ![Azure Stream Analytics Tools v seznamu povolených rozšíření ve Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Aktivovat rozšíření Azure Stream Analytics

1. Vyberte **Azure** ikonu na panelu aktivit VS Code. **Stream Analytics** se nebude zobrazovat v postranní panel. V části **Stream Analytics**vyberte **přihlášení k Azure**. 

   ![Přihlaste se k Azure v sadě Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Když jste přihlášeni, zobrazí se název svého účtu Azure na stavovém řádku v levém horním rohu okna nástroje VS Code.

> [!NOTE]
> Nástroje Azure Stream Analytics automaticky přihlásí v příštím Pokud nemáte Odhlásit se. Pokud má váš účet dvojúrovňové ověřování, doporučuje se, že používáte ověřovací telefonní místo používání kódu PIN.
> Pokud máte problémy s výpisy seznamů prostředků, odhlaste se a přihlásit znovu obvykle pomůže. Chcete-li odhlásit, zadejte příkaz `Azure: Sign Out`.

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

   ![Vytvořit IoT Hub](./media/quick-create-vs-code/create-iot-hub.png)

4. Vyberte **Další: Nastavení velikosti a měřítka**.

5. Zvolte si **Úroveň ceny a škálování**. Pro účely tohoto rychlého startu vyberte **F1 – Free** vrstvy, pokud je stále k dispozici v rámci předplatného. Pokud není k dispozici na úrovni free, zvolte na nejnižší úrovni k dispozici. Další informace najdete v tématu [ceny služby IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Velikosti a škálování služby IoT Hub](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Vyberte **Zkontrolovat a vytvořit**. Zkontrolujte informace o službě IoT Hub a klikněte na tlačítko **vytvořit**. Služby IoT Hub může trvat několik minut pro vytvoření. Průběh můžete sledovat v podokně **Oznámení**.

7. V navigační nabídce služby IoT Hub, klikněte na tlačítko **přidat** pod **zařízení IoT**. Přidat **ID zařízení** a klikněte na tlačítko **Uložit**.

   ![Přidání zařízení do služby IoT Hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Po vytvoření zařízení ho otevřete z **zařízení IoT** seznamu. Kopírovat **připojovací řetězec – primární klíč** a uložte ho do poznámkového bloku pro pozdější použití.

   ![Zkopírujte připojovací řetězec služby IoT Hub zařízení](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Vytvořit úložiště objektů blob

1. V levém horním rohu webu Azure Portal vyberte **Vytvořit prostředek** > **Úložiště** > **Účet úložiště**.

2. V **vytvořit účet úložiště** podokně zadejte skupinu název, umístění a prostředek účtu úložiště. Zvolte stejné umístění a skupině prostředků jako centra IoT, které jste vytvořili. Pak klikněte na tlačítko **revize + vytvořit** k vytvoření účtu.

   ![Vytvoření účtu úložiště](./media/quick-create-vs-code/create-storage-account.png)

3. Po vytvoření účtu úložiště, vyberte **objekty BLOB** na dlaždici **přehled** podokně.

   ![Přehled účtu úložiště](./media/quick-create-vs-code/blob-storage.png)

4. Z **služby Blob Service** stránce **kontejneru** a zadejte název kontejneru, jako třeba *container1*. Nechte **úroveň veřejného přístupu** jako **privátní (bez anonymního přístupu)** a vyberte **OK**.

   ![Vytvoření kontejneru objektů blob](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Vytvoření projektu Stream Analytics

1. Ve Visual Studio Code, stiskněte klávesu **Ctrl + Shift + P** otevřete paletu příkazů. Zadejte **ASA** a vyberte **Azure Stream Analytics: Vytvoření nového projektu**.

   ![Vytvoření nového projektu](./media/quick-create-vs-code/create-new-project.png)

2. Zadejte název projektu, jako je třeba **myASAproj** a vyberte složku pro váš projekt.

    ![Vytvořit název projektu](./media/quick-create-vs-code/create-project-name.png)

3. Nový projekt bude přidán do pracovního prostoru. Projekt Azure Stream Analytics se skládá ze skriptu dotazu **(*.asaql)**, **JobConfig.json** souboru a **asaproj.json** konfigurační soubor.

   ![Soubory projektu Stream Analytics ve VS Code](./media/quick-create-vs-code/asa-project-files.png)

4. Konfigurační soubor asaproj.json obsahuje vstupy, výstupy a úlohy konfigurační soubor informace potřebné k odeslání úlohy Azure Stream Analytics k Azure.

   ![Soubor konfigurace úlohy Stream Analytics ve VS Code](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Při přidávání vstupy a výstupy z palety příkazů, příslušnými cestami se přidají do **asaproj.json** automaticky. Pokud přidáte nebo odeberete přímo vstupů nebo výstupů na disku, je potřeba ručně přidat nebo odebrat z **asaproj.json**. Můžete také vložit vstupy a výstupy v některém umístit pak odkazovat na ně v různé úlohy zadáním cesty v každém **asaproj.json**.

## <a name="define-an-input"></a>Definujte vstupní hodnota

1. Vyberte **Ctrl + Shift + P** otevřete paletu příkazů a zadejte **Azure Stream Analytics: Přidat vstup**.

   ![Přidat vstup Stream Analytics ve VS Code](./media/quick-create-vs-code/add-input.png)

2. Zvolte **služby IoT Hub** pro typ vstupu.

   ![Vyberte službu IoT Hub jako možnost vstupu](./media/quick-create-vs-code/iot-hub.png)

3. Zvolte skript dotazu Azure Stream Analytics, který bude používat vstup. By měl automaticky naplnit cesta k souboru **myASAproj.asaql**.

   ![Vyberte skript, Azure Stream Analytics ve Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Zadejte název vstupního souboru jako **IotHub.json**.

5. Upravit **IoTHub.json** s použitím následujících hodnot. Ponechte výchozí hodnoty pro pole není uvedená níže. Použití CodeLens vám pomůžou zadat řetězec nebo vyberte z rozevíracího seznamu.

   |Nastavení|Navrhovaná hodnota|Popis|
   |-------|---------------|-----------|
   |Název|Vstup|Zadejte název pro identifikaci vstupu úlohy.|
   |IotHubNamespace|MyASAIoTHub|Vyberte nebo zadejte název služby IoT Hub. Služby IoT Hub názvy jsou automaticky zjišťují, pokud jsou vytvořené v rámci stejného předplatného.|
   |Koncový bod|Zasílání zpráv| |
   |SharedAccessPolicyName|iothubowner| |

## <a name="define-an-output"></a>Definování výstup

1. Vyberte **Ctrl + Shift + P** otevřete paletu příkazů. Potom zadejte **Azure Stream Analytics: Přidat výstup**.

   ![Přidat výstupní Stream Analytics ve VS Code](./media/quick-create-vs-code/add-output.png)

2. Zvolte **úložiště objektů Blob** pro typ jímky.

3. Zvolte skript dotazu Azure Stream Analytics, který bude používat tento vstup.

4. Zadejte název výstupního souboru jako **BlobStorage.json**.

5. Upravit **BlobStorage.json** s použitím následujících hodnot. Ponechte výchozí hodnoty pro pole není uvedená níže. Použití CodeLens vám pomůžou zadat řetězec nebo vyberte z rozevíracího seznamu.

   |Nastavení|Navrhovaná hodnota|Popis|
   |-------|---------------|-----------|
   |Název|Výstup| Zadejte název pro identifikaci výstupu úlohy.|
   |Účet úložiště|asaquickstartstorage|Vyberte nebo zadejte název účtu úložiště. Pokud jsou názvy vytvořeny v rámci stejného předplatného, zjišťují se automaticky.|
   |Kontejner|container1|Vyberte existující kontejner, který jste vytvořili v účtu úložiště.|
   |Vzor cesty|output|Zadejte název cesty k souboru, která se vytvoří v rámci kontejneru.|

## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

1. Otevřít **myASAproj.asaql** ze složky projektu.

2. Přidejte následující dotaz:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="compile-script"></a>Kompilace skriptu

Kompilace skriptu provede dvě věci: Zkontrolujte syntaxi a generování šablon Azure Resource Manageru pro automatické nasazení.

Existují dva způsoby, jak aktivovat kompilace skriptu:

1. Vyberte skript, který z pracovního prostoru a potom aktivovat kompilace z palety příkazů. 

   ![Použití VS Code paletu příkazů pro kompilaci skriptu](./media/quick-create-vs-code/compile-script1.png)

2. Klikněte pravým tlačítkem na skript a vyberte **Azure Stream Analytics: kompilace skriptu**.

    ![Klikněte pravým tlačítkem na skript ASA kompilace](./media/quick-create-vs-code/compile-script2.png)

3. Po kompilaci najdete dvě vygenerované šablony Azure Resource Manageru v **nasadit** složky vašeho projektu. Tyto dva soubory se používají pro automatické nasazení.

## <a name="submit-a-stream-analytics-job-to-azure"></a>Odeslání úlohy Stream Analytics do Azure

1. V okně Skript editoru Visual Studio Code, vyberte **vyberte z vašich předplatných**.

   ![Vyberte z předplatných textu v editoru skriptů](./media/quick-create-vs-code/select-subscription.png)

2. Vyberte své předplatné ze seznamu místní nabídky.

3. Vyberte **vyberte úlohu**. Pak zvolte možnost vytvořit novou úlohu.

4. Zadejte název úlohy, **myASAjob** a pak postupujte podle pokynů pro výběr skupiny prostředků a umístění.

5. Vyberte **odeslat do Azure**. Protokoly najdete ve výstupním okně. 

6. Při vytvoření úlohy, zobrazí se v Průzkumníku Stream Analytics.

## <a name="run-the-iot-simulator"></a>Spustit simulátor IoT

1. Otevřít [Online simulátor Raspberry Pi Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/) v nové záložce prohlížeče nebo okno.

2. Nahraďte zástupný text v řádku 15 připojovací řetězec služby Azure IoT Hub zařízení, které jste si uložili v předchozí části.

3. Klikněte na **Run** (Spustit). Výstup by měl zobrazit data ze senzorů a zprávy odeslané do služby IoT Hub.

   ![Online simulátor Raspberry Pi Azure IoT](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

1. Otevřete Průzkumníka Stream Analytics ve Visual Studio Code a vyhledejte vaši úlohu**myASAJob**.

2. Klikněte pravým tlačítkem na název úlohy. Vyberte **Start** v místní nabídce.

3. Zvolte **nyní** v automaticky otevíraném okně spustíte úlohu.

4. Poznámka: stav úlohy se změnila na **systémem**. Klikněte pravým tlačítkem na název projektu a zvolte **otevřete zobrazení úloh portálu** naleznete v tématu vstupní a výstupní události metriky. Může to trvat několik minut.

5. Chcete-li zobrazit výsledky, otevřete úložiště objektů blob v rozšíření Visual Studio Code nebo na webu Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal vyberte **Skupiny prostředků** a potom zvolte název vytvořeného prostředku.  

2. Na stránce skupiny prostředků zvolte **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a potom vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili jednoduchou úlohu Stream Analytics pomocí nástroje Visual Studio Code. Můžete také nasadit pomocí úloh Stream Analytics [webu Azure portal](stream-analytics-quick-create-portal.md), [Powershellu](stream-analytics-quick-create-powershell.md)a sady Visual Studio (datový proud analytics rychlé – vytvořit – vs.md). 

Další informace o Azure Stream Analytics tools for Visual Studio, pokračujte v následujícím článku:

> [!div class="nextstepaction"]
> [Chcete-li zobrazit úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-vs-tools.md)