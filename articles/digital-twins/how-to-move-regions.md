---
title: Přesunutí instance do jiné oblasti Azure
titleSuffix: Azure Digital Twins
description: Podívejte se, jak přesunout instanci digitálních vláken Azure z jedné oblasti Azure do jiné.
author: baanders
ms.author: baanders
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
ms.openlocfilehash: 6393b0b8d794345fded95718a2581ae9b929ad49
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94381146"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Přesunutí instance digitálních vláken Azure do jiné oblasti Azure

Pokud potřebujete přesunout instanci digitálních vláken Azure z jedné oblasti do druhé, aktuální proces je znovu vytvořit prostředky v nové oblasti a pak odstranit původní prostředky. Na konci tohoto procesu budete pracovat s novou instancí digitálních vláken Azure, která je stejná jako první, s výjimkou aktualizovaného umístění.

Tento článek obsahuje informace o tom, jak provést kompletní přesun a kopírování přes všechno, co budete potřebovat k tomu, aby se nová instance shodovala s původní.

Tento proces zahrnuje následující kroky:

1. Příprava: Stáhněte si původní modely, vlákna a graf.
1. Move: Vytvořte novou instanci digitálních vláken Azure v nové oblasti.
1. Move: znovu naplňte novou instanci digitálních vláken Azure.
    - Nahrajte původní modely, vlákna a graf.
    - Opětovné vytvoření koncových bodů a tras
    - Znovu propojte připojené prostředky.
1. Vyčistit zdrojové prostředky: odstraňte původní instanci.

## <a name="prerequisites"></a>Požadavky

Než se pokusíte znovu vytvořit instanci digitálního vlákna v Azure, přečtěte si komponenty původní instance, abyste měli jasný nápad na všechny části, které je potřeba znovu vytvořit.

Je potřeba zodpovědět některé otázky:

* Jaké jsou *modely* nahrané do mé instance? Kolik?
* Jaké jsou *vlákna* v mé instanci? Kolik?
* Jaký je obecný tvar *grafu* v mé instanci? Kolik relací je?
* Jaké *koncové body* mám v mé instanci?
* Jaké *trasy* mám v mé instanci? Mají filtry?
* Kde se moje instance *připojuje k jiným službám Azure* ? Mezi běžné integrační body patří:

    - Azure Event Grid, Azure Event Hubs nebo Azure Service Bus
    - Azure Functions
    - Azure Logic Apps
    - Azure Time Series Insights
    - Azure Maps
    - Služba Azure IoT Hub Device Provisioning
* Jaké jiné *osobní nebo firemní aplikace* mám připojit k mé instanci?

Tyto informace můžete shromáždit pomocí [Azure Portal](https://portal.azure.com), [Azure Digital revlákens API a SDK](how-to-use-apis-sdks.md), [příkazů Azure Digital](how-to-use-cli.md)CLI nebo ukázky v Průzkumníkovi služby [Azure Digital (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

## <a name="prepare"></a>Příprava

V této části budete připravovat, abyste instanci znovu vytvořili stažením původních modelů, vláken a grafů z původní instance. Tento článek používá ukázku [Průzkumníka ADT](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) pro tento úkol.

>[!NOTE]
>Je možné, že již máte soubory, které obsahují modely nebo graf ve vaší instanci. Pokud ano, nemusíte si znovu stahovat všechno, ale jenom ty, které jste nezadali, nebo něco, co se mohlo od původně nahraných těchto souborů změnit. Můžete mít například vlákna, která byla aktualizována novými daty.

### <a name="limitations-of-adt-explorer"></a>Omezení Průzkumníka aplikace ADT

[Ukázka Průzkumníka ADT](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) je ukázka klientské aplikace, která podporuje vizuální znázornění grafu a poskytuje vizuální interakci s vaší instancí. Tento článek ukazuje, jak ho používat ke stažení a pozdějšímu nahrávání, vašich modelů, vláken a grafů.

Tato ukázka není úplným nástrojem. Nebyla testována zátěžová a nebyla vytvořena pro zpracování grafů o velké velikosti. V důsledku toho mějte na paměti následující nepřipravená omezení vzorkování:

* Vzorek je aktuálně testován pouze v grafu velikosti až 1 000 uzlů a 2 000 vztahů.
* Ukázka nepodporuje opakování v případě jakýchkoli přerušovaných selhání.
* Ukázka nebude nutně informovat uživatele, pokud data nahraná nejsou úplná.
* Ukázka nezpracovává chyby vyplývající z velmi rozsáhlých grafů, které překračují dostupné prostředky, jako je paměť.

Pokud ukázka nedokáže zpracovat velikost grafu, můžete ho exportovat a importovat pomocí dalších nástrojů pro vývojáře digitálních vláken Azure:

* [Příkazy rozhraní příkazového řádku Azure pro digitální vlákna](how-to-use-cli.md)
* [Rozhraní API a sady SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md)

### <a name="set-up-the-adt-explorer-application"></a>Nastavení aplikace pro Průzkumníka ADT

Pokud chcete pokračovat v Průzkumníkovi aplikace ADT, nejdřív si stáhněte ukázkový kód aplikace a nastavte ho tak, aby se spouštěl na vašem počítači.

Ukázku získáte v tématu [Průzkumník aplikace ADT](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Vyberte tlačítko **Stáhnout ZIP** a Stáhněte si soubor. zip tohoto ukázkového kódu do počítače jako **Azure_Digital_Twins__ADT__explorer.zip**. Rozbalte soubor.

Dále nastavte a nakonfigurujte oprávnění pro Průzkumníka aplikace ADT. Postupujte podle pokynů v části [Nastavení digitálních vláken Azure a Průzkumníka ADT](quickstart-adt-explorer.md#set-up-azure-digital-twins-and-adt-explorer) v rychlém startu digitálního vlákna Azure. Tato část vás provede následujícími kroky:

1. Nastavte instanci digitálních vláken Azure. Tuto část můžete přeskočit, protože již máte instanci.
1. Nastavte místní přihlašovací údaje Azure pro poskytnutí přístupu k vaší instanci.
1. Spusťte Průzkumníka aplikace ADT a nakonfigurujte ho tak, aby se připojil k vaší instanci. Použijete *název hostitele* původní instance digitálních vláken Azure, kterou přesouváte.

Nyní byste měli mít ukázkovou aplikaci Průzkumníka ADT spuštěnou v prohlížeči na vašem počítači. Vzorek by měl být připojený k původní instanci digitálních vláken Azure.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/explorer-blank.png":::

Chcete-li ověřit připojení, vyberte tlačítko **Spustit dotaz** a spusťte výchozí dotaz, který zobrazí všechny vlákna a relace v grafu v poli **Průzkumník grafů** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Je zvýrazněno tlačítko spustit dotaz pro čtení v pravém horním rohu okna." lightbox="media/how-to-move-regions/run-query.png":::

Průzkumník aplikace ADT můžete nechat spuštěný, protože ho později v tomto článku budete moct znovu nahrát do vaší nové instance v cílové oblasti.

### <a name="download-models-twins-and-graph"></a>Stáhnout modely, vlákna a graf

V dalším kroku Stáhněte do svého řešení modely, vlákna a graf.

Chcete-li stáhnout všechny tyto položky najednou, zajistěte, aby se v poli **zobrazení grafu** zobrazil celý graf. Pokud se celý graf už nezobrazuje, znovu spusťte výchozí dotaz `SELECT * FROM digitaltwins` v poli **Průzkumník dotazů** .
 
Pak vyberte ikonu **grafu exportu** v poli **zobrazení grafu** .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="V poli zobrazení grafu je zvýrazněna ikona. Zobrazuje šipku ukazující mimo Cloud." lightbox="media/how-to-move-regions/export-graph.png":::

Tato akce povolí odkaz **ke stažení** v poli **zobrazení grafu** . Vyberte ji pro stažení reprezentace výsledku dotazu založeného na formátu JSON, která zahrnuje vaše modely, vlákna a vztahy. Tato akce by měla na váš počítač stáhnout soubor. JSON.

>[!NOTE]
>Pokud se zdá, že stažený soubor má jinou příponu, zkuste rozšíření upravit přímo a změnit na. JSON.

## <a name="move"></a>Přesunout

Dále dokončíte přesunutí instance vytvořením nové instance v cílové oblasti. Pak je naplníte daty a komponentami z původní instance.

### <a name="create-a-new-instance"></a>Vytvořit novou instanci

Nejdřív vytvořte novou instanci digitálních vláken Azure v cílové oblasti. Postupujte podle kroků v tématu [Postupy: nastavení instance a ověřování](how-to-set-up-instance-portal.md). Pamatujte na tyto ukazatele:

* Stejný název můžete zachovat pro novou instanci, *Pokud* se nachází v jiné skupině prostředků. Pokud potřebujete použít stejnou skupinu prostředků, která obsahuje původní instanci, bude vaše nová instance potřebovat vlastní jedinečný název.
* Až se zobrazí výzva k zadání umístění, zadejte novou cílovou oblast.

Po dokončení tohoto kroku budete potřebovat název hostitele nové instance, abyste mohli pokračovat v nastavování s Vašimi daty. Pokud jste si nechtěli poznamenat název hostitele během instalace, postupujte podle [těchto pokynů](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) a získejte ho hned z Azure Portal.

### <a name="repopulate-the-old-instance"></a>Znovu naplnit starou instanci

V dalším kroku nastavíte novou instanci tak, aby byla kopií originálu.

#### <a name="upload-the-original-models-twins-and-graph-by-using-adt-explorer"></a>Nahrání původních modelů, vláken a grafu pomocí nástroje ADT Explorer

V této části můžete znovu nahrát vaše modely, vlákna a graf do nové instance. Pokud nemáte žádné modely, vlákna nebo graf v původní instanci nebo je nechcete přesunout do nové instance, můžete přejít k [Další části](#re-create-endpoints-and-routes).

V opačném případě se vraťte do okna prohlížeče se spuštěným průzkumníkem aplikace ADT a postupujte podle těchto kroků.

##### <a name="connect-to-the-new-instance"></a>Připojit k nové instanci

V současné době je Průzkumník aplikace ADT připojený k původní instanci digitálních vláken Azure. Výběrem tlačítka **Přihlásit** v pravém horním rohu okna přepněte připojení tak, aby odkazovalo na novou instanci.

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Průzkumník ADT – zvýrazní se ikona přihlášení v pravém horním rohu okna. Ikona zobrazuje jednoduché Silhouette osoby překrývající Silhouette klíče." lightbox="media/how-to-move-regions/sign-in.png":::

Nahraďte **adresu URL aplikace ADT** tak, aby odrážela vaši novou instanci. Změňte tuto hodnotu tak, aby četla *protokol https://{New instance Host}*.

Vyberte **Connect** (Připojit). Může se zobrazit výzva k opětovnému přihlášení s přihlašovacími údaji Azure nebo udělení souhlasu této aplikace pro vaši instanci.

##### <a name="upload-models-twins-and-graph"></a>Nahrávání modelů, vláken a grafů

Dále nahrajte součásti řešení, které jste stáhli dříve, do nové instance.

Chcete-li nahrát vaše modely, vlákna a graf, vyberte ikonu **importovat graf** v poli **zobrazení grafu** . Tato možnost nahraje všechny tři tyto součásti najednou. Dokonce nahrává modely, které se v grafu aktuálně nepoužívají.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="V poli zobrazení grafu je zvýrazněna ikona. Zobrazuje šipku ukazující na Cloud." lightbox="media/how-to-move-regions/import-graph.png":::

V poli pro výběr souboru přejdete do staženého grafu. Vyberte soubor Graph **. JSON** a vyberte **otevřít**.

Po několika sekundách otevře Průzkumník služby ADT zobrazení **importu** , ve kterém se zobrazí náhled grafu, který se má načíst.

Pokud chcete potvrdit nahrávání grafu, vyberte ikonu **Uložit** v pravém horním rohu pole **zobrazení grafu** .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Zvýraznění ikony uložit v podokně Náhled grafu" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Průzkumník ADT teď nahrává vaše modely a grafy (včetně vláken a relací) do nové instance digitálních vláken Azure. Měla by se zobrazit zpráva o úspěchu, která označuje, kolik modelů, vláken a relací bylo odesláno.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Dialogové okno s oznámením o úspěchu importu grafu Čte import úspěšně. 2 importované modely. 4 naimportovány zdvojené. 2 importované relace. '" lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Pokud chcete ověřit, jestli se všechno úspěšně nahrálo, vyberte v poli **Průzkumník graphu** tlačítko **Spustit dotaz** a spusťte výchozí dotaz, který zobrazí všechny vlákna a relace v grafu. Tato akce také aktualizuje seznam modelů v poli **zobrazení modelu** .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Zvýrazněte tlačítko spustit dotaz v pravém horním rohu okna." lightbox="media/how-to-move-regions/run-query.png":::

Měl by se zobrazit graf se všemi jeho dvojitými vazbymi a relacemi zobrazenými v poli **Průzkumník grafů** . Měly by se také zobrazit vaše modely uvedené v poli **zobrazení modelu** .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Zobrazení Průzkumníka aplikace ADT zobrazující dva modely zvýrazněné v poli zobrazení modelu a graf zvýrazněný v poli Průzkumník grafů." lightbox="media/how-to-move-regions/post-upload.png":::

Tato zobrazení potvrzují, že vaše modely, vlákna a grafy byly znovu nahrány do nové instance v cílové oblasti.

#### <a name="re-create-endpoints-and-routes"></a>Opětovné vytvoření koncových bodů a tras

Pokud máte v původní instanci koncové body nebo trasy, budete je muset v nové instanci znovu vytvořit. Pokud v původní instanci nemáte žádné koncové body nebo trasy nebo je nechcete přesunout do nové instance, můžete přejít k [Další části](#relink-connected-resources).

Jinak postupujte podle kroků v tématu [Postupy: Správa koncových bodů a tras](how-to-manage-routes-portal.md) pomocí nové instance. Pamatujte na tyto ukazatele:

* *Nemusíte* znovu vytvářet Event Grid, Event Hubs nebo Service Bus prostředek, který používáte pro koncový bod. Další informace najdete v části požadavky v pokynech ke koncovému bodu. Stačí znovu vytvořit koncový bod v instanci digitálních vláken Azure.
* Názvy koncových bodů a směrování můžete opakovaně používat, protože jsou vymezeny na jinou instanci.
* Nezapomeňte přidat všechny požadované filtry na trasy, které vytvoříte.

#### <a name="relink-connected-resources"></a>Znovu propojit připojené prostředky

Pokud máte jiné aplikace nebo prostředky Azure, které jsou připojené k původní instanci digitálních vláken Azure, budete muset připojení upravit tak, aby místo toho dosáhlo nové instance. Tyto prostředky můžou zahrnovat další služby Azure nebo osobní nebo firemní aplikace, které jste nastavili pro práci s digitálními úkoly Azure.

Pokud nemáte žádné další prostředky připojené k původní instanci nebo je nechcete přesunout do nové instance, můžete přejít k [Další části](#verify).

V opačném případě zvažte připojené prostředky ve vašem scénáři. Nemusíte odstraňovat a znovu vytvářet žádné připojené prostředky. Místo toho je třeba upravit body, které se připojují k instanci digitálních vláken Azure pomocí jejího názvu hostitele. Pak tyto body aktualizujte tak, aby místo originálu používaly název hostitele nové instance.

Přesné prostředky, které je třeba upravit, závisí na vašem scénáři, ale zde jsou některé běžné integrační body:

* Azure Functions. Pokud máte funkci Azure, jejíž kód obsahuje název hostitele původní instance, měli byste tuto hodnotu aktualizovat na název hostitele nové instance a znovu publikovat funkci.
* Event Grid, Event Hubs nebo Service Bus.
* Logic Apps.
* Time Series Insights.
* Azure Maps.
* IoT Hub Device Provisioning Service.
* Osobní nebo firemní aplikace mimo Azure, jako je například klientská aplikace vytvořená v [kurzu: Code a klientská aplikace](tutorial-code.md), která se připojí k instanci a volá rozhraní API digitálních vláken Azure.
* Registrace aplikací Azure AD *není* nutné znovu vytvářet. Pokud k připojení k rozhraním API digitálních vláken Azure používáte [registraci aplikace](how-to-create-app-registration.md) , můžete stejnou registraci aplikace znovu použít s novou instancí.

Po dokončení tohoto kroku by nová instance v cílové oblasti měla být kopií původní instance.

## <a name="verify"></a>Ověření

Chcete-li ověřit, zda byla nová instance nastavena správně, použijte následující nástroje:

* [Azure Portal.](https://portal.azure.com) Portál je dobrý pro ověření, že vaše nová instance existuje a je ve správné cílové oblasti. Je to také dobré pro ověření koncových bodů a tras a připojení k ostatním službám Azure.
* [Příkazy rozhraní příkazového řádku Azure digitální vlákna](how-to-use-cli.md). Tyto příkazy jsou vhodné pro ověření, že vaše nová instance existuje a je ve správné cílové oblasti. Lze je také použít k ověření dat instance.
* [Průzkumník aplikace ADT](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) Průzkumník aplikace ADT je vhodný pro ověřování dat instancí, jako jsou modely, vlákna a grafy.
* [Rozhraní API a sady SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md). Tyto prostředky jsou vhodné pro ověřování dat instancí, jako jsou modely, vlákna a grafy. Jsou také vhodné pro ověření koncových bodů a tras.

Můžete také zkusit spustit libovolné vlastní aplikace nebo komplexní toky, které jste spustili s původní instancí, abyste ověřili, že fungují správně s novou instancí.

## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků

Teď, když je vaše nová instance nastavená v cílové oblasti s kopií dat a připojení původní instance, můžete původní instanci odstranit.

Můžete použít [Azure Portal](https://portal.azure.com), rozhraní příkazového [řádku Azure](how-to-use-cli.md)nebo [rozhraní API plochy ovládacího prvku](how-to-use-apis-sdks.md#overview-control-plane-apis).

Pokud chcete instanci odstranit pomocí Azure Portal, [Otevřete portál](https://portal.azure.com) v okně prohlížeče a do původní instance digitálního vlákna Azure se dostanete tak, že vyhledáte název na panelu hledání na portálu.

Vyberte tlačítko **Odstranit** a dokončete odstranění podle pokynů.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Zobrazení podrobností instance digitálních vláken Azure v Azure Portal na kartě Přehled. Tlačítko Odstranit je zvýrazněno.":::