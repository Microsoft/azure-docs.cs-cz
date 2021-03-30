---
title: Úložiště centrálních tajných kódů Azure Service Fabric
description: Tento článek popisuje, jak používat úložiště centrálních tajných kódů v Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: e9fd435803ad5354b0eb2d4f5de50009a8cbbfe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88869751"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Úložiště centrálních tajných kódů v Azure Service Fabric 
Tento článek popisuje, jak v Azure Service Fabric použít centrální úložiště tajných kódů (CSS) k vytváření tajných kódů v Service Fabricch aplikacích. CSS je místní mezipaměť úložiště tajných klíčů, která uchovává citlivá data, například heslo, tokeny a klíče, zašifrované v paměti.

  > [!NOTE] 
  > Při první aktivaci šablon stylů CSS před SF verze 7,1. CU3, aktivace může selhat a opustit šablonu stylů CSS v trvale nesprávném stavu, pokud je v clusteru ověřovaném systémem Windows aktivována šablona stylů CSS; Šablona stylů CSS je aktivována v jakémkoli clusteru `EncryptionCertificateThumbprint` , ale je nesprávně deklarována nebo není nainstalován odpovídající certifikát/seznam ACL-Ed na uzlech. V případě clusteru ověřování systému Windows přejděte do 7,1. Než budete pokračovat, CU3. U ostatních clusterů Prosím překontrolujte tyto nevarianty nebo je přidejte do 7,1. CU3.
  
## <a name="enable-central-secrets-store"></a>Povolit úložiště centrálních tajných kódů
Přidejte následující skript do konfigurace clusteru v části `fabricSettings` pro povolení šablon stylů CSS. Pro šablony stylů CSS doporučujeme použít jiný certifikát než certifikát clusteru. Ujistěte se, že je šifrovací certifikát nainstalovaný na všech uzlech a `NetworkService` má oprávnění ke čtení pro privátní klíč certifikátu.
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
                    "value":  "1"
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
Tajný prostředek můžete vytvořit pomocí REST API.
  > [!NOTE] 
  > Pokud cluster používá ověřování systému Windows, je žádost REST odeslána přes nezabezpečený kanál HTTP. Doporučujeme použít cluster se systémem x509 se zabezpečenými koncovými body.

Pokud chcete vytvořit `supersecret` prostředek tajného kódu pomocí REST API, udělejte požadavek PUT na `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` . K vytvoření tajného prostředku potřebujete certifikát clusteru nebo klientský certifikát správce.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>Nastavte tajnou hodnotu.

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

1. Přidejte do souboru **settings.xml** oddíl s následujícím fragmentem kódu. Všimněte si, že hodnota je ve formátu { `secretname:version` }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importujte část v **ApplicationManifest.xml**.
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

   Proměnná prostředí `SecretPath` bude ukazovat na adresář, ve kterém jsou uloženy všechny tajné kódy. Každý parametr uvedený v `testsecrets` oddílu je uložen v samostatném souboru. Aplikace teď může tajný klíč použít následujícím způsobem:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Připojte tajné klíče ke kontejneru. Jediná změna nutná k zpřístupnění tajných kódů uvnitř kontejneru je `specify` bod připojení v `<ConfigPackage>` .
Následující fragment kódu je upravený **ApplicationManifest.xml**.  

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

1. Můžete vytvořit vazby tajného kódu k proměnné prostředí procesu zadáním `Type='SecretsStoreRef` . Následující fragment kódu je příkladem vytvoření vazby `supersecret` verze `ver1` k proměnné prostředí `MySuperSecret` v **ServiceManifest.xml**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [zabezpečení aplikací a služeb](service-fabric-application-and-service-security.md).
