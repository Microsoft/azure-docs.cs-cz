---
title: Spravovat tajné klíče aplikace Azure Service Fabric | Microsoft Docs
description: Zjistěte, jak zabezpečit tajný hodnoty v aplikaci Service Fabric.
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
ms.date: 03/21/2018
ms.author: vturecek
ms.openlocfilehash: fa79d50d6ef2899dcaf4116dcfe8ac7fae077959
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="manage-secrets-in-service-fabric-applications"></a>Spravovat tajných klíčů v aplikace Service Fabric
Tento průvodce vás provede kroky správy tajných klíčů v aplikace Service Fabric. Tajné klíče může být žádné citlivé informace, jako je například úložiště připojovací řetězce, hesla nebo jiné hodnoty, které by neměly být zpracovány v prostém textu.

[Azure Key Vault] [ key-vault-get-started] zde slouží jako umístění úložiště bezpečné pro certifikáty a jako způsob, jak získat certifikáty, které jsou nainstalované na clusterů Service Fabric v Azure. Pokud nejsou nasazení do Azure, není nutné používat ke správě tajných klíčů v Service Fabric aplikace Key Vault. Ale *pomocí* tajných klíčů v aplikaci je Cloudová platforma vznikl k aplikacím umožňují nasadit do clusteru s podporou hostovat kdekoli. 

## <a name="obtain-a-data-encipherment-certificate"></a>Získejte certifikát pro šifrování dat
Certifikát šifrování dat se používají výhradně pro šifrování a dešifrování konfigurace hodnoty v souborech Settings.xml služby a není používá pro ověřování nebo podpisový šifrovací textu. Certifikát musí splňovat následující požadavky:

* Certifikát musí obsahovat privátní klíč.
* Certifikát se musí vytvořit pro výměnu klíčů, exportovat do souboru Personal Information Exchange (.pfx).
* Použití klíče certifikátu musí obsahovat šifrování dat (10) a nesmí obsahovat Server ověřování nebo ověřování klientů. 
  
  Při vytváření certifikát podepsaný svým držitelem pomocí prostředí PowerShell, například `KeyUsage` musí být nastaven příznak `DataEncipherment`:
  
  ```powershell
  New-SelfSignedCertificate -Type DocumentEncryptionCert -KeyUsage DataEncipherment -Subject mydataenciphermentcert -Provider 'Microsoft Enhanced Cryptographic Provider v1.0'
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalace certifikátu v clusteru
Tento certifikát musí být nainstalován na každém uzlu v clusteru. Použije se v době běhu k dešifrování hodnot uložených v souborech Settings.xml služby. V tématu [postup vytvoření clusteru s podporou pomocí Azure Resource Manager] [ service-fabric-cluster-creation-via-arm] pokyny pro instalaci. 

## <a name="encrypt-application-secrets"></a>Šifrování tajné klíče aplikace
Pokud nasazujete aplikaci, šifrování tajný hodnoty s certifikátem a jejich vložení do služby souborech Settings.xml konfigurační soubor. Sada Service Fabric SDK obsahuje vestavěné tajný šifrování a dešifrování funkce. Tajný hodnoty může být v vytvořené čas zašifrované dešifrovat a čtení prostřednictvím kódu programu v kódu služby. 

Následující příkaz prostředí PowerShell se používá k šifrování tajného klíče. Tento příkaz šifruje pouze hodnotu parametru. provede **není** přihlásit šifrovaný text. Je nutné použít stejný certifikát šifrování, který je nainstalován v clusteru k vytvoření ciphertext tajný hodnoty:

```powershell
Invoke-ServiceFabricEncryptText -CertStore -CertThumbprint "<thumbprint>" -Text "mysecret" -StoreLocation CurrentUser -StoreName My
```

Výsledný řetězec s kódováním base-64 obsahuje jak na tajný šifrovaný text a také informace o certifikátu, který byl použit k jejich zašifrování.  Řetězec s kódováním base-64 lze vložit do parametru v konfiguračním souboru na souborech Settings.xml vaší služby pomocí `IsEncrypted` atribut nastaven na `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```

### <a name="inject-application-secrets-into-application-instances"></a>Vložit tajné klíče aplikace do instance aplikace
V ideálním případě by měl být nasazení do různých prostředí jako automatizované nejblíže. To můžete udělat tak, že provádění tajný šifrování v prostředí pro sestavování a poskytování šifrované tajné klíče jako parametry, při vytváření instancí aplikace.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Použití přepsatelnými parametry v souborech Settings.xml
Konfigurační soubor souborech Settings.xml umožňuje přepsatelnými parametry, které lze zadat v okamžiku vytvoření aplikace. Použití `MustOverride` atribut místo hodnotu pro parametr:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Pokud chcete přepsat hodnoty v souborech Settings.xml, deklarujte parametrem přepsání pro službu v ApplicationManifest.xml:

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

Teď hodnotu lze zadat jako *aplikace parametr* při vytváření instance aplikace. Vytvoření instance aplikace mohou být skripty pomocí prostředí PowerShell, nebo napsané v jazyce C# pro snadnou integraci v procesu sestavení.

Pomocí prostředí PowerShell, parametr je dodána na `New-ServiceFabricApplication` příkaz jako [zatřiďovací tabulku](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
PS C:\Users\vturecek> New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Pomocí jazyka C#, parametry aplikace jsou určené v `ApplicationDescription` jako `NameValueCollection`:

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

## <a name="decrypt-secrets-from-service-code"></a>Dešifrování tajné klíče z kódu služby
Šifrované hodnoty mimo souborech Settings.xml si můžete přečíst dešifrováním je šifrování certifikát použitý k šifrování tajného klíče. Služby v Service Fabric běží pod účtem NETWORK SERVICE ve výchozím nastavení v systému Windows a nemají přístup k certifikáty, které jsou nainstalovány na uzlu bez zvláštní nastavení.

Když používá certifikát, šifrování dat, je třeba Ujistěte se, zda síťové služby nebo ať uživatelský účet služby je spuštěno má přístup k privátní klíč certifikátu. Udělení přístupu pro vaši službu automaticky, pokud je třeba nakonfigurovat tak, Service Fabric bude zpracovávat. Tuto konfiguraci lze provést v ApplicationManifest.xml definováním uživatelů a zásady zabezpečení pro certifikáty. V následujícím příkladu je účet NETWORK SERVICE poskytnut přístup pro čtení k definované jeho kryptografický otisk certifikátu:

```xml
<ApplicationManifest … >
    <Principals>
        <Users>
            <User Name="Service1" AccountType="NetworkService" />
        </Users>
    </Principals>
  <Policies>
    <SecurityAccessPolicies>
      <SecurityAccessPolicy GrantRights=”Read” PrincipalRef="Service1" ResourceRef="MyCert" ResourceType="Certificate"/>
    </SecurityAccessPolicies>
  </Policies>
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

> [!NOTE]
> Při kopírování kryptografický otisk certifikátu z modulu certifikát úložiště snap-in v systému Windows, neviditelná znak je umístěn na začátku řetězce kryptografický otisk. Tento znak neviditelná může způsobit chybu při pokusu o vyhledat certifikát pomocí kryptografického otisku, takže je nutné odstranit tento další znak.
> 
> 

### <a name="use-application-secrets-in-service-code"></a>Použití aplikace tajných klíčů v kódu služby
Rozhraní API pro přístup k hodnoty konfigurace z souborech Settings.xml v balíčku konfigurace umožňuje snadno dešifrování hodnot, které mají `IsEncrypted` atribut nastaven na `true`. Vzhledem k tomu, že šifrované text obsahuje informace o certifikát použitý k šifrování, není potřeba ručně najít certifikát. Právě musí být nainstalovaný na uzlu, který je služba spuštěná na certifikátu. Jednoduše volání `DecryptValue()` metoda pro načtení původní tajná hodnota:

```csharp
ConfigurationPackage configPackage = this.Context.CodePackageActivationContext.GetConfigurationPackageObject("Config");
SecureString mySecretValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue()
```

## <a name="next-steps"></a>Další kroky
Další informace o [aplikace a služby zabezpečení](service-fabric-application-and-service-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-secret-management/overview.png
