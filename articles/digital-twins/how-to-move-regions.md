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
ms.openlocfilehash: 3c7f9ed9558adc9d129d1df767a05aff1fa4c66c
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92047382"
---
# <a name="move-an-azure-digital-twins-instance-to-a-different-azure-region"></a>Přesunutí instance digitálních vláken Azure do jiné oblasti Azure

Pokud potřebujete přesunout instanci digitálních vláken Azure z jedné oblasti do druhé, aktuální proces je **znovu vytvořit prostředky v nové oblasti**a pak odstranit původní prostředky. Na konci tohoto procesu budete pracovat s novou instancí digitálních vláken Azure, která je stejná jako první, s výjimkou aktualizovaného umístění.

Tento článek obsahuje informace o tom, jak provést úplné přesunutí, kopírování přes všechno, co budete potřebovat k tomu, aby se nová instance shodovala s původní.

Tento proces zahrnuje následující kroky:
1. Příprava: Stáhněte si původní modely, vlákna a graf.
2. Move: Vytvořte novou instanci digitálních vláken Azure v nové oblasti.
3. Move: znovu naplňte novou instanci digitálních vláken Azure.
    - Nahrajte původní modely, vlákna a graf.
    - Opětovné vytvoření koncových bodů a tras
    - Znovu propojte připojené prostředky.
4. Vyčistit zdrojové prostředky: odstranit původní instanci.

## <a name="prerequisites"></a>Požadované součásti

Předtím, než se pokusíte znovu vytvořit instanci digitálních vláken Azure, je vhodné přejít na součásti původní instance a získat jasný nápad na všechny části, které bude nutné znovu vytvořit.

Tady jsou některé otázky, které byste mohli zvážit:
* Jaké jsou **modely** nahrané do mé instance? Kolik?
* Jaké jsou **vlákna** v mé instanci? Kolik?
* Jaký je obecný tvar **grafu** v mé instanci? Kolik relací je?
* Jaké **koncové body** mám v mé instanci?
* Jaké **trasy** mám v mé instanci? Mají filtry?
* Kde se moje instance **připojuje k jiným službám Azure**? Mezi běžné integrační body patří...
    - Event Grid, centrum událostí nebo Service Bus
    - Azure Functions
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - Služba Device Provisioning (DPS)
* Jaké jiné **osobní nebo firemní aplikace** mám připojit k mé instanci?

Tyto informace můžete shromáždit pomocí [Azure Portal](https://portal.azure.com), [rozhraní API a sad SDK pro Azure](how-to-use-apis-sdks.md), digitálních vláken Azure, [příkazů CLI](how-to-use-cli.md)nebo ukázky v [Průzkumníkovi digitálních vláken Azure (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

## <a name="prepare"></a>Příprava

V této části se připravujete na opětovné vytvoření instance **stažením původních modelů, vláken a grafů** z původní instance. V tomto článku se používá ukázka [Průzkumníka digitálních vláken Azure (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) .

>[!NOTE]
>Již možná máte soubory obsahující modely a/nebo graf ve vaší instanci. Pokud ano, nemusíte znovu stahovat všechno, ale jenom ty, které jste nezměnili, nebo něco, co se mohlo od původně nahraných těchto souborů změnit (například vlákna, která se možná aktualizovala novými daty).

### <a name="limitations-of-adt-explorer"></a>Omezení Průzkumníka aplikace ADT

[Ukázka Průzkumníka digitálních vláken Azure (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) je ukázka klientské aplikace, která podporuje vizuální znázornění grafu a poskytuje vizuální interakci s vaší instancí. Tento článek ukazuje, jak ho používat ke stažení a pozdějšímu nahrávání, vašich modelů, vláken a grafů.

Upozorňujeme však, že se jedná o **ukázku** a ne úplný nástroj. Nebyla testována zátěžová a nebyla vytvořena pro zpracování grafů o velké velikosti. V důsledku toho je potřeba vzít v úvahu následující předem uvedená omezení pro vzor:
* Vzorek se aktuálně testoval jenom na velikost grafu až 1000 uzlů a 2000 vztahů
* Ukázka nepodporuje opakování v případě jakýchkoli přerušovaných selhání.
* Ukázka nebude nutně informovat uživatele, pokud jsou data nahraná neúplná.
* Ukázka nezpracovává chyby vyplývající z velmi rozsáhlých grafů, které překračují dostupné prostředky, jako je paměť.

Pokud ukázka nedokáže zpracovat velikost grafu, můžete ho exportovat a importovat pomocí dalších nástrojů pro vývojáře digitálních vláken Azure.
* [Příkazy rozhraní příkazového řádku Azure pro digitální vlákna](how-to-use-cli.md)
* [Rozhraní API a sady SDK pro digitální vlákna Azure](how-to-use-apis-sdks.md)

### <a name="set-up-adt-explorer-application"></a>Nastavení aplikace pro aplikaci ADT Explorer

Pokud chcete pokračovat v Průzkumníkovi aplikace ADT, nejdřív si stáhněte ukázkový kód aplikace a nastavte ho tak, aby se spouštěl na vašem počítači. 

Přejděte k ukázce sem: [Průzkumník digitálních vláken Azure (ADT)](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Stiskněte tlačítko *Stáhnout soubor zip* a Stáhněte si *. Soubor ZIP* tohoto ukázkového kódu na váš počítač jako _**ADT_Explorer.zip**_. Rozbalte soubor.

Dále nastavte oprávnění pro Průzkumníka aplikace ADT ke spuštění na vašem počítači. Provedete to podle kroků v části [*Nastavení oprávnění Průzkumníka programu ADT*](quickstart-adt-explorer.md#set-adt-explorer-permissions) v rychlém startu pro digitální vlákna Azure.

Nakonec spusťte a nakonfigurujte Průzkumníka aplikace ADT, aby se připojil k původní instanci digitálních vláken Azure. Postupujte podle kroků v části [*spuštění a konfigurace nástroje ADT Explorer*](quickstart-adt-explorer.md#run-and-configure-adt-explorer) v rychlém startu.

Nyní byste měli mít ukázkovou aplikaci Průzkumníka ADT spuštěnou v prohlížeči na vašem počítači. Vzorek by měl být připojený k původní instanci digitálních vláken Azure.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/explorer-blank.png":::

Chcete-li ověřit připojení, můžete stisknout tlačítko *Spustit dotaz* a spustit výchozí dotaz, který v grafu zobrazí všechny vazby a relace v grafu v poli *Průzkumník grafů* .

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/run-query.png":::

Můžete nechat Průzkumníka aplikace ADT spuštěnou, protože ho později budete moct později v tomto článku znovu nahrát do nové instance v cílové oblasti.

### <a name="download-models-twins-and-graph"></a>Stáhnout modely, vlákna a graf

V dalším kroku Stáhněte do svého řešení modely, vlákna a graf.

Chcete-li některé z těchto souborů stáhnout najednou, zajistěte, aby se v poli *zobrazení grafu* zobrazoval úplný graf (můžete to provést tak, že znovu spustíte výchozí dotaz z okna `SELECT * FROM digitaltwins` *Průzkumník dotazů* ).
 
Pak v poli *zobrazení grafu* stiskněte ikonu *Export grafu* .

:::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/export-graph.png":::

Tím se povolí odkaz *ke stažení* v *zobrazení grafu*. Vyberte ji a Stáhněte si výsledek dotazu založený na formátu JSON, včetně vašich modelů, vláken a vztahů. To by mělo na váš počítač stáhnout soubor *. JSON* .

>[!NOTE]
>Pokud se zdá, že stažený soubor má jinou příponu, zkuste rozšíření upravit přímo a změnit na *. JSON*.

## <a name="move"></a>Přesunout

Dále dokončíte přesunutí instance vytvořením nové instance v cílové oblasti a jejím naplněním dat a součástí z původní instance.

### <a name="create-a-new-instance"></a>Vytvořit novou instanci

Nejdřív **vytvořte novou instanci digitálních vláken Azure v cílové oblasti**. Provedete to podle kroků v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md)a zachovávání těchto ukazatelů:
* Stejný název můžete zachovat pro novou instanci, **Pokud** se nachází v jiné skupině prostředků. Pokud potřebujete použít stejnou skupinu prostředků, která obsahuje původní instanci, bude vaše nová instance potřebovat vlastní jedinečný název.
* Až se zobrazí výzva k zadání umístění, zadejte cílovou novou oblast.
* Registraci aplikace **nemusíte** vytvářet znovu. Vaše nová instance může znovu použít stejnou registraci aplikace, kterou už máte.
    - Pokud používáte článek s [skriptem](how-to-set-up-instance-scripted.md) instalace, můžete po zobrazení výzvy znovu zadat podrobnosti o registraci existující aplikace, a to bez zadání nového názvu.
    - Pokud používáte portál pro ruční instalaci [portálu](how-to-set-up-instance-portal.md) nebo rozhraní příkazového [řádku](how-to-set-up-instance-cli.md) , můžete zastavit za *instancí vytvoření digitálního vlákna Azure* a *nastavením kroků přístupu uživatele* . Není nutné pokračovat v *Nastavení oprávnění přístupu pro klientské aplikace*.

Po dokončení budete potřebovat **název hostitele** nové instance, abyste mohli pokračovat v nastavování s Vašimi daty. Pokud jste to neudělali během instalace, můžete postupovat podle [těchto pokynů](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) a získat to hned z Azure Portal.

### <a name="repopulate-old-instance"></a>Znovu naplnit starou instanci

V dalším kroku nastavíte novou instanci tak, aby byla kopií originálu.

#### <a name="upload-original-models-twins-and-graph-using-adt-explorer"></a>Nahrání původních modelů, vláken a grafů pomocí Průzkumníka aplikace ADT

V této části můžete znovu nahrát vaše modely, vlákna a graf do nové instance. Pokud nemáte žádné modely, vlákna nebo grafy v původní instanci nebo je nechcete přesunout do nové instance, můžete přejít k [Další části](#recreate-endpoints-and-routes).

Jinak pokračujte tím, že se vrátíte do okna prohlížeče, ve kterém je spuštěný **Průzkumník aplikace ADT** , a postupujte podle následujících kroků.

##### <a name="connect-to-the-new-instance"></a>Připojit k nové instanci

V současné době je Průzkumník aplikace ADT připojený k původní instanci digitálních vláken Azure. Přepněte připojení tak, aby odkazovalo na novou instanci, tak, že kliknete na tlačítko *Přihlásit* v horní části okna. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/sign-in.png":::

Vzhledem k tomu, že používáte registraci aplikace znovu, stačí nahradit *adresu URL programu ADT*. Změňte tuto hodnotu na ta, která načte *https://{New instance hostname}*.

Stiskněte *připojit*. Může se zobrazit výzva k opětovnému přihlášení s přihlašovacími údaji Azure a/nebo udělení tohoto souhlasu s aplikací pro vaši instanci.

##### <a name="upload-models-twins-and-graph"></a>Nahrávání modelů, vláken a grafů

Dále nahrajte součásti řešení, které jste stáhli dříve, do nové instance.

Chcete-li nahrát vaše **modely, vlákna a graf**, stiskněte ikonu *Import grafu* v poli *zobrazení grafu* . Tato možnost nahraje všechny tři tyto součásti najednou (dokonce i modely, které se v grafu aktuálně nepoužívají).

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/import-graph.png":::

V poli pro výběr souboru přejděte do staženého grafu. Vyberte soubor Graph *. JSON* a stiskněte *tlačítko otevřít*.

Po několika sekundách otevře Průzkumník aplikace ADT zobrazení pro *Import* , ve kterém se zobrazí náhled grafu, který se bude načítat.

Pokud chcete potvrdit nahrávání grafu, stiskněte ikonu *Uložit* v pravém horním rohu *zobrazení grafu*:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Průzkumník aplikace ADT nyní nahraje vaše modely a graf (včetně zdvojených a relací) do nové instance digitálního vlákna Azure. Měla by se zobrazit zpráva o úspěchu, která označuje, kolik modelů, vláken a relací bylo odesláno:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/import-success.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Pokud chcete ověřit, jestli se všechno úspěšně nahrálo, klikněte na tlačítko *Spustit dotaz* v poli *Průzkumník grafů* a spusťte výchozí dotaz, který zobrazí všechny vazby a relace v grafu. Tím se také obnoví seznam modelů v *zobrazení modelu*.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/run-query.png":::

Měl by se zobrazit graf se všemi jeho dvojitými vazbymi a relacemi zobrazenými v poli *Průzkumník grafů* . Měly by se také zobrazit vaše modely uvedené v poli *zobrazení modelu* .

:::image type="content" source="media/how-to-move-regions/post-upload.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce." lightbox="media/how-to-move-regions/post-upload.png":::

Tím se potvrdí, že vaše modely, vlákna a grafy se znovu nahrály do nové instance v cílové oblasti.

#### <a name="recreate-endpoints-and-routes"></a>Opětovné vytvoření koncových bodů a tras

Pokud máte v původní instanci **koncové body nebo trasy** , budete je muset v nové instanci znovu vytvořit. Pokud v původní instanci nemáte žádné koncové body nebo trasy nebo je nechcete přesunout do nové instance, můžete přejít k [Další části](#re-link-connected-resources).

V opačném případě pokračujte podle kroků v tématu [*Postup: Správa koncových bodů a tras*](how-to-manage-routes-portal.md) pomocí nové instance, přičemž mějte na paměti tyto ukazatele: 
* **Nemusíte** vytvářet znovu Event Grid, centrum událostí ani prostředek Service Bus, které používáte pro koncový bod (*požadavky* v pokynech pro koncové body). Stačí znovu vytvořit koncový bod v instanci digitálních vláken Azure.
* Můžete znovu použít koncová **jména**a směrování, protože jsou vymezeny na jinou instanci.
* Nezapomeňte přidat všechny požadované **filtry** na trasy, které vytvoříte.

#### <a name="re-link-connected-resources"></a>Znovu propojit připojené prostředky

Pokud máte jiné aplikace nebo prostředky Azure, které jsou připojené k původní instanci digitálních vláken Azure, budete muset připojení upravit tak, aby místo toho dosáhlo nové instance. To může zahrnovat další služby Azure, osobní nebo firemní aplikace, které jste nastavili pro práci s digitálními úkoly Azure.

Pokud nemáte žádné další prostředky připojené k původní instanci nebo je nechcete přesunout do nové instance, můžete přejít k [Další části](#verify).

V opačném případě pokračujte ve svém scénáři i na připojené prostředky. Nemusíte odstraňovat a znovu vytvářet žádné připojené prostředky; místo toho stačí upravit body, které se připojují k instanci digitálních vláken Azure pomocí jejího **názvu hostitele**, a aktualizovat je tak, aby místo originálu používaly název hostitele nové instance.

Přesné prostředky, které je třeba upravit, závisí na vašem scénáři, ale zde jsou některé běžné integrační body:
* Azure Functions. Pokud máte funkci Azure, jejíž kód obsahuje název hostitele původní instance, měli byste tuto hodnotu aktualizovat na název hostitele nové instance a funkci znovu publikovat.
* Event Grid, Event Hubs nebo Service Bus
* Logic Apps
* Time Series Insights
* Azure Maps
* Služba Device Provisioning (DPS)
* Osobní nebo firemní aplikace mimo Azure, jako je například **klientská aplikace** vytvořená v [*kurzu: Code a klientská aplikace*](tutorial-code.md), která se připojí k instanci a volá rozhraní API digitálních vláken Azure

Po dokončení tohoto kroku by nová instance v cílové oblasti měla být kopií původní instance.

## <a name="verify"></a>Ověření

Chcete-li ověřit, zda byla nová instance nastavena správně, můžete použít následující nástroje:
* [**Azure Portal**](https://portal.azure.com) (dobrý pro ověření, že vaše nová instance existuje a je ve správné cílové oblasti. taky dobrý pro ověření koncových bodů a tras a připojení k dalším službám Azure)
* [ **Příkazy rozhraní příkazového řádku** služby Azure Digital](how-to-use-cli.md) vyplní (dobrým účelem ověření, že vaše nová instance existuje a je ve správné cílové oblasti; dá se taky použít k ověření dat instance).
* [**Průzkumník aplikace ADT**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (dobrý pro ověřování dat instance, jako jsou modely, vlákna a graf)
* [Rozhraní API a sady SDK pro digitální vlákna](how-to-use-apis-sdks.md) (vhodné pro ověřování dat instancí, jako jsou modely, vlákna a graf), jsou vhodné i pro ověření koncových bodů a tras.

Můžete také zkusit spustit libovolné vlastní aplikace nebo komplexní toky, které jste spustili s původní instancí, abyste mohli ověřit, že pracují s novou instancí správně.

## <a name="clean-up-source-resources"></a>Vyčištění zdrojových prostředků

Teď, když je vaše nová instance nastavená v cílové oblasti s kopií dat a připojení původní instance, můžete **původní instanci odstranit**.

To můžete provést v [Azure Portal](https://portal.azure.com), pomocí rozhraní příkazového [řádku](how-to-use-cli.md)nebo pomocí [rozhraní API plochy ovládacího prvku](how-to-use-apis-sdks.md#overview-control-plane-apis).

Pokud chcete instanci odstranit pomocí Azure Portal, [Otevřete portál](https://portal.azure.com) v okně prohlížeče a přejděte do původní instance digitálního vlákna Azure, a to tak, že vyhledáte jeho název na panelu hledání na portálu.

Stiskněte tlačítko *Odstranit* a dokončete odstranění podle pokynů.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="Okno prohlížeče zobrazující aplikaci spuštěnou na localhost: 3000. Aplikace se nazývá aplikace ADT Explorer a obsahuje pole pro Průzkumníka dotazů, zobrazení modelu, zobrazení grafu a Průzkumník vlastností. Ještě nejsou k dispozici žádná data na obrazovce.":::