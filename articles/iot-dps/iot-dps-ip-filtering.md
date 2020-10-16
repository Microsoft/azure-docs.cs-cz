---
title: Filtry připojení IP pro Azure IoT DPS | Microsoft Docs
description: Tady se dozvíte, jak pomocí filtrování IP blokovat připojení z určitých IP adres k vaší instanci Azure IoT DPS. Můžete zablokovat připojení z jednotlivých IP adres nebo rozsahů IP adres.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 580c378df5fc3912aa540b5d85adf99bc42605e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86511938"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Použití filtrů připojení IP pro Azure IoT DPS

Důležitým aspektem každého řešení IoT je zabezpečení. Někdy je potřeba v rámci konfigurace zabezpečení explicitně zadat IP adresy, ze kterých se smějí připojovat zařízení. Funkce *filtru IP* pro službu Azure IoT Hub DPS (Device Provisioning Service) umožňuje nakonfigurovat pravidla pro odmítnutí nebo přijetí přenosů z konkrétních IPv4 adres.

## <a name="when-to-use"></a>Kdy je použít

Existují dva konkrétní případy použití, kdy je dobré zablokovat připojení ke koncovému bodu DPS z určitých IP adres:

* Vaše služba DPS by měla přijímat přenosy jenom ze zadaného rozsahu IP adres a všechny ostatní zamítnout. To může být nutné třeba v případě, že pomocí služby DPS v kombinaci s [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) vytváříte privátní připojení mezi službou DPS a zařízeními.

* Potřebujete zamítnout přenosy z IP adres, které správce DPS označil za podezřelé.

## <a name="how-filter-rules-are-applied"></a>Způsob uplatňování pravidel filtru

Pravidla filtru IP adres se uplatňují na úrovni instance služby DPS. Tím pádem se pravidla filtru IP adres uplatňují na všechna připojení ze zařízení a back-endových aplikací přes kterýkoli podporovaný protokol.

Každý pokus o připojení z IP adresy, která ve vaší instanci služby DPS odpovídá pravidlu odmítnutí IP adresy, dostane odezvu s kódem a popisem stavu 401 Neautorizováno. Zpráva odpovědi neinformuje o pravidle IP adres.

## <a name="default-setting"></a>Výchozí nastavení

Ve výchozím nastavení je mřížka **Filtr IP** na portálu služby DPS prázdná. Toto výchozí nastavení znamená, že služba DPS přijímá připojení ze všech IP adres. Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0.

![Výchozí nastavení filtru IP služby IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Přidání nebo úprava pravidla filtru IP

Pokud chcete přidat pravidlo filtru IP, vyberte **+ Přidat pravidlo filtru IP adres**.

![Přidání pravidla filtru IP adres do služby IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Po výběru možnosti **Přidat pravidlo filtru IP adres** vyplňte zobrazená pole.

![Po výběru možnosti Přidat pravidlo filtru IP adres](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Zadejte **název** pravidla filtru IP adres. Musí se jednat o jedinečný alfanumerický řetězec s délkou do 128 znaků, ve kterém se nerozlišují malá a velká písmena. Přípustné jsou jenom 7bitové alfanumerické znaky ASCII a znaky `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`.

* Zadejte jednu IPv4 adresu nebo blok IP adres v zápisu CIDR. Například adresa 192.168.100.0/22 v zápisu CIDR odpovídá 1024 IPv4 adresám od 192.168.100.0 do 192.168.103.255.

* Jako **akci** pravidla filtru IP adres vyberte **Povolit** nebo **Blokovat**.

Po vyplnění polí pravidlo uložte výběrem položky **Uložit**. Zobrazí se upozornění s informací, že probíhá aktualizace.

![Oznámení o uložení pravidla filtru IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Když dosáhnete maximálního počtu 10 pravidel filtru IP adres, možnost **Přidat** už nebude aktivní.

Pokud chcete upravit stávající pravidlo, vyberte údaje, které chcete změnit, proveďte změnu a pak úpravy uložte výběrem položky **Uložit**.

> [!NOTE]
> Zamítnutí určitých IP adres může zabránit jiným službám Azure v interakci s danou instancí služby DPS.

## <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP adres

Pokud chcete některé pravidlo filtru IP adres odstranit, vyberte na příslušném řádku ikonu odpadkového koše a pak vyberte možnost **Uložit**. Dané pravidlo se odebere a změna se uloží.

![Odstranění pravidla filtru IP adres služby IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aktualizace pravidel filtru IP adres v kódu

Filtr IP adres služby DPS můžete načíst a upravit pomocí koncového bodu REST poskytovatele prostředků Azure. Projděte si část `properties.ipFilterRules` v tématu o [metodě createorupdate](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

V současné době nepodporujeme aktualizaci pravidel filtru IP adres služby DPS v prostředí Azure CLI ani Azure PowerShell, ale můžete ji provést pomocí šablon Azure Resource Manageru. Pokyny k používání šablon Resource Manageru najdete v tématu o šablonách [Azure Resource Manageru](../azure-resource-manager/templates/overview.md). Následující příklady šablon ukazují, jak vytvořit, upravit a odstranit pravidlo filtru IP adres služby DPS.

### <a name="add-an-ip-filter-rule"></a>Přidání pravidla filtru IP adres

Následující příklad šablony vytvoří nové pravidlo filtru IP adres s názvem AllowAll, které přijímá všechny přenosy.

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

Aktualizujte atributy pravidla filtru IP adres této šablony podle svých požadavků.

| Atribut                | Popis |
| ------------------------ | ----------- |
| **FilterName**           | Zadejte název pravidla filtru IP adres. Musí se jednat o jedinečný alfanumerický řetězec s délkou do 128 znaků, ve kterém se nerozlišují malá a velká písmena. Přípustné jsou jenom 7bitové alfanumerické znaky ASCII a znaky {-, :, /, \', ., +, %, _, #, *, ?, !, (, ), ,, =, @, ;, '}. |
| **Akce**               | Jako akci pro pravidlo filtru IP adres můžete zadat hodnotu **Přijmout** nebo **Odmítnout** . |
| **ipMask**               | Zadejte jednu IPv4 adresu nebo blok IP adres v zápisu CIDR. Například adresa 192.168.100.0/22 v zápisu CIDR odpovídá 1024 IPv4 adresám od 192.168.100.0 do 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aktualizace pravidla filtru IP adres

Následující příklad šablony aktualizuje předchozí pravidlo filtru IP adres s názvem AllowAll tak, aby zamítlo veškeré přenosy.

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

### <a name="delete-an-ip-filter-rule"></a>Odstranění pravidla filtru IP adres

Následující příklad šablony odstraní všechna pravidla filtru IP adres pro danou instanci služby DPS.

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



## <a name="ip-filter-rule-evaluation"></a>Vyhodnocení pravidel filtru IP adres

Pravidla filtru IP adres se uplatňují jedno pod druhém a akci přijetí nebo odmítnutí určí první pravidlo odpovídající příslušné IP adrese.

Pokud například chcete přijmout adresy v rozsahu 192.168.100.0/22 a všechny ostatní odmítnout, první pravidlo v mřížce by mělo přijmout rozsah adres 192.168.100.0/22. Další pravidlo by mělo pomocí rozsahu 0.0.0.0/0 odmítnout všechny adresy.

Pořadí pravidel filtru IP adres v mřížce můžete změnit kliknutím na tři svislé tečky na začátku řádku a přetažením.

Pokud chcete uložit nové pořadí pravidel filtru IP adres, klikněte na **Uložit**.

![Změna pořadí pravidel filtru IP adres služby DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Další kroky

Další informace o správě služby DPS najdete v těchto tématech:

* [Principy IP adres služby IoT DPS](iot-dps-understand-ip-address.md)
* [Konfigurace služby DPS pomocí Azure CLI](how-to-manage-dps-with-cli.md)
* [Řízení přístupu ke službě DPS](how-to-control-access.md)
