---
title: Zobrazení zpráv služby Azure IoT Hub směrování výsledky (.NET) | Dokumentace Microsoftu
description: Zobrazení výsledků směrování zpráv služby Azure IoT Hub
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: f34799bbf2142ba07c29915deae5b5dbe590c9fc
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330533"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Kurz: Část 2 – zobrazení směrování zpráv

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Pravidla pro směrování zpráv

Jedná se o pravidla pro směrování zpráv. Tyto byly nastaveny v části 1 tohoto kurzu a jejich fungování v této druhé části uvidíte.

|value |Výsledek|
|------|------|
|level="storage" |Zapsat do úložiště Azure Storage.|
|level="critical" |Zapsat do fronty Service Bus. Aplikace logiky načte zprávu z fronty a pomocí Office 365 ji odešle e-mailem.|
|default |Zobrazte tato data pomocí Power BI.|

Teď vytvoříte prostředky, ke kterým se zprávy budou směrovat, spustí aplikaci pro odesílání zpráv do centra a v tématu směrování v akci.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky  

Fronta Service Bus se použije pro příjem zpráv označených jako kritické. Dále vytvořte aplikaci logiky pro monitorování fronty Service Bus, která odešle e-mail, kdykoli se ve frontě objeví nová zpráva.

1. V [webu Azure portal](https://portal.azure.com)vyberte **+ vytvořit prostředek**. Do vyhledávacího pole zadejte **aplikace logiky** a stiskněte Enter. Ve výsledcích hledání zobrazí, vyberte aplikaci logiky a pak vyberte **vytvořit** a pokračujte **vytvořit aplikaci logiky** podokně. Vyplňte jednotlivá pole.

   **Název**: Toto pole je název aplikace logiky. Tento kurz používá **ContosoLogicApp**.

   **Předplatné**: Vyberte své předplatné Azure.

   **Skupina prostředků**: Vyberte **použít existující** a vyberte skupinu prostředků. Tento kurz používá **ContosoResources**.

   **Umístění**: Použijte vaši polohu. Tento kurz používá **Západní USA**.

   **Log Analytics**: Toto přepínací tlačítko by měla být vypnuta.

   ![Vytvoření aplikace logiky obrazovky](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Vyberte **Vytvořit**.

2. Teď přejděte do aplikace logiky. Nejjednodušší způsob, jak získat aplikace logiky je výběr **skupiny prostředků**, vyberte skupinu prostředků (Tento kurz používá **ContosoResources**), ze seznamu prostředků vyberte aplikaci logiky. Zobrazí se stránka návrháře aplikace logiky (možná se budete muset kvůli zobrazení celé stránky posunout doprava). Na stránce návrhář pro Logic Apps, přejděte dolů na položku se zobrazí dlaždice s upozorněním **prázdná aplikace logiky +** a vyberte ji. Výchozí kartu se "Pro vás". Pokud toto podokno je prázdný, vyberte **všechny** zobrazíte všechny konektory a triggery, které jsou k dispozici.

3. Vyberte **služby Service Bus** ze seznamu konektorů.

   ![Seznam konektorů](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Zobrazí se seznam aktivačních událostí. Vyberte **při doručení zprávy do fronty (automatické dokončení) nebo Service Bus**.

   ![Seznam aktivačních událostí pro službu Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Na další obrazovce zadejte Název připojení. Tento kurz používá **ContosoConnection**.

   ![Nastavení připojení pro frontu služby Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Vyberte obor názvů služby Service Bus. Tento kurz používá **ContosoSBNamespace**. Když vyberete obor názvů, portál se dotáže oboru názvů služby Service Bus a načte klíče. Vyberte **RootManageSharedAccessKey** a vyberte **vytvořit**.

   ![Dokončuje se nastavování připojení](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Na další obrazovce vyberte z rozevíracího seznamu název fronty (tento kurz používá **contososbqueue**). Pro zbývající pole můžete použít výchozí hodnoty.

   ![Možnosti fronty](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Nyní nastavte akci odeslání e-mailu při přijetí nové zprávy do fronty. V návrháři pro Logic Apps, vyberte **+ nový krok** Pokud chcete přidat krok, pak vyberte **všechny** zobrazíte všechny dostupné možnosti. V **zvolte akci** podokno, vyhledejte a vyberte **Office 365 Outlook**. Na obrazovce aktivační události vyberte **odeslání e-mailu a Office 365 Outlook**.  

   ![Možnosti Office 365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Přihlaste se ke svému účtu Office 365 k nastavení připojení. Pokud to vyprší časový limit, stačí znovu. Zadejte e-mailové adresy příjemců e-mailu. Také zadejte předmět a zprávu, kterou chcete mít v těle zprávy. Pro testování zadejte jako adresu příjemce vlastní e-mail.

   Vyberte **Přidat dynamický obsah** zobrazíte obsah ze zprávy, který může obsahovat. Vyberte **Obsah** – příchozí zpráva bude zahrnuta do e-mailu.

   ![Možnosti e-mailu pro aplikaci logiky](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Vyberte **Uložit**. Pak návrháře aplikace logiky zavřete.

## <a name="set-up-azure-stream-analytics"></a>Nastavení služby Azure Stream Analytics

Pokud chcete zobrazit data ve vizualizaci Power BI, nejprve vytvořte úlohu Stream Analytics pro načítání dat. Mějte na paměti, že pouze zprávy, jejichž vlastnost **level** je **normal**, se odesílají výchozímu koncovému bodu a budou úlohou Stream Analytics vizualizované v PowerBI.

### <a name="create-the-stream-analytics-job"></a>Vytvoření úlohy služby Stream Analytics

1. V [webu Azure portal](https://portal.azure.com)vyberte **vytvořit prostředek** > **Internet of Things** > **úlohy Stream Analytics**.

2. Zadejte o úloze následující informace.

   **Název úlohy**: Název úlohy. Název musí být globálně jedinečný. Tento kurz používá **contosoJob**.

   **Předplatné**: Předplatné Azure, který používáte pro tento kurz.

   **Skupina prostředků**: Použijte stejnou skupinu prostředků, které jsou používané služby IoT hub. Tento kurz používá **ContosoResources**.

   **Umístění**: Použijte stejné umístění, které jsou používané ve skriptu instalace. Tento kurz používá **Západní USA**.

   ![Vytvoření úlohy stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Vyberte **vytvořit** vytvořit příslušnou úlohu. Chcete-li se vrátit do úlohy, vyberte **skupiny prostředků**. Tento kurz používá **ContosoResources**. Vyberte skupinu prostředků a potom v seznamu prostředků vyberte úlohu Stream Analytics.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

4. V části **topologie úlohy**vyberte **vstupy**.

5. V **vstupy** vyberte **přidat vstup streamu** a vyberte službu IoT Hub. Na další obrazovce vyplňte následující pole:

   **Vstupní alias**: Tento kurz používá **contosoinputs**.

   **Vyberte službu IoT Hub ze svého předplatného**: Vyberte tuto možnost tlačítko přepínače.

   **Předplatné**: Vyberte předplatné Azure, které používáte pro účely tohoto kurzu.

   **IoT Hub**: Vyberte službu IoT Hub. Tento kurz používá **ContosoTestHub**.

   **Koncový bod**: Vyberte **zasílání zpráv**. (Pokud vyberete monitorování operací, získáte namísto procházejících dat telemetrická data o centru IoT.) 

   **Název zásad sdíleného přístupu**: Vyberte **služby**. Portál vyplní Klíč zásad sdíleného přístupu za vás.

   **Skupina uživatelů**: Vyberte skupinu příjemců nastavili v kroku 1 tohoto kurzu. Tento kurz používá **contosoconsumers**.
   
   Pro zbývající pole můžete použít výchozí hodnoty. 

   ![Nastavení vstup pro úlohu stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

6. Vyberte **Uložit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **topologie úlohy**vyberte **výstupy**.

2. V **výstupy** vyberte **přidat**a pak vyberte **Power BI**. Na další obrazovce vyplňte následující pole:

   **Alias pro výstup**: Jedinečný alias pro výstup. Tento kurz používá **contosooutputs**. 

   **Název datové sady**: Název datové sady, který se má použít v Power BI. Tento kurz používá **contosodataset**. 

   **Název tabulky**: Název tabulky, který se má použít v Power BI. Tento kurz používá **contosotable**.

   Pro zbývající pole můžete použít výchozí hodnoty.

3. Vyberte **Authorize**a přihlaste se ke svému účtu Power BI. (To může trvat více než jeden pokus).

   ![Nastavit výstupy pro úlohu stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Vyberte **Uložit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** vyberte **Dotaz**.

2. Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy. Tento kurz používá **contosoinputs**.

3. Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup. Tento kurz používá **contosooutputs**.

   ![Nastavit dotaz na úlohu stream analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Vyberte **Uložit**.

5. Zavřete podokno dotazu. Můžete vrátit k zobrazení prostředků ve skupině prostředků. Vyberte úlohu Stream Analytics. V tomto kurzu má název **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics, vyberte **Start** > **nyní** > **Start**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

K vytvoření sestavy Power BI potřebujete data, takže Power BI je potřeba po vytvoření zařízení a spuštění aplikace simulace zařízení nastavit.

## <a name="run-simulated-device-app"></a>Spusťte aplikaci simulovaného zařízení

V části 1 tohoto kurzu můžete nastavit zařízení pro simulaci zařízení IoT pomocí. V této části si stáhnout konzolové aplikace .NET, která simuluje zařízení odesílá zprávy typu zařízení cloud do služby IoT hub, za předpokladu, že jste již stáhněte aplikaci a prostředků v části 1 tohoto kurzu.

Tato aplikace odesílá zprávy pro jednotlivé metody směrování jinou zprávu. Je zde i složku stahování, který obsahuje úplnou šablonu Azure Resource Manageru a soubor parametrů, jakož i rozhraní příkazového řádku Azure a skripty prostředí PowerShell.

Pokud se nepovedlo stáhnout soubory z úložiště v kroku 1 tohoto kurzu, pokračujte a stáhněte si je teď na [simulace zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Vyberte tento odkaz ke stažení úložiště s několika aplikacemi v. řešení, které hledáte, je iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Poklikejte na soubor řešení (IoT_SimulatedDevice.sln) otevřete kód v sadě Visual Studio a pak otevřete soubor Program.cs. Nahraďte `{iot hub hostname}` názvem hostitele centra IoT. Formát názvu hostitele centra IoT je **{iot-hub-name}.azure-devices.net**. V tomto kurzu je název hostitele centra **ContosoTestHub.azure-devices.net**. Dále nahraďte `{device key}` klíčem zařízení, který jste si předtím uložili při vytváření simulovaného zařízení. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Spuštění a testování

Spusťte konzolovou aplikaci. Počkejte několik minut. Na obrazovce konzoly aplikace uvidíte odesílané zprávy.

Aplikace odesílá zprávy typu zařízení-cloud do centra IoT každou sekundu. Zpráva obsahuje serializovaný objekt JSON s ID zařízení, teplotou, vlhkostí a úrovní zprávy, jejíž výchozí hodnota je `normal`. Úrovni zprávy se náhodně přiřazuje `critical` nebo `storage`, což způsobí směrování zprávy do účtu úložiště nebo do fronty Service Bus (což aktivuje aplikaci logiky, která odešle e-mail). Zprávy s výchozí hodnotou `normal` se zobrazí v sestavě BI, kterou připravíte v dalším kroku.

Pokud je všechno správně nastavené, v tomto okamžiku byste měli získat následující výsledky:

1. Začnete dostávat e-maily o kritických zprávách.

   ![Výsledný e-mailů](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   To znamená, že následující tvrzení jsou pravdivá. 

   * Směrování do fronty Service Bus pracuje správně.
   * Aplikace logiky přebírající zprávy z fronty Service Bus pracuje správně.
   * Konektor aplikace logiky do Outlooku pracuje správně. 

2. V [webu Azure portal](https://portal.azure.com)vyberte **skupiny prostředků** a vyberte skupinu prostředků. Tento kurz používá **ContosoResources**. Vyberte účet úložiště, vyberte **objekty BLOB**, potom vyberte kontejner. Tento kurz používá **contosoresults**. Měli byste vidět složku, ve které můžete procházet adresáře, dokud neuvidíte jeden nebo několik souborů. Otevřete jeden z těchto souborů; obsahují položky směrované do účtu úložiště. 

   ![Soubory s výsledky ve službě storage](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Tohoto výsledku znamená, že platí následující příkaz.

   * Směrování do účtu úložiště pracuje správně.

Nyní se stále běžící aplikací vytvořte vizualizaci Power BI pro zprávy přicházející přes výchozí směrování.

## <a name="set-up-the-power-bi-visualizations"></a>Nastavení vizualizací Power BI

1. Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/).

2. Přejděte na **Pracovní prostory** a vyberte pracovní prostor, který jste nastavili při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **My Workspace**. 

3. Vyberte **datových sad**. Pokud nemáte žádné datové sady, počkejte několik minut a zkontrolovat znovu.

   Měli byste vidět datovou sadu určenou při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **contosodataset**. (Zobrazení datové sady může na začátku 5 až 10 minut trvat.)

4. V části **akce**, vyberte ikonu první k vytvoření sestavy.

   ![Pracovní prostor Power BI s ikonou akce a sestava zvýrazněný](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Vytvořte spojnicový graf zobrazující v reálném čase vývoj teploty.

   * Na stránce vytváření sestav přidejte spojnicový graf tak, že vyberete ikonu spojnicového grafu.

     ![Vizualizace a pole](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * V podokně **Pole** rozbalte tabulku, kterou jste určili při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **contosotable**.

   * Přetáhněte **EventEnqueuedUtcTime** na **Osu** v podokně **Vizualizace**.

   * Přetáhněte položku **temperature** na **Hodnoty**.

   Vytvoří spojnicový graf. Na ose x bude datum a čas v časovém pásmu UTC. Na ose y bude hodnota snímače teploty.

6. Vytvořte jiný spojnicový graf zobrazující v reálném čase vývoj vlhkosti. Při vytváření druhého grafu postupujte stejně a přetáhněte **EventEnqueuedUtcTime** na osu x a **humidity** na osu y.

   ![Konečná sestava Power BI s dva grafy](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Vyberte **Uložit** pro uložení sestavy.

Nyní byste měli vidět příchozí data v obou grafech. To znamená, že jsou splněny následující příkazy:

   * Směrování do výchozího koncového bodu pracuje správně.
   * Úloha Azure Stream Analytics správně streamuje.
   * Vizualizace Power BI je nastavená správně.

Můžete aktualizovat grafů zobrazíte nejnovější data tak, že vyberete tlačítko Aktualizovat v horní části okna Power BI. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud chcete odebrat všechny prostředky, které jste vytvořili prostřednictvím obě části tohoto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V tomto případě se odebere centrum IoT, obor názvů a fronta Service Bus, aplikace logiky, účet úložiště i samotná skupina prostředků. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Vyčištění prostředků ve vizualizaci Power BI

Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/). Přejděte do svého pracovního prostoru. Tento kurz používá **My Workspace**. Pokud chcete odebrat vizualizace Power BI, přejděte do datové sady a vyberte Koše můžete ikonu odstranění datové sady. Tento kurz používá **contosodataset**. Pokud datovou sadu odeberete, odebere se také sestava.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Pomocí Azure CLI pro vyčištění prostředků

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` byl nastaven na **ContosoResources** zpět na začátku tohoto kurzu.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Použití Powershellu k vyčištění prostředků

Chcete-li odebrat skupinu prostředků, použijte [odebrat AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) příkazu. `$resourceGroup` byl nastaven na **ContosoResources** zpět na začátku tohoto kurzu.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu část 2 jste zjistili, jak pomocí směrování zpráv pro směrování zpráv ve službě IoT Hub do různých cílů provedením následujících úloh.  

**Část I: Vytvoření prostředků, nastavte směrování zpráv**
> [!div class="checklist"]
> * Vytvoření prostředků – služby IoT hub, účet úložiště, fronty Service Bus a simulovaných zařízení.
> * Konfigurace koncových bodů a směrování zpráv ve službě IoT Hub pro účet úložiště a fronty služby Service Bus.

**Část II: Odesílání zpráv do centra, zobrazení směrované výsledků**
> [!div class="checklist"]
> * Vytvoření aplikace logiky, která se aktivuje a odešle e-mail, kdykoli se ve frontě Service Bus objeví nová zpráva.
> * Stažení a spuštění aplikaci, která bude simulovat IoT zařízení odesílající zprávy do centra s různými možnosti směrování.
> * Vytvoření vizualizace Power BI pro data odesílaná výchozím koncovým bodem.
> * Zobrazení výsledků...
> * ...ve frontě Service Bus a v e-mailech.
> * ...v účtu úložiště.
> * ...ve vizualizaci Power BI.

V dalším kurzu se dozvíte, jak spravovat stav zařízení IoT. 

> [!div class="nextstepaction"]
> [Nastavení a Diagnostika a metriky pomocí služby IoT Hub](tutorial-use-metrics-and-diags.md)
