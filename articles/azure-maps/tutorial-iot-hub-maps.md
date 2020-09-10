---
title: 'Kurz: implementace prostorových analýz IoT pomocí map Microsoft Azure'
description: Integruje IoT Hub s rozhraními API služby Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 86ae186d3d8b131955be7d9fa2c305316dea9f00
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658450"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Kurz: implementace prostorových analýz IoT pomocí Azure Maps

Ve scénáři IoT je běžné zaznamenávat a sledovat relevantní události, ke kterým dochází v prostoru a čase. Příklady scénářů zahrnují správu loďstva, sledování prostředků, mobilitu a aplikace inteligentního měst. Tento kurz vás provede řešením, které sleduje využitý pohyb auta pomocí Azure Maps rozhraní API.

V tomto kurzu provedete tyto kroky:

> [!div class="checklist"]
> * Vytvořte účet Azure Storage pro protokolování dat sledování auta.
> * Pomocí rozhraní API pro nahrání dat nahrajte geografickou ochranu do služby Azure Maps data Service.
> * Vytvořte IoT Hub a zaregistrujte zařízení.
> * Vytvořte funkci v Azure Functions, implementujte obchodní logiku na základě Azure Maps prostorových analýz.
> * Přihlaste se k odběru událostí telemetrie zařízení IoT pomocí služby Azure Functions prostřednictvím Event Grid.
> * Vyfiltrujte události telemetrie pomocí IoT Hub směrování zpráv.

## <a name="prerequisites"></a>Předpoklady

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. [Vytvořte účet Azure Maps](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Získejte primární klíč předplatného](quick-demo-map-app.md#get-the-primary-key-for-your-account), označovaný také jako primární klíč nebo klíč předplatného. Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).

4. [Vytvořte skupinu prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). V tomto kurzu pojmenujte naši skupinu prostředků *ContosoRental*, ale můžete si vybrat libovolný název.

5. Stáhněte [projekt RentalCarSimulation C# ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

V tomto kurzu se používá aplikace [po](https://www.postman.com/) aplikaci, ale můžete zvolit jiné vývojové prostředí API.

## <a name="use-case-rental-car-tracking"></a>Případ použití: sledování auta pronájmu

Tento kurz demonstruje následující scénář: společnost pro nájem auta chce do protokolu zaprotokolovat informace o poloze, ujeté vzdálenosti a stavu provozu svých půjčovnních automobilů. Společnost si také přeje ukládat tyto informace pokaždé, když automobil opustí správnou geografickou oblast.

V našem případě použití jsou Půjčovna automobilů vybavená zařízeními IoT, která pravidelně odesílají data telemetrie do Azure IoT Hub. Telemetrie zahrnuje aktuální umístění a označuje, jestli je modul auta spuštěný. Schéma umístění zařízení dodržuje [schéma IoT technologie Plug and Play pro geoprostorové údaje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Schéma telemetrie zařízení automobilového auta vypadá jako v následujícím kódu JSON:

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

V tomto kurzu sledujeme jenom jedno vozidlo. Až nastavíme služby Azure, budete muset stáhnout [projekt RentalCarSimulation C# ](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) , aby se spustil simulátor vozidla. Celý proces, od události až po spuštění funkce, je shrnutý v následujících krocích:

1. Zařízení v rámci vozidla odesílá data telemetrie do centra IoT.

2. Pokud je modul auta spuštěný, Centrum IoT publikuje data telemetrie do Event Grid.

3. Aktivuje se funkce Azure Function z důvodu jejich odběru událostí telemetrie zařízení.

4. Funkce zaznamená souřadnice umístění zařízení vozidlo, čas události a ID zařízení. Pak použije [rozhraní API prostorového geografického zobrazení](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence) k určení, jestli je automobil řízený mimo geografickou oblast. Pokud se provedlo cestování mimo hranice geografické oblasti, funkce uloží data umístění přijatá z události do našeho kontejneru objektů BLOB. Naše funkce se také dotazuje na [hledání zpětného](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) překladu, aby přeložilo umístění souřadnic na ulici a uložilo je do zbytku dat o umístění zařízení.

Následující diagram obsahuje podrobný přehled systému.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Přehled systému":::

Následující obrázek zvýrazňuje oblast geografického rozkladu modře. Trasa k půjčovně aut je označena zelenou čárou.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Trasa geografické ploty":::

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

Pro uložení dat sledování narušení auta vytvoříme ve vaší skupině prostředků [účet úložiště pro obecné účely v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview#general-purpose-v2-accounts) . Pokud jste ještě nevytvořili skupinu prostředků, postupujte podle pokynů v části [Vytvoření skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal#create-resource-groups). V tomto kurzu pojmenujte naši skupinu prostředků *ContosoRental*.

Pokud chcete vytvořit účet úložiště, postupujte podle pokynů v části [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal). V tomto kurzu pojmenováváme účet úložiště *contosorentalstorage*, ale můžete si ho pojmenovat sami.

Po úspěšném vytvoření účtu úložiště musíme vytvořit kontejner pro uložení dat protokolování.

1. Přejděte k nově vytvořenému účtu úložiště. V části Základy klikněte na odkaz **kontejnery** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Kontejnery pro úložiště objektů BLOB":::

2. Klikněte na tlačítko **+ kontejner** v levém horním rohu. Panel se zobrazí na pravé straně prohlížeče. Pojmenujte kontejner *Contoso-pronájem-logs* a klikněte na **vytvořit**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Vytvoření kontejneru objektů blob":::

3. Přejděte do okna **přístupové klíče** v účtu úložiště a zkopírujte **název účtu úložiště** a hodnotu **klíče** do oddílu **klíč1** . Obě hodnoty budeme potřebovat v části [Vytvoření funkce Azure a přidání Event Gridho předplatného](#create-an-azure-function-and-add-an-event-grid-subscription) .

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Kopírovat název a klíč účtu úložiště":::

## <a name="upload-a-geofence"></a>Nahrát geografickou ochranu

Nyní k [nahrání geografické zóny](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) do Azure Maps služby použijeme [aplikaci post](https://www.getpostman.com) . Geografická oblast definuje oprávněnou geografickou oblast pro naše vozidlo pronájmu.  K určení, jestli se automobil přesunul mimo oblast geografické oblasti, budeme používat geografickou oblast v naší službě Azure Functions.

Otevřete aplikaci pro odesílání a podle následujících pokynů nahrajte geografickou ochranou pomocí Azure Maps rozhraní API pro nahrání dat.  

1. Otevřete aplikaci pro vyúčtování. V horní části okna po aplikaci vyberte **Nový**. V okně **vytvořit nové** vyberte **kolekce**.  Pojmenujte kolekci a vyberte tlačítko **vytvořit** .

2. Pokud chcete vytvořit žádost, vyberte **Nový** znovu. V okně **vytvořit nové** vyberte **požadavek**. Zadejte **název žádosti** . Vyberte kolekci, kterou jste vytvořili v předchozím kroku, a pak vyberte **Uložit**.

3. Na kartě tvůrce vyberte metodu **post** http a zadejte následující adresu URL pro nahrání geografického ohraničení do rozhraní API pro nahrání dat. Nezapomeňte nahradit `{subscription-key}` primární klíč předplatného.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    Hodnota "\ JSON" na `dataFormat` parametru v cestě URL představuje formát dat, která se nahrávají.

4. Klikněte na **tělo** a pak vyberte formát **nezpracovaného** vstupu a jako vstupní formát v rozevíracím seznamu zvolte **JSON** . [Sem](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)otevřete datový soubor JSON a zkopírujte kód JSON do části text. Klikněte na **Odeslat**.

5. Klikněte na modré tlačítko **Odeslat** a počkejte na zpracování žádosti. Až se žádost dokončí, přejdete na kartu **hlavičky** odpovědi. Zkopírujte hodnotu klíče **umístění** , což je `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Chcete-li zjistit stav volání rozhraní API, vytvořte požadavek **Get** http na `status URL` . K adrese URL pro ověření budete muset připojit primární klíč předplatného. Požadavek **Get** by měl vypadat jako následující adresa URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}

7. When the **GET** HTTP request completes successfully, it will return a `resourceLocation`. The `resourceLocation` contains the unique `udid` for the uploaded content. You'll need to copy this `udid` for later use in this tutorial.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-azure-iot-hub"></a>Vytvoření Azure IoT Hubu

Azure IoT Hub umožňuje zabezpečenou a spolehlivou obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje.  V našem scénáři chceme získat informace z našeho zařízení v rámci vozidla, aby bylo možné určit umístění půjčovny. V této části vytvoříme centrum IoT v rámci skupiny prostředků *ContosoRental* . Služby IoT Hub budou zodpovědné za publikování událostí telemetrie zařízení.

> [!NOTE]
> Funkce centra IoT pro publikování událostí telemetrie zařízení v Event Grid je ve verzi Public Preview. Funkce Public Preview jsou dostupné ve všech oblastech kromě **východní USA, západní USA, západní Evropa, Azure Government, Azure Čína 21Vianet** a **Azure Německo**.

Pokud chcete ve skupině prostředků *ContosoRental* vytvořit centrum IoT, postupujte podle kroků v části [Vytvoření služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).

## <a name="register-a-device-in-iot-hub"></a>Registrace zařízení ve IoT Hub

Zařízení se nemůžou připojit ke službě IoT Hub, pokud se nezaregistrují v registru identit centra IoT. V našem scénáři vytvoříme jedno zařízení s názvem, *InVehicleDevice*. Pokud chcete zařízení vytvořit a zaregistrovat v rámci služby IoT Hub, postupujte podle kroků v části [Registrace nového zařízení ve službě IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal#register-a-new-device-in-the-iot-hub). Nezapomeňte zkopírovat **primární připojovací řetězec** vašeho zařízení, protože ho použijeme v pozdějším kroku.

## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Vytvoření funkce Azure functions a přidání předplatného Event Grid

Azure Functions je výpočetní služba bez serveru, která umožňuje spustit malé části kódu ("funkce") bez nutnosti explicitně zřizovat nebo spravovat výpočetní infrastrukturu. Další informace o Azure Functions najdete v dokumentaci ke [službě Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) .

Tato funkce je "aktivovaná" určitou událostí. V našem scénáři vytvoříme funkci, která se aktivuje triggerem Event Grid. Vytvoříme relaci mezi triggerem a funkcí vytvořením odběru událostí pro události telemetrie zařízení služby IoT Hub. Když dojde k události telemetrie zařízení, bude funkce volána jako koncový bod a obdrží relevantní data pro zařízení, které [jsme předtím zaregistrovali ve službě IoT Hub](#register-a-device-in-iot-hub).

Kód skriptu jazyka C#, který bude obsahovat naše funkce, se [tady](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)může zobrazit.

Teď nastavíme naši funkci Azure Functions.

1. Na řídicím panelu Azure Portal klikněte na **vytvořit prostředek**. Do textového pole hledání zadejte **Function App** . Klikněte na **Function App**. Klikněte na **Vytvořit**.

2. Na stránce vytváření **Function App** název své aplikace Function App. V části **Skupina prostředků**v rozevíracím seznamu vyberte *ContosoRental* .  Jako **zásobník modulu runtime**vyberte *.NET Core* . Klikněte na **Další: hostování >** v dolní části stránky.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Vytvoření aplikace funkcí":::

3. V poli **účet úložiště**vyberte účet úložiště, který jste vytvořili v části [Vytvoření účtu služby Azure Storage](#create-an-azure-storage-account). Klikněte na **Zkontrolovat a vytvořit**.

4. Zkontrolujte podrobnosti aplikace Function App a klikněte na **vytvořit**.

5. Po vytvoření aplikace se do ní přidá funkce. Přejít do aplikace Function App. Klikněte na okno **Functions (funkce** ). V horní části stránky klikněte na **+ Přidat** . Zobrazí se panel šablony funkce. Procházejte dolů na panelu. Klikněte na **aktivační událost Azure Event Grid**.

     >[!WARNING]
    > **Aktivační událost centra událostí Azure** a šablony **triggeru Azure Event Grid** mají podobné názvy. Ujistěte se, že kliknete na šablonu **triggeru Azure Event Grid** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Vytvoření funkce":::

6. Zadejte název funkce. V tomto kurzu použijeme název, *GetGeoFunction*, ale můžete použít libovolný název, který chcete. Klikněte na **vytvořit funkci**.

7. V levé nabídce klikněte na okno **kód + test** . Zkopírujte a vložte [skript jazyka C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) do okna Code (kód).

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopírování a vkládání kódu do okna funkcí":::

8. V kódu jazyka C# nahraďte následující parametry. Klikněte na **Uložit**. Neklepejte ještě na **test/běh** .
    * Nahraďte **SUBSCRIPTION_KEY** klíčem primárního předplatného pro Azure Maps účtu.
    * Nahraďte **UDIDi** `udid` geografickou ochranou, kterou jste nahráli v rámci [nahrávání geografického](#upload-a-geofence)plotu.
    * Funkce **CreateBlobAsync** ve skriptu vytvoří objekt blob na událost v účtu úložiště dat. Pomocí přístupového klíče účtu úložiště, názvu účtu a kontejneru úložiště dat nahraďte **ACCESS_KEY**, **ACCOUNT_NAME**a **STORAGE_CONTAINER_NAME** . Tyto hodnoty se vygenerovaly při vytvoření účtu úložiště v [Vytvoření účtu úložiště Azure](#create-an-azure-storage-account).

9. V nabídce na levé straně klikněte na okno **integrace** . V diagramu klikněte na **Trigger Event Grid** . Zadejte název triggeru *eventGridEvent*a klikněte na **vytvořit Event Grid předplatné**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Přidání odběru události":::

10. Vyplňte podrobnosti předplatného. Pojmenujte odběr události. V případě *schématu událostí*vyberte *Event Grid schéma*. V **části typy témat**vyberte *účty Azure IoT Hub*. V části **Skupina prostředků**vyberte skupinu prostředků, kterou jste vytvořili na začátku tohoto kurzu. V části **prostředek**vyberte Centrum IoT, které jste vytvořili v části [Vytvoření služby Azure IoT Hub](#create-an-azure-iot-hub). Pro možnost **Filtr na typy událostí**vyberte *telemetrie zařízení*. Po zvolení těchto možností uvidíte změnu **typu tématu** *IoT Hub*. V **části název systémového tématu**můžete použít stejný název jako prostředek.  Nakonec klikněte na **Vybrat koncový bod** v části **Podrobnosti koncového bodu** . Přijměte všechna nastavení a klikněte na **potvrdit výběr**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Vytvořit odběr události":::

11. Zkontrolujte nastavení. Ujistěte se, že koncový bod Určuje funkci, kterou jste vytvořili na začátku této části. Klikněte na **Vytvořit**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Vytvořit potvrzení odběru události":::

12. Teď jste zpátky na panel **Upravit aktivační událost** . Klikněte na **Uložit**.

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrování událostí pomocí IoT Hub směrování zpráv

Když do funkce Azure Function přidáte předplatné Event Grid, v zadaném centru IoT Hub se automaticky vytvoří trasa pro zasílání zpráv. Směrování zpráv umožňuje směrovat různé datové typy do různých koncových bodů. Můžete například směrovat zprávy telemetrie zařízení, události životního cyklu zařízení a události změny zařízení s dvojitou změnou. Další informace o směrování zpráv služby IoT Hub najdete v tématu [použití IoT Hub směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Směrování zpráv ve IoT Hub":::

V našem ukázkovém scénáři chceme dostávat zprávy jenom v případě, že se Půjčovna aut přesouvá. Vytvoříme dotaz směrování pro filtrování událostí, ve kterých se `Engine` vlastnost rovná **"on"**. Dotaz na směrování vytvoříte tak, že kliknete na trasu **RouteToEventGrid** a nahradíte **dotaz směrování** pomocí **"Engine =" v** "a kliknete na **Uložit**. Centrum IoT teď bude publikovat jenom telemetrii zařízení, ve které je modul ZAPNUTý.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Filtrovat zprávy směrování":::

>[!TIP]
>Existují různé způsoby, jak dotazovat zprávy ze zařízení do cloudu, a získat další informace o syntaxi směrování zpráv, najdete v tématu [IoT Hub směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax).

## <a name="send-telemetry-data-to-iot-hub"></a>Odeslat data telemetrie do IoT Hub

Jakmile je naše funkce Azure v provozu, můžeme teď odesílat data telemetrie do služby IoT Hub, která je bude směrovat do Event Grid. Pojďme použít aplikaci v jazyce C# k simulaci dat o poloze pro zařízení v automobilu. Chcete-li spustit aplikaci, potřebujete .NET Core SDK 2.1.0 nebo vyšší na svém vývojovém počítači. Pomocí následujících kroků odešlete Simulovaná data telemetrie do IoT Hub.

1. Pokud jste to ještě neudělali, Stáhněte projekt [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#.

2. V libovolném textovém editoru otevřete soubor simulatedCar.cs a nahraďte hodnotu hodnotou, `connectionString` kterou jste uložili při registraci zařízení, a uložte změny do souboru.

3. Ujistěte se, že máte na svém počítači nainstalovanou platformu .NET Core. V místním okně terminálu přejděte do kořenové složky projektu C# a spuštěním následujícího příkazu nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:

    ```cmd/sh
    dotnet restore
    ```

4. Ve stejném terminálu spusťte následující příkaz, který sestaví a spustí aplikaci simulace půjčovny aut:

    ```cmd/sh
    dotnet run
    ```

  Místní terminál by měl vypadat jako na následujícím obrázku.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Výstup terminálu":::

Pokud teď kontejner úložiště objektů BLOB otevřete, uvidíte čtyři objekty blob pro umístění, kde bylo vozidlo mimo geografickou oblast.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Zobrazit objekty blob uvnitř kontejneru":::

Níže uvedená mapa ukazuje čtyři body umístění vozidel mimo geografickou oblast. Každé umístění bylo zaznamenáno v pravidelných časových intervalech.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Mapa porušení":::

## <a name="explore-azure-maps-and-iot"></a>Prozkoumejte Azure Maps a IoT

Pokud chcete prozkoumat rozhraní API Azure Maps použitá v tomto kurzu, přečtěte si:

* [Získat reverzní adresu pro hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Získat geografickou ochranu](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Úplný seznam Azure Maps rozhraní REST API najdete v tématu:

* [Rozhraní REST API pro Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Další informace o službě IoT technologie Plug and Play najdete v těchto tématech:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Seznam zařízení, která jsou v Azure Certified for IoT, získáte na webu:

* [Zařízení s certifikací Azure](https://catalog.azureiotsolutions.com/)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak odeslat zařízení do telemetrie cloudu a další způsoby, najdete tady:

> [!div class="nextstepaction"]
> [Odeslání telemetrie ze zařízení](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
