---
title: Vytvořit zařízení transparentní brány – Azure IoT Edge | Microsoft Docs
description: Použít zařízení Azure IoT Edge jako transparentní bránu, která může zpracovávat informace ze zařízení pro příjem dat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0155294777e1d732e5ff3874102b90049d9a123d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782581"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána

Tento článek poskytuje podrobné pokyny ke konfiguraci IoT Edge zařízení pro fungování jako transparentní brány pro jiná zařízení, která budou komunikovat s IoT Hub. V tomto článku se používá pojem *IoT Edge brána* , která odkazuje na IoT Edge zařízení nakonfigurované jako transparentní bránu. Další informace najdete v tématu [jak se dá zařízení IoT Edge použít jako brána](./iot-edge-as-gateway.md).

>[!NOTE]
>Aktuálně
>
> * Zařízení s podporou Edge se nemůžou připojit k IoT Edge branám.
> * Podřízená zařízení nemůžou používat nahrávání souborů.

Existují tři obecné kroky k nastavení úspěšného transparentního připojení brány. Tento článek popisuje první krok:

1. **Nakonfigurujte zařízení brány jako server tak, aby se k nim mohli připojit podřízená zařízení bezpečně. Nastavte bránu tak, aby přijímala zprávy ze zařízení pro příjem dat, a směrovat je do správného umístění.**
2. Vytvořte identitu zařízení pro zařízení pro příjem dat, aby se mohla ověřit pomocí IoT Hub. Nakonfigurujte zařízení pro příjem zpráv, aby odesílala zprávy přes zařízení brány. Další informace najdete v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Připojte zařízení pro příjem dat k zařízení brány a začněte odesílat zprávy. Další informace najdete v tématu [připojení zařízení pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Aby zařízení fungovalo jako brána, musí se bezpečně připojit ke svým podřízeným zařízením. Azure IoT Edge umožňuje použít infrastrukturu veřejných klíčů (PKI) k nastavení zabezpečených připojení mezi zařízeními. V tomto případě umožníme, aby se pro zařízení s IoT Edge připojila k zařízení, které funguje jako transparentní brána. Aby bylo možné zajistit přiměřené zabezpečení, musí zařízení pro příjem dat potvrdit identitu zařízení brány. Tato kontrolu identity zabraňuje zařízením v připojení k potenciálně škodlivým branám.

Pro zařízení pro příjem dat může být libovolná aplikace nebo platforma, která má vytvořenou identitu pomocí cloudové služby [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) . Tyto aplikace často používají [sadu SDK pro zařízení Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Navazující zařízení by mohlo být i aplikace běžící na samotném zařízení IoT Edge brány. IoT Edge zařízení ale nemůže být podřízená bráně IoT Edge.

Můžete vytvořit jakoukoli infrastrukturu certifikátů, která umožňuje důvěryhodnost potřebnou pro topologii zařízení a brány. V tomto článku se předpokládáme, že použijete stejné nastavení certifikátu, které byste použili k povolení [zabezpečení CA x. 509](../iot-hub/iot-hub-x509ca-overview.md) v IoT Hub, což zahrnuje certifikát CA x. 509, který je přidružený ke konkrétnímu centru IoT (KOŘENová CA služby IoT Hub), sérii certifikátů podepsaných touto certifikační autoritou a certifikační autoritou pro IoT Edge zařízení.

>[!NOTE]
>Termín *kořenového certifikátu certifikační autority* , který se používá v těchto článcích, se vztahuje k veřejnému certifikátu veřejného klíče certifikační autority infrastruktury veřejných klíčů, a ne nutně k certifikátu kořenové certifikační autority. V mnoha případech je ve skutečnosti veřejný certifikát zprostředkující certifikační autority.

Následující kroky vás provedou procesem vytvoření certifikátů a jejich instalací do správných míst v bráně. K vygenerování certifikátů můžete použít libovolný počítač a pak je zkopírovat do zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

Zařízení se systémem Linux nebo Windows s nainstalovaným IoT Edge.

## <a name="set-up-the-device-ca-certificate"></a>Nastavení certifikátu certifikační autority zařízení

Všechny IoT Edge brány potřebují nainstalovaný certifikát pro certifikační autoritu zařízení. Démon zabezpečení IoT Edge používá certifikát certifikační autority pro IoT Edge zařízení k podepsání certifikátu certifikační autority pro úlohy, který zase podepisuje certifikát serveru pro Centrum IoT Edge. Brána prezentuje svůj certifikát serveru pro zařízení pro příjem dat během zahájení připojení. Zařízení pro příjem dat kontroluje, zda je certifikát serveru součástí řetězu certifikátů, který se shrnuje do certifikátu kořenové certifikační autority. V rámci tohoto procesu může zařízení pro příjem dat potvrdit, že brána pochází z důvěryhodného zdroje. Další informace najdete v tématu [Vysvětlení způsobu, jakým Azure IoT Edge používá certifikáty](iot-edge-certs.md).

![Nastavení certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

Certifikát kořenové certifikační autority a certifikát certifikační autority zařízení (s jeho privátním klíčem) se musí nacházet na zařízení IoT Edge brány a nakonfigurované v souboru IoT Edge config. yaml. Pamatujte, že v tomto případě *kořenový certifikát certifikační autority* znamená nejvyšší certifikační autoritu pro tento scénář IoT Edge. Certifikát certifikační autority zařízení brány a certifikáty zařízení pro příjem dat musí být zahrnuté do stejného kořenového certifikátu certifikační autority.

>[!TIP]
>Postup instalace certifikátu kořenové certifikační autority a certifikátu certifikační autority zařízení do zařízení IoT Edge je podrobněji vysvětlen v tématu [Správa certifikátů v IoT Edgem zařízení](how-to-manage-device-certificates.md).

Připravte si následující soubory:

* Certifikát kořenové certifikační autority
* Certifikát certifikační autority zařízení
* Privátní klíč certifikační autority zařízení

V produkčních scénářích byste tyto soubory měli vytvořit s vlastní certifikační autoritou. Pro vývojové a testovací scénáře můžete použít ukázkové certifikáty.

1. Pokud používáte ukázkové certifikáty, použijte k vytvoření souborů následující sadu kroků:
   1. [Vytvořte certifikát kořenové certifikační autority](how-to-create-test-certificates.md#create-root-ca-certificate). Na konci těchto pokynů budete mít soubor certifikátu kořenové certifikační autority:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.

   2. [Vytvořte certifikát certifikační autority IoT Edge zařízení](how-to-create-test-certificates.md#create-iot-edge-device-ca-certificates). Na konci těchto pokynů budete mít dva soubory, certifikát certifikační autority zařízení a jeho privátní klíč:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem`ani
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Pokud jste tyto soubory vytvořili na jiném počítači, zkopírujte je do zařízení IoT Edge.

3. Na zařízení IoT Edge otevřete konfigurační soubor démona zabezpečení.
   * Systému`C:\ProgramData\iotedge\config.yaml`
   * Linux`/etc/iotedge/config.yaml`

4. V souboru vyhledejte část **certifikáty** a zadejte identifikátory URI souborů pro tyto tři soubory jako hodnoty pro následující vlastnosti:
   * **device_ca_cert**: certifikát certifikační autority zařízení
   * **device_ca_pk**: privátní klíč certifikační autority zařízení
   * **trusted_ca_certs**: certifikát kořenové certifikační autority

5. Uložte soubor a zavřete ho.

6. Restartujte IoT Edge.
   * Systému`Restart-Service iotedge`
   * Linux`sudo systemctl restart iotedge`

## <a name="deploy-edgehub-to-the-gateway"></a>Nasazení edgeHub do brány

Při první instalaci IoT Edge na zařízení se automaticky spustí pouze jeden systémový modul: Agent IoT Edge. Po vytvoření prvního nasazení pro zařízení se spustí i druhý systémový modul, Centrum IoT Edge.

Centrum IoT Edge zodpovídá za příjem příchozích zpráv ze zařízení pro příjem dat a jejich směrování do dalšího cíle. Pokud na vašem zařízení není modul **edgeHub** spuštěný, vytvořte počáteční nasazení pro vaše zařízení. Nasazení bude vypadat prázdné, protože nepřidáte žádné moduly, ale zajistěte, aby byly spuštěny oba systémové moduly.

Můžete zkontrolovat, které moduly jsou spuštěny na zařízení, kontrolou podrobností o zařízení v Azure Portal, zobrazením stavu zařízení v aplikaci Visual Studio nebo Visual Studio Code nebo spuštěním příkazu `iotedge list` na samotném zařízení.

Pokud je modul **edgeAgent** spuštěný bez modulu **edgeHub** , použijte následující postup:

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Přejít na **IoT Edge** a vybrat IoT Edge zařízení, které chcete použít jako bránu.

3. Vyberte možnost **nastavit moduly**.

4. Vyberte **Další: trasy**.

5. Na stránce **trasy** byste měli mít výchozí trasu, která odesílá všechny zprávy, ať už z modulu nebo ze zařízení pro příjem dat, na IoT Hub. Pokud ne, přidejte novou trasu s následujícími hodnotami a pak vyberte **zkontrolovat + vytvořit**:
   * **Název**:`route`
   * **Hodnota**:`FROM /messages/* INTO $upstream`

6. Na stránce **Revize + vytvořit** vyberte **vytvořit**.

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

Níže uvedený postup slouží jako příklad, který odešle zprávy ze všech podřízených zařízení do modulu s názvem `ai_insights` a následně z `ai_insights` IoT Hub.

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

Od [verze 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) modulu runtime IoT Edge se zařízení brány a zařízení, která se k němu připojují, dají nakonfigurovat pro rozšířené operace offline.

Díky této funkci se můžou místní moduly nebo zařízení se systémem pro příjem dat podle potřeby znovu ověřit u IoT Edge zařízení a vzájemně komunikovat pomocí zpráv a metod, i když se odpojíte od služby IoT Hub. Další informace najdete v tématu [vysvětlení rozšířených funkcí offline pro IoT Edge zařízení, moduly a podřízená zařízení](offline-capabilities.md).

Chcete-li povolit rozšířené možnosti offline, navažte vztah mezi nadřazenými a podřízenými zařízeními mezi zařízením IoT Edge brány a zařízeními pro příjem dat, která se k němu připojí. Tyto kroky jsou podrobněji vysvětleny v dalším článku této série a [ověřují zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Další kroky

Když teď máte zařízení IoT Edge nastavené jako transparentní bránu, musíte nakonfigurovat zařízení pro příjem dat tak, aby důvěřovala bráně a odesílala do ní zprávy. Pokračujte tím, že na [Azure IoT Hub ověříte zařízení pro příjem dat](how-to-authenticate-downstream-device.md) s dalšími kroky v části nastavení vašeho transparentního scénáře brány.
