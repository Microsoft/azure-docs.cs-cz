---
title: Nasazení aplikace Service Fabric se systémem přiřazeným MI
description: Tento článek ukazuje, jak přiřadit systémem přiřazenou spravovanou identitu k aplikaci Azure Service Fabric
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: c5c7a17c51eee18d9b7276f2c57289a5de5c8181
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415660"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity"></a>Nasazení aplikace Service Fabric se spravovanou identitou přiřazenou systémem

Chcete-li získat přístup k funkci spravované identity pro aplikace Azure Service Fabric, musíte nejprve povolit službu Token spravované identity v clusteru. Tato služba je zodpovědná za ověřování aplikací Service Fabric pomocí jejich spravovaných identit a za získání přístupových tokenů jejich jménem. Jakmile je služba povolena, můžete ji vidět v aplikaci Service Fabric Explorer v části **Systém** v levém podokně, spuštěné pod názvem **fabric:/System/ManagedIdentityTokenService** vedle jiných systémových služeb.

> [!NOTE] 
> Nasazení aplikací Service Fabric se spravovanými `"2019-06-01-preview"`identitami je podporováno počínaje verzí rozhraní API . Můžete také použít stejnou verzi rozhraní API pro typ aplikace, verzi typu aplikace a prostředky služby. Minimální podporovaný modul runtime Service Fabric je 6,5 CU2. V additoin, sestavení / balíček prostředí by měl mít také SF .Net SDK na CU2 nebo vyšší

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

### <a name="application-template"></a>Šablona aplikace

Chcete-li povolit aplikaci se systémem přiřazenou spravovanou identitou, přidejte vlastnost **identity** do prostředku aplikace s **typem systemAssigned,** jak je znázorněno v příkladu níže:

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
Tato vlastnost deklaruje (Správce prostředků Azure a zprostředkovatelé prostředků spravované identity a prostředků`system assigned`infrastruktury služeb, respektive, že tento prostředek musí mít implicitní ( ) spravované identity.

### <a name="application-and-service-package"></a>Balíček aplikací a služeb

1. Aktualizujte manifest aplikace a přidejte element **ManagedIdentity** do části **Principals** obsahující jednu položku, jak je znázorněno níže:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    To mapuje identitu přiřazenou aplikaci jako prostředek na popisný název pro další přiřazení ke službám obsahujícím aplikaci. 

2. V části **ServiceManifestImport** odpovídající službě, které je přiřazena spravovaná identita, přidejte element **IdentityBindingPolicy,** jak je uvedeno níže:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Tento prvek přiřadí identitu aplikace službě; bez tohoto přiřazení nebude mít služba přístup k identitě aplikace. Ve výše uvedeném fragmentu `SystemAssigned` je identita (což je vyhrazené klíčové slovo) mapována `WebAdmin`na definici služby pod popisným názvem .

3. Aktualizujte manifest služby a přidejte prvek **ManagedIdentity** do části `ServiceIdentityRef` **Prostředky** s názvem odpovídajícím hodnotě nastavení z `IdentityBindingPolicy` definice v manifestu aplikace:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Toto je ekvivalentní mapování identity na službu, jak je popsáno výše, ale z hlediska definice služby. Identita je zde odkazována popisný název (`WebAdmin`), jak je uvedeno v manifestu aplikace.

## <a name="next-steps"></a>Další kroky
* Kontrola [podpory spravovaných identit](./concepts-managed-identity.md) ve službě Azure Service Fabric
* [Nasazení nového](./configure-new-azure-service-fabric-enable-managed-identity.md) Cluster Azure Service Fabric s podporou spravovaných identit 
* [Povolení spravované identity](./configure-existing-cluster-enable-managed-identity-token-service.md) v existujícím clusteru Azure Service Fabric
* Využití spravované identity aplikace Service Fabric [ze zdrojového kódu](./how-to-managed-identity-service-fabric-app-code.md)
* [Nasazení aplikace Azure Service Fabric s uživatelem přiřazenou spravovanou identitou](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Udělení přístupu aplikací Azure Service Fabric k dalším prostředkům Azure](./how-to-grant-access-other-resources.md)
