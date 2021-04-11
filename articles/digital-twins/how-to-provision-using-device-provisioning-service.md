---
title: Automatická správa zařízení pomocí služby Device Provisioning Service
titleSuffix: Azure Digital Twins
description: Přečtěte si, jak nastavit automatizovaný proces pro zřizování a vyřazení zařízení IoT v digitálních událostech Azure pomocí služby Device Provisioning (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a571d92dd9663c7d2d0a576b59e5cd2b3352cb76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950965"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Automatická správa zařízení v digitálních prostředcích Azure pomocí služby Device Provisioning (DPS)

V tomto článku se dozvíte, jak integrovat digitální vlákna Azure se [službou Device Provisioning (DPS)](../iot-dps/about-iot-dps.md).

Řešení popsané v tomto článku vám umožní automatizovat proces **_zřízení_** a **_vyřazení_** zařízení IoT Hub v digitálních prostředcích Azure pomocí služby Device Provisioning. 

Další informace o fázích _zřizování_ a _vyřazení_ a lepší pochopení sady obecných fází správy zařízení, které jsou společné pro všechny projekty IoT v podniku, najdete v [části *životní cyklus zařízení*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) v dokumentaci správy zařízení IoT Hub.

## <a name="prerequisites"></a>Požadavky

Než budete moct nastavit zřizování, musíte nastavit následující:
* **instance digitálního vlákna Azure**. Postupujte podle pokynů v tématu [*Postupy: nastavení instance a ověřování*](how-to-set-up-instance-portal.md) pro vytvoření instance digitálních vláken Azure. Shromážděte **_název hostitele_** instance v Azure Portal ([pokyny](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* **Centrum IoT**. Pokyny najdete v části *vytvoření IoT Hub* tohoto [IoT Hub rychlé](../iot-hub/quickstart-send-telemetry-cli.md)spuštění.
* [**funkce Azure**](../azure-functions/functions-overview.md) , která aktualizuje digitální informace na základě IoT Hub dat. Postupujte podle pokynů v tématu [*How to:*](how-to-ingest-iot-hub-data.md) ingestování dat služby IoT Hub a vytvoření této funkce Azure Functions. Shromážděte **_název_** funkce pro použití v tomto článku.

Tato ukázka také používá **simulátor zařízení** , který zahrnuje zřizování pomocí služby Device Provisioning. Simulátor zařízení je umístěný tady: [Ukázka digitálních vláken Azure a IoT Hub Integration Sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Získejte vzorový projekt na vašem počítači tak, že přejdete na vzorový odkaz a vyberete tlačítko *Stáhnout ZIP* pod nadpisem. Vyextrahování stažené složky.

Budete potřebovat [**Node.js**](https://nodejs.org/download) nainstalovanou na svém počítači. Simulátor zařízení je založený na **Node.js**, verzi 10.0. x nebo novější.

## <a name="solution-architecture"></a>Architektura řešení

Následující obrázek znázorňuje architekturu tohoto řešení pomocí digitálních vláken Azure se službou Device Provisioning. Zobrazuje jak zřizování zařízení, tak i jejich vyřazení.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Diagram zařízení a několik služeb Azure v uceleném scénáři. Data se převedou mezi zařízením termostata a DPS. Data také přecházejí z DPS do IoT Hub a do digitálních vláken Azure pomocí funkce Azure s označením &quot;přidělení&quot;. Data z manuální akce odstranit zařízení přecházejí do IoT Hub > Event Hubs > Azure Functions digitálních vláken Azure." lightbox="media/how-to-provision-using-dps/flows.png":::

Tento článek je rozdělen do dvou částí:
* [*Automatické zřizování zařízení pomocí služby Device Provisioning Service*](#auto-provision-device-using-device-provisioning-service)
* [*Automatické vyřazení zařízení pomocí IoT Hub událostí životního cyklu*](#auto-retire-device-using-iot-hub-lifecycle-events)

Podrobnější vysvětlení jednotlivých kroků v architektuře najdete v jejich jednotlivých částech dále v článku.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Automatické zřizování zařízení pomocí služby Device Provisioning Service

V této části budete připojovat službu Device Provisioning k digitálním Vlákenám Azure k automatickému zřizování zařízení přes níže uvedenou cestu. Toto je výňatek z plné architektury uvedené [výše](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Diagram zřizovacího toku – výňatek diagramu architektury řešení, s čísly, která se přidávají k označení částí toku. Data se převedou mezi zařízeními termostata a DPS (1 pro zařízení > DPS a 5 pro zařízení DPS >). Data také přecházejí z DPS do IoT Hub (4) a do digitálních vláken Azure (3) prostřednictvím funkce Azure s označením &quot;přidělení&quot; (2)." lightbox="media/how-to-provision-using-dps/provision.png":::

Tady je popis toku procesu:
1. Zařízení kontaktuje koncový bod DPS a předává identifikační informace, aby prokázal svoji identitu.
2. DPS ověří identitu zařízení tím, že ověří ID registrace a klíč proti seznamu registrací a zavolá [funkci Azure](../azure-functions/functions-overview.md) , která provede přidělení.
3. Funkce Azure ve službě Azure Digital [revláken pro zařízení vytvoří nový objekt](concepts-twins-graph.md) . Vlákna bude mít stejný název jako **ID registrace** zařízení.
4. DPS registruje zařízení ve službě IoT Hub a naplní požadovaný doplněný stav zařízení.
5. IoT Hub vrátí zařízení informace o ID zařízení a informace o připojení ke službě IoT Hub. Zařízení se teď může připojit ke službě IoT Hub.

V následujících částech najdete postup nastavení tohoto toku zařízení pro Automatické zřizování.

### <a name="create-a-device-provisioning-service"></a>Vytvořit službu Device Provisioning Service

Když se v rámci služby Device Provisioning zřídí nové zařízení, v digitálních proobjektech Azure se stejným názvem, jako je ID registrace, se dá vytvořit nové vlákna pro toto zařízení.

Vytvořte instanci služby Device Provisioning, která se použije ke zřízení zařízení IoT. Můžete buď použít níže uvedené pokyny pro Azure CLI, nebo použít Azure Portal: [*rychlý Start: nastavení IoT Hub Device Provisioning Service s Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

Pomocí následujícího příkazu rozhraní příkazového řádku Azure se vytvoří služba Device Provisioning. Budete muset zadat název služby Device Provisioning, skupinu prostředků a oblast. Pokud chcete zjistit, které oblasti podporují službu Device Provisioning, přejděte na [*produkty Azure dostupné v jednotlivých oblastech*](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
Příkaz se dá spustit v [Cloud Shell](https://shell.azure.com)nebo lokálně, pokud máte [na svém počítači nainstalované](/cli/azure/install-azure-cli)rozhraní příkazového řádku Azure.

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Přidání funkce pro použití se službou Device Provisioning

V projektu Function App, který jste vytvořili v části [požadavky](#prerequisites) , vytvoříte novou funkci pro použití se službou Device Provisioning. Tuto funkci bude používat služba Device Provisioning ve [vlastních zásadách přidělení](../iot-dps/how-to-use-custom-allocation-policies.md) ke zřízení nového zařízení.

Začněte tím, že otevřete projekt Function App v aplikaci Visual Studio na svém počítači a pak postupujte podle následujících kroků.

#### <a name="step-1-add-a-new-function"></a>Krok 1: Přidání nové funkce 

Přidejte novou funkci typu *http-Trigger* do projektu Function App v aplikaci Visual Studio.

:::image type="content" source="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png" alt-text="Snímek obrazovky se zobrazením sady Visual Studio pro přidání funkce Azure typu Trigger http do projektu Function App." lightbox="media/how-to-provision-using-dps/add-http-trigger-function-visual-studio.png":::

#### <a name="step-2-fill-in-function-code"></a>Krok 2: vyplnění kódu funkce

Přidejte do projektu nový balíček NuGet: [Microsoft. Azure. Devices. Provisioning. Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Je možné, že budete muset do projektu přidat další balíčky, i když balíčky používané v kódu nejsou součástí projektu.

V nově vytvořeném souboru s kódem funkce vložte následující kód, přejmenujte funkci na *DpsAdtAllocationFunc. cs* a uložte soubor.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Krok 3: publikování aplikace Function App do Azure

Publikujte projekt pomocí funkce *DpsAdtAllocationFunc. cs* do aplikace Function App v Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-device-provisioning-enrollment"></a>Vytvořit registraci zařízení pro zřizování

V dalším kroku budete muset vytvořit registraci ve službě Device Provisioning pomocí **vlastní alokační funkce**. Postupujte podle pokynů v části [*Vytvoření registrace*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) v článku vlastní zásady přidělování v dokumentaci ke službě Device Provisioning.

Při procházení tohoto toku se ujistěte, že jste vybrali následující možnosti, pomocí kterých propojíte registraci s právě vytvořenou funkcí.

* **Vyberte, jak chcete přiřadit zařízení k** centrům: vlastní (použijte funkci Azure Functions).
* **Vyberte centra IoT, ke kterým se má tato skupina přiřadit:** Zvolte název služby IoT Hub nebo vyberte *odkaz vytvořit nové centrum IoT Hub* a z rozevíracího seznamu zvolte Centrum IoT.

Pak klikněte na tlačítko *vybrat novou funkci* a propojte aplikaci Function App se skupinou pro registraci. Pak zadejte následující hodnoty:

* **Předplatné**: vaše předplatné Azure se automaticky vyplní. Ujistěte se, že se jedná o správné předplatné.
* **Function App**: vyberte název aplikace Function App.
* **Funkce**: vyberte DpsAdtAllocationFunc.

Uložte si podrobnosti.                  

:::image type="content" source="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Snímek obrazovky s oknem podrobností skupiny pro zápis k výběru vlastní (použijte funkci Azure) a název centra IoT v oddílech vyberte, jak chcete přiřadit zařízení k rozbočovačům a vybrat centra IoT, ke kterým se dá tato skupina přiřadit. Také v rozevíracím seznamu vyberte své předplatné, aplikace Function App a nezapomeňte vybrat DpsAdtAllocationFunc." lightbox="media/how-to-provision-using-dps/link-enrollment-group-to-iot-hub-and-function-app.png":::

Po vytvoření registrace se **primární klíč** pro registraci použije později ke konfiguraci simulátoru zařízení pro tento článek.

### <a name="set-up-the-device-simulator"></a>Nastavení simulátoru zařízení

Tato ukázka používá simulátor zařízení, který zahrnuje zřizování pomocí služby Device Provisioning. Simulátor zařízení je umístěný tady: [Ukázka digitálních vláken Azure a IoT Hub Integration Sample](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Pokud jste už ukázku nestáhli, Získejte ji hned tak, že přejdete na vzorový odkaz a v nadpisu vyberete tlačítko *Stáhnout ZIP* . Vyextrahování stažené složky.

#### <a name="upload-the-model"></a>Nahrání modelu

Simulátor zařízení je zařízení typu termostatu, které používá model s tímto ID: `dtmi:contosocom:DigitalTwins:Thermostat;1` . Tento model budete muset nahrát do digitálních vláken Azure, abyste mohli vytvořit nevlákenný tento typ pro zařízení.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Další informace o modelech naleznete v tématu [*How to: Manage Models*](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Konfigurace a spuštění simulátoru

V příkazovém okně přejděte na staženou ukázku *digitálních vláken Azure a IoT Hub integraci* , kterou jste dříve stáhli, a potom do adresáře *zařízení-simulátor* . Dále nainstalujte závislosti pro projekt pomocí následujícího příkazu:

```cmd
npm install
```

Potom v adresáři simulátoru zařízení zkopírujte soubor. env. template do nového souboru s názvem. ENV a shromážděte následující hodnoty pro vyplnění nastavení:

* PROVISIONING_IDSCOPE: tuto hodnotu získáte tak, že v [Azure Portal](https://portal.azure.com/)přejdete do vaší služby Device Provisioning, potom v nabídce Možnosti vyberete *Přehled* a vyhledáte *Rozsah ID* pole.

    :::image type="content" source="media/how-to-provision-using-dps/id-scope.png" alt-text="Snímek obrazovky Azure Portal zobrazení stránky s přehledem zřizování zařízení pro zkopírování hodnoty rozsahu ID." lightbox="media/how-to-provision-using-dps/id-scope.png":::

* PROVISIONING_REGISTRATION_ID: můžete si vybrat ID registrace pro vaše zařízení.
* ADT_MODEL_ID: `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY: Jedná se o primární klíč pro registraci, kterou jste nastavili dříve. Pokud chcete tuto hodnotu znovu získat, přejděte do vaší služby Device Provisioning v Azure Portal, vyberte *spravovat registrace* a pak vyberte skupinu registrací, kterou jste vytvořili dříve, a zkopírujte *primární klíč*.

    :::image type="content" source="media/how-to-provision-using-dps/sas-primary-key.png" alt-text="Snímek obrazovky Azure Portal zobrazení služby Device Provisioning pro správu registrací a zkopírováním hodnoty primárního klíče SAS" lightbox="media/how-to-provision-using-dps/sas-primary-key.png":::

Teď pomocí výše uvedených hodnot aktualizujte nastavení souboru. env.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary SAS key>"
```

Uložte soubor a zavřete ho.

### <a name="start-running-the-device-simulator"></a>Začněte používat simulátor zařízení.

V příkazovém okně v adresáři *simulátor zařízení* spusťte simulátor zařízení pomocí následujícího příkazu:

```cmd
node .\adt_custom_register.js
```

Mělo by se zobrazit, že zařízení je registrované a připojené k IoT Hub, a pak začít odesílat zprávy.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Snímek obrazovky okno Příkaz zobrazující registraci zařízení a odesílání zpráv" lightbox="media/how-to-provision-using-dps/output.png":::

### <a name="validate"></a>Ověření

V důsledku toku, který jste nastavili v tomto článku, se zařízení automaticky zaregistruje v digitálních Vlákenách Azure. Pomocí následujícího příkazu rozhraní příkazového [řádku Azure Digital](how-to-use-cli.md) prohledatelné vyhledáte dvojitou adresu zařízení v instanci digitálních vláken Azure, kterou jste vytvořili.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Mělo by se zobrazit nevláken zařízení, které se nachází v instanci digitálních vláken Azure.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Snímek obrazovky okno Příkaz znázorňující nově vytvořené vlákna." lightbox="media/how-to-provision-using-dps/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Automatické vyřazení zařízení pomocí IoT Hub událostí životního cyklu

V této části budete připojovat IoT Hub události životního cyklu do digitálních vláken Azure k automatickému vyřazení zařízení pomocí níže uvedené cesty. Toto je výňatek z plné architektury uvedené [výše](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Diagram toku zařízení vyřazení – ukázka diagramu architektury řešení, s čísly, která se přiřadí k oddílům toku. Zařízení termostatu se zobrazí bez připojení ke službám Azure v diagramu. Data z manuální akce odstranit zařízení nacházejí do IoT Hub (1) > Event Hubs (2) > Azure Functions > digitálních vláken Azure (3)." lightbox="media/how-to-provision-using-dps/retire.png":::

Tady je popis toku procesu:
1. Externí nebo ruční proces aktivuje odstranění zařízení v IoT Hub.
2. IoT Hub odstraní zařízení a vygeneruje událost [životního cyklu zařízení](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) , která bude směrována do [centra událostí](../event-hubs/event-hubs-about.md).
3. Funkce Azure odstraní v digitálních událostech Azure vlákna tohoto zařízení.

V následujících částech najdete postup nastavení tohoto toku automatického vyřazení zařízení.

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

V dalším kroku vytvoříte [centrum událostí](../event-hubs/event-hubs-about.md) Azure pro příjem IoT Hubch událostí životního cyklu. 

Postupujte podle kroků popsaných v rychlém startu [*vytvoření centra událostí*](../event-hubs/event-hubs-create.md) . Pojmenujte centrum událostí *lifecycleevents*. Tento název centra událostí použijete při nastavování IoT Hub trasy a funkci Azure Functions v dalších částech.

Níže uvedený snímek obrazovky ukazuje vytvoření centra událostí.
:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png" alt-text="Snímek obrazovky okna Azure Portal pro vytvoření centra událostí s názvem lifecycleevents." lightbox="media/how-to-provision-using-dps/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Vytvoření zásad SAS pro centrum událostí

V dalším kroku budete muset vytvořit [zásadu sdíleného přístupového podpisu (SAS)](../event-hubs/authorize-access-shared-access-signature.md) pro konfiguraci centra událostí pomocí aplikace Function App.
K tomu je potřeba
1. Přejděte do centra událostí, které jste právě vytvořili v Azure Portal a v možnostech nabídky vlevo vyberte **zásady sdíleného přístupu** .
2. Vyberte **Přidat**. V okně *Přidat zásady SAS* , které se otevře, zadejte název zásady podle svého výběru a zaškrtněte políčko *poslech* .
3. Vyberte **Vytvořit**.
    
:::image type="content" source="media/how-to-provision-using-dps/add-event-hub-sas-policy.png" alt-text="Snímek obrazovky Azure Portal, do kterého se mají přidat zásady SAS centra událostí" lightbox="media/how-to-provision-using-dps/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Konfigurace centra událostí pomocí aplikace Function App

Dále nakonfigurujte aplikaci funkce Azure Functions, kterou jste nastavili v části [požadavky](#prerequisites) , abyste mohli pracovat s novým centrem událostí. To provedete tak, že v aplikaci Function App nastavíte proměnnou prostředí s připojovacím řetězcem centra událostí.

1. Otevřete zásadu, kterou jste právě vytvořili, a zkopírujte hodnotu **připojovací řetězec – primární klíč** .

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png" alt-text="Snímek obrazovky Azure Portal ke zkopírování připojovacího řetězce – primární klíč" lightbox="media/how-to-provision-using-dps/event-hub-sas-policy-connection-string.png":::

2. Připojovací řetězec přidejte jako proměnnou v nastavení aplikace Functions pomocí následujícího příkazu Azure CLI. Příkaz se dá spustit v [Cloud Shell](https://shell.azure.com)nebo lokálně, pokud máte [na svém počítači nainstalované](/cli/azure/install-azure-cli)rozhraní příkazového řádku Azure.

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Přidání funkce k vyřazení z IoT Hub událostí životního cyklu

V projektu aplikace Function App, který jste vytvořili v části [požadavky](#prerequisites) , vytvoříte novou funkci pro vyřazení stávajícího zařízení pomocí IoT Hub událostí životního cyklu.

Další informace o událostech životního cyklu najdete v tématu [*IoT Hub událostí bez telemetrie*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Další informace o použití Event Hubs s Azure Functions najdete v tématu [*Trigger Azure Event Hubs pro Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Začněte tím, že otevřete projekt Function App v aplikaci Visual Studio na svém počítači a pak postupujte podle následujících kroků.

#### <a name="step-1-add-a-new-function"></a>Krok 1: Přidání nové funkce
     
Přidejte do projektu Function App v aplikaci Visual Studio novou funkci *aktivační události centra událostí* .

:::image type="content" source="media/how-to-provision-using-dps/create-event-hub-trigger-function.png" alt-text="Snímek obrazovky okna sady Visual Studio, ve kterém se do projektu Function App přidá funkce Azure typu Trigger centra událostí" lightbox="media/how-to-provision-using-dps/create-event-hub-trigger-function.png":::

#### <a name="step-2-fill-in-function-code"></a>Krok 2: vyplnění kódu funkce

V nově vytvořeném souboru s kódem funkce vložte následující kód, přejmenujte funkci na a `DeleteDeviceInTwinFunc.cs` uložte soubor.

:::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

#### <a name="step-3-publish-the-function-app-to-azure"></a>Krok 3: publikování aplikace Function App do Azure

Publikujte projekt pomocí funkce *DeleteDeviceInTwinFunc. cs* do aplikace Function App v Azure.

[!INCLUDE [digital-twins-publish-and-configure-function-app.md](../../includes/digital-twins-publish-and-configure-function-app.md)]

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Vytvoření trasy IoT Hub pro události životního cyklu

Nyní nastavíte IoT Hub trasu pro směrování událostí životního cyklu zařízení. V takovém případě budete specificky naslouchat událostem odstranění zařízení, které identifikoval `if (opType == "deleteDeviceIdentity")` . Tím se aktivuje odstranění položky digitálního vlákna, které dokončí vyřazení zařízení a jeho digitálního vlákna.

Nejdřív budete muset ve službě IoT Hub vytvořit koncový bod centra událostí. Pak přidáte trasu do služby IoT Hub pro odesílání událostí životního cyklu do tohoto koncového bodu centra událostí.
Pomocí těchto kroků vytvořte koncový bod centra událostí:

1. V [Azure Portal](https://portal.azure.com/)přejděte do služby IoT Hub, kterou jste vytvořili v části [požadavky](#prerequisites) , a v možnostech nabídky vlevo vyberte **směrování zpráv** .
2. Vyberte kartu **vlastní koncové body** .
3. Vyberte **+ Přidat** a zvolte **centra událostí** a přidejte koncový bod typu centra událostí.

    :::image type="content" source="media/how-to-provision-using-dps/event-hub-custom-endpoint.png" alt-text="Snímek obrazovky okna sady Visual Studio pro přidání vlastního koncového bodu centra událostí" lightbox="media/how-to-provision-using-dps/event-hub-custom-endpoint.png":::

4. V okně *přidat koncový bod centra událostí* , který se otevře, vyberte následující hodnoty:
    * **Název koncového bodu**: vyberte název koncového bodu.
    * **Obor názvů centra událostí**: v rozevíracím seznamu vyberte svůj obor názvů centra událostí.
    * **Instance centra událostí**: vyberte název centra událostí, který jste vytvořili v předchozím kroku.
5. Vyberte **Vytvořit**. Nechejte toto okno otevřené a přidejte trasu do dalšího kroku.

    :::image type="content" source="media/how-to-provision-using-dps/add-event-hub-endpoint.png" alt-text="Snímek obrazovky okna sady Visual Studio pro přidání koncového bodu centra událostí" lightbox="media/how-to-provision-using-dps/add-event-hub-endpoint.png":::

Dále přidáte trasu, která se připojí ke koncovému bodu, který jste vytvořili v předchozím kroku, s dotazem směrování, který odesílá události odstranění. Pomocí těchto kroků vytvořte trasu:

1. Přejděte na kartu *trasy* a výběrem **Přidat** přidejte trasu.

    :::image type="content" source="media/how-to-provision-using-dps/add-message-route.png" alt-text="Snímek obrazovky okna sady Visual Studio, ve kterém můžete přidat trasu pro odesílání událostí." lightbox="media/how-to-provision-using-dps/add-message-route.png":::

2. Na stránce *Přidat trasu* , která se otevře, vyberte následující hodnoty:

   * **Název**: vyberte název trasy. 
   * **Koncový bod**: z rozevíracího seznamu vyberte koncový bod centra událostí, který jste vytvořili dříve.
   * **Zdroj dat**: vyberte *události životního cyklu zařízení*.
   * **Dotaz směrování**: zadejte `opType='deleteDeviceIdentity'` . Tento dotaz omezuje události životního cyklu zařízení, aby odesílaly jenom události odstranění.

3. Vyberte **Uložit**.

    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Snímek obrazovky okna Azure Portal pro přidání trasy k odeslání událostí životního cyklu" lightbox="media/how-to-provision-using-dps/lifecycle-route.png":::

Jakmile projdete tento tok, vše je nastaveno na kompletní vyřazení zařízení do konce.

### <a name="validate"></a>Ověření

Chcete-li aktivovat proces vyřazení, je nutné ručně odstranit zařízení z IoT Hub.

Můžete to provést pomocí příkazu rozhraní [příkazového řádku Azure](/cli/azure/ext/azure-iot/iot/hub/module-identity#ext_azure_iot_az_iot_hub_module_identity_delete) nebo v Azure Portal. Pomocí následujících kroků odstraňte zařízení v Azure Portal:

1. Přejděte do služby IoT Hub a v nabídce vlevo vyberte možnost **zařízení IoT** . 
2. Zobrazí se zařízení s ID registrace zařízení, které jste zvolili v [první polovině tohoto článku](#auto-provision-device-using-device-provisioning-service). Alternativně můžete zvolit jakékoli jiné zařízení, které se má odstranit, pokud má v digitálních proobjektech Azure dvojitou hodnotu, abyste mohli ověřit, že se po odstranění zařízení vytvoří dvojitá vlákna automaticky.
3. Vyberte zařízení a zvolte **Odstranit**.

:::image type="content" source="media/how-to-provision-using-dps/delete-device-twin.png" alt-text="Snímek obrazovky Azure Portal pro odstranění vlákna ze zařízení IoT." lightbox="media/how-to-provision-using-dps/delete-device-twin.png":::

Zobrazení změn v rámci digitálních vláken Azure může trvat několik minut.

Pomocí následujícího příkazu [Azure Digital](how-to-use-cli.md) propouštějící rozhraní příkazového řádku ověříte, že se odstranila vlákna zařízení v instanci digitálních vláken Azure.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id "<Device Registration ID>"
```

Měli byste vidět, že se vlákna zařízení v instanci digitálních vláken Azure už nenašly.

:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Snímek obrazovky okno Příkaz znázorňující nenalezené vlákna." lightbox="media/how-to-provision-using-dps/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky vytvořené v tomto článku nepotřebujete, odstraňte je pomocí těchto kroků.

Pomocí Azure Cloud Shell nebo místních rozhraní příkazového řádku Azure můžete odstranit všechny prostředky Azure ve skupině prostředků pomocí příkazu [AZ Group Delete](/cli/azure/group#az-group-delete) . Tím odeberete skupinu prostředků. instance digitálního vlákna Azure; Centrum IoT a registrace zařízení v centru téma Event Grid a související odběry; obor názvů centra událostí a aplikace Azure Functions, včetně přidružených prostředků, jako je úložiště.

> [!IMPORTANT]
> Odstranění skupiny prostředků je nevratné. Skupina prostředků i všechny prostředky v ní obsažené se trvale odstraní. Ujistěte se, že nechtěně neodstraníte nesprávnou skupinu prostředků nebo prostředky. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Pak odstraňte ukázkovou složku projektu, kterou jste stáhli z místního počítače.

## <a name="next-steps"></a>Další kroky

Digitální vlákna vytvořená pro zařízení se ukládají jako Plochá hierarchie v rámci digitálních vláken Azure, ale dají se rozšířit pomocí informací o modelu a víceúrovňové hierarchie pro organizaci. Pokud se chcete dozvědět víc o tomto konceptu, přečtěte si:

* [*Koncepty: digitální vlákna a Dvojitá graf*](concepts-twins-graph.md)

Další informace o použití požadavků HTTP s Azure Functions najdete v těchto tématech:

* [*Aktivační událost požadavku HTTP Azure pro Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md)

Můžete napsat vlastní logiku, která automaticky poskytne tyto informace pomocí modelu a dat grafu, která jsou již uložena v rámci digitálních vláken Azure. Další informace o správě, upgradu a načítání informací z grafu vláken naleznete v následujících tématech:

* [*Postupy: Správa digitálního vlákna*](how-to-manage-twin.md)
* [*Postupy: dotazování na nevlákenný graf*](how-to-query-graph.md)