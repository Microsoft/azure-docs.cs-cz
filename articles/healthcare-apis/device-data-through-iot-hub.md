---
title: 'Kurz: příjem dat ze zařízení prostřednictvím Azure IoT Hub'
description: V tomto kurzu se dozvíte, jak povolit směrování dat zařízení z IoT Hub do Azure API pro FHIR prostřednictvím konektoru IoT.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 06/27/2020
ms.author: punagpal
ms.openlocfilehash: 95e3e2be175fa810b1b966a7dda5a0e53e23d780
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536735"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Kurz: příjem dat ze zařízení prostřednictvím Azure IoT Hub

IoT Connector nabízí možnost ingestovat data ze zařízení s internetem zdravotnických věcí (IoMT) do Azure API pro FHIR. [Nasazení konektoru IoT (Preview) pomocí Azure Portalho](iot-fhir-portal-quickstart.md) rychlého startu ukázal příklad zařízení spravovaného službou Azure IoT Central [odesílání telemetrie](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) do služby IoT Connector. IoT Connector může také pracovat se zařízeními zřízenými a spravovanými prostřednictvím Azure IoT Hub. Tento kurz poskytuje postup připojení a směrování dat zařízení z Azure IoT Hub do služby IoT Connector.

## <a name="prerequisites"></a>Předpoklady

- Aktivní předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
- Azure API pro prostředek FHIR s minimálně jedním konektorem IoT – [nasazení konektoru IoT Connector (Preview) pomocí Azure Portal](iot-fhir-portal-quickstart.md)
- Prostředek Azure IoT Hub připojený pomocí reálných nebo simulovaných zařízení – [vytvoření centra IoT pomocí Azure Portal](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Pokud používáte aplikaci simulovaného zařízení IoT Hub v Azure, můžete si vybrat aplikaci podle vlastního výběru mezi různými podporovanými jazyky a systémy.

## <a name="get-connection-string-for-iot-connector-preview"></a>Získání připojovacího řetězce pro IoT Connector (Preview)

Azure IoT Hub vyžaduje připojovací řetězec pro zabezpečené připojení k vašemu konektoru IoT. Vytvořte nový připojovací řetězec pro konektor IoT, jak je popsáno v tématu [vygenerování připojovacího řetězce](iot-fhir-portal-quickstart.md#generate-a-connection-string). Zachovejte tento připojovací řetězec, který se použije v dalším kroku.

IoT Connector přijímá zprávy zařízení pomocí instance centra událostí Azure v digestoři. Připojovací řetězec vytvořený výše je v podstatě připojovací řetězec k tomuto základnímu centru událostí.

## <a name="connect-azure-iot-hub-with-the-iot-connector-preview"></a>Připojení Azure IoT Hub pomocí konektoru IoT (Preview)

Azure IoT Hub podporuje funkci [směrování zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c) , která poskytuje schopnost odesílat data zařízení do různých služeb Azure, jako je centrum událostí, účet úložiště a Service Bus. Služba IoT Connector využívá tuto funkci k připojení a posílání dat zařízení z Azure IoT Hub do koncového bodu centra událostí.

> [!NOTE] 
> V tuto chvíli můžete k [Vytvoření směrování zpráv](https://docs.microsoft.com/azure/iot-hub/tutorial-routing) použít jenom příkaz PowerShellu nebo rozhraní příkazového řádku, protože centrum událostí konektoru IoT není hostované na předplatném zákazníka, takže se vám ho nebude zobrazovat prostřednictvím Azure Portal. Když se ale objekty pro směrování zpráv přidávají pomocí PowerShellu nebo rozhraní příkazového řádku, zobrazí se na Azure Portal a dají se z ní spravovat.

Nastavení směrování zpráv se skládá ze dvou kroků.

### <a name="add-an-endpoint"></a>Přidání koncového bodu
Tento krok definuje koncový bod, na který IoT Hub data směrovat. Vytvořte tento koncový bod pomocí příkazu PowerShellu [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) nebo [AZ IoT Hub Routing-Endpoint Create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) CLI na základě vaší předvolby.

Tady je seznam parametrů, které se mají použít s příkazem pro vytvoření koncového bodu:

|Parametr PowerShellu|Parametr CLI|Popis|
|---|---|---|
|ResourceGroupName|resource-group|Název skupiny prostředků vašeho IoT Hub prostředku|
|Název|název centra|Název prostředku IoT Hub.|
|Koncový bod|název koncového bodu|Použijte název, který chcete přiřadit ke vytvářenému koncovému bodu.|
|EndpointType|Typ koncového bodu|Typ koncového bodu, ke kterému IoT Hub potřebovat připojit Pro rozhraní příkazového řádku použijte hodnotu literálu "EventHub" pro PowerShell a "eventhub".|
|EndpointResourceGroup|koncový bod-prostředek-skupina|Název skupiny prostředků pro prostředek služby Azure API vašeho konektoru IoT pro FHIR. Tuto hodnotu můžete získat ze stránky přehled rozhraní Azure API pro FHIR.|
|EndpointSubscriptionId|koncový bod – ID předplatného|ID předplatného Azure API vašeho konektoru IoT pro prostředek FHIR. Tuto hodnotu můžete získat ze stránky přehled rozhraní Azure API pro FHIR.|
|Vlastnosti|connection-string|Připojovací řetězec k vašemu konektoru IoT Použijte hodnotu, kterou jste získali v předchozím kroku.|

### <a name="add-a-message-route"></a>Přidat trasu zpráv
Tento krok definuje trasu zpráv pomocí koncového bodu vytvořeného výše. Vytvořte trasu pomocí příkazu [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShellu nebo [AZ IoT Hub Route Create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) CLI, a to na základě vaší předvolby.

Tady je seznam parametrů, které se mají použít s příkazem pro vytvoření koncového bodu:

|Parametr PowerShellu|Parametr CLI|Popis|
|---|---|---|
|ResourceGroupName|g|Název skupiny prostředků vašeho IoT Hub prostředku|
|Název|název centra|Název prostředku IoT Hub.|
|Koncový bod|název koncového bodu|Název koncového bodu, který jste vytvořili výše.|
|RouteName|název trasy|Název, který se má přiřadit k vytvořenému směrování zpráv.|
|Zdroj|typ zdroje|Typ dat, která se mají odeslat do koncového bodu. Pro rozhraní příkazového řádku použijte hodnotu literálu "DeviceMessages" pro PowerShell a "DeviceMessages".|

## <a name="send-device-message-to-iot-hub"></a>Odeslat zprávu zařízení do IoT Hub

Pomocí zařízení (reálné nebo simulované) odešlete ukázkovou zprávu o kurzu zobrazené níže do Azure IoT Hub. Tato zpráva se přesměruje do konektoru IoT, kde se zpráva transformuje na prostředek sledování FHIR a uloží se do Azure API pro FHIR.

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
> Nezapomeňte poslat zprávu zařízení, která odpovídá [šablonám mapování](iot-mapping-templates.md) nakonfigurovaným s vaším konektorem IoT.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Zobrazení dat zařízení v rozhraní Azure API pro FHIR

Prostředky pro sledování FHIR vytvořené službou IoT Connector můžete zobrazit v rozhraní Azure API pro FHIR pomocí post. Nastavte svého poskytovatele [pro přístup k rozhraní API Azure pro FHIR](access-fhir-postman-tutorial.md) a `GET` vyžádejte si požadavek na `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` zobrazení pozorovacích prostředků FHIR s hodnotou srdce odeslanou v předchozí ukázkové zprávě.

> [!TIP]
> Ujistěte se, že váš usx'er má odpovídající přístup k rozhraní Azure API pro rovinu dat FHIR. Pomocí [Access Control založeného na rolích Azure](configure-azure-rbac.md) můžete přiřadit požadované role roviny dat.


## <a name="next-steps"></a>Další kroky

V této příručce pro rychlý Start jste nastavili Azure IoT Hub k směrování dat zařízení do služby IoT Connector. Chcete-li získat další informace o službě IoT Connector, vyberte níže v následujících krocích:

Pochopení různých fází toku dat v rámci služby IoT Connector.

>[!div class="nextstepaction"]
>[Tok dat konektor IoT](iot-data-flow.md)

Naučte se konfigurovat konektor IoT pomocí šablon pro mapování zařízení a FHIR.

>[!div class="nextstepaction"]
>[Šablony mapování pro IoT Connector](iot-mapping-templates.md)

FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.

