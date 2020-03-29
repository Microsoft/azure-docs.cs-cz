---
title: Azure Service Fabric – konfigurace přihlašovacích údajů úložiště kontejnerů
description: Konfigurace pověření úložiště pro stahování bitových kopií z registru kontejnerů
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934980"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Konfigurace přihlašovacích údajů úložiště pro vaši aplikaci ke stažení iobrazů kontejnerů

Nakonfigurujte `RepositoryCredentials` ověřování `ContainerHostPolicies` registru kontejnerů přidáním do části manifestu aplikace. Přidejte účet a heslo pro registr kontejnerů *(myregistry.azurecr.io* v příkladu níže), který umožňuje službě stáhnout image kontejneru z úložiště.

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

Doporučujeme zašifrovat heslo úložiště pomocí certifikátu šifrování, který je nasazen do všech uzlů clusteru. Když Service Fabric nasadí balíček služby do clusteru, certifikát šifrování se použije k dešifrování šifrovaného textu. Rutina Invoke-ServiceFabricEncryptText se používá k vytvoření šifrovaného textu pro heslo, který se přidá do souboru ApplicationManifest.xml.
Další informace o certifikátech a sémantiku šifrování najdete v tématu [Správa tajných](service-fabric-application-secret-management.md) klíčů.

## <a name="configure-cluster-wide-credentials"></a>Konfigurace pověření pro celý cluster

Service Fabric umožňuje konfigurovat pověření pro celý cluster, která lze použít jako výchozí pověření úložiště aplikacemi.

Tuto funkci lze povolit nebo `UseDefaultRepositoryCredentials` zakázat přidáním atributu do `ContainerHostPolicies` souboru ApplicationManifest.xml s hodnotou a `true` nebo. `false`

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

Service Fabric pak používá výchozí pověření úložiště, které lze zadat `Hosting` v ClusterManifest v části.  Pokud `UseDefaultRepositoryCredentials` `true`je , Service Fabric čte následující hodnoty z ClusterManifest:

* DefaultContainerRepositoryAccountName (řetězec)
* DefaultContainerRepositoryPassword (řetězec)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (řetězec)

Zde je příklad toho, co `Hosting` lze přidat do oddílu v souboru ClusterManifestTemplate.json. Oddíl `Hosting` lze přidat při vytvoření clusteru nebo později při upgradu konfigurace. Další informace najdete [v tématu Změna nastavení clusteru Azure Service Fabric](service-fabric-cluster-fabric-settings.md) a Správa [tajných kódů aplikací Azure Service Fabric](service-fabric-application-secret-management.md)

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

## <a name="use-tokens-as-registry-credentials"></a>Použití tokenů jako pověření registru

Service Fabric podporuje použití tokenů jako přihlašovacích údajů ke stažení obrázků pro vaše kontejnery.  Tato funkce využívá *spravovanou identitu* základní škálovací sady virtuálních počítačů k ověření do registru, což eliminuje potřebu správy uživatelských pověření.  Další informace najdete [v tématu Spravované identity pro prostředky Azure.](../active-directory/managed-identities-azure-resources/overview.md)  Použití této funkce vyžaduje následující kroky:

1. Ujistěte se, že je pro virtuální hod *povolena spravovaná identita přiřazená k systému.*

    ![Portál Azure: Možnost vytvoření škálovací sady virtuálních strojů](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Udělte oprávnění škálovací sadě virtuálních strojů pro vyžádat nebo číst obrázky z registru. Z okna Řízení přístupu (IAM) vašeho registru kontejnerů Azure na webu Azure Portal přidejte *přiřazení role* pro váš virtuální počítač:

    ![Přidání jistiny virtuálního virtuálního objektu do ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Dále upravte manifest aplikace. V `ContainerHostPolicies` části přidejte `‘UseTokenAuthenticationCredentials=”true”`atribut .

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
    > Příznak `UseDefaultRepositoryCredentials` nastavený na `UseTokenAuthenticationCredentials` hodnotu true, zatímco je true způsobí chybu během nasazení.

## <a name="next-steps"></a>Další kroky

* Další informace o [ověřování registru kontejneru](../container-registry/container-registry-authentication.md).
