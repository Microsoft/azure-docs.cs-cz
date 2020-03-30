---
title: Správa tajných klíčů síťových sítí infrastruktury azure service
description: Správa tajných kódů aplikací, abyste mohli bezpečně vytvářet a nasazovat aplikaci Service Fabric Mesh.
ms.date: 4/2/2019
ms.topic: conceptual
ms.openlocfilehash: d7946092a0bebe374404870fcd711ad33cc98b11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461931"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Správa tajných klíčů síťových aplikací service fabric
Service Fabric Mesh podporuje tajné kódy jako prostředky Azure. Tajný klíč sítě Service Fabric může být jakékoli citlivé textové informace, jako jsou připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by měly být uloženy a přenášeny bezpečně. Tento článek ukazuje, jak používat service fabric zabezpečené úložiště přihlašovacích informací ke nasazení a údržbě tajných kódů.

Tajný klíč síťové sítě se skládá z:
* A **Secrets** prostředek, což je kontejner, který ukládá tajné klíče textu. Tajné klíče obsažené v prostředku **tajné klíče** jsou uloženy a přenášeny bezpečně.
* Jeden nebo více prostředků **tajných kódů a hodnot,** které jsou uloženy v kontejneru prostředků **tajné klíče.** Každý prostředek **Tajné klíče a hodnoty** se vyznačuje číslem verze. Nelze změnit verzi prostředku **tajné klíče a hodnoty,** pouze připojit novou verzi.

Správa tajných kódů se skládá z následujících kroků:
1. Deklarujte prostředek **tajných kódů** sítě v souboru YAML nebo JSON modelu Azure pomocí definic contentType inlinedValue a SecretsStoreRef.
2. Deklarujte prostředky **tajných kódů a hodnot** sítě v souboru YAML nebo JSON modelu Azure, který bude uložen v prostředku **tajné kódy** (z kroku 1).
3. Upravte aplikaci Mřížka tak, aby odkazovala na tajné hodnoty sítě.
4. Nasazení nebo postupné upgradování aplikace Mesh využívat tajné hodnoty.
5. Pro správu životního cyklu služby Zabezpečené úložiště přihlašovacích zařízení použijte příkazy příkazu příkazu příkazu příkazu příkazu Příkaz příkazu AZ Azure.

## <a name="declare-a-mesh-secrets-resource"></a>Deklarovat prostředek tajných kódů sítě
Prostředek mřížky secrets je deklarován v souboru JSON nebo YAML modelu Azure pomocí definice druhu inlinedValue. Prostředek mřížky secrets podporuje tajné kódy služby Zabezpečené úložiště dat. 
>
Následuje příklad, jak deklarovat prostředky tajných kódů sítě v souboru JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
Následuje příklad, jak deklarovat prostředky tajných kódů sítě v souboru YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Deklarovat prostředky tajných kódů/hodnot sítě
Prostředky tajných kódů a hodnot sítě mají závislost na prostředcích tajných kódů sítě definovaných v předchozím kroku.

Pokud jde o vztah mezi "zdroje" sekce "hodnota:" a "name:" pole: druhá část řetězce "name:" vymezuje dvojtečka je číslo verze používané pro tajný klíč a název před dvojtečkou musí odpovídat mesh tajný klíč hodnotu, pro kterou má Závislost. Například pro ```name: mysecret:1.0```element je číslo verze 1.0 ```mysecret``` a název ```"value": "mysecret"```se musí shodovat s dříve definovaným .

>
Následuje příklad, jak deklarovat prostředky tajných kódů/hodnot sítě v souboru JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
Následuje příklad, jak deklarovat prostředky tajných kódů/hodnot sítě v souboru YAML:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Úprava síťové aplikace pro odkazování na tajné hodnoty sítě
Aplikace Service Fabric Mesh musí být vědomi následující dva řetězce, aby bylo možné využívat zabezpečené úložiště tajných hodnot služby:
1. Microsoft.ServiceFabricMesh/Secrets.name obsahuje název souboru a bude obsahovat hodnotu Secrets ve formátu prostého textu.
2. Proměnná prostředí Systému Windows nebo Linux "Fabric_SettingPath" obsahuje cestu k adresáři, kam budou přístupné soubory obsahující hodnoty tajných kódů služby Zabezpečené úložiště přihlašovacích informací. Toto je "C:\Settings" pro Windows hostované a "/var/settings" pro aplikace Mesh hostované v Linuxu.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Nasazení nebo použití postupné inovace pro aplikaci Mesh ke spotřebě tajných hodnot
Vytváření tajných kódů nebo verzí tajných kódů/hodnot je omezeno na nasazení deklarovaná modelem prostředků. Jediným způsobem, jak vytvořit tyto prostředky, je předání montovny modelu prostředků JSON nebo souboru YAML pomocí příkazu **nasazení sítě az** následujícím způsobem:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Příkazy příkazu k příkazu Azure pro správu životního cyklu služby Zabezpečené úložiště přihlašovacích zařízení

### <a name="create-a-new-secrets-resource"></a>Vytvoření nového prostředku tajných kódů
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Předat buď **šablona-soubor** nebo **template-uri** (ale ne obojí).

Například:
- Nasazení sítě az create --c:\MyMeshTemplates\SecretTemplate1.txt
- az mesh nasazení vytvořit\/--https: /www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Zobrazit tajemství
Vrátí popis tajného klíče (ale ne hodnotu).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Odstranění tajného klíče

- Tajný klíč nelze odstranit, pokud je odkazován aplikací sítě.
- Odstraněním prostředku secrets odstraníte všechny verze tajných kódů a prostředků.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Seznam tajných kódů v předplatném
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Seznam tajných kódů ve skupině prostředků
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Vypsat všechny verze tajného klíče
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Zobrazit hodnotu tajné verze
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Odstranit hodnotu tajné verze
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Další kroky 
Další informace o síti Service Fabric Mesh najdete v přehledu:
- [Service Fabric Mesh – přehled](service-fabric-mesh-overview.md)
