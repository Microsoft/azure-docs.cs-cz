---
title: Správa tajných klíčů aplikací Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak zabezpečit hodnoty tajných kódů v aplikaci Service Fabric (nezávislý na platformě).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: a0003ee02c09ad8c99d6fa94935f96527c146e7d
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063807"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Spravovat šifrované tajné kódy aplikace Service Fabric
Tento průvodce vás provede kroky pro správu tajných kódů v aplikaci Service Fabric. Tajné klíče může být žádné citlivé údaje, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovat ve formátu prostého textu.

Použití šifrované tajné klíče v aplikaci Service Fabric zahrnuje tři kroky:
* Nastavit šifrovací certifikát a šifrování tajných kódů.
* Zadejte šifrované tajné klíče v aplikaci.
* Dešifrujte šifrované tajné klíče z kódu služby.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Nastavit šifrovací certifikát a šifrování tajných kódů
Nastavení šifrovací certifikát a jeho použití k šifrování tajných kódů se pohybuje mezi Windows a Linux.
* [Nastavit šifrovací certifikát a šifrování tajných kódů v clusterech Windows.][secret-management-windows-specific-link]
* [Nastavit šifrovací certifikát a šifrování tajných kódů na clusterech s Linuxem.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Zadejte šifrované tajné klíče v aplikaci
Předchozí krok popisuje, jak k šifrování tajného kódu s certifikátem a vytvořit kódovaný řetězec base64 pro použití v aplikaci. Tento řetězec s kódováním base-64 lze zadat jako šifrované [parametr] [ parameters-link] Settings.xml služby, nebo jako šifrované [proměnnou prostředí] [ environment-variables-link] v souboru ServiceManifest.xml služby.

Zadejte šifrované [parametr] [ parameters-link] v konfiguračním souboru Settings.xml vaše služba se `IsEncrypted` atribut nastaven na `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Zadejte šifrované [proměnnou prostředí] [ environment-variables-link] v souboru ServiceManifest.xml vaše služba se `Type` atribut nastaven na `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>Vložit do instance aplikace tajných klíčů aplikací
V ideálním případě by měl být nasazení do různých prostředí jako automatizované co nejvíc. To lze provést pomocí provádí šifrování tajných kódů v prostředí sestavení a poskytuje šifrované tajné kódy jako parametry při vytváření instance aplikace.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Použití přepsatelnými parametry v Settings.xml
Konfigurace souboru Settings.xml umožňuje přepsatelnými parametry, které mohou být poskytnuty v okamžiku vytváření aplikace. Použití `MustOverride` atribut namísto zadávání hodnoty parametru:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Chcete-li přepsat hodnoty v Settings.xml, deklarujte parametr přepsání pro služby v souboru ApplicationManifest.xml:

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

Nyní lze upravit hodnotu jako *parametr aplikace* při vytváření instance aplikace. Vytvoření instance aplikace možnost využívat skripty prostředí PowerShell nebo napsaných v C#, pro snadnou integraci v procesu sestavení.

Pomocí Powershellu, tento parametr je zadaný pro `New-ServiceFabricApplication` příkaz jako [zatřiďovací tabulku](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Pomocí C#, parametry aplikace jsou určené v `ApplicationDescription` jako `NameValueCollection`:

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Dešifrovat šifrované tajné klíče z kódu služby
Rozhraní API pro přístup k [parametry] [ parameters-link] a [proměnné prostředí] [ environment-variables-link] umožňují snadno dešifrování šifrovaných hodnot. Protože zašifrovaný řetězec obsahuje informace o certifikát použitý k šifrování, není nutné ručně zadat certifikát. Právě musí být nainstalovaný na uzlu, na kterém služba běží na certifikátu.

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

## <a name="next-steps"></a>Další postup
Další informace o [aplikace a služby zabezpečení](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
