---
title: Vytvoření zařízení s transparentní bránou – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Použití zařízení Azure IoT Edge jako transparentní brány, která dokáže zpracovávat informace z podřizovaných zařízení
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687165"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána

Tento článek obsahuje podrobné pokyny pro konfiguraci zařízení IoT Edge tak, aby fungovalo jako transparentní brána pro ostatní zařízení pro komunikaci s službou IoT Hub. Tento článek používá termín *Brána IoT Edge* odkazovat na zařízení IoT Edge nakonfigurované jako transparentní brána. Další informace najdete [v tématu Jak lze zařízení IoT Edge použít jako bránu](./iot-edge-as-gateway.md).

>[!NOTE]
>Aktuálně:
>
> * Zařízení s podporou okrajů se nemohou připojit k bránám IoT Edge.
> * Zařízení pro příjem dat nemohou nahrávat soubory.

Existují tři obecné kroky k nastavení úspěšného připojení transparentní brány. Tento článek popisuje první krok:

1. **Zařízení brány musí být schopno bezpečně se připojit k zařízením pro příjem dat, přijímat komunikaci z podřazených zařízení a směrovat zprávy na správné místo určení.**
2. Zařízení pro příjem dat musí mít identitu zařízení, aby bylo možné ověřit pomocí služby IoT Hub a vědět, jak komunikovat prostřednictvím svého zařízení brány. Další informace najdete [v tématu Ověření navazujícího zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Zařízení pro příjem dat se musí bezpečně připojit k zařízení brány. Další informace najdete [v tématu Připojení podřízeného zařízení k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Aby zařízení fungovalo jako brána, musí být schopno bezpečně se připojit ke svým zařízením pro příjem dat. Azure IoT Edge umožňuje používat infrastrukturu veřejných klíčů (PKI) k nastavení zabezpečeného připojení mezi zařízeními. V tomto případě povolujeme zařízení pro příjem dat připojení k zařízení IoT Edge, které funguje jako transparentní brána. Aby bylo zachováno přiměřené zabezpečení, mělo by zařízení pro příjem dat potvrdit identitu zařízení brány. Tato kontrola identity zabraňuje připojení zařízení k potenciálně škodlivým bránám.

Podřízené zařízení ve scénáři transparentní brány může být libovolná aplikace nebo platforma, která má identitu vytvořenou pomocí cloudové služby [Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub) V mnoha případech tyto aplikace používají [azure iot zařízení SDK](../iot-hub/iot-hub-devguide-sdks.md). Pro všechny praktické účely může být zařízení pro příjem dat dokonce aplikace spuštěná na samotném zařízení brány IoT Edge. Zařízení IoT Edge však nemůže být po proudu brány IoT Edge.

Můžete vytvořit libovolnou infrastrukturu certifikátů, která umožní důvěryhodnost požadovanou pro topologii brány zařízení. V tomto článku předpokládáme stejné nastavení certifikátu, které byste použili k povolení [zabezpečení certifikační autority X.509](../iot-hub/iot-hub-x509ca-overview.md) v centru IoT Hub, které zahrnuje certifikát Certifikační autority X.509 přidružený ke konkrétnímu centru IoT hub (kořenová certifikační autorita centra IoT), řadu certifikátů podepsaných touto certifikační autoritou a certifikační autoritu pro zařízení IoT Edge.

![Nastavení certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termín "kořenová certifikační autorita" používaný v tomto článku odkazuje na nejvyšší veřejný certifikát autority řetězce certifikátů PKI a ne nutně kořenový certifikát syndikované certifikační autority. V mnoha případech se ve skutečnosti jedná o zprostředkující veřejný certifikát certifikační autority.

Démon zabezpečení IoT Edge používá certifikát certifikační autority zařízení IoT Edge k podepsání certifikátu certifikační autority pracovního vytížení, který zase podepisuje certifikát serveru pro centrum IoT Edge. Brána představuje certifikát serveru do zařízení pro příjem dat během zahájení připojení. Zařízení pro příjem dat zkontroluje, zda je certifikát serveru součástí řetězu certifikátů, který se shrnuje do kořenového certifikátu certifikační autority. Tento proces umožňuje zařízení pro příjem dat k potvrzení, že brána pochází z důvěryhodného zdroje. Další informace najdete [v tématu Zjistěte, jak Azure IoT Edge používá certifikáty](iot-edge-certs.md).

Následující kroky vás provedou procesem vytváření certifikátů a jejich instalace na správných místech brány. Ke generování certifikátů můžete použít libovolný počítač a potom je zkopírovat do zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge nakonfigurované s [produkčními certifikáty](how-to-manage-device-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení edgeHubu do brány

Při první instalaci IoT Edge na zařízení se automaticky spustí pouze jeden systémový modul: agent IoT Edge. Jakmile vytvoříte první nasazení více zařízení, druhý systémový modul, i centrum IoT Edge, se spustí také.

Centrum IoT Edge je zodpovědné za příjem příchozích zpráv z podřízeného zařízení a jejich směrování na další cíl. Pokud modul **edgeHub** neběží na vašem zařízení, vytvořte počáteční nasazení pro vaše zařízení. Nasazení bude vypadat prázdné, protože nepřidáte žádné moduly, ale zajistí, že jsou spuštěny oba systémové moduly.

Moduly spuštěné na zařízení můžete zkontrolovat podrobnosti jeho zařízení na webu Azure Portal, zobrazit stav zařízení v sadě `iotedge list` Visual Studio nebo Visual Studio Code nebo spustit příkaz na samotném zařízení.

Pokud je modul **edgeAgent** spuštěn bez modulu **edgeHub,** použijte následující kroky:

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Přejděte na **IoT Edge** a vyberte zařízení IoT Edge, které chcete použít jako bránu.

3. Vyberte **možnost Nastavit moduly**.

4. Vyberte **Další**.

5. Na stránce **Zadat trasy** byste měli mít výchozí trasu, která odesílá všechny zprávy ze všech modulů do centra IoT Hub. Pokud tomu tak není, přidejte následující kód a vyberte **Next** (Další).

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. Na stránce **Předlohy Revize** vyberte **Odeslat**.

## <a name="open-ports-on-gateway-device"></a>Otevření portů na zařízení brány

Standardní zařízení IoT Edge nepotřebují žádné příchozí připojení, aby fungovala, protože veškerá komunikace s IoT Hubem se provádí prostřednictvím odchozích připojení. Zařízení brány se liší, protože potřebují přijímat zprávy ze svých zařízení pro příjem dat. Pokud je brána firewall mezi zařízeními pro příjem dat a zařízením brány, musí být komunikace možná i přes bránu firewall.

Aby mohl scénář brány fungovat, musí být alespoň jeden z podporovaných protokolů centra IoT Edge otevřený pro příchozí provoz z podřízeného zařízení. Podporované protokoly jsou MQTT, AMQP, HTTPS, MQTT přes WebSockets a AMQP přes WebSockets.

| Port | Protocol (Protokol) |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Směrování zpráv z podřazených zařízení

Modul runtime IoT Edge může směrovat zprávy odeslané z podřízených zařízení, stejně jako zprávy odeslané moduly. Tato funkce umožňuje provádět analýzy v modulu spuštěném na bráně před odesláním dat do cloudu.

V současné době je způsob směrování zpráv odeslaných zařízeními příjem dat jejich odlišením od zpráv odeslaných moduly. Zprávy odeslané moduly všechny obsahují vlastnost systému s názvem **connectionModuleId,** ale zprávy odeslané podřízené zařízení ne. Klauzuli WHERE postupu můžete použít k vyloučení všech zpráv, které obsahují tuto vlastnost systému.

Níže uvedená trasa je příkladem, který by odesílat `ai_insights`zprávy z `ai_insights` libovolného zařízení příjem dat do modulu s názvem a potom z ioT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Další informace o směrování zpráv naleznete v [tématu Nasazení modulů a vytvoření tras](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Povolit rozšířenou offline operaci

Počínaje [verzí v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) runtime IoT Edge lze zařízení brány a zařízení pro připojení k němu nakonfigurovat pro rozšířený offline provoz.

Díky této funkci mohou místní moduly nebo podřízené zařízení znovu ověřit pomocí zařízení IoT Edge podle potřeby a komunikovat mezi sebou pomocí zpráv a metod i při odpojení od služby IoT hub. Další informace najdete [v tématu Principy rozšířených offline funkcí pro zařízení, moduly a podřízená zařízení IoT Edge](offline-capabilities.md).

Chcete-li povolit rozšířené možnosti offline, vytvořte vztah mezi nadřazeným a podřízeným zařízením mezi bránou IoT Edge a zařízeními pro příjem dat, která se k němu připojí. Tyto kroky jsou podrobněji vysvětleny v [části Ověření navazujícího zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte zařízení IoT Edge, které funguje jako transparentní brána, musíte nakonfigurovat podřazená zařízení tak, aby důvěřovala bráně a odesílala jí zprávy. Pokračujte na [ověření navazující zařízení do služby Azure IoT Hub](how-to-authenticate-downstream-device.md) pro další kroky při nastavování scénáře transparentní brány.
