---
title: Filtry připojení IP pro Azure IoT DPS | Microsoft Docs
description: Jak používat filtrování protokolu IP k blokování připojení z konkrétních IP adres do vaší instance Azure IoT DPS. Můžete zablokovat připojení z individuálních nebo rozsahů IP adres.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79284912"
---
# <a name="use-ip-filters"></a>Použití filtrů IP adres

Zabezpečení je důležitým aspektem řešení IoT. Někdy musíte explicitně zadat IP adresy, ze kterých se zařízení můžou připojit, jako součást konfigurace zabezpečení. Funkce *filtru IP* pro Azure IoT Hub Device PROVISIONING Service (DPS) umožňuje konfigurovat pravidla pro zamítnutí nebo přijetí provozu z konkrétních adres IPv4.

## <a name="when-to-use"></a>Kdy je použít

Existují dva konkrétní případy použití, kdy je vhodné zablokovat připojení ke koncovému bodu DPS z určitých IP adres:

* Vaše DPS by měl přijímat přenosy jenom z určeného rozsahu IP adres a zamítnout všechno ostatní. Používáte například svůj DPS s [trasou Azure Express](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) k vytváření privátních připojení mezi DPS a vašimi zařízeními.

* Je nutné zamítnout provoz z IP adres, které se identifikovaly jako podezřelé správcem DPS.

## <a name="how-filter-rules-are-applied"></a>Jak se používají pravidla filtru

Pravidla filtru IP jsou použita na úrovni instance DPS. Proto se pravidla filtru IP použijí na všechna připojení ze zařízení a back-endové aplikace s využitím jakéhokoli podporovaného protokolu.

Jakýkoli pokus o připojení z IP adresy, která odpovídá pravidlu odmítnutí protokolu IP ve vaší instanci DPS, obdrží ne401 autorizovaný kód stavu a popis. Zpráva odpovědi nezmiňuje pravidlo protokolu IP.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení je mřížka **filtru IP** na portálu pro DPS prázdná. Toto výchozí nastavení znamená, že vaše DPS přijímá připojení z libovolné IP adresy. Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0.

![Výchozí nastavení filtru IP adres IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidat nebo upravit pravidlo filtru IP

Pokud chcete přidat pravidlo filtru IP, vyberte **+ Přidat pravidlo filtru IP**.

![Přidání pravidla filtru IP do IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Po výběru možnosti **Přidat pravidlo filtru protokolu IP**zadejte pole.

![Po výběru možnosti Přidat pravidlo filtru IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Zadejte **název** pravidla filtru IP. Musí se jednat o jedinečný řetězec bez rozlišení velkých a malých písmen, který je dlouhý až 128 znaků. Jsou přijímány pouze alfanumerické znaky ASCII a `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` 7.

* Zadejte jednu adresu IPv4 nebo blok IP adres v zápisu CIDR. Například v zápisu CIDR 192.168.100.0/22 představuje adresy IPv4 1024 z 192.168.100.0 do 192.168.103.255.

* Jako **akci** pro pravidlo filtru IP vyberte možnost **povoluje** nebo **zablokovat** .

Po vyplnění polí vyberte **Uložit** a uložte pravidlo. Zobrazí se upozornění oznamující, že probíhá aktualizace.

![Oznámení o uložení pravidla filtru IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Možnost **Přidat** je zakázaná, když dosáhnete maximálního počtu 10 pravidel filtru IP.

Pokud chcete upravit stávající pravidlo, vyberte data, která chcete změnit, proveďte změnu a pak kliknutím na **Uložit** uložte úpravy.

> [!NOTE]
> Odmítání IP adres může ostatním službám Azure zabránit v interakci s instancí DPS.

## <a name="delete-an-ip-filter-rule"></a>Odstraní pravidlo filtru IP.

Pokud chcete odstranit pravidlo filtru IP, vyberte na tomto řádku ikonu odpadkového koše a pak vyberte **Uložit**. Pravidlo se odebere a změna se uloží.

![Odstranění pravidla filtru IP adres IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aktualizovat pravidla filtru IP v kódu

Pomocí koncového bodu REST poskytovatele prostředků Azure můžete načítat a upravovat svůj filtr IP DPS. Viz `properties.ipFilterRules` v tématu [Metoda createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Aktualizace pravidel filtru IP DPS není v současnosti podporovaná pomocí Azure CLI nebo Azure PowerShell, ale dá se s Azure Resource Manager šablony dosáhnout. Pokyny k používání šablon Správce prostředků najdete v tématu [Azure Resource Manager šablon](../azure-resource-manager/templates/overview.md) . Níže uvedené příklady šablon ukazují, jak vytvořit, upravit a odstranit pravidla filtru IP pro DPS.

### <a name="add-an-ip-filter-rule"></a>Přidat pravidlo filtru IP

Následující příklad šablony vytvoří nové pravidlo filtru IP s názvem "AllowAll", které přijímá veškerý provoz.

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

Aktualizujte atributy pravidla filtru IP pro šablonu na základě vašich požadavků.

| Atribut                | Popis |
| ------------------------ | ----------- |
| **FilterName**           | Zadejte název pravidla filtru IP. Musí se jednat o jedinečný řetězec bez rozlišení velkých a malých písmen, který je dlouhý až 128 znaků. Jenom znaky ASCII 7 bitů plus {"-", ":", "/"\', ".", "+", "%", "_", "*", "*", "?",!, jsou přijaty tyto "(", ")", ",", "@", ";", "". |
| **Akce**               | Přijaté hodnoty se **přijímají** nebo **zamítnou** jako akce pro pravidlo filtru IP. |
| **ipMask**               | Zadejte jednu adresu IPv4 nebo blok IP adres v zápisu CIDR. Například v zápisu CIDR 192.168.100.0/22 představuje adresy IPv4 1024 z 192.168.100.0 do 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aktualizovat pravidlo filtru IP

Následující příklad šablony aktualizuje pravidlo filtru IP s názvem "AllowAll", které bylo zobrazeno dříve, pro zamítnutí všech přenosů.

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

### <a name="delete-an-ip-filter-rule"></a>Odstraní pravidlo filtru IP.

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

Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

Například pokud chcete přijmout adresy v rozsahu 192.168.100.0/22 a odmítnout všechno ostatní, první pravidlo v mřížce by mělo přijmout rozsah adres 192.168.100.0/22. Další pravidlo by mělo odmítnout všechny adresy pomocí rozsahu 0.0.0.0/0.

Pořadí pravidel filtru IP v mřížce můžete změnit kliknutím na tři svislé tečky na začátku řádku a pomocí přetažení.

Pokud chcete uložit nové pořadí pravidel filtru IP, klikněte na **Uložit**.

![Změna pořadí pravidel filtru IP DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Další kroky

Další informace o správě DPS najdete v těchto tématech:

* [Principy IP adres IoT DPS](iot-dps-understand-ip-address.md)
* [Konfigurace DPS pomocí Azure CLI](how-to-manage-dps-with-cli.md)
* [Řízení přístupu k DPS](how-to-control-access.md)
