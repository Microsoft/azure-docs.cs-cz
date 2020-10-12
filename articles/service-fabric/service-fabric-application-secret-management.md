---
title: Správa tajných kódů aplikace Service Fabric Azure
description: Naučte se zabezpečit tajné hodnoty v Service Fabric aplikaci (Platform-nezávislá).
ms.topic: conceptual
ms.date: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a11869c3b606ed9e74ce4f598109139fa1bb4164
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89012819"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Správa šifrovaných tajných kódů v aplikacích Service Fabric
Tento průvodce vás provede jednotlivými kroky správy tajných kódů v aplikaci Service Fabric. Tajné kódy můžou obsahovat citlivé informace, jako jsou například připojovací řetězce úložiště, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu.

Použití šifrovaných tajných klíčů v Service Fabric aplikaci zahrnuje tři kroky:
* Nastavte šifrovací certifikát a Šifrujte tajné klíče.
* Zadejte šifrované tajné klíče v aplikaci.
* Dešifrování šifrovaných tajných klíčů z kódu služby.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Nastavení šifrovacího certifikátu a šifrování tajných klíčů
Nastavení šifrovacího certifikátu a jeho použití k šifrování tajných kódů se liší mezi systémy Windows a Linux.
* [Nastavte šifrovací certifikát a Šifrujte tajné klíče v clusterech Windows.][secret-management-windows-specific-link]
* [Nastavte šifrovací certifikát a Šifrujte tajné klíče v clusterech se systémem Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Určení šifrovaných tajných klíčů v aplikaci
Předchozí krok popisuje, jak šifrovat tajný klíč pomocí certifikátu a vytvořit řetězec s kódováním Base-64 pro použití v aplikaci. Tento řetězec s kódováním Base-64 lze zadat jako zašifrovaný [parametr][parameters-link] v Settings.xml služby nebo jako zašifrovanou [proměnnou prostředí][environment-variables-link] v ServiceManifest.xml služby.

V konfiguračním souboru Settings.xml služby zadejte zašifrovaný [parametr][parameters-link] s `IsEncrypted` atributem nastaveným na `true` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
V souboru ServiceManifest.xml vaší služby zadejte šifrovanou [proměnnou prostředí][environment-variables-link] s `Type` atributem nastaveným na `Encrypted` :
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Tajné kódy by měly být zahrnuté i do vaší aplikace Service Fabric zadáním certifikátu v manifestu aplikace. Přidejte element **SecretsCertificate** do **ApplicationManifest.xml** a vložte kryptografický otisk požadovaného certifikátu.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> Po aktivaci aplikace, která určuje SecretsCertificate, se v Service Fabric najde certifikát pro porovnání a udělí identitě, na které aplikace běží, úplná oprávnění k privátnímu klíči certifikátu. Service Fabric také monitorovat změny certifikátu a patřičně znovu použít oprávnění. Pokud chcete zjistit změny certifikátů deklarovaných běžným názvem, Service Fabric spustí pravidelný úkol, který najde všechny vyhovující certifikáty, a porovná ho se seznamem kryptografických otisků v mezipaměti. Když se zjistí nový kryptografický otisk, znamená to, že se certifikát od tohoto subjektu obnovil. Úloha se spustí jednou za minutu na každém uzlu clusteru.
>
> Zatímco SecretsCertificate umožňuje deklarace na základě subjektu, Upozorňujeme, že zašifrovaná nastavení jsou vázaná na pár klíčů, který se použil k zašifrování nastavení na klientovi. Musíte zajistit, aby původní šifrovací certifikát (nebo ekvivalentní) odpovídal deklaraci založené na předmětu a že je nainstalovaný, včetně odpovídajícího privátního klíče, na všech uzlech clusteru, které by mohly hostovat aplikaci. Všechny časově platné certifikáty, které odpovídají deklaraci založené na předmětu a sestavené ze stejného páru klíčů jako původní šifrovací certifikát, se považují za ekvivalentní.
>

### <a name="inject-application-secrets-into-application-instances"></a>Vložení tajných kódů aplikace do instancí aplikace
V ideálním případě by mělo být nasazení do různých prostředí co nejlépe automatizovaně. To lze provést provedením tajného šifrování v prostředí sestavení a zadáním šifrovaných tajných klíčů jako parametrů při vytváření instancí aplikace.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Použijte v Settings.xml přepsatelné parametry
Konfigurační soubor Settings.xml umožňuje přepsatelné parametry, které lze zadat v době vytváření aplikace. `MustOverride`Místo zadání hodnoty parametru použijte atribut:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Chcete-li přepsat hodnoty v Settings.xml, deklarujte parametr přepsání pro službu v ApplicationManifest.xml:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Nyní je možné při vytváření instance aplikace zadat hodnotu jako *parametr aplikace* . Vytvoření instance aplikace lze skriptovat pomocí prostředí PowerShell nebo napsaného v jazyce C# pro jednoduchou integraci v procesu sestavení.

Pomocí PowerShellu je parametr dodán `New-ServiceFabricApplication` příkazu jako [zatřiďovací tabulka](/previous-versions/windows/it-pro/windows-powershell-1.0/ee692803(v=technet.10)):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Pomocí jazyka C# jsou parametry aplikace zadány v `ApplicationDescription` podobě jako `NameValueCollection` :

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Dešifrování šifrovaných tajných klíčů z kódu služby
Rozhraní API pro přístup k [parametrům][parameters-link] a [proměnným prostředí][environment-variables-link] umožňují snadné dešifrování šifrovaných hodnot. Vzhledem k tomu, že zašifrovaný řetězec obsahuje informace o certifikátu, který se používá k šifrování, nemusíte ručně zadávat certifikát. Certifikát stačí nainstalovat jenom na uzel, na kterém je služba spuštěná.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Další kroky
* Service Fabric [úložiště tajných](service-fabric-application-secret-store.md) kódů 
* Další informace o [zabezpečení aplikací a služeb](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md
