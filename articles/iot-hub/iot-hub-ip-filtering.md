---
title: Filtry připojení IP pro Azure IoT Hub | Microsoft Docs
description: Jak používat filtrování protokolu IP k blokování připojení z konkrétních IP adres pro do služby Azure IoT Hub. Můžete zablokovat připojení z individuálních nebo rozsahů IP adres.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: robinsh
ms.openlocfilehash: 1ba3c89ea4f964f9e6fd5f902aab29a83a058f25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074726"
---
# <a name="use-ip-filters"></a>Použití filtrů IP adres

Zabezpečení je důležitým aspektem řešení IoT založeného na Azure IoT Hub. Někdy musíte explicitně zadat IP adresy, ze kterých se zařízení můžou připojit, jako součást konfigurace zabezpečení. Funkce *filtrování IP adres* umožňuje konfigurovat pravidla pro zamítnutí nebo přijetí provozu z konkrétních adres IPv4.

## <a name="when-to-use"></a>Kdy je použít

Existují dva konkrétní případy použití, když je užitečné zablokovat IoT Hub koncových bodů pro určité IP adresy:

* Vaše centrum IoT by mělo přijímat přenosy jenom z určeného rozsahu IP adres a zamítnout všechno ostatní. Například používáte službu IoT Hub se službou [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) k vytváření privátních připojení mezi službou IoT Hub a místní infrastrukturou.

* Musíte odmítnout provoz z IP adres, které správce služby IoT Hub identifikoval jako podezřelé.

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP jsou použita na úrovni služby IoT Hub. Proto se pravidla filtru IP použijí na všechna připojení ze zařízení a back-endové aplikace s využitím jakéhokoli podporovaného protokolu. Nicméně klienti načtou přímo z [integrovaného koncového bodu kompatibilního](iot-hub-devguide-messages-read-builtin.md) s centrem událostí (ne prostřednictvím připojovacího řetězce IoT Hub) nejsou vázány na pravidla filtru IP. 

Jakýkoli pokus o připojení z IP adresy, která odpovídá pravidlu odmítnutí protokolu IP ve službě IoT Hub, obdrží ne401 autorizovaný kód stavu a popis. Zpráva odpovědi nezmiňuje pravidlo protokolu IP. Odmítání IP adres může zabránit jiným službám Azure, jako je Azure Stream Analytics, Azure Virtual Machines nebo Device Explorer v Azure Portal z interakce se službou IoT Hub.

> [!NOTE]
> Pokud musíte použít Azure Stream Analytics (ASA) ke čtení zpráv ze služby IoT Hub s povoleným filtrem IP adres, použijte název a koncový bod služby IoT Hub, který je kompatibilní s centrem událostí, a ručně přidejte [vstup datového proudu Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) do ASA.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení je mřížka **filtru IP** na portálu pro Centrum IoT prázdná. Toto výchozí nastavení znamená, že vaše centrum akceptuje připojení z libovolné IP adresy. Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0.

Pokud se chcete dostat na stránku nastavení filtru IP adres, vyberte **sítě**, **veřejný přístup**a pak zvolte **vybrané rozsahy IP adres**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidat nebo upravit pravidlo filtru IP

Pokud chcete přidat pravidlo filtru IP, vyberte **+ Přidat pravidlo filtru IP**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

Po výběru možnosti **Přidat pravidlo filtru protokolu IP**zadejte pole.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

* Zadejte **název** pravidla filtru IP. Musí se jednat o jedinečný řetězec bez rozlišení velkých a malých písmen, který je dlouhý až 128 znaků. Jsou přijímány pouze alfanumerické znaky ASCII a 7 `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` .

* Zadejte jednu adresu IPv4 nebo blok IP adres v zápisu CIDR. Například v zápisu CIDR 192.168.100.0/22 představuje adresy IPv4 1024 z 192.168.100.0 do 192.168.103.255.

* Jako **akci** pro pravidlo filtru IP vyberte možnost **povoluje** nebo **zablokovat** .

Po vyplnění polí vyberte **Uložit** a uložte pravidlo. Zobrazí se upozornění oznamující, že probíhá aktualizace.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

Možnost **Přidat** je zakázaná, když dosáhnete maximálního počtu 10 pravidel filtru IP.

Pokud chcete upravit stávající pravidlo, vyberte data, která chcete změnit, proveďte změnu a pak kliknutím na **Uložit** uložte úpravy.

## <a name="delete-an-ip-filter-rule"></a>Odstraní pravidlo filtru IP.

Pokud chcete odstranit pravidlo filtru IP, vyberte na tomto řádku ikonu odpadkového koše a pak vyberte **Uložit**. Pravidlo se odebere a změna se uloží.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Načtení a aktualizace filtrů IP adres pomocí Azure CLI

Filtry IP IoT Hub můžete načíst a aktualizovat prostřednictvím rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Chcete-li načíst aktuální filtry IP IoT Hub, spusťte příkaz:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Tato akce vrátí objekt JSON, ve kterém jsou uvedené existující filtry IP adres pod `properties.ipFilterRules` klíčem:

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

Chcete-li přidat nový filtr IP adres pro IoT Hub, spusťte příkaz:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Pokud chcete ve svém IoT Hub odebrat existující filtr IP adres, spusťte:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Všimněte si, že `<ipFilterIndexToRemove>` musí odpovídat řazení filtrů IP adres v IoT Hub `properties.ipFilterRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Načtení a aktualizace filtrů IP adres pomocí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Filtry IP IoT Hub můžete načíst a nastavit prostřednictvím [Azure PowerShell](/powershell/azure/).

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

## <a name="update-ip-filter-rules-using-rest"></a>Aktualizace pravidel filtru IP pomocí REST

Můžete také načíst a upravit filtr IP IoT Hub pomocí koncového bodu REST poskytovatele prostředků Azure. Viz `properties.ipFilterRules` v tématu [Metoda createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

Například pokud chcete přijmout adresy v rozsahu 192.168.100.0/22 a odmítnout všechno ostatní, první pravidlo v mřížce by mělo přijmout rozsah adres 192.168.100.0/22. Další pravidlo by mělo odmítnout všechny adresy pomocí rozsahu 0.0.0.0/0.

Pořadí pravidel filtru IP v mřížce můžete změnit kliknutím na tři svislé tečky na začátku řádku a pomocí přetažení.

Pokud chcete uložit nové pořadí pravidel filtru IP, klikněte na **Uložit**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-rule-order.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

## <a name="next-steps"></a>Další kroky

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub metriky](iot-hub-metrics.md)
