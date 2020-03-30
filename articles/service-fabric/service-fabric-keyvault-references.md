---
title: Azure Service Fabric – použití odkazů na aplikaci KeyVault aplikace Service Fabric
description: Tento článek vysvětluje, jak používat podporu keyvaultreference služby pro tajné kódy aplikací.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545479"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Podpora KeyVaultReference pro aplikace Service Fabric (náhled)

Běžnou výzvou při vytváření cloudových aplikací je, jak bezpečně ukládat tajné klíče vyžadované vaší aplikací. Například můžete chtít uložit pověření úložiště kontejneru v trezoru keyvault a odkazovat na něj v manifestu aplikace. Service Fabric KeyVaultReference používá service fabric spravované identity a usnadňuje odkazování keyvault tajných kódů. Zbývající část tohoto článku podrobně popisuje použití service fabric KeyVaultReference a obsahuje některé typické použití.

## <a name="prerequisites"></a>Požadavky

- Spravovaná identita pro aplikaci (MIT)
    
    Podpora Service Fabric KeyVaultReference používá spravovanou identitu aplikace, a proto by plánování aplikací k použití keyvaultreferences mělo používat spravovanou identitu. Podle tohoto [dokumentu](concepts-managed-identity.md) povolte spravovanou identitu pro vaši aplikaci.

- Centrální úložiště tajemství (CSS).

    Centrální úložiště tajných klíčů (CSS) je zašifrovaná místní tajná paměť service fabric. CSS je místní mezipaměť tajného úložiště, která uchovává citlivá data, jako je heslo, tokeny a klíče, šifrovaná v paměti. KeyVaultReference, po načtení, jsou uloženy do mezipaměti v CSS.

    Chcete-li povolit všechny `fabricSettings` požadované funkce pro podporu KeyVaultReference, přidejte níže uvedené níže do konfigurace clusteru.

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
    > Doporučujeme použít samostatný šifrovací certifikát pro CSS. Můžete jej přidat do sekce "CentralSecretService".
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Aby se změny projevily, budete také muset změnit zásady upgradu a určit vynucené restartování runtime Service Fabric v každém uzlu, zatímco upgrade probíhá v clusteru. Toto restartování zajišťuje, že nově povolená systémová služba je spuštěna a spuštěna na každém uzlu. V fragmentu níže forceRestart je základní nastavení; stávající hodnoty pro zbývající část nastavení.
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
- Udělit oprávnění ke spravované identitě aplikace do trezoru klíčů

    Odkazem na tento [dokument](how-to-grant-access-other-resources.md) zobrazíte, jak udělit přístup spravované identity keyvault. Všimněte si také, pokud používáte spravované identity systému, spravovaná identita se vytvoří až po nasazení aplikace.

## <a name="keyvault-secret-as-application-parameter"></a>Keyvault tajný klíč jako parametr aplikace
Řekněme, že aplikace potřebuje číst heslo back-enddatabáze uložené v keyvault, Service Fabric KeyVaultReference podpora usnadňuje. Níže uvedený `DBPassword` příklad čte tajný klíč z trezoru keyvault pomocí podpory Service Fabric KeyVaultReference.

- Přidání oddílu do souboru settings.xml

    Definovat `DBPassword` parametr `KeyVaultReference` s typem a hodnotou`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Odkaz na nový oddíl v ApplicationManifest.xml v aplikaci`<ConfigPackagePolicies>`

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

    Instance service fabric na službě vyřeší parametr KeyVaultReference pomocí spravované identity aplikace. Každý parametr `<Section  Name=dbsecrets>` uvedený v části bude soubor pod složkou, na kterou se vztahuje aplikace EnvironmentVariable SecretPath. Pod c# kód fragment ukazují, jak číst DBPassword ve vaší aplikaci.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Pro scénář kontejneru můžete použít MountPoint k `secrets` řízení, kde bude připojen.

## <a name="keyvault-secret-as-environment-variable"></a>Keyvault tajný klíč jako proměnná prostředí

Proměnné prostředí Service Fabric nyní podporují typ KeyVaultReference, níže příklad ukazuje, jak svázat proměnnou prostředí s tajným klíčem uloženým v úložišti KeyVault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Keyvault tajný klíč jako heslo úložiště kontejnerů
KeyVaultReference je podporovaný typ pro úložiště úložiště kontejnerů, níže příklad ukazuje, jak použít keyvault odkaz jako heslo úložiště kontejnerů.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>Nejčastější dotazy
- Spravovanou identitu musí být povolena pro podporu KeyVaultReference, aktivace aplikace se nezdaří, pokud je keyvaultreference použita bez povolení spravované identity.

- Pokud používáte systém přiřazenou identitu, je vytvořen pouze po nasazení aplikace a tím se vytvoří cyklická závislost. Po nasazení aplikace můžete udělit systému přiřazená oprávnění k přístupu k identitě keyvault. Systém přiřazenou identitu můžete najít podle názvu {cluster}/{název aplikace}/{servicename}

- Trezor keyvault musí být ve stejném předplatném jako cluster prostředků infrastruktury služeb. 

## <a name="next-steps"></a>Další kroky

* [Dokumentace azure keyvault](https://docs.microsoft.com/azure/key-vault/)
