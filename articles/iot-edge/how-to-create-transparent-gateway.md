---
title: Vytvořit zařízení transparentní brány – Azure IoT Edge | Microsoft Docs
description: Použít zařízení Azure IoT Edge jako transparentní bránu, která může zpracovávat informace ze zařízení pro příjem dat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ae01fc2ef8761305c2096904471ce75b69d1150d
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048402"
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

Pro zařízení pro příjem dat může být libovolná aplikace nebo platforma, která má vytvořenou identitu pomocí cloudové služby [Azure IoT Hub](../iot-hub/index.yml) . Tyto aplikace často používají [sadu SDK pro zařízení Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Navazující zařízení by mohlo být i aplikace běžící na samotném zařízení IoT Edge brány. IoT Edge zařízení ale nemůže být podřízená bráně IoT Edge.

Můžete vytvořit jakoukoli infrastrukturu certifikátů, která umožňuje důvěryhodnost potřebnou pro topologii zařízení a brány. V tomto článku se předpokládáme, že použijete stejné nastavení certifikátu, které byste použili k povolení [zabezpečení CA x. 509](../iot-hub/iot-hub-x509ca-overview.md) v IoT Hub, což zahrnuje certifikát CA x. 509, který je přidružený ke konkrétnímu centru IoT (KOŘENová CA služby IoT Hub), sérii certifikátů podepsaných touto certifikační autoritou a certifikační autoritou pro IoT Edge zařízení.

>[!NOTE]
>Termín *kořenového certifikátu certifikační autority* , který se používá v těchto článcích, se vztahuje k veřejnému certifikátu veřejného klíče certifikační autority infrastruktury veřejných klíčů, a ne nutně k certifikátu kořenové certifikační autority. V mnoha případech je ve skutečnosti veřejný certifikát zprostředkující certifikační autority.

Následující kroky vás provedou procesem vytvoření certifikátů a jejich instalací do správných míst v bráně. K vygenerování certifikátů můžete použít libovolný počítač a pak je zkopírovat do zařízení IoT Edge.

## <a name="prerequisites"></a>Požadované součásti

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
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` ani
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Pokud jste tyto soubory vytvořili na jiném počítači, zkopírujte je do zařízení IoT Edge.

3. Na zařízení IoT Edge otevřete konfigurační soubor démona zabezpečení.
   * Systému `C:\ProgramData\iotedge\config.yaml`
   * Linux `/etc/iotedge/config.yaml`

4. V souboru vyhledejte část **certifikáty** a zadejte identifikátory URI souborů pro tyto tři soubory jako hodnoty pro následující vlastnosti:
   * **device_ca_cert**: certifikát certifikační autority zařízení
   * **device_ca_pk**: privátní klíč certifikační autority zařízení
   * **trusted_ca_certs**: certifikát kořenové certifikační autority

5. Uložte soubor a zavřete ho.

6. Restartujte IoT Edge.
   * Systému `Restart-Service iotedge`
   * Linux `sudo systemctl restart iotedge`

## <a name="deploy-edgehub-and-route-messages"></a>Nasazení edgeHub a směrování zpráv

Podřízená zařízení odesílají telemetrii a zprávy do zařízení brány, kde je modul centra IoT Edge zodpovědný za směrování informací do jiných modulů nebo pro IoT Hub. Pokud chcete pro tuto funkci připravit zařízení brány, ujistěte se, že:

* Do zařízení se nasadí modul IoT Edge hub.

  Při první instalaci IoT Edge na zařízení se automaticky spustí pouze jeden systémový modul: Agent IoT Edge. Jakmile vytvoříte první nasazení zařízení, spustí se i druhý systémový modul, Centrum IoT Edge. Pokud na vašem zařízení není modul **edgeHub** spuštěný, vytvořte nasazení pro vaše zařízení.

* Modul IoT Edge hub má nastavené trasy pro zpracování příchozích zpráv z podřízených zařízení.

  Zařízení brány musí mít k dispozici trasu pro zpracování zpráv ze zařízení pro příjem dat, jinak se tyto zprávy nezpracují. Zprávy můžete odeslat do modulů na zařízení brány nebo přímo do IoT Hub.

Pokud chcete nasadit modul centra IoT Edge a nakonfigurovat ho pomocí tras pro zpracování příchozích zpráv z podřízených zařízení, postupujte takto:

1. Na webu Azure Portal přejděte do svého centra IoT.

2. Přejít na **IoT Edge** a vybrat IoT Edge zařízení, které chcete použít jako bránu.

3. Vyberte možnost **nastavit moduly**.

4. Na stránce **moduly** můžete přidat libovolné moduly, které chcete nasadit do zařízení brány. Pro účely tohoto článku se zaměřujeme na konfiguraci a nasazení modulu edgeHub, který není nutné explicitně nastavit na této stránce.

5. Vyberte **Další: trasy**.

6. Na stránce **trasy** se ujistěte, že existuje trasa pro zpracování zpráv přicházejících ze zařízení pro příjem dat. Příklad:

   * Trasa, která odesílá všechny zprávy, ať už z modulu, nebo ze zařízení pro příjem dat, na IoT Hub:
       * **Název**: `allMessagesToHub`
       * **Hodnota**: `FROM /messages/* INTO $upstream`

   * Trasa, která odesílá všechny zprávy ze všech podřízených zařízení do IoT Hub:
      * **Název**: `allDownstreamToHub`
      * **Hodnota**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      Tato trasa funguje, protože na rozdíl od zpráv z IoT Edgech modulů nejsou zprávy ze zařízení se systémem pro příjem dat k nim přidružena ID modulu. Použití klauzule **WHERE** trasy umožňuje vyfiltrovat všechny zprávy s touto vlastností systému.

      Další informace o směrování zpráv najdete v tématu [nasazení modulů a vytváření tras](./module-composition.md#declare-routes).

7. Po vytvoření trasy nebo tras vyberte **zkontrolovat + vytvořit**.

8. Na stránce **Revize + vytvořit** vyberte **vytvořit**.

## <a name="open-ports-on-gateway-device"></a>Otevřít porty na zařízení brány

Zařízení Standard IoT Edge nepotřebují žádné příchozí připojení, protože veškerá komunikace s IoT Hub se provádí prostřednictvím odchozích připojení. Zařízení brány jsou odlišná, protože potřebují přijímat zprávy ze svých podřízených zařízení. Pokud je brána firewall mezi zařízeními pro příjem dat a zařízením brány, musí být komunikace také možná přes bránu firewall.

Aby mohl scénář brány fungovat, musí být aspoň jeden z podporovaných protokolů centra IoT Edge otevřený pro příchozí provoz ze zařízení se systémem. Podporované protokoly jsou MQTT, AMQP, HTTPS, MQTT přes WebSockets a AMQP přes objekty WebSockets.

| Port | Protokol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="enable-extended-offline-operation"></a>Povolit rozšířenou offline operaci

Od [verze 1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) modulu runtime IoT Edge se zařízení brány a zařízení, která se k němu připojují, dají nakonfigurovat pro rozšířené operace offline.

Díky této funkci se můžou místní moduly nebo zařízení se systémem pro příjem dat podle potřeby znovu ověřit u IoT Edge zařízení a vzájemně komunikovat pomocí zpráv a metod, i když se odpojíte od služby IoT Hub. Další informace najdete v tématu [vysvětlení rozšířených funkcí offline pro IoT Edge zařízení, moduly a podřízená zařízení](offline-capabilities.md).

Chcete-li povolit rozšířené možnosti offline, navažte vztah mezi nadřazenými a podřízenými zařízeními mezi zařízením IoT Edge brány a zařízeními pro příjem dat, která se k němu připojí. Tyto kroky jsou podrobněji vysvětleny v dalším článku této série a [ověřují zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Další kroky

Když teď máte zařízení IoT Edge nastavené jako transparentní bránu, musíte nakonfigurovat zařízení pro příjem dat tak, aby důvěřovala bráně a odesílala do ní zprávy. Pokračujte tím, že na [Azure IoT Hub ověříte zařízení pro příjem dat](how-to-authenticate-downstream-device.md) s dalšími kroky v části nastavení vašeho transparentního scénáře brány.