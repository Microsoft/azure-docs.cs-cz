---
title: Požadavky na systém Microsoft Azure Data Box Edge | Dokumentace Microsoftu
description: Další informace o softwaru a síťové požadavky pro Azure Data Box hranici
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 52d2061262fd04e68ed13aac8932c23b7074f83e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113766"
---
# <a name="azure-data-box-edge-system-requirements-preview"></a>Azure Data Box Edge požadavky na systém (preview)

Tento článek popisuje důležité systémové požadavky pro vaše řešení Microsoft Azure Data Box Edge a klienti připojení k Azure Data Box Edge. Doporučujeme, abyste si informace o pečlivě před nasazením hranici pole Data. Můžete se vrátit zpět k těmto informacím podle potřeby během nasazení a následná operace.

Požadavky na systém pro okraji pole Data patří:

- **Požadavky na software pro hostitele** -popisuje podporované platformy, prohlížeče pro místní konfiguraci, uživatelského rozhraní, klienti SMB a veškeré další požadavky pro klienty, kteří přístup k zařízení.
- **Požadavky na síť pro zařízení** – poskytuje informace o žádné požadavky na síť pro operaci fyzickém zařízení.

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před nasazením tohoto řešení si prosím přečtěte [podmínky použití verze Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="supported-os-for-clients-connected-to-device"></a>Podporovaný operační systém pro klienty připojené k zařízení

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Podporované protokoly klientům přístup k zařízení

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Účty úložiště podporuje

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Typy podporovaných úložišť

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Podporované prohlížeče pro místního webového uživatelského rozhraní

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Požadavky na síťové porty

### <a name="port-requirements-for-data-box-edge"></a>Požadavky na porty pro Data Box Edge

Následující tabulka uvádí porty, které je potřeba otevřít v bráně firewall povolit pro protokol SMB, cloud nebo přenos pro správu. V této tabulce *v* nebo *příchozí* odkazuje na směru, které mají přístup příchozí požadavky klienta na vaše zařízení. *Navýšení kapacity* nebo *odchozí* odkazuje na směru, ve kterém zařízení Data Box Edge odesílá data externě, nad rámec nasazení, například odchozí k Internetu.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Požadavky na porty pro IoT Edge

Azure IoT Edge umožňuje odchozí komunikaci z místní hraniční zařízení do cloudu Azure pomocí podporovaných protokolů služby IoT Hub. Příchozí komunikace je pouze požadovaná u konkrétních scénářů, kde je Azure IoT Hub potřeba doručit bez vyžádání zprávy na zařízení Azure IoT Edge (například v cloudu pro zasílání zpráv zařízení).

Použijte následující tabulku pro konfigurace portů pro servery, které hostují modul runtime Azure IoT Edge:

| Port č. | Snížení nebo navýšení kapacity | Rozsah portů | Požaduje se | Doprovodné materiály |
|----------|-----------|------------|----------|----------|
| TCP 5671 (AMQP)| Výstup       | WAN        | Ano      | Výchozí komunikační protokol pro IoT Edge. Musí být otevřený, pokud Azure IoT Edge není nakonfigurovaná pro jiné podporované protokoly nebo je požadovaný komunikační protokol AMQP. <br>5672 pro AMQP nepodporuje IoT Edge. <br>Blokovat tento port při použití Azure IoT Edge různé IoT Hub podporovaný protokol. |
| TCP 443 (HTTPS)| Výstup       | WAN        | Ano      | Odchozí otevřen pro IoT Edge zřizování. Pokud máte transparentní brány pomocí zařízení typu list, které může odesílat požadavky metod. Port 443 v takovém případě nemusí být otevřený, aby externí sítě připojit ke službě IoT Hub nebo poskytuje služby IoT Hub pomocí Azure IoT Edge. Proto může být příchozí pravidlo s omezeným přístupem pouze otevřít příchozí z interní sítě. |
| TCP 5671 (AMQP) | V        |            | Ne       | Příchozí připojení by se zablokovat.|
| TCP 443 (HTTPS) | V        |            | V některých případech naleznete v tématu komentáře | Příchozí připojení by měl být otevřen pouze pro konkrétní scénáře. Pokud jiným protokolem než HTTP protokoly, jako AMQP, MQTT se nedá nakonfigurovat, nelze odesílat zprávy přes WebSockets přes port 443. |

Podrobnější informace, přejděte na [brány Firewall a pravidla konfigurace portů pro nasazení IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Vzory adres URL pro pravidla brány firewall

Správci sítě můžou často konfigurace pravidla brány firewall na Upřesnit na základě vzory adres URL pro filtrování příchozího a odchozího provozu. Vaše zařízení Data Box Edge a služba závisí na jiné aplikace od Microsoftu, jako je Azure Service Bus, Azure Active Directory Access Control, účty úložiště a serverům Microsoft Update. Vzory adres URL, které jsou přidružené k těmto aplikacím je možné nakonfigurovat pravidla brány firewall. Je důležité pochopit, že můžete změnit vzory adres URL, které jsou přidružené k těmto aplikacím. Tyto změny vyžadují správce sítě, monitorovat a aktualizovat pravidla brány firewall na hranici jako pole Data a v případě potřeby.

Doporučujeme nastavit pravidla brány firewall pro odchozí provoz, založené na okraji pole Data liberally pevné IP adresy, ve většině případů. Ale můžete použít následující informace pro nastavení pravidla brány firewall na pokročilé, které budete potřebovat pro vytvoření zabezpečeného prostředí.

> [!NOTE]
> - Zařízení (zdroj) IP adresy musí být vždy nastavená na všechna rozhraní sítě povolenou podporu cloudu.
> - Cílové IP adresy musí být nastavená na [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Vzory adres URL pro funkce brány

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Vzory adres URL pro výpočetní funkce

| Vzor adresy URL                      | Komponenta nebo funkce                     |   |
|----------------------------------|---------------------------------------------|---|
| `https://mcr.microsoft.com`<br></br>https://\*.cdn.mscr.io | Microsoft registru kontejneru (povinné)               |   |
| https://\*.azurecr.io                     | Registry kontejnerů osobní a třetí strany (volitelné) |   |
| https://\*.azure-devices.net              | Přístup k službě IoT Hub (povinné)                             |   |

## <a name="internet-bandwidth"></a>Šířka pásma sítě Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Další krok

- [Nasazení Azure Data Box hranici](data-box-Edge-deploy-prep.md)
