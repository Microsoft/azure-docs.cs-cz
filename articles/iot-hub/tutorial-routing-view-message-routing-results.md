---
title: Zobrazení výsledků směrování zpráv služby Azure IoT Hub (.NET) | Dokumenty společnosti Microsoft
description: Po nastavení všech prostředků pomocí části 1 kurzu přidejte možnost směrování zpráv do Azure Stream Analytics a zobrazte výsledky v PowerBI.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2018
ms.author: robinsh
ms.custom: mvc
ms.openlocfilehash: bfee4e64070e5f37eaa3d63280409f00c0ed8672
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73890393"
---
# <a name="tutorial-part-2---view-the-routed-messages"></a>Kurz: Část 2 - Zobrazení směrovaných zpráv

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rules-for-routing-the-messages"></a>Pravidla pro směrování zpráv

Toto jsou pravidla pro směrování zpráv; tyto byly nastaveny v části 1 tohoto kurzu a uvidíte je pracovat v této druhé části.

|Hodnota |Výsledek|
|------|------|
|level="storage" |Zapsat do úložiště Azure Storage.|
|level="critical" |Zapsat do fronty Service Bus. Aplikace logiky načte zprávu z fronty a pomocí Office 365 ji odešle e-mailem.|
|default |Zobrazte tato data pomocí Power BI.|

Nyní můžete vytvořit prostředky, do kterých budou zprávy směrovány, spustit aplikaci pro odesílání zpráv do centra a zobrazit směrování v akci.

## <a name="create-a-logic-app"></a>Vytvoření aplikace logiky  

Fronta Service Bus se použije pro příjem zpráv označených jako kritické. Dále vytvořte aplikaci logiky pro monitorování fronty Service Bus, která odešle e-mail, kdykoli se ve frontě objeví nová zpráva.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **+ Vytvořit prostředek**. Do vyhledávacího pole zadejte **aplikace logiky** a stiskněte Enter. Ze zobrazených výsledků hledání vyberte Aplikace logiky a pak vyberte **Vytvořit** a pokračujte v podokně **Vytvořit aplikaci logiky.** Vyplňte jednotlivá pole.

   **Název:** V tomto poli je název aplikace logiky. Tento kurz používá **ContosoLogicApp**.

   **Předplatné**: Vyberte své předplatné Azure.

   **Skupina prostředků**: Vyberte **Použít existující** a vyberte skupinu prostředků. Tento kurz používá **ContosoResources**.

   **Umístění:** Použijte vaše umístění. Tento kurz používá **USA – západ**.

   **Log Analytics:** Tento přepínač by měl být vypnutý.

   ![Obrazovka Vytvořit aplikaci logiky](./media/tutorial-routing-view-message-routing-results/create-logic-app.png)

   Vyberte **Vytvořit**. Nasazení aplikace může trvat několik minut.

2. Teď přejděte do aplikace logiky. Nejjednodušší způsob, jak se dostat do aplikace logiky, je vybrat **skupiny prostředků**, vyberte skupinu prostředků (tento kurz používá **ContosoResources**), a pak vyberte aplikaci logiky ze seznamu prostředků. 

    Zobrazí se stránka návrháře aplikace logiky (možná se budete muset kvůli zobrazení celé stránky posunout doprava). Na stránce Návrhář eaplikací logiky přejděte dolů, dokud se nezobrazí dlaždice s nápisem **Prázdná aplikace logiky +** a vyberte ji. Výchozí karta je "Pro vás". Pokud je toto podokno prázdné, vyberte **Vše,** chcete-li zobrazit všechny dostupné konektory a aktivační události.

3. Ze seznamu konektorů vyberte **Service Bus.**

   ![Seznam konektorů](./media/tutorial-routing-view-message-routing-results/logic-app-connectors.png)

4. Zobrazí se seznam aktivačních událostí. Vyberte **Při přijetí zprávy ve frontě (automatické dokončování) / Service Bus**.

   ![Seznam aktivačních událostí pro sběrnici Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-triggers.png)

5. Na další obrazovce zadejte Název připojení. Tento kurz používá **ContosoConnection**.

   ![Nastavení připojení fronty služby Service Bus](./media/tutorial-routing-view-message-routing-results/logic-app-define-connection.png)

   Vyberte obor názvů Service Bus. Tento kurz používá **ContosoSBNamespace**. Když vyberete obor názvů, portál se dotáže oboru názvů služby Service Bus a načte klíče. Vyberte **RootManageSharedAccessKey** a vyberte **Vytvořit**.

   ![Dokončení nastavení připojení](./media/tutorial-routing-view-message-routing-results/logic-app-finish-connection.png)

6. Na další obrazovce vyberte z rozevíracího seznamu název fronty (tento kurz používá **contososbqueue**). Pro zbývající pole můžete použít výchozí hodnoty.

   ![Možnosti fronty](./media/tutorial-routing-view-message-routing-results/logic-app-queue-options.png)

7. Nyní nastavte akci odeslání e-mailu při přijetí nové zprávy do fronty. V Návrháři aplikací logiky vyberte **+ Nový krok** pro přidání kroku a pak vyberte **Vše,** abyste viděli všechny dostupné možnosti. V **podokně Zvolte akce** vyhledejte a vyberte **Office 365 Outlook**. Na obrazovce Akce vyberte **Odeslat e-mail / Office 365 Outlook**.  

   ![Možnosti Office365](./media/tutorial-routing-view-message-routing-results/logic-app-select-outlook.png)

8. Přihlaste se ke svému účtu Office 365 a nastavte připojení. Pokud tento časový doby ven, zkuste to znovu. Zadejte e-mailové adresy příjemců e-mailu. Také zadejte předmět a zprávu, kterou chcete mít v těle zprávy. Pro testování zadejte jako adresu příjemce vlastní e-mail.

   Vyberte **Přidat dynamický obsah,** chcete-li zobrazit obsah ze zprávy, kterou můžete zahrnout. Vyberte **Obsah** – příchozí zpráva bude zahrnuta do e-mailu.

   ![Možnosti e-mailu pro aplikaci logiky](./media/tutorial-routing-view-message-routing-results/logic-app-send-email.png)

9. Vyberte **Uložit**. Pak návrháře aplikace logiky zavřete.

## <a name="set-up-azure-stream-analytics"></a>Nastavení služby Azure Stream Analytics

Pokud chcete zobrazit data ve vizualizaci Power BI, nejprve vytvořte úlohu Stream Analytics pro načítání dat. Mějte na paměti, že pouze zprávy, jejichž vlastnost **level** je **normal**, se odesílají výchozímu koncovému bodu a budou úlohou Stream Analytics vizualizované v PowerBI.

### <a name="create-the-stream-analytics-job"></a>Vytvoření úlohy služby Stream Analytics

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **Vytvořit úlohu** > Internet**of Things** > **Stream Analytics**.

2. Zadejte o úloze následující informace.

   **Název úlohy:** Název, který chcete úloze dát. Název musí být globálně jedinečný. Tento kurz používá **contosoJob**.

   **Předplatné**: Předplatné Azure, které používáte pro kurz.

   **Skupina prostředků:** Použijte stejnou skupinu prostředků jako pro centrum IoT. Tento kurz používá **ContosoResources**.

   **Umístění:** Použijte stejné umístění, které používáte v instalačním skriptu. Tento kurz používá **USA – západ**.

   ![Vytvoření úlohy analýzy datového proudu](./media/tutorial-routing-view-message-routing-results/stream-analytics-create-job.png)

3. Chcete-li úlohu vytvořit, vyberte **vytvořit.** Nasazení může trvat několik minut.

    Chcete-li se k úloze vrátit, vyberte **možnost Skupiny zdrojů**. Tento kurz používá **ContosoResources**. Vyberte skupinu prostředků a v seznamu zdrojů vyberte úlohu Stream Analytics.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy**vyberte **Vstupy**.

2. V podokně **Vstupy** vyberte **Přidat vstup datového proudu** a vyberte IoT Hub. Na další obrazovce vyplňte následující pole:

   **Alias pro vstup:** Tento kurz používá **contosoinputs**.

   **Vyberte ioT hub z předplatného**: Vyberte tuto možnost přepínacího tlačítka.

   **Předplatné**: Vyberte předplatné Azure, které používáte pro tento kurz.

   **IoT Hub**: Vyberte centrum IoT Hub. Tento kurz používá **ContosoTestHub**.

   **Koncový bod:** vyberte **Zasílání zpráv**. (Pokud vyberete monitorování operací, získáte namísto procházejících dat telemetrická data o centru IoT.) 

   **Název zásady sdíleného přístupu**: Vyberte **službu**. Portál vyplní Klíč zásad sdíleného přístupu za vás.

   **Skupina spotřebitelů**: Vyberte skupinu spotřebitelů nastavenou v části 1 tohoto kurzu. Tento kurz používá **contosoconsumers**.
   
   Pro zbývající pole můžete použít výchozí hodnoty. 

   ![Nastavení vstupů pro úlohu analýzy datového proudu](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-inputs.png)

3. Vyberte **Uložit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy**vyberte **Výstupy**.

2. V podokně **Výstupy** vyberte **Přidat**a pak vyberte **Power BI**. Na další obrazovce vyplňte následující pole:

   **Alias pro výstup:** Jedinečný alias pro výstup. Tento kurz používá **contosooutputs**. 

   **Název datové sady:** Název datové sady, kterou chcete v Power BI použít. Tento kurz používá **contosodataset**. 

   **Název tabulky:** Název tabulky, kterou chcete v Power BI použít. Tento kurz používá **contosotable**.

   Pro zbývající pole můžete použít výchozí hodnoty.

3. Vyberte **Autorizovat**a přihlaste se ke svému účtu Power BI. (To může trvat více než jeden pokus).

   ![Nastavení výstupů pro úlohu analýzy datového proudu](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-outputs.png)

4. Vyberte **Uložit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

1. V části **Topologie úlohy** vyberte **Dotaz**.

2. Nahraďte `[YourInputAlias]` názvem aliasu pro vstup úlohy. Tento kurz používá **contosoinputs**.

3. Nahraďte `[YourOutputAlias]` názvem aliasu pro výstup. Tento kurz používá **contosooutputs**.

   ![Nastavení dotazu pro úlohu analýzy datového proudu](./media/tutorial-routing-view-message-routing-results/stream-analytics-job-query.png)

4. Vyberte **Uložit**.

5. Zavřete podokno dotazu. Vrátíte se k zobrazení zdrojů ve skupině zdrojů. Vyberte úlohu Stream Analytics. V tomto kurzu má název **contosoJob**.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics vyberte **Spustit** > **nyní** > **spustit**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

K vytvoření sestavy Power BI potřebujete data, takže Power BI je potřeba po vytvoření zařízení a spuštění aplikace simulace zařízení nastavit.

## <a name="run-simulated-device-app"></a>Spuštění aplikace simulovaných zařízení

V části 1 tohoto kurzu nastavíte zařízení tak, aby simulovalo pomocí zařízení IoT. V této části si stáhnete aplikaci konzoly .NET, která simuluje toto zařízení odesílající zprávy zařízení cloud do centra IoT (za předpokladu, že jste aplikaci a prostředky v části 1 ještě nestáhli).

Tato aplikace odesílá zprávy pro každou z různých metod směrování zpráv. V souboru ke stažení je také složka, která obsahuje úplnou šablonu a soubor parametrů Azure Resource Manageru, stejně jako skripty Azure CLI a PowerShell.

Pokud jste nestáhli soubory z úložiště v části 1 tohoto kurzu, pokračujte a stáhněte si je nyní ze [simulace zařízení IoT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Výběrem tohoto odkazu stáhnete úložiště s několika aplikacemi v něm; řešení, které hledáte, je iot-hub/Tutorials/Routing/IoT_SimulatedDevice.sln. 

Poklepáním na soubor řešení (IoT_SimulatedDevice.sln) otevřete kód v sadě Visual Studio a otevřete Program.cs. Nahraďte `{your hub name}` názvem hostitele centra IoT. Formát názvu hostitele centra IoT je **{iot-hub-name}.azure-devices.net**. V tomto kurzu je název hostitele centra **ContosoTestHub.azure-devices.net**. Dále nahraďte `{your device key}` klíčem zařízení, který jste si předtím uložili při vytváření simulovaného zařízení. 

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

2. Na [webu Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků** a vyberte skupinu prostředků. Tento kurz používá **ContosoResources**. 

    Vyberte účet úložiště, vyberte **Kontejnery**a pak vyberte Kontejner. Tento kurz používá **contosoresults**. Měli byste vidět složku, ve které můžete procházet adresáře, dokud neuvidíte jeden nebo několik souborů. Otevřete jeden z těchto souborů; obsahují položky směrované do účtu úložiště. 

   ![Výsledné soubory v úložišti](./media/tutorial-routing-view-message-routing-results/results-in-storage.png)

Tento výsledek znamená, že následující příkaz je true.

   * Směrování do účtu úložiště pracuje správně.

Teď, když je aplikace stále spuštěná, nastavte vizualizaci Power BI tak, aby se uviděly zprávy přicházející přes výchozí směrování.

## <a name="set-up-the-power-bi-visualizations"></a>Nastavení vizualizací Power BI

1. Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/).

2. Přejděte na **Pracovní prostory** a vyberte pracovní prostor, který jste nastavili při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **My Workspace**. 

3. Vyberte **datové sady**. Pokud nemáte žádné datové sady, počkejte několik minut a zkontrolujte znovu.

   Měli byste vidět datovou sadu určenou při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **contosodataset**. (Zobrazení datové sady může na začátku 5 až 10 minut trvat.)

4. V části **AKCE**vyberte první ikonu pro vytvoření sestavy.

   ![Pracovní prostor Power BI se zvýrazněnou ikonou Akce a sestavy](./media/tutorial-routing-view-message-routing-results/power-bi-actions.png)

5. Vytvořte spojnicový graf zobrazující v reálném čase vývoj teploty.

   * Na stránce vytvoření sestavy přidejte spojnicový graf výběrem ikony spojnicového grafu.

     ![Vizualizace a pole](./media/tutorial-routing-view-message-routing-results/power-bi-visualizations-and-fields.png)

   * V podokně **Pole** rozbalte tabulku, kterou jste určili při vytváření výstupu pro úlohu služby Stream Analytics. Tento kurz používá **contosotable**.

   * Přetáhněte **EventEnqueuedUtcTime** na **Osu** v podokně **Vizualizace**.

   * Přetáhněte položku **temperature** na **Hodnoty**.

   Vytvoří spojnicový graf. Na ose x bude datum a čas v časovém pásmu UTC. Na ose y bude hodnota snímače teploty.

6. Vytvořte jiný spojnicový graf zobrazující v reálném čase vývoj vlhkosti. Chcete-li nastavit druhý graf, postupujte stejným způsobem pro první graf, umístění **EventEnqueuedUtcTime** na osu x (**osa**) a **vlhkost** na ose y (**Hodnoty**).

   ![Závěrečná sestava Power BI se dvěma grafy](./media/tutorial-routing-view-message-routing-results/power-bi-report.png)

7. Vyberte **Uložit,** chcete-li sestavu uložit, zadejte název sestavy, pokud se zobrazí výzva.

Nyní byste měli vidět příchozí data v obou grafech. Tento výsledek znamená, že platí následující příkazy:

   * Směrování do výchozího koncového bodu pracuje správně.
   * Úloha Azure Stream Analytics správně streamuje.
   * Vizualizace Power BI je nastavená správně.

Grafy můžete aktualizovat a zobrazit nejnovější data tak, že v horní části okna Power BI vyberete tlačítko Aktualizovat. 

## <a name="clean-up-resources"></a>Vyčištění prostředků 

Pokud chcete odebrat všechny prostředky Azure, které jste vytvořili prostřednictvím obou částí tohoto kurzu, odstraňte skupinu prostředků. Tato akce odstraní všechny prostředky, které skupina obsahuje. V tomto případě se odebere centrum IoT, obor názvů a fronta Service Bus, aplikace logiky, účet úložiště i samotná skupina prostředků. Můžete taky odebrat prostředky Power BI a vymazat e-maily odeslané během kurzu.

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Vyčištění prostředků ve vizualizaci Power BI

Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/). Přejděte do svého pracovního prostoru. Tento kurz používá **My Workspace**. Pokud chcete vizualizaci Power BI odebrat, přejděte na Datové sady a vyberte ikonu koše a datovou sadu smažete. Tento kurz používá **contosodataset**. Pokud datovou sadu odeberete, odebere se také sestava.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Použití azure cli k vyčištění prostředků

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). `$resourceGroup`byl nastaven na **ContosoResources** zpět na začátku tohoto kurzu.

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="use-powershell-to-clean-up-resources"></a>Čištění prostředků pomocí Prostředí PowerShell

Chcete-li skupinu prostředků odebrat, použijte příkaz [Odebrat azResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) `$resourceGroup`byl nastaven na **ContosoResources** zpět na začátku tohoto kurzu.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

### <a name="clean-up-test-emails"></a>Vyčištění testovacích e-mailů

Můžete také odstranit množství e-mailů ve složce doručené pošty, které byly generovány prostřednictvím aplikace logiky, zatímco aplikace zařízení byla spuštěna.

## <a name="next-steps"></a>Další kroky

V tomto dvoudílném kurzu jste se naučili používat směrování zpráv ke směrování zpráv služby IoT Hub do různých cílů provedením následujících úkolů.  

**Část I: Vytvoření zdrojů, nastavení směrování zpráv**
> [!div class="checklist"]
> * Vytvořte prostředky – centrum IoT, účet úložiště, frontu service bus a simulované zařízení.
> * Nakonfigurujte koncové body a trasy zpráv v centru IoT Hub pro účet úložiště a frontu service bus.

**Část II: Odesílání zpráv do centra, zobrazení směrovaných výsledků**
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
> [Nastavení a použití metrik a diagnostiky pomocí centra IoT Hub](tutorial-use-metrics-and-diags.md)
