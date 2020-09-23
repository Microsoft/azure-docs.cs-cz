---
title: Konfigurace brány firewall protokolu IP pro Azure Relay obor názvů
description: Tento článek popisuje, jak používat pravidla brány firewall k povolení připojení z konkrétních IP adres k oborům názvů Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 250158aff2ceb89e2823b711717f1d3a1cad438c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90976012"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Konfigurace brány firewall protokolu IP pro obor názvů Azure Relay
Ve výchozím nastavení jsou obory názvů Relay přístupné z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na více než jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v [CIDR (směrování mezi doménami bez třídy)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Tato funkce je užitečná ve scénářích, ve kterých Azure Relay by měly být dostupné jenom z určitých dobře známých lokalit. Pravidla brány firewall umožňují konfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Pokud například používáte předávání pomocí [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services), můžete vytvořit **pravidlo brány firewall** , které umožní provoz jenom z místních IP adres infrastruktury. 


> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. 


## <a name="enable-ip-firewall-rules"></a>Povolit pravidla brány firewall protokolu IP
Pravidla brány firewall protokolu IP se používají na úrovni oboru názvů. Proto se pravidla vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu. Jakýkoli pokus o připojení z IP adresy, která neodpovídá povolenému pravidlu IP v oboru názvů, je odmítnut jako neautorizovaný. Odpověď nezmiňuje pravidlo protokolu IP. Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

### <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části se dozvíte, jak pomocí Azure Portal vytvořit pravidla brány firewall protokolu IP pro obor názvů. 

1. V [Azure Portal](https://portal.azure.com)přejděte do **oboru názvů služby Relay** .
2. V nabídce vlevo vyberte možnost **sítě** . Pokud v části **Povolení přístupu z** vyberete možnost **všechny sítě** , obor názvů Relay akceptuje připojení z libovolné IP adresy. Toto nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0. 

    ![Snímek obrazovky s vybranou možností všechny sítě zobrazí stránku síť.](./media/ip-firewall/all-networks-selected.png)
1. Pokud chcete omezit přístup k určitým sítím a IP adresám, vyberte možnost **vybrané sítě** . V části **Brána firewall** postupujte podle následujících kroků:
    1. Vyberte možnost **Přidat IP adresu klienta** a poskytněte vaší aktuální IP adrese přístup k oboru názvů. 
    2. Pro **Rozsah adres**zadejte konkrétní IPv4 adresu nebo rozsah adres IPv4 v zápisu CIDR. 

        ![Firewall – vybraná možnost všechny sítě](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů. Počkejte několik minut, než se potvrzení zobrazí v oznámeních na portálu.


### <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru
Následující šablona Správce prostředků umožňuje přidat pravidlo filtru IP do existujícího oboru názvů Relay.

Šablona přijímá jeden parametr: **ipMask**, což je jedna adresa IPv4 nebo blok IP adres v zápisu CIDR. Například v zápisu CIDR 70.37.104.0/24 představuje 256 IPv4 adres z 70.37.104.0 do 70.37.104.255, přičemž 24 značí počet významných bitů předpony pro daný rozsah.

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
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
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
        "type": "Microsoft.Relay/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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

Pokud chcete nasadit šablonu, postupujte podle pokynů [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).



## <a name="next-steps"></a>Další kroky
Další informace o dalších funkcích souvisejících se zabezpečením sítě najdete v tématu [zabezpečení sítě](network-security.md).


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
