---
title: Vytváření prostředí ISEs (Integration Service Environment) pomocí Logic Apps REST API
description: Vytvořte prostředí ISE (Integration Service Environment) pomocí REST API Logic Apps, abyste mohli získat přístup k Azure Virtual Networks (virtuální sítě) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d4500229800fa5d1743779b29927637777647e47
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550653"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Vytvoření prostředí integrační služby (ISE) s využitím rozhraní REST API služby Logic Apps

Pro scénáře, ve kterých aplikace logiky a účty pro integraci potřebují přístup k [virtuální síti Azure](../virtual-network/virtual-networks-overview.md), můžete pomocí REST API Logic Apps vytvořit [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . Další informace o ISEs najdete v tématu [přístup k prostředkům Azure Virtual Network z Azure Logic Apps](connect-virtual-network-vnet-isolated-environment-overview.md).

V tomto článku se dozvíte, jak vytvořit ISE pomocí REST API Logic Apps všeobecně. V případě potřeby můžete také ve svých ISE povolit [spravovanou identitu přiřazenou systémem nebo uživateli](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) , ale jenom pomocí Logic Apps REST API v tuto chvíli. Tato identita umožňuje vašemu ISE ověřovat přístup k zabezpečeným prostředkům, například k virtuálním počítačům a jiným systémům nebo službám, které jsou v systému nebo připojeny k virtuální síti Azure. Tímto způsobem se nemusíte přihlašovat pomocí svých přihlašovacích údajů.

Další informace o dalších způsobech vytvoření ISE najdete v těchto článcích:

* [Vytvoření ISE pomocí Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Vytvoření ISE pomocí ukázkové Azure Resource Manager šablony pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Vytvoření ISE, který podporuje použití klíčů spravovaných zákazníkem k šifrování neaktivních dat](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Požadavky

* Stejné [předpoklady](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) a [požadavky na přístup](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jako při vytváření ISE v Azure Portal

* Všechny další prostředky, které chcete použít se svým ISE, abyste mohli zahrnout jejich informace do definice ISE, například: 

  * Pokud chcete povolit podporu certifikátů podepsaných svým držitelem, musíte do definice ISE zahrnout informace o tomto certifikátu.

  * Pokud chcete povolit spravovanou identitu přiřazenou uživatelem, musíte tuto identitu vytvořit předem a do `objectId` `principalId` definice ISE zahrnout vlastnosti a a `clientId` jejich hodnoty. Další informace najdete v tématu [Vytvoření spravované identity přiřazené uživatelem v Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

* Nástroj, který můžete použít k vytvoření ISE, voláním Logic Apps REST API s požadavkem PUT protokolu HTTPS. Můžete například použít [post](https://www.getpostman.com/downloads/)nebo můžete vytvořit aplikaci logiky, která provede tuto úlohu.

## <a name="create-the-ise"></a>Vytvoření ISE

Pokud chcete vytvořit ISE voláním REST API Logic Apps, udělejte tuto žádost o vložení HTTPS:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Verze Logic Apps REST API 2019-05-01 vyžaduje, abyste si pro konektory ISE napravili vlastní požadavek HTTP PUT.

Nasazení obvykle trvá do dvou hodin, než se dokončí. V některých případech může nasazení trvat až čtyři hodiny. Pokud chcete zjistit stav nasazení, v [Azure Portal](https://portal.azure.com)na panelu nástrojů Azure vyberte ikonu oznámení, která otevře podokno oznámení.

> [!NOTE]
> Pokud se nasazení nepovede nebo odstraníte ISE, může Azure trvat až hodinu, než se vaše podsítě uvolní. Tato prodleva znamená, že možná budete muset počkat, než tyto podsítě znovu použijete v jiné ISE.
>
> Pokud virtuální síť odstraníte, Azure obvykle trvá až dvě hodiny, než se uvolní vaše podsítě, ale tato operace může trvat delší dobu. 
> Při odstraňování virtuálních sítí se ujistěte, že nejsou připojené žádné prostředky. 
> Viz [odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Hlavička požadavku

V hlavičce požadavku zahrňte tyto vlastnosti:

* `Content-type`: Nastavte tuto vlastnost na hodnotu `application/json` .

* `Authorization`: Nastavte tuto hodnotu vlastnosti na nosný token pro zákazníka, který má přístup k předplatnému Azure nebo skupině prostředků, kterou chcete použít.

<a name="request-body"></a>

## <a name="request-body"></a>Text požadavku

V textu žádosti zadejte definici prostředků, která se má použít k vytvoření vašeho ISEu, včetně informací o dalších funkcích, které chcete povolit na ISE, například:

* Chcete-li vytvořit ISE, který umožňuje používat certifikát podepsaný svým držitelem a certifikát vydaný v umístění podniková certifikační autorita, která je nainstalována v `TrustedRoot` umístění, zahrňte `certificates` objekt do oddílu definice ISE `properties` , jak je popsáno dále v tomto článku.

* Pokud chcete vytvořit ISE, který používá spravovanou identitu přiřazenou systémem nebo uživatelem, zahrňte `identity` objekt se spravovaným typem identity a dalšími požadovanými informacemi v definici ISE, jak je uvedeno dále v tomto článku.

* Pokud chcete vytvořit ISE, který používá klíče spravované zákazníkem a Azure Key Vault k šifrování neaktivních dat, uveďte [informace, které umožňují zákaznickou podporu](customer-managed-keys-integration-service-environment.md). Klíče spravované zákazníkem můžete nastavit *jenom při vytváření*, ne později.

### <a name="request-body-syntax"></a>Syntaxe textu žádosti

Tady je syntaxe textu žádosti, která popisuje vlastnosti, které se mají použít při vytváření ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Příklad textu žádosti

Tento ukázkový text požadavku zobrazuje ukázkové hodnoty:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>Přidat vlastní kořenové certifikáty

Často používáte ISE k připojení k vlastním službám ve vaší virtuální síti nebo místně. Tyto vlastní služby jsou často chráněny certifikátem vydaným vlastní kořenovou certifikační autoritou, jako je například podniková certifikační autorita nebo certifikát podepsaný svým držitelem. Další informace o použití certifikátů podepsaných svým držitelem najdete v tématech [zabezpečený přístup a přístup k datům pro odchozí hovory na jiné služby a systémy](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests). Aby se vaše ISE úspěšně připojovala k těmto službám prostřednictvím protokolu TLS (Transport Layer Security), potřebuje vaše ISE přístup k těmto kořenovým certifikátům.

#### <a name="considerations-for-adding-custom-root-certificates"></a>Pokyny pro přidání vlastních kořenových certifikátů

Před aktualizací ISE pomocí vlastního důvěryhodného kořenového certifikátu si přečtěte tyto požadavky:

* Ujistěte se, že jste nahráli kořenový certifikát *a* všechny zprostředkující certifikáty. Maximální počet certifikátů je 20.

* Odesílání kořenových certifikátů je operace nahrazení, kde nejnovější nahrávání přepíše předchozí nahrávání. Pokud třeba odešlete požadavek, který nahraje jeden certifikát, a pak odešlete další požadavek na nahrání jiného certifikátu, ISE použije jenom druhý certifikát. Pokud potřebujete použít oba certifikáty, přidejte je společně do stejného požadavku.  

* Nahrávání kořenových certifikátů je asynchronní operace, která může nějakou dobu trvat. Chcete-li zjistit stav nebo výsledek, můžete odeslat `GET` žádost pomocí stejného identifikátoru URI. Zpráva s odpovědí obsahuje `provisioningState` pole, které vrací `InProgress` hodnotu, když operace nahrávání stále pracuje. Je `provisioningState` -li hodnota `Succeeded` , operace odeslání je dokončena.

#### <a name="request-syntax"></a>Syntaxe žádosti

Pokud chcete ISE aktualizovat pomocí vlastního důvěryhodného kořenového certifikátu, odešlete následující požadavek na opravu HTTPS na [adresu URL Azure Resource Manager, která se liší v závislosti na vašem prostředí Azure](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane), například:

| Prostředí | Adresa URL Azure Resource Manager |
|-------------|----------------------------|
| Globální Azure (více tenantů) | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure China 21Vianet | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>Syntaxe textu žádosti pro přidání vlastních kořenových certifikátů

Tady je syntaxe textu žádosti, která popisuje vlastnosti, které se mají použít při přidávání kořenových certifikátů:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>Další kroky

* [Přidání prostředků do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
