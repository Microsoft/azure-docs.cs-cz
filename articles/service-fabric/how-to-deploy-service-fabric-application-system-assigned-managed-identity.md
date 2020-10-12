---
title: Nasazení aplikace Service Fabric se systémem MI přiřazeným
description: V tomto článku se dozvíte, jak přiřadit spravovanou identitu přiřazenou systémem k aplikaci Azure Service Fabric.
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81415660"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>Nasazení aplikace Service Fabric se spravovanou identitou přiřazenou systémem

Aby bylo možné získat přístup k funkci Managed identity pro aplikace Service Fabric Azure, musíte nejdřív v clusteru povolit službu Managed identity token. Tato služba zodpovídá za ověřování Service Fabric aplikací pomocí svých spravovaných identit a pro získání přístupových tokenů jejich jménem. Jakmile je služba povolená, můžete ji zobrazit v Service Fabric Explorer v části **systém** v levém podokně, která je spuštěná pod názvem **Fabric:/System/ManagedIdentityTokenService** vedle ostatních systémových služeb.

> [!NOTE] 
> Nasazení aplikací Service Fabric se spravovanými identitami se podporuje od verze rozhraní API `"2019-06-01-preview"` . Můžete použít také stejnou verzi rozhraní API pro typ aplikace, verzi typu aplikace a prostředky služby. Minimální podporovaná Service Fabric runtime je 6,5 CU2. V additoin by prostředí pro sestavení nebo balení mělo mít také SF .NET SDK na CU2 nebo vyšší.

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

### <a name="application-template"></a>Šablona aplikace

Chcete-li povolit aplikaci se spravovanou identitou přiřazenou systémem, přidejte do prostředku aplikace vlastnost **identity** s typem **systemAssigned** , jak je znázorněno v následujícím příkladu:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Tato vlastnost deklaruje (pro Azure Resource Manager a poskytovatele prostředků spravované identity a Service Fabric, v uvedeném pořadí, že tento prostředek musí mít `system assigned` spravovanou () spravovanou identitu.

### <a name="application-and-service-package"></a>Balíček aplikace a služby

1. Aktualizujte manifest aplikace a přidejte tak element **ManagedIdentity** v části **objekty zabezpečení** obsahující jednu položku, jak je znázorněno níže:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Tato služba mapuje identitu přiřazenou aplikaci jako prostředek k popisnému názvu, aby bylo možné další přiřazení ke službám, které tvoří aplikaci. 

2. V části **ServiceManifestImport** , která odpovídá službě, ke které je přiřazena spravovaná identita, přidejte element **IdentityBindingPolicy** , jak je uvedeno níže:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Tento prvek přiřadí identitu aplikace ke službě. bez tohoto přiřazení nebude služba moci získat přístup k identitě aplikace. Ve výše uvedeném fragmentu kódu `SystemAssigned` je identita (což je rezervované klíčové slovo) namapována na definici služby pod popisným názvem `WebAdmin` .

3. Aktualizujte manifest služby pro přidání elementu **ManagedIdentity** do oddílu **Resources** s názvem, který odpovídá hodnotě `ServiceIdentityRef` nastavení z `IdentityBindingPolicy` definice v manifestu aplikace:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Jedná se o ekvivalentní mapování identity ke službě, jak je popsáno výše, ale z perspektivy definice služby. Na identitu tady odkazuje jeho popisný název ( `WebAdmin` ), jak je deklarované v manifestu aplikace.

## <a name="next-steps"></a>Další kroky
* Kontrola [podpory spravovaných identit](./concepts-managed-identity.md) v Azure Service Fabric
* [Nasadit novou](./configure-new-azure-service-fabric-enable-managed-identity.md) Cluster Azure Service Fabric s podporou spravované identity 
* [Povolení spravované identity](./configure-existing-cluster-enable-managed-identity-token-service.md) v existujícím clusteru Azure Service Fabric
* Využití spravované identity Service Fabric aplikace [ze zdrojového kódu](./how-to-managed-identity-service-fabric-app-code.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Udělení přístupu k aplikacím Azure Service Fabric k ostatním prostředkům Azure](./how-to-grant-access-other-resources.md)
