---
title: 'Kurz: nasazení digitálních vláken Azure | Microsoft Docs'
description: Naučte se, jak nasadit vaši instanci digitálních vláken Azure a jak konfigurovat prostorové prostředky pomocí kroků v tomto kurzu.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/17/2019
ms.openlocfilehash: 54afe8ea67996562c88a2ade2ec16c4eaa89cdee
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949743"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Kurz: nasazení digitálních vláken Azure ve verzi Preview a konfigurace prostorového grafu

Službu Azure Digital Replaces ve verzi Preview můžete použít k spojování osob, míst a zařízení v souvislém prostorovém systému. V této sérii kurzů se dozvíte, jak používat digitální vlákna Azure k detekci obsazení místností s využitím optimálních podmínek teploty a kvality ovzduší. 

Tyto kurzy vás provedou pomocí konzolové aplikace .NET a vytvoří scénář budovy pro Office. Budova má v každé podlaze více podlah a místností. Místnosti obsahují zařízení s připojenými senzory, které zjišťují pohyb, okolní teplotu a kvalitu ovzduší. 

Naučíte se, jak replikovat fyzické oblasti a entity do budovy jako digitální objekty pomocí služby Azure Digital Service. Události zařízení simulujete pomocí jiné konzolové aplikace. Pak se dozvíte, jak monitorovat události, které pocházejí z těchto fyzických oblastí a entit téměř v reálném čase. 

Správce systému Office může tyto informace využít k tomu, aby zaměstnanci, kteří pracují s tímto sestavením, mohl pokrýt místnosti s optimálními podmínkami. Správce zařízení Office může pomocí vaší instalace získat trendy využití místností a monitorovat pracovní podmínky pro účely údržby.

V prvním kurzu této série se naučíte:

> [!div class="checklist"]
> * Nasaďte digitální vlákna.
> * Udělte aplikaci oprávnění.
> * Upravte ukázkovou aplikaci digitálních vláken.
> * Zřízení vaší budovy

Tyto kurzy používají a upravují stejné ukázky, které v [rychlém startu hledají dostupné místnosti](quickstart-view-occupancy-dotnet.md) , pro podrobnější a podrobné pokrytí konceptů.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- .NET Core SDK. Ukázky digitálních vláken Azure, které se používají v těchto kurzech, C#se napíší v. Nezapomeňte ve vývojovém počítači nainstalovat [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) a sestavte a spusťte ukázku. Ověřte, že je ve vašem počítači nainstalovaná správná verze spuštěním `dotnet --version` v příkazovém okně.

- [Visual Studio Code](https://code.visualstudio.com/) k prozkoumávání ukázkového kódu. 

## <a name="deploy-digital-twins"></a>Nasazení digitálních vláken

Postup v této části použijte k vytvoření nové instance služby Azure Digital revlákens. Pro každé předplatné se dá vytvořit jenom jedna instance. Pokud již máte spuštěnou službu, přejděte k další části. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Udělení oprávnění vaší aplikaci

Digitální vlákna využívají [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) k řízení [přístupu pro čtení a zápis](../active-directory/develop/v1-permissions-and-consent.md) ke službě. Všechny aplikace, které se musí připojit k instanci digitálního vlákna, musí být zaregistrované ve službě Azure AD. Postup v této části ukazuje, jak zaregistrovat ukázkovou aplikaci.

Pokud už máte registraci aplikace, můžete ji pro svou ukázku znovu použít. Projděte si ale tuto část, abyste se ujistili, že je registrace vaší aplikace správně nakonfigurovaná.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Konfigurace ukázky digitálních vláken

V této části se seznámíte s aplikací digitálních vláken Azure, která komunikuje s [rozhraními REST API pro digitální vlákna](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Stažení ukázky

Pokud už máte stažené ukázky pro rychlé zprovoznění, které vám pomůžou [Najít dostupné místnosti](quickstart-view-occupancy-dotnet.md), můžete tyto kroky přeskočit.

1. Stáhněte si [ukázky digitálních vláken .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extrahujte obsah složky zip na vašem počítači.

### <a name="explore-the-sample"></a>Prozkoumat ukázku

V části extrahovaná ukázková složka otevřete soubor **Digital-Twins-Samples-csharp\digital-Twins-Samples.Code-Workspace** v Visual Studio Code. Obsahuje dva projekty:

* K nakonfigurování a zřízení [grafu prostorové logiky](concepts-objectmodel-spatialgraph.md#digital-twins-object-models)můžete použít ukázku zřizování **– rychlý Start** . Tento graf je numerická bitová kopie vašich fyzických prostorů a prostředků v nich. Používá [objektový model](concepts-objectmodel-spatialgraph.md#digital-twins-object-models), který definuje objekty pro inteligentní sestavení. Úplný seznam digitálních objektů a rozhraní REST API najdete v [této dokumentaci REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger) nebo v adrese URL rozhraní API pro správu, která byla vytvořena pro [vaši instanci](#deploy-digital-twins).

   Pokud chcete prozkoumat ukázku a zjistit, jak komunikuje s instancí digitálního vlákna, můžete začít se složkou **src\actions** . Soubory v této složce implementují příkazy, které budete používat v těchto kurzech:
    - Soubor **provisionSample.cs** ukazuje, jak zřídit prostorový graf.
    - Soubor **getSpaces.cs** získává informace o zřízených prostorech.
    - Soubor **getAvailableAndFreshSpaces.cs** získá výsledky vlastní funkce označované jako uživatelsky definovaná funkce.
    - Soubor **createEndpoints.cs** vytvoří koncové body pro interakci s ostatními službami.

* Ukázka simulace **zařízení – připojení** simuluje data senzorů a odesílá je do služby IoT Hub zřízené pro instanci digitálního vlákna. Tuto ukázku použijete v [dalším kurzu po zřízení prostorového grafu](tutorial-facilities-udf.md#simulate-sensor-data). Identifikátory senzorů a zařízení, které použijete ke konfiguraci této ukázky, by měly být stejné jako to, co použijete ke zřízení grafu.

### <a name="configure-the-provisioning-sample"></a>Nakonfigurovat ukázku zřizování

1. Otevřete příkazové okno a pokračujte na stažený vzorek. Spusťte následující příkaz:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Spusťte tento příkaz a obnovte závislosti na vzorovém projektu:

    ```cmd/sh
    dotnet restore
    ```

1. V Visual Studio Code otevřete soubor [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) v projektu pro **rychlý Start pro obsazení** . Aktualizujte následující hodnoty:
   * **ClientID**: Zadejte ID aplikace pro registraci vaší aplikace služby Azure AD. Toto ID jste si poznamenali v části, kde jste [nastavili oprávnění aplikace](#grant-permissions-to-your-app).
   * **Tenant**: Zadejte ID adresáře vašeho [tenanta Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Toto ID jste si také poznamenali v části, kde jste [nastavili oprávnění aplikace](#grant-permissions-to-your-app).
   * **Baseurl**: zadejte adresu URL instance digitálního vlákna. Chcete-li získat tuto adresu URL, nahraďte zástupné symboly v této adrese URL hodnotami vaší instance: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Tuto adresu URL můžete získat také změnou adresy URL rozhraní API pro správu v [části nasazení](#deploy-digital-twins). Nahraďte **Swagger/** **rozhraním API/v 1.0/** .

1. Podívejte se na seznam digitálních vlastností, které můžete prozkoumat pomocí ukázky. Spusťte následující příkaz:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Pochopení procesu zřizování

V této části se dozvíte, jak ukázka zřídí prostorový graf budovy.

V Visual Studio Code přejděte do složky **Occupancy-quickstart\src\actions** a otevřete soubor **provisionSample.cs**. Poznamenejte si následující funkci:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Tato funkce používá [provisionSample. yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) ve stejné složce. Otevřete tento soubor a podívejte se na hierarchii kancelář budovy: *místo*, *podlahová*plocha, *oblast*a *místnosti*. Každý z těchto fyzických prostorů může obsahovat *zařízení* a *senzory*. Každá položka má předdefinovanou `type` @ no__t-1for, například Floor, pokoj.

Vzorový soubor **YAML** zobrazuje prostorový graf, který používá model objektu digitálního vláken `Default`. Tento model poskytuje obecné názvy pro většinu typů. Obecné názvy jsou pro budova dostatečné. Příklady jsou teploty pro SensorDataType a mapují pro SpaceBlobType. Vzorový typ místa je místnost s podtypy FocusRoom, ConferenceRoom a tak dále. 

Pokud jste museli vytvořit prostorový graf pro jiný typ místa, například objekt pro vytváření, budete možná potřebovat jiný objektový model. Modely, které je možné použít, zjistíte spuštěním příkazu `dotnet run GetOntologies` na příkazovém řádku pro ukázku zřizování. 

Další informace o prostorových grafech a objektových modelech najdete v tématu [Principy digitálních objektů a grafů prostorové logiky](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Úprava ukázkového prostorového grafu

Soubor **provisionSample. yaml** obsahuje následující uzly:

- **prostředky**: uzel `resources` vytvoří prostředek Azure IoT Hub ke komunikaci se zařízeními v instalaci. Centrum IoT v kořenovém uzlu grafu může komunikovat se všemi zařízeními a senzory v grafu.  

- **Spaces**: v objektovém modelu digitálního vlákna `spaces` představuje fyzické umístění. Každé místo má `Type` @ no__t-1for příklad, oblast, místo nebo zákazník @ no__t-2and a uživatelsky přívětivou `Name`. Mezery můžou patřit do jiných mezer a vytvořit hierarchickou strukturu. Soubor provisionSample. yaml má prostorový graf imaginární budovy. Všimněte si logických vnoření mezer typu `Floor` v rámci `Venue` `Area` v podlaze a uzlech `Room` v oblasti. 

- **zařízení**: prostory můžou obsahovat `devices`, což jsou fyzické nebo virtuální entity, které spravují řadu senzorů. Zařízení může být například telefon uživatele, jeho senzory maliny nebo brána. V imaginárním sestavení v ukázce si všimněte, jak místnost s názvem **Ohnisková místnost** obsahuje zařízení **malin 3 a1** . Každý uzel zařízení je identifikovaný jedinečným `hardwareId`, což je pevně zakódované v ukázce. Pokud chcete tuto ukázku nakonfigurovat pro skutečnou produkci, nahraďte je hodnotami z vašeho nastavení.  

- **senzory**: zařízení může obsahovat více `sensors`. Můžou detekovat a nahrávat fyzické změny, jako je teplota, pohyb a úroveň baterie. Každý uzel senzoru jednoznačně identifikuje `hardwareId`, pevně zakódované. V případě vlastní aplikace je nahraďte pomocí jedinečných identifikátorů senzorů v nastavení. Soubor provisionSample. yaml má dva senzory k záznamu *pohybu* a *CarbonDioxide*. Přidáním následujících řádků pod řádky pro senzor CarbonDioxide přidejte další senzor pro záznam *teploty*. Všimněte si, že tyto jsou k dispozici v provisionSample. yaml jako řádky s komentářem. Můžete je odkomentovat odebráním znaku `#` na začátku každého řádku. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Zajistěte, aby byly klíče `dataType` a `hardwareId` zarovnané s příkazy nad tento fragment kódu. Také se ujistěte, že editor nenahrazuje mezery pomocí karet. 

Uložte a zavřete soubor provisionSample. yaml. V dalším kurzu přidáte do tohoto souboru Další informace a potom zřídíte ukázkové sestavení digitálních vláken Azure.

> [!TIP]
> Prostorový graf můžete zobrazit a upravit pomocí [prohlížeče grafu digitálních vláken Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete přestat zkoumat digitální vlákna Azure v tomto okamžiku, můžete odstranit prostředky vytvořené v tomto kurzu:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**, vyberte skupinu prostředků vaše digitální vlákna a vyberte **Odstranit**.

    > [!TIP]
    > Pokud jste narazili na potíže s odstraněním instance digitálního vlákna, aktualizovala se aktualizace služby s touto opravou. Zkuste prosím instanci znovu odstranit.

1. V případě potřeby odstraňte ukázkovou aplikaci v pracovním počítači.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak implementovat vlastní logiku pro monitorování podmínek v sestavování vzorů, přečtěte si další kurz v řadě: 
> [!div class="nextstepaction"]
> [Kurz: zajištění pracovních podmínek pro sestavování a sledování](tutorial-facilities-udf.md)