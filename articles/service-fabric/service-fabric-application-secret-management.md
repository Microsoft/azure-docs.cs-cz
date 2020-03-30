---
title: Správa tajných klíčů aplikací Azure Service Fabric
description: Zjistěte, jak zabezpečit tajné hodnoty v aplikaci Service Fabric (platforma nezávislá).
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 4a489993f982993d5703a9b46d42fffaa6134038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259055"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Správa šifrovaných tajných klíčů v aplikacích Service Fabric
Tato příručka vás provede kroky správy tajných kódů v aplikaci Service Fabric. Tajné klíče mohou být jakékoli citlivé informace, jako jsou řetězce připojení úložiště, hesla nebo jiné hodnoty, které by neměly být zpracovány ve formátu prostého textu.

Použití šifrovaných tajných klíčů v aplikaci Service Fabric zahrnuje tři kroky:
* Nastavte šifrovací certifikát a šifrujte tajné klíče.
* Zašifrované tajné klíče v aplikaci.
* Dešifrujte šifrované tajné klíče z kódu služby.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Nastavení šifrovacího certifikátu a šifrování tajných kódů
Nastavení šifrovacího certifikátu a jeho použití k šifrování tajných kódů se v jednotlivých systémech Windows a Linux liší.
* [Nastavte šifrovací certifikát a šifrujte tajné klíče v clusterech Windows.][secret-management-windows-specific-link]
* [Nastavte šifrovací certifikát a šifrujte tajné kódy v clusterech Linux.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Určení šifrovaných tajných klíčů v aplikaci
Předchozí krok popisuje, jak šifrovat tajný klíč pomocí certifikátu a vytvořit řetězec kódovaný základní 64 pro použití v aplikaci. Tento kódovaný řetězec base-64 lze zadat jako šifrovaný [parametr][parameters-link] v souboru Settings.xml služby nebo jako šifrovanou [proměnnou prostředí][environment-variables-link] v souboru ServiceManifest.xml služby.

Zašifrovaný [parametr][parameters-link] zadejte v konfiguračním souboru Settings.xml služby s atributem nastaveným `IsEncrypted` na `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Zadejte šifrovanou [proměnnou prostředí][environment-variables-link] v souboru `Type` ServiceManifest.xml služby s atributem nastaveným na `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Hlavní klíče by měly být také zahrnuty do aplikace Service Fabric zadáním certifikátu v manifestu aplikace. Přidejte prvek **SecretsCertificate** do **applicationManifest.xml** a zahrňte kryptografický otisk požadovaného certifikátu.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Vstříknutí tajných kódů aplikace do instancí aplikace
V ideálním případě by nasazení do různých prostředí mělo být co nejautomatizovanější. Toho lze dosáhnout provedením tajného šifrování v prostředí sestavení a poskytnutím šifrovaných tajných kódů jako parametrů při vytváření instancí aplikace.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Použití overridable parametrů v Souboru Settings.xml
Konfigurační soubor Settings.xml umožňuje překážné parametry, které mohou být poskytnuty v době vytvoření aplikace. Použijte `MustOverride` atribut namísto poskytnutí hodnoty parametru:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Chcete-li přepsat hodnoty v souboru Settings.xml, deklarujte parametr přepsání služby v souboru ApplicationManifest.xml:

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

Nyní lze hodnotu zadat jako *parametr aplikace* při vytváření instance aplikace. Vytvoření instance aplikace lze skriptovat pomocí prostředí PowerShell nebo zapsáno v c#, pro snadnou integraci do procesu sestavení.

Pomocí prostředí PowerShell je parametr `New-ServiceFabricApplication` dodáván příkazu jako [tabulka hash](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Pomocí jazyka C# jsou parametry `ApplicationDescription` aplikace `NameValueCollection`zadány v poli :

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Dešifrování šifrovaných tajných kódů z kódu služby
Rozhraní API pro přístup k [parametrům][parameters-link] a [proměnným prostředí][environment-variables-link] umožňují snadné dešifrování šifrovaných hodnot. Vzhledem k tomu, že šifrovaný řetězec obsahuje informace o certifikátu použitém pro šifrování, není nutné certifikát zadávat ručně. Certifikát je třeba nainstalovat pouze na uzlu, na který je služba spuštěna.

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
* Úložiště [tajných kódů](service-fabric-application-secret-store.md) service fabric 
* Další informace o [zabezpečení aplikací a služeb](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md