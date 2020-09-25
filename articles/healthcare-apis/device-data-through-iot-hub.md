---
title: 'Kurz: příjem dat ze zařízení prostřednictvím Azure IoT Hub'
description: V tomto kurzu se dozvíte, jak povolit směrování dat zařízení z IoT Hub do Azure API pro FHIR prostřednictvím služby Azure IoT Connector pro FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 3b2e4a1ae5ff43283893b286dafb38491a1181b4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91308220"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Kurz: příjem dat ze zařízení prostřednictvím Azure IoT Hub

Azure IoT Connector pro FHIR * nabízí možnost ingestovat data ze zařízení s IoMT (Internet of medicíny) do Azure API pro FHIR. [Nasazení Azure IoT Connectoru pro FHIR (Preview) pomocí Azure Portal](iot-fhir-portal-quickstart.md) rychlý Start ukazuje příklad zařízení spravovaného službou Azure IoT Central [odesílání telemetrie](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) do Azure IoT Connector pro FHIR. Azure IoT Connector pro FHIR může také pracovat se zařízeními zřízenými a spravovanými prostřednictvím Azure IoT Hub. V tomto kurzu najdete postup připojení a směrování dat zařízení z Azure IoT Hub do služby Azure IoT Connector pro FHIR.

## <a name="prerequisites"></a>Požadavky

- Aktivní předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
- Azure API pro prostředek FHIR s minimálně jedním konektorem Azure IoT pro FHIR- [Deploy Azure IoT Connector pro FHIR (Preview) pomocí Azure Portal](iot-fhir-portal-quickstart.md)
- Prostředek Azure IoT Hub připojený pomocí reálných nebo simulovaných zařízení – [vytvoření centra IoT pomocí Azure Portal](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Pokud používáte aplikaci simulovaného zařízení IoT Hub v Azure, můžete si vybrat aplikaci podle vlastního výběru mezi různými podporovanými jazyky a systémy.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Získání připojovacího řetězce pro Azure IoT Connector pro FHIR (Preview)

Azure IoT Hub vyžaduje připojovací řetězec pro zabezpečené připojení ke službě Azure IoT Connector pro FHIR. Vytvořte nový připojovací řetězec pro Azure IoT Connector pro FHIR, jak je popsáno v tématu [vygenerování připojovacího řetězce](iot-fhir-portal-quickstart.md#generate-a-connection-string). Zachovejte tento připojovací řetězec, který se použije v dalším kroku.

Azure IoT Connector pro FHIR používá ke příjemi zpráv zařízení instanci centra událostí Azure v digestoři. Připojovací řetězec vytvořený výše je v podstatě připojovací řetězec k tomuto základnímu centru událostí.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Připojení Azure IoT Hub k Azure IoT Connectoru pro FHIR (Preview)

Azure IoT Hub podporuje funkci [směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) , která poskytuje schopnost odesílat data zařízení do různých služeb Azure, jako je centrum událostí, účet úložiště a Service Bus. Azure IoT Connector pro FHIR využívá tuto funkci k připojení a posílání dat zařízení z Azure IoT Hub do koncového bodu centra událostí.

> [!NOTE] 
> V tuto chvíli můžete k [Vytvoření směrování zpráv](https://docs.microsoft.com/azure/iot-hub/tutorial-routing) použít jenom příkaz PowerShellu nebo rozhraní příkazového řádku, protože Azure IoT Connector pro centrum událostí FHIR není hostovaný na předplatném zákazníka, takže se vám ho nebude zobrazovat prostřednictvím Azure Portal. Když se ale objekty pro směrování zpráv přidávají pomocí PowerShellu nebo rozhraní příkazového řádku, zobrazí se na Azure Portal a dají se z ní spravovat.

Nastavení směrování zpráv se skládá ze dvou kroků.

### <a name="add-an-endpoint"></a>Přidání koncového bodu
Tento krok definuje koncový bod, na který IoT Hub data směrovat. Vytvořte tento koncový bod pomocí příkazu PowerShellu [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) nebo [AZ IoT Hub Routing-Endpoint Create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) CLI na základě vaší předvolby.

Tady je seznam parametrů, které se mají použít s příkazem pro vytvoření koncového bodu:

|Parametr PowerShellu|Parametr CLI|Popis|
|---|---|---|
|ResourceGroupName|resource-group|Název skupiny prostředků vašeho IoT Hub prostředku|
|Name|název centra|Název prostředku IoT Hub.|
|Koncový bod|název koncového bodu|Použijte název, který chcete přiřadit ke vytvářenému koncovému bodu.|
|EndpointType|Typ koncového bodu|Typ koncového bodu, ke kterému IoT Hub potřebovat připojit Pro rozhraní příkazového řádku použijte hodnotu literálu "EventHub" pro PowerShell a "eventhub".|
|EndpointResourceGroup|koncový bod-prostředek-skupina|Název skupiny prostředků pro Azure IoT Connector pro prostředek FHIR Azure API pro FHIR. Tuto hodnotu můžete získat ze stránky přehled rozhraní Azure API pro FHIR.|
|EndpointSubscriptionId|koncový bod – ID předplatného|ID předplatného pro Azure IoT Connector pro prostředek FHIR Azure API pro FHIR. Tuto hodnotu můžete získat ze stránky přehled rozhraní Azure API pro FHIR.|
|Vlastnosti|connection-string|Připojovací řetězec ke službě Azure IoT Connector pro FHIR. Použijte hodnotu, kterou jste získali v předchozím kroku.|

### <a name="add-a-message-route"></a>Přidat trasu zpráv
Tento krok definuje trasu zpráv pomocí koncového bodu vytvořeného výše. Vytvořte trasu pomocí příkazu [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShellu nebo [AZ IoT Hub Route Create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) CLI, a to na základě vaší předvolby.

Tady je seznam parametrů, které se mají použít s příkazem pro přidání trasy zprávy:

|Parametr PowerShellu|Parametr CLI|Popis|
|---|---|---|
|ResourceGroupName|g|Název skupiny prostředků vašeho IoT Hub prostředku|
|Name|název centra|Název prostředku IoT Hub.|
|Koncový bod|název koncového bodu|Název koncového bodu, který jste vytvořili výše.|
|RouteName|název trasy|Název, který se má přiřadit k vytvořenému směrování zpráv.|
|Zdroj|typ zdroje|Typ dat, která se mají odeslat do koncového bodu. Pro rozhraní příkazového řádku použijte hodnotu literálu "DeviceMessages" pro PowerShell a "DeviceMessages".|

## <a name="send-device-message-to-iot-hub"></a>Odeslat zprávu zařízení do IoT Hub

Pomocí zařízení (reálné nebo simulované) odešlete ukázkovou zprávu o kurzu zobrazené níže do Azure IoT Hub. Tato zpráva se přesměruje do služby Azure IoT Connector pro FHIR, kde se zpráva transformuje na prostředek pozorování FHIR a uloží se do Azure API pro FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Nezapomeňte poslat zprávu zařízení, která odpovídá [šablonám mapování](iot-mapping-templates.md) nakonfigurovaným s vaším konektorem Azure IoT Connector pro FHIR.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Zobrazení dat zařízení v rozhraní Azure API pro FHIR

Prostředky pro sledování FHIR vytvořené službou Azure IoT Connector pro FHIR v Azure API pro FHIR můžete zobrazit pomocí post. Nastavte svého poskytovatele [pro přístup k rozhraní API Azure pro FHIR](access-fhir-postman-tutorial.md) a `GET` vyžádejte si požadavek na `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` zobrazení pozorovacích prostředků FHIR s hodnotou srdce odeslanou v předchozí ukázkové zprávě.

> [!TIP]
> Ujistěte se, že váš uživatel má odpovídající přístup k rozhraní Azure API pro rovinu dat FHIR. Pomocí [řízení přístupu na základě role Azure (Azure RBAC)](configure-azure-rbac.md) přiřaďte požadované role roviny dat.


## <a name="next-steps"></a>Další kroky

V této příručce pro rychlý Start jste nastavili Azure IoT Hub ke směrování dat zařízení do služby Azure IoT Connector pro FHIR. Pokud chcete získat další informace o službě Azure IoT Connector pro FHIR, vyberte níže v následujících krocích:

Pochopení různých fází toku dat v rámci služby Azure IoT Connector pro FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro tok dat FHIR](iot-data-flow.md)

Naučte se konfigurovat konektor IoT pomocí šablon pro mapování zařízení a FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro šablony mapování FHIR](iot-mapping-templates.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview).

FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.
