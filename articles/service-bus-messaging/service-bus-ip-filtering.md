---
title: Konfigurace pravidel brány firewall protokolu IP pro Azure Service Bus
description: Jak používat pravidla brány firewall k povolení Azure Service Bus Připojení z konkrétních IP adres.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: 747e15e912033c5bc6a1f64ab389f1ab03f40c0b
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962318"
---
# <a name="allow-access-to-azure-service-bus-namespace-from-specific-ip-addresses-or-ranges"></a>Povolení přístupu k oboru názvů Azure Service Bus z konkrétních IP adres nebo rozsahů
Ve výchozím nastavení jsou Service Bus obory názvů přístupné z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP je můžete omezit na další jenom na sadu IPv4 adres nebo rozsahů IPv4 adres v zápisu [CIDR (bez třídy) (směrování Inter-Domain)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) .

Tato funkce je užitečná ve scénářích, ve kterých Azure Service Bus by měly být dostupné jenom z určitých dobře známých lokalit. Pravidla brány firewall umožňují konfigurovat pravidla pro příjem provozu pocházejících z konkrétních IPv4 adres. Pokud například používáte Service Bus s využitím [Azure Express Route][express-route], můžete vytvořit **pravidlo brány firewall** , které umožní provoz jenom z místních IP adres nebo adres podnikové brány NAT. 

## <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP
Pravidla brány firewall protokolu IP se používají na úrovni oboru názvů Service Bus. Proto se pravidla vztahují na všechna připojení z klientů pomocí libovolného podporovaného protokolu. Všechny pokusy o připojení z IP adresy, které neodpovídají povolenému pravidlu IP v oboru názvů Service Bus, se odmítnou jako neoprávněné. Odpověď nezmiňuje pravidlo protokolu IP. Pravidla filtru IP se aplikují v pořadí a první pravidlo, které odpovídá IP adrese, určuje akci přijmout nebo odmítnout.

## <a name="important-points"></a>Důležité body
- Brány firewall a virtuální sítě se podporují jenom v Service Bus úrovně **Premium** . Pokud upgrade na úroveň **Premier** není možnost, doporučujeme, abyste zachovali zabezpečený token (SAS) sdíleného přístupového podpisu (SAS) a sdíleli ho jenom autorizovaní uživatelé. Informace o ověřování SAS najdete v tématu [ověřování a autorizace](service-bus-authentication-and-authorization.md#shared-access-signature).
- Zadejte **alespoň jedno pravidlo brány firewall protokolu IP nebo pravidlo virtuální sítě** pro obor názvů, aby bylo možné provozovat pouze ze zadaných IP adres nebo podsítí virtuální sítě. Pokud neexistují žádná pravidla IP a virtuální sítě, můžete k oboru názvů přistupovat prostřednictvím veřejného Internetu (pomocí přístupového klíče).  
- Implementace pravidel brány firewall může zabránit ostatním službám Azure v interakci s Service Bus. V případě výjimky můžete povolit přístup k Service Bus prostředkům z určitých **důvěryhodných služeb** i v případě, že je povolené filtrování IP adres. Seznam důvěryhodných služeb najdete v tématu [důvěryhodné služby](#trusted-microsoft-services). 

    Následující služby společnosti Microsoft musí být ve virtuální síti.
    - Azure App Service
    - Azure Functions

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části se dozvíte, jak pomocí Azure Portal vytvořit pravidla brány firewall protokolu IP pro Service Bus obor názvů. 

1. V [Azure Portal](https://portal.azure.com)přejděte do **oboru názvů Service Bus** .
2. V nabídce vlevo vyberte v části **Nastavení** možnost **sítě** .  

    > [!NOTE]
    > Karta **síť** se zobrazí jenom pro obory názvů úrovně **Premium** .  
    
    :::image type="content" source="./media/service-bus-ip-filtering/default-networking-page.png" alt-text="Stránka sítě – výchozí" lightbox="./media/service-bus-ip-filtering/default-networking-page.png":::
    
    Pokud vyberete možnost **všechny sítě** , obor názvů Service Bus akceptuje připojení z libovolné IP adresy. Toto výchozí nastavení odpovídá pravidlu, které přijímá rozsah IP adres 0.0.0.0/0. 

    ![Snímek obrazovky stránky Azure Portal sítě Možnost povolení přístupu ze všech sítí je vybrána na kartě brány firewall a virtuální sítě.](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Pokud chcete povolený přístup jenom ze zadané IP adresy, vyberte možnost **vybrané sítě** , pokud už není vybraná. V části **Brána firewall** postupujte podle následujících kroků:
    1. Vyberte možnost **Přidat IP adresu klienta** a poskytněte vaší aktuální IP adrese přístup k oboru názvů. 
    2. Pro **Rozsah adres** zadejte konkrétní IPv4 adresu nebo rozsah adres IPv4 v zápisu CIDR. 
    3. Určete, zda chcete, aby **důvěryhodné služby společnosti Microsoft vynechal tuto bránu firewall**. 

        >[!WARNING]
        > Pokud vyberete možnost **vybrané sítě** a na tuto stránku nepřidáte aspoň jedno pravidlo firewallu IP nebo virtuální síť, přístup k oboru názvů se dá získat přes veřejný Internet (pomocí přístupové klávesy).    

        ![Snímek obrazovky stránky Azure Portal sítě Je vybraná možnost pro povolení přístupu z vybraných sítí a zvýrazní se oddíl brány firewall.](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů. Počkejte několik minut, než se potvrzení zobrazí v oznámeních na portálu.

    > [!NOTE]
    > Pokud chcete omezit přístup k určitým virtuálním sítím, přečtěte si téma [Povolení přístupu z konkrétních sítí](service-bus-service-endpoints.md).

[!INCLUDE [service-bus-trusted-services](../../includes/service-bus-trusted-services.md)]

## <a name="use-resource-manager-template"></a>Použití šablony Resource Manageru
Tato část obsahuje ukázkovou Azure Resource Manager šablonu, která přidá virtuální síť a pravidlo brány firewall do existujícího oboru názvů Service Bus.

**ipMask** je jedna adresa IPv4 nebo blok IP adres v zápisu CIDR. Například v zápisu CIDR 70.37.104.0/24 představuje 256 IPv4 adres z 70.37.104.0 do 70.37.104.255, přičemž 24 značí počet významných bitů předpony pro daný rozsah.

Při přidávání pravidel virtuální sítě nebo bran firewall nastavte hodnotu `defaultAction` na `Deny` .


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
        "type": "Microsoft.ServiceBus/namespaces/networkrulesets",
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

Pokud chcete nasadit šablonu, postupujte podle pokynů [Azure Resource Manager][lnk-deploy].

> [!IMPORTANT]
> Pokud neexistují žádná pravidla pro IP a virtuální sítě, veškerý provoz se předává do oboru názvů, a to i v případě, že nastavíte `defaultAction` na `deny` . K oboru názvů se dá získat přístup prostřednictvím veřejného Internetu (pomocí přístupového klíče). Zadejte alespoň jedno pravidlo IP nebo pravidlo virtuální sítě pro obor názvů, aby bylo možné provozovat pouze ze zadaných IP adres nebo podsítě virtuální sítě.  


## <a name="next-steps"></a>Další kroky

Informace o omezení přístupu k Service Bus k virtuálním sítím Azure najdete na následujícím odkazu:

- [Virtual Network koncové body služby pro Service Bus][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  ../expressroute/expressroute-faqs.md#supported-services