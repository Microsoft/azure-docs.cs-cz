---
title: Azure IoT Hub IP připojení filtry | Dokumentace Microsoftu
description: Použití filtru bloku připojení z konkrétní IP adresy IP adres pro službu Azure IoT hub. Můžete blokovat připojení z jednotlivé nebo rozsahy IP adres.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: 864af9cae35912d95f2c0bf0b574a5ca2404a608
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190637"
---
# <a name="use-ip-filters"></a>Pomocí filtrů IP adres

Zabezpečení je důležitou součástí jakékoli řešení IoT, které jsou založené na službě Azure IoT Hub. Někdy je potřeba explicitně zadat IP adresy, ze kterých se můžete připojit zařízení jako součást konfigurace zabezpečení. _Filtr IP_ funkce umožňuje konfigurovat pravidla pro odmítnutí nebo přijímá provoz z konkrétní adresy IPv4.

## <a name="when-to-use"></a>Kdy je použít

Existují dva zvláštní případy použití při je užitečné pro blokování koncové body IoT Hubu pro určité IP adresy:

- Služby IoT hub by měla přijímat provoz pouze ze zadaného rozsahu IP adres a odmítnout všechno ostatní. Například používáte službu IoT hub s [Azure Express Route] můžete vytvářet privátní připojení mezi služby IoT hub a v místní infrastruktuře.
- Budete muset odmítnout provoz z IP adresy, které byly identifikovány jako podezřelou správcem centra IoT.

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP se použijí na úrovni služby IoT Hub. Takže pravidla filtru IP platí pro všechna připojení ze zařízení a back endové aplikace pomocí libovolného protokolu pro podporované.

Jakékoli pokusy o připojení z IP adresy, která odpovídá předávaní pravidlu IP ve službě IoT hub přijímá neoprávněným stavový kód 401 a popis. Zprávy s odpovědí není zmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení **filtr IP** mřížky na portálu pro službu IoT hub je prázdný. Toto výchozí nastavení znamená, že vaše Centrum akceptuje připojení jakékoli IP adresy. Toto výchozí nastavení je ekvivalentní pravidlo, které přijímá tento rozsah IP adres 0.0.0.0/0.

![Nastavení filtru IP výchozí služby IoT Hub][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidat nebo upravit pravidlo filtru IP

Když přidáte pravidlo filtru IP, zobrazí se výzva pro následující hodnoty:

- **Název pravidla filtru IP** , který musí být jedinečný, velká a malá písmena, alfanumerický řetězec do 128 znaků. Pouze ASCII 7 bitů alfanumerických znaků plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` nepřijmou.
- Vyberte **odmítnout** nebo **přijmout** jako **akce** pro pravidla filtru IP.
- Zadejte jednu adresu IPv4 nebo blok IP adres v zápisu CIDR. Například v CIDR notation 192.168.100.0/22 představuje 1024 IPv4 adres od 192.168.100.0 do 192.168.103.255.

![Přidání pravidla filtru IP do služby IoT hub][img-ip-filter-add-rule]

Po uložení pravidla se zobrazí výstraha s upozorněním, že probíhá aktualizace.

![Oznámení o uložení pravidlo filtru IP][img-ip-filter-save-new-rule]

**Přidat** možnost je vypnuta, když dosáhnou maximálního počtu 10 pravidla filtru IP.

Poklikáním na řádek, který obsahuje pravidlo můžete upravit stávající pravidlo.

> [!NOTE]
> Odmítnutí IP adresy můžete zabránit ostatních služeb Azure (jako je Azure Stream Analytics, Azure Virtual Machines nebo Device Explorer na portálu) interakci s centrem IoT.

> [!WARNING]
> Pokud používáte Azure Stream Analytics (ASA) ke čtení zpráv ze služby IoT hub s povolené filtrování protokolu IP, použijte název kompatibilní s centrem událostí a koncový bod služby IoT Hub v připojovacím řetězci Azure Stream Analytics.

## <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP

Pokud chcete odstranit pravidlo filtru IP, vyberte v tabulce a klikněte na jedno nebo více pravidel **odstranit**.

![Odstranění pravidla filtru IP centra IoT][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použity v zadaném pořadí a první pravidlo, které odpovídá IP adrese Určuje akci, přijmout nebo odmítnout.

Například pokud chcete adresy v rozsahu 192.168.100.0/22 přijmout nebo odmítnout všechno ostatní, by měla přijímat prvního pravidla v mřížce 192.168.100.0/22 rozsah adres. Další pravidlo by měl odmítnout všechny adresy pomocí rozsahu 0.0.0.0/0.

Můžete změnit pořadí pravidel filtru IP v mřížce kliknutím na tři svislé tečky na začátku řádku a pomocí přetažení a vyřadit.

Chcete-li uložit nové pořadí pravidel filtru IP, klikněte na tlačítko **Uložit**.

![Změnit pořadí pravidel filtru IP centra IoT][img-ip-filter-rule-order]

## <a name="next-steps"></a>Další postup

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

- [Monitorování operací][lnk-monitor]
- [Metriky služby IoT Hub][lnk-metrics]

<!-- Images -->
[img-ip-filter-default]: ./media/iot-hub-ip-filtering/ip-filter-default.png
[img-ip-filter-add-rule]: ./media/iot-hub-ip-filtering/ip-filter-add-rule.png
[img-ip-filter-save-new-rule]: ./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png
[img-ip-filter-delete-rule]: ./media/iot-hub-ip-filtering/ip-filter-delete-rule.png
[img-ip-filter-rule-order]: ./media/iot-hub-ip-filtering/ip-filter-rule-order.png


<!-- Links -->

[IoT Hub developer guide]: iot-hub-devguide.md
[Azure Express Route]:  https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
