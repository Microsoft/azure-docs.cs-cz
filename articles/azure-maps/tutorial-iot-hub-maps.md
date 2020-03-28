---
title: 'Kurz: Implementace prostorové analýzy IoT | Mapy Microsoft Azure'
description: Integrujte IoT Hub s rozhraními API služby Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: cfea9aa7bfcc9a9698bb93bdf54797481b8539ce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80333966"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Kurz: Implementace prostorové analýzy IoT pomocí Azure Maps

Ve scénáři IoT je běžné zachytit a sledovat relevantní události, ke kterým dochází v prostoru a čase. Mezi příklady scénářů patří správa vozového parku, sledování majetku, mobilita a aplikace inteligentních měst. Tento kurz vás provede vzorem řešení pomocí rozhraní API Azure Maps. Relevantní události jsou zachyceny službou IoT Hub pomocí modelu odběru událostí poskytovaného službou Event Grid.

V tomto tutoriálu budete:

> [!div class="checklist"]
> * Vytvořit IoT Hub.
> * Nahrajte oblast geografické zóny do Služby Azure Maps, Data pomocí rozhraní API pro nahrávání dat.
> * Vytvořte funkci ve službě Azure Functions a implementujte obchodní logiku založenou na prostorové analýze Azure Maps.
> * Přihlaste se k odběru telemetrických událostí zařízení IoT z funkce Azure prostřednictvím služby Event Grid.
> * Filtrujte události telemetrie pomocí směrování zpráv služby IoT Hub.
> * Vytvořte účet úložiště pro ukládání relevantních dat událostí.
> * Simulujte zařízení IoT ve vozidle.
    

## <a name="use-case"></a>Případ použití

Toto řešení ukazuje scénář, kdy autopůjčovna plánuje sledovat a zaznamenávat události pro své pronajaté vozy. Autopůjčovny obvykle pronajímají auta do určité geografické oblasti. Musí sledovat místa, kde se auta nacházejí, když jsou pronajata. Musí být zaznamenány instance automobilu opouštějící vybranou zeměpisnou oblast. Protokolování dat zajišťuje, že zásady, poplatky a další obchodní aspekty budou správně zpracovány.

V našem případě použití jsou pronajaté vozy vybaveny zařízeními IoT, která pravidelně odesílají telemetrická data do služby Azure IoT Hub. Telemetrie zahrnuje aktuální polohu a označuje, zda je motor vozu v chodu. Schéma umístění zařízení dodržuje schéma IoT [Plug and Play pro geoprostorová data](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md). Telemetrické schéma zařízení pronajatého vozu vypadá takto:

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

K dosažení našeho cíle použijeme telemetrii zařízení ve vozidle. Chceme vykonat pravidla geofencingu. A chceme reagovat vždy, když obdržíme událost, která naznačuje, že se auto pohnulo. Chcete-li tak učinit, budeme se přihlásit k odběru událostí telemetrie zařízení z ioT hubu prostřednictvím Event Grid. 

Existuje několik způsobů, jak se přihlásit k odběru Event Grid, v tomto kurzu používáme Funkce Azure. Funkce Azure reaguje na události publikované v gridu událostí. Implementuje také obchodní logiku pronájmu vozů, která je založena na prostorové analýze Azure Maps. 

Kód uvnitř funkce Azure zkontroluje, zda vozidlo opustilo geofence. Pokud vozidlo opustilo geofence, funkce Azure shromažďuje další informace, jako je například adresa přidružená k aktuální mu. Funkce také implementuje logiku pro ukládání smysluplných dat událostí v úložišti objektů blob dat, které pomáhá poskytnout popis okolností události. 

Okolnosti akce mohou být užitečné pro autopůjčovnu a zákazníka pronájmu. Následující diagram poskytuje přehled na vysoké úrovni systému.

 
  <center>

  ![Přehled systému](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

Následující obrázek představuje oblast geofence zvýrazněnou modře. Trasa pronajatého vozidla je označena zelenou čárou.

  ![Trasa geofence](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Požadavky 

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Chcete-li provést kroky v tomto kurzu, musíte nejprve vytvořit skupinu prostředků na webu Azure Portal. Chcete-li vytvořit skupinu prostředků, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Vyberte **Skupiny prostředků**.
    
   ![Skupiny prostředků](./media/tutorial-iot-hub-maps/resource-group.png)

3. V části **Skupiny prostředků**vyberte **Přidat**.
    
   ![Přidat skupinu prostředků](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Zadejte následující hodnoty vlastností:
    * **Předplatné:** Vyberte předplatné Azure.
    * **Skupina prostředků:** Jako název skupiny zdrojů zadejte "ContosoRental".
    * **Region:** Vyberte oblast pro skupinu prostředků.  

    ![Podrobnosti o skupině prostředků](./media/tutorial-iot-hub-maps/resource-details.png)

    Vyberte **Revize + vytvořit**a pak na další stránce vyberte **Vytvořit.**

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps 

K implementaci obchodní logiky založené na prostorové analýze Azure Maps musíme vytvořit účet Azure Maps ve skupině prostředků, kterou jsme vytvořili. Postupujte podle pokynů v [webu Vytvoření účtu k](quick-demo-map-app.md#create-an-account-with-azure-maps) vytvoření předplatného účtu Azure Maps s cenovou úrovní S1. Postupujte podle pokynů v [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) a získejte primární klíč pro svůj účet. Další informace o ověřování v Azure Maps najdete v [tématu správa ověřování v Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Chcete-li protokolovat data událostí, vytvoříme úložiště pro obecné účely **v2,** které poskytuje přístup ke všem službám Azure Storage: objektům BLOB, souborům, frontám, tabulkám a diskům.  Budeme muset umístit tento účet úložiště do skupiny prostředků "ContosoRental" pro ukládání dat jako objekty BLOB. Chcete-li vytvořit účet úložiště, postupujte podle pokynů při [vytváření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Dále budeme muset vytvořit kontejner pro ukládání objektů BLOB. Postupujte podle následujících kroků:

1. Ve svém "účet úložiště - objekt blob, soubor, tabulka, fronta", přejděte na kontejnery.

    ![Kuličky](./media/tutorial-iot-hub-maps/blobs.png)

2. Klikněte na tlačítko kontejneru v levém horním rohu a pojmenujte kontejner "contoso-rental-logs" a klikněte na tlačítko "OK".

    ![kontejner blob](./media/tutorial-iot-hub-maps/blob-container.png)

3. Přejděte do okna **Přístupové klíče** v účtu úložiště a zkopírujte "název účtu úložiště" a "přístupový klíč". Jsou potřeba v pozdějším kroku.

    ![přístupové klávesy](./media/tutorial-iot-hub-maps/access-keys.png)


Teď máme účet úložiště a kontejner pro protokolování dat událostí. Dále vytvoříme centrum IoT.

### <a name="create-an-iot-hub"></a>Vytvořit IoT Hub

Služba IoT Hub je spravovaná služba v cloudu. IoT Hub funguje jako centrální centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. Chcete-li směrovat telemetrické zprávy zařízení do mřížky událostí, vytvořte centrum IoT Hub v rámci skupiny prostředků "ContosoRental". Nastavte integraci trasy zpráv, kde budeme filtrovat zprávy na základě stavu motoru vozu. Také budeme posílat telemetrické zprávy zařízení do Event Grid, kdykoli se auto pohybuje.

> [!Note] 
> Funkce služby IoT Hub pro publikování událostí telemetrie zařízení v event gridu je ve verzi Public Preview. Funkce veřejné ho verze Preview jsou k dispozici ve všech oblastech kromě **východníCH USA, Západní USA, Západní Evropa, Azure Government, Azure China 21Vianet** a **Azure Germany**. 

Vytvořte aplikaci Iot Hub podle kroků při [vytváření oddílu IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub).


### <a name="register-a-device"></a>Registrování zařízení 

Chcete-li se připojit k centru IoT Hub, musí být zařízení zaregistrováno. Pokud chcete zaregistrovat zařízení v centru IoT hub, postupujte podle následujících kroků:

1. Ve svém IoT Hubu klikněte na okno "Zařízení IoT" a klikněte na "Nový".

    ![přídavné zařízení](./media/tutorial-iot-hub-maps/add-device.png)

2. Na stránce vytvořit zařízení pojmenujte zařízení IoT a klikněte na tlačítko "Uložit".
    
    ![registrační zařízení](./media/tutorial-iot-hub-maps/register-device.png)

3. Uložte **primární připojovací řetězec** zařízení, abyste ho použili v pozdějším kroku, ve kterém je třeba změnit zástupný symbol s tímto připojovacím řetězcem.

    ![přídavné zařízení](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Nahrát geofence

Pomocí [aplikace Postman](https://www.getpostman.com) [nahrajeme geografickou zónu](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) do služby Azure Maps pomocí rozhraní Azure Maps Data Upload API. Každá událost, kdy je auto mimo tuto geofence bude zaznamenána.

Otevřete aplikaci Postman a podle následujících kroků nahrajte geografickou zónu pomocí rozhraní Azure Maps, Data Upload API.  

1. V aplikaci Pošťák klikněte na nový | Vytvořte nový a vyberte Požádat. Zadejte název požadavku pro odeslání dat geografické ploty, vyberte kolekci nebo složku, do které ji chcete uložit, a klikněte na Uložit.

    ![Nahrání geofences pomocí Pošťáka](./media/tutorial-iot-hub-maps/postman-new.png)

2. Na kartě tvůrce vyberte metodu POST HTTP a zadejte následující adresu URL pro vytvoření požadavku POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Hodnota "geojson" proti `dataFormat` parametru v cestě URL představuje formát nahraných dat.

3. Klikněte na **Params**a zadejte následující páry klíč/hodnota, které se použijí pro adresu URL požadavku POST. Nahraďte hodnotu klíče předplatného klíčem Azure Maps.
   
    ![Key-Value params Pošťák](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Klepněte na **body** a vyberte **nezpracovaný** vstupní formát a z rozevíracího seznamu zvolte **JSON (aplikace/text).** Sem otevřete datový [here](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)soubor JSON a zkopírujte json v části tělo jako data, která chcete nahrát, a klepněte na tlačítko **Odeslat**.
    
    ![zveřejnit data](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Zkontrolujte záhlaví odpovědí. Na základě úspěšné žádosti bude hlavička **Umístění** obsahovat identifikátor URI stavu, který zkontroluje aktuální stav požadavku na nahrání. Identifikátor URI stavu bude následujícího formátu. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Zkopírujte identifikátor URI stavu `subscription-key` a přidejte k němu parametr. Přiřaďte `subscription-key` parametru hodnotu klíče předplatného účtu Azure Maps. Formát identifikátoru URI stavu by měl `{Subscription-key}` být podobný formátu níže a měl by být nahrazen klíčem předplatného.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Chcete-li `udId` získat, otevřete novou kartu v aplikaci Postman a vyberte metodu GET HTTP na kartě tvůrce a vytvořte požadavek GET na stavovém uri. Pokud bylo nahrání dat úspěšné, obdržíte v těle odpovědi udId. Zkopírujte udId pro pozdější použití.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Dále vytvoříme funkci Azure v rámci skupiny prostředků "ContosoRental" a pak nastavíme trasu zpráv v centru IoT Hub pro filtrování telemetrických zpráv zařízení.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Vytvoření funkce Azure a přidání předplatného sítě událostí

Azure Functions je výpočetní služba bez serveru, která nám umožňuje spouštět kód na vyžádání, aniž bychom museli explicitně zřizovali nebo spravovali výpočetní infrastrukturu. Další informace o funkcích Azure najdete v dokumentaci k [funkcím Azure.](https://docs.microsoft.com/azure/azure-functions/functions-overview) 

Logika, kterou implementujeme ve funkci, je použití lokalizačních dat pocházejících z telemetrie zařízení ve vozidle pro posouzení stavu geofence. V případě, že dané vozidlo vyjede mimo geofence, funkce získá více informací, jako je adresa místa prostřednictvím [rozhraní Get Search Address Reverse API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Toto rozhraní API převádí danou souřadnici umístění na lidskou srozumitelnou adresu. 

Všechny relevantní informace o události se pak uchovává v úložišti objektů blob. Krok 5 níže odkazuje na spustitelný kód implementující tuto logiku. Podle následujících kroků vytvořte funkci Azure, která odesílá datové protokoly do kontejneru objektů blob v účtu úložiště objektů blob a přidejte k němu předplatné Event Grid.

1. Na řídicím panelu portálu Azure vyberte vytvořit prostředek. V seznamu dostupných typů prostředků vyberte **Vypočítat** a pak vyberte **Aplikace funkce**.

    ![vytvořit zdroj](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na stránce vytvoření **aplikace funkce** pojmenujte aplikaci funkce. V části **Skupina zdrojů**vyberte **použít existující**a v rozevíracím seznamu vyberte "ContosoRental". Vyberte ".NET Core" jako zásobník runtime. V části **Hostování**vyberte **účet úložiště**název účtu úložiště z předchozího kroku. V našem předchozím kroku jsme pojmenovali účet úložiště **v2storage**.  Potom vyberte **Revize+Vytvořit**.
    
    ![vytvořit aplikaci](./media/tutorial-iot-hub-maps/rental-app.png)

2. Zkontrolujte podrobnosti o aplikaci funkce a vyberte "Vytvořit".

3. Jakmile je aplikace vytvořena, musíme k ní přidat funkci. Přejděte do aplikace funkce. Klepnutím na **tlačítko Nová funkce** přidáte funkci a jako vývojové prostředí zvolte **In-Portal.** Potom vyberte **Pokračovat**.

    ![vytvořit funkci](./media/tutorial-iot-hub-maps/function.png)

4. Zvolte **Další šablony** a klikněte na **Dokončit a zobrazit šablony**. 

5. Vyberte šablonu s **aktivační událostí Sítě událostí Azure .** Pokud se zobrazí výzva, nainstalujte rozšíření, pojmenujte funkci a vyberte **Vytvořit**.

    ![šablona funkce](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    **Aktivační událost centra událostí Azure** a aktivační událost **mřížky událostí Azure** mají podobné ikony. Ujistěte se, že jste vybrali **aktivační událost mřížky událostí Azure**.

6. Zkopírujte [kód Jazyka C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) do funkce.
 
7. Ve skriptu C# nahraďte následující parametry. Klikněte na **Uložit**. Ještě neklikejte na **Spustit**
    * Nahraďte **SUBSCRIPTION_KEY** primárním klíčem předplatného účtu Azure Maps.
    * Nahraďte **UDID** udId geofence, které jste nahráli, 
    * Funkce **CreateBlobAsync** ve skriptu vytvoří objekt blob na událost v účtu úložiště dat. Nahraďte **ACCESS_KEY**, **ACCOUNT_NAME**a **STORAGE_CONTAINER_NAME** pomocí přístupového klíče, názvu účtu a kontejneru úložiště dat účtu vašeho účtu.

10. Klikněte na **Přidat předplatné event gridu**.
    
    ![mřížka událostí](./media/tutorial-iot-hub-maps/add-egs.png)

11. Vyplňte podrobnosti o předplatném v části **PODROBNOSTI O PŘEDPLATNÉUDÁLOSTI** Pojmenujte předplatné události. Pro schéma události zvolte "Schéma mřížky událostí". V **části PODROBNOSTI O TÉMATU** vyberte jako typ tématu "Účty Služby Azure IoT Hub". Zvolte stejné předplatné, které jste použili pro vytvoření skupiny prostředků, vyberte "ContosoRental" jako "Skupina prostředků". Vyberte ioT hub, který jste vytvořili jako "prostředek". Vyberte **telemetrii zařízení** jako typ události. Po výběru těchto možností se automaticky zobrazí změna "Typ tématu" na "IoT Hub".

    ![event-grid-subscription](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrování událostí pomocí směrování zpráv služby IoT Hub

Po přidání předplatného služby Event Grid do funkce Azure se v okně Směrování zpráv služby IoT Hub zobrazí výchozí trasa zprávy do mřížky **událostí.** Směrování zpráv umožňuje směrovat různé datové typy do různých koncových bodů. Můžete například směrovat telemetrické zprávy zařízení, události životního cyklu zařízení a události změny dvojčete zařízení. Další informace o směrování zpráv služby IoT hub najdete [v tématu Použití směrování zpráv služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![hub-EG-trasa](./media/tutorial-iot-hub-maps/hub-route.png)

V našem příkladu scénářchceme odfiltrovat všechny zprávy, kde se pohybuje pronajaté vozidlo. Aby bylo možné publikovat takové události telemetrie zařízení do event gridu, `Engine` použijeme směrovací dotaz k filtrování událostí, kde je vlastnost **"ON"**. Existují různé způsoby, jak dotazovat zprávy zařízení-cloud IoT, chcete-li se dozvědět více o syntaxi směrování zpráv, najdete v [tématu Směrování zpráv služby IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Chcete-li vytvořit směrovací dotaz, klepněte na trasu **RouteToEventGrid** a nahraďte **směrovací dotaz** **příkazem "Engine='ON"** a klepněte na tlačítko **Uložit**. Teď IoT hub bude publikovat pouze telemetrie zařízení, kde je zapnutý modul.

![hub-EG-filtr](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Odesílání telemetrických dat do služby IoT Hub

Jakmile bude naše funkce Azure zprovozněna, můžeme teď odesílat telemetrická data do služby IoT Hub, která je přesměruje do mřížky událostí. Použijeme aplikaci C# k simulaci údajů o poloze pro zařízení ve vozidle pronajatého auta. Ke spuštění aplikace potřebujete ve vývojovém počítači .NET Core SDK 2.1.0 nebo vyšší. Podle následujících kroků odešlete simulovaná telemetrická data do služby IoT Hub.

1. Stáhněte si projekt [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#. 

2. Otevřete soubor simulatedCar.cs v textovém editoru podle `connectionString` vašeho výběru a nahraďte hodnotu souboru uloženou při registraci zařízení a uložení změn do souboru.
 
3. Ujistěte se, že máte v počítači nainstalováno jádro .NET Core. V místním okně terminálu přejděte do kořenové složky projektu C# a spusťte následující příkaz pro instalaci požadovaných balíčků pro aplikaci simulovaného zařízení:
    
    ```cmd/sh
    dotnet restore
    ```

4. Ve stejném terminálu spusťte následující příkaz pro sestavení a spuštění aplikace simulace pronájmu auta:

    ```cmd/sh
    dotnet run
    ```

  Místní terminál by měl vypadat jako ten níže.

  ![Výstup terminálu](./media/tutorial-iot-hub-maps/terminal.png)

Pokud teď otevřete kontejner úložiště objektů blob, měli byste vidět čtyři objekty BLOB pro umístění, kde bylo vozidlo mimo geofence.

![Zadat objekt blob](./media/tutorial-iot-hub-maps/blob.png)

Níže uvedená mapa ukazuje čtyři body, kde bylo vozidlo mimo geofence, zaznamenané v pravidelných časových intervalech.

![mapa porušení](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Další kroky

Informace o rozhraních API Map Azure použité v tomto kurzu najdete v tématu:

* [Získat adresu vyhledávání zpět](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Získat geofence](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Úplný seznam rozhraní API AZURE Maps REST najdete v tématu:

* [Rozhraní API REST map Azure](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Další informace o Technologii IoT Plug and Play najdete v tématu:

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Pokud chcete získat seznam zařízení, která mají certifikaci Azure pro IoT, navštivte:

* [Zařízení s certifikací Azure](https://catalog.azureiotsolutions.com/)

Další informace o tom, jak odesílat zařízení do cloudové telemetrie a naopak, najdete v tématu:

* [Odeslání telemetrie ze zařízení](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
