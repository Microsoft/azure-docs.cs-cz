---
title: Azure IoT Hub IP připojení filtry | Dokumentace Microsoftu
description: Použití filtru bloku připojení z konkrétní IP adresy IP adres pro službu Azure IoT hub. Můžete blokovat připojení z jednotlivé nebo rozsahy IP adres.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: d549127b5cbdb3a94e435e753592f3227cb95f3a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232210"
---
# <a name="use-ip-filters"></a>Použití filtrů IP adres

Zabezpečení je důležitou součástí jakékoli řešení IoT, které jsou založené na službě Azure IoT Hub. Někdy je potřeba explicitně zadat IP adresy, ze kterých se můžete připojit zařízení jako součást konfigurace zabezpečení. *Filtr IP* funkce umožňuje konfigurovat pravidla pro odmítnutí nebo přijímá provoz z konkrétní adresy IPv4.

## <a name="when-to-use"></a>Kdy je použít

Existují dva zvláštní případy použití při je užitečné pro blokování koncové body IoT Hubu pro určité IP adresy:

* Služby IoT hub by měla přijímat provoz pouze ze zadaného rozsahu IP adres a odmítnout všechno ostatní. Například používáte službu IoT hub s [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) můžete vytvářet privátní připojení mezi služby IoT hub a v místní infrastruktuře.

* Budete muset odmítnout provoz z IP adresy, které byly identifikovány jako podezřelou správcem centra IoT.

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP se použijí na úrovni služby IoT Hub. Takže pravidla filtru IP platí pro všechna připojení ze zařízení a back endové aplikace pomocí libovolného protokolu pro podporované.

Jakékoli pokusy o připojení z IP adresy, která odpovídá předávaní pravidlu IP ve službě IoT hub přijímá neoprávněným stavový kód 401 a popis. Zprávy s odpovědí není zmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení **filtr IP** mřížky na portálu pro službu IoT hub je prázdný. Toto výchozí nastavení znamená, že vaše Centrum akceptuje připojení z libovolné IP adresy. Toto výchozí nastavení je ekvivalentní pravidlo, které přijímá tento rozsah IP adres 0.0.0.0/0.

![Nastavení filtru IP výchozí služby IoT Hub](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidat nebo upravit pravidlo filtru IP

Když přidáte pravidlo filtru IP, zobrazí se výzva pro následující hodnoty:

* **Název pravidla filtru IP** , který musí být jedinečný, velká a malá písmena, alfanumerický řetězec do 128 znaků. Pouze ASCII 7 bitů alfanumerických znaků plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` nepřijmou.

* Vyberte **odmítnout** nebo **přijmout** jako **akce** pro pravidla filtru IP.

* Zadejte jednu adresu IPv4 nebo blok IP adres v zápisu CIDR. Například v CIDR notation 192.168.100.0/22 představuje 1024 IPv4 adres od 192.168.100.0 do 192.168.103.255.

![Přidání pravidla filtru IP do služby IoT hub](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Po uložení pravidla se zobrazí výstraha s upozorněním, že probíhá aktualizace.

![Oznámení o uložení pravidlo filtru IP](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

**Přidat** možnost je vypnuta, když dosáhnou maximálního počtu 10 pravidla filtru IP.

Poklikáním na řádek, který obsahuje pravidlo můžete upravit stávající pravidlo.

> [!NOTE]
> Odmítnutí IP adresy můžete zabránit ostatních služeb Azure (jako je Azure Stream Analytics, Azure Virtual Machines nebo Device Explorer na portálu) interakci s centrem IoT.

> [!WARNING]
> Pokud používáte Azure Stream Analytics (ASA) ke čtení zpráv ze služby IoT hub s povolené filtrování protokolu IP, použijte název kompatibilní s centrem událostí a koncový bod služby IoT Hub v připojovacím řetězci Azure Stream Analytics.

## <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP

Pokud chcete odstranit pravidlo filtru IP, vyberte v tabulce a klikněte na jedno nebo více pravidel **odstranit**.

![Odstranění pravidla filtru IP centra IoT](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Načítají a aktualizují filtrů IP adres pomocí Azure CLI

Filtry IP adres služby IoT Hub můžete načíst a aktualizovat prostřednictvím [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). 

Pokud chcete načíst aktuální filtry IP služby IoT Hub, spusťte:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Vrátí objekt JSON, ve kterém se zobrazí vaše existující filtry IP `properties.ipFilterRules` klíč:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Chcete-li přidat nový filtr IP pro vaše Centrum IoT, spusťte:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Pokud chcete odebrat existující filtr IP ve službě IoT Hub, spusťte:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Všimněte si, že `<ipFilterIndexToRemove>` musí odpovídat pořadí filtrů IP adres ve službě IoT Hub `properties.ipFilterRules`.


## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Načítají a aktualizují filtrů IP adres pomocí Azure Powershellu

Filtry IP adres služby IoT Hub můžete načíst a nastavit prostřednictvím [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0). 

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzureRmResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzureRmResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Aktualizovat pravidla filtru IP pomocí rozhraní REST

Může také načíst a úprava filtru IP služby IoT Hub pomocí koncového bodu REST poskytovatel prostředků Azure. Zobrazit `properties.ipFilterRules` v [metodu createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).


## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použity v zadaném pořadí a první pravidlo, které odpovídá IP adrese Určuje akci, přijmout nebo odmítnout.

Například pokud chcete adresy v rozsahu 192.168.100.0/22 přijmout nebo odmítnout všechno ostatní, by měla přijímat prvního pravidla v mřížce 192.168.100.0/22 rozsah adres. Další pravidlo by měl odmítnout všechny adresy pomocí rozsahu 0.0.0.0/0.

Můžete změnit pořadí pravidel filtru IP v mřížce kliknutím na tři svislé tečky na začátku řádku a pomocí přetažení a vyřadit.

Chcete-li uložit nové pořadí pravidel filtru IP, klikněte na tlačítko **Uložit**.

![Změnit pořadí pravidel filtru IP centra IoT](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Další postup

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Monitorování operací](iot-hub-operations-monitoring.md)
* [Metriky služby IoT Hub](iot-hub-metrics.md)