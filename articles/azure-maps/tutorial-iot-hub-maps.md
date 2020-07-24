---
title: 'Kurz: implementace prostorové analýzy IoT | Mapy Microsoft Azure'
description: Integruje IoT Hub s rozhraními API služby Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 1f0668a897f5bc88fbaeb770bc045e56dae2d48f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075494"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Kurz: implementace prostorových analýz IoT pomocí Azure Maps

Ve scénáři IoT je běžné zaznamenávat a sledovat relevantní události, ke kterým dochází v prostoru a čase. Příklady scénářů zahrnují správu loďstva, sledování prostředků, mobilitu a aplikace inteligentního měst. Tento kurz vás provede vzorem řešení pomocí Azure Maps rozhraní API. Relevantní události jsou zachyceny pomocí IoT Hub pomocí modelu odběru událostí, který poskytuje Event Grid.

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

Toto řešení předvádí situaci, kdy se společnost půjčovny automobilů plánuje sledovat a protokolovat události pro jeho půjčovnu automobilů. Společnosti pro nájem auta obvykle pronajímat automobily do konkrétní geografické oblasti. Musí sledovat, jaké má vaše automobily v průběhu pronájmu. Instance auta, které opouští zvolenou geografickou oblast, musí být protokolovány. Protokolování dat zajišťuje správné zpracování zásad, poplatků a dalších obchodních aspektů.

V našem případě použití jsou Půjčovna automobilů vybavená zařízeními IoT, která pravidelně odesílají data telemetrie do Azure IoT Hub. Telemetrie zahrnuje aktuální umístění a označuje, jestli je modul auta spuštěný. Schéma umístění zařízení dodržuje [schéma IoT technologie Plug and Play pro geoprostorové údaje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Schéma telemetrie zařízení pro půjčovnu aut vypadá takto:

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

Pojďme využít telemetrii zařízení v vozidlo k dosažení našeho cíle. Chceme spustit pravidla pro monitorování geografických zón. A chceme odpovědět, kdykoli obdržením události, která indikuje, že se automobil přesunul. Uděláte to tak, že se přihlásíte k odběru událostí telemetrie zařízení od IoT Hub přes Event Grid. 

Existuje několik způsobů, jak se přihlásit k odběru Event Grid, v tomto kurzu používáme Azure Functions. Azure Functions reaguje na události publikované v Event Grid. Implementuje také obchodní logiku pro Pronájem auta, která je založená na Azure Maps prostorové analýzy. 

Kód v rámci Azure Function kontroluje, jestli vozidlo opustilo geografickou ochranu. Pokud vozidlo opustilo geografickou oblast, funkce Azure shromáždí další informace, jako je adresa přidružená k aktuálnímu umístění. Funkce také implementuje logiku pro ukládání smysluplných dat událostí do úložiště objektů BLOB dat, které pomáhá poskytnout popis okolností události. 

Případy událostí mohou být užitečné pro společnost půjčovny aut a zákazníka na nájem. Následující diagram obsahuje podrobný přehled systému.

 
  <center>

  ![Přehled systému](./media/tutorial-iot-hub-maps/system-diagram.png)
  
  </center>

Následující obrázek představuje oblast geografické oblasti zvýrazněnou modrou. Trasa prostředku pronájmu je označena zelenou čárou.

  ![Trasa geografické ploty](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Předpoklady 

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Abyste mohli dokončit kroky v tomto kurzu, musíte nejdřív vytvořit skupinu prostředků v Azure Portal. Chcete-li vytvořit skupinu prostředků, proveďte následující kroky:

1. Přihlaste se na portál [Azure Portal](https://portal.azure.com).

2. Vyberte **skupiny prostředků**.
    
   ![Skupiny prostředků](./media/tutorial-iot-hub-maps/resource-group.png)

3. V části **skupiny prostředků**vyberte **Přidat**.
    
   ![Přidat skupinu prostředků](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Zadejte následující hodnoty vlastností:
    * **Předplatné:** Vyberte své předplatné Azure.
    * **Skupina prostředků:** Jako název skupiny prostředků zadejte "ContosoRental".
    * **Oblast:** Vyberte oblast pro skupinu prostředků.  

    ![Podrobnosti skupiny prostředků](./media/tutorial-iot-hub-maps/resource-details.png)

    Vyberte **zkontrolovat + vytvořit**a pak na další stránce vyberte **vytvořit** .

### <a name="create-an-azure-maps-account"></a>Vytvoření účtu Azure Maps 

Abychom mohli implementovat obchodní logiku na základě Azure Maps prostorové analýzy, musíme vytvořit účet Azure Maps ve skupině prostředků, kterou jsme vytvořili. Podle pokynů v tématu [Vytvoření účtu](quick-demo-map-app.md#create-an-azure-maps-account) vytvořte předplatné Azure Maps účtu s cenovou úrovní S1. Použijte k získání primárního klíče pro váš účet postup uvedený v části [získání primárního klíče](quick-demo-map-app.md#get-the-primary-key-for-your-account) . Další informace o ověřování v Azure Maps najdete v tématu [Správa ověřování v Azure Maps](how-to-manage-authentication.md).



### <a name="create-a-storage-account"></a>vytvořit účet úložiště

K protokolování dat událostí vytvoříme **v2storage** pro obecné účely, který poskytuje přístup ke všem službám Azure Storage: objekty blob, soubory, fronty, tabulky a disky.  Tento účet úložiště budeme muset umístit do skupiny prostředků "ContosoRental", aby se data ukládala jako objekty blob. Pokud chcete vytvořit účet úložiště, postupujte podle pokynů v částech [Vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal). Dále bude nutné vytvořit kontejner pro ukládání objektů BLOB. Postupujte podle následujících kroků:

1. V části účet úložiště – objekt blob, soubor, tabulka, fronta, přejděte na kontejnery.

    ![struktur](./media/tutorial-iot-hub-maps/blobs.png)

2. Klikněte na tlačítko kontejner v levém horním rohu a pojmenujte kontejner "contoso-pronájem-logs" a klikněte na OK.

    ![blob – kontejner](./media/tutorial-iot-hub-maps/blob-container.png)

3. Přejděte do okna **přístupové klíče** v účtu úložiště a zkopírujte "název účtu úložiště" a "přístupový klíč". Jsou potřeba v pozdějším kroku.

    ![přístupové klíče](./media/tutorial-iot-hub-maps/access-keys.png)


Teď máme účet úložiště a kontejner pro protokolování dat událostí. V dalším kroku vytvoříme centrum IoT.

### <a name="create-an-iot-hub"></a>Vytvořit IoT Hub

IoT Hub je spravovaná služba v cloudu. IoT Hub slouží jako centrální Centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. Aby bylo možné směrovat zprávy telemetrie zařízení do Event Grid, vytvořte IoT Hub v rámci skupiny prostředků "ContosoRental". Nastavte integraci zpráv v rámci směrování zpráv, kde budeme filtrovat zprávy na základě stavu modulu auta. Zprávy telemetrie zařízení také pošleme Event Grid při každém přesunu auta.

> [!Note] 
> Funkce IoT Hub pro publikování událostí telemetrie zařízení v Event Grid je ve verzi Public Preview. Funkce Public Preview jsou dostupné ve všech oblastech kromě **východní USA, západní USA, západní Evropa, Azure Government, Azure Čína 21Vianet** a **Azure Německo**. 

Pomocí postupu v [části vytvoření IoT Hub](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)vytvořte centrum IoT.


### <a name="register-a-device"></a>Registrace zařízení 

Aby bylo možné se připojit k IoT Hub, musí být zařízení registrované. Pokud chcete zaregistrovat zařízení ve službě IoT Hub, postupujte podle následujících kroků:

1. V IoT Hub klikněte na okno zařízení IoT a klikněte na nový.

    ![Přidat zařízení](./media/tutorial-iot-hub-maps/add-device.png)

2. Na stránce vytvořit zařízení pojmenujte zařízení IoT a klikněte na Uložit.
    
    ![registrace zařízení](./media/tutorial-iot-hub-maps/register-device.png)

3. Uložte **primární připojovací řetězec** vašeho zařízení, abyste ho mohli použít v pozdějším kroku, ve kterém potřebujete změnit zástupný symbol pomocí tohoto připojovacího řetězce.

    ![Přidat zařízení](./media/tutorial-iot-hub-maps/connection-string.png)

## <a name="upload-geofence"></a>Nahrát geografickou ochranu

Pro [nahrání geografického](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) data do služby Azure Maps pomocí rozhraní API pro nahrání dat Azure Maps použijeme [aplikaci post](https://www.getpostman.com) . Jakákoli událost, pokud je auto mimo tuto geografickou oblast, se zaprotokoluje.

Otevřete aplikaci pro vystavování a podle následujících pokynů nahrajte geografickou ochranou pomocí Azure Maps, rozhraní API pro nahrání dat.  

1. V aplikaci pro odesílání klikněte na nový | Vytvořte nový a vyberte požadavek. Zadejte název žádosti pro nahrávání dat o geografickou ochranou, vyberte kolekci nebo složku, do které se má uložit, a klikněte na Uložit.

    ![Nahrávat geografické ploty pomocí metody post](./media/tutorial-iot-hub-maps/postman-new.png)

2. Na kartě tvůrce vyberte metodu POST HTTP a zadejte následující adresu URL, která odešle požadavek POST.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    Hodnota "\ JSON" na `dataFormat` parametru v cestě URL představuje formát dat, která se nahrávají.

3. Klikněte na **parametry**a zadejte následující páry klíč/hodnota, které se použijí pro adresu URL požadavku POST. Nahraďte hodnotu klíčového předplatného klíčem Azure Maps.
   
    ![Post – parametry klíč-hodnota](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. Klikněte na **tělo** a pak vyberte formát **nezpracovaného** vstupu a jako vstupní formát v rozevíracím seznamu zvolte **JSON (aplikace/text)** . [Sem](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)otevřete datový soubor JSON a zkopírujte ho do části tělo jako data, která chcete nahrát, a klikněte na **Odeslat**.
    
    ![odeslání dat](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Zkontrolujte hlavičky odpovědi. Po úspěšné žádosti bude hlavička **umístění** obsahovat identifikátor URI stavu, aby zkontrolovala aktuální stav žádosti o nahrání. Identifikátor URI stavu bude v následujícím formátu. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Zkopírujte identifikátor URI stavu a přidejte `subscription-key` do něj parametr. K parametru přiřaďte hodnotu klíč předplatného účtu Azure Maps `subscription-key` . Formát identifikátoru URI stavu by měl být podobný následujícímu a byl `{Subscription-key}` nahrazen klíčem předplatného.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. Pokud chcete, otevřete v aplikaci pro vystavení `udId` novou kartu a na kartě tvůrce vyberte získat metodu HTTP a vytvořte požadavek GET na identifikátor URI stavu. Pokud se vaše data úspěšně nahrála, obdržíte udId v těle odpovědi. Zkopírujte udId pro pozdější použití.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


V dalším kroku vytvoříme funkci Azure ve skupině prostředků ContosoRental a pak nastavíte trasu zpráv v IoT Hub, aby se vyfiltroval zprávy telemetrie zařízení.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Vytvoření funkce Azure functions a přidání předplatného Event Grid

Azure Functions je výpočetní služba bez serveru, která nám umožňuje spustit kód na vyžádání, aniž by bylo nutné explicitně zřizovat nebo spravovat výpočetní infrastrukturu. Další informace o Azure Functions najdete v dokumentaci ke [službě Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) . 

Logika, kterou implementujeme ve funkci, používá data umístění přicházející z telemetrie zařízení v rámci vozidla pro vyhodnocení stavu geografické oblasti. V případě, že dané vozidlo přechází mimo geografickou ochranou, funkce získá další informace, jako je adresa umístění, prostřednictvím [rozhraní Get Search address reverzní API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse). Toto rozhraní API překládá dané umístění na adresu, kterou si pochopíte. 

Všechny relevantní informace o událostech se pak uchovávají v úložišti objektů BLOB. Krok 5 ukazuje na spustitelný kód implementující takovou logiku. Pomocí následujících kroků vytvořte funkci Azure, která odešle protokoly dat do kontejneru objektů BLOB v účtu úložiště BLOB a přidejte do ní Event Grid předplatné.

1. Na řídicím panelu Azure Portal vyberte vytvořit prostředek. V seznamu dostupných typů prostředků vyberte **COMPUTE** a pak vyberte **Function App**.

    ![vytvořit prostředek](./media/tutorial-iot-hub-maps/create-resource.png)

2. Na stránce vytváření **Function App** název své aplikace Function App. V části **Skupina prostředků**vyberte **použít existující**a v rozevíracím seznamu vyberte "ContosoRental". Jako zásobník modulu runtime vyberte .NET Core. V části **hostování**pro **účet úložiště**vyberte název účtu úložiště z předchozího kroku. V předchozím kroku jsme najmenovali účet úložiště **v2storage**.  Pak vyberte **zkontrolovat + vytvořit**.
    
    ![vytvořit aplikaci](./media/tutorial-iot-hub-maps/rental-app.png)

2. Zkontrolujte podrobnosti aplikace Function App a vyberte vytvořit.

3. Po vytvoření aplikace musíme do ní přidat funkci. Přejít do aplikace Function App. Klikněte na **Nová funkce** a přidejte funkci a jako vývojové prostředí vyberte možnost jako **portál** . Pak vyberte **pokračovat**.

    ![vytvořit funkci](./media/tutorial-iot-hub-maps/function.png)

4. Vyberte **Další šablony** a klikněte na tlačítko **Dokončit a zobrazit šablony**. 

5. Vyberte šablonu s **triggerem Azure Event Grid**. Nainstalovat rozšíření Pokud se zobrazí výzva, pojmenujte funkci a vyberte **vytvořit**.

    ![Šablona funkce](./media/tutorial-iot-hub-maps/eventgrid-funct.png)
    
    **Aktivační událost centra událostí Azure** a **aktivační událost Azure Event Grid** mají podobné ikony. Ujistěte se, že jste vybrali **aktivační událost Azure Event Grid**.

6. Zkopírujte [kód jazyka C#](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) do funkce.
 
7. V skriptu jazyka C# nahraďte následující parametry. Klikněte na **Uložit**. Neklepejte ještě na **Spustit** .
    * Nahraďte **SUBSCRIPTION_KEY** klíčem primárního předplatného účtu Azure Maps.
    * Nahraďte **udId** UDIDou geograficky, kterou jste nahráli. 
    * Funkce **CreateBlobAsync** ve skriptu vytvoří objekt blob na událost v účtu úložiště dat. Pomocí přístupového klíče účtu úložiště, názvu účtu a kontejneru úložiště dat nahraďte **ACCESS_KEY**, **ACCOUNT_NAME**a **STORAGE_CONTAINER_NAME** .

10. Klikněte na **přidat Event Grid předplatné**.
    
    ![Přidat událost-mřížka](./media/tutorial-iot-hub-maps/add-egs.png)

11. Vyplňte podrobnosti předplatného, v části **Podrobnosti odběru události** zadejte název vašeho odběru události. V části schéma událostí vyberte "Event Grid schéma". V části **Podrobnosti o tématu** vyberte "účty Azure IoT Hub" jako typ tématu. Zvolte stejné předplatné, které jste použili k vytvoření skupiny prostředků, a jako skupinu prostředků vyberte "ContosoRental". Vyberte IoT Hub, který jste vytvořili jako prostředek. Jako typ události vyberte **telemetrie zařízení** . Po výběru těchto možností se zobrazí text "typ tématu", který se změní na "IoT Hub" automaticky.

    ![událost-mřížka-předplatné](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>Filtrování událostí pomocí IoT Hub směrování zpráv

Po přidání předplatného Event Grid do funkce Azure Function se zobrazí výchozí trasa zprávy, která se Event Grid v **okně Směrování zpráv** v IoT Hub. Směrování zpráv umožňuje směrovat různé datové typy do různých koncových bodů. Můžete například směrovat zprávy telemetrie zařízení, události životního cyklu zařízení a události změny zařízení s dvojitou změnou. Další informace o směrování zpráv služby IoT Hub najdete v tématu [použití IoT Hub směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![rozbočovač – např. trasa](./media/tutorial-iot-hub-maps/hub-route.png)

V našem ukázkovém scénáři chceme vyfiltrovat všechny zprávy, u kterých se vozidlo za pronájem pohybuje. Aby se tyto události telemetrie zařízení publikovaly Event Grid, použijeme dotaz směrování k filtrování událostí, ve kterých `Engine` je vlastnost **"on"**. Existují různé způsoby, jak dotazovat zprávy ze zařízení do cloudu, a získat další informace o syntaxi směrování zpráv, najdete v tématu [IoT Hub směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Dotaz na směrování vytvoříte tak, že kliknete na trasu **RouteToEventGrid** a nahradíte **dotaz směrování** pomocí **"Engine =" v** "a kliknete na **Uložit**. Centrum IoT teď bude publikovat jenom telemetrii zařízení, ve které je modul ZAPNUTý.

![rozbočovač – např. filtr](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Odeslat data telemetrie do IoT Hub

Jakmile je naše funkce Azure v provozu, můžeme teď do IoT Hub odesílat data telemetrie, která je směrují do Event Grid. Pojďme použít aplikaci v jazyce C# k simulaci dat o poloze pro zařízení v automobilu. Chcete-li spustit aplikaci, potřebujete .NET Core SDK 2.1.0 nebo vyšší na svém vývojovém počítači. Pomocí následujících kroků odešlete Simulovaná data telemetrie do IoT Hub.

1. Stáhněte projekt [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#. 

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

* [IoT Plug and Play](https://docs.microsoft.com/azure/iot-pnp)

Seznam zařízení, která jsou v Azure Certified for IoT, získáte na webu:

* [Zařízení s certifikací Azure](https://catalog.azureiotsolutions.com/)

Další informace o tom, jak odeslat zařízení do telemetrie cloudu a další způsoby, najdete tady:

* [Odeslání telemetrie ze zařízení](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
