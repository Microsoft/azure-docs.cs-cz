---
title: Pravidla brány firewall pro Azure Event Hubs | Microsoft Docs
description: Pomocí pravidel brány firewall povolte připojení z konkrétních IP adres do Azure Event Hubs.
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 596d506c0c4f6d79696b3019fd903e549149c656
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92056204"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>Povolení přístupu k oborům názvů Azure Event Hubs z konkrétních IP adres nebo rozsahů
Ve výchozím nastavení jsou Event Hubs obory názvů přístupné z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na další jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v zápisu [CIDR (bez třídy) (směrování Inter-Domain)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Tato funkce je užitečná ve scénářích, ve kterých by měl být Azure Event Hubs dostupný jenom z určitých dobře známých lokalit. Pravidla brány firewall umožňují konfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Pokud například používáte Event Hubs s využitím [Azure Express Route][express-route], můžete vytvořit **pravidlo brány firewall** , které umožní provoz jenom z vašich místních IP adres infrastruktury. 

>[!IMPORTANT]
> Pokud požadavky pocházejí ze služby, která není povolená pro veřejné IP adresy, zapnete pravidla brány firewall pro obor názvů Event Hubs zablokuje příchozí požadavky ve výchozím nastavení. Blokované požadavky zahrnují ty z jiných služeb Azure, od Azure Portal, ze služeb protokolování a metriky atd. 
>
> Tady jsou některé ze služeb, které nemůžou získat přístup k Event Hubs prostředkům, když je povolené filtrování IP adres. Všimněte si, že seznam **není vyčerpávající.**
>
> - Azure Stream Analytics
> - Trasy k Azure IoT Hub
> - Device Explorer Azure IoT
> - Azure Event Grid
> - Azure Monitor (nastavení diagnostiky)
>
> V případě výjimky můžete povolit přístup k Event Hubs prostředkům z určitých důvěryhodných služeb i v případě, že je povolené filtrování IP adres. Seznam důvěryhodných služeb najdete v tématu [důvěryhodné služby společnosti Microsoft](#trusted-microsoft-services).

## <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP
Pravidla brány firewall protokolu IP se používají na úrovni oboru názvů Event Hubs. Pravidla se proto vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu. Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v oboru názvů Event Hubs, jsou odmítnuty jako neautorizované. Odpověď nezmiňuje pravidlo protokolu IP. Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části se dozvíte, jak pomocí Azure Portal vytvořit pravidla brány firewall protokolu IP pro Event Hubs obor názvů. 

1. V [Azure Portal](https://portal.azure.com)přejděte do **oboru názvů Event Hubs** .
4. V části **Nastavení** v nabídce vlevo vyberte **sítě** . Karta **síť** se zobrazí jenom pro **standardní** nebo **vyhrazené** obory názvů. 
    > [!NOTE]
    > Ve výchozím nastavení je vybraná možnost **vybrané sítě** , jak je znázorněno na následujícím obrázku. Pokud nezadáte pravidlo brány firewall protokolu IP nebo přidáte virtuální síť na této stránce, přístup k oboru názvů se dá získat prostřednictvím **veřejného Internetu** (pomocí přístupového klíče).  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="Karta sítě – volba vybraných sítí" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    Pokud vyberete možnost **všechny sítě** , centrum událostí přijme připojení z libovolné IP adresy (pomocí přístupového klíče). Toto nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0. 

    ![Snímek obrazovky zobrazující stránku brány firewall a virtuální sítě se zvolenou možností všechny sítě](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. Pokud chcete omezit přístup ke konkrétním IP adresám, potvrďte, že je vybraná možnost **vybraná síť** . V části **Brána firewall** postupujte podle následujících kroků:
    1. Vyberte možnost **Přidat IP adresu klienta** a poskytněte vaší aktuální IP adrese přístup k oboru názvů. 
    2. Pro **Rozsah adres**zadejte konkrétní IPv4 adresu nebo rozsah adres IPv4 v zápisu CIDR. 
3. Určete, zda chcete, aby **důvěryhodné služby společnosti Microsoft vynechal tuto bránu firewall**. Podrobnosti najdete v tématu [důvěryhodné služby Microsoftu](#trusted-microsoft-services) . 

      ![Firewall – vybraná možnost všechny sítě](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů. Počkejte několik minut, než se potvrzení zobrazí v oznámeních na portálu.

    > [!NOTE]
    > Pokud chcete omezit přístup k určitým virtuálním sítím, přečtěte si téma [Povolení přístupu z konkrétních sítí](event-hubs-service-endpoints.md).

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru

> [!IMPORTANT]
> Pravidla brány firewall jsou podporovaná na úrovni **Standard** a **vyhrazené** Event Hubs. Na úrovni Basic se nepodporuje.

Následující šablona Správce prostředků umožňuje přidat pravidlo filtru IP do existujícího oboru názvů Event Hubs.

Parametry šablony:

- **ipMask** je jedna adresa IPv4 nebo blok IP adres v zápisu CIDR. Například v zápisu CIDR 70.37.104.0/24 představuje 256 IPv4 adres z 70.37.104.0 do 70.37.104.255, přičemž 24 značí počet významných bitů předpony pro daný rozsah.

> [!NOTE]
> I když nejsou možná žádná pravidla odepření, má šablona Azure Resource Manager výchozí akci nastavenou na **Povolit** , což neomezuje připojení.
> Při vytváření pravidel pro Virtual Network nebo brány firewall je nutné změnit ***"defaultAction"*** .
> 
> Výsledkem
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
        "type": "Microsoft.EventHub/namespaces/networkrulesets",
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

Pokud chcete nasadit šablonu, postupujte podle pokynů [Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Další kroky

Informace o omezení přístupu k Event Hubs k virtuálním sítím Azure najdete na následujícím odkazu:

- [Virtual Network koncové body služby pro Event Hubs][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
