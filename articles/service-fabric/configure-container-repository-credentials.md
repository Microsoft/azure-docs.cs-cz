---
title: Azure Service Fabric – konfigurace přihlašovacích údajů úložiště kontejneru | Microsoft Docs
description: Konfigurace přihlašovacích údajů úložiště pro stahování imagí z registru kontejnerů
services: service-fabric
documentationcenter: .net
author: arya
manager: gkhanna
ms.assetid: b93d31e5-9e4c-4405-b266-c0efa4643d97
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 8/1/2019
ms.author: arya
ms.openlocfilehash: cfe212a150da0e5828f48de3bf2692ab2a44c672
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657161"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Konfigurace přihlašovacích údajů úložiště pro vaši aplikaci ke stažení imagí kontejneru

Nakonfigurujte ověřování registru kontejneru přidáním `RepositoryCredentials` do `ContainerHostPolicies` v souboru ApplicationManifest.xml. Přidejte účet a heslo pro registr kontejneru myregistry.azurecr.io, který službě umožňuje stáhnout image kontejneru z úložiště.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Doporučuje se šifrovat heslo úložiště pomocí certifikátu zašifrování, který je nasazený na všech uzlech clusteru. Když Service Fabric nasadí balíček služby do clusteru, certifikát šifrování se použije k dešifrování šifrovaného textu. Rutina Invoke-ServiceFabricEncryptText se používá k vytvoření šifrovaného textu pro heslo, který se přidá do souboru ApplicationManifest.xml.
Další informace o certifikátech a sémantikě šifrování najdete v tématu [Správa tajných klíčů](service-fabric-application-secret-management.md) .

## <a name="configure-cluster-wide-credentials"></a>Konfigurace přihlašovacích údajů na úrovni clusteru

Service Fabric vám umožní nakonfigurovat přihlašovací údaje pro všechny clustery, které se dají použít jako výchozí přihlašovací údaje úložiště aplikacemi.

Tuto funkci lze povolit `UseDefaultRepositoryCredentials` nebo zakázat přidáním atributu do `ContainerHostPolicies` souboru `true` souboru ApplicationManifest. XML s hodnotou nebo `false` .

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric pak použije výchozí přihlašovací údaje úložiště, které se dají zadat v manifestem clusteru pod `Hosting` oddílem.  Pokud `UseDefaultRepositoryCredentials` je`true`, Service Fabric přečte následující hodnoty z manifestem clusteru:

* DefaultContainerRepositoryAccountName (řetězec)
* DefaultContainerRepositoryPassword (řetězec)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (String)---podporováno počínaje modulem runtime 6,4.

Tady je příklad toho, co je možné přidat do `Hosting` části v souboru ClusterManifestTemplate. JSON. `Hosting` Oddíl lze přidat při vytváření clusteru nebo později v upgradu konfigurace. Další informace najdete v tématu [Změna nastavení clusteru azure Service Fabric](service-fabric-cluster-fabric-settings.md) a [Správa tajných klíčů aplikací Azure Service Fabric](service-fabric-application-secret-management.md) .

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>Využití spravované identity sady škálování virtuálních počítačů pomocí služby Managed identity Service (MSI)

Service Fabric podporuje použití tokenů jako přihlašovacích údajů ke stažení imagí pro vaše kontejnery.  Tato funkce využívá spravovanou identitu základní sady pro škálování virtuálního počítače k ověření v registru a eliminuje nutnost spravovat přihlašovací údaje uživatele.  Další informace najdete v tématu [Identita spravované služby](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) .  Použití této funkce vyžaduje následující kroky:

1.  Ujistěte se, že pro virtuální počítač je povolená spravovaná identita přiřazená systémem (viz snímek obrazovky níže).

    ![Vytvořit identitu sady škálování virtuálních počítačů](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  Potom udělte oprávnění k virtuálnímu počítači (SS) pro načtení a čtení imagí z registru.  V okně Azure můžete přejít na Access Control (IAM) ACR a dát vašemu VIRTUÁLNÍmu počítači správná oprávnění, jak vidíte níže:

    ![Přidat objekt zabezpečení virtuálního počítače do ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  Po dokončení výše uvedeného postupu upravte soubor souboru ApplicationManifest. XML.  Vyhledejte značku s označením "ContainerHostPolicies" a přidejte atribut `‘UseTokenAuthenticationCredentials=”true”`.

    ```json
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > Příznak `UseDefaultRepositoryCredentials` nastavený na hodnotu true, `UseTokenAuthenticationCredentials` Pokud je true, způsobí chybu během nasazování.

## <a name="next-steps"></a>Další postup

* Přečtěte si další informace o [ověřování registrů kontejnerů](/azure/container-registry/container-registry-authentication).
