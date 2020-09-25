---
title: Azure Service Fabric – použití Service Fabricch odkazů na Trezor klíčů klíčů aplikací
description: Tento článek vysvětluje, jak používat podporu KeyVaultReference Service-Fabric pro tajné klíče pro aplikace.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: c4de6ae17ae728e1dbadbd6d6e2d94c0e1471112
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261137"
---
# <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Podpora KeyVaultReference pro aplikace Service Fabric (Preview)

Běžným problémem při sestavování cloudových aplikací je bezpečné ukládání tajných kódů vyžadovaných vaší aplikací. Můžete například chtít uložit přihlašovací údaje úložiště kontejnerů do trezoru klíčů a odkazovat na něj v manifestu aplikace. Service Fabric KeyVaultReference používá Service Fabric spravovanou identitu a usnadňuje referenční informace trezoru klíčů. Zbývající část tohoto článku podrobně popisuje, jak používat Service Fabric KeyVaultReference a obsahuje některé typické využití.

> [!IMPORTANT]
> Použití této funkce Preview se v produkčních prostředích nedoporučuje.

> [!NOTE]
> Funkce náhledu odkazu na Trezor klíčů podporuje jenom tajné klíče se [správou verzí](https://docs.microsoft.com/azure/key-vault/general/about-keys-secrets-certificates#objects-identifiers-and-versioning) . Tajné kódy bez verzí nejsou podporovány.

## <a name="prerequisites"></a>Požadavky

- Spravovaná identita pro aplikaci (MIT)
    
    Podpora Service Fabric KeyVaultReference používá spravovanou identitu aplikace, takže aplikace, které plánuje používat KeyVaultReferences, by měly používat spravovanou identitu. Podle tohoto [dokumentu](concepts-managed-identity.md) povolte spravovanou identitu pro vaši aplikaci.

- Úložiště centrálních tajných kódů (CSS).

    Úložiště centrálních tajných kódů (CSS) je Service Fabric šifrované mezipaměti místních tajných klíčů. CSS je místní mezipaměť úložiště tajných klíčů, která uchovává citlivá data, například heslo, tokeny a klíče, zašifrované v paměti. KeyVaultReference, po načtení, jsou ukládány do mezipaměti v šablonách stylů CSS.

    Níže přidejte do konfigurace clusteru v části `fabricSettings` , abyste povolili všechny požadované funkce pro podporu KeyVaultReference.

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
                }
                ]
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > Pro šablony stylů CSS doporučujeme použít samostatný šifrovací certifikát. Můžete ho přidat pod oddíl "CentralSecretService".
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Aby se změny projevily, budete také muset změnit zásadu upgradu, aby určovala vynucené restartování Service Fabric modulu runtime na každém uzlu, protože upgrade probíhají prostřednictvím clusteru. Tento restart zajistí, že se nově povolená systémová služba spustí a spustí na každém uzlu. V následujícím fragmentu kódu je forceRestart základním nastavením; pro zbývající část nastavení použijte existující hodnoty.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Udělení oprávnění přístupu spravované identitě aplikace do trezoru klíčů

    Odkaz na tento [dokument](how-to-grant-access-other-resources.md) vám umožní zjistit, jak udělit spravované identitě přístup k trezoru klíčů. Všimněte si také, že pokud používáte spravovanou identitu přiřazenou systémem, je spravovaná identita vytvořena až po nasazení aplikace.

## <a name="keyvault-secret-as-application-parameter"></a>Tajný klíč trezoru klíčů jako parametr aplikace
Řekněme, že aplikace potřebuje číst heslo back-end databáze uloženou v trezoru klíčů, Service Fabric podpora KeyVaultReference to usnadňuje. Níže uvedený příklad přečte `DBPassword` tajný kód z trezoru klíčů pomocí podpory Service Fabric KeyVaultReference.

- Přidání oddílu do settings.xml

    Definovat `DBPassword` parametr s typem `KeyVaultReference` a hodnotou `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Odkázat na nový oddíl v ApplicationManifest.xml `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Použití KeyVaultReference ve vaší aplikaci

    Service Fabric při vytváření instance služby vyřeší parametr KeyVaultReference pomocí spravované identity aplikace. Každý parametr uvedený v části `<Section  Name=dbsecrets>` bude soubor ve složce, na kterou odkazuje objekt EnvironmentVariable SecretPath. Pod fragmentem kódu v jazyce C# ukazují, jak číst DBPassword ve vaší aplikaci.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Pro scénář kontejneru můžete použít přípojný bod k určení, kam bude `secrets` připojen.

## <a name="keyvault-secret-as-environment-variable"></a>Tajný klíč trezoru klíčů jako proměnná prostředí

Service Fabric proměnných prostředí teď podporuje typ KeyVaultReference, nižší příklad ukazuje, jak vytvořit instanci proměnné prostředí s tajným kódem uloženým v trezoru klíčů.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Tajný klíč trezoru klíčů jako heslo úložiště kontejneru
KeyVaultReference je podporovaný typ pro RepositoryCredentials kontejneru, níže ukazuje, jak použít odkaz trezoru klíčů jako heslo úložiště kontejnerů.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Časté otázky
- Pro podporu KeyVaultReference je potřeba povolit spravovanou identitu, aktivace vaší aplikace selže, pokud se KeyVaultReference použije bez povolení spravované identity.

- Pokud používáte identitu přiřazenou systémem, je vytvořena až po nasazení aplikace a tím se vytvoří cyklická závislost. Jakmile je vaše aplikace nasazená, můžete k trezoru klíčů udělit oprávnění k přístupu k identitě přidělené systémem. Identitu přiřazenou systémem můžete najít podle názvu {cluster}/{Application Name}/{ServiceName}

- Trezor klíčů musí být ve stejném předplatném jako cluster Service Fabric. 

## <a name="next-steps"></a>Další kroky

* [Dokumentace k trezoru klíčů Azure](../key-vault/index.yml)
