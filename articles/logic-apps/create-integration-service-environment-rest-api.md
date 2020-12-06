---
title: Vytváření prostředí ISEs (Integration Service Environment) pomocí Logic Apps REST API
description: Vytvořte prostředí ISE (Integration Service Environment) pomocí REST API Logic Apps, abyste mohli získat přístup k Azure Virtual Networks (virtuální sítě) z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 12/05/2020
ms.openlocfilehash: 783431c4888a68e24cf3d2603c541c4797ea65d8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741095"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Vytvoření prostředí integrační služby (ISE) s využitím rozhraní REST API služby Logic Apps

Pro scénáře, ve kterých aplikace logiky a účty pro integraci potřebují přístup k [virtuální síti Azure](../virtual-network/virtual-networks-overview.md), můžete pomocí REST API Logic Apps vytvořit [ *prostředí ISE (Integration Service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) . Další informace o ISEs najdete v tématu [přístup k prostředkům Azure Virtual Network z Azure Logic Apps](connect-virtual-network-vnet-isolated-environment-overview.md).

V tomto článku se dozvíte, jak vytvořit ISE pomocí REST API Logic Apps všeobecně. V případě potřeby můžete také ve svých ISE povolit [spravovanou identitu přiřazenou systémem nebo uživateli](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) , ale jenom pomocí Logic Apps REST API v tuto chvíli. Tato identita umožňuje vašemu ISE ověřovat přístup k zabezpečeným prostředkům, například k virtuálním počítačům a jiným systémům nebo službám, které jsou v systému nebo připojeny k virtuální síti Azure. Tímto způsobem se nemusíte přihlašovat pomocí svých přihlašovacích údajů.

Další informace o dalších způsobech vytvoření ISE najdete v těchto článcích:

* [Vytvoření ISE pomocí Azure Portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Vytvoření ISE pomocí ukázkové Azure Resource Manager šablony pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Vytvoření ISE, který podporuje použití klíčů spravovaných zákazníkem k šifrování neaktivních dat](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Předpoklady

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

* Chcete-li vytvořit ISE, který umožňuje používat certifikát podepsaný svým držitelem, který je nainstalován v `TrustedRoot` umístění, zahrňte `certificates` objekt do oddílu definice ISE `properties` , jak je uvedeno dále v tomto článku.

  Pokud chcete tuto funkci povolit pro existující ISE, můžete odeslat žádost o opravu jenom pro `certificates` objekt. Další informace o použití certifikátů podepsaných svým držitelem najdete v tématech [zabezpečený přístup a přístup k datům pro odchozí hovory na jiné služby a systémy](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests).

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
      // Include `certificates` object to enable self-signed certificate support
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
```

## <a name="next-steps"></a>Další kroky

* [Přidání prostředků do prostředí integrační služby](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Správa prostředí integrační služby](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
