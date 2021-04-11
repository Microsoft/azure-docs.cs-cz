---
title: Konfigurace koncových bodů služby virtuální sítě pro Azure Service Bus
description: Tento článek poskytuje informace o tom, jak přidat koncový bod služby Microsoft. ServiceBus do virtuální sítě.
ms.topic: article
ms.date: 03/29/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 9b5aba6c5ad4e1e6d0c90b99ebcdf441bb35cc39
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960448"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-virtual-networks"></a>Povolení přístupu k oboru názvů Azure Service Bus z konkrétních virtuálních sítí
Integrace Service Bus s [koncovými body služby Virtual Network (VNET)][vnet-sep] umožňuje zabezpečenému přístupu k funkcím zasílání zpráv z úloh, jako jsou virtuální počítače, které jsou svázané s virtuálními sítěmi, a cestu síťového provozu, která je zabezpečená na obou koncích.

Po navázání vazby na alespoň jeden koncový bod služby virtuální sítě nebude příslušný obor názvů Service Bus nadále přijímat přenosy z odkudkoli, ale z autorizovaných virtuálních sítí a volitelně i konkrétních IP adres Internetu. Z perspektivy virtuální sítě naváže obor názvů Service Bus na koncový bod služby a nakonfiguruje izolovaný síťový tunel z podsítě virtuální sítě do služby zasílání zpráv.

Výsledkem je privátní a izolovaný vztah mezi úlohami vázanými na podsíť a odpovídajícím oborem názvů Service Bus, a to i přes pozorovatelnou síťovou adresu koncového bodu služby zasílání zpráv ve veřejném rozsahu IP adres.

## <a name="important-points"></a>Důležité body
- Virtuální sítě se podporují jenom v oborech názvů Service Bus [úrovně Premium](service-bus-premium-messaging.md) . Při použití koncových bodů služby virtuální sítě s Service Bus byste neměli povolit tyto koncové body v aplikacích, které používají obory názvů Service Bus úrovně Standard a Premium. Vzhledem k tomu, že úroveň Standard nepodporuje virtuální sítě. Koncový bod je omezený jenom na obory názvů úrovně Premium.
- Implementace integrace virtuálních sítí může ostatním službám Azure zabránit v interakci s Service Bus. V případě výjimky můžete povolit přístup k Service Bus prostředkům z určitých **důvěryhodných služeb** i v případě, že jsou povolené koncové body síťové služby. Seznam důvěryhodných služeb najdete v tématu [důvěryhodné služby](#trusted-microsoft-services).

    Následující služby společnosti Microsoft musí být ve virtuální síti.
    - Azure App Service
    - Azure Functions
- Zadejte **alespoň jedno pravidlo IP nebo pravidlo virtuální sítě** pro obor názvů, aby bylo možné provozovat pouze ze zadaných IP adres nebo podsítě virtuální sítě. Pokud neexistují žádná pravidla IP a virtuální sítě, můžete k oboru názvů přistupovat prostřednictvím veřejného Internetu (pomocí přístupového klíče).  

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Pokročilé scénáře zabezpečení povolené integrací virtuální sítě 

Řešení, která vyžadují těsné a compartmentalized zabezpečení a kde podsítě virtuální sítě poskytují segmentaci mezi službami compartmentalized, obecně potřebují cesty komunikace mezi službami, které se nacházejí v těchto oddílech.

Veškerá okamžitá trasa IP mezi oddíly, včetně těch, které přenáší HTTPS přes protokol TCP/IP, přináší riziko zneužití chyb zabezpečení z síťové vrstvy. Služby zasílání zpráv poskytují zcela izolované komunikační cesty, ve kterých se při přechodu mezi stranami na disk zapisují i zprávy. Úlohy ve dvou různých virtuálních sítích, které jsou vázané na stejnou instanci Service Bus, mohou komunikovat efektivně a spolehlivě prostřednictvím zpráv, zatímco příslušná integrita hranice izolace sítě zůstane zachovaná.
 
To znamená, že vaše cloudová řešení citlivá na zabezpečení nezískají přístup k špičkovým spolehlivým a škálovatelným funkcím asynchronního zasílání zpráv, ale teď můžou pomocí zasílání zpráv vytvářet cesty pro komunikaci mezi oddíly zabezpečeného řešení, které jsou z vlastního hlediska bezpečnější než ty, které jsou dosažitelné pro libovolný režim komunikace peer-to-peer, včetně protokolu HTTPS a dalších protokolů soketu zabezpečen

## <a name="binding-service-bus-to-virtual-networks"></a>Vytvoření vazby Service Bus k virtuálním sítím

*Pravidla virtuální sítě* jsou funkcí zabezpečení brány firewall, která určuje, jestli Server Azure Service Bus akceptuje připojení z konkrétní podsítě virtuální sítě.

Vytvoření vazby oboru názvů Service Bus k virtuální síti je proces se dvěma kroky. Nejdřív je potřeba vytvořit **koncový bod služby Virtual Network** v podsíti Virtual Network a povolit ho pro **Microsoft. ServiceBus** , jak je vysvětleno v tématu [Přehled koncového bodu služby][vnet-sep]. Po přidání koncového bodu služby navážete obor názvů Service Bus s **pravidlem virtuální sítě**.

Pravidlo virtuální sítě je přidružení oboru názvů Service Bus k podsíti virtuální sítě. I když toto pravidlo existuje, budou mít všechny úlohy vázané na podsíť přístup k oboru názvů Service Bus. Service Bus sám o sobě nenavazuje odchozí připojení, nemusí získat přístup, a proto nikdy neudělí přístup k podsíti tím, že toto pravidlo povolí.

> [!NOTE]
> Mějte na paměti, že koncový bod síťové služby poskytuje aplikacím běžícím ve virtuální síti přístup k oboru názvů Service Bus. Virtuální síť ovládá dostupnost koncového bodu, ale ne operace, které je možné provést na Service Bus entit (fronty, témata nebo odběry). Použijte Azure Active Directory (Azure AD) k autorizaci operací, které můžou aplikace provádět na oboru názvů a jejích entitách. Další informace najdete v tématu [ověřování a autorizace aplikace pomocí Azure AD pro přístup k Service Bus entit](authenticate-application.md).


## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části se dozvíte, jak pomocí Azure Portal přidat koncový bod služby virtuální sítě. Chcete-li omezit přístup, je nutné integrovat koncový bod služby virtuální sítě pro tento Event Hubs obor názvů.

1. V [Azure Portal](https://portal.azure.com)přejděte do **oboru názvů Service Bus** .
2. V nabídce vlevo vyberte v části **Nastavení** možnost **sítě** .  

    > [!NOTE]
    > Karta **síť** se zobrazí jenom pro obory názvů úrovně **Premium** .  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Stránka sítě – výchozí" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Pokud vyberete možnost **všechny sítě** , obor názvů Service Bus akceptuje připojení z libovolné IP adresy. Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0. 

    ![Firewall – vybraná možnost všechny sítě](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
2. Pokud chcete omezit přístup k určitým virtuálním sítím, vyberte možnost **vybrané sítě** , pokud už není vybraná.
1. V části **Virtual Network** stránky vyberte **+ Přidat existující virtuální síť**. 

    ![Přidat existující virtuální síť](./media/service-endpoints/add-vnet-menu.png)

    >[!WARNING]
    > Pokud vyberete možnost **vybrané sítě** a na tuto stránku nepřidáte aspoň jedno pravidlo firewallu IP nebo virtuální síť, přístup k oboru názvů se dá získat přes veřejný Internet (pomocí přístupové klávesy).
3. V seznamu virtuálních sítí vyberte virtuální síť a pak vyberte **podsíť**. Před přidáním virtuální sítě do seznamu musíte povolit koncový bod služby. Pokud koncový bod služby není povolený, portál vás vyzve, abyste ho povolili.
   
   ![vybrat podsíť](./media/service-endpoints/select-subnet.png)

4. Po povolení koncového bodu služby pro podsíť pro **Microsoft. ServiceBus** by se měla zobrazit následující úspěšná zpráva. V dolní části stránky vyberte **Přidat** a přidejte síť. 

    ![Vyberte podsíť a povolte koncový bod.](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Pokud nemůžete povolit koncový bod služby, můžete chybějící koncový bod služby virtuální sítě ignorovat pomocí šablony Správce prostředků. Tato funkce není na portálu k dispozici.
6. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů. Počkejte několik minut, než se potvrzení zobrazí v oznámeních na portálu. Tlačítko **Uložit** by mělo být zakázáno. 

    ![Uložit síť](./media/service-endpoints/save-vnet.png)

    > [!NOTE]
    > Pokyny k povolení přístupu z konkrétních IP adres nebo rozsahů najdete v tématu [Povolení přístupu z konkrétních IP adres nebo rozsahů](service-bus-ip-filtering.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru
Následující vzorová Správce prostředků šablona přidá pravidlo virtuální sítě do existujícího oboru názvů Service Bus. Pro síťové pravidlo určuje ID podsítě ve virtuální síti. 

ID je plně kvalifikovaná cesta Správce prostředků pro podsíť virtuální sítě. Například `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` pro výchozí podsíť virtuální sítě.

Při přidávání pravidel virtuální sítě nebo bran firewall nastavte hodnotu `defaultAction` na `Deny` .

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

Pokud chcete nasadit šablonu, postupujte podle pokynů [Azure Resource Manager][lnk-deploy].

> [!IMPORTANT]
> Pokud neexistují žádná pravidla pro IP a virtuální sítě, veškerý provoz se předává do oboru názvů, a to i v případě, že nastavíte `defaultAction` na `deny` .  K oboru názvů se dá získat přístup prostřednictvím veřejného Internetu (pomocí přístupového klíče). Zadejte alespoň jedno pravidlo IP nebo pravidlo virtuální sítě pro obor názvů, aby bylo možné provozovat pouze ze zadaných IP adres nebo podsítě virtuální sítě.  

## <a name="next-steps"></a>Další kroky

Další informace o virtuálních sítích najdete v následujících odkazech:

- [Koncové body služby virtuální sítě Azure][vnet-sep]
- [Azure Service Bus filtrování IP adres][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
