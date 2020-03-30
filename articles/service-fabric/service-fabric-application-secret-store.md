---
title: Úložiště centrálních tajných klíčů azure service fabric
description: Tento článek popisuje, jak používat centrální úložiště tajných klíčů ve službě Azure Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 11fb94a9fba40e6f2474ad64f5eb0c454be28ca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589160"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Centrální úložiště tajných klíčů ve službě Azure Fabric 
Tento článek popisuje, jak používat centrální úložiště tajných klíčů (CSS) v Azure Service Fabric k vytvoření tajných kódů v aplikacích Service Fabric. CSS je místní mezipaměť tajného úložiště, která uchovává citlivá data, jako je heslo, tokeny a klíče, šifrovaná v paměti.

## <a name="enable-central-secrets-store"></a>Povolit centrální úložiště tajných klíčů
Chcete-li povolit css, přidejte do konfigurace `fabricSettings` clusteru následující skript. Doporučujeme použít jiný certifikát než certifikát clusteru pro CSS. Ujistěte se, že šifrovací `NetworkService` certifikát je nainstalován na všech uzlech a že má oprávnění ke čtení soukromého klíče certifikátu.
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
## <a name="declare-a-secret-resource"></a>Deklarovat tajný zdroj
Tajný prostředek můžete vytvořit pomocí šablony Azure Resource Manager nebo rozhraní REST API.

### <a name="use-resource-manager"></a>Použít Správce prostředků

Pomocí následující šablony můžete použít Správce prostředků k vytvoření tajného prostředku. Šablona vytvoří `supersecret` tajný zdroj, ale zatím není pro tajný prostředek nastavena žádná hodnota.


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

Chcete-li `supersecret` vytvořit tajný prostředek pomocí rozhraní REST `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`API, vytvořte požadavek PUT na program . K vytvoření tajného prostředku potřebujete certifikát clusteru nebo klientský certifikát správce.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Nastavení tajné hodnoty

### <a name="use-the-resource-manager-template"></a>Použití šablony Správce prostředků

K vytvoření a nastavení tajné hodnoty použijte následující šablonu Správce prostředků. Tato šablona nastaví `supersecret` tajnou hodnotu tajného prostředku jako verzi `ver1`.
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

Pomocí následujícího skriptu můžete použít rozhraní REST API k nastavení tajné hodnoty.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Zkontrolujte tajnou hodnotu
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Použití tajného klíče v aplikaci

Chcete-li použít tajný klíč v aplikaci Service Fabric, postupujte takto.

1. Přidejte oddíl do souboru **settings.xml** s následujícím fragmentem. Zde si všimněte, že`secretname:version`hodnota je ve formátu { }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importujte oddíl v **applicationManifest.xml**.
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

   Proměnná `SecretPath` prostředí bude přeukazovat na adresář, kde jsou uloženy všechny tajné klíče. Každý parametr uvedený `testsecrets` v sekci je uložen v samostatném souboru. Aplikace nyní může použít tajný klíč takto:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Připojte tajemství do kontejneru. Jedinou změnou, která je nutná k `specify` zpřístupnění `<ConfigPackage>`tajných kódů uvnitř kontejneru, je přípojný bod v .
Následující úryvek je upravený **applicationManifest.xml**.  

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
   Tajné klíče jsou k dispozici pod přípojnou bodem uvnitř kontejneru.

1. Tajný klíč můžete svázat s proměnnou `Type='SecretsStoreRef`prostředí procesu zadáním . Následující úryvek je příkladem svázání `supersecret` verze `ver1` s proměnnou prostředí v `MySuperSecret` **souboru ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Další kroky
Další informace o [zabezpečení aplikací a služeb](service-fabric-application-and-service-security.md).
