---
title: 'Kurz: implementace prostorových analýz IoT pomocí Azure Maps'
description: 'Kurz: integrace IoT Hub s rozhraními API služby Azure Maps.'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 6c35b52149e3c0117c727771d38d0f010180fc63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432812"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Kurz: implementace prostorových analýz IoT pomocí Azure Maps

Sledování a zachytávání relevantních událostí, ke kterým dochází v prostoru a čase, je běžným scénářem IoT. Například při správě loďstva, sledování prostředků, mobilitě a aplikacích inteligentního měst. Tento kurz vás provede vzorem řešení pro použití Azure Maps rozhraní API proti relevantním událostem zachyceným pomocí IoT Hub pomocí modelu odběru událostí, který poskytuje Event Grid.

V tomto kurzu provedete tyto kroky:

> [!div class="checklist"]
> * Vytvořit IoT Hub.
> * Nahrajte oblast geografické oblasti v Azure Maps datové službě pomocí rozhraní API pro nahrání dat.
> * Vytvořte funkci v Azure Functions, implementujte obchodní logiku na základě Azure Maps prostorových analýz.
> * Přihlaste se k odběru událostí telemetrie zařízení IoT pomocí služby Azure Functions prostřednictvím Event Grid.
> * Vyfiltrujte události telemetrie pomocí IoT Hub směrování zpráv.
> * Vytvořte účet úložiště, do kterého se budou ukládat relevantní data události.
> * Simulujte zařízení IoT ve vozidle.
    

## <a name="use-case"></a>Případ použití

Budeme exemplify řešení pro situaci, kdy se společnost půjčovny aut plánuje monitorovat a protokolovat události pro své pronajaté automobily. Společnosti půjčovny aut jsou často pronajaté automobily pro konkrétní geografickou oblast a potřebují si udržet přehled o jejich umístění i při jejich pronájmu. Jakákoli instance, která zahrnuje auto opouštějící určenou geografickou oblast, musí být zaprotokolována, aby bylo možné správně zpracovat zásady, poplatky a jiné obchodní aspekty.

V našem případě použití jsou Půjčovna auta vybavená zařízeními IoT, která pravidelně odesílají data telemetrie do Azure IoT Hub. Telemetrie zahrnuje aktuální umístění a označuje, jestli je modul automobilu spuštěný, nebo ne. Schéma umístění zařízení dodržuje [schéma IoT technologie Plug and Play pro geoprostorové údaje](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). Schéma telemetrie zařízení pro půjčovnu aut vypadá takto:

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

K dosažení tohoto cíle se dá použít telemetrie zařízení ve vozidle. Naším cílem je spouštět pravidla geografických zón a patřičně reagovat pokaždé, když událost, která signalizuje, že se přesunulo umístění auta. Uděláte to tak, že se přihlásíte k odběru událostí telemetrie zařízení od IoT Hub přes Event Grid, aby se požadovaná obchodní logika zákazníka mohla provádět jenom v případě potřeby. Existuje několik způsobů, jak se přihlásit k odběru Event Grid, v tomto kurzu použijeme Azure Functions. Azure Functions reaguje na události publikované v Event Grid a implementuje obchodní logiku půjčovny aut na základě Azure Mapsch prostorových analýz. Funkce Azure se skládá z kontroly, zda vozidlo opustilo geografickou oblast, a pokud ano, shromažďuje další informace, jako je adresa přidružená k aktuálnímu umístění. Tato funkce také implementuje logiku pro ukládání smysluplných dat událostí do úložiště objektů BLOB dat, která pomáhají poskytnout přesný popis případů událostí analytikovi pronájmu automobilu a také zákazníkovi zapůjčení.

Následující diagram obsahuje podrobný přehled systému.

 
  <center>

  Přehled systému ![](./media/tutorial-iot-hub-maps/system-diagram.png)</center>

Následující obrázek představuje oblast geografické oblasti, která se zvýrazní v trase modrého a prostředku pronájmu, jako zelenou čáru.

  ![Trasa geografické ploty](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Požadavky 

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Abyste mohli dokončit kroky v tomto kurzu, musíte nejdřív vytvořit skupinu prostředků v Azure Portal. Pokud chcete vytvořit skupinu prostředků, postupujte podle následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Vyberte **skupiny prostředků**.
    
   ![Skupiny prostředků](./media/tutorial-iot-hub-maps/resource-group.png)

3. V části skupiny prostředků vyberte **Přidat**.
    
   ![Přidat skupinu prostředků](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Zadejte následující hodnoty vlastností:
    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků:** Jako název skupiny prostředků zadejte "ContosoRental".
    * **Oblast:** Vyberte oblast pro skupinu prostředků.  

    ![Podrobnosti skupiny prostředků](./media/tutorial-iot-hub-maps/resource-details.png)

    Klikněte na tlačítko **zkontrolovat + vytvořit** a na další stránce vyberte **vytvořit** .

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps 

Aby bylo možné implementovat obchodní logiku na základě Azure Maps prostorových analýz, musíme vytvořit účet Azure Maps ve skupině prostředků, kterou jsme vytvořili. Postupujte podle pokynů v části [Vytvoření účtu](quick-demo-map-app.md#create-an-account-with-azure-maps) a vytvořte předplatné účtu Azure Maps s cenovou úrovní S1 a podle kroků v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) Získejte primární klíč pro svůj účet. Další podrobnosti o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Aby bylo možné protokolovat data události, vytvoříme účet pro obecné účely **v2storage** ve skupině prostředků "ContosoRental" pro ukládání dat jako objektů BLOB. Pokud chcete vytvořit účet úložiště, postupujte podle pokynů v částech [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Dále bude nutné vytvořit kontejner pro ukládání objektů BLOB. Postupujte podle následujících kroků:

1. V účtu úložiště přejděte na objekty blob.

    ![struktur](./media/tutorial-iot-hub-maps/blobs.png)

2. Klikněte na tlačítko kontejner v levém horním rohu a pojmenujte kontejner "contoso-pronájem-logs" a klikněte na OK.

    ![blob – kontejner](./media/tutorial-iot-hub-maps/blob-container.png)

3. Přejděte do okna **přístupové klíče** v účtu úložiště a zkopírujte název účtu a přístupový klíč. budeme je používat později.

    ![přístupové klíče](./media/tutorial-iot-hub-maps/access-keys.png)


Teď, když máme účet úložiště a kontejner pro protokolování dat událostí, potom vytvoříme centrum IoT.

### <a name="create-an-iot-hub"></a>Vytvořit IoT Hub

IoT Hub je spravovaná služba v cloudu, která funguje jako centrální Centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. Aby bylo možné směrovat zprávy telemetrie zařízení do Event Grid, vytvoříme IoT Hub v rámci skupiny prostředků ContosoRental a nastavíme integraci směrování zpráv, kde budeme filtrovat zprávy na základě stavu modulu auta a pošle telemetrii zařízení. zprávy na Event Grid při každém přesunu auta. 

> [!Note] 
> Funkce IoT Hub pro publikování událostí telemetrie zařízení v Event Grid je ve verzi Public Preview. Funkce Public Preview jsou dostupné ve všech oblastech kromě **východní USA, západní USA, západní Evropa, Azure Government, Azure Čína 21Vianet** a **Azure Německo**. 

Pomocí postupu v [části vytvoření IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)vytvořte centrum IoT.


### <a name="register-a-device"></a>Registrování zařízení 

Aby bylo možné se připojit k IoT Hub, musí být zařízení registrované. Pokud chcete zaregistrovat zařízení ve službě IoT Hub, postupujte podle následujících kroků:

1. V IoT Hub klikněte na okno zařízení IoT a klikněte na nový.

    ![Přidat zařízení](./media/tutorial-iot-hub-maps/add-device.png)

2. Na stránce vytvořit zařízení pojmenujte zařízení IoT a klikněte na Uložit.
    
    ![registrace zařízení](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>Nahrát geografickou ochranu

Pro [nahrání geografického](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) data do služby Azure Maps pomocí rozhraní API pro nahrání dat Azure Maps použijeme [aplikaci post](https://www.getpostman.com) . Jakákoli událost, pokud je auto mimo tuto geografickou oblast, se zaprotokoluje.

Otevřete aplikaci pro vystavování a podle následujících pokynů nahrajte geografickou ochranou pomocí Azure Maps, rozhraní API pro nahrání dat.  

1. V aplikaci pro odesílání klikněte na nový | Vytvořte nový a vyberte požadavek. Zadejte název žádosti pro nahrávání dat o geografickou ochranou, vyberte kolekci nebo složku, do které se má uložit, a klikněte na Uložit.

    ![Nahrávat geografické ploty pomocí metody post](./media/tutorial-iot-hub-maps/postman-new.png)

2. Na kartě tvůrce vyberte metodu POST HTTP a zadejte následující adresu URL, která odešle požadavek POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Hodnota "\ JSON" na parametru `dataFormat` v cestě URL představuje formát dat, která se nahrávají.

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí pro adresu URL požadavku POST. Nahraďte hodnotu klíčového předplatného klíčem Azure Maps.
   
    ![Post – parametry klíč-hodnota](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Klikněte na **text** a pak vyberte formát **nezpracovaného** vstupu a jako vstupní formát v rozevíracím seznamu zvolte **JSON (aplikace/text)** . [Sem](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)otevřete datový soubor JSON a zkopírujte ho do části text v poli post jako data, která se mají nahrát, a klikněte na **Odeslat**.
    
    ![odeslání dat](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Zkontrolujte hlavičky odpovědi. Po úspěšné žádosti bude hlavička **umístění** obsahovat identifikátor URI stavu, aby zkontrolovala aktuální stav žádosti o nahrání. Identifikátor URI stavu bude v následujícím formátu. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Zkopírujte identifikátor URI stavu a přidejte do něj parametr `subscription-key` s jeho hodnotou jako klíč předplatného pro Azure Maps účet. Formát identifikátoru URI stavu by měl vypadat takto:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Chcete-li získat `udId` otevřete novou kartu v aplikaci post a na kartě tvůrce vyberte získat metodu HTTP a vytvořte požadavek GET na identifikátor URI stavu. Pokud se vaše data úspěšně nahrála, obdržíte udId v těle odpovědi. Zkopírujte udId pro pozdější použití.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


V dalším kroku vytvoříme funkci Azure ve skupině prostředků ContosoRental a pak nastavíte trasu zpráv v IoT Hub, aby se vyfiltroval zprávy telemetrie zařízení.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Vytvoření funkce Azure functions a přidání předplatného Event Grid

Azure Functions je výpočetní služba bez serveru, která nám umožňuje spustit kód na vyžádání, aniž by bylo nutné explicitně zřizovat nebo spravovat výpočetní infrastrukturu. Další informace o Azure Functions najdete v dokumentaci ke [službě Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) . Logika, kterou implementujeme ve funkci, používá data umístění přicházející z telemetrie zařízení v rámci vozidla pro vyhodnocení stavu geografické oblasti. V případě, že dané vozidlo přechází mimo geografickou oblast, funkce bude shromažďovat další informace, jako je adresa umístění, prostřednictvím [rozhraní Get Search Reverted API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) , které překládá dané umístění do popisné adresy. Všechny relevantní informace o událostech se pak uloží do úložiště objektů BLOB. Krok 5 ukazuje na spustitelný kód implementující takovou logiku. Pomocí následujících kroků vytvořte funkci Azure, která odešle protokoly dat do kontejneru objektů BLOB v účtu úložiště a přidá do ní Event Grid předplatné.

1. Na řídicím panelu Azure Portal vyberte vytvořit prostředek. V seznamu dostupných typů prostředků vyberte **COMPUTE** a pak vyberte **aplikace Function App**.

    ![vytvořit prostředek](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na stránce Vytvoření aplikace Function App zadejte název své aplikace Function App, v části **Skupina prostředků** vyberte **použít existující**a v rozevíracím seznamu vyberte "ContosoRental". Jako zásobník modulu runtime vyberte .NET Core, v části **úložiště** vyberte **použít existující** a v rozevíracím seznamu vyberte Contosorentaldata a klikněte na **vytvořit**.
    
    ![vytvořit aplikaci](./media/tutorial-iot-hub-maps/rental-app.png)

3. Po vytvoření aplikace musíme do ní přidat funkci. Přejděte do aplikace Function App a kliknutím na **Nová funkce** Přidejte funkci, jako vývojové prostředí zvolte **in-Portal** a pak vyberte **pokračovat**.

    ![vytvořit funkci](./media/tutorial-iot-hub-maps/function.png)

4. Vyberte **Další šablony** a klikněte na tlačítko **Dokončit a zobrazit šablony**. 

5. Vyberte šablonu s **triggerem Azure Event Grid**. Nainstalovat rozšíření Pokud se zobrazí výzva, pojmenujte funkci a stiskněte **vytvořit**.

    ![Šablona funkce](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. Zkopírujte [kód jazyka c#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) do funkce a klikněte na **Uložit**.
 
7. V skriptu jazyka c# nahraďte následující parametry:
    * Nahraďte **SUBSCRIPTION_KEY** klíčem primárního předplatného účtu Azure Maps.
    * Nahraďte **udId** UDIDou geograficky, kterou jste nahráli. 
    * Funkce **CreateBlobAsync** ve skriptu vytvoří objekt blob na událost v účtu úložiště dat. Nahraďte **ACCESS_KEY**, **ACCOUNT_NAME** a **STORAGE_CONTAINER_NAME** pomocí přístupového klíče účtu úložiště a názvu účtu a kontejneru úložiště dat.

10. Klikněte na **přidat Event Grid předplatné**.
    
    ![Přidat událost-mřížka](./media/tutorial-iot-hub-maps/add-egs.png)

11. Vyplňte podrobnosti předplatného, v části **Podrobnosti odběru události** název vašeho předplatného a schématu událostí vyberte Event Grid schéma. V části **Podrobnosti o tématu** vyberte účty Azure IoT Hub jako typ tématu, zvolte stejné předplatné, které jste použili k vytvoření skupiny prostředků, vyberte "ContosoRental" jako "skupinu prostředků" a zvolte IoT Hub, který jste vytvořili jako prostředek. Jako typ události vyberte **telemetrie zařízení** . Po zvolení těchto možností se zobrazí text "typ tématu", který se změní na "IoT Hub" automaticky.

    ![událost-mřížka-předplatné](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrování událostí pomocí IoT Hub směrování zpráv

Po přidání předplatného Event Grid do funkce Azure se teď můžete podívat na výchozí směrování zprávy, které se Event Grid v **okně Směrování zpráv** v IoT Hub. Směrování zpráv umožňuje směrovat různé typy dat, jako jsou zprávy telemetrie zařízení, události životního cyklu zařízení a události s dvojitou změnou v zařízení do různých koncových bodů. Další informace o směrování zpráv služby IoT Hub najdete v tématu [použití IoT Hub směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![rozbočovač – např. trasa](./media/tutorial-iot-hub-maps/hub-route.png)

V našem ukázkovém scénáři chceme vyfiltrovat všechny zprávy, u kterých se vozidlo za pronájem pohybuje. Aby se tyto události telemetrie zařízení publikovaly Event Grid, použijeme dotaz směrování k filtrování událostí, kde je vlastnost `Engine` **"on"** . Existují různé způsoby, jak dotazovat zprávy ze zařízení do cloudu, a získat další informace o syntaxi směrování zpráv, najdete v tématu [IoT Hub směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Dotaz na směrování vytvoříte tak, že kliknete na trasu **RouteToEventGrid** a nahradíte **dotaz směrování** pomocí **"Engine =" v** "a kliknete na **Uložit**. Centrum IoT teď bude publikovat jenom telemetrii zařízení, ve které je modul ZAPNUTý.

![rozbočovač – např. filtr](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Odeslat data telemetrie do IoT Hub

Jakmile je naše funkce Azure v provozu, pošleme do IoT Hub data telemetrie, která je směrují do Event Grid. Použijeme aplikaci v jazyce c# k simulaci dat o poloze pro zařízení v rámci vozidla ze půjčovny vozidel. Chcete-li spustit aplikaci, potřebujete .NET Core SDK 2.1.0 nebo vyšší na svém vývojovém počítači. Pomocí následujících kroků odešlete Simulovaná data telemetrie do IoT Hub.

1. Stáhněte projekt [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) c#. 

2. Otevřete soubor simulatedCar.cs v textovém editoru podle vašeho výběru a nahraďte hodnotu `connectionString` hodnotou, kterou jste uložili při registraci zařízení, a uložte změny do souboru.
 
3. V místním okně terminálu přejděte do kořenové složky C# projektu a spuštěním následujícího příkazu nainstalujte požadované balíčky pro aplikaci simulovaného zařízení:
    
    ```cmd/sh
    dotnet restore
    ```

4. Ve stejném terminálu spusťte následující příkaz, který sestaví a spustí aplikaci simulace půjčovny aut:

    ```cmd/sh
    dotnet run
    ```

  Místní terminál by měl vypadat jako na následujícím obrázku.

  ![Výstup terminálu](./media/tutorial-iot-hub-maps/terminal.png)

Pokud teď kontejner úložiště objektů BLOB otevřete, měli byste být schopni zobrazit čtyři objekty blob pro umístění, kde bylo vozidlo mimo geografickou oblast.

![Zadat objekt BLOB](./media/tutorial-iot-hub-maps/blob.png)

Níže uvedená mapa znázorňuje čtyři body, ve kterých bylo vozidlo mimo geografickou oblast, protokolované v pravidelných časových intervalech.

![Mapa porušení](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Další kroky

Pokud chcete prozkoumat rozhraní API Azure Maps použitá v tomto kurzu, přečtěte si:

* [Získat reverzní adresu pro hledání](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Získat geografickou ochranu](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Úplný seznam Azure Maps rozhraní REST API najdete v tématu:

* [Rozhraní REST API pro Azure Maps](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Další informace o službě IoT technologie Plug and Play najdete v těchto tématech:

* [technologie Plug and Play IoT](https://docs.microsoft.com/azure/iot-pnp)

Seznam zařízení, která jsou v Azure Certified for IoT, získáte na webu:

* [Zařízení s certifikací Azure](https://catalog.azureiotsolutions.com/)

Další informace o tom, jak odeslat zařízení do telemetrie cloudu a naopak, najdete v těchto tématech:

* [Odeslání telemetrie ze zařízení](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
