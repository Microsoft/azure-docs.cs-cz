---
title: Filtry IP IoT Hub Azure | Microsoft Docs
description: Jak používat filtrování protokolu IP k povolení připojení z konkrétních IP adres pro do služby Azure IoT Hub.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: jlian
ms.openlocfilehash: bf9d58926c5a0fdc7c305e1d9daebfa1c8c9cf63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023575"
---
# <a name="use-ip-filters"></a>Použití filtrů IP adres

Zabezpečení je důležitým aspektem řešení IoT založeného na Azure IoT Hub. Někdy je potřeba v rámci konfigurace zabezpečení explicitně zadat IP adresy, ze kterých se smějí připojovat zařízení. Funkce *filtrování IP adres* umožňuje konfigurovat pravidla pro zamítnutí nebo přijetí provozu z konkrétních adres IPv4.

## <a name="when-to-use"></a>Kdy je použít

Použijte filtr IP pro příjem provozu jenom z určeného rozsahu IP adres a zamítnout všechno ostatní. Například používáte službu IoT Hub se službou [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) k vytváření privátních připojení mezi službou IoT Hub a místní infrastrukturou.

## <a name="default-setting"></a>Výchozí nastavení

Pokud se chcete dostat na stránku nastavení filtru IP adres, vyberte **sítě**, **veřejný přístup** a pak zvolte **vybrané rozsahy IP adres**:

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="Výchozí nastavení filtru IP IoT Hub":::

Ve výchozím nastavení je mřížka **filtru IP** na portálu pro Centrum IoT prázdná. Toto výchozí nastavení znamená, že vaše centrum blokuje připojení ze všech IP adres. Toto výchozí nastavení odpovídá pravidlu, které blokuje `0.0.0.0/0` Rozsah IP adres.

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidání nebo úprava pravidla filtru IP

Pokud chcete přidat pravidlo filtru IP, vyberte **+ Přidat pravidlo filtru IP adres**. Pokud chcete rychle přidat IP adresu vašeho počítače, klikněte na **Přidat IP adresu klienta**. 

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="Přidání pravidla filtru IP do služby IoT Hub":::

Po výběru možnosti **Přidat pravidlo filtru IP adres** vyplňte zobrazená pole. Tato pole jsou předem vyplněna, pokud jste vybrali možnost Přidat IP adresu klienta.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="Po výběru možnosti Přidat pravidlo filtru IP adres":::

* Zadejte **název** pravidla filtru IP adres. Tento název musí být jedinečný, nerozlišující velká a malá písmena, dlouhé řetězce až 128 znaků. Přípustné jsou jenom 7bitové alfanumerické znaky ASCII a znaky `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Zadejte jednu IPv4 adresu nebo blok IP adres v zápisu CIDR. Například adresa 192.168.100.0/22 v zápisu CIDR odpovídá 1024 IPv4 adresám od 192.168.100.0 do 192.168.103.255.

Po vyplnění polí pravidlo uložte výběrem položky **Uložit**. Zobrazí se upozornění s informací, že probíhá aktualizace.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="Oznámení o uložení pravidla filtru IP":::

Když dosáhnete maximálního počtu 10 pravidel filtru IP adres, možnost **Přidat** už nebude aktivní.

Pokud chcete upravit stávající pravidlo, vyberte údaje, které chcete změnit, proveďte změnu a pak úpravy uložte výběrem položky **Uložit**.

## <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP adres

Pokud chcete některé pravidlo filtru IP adres odstranit, vyberte na příslušném řádku ikonu odpadkového koše a pak vyberte možnost **Uložit**. Dané pravidlo se odebere a změna se uloží.

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="Odstraní pravidlo filtru IP IoT Hub.":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>Použít pravidla filtru IP na integrovaný koncový bod kompatibilní s centrem událostí

Chcete-li použít pravidla filtru IP pro vestavěný koncový bod kompatibilní s centrem událostí, zaškrtněte políčko u pole **použít filtry IP na předdefinovaný koncový bod** a pak vyberte **Uložit**.

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="Obrázek znázorňující přepínač pro vestavěný koncový bod a uložit":::

> [!NOTE]
> Tato možnost není k dispozici zdarma (F1) centra IoT. Pokud chcete použít pravidla filtru IP na předdefinovaný koncový bod, použijte placené centrum IoT.

Když tuto možnost povolíte, vaše pravidla filtru IP se replikují do integrovaného koncového bodu, takže k němu budou mít přístup jenom důvěryhodné rozsahy IP adres.

Pokud tuto možnost zakážete, bude integrovaný koncový bod přístupný všem IP adresám. Toto chování může být užitečné, pokud chcete číst ze koncového bodu se službami se měnícími se IP adresami, jako je Azure Stream Analytics. 

## <a name="how-filter-rules-are-applied"></a>Způsob uplatňování pravidel filtru

Pravidla filtru IP jsou použita na úrovni služby IoT Hub. Proto se pravidla filtru IP použijí na všechna připojení ze zařízení a back-endové aplikace s využitím jakéhokoli podporovaného protokolu. Můžete také zvolit, jestli jsou na tato pravidla navázány [integrované koncové body centra událostí](iot-hub-devguide-messages-read-builtin.md) (ne prostřednictvím připojovacího řetězce IoT Hub). 

Jakýkoli pokus o připojení z IP adresy, která explicitně není povolená, obdrží ne401 autorizovaný kód stavu a popis. Zpráva odpovědi neinformuje o pravidle IP adres. Odmítání IP adres může zabránit jiným službám Azure, jako je Azure Stream Analytics, Azure Virtual Machines nebo Device Explorer v Azure Portal z interakce se službou IoT Hub.

> [!NOTE]
> Pokud musíte použít Azure Stream Analytics (ASA) ke čtení zpráv ze služby IoT Hub s povoleným filtrem IP, **zakažte** u možnosti **použít filtry IP adres na integrovaný koncový bod** a pak použijte název a koncový bod kompatibilní s centrem událostí v centru IoT k ručnímu přidání [vstupu Event Hubs datového proudu](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs) do ASA.

### <a name="ordering"></a>Řazení

Pravidla filtru IP adres *povolují* pravidla a používají se bez řazení. K IoT Hub se smějí připojovat pouze IP adresy, které přidáváte. 

Například pokud chcete přijmout adresy v rozsahu `192.168.100.0/22` a odmítnout všechno ostatní, stačí do mřížky přidat jenom jedno pravidlo s rozsahem adres `192.168.100.0/22` .

### <a name="azure-portal"></a>portál Azure 

Pravidla filtru IP se používají taky při použití IoT Hub prostřednictvím Azure Portal. Důvodem je to, že volání rozhraní API služby IoT Hub se přímo využívají v prohlížeči s vašimi přihlašovacími údaji, které jsou konzistentní s jinými službami Azure. Pokud chcete získat přístup k IoT Hub pomocí Azure Portal, když je povolený filtr IP adres, přidejte IP adresu počítače do povolených. 

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Načtení a aktualizace filtrů IP adres pomocí Azure CLI

Filtry IP IoT Hub můžete načíst a aktualizovat prostřednictvím rozhraní příkazového [řádku Azure CLI](/cli/azure/).

Chcete-li načíst aktuální filtry IP IoT Hub, spusťte příkaz:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Tato akce vrátí objekt JSON, ve kterém jsou uvedené existující filtry IP adres pod `properties.networkRuleSets` klíčem:

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

Chcete-li přidat nový filtr IP adres pro IoT Hub, spusťte příkaz:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

Pokud chcete ve svém IoT Hub odebrat existující filtr IP adres, spusťte:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

Tady `<ipFilterIndexToRemove>` musí odpovídat řazení filtrů IP adres v IoT Hub `properties.networkRuleSets.ipRules` .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Načtení a aktualizace filtrů IP adres pomocí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Filtry IP IoT Hub můžete načíst a nastavit prostřednictvím [Azure PowerShell](/powershell/azure/).

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Aktualizace pravidel filtru IP pomocí REST


Můžete také načíst a upravit filtr IP IoT Hub pomocí koncového bodu REST poskytovatele prostředků Azure. Projděte si část `properties.networkRuleSets` v tématu o [metodě createorupdate](/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-classic-retirement"></a>Vyřazení filtru IP (klasické)

Klasický filtr IP adres je vyřazený. Další informace najdete v tématu [IoT Hub klasický filtr IP adres a postup upgradu](iot-hub-ip-filter-classic.md).

## <a name="next-steps"></a>Další kroky

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub metriky](./monitor-iot-hub.md)
* [Podpora IoT Hub pro virtuální sítě s privátním odkazem a spravovanou identitou](virtual-network-support.md)
* [Správa přístupu k veřejné síti ve službě IoT Hub](iot-hub-public-network-access.md)
* [Monitorování IoT Hubu](monitor-iot-hub.md)
