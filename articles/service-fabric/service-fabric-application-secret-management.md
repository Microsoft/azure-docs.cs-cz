---
title: Správa tajných klíčů pro Azure Service Fabric aplikace | Microsoft Docs
description: Naučte se zabezpečit tajné hodnoty v Service Fabric aplikaci (Platform-nezávislá).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: a24f670314d2f6679e37b438a74421e0e84604e2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075491"
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
Předchozí krok popisuje, jak šifrovat tajný klíč pomocí certifikátu a vytvořit řetězec s kódováním Base-64 pro použití v aplikaci. Tento řetězec zakódovaný v základní-64 se dá zadat jako zašifrovaný [parametr][parameters-link] v nastavení služby. XML nebo jako zašifrovaná [Proměnná prostředí][environment-variables-link] v souboru ServiceManifest. XML služby.

V konfiguračním souboru Settings. XML vaší služby zadejte zašifrovaný [parametr][parameters-link] s atributem `IsEncrypted` nastaveným na `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
V souboru ServiceManifest. XML vaší služby zadejte šifrovanou [proměnnou prostředí][environment-variables-link] s atributem `Type` nastaveným na `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

Tajné kódy by měly být zahrnuté i do vaší aplikace Service Fabric zadáním certifikátu v manifestu aplikace. Přidejte do **souboru souboru ApplicationManifest. XML** element **SecretsCertificate** a zahrňte do něj kryptografický otisk požadovaného certifikátu.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Vložení tajných kódů aplikace do instancí aplikace
V ideálním případě by mělo být nasazení do různých prostředí co nejlépe automatizovaně. To lze provést provedením tajného šifrování v prostředí sestavení a zadáním šifrovaných tajných klíčů jako parametrů při vytváření instancí aplikace.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Použití přepisovatelných parametrů v Settings. XML
Konfigurační soubor Settings. XML umožňuje přepsatelné parametry, které lze zadat v době vytváření aplikace. Místo zadání hodnoty parametru použijte atribut `MustOverride`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Chcete-li přepsat hodnoty v souboru Settings. XML, deklarujte parametr přepsání pro službu v souboru ApplicationManifest. XML:

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

Nyní je možné při vytváření instance aplikace zadat hodnotu jako *parametr aplikace* . Vytvoření instance aplikace může být skriptem pomocí PowerShellu nebo napsaného C#v systému pro jednoduchou integraci v procesu sestavení.

Pomocí PowerShellu se parametr doplní do příkazu `New-ServiceFabricApplication` jako [zatřiďovací tabulka](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Použití C#, parametry aplikace jsou zadány v `ApplicationDescription` jako `NameValueCollection`:

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