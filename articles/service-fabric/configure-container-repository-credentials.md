---
title: Azure Service Fabric – konfigurace přihlašovacích údajů úložiště kontejnerů
description: Konfigurace přihlašovacích údajů úložiště pro stahování imagí z registru kontejnerů
author: arya
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: arya
ms.openlocfilehash: 25fe3c69b19d397137d1e1802e941e0433a1b160
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351660"
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

Tuto funkci lze povolit nebo zakázat přidáním atributu `UseDefaultRepositoryCredentials` pro `ContainerHostPolicies` v souboru souboru ApplicationManifest. XML s hodnotou `true` nebo `false`.

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

Service Fabric pak použije výchozí přihlašovací údaje úložiště, které se dají zadat v manifestem clusteru pod oddílem `Hosting`.  Pokud je `UseDefaultRepositoryCredentials` `true`, Service Fabric přečte následující hodnoty z manifestem clusteru:

* DefaultContainerRepositoryAccountName (řetězec)
* DefaultContainerRepositoryPassword (řetězec)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (String)---podporováno počínaje modulem runtime 6,4.

Tady je příklad toho, co je možné přidat do oddílu `Hosting` v souboru ClusterManifestTemplate. JSON. Oddíl `Hosting` lze přidat při vytváření clusteru nebo později v upgradu konfigurace. Další informace najdete v tématu [Změna nastavení clusteru azure Service Fabric](service-fabric-cluster-fabric-settings.md) a [Správa tajných klíčů aplikací Azure Service Fabric](service-fabric-application-secret-management.md) .

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

3.  Po dokončení výše uvedeného postupu upravte soubor souboru ApplicationManifest. XML.  Vyhledejte značku s názvem "ContainerHostPolicies" a přidejte `‘UseTokenAuthenticationCredentials=”true”`atributu.

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
    > Příznak `UseDefaultRepositoryCredentials` nastaven na hodnotu true, zatímco `UseTokenAuthenticationCredentials` hodnota true způsobí chybu během nasazování.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [ověřování registrů kontejnerů](/azure/container-registry/container-registry-authentication).
