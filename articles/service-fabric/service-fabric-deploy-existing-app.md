---
title: Nasazení existujícího spustitelného souboru do Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak zabalit existující aplikaci jako Host spustitelného souboru, takže je možné nasadit do clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: mfussell
ms.openlocfilehash: a80895db8a89b8d9392d0ed067b95daa23474d8b
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113865"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Balení a nasazení existujícího spustitelného souboru do Service Fabric
Při vytváření balíčku existujícího spustitelného souboru jako [spustitelný soubor typu Host](service-fabric-guest-executables-introduction.md), můžete buď použít šablonu projektu sady Visual Studio nebo [ručně vytvořit balíček aplikace](#manually). Pomocí sady Visual Studio, struktury balíček aplikace a soubory manifestu jsou vytvářeny nová šablona projektu pro vás.

> [!TIP]
> Nejjednodušší způsob, jak balíček existující spustitelný soubor do služby Windows je použití sady Visual Studio a v Linuxu pomocí Yeomana
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Zabalení a nasazení existujícího spustitelného souboru pomocí sady Visual Studio
Visual Studio poskytuje šablony služby Service Fabric vám pomůžou nasadit spustitelný soubor typu Host do clusteru Service Fabric.

1. Zvolte **souboru** > **nový projekt**a vytvářet aplikace Service Fabric.
2. Zvolte **spustitelný soubor typu Host** jako šablonu služby.
3. Klikněte na tlačítko **Procházet** vyplňte zbývající parametry pro vytvoření služby a vyberte složku s spustitelný soubor.
   * *Chování balíčku kódu*. Je možné nastavit ke zkopírování veškerý obsah složky do projektu sady Visual Studio, což je užitečné, pokud spustitelný soubor nezmění. Pokud očekáváte, že spustitelný soubor, měnit a chcete mít možnost, aby se získaly nové buildy dynamicky, můžete místo toho odkaz na složku. Propojené složky můžete používat při vytváření projektu aplikace v sadě Visual Studio. Toto odkazuje na umístění zdroje z v rámci projektu, umožňuje vám aktualizovat spustitelný soubor typu Host do konečného zdroje. Tyto aktualizace se stanou součástí balíčku aplikace na sestavení.
   * *Program* určuje spustitelného souboru, který má být spuštěna, spusťte službu.
   * *Argumenty* Určuje argumenty, které by měly být předány spustitelnému souboru. Může být seznam parametrů s argumenty.
   * *WorkingFolder* určuje pracovní adresář pro proces, který se má spustit. Můžete zadat tři hodnoty:
     * `CodeBase` Určuje, že v pracovním adresáři bude nastavena na adresář kódu v balíčku aplikace (`Code` adresář se v předchozím struktura souborů).
     * `CodePackage` Určuje, že v pracovním adresáři bude nastavit do kořenového adresáře balíčku aplikace (`GuestService1Pkg` je znázorněno v předchozí strukturu souborů).
     * `Work` Určuje, že jsou soubory umístěny v podadresáři volá práce.
4. Zadejte název služby a klikněte na **OK**.
5. Pokud vaše služba potřebuje koncový bod pro komunikaci, můžete teď přidat protokol, port a typ do souboru ServiceManifest.xml. Například: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Teď můžete pomocí balíčku a publikování akce na místní cluster laděním řešení v sadě Visual Studio. Až budete připraveni, můžete publikovat aplikaci do vzdáleného clusteru nebo řešení do správy zdrojového kódu se změnami.
7. Čtení [zkontrolujte spuštěné aplikaci](#check-your-running-application) se dozvíte, jak chcete-li zobrazit vaše služby spustitelné hostem spuštěné v Service Fabric Explorer.

Návod příkladu najdete v tématu [vytvoření vaší první aplikace spustitelná hostem pomocí sady Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Pomocí generátoru Yeoman balíčků a nasazení existujícího spustitelného souboru v Linuxu

Postup pro vytvoření a nasazení v Linuxu spustitelného souboru hosta je stejný jako při nasazování aplikace csharp nebo java.

1. V terminálu zadejte `yo azuresfguest`.
2. Pojmenujte svoji aplikaci.
3. Pojmenujte službu a uveďte podrobnosti, včetně cesta ke spustitelnému souboru a parametry, které se musí volat s.

Yeoman vytvoří balíček aplikace se příslušná aplikace a soubory manifestu spolu s instalaci a odinstalaci skripty.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Ručně zabalení a nasazení existujícího spustitelného souboru
Proces ručně balení spustitelný soubor typu Host jsou založené na těmito obecnými kroky:

1. Vytvoření balíčku adresářovou strukturu.
2. Přidáte kód a konfigurační soubory aplikace.
3. Upravte soubor manifestu služby.
4. Upravte soubor manifestu aplikace.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Vytvoření balíčku adresářovou strukturu
Můžete začít tak, že vytvoříte adresářovou strukturu, jak je popsáno v předchozí části "Balíček souboru struktury aplikace."

### <a name="add-the-applications-code-and-configuration-files"></a>Přidat kód a konfigurační soubory aplikace
Po vytvoření adresářové struktury můžete přidat kód aplikace a konfigurační soubory v části kódu a konfiguračním adresáře. Můžete také vytvořit další adresáře souborů nebo podadresáře v adresáři kódu nebo konfigurace.

Service Fabric se `xcopy` obsahu kořenový adresář aplikace, takže není k dispozici žádné předdefinované strukturu použít jiné než vytváření dva hlavní adresáře, kódu a nastavení. (Můžete si vybrat různé názvy potřebujete. Další podrobnosti najdete v další části.)

> [!NOTE]
> Ujistěte se, že složku zahrnujete všechny soubory a závislosti, které aplikace potřebuje. Service Fabric překopíruje obsah balíčku aplikace na všech uzlech v clusteru, kam aplikace služeb pro nasazení. Balíček měl obsahovat veškerý kód, který aplikace potřebuje ke spuštění. Nepředpokládejte, že závislosti jsou již nainstalovány.
>
>

### <a name="edit-the-service-manifest-file"></a>Upravit soubor manifestu služby
Dalším krokem je upravte soubor manifestu služby a uveďte následující informace:

* Název typu služby. To je ID, které se Service Fabric používá k identifikaci služby.
* Příkaz ke spuštění aplikace (ExeHost) použít.
* Jakýkoli skript, který je potřeba spustit, aby se nastavení aplikace (SetupEntrypoint).

Následující je příklad `ServiceManifest.xml` souboru:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

V následujících částech se přenášejí prostřednictvím různých součástí souboru, který je potřeba aktualizovat.

#### <a name="update-servicetypes"></a>ServiceType aktualizace
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Můžete vybrat libovolný název, který chcete použít pro `ServiceTypeName`. Hodnota se používá v `ApplicationManifest.xml` souboru k identifikaci služby.
* Zadejte `UseImplicitHost="true"`. Tento atribut oznamuje Service Fabric, že služby jsou založené na samostatnou aplikaci, všechny Service Fabric je potřeba udělat, je spuštění jako proces a monitorovat jeho stav.

#### <a name="update-codepackage"></a>CodePackage aktualizace
CodePackage prvek určuje umístění (a verzi) služby v kódu.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` Element slouží k určení názvu adresáře v balíčku aplikace, která obsahuje služby v kódu. `CodePackage` má také `version` atribut. To lze použít k určení verze kódu a můžete také potenciálně použije pro upgrade služby v kódu s použitím infrastruktury pro správu životního cyklu aplikací v Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Volitelné: Aktualizace SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Prvek SetupEntryPoint slouží k určení spustitelného souboru nebo dávkového souboru, který má být spuštěn, předtím, než se spustí služby v kódu. Je volitelný krok, takže nemusí být zahrnuta, pokud není požadována žádná inicializace. SetupEntryPoint je proveden při každém restartování služby.

Existuje pouze jednu SetupEntryPoint, instalačních skriptů budou muset být seskupeny do jednoho dávkový soubor, pokud instalační program aplikace vyžaduje několik skriptů. SetupEntryPoint může spustit libovolný typ souborů: spustitelné soubory, dávkové soubory a rutiny prostředí PowerShell. Další podrobnosti najdete v tématu [nakonfigurovat SetupEntryPoint](service-fabric-application-runas-security.md).

V předchozím příkladu běží SetupEntryPoint dávkový soubor s názvem `LaunchConfig.cmd` , který je umístěný v `scripts` jedná o podadresář adresáře kódu (za předpokladu, že WorkingFolder prvek je nastaven na hodnotu základu kódu).

#### <a name="update-entrypoint"></a>Aktualizovat vstupního bodu
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

`EntryPoint` Element v souboru manifestu služby se používá k určení způsobu spuštění služby.

`ExeHost` Prvek Určuje spustitelný soubor (a argumenty), která by měla sloužit ke spuštění služby. Volitelně můžete přidat `IsExternalExecutable="true"` atribut `ExeHost` k označení, že program je externí spustitelného souboru mimo balíček kódu. Například, `<ExeHost IsExternalExecutable="true">`.

* `Program` Určuje název spustitelného souboru, který by měl spustit službu.
* `Arguments` zadává argumenty, které by měly být předány spustitelnému souboru. Může být seznam parametrů s argumenty.
* `WorkingFolder` Určuje pracovní adresář pro proces, který se má spustit. Můžete zadat tři hodnoty:
  * `CodeBase` Určuje, že v pracovním adresáři bude nastavena na adresář kódu v balíčku aplikace (`Code` adresáře v předchozí strukturu souborů).
  * `CodePackage` Určuje, že v pracovním adresáři bude nastavit do kořenového adresáře balíčku aplikace (`GuestService1Pkg` v předchozí strukturu souborů).
    * `Work` Určuje, že jsou soubory umístěny v podadresáři volá práce.

WorkingFolder je vhodné nastavit správný pracovního adresáře tak, aby aplikace nebo inicializační skripty lze použít relativní cesty.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Aktualizovat koncové body a registraci ve službě pojmenování pro komunikaci
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
V předchozím příkladu `Endpoint` prvek určuje koncové body, které aplikace může naslouchat. V tomto příkladu Node.js aplikace naslouchá na protokolu http na portu 3000.

Kromě toho můžete požádat tak další služby můžete zjistit adresu koncového bodu pro tuto službu publikovat tento koncový bod ke službě pojmenování Service Fabric. To umožňuje, abyste mohli ke komunikaci mezi službami, které jsou spustitelné soubory hosta.
Adresa koncového bodu publikované má formát `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` a `PathSuffix` jsou volitelné atributy. `IPAddressOrFQDN` je IP adresa nebo plně kvalifikovaný název domény uzlu získá umístit tento spustitelný soubor a se počítá za vás.

V následujícím příkladu se po nasazení služby v Service Fabric Explorer uvidíte koncový bod podobná `http://10.1.4.92:3000/myapp/` publikována pro instance služby. Nebo pokud je to místní počítač, zobrazí `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Můžete použít tyto adresy s [reverzní proxy server](service-fabric-reverseproxy.md) ke komunikaci mezi službami.

### <a name="edit-the-application-manifest-file"></a>Upravit soubor manifestu aplikace
Po nakonfigurování `Servicemanifest.xml` souboru, je třeba provést některé změny `ApplicationManifest.xml` zajistit, že se používají služby správný typ a název souboru.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
V `ServiceManifestImport` element, můžete zadat jednu nebo víc služeb, které chcete zahrnout do aplikace. Služby jsou odkazovány pomocí `ServiceManifestName`, který určuje název adresáře, kde `ServiceManifest.xml` se nachází soubor.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Nastavení protokolování
Pro spustitelné soubory hosta je užitečné mít možnost zobrazí protokoly konzoly a zjistěte, pokud aplikace a konfigurační skripty zobrazí případné chyby.
Přesměrování konzoly se dá nakonfigurovat v `ServiceManifest.xml` soubor pomocí `ConsoleRedirection` elementu.

> [!WARNING]
> Nikdy nepoužívejte zásady přesměrování konzoly v aplikaci, která je nasazená v produkčním prostředí, protože to může mít vliv převzetí služeb při selhání aplikace. *Pouze* používá se pro místní vývoj a ladění.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` je možné přesměrovat výstup konzoly (stdout i stderr) do pracovního adresáře. To umožňuje ověřit, že neexistují žádné chyby během instalace nebo spuštění aplikace v clusteru Service Fabric.

`FileRetentionCount` Určuje, kolik souborů se ukládají do pracovního adresáře. Hodnota 5, například znamená, že soubory protokolu pro předchozí pět spuštění se ukládají v pracovním adresáři.

`FileMaxSizeInKb` Určuje maximální velikost souboru protokolu.

Soubory protokolu jsou uloženy v jednom z pracovních adresářů služby. Chcete-li určit, kde jsou umístěny soubory, pomocí Service Fabric Explorer rozhodnout, který uzel služby běží na a který pracovní adresář, který se používá. Tento proces se věnujeme dále v tomto článku.

## <a name="deployment"></a>Nasazení
Posledním krokem je [při nasazování aplikace](service-fabric-deploy-remove-applications.md). Následující skript prostředí PowerShell ukazuje, jak nasadit aplikaci do místního vývojového clusteru a spusťte novou službu Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před kopírováním pro úložiště bitových kopií, pokud balíček je velký nebo má mnoho souborů. Přečtěte si další [tady](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Služba Service Fabric je možné nasadit v různých "konfigurace". Například je možné nasadit jako jeden nebo více instancí, nebo je možné nasadit tak, že existuje jedna instance služby na každém uzlu clusteru Service Fabric.

`InstanceCount` Parametr `New-ServiceFabricService` rutina slouží k určení, kolik instancí služby, které má být spuštěna v clusteru Service Fabric. Můžete nastavit `InstanceCount` hodnotu, v závislosti na typu aplikace, kterou nasazujete. Dvě nejběžnější scénáře jsou:

* `InstanceCount = "1"`. V takovém případě je pouze jedna instance služby nasazené v clusteru. Service Fabric Plánovač Určuje, který uzel službu bude možné nasadit na.
* `InstanceCount ="-1"`. V takovém případě se na všech uzlech v clusteru Service Fabric nasadí jednu instanci služby. Výsledek se nutnosti jeden (a pouze jeden) instance služby pro každý uzel v clusteru.

Toto je užitečné konfigurace pro front-endové aplikace (například koncový bod REST), protože klientské aplikace potřebují "připojit" některý z uzlů v clusteru použít koncový bod. Tato konfigurace je také možné po, například všechny uzly clusteru Service Fabric jsou připojené ke službě Vyrovnávání zatížení. Komunikace s klienty lze následně distribuovat napříč službou, na kterém běží na všech uzlech v clusteru.

## <a name="check-your-running-application"></a>Zkontrolujte běžící aplikace
V Service Fabric Explorer Identifikujte uzlu, kde je služba spuštěna. V tomto příkladu poběží Node1:

![Uzel, ve kterém je spuštěna služba](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Pokud přejdete do uzlu a přejděte do aplikace, se zobrazí informace o základní uzel, včetně jeho umístění na disku.

![Umístění na disku](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Pokud přejděte do adresáře pomocí Průzkumníka serveru můžete najít pracovní adresář a složka služby protokolu, jak je znázorněno na následujícím snímku obrazovky: 

![Umístění protokolu](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak spustitelný soubor typu Host balení a nasazení do Service Fabric. Naleznete v následujících článcích pro související informace a úkoly.

* [Ukázka pro balení a nasazení hostujícího spustitelného souboru](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), včetně odkazu na předběžnou verzi nástroje pro balení
* [Ukázka dvě hosta spustitelných souborů (C# a Node.js) komunikaci přes službu pojmenování pomocí rozhraní REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Nasazení několika hostujících spustitelných souborů](service-fabric-deploy-multiple-apps.md)
* [Vytvoření první aplikace Service Fabric pomocí sady Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
