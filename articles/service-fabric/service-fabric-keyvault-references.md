---
title: Azure Service Fabric – použití Service Fabricch odkazů na Trezor klíčů klíčů aplikací
description: Tento článek vysvětluje, jak používat podporu KeyVaultReference Service-Fabric pro tajné klíče pro aplikace.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898592"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Podpora KeyVaultReference pro aplikace Service Fabric nasazené v Azure

Běžným problémem při sestavování cloudových aplikací je postup bezpečné distribuce tajných kódů do vašich aplikací. Například můžete chtít nasadit klíč databáze do aplikace bez vystavení klíče během kanálu nebo operátoru. Service Fabric podpora KeyVaultReference usnadňuje nasazení tajných kódů do aplikací jednoduše odkazem na adresu URL tajného kódu, který je uložený v Key Vault. Service Fabric pořídí načtení tohoto tajného klíče jménem spravované identity vaší aplikace a aktivace aplikace s tajným klíčem.

> [!NOTE]
> Podpora KeyVaultReference pro aplikace Service Fabric je GA (Předběžná verze Preview) od Service Fabric verze 7,2 CU5. Před použitím této funkce doporučujeme provést upgrade na tuto verzi.

> [!NOTE]
> Podpora KeyVaultReference pro aplikace Service Fabric podporuje pouze tajné kódy s [verzemi](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) . Tajné kódy bez verzí nejsou podporovány. Key Vault musí být ve stejném předplatném jako cluster Service Fabric. 

## <a name="prerequisites"></a>Předpoklady

- Spravovaná identita pro aplikace Service Fabric

    Podpora Service Fabric KeyVaultReference používá ke čtení tajných kódů jménem aplikace spravovanou identitu aplikace, takže vaše aplikace musí být nasazená přes a přiřazená spravovaná identita. Podle tohoto [dokumentu](concepts-managed-identity.md) povolte spravovanou identitu pro vaši aplikaci.

- Úložiště centrálních tajných kódů (CSS).

    Úložiště centrálních tajných kódů (CSS) je Service Fabric šifrované mezipaměti místních tajných klíčů. Tato funkce používá šablony stylů CSS k ochraně a zachování tajných kódů po jejich načtení z Key Vault. Povolení této volitelné systémové služby je také vyžadováno pro využívání této funkce. Podle tohoto [dokumentu](service-fabric-application-secret-store.md) povolte a nakonfigurujte šablony stylů CSS.

- Udělení oprávnění přístupu spravované identitě aplikace do trezoru klíčů

    Odkaz na tento [dokument](how-to-grant-access-other-resources.md) vám umožní zjistit, jak udělit spravované identitě přístup k trezoru klíčů. Všimněte si také, že pokud používáte spravovanou identitu přiřazenou systémem, je spravovaná identita vytvořena až po nasazení aplikace. To může vytvořit časování časování, ve kterém se aplikace pokusí o přístup ke tajnému kódu, než může být identita udělena přístup k trezoru. Název identity přiřazené systémem bude `{cluster name}/{application name}/{service name}` .
    
## <a name="use-keyvaultreferences-in-your-application"></a>Použití KeyVaultReferences ve vaší aplikaci
KeyVaultReferences lze spotřebovat různými způsoby
- [Jako proměnná prostředí](#as-an-environment-variable)
- [Připojeno jako soubor do kontejneru](#mounted-as-a-file-into-your-container)
- [Jako odkaz na heslo úložiště kontejneru](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>Jako proměnná prostředí

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Připojeno jako soubor do kontejneru

- Přidání oddílu do settings.xml

    Definovat `MySecret` parametr s typem `KeyVaultReference` a hodnotou `<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- Odkázat na nový oddíl v ApplicationManifest.xml `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Využívání tajných kódů z kódu služby

    Každý parametr uvedený v části `<Section  Name=MySecrets>` bude soubor ve složce, na kterou odkazuje objekt EnvironmentVariable SecretPath. Níže uvedený fragment kódu jazyka C# ukazuje, jak číst MySecret z aplikace.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > Přípojný bod řídí složku, do které budou připojeny soubory obsahující tajné hodnoty.

### <a name="as-a-reference-to-a-container-repository-password"></a>Jako odkaz na heslo úložiště kontejneru

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Další kroky

* [Dokumentace k trezoru klíčů Azure](../key-vault/index.yml)
* [Další informace o úložišti centrálních tajných klíčů](service-fabric-application-secret-store.md)
* [Informace o spravované identitě pro aplikace Service Fabric](concepts-managed-identity.md)
