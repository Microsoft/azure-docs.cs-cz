---
title: Filtry připojení Azure IoT Hub IP | Microsoft Docs
description: Jak používat IP filtrování bloku připojení z konkrétní IP adresy pro do služby Azure IoT hub. Můžete blokovat připojení z jednotlivých nebo rozsahy IP adres.
author: BeatriceOltean
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: boltean
ms.openlocfilehash: fa44fd21eadb910ce90523b46332505c7303751e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635963"
---
# <a name="use-ip-filters"></a>Použití filtrů IP

Zabezpečení je důležitým aspektem jakékoli řešení IoT podle Azure IoT Hub. Někdy je třeba explicitně zadat IP adresy, ze kterých se zařízení můžou připojit jako součást konfigurace zabezpečení. _Filtr IP_ funkce umožňuje konfigurovat pravidla pro odmítat nebo přijetí provoz z konkrétní adresy IPv4.

## <a name="when-to-use"></a>Kdy je použít

Existují dvě určité případy použití, pokud je užitečné pro blokování koncové body centra IoT pro určité IP adresy:

- Služby IoT hub by měl přijímat přenosy pouze ze zadaného rozsahu IP adres a všem ostatním odmítnout. Například používáte služby IoT hub s [Azure Expressroute] vytvářet privátní připojení mezi služby IoT hub a v místní infrastruktuře.
- Budete muset odmítnout provoz z IP adresy, které byly identifikovány jako podezřelou Správce centra IoT.

## <a name="how-filter-rules-are-applied"></a>Používání pravidel filtru

Pravidla filtr IP se používají na úrovni služby IoT Hub. Proto pravidla filtru IP pro všechna připojení ze zařízení a back-end aplikace používající libovolný protokol pro podporované.

Všechny pokus o připojení z IP adresy, který odpovídá pravidlu předávaní IP ve službě IoT hub přijímá neoprávněným stavový kód 401 a popis. Zpráva odpovědi není zmiňuje pravidlo IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení **filtr IP** mřížky na portálu služby IoT hub je prázdný. Toto výchozí nastavení znamená, že vaše Centrum přijímá připojení jakékoli IP adresy. Toto výchozí nastavení je ekvivalentní pravidlo, které přijímá 0.0.0.0/0 rozsah IP adres.

![Nastavení filtru IP výchozí služby IoT Hub][img-ip-filter-default]

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidat nebo upravit pravidlo filtru IP

Když přidáte pravidlo filtru IP, budete vyzváni k následující hodnoty:

- **Název pravidla filtru IP** , musí být jedinečný, velká a malá písmena, alfanumerické řetězec až 128 znaků. Pouze ASCII 7bitového alfanumerické znaky plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` jsou podmínky přijaty.
- Vyberte **odmítnout** nebo **přijmout** jako **akce** pravidla filtru IP.
- Zadejte adresu samostatného protokolu IPv4, nebo blok IP adres v notaci CIDR. Například v CIDR zápis 192.168.100.0/22 představuje adresy IPv4 1024 z 192.168.100.0 192.168.103.255.

![Přidání pravidla filtru IP do služby IoT hub][img-ip-filter-add-rule]

Po uložení pravidla se zobrazí výstraha upozorněním, že probíhá aktualizace.

![Oznámení o ukládání pravidlo filtru IP][img-ip-filter-save-new-rule]

**Přidat** možnost je vypnuta, když se dostanete maximálně 10 pravidla filtru IP.

Dvakrát klikněte na řádek, který obsahuje pravidlo, můžete upravit existující pravidlo.

> [!NOTE]
> Odmítat IP adresy můžete zabránit jiné služby Azure (například Azure Stream Analytics, virtuální počítače Azure nebo Průzkumníka zařízení na portálu) v interakci s centrem IoT.

> [!WARNING]
> Používáte-li ke čtení zpráv ze služby IoT hub s povoleným filtrováním IP Azure Stream Analytics (ASA), můžete použijte v připojovacím řetězci ASA kompatibilní s centrem událostí název a koncový bod služby IoT Hub.

## <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP

Pokud chcete odstranit pravidlo filtru IP, vyberte jeden nebo více pravidel v mřížce a klikněte na **odstranit**.

![Odstranění pravidla filtru IP centra IoT][img-ip-filter-delete-rule]

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použity v zadaném pořadí a první pravidlo, které odpovídá IP adrese Určuje akci, přijměte nebo odmítněte.

Například pokud chcete přijmout adresy v rozsahu 192.168.100.0/22 a všem ostatním odmítnout, musí přijmout prvního pravidla v mřížce 192.168.100.0/22 rozsah adres. Další pravidlo by měl zamítnout všechny adresy pomocí 0.0.0.0/0 rozsahu.

Můžete změnit pořadí pravidel filtru IP v mřížce klepnutím na tlačítko se třemi tečkami svislé na začátek řádku a pomocí přetahování a vyřadit.

Chcete-li uložit nové pořadí pravidel filtru IP, klikněte na tlačítko **Uložit**.

![Změna pořadí pravidel filtru IP centra IoT][img-ip-filter-rule-order]

## <a name="next-steps"></a>Další postup

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

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
[Azure Expressroute]:  https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-metrics]: iot-hub-metrics.md
