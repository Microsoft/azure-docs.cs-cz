---
title: Service Fabric úložiště tajných klíčů | Microsoft Docs
description: Tento článek popisuje, jak používat Service Fabricho úložiště tajných klíčů.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 5315a8806f45e40204e8500e97c3440bfa9ab8b2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077346"
---
#  <a name="service-fabric-secrets-store"></a>Service Fabric úložiště tajných kódů
Tento článek popisuje, jak vytvářet a používat tajné klíče v aplikacích Service Fabric s využitím Service Fabricho úložiště tajných klíčů (CSS). CSS je místní mezipaměť úložiště v tajných klíčích, která slouží k uchovávání citlivých dat, jako je například heslo, tokeny a klíče šifrované v paměti.

## <a name="enabling-secrets-store"></a>Povoluje se úložiště tajných klíčů.
 Pokud chcete povolit CSS, přidejte níže do konfigurace clusteru v části `fabricSettings`. Doporučuje se použít certifikát jiný než certifikát clusteru pro šablony stylů CSS. Ujistěte se, že je šifrovací certifikát nainstalovaný na všech uzlech a `NetworkService` má oprávnění ke čtení pro privátní klíč certifikátu.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService"
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
```
## <a name="declare-secret-resource"></a>Deklarovat prostředek tajného kódu
Tajný prostředek můžete vytvořit buď pomocí šablony Správce prostředků, nebo pomocí REST API.

* Použití šablony Resource Manageru
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
Výše uvedená šablona vytvoří `supersecret` prostředek tajného kódu, ale zatím není nastavená žádná hodnota pro tajný prostředek.

* S využitím REST API

Pokud chcete vytvořit prostředek tajného kódu, `supersecret` vytvořit žádost o vložení do `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. K vytvoření tajného klíče potřebujete certifikát clusteru nebo klientský certifikát správce.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Nastavit tajnou hodnotu
* Použití šablony Resource Manageru

Níže uvedená šablona Správce prostředků vytvoří a nastaví hodnotu pro tajný `supersecret` s verzí `ver1`.
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
* S využitím REST API

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>Použití tajného klíče v aplikaci

1.  Do souboru Settings. xml přidejte část s následujícím obsahem. Všimněte si, že hodnota je ve formátu {`secretname:version`}.

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. Teď importujte oddíl v souboru souboru ApplicationManifest. XML.
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

Proměnná prostředí SecretPath odkazuje na adresář, ve kterém jsou uložené všechny tajné kódy. Každý parametr uvedený v části `testsecrets` se uloží do samostatného souboru. Aplikace teď může používat tajný klíč, jak je znázorněno níže.
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Připojení tajných kódů ke kontejneru

Pouze změna nutná k zpřístupnění tajných kódů uvnitř kontejneru je zadání přípojný bod v `<ConfigPackage>`.
Tady je upravený souboru ApplicationManifest. XML.  

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
Tajné kódy budou k dispozici pod přípojným bodem ve vašem kontejneru.

4. Vazba tajného kódu na proměnnou prostředí 

Můžete vytvořit vazby tajného kódu k proměnné prostředí procesu zadáním Type = ' SecretsStoreRef '. Tady je příklad, jak vytvořit `supersecret` `ver1` verze na proměnnou prostředí `MySuperSecret` v souboru ServiceManifest. XML.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Další kroky
Další informace o [zabezpečení aplikací a služeb](service-fabric-application-and-service-security.md)