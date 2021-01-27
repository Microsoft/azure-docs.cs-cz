---
title: Azure Media Services události v Azure Log Analytics: Azure Media Services popis: Naučte se ukládat Azure Media Services události do Azure Log Analytics.
služby: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' ' MS. Service: Media-Services MS. rebavování: MS. téma: tutorial MS. Date: 08/24/2020 MS. Author: inhenkel
---

# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Kurz: ukládání Azure Media Servicesch událostí v Azure Log Analytics

## <a name="azure-media-services-events"></a>Události Azure Media Services

Azure Media Services V3 generuje události na [Azure Event Grid](media-services-event-schemas.md). Můžete se přihlásit k odběru událostí mnoha způsoby a ukládat je do úložišť dat. V tomto kurzu se přihlásíte k odběru Media Servicesch událostí pomocí [toku aplikace protokolu](https://azure.microsoft.com/services/logic-apps/). Aplikace logiky se spustí pro každou událost a uloží tělo události do Azure Log Analytics. Jakmile se události nacházejí v Azure Log Analytics, můžete pomocí dalších služeb Azure vytvořit řídicí panel, monitorování a upozornění na těchto událostech, i když v tomto kurzu to nebudeme.

> [!NOTE]
> To je užitečné, pokud už jste obeznámeni s používáním FFmpeg jako místního kodéru.  Pokud ne, je to v pořádku. Níže je uveden příkazový řádek a pokyny pro streamování videa.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření žádného toku aplikace logiky kódu
> * Témata týkající se přihlášení k odběru Azure Media Services události
> * Analýza událostí a ukládání do Azure Log Analytics
> * Dotazování událostí z Azure Log Analytics

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

> * [Předplatné Azure](how-to-set-azure-subscription.md)
> * Účet [Media Services](create-account-howto.md) a skupina prostředků.
> * Instalace [ffmpeg](https://ffmpeg.org/download.html) pro váš operační systém.
> * Pracovní prostor [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md)

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Přihlášení k odběru události Media Services s využitím aplikace logiky

1. Pokud jste tak již neučinili, vytvořte v Azure Portal pracovní prostor [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md) . Budete potřebovat ID pracovního prostoru a jeden z klíčů, takže okno prohlížeče zůstane otevřené. Pak otevřete portál na jiné kartě nebo v okně.

1. Přejděte k účtu Azure Media Services a vyberte **události**. Zobrazí se všechny metody přihlášení k odběru Azure Media Servicesch událostí.
    > [!div class="mx-imgBorder"]
    > ![Portál Azure Media Services](media/tutorial-events-log-analytics/select-events-01a.png)

1. Vyberte **ikonu Logic Apps** pro vytvoření aplikace logiky. Otevře se Návrhář aplikace logiky, kde můžete vytvořit tok pro zachycení událostí a jejich vložení do Log Analytics. 
    > [!div class="mx-imgBorder"]
    > ![Vytvoření aplikace logiky](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. Vyberte **ikonu +**, vyberte tenanta, kterého chcete použít, a pak vyberte přihlásit se. Zobrazí se výzva k přihlášení Microsoftu.
    > [!div class="mx-imgBorder"]
    > ![Připojení k Azure Event Grid ](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
 ![ Výběr tenanta](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. Vyberte **pokračovat** a přihlaste se k odběru událostí Media Services.
    > [!div class="mx-imgBorder"]
    > ![Připojeno k Azure Event Grid](media/tutorial-events-log-analytics/select-continue-04.png)

1. V seznamu **typ prostředku** vyhledejte "Microsoft. Media. MediaServices".
    > [!div class="mx-imgBorder"]
    >![Azure Media Services události prostředků](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. Vyberte **položku Typ události**. Zobrazí se seznam všech událostí, které Azure Media Services emitují. Můžete vybrat události, které chcete sledovat. Můžete přidat více typů událostí. (Později provedete malou změnu toku aplikace logiky pro uložení každého typu události do samostatného protokolu Log Analytics a šíříte název typu události do Log Analytics názvu protokolu dynamicky.)
    > [!div class="mx-imgBorder"]
    > ![Typ události Azure Media Services](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. Vyberte **Save As** (Uložit jako).

1. Zadejte název aplikace logiky.  Ve výchozím nastavení je vybraná skupina prostředků. Ostatní nastavení ponechte tak, jak jsou, a pak vyberte **vytvořit**.  Vrátíte se na domovskou obrazovku Azure.
    > [!div class="mx-imgBorder"]
    > ![Rozhraní pro pojmenovávání aplikací logiky](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Vytvoření akce

Teď, když jste se přihlásili k odběru událostí, vytvořte akci.

1. Pokud se vám portál vrátil zpátky na domovskou obrazovku, přejděte zpátky do aplikace logiky, kterou jste právě vytvořili, a prohledejte všechny prostředky pro název aplikace.

1. Vyberte svou aplikaci a pak vyberte **Návrhář aplikace logiky**. Otevře se podokno návrháře.

1. Vyberte **+ Nový krok**.

1. Vzhledem k tomu, že chcete události vložit do služby Azure Log Analytics, vyhledejte "Azure Log Analytics" a vyberte "Azure Log Analytics data collector".
    > [!div class="mx-imgBorder"]
    > ![Kolekce dat Azure Log Analytics](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Pokud se chcete připojit k pracovnímu prostoru Log Analytics, budete potřebovat ID pracovního prostoru a klíč agenta. Otevřete Azure Portal na nové kartě nebo v okně, přejděte do pracovního prostoru Log Analytics, který jste vytvořili před začátkem tohoto kurzu.
    > [!div class="mx-imgBorder"]
    > ![ID pracovního prostoru Azure Log Analytics](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. V nabídce vlevo vyhledejte **Správa agenti** a vyberte ji. Tím se zobrazí klíče agenta, které byly vygenerovány.
    > [!div class="mx-imgBorder"]
    > ![Správa agentů Azure Log Analytics](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. Zkopírujte *ID pracovního prostoru*.
    > [!div class="mx-imgBorder"]
    > ![Kopírovat ID pracovního prostoru](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. Na jiné kartě prohlížeče nebo okně v části kolekce dat Azure Log Analytics vyberte **Odeslat data**, zadejte název připojení a pak vložte *ID pracovního prostoru* do pole **ID pracovního prostoru** .

1. Vraťte se na kartu nebo okno prohlížeče pracovního prostoru a zkopírujte *klíč pracovního prostoru*.
    > [!div class="mx-imgBorder"]
    > ![Primární klíč správy agentů](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. Na jiné kartě nebo v okně prohlížeče vložte *klíč pracovního prostoru* do pole **klíč pracovního prostoru** .

1. Vyberte **Vytvořit**. Nyní vytvoříte text požadavku JSON a název vlastního protokolu.

1. Vyberte pole **text požadavku JSON** .  Zobrazí se odkaz na **Přidání dynamického obsahu** .

1. Vyberte **Přidat dynamický obsah** a pak vyberte **téma**.

1. Stejnou pro **vlastní název protokolu**.
    > [!div class="mx-imgBorder"]
    > ![Vybrané téma](media/tutorial-events-log-analytics/topic-selected.png)

1. Vyberte **zobrazení kódu** aplikace logiky. Vyhledejte vstupy a Log-Type řádky.
    > [!div class="mx-imgBorder"]
    > ![Zobrazení kódu dvou řádků](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. Změňte `body` hodnotu z `"@triggerBody()?['topic']"` na `"@{triggerBody()}"` . Slouží k analýze celé zprávy na Log Analytics.

1. Změňte `Log-Type` z `"@triggerBody()?['topic']"` na `"@replace(triggerBody()?['eventType'],'.','')"` . (Nahradí ".", protože nejsou povolené v názvech protokolů Log Analytics.)
    > [!div class="mx-imgBorder"]
    > ![JSON aplikace logiky po změně](media/tutorial-events-log-analytics/changed-lines.png)

1. Vyberte **Uložit**.

1. Pokud chcete ověřit, vyberte **Návrhář aplikace logiky**.
    > [!div class="mx-imgBorder"]
    > ![Ověření textu a kroků funkce](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. Když prohlížíte všechny prostředky ve skupině prostředků, bude v seznamu uvedena aplikace logiky a dvě konektory rozhraní API Logic Apps, jednu pro události a jednu pro Log Analytics. Další informace o Event Grid systémových tématech najdete v článku [Event Grid systémová témata](../../event-grid/system-topics.md).
    > [!div class="mx-imgBorder"]
    > ![Zobrazit všechny nové prostředky ve skupině prostředků](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Test

Pokud chcete otestovat, jak to funguje, vytvořte v Azure Media Services živou událost. Vytvořte živou událost RTMP a pomocí FFmpeg nahrajte datový proud "Live" na základě ukázkového souboru. MP4. Po vytvoření události Získejte adresu URL ingestování RTMP.

1. Z účtu Media Services vyberte **živé streamování**.
    > [!div class="mx-imgBorder"]
    > ![Vytvořit Azure Media Services živou událost](media/tutorial-events-log-analytics/live-event.png)

1. Vyberte **Přidat živou událost**.

1. Zadejte název do pole **název živé události** . (Pole **Popis** je volitelné.)

1. Vyberte **standardní** kódování cloudu.

1. Pro přednastavení kódování vyberte **Výchozí hodnota 720p** .

1. Vyberte vstupní protokol **RTMP** .

1. Jako trvalá vstupní adresa URL vyberte **Ano** .

1. Vyberte **Ano** , pokud chcete událost při vytvoření události spustit.

    > [!WARNING]
    > Fakturace se spustí, pokud vyberete Ano.  Pokud chcete počkat na spuštění streamu *, dokud nezačnete* streamování s ffmpeg, vyberte **ne** a nezapomeňte zahájit živou událost a pak.

    > [!div class="mx-imgBorder"]
    > ![Nastavení živé události](media/tutorial-events-log-analytics/live-event-settings.png)

1. Vyberte možnost **Mám všechna práva k použití zaškrtávacího políčka Content/File...** .

1. Vyberte **Zkontrolovat a vytvořit**.

1. Zkontrolujte nastavení a pak vyberte **vytvořit**.  Zobrazí se seznam událostí Live a zobrazí se adresa URL pro příjem živé události.

1. Zkopírujte **adresu URL pro přijímání** do schránky.

1. Kliknutím na **živá událost** v seznamu zobrazíte zobrazení výrobce.

### <a name="stream-with-ffmpeg-cli"></a>Streamování pomocí rozhraní příkazového řádku FFmpeg

1. Použijte následující příkazový řádek.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. Přejděte `<ingestURL>` na adresu URL pro příjem dat, kterou jste zkopírovali do schránky.
1. Přejděte `<localpathtovideo>` na místní cestu k souboru, který chcete streamovat z ffmpeg.
1. Přidejte jedinečný název na konec, například `mystream` .
1. Upravte příkazový řádek tak, aby odrážel váš zdrojový soubor testu a všechny další systémové proměnné.
1. Příkaz spusťte. Po několika sekundách by měl přehrávač "zobrazení" sledovat, aby bylo spuštěno streamování. (Pokud se video nezobrazí automaticky, aktualizujte přehrávač.)

    > [!div class="mx-imgBorder"]
    > ![Ověření správného ingestování videa v přehrávači Preview](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>Ověřit události

V případě živého streamu Azure Media Services emituje různé události, které aktivují tok aplikace logiky. Pokud chcete ověřit, přejděte do aplikace logiky a zjistěte, jestli události vyvolané událostmi z Media Services neaktivují.

1. Přejděte na stránku Přehled aplikace logiky, měla by se zobrazit zpráva o tom, že úlohy jsou uvedené v části Historie spuštění, které byly úspěšně dokončeny.
    > [!div class="mx-imgBorder"]
    > ![Ověření úspěšného spuštění úlohy v aplikaci logiky](media/tutorial-events-log-analytics/run-history.png)

1. Vyberte úspěšnou úlohu. Zobrazí se podrobnosti o úloze během běhu.
1. Vyberte **Odeslat data** a rozbalte je. V tomto případě `MicrosoftMediaLiveEventEncoderConnected` událost ukazuje, že byla zachycena, a také analyzovaný text. To je to, co se doručí do pracovního prostoru Azure Log Analytics.
    > [!div class="mx-imgBorder"]
    > ![Viz odeslání dat](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>Ověření protokolů

1. Přejděte na Log Analytics pracovní prostor, který jste vytvořili dříve.

1. Vyberte **Protokoly**.
1. Zavřete okno příklady dotazů.
1. Bude existovat vlastní seznam protokolů. Vyberte šipku dolů a rozbalte ji. Zobrazí se název události `MicrosoftMediaLiveEventEncoderConnected` .
1. Vyberte název události a rozbalte ji.
1. Když vyberete ikonu "oči", zobrazí se náhled výsledku dotazu.
1. Vyberte možnost **Zobrazit v editoru dotazů** a potom vyberte položku v seznamu **TimeGenerated UTC** a rozbalte ji a zobrazte nezpracovaná data.

![Viz podrobný výstup události v Log Analytics](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Odstranění prostředků

Pokud nechcete dál používat prostředky, které jste vytvořili v tomto kurzu, odstraňte všechny prostředky ve skupině prostředků, jinak se vám bude účtovat i nadále.

## <a name="next-steps"></a>Další kroky

Můžete vytvářet různé dotazy a ukládat je. Ty je možné přidat na [řídicí panel Azure](../../azure-monitor/learn/tutorial-logs-dashboards.md).