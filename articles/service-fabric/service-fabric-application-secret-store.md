---
title: Úložiště centrálních tajných kódů Azure Service Fabric
description: Tento článek popisuje, jak používat úložiště centrálních tajných kódů v Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589160"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Úložiště centrálních tajných kódů v Azure Service Fabric 
Tento článek popisuje, jak v Azure Service Fabric použít centrální úložiště tajných kódů (CSS) k vytváření tajných kódů v Service Fabricch aplikacích. CSS je místní mezipaměť úložiště tajných klíčů, která uchovává citlivá data, například heslo, tokeny a klíče, zašifrované v paměti.

## <a name="enable-central-secrets-store"></a>Povolit úložiště centrálních tajných kódů
Přidejte následující skript do konfigurace clusteru v části `fabricSettings` pro povolení šablon stylů CSS. Pro šablony stylů CSS doporučujeme použít jiný certifikát než certifikát clusteru. Ujistěte se, že je šifrovací certifikát nainstalovaný na všech uzlech a že `NetworkService` má oprávnění ke čtení pro privátní klíč certifikátu.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Deklarace tajného prostředku
Tajný prostředek můžete vytvořit buď pomocí šablony Azure Resource Manager, nebo REST API.

### <a name="use-resource-manager"></a>Použít Správce prostředků

Pomocí následující šablony použijte Správce prostředků k vytvoření tajného prostředku. Šablona vytvoří prostředek `supersecret` tajný klíč, ale zatím není nastavená žádná hodnota pro tajný prostředek.


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>Použití rozhraní REST API

Chcete-li vytvořit prostředek `supersecret` tajný klíč pomocí REST API, vytvořte do `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`požadavek PUT. K vytvoření tajného prostředku potřebujete certifikát clusteru nebo klientský certifikát správce.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Nastavte tajnou hodnotu.

### <a name="use-the-resource-manager-template"></a>Použití šablony Správce prostředků

Pomocí následující šablony Správce prostředků vytvořte a nastavte tajnou hodnotu. Tato šablona nastavuje tajnou hodnotu pro prostředek `supersecret` tajného klíče jako `ver1`verze.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>Použití rozhraní REST API

Pomocí následujícího REST API skriptu nastavte tajnou hodnotu.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Prověřte tajnou hodnotu.
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Použití tajného klíče v aplikaci

Při použití tajného klíče ve vaší aplikaci Service Fabric postupujte podle těchto kroků.

1. Přidejte do souboru **Settings. XML** část s následujícím fragmentem kódu. Všimněte si, že hodnota je ve formátu {`secretname:version`}.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importujte oddíl v **souboru souboru ApplicationManifest. XML**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   Proměnná prostředí `SecretPath` bude odkazovat na adresář, ve kterém jsou uloženy všechny tajné kódy. Každý parametr uvedený v části `testsecrets` je uložen v samostatném souboru. Aplikace teď může tajný klíč použít následujícím způsobem:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Připojte tajné klíče ke kontejneru. Jediná změna nutná k zpřístupnění tajných kódů uvnitř kontejneru je `specify` přípojný bod v `<ConfigPackage>`.
Následující fragment kódu je upravený **souboru ApplicationManifest. XML**.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Tajné kódy jsou k dispozici pod přípojným bodem v kontejneru.

1. Můžete vytvořit vazby tajného kódu k proměnné prostředí procesu zadáním `Type='SecretsStoreRef`. Následující fragment kódu je příklad, jak vytvořit navázání `supersecret` verze `ver1` na proměnnou prostředí `MySuperSecret` v **souboru ServiceManifest. XML**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [zabezpečení aplikací a služeb](service-fabric-application-and-service-security.md).
