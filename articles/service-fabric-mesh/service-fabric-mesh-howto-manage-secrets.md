---
title: Správa tajných klíčů aplikací Azure Service Fabric sítě | Dokumentace Microsoftu
description: Správa tajných klíčů aplikací vám umožní bezpečně vytvoření a nasazení aplikace Service Fabric mřížky.
services: service-fabric-mesh
keywords: tajné kódy
author: aljo
ms.author: aljo
ms.date: 11/28/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: chackdan
ms.openlocfilehash: d92726ebc2cd4c6c44afdb2d2a9f53ab5441ac32
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893492"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Správa tajných klíčů aplikací Service Fabric sítě
Sítě pro Service Fabric podporuje tajné kódy jako prostředky Azure. Tajný klíč služby prostředků infrastruktury sítě může být libovolné citlivé textové informace, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které mají být uloženy a bezpečně přenášet. Tento článek ukazuje, jak pomocí Service Fabric Secure Store Service můžete nasadit a spravovat tajné kódy.

Aplikace sítě tajný kód se skládá ze:
* A **tajných kódů** prostředků, což je kontejner, který uchovává tajné kódy text. Tajné kódy obsažené **tajných kódů** prostředků jsou uloženy a bezpečně přenášet.
* Jeden nebo více **tajné klíče/hodnoty** prostředky, které jsou uloženy v **tajných kódů** kontejner prostředků. Každý **tajné klíče/hodnoty** prostředků se liší podle čísla verze. Nelze změnit verzi **tajné klíče/hodnoty** prostředků, jenom přidat novou verzi.

Správa tajných klíčů se skládá z následujících kroků:
1. Deklarovat sítě **tajných kódů** prostředků v Azure Resource modelu YAML nebo JSON souboru pomocí inlinedValue druh a SecretsStoreRef contentType definice.
2. Deklarovat síť **tajné klíče/hodnoty** prostředky v Azure Resource modelu YAML nebo JSON souboru, který se uloží do **tajných kódů** prostředků (z kroku 1).
3. Upravte aplikaci sítě tak, aby odkazovaly hodnoty tajných kódů sítě.
4. Nasazení nebo postupného upgradu aplikací síť pro používání hodnoty tajných kódů.
5. Použití Azure "az" CLI příkazy pro správu životního cyklu Secure Store Service.

## <a name="declare-a-mesh-secrets-resource"></a>Deklarujte prostředek mřížky tajných kódů
Prostředek mřížky tajné kódy je deklarován v souboru YAML nebo JSON modelu prostředků Azure pomocí inlinedValue druh a definice SecretsStoreRef contentType. Prostředek mřížky tajných kódů podporuje tajných kódů služby Secure Store zdrojem. 
>
Následuje příklad toho, jak deklarovat prostředky sítě tajných kódů v souboru JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
  ]
}
```
Následuje příklad toho, jak deklarovat prostředků sítě tajných kódů v souboru YAML:
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

## <a name="declare-mesh-secretsvalues-resources"></a>Deklarovat mřížky tajné klíče a hodnoty prostředků
Tajné klíče a hodnoty prostředků sítě jsou závislá na prostředky sítě tajné kódy definované v předchozím kroku.

Pokud jde o vztah mezi část "resources" "hodnota:" a "název:" pole: Druhá část "název:" řetězců oddělených středníkem je číslo verze před dvojtečkou musí odpovídat tajná hodnota sítě, pro kterou má použít pro tajný klíč a název závislost. Například pro element ```name: mysecret:1.0```, je číslo verze 1.0 a název ```mysecret``` musí odpovídat dříve definované ```"value": "mysecret"```.

>
Následuje příklad toho, jak deklarovat prostředky sítě tajné klíče/hodnoty v souboru JSON:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "eastus",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "my-secret-value-v1": {
      "type": "string",
      "metadata": {
        "description": "My Mesh Application Secret Value."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MySecret.txt",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]",
      "properties": {
        "kind": "inlinedValue",
        "description": "My Mesh Application Secret",
        "contentType": "SecretsStoreRef",
        "value": "mysecret",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "mysecret:1.0",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        'Microsoft.ServiceFabricMesh/secrets/MySecret.txt'
      ],
      "properties": {
        "value": "[parameters('my-secret-value-v1)]"
      }
    }
  ]
}
```
Následuje příklad toho, jak deklarovat prostředků sítě tajné klíče/hodnoty v souboru YAML:
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

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Upravit aplikaci sítě tak, aby odkazovaly hodnoty mřížky tajný klíč
Aplikace Service Fabric sítě je potřeba mít na paměti následující dva řetězce. aby bylo možné využívat tajný klíč služby Secure Store hodnoty:
1. Micrsoft.ServiceFabricMesh/Secrets.name obsahuje název souboru a bude obsahovat hodnoty tajných kódů jako prostý text.
2. Windows nebo Linuxem proměnnou prostředí "Fabric_SettingPath" obsahuje cestu k adresáři pro soubory, které obsahují hodnoty tajných kódů služby Secure Store kde budou přístupné. Toto je "C:\Settings" pro hostované Windows "/ var/nastavení a" pro aplikace hostované v systému Linux sítě v uvedeném pořadí.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Nasazení nebo využívají hodnoty tajných kódů pomocí upgradu se zajištěním provozu pro aplikace sítě
Vytváření tajné kódy a/nebo verze tajné klíče/hodnoty je omezená na modelu prostředků, které jsou deklarovány nasazení. Jediný způsob, jak vytvořit tyto prostředky je předáním prostředků modelu JSON nebo YAML soubor pomocí **nasazení sítě az** takto:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Příkazy Azure CLI pro správu životního cyklu Secure Store Service

### <a name="create-a-new-secrets-resource"></a>Vytvořit nový prostředek tajných kódů
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Předat buď **soubor šablony** nebo **šablona identifikátoru uri** (ale ne obojí).

Příklad:
- nasazení sítě az vytvořit--c:\MyMeshTemplates\SecretTemplate1.txt
- Vytvoření nasazení sítě az-- https://www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Zobrazit tajný kód.
Vrací popis tajný kód (ale nikoli hodnotu).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Odstranění tajného kódu

- Tajný kód. nelze odstranit, pokud je odkazuje aplikace sítě.
- Odstraňuje se prostředek tajných kódů odstraní všechny tajné kódy prostředků nebo verze.
```azurecli-interactive
az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="list-secrets-in-subscription"></a>Výpis tajných kódů v rámci předplatného
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Výpis tajných klíčů ve skupině prostředků
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Zobrazí seznam všech verzí tajného kódu
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Zobrazit hodnotu verzi tajného klíče
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Odstranit hodnotu verzi tajného klíče
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Další postup 
Další informace o Service Fabric sítě, naleznete v přehledu:
- [Přehled Service Fabric mřížky](service-fabric-mesh-overview.md)
