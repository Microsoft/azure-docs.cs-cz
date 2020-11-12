---
title: Brány pro zařízení pro příjem dat – Azure IoT Edge | Microsoft Docs
description: Pomocí Azure IoT Edge můžete vytvořit transparentní, neprůhledné nebo proxy zařízení brány proxy, které odesílá data z více podřízených zařízení do cloudu nebo je místně zpracuje.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 83e8089073f7e7e7634ddf00f7276e12aaf645b0
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536434"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>Jak se dá zařízení IoT Edge využít jako brána

Zařízení IoT Edge můžou fungovat jako brány a zajistit tak připojení mezi ostatními zařízeními v síti a IoT Hub.

Modul IoT Edge hub funguje jako IoT Hub, takže může zpracovávat připojení ze všech zařízení, která mají identitu s IoT Hub, včetně dalších zařízení IoT Edge. Tento typ vzoru brány se nazývá *transparentní* , protože zprávy mohou být předávány ze zařízení pro příjem dat, a to IoT Hub, jako kdyby mezi nimi existovala brána.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Od verze 1,2 IoT Edge můžou transparentní brány zpracovávat podřízená připojení z jiných zařízení IoT Edge.
::: moniker-end

Pro zařízení, která se nemůžou připojovat k IoT Hub vlastním, IoT Edge brány můžou toto připojení poskytnout. Tento typ vzoru brány se nazývá *Překlad* , protože IoT Edge zařízení musí před přesměrováním do IoT Hub provést zpracování příchozích zpráv pro příchozí zařízení. Tyto scénáře vyžadují u IoT Edge brány další moduly, které zpracovávají kroky zpracování.

Modely transparentních a překladatelských bran se vzájemně vylučují. Jedno zařízení IoT Edge může fungovat jako transparentní brána i jako brána pro překlad.

Všechny vzory bran poskytují následující výhody:

* **Analýzy na hraničních** zařízeních – používají se místně služby AI ke zpracování dat přicházejících ze zařízení, která se neodesílají do cloudu, bez odesílání telemetrie s plnou věrností. Najděte a reagují na přehledy místně a odešlete jenom podmnožinu dat, která IoT Hub.
* **Izolace zařízení** po jednotlivých zařízeních – zařízení brány může chránit všechna zařízení pro příjem dat před expozicí Internetu. Může se navázat mezi sítí provozní technologie (OT), která nemá připojení, a sítí informačních technologií (IT), která poskytuje přístup k webu. Podobně se zařízení, která nemají možnost se připojit k IoT Hub sám o sobě, můžou místo toho připojit k zařízení brány.
* **Multiplexing připojení** – všechna zařízení, která se připojují k IoT Hub přes IoT Edge bránu, používají stejné základní připojení.
* **Vyhlazení provozu** – IoT Edge zařízení automaticky implementuje exponenciální omezení rychlosti, pokud IoT Hub omezuje provoz, zatímco se zprávy ukládají místně. Díky této výhodě je vaše řešení odolné vůči špičkám v provozu.
* **Podpora offline** – zařízení brány ukládá zprávy a zdvojené aktualizace, které nelze doručit IoT Hub.

## <a name="transparent-gateways"></a>Transparentní brány

V modelu transparentní brány se zařízení, která se teoreticky můžou připojit k IoT Hub se můžou místo toho připojit k zařízení brány. Zařízení pro příjem dat mají vlastní IoT Hub identity a připojují se pomocí protokolů MQTT nebo AMQP. Brána jednoduše předává komunikaci mezi zařízeními a IoT Hubem. Zařízení i uživatelé, kteří s nimi pracují prostřednictvím IoT Hub, nevědí, že brána Mediating jejich komunikaci. Nedostatečné povědomí znamená, že brána je považována za *transparentní*.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

IoT Edge zařízení nemohou být podřízená bráně IoT Edge.

![Diagram – model transparentní brány](./media/iot-edge-as-gateway/edge-as-gateway-transparent.png)

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

Počínaje verzí 1.2.0 se zařízení můžou přes transparentní brány připojit IoT Edge.

<!-- TODO add a downstream IoT Edge device to graphic -->

::: moniker-end

### <a name="parent-and-child-relationships"></a>Vztahy nadřazenosti a podřízenosti

Vztahy transparentní brány v IoT Hub deklarujete tak, že nastavíte IoT Edge bránu jako *nadřazenou* *podřízenou položku podřízeného* zařízení, která se k němu připojuje.

Vztah nadřazenosti/podřízenosti je v konfiguraci brány v třech bodech:

#### <a name="cloud-identities"></a>Cloudové identity

Všechna zařízení ve scénáři transparentní brány potřebují cloudové identity, aby se mohly ověřit IoT Hub. Když vytváříte nebo aktualizujete identitu zařízení, můžete nastavit nadřazená nebo podřízená zařízení zařízení. Tato konfigurace autorizuje nadřazené zařízení brány, aby zpracovával ověřování pro svá podřízená zařízení.

Podřízená zařízení můžou mít jenom jednu nadřazenou položku. Každý nadřazený objekt může mít až 100 podřízených položek.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Zařízení IoT Edge můžou být rodiče i podřízené položky v transparentních relacích brány. Je možné vytvořit hierarchii více IoT Edgech zařízení. Nejvyšší uzel hierarchie brány může mít až pět generací podřízených objektů. Například zařízení IoT Edge může mít pět vrstev IoT Edgech zařízení, která jsou propojená jako podřízené položky. Ale zařízení IoT Edge v páté generaci nemůže mít žádné podřízené položky, IoT Edge ani jinak.
::: moniker-end

#### <a name="gateway-discovery"></a>Zjišťování brány

Podřízené zařízení musí být schopné najít své nadřazené zařízení v místní síti. Nakonfigurujte zařízení brány pomocí názvu **hostitele** , buď plně kvalifikovaného názvu domény (FQDN) nebo IP adresy, kterou budou používat podřízená zařízení k jejímu vyhledání.

V zařízeních IoT pro příjem dat pomocí parametru **gatewayHostname** v připojovacím řetězci nasměrujte na nadřazené zařízení.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Na zařízeních IoT Edge pro příjem dat použijte parametr **parent_hostname** v souboru config. yaml a nasměrujte na nadřazené zařízení.
::: moniker-end

#### <a name="secure-connection"></a>Zabezpečené připojení

Nadřazená a podřízená zařízení také potřebují ověřovat připojení navzájem. Každé zařízení potřebuje kopii sdílené kořenové certifikační autority, kterou používají podřízená zařízení k ověření, že se připojují ke správné bráně.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
Pokud se k sobě navzájemně připojí více IoT Edge bran v hierarchii brány, musí všechna zařízení v hierarchii používat jeden řetěz certifikátů.
::: moniker-end

### <a name="device-capabilities-behind-transparent-gateways"></a>Možnosti zařízení za transparentními branami

Všechny primitivní IoT Hub, které pracují s kanálem zasílání zpráv IoT Edge, podporují také transparentní scénáře brány. Každá brána IoT Edge obsahuje možnosti pro zprávy, které docházejí prostřednictvím služby Store a předávání.

V následující tabulce najdete informace o tom, jak se pro zařízení v porovnání se zařízeními za bránami podporují různé možnosti IoT Hub.

<!-- 1.0.10 -->
::: moniker range="iotedge-2018-06"

| Schopnost | Zařízení IoT | IoT za bránou |
| ---------- | ---------- | -------------------- |
| [Zprávy ze zařízení do cloudu (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Ano – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – podřízené IoT D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Zprávy z cloudu na zařízení (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Ano – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – C2D podřízeného IoT](./media/iot-edge-as-gateway/check-yes.png) |
| [Přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Ano – metoda Direct pro IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – podřízená metoda IoT Direct](./media/iot-edge-as-gateway/check-yes.png) |
| [Vlákna zařízení](../iot-hub/iot-hub-devguide-device-twins.md) a [vlákna v modulu](../iot-hub/iot-hub-devguide-module-twins.md) | ![Ano – vlákna IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – vlákna s podřízenými IoT](./media/iot-edge-as-gateway/check-yes.png) |
| [Nahrání souboru](../iot-hub/iot-hub-devguide-file-upload.md) | ![Ano – nahrání souboru IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Ne – nahrání podřízeného souboru IoT](./media/iot-edge-as-gateway/crossout-no.png) |

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

| Schopnost | Zařízení IoT | IoT za bránou | Zařízení IoT Edge | IoT Edge za bránou |
| ---------- | ---------- | --------------------------- | --------------- | -------------------------------- |
| [Zprávy ze zařízení do cloudu (D2C)](../iot-hub/iot-hub-devguide-messages-d2c.md) |  ![Ano – IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – podřízené IoT D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – podřízené IoT Edge D2C](./media/iot-edge-as-gateway/check-yes.png) |
| [Zprávy z cloudu na zařízení (C2D)](../iot-hub/iot-hub-devguide-messages-c2d.md) | ![Ano – IoT C2D](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – C2D podřízeného IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Bez IoT Edge C2D](./media/iot-edge-as-gateway/crossout-no.png) | ![C2D podřízených objektů bez IoT Edge](./media/iot-edge-as-gateway/crossout-no.png) |
| [Přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) | ![Ano – metoda Direct pro IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – podřízená metoda IoT Direct](./media/iot-edge-as-gateway/check-yes.png) | ![Metoda Yes-IoT Edge Direct](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – podřízená IoT Edge přímá metoda](./media/iot-edge-as-gateway/check-yes.png) |
| [Vlákna zařízení](../iot-hub/iot-hub-devguide-device-twins.md) a [vlákna v modulu](../iot-hub/iot-hub-devguide-module-twins.md) | ![Ano – vlákna IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – vlákna s podřízenými IoT](./media/iot-edge-as-gateway/check-yes.png) | ![IoT Edge vlákna Ano](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – podřízené IoT Edge vlákna](./media/iot-edge-as-gateway/check-yes.png) |
| [Nahrání souboru](../iot-hub/iot-hub-devguide-file-upload.md) | ![Ano – nahrání souboru IoT](./media/iot-edge-as-gateway/check-yes.png) | ![Ne – nahrání podřízeného souboru IoT](./media/iot-edge-as-gateway/crossout-no.png) | ![NeIoT Edge nahrávání souborů](./media/iot-edge-as-gateway/crossout-no.png) | ![NeIoT Edge nahrávání podřízeného souboru](./media/iot-edge-as-gateway/crossout-no.png) |
| Žádosti o přijetí změn image kontejneru |   |   | ![Ano – IoT Edge vyžádání obsahu kontejneru](./media/iot-edge-as-gateway/check-yes.png) | ![Ano-podřízený IoT Edge kontejner pro vyžádání obsahu](./media/iot-edge-as-gateway/check-yes.png) |
| Nahrání objektu BLOB |   |   | ![Ano – IoT Edge nahrávání objektů BLOB](./media/iot-edge-as-gateway/check-yes.png) | ![Ano – nahrání podřízeného objektu BLOB IoT Edge](./media/iot-edge-as-gateway/check-yes.png) |

**Image kontejnerů** se dají stahovat, ukládat a doručovat z nadřízených zařízení na podřízená zařízení.

**Objekty blob** , včetně sad a protokolů podpory, je možné nahrávat z podřízených zařízení do nadřazených zařízení.

::: moniker-end

## <a name="translation-gateways"></a>Brány pro překlad

Pokud se bezplatných zařízení nemůže připojit k IoT Hub, musí IoT Edge brána fungovat jako překladatel. Tento model je často nutný pro zařízení, která nepodporují MQTT, AMQP nebo HTTP. Vzhledem k tomu, že se tato zařízení nemohou připojit k IoT Hub, nemůžou se také připojit k modulu IoT Edge hub bez předběžného zpracování.

Vlastní nebo moduly třetích stran, které jsou často specifické pro hardware nebo protokol daného zařízení, je nutné nasadit do brány IoT Edge. Tyto moduly překladu přebírají příchozí zprávy a zamění je ve formátu, který lze chápat pomocí IoT Hub.

Existují dva vzory pro brány překladu: *Překlad protokolu* a *Překlad identity*.

![Diagram – vzory bran pro překlad](./media/iot-edge-as-gateway/edge-as-gateway-translation.png)

### <a name="protocol-translation"></a>Překlad protokolu

V rámci vzoru brány překladu protokolů má identita s IoT Hub jenom brána IoT Edge. Modul překladu přijímá zprávy ze zařízení pro příjem dat, překládá je na podporovaný protokol a zařízení IoT Edge odesílá zprávy jménem zařízení pro příjem dat. Všechny informace vypadají jako z jednoho zařízení a brány. Pokud cloudové aplikace chtějí analyzovat data podle jednotlivých zařízení, musí podřízená zařízení vložit do svých zpráv dodatečné identifikační údaje. Kromě toho IoT Hub primitivních hodnot, jako jsou například vlákna a přímé metody, jsou podporovány pouze pro zařízení brány, nikoli pro zařízení pro příjem dat. Brány v tomto modelu se považují za *neprůhledné* , a to na rozdíl od transparentních bran, protože zakrývají identity podřízených zařízení.

Překlad protokolu podporuje zařízení, která jsou omezená na prostředky. Mnoho stávajících zařízení vyrábí data, která můžou být v Power Business Insights. nejsou ale navržené s ohledem na cloudové připojení. Neprůhledné brány umožňují, aby tato data byla odemčená a používána v řešení IoT.

### <a name="identity-translation"></a>Překlad identity

Vzor brány pro překlad identity se sestavuje na převodech protokolů, ale brána IoT Edge taky poskytuje identitu zařízení IoT Hub jménem zařízení pro příjem dat. Modul překladu zodpovídá za porozumění protokolu používanému zařízeními pro příjem dat, poskytování identity a překladu jejich zpráv do IoT Hub primitivních hodnot. Podřízená zařízení se v IoT Hubu zobrazují jako prvotřídní zařízení s dvojčaty a metodami. Uživatel může se zařízeními v IoT Hubu pracovat a není si vědom zprostředkujícího zařízení brány.

Překlad identity poskytuje výhody překladu protokolů a navíc umožňuje úplnou správu zařízení z cloudu. Všechna zařízení v řešení IoT se zobrazí v IoT Hub bez ohledu na protokol, který používají.

### <a name="device-capabilities-behind-translation-gateways"></a>Možnosti zařízení za překlady bran

Následující tabulka vysvětluje, jak jsou IoT Hub funkce rozšířeny na zařízení se systémy pro příjem dat v obou vzorech brány pro překlad.

| Schopnost | Překlad protokolu | Překlad identity |
| ---------- | -------------------- | -------------------- |
| Identity uložené v registru IoT Hub identity | Jenom identita zařízení brány | Identity všech připojených zařízení |
| Dvojče zařízení | Pouze brána má nevlákenná zařízení a moduly. | Každé připojené zařízení má vlastní zařízení. |
| Přímé metody a zprávy z cloudu na zařízení | Cloud může adresovat jenom zařízení brány. | Cloud může adresovat každé připojené zařízení jednotlivě. |
| [Omezení IoT Hub a kvóty](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Použít na zařízení brány | Použít pro každé zařízení |

Při použití vzoru překladu protokolů budou všechna zařízení připojující prostřednictvím této brány sdílet stejnou frontu typu cloud-zařízení, která může obsahovat maximálně 50 zpráv. Tento model použijte jenom v případě, že se přes každou bránu pole připojuje málo zařízení a jejich provoz z cloudu na zařízení je nízký.

Modul runtime IoT Edge nezahrnuje možnosti protokolu ani překladu identity. Tyto vzory vyžadují vlastní moduly nebo moduly třetích stran, které jsou často specifické pro použitý hardware a protokol. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) obsahuje několik modulů překladu protokolů, ze kterých si můžete vybrat. Ukázku, která používá vzor překladu identity, najdete v tématu [Azure IoT Edge LoRaWAN Starter Kit](https://github.com/Azure/iotedge-lorawan-starterkit).

## <a name="next-steps"></a>Další kroky

Přečtěte si tři kroky nastavení transparentní brány:

* [Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](how-to-create-transparent-gateway.md)
* [Ověření podřízeného zařízení pro Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Připojení podřízeného zařízení k bráně služby Azure IoT Edge](how-to-connect-downstream-device.md)
