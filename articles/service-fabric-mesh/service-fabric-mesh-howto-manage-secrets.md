---
title: Správa tajných klíčů aplikací pro síť Azure Service Fabric
description: Spravujte tajné klíče aplikace, abyste mohli bezpečně vytvořit a nasadit aplikaci Service Fabric sítě.
ms.date: 4/2/2019
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 379560b73c38558fe0b712ed5e036c7a3736b600
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500704"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Správa tajných klíčů pro aplikace Service Fabric sítě
Service Fabricová síť podporuje tajné klíče jako prostředky Azure. Service Fabric se může jednat o informace o citlivých textech, jako jsou například připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by se měly bezpečně ukládat a přenášet. V tomto článku se dozvíte, jak používat Service Fabric Zabezpečené úložiště přihlašovacích údajů k nasazení a údržbě tajných kódů.

Tajný kód aplikace sítě se skládá z těchto součástí:
* Prostředek **tajných** kódů, což je kontejner, který ukládá text tajných kódů. Tajné kódy obsažené v prostředku **tajných** kódů se ukládají a bezpečně odesílají.
* Jeden nebo více prostředků **tajných klíčů a hodnot** , které jsou uloženy v kontejneru prostředků **tajných** kódů. Jednotlivé zdroje **tajných kódů a hodnot** jsou rozlišené číslem verze. Nemůžete změnit verzi prostředku **tajných** kódů a hodnot, připojit pouze novou verzi.

Správa tajných klíčů se skládá z následujících kroků:
1. Deklarujete prostředek **sítě pro** YAML nebo soubor JSON s použitím inlinedValue druhu a definice SecretsStoreRef ContentType.
2. Deklarujete prostředky sítě **/hodnoty** prostředků v YAML nebo souboru JSON modelu prostředku Azure, který bude uložený v prostředku **tajných** kódů (z kroku 1).
3. Upravte mřížkovou aplikaci tak, aby odkazovala na hodnoty tajného klíče sítě.
4. Nasaďte nebo proveďte upgrade aplikace sítě, aby se využívaly tajné hodnoty.
5. Pro správu životního cyklu Zabezpečené úložiště přihlašovacích údajů použijte příkazy rozhraní příkazového řádku Azure "AZ".

## <a name="declare-a-mesh-secrets-resource"></a>Deklarace prostředku sítě s tajnými klíči
Prostředek sítě s tajnými klíči je deklarovaný v souboru JSON nebo YAML v modelu prostředků Azure pomocí definice druhu inlinedValue. Prostředek sítě s tajnými klíči podporuje Zabezpečené úložiště přihlašovacích údajů zdroj tajných kódů. 
>
Následuje příklad deklarace prostředků tajného klíče sítě v souboru JSON:

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
Následuje příklad, jak deklarovat prostředky pro tajné klíče sítě v souboru YAML:
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

## <a name="declare-mesh-secretsvalues-resources"></a>Deklarovat informace o prostředcích a hodnotách sítě
Zdroje informací o prostředcích a hodnotách sítě mají závislost na prostředcích tajných klíčů definovaných v předchozím kroku.

Informace o vztahu mezi "prostředky" v části "hodnota" a "název:" pole: Druhá část řetězce "název:", která je oddělena dvojtečkou, je číslo verze používané pro tajný kód a název před dvojtečkou musí odpovídat hodnotě v tajnosti sítě, pro kterou má závislost. Například pro element je ```name: mysecret:1.0``` číslo verze 1,0 a název ```mysecret``` musí odpovídat dříve definovanému ```"value": "mysecret"``` .

>
Následuje příklad, jak deklarovat informace o prostředcích a hodnotách sítě v souboru JSON:

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
Následuje příklad, jak deklarovat informace o prostředcích a hodnotách sítě v souboru YAML:
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

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Úprava aplikace v síti pro odkazování na hodnoty v tajnosti
Aby bylo možné využívat hodnoty Zabezpečené úložiště přihlašovacích údajů tajného klíče, musí aplikace Service Fabric sítě obsahovat informace o následujících dvou řetězcích:
1. Microsoft. ServiceFabricMesh/tajných kódů. název obsahuje název souboru a bude obsahovat hodnotu tajných kódů ve formátu prostého textu.
2. Proměnná prostředí systému Windows nebo Linux "Fabric_SettingPath" obsahuje cestu k adresáři, do kterého budou přístupné soubory obsahující Zabezpečené úložiště přihlašovacích údajů tajné hodnoty tajných kódů. Toto je "C:\Settings" pro aplikace sítě s operačním systémem Linux a "/var/Settings" v uvedeném pořadí.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Nasazení nebo použití postupného upgradu pro aplikace sítě pro využívání tajných hodnot
Vytváření tajných klíčů a hodnot tajných kódů a hodnot je omezené na modelem deklarovaného nasazení v modelu prostředků. Jediným způsobem, jak tyto prostředky vytvořit, je předání souboru JSON nebo souboru YAML modelu prostředku pomocí příkazu **AZ oky Deployment** následujícím způsobem:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Příkazy rozhraní příkazového řádku Azure pro správu životního cyklu Zabezpečené úložiště přihlašovacích údajů

### <a name="create-a-new-secrets-resource"></a>Vytvořit nový prostředek tajných kódů
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Předejte buď **Template-File** , nebo **template-URI** (ale ne obojí).

Například:
- AZ Mesh Deployment Create --c:\MyMeshTemplates\SecretTemplate1.txt
- AZ oky Deployment Create--https: \/ /www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Zobrazit tajný kód
Vrátí popis tajného kódu (ale ne hodnoty).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Odstranění tajného klíče

- Tajný kód nelze odstranit, pokud na něj odkazuje aplikace sítě.
- Odstraněním prostředku tajných kódů dojde k odstranění všech tajných kódů a verzí prostředků.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Výpis tajných kódů v předplatném
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Výpis tajných kódů ve skupině prostředků
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Vypíše všechny verze tajného kódu.
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
Další informace o Service Fabric sítě najdete v článku Přehled:
- [Přehled Service Fabric sítě](service-fabric-mesh-overview.md)
