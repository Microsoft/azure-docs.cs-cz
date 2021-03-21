---
title: 'Kurz: vytvoření grafu v oblasti digitálních vláken Azure (klientská aplikace)'
titleSuffix: Azure Digital Twins
description: Kurz pro vytvoření scénáře digitálních vláken Azure pomocí ukázkové aplikace příkazového řádku
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493696"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Kurz: vytvoření grafu digitálních vláken Azure pomocí ukázkové klientské aplikace

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

V tomto kurzu sestavíte graf v oblasti digitálních vláken Azure pomocí modelů, vláken a vztahů. Nástroj pro tento kurz je **ukázková klientská aplikace příkazového řádku** pro interakci s instancí digitálních vláken Azure. Klientská aplikace je podobná té, kterou jste napsali v [*kurzu: Code a klientská aplikace*](tutorial-code.md).

Tuto ukázku můžete použít k provádění základních akcí v Azure, jako je nahrávání modelů, vytváření a úpravy vláken a vytváření relací. Můžete se také podívat na [kód ukázky](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) , kde se dozvíte o rozhraních API digitálních vláken Azure a postup implementace vlastních příkazů úpravou ukázkového projektu, který byste ale chtěli.

V tomto kurzu budete...
> [!div class="checklist"]
> * Modelování prostředí
> * Vytvoření digitálních dvojčat
> * Přidání relací pro vytvoření grafu
> * Dotaz na graf pro zodpovězení otázek

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Spuštění ukázkového projektu

Teď, když je aplikace a ověřování nastavená, spusťte projekt pomocí tohoto tlačítka na panelu nástrojů:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Snímek obrazovky s tlačítkem Start v aplikaci Visual Studio (projekt SampleClientApp)" lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Otevře se okno konzoly, provede se ověřování a počká na příkaz. 
* Ověřování se provádí prostřednictvím prohlížeče: otevře se výchozí webový prohlížeč s výzvou k ověření. Pomocí této výzvy se můžete přihlásit pomocí přihlašovacích údajů Azure. Pak můžete zavřít kartu nebo okno prohlížeče.

Tady je snímek obrazovky, jak vypadá konzola projektu jako:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Snímek obrazovky úvodní zprávy z aplikace z příkazového řádku" lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> Seznam všech možných příkazů, které můžete použít s tímto projektem, získáte, když zadáte `help` v konzole projektu a stisknete vrátit.

Nechejte konzoli projektu spuštěnou pro zbytek kroků v tomto kurzu.

## <a name="model-a-physical-environment-with-dtdl"></a>Modelování fyzického prostředí pomocí DTDL

Teď, když je instance digitálních vláken Azure a ukázková aplikace nastavená, můžete začít sestavovat graf scénáře. 

Prvním krokem při vytváření řešení digitálních vláken Azure je definování dvojitých [**modelů**](concepts-models.md) pro vaše prostředí. 

Modely jsou podobné třídám v objektově orientovaném programovacím jazyce; poskytují uživatelsky definované šablony pro [digitální vlákna](concepts-twins-graph.md) , které budou následovat a vytvoří se později. Jsou napsány v jazyce podobném formátu JSON, který se nazývá **Digital DTDL (digitální vlákna)**, a může definovat *vlastnosti*, *telemetrie*, *relace* a *komponenty* s dvojitou čárkou.

> [!NOTE]
> DTDL také umožňuje definici *příkazů* pro digitální vlákna. V současné době se ale ve službě Azure Digital probíhají příkazy aktuálně nepodporují.

V okně aplikace Visual Studio, kde je otevřen projekt _**AdtE2ESample**_ , použijte podokno *Průzkumník řešení* a přejděte do *složky AdtSampleApp\SampleClientApp\Models*. Tato složka obsahuje ukázkové modely.

Vyberte možnost *Room.jszapnuto* a otevřete ji v okně pro úpravy a změňte ji následujícími způsoby:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Nahrávání modelů do digitálních vláken Azure

Po navržení modelů je budete muset nahrát do instance digitálního vlákna Azure. Tím se nakonfiguruje instance služby Azure Digital Service s vlastním slovníkem domény. Po nahrání modelů můžete vytvořit zdvojené instance, které je používají.

1. V okně konzoly projektu spusťte následující příkaz, který nahraje aktualizovaný model *místnosti* , a také model *podlah* , který použijete v další části k vytvoření různých typů vláken.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    Výstup by měl označovat, že se modely úspěšně vytvořily.

1. Ověřte vytvoření modelů spuštěním příkazu `GetModels true` . Tím se dotazuje instance digitálního vlákna Azure pro všechny nahrané modely a vytiskne jejich úplné informace. Ve výsledcích vyhledejte upravený model *místnosti* :

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Snímek obrazovky výsledku z getmodels zobrazující aktualizovaný model místnosti" lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Chyby

Ukázková aplikace také zpracovává chyby ze služby. 

Opakovaným spuštěním `CreateModels` příkazu zkuste znovu nahrát jeden ze stejných modelů, které jste právě Nahráli, a to za sekundu:

```cmd/sh
CreateModels Room
```

Jelikož modely nelze přepsat, nyní bude vrácena chyba služby.
Podrobnosti o tom, jak odstranit existující modely, naleznete v tématu [*How to: Manage DTDL Models*](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>Vytvoření digitálních dvojčat

Teď, když se některé modely nahrály do instance digitálního vlákna v Azure, můžete vytvořit [**digitální vlákna**](concepts-twins-graph.md) na základě definic modelů. Digitální vlákna reprezentují entity v rámci vašeho podnikového prostředí – například senzory ve farmě, místnosti v budově nebo světla v kleci. 

K vytvoření digitálního vlákna použijte `CreateDigitalTwin` příkaz. Musíte odkazovat na model, na kterém je založena, a může volitelně definovat počáteční hodnoty pro všechny vlastnosti v modelu. V této fázi není nutné předávat žádné informace o relaci.

1. Spusťte tento kód v konzole spuštění projektu, abyste vytvořili několik vláken na základě modelu *místnosti* , který jste aktualizovali dříve, a dalšího modelu, *podlaží*. V této *místnosti* se nacházejí tři vlastnosti, takže můžete zadat argumenty s počátečními hodnotami. (Inicializace hodnot vlastností je obecně volitelná, ale jsou pro tento kurz potřeba.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    Výstup z těchto příkazů by měl označovat, že se vlákna úspěšně vytvořila. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Snímek obrazovky znázorňující výpis z výsledku příkazů CreateDigitalTwin, který zahrnuje floor0, floor1, room0 a Room1." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. Spuštěním příkazu si můžete ověřit, že se vlákna vytvořila `Query` . Tento příkaz se dotazuje vaší instance digitálního vlákna Azure pro všechny digitální vlákna, které obsahuje. Ve výsledcích vyhledejte vlákna *room0*, *Room1*, *floor0* a *floor1* .

### <a name="modify-a-digital-twin"></a>Úprava digitálního vlákna

Můžete také upravit vlastnosti vlákna, které jste vytvořili. 

> [!NOTE]
> Základní REST API používá formát [opravy JSON](http://jsonpatch.com/) k definování aktualizací pro dvojitou hodnotu. Aplikace příkazového řádku používá tento formát také k tomu, aby truer prostředí, které očekává základní rozhraní API.

1. Spusťte tento příkaz, aby se změnila *room0á* místnost z *room0* na *PresidentialSuite*:
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    Výstup by měl indikovat, že se úspěšně aktualizovala vlákna.

1. Aktualizaci můžete úspěšně ověřit spuštěním tohoto příkazu, aby se zobrazily informace o *room0*:

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    Výstup by měl odpovídat aktualizovanému názvu.


## <a name="create-a-graph-by-adding-relationships"></a>Vytvoření grafu přidáním relací

V dalším kroku můžete vytvořit některé **relace** mezi těmito podmnožinami a spojit je s [**dvojitým grafem**](concepts-twins-graph.md). K reprezentaci celého prostředí se používají dvojitá grafu. 

Typy vztahů, které lze vytvořit z jednoho vlákna na jiný, jsou definovány v rámci [modelů](#model-a-physical-environment-with-dtdl) , které jste nahráli dříve. [Definice modelu pro *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) určuje, že podlaha může mít typ vztahu s názvem, který *obsahuje*. Díky tomu je možné vytvořit relaci typu *Contains* z každé *podlahové* vlákna do odpovídající místnosti, kterou obsahuje.

Chcete-li přidat relaci, použijte `CreateRelationship` příkaz. Určete, zda vztah pochází od, typu relace a vlákna, ke kterému se vztah připojuje. Nakonec Udělte tomuto vztahu jedinečné ID.

1. Spusťte následující kód, který přidá relaci "obsahuje" ze všech vláken s *podlahou* , které jste vytvořili dříve, do odpovídající vlákna *místnosti* . Relace mají název *relationship0* a *relationship1*.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >Relace *Contains* v [modelu *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) byla také definována se dvěma vlastnostmi řetězce `ownershipUser` a `ownershipDepartment` , takže můžete také zadat argumenty s počátečními hodnotami pro tyto hodnoty při vytváření vztahů.
    > Tady je alternativní verze příkazu pro vytvoření *relationship0* , která také určuje počáteční hodnoty pro tyto vlastnosti:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    Výstup z těchto příkazů potvrzuje, že relace byly úspěšně vytvořeny:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Snímek obrazovky výňatku z výsledku příkazů CreateRelationship, který zahrnuje relationship0 a relationship1." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. Můžete ověřit vztahy pomocí některého z následujících příkazů, které dotazují relace v instanci digitálních vláken Azure.
    * Zobrazení všech relací odcházejících z jednotlivých podlaží (Zobrazení relací z jedné strany):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * Zobrazení všech relací přicházejících v každé místnosti (zobrazení vztahu ze strany "ostatní"):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * Chcete-li tyto relace vyhledat jednotlivě, podle ID:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

Vlákna a vztahy, které jste nastavili v tomto kurzu, tvoří následující koncepční graf:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Diagram znázorňující koncepční graf floor0 je připojen prostřednictvím relationship0 k room0 a floor1 je připojen prostřednictvím relationship1 k Room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Dotaz na dvojitou grafickou odpověď na otázky prostředí

Hlavní funkcí digitálních vláken Azure je schopnost snadno a efektivně [dotazovat](concepts-query-language.md) se na váš graf, aby odpovídal na dotazy týkající se vašeho prostředí. 

Spusťte následující příkazy v konzole spuštění projektu, abyste odpověděli na některé otázky týkající se ukázkového prostředí.

1. **Jaké jsou všechny entity z mého prostředí reprezentované ve službě Azure Digital revláken?** (dotaz na vše)

    ```cmd/sh
    Query
    ```

    To vám umožní pořídit si vaše prostředí na první pohled a zajistit, aby vše představovalo, jak byste chtěli být v rámci digitálních vláken Azure. Výsledek tohoto je výstup obsahující každou digitální dvojitou hodnotu s jeho podrobnostmi. Tady je ukázka:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Snímek obrazovky znázorňující částečný výsledek z neúspěšného dotazu, včetně room0 a floor1.":::

    >[!NOTE]
    >V ukázkovém projektu `Query` je příkaz bez dalších argumentů ekvivalentem `Query SELECT * FROM DIGITALTWINS` . K dotazování všech vláken ve vaší instanci pomocí [rozhraní API dotazů](/rest/api/digital-twins/dataplane/query) nebo [příkazů CLI](how-to-use-cli.md)použijte dotaz delší (kompletní).

1. **Jaké jsou všechny místnosti v mém prostředí?** (dotaz podle modelu)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Dotaz můžete omezit na vlákna určitého typu, abyste získali konkrétnější informace o tom, co je znázorněno. Výsledek tohoto zobrazení je *room0* a *Room1*, ale nezobrazuje *floor0* nebo *floor1* (protože **se jedná o** podlahu, ne místnosti).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Snímek obrazovky s výsledkem z dotazu modelu zobrazující pouze room0 a Room1.":::

1. **Jaké jsou všechny místnosti v *floor0*?** (dotaz podle relace)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Dotaz na základě relací v grafu vám umožní získat informace o tom, jak jsou vlákna propojená, nebo omezit dotaz na určitou oblast. Pouze *room0* je na *floor0*, takže se jedná o jediné místo ve výsledku.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Snímek obrazovky s výsledkem dotazu relace, který zobrazuje room0":::

1. **Jaké jsou všechny vlákna v mém prostředí s teplotou vyšší než 75?** (dotaz podle vlastnosti)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Dotaz na graf můžete na základě vlastností využít k zodpovězení nejrůznějších otázek, mezi které patří hledání ve vašem prostředí, které může vyžadovat pozornost. Podporovány jsou i další operátory porovnání ( *<* , *>* , *=* , nebo *! =*). *Room1* se zobrazí ve výsledcích, protože má teplotu 80.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Snímek obrazovky s výsledkem z dotazu vlastnosti zobrazující pouze Room1.":::

1. **Jaké jsou všechny místnosti v *floor0* s teplotou vyšší 75?** (složený dotaz)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Můžete také zkombinovat předchozí dotazy jako v SQL, a to pomocí operátorů kombinace, jako je `AND` , `OR` , `NOT` . Tento dotaz používá `AND` k tomu, aby předchozí dotaz o dvojitě specifických teplotách byl konkrétnější. Výsledek teď obsahuje jenom místnosti s teplotami nad 75, které jsou v *floor0*, což v tomto případě není žádné z nich. Sada výsledků dotazu je prázdná.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Snímek obrazovky výsledku složeného dotazu, který nezobrazuje žádné výsledky" lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete zvolit prostředky, které chcete odebrat, v závislosti na tom, co byste chtěli udělat dál.

* **Pokud máte v plánu pokračovat k dalšímu kurzu**, můžete zde nastavené prostředky zachovat, abyste mohli dál používat tuto instanci digitálních vláken Azure a nakonfigurovanou ukázkovou aplikaci pro další kurz.

* **Pokud chcete pokračovat v používání instance digitálního vlákna Azure, ale vymažte všechny jeho modely, vlákna a vztahy**, můžete `DeleteAllTwins` `DeleteAllModels` k vymazání nevláken a modelů ve vaší instanci použít příkazy a příkazy ukázkové aplikace.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Je také možné, že budete chtít odstranit složku projektu z místního počítače.

## <a name="next-steps"></a>Další kroky 

V tomto kurzu jste začali s digitálními podmnožinami Azure vytvořením grafu ve vaší instanci pomocí ukázkové klientské aplikace. Vytvořili jste modely, digitální vlákna a vztahy k vytvoření grafu. Také jste spustili některé dotazy v grafu, abyste získali představu o druzích otázek, které může Azure Digital revláken využít k zodpovězení prostředí.

Přejděte k dalšímu kurzu a slučte digitální vlákna Azure s ostatními službami Azure a dokončete tak kompletní scénář založený na datech:
> [!div class="nextstepaction"]
> [*Kurz: připojení kompletního řešení*](tutorial-end-to-end.md)