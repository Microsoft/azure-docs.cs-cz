---
title: Vytvořit zařízení transparentní brány – Azure IoT Edge | Microsoft Docs
description: Použít zařízení Azure IoT Edge jako transparentní bránu, která může zpracovávat informace ze zařízení pro příjem dat
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f7f05fb84ff6cbe320e8f479912bdcdefdc41021
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201657"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Tento článek poskytuje podrobné pokyny ke konfiguraci IoT Edge zařízení pro fungování jako transparentní brány pro jiná zařízení, která budou komunikovat s IoT Hub. V tomto článku se používá pojem *IoT Edge brána* , která odkazuje na IoT Edge zařízení nakonfigurované jako transparentní bránu. Další informace najdete v tématu [jak se dá zařízení IoT Edge použít jako brána](./iot-edge-as-gateway.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

>[!NOTE]
>V IoT Edge verzích 1,1 a starších se IoT Edge zařízení nemůže nacházet IoT Edge bráně.
>
>Podřízená zařízení nemůžou používat nahrávání souborů.

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Podřízená zařízení nemůžou používat nahrávání souborů.

::: moniker-end

Existují tři obecné kroky k nastavení úspěšného transparentního připojení brány. Tento článek popisuje první krok:

1. **Nakonfigurujte zařízení brány jako server tak, aby se k nim mohli připojit podřízená zařízení bezpečně. Nastavte bránu tak, aby přijímala zprávy ze zařízení pro příjem dat, a směrovat je do správného umístění.**
2. Vytvořte identitu zařízení pro zařízení pro příjem dat, aby se mohla ověřit pomocí IoT Hub. Nakonfigurujte zařízení pro příjem zpráv, aby odesílala zprávy přes zařízení brány. Postup najdete v tématu [ověření zařízení pro příjem dat do Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Připojte zařízení pro příjem dat k zařízení brány a začněte odesílat zprávy. Postup najdete v tématu [připojení zařízení pro příjem dat k bráně Azure IoT Edge](how-to-connect-downstream-device.md).

Aby zařízení fungovalo jako brána, musí se bezpečně připojit ke svým podřízeným zařízením. Azure IoT Edge umožňuje použít infrastrukturu veřejných klíčů (PKI) k nastavení zabezpečených připojení mezi zařízeními. V tomto případě umožníme, aby se pro zařízení s IoT Edge připojila k zařízení, které funguje jako transparentní brána. Aby bylo možné zajistit přiměřené zabezpečení, musí zařízení pro příjem dat potvrdit identitu zařízení brány. Tato kontrolu identity zabraňuje zařízením v připojení k potenciálně škodlivým branám.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Pro zařízení pro příjem dat může být libovolná aplikace nebo platforma, která má vytvořenou identitu pomocí cloudové služby [Azure IoT Hub](../iot-hub/index.yml) . Tyto aplikace často používají [sadu SDK pro zařízení Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Navazující zařízení by mohlo být i aplikace běžící na samotném zařízení IoT Edge brány. IoT Edge zařízení ale nemůže být podřízená bráně IoT Edge.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Pro zařízení pro příjem dat může být libovolná aplikace nebo platforma, která má vytvořenou identitu pomocí cloudové služby [Azure IoT Hub](../iot-hub/index.yml) . Tyto aplikace často používají [sadu SDK pro zařízení Azure IoT](../iot-hub/iot-hub-devguide-sdks.md). Navazující zařízení by mohlo být i aplikace běžící na samotném zařízení IoT Edge brány.
:::moniker-end
<!-- end 1.2 -->

Můžete vytvořit jakoukoli infrastrukturu certifikátů, která umožňuje důvěryhodnost potřebnou pro topologii zařízení a brány. V tomto článku se předpokládáme, že použijete stejné nastavení certifikátu, které byste použili k povolení [zabezpečení CA x. 509](../iot-hub/iot-hub-x509ca-overview.md) v IoT Hub, což zahrnuje certifikát CA x. 509, který je přidružený ke konkrétnímu centru IoT (KOŘENová CA služby IoT Hub), sérii certifikátů podepsaných touto certifikační autoritou a certifikační autoritou pro IoT Edge zařízení.

>[!NOTE]
>Termín *kořenového certifikátu certifikační autority* , který se používá v těchto článcích, se vztahuje k veřejnému certifikátu veřejného klíče certifikační autority infrastruktury veřejných klíčů, a ne nutně k certifikátu kořenové certifikační autority. V mnoha případech je ve skutečnosti veřejný certifikát zprostředkující certifikační autority.

Následující kroky vás provedou procesem vytvoření certifikátů a jejich instalací do správných míst v bráně. K vygenerování certifikátů můžete použít libovolný počítač a pak je zkopírovat do zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

Zařízení se systémem Linux nebo Windows s nainstalovaným IoT Edge.

Pokud zařízení nemáte připravené, můžete ho vytvořit na virtuálním počítači Azure. Postupujte podle kroků v části [nasazení prvního IoT Edge modulu do virtuálního počítače se systémem Linux](quickstart-linux.md) a vytvořte IoT Hub, vytvořte virtuální počítač a nakonfigurujte modul runtime IoT Edge.

## <a name="set-up-the-device-ca-certificate"></a>Nastavení certifikátu certifikační autority zařízení

Všechny IoT Edge brány potřebují nainstalovaný certifikát pro certifikační autoritu zařízení. Démon zabezpečení IoT Edge používá certifikát certifikační autority pro IoT Edge zařízení k podepsání certifikátu certifikační autority pro úlohy, který zase podepisuje certifikát serveru pro Centrum IoT Edge. Brána prezentuje svůj certifikát serveru pro zařízení pro příjem dat během zahájení připojení. Zařízení pro příjem dat kontroluje, zda je certifikát serveru součástí řetězu certifikátů, který se shrnuje do certifikátu kořenové certifikační autority. V rámci tohoto procesu může zařízení pro příjem dat potvrdit, že brána pochází z důvěryhodného zdroje. Další informace najdete v tématu [Vysvětlení způsobu, jakým Azure IoT Edge používá certifikáty](iot-edge-certs.md).

![Nastavení certifikátu brány](./media/how-to-create-transparent-gateway/gateway-setup.png)

Certifikát kořenové certifikační autority a certifikát certifikační autority zařízení (s jeho privátním klíčem) se musí nacházet na zařízení IoT Edge brány a nakonfigurované v konfiguračním souboru IoT Edge. Pamatujte, že v tomto případě *kořenový certifikát certifikační autority* znamená nejvyšší certifikační autoritu pro tento scénář IoT Edge. Certifikát certifikační autority zařízení brány a certifikáty zařízení pro příjem dat musí být zahrnuté do stejného kořenového certifikátu certifikační autority.

>[!TIP]
>Postup instalace certifikátu kořenové certifikační autority a certifikátu certifikační autority zařízení do zařízení IoT Edge je podrobněji vysvětlen v tématu [Správa certifikátů v IoT Edgem zařízení](how-to-manage-device-certificates.md).

Připravte si následující soubory:

* Certifikát kořenové certifikační autority
* Certifikát certifikační autority zařízení
* Privátní klíč certifikační autority zařízení

V produkčních scénářích byste tyto soubory měli vytvořit s vlastní certifikační autoritou. Pro vývojové a testovací scénáře můžete použít ukázkové certifikáty.

Pokud nemáte vlastní certifikační autoritu a chcete použít ukázkové certifikáty, použijte pokyny v tématu [Vytvoření ukázkových certifikátů k otestování IoT Edge funkcí zařízení](how-to-create-test-certificates.md) k vytvoření souborů. Na této stránce je třeba provést následující kroky:

   1. Začněte tím, že nastavíte skripty pro generování certifikátů na vašem zařízení.
   2. Vytvořte certifikát kořenové certifikační autority. Na konci těchto pokynů budete mít soubor certifikátu kořenové certifikační autority:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.
   3. Vytvoření certifikátů certifikační autority IoT Edge zařízení. Na konci těchto pokynů budete mít certifikát certifikační autority zařízení a jeho privátní klíč:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` ani
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

Pokud jste certifikáty vytvořili na jiném počítači, zkopírujte je do zařízení IoT Edge a pak pokračujte dalšími kroky.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Na zařízení IoT Edge otevřete konfigurační soubor démona zabezpečení.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. V souboru vyhledejte část **nastavení certifikátu** . Odkomentujte čtyři řádky začínající **certifikáty:** a zadejte identifikátory URI souborů pro tyto tři soubory jako hodnoty pro následující vlastnosti:
   * **device_ca_cert**: certifikát certifikační autority zařízení
   * **device_ca_pk**: privátní klíč certifikační autority zařízení
   * **trusted_ca_certs**: certifikát kořenové certifikační autority

   Ujistěte se, že v **certifikátech** nejsou žádné předchozí prázdné znaky a že ostatní řádky jsou odsazené o dva mezery.

1. Uložte soubor a zavřete ho.

1. Restartujte IoT Edge.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`
:::moniker-end
<!-- end 1.1 -->

<!--1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Na zařízení IoT Edge otevřete konfigurační soubor: `/etc/aziot/config.toml`

   >[!TIP]
   >Pokud konfigurační soubor ještě v zařízení neexistuje, `/etc/aziot/config.toml.edge.template` vytvořte ho pomocí jako šablonu.

1. Najděte `trust_bundle_cert` parametr. Odkomentujte tento řádek a zadejte identifikátor URI souboru certifikátu kořenové certifikační autority na vašem zařízení.

1. Vyhledejte `[edge_ca]` část souboru. Odkomentujte tři řádky v této části a zadejte identifikátory URI souborů k certifikátu a klíčovým souborům jako hodnoty pro následující vlastnosti:
   * **certifikát: certifikát** certifikační autority zařízení
   * **PK**: privátní klíč certifikační autority zařízení

1. Uložte soubor a zavřete ho.

1. Provedené změny použijte.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

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

6. Na stránce **trasy** se ujistěte, že existuje trasa pro zpracování zpráv přicházejících ze zařízení pro příjem dat. Například:

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

## <a name="next-steps"></a>Další kroky

Když teď máte zařízení IoT Edge nastavené jako transparentní bránu, musíte nakonfigurovat zařízení pro příjem dat tak, aby důvěřovala bráně a odesílala do ní zprávy. Pokračujte tím, že na [Azure IoT Hub ověříte zařízení pro příjem dat](how-to-authenticate-downstream-device.md) s dalšími kroky v části nastavení vašeho transparentního scénáře brány.