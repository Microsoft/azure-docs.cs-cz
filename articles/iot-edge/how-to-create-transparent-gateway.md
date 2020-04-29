---
title: Vytvořit zařízení transparentní brány – Azure IoT Edge | Microsoft Docs
description: Použít zařízení Azure IoT Edge jako transparentní bránu, která může zpracovávat informace ze zařízení pro příjem dat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: e563e67b5e951b43e5782f8c845c8ec46ff3e9bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687165"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána

Tento článek poskytuje podrobné pokyny ke konfiguraci IoT Edge zařízení pro fungování jako transparentní brány pro jiná zařízení, která budou komunikovat s IoT Hub. V tomto článku se používá pojem *IoT Edge brána* , která odkazuje na IoT Edge zařízení nakonfigurované jako transparentní bránu. Další informace najdete v tématu [jak se dá zařízení IoT Edge použít jako brána](./iot-edge-as-gateway.md).

>[!NOTE]
>Aktuálně
>
> * Zařízení s podporou Edge se nemůžou připojit k IoT Edge branám.
> * Podřízená zařízení nemůžou používat nahrávání souborů.

Existují tři obecné kroky k nastavení úspěšného transparentního připojení brány. Tento článek popisuje první krok:

1. **Zařízení brány musí být schopné bezpečně připojit se k zařízením pro příjem dat, přijímat komunikaci ze zařízení pro příjem dat a směrovat zprávy do správného umístění.**
2. Aby se zařízení mohla ověřit pomocí IoT Hub, musí mít k dispozici identitu zařízení, která může komunikovat prostřednictvím zařízení brány. Další informace najdete v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Zařízení pro příjem dat se musí bezpečně připojit k zařízení brány. Další informace najdete v tématu [připojení zařízení pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Aby zařízení fungovalo jako brána, musí být schopné bezpečně se připojit ke svým zařízením pro příjem dat. Azure IoT Edge umožňuje použít infrastrukturu veřejných klíčů (PKI) k nastavení zabezpečených připojení mezi zařízeními. V tomto případě umožníme, aby se pro zařízení s IoT Edge připojila k zařízení, které funguje jako transparentní brána. Aby bylo možné zajistit přiměřené zabezpečení, musí zařízení pro příjem dat potvrdit identitu zařízení brány. Tato kontrolu identity zabraňuje zařízením v připojení k potenciálně škodlivým branám.

Pro podřízené zařízení v transparentní bráně může být libovolná aplikace nebo platforma, která má identitu vytvořenou pomocí cloudové služby [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) . V mnoha případech tyto aplikace používají [sadu SDK pro zařízení Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). V případě všech praktických účelů může být jako aplikace spuštěná na samotném zařízení IoT Edge brány. IoT Edge zařízení ale nemůže být podřízená bráně IoT Edge.

Můžete vytvořit jakoukoli infrastrukturu certifikátů, která umožňuje důvěryhodnost potřebnou pro topologii zařízení a brány. V tomto článku se předpokládáme, že použijete stejné nastavení certifikátu, které byste použili k povolení [zabezpečení CA x. 509](../iot-hub/iot-hub-x509ca-overview.md) v IoT Hub, což zahrnuje certifikát CA x. 509, který je přidružený ke konkrétnímu centru IoT (KOŘENová CA služby IoT Hub), sérii certifikátů podepsaných touto certifikační autoritou a certifikační autoritou pro IoT Edge zařízení.

![Nastavení certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Pojem "Kořenová certifikační autorita", který se používá v celém tomto článku, odkazuje na veřejný certifikát certifikační autority certifikátu PKI, a ne nutně na kořen certifikátu pro syndikátní certifikační autoritu. V mnoha případech je ve skutečnosti veřejný certifikát zprostředkující certifikační autority.

Démon zabezpečení IoT Edge používá certifikát certifikační autority pro IoT Edge zařízení k podepsání certifikátu certifikační autority pro úlohy, který zase podepisuje certifikát serveru pro Centrum IoT Edge. Brána prezentuje svůj certifikát serveru pro zařízení pro příjem dat během zahájení připojení. Zařízení pro příjem dat kontroluje, zda je certifikát serveru součástí řetězu certifikátů, který se shrnuje do certifikátu kořenové certifikační autority. V rámci tohoto procesu může zařízení pro příjem dat potvrdit, že brána pochází z důvěryhodného zdroje. Další informace najdete v tématu [Vysvětlení způsobu, jakým Azure IoT Edge používá certifikáty](iot-edge-certs.md).

Následující kroky vás provedou procesem vytvoření certifikátů a jejich instalací do správných míst v bráně. K vygenerování certifikátů můžete použít libovolný počítač a pak je zkopírovat do zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge, které je nakonfigurováno pomocí [provozních certifikátů](how-to-manage-device-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení edgeHub do brány

Při první instalaci IoT Edge na zařízení se automaticky spustí pouze jeden systémový modul: Agent IoT Edge. Když vytvoříte první nasazení na nějaké zařízení, spustí se i druhý systémový modul, Centrum IoT Edge.

Centrum IoT Edge zodpovídá za příjem příchozích zpráv ze zařízení pro příjem dat a jejich směrování do dalšího cíle. Pokud na vašem zařízení není modul **edgeHub** spuštěný, vytvořte počáteční nasazení pro vaše zařízení. Nasazení bude vypadat prázdné, protože nepřidáte žádné moduly, ale zajistěte, aby byly spuštěny oba systémové moduly.

Můžete zkontrolovat, které moduly jsou spuštěny na zařízení, kontrolou podrobností o zařízení v Azure Portal, zobrazením stavu zařízení v aplikaci Visual Studio nebo Visual Studio Code nebo spuštěním příkazu `iotedge list` na samotném zařízení.

Pokud je modul **edgeAgent** spuštěný bez modulu **edgeHub** , použijte následující postup:

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Přejít na **IoT Edge** a vybrat IoT Edge zařízení, které chcete použít jako bránu.

3. Vyberte možnost **nastavit moduly**.

4. Vyberte **Další**.

5. Na stránce **zadat trasy** byste měli mít výchozí trasu, která odesílá všechny zprávy ze všech modulů do IoT Hub. Pokud tomu tak není, přidejte následující kód a vyberte **Next** (Další).

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Na stránce **zkontrolovat šablonu** vyberte **Odeslat**.

## <a name="open-ports-on-gateway-device"></a>Otevřít porty na zařízení brány

Zařízení Standard IoT Edge nepotřebují žádné příchozí připojení, protože veškerá komunikace s IoT Hub se provádí prostřednictvím odchozích připojení. Zařízení brány jsou odlišná, protože potřebují přijímat zprávy ze svých podřízených zařízení. Pokud je brána firewall mezi zařízeními pro příjem dat a zařízením brány, musí být komunikace také možná přes bránu firewall.

Aby mohl scénář brány fungovat, musí být aspoň jeden z podporovaných protokolů centra IoT Edge otevřený pro příchozí provoz ze zařízení se systémem. Podporované protokoly jsou MQTT, AMQP, HTTPS, MQTT přes WebSockets a AMQP přes objekty WebSockets.

| Port | Protocol (Protokol) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="route-messages-from-downstream-devices"></a>Směrování zpráv ze zařízení pro příjem dat

Modul runtime IoT Edge může směrovat zprávy odesílané ze zařízení pro příjem dat stejně jako zprávy odesílané moduly. Tato funkce umožňuje provádět analýzy v modulu spuštěném v bráně před odesláním dat do cloudu.

V současné době je způsob směrování zpráv odesílaných pomocí navazujících zařízení odlišený od zpráv odesílaných moduly. Zprávy odesílané modulem All obsahují vlastnost systému nazvanou **connectionModuleId** , ale zprávy odesílané ze zařízení pro příjem dat nejsou. Klauzuli WHERE trasy můžete použít k vyloučení všech zpráv, které obsahují tuto vlastnost systému.

Níže uvedený postup slouží jako příklad, který odešle zprávy ze všech podřízených zařízení do modulu s `ai_insights`názvem a následně z `ai_insights` IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Další informace o směrování zpráv najdete v tématu [nasazení modulů a vytváření tras](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Povolit rozšířenou offline operaci

Od verze sady IoT Edge runtime v [1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) se dá zařízení brány a zařízení, která se k němu připojují, nakonfigurovat pro rozšířené operace offline.

Díky této funkci se můžou místní moduly nebo zařízení se systémem pro příjem dat podle potřeby znovu ověřit u IoT Edge zařízení a vzájemně komunikovat pomocí zpráv a metod, i když se odpojíte od služby IoT Hub. Další informace najdete v tématu [vysvětlení rozšířených funkcí offline pro IoT Edge zařízení, moduly a podřízená zařízení](offline-capabilities.md).

Chcete-li povolit rozšířené možnosti offline, navažte vztah mezi nadřazenými a podřízenými zařízeními mezi zařízením IoT Edge brány a zařízeními pro příjem dat, která se k němu připojí. Tyto kroky jsou podrobněji vysvětleny v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge pracující jako transparentní bránu, musíte nakonfigurovat zařízení pro příjem dat tak, aby důvěřovala bráně a odesílala do ní zprávy. Pokračujte tím, že na [Azure IoT Hub ověříte zařízení pro příjem dat](how-to-authenticate-downstream-device.md) s dalšími kroky v části nastavení vašeho transparentního scénáře brány.
