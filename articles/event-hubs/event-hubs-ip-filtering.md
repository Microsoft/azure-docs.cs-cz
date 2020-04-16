---
title: Pravidla brány firewall centra událostí Azure | Dokumenty společnosti Microsoft
description: Pomocí pravidel brány firewall povolte připojení z konkrétních IP adres do Centra událostí Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.custom: seodec18
ms.topic: article
ms.date: 12/20/2019
ms.author: spelluru
ms.openlocfilehash: 18212726f0ab921a05a3b640a32754c62958d047
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393146"
---
# <a name="configure-ip-firewall-rules-for-an-azure-event-hubs-namespace"></a>Konfigurace pravidel brány firewall IP pro obor názvů Centra událostí Azure
Ve výchozím nastavení jsou obory názvů Event Hubs přístupné z Internetu, pokud je požadavek dodáván s platným ověřováním a autorizací. S ip firewallem ji můžete dále omezit pouze na sadu adres IPv4 nebo rozsahy adres IPv4 v zápisu [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Tato funkce je užitečná ve scénářích, ve kterých azure event huby by měly být přístupné jenom z určitých známých webů. Pravidla brány firewall umožňují konfigurovat pravidla pro přijímání přenosů pocházejících z konkrétních adres IPv4. Pokud například používáte centra událostí s [Azure Express Route][express-route], můžete vytvořit pravidlo brány **firewall,** které povolí provoz pouze z vašich ip adres infrastruktury. 

>[!WARNING]
> Povolení filtrování IP adres může zabránit interakci jiných služeb Azure s centry událostí.
>
> Důvěryhodné služby společnosti Microsoft nejsou podporovány při implementaci virtuálních sítí.
>
> Běžné scénáře Azure, které nefungují s virtuálními sítěmi (všimněte si, že seznam **není** vyčerpávající) -
> - Azure Monitor (nastavení diagnostiky)
> - Azure Stream Analytics
> - Integrace s Azure Event Grid
> - Trasy rozbočovače Azure IoT Hub
> - Průzkumník zařízení Azure IoT
>
> Následující služby společnosti Microsoft musí být ve virtuální síti
> - Azure Web Apps
> - Azure Functions


## <a name="ip-firewall-rules"></a>Pravidla brány firewall IP
Pravidla brány firewall IP se použijí na úrovni oboru názvů Event Hubs. Proto pravidla platí pro všechna připojení z klientů pomocí libovolného podporovaného protokolu. Jakýkoli pokus o připojení z adresy IP, která neodpovídá povolenému pravidlu PROTOKOLU IP v oboru názvů Event Hubs, je odmítnut jako neoprávněný. Odpověď neuvádí pravidlo IP. Pravidla filtru IP jsou použita v pořadí a první pravidlo, které odpovídá adrese IP, určuje akci přijmout nebo odmítnout.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Tato část ukazuje, jak pomocí portálu Azure k vytvoření pravidel brány firewall IP pro obor názvů Event Hubs. 

1. Přejděte do **oboru názvů Centra událostí** na webu Azure [Portal](https://portal.azure.com).
2. V levé nabídce vyberte **možnost Síť.** Pokud vyberete možnost **Všechny sítě,** centrum událostí přijme připojení z libovolné adresy IP. Toto nastavení je ekvivalentní pravidlu, které přijímá rozsah adres IP 0.0.0.0/0. 

    ![Brána firewall – byla vybrána možnost Všechny sítě](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Chcete-li omezit přístup k určitým sítím a adresám IP, vyberte možnost **Vybrané sítě.** V části **Brána firewall** postupujte takto:
    1. Vyberte možnost Přidat ip **adresu klienta,** chcete-li aktuální ip adresu klienta získat přístup k oboru názvů. 
    2. Pro **rozsah adres**zadejte do zápisu CIDR konkrétní adresu IPv4 nebo rozsah adresy IPv4. 
    3. Určete, zda chcete **povolit důvěryhodným službám společnosti Microsoft obejít tuto bránu firewall**. 

        ![Brána firewall – byla vybrána možnost Všechny sítě](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. **Chcete-li** uložit nastavení, vyberte uložit na panelu nástrojů možnost Uložit. Počkejte několik minut, než se potvrzení zobrazí na portálu oznámení.


## <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru

> [!IMPORTANT]
> Pravidla brány firewall jsou podporována ve **standardních** a **vyhrazených** úrovních centra událostí. Není podporována v základní vrstvě.

Následující šablona Správce prostředků umožňuje přidání pravidla filtru IP do existujícího oboru názvů Event Hubs.

Parametry šablony:

- **ipMask** je jedna IPv4 adresa nebo blok IP adres v CIDR notaci. Například v CIDR zápis70.37.104.0/24 představuje 256 IPv4 adresy od 70.37.104.0 do 70.37.104.255, s 24 označující počet významných předponových bitů pro rozsah.

> [!NOTE]
> Zatímco neexistují žádná pravidla odepřít možné, šablona Azure Resource Manager má výchozí akce nastavena na **"Povolit",** která neomezuje připojení.
> Při vytváření pravidel virtuální sítě nebo brány firewall musíme změnit ***"defaultAction"***
> 
> Z
> ```json
> "defaultAction": "Allow"
> ```
> na
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Chcete-li šablonu nasadit, postupujte podle pokynů pro [Správce prostředků Azure][lnk-deploy].

## <a name="next-steps"></a>Další kroky

Omezení přístupu k event hubům do virtuálních sítí Azure najdete v následujícím odkazu:

- [Koncové body služby Virtuální síť pro centra událostí][lnk-vnet]

<!-- Links -->

[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
