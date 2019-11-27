---
title: 'Kurz: nasazení prostředí Preview a prostorového grafu – digitální vlákna Azure | Microsoft Docs'
description: Zjistěte, jak nasadit instanci služby Azure digitální dvojče a nakonfigurovat prostředky prostorových pomocí kroků v tomto kurzu.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/12/2019
ms.openlocfilehash: 20174a4eafb4e72fb62eeff6df2d129b91016b9e
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383029"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Kurz: nasazení digitálních vláken Azure ve verzi Preview a konfigurace prostorového grafu

Službu Azure Digital Replaces ve verzi Preview můžete použít k spojování osob, míst a zařízení v souvislém prostorovém systému. Tato série kurzů ukazuje, jak použít k detekci obsazení místa s optimálních podmínek jakosti teplotu a air digitální dvojče Azure. 

Tyto kurzy vás provedou konzolovou aplikaci .NET pro vytváření scénáři kancelářskou budovu. Sestavování má více podlahy a místnosti v rámci každý floor. Místnostech obsahovat zařízení připojených senzorů, které detekce pohybu, okolní teploty a vzduchu kvality. 

Se dozvíte, jak pomocí služby Azure digitální dvojče replikovat fyzické oblasti a entity v budově jako digitální objekty. Události zařízení budete simulovat pomocí jiného konzolové aplikace. Poté se dozvíte, jak monitorovat události, které pocházejí z těchto fyzických oblasti a entity téměř v reálném čase. 

Správce kanceláře může s využitím těchto informací pomáhat zaměstnancům pracujícím v budově s rezervací zasedacích místností s optimálními podmínkami. Správce zařízení sady office můžete použít nastavení trendy využití místností a sledovat práci podmínky pro účely údržby.

V prvním kurzu této série získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nasazení digitální dvojče.
> * Udělení oprávnění pro vaši aplikaci.
> * Upravte digitální dvojče ukázkovou aplikaci.
> * Zřízení vašeho sestavení.

V těchto kurzech se za účelem podrobnějšího pokrytí konceptů používají a upravují stejné ukázky jako v [rychlém startu věnovaném zjištění dostupných místností](quickstart-view-occupancy-dotnet.md).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- .NET Core SDK. Ukázky Azure digitální dvojče použité v těchto kurzech jsou napsané v C#. Nezapomeňte ve vývojovém počítači nainstalovat [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) a sestavte a spusťte ukázku. Ověřte, že je ve vašem počítači nainstalovaná správná verze spuštěním `dotnet --version` v příkazovém okně.

- [Visual Studio Code](https://code.visualstudio.com/) pro zkoumání vzorového kódu. 

## <a name="deploy-digital-twins"></a>Nasazení služby Digital Twins

Chcete-li vytvořit novou instanci služby Azure digitální dvojče postupujte podle kroků v této části. Je možné vytvořit pouze jednu instanci na jedno předplatné. Pokud už máte jedno spuštěné, přejděte k další části. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Udělení oprávnění pro aplikaci

Digitální vlákna využívají [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) k řízení [přístupu pro čtení a zápis](../active-directory/develop/v1-permissions-and-consent.md) ke službě. Všechny aplikace, které potřebuje pro připojení k vaší instanci digitální dvojče musí být zaregistrovaná s Azure AD. Postup v této části ukazuje, jak zaregistrovat ukázkovou aplikaci.

Pokud už máte registrace aplikace, můžete znovu použít pro vaši ukázku. Projděte si však tuto část a ujistěte se, že je vaše registrace aplikace správně nakonfigurovaná.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Konfigurace digitální dvojče ukázky

V této části se seznámíte s aplikací digitálních vláken Azure, která komunikuje s [rozhraními REST API pro digitální vlákna](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Stažení ukázky

Pokud už máte stažené ukázky pro [rychlý start věnovaný zjištění dostupných místností](quickstart-view-occupancy-dotnet.md), můžete tyto kroky přeskočit.

1. Stáhněte si [ukázky digitálních vláken .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extrahujte obsah složky zip na svém počítači.

### <a name="explore-the-sample"></a>Zkoumání ukázky

V části extrahovaná ukázková složka otevřete soubor **Digital-Twins-Samples-csharp\digital-Twins-Samples.Code-Workspace** v Visual Studio Code. Obsahuje dva projekty:

* K nakonfigurování a zřízení [grafu prostorové logiky](concepts-objectmodel-spatialgraph.md#digital-twins-object-models)můžete použít ukázku zřizování **– rychlý Start** . Tento graf je digitalizovaná obrázek prostory vaší fyzické a prostředky v nich. Používá [objektový model](concepts-objectmodel-spatialgraph.md#digital-twins-object-models), který definuje objekty pro inteligentní sestavení. Úplný seznam digitálních objektů a rozhraní REST API najdete v [této dokumentaci REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger) nebo v adrese URL rozhraní API pro správu, která byla vytvořena pro [vaši instanci](#deploy-digital-twins).

   Pokud chcete prozkoumat ukázku a zjistit, jak komunikuje s instancí digitálního vlákna, můžete začít se složkou **src\actions** . Soubory v této složce implementovat příkazy, které použijete v následujících kurzech:
    - Soubor **provisionSample.cs** ukazuje, jak zřídit prostorový graf.
    - Soubor **getSpaces.cs** získává informace o zřízených prostorech.
    - Soubor **getAvailableAndFreshSpaces.cs** získá výsledky vlastní funkce označované jako uživatelsky definovaná funkce.
    - Soubor **createEndpoints.cs** vytvoří koncové body pro interakci s ostatními službami.

* Ukázka simulace **zařízení – připojení** simuluje data senzorů a odesílá je do služby IoT Hub zřízené pro instanci digitálního vlákna. Tuto ukázku použijete v [dalším kurzu po zřízení prostorového grafu](tutorial-facilities-udf.md#simulate-sensor-data). Identifikátory ze senzorů a zařízení, které můžete použít ke konfiguraci této ukázce by měl být stejný jako budete používat ke zřízení grafu.

### <a name="configure-the-provisioning-sample"></a>Konfigurace ukázky zřizování

1. Otevřete okno příkazového řádku a přejděte na staženou ukázku. Spusťte následující příkaz:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Spuštěním následujícího příkazu obnovte závislosti ukázkového projektu:

    ```cmd/sh
    dotnet restore
    ```

1. V Visual Studio Code otevřete soubor [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) v projektu pro **rychlý Start pro obsazení** . Aktualizací následujících hodnot:
   * **ClientID**: Zadejte ID aplikace pro registraci vaší aplikace služby Azure AD. Toto ID jste si poznamenali v části, kde jste [nastavili oprávnění aplikace](#grant-permissions-to-your-app).
   * **Tenant**: Zadejte ID adresáře vašeho [tenanta Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Toto ID jste si také poznamenali v části, kde jste [nastavili oprávnění aplikace](#grant-permissions-to-your-app).
   * **BaseUrl:** Zadejte adresu URL vaší instance služby Digital Twins. Chcete-li získat tuto adresu URL, nahraďte zástupné symboly v této adrese URL hodnotami pro vaši instanci: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Tuto adresu URL můžete získat také změnou adresy URL rozhraní API pro správu v [části nasazení](#deploy-digital-twins). Nahraďte **Swagger/** **rozhraním API/v 1.0/** .

1. Podívejte se do seznamu digitální dvojče funkce, které můžete prozkoumat pomocí ukázky. Spusťte následující příkaz:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Vysvětlení procesu zřizování

Tato část ukazuje, jak ukázka zřizuje prostorový graf budovy.

V Visual Studio Code přejděte do složky **Occupancy-quickstart\src\actions** a otevřete soubor **provisionSample.cs**. Všimněte si následující funkce:

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

Tato funkce používá [provisionSample. yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) ve stejné složce. Otevřete tento soubor a podívejte se na hierarchii kancelář budovy: *místo*, *podlahová*plocha, *oblast*a *místnosti*. Každý z těchto fyzických prostorů může obsahovat *zařízení* a *senzory*. Každá položka má předdefinovanou `type`&mdash;například podlahová, pokojová.

Vzorový soubor **YAML** zobrazuje prostorový graf, který používá model objektu `Default` Digital revlákened. Tento model poskytuje obecné názvy pro většinu typů. Obecné názvy jsou dostačující pro budovy. Příklady jsou teploty SensorDataType a mapování pro SpaceBlobType. Typ místa příklad je místnosti s podtypy FocusRoom ConferenceRoom a tak dále. 

Kdybyste potřebovali vytvořit prostorový graf pro jiný typ místa, například pro továrnu, pravděpodobně byste potřebovali jiný objektový model. Modely, které je možné použít, zjistíte spuštěním příkazu `dotnet run GetOntologies` na příkazovém řádku pro ukázku zřizování. 

Další informace o prostorových grafech a objektových modelech najdete v tématu [Principy digitálních objektů a grafů prostorové logiky](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Upravit ukázkové prostorových grafu

Soubor **provisionSample. yaml** obsahuje následující uzly:

- **prostředky**: uzel `resources` vytvoří prostředek Azure IoT Hub ke komunikaci se zařízeními v instalaci. Služby IoT hub v kořenovém uzlu grafu může komunikovat s všech zařízení a senzorů v grafu.  

- **spaces:** V objektovém modelu služby Digital Twins představuje uzel `spaces` fyzická umístění. Každé místo má `Type`&mdash;například, oblast, místo nebo&mdash;zákazníka a uživatelsky přívětivé `Name`. Mezery může patřit do jiné prostory vytváření hierarchickou strukturu. Soubor provisionSample.yaml obsahuje prostorový graf imaginární budovy. Všimněte si logických vnořených mezer typu `Floor` v rámci `Venue`, `Area` v podlaze a `Room` uzlech v oblasti. 

- **devices:** Prostory můžou obsahovat zařízení (`devices`), což jsou fyzické nebo virtuální entity, které spravují několik senzorů. Zařízení může být například telefonu uživatele, pod senzor Raspberry Pi nebo brány. V imaginární budově v naší ukázce si všimněte, že místnost **Focus Room** obsahuje zařízení **Raspberry Pi 3 A1**. Jednotlivé uzly zařízení jsou identifikované jedinečnou hodnotou `hardwareId` (ID hardwaru), která je v této ukázce pevně zakódovaná. Pokud chcete tuto ukázku nakonfigurovat pro skutečný provoz v produkčním prostředí, nahraďte tyto hodnoty odpovídajícími hodnotami z vašeho systému.  

- **senzory**: zařízení může obsahovat více `sensors`. Dokáže detekovat a teploty, pohybu a stav baterie, jako jsou fyzické změny záznamu. Každý uzel senzoru je jednoznačně identifikovaný hodnotou `hardwareId` (ID hardwaru), která je zde pevně zakódovaná. Pro aplikace skutečný nahraďte tyto pomocí jedinečných identifikátorů pro čidel v nastavení aplikace. Soubor provisionSample. yaml má dva senzory k záznamu *pohybu* a *CarbonDioxide*. Přidejte další senzor, který bude zaznamenávat teplotu (*Temperature*), a to přidáním následujících řádků pod řádky senzoru CarbonDioxide. Všimněte si, že tyto jsou k dispozici v provisionSample.yaml jako komentovaná řádky. Můžete je odkomentovat odebráním znaku `#` před každým řádkem. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Zajistěte, aby byly klíče `dataType` a `hardwareId` zarovnané s příkazy nad tento fragment kódu. Také se ujistěte, že váš editor nenahrazuje mezery tabulátorem. 

Uložte a zavřete soubor provisionSample.yaml. V dalším kurzu budete do tohoto souboru přidejte další informace a pak zřízení budovy ukázka digitální dvojče Azure.

> [!TIP]
> Prostorový graf můžete zobrazit a upravit pomocí [prohlížeče grafu digitálních vláken Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zastavit v tuto chvíli seznámení digitální dvojče Azure, bez obav odstraňte prostředky vytvořené v tomto kurzu:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**, vyberte skupinu prostředků vaše digitální vlákna a vyberte **Odstranit**.

    > [!TIP]
    > Pokud zaznamenal/zaznamenala jste potíže odstraníte instanci digitální dvojče, aktualizace služby se týká jenom s opravou. Zkuste to prosím znovu odstraníte instanci.

1. V případě potřeby odstraňte ukázkovou aplikaci na svém počítači práce.

## <a name="next-steps"></a>Další kroky

Chcete-li zjistěte, jak implementovat vlastní logiku monitorování podmínek ve vaší ukázce sestavování, přejděte na další kurz v této sérii: 
> [!div class="nextstepaction"]
> [Kurz: Zřízení budovy a monitorování pracovních podmínek](tutorial-facilities-udf.md)