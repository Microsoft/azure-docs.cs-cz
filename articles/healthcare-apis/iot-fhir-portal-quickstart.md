---
title: 'Rychlý Start: nasazení konektoru Azure IoT Connector pro FHIR (Preview) pomocí Azure Portal'
description: V tomto rychlém startu se dozvíte, jak nasadit, konfigurovat a používat funkci Azure IoT Connector pro FHIR rozhraní API Azure pro FHIR pomocí Azure Portal.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 09/08/2020
ms.author: punagpal
ms.openlocfilehash: 3b6db74d637ac17ef5d09e1d9c5d3dac30ba8ba9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89594369"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>Rychlý Start: nasazení konektoru Azure IoT Connector pro FHIR (Preview) pomocí Azure Portal

Azure IoT Connector pro FHIR * je volitelná funkce Azure API pro FHIR, která poskytuje schopnost ingestovat data ze zařízení s IoMT (internetem lékařských věcí). Ve fázi Preview je funkce Azure IoT Connector for FHIR dostupná zdarma. V tomto rychlém startu se naučíte:
- Nasazení a konfigurace konektoru Azure IoT pro FHIR pomocí Azure Portal
- Použití simulovaného zařízení k posílání dat do služby Azure IoT Connector pro FHIR
- Zobrazení prostředků vytvořených službou Azure IoT Connector pro FHIR v rozhraní API Azure pro FHIR

## <a name="prerequisites"></a>Předpoklady

- Aktivní předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
- Rozhraní Azure API pro FHIR Resource – [nasazení rozhraní Azure API pro FHIR pomocí Azure Portal](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Přejít na Azure API pro prostředek FHIR

Otevřete [Azure Portal](https://portal.azure.com) a vyhledejte prostředek Azure **API for FHIR** , pro který chcete vytvořit službu Azure IoT Connector pro funkci FHIR.

[![Azure API pro prostředek FHIR](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

V navigační nabídce vlevo klikněte na **konektor IoT (Preview)** v části **Doplňky** a otevřete stránku **konektory IoT** .

[![Funkce konektoru IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>Vytvoření nového konektoru Azure IoT Connector pro FHIR (Preview)

Kliknutím na tlačítko **Přidat** otevřete stránku **vytvořit konektor IoT Connector** .

[![Přidat konektor IoT](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Zadejte nastavení pro nový konektor Azure IoT pro FHIR. Klikněte na tlačítko **vytvořit** a pro nasazení FHIR očekávat službu Azure IoT Connector.

> [!NOTE]
> Pro tuto instalaci musíte vybrat **vytvořit** jako hodnotu pro rozevírací seznam **typ řešení** . 

[![Vytvoření konektoru IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Nastavení|Hodnota|Popis |
|---|---|---|
|Název konektoru|Jedinečný název|Zadejte název, který identifikuje váš konektor Azure IoT pro FHIR. Tento název by měl být jedinečný v rámci Azure API pro prostředek FHIR. Název může obsahovat jenom malá písmena, číslice a znak spojovníku (-). Musí začínat a končit písmenem nebo číslicí a musí mít délku 3-24 znaků.|
|Typ řešení|Vyhledat nebo vytvořit|Pokud máte mimo IP síť, vyberte **Vyhledat** v případě, že máte v rozhraní API Azure pro FHIR vytvořit prostředky [zařízení](https://www.hl7.org/fhir/device.html) a [pacient](https://www.hl7.org/fhir/patient.html) FHIR. Azure IoT Connector pro FHIR použije odkaz na tyto prostředky při vytváření prostředku [pozorovacích](https://www.hl7.org/fhir/observation.html) FHIR, který bude reprezentovat data zařízení. Vyberte **vytvořit** , když chcete, aby Azure IoT Connector pro FHIR vytvářelo holé prostředky zařízení a pacienta v rozhraní API Azure pro FHIR pomocí odpovídajících hodnot identifikátorů přítomných v datech zařízení.|

Po dokončení instalace se nově vytvořený konektor Azure IoT pro FHIR zobrazí na stránce **konektory IoT** .

[![Vytvořil se konektor IoT.](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>Konfigurace Azure IoT Connectoru pro FHIR (Preview)

Azure IoT Connector pro FHIR potřebuje dvě šablony mapování pro transformaci zpráv zařízení na prostředky sledování založené na FHIR: **mapování zařízení** a **mapování FHIR**. Váš konektor Azure IoT pro FHIR není plně funkční, dokud se tato mapování neodešlou.

[![Chybějící mapování konektoru IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Pokud chcete nahrát šablony mapování, klikněte na nově nasazený konektor Azure IoT pro FHIR a přejděte na stránku **IoT Connector** .

[![Kliknutí na konektor IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Mapování zařízení

Šablona mapování zařízení transformuje data zařízení do normalizovaného schématu. Na stránce **konektor IoT** klikněte na tlačítko **Konfigurovat mapování zařízení** a přejděte na stránku **mapování zařízení** . 

[![IoT Connector klikněte na konfigurovat mapování zařízení.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

Na stránce **mapování zařízení** přidejte následující skript do editoru JSON a klikněte na **Uložit**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![Mapování zařízení IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)

#### <a name="fhir-mapping"></a>Mapování FHIR

Šablona mapování FHIR transformuje normalizovanou zprávu na prostředek pozorování založený na FHIR. Na stránce **konektor IoT** klikněte na tlačítko **nakonfigurovat mapování FHIR** a přejděte na stránku **mapování FHIR** .  

[![IoT Connector klikněte na konfigurovat mapování FHIR.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

Na stránce **mapování FHIR** přidejte následující skript do editoru JSON a klikněte na **Uložit**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![Mapování FHIR pro IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Vygenerování připojovacího řetězce

Zařízení IoMT potřebuje připojovací řetězec pro připojení a posílání zpráv do služby Azure IoT Connector pro FHIR. Na stránce **konektor IoT** pro nově nasazený konektor Azure IoT Connector pro FHIR vyberte tlačítko **Spravovat připojení klienta** . 

[![IoT Connector klikněte na Spravovat připojení klientů.](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

Po stránce **připojení** klikněte na tlačítko **Přidat** a vytvořte nové připojení. 

[![Připojení konektoru IoT](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Zadejte popisný název tohoto připojení v překryvném okně a klikněte na tlačítko **vytvořit** .

[![Nové připojení konektoru IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Vyberte nově vytvořené připojení ze stránky **připojení** a zkopírujte hodnotu pole **primární připojovací řetězec** z překryvného okna na pravé straně.

[![Připojovací řetězec konektoru IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Zachovat tento připojovací řetězec, který se má použít v pozdějším kroku. 

## <a name="connect-your-devices-to-iot"></a>Připojení zařízení k IoT

Azure nabízí rozsáhlou sadu produktů IoT pro připojení a správu vašich zařízení IoT. Pomocí služby Azure IoT Hub můžete vytvořit vlastní řešení založené na PaaS nebo začít s platformou spravovat aplikace IoT pomocí Azure IoT Central. V tomto kurzu budeme využívat Azure IoT Central, který obsahuje šablony řešení zaměřené na obor, které vám pomůžou začít.

Nasaďte [šablonu aplikace pro monitorování nepřetržitého pacienta](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template). Tato šablona obsahuje dvě simulovaná zařízení, která vytvářejí data v reálném čase, která vám pomůžou začít: **inteligentní** a **inteligentní kolenum**.

> [!NOTE]
> Až budou vaše skutečná zařízení připravená, můžete pomocí stejné aplikace IoT Central připojit [zařízení](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template) a vyměnit simulátory zařízení. Data zařízení se automaticky začnou přesměrovat do FHIR. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Připojení dat IoT k Azure IoT Connectoru pro FHIR (Preview)
> [!WARNING]
> Šablona mapování zařízení uvedená v této příručce je navržená pro práci s exportem dat (starší verze) v rámci IoT Central.

Po nasazení IoT Central aplikace začnou vaše dvě předem připravená simulovaná zařízení generovat telemetrii. V tomto kurzu budeme ingestovat telemetrii z *inteligentního* simulátoru opravy do FHIR prostřednictvím služby Azure IoT Connector pro FHIR. Chcete-li exportovat data IoT do služby Azure IoT Connector pro FHIR, chceme [v rámci IoT Central nastavit průběžný export dat](https://docs.microsoft.com/azure/iot-central/core/howto-export-data-legacy). Na stránce průběžné exportu dat:
- Jako cíl exportu vyberte *Azure Event Hubs* .
- Vyberte *použít hodnotu připojovacího řetězce* pro **Event Hubs pole obor názvů** .
- Poskytněte konektor Azure IoT Connector pro připojovací řetězec FHIR, který jste získali v předchozím kroku pro pole **připojovací řetězec** .
- *Možnost zachování* **telemetrie** pro **data pro export** pole

## <a name="view-device-data-in-azure-api-for-fhir"></a>Zobrazení dat zařízení v rozhraní Azure API pro FHIR

Můžete zobrazit prostředky pro sledování založené na FHIR, které vytvořila služba Azure IoT Connector pro FHIR v Azure API pro FHIR pomocí post. Nastavte svého poskytovatele [pro přístup k rozhraní API Azure pro FHIR](access-fhir-postman-tutorial.md) a `GET` vyžádejte si požadavek na `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` zobrazení informací o prostředcích FHIR pro sledování pomocí hodnoty srdcových sazeb. 

> [!TIP]
> Ujistěte se, že váš uživatel má odpovídající přístup k rozhraní Azure API pro rovinu dat FHIR. Pomocí [řízení přístupu na základě role Azure (Azure RBAC)](configure-azure-rbac.md) přiřaďte požadované role roviny dat.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete instanci Azure IoT Connectoru pro FHIR odstranit odebráním přidružené skupiny prostředků nebo přidruženého rozhraní Azure API pro službu FHIR nebo samotné instance Azure IoT Connectoru pro instanci FHIR. 

Pokud chcete přímo odebrat službu Azure IoT Connector pro instanci FHIR, vyberte stránku instance ze služby **IoT** Connectors, přejděte na stránku **IoT Connector** a klikněte na tlačítko **Odstranit** . Po zobrazení výzvy k potvrzení vyberte **Ano** . 

[![Odstranit instanci konektoru IoT](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Další kroky

V tomto průvodci rychlým startem jste nasadili funkci Azure IoT Connector for FHIR do svého prostředku Azure API pro FHIR. Pokud chcete získat další informace o službě Azure IoT Connector pro FHIR, vyberte níže v následujících krocích:

Pochopení různých fází toku dat v rámci služby Azure IoT Connector pro FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro tok dat FHIR](iot-data-flow.md)

Naučte se konfigurovat konektor IoT pomocí šablon pro mapování zařízení a FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector pro šablony mapování FHIR](iot-mapping-templates.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview).

FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.