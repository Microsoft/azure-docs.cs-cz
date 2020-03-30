---
title: Filtry připojení IP centra Azure IoT | Dokumenty společnosti Microsoft
description: Jak pomocí filtrování IP adres blokovat připojení z konkrétních IP adres do centra Azure IoT. Můžete blokovat připojení z jednotlivých nebo rozsahů IP adres.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68414265"
---
# <a name="use-ip-filters"></a>Použití filtrů IP adres

Zabezpečení je důležitým aspektem každého řešení IoT založeného na Azure IoT Hub. Někdy je třeba explicitně zadat IP adresy, ze kterých se zařízení mohou připojit jako součást konfigurace zabezpečení. Funkce *filtru IP* umožňuje konfigurovat pravidla pro odmítnutí nebo přijetí přenosů z konkrétních adres IPv4.

## <a name="when-to-use"></a>Kdy je použít

Existují dva konkrétní případy použití, kdy je užitečné blokovat koncové body ioT hubu pro určité IP adresy:

* Vaše služba IoT hub by měla přijímat provoz pouze ze zadaného rozsahu IP adres a odmítnout všechno ostatní. Například používáte službu IoT hub s [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) k vytvoření privátních připojení mezi službou IoT hub a místní infrastrukturou.

* Je třeba odmítnout provoz z IP adres, které byly označeny jako podezřelé správcem služby IoT hub.

## <a name="how-filter-rules-are-applied"></a>Použití pravidel filtru

Pravidla filtru IP se použijí na úrovni služby Služby IoT Hub. Proto pravidla filtru IP platí pro všechna připojení ze zařízení a back-endových aplikací pomocí libovolného podporovaného protokolu.

Jakýkoli pokus o připojení z IP adresy, která odpovídá odmítnutí pravidla IP v centru IoT, obdrží neoprávněný stavový kód a popis 401. Zpráva s odpovědí neuvádí pravidlo IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení je **mřížka filtru IP** na portálu pro centrum IoT prázdná. Toto výchozí nastavení znamená, že rozbočovač přijímá připojení z libovolné adresy IP. Toto výchozí nastavení je ekvivalentní pravidlu, které přijímá rozsah adres IP 0.0.0.0/0.

![Výchozí nastavení filtru IP centra IoT Hub](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidání nebo úprava pravidla filtru IP

Chcete-li přidat pravidlo filtru IP, vyberte **možnost + Přidat pravidlo filtru IP**.

![Přidání pravidla filtru IP do centra IoT](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Po výběru **možnosti Přidat pravidlo filtru IP**vyplňte pole.

![Po výběru pravidla přidat filtr IP](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Zadejte **název** pravidla filtru IP. Musí se jednat o jedinečný alfanumerický řetězec bez velkých a malých písmen o hodnotě až 128 znaků. Jsou přijímány pouze 7bitové alfanumerické znaky `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` ASCII plus.

* Zadejte jednu adresu IPv4 nebo blok IP adres v zápisu CIDR. Například v CIDR zápis 192.168.100.0/22 představuje 1024 IPv4 adresy od 192.168.100.0 do 192.168.103.255.

* Jako **akci** pro pravidlo filtru IP vyberte **Povolit** nebo **Blokovat.**

Po vyplnění polí vyberte **Uložit,** chcete-li pravidlo uložit. Zobrazí se výstraha s upozorněním, že aktualizace probíhá.

![Oznámení o uložení pravidla filtru IP](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

Možnost **Přidat** je zakázána, pokud dosáhnete maximálně 10 pravidel filtru IP.

Chcete-li upravit existující pravidlo, vyberte data, která chcete změnit, proveďte změnu a pak vyberte **Uložit,** chcete-li úpravy uložit.

> [!NOTE]
> Odmítnutí IP adres může zabránit interakci jiných služeb Azure (jako je Azure Stream Analytics, Virtuální počítače Azure nebo Průzkumník zařízení na portálu) s centrem IoT.

> [!WARNING]
> Pokud používáte Azure Stream Analytics (ASA) ke čtení zpráv z centra IoT s povoleným filtrováním IP adres, použijte název a koncový bod kompatibilní s centrem Událostí v připojovacím řetězci ASA.

## <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP

Chcete-li odstranit pravidlo filtru IP, vyberte na tomto řádku ikonu koše a pak vyberte **Uložit**. Pravidlo je odebráno a změna uložena.

![Odstranění pravidla filtru IP centra IoT](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Načtení a aktualizace filtrů IP pomocí azure cli

Filtry IP služby IoT Hub lze načíst a aktualizovat prostřednictvím [azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Chcete-li načíst aktuální IP filtry vašeho ioT hubu, spusťte:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Tím se vrátí objekt JSON, kde jsou `properties.ipFilterRules` vaše stávající IP filtry uvedeny pod klíčem:

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

Pokud chcete přidat nový filtr IP pro ioT hub, spusťte:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Pokud chcete odebrat existující filtr IP v centru IoT Hub, spusťte:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Všimněte `<ipFilterIndexToRemove>` si, že musí odpovídat řazení IP filtrů ve vašem IoT Hub `properties.ipFilterRules`.

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Načtení a aktualizace filtrů IP pomocí Azure PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ip filtry vašeho IoT Hubu se můžou načítat a načítat přes [Azure PowerShell](/powershell/azure/overview).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Aktualizace pravidel filtru IP pomocí rest

Můžete také načíst a upravit ip filtr služby IoT Hub pomocí koncového bodu REST zprostředkovatele prostředků Azure. Viz `properties.ipFilterRules` metoda [createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použita v pořadí a první pravidlo, které odpovídá adrese IP, určuje akci přijmout nebo odmítnout.

Například pokud chcete přijmout adresy v rozsahu 192.168.100.0/22 a odmítnout vše ostatní, první pravidlo v mřížce by měl přijmout rozsah adres 192.168.100.0/22. Další pravidlo by mělo odmítnout všechny adresy pomocí rozsahu 0.0.0.0/0.

Pořadí pravidel filtru IP v mřížce můžete změnit tak, že kliknete na tři svislé tečky na začátku řádku a použijete přetažení.

Chcete-li uložit nové pořadí pravidel filtru IP, klepněte na tlačítko **Uložit**.

![Změna pořadí pravidel filtru IP centra IoT Hub](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Další kroky

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Monitorování operací](iot-hub-operations-monitoring.md)
* [Metriky IoT Hubu](iot-hub-metrics.md)
