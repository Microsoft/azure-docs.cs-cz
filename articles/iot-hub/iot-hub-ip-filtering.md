---
title: Filtry připojení IP pro Azure IoT Hub | Microsoft Docs
description: Jak používat filtrování protokolu IP k blokování připojení z konkrétních IP adres pro do služby Azure IoT Hub. Můžete zablokovat připojení z jednotlivých IP adres nebo rozsahů IP adres.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/25/2020
ms.author: robinsh
ms.openlocfilehash: a9a95c0151ed6dd3a2ad4a9d548723cdff0bcfb8
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147090"
---
# <a name="use-ip-filters"></a>Použití filtrů IP adres

Zabezpečení je důležitým aspektem řešení IoT založeného na Azure IoT Hub. Někdy je potřeba v rámci konfigurace zabezpečení explicitně zadat IP adresy, ze kterých se smějí připojovat zařízení. Funkce *filtrování IP adres* umožňuje konfigurovat pravidla pro zamítnutí nebo přijetí provozu z konkrétních adres IPv4.

## <a name="when-to-use"></a>Kdy je použít

Existují dva konkrétní případy použití, když je užitečné zablokovat IoT Hub koncových bodů pro určité IP adresy:

* Vaše centrum IoT by mělo přijímat přenosy jenom z určeného rozsahu IP adres a zamítnout všechno ostatní. Například používáte službu IoT Hub se službou [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) k vytváření privátních připojení mezi službou IoT Hub a místní infrastrukturou.

* Musíte odmítnout provoz z IP adres, které správce služby IoT Hub identifikoval jako podezřelé.

## <a name="how-filter-rules-are-applied"></a>Způsob uplatňování pravidel filtru

Pravidla filtru IP jsou použita na úrovni služby IoT Hub. Proto se pravidla filtru IP použijí na všechna připojení ze zařízení a back-endové aplikace s využitím jakéhokoli podporovaného protokolu. Nicméně klienti načtou přímo z [integrovaného koncového bodu kompatibilního](iot-hub-devguide-messages-read-builtin.md) s centrem událostí (ne prostřednictvím připojovacího řetězce IoT Hub) nejsou vázány na pravidla filtru IP. 

Jakýkoli pokus o připojení z IP adresy, která odpovídá pravidlu odmítnutí protokolu IP ve službě IoT Hub, obdrží ne401 autorizovaný kód stavu a popis. Zpráva odpovědi neinformuje o pravidle IP adres. Odmítání IP adres může zabránit jiným službám Azure, jako je Azure Stream Analytics, Azure Virtual Machines nebo Device Explorer v Azure Portal z interakce se službou IoT Hub.

> [!NOTE]
> Pokud musíte použít Azure Stream Analytics (ASA) ke čtení zpráv ze služby IoT Hub s povoleným filtrem IP adres, použijte název a koncový bod služby IoT Hub, který je kompatibilní s centrem událostí, a ručně přidejte [vstup datového proudu Event Hubs](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) do ASA.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení je mřížka **filtru IP** na portálu pro Centrum IoT prázdná. Toto výchozí nastavení znamená, že vaše centrum akceptuje připojení z libovolné IP adresy. Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0.

Pokud se chcete dostat na stránku nastavení filtru IP adres, vyberte **sítě**, **veřejný přístup**a pak zvolte **vybrané rozsahy IP adres**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidání nebo úprava pravidla filtru IP

Pokud chcete přidat pravidlo filtru IP, vyberte **+ Přidat pravidlo filtru IP adres**.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

Po výběru možnosti **Přidat pravidlo filtru IP adres** vyplňte zobrazená pole.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

* Zadejte **název** pravidla filtru IP adres. Musí se jednat o jedinečný alfanumerický řetězec s délkou do 128 znaků, ve kterém se nerozlišují malá a velká písmena. Přípustné jsou jenom 7bitové alfanumerické znaky ASCII a znaky `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Zadejte jednu IPv4 adresu nebo blok IP adres v zápisu CIDR. Například adresa 192.168.100.0/22 v zápisu CIDR odpovídá 1024 IPv4 adresám od 192.168.100.0 do 192.168.103.255.

* Jako **akci** pravidla filtru IP adres vyberte **Povolit** nebo **Blokovat**.

Po vyplnění polí pravidlo uložte výběrem položky **Uložit**. Zobrazí se upozornění s informací, že probíhá aktualizace.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

Když dosáhnete maximálního počtu 10 pravidel filtru IP adres, možnost **Přidat** už nebude aktivní.

Pokud chcete upravit stávající pravidlo, vyberte údaje, které chcete změnit, proveďte změnu a pak úpravy uložte výběrem položky **Uložit**.

## <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP adres

Pokud chcete některé pravidlo filtru IP adres odstranit, vyberte na příslušném řádku ikonu odpadkového koše a pak vyberte možnost **Uložit**. Dané pravidlo se odebere a změna se uloží.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Načtení a aktualizace filtrů IP adres pomocí Azure CLI

Filtry IP IoT Hub můžete načíst a aktualizovat prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/?view=azure-cli-latest).

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

Můžete také načíst a upravit filtr IP IoT Hub pomocí koncového bodu REST poskytovatele prostředků Azure. Projděte si část `properties.ipFilterRules` v tématu o [metodě createorupdate](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidel filtru IP adres

Pravidla filtru IP adres se uplatňují jedno pod druhém a akci přijetí nebo odmítnutí určí první pravidlo odpovídající příslušné IP adrese.

Pokud například chcete přijmout adresy v rozsahu 192.168.100.0/22 a všechny ostatní odmítnout, první pravidlo v mřížce by mělo přijmout rozsah adres 192.168.100.0/22. Další pravidlo by mělo pomocí rozsahu 0.0.0.0/0 odmítnout všechny adresy.

Pořadí pravidel filtru IP adres v mřížce můžete změnit kliknutím na tři svislé tečky na začátku řádku a přetažením.

Pokud chcete uložit nové pořadí pravidel filtru IP adres, klikněte na **Uložit**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-rule-order.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

## <a name="next-steps"></a>Další kroky

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub metriky](iot-hub-metrics.md)