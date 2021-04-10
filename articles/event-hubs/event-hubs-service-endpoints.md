---
title: Koncové body služby Virtual Network – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak přidat koncový bod služby Microsoft. EventHub do virtuální sítě.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: f7f0f3ff480018c9bfc5d9c6f34cf7e2935f8d6a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959955"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-virtual-networks"></a>Povolení přístupu k oborům názvů Azure Event Hubs z konkrétních virtuálních sítí 

Integrace Event Hubs s [koncovými body služby Virtual Network (VNET)][vnet-sep] umožňuje zabezpečenému přístupu k funkcím zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou svázané s virtuálními sítěmi, a cestu síťového provozu, která je zabezpečená na obou koncích. 

Po navázání vazby na alespoň jeden koncový bod služby virtuální sítě musí příslušný obor názvů Event Hubs nadále přijímat provoz z libovolného místa, ale z nich budou ověřeny podsítě ve virtuálních sítích. Z perspektivy virtuální sítě naváže obor názvů Event Hubs na koncový bod služby a nakonfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv. 

Výsledkem je privátní a izolovaný vztah mezi úlohami vázanými na podsíť a odpovídajícím oborem názvů Event Hubs, a to i přes pozorovatelnou síťovou adresu koncového bodu služby zasílání zpráv ve veřejném rozsahu IP adres. K tomuto chování existuje výjimka. Povolení koncového bodu služby ve výchozím nastavení povolí `denyall` pravidlo v [bráně firewall protokolu IP](event-hubs-ip-filtering.md) přidružené k virtuální síti. Můžete přidat konkrétní IP adresy v bráně firewall protokolu IP a povolit tak přístup ke veřejnému koncovému bodu centra událostí. 

## <a name="important-points"></a>Důležité body
- Tato funkce je podporovaná pro **standardní** i **vyhrazené** úrovně. Na úrovni **Basic** se nepodporuje.
- Povolením virtuálních sítí pro váš obor názvů Event Hubs zablokují příchozí požadavky ve výchozím nastavení, pokud požadavky pocházejí ze služby, která se nepoužívá v povolených virtuálních sítích. Blokované požadavky zahrnují ty z jiných služeb Azure, od Azure Portal, ze služeb protokolování a metriky atd. V případě výjimky můžete povolit přístup k Event Hubs prostředkům z určitých **důvěryhodných služeb** i v případě, že jsou povolené virtuální sítě. Seznam důvěryhodných služeb najdete v tématu [důvěryhodné služby](#trusted-microsoft-services).
- Zadejte **alespoň jedno pravidlo IP nebo pravidlo virtuální sítě** pro obor názvů, aby bylo možné provozovat pouze ze zadaných IP adres nebo podsítě virtuální sítě. Pokud neexistují žádná pravidla IP a virtuální sítě, můžete k oboru názvů přistupovat prostřednictvím veřejného Internetu (pomocí přístupového klíče).  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé scénáře zabezpečení povolené integrací virtuální sítě 

Řešení, která vyžadují těsné a compartmentalized zabezpečení a kde podsítě virtuální sítě poskytují segmentaci mezi službami compartmentalized, stále potřebují komunikační cesty mezi službami, které se nacházejí v těchto oddílech.

Veškerá okamžitá trasa IP mezi oddíly, včetně těch, které přenáší HTTPS přes protokol TCP/IP, přináší riziko zneužití chyb zabezpečení z síťové vrstvy. Služby zasílání zpráv poskytují izolované komunikační cesty, ve kterých se při přechodu mezi stranami na disk zapisují i zprávy. Úlohy ve dvou různých virtuálních sítích, které jsou vázané na stejnou instanci Event Hubs, mohou komunikovat efektivně a spolehlivě prostřednictvím zpráv, zatímco příslušná integrita hranice izolace sítě zůstane zachovaná.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nezískají přístup k špičkovým spolehlivým a škálovatelným funkcím asynchronního zasílání zpráv, ale teď můžou pomocí zasílání zpráv vytvářet cesty pro komunikaci mezi oddíly zabezpečeného řešení, které jsou z vlastního hlediska bezpečnější než ty, které jsou dosažitelné pro libovolný režim komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečen

## <a name="bind-event-hubs-to-virtual-networks"></a>Navázání centra událostí k virtuálním sítím

**Pravidla virtuální sítě** jsou funkcí zabezpečení brány firewall, která určuje, jestli váš obor názvů Azure Event Hubs akceptuje připojení z konkrétní podsítě virtuální sítě.

Vytvoření vazby oboru názvů Event Hubs k virtuální síti je proces se dvěma kroky. Nejdřív je potřeba vytvořit **koncový bod služby virtuální sítě** v podsíti virtuální sítě a povolit ho pro **Microsoft. EventHub** , jak je vysvětleno v článku [Přehled koncového bodu služby][vnet-sep] . Po přidání koncového bodu služby navážete obor názvů Event Hubs s **pravidlem virtuální sítě**.

Pravidlo virtuální sítě je přidružení oboru názvů Event Hubs k podsíti virtuální sítě. I když toto pravidlo existuje, budou mít všechny úlohy vázané na podsíť přístup k oboru názvů Event Hubs. Event Hubs sám o sobě nenavazuje odchozí připojení, nemusí získat přístup, a proto nikdy neudělí přístup k podsíti tím, že toto pravidlo povolí.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části se dozvíte, jak pomocí Azure Portal přidat koncový bod služby virtuální sítě. Chcete-li omezit přístup, je nutné integrovat koncový bod služby virtuální sítě pro tento Event Hubs obor názvů.

1. V [Azure Portal](https://portal.azure.com)přejděte do **oboru názvů Event Hubs** .
4. V části **Nastavení** v nabídce vlevo vyberte **sítě** . Karta **síť** se zobrazí jenom pro **standardní** nebo **vyhrazené** obory názvů. 

    > [!WARNING]
    > Pokud vyberete možnost **vybrané sítě** a na tuto stránku nepřidáte aspoň jedno pravidlo FIREWALLU protokolu IP nebo virtuální síť, přístup k oboru názvů se dá získat prostřednictvím **veřejného Internetu** (pomocí přístupové klávesy). 

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Karta sítě – volba vybraných sítí" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Pokud vyberete možnost **všechny sítě** , centrum událostí přijme připojení z libovolné IP adresy (pomocí přístupového klíče). Toto nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0. 

    ![Firewall – vybraná možnost všechny sítě](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Pokud chcete omezit přístup k určitým sítím, vyberte možnost **vybrané sítě** v horní části stránky, pokud už není vybraná.
2. V části **Virtual Network** stránky vyberte **+ Přidat existující virtuální síť** _. Vyberte _ *+ vytvořit novou virtuální síť**, pokud chcete vytvořit novou virtuální síť. 

    ![Přidat existující virtuální síť](./media/event-hubs-tutorial-vnet-and-firewalls/add-vnet-menu.png)

    >[!WARNING]
    > Pokud vyberete možnost **vybrané sítě** a na tuto stránku nepřidáte aspoň jedno pravidlo firewallu IP nebo virtuální síť, přístup k oboru názvů se dá získat přes veřejný Internet (pomocí přístupové klávesy).
3. V seznamu virtuálních sítí vyberte virtuální síť a pak vyberte **podsíť**. Před přidáním virtuální sítě do seznamu musíte povolit koncový bod služby. Pokud koncový bod služby není povolený, portál vás vyzve, abyste ho povolili.
   
   ![vybrat podsíť](./media/event-hubs-tutorial-vnet-and-firewalls/select-subnet.png)

4. Po povolení koncového bodu služby pro podsíť pro **Microsoft. EventHub** by se měla zobrazit následující úspěšná zpráva. V dolní části stránky vyberte **Přidat** a přidejte síť. 

    ![Vyberte podsíť a povolte koncový bod.](./media/event-hubs-tutorial-vnet-and-firewalls/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Pokud nemůžete povolit koncový bod služby, můžete chybějící koncový bod služby virtuální sítě ignorovat pomocí šablony Správce prostředků. Tato funkce není na portálu k dispozici.
5. Určete, zda chcete, aby **důvěryhodné služby společnosti Microsoft vynechal tuto bránu firewall**. Podrobnosti najdete v tématu [důvěryhodné služby Microsoftu](#trusted-microsoft-services) . 
6. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů. Počkejte několik minut, než se potvrzení zobrazí v oznámeních na portálu.

    ![Uložit síť](./media/event-hubs-tutorial-vnet-and-firewalls/save-vnet.png)

    > [!NOTE]
    > Pokud chcete omezit přístup ke konkrétním IP adresám nebo rozsahům, přečtěte si téma [Povolení přístupu z konkrétních IP adres nebo rozsahů](event-hubs-ip-filtering.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru
Následující vzorová Správce prostředků šablona přidá pravidlo virtuální sítě do existujícího oboru názvů Event Hubs. Pro síťové pravidlo určuje ID podsítě ve virtuální síti. 

ID je plně kvalifikovaná cesta Správce prostředků pro podsíť virtuální sítě. Například `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

Při přidávání pravidel virtuální sítě nebo bran firewall nastavte hodnotu `defaultAction` na `Deny` .


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

Pokud chcete nasadit šablonu, postupujte podle pokynů [Azure Resource Manager][lnk-deploy].

> [!IMPORTANT]
> Pokud neexistují žádná pravidla pro IP a virtuální sítě, veškerý provoz se předává do oboru názvů, a to i v případě, že nastavíte `defaultAction` na `deny` .  K oboru názvů se dá získat přístup prostřednictvím veřejného Internetu (pomocí přístupového klíče). Zadejte alespoň jedno pravidlo IP nebo pravidlo virtuální sítě pro obor názvů, aby bylo možné provozovat pouze ze zadaných IP adres nebo podsítě virtuální sítě.  

## <a name="next-steps"></a>Další kroky

Další informace o virtuálních sítích najdete v následujících odkazech:

- [Koncové body služby virtuální sítě Azure][vnet-sep]
- [Filtrování IP Event Hubs Azure][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: event-hubs-ip-filtering.md
