---
title: Rychlý Start – prozkoumat vzorový scénář
titleSuffix: Azure Digital Twins
description: Rychlý Start – ukázka použití Průzkumníka digitálních vláken Azure k vizualizaci a prozkoumání předdefinovaného scénáře.
author: baanders
ms.author: baanders
ms.date: 9/24/2020
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: cb501dd8e548ea2f6ce8b63fbffb1c4b2403c9a6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035955"
---
# <a name="quickstart---explore-a-sample-azure-digital-twins-scenario-using-azure-digital-twins-explorer"></a>Rychlý Start – Prozkoumejte ukázkový scénář digitálních vláken Azure pomocí Průzkumníka digitálních vláken Azure

Pomocí digitálních vláken Azure můžete vytvářet a interagovat s živými modely vašich reálných prostředí. Nejprve modelujte jednotlivé prvky jako *digitální vlákna*. Pak je připojíte ke znalostnímu *diagramu* , který může reagovat na živé události a dotazovat se na informace.

V tomto rychlém startu prozkoumáte předem sestavený graf digitálních vláken Azure s použitím ukázkové aplikace s názvem [Průzkumník digitálních vláken Azure](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Použijete Průzkumníka digitálních vláken Azure k těmto akcím:

- Nahrajte digitální reprezentaci prostředí.
- Zobrazení vizuálních obrázků vláken a grafů, které jsou vytvořeny tak, aby představovaly prostředí v rámci digitálních vláken Azure.
- Pomocí vizuálního prostředí založeného na prohlížeči provádějte další aktivity správy.

Rychlý Start obsahuje následující hlavní kroky:

1. Nastavte instanci digitálních vláken Azure a Průzkumníka digitálních vláken Azure.
1. Nahrajte předem sestavené modely a data grafu a vytvořte vzorový scénář.
1. Prozkoumejte graf scénáře, který se vytvoří.
1. Proveďte změny v grafu.

Vzorový graf, se kterým budete pracovat, představuje sestavení se dvěma podlahami a dvěma místnosti. Graf bude vypadat jako tento obrázek:

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Zobrazení grafu tvořeného čtyřmi kruhovými uzly, které jsou připojeny šipkami. Kruh označený jako ' Floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '. Kruh označený jako ' Floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room0 '. ' Floor1 ' a ' Floor0 ' nejsou připojeny.":::

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu budete potřebovat předplatné Azure. Pokud ho ještě nemáte, vytvořte si [ho zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) hned teď.

Budete také potřebovat **Node.js** na svém počítači. Pokud chcete získat nejnovější verzi, přečtěte si téma [Node.js](https://nodejs.org/).

Nakonec také budete muset stáhnout ukázku, která se má použít při rychlém startu. Ukázková aplikace je **Průzkumník digitálních vláken Azure**. Tato ukázka obsahuje aplikaci, kterou používáte v rychlém startu k načtení a prozkoumání scénáře digitálních vláken Azure. Obsahuje také ukázkové soubory scénáře. Ukázku získáte tak, že přejdete na [Průzkumník digitálních vláken Azure](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Vyberte tlačítko **Stáhnout ZIP** a Stáhněte si soubor. zip tohoto ukázkového kódu do vašeho počítače. Rozbalte složku **Azure_Digital_Twins__ADT__explorer.zip** a extrahujte soubory.

## <a name="set-up-azure-digital-twins-and-azure-digital-twins-explorer"></a>Nastavení digitálních vláken Azure a Průzkumníka digitálních vláken Azure

Prvním krokem při práci se službou Azure Digital proworking je nastavení instance digitálních vláken Azure. Po vytvoření instance služby a nastavení přihlašovacích údajů pro ověřování pomocí Průzkumníka digitálních vláken Azure se můžete připojit k instanci v Průzkumníkovi digitálních vláken Azure a naplnit ji pomocí ukázkových dat později v rychlém startu.

Zbytek této části vás provede následujícími kroky.

### <a name="set-up-an-azure-digital-twins-instance"></a>Nastavení instance digitálních vláken Azure

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="set-up-local-azure-credentials"></a>Nastavit místní přihlašovací údaje Azure

Aplikace Průzkumník digitálních vláken Azure používá [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (součást `Azure.Identity` knihovny) k ověřování uživatelů s instancí digitálních vláken Azure, když ji spustíte na místním počítači. Další informace o různých způsobech, jak se klientská aplikace může ověřit pomocí digitálních vláken Azure, najdete v tématu [zápis ověřovacího kódu aplikace](how-to-authenticate-client.md).

V případě tohoto typu ověřování bude Průzkumník digitálních vláken Azure Hledat přihlašovací údaje v rámci místního prostředí, jako je například přihlášení Azure v místním rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) nebo v sadě Visual Studio nebo Visual Studio Code. Z tohoto důvodu byste se měli *k Azure přihlašovat místně* prostřednictvím jednoho z těchto mechanismů, abyste nastavili přihlašovací údaje pro aplikaci Průzkumník digitálních vláken Azure.

Pokud už jste se k Azure přihlásili jedním z těchto způsobů, můžete přejít k [Další části](#run-and-configure-azure-digital-twins-explorer).

V opačném případě můžete místní Azure CLI nainstalovat pomocí těchto kroků:

1. Pomocí [tohoto odkazu na instalaci](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) dokončete instalaci, která odpovídá vašemu operačnímu systému.
1. Otevřete na svém počítači okno konzoly.
1. Spusťte `az login` příkaz a podle pokynů k ověření se přihlaste ke svému účtu Azure.
1. Možný poslední krok: Pokud v rámci tohoto účtu použijete více předplatných Azure, nastavte kontext ověřování na předplatné Azure, které obsahuje instanci digitálního vlákna Azure, a to spuštěním `az account set --subscription "<your-subscription-name-or-ID>"` (hodnota název nebo ID předplatného bude fungovat).

Po přihlášení by měl Průzkumník digitálních vláken Azure automaticky vybírat přihlašovací údaje Azure při spuštění v další části.

V případě potřeby můžete okno konzoly ověřování zavřít. Nebo ho můžete nechat otevřený pro použití v dalším kroku.

### <a name="run-and-configure-azure-digital-twins-explorer"></a>Spuštění a konfigurace Průzkumníka digitálních vláken Azure

V dalším kroku spusťte aplikaci Průzkumník digitálních vláken Azure a nakonfigurujte ji pro instanci digitálních vláken Azure.

1. Přejít do složky Downloaded and unzip **Azure_Digital_Twins__ADT__explorer** .
Otevřete okno konzoly do umístění složky **Azure_Digital_Twins__ADT__explorer/Client/src**.

1. Spusťte `npm install` , aby se stáhly všechny požadované závislosti.

1. Spusťte aplikaci spuštěním `npm run start` .

   Po několika sekundách se otevře okno prohlížeče a aplikace se zobrazí v prohlížeči.

   :::image type="content" source="media/quickstart-adt-explorer/explorer-blank.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá Průzkumník digitálních vláken Azure a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/quickstart-adt-explorer/explorer-blank.png":::

1. V pravém horním rohu okna vyberte tlačítko **Přihlásit** , jak je znázorněno na následujícím obrázku, abyste mohli nakonfigurovat Průzkumníka digitálních vláken Azure pro práci s instancí, kterou jste nastavili.

   :::image type="content" source="media/quickstart-adt-explorer/sign-in.png" alt-text="Průzkumník digitálních vláken Azure zvýrazňuje ikonu přihlášení v horní části okna. Ikona zobrazuje jednoduché Silhouette osoby překrývající Silhouette klíče." lightbox="media/quickstart-adt-explorer/sign-in.png":::

1. Zadejte adresu URL instance digitálních vláken Azure, kterou jste shromáždili dříve v části [Nastavení instance digitálních vláken Azure](#set-up-an-azure-digital-twins-instance) , ve formátu *https://{instance hosta}*.

>[!NOTE]
> Tyto informace můžete kdykoli znovu navštívit nebo upravit tak, že vyberete stejnou ikonu pro opětovné otevření **přihlašovacího** pole. Zachová se hodnoty, které jste předali.

> [!TIP]
> Pokud `SignalRService.subscribe` se při připojení zobrazí chybová zpráva, ujistěte se, že adresa URL digitálních vláken Azure začíná na *https://*.

Pokud se zobrazí **oprávnění požadované** automaticky otevírané okno od společnosti Microsoft, poskytněte pro tuto aplikaci souhlas a potvrďte pokračování.

## <a name="add-the-sample-data"></a>Přidat ukázková data

V dalším kroku naimportujete vzorový scénář a graf do Průzkumníka digitálních vláken Azure. Ukázkový scénář je také umístěn ve složce **Azure_Digital_Twins__ADT__explorer** , kterou jste stáhli dříve.

### <a name="models"></a>Modely

Prvním krokem v řešení digitálních vláken Azure je definování slovníku pro vaše prostředí. Vytvoříte vlastní [modely](concepts-models.md) , které popisují typy entit, které ve vašem prostředí existují.

Každý model je napsán v jazyce, jako je JSON-LD, označovaný jako Digital DTDL Definition Language (). Každý model popisuje jeden typ entity z pohledu jeho *vlastností*, *telemetrie*, *vztahů* a *komponent*. Později tyto modely použijete jako základ pro digitální vlákna, které představuje konkrétní instance těchto typů.

Obvykle při vytváření modelu dokončíte tři kroky:

1. Napište definici modelu. V rychlém startu je tento krok již proveden jako součást ukázkového řešení.
1. Ověřte ho a ujistěte se, že je syntaxe přesná. V rychlém startu je tento krok již proveden jako součást ukázkového řešení.
1. Nahrajte ho do instance digitálního vlákna Azure.
 
Pro tento rychlý Start jsou soubory modelů již zapsány a ověřovány. Ty jsou součástí řešení, které jste stáhli. V této části nahrajete do své instance dva předpsané modely, abyste definovali tyto komponenty stavebního prostředí:

* Floor
* Prostor

#### <a name="upload-models"></a>Nahrávání modelů

Pomocí těchto kroků nahrajte modely.

1. V poli **zobrazení modelu** vyberte ikonu **nahrát model** .

   :::image type="content" source="media/quickstart-adt-explorer/upload-model.png" alt-text="V poli zobrazení modelu je zvýrazněna střední ikona. Zobrazuje šipku ukazující na Cloud." lightbox="media/quickstart-adt-explorer/upload-model.png":::
 
1. V zobrazeném poli pro výběr souboru přejdete do složky **Azure_Digital_Twins__ADT__explorer/Client/Examples** ve staženém úložišti.
1. Vyberte **Room.jszapnuto** a **Floor.jszapnuto** a vyberte **OK**. Pokud chcete, můžete nahrávat další modely, ale nepoužijí se v tomto rychlém startu.
1. Použijte místní dialogové okno s výzvou, abyste se přihlásili ke svému účtu Azure.

>[!NOTE]
>Pokud se zobrazí následující chybová zpráva: :::image type="content" source="media/quickstart-adt-explorer/error-models-popup.png" alt-text="automaticky otevírané okno čte chybu: Chyba při načítání modelů: ClientAuthError: Chyba při otevírání překryvného okna. K tomu může dojít, pokud používáte Internet Explorer nebo pokud jsou automaticky otevíraná okna blokována v prohlížeči. tlačítko s tlačítkem Zavřít v dolní části." border="false"::: 
> Zkuste zakázat blokování automaticky otevíraných oken nebo použít jiný prohlížeč.

Průzkumník digitálních vláken Azure teď nahraje tyto soubory modelu do instance digitálního vlákna Azure. Měly by se zobrazit v poli **zobrazení modelu** a zobrazit jejich popisné názvy a úplná ID modelu. Můžete vybrat ikony Zobrazit informace o **modelu** , abyste si zobrazili kód DTDL za nimi.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/model-info.png" alt-text="Zobrazení pole zobrazení modelu se dvěma definicemi modelů, které jsou uvedeny uvnitř, Floor (dtmi: priklad: Floor; 1) a místnost (dtmi: Příklad: místnost; 1). Pro každý model se zvýrazní ikona zobrazení informací o modelu ukazující písmeno &quot;i&quot; v kruhu." lightbox="media/quickstart-adt-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Vlákna a Dvojitá graf

Teď, když se některé modely nahrály do instance digitálního vlákna Azure, můžete přidat [digitální vlákna](concepts-twins-graph.md) , která následují za definicemi modelu.

Digitální vlákna reprezentují skutečné entity v rámci vašeho podnikového prostředí. Můžou to být věci, jako jsou senzory ve farmě, světla v kleci nebo – v tomto rychlém startu – místnosti v rámci stavebních prostorů. Můžete vytvořit mnoho vláken libovolného typu daného modelu, například více místností, které používají model *Room* . Připojíte je s relacemi do *vlákna* , které představuje celé prostředí.

V této části nahrajete předvytvořená vlákna, která jsou připojená do předvytvořeného grafu. Graf obsahuje dvě podlahová a dvě místnosti připojená v následujícím rozložení:

* Floor0
    - Obsahuje Room0
* Floor1
    - Obsahuje Room1

#### <a name="import-the-graph"></a>Import grafu

Pomocí následujícího postupu naimportujte graf.

1. V poli **zobrazení grafu** vyberte ikonu **Import grafu** .

   :::image type="content" source="media/quickstart-adt-explorer/import-graph.png" alt-text="V poli zobrazení grafu je zvýrazněna ikona. Zobrazuje šipku ukazující na Cloud." lightbox="media/quickstart-adt-explorer/import-graph.png":::

2. V poli pro výběr souboru přejdete do složky **Azure_Digital_Twins__ADT__explorer/Client/Examples** a vyberete **buildingScenario.xlsx** soubor tabulky. Tento soubor obsahuje popis ukázkového grafu. Vyberte **OK**.

   Po několika sekundách Průzkumník digitálních vláken Azure otevře zobrazení **importu** , ve kterém se zobrazí náhled grafu, který se má načíst.

3. Pokud chcete potvrdit nahrávání grafu, vyberte ikonu **Uložit** v pravém horním rohu pole **zobrazení grafu** .

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/graph-preview-save.png" alt-text="Zvýraznění ikony uložit v podokně Náhled grafu" lightbox="media/quickstart-adt-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Průzkumník digitálních vláken Azure teď pomocí nahraného souboru vytvoří požadované zdvojení a vztahy mezi nimi. Po dokončení se zobrazí dialogové okno. Vyberte **Zavřít**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/import-success.png" alt-text="Dialogové okno s oznámením o úspěchu importu grafu Čte import úspěšně. 4 naimportovány zdvojené. 2 importované relace. '" lightbox="media/quickstart-adt-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Graf se teď nahrál do Průzkumníka digitálních vláken Azure. Chcete-li zobrazit graf, vyberte tlačítko **Spustit dotaz** v poli **Průzkumník grafu** v horní části okna Průzkumníka digitálních vláken Azure.

   :::image type="content" source="media/quickstart-adt-explorer/run-query.png" alt-text="Zvýrazní se tlačítko spustit dotaz v pravém horním rohu okna." lightbox="media/quickstart-adt-explorer/run-query.png":::

Tato akce spustí výchozí dotaz pro výběr a zobrazení všech digitálních vláken. Průzkumník digitálních vláken Azure načítá všechny vlákna a vztahy ze služby. Kreslí graf definovaný v poli **zobrazení grafu** .

## <a name="explore-the-graph"></a>Prozkoumat graf

Teď si můžete prohlédnout nahraný graf ukázkového scénáře.

:::image type="content" source="media/quickstart-adt-explorer/graph-view-full.png" alt-text="Zobrazení pole zobrazení grafu s dvojitým grafem v. Kruh označený jako ' floor1 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' Room1 '. Kruh označený jako ' floor0 ' je připojen šipkou, která je označena ' Contains ' na kroužek označený ' room0 '.":::

Kruhy (grafy "uzly") reprezentují digitální vlákna. Řádky prezentují relace. **Floor0** se nevlákenně obsahuje **Room0** a vlákna **Floor1** obsahuje **Room1**.

Pokud používáte myš, můžete přetáhnout části grafu, abyste je mohli přesunout.

### <a name="view-twin-properties"></a>Zobrazit zdvojené vlastnosti

Můžete vybrat dvojitou hodnotu pro zobrazení seznamu vlastností a jejich hodnot v poli **Průzkumník vlastností** .

Tady jsou vlastnosti Room0:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room0.png" alt-text="Zvýrazněte kolem okna Průzkumník vlastností, které zobrazuje vlastnosti pro Room0, které zahrnují (mimo jiné) $dtId pole Room0, pole teploty 70 a pole vlhkosti 30." lightbox="media/quickstart-adt-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room0 má teplotu 70.

Tady jsou vlastnosti Room1:

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/properties-room1.png" alt-text="Zvýrazněte kolem okna Průzkumník vlastností, které zobrazuje vlastnosti pro Room1, které zahrnují (mimo jiné) $dtId pole Room1, pole teploty 80 a pole vlhkosti 60." lightbox="media/quickstart-adt-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Room1 má teplotu 80.

### <a name="query-the-graph"></a>Dotazování grafu

Hlavní funkcí digitálních vláken Azure je schopnost snadno a efektivně [dotazovat](concepts-query-language.md) se na váš graf, aby odpovídal na dotazy týkající se vašeho prostředí.

Jedním ze způsobů, jak zadat dotaz na vlákna v grafu, je podle jejich *vlastností*. Dotazování na základě vlastností může přispět k zodpovězení nejrůznějších otázek. Můžete například najít mimo jiné vaše prostředí, které může vyžadovat pozornost.

V této části spustíte dotaz, který odpoví na otázku, kolik vláken ve vašem prostředí má teplotu vyšší než 75.

Chcete-li zobrazit odpověď, spusťte následující dotaz v poli **Průzkumník dotazů** .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Odvolání ze zobrazení vlastností s dvojitou platností dříve, než Room0 má teplotu 70 a Room1 má teplotu 80. Z tohoto důvodu se tady zobrazí jenom Room1.
    
:::image type="content" source="media/quickstart-adt-explorer/result-query-property-before.png" alt-text="Výsledky dotazu vlastnosti zobrazující pouze Room1." lightbox="media/quickstart-adt-explorer/result-query-property-before.png":::

>[!TIP]
> Jiné operátory porovnání (<, >, = nebo! =) jsou také podporovány v rámci předchozího dotazu. Můžete zkusit zapojit tyto operátory, jiné hodnoty nebo jiné vlastnosti do dotazu, abyste si vyzkoušeli odpovědi na vlastní otázky.

## <a name="edit-data-in-the-graph"></a>Úprava dat v grafu

Pomocí Průzkumníka digitálních vláken Azure můžete upravit vlastnosti vláken reprezentovaných v grafu. V této části zvýšíme teplotu Room0 až 76.

Začněte tak, že vyberete **Room0** a zobrazíte jeho seznam vlastností v poli **Průzkumník vlastností** .

Vlastnosti v tomto seznamu lze upravovat. Vyberte hodnotu teploty **70** a povolte tak zadání nové hodnoty. Zadejte **76** a výběrem ikony **Uložit** aktualizujte teplotu na **76**.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-adt-explorer/new-properties-room0.png" alt-text="Okno Průzkumník vlastností zobrazující vlastnosti pro Room0. Hodnota teploty je upravitelné pole zobrazující 76 a zvýrazní se ikona Uložit." lightbox="media/quickstart-adt-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Teď se zobrazí okno s **informacemi o opravě** , kde se zobrazí kód opravy, který se použil na pozadí s [rozhraními API](how-to-use-apis-sdks.md) pro digitální vlákna Azure, aby se aktualizace provedla. Vyberte **Zavřít**.

### <a name="query-to-see-the-result"></a>Dotaz pro zobrazení výsledku

Chcete-li ověřit, že se v grafu úspěšně zaregistrovala vaše aktualizace na teplotu pro Room0, znovu spusťte dotaz ze starší verze, abyste získali všechny vlákna v prostředí s teplotou vyšší než 75.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="TemperatureQuery":::

Teď, když se teplota Room0 změnila z 70 na 76, ve výsledku by se měla zobrazit obě vlákna.

:::image type="content" source="media/quickstart-adt-explorer/result-query-property-after.png" alt-text="Výsledky dotazu vlastnosti zobrazující Room0 i Room1." lightbox="media/quickstart-adt-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Recenze a dát učení

V tomto rychlém startu jste vytvořili instanci digitálních vláken Azure, připojili ji k Průzkumníku digitálních vláken Azure a naplnili ji ukázkovým scénářem.

Pak jste prozkoumali graf podle:

* Použití dotazu k zodpovězení otázky týkající se scénáře.
* Úprava vlastnosti u digitálního vlákna.
* Spusťte dotaz znovu, abyste viděli, jak se odpověď změnila v důsledku aktualizace.

Účelem tohoto cvičení je Ukázat, jak můžete pomocí grafu digitálních vláken Azure zodpovědět otázky týkající se vašeho prostředí, i když se prostředí stále mění.

V tomto rychlém startu jste provedli aktualizaci teploty ručně. Je běžné, že v digitálních prostředcích Azure propojí digitální vlákna se skutečnými zařízeními IoT, aby se aktualizace automaticky přijímaly na základě dat telemetrie. Tímto způsobem můžete vytvořit živý graf, který vždy odráží skutečný stav vašeho prostředí. Dotazy můžete použít k získání informací o tom, co se ve vašem prostředí děje v reálném čase.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zabalit práci pro tento rychlý Start, nejprve ukončete spuštěnou konzolovou aplikaci. Tato akce vypne připojení k aplikaci Průzkumník digitálních vláken Azure v prohlížeči. V prohlížeči už nebudete moct zobrazovat živá data. Kartu prohlížeče můžete zavřít.

Pak můžete zvolit prostředky, které chcete odebrat, podle toho, co byste chtěli udělat dál.

* **Pokud máte v úmyslu pokračovat na kurzy digitálních vláken Azure**, můžete tuto instanci v tomto rychlém startu znovu použít pro tyto články a nemusíte ji odebrat.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Je také možné, že budete chtít odstranit složku projektu z místního počítače.

## <a name="next-steps"></a>Další kroky

Dál se můžete podívat na kurzy digitálních vláken Azure a vytvořit vlastní scénář a nástroje pro interakci s digitálními scénáři Azure.

> [!div class="nextstepaction"]
> [Kurz: vytvoření kódu klientské aplikace](tutorial-code.md)