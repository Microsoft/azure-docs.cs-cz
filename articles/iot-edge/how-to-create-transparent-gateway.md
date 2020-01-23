---
title: Vytvoření transparentní brány zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Použití zařízení Azure IoT Edge jako transparentní brána, která dokáže zpracovávat informace ze zařízení příjem dat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bf60bfb41e48220845e9aa26dc26f20e6ed60d16
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510681"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurace zařízení tak, aby fungoval jako transparentní brána IoT Edge

Tento článek poskytuje podrobné pokyny ke konfiguraci IoT Edge zařízení pro fungování jako transparentní brány pro jiná zařízení, která budou komunikovat s IoT Hub. V tomto článku se používá pojem *IoT Edge brána* , která odkazuje na IoT Edge zařízení nakonfigurované jako transparentní bránu. Další informace najdete v tématu [jak se dá zařízení IoT Edge použít jako brána](./iot-edge-as-gateway.md).

>[!NOTE]
>Aktuálně:
>
> * Hraniční zařízení nemůže připojit k brány IoT Edge.
> * Příjem dat zařízení nemohou použít nahrávání souboru.

Existují tři obecné kroky k nastavení úspěšného transparentního připojení brány. Tento článek popisuje první krok:

1. **Zařízení brány musí být schopné bezpečně připojit se k zařízením pro příjem dat, přijímat komunikaci ze zařízení pro příjem dat a směrovat zprávy do správného umístění.**
2. Aby se zařízení mohla ověřit pomocí IoT Hub, musí mít k dispozici identitu zařízení, která může komunikovat prostřednictvím zařízení brány. Další informace najdete v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Zařízení pro příjem dat se musí bezpečně připojit k zařízení brány. Další informace najdete v tématu [připojte zařízení za příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Aby zařízení fungovalo jako brána, musí být schopné bezpečně se připojit ke svým zařízením pro příjem dat. Azure IoT Edge umožňuje používat infrastrukturu veřejných klíčů (PKI) nastavení zabezpečeného připojení mezi zařízeními. V tomto případě jsme se umožní příjem dat zařízení pro připojení k zařízení IoT Edge sloužit jako transparentní brána. Aby bylo možné zajistit přiměřené zabezpečení, musí zařízení pro příjem dat potvrdit identitu zařízení brány. Tato kontrolu identity zabraňuje zařízením v připojení k potenciálně škodlivým branám.

Pro podřízené zařízení v transparentní bráně může být libovolná aplikace nebo platforma, která má identitu vytvořenou pomocí cloudové služby [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) . V mnoha případech se tyto aplikace používat [zařízení Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md). Pro všechny praktické účely může být podřízený zařízení i aplikace běžící na samotném zařízení brány IoT Edge. IoT Edge zařízení ale nemůže být podřízená bráně IoT Edge.

Můžete vytvořit jakékoli infrastrukturu certifikátů, která umožňuje důvěryhodnosti vyžadované pro topologii zařízení brány. V tomto článku se předpokládáme, že použijete stejné nastavení certifikátu, které byste použili k povolení [zabezpečení CA x. 509](../iot-hub/iot-hub-x509ca-overview.md) v IoT Hub, což zahrnuje certifikát CA x. 509, který je přidružený ke konkrétnímu centru IoT (KOŘENová CA služby IoT Hub), sérii certifikátů podepsaných touto certifikační autoritou a certifikační autoritou pro IoT Edge zařízení.

![Instalace certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Pojem "Kořenová certifikační autorita", který se používá v celém tomto článku, odkazuje na veřejný certifikát certifikační autority certifikátu PKI, a ne nutně na kořen certifikátu pro syndikátní certifikační autoritu. V mnoha případech je ve skutečnosti veřejný certifikát zprostředkující certifikační autority.

Brána při zahájení připojení prezentuje své IoT Edge certifikát CA zařízení. Zařízení pro příjem dat zkontroluje, že certifikát CA IoT Edge zařízení je podepsaný certifikátem kořenové certifikační autority. V rámci tohoto procesu může zařízení pro příjem dat potvrdit, že brána pochází z důvěryhodného zdroje.

Následující kroky vás provedou procesem vytvoření certifikátů a jejich instalací do správných míst v bráně. Můžete použít libovolný počítač, čímž vygenerujete certifikáty a potom zkopírovat je do zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge, které je nakonfigurováno pomocí [provozních certifikátů](how-to-install-production-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení edgeHub do brány

Při první instalaci IoT Edge na zařízení se automaticky spustí pouze jeden systémový modul: Agent IoT Edge. Když vytvoříte první nasazení na nějaké zařízení, spustí se i druhý systémový modul, Centrum IoT Edge.

Centrum IoT Edge zodpovídá za příjem příchozích zpráv ze zařízení pro příjem dat a jejich směrování do dalšího cíle. Pokud na vašem zařízení není modul **edgeHub** spuštěný, vytvořte počáteční nasazení pro vaše zařízení. Nasazení bude vypadat prázdné, protože nepřidáte žádné moduly, ale zajistěte, aby byly spuštěny oba systémové moduly.

Můžete zkontrolovat, které moduly jsou spuštěny na zařízení, kontrolou podrobností o zařízení v Azure Portal, zobrazením stavu zařízení v aplikaci Visual Studio nebo Visual Studio Code nebo spuštěním příkazu `iotedge list` na samotném zařízení.

Pokud je modul **edgeAgent** spuštěný bez modulu **edgeHub** , použijte následující postup:

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Přejděte na **IoT Edge** a vyberte zařízení IoT Edge, který chcete použít jako bránu.

3. Vyberte **Nastavit moduly**.

4. Vyberte **Next** (Další).

5. V **trasy zadejte** stránky, měli byste mít výchozí trasu, která odešle všechny zprávy ze všech modulů pro službu IoT Hub. Pokud tomu tak není, přidejte následující kód a vyberte **Next** (Další).

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. V **šablona kontrolní** stránce **odeslat**.

## <a name="open-ports-on-gateway-device"></a>Otevřít porty na zařízení brány

Zařízení Standard IoT Edge nepotřebují žádné příchozí připojení, protože veškerá komunikace s IoT Hub se provádí prostřednictvím odchozích připojení. Zařízení brány jsou odlišná, protože potřebují přijímat zprávy ze svých podřízených zařízení. Pokud je brána firewall mezi zařízeními pro příjem dat a zařízením brány, musí být komunikace také možná přes bránu firewall.

Aby mohl scénář brány fungovat, musí být aspoň jeden z podporovaných protokolů centra IoT Edge otevřený pro příchozí provoz ze zařízení se systémem. Podporované protokoly jsou MQTT, AMQP, HTTPS, MQTT přes WebSockets a AMQP přes objekty WebSockets.

| Port | Protocol (Protokol) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Směrování zpráv ze zařízení příjem dat

Modul runtime IoT Edge může směrovat zprávy odeslané ze zařízení příjem dat, stejně jako zprávy odeslané moduly. Tato funkce umožňuje provádět analýzy v modulu spuštěném v bráně před odesláním dat do cloudu.

V současné době je tak, jak směrovat zprávy odesílané zařízeními podřízené rozlišení na zprávy odeslané moduly. Zprávy odesílané všechny moduly obsahují systém vlastnost s názvem **connectionModuleId** ale zprávy odesílané zařízeními, podřízené, tomu tak není. Klauzule WHERE trasy můžete vyloučit všechny zprávy, které obsahují tuto vlastnost systému.

Níže uvedený postup slouží jako příklad, který by odesílal zprávy ze všech podřízených zařízení do modulu s názvem `ai_insights`a potom z `ai_insights` na IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Další informace o směrování zpráv, najdete v části [nasadit moduly a vytvářet](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Povolit rozšířenou offline operaci

Od verze sady IoT Edge runtime v [1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) se dá zařízení brány a zařízení, která se k němu připojují, nakonfigurovat pro rozšířené operace offline.

Díky této funkci se můžou místní moduly nebo zařízení se stejným zařízením znovu ověřit podle potřeby IoT Edge zařízení a vzájemně komunikovat pomocí zpráv a metod, a to i v případě, že se odpojíte od služby IoT Hub. Další informace najdete v tématu [porozumění rozšířené offline možnosti pro IoT Edge, zařízení, moduly a podřízená zařízení](offline-capabilities.md).

Chcete-li povolit rozšířené možnosti offline, navažte vztah mezi nadřazenými a podřízenými zařízeními mezi zařízením IoT Edge brány a zařízeními pro příjem dat, která se k němu připojí. Tyto kroky jsou podrobněji vysvětleny v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge funguje jako transparentní brána, musíte pro příjem dat zařízení důvěřovat brány a odesílat zprávy do něj. Pokračujte tím, že na [Azure IoT Hub ověříte zařízení pro příjem dat](how-to-authenticate-downstream-device.md) s dalšími kroky v části nastavení vašeho transparentního scénáře brány.
