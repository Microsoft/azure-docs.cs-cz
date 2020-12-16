---
title: 'Kurz: implementace prostorové analýzy IoT | Mapy Microsoft Azure'
description: Kurz týkající se integrace IoT Hub s rozhraními API služby Microsoft Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d475c76d338270cb9b8e34ea8563cdfd8fdf5122
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563054"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Kurz: implementace prostorových analýz IoT pomocí Azure Maps

Ve scénáři IoT je běžné zaznamenávat a sledovat relevantní události, ke kterým dochází v prostoru a čase. Mezi příklady patří Správa loďstva, sledování prostředků, mobilita a aplikace pro inteligentní město. Tento kurz vás provede řešením, které sleduje využitý pohyb auta pomocí Azure Maps rozhraní API.

V tomto kurzu provedete tyto kroky:

> [!div class="checklist"]
> * Vytvořte účet úložiště Azure pro protokolování dat sledování auta.
> * Nahrajte geografickou ochranu do služby Azure Maps data Service (Preview) pomocí rozhraní API pro nahrání dat.
> * Vytvořte centrum v Azure IoT Hub a zaregistrujte zařízení.
> * Vytvořte funkci v Azure Functions, implementujte obchodní logiku na základě Azure Maps prostorových analýz.
> * Přihlaste se k odběru událostí telemetrie zařízení IoT pomocí služby Azure Functions prostřednictvím Azure Event Grid.
> * Vyfiltrujte události telemetrie pomocí IoT Hub směrování zpráv.

## <a name="prerequisites"></a>Předpoklady

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. [Vytvořte účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného. Další informace najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

4. [Vytvořte skupinu prostředků](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). V tomto kurzu pojmenujte naši skupinu prostředků *ContosoRental*, ale můžete si vybrat libovolný název.

5. Stáhněte [projekt RentalCarSimulation C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

V tomto kurzu se používá aplikace [po](https://www.postman.com/) aplikaci, ale můžete zvolit jiné vývojové prostředí API.

## <a name="use-case-rental-car-tracking"></a>Případ použití: sledování auta pronájmu

Řekněme, že společnost pro nájem auta chce do protokolu zaprotokolovat informace o poloze, ujeté vzdálenosti a stavu spuštění pro své půjčovny automobilů. Společnost také chce tyto informace uložit vždy, když automobil opustí správnou geografickou oblast.

Půjčovna vozidel jsou vybavená zařízeními IoT, která pravidelně odesílají data telemetrie IoT Hub. Telemetrie zahrnuje aktuální umístění a označuje, jestli je modul auta spuštěný. Schéma umístění zařízení dodržuje [schéma IoT technologie Plug and Play pro geoprostorové údaje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Schéma telemetrie zařízení automobilového auta vypadá jako v následujícím kódu JSON:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

V tomto kurzu sledujete jenom jedno vozidlo. Po nastavení služeb Azure budete muset stáhnout [projekt RentalCarSimulation C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) , aby se spustil simulátor vozidla. Celý proces, od události až po spuštění funkce, je shrnutý v následujících krocích:

1. Zařízení v rámci vozidla odesílá data telemetrie IoT Hub.

2. Pokud je modul auta spuštěný, centrum publikuje data telemetrie a Event Grid.

3. Aktivuje se funkce Azure Function z důvodu jejich odběru událostí telemetrie zařízení.

4. Funkce zaznamená souřadnice umístění zařízení vozidlo, čas události a ID zařízení. Pak použije [rozhraní API prostorového geografického zobrazení](/rest/api/maps/spatial/getgeofence) k určení, jestli je automobil řízený mimo geografickou oblast. Pokud se provedlo cestování mimo hranice geografické oblasti, funkce uloží data umístění přijatá z události do kontejneru objektů BLOB. Funkce také dotazuje [adresu hledání zpět](/rest/api/maps/search/getsearchaddressreverse) , aby přeložila umístění souřadnic na ulici a ukládá je do zbytku dat o umístění zařízení.

Následující diagram znázorňuje přehled systému na nejvyšší úrovni.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagram přehledu systému":::

Následující obrázek zvýrazňuje oblast geografického rozkladu modře. Trasa k půjčovně aut je označena zelenou čárou.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Obrázek ukazující trasu geografického uspořádání.":::

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

Pokud chcete uložit data sledování narušení auta, vytvořte ve skupině prostředků [účet úložiště pro obecné účely v2](../storage/common/storage-account-overview.md#general-purpose-v2-accounts) . Pokud jste ještě nevytvořili skupinu prostředků, postupujte podle pokynů v části [Vytvoření skupiny prostředků](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). V tomto kurzu Pojmenujte skupinu prostředků *ContosoRental*.

Pokud chcete vytvořit účet úložiště, postupujte podle pokynů v části [Vytvoření účtu úložiště](../storage/common/storage-account-create.md?tabs=azure-portal). V tomto kurzu pojmenujte účet úložiště *contosorentalstorage*, ale obecně ho můžete pojmenovat sami.

Po úspěšném vytvoření účtu úložiště je nutné vytvořit kontejner pro uložení dat protokolování.

1. Přejít na nově vytvořený účet úložiště. V části **základy** vyberte odkaz **kontejnery** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Snímek obrazovky s kontejnery pro úložiště objektů BLOB":::

2. V levém horním rohu vyberte **+ kontejner**. Panel se zobrazí na pravé straně prohlížeče. Pojmenujte kontejner *Contoso-pronájem-logs* a vyberte **vytvořit**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Snímek obrazovky s vytvořením kontejneru objektů BLOB":::

3. Přejděte do podokna **přístupové klíče** v účtu úložiště a zkopírujte **název účtu úložiště** a hodnotu **klíče** do oddílu **klíč1** . Obě tyto hodnoty budete potřebovat v části Vytvoření funkce Azure a přidání předplatného Event Grid.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Snímek obrazovky s názvem a klíčem pro kopírování účtu úložiště":::

## <a name="upload-a-geofence"></a>Nahrát geografickou ochranu

V dalším kroku [odešlete geografickou ochranou](./geofence-geojson.md) do Azure Mapsu [aplikaci po](https://www.getpostman.com) odeslání. Geografická oblast definuje oprávněnou geografickou oblast pro naše vozidlo pronájmu. K určení, jestli se automobil přesunul mimo oblast geografické oblasti, budete používat geografickou oblast ve službě Azure Function.

Pomocí následujícího postupu nahrajte geografickou ochranou Azure Maps rozhraní API pro nahrání dat: 

1. Otevřete aplikaci pro vyúčtování a vyberte **Nová**. V okně **vytvořit nové** vyberte **kolekce**. Pojmenujte kolekci a vyberte **vytvořit**.

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek** a zadejte název žádosti. Vyberte kolekci, kterou jste vytvořili v předchozím kroku, a pak vyberte **Uložit**.

3. Na kartě tvůrce vyberte metodu **post** http a zadejte následující adresu URL pro nahrání geografického ohraničení do rozhraní API pro nahrání dat. Nezapomeňte nahradit `{subscription-key}` primární klíč předplatného.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    V cestě URL `geojson` hodnota s `dataFormat` parametrem představuje formát dat odesílaných.

4. Jako formát vstupu vyberte **tělo**  >  **raw** a v rozevíracím seznamu zvolte **JSON** . [Otevřete datový soubor JSON](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)a zkopírujte kód JSON do části text. Vyberte **Odeslat**.

5. Vyberte **Odeslat** a počkejte na zpracování žádosti. Po dokončení žádosti přejít na kartu **hlavičky** odpovědi. Zkopírujte hodnotu klíče **umístění** , což je `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Chcete-li zjistit stav volání rozhraní API, vytvořte požadavek **Get** http na `status URL` . K adrese URL pro ověření budete muset připojit primární klíč předplatného. Požadavek **Get** by měl vypadat jako následující adresa URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. Po úspěšném dokončení požadavku **Get** http vrátí `resourceLocation` . `resourceLocation`Obsahuje jedinečný `udid` pro nahraný obsah. Tento `udid` kurz si můžete zkopírovat pro pozdější použití v tomto kurzu.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

IoT Hub umožňuje zabezpečenou a spolehlivou obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. Pro účely tohoto kurzu chcete získat informace ze zařízení v rámci vozidla, abyste zjistili umístění půjčovny. V této části vytvoříte centrum IoT v rámci skupiny prostředků *ContosoRental* . Tento rozbočovač bude zodpovědný za publikování událostí telemetrie zařízení.

> [!NOTE]
> Možnost publikovat události telemetrie zařízení v Event Grid je momentálně ve verzi Preview. Tato funkce je dostupná ve všech oblastech kromě následujících: Východní USA, Západní USA, Západní Evropa, Azure Government, Azure Čína 21Vianet a Azure Německo.

Pokud chcete ve skupině prostředků *ContosoRental* vytvořit centrum IoT, postupujte podle kroků v části [Vytvoření služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-your-iot-hub"></a>Registrace zařízení ve službě IoT Hub

Zařízení se nemůžou připojit ke službě IoT Hub, pokud se nezaregistrují v registru identit centra IoT. Tady vytvoříte jedno zařízení s názvem, *InVehicleDevice*. Pokud chcete zařízení vytvořit a zaregistrovat v rámci služby IoT Hub, postupujte podle kroků v části [Registrace nového zařízení ve službě IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Nezapomeňte zkopírovat primární připojovací řetězec vašeho zařízení. Budete ho potřebovat později.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Vytvoření funkce a přidání předplatného Event Grid

Azure Functions je výpočetní služba bez serveru, která umožňuje spustit malé části kódu ("funkce") bez nutnosti explicitně zřizovat nebo spravovat výpočetní infrastrukturu. Další informace najdete v tématu [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

Funkci spustí určitá událost. Tady vytvoříte funkci, která se aktivuje triggerem Event Grid. Vytvořením odběru událostí pro IoT Hub události telemetrie zařízení vytvořte vztah mezi triggerem a funkcí. Když dojde k události telemetrie zařízení, je funkce volána jako koncový bod a obdrží relevantní data pro zařízení, které jste předtím zaregistrovali v IoT Hub.

Zde je [kód skriptu jazyka C#, který bude obsahovat vaše funkce](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Teď nastavte funkci Azure Functions.

1. Na řídicím panelu Azure Portal vyberte **vytvořit prostředek**. Do textového pole hledání zadejte **Function App** . Vyberte **Function App**  >  **vytvořit**.

1. Na stránce vytváření **Function App** název své aplikace Function App. V části **Skupina prostředků** v rozevíracím seznamu vyberte **ContosoRental** . Jako **zásobník modulu runtime** vyberte **.NET Core** . V dolní části stránky vyberte **Další: hostování >**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Snímek obrazovky s vytvořením aplikace Function App":::

1. V poli **účet úložiště** vyberte účet úložiště, který jste vytvořili v části [Vytvoření účtu služby Azure Storage](#create-an-azure-storage-account). Vyberte **Zkontrolovat a vytvořit**.

1. Zkontrolujte podrobnosti aplikace Function App a vyberte **vytvořit**.

1. Po vytvoření aplikace se do ní přidá funkce. Přejít do aplikace Function App. Vyberte podokno **funkce** . V horní části stránky vyberte **+ Přidat**. Zobrazí se panel šablony funkce. Posuňte se dolů na panel a vyberte **Azure Event Grid Trigger**.

     >[!IMPORTANT]
    > **Aktivační událost centra událostí Azure** a šablony **triggeru Azure Event Grid** mají podobné názvy. Ujistěte se, že jste vybrali šablonu **triggeru Azure Event Grid** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Snímek obrazovky s vytvořením funkce":::

1. Zadejte název funkce. V tomto kurzu použijete název *GetGeoFunction*, ale obecně můžete použít libovolný název, který chcete. Vyberte **vytvořit funkci**.

1. V nabídce vlevo vyberte podokno **Code + test** . Zkopírujte a vložte [skript jazyka C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) do okna Code (kód).

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopírovat/snímek obrazovky vložení kódu do okna funkce":::

1. V kódu jazyka C# nahraďte následující parametry:
    * Nahraďte **SUBSCRIPTION_KEY** klíčem primárního předplatného pro Azure Maps účtu.
    * Nahraďte **UDIDi** `udid` geografickou ochranou, kterou jste nahráli v rámci [nahrávání geografického](#upload-a-geofence)plotu.
    * `CreateBlobAsync`Funkce ve skriptu vytvoří objekt blob na událost v účtu úložiště dat. Pomocí přístupového klíče účtu úložiště, názvu účtu a kontejneru úložiště dat nahraďte **ACCESS_KEY**, **ACCOUNT_NAME** a **STORAGE_CONTAINER_NAME** . Tyto hodnoty se vygenerovaly při vytvoření účtu úložiště v [Vytvoření účtu úložiště Azure](#create-an-azure-storage-account).

1. V nabídce vlevo vyberte podokno **integrace** . V diagramu vyberte **aktivační událost Event Grid** . Zadejte název triggeru *eventGridEvent* a vyberte **vytvořit Event Grid předplatné**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Snímek obrazovky s přidáním odběru událostí":::

1. Vyplňte podrobnosti předplatného. Pojmenujte odběr události. V případě **schématu událostí** vyberte **Event Grid schéma**. V **části typy témat** vyberte **účty Azure IoT Hub**. V části **Skupina prostředků** vyberte skupinu prostředků, kterou jste vytvořili na začátku tohoto kurzu. V části **prostředek** vyberte Centrum IoT, které jste vytvořili v tématu vytvoření centra IoT Azure. Pro možnost **Filtr na typy událostí** vyberte **telemetrie zařízení**.

   Po zvolení těchto možností uvidíte změnu **typu tématu** **IoT Hub**. V **části název systémového tématu** můžete použít stejný název jako prostředek. Nakonec v části **Podrobnosti o koncovém bodu** vyberte **možnost vybrat koncový bod**. Přijměte všechna nastavení a vyberte **potvrdit výběr**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Snímek obrazovky pro vytvoření odběru události":::

1. Zkontrolujte nastavení. Ujistěte se, že koncový bod Určuje funkci, kterou jste vytvořili na začátku této části. Vyberte **Vytvořit**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Snímek obrazovky s potvrzením vytvoření odběru události":::

1. Teď jste zpátky na panel **Upravit aktivační událost** . Vyberte **Uložit**.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Filtrování událostí pomocí IoT Hubho směrování zpráv

Když do funkce Azure Function přidáte předplatné Event Grid, v zadaném centru IoT Hub se automaticky vytvoří trasa pro zasílání zpráv. Směrování zpráv umožňuje směrovat různé datové typy do různých koncových bodů. Můžete například směrovat zprávy telemetrie zařízení, události životního cyklu zařízení a události změny zařízení s dvojitou změnou. Další informace najdete v tématu [použití IoT Hub směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Snímek obrazovky s směrováním zpráv ve IoT Hub.":::

V ukázkovém scénáři budete chtít dostávat jenom zprávy, když se pronájem auta přesune. Vytvořte dotaz směrování pro filtrování událostí, ve kterých se `Engine` vlastnost rovná **"on"**. Pokud chcete vytvořit dotaz směrování, vyberte trasu **RouteToEventGrid** a nahraďte **dotaz směrování** pomocí **"Engine =" na**". Pak vyberte **Uložit**. Centrum IoT teď publikuje jenom telemetrii zařízení, ve které je modul zapnutý.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Snímek obrazovky se zprávami o směrování filtru.":::

>[!TIP]
>Existují různé způsoby, jak zadávat dotazy na zprávy ze zařízení do cloudu IoT. Další informace o syntaxi směrování zpráv najdete v tématu [IoT Hub směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Odeslat data telemetrie do IoT Hub

Když je vaše funkce Azure spuštěná, můžete teď do služby IoT Hub odesílat data telemetrie, která je směrují do Event Grid. Použijte aplikaci v jazyce C# k simulaci dat o poloze pro zařízení v rámci vozidla pro půjčovnu vozidel. Ke spuštění aplikace potřebujete .NET Core SDK 2.1.0 nebo novější ve vývojovém počítači. Pomocí těchto kroků odešlete Simulovaná data telemetrie do centra IoT:

1. Pokud jste to ještě neudělali, Stáhněte projekt [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#.

2. Otevřete `simulatedCar.cs` soubor v textovém editoru podle vašeho výběru a nahraďte hodnotu hodnotou, `connectionString` kterou jste uložili při registraci zařízení. Uložte změny do souboru.

3. Ujistěte se, že máte na svém počítači nainstalovanou platformu .NET Core. V místním okně terminálu přejděte do kořenové složky projektu C# a spuštěním následujícího příkazu nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

4. Ve stejném terminálu spusťte následující příkaz, který sestaví a spustí aplikaci simulace půjčovny aut:

    ```cmd/sh
    dotnet run
    ```


  Místní terminál by měl vypadat jako na následujícím obrázku.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Snímek obrazovky s výstupem terminálu":::

Pokud teď kontejner úložiště objektů BLOB otevřete, uvidíte čtyři objekty blob pro umístění, kde bylo vozidlo mimo geografickou oblast.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Snímek obrazovky zobrazení objektů BLOB v kontejneru":::

Následující mapa znázorňuje čtyři body umístění vozidel mimo geografickou oblast. Každé umístění bylo zaznamenáno v pravidelných časových intervalech.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Snímek obrazovky s mapou porušení":::

## <a name="explore-azure-maps-and-iot"></a>Prozkoumejte Azure Maps a IoT

Pokud chcete prozkoumat rozhraní API Azure Maps použitá v tomto kurzu, přečtěte si:

* [Získat reverzní adresu pro hledání](/rest/api/maps/search/getsearchaddressreverse)
* [Získat geografickou ochranu](/rest/api/maps/spatial/getgeofence)

Úplný seznam Azure Maps rozhraní REST API najdete v tématu:

* [Rozhraní REST API pro Azure Maps](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

Seznam zařízení, která jsou v Azure Certified for IoT, získáte na webu:

* [Zařízení s certifikací Azure](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak odeslat telemetrii ze zařízení do cloudu a druhý postup, najdete v těchto tématech:


> [!div class="nextstepaction"]
> [Odeslání telemetrie ze zařízení](../iot-hub/quickstart-send-telemetry-dotnet.md)
