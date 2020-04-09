---
title: Konfigurace pravidel brány firewall IP pro Službu Azure Service Bus
description: Jak pomocí pravidel brány firewall povolit připojení z konkrétních IP adres do Služby Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 24591c20ed707d9541eece0698ecd6e6b5ddee35
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878183"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Konfigurace pravidel brány firewall IP pro Službu Azure Service Bus
Ve výchozím nastavení jsou obory názvů služby Service Bus přístupné z Internetu, pokud je požadavek dodáván s platným ověřováním a autorizací. S ip firewallem ji můžete dále omezit pouze na sadu adres IPv4 nebo rozsahy adres IPv4 v zápisu [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Tato funkce je užitečná ve scénářích, ve kterých by azure service bus měla být přístupná jenom z určitých známých webů. Pravidla brány firewall umožňují konfigurovat pravidla pro přijímání přenosů pocházejících z konkrétních adres IPv4. Pokud například používáte službu Service Bus s [trasou Azure Express ,][express-route]můžete vytvořit **pravidlo brány firewall,** které povolí provoz pouze z vašich místních IP adres infrastruktury nebo z adres podnikové brány NAT. 

> [!IMPORTANT]
> Brány firewall a virtuální sítě jsou podporovány pouze v **prémiové** úrovni service bus. Pokud upgrade na **úroveň Premier** není možnost, doporučujeme zachovat token sdíleného přístupového podpisu (SAS) zabezpečený a sdílet s pouze oprávněnými uživateli. Informace o ověřování SAS naleznete v [tématu Ověřování a autorizace](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>Pravidla brány firewall IP
Pravidla brány firewall IP jsou použita na úrovni oboru názvů Service Bus. Proto pravidla platí pro všechna připojení z klientů pomocí libovolného podporovaného protokolu. Jakýkoli pokus o připojení z adresy IP, která neodpovídá povolenému pravidlu PROTOKOLU IP v oboru názvů service bus, je odmítnut jako neoprávněný. Odpověď neuvádí pravidlo IP. Pravidla filtru IP jsou použita v pořadí a první pravidlo, které odpovídá adrese IP, určuje akci přijmout nebo odmítnout.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části se ukazuje, jak pomocí portálu Azure vytvořit pravidla brány firewall IP pro obor názvů Service Bus. 

1. Přejděte do **oboru názvů Service Bus** na webu Azure [Portal](https://portal.azure.com).
2. V levé nabídce vyberte **možnost Síť.** Ve výchozím nastavení je vybrána možnost **Všechny sítě.** Obor názvů service bus přijímá připojení z libovolné adresy IP. Toto výchozí nastavení je ekvivalentní pravidlu, které přijímá rozsah adres IP 0.0.0.0/0. 

    ![Brána firewall – byla vybrána možnost Všechny sítě](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. V horní části stránky vyberte možnost **Vybrané sítě.** V části **Brána firewall** postupujte takto:
    1. Vyberte možnost Přidat ip **adresu klienta,** chcete-li aktuální ip adresu klienta získat přístup k oboru názvů. 
    2. Pro **rozsah adres**zadejte do zápisu CIDR konkrétní adresu IPv4 nebo rozsah adresy IPv4. 
    3. Určete, zda chcete **povolit důvěryhodným službám společnosti Microsoft obejít tuto bránu firewall**. 

        ![Brána firewall – byla vybrána možnost Všechny sítě](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. **Chcete-li** uložit nastavení, vyberte uložit na panelu nástrojů možnost Uložit. Počkejte několik minut, než se potvrzení zobrazí na portálu oznámení.

## <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru
Tato část obsahuje ukázkovou šablonu Azure Resource Manager, která vytvoří virtuální síť a pravidlo brány firewall.


Následující šablona Správce prostředků umožňuje přidání pravidla virtuální sítě do existujícího oboru názvů Service Bus.

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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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

Omezení přístupu k Service Bus do virtuálních sítí Azure najdete v následujícím odkazu:

- [Koncové body služby Virtuální síť pro sběrnici Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
