---
title: Azure Service Fabric – konfigurace přihlašovacích údajů úložiště kontejnerů
description: Konfigurace přihlašovacích údajů úložiště pro stahování imagí z registru kontejnerů
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0c6421fed88a3909db717c13a6b3faf51c4491cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574815"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Konfigurace přihlašovacích údajů úložiště pro vaši aplikaci ke stažení imagí kontejneru

Nakonfigurujte ověřování pomocí registru kontejnerů přidáním `RepositoryCredentials` do `ContainerHostPolicies` části manifestu aplikace. Přidejte účet a heslo pro registr kontejneru (*myregistry.azurecr.IO* v následujícím příkladu), což službě umožňuje stáhnout image kontejneru z úložiště.

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

Tuto funkci lze povolit nebo zakázat přidáním `UseDefaultRepositoryCredentials` atributu do `ContainerHostPolicies` v ApplicationManifest.xml s `true` `false` hodnotou nebo.

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

Service Fabric pak použije výchozí přihlašovací údaje úložiště, které se dají zadat v manifestem clusteru pod `Hosting` oddílem.  Pokud `UseDefaultRepositoryCredentials` je `true` , Service Fabric přečte následující hodnoty z manifestem clusteru:

* DefaultContainerRepositoryAccountName (řetězec)
* DefaultContainerRepositoryPassword (řetězec)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (řetězec)

Tady je příklad toho, co je možné přidat do `Hosting` části ClusterManifestTemplate.jsv souboru. `Hosting`Oddíl lze přidat při vytváření clusteru nebo později v upgradu konfigurace. Další informace najdete v tématu [Změna nastavení clusteru azure Service Fabric](service-fabric-cluster-fabric-settings.md) a [Správa tajných klíčů aplikací Azure Service Fabric](service-fabric-application-secret-management.md) .

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

## <a name="use-tokens-as-registry-credentials"></a>Použití tokenů jako přihlašovacích údajů registru

Service Fabric podporuje použití tokenů jako přihlašovacích údajů ke stažení imagí pro vaše kontejnery.  Tato funkce využívá *spravovanou identitu* základní sady pro škálování virtuálního počítače k ověření v registru a eliminuje nutnost spravovat přihlašovací údaje uživatele.  Další informace najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) .  Použití této funkce vyžaduje následující kroky:

1. Ujistěte se, že je pro virtuální počítač povolená *spravovaná identita přiřazená systémem* .

    ![Azure Portal: možnost vytvořit identitu sady škálování virtuálního počítače](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

> [!NOTE]
> V případě uživatelsky přiřazené spravované identity tento krok přeskočte. Zbývající kroky budou fungovat stejně, pokud je sada škálování přidružená jenom k jedné spravované identitě přiřazené uživatelem.

2. Udělte oprávnění k sadě škálování virtuálního počítače pro načtení a čtení imagí z registru. V okně Access Control (IAM) Azure Container Registry v Azure Portal přidejte *přiřazení role* pro virtuální počítač:

    ![Přidat objekt zabezpečení virtuálního počítače do ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Dále upravte manifest aplikace. V `ContainerHostPolicies` části přidejte atribut `‘UseTokenAuthenticationCredentials=”true”` .

    ```xml
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
    > Příznak `UseDefaultRepositoryCredentials` nastavený na hodnotu true, pokud `UseTokenAuthenticationCredentials` je true, způsobí chybu během nasazování.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [ověřování registrů kontejnerů](../container-registry/container-registry-authentication.md).
