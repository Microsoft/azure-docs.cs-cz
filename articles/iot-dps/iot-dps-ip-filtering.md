---
title: Filtry připojení IP služby Azure IoT DPS | Dokumenty společnosti Microsoft
description: Jak pomocí filtrování IP adres blokovat připojení z konkrétních IP adres do instance Azure IoT DPS. Můžete blokovat připojení z jednotlivých nebo rozsahů IP adres.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284912"
---
# <a name="use-ip-filters"></a>Použití filtrů IP adres

Zabezpečení je důležitým aspektem každého řešení IoT. Někdy je třeba explicitně zadat IP adresy, ze kterých se zařízení mohou připojit jako součást konfigurace zabezpečení. Funkce *filtru IP* pro službu Azure IoT Hub Device Provisioning Service (DPS) umožňuje nakonfigurovat pravidla pro odmítnutí nebo přijetí provozu z konkrétních adres IPv4.

## <a name="when-to-use"></a>Kdy je použít

Existují dva konkrétní případy použití, kde je užitečné blokovat připojení ke koncovému bodu DPS z určitých adres IP:

* Váš DPS by měl přijímat provoz pouze z určeného rozsahu IP adres a odmítnout vše ostatní. Například používáte DPS s [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) k vytvoření soukromých připojení mezi DPS a vaše zařízení.

* Je třeba odmítnout provoz z IP adres, které byly správcem DPS označeny jako podezřelé.

## <a name="how-filter-rules-are-applied"></a>Použití pravidel filtru

Pravidla filtru IP jsou použita na úrovni instance DPS. Proto pravidla filtru IP platí pro všechna připojení ze zařízení a back-endových aplikací pomocí libovolného podporovaného protokolu.

Jakýkoli pokus o připojení z adresy IP, která odpovídá odmítnutí pravidla IP v instanci DPS, obdrží neoprávněný stavový kód a popis 401. Zpráva s odpovědí neuvádí pravidlo IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení je **mřížka filtru IP** na portálu pro DPS prázdná. Toto výchozí nastavení znamená, že váš DPS přijímá připojení z libovolné IP adresy. Toto výchozí nastavení je ekvivalentní pravidlu, které přijímá rozsah adres IP 0.0.0.0/0.

![Výchozí nastavení filtru IP služby IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidání nebo úprava pravidla filtru IP

Chcete-li přidat pravidlo filtru IP, vyberte **možnost + Přidat pravidlo filtru IP**.

![Přidání pravidla filtru IP do protokolu IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Po výběru **možnosti Přidat pravidlo filtru IP**vyplňte pole.

![Po výběru pravidla přidat filtr IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Zadejte **název** pravidla filtru IP. Musí se jednat o jedinečný alfanumerický řetězec bez velkých a malých písmen o hodnotě až 128 znaků. Jsou přijímány pouze 7bitové alfanumerické znaky `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` ASCII plus.

* Zadejte jednu adresu IPv4 nebo blok IP adres v zápisu CIDR. Například v CIDR zápis 192.168.100.0/22 představuje 1024 IPv4 adresy od 192.168.100.0 do 192.168.103.255.

* Jako **akci** pro pravidlo filtru IP vyberte **Povolit** nebo **Blokovat.**

Po vyplnění polí vyberte **Uložit,** chcete-li pravidlo uložit. Zobrazí se výstraha s upozorněním, že aktualizace probíhá.

![Oznámení o uložení pravidla filtru IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Možnost **Přidat** je zakázána, pokud dosáhnete maximálně 10 pravidel filtru IP.

Chcete-li upravit existující pravidlo, vyberte data, která chcete změnit, proveďte změnu a pak vyberte **Uložit,** chcete-li úpravy uložit.

> [!NOTE]
> Odmítnutí IP adres může zabránit interakci jiných služeb Azure s instancí DPS.

## <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP

Chcete-li odstranit pravidlo filtru IP, vyberte na tomto řádku ikonu koše a pak vyberte **Uložit**. Pravidlo je odebráno a změna uložena.

![Odstranění pravidla filtru IP protokolu IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aktualizace pravidel filtru IP v kódu

Filtr IP dps můžete načíst a upravit pomocí koncového bodu REST zprostředkovatele prostředků Azure. Viz `properties.ipFilterRules` metoda [createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Aktualizace pravidel filtru DPS IP není aktuálně podporovaná pomocí azure cli nebo Azure PowerShellu, ale lze ji provést pomocí šablon Azure Resource Manager. Informace o používání šablon Správce prostředků Azure najdete v tématu v [části Šablony](../azure-resource-manager/templates/overview.md) Správce prostředků Azure. Příklady šablon, které následují, ukazují, jak vytvořit, upravit a odstranit pravidla filtru DPS IP.

### <a name="add-an-ip-filter-rule"></a>Přidání pravidla filtru IP

Následující příklad šablony vytvoří nové pravidlo filtru IP s názvem AllowAll, které přijímá veškerý provoz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Aktualizujte atributy pravidla filtru IP šablony na základě vašich požadavků.

| Atribut                | Popis |
| ------------------------ | ----------- |
| **Název filtru**           | Zadejte název pravidla filtru IP. Musí se jednat o jedinečný alfanumerický řetězec bez velkých a malých písmen o hodnotě až 128 znaků. Přijímají se pouze 7bitové alfanumerické znaky ASCII plus {'-', ',,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,',,,,',,,',,,,,,,,,,,,,,',,,",',,",',,,,,,,,,,,,,,,,,,,,,',,',,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,\' |
| **Akce**               | Přijaté hodnoty jsou **Přijmout** nebo **Odmítnout** jako akce pro pravidlo filtru IP. |
| **maska ip**               | Zadejte jednu adresu IPv4 nebo blok IP adres v zápisu CIDR. Například v CIDR zápis 192.168.100.0/22 představuje 1024 IPv4 adresy od 192.168.100.0 do 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aktualizace pravidla filtru IP

Následující příklad šablony aktualizuje pravidlo filtru IP s názvem "AllowAll", které bylo zobrazeno dříve, aby bylo odmítnuto veškerý provoz.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP

Následující příklad šablony odstraní všechna pravidla filtru IP pro instanci DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidla filtru IP

Pravidla filtru IP jsou použita v pořadí a první pravidlo, které odpovídá adrese IP, určuje akci přijmout nebo odmítnout.

Například pokud chcete přijmout adresy v rozsahu 192.168.100.0/22 a odmítnout vše ostatní, první pravidlo v mřížce by měl přijmout rozsah adres 192.168.100.0/22. Další pravidlo by mělo odmítnout všechny adresy pomocí rozsahu 0.0.0.0/0.

Pořadí pravidel filtru IP v mřížce můžete změnit tak, že kliknete na tři svislé tečky na začátku řádku a použijete přetažení.

Chcete-li uložit nové pořadí pravidel filtru IP, klepněte na tlačítko **Uložit**.

![Změna pořadí pravidel filtru DPS IP](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Další kroky

Chcete-li dále prozkoumat správu DPS, viz:

* [Principy IP adres IoT DPS](iot-dps-understand-ip-address.md)
* [Konfigurace DPS pomocí rozhraní příkazového příkazu Azure](how-to-manage-dps-with-cli.md)
* [Řízení přístupu k DPS](how-to-control-access.md)
