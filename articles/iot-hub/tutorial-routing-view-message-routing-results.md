---
title: Zobrazení výsledků směrování zpráv služby Azure IoT Hub (.NET) | Microsoft Docs
description: Po nastavení všech prostředků pomocí části 1 tohoto kurzu přidejte možnost směrovat zprávy do Azure Stream Analytics a zobrazit výsledky v PowerBI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: ddba41acfcfe497059a661b701906cda0d4d77c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398353"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Kurz: část 2 – zobrazení směrovaných zpráv

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Pravidla pro směrování zpráv

Toto jsou pravidla pro směrování zpráv; Ty byly nastavily v části 1 tohoto kurzu a v této druhé části je fungují.

|Hodnota |Výsledek|
|------|------|
|level="storage" |Zapsat do úložiště Azure Storage.|
|level="critical" |Zapsat do fronty Service Bus. Aplikace logiky načte zprávu z fronty a pomocí Office 365 ji odešle e-mailem.|
|default |Zobrazte tato data pomocí Power BI.|

Nyní vytvoříte prostředky, na které budou směrovány zprávy, spustíte aplikaci pro posílání zpráv do centra a provedete směrování v akci.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky  

Fronta Service Bus se použije pro příjem zpráv označených jako kritické. Dále vytvořte aplikaci logiky pro monitorování fronty Service Bus, která odešle e-mail, kdykoli se ve frontě objeví nová zpráva.

1. V [Azure Portal](https://portal.azure.com)vyberte **+ vytvořit prostředek**. Do vyhledávacího pole zadejte **aplikace logiky** a stiskněte Enter. V zobrazených výsledcích hledání vyberte aplikace logiky a pak vyberte **vytvořit** , abyste mohli pokračovat v podokně **vytvořit aplikaci logiky** . Vyplňte jednotlivá pole.

   **Název:** V tomto poli je název aplikace logiky. Tento kurz používá **ContosoLogicApp**.

   **Předplatné**: Vyberte své předplatné Azure.

   **Skupina prostředků**: vyberte **použít existující** a vyberte svou skupinu prostředků. Tento kurz používá **ContosoResources**.

   **Umístění:** Použijte vaše umístění. Tento kurz používá **USA – západ**.

   **Log Analytics:** Tento přepínač by měl být vypnutý.

   ![Obrazovka vytvoření aplikace logiky](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Vyberte **Vytvořit**. Nasazení aplikace může trvat několik minut.

2. Teď přejděte do aplikace logiky. Nejjednodušší způsob, jak se dostat do aplikace logiky, je vybrat **skupiny prostředků**, vybrat skupinu prostředků (Tento kurz používá **ContosoResources**) a pak vybrat aplikaci logiky ze seznamu prostředků. 

    Zobrazí se stránka návrháře aplikace logiky (možná se budete muset kvůli zobrazení celé stránky posunout doprava). Na stránce návrháře Logic Apps se posuňte dolů, dokud se nezobrazí dlaždice s textem **prázdná aplikace logiky +** a vyberte ji. Výchozí karta je "za vás". Pokud je toto podokno prázdné, výběrem možnost **vše** zobrazíte všechny konektory a triggery, které jsou k dispozici.

3. V seznamu konektorů vyberte **Service Bus** .

   ![Seznam konektorů](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Zobrazí se seznam aktivačních událostí. Vyberte, **kdy se má zpráva přijmout do fronty (automatické dokončování) nebo Service Bus**.

   ![Seznam aktivačních událostí pro Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Na další obrazovce zadejte Název připojení. Tento kurz používá **ContosoConnection**.

   ![Nastavení připojení pro frontu Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Vyberte obor názvů Service Bus. Tento kurz používá **ContosoSBNamespace**. Když vyberete obor názvů, portál se dotáže oboru názvů služby Service Bus a načte klíče. Vyberte **RootManageSharedAccessKey** a vyberte **vytvořit**.

   ![Dokončuje se nastavování připojení.](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Na další obrazovce vyberte z rozevíracího seznamu název fronty (tento kurz používá **contososbqueue**). Pro zbývající pole můžete použít výchozí hodnoty.

   ![Možnosti fronty](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Nyní nastavte akci odeslání e-mailu při přijetí nové zprávy do fronty. V Návrháři Logic Apps vyberte **+ Nový krok** a přidejte krok a pak výběrem možnosti **vše** zobrazte všechny dostupné možnosti. V podokně **Zvolte akci** vyhledejte a vyberte **Office 365 Outlook**. Na obrazovce akce vyberte **Odeslat e-mail/Office 365 Outlook**.  

   ![Možnosti Office 365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Přihlaste se ke svému pracovnímu nebo školnímu účtu a nastavte připojení. Pokud vyprší časový limit, zkuste to znovu. Zadejte e-mailové adresy příjemců e-mailu. Také zadejte předmět a zprávu, kterou chcete mít v těle zprávy. Pro testování zadejte jako adresu příjemce vlastní e-mail.

   Pokud chcete zobrazit obsah zprávy, kterou můžete zahrnout, vyberte **Přidat dynamický obsah** . Vyberte **Obsah** – příchozí zpráva bude zahrnuta do e-mailu.

   ![Možnosti e-mailu pro aplikaci logiky](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Vyberte **Uložit**. Pak návrháře aplikace logiky zavřete.

## <a name="set-up-azure-stream-analytics"></a>Nastavení služby Azure Stream Analytics

Pokud chcete zobrazit data ve vizualizaci Power BI, nejprve vytvořte úlohu Stream Analytics pro načítání dat. Mějte na paměti, že pouze zprávy, jejichž vlastnost **level** je **normal**, se odesílají výchozímu koncovému bodu a budou úlohou Stream Analytics vizualizované v PowerBI.

### <a name="create-the-stream-analytics-job"></a>Vytvoření úlohy služby Stream Analytics

1. V [Azure Portal](https://portal.azure.com)vyberte **vytvořit prostředek**  >  **Internet věcí**  >  **Stream Analytics úlohu**.

2. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný. Tento kurz používá **contosoJob**.

   **Předplatné**: předplatné Azure, které používáte pro tento kurz.

   **Skupina prostředků:** Použijte stejnou skupinu prostředků jako pro centrum IoT. Tento kurz používá **ContosoResources**.

   **Umístění:** Použijte stejné umístění, které používáte v instalačním skriptu. Tento kurz používá **USA – západ**.

   ![Vytvoření úlohy Stream Analytics](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Vyberte **vytvořit** a vytvořte úlohu. Nasazení může trvat několik minut.

    Pokud se chcete vrátit do úlohy, vyberte **skupiny prostředků**. Tento kurz používá **ContosoResources**. Vyberte skupinu prostředků a potom v seznamu prostředků vyberte úlohu Stream Analytics.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **topologie úlohy**vyberte **vstupy**.

2. V podokně **vstupy** vyberte **Přidat vstup streamu** a vyberte IoT Hub. Na další obrazovce vyplňte následující pole:

   **Alias pro vstup:** Tento kurz používá **contosoinputs**.

   **V předplatném vyberte IoT Hub**: Vyberte tuto možnost přepínače.

   **Předplatné**: vyberte předplatné Azure, které používáte pro tento kurz.

   **IoT Hub**: vyberte Centrum IoT. Tento kurz používá **ContosoTestHub**.

   **Koncový bod:** vyberte **Zasílání zpráv**. (Pokud vyberete monitorování operací, získáte namísto procházejících dat telemetrická data o centru IoT.) 

   **Název zásady sdíleného přístupu**: vyberte **Služba**. Portál vyplní Klíč zásad sdíleného přístupu za vás.

   **Skupina příjemců**: vyberte skupinu uživatelů nastavenou v části 1 tohoto kurzu. Tento kurz používá **contosoconsumers**.
   
   Pro zbývající pole můžete použít výchozí hodnoty. 

   ![Nastavte vstupy pro úlohu Stream Analytics.](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Vyberte **Uložit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **topologie úlohy**vyberte **výstupy**.

2. V podokně **výstupy** vyberte **Přidat**a pak vyberte **Power BI**. Na další obrazovce vyplňte následující pole:

   **Alias pro výstup:** Jedinečný alias pro výstup. Tento kurz používá **contosooutputs**. 

   **Název datové sady:** Název datové sady, kterou chcete v Power BI použít. Tento kurz používá **contosodataset**. 

   **Název tabulky:** Název tabulky, kterou chcete v Power BI použít. Tento kurz používá **contosotable**.

   Pro zbývající pole můžete použít výchozí hodnoty.

3. Vyberte **autorizovat**a přihlaste se ke svému účtu Power BI. (Může to trvat více než jeden pokus).

   ![Nastavte výstupy pro úlohu Stream Analytics.](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Vyberte **Uložit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **topologie úlohy**vyberte možnost **dotaz**.

2. Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy. Tento kurz používá **contosoinputs**.

3. Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup. Tento kurz používá **contosooutputs**.

   ![Nastavte dotaz pro úlohu Stream Analytics.](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Vyberte **Uložit**.

5. Zavřete podokno dotazu. Vrátíte se do zobrazení prostředků ve skupině prostředků. Vyberte úlohu Stream Analytics. V tomto kurzu má název **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V Stream Analytics úlohy vyberte **Spustit**  >  **nyní**  >  **Start**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

K vytvoření sestavy Power BI potřebujete data, takže Power BI je potřeba po vytvoření zařízení a spuštění aplikace simulace zařízení nastavit.

## <a name="run-simulated-device-app"></a>Spustit aplikaci simulovaného zařízení

V části 1 tohoto kurzu jste nastavili zařízení pro simulaci pomocí zařízení IoT. V této části si stáhnete konzolovou aplikaci .NET, která simuluje, že zařízení odesílá zprávy ze zařízení do cloudu do služby IoT Hub (za předpokladu, že jste už nestáhli aplikaci a prostředky v části 1).

Tato aplikace odesílá zprávy pro každou z různých metod směrování zpráv. K dispozici je také složka v souboru ke stažení, která obsahuje úplnou Azure Resource Manager šablonu a soubor parametrů a také skripty Azure CLI a PowerShell.

Pokud jste soubory nestáhli z úložiště v části 1 tohoto kurzu, pokračujte a Stáhněte si je hned z [simulace zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Výběr tohoto odkazu stáhne úložiště s několika aplikacemi. řešení, které hledáte, je IoT-Hub/kurzy/Routing/IoT_SimulatedDevice. sln. 

Dvojím kliknutím na soubor řešení (IoT_SimulatedDevice. sln) otevřete kód v aplikaci Visual Studio a pak otevřete Program.cs. Nahraďte `{your hub name}` názvem hostitele centra IoT. Formát názvu hostitele centra IoT je **{iot-hub-name}.azure-devices.net**. V tomto kurzu je název hostitele centra **ContosoTestHub.azure-devices.net**. Dále nahraďte `{your device key}` klíčem zařízení, který jste si předtím uložili při vytváření simulovaného zařízení. 

   ```csharp
        static string s_myDeviceId = "Contoso-Test-Device";
        static string s_iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string s_deviceKey = "{your device key}";
   ```

## <a name="run-and-test"></a>Spuštění a testování

Spusťte konzolovou aplikaci. Počkejte několik minut. Na obrazovce konzoly aplikace uvidíte odesílané zprávy.

Aplikace odesílá zprávy typu zařízení-cloud do centra IoT každou sekundu. Zpráva obsahuje serializovaný objekt JSON s ID zařízení, teplotou, vlhkostí a úrovní zprávy, jejíž výchozí hodnota je `normal`. Úrovni zprávy se náhodně přiřazuje `critical` nebo `storage`, což způsobí směrování zprávy do účtu úložiště nebo do fronty Service Bus (což aktivuje aplikaci logiky, která odešle e-mail). Zprávy s výchozí hodnotou `normal` se zobrazí v sestavě BI, kterou připravíte v dalším kroku.

Pokud je všechno správně nastavené, v tomto okamžiku byste měli získat následující výsledky:

1. Začnete dostávat e-maily o kritických zprávách.

   ![Výsledné e-maily](./media/tutorial-routing-view-message-routing-results/results-in-email.png)

   Tento výsledek znamená, že následující příkazy jsou pravdivé. 

   * Směrování do fronty Service Bus pracuje správně.
   * Aplikace logiky přebírající zprávy z fronty Service Bus pracuje správně.
   * Konektor aplikace logiky do Outlooku pracuje správně. 

2. V [Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků** a vyberte skupinu prostředků. Tento kurz používá **ContosoResources**. 

    Vyberte účet úložiště, vyberte **kontejnery**a pak vyberte kontejner. Tento kurz používá **contosoresults**. Měli byste vidět složku, ve které můžete procházet adresáře, dokud neuvidíte jeden nebo několik souborů. Otevřete jeden z těchto souborů; obsahují položky směrované do účtu úložiště. 

   ![Soubory výsledků v úložišti](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Tento výsledek znamená, že následující příkaz má hodnotu true.

   * Směrování do účtu úložiště pracuje správně.

Teď, když je aplikace pořád spuštěná, nastavte vizualizaci Power BI, abyste viděli zprávy přicházející ve výchozím směrování.

## <a name="set-up-the-power-bi-visualizations"></a>Nastavení vizualizací Power BI

1. Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/).

2. Přejděte na **Pracovní prostory** a vyberte pracovní prostor, který jste nastavili při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **My Workspace**. 

3. Vyberte **datové sady**. Pokud nemáte žádné datové sady, počkejte pár minut a zkuste to znovu.

   Měli byste vidět datovou sadu určenou při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **contosodataset**. (Zobrazení datové sady může na začátku 5 až 10 minut trvat.)

4. V části **Akce**vyberte první ikonu pro vytvoření sestavy.

   ![Power BI pracovní prostor s zvýrazněnými akcemi a ikonou sestavy](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Vytvořte spojnicový graf zobrazující v reálném čase vývoj teploty.

   * Na stránce vytváření sestavy přidejte spojnicový graf výběrem ikony spojnicového grafu.

     ![Vizualizace a pole](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * V podokně **Pole** rozbalte tabulku, kterou jste určili při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **contosotable**.

   * Přetáhněte **EventEnqueuedUtcTime** na **Osu** v podokně **Vizualizace**.

   * Přetáhněte položku **temperature** na **Hodnoty**.

   Vytvoří spojnicový graf. Na ose x bude datum a čas v časovém pásmu UTC. Na ose y bude hodnota snímače teploty.

6. Vytvořte jiný spojnicový graf zobrazující v reálném čase vývoj vlhkosti. Pro nastavení druhého grafu použijte stejný postup jako u prvního grafu a umístěte **EventEnqueuedUtcTime** na osu x (**osu**) a **vlhkost** na ose y (**hodnoty**).

   ![Poslední Power BI sestava pomocí dvou grafů](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Vyberte **Uložit** , pokud chcete sestavu uložit, a pokud se zobrazí výzva, zadejte název sestavy.

Nyní byste měli vidět příchozí data v obou grafech. Tento výsledek znamená, že jsou splněné následující příkazy:

   * Směrování do výchozího koncového bodu pracuje správně.
   * Úloha Azure Stream Analytics správně streamuje.
   * Vizualizace Power BI je nastavená správně.

Kliknutím na tlačítko Aktualizovat v horní části okna Power BI můžete aktualizovat grafy, aby se zobrazila nejnovější data. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud chcete odebrat všechny prostředky Azure, které jste vytvořili v obou částech tohoto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V tomto případě se odebere centrum IoT, obor názvů a fronta Service Bus, aplikace logiky, účet úložiště i samotná skupina prostředků. Můžete také odebrat prostředky Power BI a vymazat e-maily odeslané v průběhu tohoto kurzu.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Vyčištění prostředků ve vizualizaci Power BI

Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/). Přejděte do svého pracovního prostoru. Tento kurz používá **My Workspace**. Chcete-li odebrat vizualizaci Power BI, klikněte na datové sady a vyberte ikonu odpadkového koše pro odstranění datové sady. Tento kurz používá **contosodataset**. Pokud datovou sadu odeberete, odebere se také sestava.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Vyčištění prostředků pomocí Azure CLI

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup` byl nastaven na **ContosoResources** zpět na začátku tohoto kurzu.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Vyčištění prostředků pomocí PowerShellu

Pokud chcete odebrat skupinu prostředků, použijte příkaz [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) . `$resourceGroup` byl nastaven na **ContosoResources** zpět na začátku tohoto kurzu.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Vyčištění testovacích e-mailů

Možná budete chtít také odstranit množství e-mailů ve vaší doručené poště, které byly vygenerovány prostřednictvím aplikace logiky v době, kdy byla aplikace zařízení spuštěna.

## <a name="next-steps"></a>Další kroky

V tomto 2 kurzu jste zjistili, jak používat směrování zpráv ke směrování IoT Hubch zpráv do různých umístění, a to prováděním následujících úkolů.  

**Část I: vytvoření prostředků, nastavení směrování zpráv**
> [!div class="checklist"]
> * Vytvoření prostředků – služby IoT Hub, účtu úložiště, fronty Service Bus a simulovaného zařízení.
> * Konfigurace koncových bodů a směrování zpráv v IoT Hub pro účet úložiště a Service Bus fronty.

**Část II: odeslání zpráv do centra, zobrazení směrovaných výsledků**
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
> [Nastavení a použití metrik a diagnostiky s IoT Hub](tutorial-use-metrics-and-diags.md)
