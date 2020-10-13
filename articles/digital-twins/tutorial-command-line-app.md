---
title: 'Kurz: zkoumání základních informací pomocí ukázkové klientské aplikace'
titleSuffix: Azure Digital Twins
description: Kurz k prozkoumání sad SDK digitálních vláken Azure s využitím ukázkové aplikace příkazového řádku
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 960f76b06644a86ebaaaed40b57db95bdb350833
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91945511"
---
# <a name="tutorial-explore-azure-digital-twins-with-a-sample-client-app"></a>Kurz: prozkoumání digitálních vláken Azure pomocí ukázkové klientské aplikace

Tento kurz zavádí ukázkovou aplikaci, která implementuje klientskou aplikaci příkazového řádku pro interakci s instancí digitálních vláken Azure. Klientská aplikace je podobná té, kterou jste napsali v [*kurzu: Code a klientská aplikace*](tutorial-code.md).

Tuto ukázku můžete použít k provádění základních akcí v Azure, jako je nahrávání modelů, vytváření a úpravy vláken a vytváření relací. Můžete se také podívat na kód ukázky, kde se dozvíte o rozhraních API digitálních vláken Azure a postup implementace vlastních příkazů úpravou ukázkového projektu, který byste ale chtěli.

V tomto kurzu budete...
> [!div class="checklist"]
> * Nastavení instance digitálních vláken Azure
> * Konfigurace ukázkové aplikace příkazového řádku pro interakci s instancí
> * Pomocí aplikace z příkazového řádku můžete prozkoumat digitální vlákna Azure, včetně **modelů**, **digitálních vláken**, **vztahů**a **dotazů** .

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>Prozkoumejte ukázkové řešení

Teď, když je nakonfigurovaná instance a ukázková aplikace, použijete ukázkový projekt a nějaký předem napsaný ukázkový kód k sestavení a prozkoumání základního řešení digitálních vláken Azure. Hlavní součásti řešení jsou **modely**, **digitální vlákna**a **vztahy**, což vede k queryablemu **grafu** prostředí.

### <a name="model-a-physical-environment-with-dtdl"></a>Modelování fyzického prostředí pomocí DTDL

Prvním krokem při vytváření řešení digitálních vláken Azure je definování dvojitých [**modelů**](concepts-models.md) pro vaše prostředí. 

Modely jsou podobné třídám v objektově orientovaném programovacím jazyce; poskytují uživatelsky definované šablony pro [digitální vlákna](concepts-twins-graph.md) , které budou následovat a vytvoří se později. Jsou napsány v jazyce podobném formátu JSON, který se nazývá **Digital DTDL (digitální vlákna)**, a může definovat *vlastnosti*, *telemetrie*, *relace*a *komponenty*s dvojitou čárkou.

> [!NOTE]
> DTDL také umožňuje definici *příkazů* pro digitální vlákna. V současné době se ale ve službě Azure Digital probíhají příkazy aktuálně nepodporují.

V okně aplikace Visual Studio, kde je otevřen projekt _**AdtE2ESample**_ , použijte podokno *Průzkumník řešení* a přejděte do složky *AdtSampleApp\SampleClientApp\Models* . Tato složka obsahuje ukázkové modely.

Vyberte možnost *Room.jszapnuto* a otevřete ji v okně pro úpravy a změňte ji následujícími způsoby:

* **Aktualizujte číslo verze**, abyste označili, že poskytujete aktualizovanou verzi tohoto modelu. Provedete to tak, že změníte *1* na konci `@id` hodnoty na *2*. Bude fungovat i číslo větší než aktuální číslo verze.
* **Upravit vlastnost**. Změňte název `Humidity` vlastnosti na *HumidityLevel* (nebo něco jiného, pokud chcete. Pokud používáte něco jiného než *HumidityLevel*, pamatujte na to, co jste použili, a místo *HumidityLevel* v průběhu tohoto kurzu ho budete používat.
* **Přidejte vlastnost**. Pod `HumidityLevel` vlastností, která končí na řádku 15, vložte následující kód pro přidání `RoomName` vlastnosti do místnosti:

    ```json
    ,
    {
      "@type": "Property",
      "name": "RoomName",
      "schema": "string"
    }
    ```
* **Přidejte relaci**. Pod `RoomName` vlastnost, kterou jste právě přidali, vložte následující kód pro přidání možnosti pro tento typ vlákna do formuláře *obsahuje* relace s jinými nezávislostmi:

    ```json
    ,
    {
      "@type": "Relationship",
      "name": "contains",
    }
    ```

Až skončíte, aktualizovaný model by měl vypadat takto:

:::image type="content" source="media/tutorial-command-line-app/room-model.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci." border="false":::

Před přechodem na. Nezapomeňte soubor uložit.

> [!TIP]
> Pokud se chcete pokusit vytvořit vlastní model, můžete vložit kód modelu *Room* do nového souboru, který uložíte s příponou *. JSON* ve složce *AdtSampleApp\SampleClientApp\Models* . Pak se můžete pohybovat s přidáním vlastností a vztahů, které budou představovat cokoli, co byste chtěli. Můžete se také podívat na Další ukázkové modely v této složce, kde najdete nápady.

> [!TIP] 
> Je k dispozici [Ukázka validátoru](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) jazyka nezávislá DTDL, kterou můžete použít ke kontrole modelu dokumentů, abyste měli jistotu, že je DTDL platný. Je postaven na knihovně analyzátoru DTDL, kterou si můžete přečíst v tématu [*Postupy: analýza a ověření modelů*](how-to-parse-models.md).

### <a name="get-started-with-the-command-line-app"></a>Začínáme s aplikací příkazového řádku

Teď, když jste definovali model, zbývající kroky zahrnují použití ukázkové aplikace k interakci s instancí digitálních vláken Azure. Spusťte projekt pomocí tohoto tlačítka na panelu nástrojů:

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

Otevře se okno konzoly, provede se ověřování a počká na příkaz. 
* Ověřování se provádí prostřednictvím prohlížeče: otevře se výchozí webový prohlížeč s výzvou k ověření. Pomocí této výzvy se můžete přihlásit pomocí přihlašovacích údajů Azure. Pak můžete zavřít kartu nebo okno prohlížeče.

Tady je snímek obrazovky, jak vypadá konzola projektu jako:

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

> [!TIP]
> Seznam všech možných příkazů, které můžete použít s tímto projektem, získáte, když zadáte `help` v konzole projektu a stisknete vrátit.
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

Nechejte konzoli projektu spuštěnou pro zbytek kroků v tomto kurzu.

#### <a name="upload-models-to-azure-digital-twins"></a>Nahrávání modelů do digitálních vláken Azure

Po navržení modelů je budete muset nahrát do instance digitálního vlákna Azure. Tím se nakonfiguruje instance služby Azure Digital Service s vlastním slovníkem domény. Po nahrání modelů můžete vytvořit zdvojené instance, které je používají.

V okně konzoly projektu spusťte následující příkaz, který nahraje aktualizovaný model *místnosti* , a také model *podlah* , který použijete v další části k vytvoření různých typů vláken.

```cmd/sh
CreateModels Room Floor
```

Výstup by měl označovat, že se modely úspěšně vytvořily.

> [!TIP]
> Pokud jste dříve navrhli svůj vlastní model, můžete ho sem nahrát také tak, že přidáte jeho název (můžete si nechat rozšíření) do `Room Floor` seznamu v příkazu výše.

Ověřte vytvoření modelů spuštěním příkazu `GetModels true` . Tím se dotazuje instance digitálního vlákna Azure pro všechny nahrané modely a vytiskne jejich úplné informace. Ve výsledcích vyhledejte upravený model *místnosti* :

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

#### <a name="errors"></a>chyby

Ukázková aplikace také zpracovává chyby ze služby. 

Opakovaným spuštěním `CreateModels` příkazu zkuste znovu nahrát jeden ze stejných modelů, které jste právě Nahráli, a to za sekundu:

```cmd/sh
CreateModels Room
```

Jelikož modely nelze přepsat, nyní bude vrácena chyba služby.
Podrobnosti o tom, jak odstranit existující modely, naleznete v tématu [*How to: Manage Custom Models*](how-to-manage-model.md).
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

### <a name="create-digital-twins"></a>Vytváření digitálních vláken

Teď, když se některé modely nahrály do instance digitálního vlákna v Azure, můžete vytvořit [**digitální vlákna**](concepts-twins-graph.md) na základě definic modelů. Digitální vlákna reprezentují entity v rámci vašeho podnikového prostředí – například senzory ve farmě, místnosti v budově nebo světla v kleci. 

K vytvoření digitálního vlákna použijte `CreateDigitalTwin` příkaz. Musíte odkazovat na model, na kterém je založena, a může volitelně definovat počáteční hodnoty pro všechny vlastnosti v modelu. V této fázi není nutné předávat žádné informace o relaci.

Spusťte tento kód v konzole spuštění projektu, abyste vytvořili několik vláken na základě modelu *místnosti* , který jste aktualizovali dříve, a dalšího modelu, *podlaží*. V této *místnosti* se nacházejí tři vlastnosti, takže můžete zadat argumenty s počátečními hodnotami.

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> Pokud jste dříve nahráli svůj vlastní model, zkuste `CreateDigitalTwin` vytvořit vlastní příkaz založený na výše uvedených příkazech a přidat tak nevlákenný typ modelu.

Výstup z těchto příkazů by měl označovat, že se vlákna úspěšně vytvořila. 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

Spuštěním příkazu můžete také ověřit, zda byly vlákna vytvořeny `Query` . Tento příkaz se dotazuje vaší instance digitálního vlákna Azure pro všechny digitální vlákna, které obsahuje. Ve výsledcích vyhledejte vlákna *floor0*, *floor1*, *room0*a *Room1* .

#### <a name="modify-a-digital-twin"></a>Úprava digitálního vlákna

Můžete také upravit vlastnosti vlákna, které jste vytvořili. Zkuste spustit tento příkaz, aby se změnila *room0á*místnost z *room0* na *PresidentialSuite*:

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

Výstup by měl indikovat, že se úspěšně aktualizovala vlákna.

Můžete také ověřit spuštěním tohoto příkazu, abyste viděli informace *room0*:

```cmd/sh
GetDigitalTwin room0
```

Výstup by měl odpovídat aktualizovanému názvu.

> [!NOTE]
> Základní REST API používá opravu JSON k definování aktualizací pro dvojitou hodnotu. Aplikace příkazového řádku odráží tento formát, takže můžete experimentovat s tím, co vaše základní rozhraní API skutečně očekávají.

### <a name="create-a-graph-by-adding-relationships"></a>Vytvoření grafu přidáním relací

V dalším kroku můžete vytvořit některé **relace** mezi těmito podmnožinami a spojit je s [**dvojitým grafem**](concepts-twins-graph.md). K reprezentaci celého prostředí se používají dvojitá grafu. 

Chcete-li přidat relaci, použijte `CreateRelationship` příkaz. Určete, jak se má vztah nacházet z, typ relace, kterou chcete přidat, a vlákna, ke kterému se vztah připojuje. Nakonec zadejte název (ID) vztahu.

Spusťte následující kód, který přidá relaci "obsahuje" ze všech vláken s *podlahou* , které jste vytvořili dříve, do odpovídající vlákna *místnosti* . Aby to bylo možné, musí *existovat relace, která je definována* v modelu *Floor* .

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

Výstup z těchto příkazů potvrzuje, že relace byly úspěšně vytvořeny:

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

Můžete také ověřit vztahy pomocí kteréhokoli z následujících příkazů, které dotazují relace v instanci digitálních vláken Azure.
* Chcete-li zobrazit všechny relace, které jsou vydány z jednotlivých podlah (Zobrazení relací z jedné strany),
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* Chcete-li zobrazit všechny vztahy přicházející v každé místnosti (zobrazení vztahu na straně "ostatní"),
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* Pokud se chcete dotazovat na tyto relace jednotlivě, 
    ```cmd/sh
    GetRelationship floor0 relationship0
    GetRelationship floor1 relationship1
    ```

Vlákna a vztahy, které jste nastavili v tomto kurzu, tvoří následující koncepční graf:

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci." border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>Dotaz na dvojitou grafickou odpověď na otázky prostředí

Hlavní funkcí digitálních vláken Azure je schopnost snadno a efektivně [dotazovat](concepts-query-language.md) se na váš graf, aby odpovídal na dotazy týkající se vašeho prostředí. Spusťte následující příkazy v konzole spuštění projektu, abyste získali představu o tom, co je to třeba.

* **Jaké jsou všechny entity v mém prostředí reprezentované ve službě Azure Digital revláken?** (dotaz na vše)

    ```cmd/sh
    Query
    ```

    To vám umožní pořídit si vaše prostředí na první pohled a zajistit, aby vše představovalo, jak byste chtěli být v rámci digitálních vláken Azure. Výsledek tohoto je výstup obsahující každou digitální dvojitou hodnotu s jeho podrobnostmi. Tady je ukázka:

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

    >[!NOTE]
    >V ukázkovém projektu `Query` je příkaz bez dalších argumentů ekvivalentem `Query SELECT * FROM DIGITALTWINS` . K dotazování všech vláken ve vaší instanci pomocí [rozhraní API dotazů](how-to-use-apis-sdks.md) nebo [příkazů CLI](how-to-use-cli.md)použijte dotaz delší (kompletní).

* **Jaké jsou všechny místnosti v mém prostředí?** (dotaz podle modelu)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Dotaz můžete omezit na vlákna určitého typu, abyste získali konkrétnější informace o tom, co je znázorněno. Výsledek tohoto zobrazení je *room0* a *Room1*, ale nezobrazuje *floor0* nebo *floor1* (protože **se jedná o** podlahu, ne místnosti).
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

* **Jaké jsou všechny místnosti v *floor0*?** (dotaz podle relace)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Dotaz na základě relací v grafu vám umožní získat informace o tom, jak jsou vlákna propojená, nebo omezit dotaz na určitou oblast. Pouze *room0* je na *floor0*, takže se jedná o jediné místo ve výsledku.

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

* **Jaké jsou všechny vlákna v mém prostředí s teplotou vyšší než 75?** (dotaz podle vlastnosti)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Dotaz na graf můžete na základě vlastností využít k zodpovězení nejrůznějších otázek, mezi které patří hledání ve vašem prostředí, které může vyžadovat pozornost. Podporovány jsou i další operátory porovnání ( *<* , *>* , *=* , nebo *! =*). *Room1* se zobrazí ve výsledcích, protože má teplotu 80.

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

* **Jaké jsou všechny místnosti v *floor0* s teplotou vyšší 75?** (složený dotaz)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Můžete také zkombinovat předchozí dotazy jako v SQL, a to pomocí operátorů kombinace, jako je `AND` , `OR` , `NOT` . Tento dotaz používá `AND` k tomu, aby předchozí dotaz o dvojitě specifických teplotách byl konkrétnější. Výsledek teď obsahuje jenom místnosti s teplotami nad 75, které jsou v *floor0*, což v tomto případě není žádné z nich. Sada výsledků dotazu je prázdná.

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="Upraveno Room.jss aktualizovaným číslem verze, vlastnostmi HumidityLevel a místností a obsahuje relaci.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Projekt v tomto kurzu je základem pro další kurz, [*kurz: připojení uceleného řešení*](tutorial-end-to-end.md). Pokud máte v plánu pokračovat k dalšímu kurzu, můžete zde nastavené prostředky zachovat, abyste mohli dál používat tuto instanci digitálních vláken Azure a nakonfigurovanou ukázkovou aplikaci.
* V takovém případě můžete použít ukázkové aplikace `DeleteAllTwins` a `DeleteAllModels` příkazy k vymazání nevláken a modelů v instanci, v uvedeném pořadí. V dalším kurzu vám poskytneme čistou SLAT.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Nakonec odstraňte ukázkovou složku projektu, kterou jste stáhli do svého místního počítače.

## <a name="next-steps"></a>Další kroky 

V tomto kurzu jste začali s digitálními podmnožinami Azure pomocí nastavení instance a klientské aplikace pro interakci s instancí. Klientská aplikace se používá k prozkoumávání digitálních vláken Azure, vytváření modelů, digitálních vláken a vztahů. V řešení jste také spustili nějaké dotazy, abyste získali představu o druzích otázek, které může Azure Digital revláken využít k zodpovězení prostředí.

Přejděte k dalšímu kurzu a použijte ukázkovou aplikaci příkazového řádku v kombinaci s jinými službami Azure a dokončete tak kompletní scénář založený na datech:
> [!div class="nextstepaction"]
> [*Kurz: připojení kompletního řešení*](tutorial-end-to-end.md)