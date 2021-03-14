---
title: 'Kurz: vytvoření grafu v oblasti digitálních vláken Azure (CLI)'
titleSuffix: Azure Digital Twins
description: Kurz pro vytvoření scénáře digitálních vláken Azure pomocí Azure CLI
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 16425c1f15b78e2d0aaa7a8a1f55ff47a44d56dd
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464664"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Kurz: vytvoření grafu digitálních vláken Azure pomocí rozhraní příkazového řádku Azure

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

V tomto kurzu sestavíte graf v oblasti digitálních vláken Azure pomocí modelů, vláken a vztahů. Nástroj pro tento kurz je [Sada příkazů pro digitální vlákna Azure, která je nastavená pro rozhraní příkazového **řádku Azure CLI**](how-to-use-cli.md). 

Příkazy rozhraní příkazového řádku můžete použít k provádění základních akcí Azure, jako je nahrávání modelů, vytváření a úpravy vláken a vytváření relací. Můžete se také podívat na [referenční dokumentaci pro *AZ DT* Command Set](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest) a zobrazit úplnou sadu příkazů rozhraní příkazového řádku.

V tomto kurzu budete...
> [!div class="checklist"]
> * Modelování prostředí
> * Vytvoření digitálních dvojčat
> * Přidání relací pro vytvoření grafu
> * Dotaz na graf pro zodpovězení otázek

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto kurzu musíte nejdřív splnit následující požadavky.

Pokud ještě nemáte předplatné Azure, vytvořte si **[bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** před tím, než začnete.

### <a name="download-the-sample-models"></a>Stažení ukázkových modelů

V tomto kurzu se používají dva předem napsané modely, které jsou součástí [komplexního ukázkového projektu](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) C# pro digitální vlákna Azure. Soubory modelu jsou umístěny zde: 
* [*Room.jsna*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*Floor.jsna*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Pokud chcete soubory získat na svém počítači, použijte navigační odkazy výše a zkopírujte do místních souborů na počítači stejné názvy (*Room.jszapnuté* a *Floor.json*).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Nastavit relaci Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Příprava instance digitálních vláken Azure

Pokud chcete pracovat s digitálními úkoly Azure v tomto článku, musíte nejdřív **nastavit instanci digitálních vláken Azure** a požadovaná oprávnění k jejímu použití. Pokud už máte instanci digitálních vláken Azure nastavenou na předchozí práci, můžete tuto instanci použít.

Jinak postupujte podle pokynů v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-cli.md). Pokyny také obsahují kroky k ověření, že jste dokončili každý krok úspěšně a že jste připraveni na přechod k používání nové instance.

Po nastavení instance digitálního vlákna Azure si poznamenejte následující hodnoty, které budete muset později připojit k instanci:
* **_název hostitele_ instance**
* **předplatné Azure** , které jste použili k vytvoření instance. 

Obě tyto hodnoty můžete pro svou instanci získat ve výstupu následujícího příkazu rozhraní příkazového řádku Azure CLI: 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Cloud Shell okno zobrazující výstup příkazu AZ DT show. Pole názvu hostitele a ID předplatného (část pole ID) se zvýrazní.":::

## <a name="model-a-physical-environment-with-dtdl"></a>Modelování fyzického prostředí pomocí DTDL

Teď, když je instance CLI a Azure Digital nastavená, můžete začít sestavovat graf scénáře. 

Prvním krokem při vytváření řešení digitálních vláken Azure je definování dvojitých [**modelů**](concepts-models.md) pro vaše prostředí. 

Modely jsou podobné třídám v objektově orientovaném programovacím jazyce; poskytují uživatelsky definované šablony pro [digitální vlákna](concepts-twins-graph.md) , které budou následovat a vytvoří se později. Jsou napsány v jazyce podobném formátu JSON, který se nazývá **Digital DTDL (digitální vlákna)**, a může definovat *vlastnosti*, *telemetrie*, *relace* a *komponenty* s dvojitou čárkou.

> [!NOTE]
> DTDL také umožňuje definici *příkazů* pro digitální vlákna. V současné době se ale ve službě Azure Digital probíhají příkazy aktuálně nepodporují.

Přejděte na počítač k *Room.jsv* souboru, který jste vytvořili v části [požadavky](#prerequisites) . Otevřete ho v editoru kódu a změňte ho následujícími způsoby:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Nahrávání modelů do digitálních vláken Azure

Po navržení modelů je budete muset nahrát do instance digitálního vlákna Azure. Tím se nakonfiguruje instance služby Azure Digital Service s vlastním slovníkem domény. Po nahrání modelů můžete vytvořit zdvojené instance, které je používají.

1. Chcete-li přidat modely pomocí Cloud Shell, bude nutné nahrát soubory modelu do úložiště Cloud Shell, aby byly soubory k dispozici při spuštění Cloud Shell příkazu, který je používá. Provedete to tak, že vyberete ikonu nahrát/stáhnout soubory a kliknete na Odeslat.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Okno Cloud Shell znázorňující výběr ikony nahrávání":::
    
    Přejděte na *Room.jsna* svém počítači a vyberte otevřít. Pak tento krok opakujte pro *Floor.jsna*.

1. Dále pomocí příkazu [**AZ DT model Create**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create) , jak vidíte níže, nahrajte aktualizovaný model *místnosti* do instance digitálního vlákna Azure. Druhý příkaz nahraje jiný model, *Floor*, který použijete v další části k vytvoření různých typů vláken.

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    Výstup z každého příkazu zobrazí informace o úspěšně nahraném modelu.

    >[!TIP]
    >Můžete také nahrát všechny modely v rámci adresáře ve stejnou dobu pomocí `--from-directory` Možnosti pro příkaz model Create. Další informace najdete v tématu [volitelné parametry pro *AZ DT model Create*](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create-optional-parameters).

1. Ověřte, že se modely vytvořily pomocí příkazu [**AZ DT model list**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_list) , jak je znázorněno níže. Tato akce vytiskne seznam všech modelů, které byly odeslány do instance digitálního vlákna Azure s úplnými informacemi. 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    Ve výsledcích vyhledejte upravený model *místnosti* :
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Výsledky příkazu Seznam modelů zobrazující aktualizovaný model místnosti" lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Chyby

Rozhraní příkazového řádku také zpracovává chyby ze služby. 

Opakovaným spuštěním `az dt model create` příkazu zkuste znovu nahrát jeden ze stejných modelů, které jste právě Nahráli, a to za sekundu:

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

Jelikož modely nelze přepsat, nyní se vrátí kód chyby `ModelIdAlreadyExists` .

## <a name="create-digital-twins"></a>Vytvoření digitálních dvojčat

Teď, když se některé modely nahrály do instance digitálního vlákna v Azure, můžete vytvořit [**digitální vlákna**](concepts-twins-graph.md) na základě definic modelů. Digitální vlákna reprezentují entity v rámci vašeho podnikového prostředí – například senzory ve farmě, místnosti v budově nebo světla v kleci. 

K vytvoření digitálního vlákna použijte příkaz [**AZ DT zdvojené vytvoření**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_create) . Musíte odkazovat na model, na kterém je založena, a může volitelně definovat počáteční hodnoty pro všechny vlastnosti v modelu. V této fázi není nutné předávat žádné informace o relaci.

1. Spusťte tento kód v Cloud Shell, abyste vytvořili několik vláken na základě modelu *místnosti* , který jste aktualizovali dříve, a dalšího modelu, *podlaží*. V této *místnosti* se nacházejí tři vlastnosti, takže můžete zadat argumenty s počátečními hodnotami. (Inicializace hodnot vlastností je obecně volitelná, ale jsou pro tento kurz potřeba.)

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Pokud používáte Cloud Shell v prostředí PowerShellu, může být potřeba, aby se znaky uvozovky vyhnuly tím, že se `--properties` hodnota JSON správně analyzuje. V této úpravě příkazy pro vytvoření vlákna místnosti budou vypadat takto:
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > To se projeví na snímku obrazovky níže.
    
    Výstup z každého příkazu zobrazí informace o úspěšném vytvoření vlákna (včetně vlastností pro vlákna místnosti, které s nimi byly inicializovány).

1. Pomocí příkazu [**AZ DT s dvojitým dotazem**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) , který vidíte níže, můžete ověřit, jestli se vlákna vytvořila. Zobrazený dotaz vyhledá všechny digitální vlákna v instanci digitálních vláken Azure.
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    Ve výsledcích vyhledejte vlákna *room0*, *Room1*, *floor0* a *floor1* . Tady je výpis, který ukazuje část výsledku tohoto dotazu.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Částečné výsledky dvojitých dotazů, které zobrazují room0 a Room1" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Úprava digitálního vlákna

Můžete také upravit vlastnosti vlákna, které jste vytvořili. 

1. Spuštěním příkazu [**AZ DT**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_update) *room0* Update změňte pracovní místnost z *room0* na *PresidentialSuite*:

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Pokud používáte Cloud Shell v prostředí PowerShellu, může být potřeba, aby se znaky uvozovky vyhnuly tím, že se `--json-patch` hodnota JSON správně analyzuje. V této úpravě by příkaz pro aktualizaci vlákna vypadal takto:
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > To se projeví na snímku obrazovky níže.
    
    Výstup z tohoto příkazu zobrazí aktuální informace o zdvojení a ve výsledku by se měla zobrazit nová hodnota `RoomName` .

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Výsledky příkazu Update zobrazujícího místnost PresidentialSuite" lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. Aktualizaci můžete ověřit tak, že spuštěním příkazu [**AZ DT**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_show) dodaný příkaz zobrazíte informace o *room0*:

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    Výstup by měl odpovídat aktualizovanému názvu.

## <a name="create-a-graph-by-adding-relationships"></a>Vytvoření grafu přidáním relací

V dalším kroku můžete vytvořit některé **relace** mezi těmito podmnožinami a spojit je s [**dvojitým grafem**](concepts-twins-graph.md). K reprezentaci celého prostředí se používají dvojitá grafu. 

Typy vztahů, které lze vytvořit z jednoho vlákna na jiný, jsou definovány v rámci [modelů](#model-a-physical-environment-with-dtdl) , které jste nahráli dříve. [Definice modelu pro *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) určuje, že podlaha může mít typ vztahu s názvem, který *obsahuje*. Díky tomu je možné vytvořit relaci typu *Contains* z každé *podlahové* vlákna do odpovídající místnosti, kterou obsahuje.

Chcete-li přidat relaci, použijte příkaz [**AZ DT s dvojitou relací Create**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_create) . Určete, zda vztah pochází od, typu relace a vlákna, ke kterému se vztah připojuje. Nakonec Udělte tomuto vztahu jedinečné ID. Pokud byla relace definována tak, aby měla vlastnosti, můžete v tomto příkazu inicializovat také vlastnosti relace.

1. Spusťte následující kód pro přidání vztahu *obsahujícího* typ ze všech vláken *podlah* , které jste vytvořili dříve, do odpovídající vlákna v *místnosti* . Relace mají název *relationship0* a *relationship1*.

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >Relace *Contains* v [modelu *Floor*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) byla také definována se dvěma vlastnostmi, `ownershipUser` `ownershipDepartment` takže můžete také zadat argumenty s počátečními hodnotami při vytváření relací.
    > Chcete-li vytvořit relaci s těmito vlastnostmi inicializované, přidejte `--properties` možnost do jednoho z výše uvedených příkazů, například:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Pokud používáte Cloud Shell v prostředí PowerShellu, může být potřeba, aby se znaky uvozovky vyhnuly tím, že se `--properties` hodnota JSON správně analyzuje.
    
    Výstup z každého příkazu zobrazí informace o úspěšně vytvořeném vztahu.

1. Můžete ověřit vztahy pomocí některého z následujících příkazů, které dotazují relace v instanci digitálních vláken Azure.
    * Zobrazení všech relací odcházejících z jednotlivých podlaží (Zobrazení relací z jedné strany):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * Zobrazení všech relací přicházejících v každé místnosti (zobrazení vztahu ze strany "ostatní"):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * Chcete-li tyto relace vyhledat jednotlivě, podle ID:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

Vlákna a vztahy, které jste nastavili v tomto kurzu, tvoří následující koncepční graf:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Graf zobrazující floor0 připojený prostřednictvím relationship0 k room0 a floor1 připojení prostřednictvím relationship1 k Room1" border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Dotaz na dvojitou grafickou odpověď na otázky prostředí

Hlavní funkcí digitálních vláken Azure je schopnost snadno a efektivně [dotazovat](concepts-query-language.md) se na váš graf, aby odpovídal na dotazy týkající se vašeho prostředí. V Azure CLI se to provádí pomocí příkazu [**AZ DT s dvojitým dotazem**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) .

Spusťte následující dotazy v Cloud Shell, abyste odpověděli na některé otázky týkající se ukázkového prostředí.

1. **Jaké jsou všechny entity z mého prostředí reprezentované ve službě Azure Digital revláken?** (dotaz na vše)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    To vám umožní pořídit si vaše prostředí na první pohled a zajistit, aby vše představovalo, jak byste chtěli být v rámci digitálních vláken Azure. Výsledek tohoto je výstup obsahující každou digitální dvojitou hodnotu s jeho podrobnostmi. Tady je ukázka:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Částečné výsledky dvojitých dotazů, které zobrazují room0 a Room1" lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >Můžete si všimnout, že se jedná o stejný příkaz, který jste použili v části [*vytvoření digitálních vláken*](#create-digital-twins) dříve k vyhledání všech digitálních vláken Azure v instanci.

1. **Jaké jsou všechny místnosti v mém prostředí?** (dotaz podle modelu)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    Dotaz můžete omezit na vlákna určitého typu, abyste získali konkrétnější informace o tom, co je znázorněno. Výsledek tohoto zobrazení je *room0* a *Room1*, ale nezobrazuje *floor0* nebo *floor1* (protože **se jedná o** podlahu, ne místnosti).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Výsledky dotazu modelu zobrazující pouze room0 a Room1" lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Jaké jsou všechny místnosti v *floor0*?** (dotaz podle relace)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    Dotaz na základě relací v grafu vám umožní získat informace o tom, jak jsou vlákna propojená, nebo omezit dotaz na určitou oblast. Pouze *room0* je na *floor0*, takže se jedná o jediné místo ve výsledku.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Výsledky dotazu relace, zobrazující room0" lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > Všimněte si, že ID vlákna (například *floor0* v dotazu výše) se dotazuje pomocí pole metadata `$dtId` . 
    >
    >Při použití Cloud Shell pro spuštění dotazu obsahujícího pole metadat, jako je tato, která začínají na `$` , byste měli řídicí rutinu `$` zakončit zpětným lomítkem, aby Cloud Shell věděli, že se nejedná o proměnnou a měla by být spotřebována jako literál v textu dotazu. To se projeví na snímku obrazovky výše.

1. **Jaké jsou všechny vlákna v mém prostředí s teplotou vyšší než 75?** (dotaz podle vlastnosti)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    Dotaz na graf můžete na základě vlastností využít k zodpovězení nejrůznějších otázek, mezi které patří hledání ve vašem prostředí, které může vyžadovat pozornost. Podporovány jsou i další operátory porovnání ( *<* , *>* , *=* , nebo *! =*). *Room1* se zobrazí ve výsledcích, protože má teplotu 80.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Výsledky dotazu na vlastnost zobrazující pouze Room1" lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Jaké jsou všechny místnosti v *floor0* s teplotou vyšší 75?** (složený dotaz)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    Můžete také zkombinovat předchozí dotazy jako v SQL, a to pomocí operátorů kombinace, jako je `AND` , `OR` , `NOT` . Tento dotaz používá `AND` k tomu, aby předchozí dotaz o dvojitě specifických teplotách byl konkrétnější. Výsledek teď obsahuje jenom místnosti s teplotami nad 75, které jsou v *floor0*, což v tomto případě není žádné z nich. Sada výsledků dotazu je prázdná.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Výsledky složeného dotazu, který nezobrazuje žádné výsledky" lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto kurzu můžete zvolit prostředky, které chcete odebrat, v závislosti na tom, co byste chtěli udělat dál.

* **Pokud máte v plánu pokračovat k dalšímu kurzu**, můžete zde nastavit prostředky, které jste si nastavili, a znovu použít instanci digitálního vlákna Azure, aniž byste museli něco dělat.

* **Pokud chcete dál používat instanci digitálních vláken Azure, ale vymažte všechny jeho modely, vlákna a relace**, můžete použít příkaz [**AZ DT s dvojitou relací Delete**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_delete), [**AZ DT reaz Delete**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_delete)a [**AZ DT model Delete**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_delete) pro vymazání vztahů, vláken a modelů ve vaší instanci, v uvedeném pořadí.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Možná budete chtít odstranit také soubory modelů, které jste vytvořili na místním počítači.

## <a name="next-steps"></a>Další kroky 

V tomto kurzu jste začali s digitálními podmnožinami Azure vytvořením grafu ve vaší instanci pomocí Azure CLI. Vytvořili jste modely, digitální vlákna a vztahy k vytvoření grafu. Také jste spustili některé dotazy v grafu, abyste získali představu o druzích otázek, které může Azure Digital revláken využít k zodpovězení prostředí.

Přejděte k dalšímu kurzu a slučte digitální vlákna Azure s ostatními službami Azure a dokončete tak kompletní scénář založený na datech:
> [!div class="nextstepaction"]
> [*Kurz: připojení kompletního řešení*](tutorial-end-to-end.md)