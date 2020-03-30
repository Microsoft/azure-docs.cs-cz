---
title: Konfigurace koncových bodů virtuální síťové služby pro Službu Azure Service Bus
description: Tento článek obsahuje informace o tom, jak přidat koncový bod služby Microsoft.ServiceBus do virtuální sítě.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9dbf65522d5c85e1054ed3f1f6ca9f86180e7f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454977"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Konfigurace koncových bodů virtuální síťové služby pro Službu Azure Service Bus

Integrace koncových bodů služby Service Bus s [virtuální sítí (VNet)][vnet-sep] umožňuje zabezpečený přístup k možnostem zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázané na virtuální sítě, přičemž cesta síťového provozu je zabezpečena na obou koncích.

Po nakonfigurované matné síti je vázán a má být vázán alespoň na jeden koncový bod služby podsítě virtuální sítě, příslušný obor názvů Service Bus již nebude přijímat přenosy odkudkoli, ale autorizované virtuální sítě. Z hlediska virtuální sítě vazba oboru názvů Service Bus na koncový bod služby konfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv.

Výsledkem je soukromý a izolovaný vztah mezi úlohami vázanými na podsíť a příslušným oborem názvů Service Bus, a to navzdory tomu, že pozorovatelná síťová adresa koncového bodu služby zasílání zpráv je ve veřejném rozsahu IP adres.

> [!IMPORTANT]
> Virtuální sítě jsou podporovány pouze v oborech názvů service bus [úrovně Premium.](service-bus-premium-messaging.md)
> 
> Při použití koncových bodů služby virtuální sítě se službou Service Bus byste neměli povolit tyto koncové body v aplikacích, které kombinují obory názvů service bus úrovně Standard a Premium. Protože úroveň Standard nepodporuje virtuální sítě. Koncový bod je omezen pouze na obory názvů úrovně Premium.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé scénáře zabezpečení povolené integrací virtuální sítě 

Řešení, která vyžadují těsné a rozčleněné zabezpečení a kde podsítě virtuálních sítí poskytují segmentaci mezi rozčleněné služby, obecně stále potřebují komunikační cesty mezi službami, které jsou v těchto oddílech.

Jakákoli okamžitá trasa IP mezi oddíly, včetně těch, které přenášejí protokol HTTPS přes Protokol TCP/IP, s sebou nese riziko zneužití zranitelných míst ze síťové vrstvy. Služby zasílání zpráv poskytují zcela izolované komunikační cesty, kde jsou zprávy dokonce zapsány na disk při přechodu mezi stranami. Úlohy ve dvou odlišných virtuálních sítích, které jsou vázány na stejnou instanci service bus můžete komunikovat efektivně a spolehlivě prostřednictvím zpráv, zatímco příslušná integrita hranice izolace sítě je zachována.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nejen získají přístup k špičkovým spolehlivým a škálovatelným funkcím zasílání zpráv azure, ale teď můžou používat zasílání zpráv k vytváření komunikačních cest mezi zabezpečenými komnatami řešení, které jsou ze své podstaty bezpečnější než to, čeho je dosažitelné v libovolném režimu komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečených protokoly TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Závazná sběrnice do virtuálních sítí

*Pravidla virtuální sítě* jsou funkce zabezpečení brány firewall, která určuje, zda server Azure Service Bus přijímá připojení z určité podsítě virtuální sítě.

Vazba oboru názvů service bus do virtuální sítě je dvoustupňový proces. Nejprve je třeba vytvořit **koncový bod služby virtuální sítě** v podsíti virtuální sítě a povolit jej pro **Microsoft.ServiceBus,** jak je vysvětleno v [přehledu koncového bodu služby][vnet-sep]. Po přidání koncového bodu služby svážete obor názvů Service Bus s **pravidlem virtuální sítě**.

Pravidlo virtuální sítě je přidružení oboru názvů Service Bus s podsítí virtuální sítě. Zatímco pravidlo existuje, všem úlohám vázaným na podsíť je udělen přístup k oboru názvů Service Bus. Service Bus sám nikdy naváže odchozí připojení, není nutné získat přístup a proto je nikdy udělen přístup k podsíti povolením tohoto pravidla.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Tato část ukazuje, jak pomocí portálu Azure k přidání koncového bodu služby virtuální sítě. Chcete-li omezit přístup, je třeba integrovat koncový bod služby virtuální sítě pro tento obor názvů Event Hubs.

1. Přejděte do **oboru názvů Service Bus** na webu Azure [Portal](https://portal.azure.com).
2. V levé nabídce vyberte **možnost Síť.** Ve výchozím nastavení je vybrána možnost **Všechny sítě.** Obor názvů přijímá připojení z libovolné adresy IP. Toto výchozí nastavení je ekvivalentní pravidlu, které přijímá rozsah adres IP 0.0.0.0/0. 

    ![Brána firewall – byla vybrána možnost Všechny sítě](./media/service-endpoints/firewall-all-networks-selected.png)
1. V horní části stránky vyberte možnost **Vybrané sítě.**
2. V části **Virtuální síť** na stránce vyberte **+Přidat existující virtuální síť**. 

    ![přidání existující virtuální sítě](./media/service-endpoints/add-vnet-menu.png)
3. Vyberte virtuální síť ze seznamu virtuálních sítí a vyberte **podsíť**. Před přidáním virtuální sítě do seznamu je třeba povolit koncový bod služby. Pokud koncový bod služby není povolen, portál vás vyzve k jeho povolení.
   
   ![vybrat podsíť](./media/service-endpoints/select-subnet.png)

4. Po povolení koncového bodu služby pro podsíť pro **službu Microsoft.ServiceBus**se zobrazí následující zpráva o úspěšné mši. Chcete-li přidat síť, vyberte **přidat** v dolní části stránky. 

    ![vybrat podsíť a povolit koncový bod](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Pokud koncový bod služby nemůžete povolit, můžete ignorovat chybějící koncový bod služby virtuální sítě pomocí šablony Správce prostředků. Tato funkce není na portálu k dispozici.
6. **Chcete-li** uložit nastavení, vyberte uložit na panelu nástrojů možnost Uložit. Počkejte několik minut, než se potvrzení zobrazí v oznámeních portálu. Tlačítko **Uložit** by mělo být zakázáno. 

    ![Uložit síť](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru
Následující šablona Správce prostředků umožňuje přidání pravidla virtuální sítě do existujícího oboru názvů Service Bus.

Parametry šablony:

* **namespaceName**: Obor názvů service bus.
* **virtualNetworkingSubnetId**: Plně kvalifikovaná cesta správce prostředků pro podsíť virtuální sítě; například `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

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

Šablona:

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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

Další informace o virtuálních sítích najdete v následujících odkazech:

- [Koncové body virtuální síťové služby Azure][vnet-sep]
- [Filtrování IP adres azure service bus][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
