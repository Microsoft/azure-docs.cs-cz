---
title: Koncové body služby Virtuální síť – Centra událostí Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak přidat koncový bod služby Microsoft.EventHub do virtuální sítě.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 11/26/2019
ms.author: shvija
ms.openlocfilehash: 91b08d6130da640adc28a3b7d85bd33f0e876caf
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390284"
---
# <a name="use-virtual-network-service-endpoints-with-azure-event-hubs"></a>Použití koncových bodů služby Virtuální síť s Azure Event Hubs

Integrace event hubů s [koncovými body služby Virtuální síť (Virtuální síť)][vnet-sep] umožňuje zabezpečený přístup k možnostem zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou vázané na virtuální sítě, přičemž cesta síťového provozu je zabezpečena na obou koncích.

Po nakonfigurované matné síti na vázaný na alespoň jeden koncový bod služby podsítě virtuální sítě nepřijímá obor názvů příslušných center událostí provoz odkudkoli, ale autorizované podsítě ve virtuálních sítích. Z hlediska virtuální sítě vazba oboru názvů Event Hubs na koncový bod služby konfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv. 

Výsledkem je soukromý a izolovaný vztah mezi úlohami vázanými na podsíť a příslušným oborem názvů Event Hubs, a to navzdory tomu, že pozorovatelná síťová adresa koncového bodu služby zasílání zpráv je ve veřejném rozsahu IP adres. Existuje výjimka z tohoto chování. Povolení koncového bodu služby ve `denyall` výchozím nastavení umožňuje pravidlo v [bráně firewall IP](event-hubs-ip-filtering.md) přidružené k virtuální síti. Do brány firewall IP můžete přidat konkrétní ip adresy, které umožní přístup k veřejnému koncovému bodu Centra událostí. 

>[!WARNING]
> Implementace integrace virtuálních sítí může zabránit interakci jiných služeb Azure s centry událostí.
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


> [!IMPORTANT]
> Virtuální sítě jsou podporovány ve **standardních** a **vyhrazených** úrovních event hubů. Není podporována v **základní** vrstvě.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé scénáře zabezpečení povolené integrací virtuální sítě 

Řešení, která vyžadují těsné a rozčleněné zabezpečení a kde podsítě virtuálních sítí poskytují segmentaci mezi rozčleněné služby, stále potřebují komunikační cesty mezi službami, které jsou v těchto oddílech.

Jakákoli okamžitá trasa IP mezi oddíly, včetně těch, které přenášejí protokol HTTPS přes Protokol TCP/IP, s sebou nese riziko zneužití zranitelných míst ze síťové vrstvy. Služby zasílání zpráv poskytují izolované komunikační cesty, kde jsou zprávy dokonce zapsány na disk při přechodu mezi stranami. Úlohy ve dvou odlišných virtuálních sítích, které jsou vázány na stejnou instanci Event Hubs, mohou efektivně a spolehlivě komunikovat prostřednictvím zpráv, zatímco je zachována integrita hranice izolace sítě.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nejen získají přístup k špičkovým spolehlivým a škálovatelným funkcím zasílání zpráv azure, ale teď můžou pomocí zasílání zpráv vytvářet komunikační cesty mezi zabezpečenými oddíly řešení, které jsou ze své podstaty bezpečnější než to, čeho je dosažitelné v jakémkoli režimu komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečených protokoly TLS.

## <a name="bind-event-hubs-to-virtual-networks"></a>Vazba rozbočovačů událostí s virtuálními sítěmi

**Pravidla virtuální sítě** jsou funkce zabezpečení brány firewall, která řídí, zda váš obor názvů Centra událostí Azure přijímá připojení z určité podsítě virtuální sítě.

Vazba oboru názvů Event Hubs do virtuální sítě je dvoustupňový proces. Nejprve je potřeba vytvořit **koncový bod virtuální síťové služby** v podsíti virtuální sítě a povolit jej pro **Microsoft.EventHub,** jak je vysvětleno v článku [přehledu koncového bodu služby.][vnet-sep] Po přidání koncového bodu služby svážete obor názvů Event Hubs s **pravidlem virtuální sítě**.

Pravidlo virtuální sítě je přidružení oboru názvů Event Hubs s podsítí virtuální sítě. Zatímco pravidlo existuje, všem úlohám vázaným na podsíť je udělen přístup k oboru názvů Event Hubs. Centrum událostí sám nikdy nevytvoří odchozí připojení, není nutné získat přístup, a proto je nikdy udělen přístup k podsíti povolením tohoto pravidla.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Tato část ukazuje, jak pomocí portálu Azure k přidání koncového bodu služby virtuální sítě. Chcete-li omezit přístup, je třeba integrovat koncový bod služby virtuální sítě pro tento obor názvů Event Hubs.

1. Přejděte do **oboru názvů Centra událostí** na webu Azure [Portal](https://portal.azure.com).
2. V levé nabídce vyberte **možnost Síť.** Pokud vyberete možnost **Všechny sítě,** centrum událostí přijme připojení z libovolné adresy IP. Toto nastavení je ekvivalentní pravidlu, které přijímá rozsah adres IP 0.0.0.0/0. 

    ![Brána firewall – byla vybrána možnost Všechny sítě](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Chcete-li restrct přístup k určitým sítím, vyberte možnost **Vybrané sítě** v horní části stránky.
2. V části **Virtuální síť** na stránce vyberte **+Přidat existující virtuální síť***. Vyberte **+ Vytvořit novou virtuální síť,** pokud chcete vytvořit novou virtuální síť. 

    ![přidání existující virtuální sítě](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)
3. Vyberte virtuální síť ze seznamu virtuálních sítí a vyberte **podsíť**. Před přidáním virtuální sítě do seznamu je třeba povolit koncový bod služby. Pokud koncový bod služby není povolen, portál vás vyzve k jeho povolení.
   
   ![vybrat podsíť](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Po povolení koncového bodu služby pro podsíť pro **Microsoft.EventHub**by se měla zobrazit následující úspěšná zpráva. Chcete-li přidat síť, vyberte **přidat** v dolní části stránky. 

    ![vybrat podsíť a povolit koncový bod](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Pokud koncový bod služby nemůžete povolit, můžete ignorovat chybějící koncový bod služby virtuální sítě pomocí šablony Správce prostředků. Tato funkce není na portálu k dispozici.
6. **Chcete-li** uložit nastavení, vyberte uložit na panelu nástrojů možnost Uložit. Počkejte několik minut, než se potvrzení zobrazí na portálu oznámení.

    ![Uložit síť](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)


## <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru

Následující šablona Správce prostředků umožňuje přidání pravidla virtuální sítě do existujícího oboru názvů Event Hubs.

Parametry šablony:

* **namespaceName**: Obor názvů Event Hubs.
* **vnetRuleName**: Název pravidla virtuální sítě, které má být vytvořeno.
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
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
                    "service": "Microsoft.EventHub"
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
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
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
- [Filtrování IP adres centra Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
