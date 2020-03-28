---
title: 'Kurz: Nasazení prostředí preview a prostorového grafu – Digitální dvojčata Azure| Dokumenty společnosti Microsoft'
description: Zjistěte, jak nasadit instanci Azure Digital Twins a nakonfigurovat prostorové prostředky pomocí kroků v tomto kurzu.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 01/10/2020
ms.openlocfilehash: 878b64fe6dd491adbb61c4c74cf4a5fc039858cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79371405"
---
# <a name="tutorial-deploy-azure-digital-twins-preview-and-configure-a-spatial-graph"></a>Kurz: Nasazení azure digitálnídvojčata Preview a konfigurace prostorový graf

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Službu Azure Digital Twins Preview můžete použít ke spojení lidí, míst a zařízení v koherentním prostorovém systému. Tato série kurzů ukazuje, jak pomocí Azure Digital Twins zjistit obsazenost místností s optimálními podmínkami teploty a kvality ovzduší. 

Tyto kurzy vás provedou konzolovou aplikací .NET a vytvoří scénář kancelářské budovy. Budova má více podlaží a pokojů v každém patře. V místnostech jsou zařízení s připojenými senzory, která detekují pohyb, teplotu okolí a kvalitu vzduchu. 

Dozvíte se, jak replikovat fyzické oblasti a entity v budově jako digitální objekty pomocí služby Azure Digital Twins. Události zařízení simulujete pomocí jiné konzolové aplikace. Pak se dozvíte, jak sledovat události, které pocházejí z těchto fyzických oblastí a entit v téměř reálném čase. 

Správce kanceláře může s využitím těchto informací pomáhat zaměstnancům pracujícím v budově s rezervací zasedacích místností s optimálními podmínkami. Správce kancelářských zařízení může použít vaše nastavení k získání trendů využití místností a ke sledování pracovních podmínek pro účely údržby.

V prvním kurzu této série získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nasaďte digitální dvojčata.
> * Udělte oprávnění vaší aplikaci.
> * Upravte ukázkovou aplikaci digitálních dvojčat.
> * Zjišďte svou budovu.

V těchto kurzech se za účelem podrobnějšího pokrytí konceptů používají a upravují stejné ukázky jako v [rychlém startu věnovaném zjištění dostupných místností](quickstart-view-occupancy-dotnet.md).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud nemáte účet Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Sada SDK jádra .NET. Ukázky Digitální dvojčata Azure použité v těchto kurzech jsou napsány v c#. Nezapomeňte nainstalovat [.NET Core SDK verze 2.1.403 nebo novější](https://www.microsoft.com/net/download) ve vývojovém počítači k sestavení a spuštění ukázky. Zkontrolujte, zda je v počítači `dotnet --version` nainstalována správná verze, spuštěním v příkazovém okně.

- [Visual Studio Code](https://code.visualstudio.com/) pro zkoumání vzorového kódu. 

## <a name="deploy-digital-twins"></a>Nasazení služby Digital Twins

Pomocí kroků v této části vytvořte novou instanci služby Azure Digital Twins. Na jedno předplatné lze vytvořit pouze jednu instanci. Pokud již máte jeden spuštěný, přejděte k další části. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="grant-permissions-to-your-app"></a>Udělení oprávnění pro aplikaci

Digitální dvojčata používají [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) k řízení [přístupu pro čtení a zápis](../active-directory/develop/v2-permissions-and-consent.md) ke službě. Každá aplikace, která potřebuje připojení k instanci Digitální dvojče, musí být registrována ve službě Azure AD. Postup v této části ukazuje, jak zaregistrovat ukázkovou aplikaci.

Pokud už máte registraci aplikace, můžete ji znovu použít jako ukázku. Projděte si však tuto část a ujistěte se, že je vaše registrace aplikace správně nakonfigurovaná.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="configure-the-digital-twins-sample"></a>Konfigurace ukázky Digitální dvojčata

Tato část vás provede aplikací Azure Digital Twins, která komunikuje s [virtuálními zařízeními REST digitálních dvojčat](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Stažení ukázky

Pokud už máte stažené ukázky pro [rychlý start věnovaný zjištění dostupných místností](quickstart-view-occupancy-dotnet.md), můžete tyto kroky přeskočit.

1. Stáhněte si [ukázky digitálních dvojčat .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip).
2. Extrahujte obsah složky zip v počítači.

### <a name="explore-the-sample"></a>Zkoumání ukázky

Ve Visual Studio Code otevřete soubor **digital-twins-samples-csharp\digital-twins-samples.code-workspace** z extrahované složky s ukázkou. Obsahuje dva projekty:

* Můžete použít zřizování ukázky **obsazení rychlý start** ke konfiguraci a zřízení grafu [prostorové inteligence](concepts-objectmodel-spatialgraph.md#digital-twins-object-models). Tento graf je digitalizovaný obraz vašich fyzických prostorů a zdrojů v nich. Používá [objektový model](concepts-objectmodel-spatialgraph.md#digital-twins-object-models), který definuje objekty pro inteligentní budovu. Úplný seznam rozhraní REST API a objektů služby Digital Twins najdete v [této dokumentaci k rozhraní REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger) nebo na adrese URL rozhraní API pro správu vytvořené pro [vaši instanci](#deploy-digital-twins).

   Chcete-li prozkoumat ukázku, abyste pochopili, jak komunikuje s instancí Digital Twins, můžete začít se složkou **src\actions.** Soubory v této složce implementují příkazy, které budete používat v těchto kurzech:
    - Soubor **provisionSample.cs** ukazuje, jak zřídit prostorový graf.
    - Soubor **getSpaces.cs** získá informace o zřízených prostorech.
    - Soubor **getAvailableAndFreshSpaces.cs** získá výsledky vlastní funkce nazývané uživatelem definovaná funkce.
    - Soubor **createEndpoints.cs** vytvoří koncové body pro interakci s jinými službami.

* Ukázková **možnost připojení zařízení** simulace simuluje data ze senzorů a odešle je do centra IoT hubu, který je zřízen pro vaši instanci Digitální dvojčata. Tuto ukázku použijete v [dalším kurzu po zřízení prostorového grafu](tutorial-facilities-udf.md#simulate-sensor-data). Identifikátory senzorů a zařízení, které používáte ke konfiguraci této ukázky, by měly být stejné jako identifikátory, které použijete k zřízení grafu.

### <a name="configure-the-provisioning-sample"></a>Konfigurace ukázky zřizování

1. Otevřete příkazové okno a přejděte ke stažené ukázce. Spusťte následující příkaz:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Spuštěním následujícího příkazu obnovte závislosti ukázkového projektu:

    ```cmd/sh
    dotnet restore
    ```

1. V kódu Visual Studia otevřete soubor [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) v projektu **možnosti rychlého startu obsazenosti.** Aktualizujte následující hodnoty:
   * **ClientId**: Zadejte ID aplikace registrace aplikace Azure AD. Toto ID jste zaznamenali v části, ve které [nastavíte oprávnění aplikací](#grant-permissions-to-your-app).
   * **Tenant**: Zadejte ID adresáře [vašeho klienta Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Toto ID jste také zaznamenali v části, ve které [nastavíte oprávnění aplikací](#grant-permissions-to-your-app).
   * **BaseUrl:** Zadejte adresu URL vaší instance služby Digital Twins. Tuto adresu URL získáte nahrazením zástupných textů v následující adrese URL za odpovídající hodnoty pro vaši instanci: `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Tuto adresu URL můžete také získat úpravou adresy URL rozhraní API pro správu v [části nasazení](#deploy-digital-twins). **Nahraďte swagger/** **api/v1.0/**.

1. Prohlédněte si seznam funkcí digitálních dvojčat, které můžete prozkoumat pomocí ukázky. Spusťte následující příkaz:

    ```cmd/sh
    dotnet run
    ```

## <a name="understand-the-provisioning-process"></a>Principy procesu zřizování

Tato část ukazuje, jak ukázka zřizuje prostorový graf budovy.

V kódu sady Visual Studio přejděte do složky **ockus_quickstart\src\actions** a otevřete soubor **provisionSample.cs**. Všimněte si následující funkce:

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

Tato funkce používá [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) ve stejné složce. Otevřete tento soubor a poznamenejte si hierarchii kancelářské budovy: *Místo*, *Podlaží*, *Plocha*a *Pokoje*. Každý z těchto fyzických prostorů může obsahovat *zařízení* a *senzory*. Každá položka má `type` &mdash;předdefinované například Floor, Room.

Ukázkový soubor **yaml** zobrazuje prostorový `Default` graf, který používá objektový model Digital Twins. Tento model poskytuje obecné názvy pro většinu typů. Obecné názvy jsou dostatečné pro budovu. Příklady jsou Teplota pro SensorDataType a Mapa pro SpaceBlobType. Ukázkovým typem mezery je Místnost s podtypy FocusRoom, ConferenceRoom a tak dále. 

Kdybyste potřebovali vytvořit prostorový graf pro jiný typ místa, například pro továrnu, pravděpodobně byste potřebovali jiný objektový model. Můžete zjistit, které modely jsou k dispozici `dotnet run GetOntologies` pro použití spuštěním příkazu na příkazovém řádku pro zřizování vzorku. 

Další informace o prostorových grafech a objektových modelech našetvete [objektových modelů digitálních dvojčat a grafu prostorové inteligence](concepts-objectmodel-spatialgraph.md).

### <a name="modify-the-sample-spatial-graph"></a>Úprava prostorového grafu vzorku

Soubor **provisionSample.yaml** obsahuje následující uzly:

- **prostředky**: `resources` Uzel vytvoří prostředek služby Azure IoT Hub pro komunikaci se zařízeními v nastavení. Centrum IoT v kořenovém uzlu grafu může komunikovat se všemi zařízeními a senzory v grafu.  

- **spaces:** V objektovém modelu služby Digital Twins představuje uzel `spaces` fyzická umístění. Každý prostor `Type` &mdash;má například region, místo&mdash;konání `Name`nebo zákazníka a přátelské . Mezery mohou patřit do jiných prostorů a vytvářet hierarchickou strukturu. ProvisionSample.yaml soubor má prostorový graf imaginární budovy. Všimněte si logického vnoření mezer typu `Floor` uvnitř `Venue`, `Area` v podlaze a `Room` uzlů v oblasti. 

- **devices:** Prostory můžou obsahovat zařízení (`devices`), což jsou fyzické nebo virtuální entity, které spravují několik senzorů. Zařízení může být například telefon uživatele, snímač Raspberry Pi nebo brána. V imaginární budově v naší ukázce si všimněte, že místnost **Focus Room** obsahuje zařízení **Raspberry Pi 3 A1**. Jednotlivé uzly zařízení jsou identifikované jedinečnou hodnotou `hardwareId` (ID hardwaru), která je v této ukázce pevně zakódovaná. Pokud chcete tuto ukázku nakonfigurovat pro skutečný provoz v produkčním prostředí, nahraďte tyto hodnoty odpovídajícími hodnotami z vašeho systému.  

- **senzory**: Zařízení může `sensors`obsahovat více . Mohou detekovat a zaznamenávat fyzické změny, jako je teplota, pohyb a úroveň baterie. Každý uzel senzoru je jednoznačně identifikovaný hodnotou `hardwareId` (ID hardwaru), která je zde pevně zakódovaná. Pro skutečné aplikace je nahraďte pomocí jedinečných identifikátorů senzorů v nastavení. Soubor provisionSample.yaml obsahuje dva senzory pro zaznamenávání pohybu (*Motion*) a úrovně oxidu uhličitého (*CarbonDioxide*). Přidejte další senzor, který bude zaznamenávat teplotu (*Temperature*), a to přidáním následujících řádků pod řádky senzoru CarbonDioxide. Ty jsou k dispozici v provisionSample.yaml jako commented-out řádky. Můžete je odkomentovat `#` odebráním znaku v přední části každého řádku. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Ujistěte `dataType` se, že klávesy a `hardwareId` jsou zarovnány s příkazy nad tímto fragmentem. Také se ujistěte, že váš editor nenahrazuje mezery tabulátorem. 

Uložte a zavřete soubor provisionSample.yaml. V dalším kurzu přidáte další informace do tohoto souboru a pak zřídíte ukázkovou sestavu Azure Digital Twins.

> [!TIP]
> Prostorový graf můžete zobrazit a upravit pomocí [prohlížeče grafů digitálních dvojčat Azure](https://github.com/Azure/azure-digital-twins-graph-viewer).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete v tomto okamžiku přestat zkoumat Azure Digital Twins, můžete odstranit prostředky vytvořené v tomto kurzu:

1. V levé nabídce na [portálu Azure](https://portal.azure.com)vyberte **Všechny prostředky**, vyberte skupinu prostředků Digitální dvojčata a vyberte **Odstranit**.

    > [!TIP]
    > Pokud došlo k potížím s odstraněním instance Digitální dvojčata, byla s opravou zavedena aktualizace služby. Opakujte odstranění instance.

1. V případě potřeby odstraňte ukázkovou aplikaci v pracovním počítači.

## <a name="next-steps"></a>Další kroky

Chcete-li se dozvědět, jak implementovat vlastní logiku pro sledování podmínek v ukázkové budově, přejděte k dalšímu kurzu v sérii: 
> [!div class="nextstepaction"]
> [Kurz: Zřízení budovy a monitorování pracovních podmínek](tutorial-facilities-udf.md)