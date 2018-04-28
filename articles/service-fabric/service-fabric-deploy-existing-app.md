---
title: Nasadit existující spustitelný soubor do Azure Service Fabric | Microsoft Docs
description: Informace k balíčkování stávající aplikace jako Host spustitelného souboru, aby ji můžete nasadit na cluster Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 07/02/2017
ms.author: mfussell;mikhegn
ms.openlocfilehash: 9d1e2f801db2ed7edf262f8a5f221057ef791106
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Zabalení a nasazení existující spustitelný soubor do Service Fabric
Když balení existující spustitelný soubor jako [spustitelný soubor hosta](service-fabric-guest-executables-introduction.md), můžete buď používat šablony projektů Visual Studio nebo [ručně vytvořit balíček aplikace](#manually). Pomocí sady Visual Studio, struktury balíček aplikace a soubory manifestu vytvářejí nové šablona projektu pro vás.

> [!TIP]
> Nejjednodušší způsob, jak spustitelný soubor do služby Windows pro existující balíček je pomocí sady Visual Studio a v systému Linux používat Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Použijte sadu Visual Studio pro zabalení a nasazení existující spustitelný soubor
Visual Studio poskytuje šablony služby Service Fabric vám pomůžou nasadit do clusteru Service Fabric Host spustitelný soubor.

1. Zvolte **soubor** > **nový projekt**a vytvářet aplikace Service Fabric.
2. Zvolte **spustitelný soubor hosta** jako šablonu služby.
3. Klikněte na tlačítko **Procházet** vyberte složku s vaší spustitelný soubor a vyplňte zbytek parametrů k vytvoření služby.
   * *Kód balíčku chování*. Může být nastaven na veškerý obsah složky zkopírujte do projektu Visual Studio, což je užitečné, pokud spustitelný soubor se nemění. Pokud očekáváte, spustitelný soubor změnit a chcete mít možnost dynamicky vyzvednutí nových sestavení automaticky, můžete místo toho odkaz ke složce. Propojené složek můžete použít při vytváření projektu aplikace v sadě Visual Studio. Tato sestava odkazuje na umístění zdroje z v projektu, což vám umožní aktualizovat Host spustitelný soubor v jeho zdroj cíl. Tyto aktualizace se stanou součástí balíčku aplikace při sestavování.
   * *Program* Určuje spustitelný soubor, který by měl být spuštěn pro spuštění služby.
   * *Argumenty* Určuje argumenty, které by měla být předána spustitelný soubor. Může být seznam parametrů s argumenty.
   * *WorkingFolder* určuje pracovní adresář pro proces, který chcete spustit. Můžete určit tří hodnot:
     * `CodeBase` Určuje, že pracovní adresář bude být nastavena na adresář kódu v balíčku aplikace (`Code` directory uvedené v předchozí strukturu souborů).
     * `CodePackage` Určuje, že pracovní adresář bude se nastaví na kořen balíčku aplikace (`GuestService1Pkg` uvedené v předchozí strukturu souborů).
     * `Work` Určuje, že soubory jsou umístěny v podadresáři volat pracovní.
4. Zadejte název služby a klikněte na **OK**.
5. Pokud vaše služba musí koncový bod pro komunikaci, můžete nyní přidat protokol, port a typ souboru ServiceManifest.xml. Například: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Teď můžete použít balíček a publikovat akce s místním clusteru tak, že ladění řešení v sadě Visual Studio. Až bude připravený, můžete publikovat aplikaci do vzdáleného clusteru nebo řešení správy zdrojového kódu se změnami.
7. Čtení [zkontrolujte běžící aplikaci](#check-your-running-application) chcete zjistit, jak chcete-li zobrazit hosta spustitelný soubor služby spuštěné v Service Fabric Exploreru.

Návod příklad najdete v tématu [vytvoření první aplikace spustitelné hosta pomocí sady Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Yeoman pomocí balíčku a nasadit existující spustitelný soubor v systému Linux

Postup pro vytvoření a nasazení hosta spustitelného souboru v systému Linux je stejný jako nasazení aplikace csharp nebo java.

1. V terminálu zadejte `yo azuresfguest`.
2. Pojmenujte svoji aplikaci.
3. Název služby a zadejte podrobnosti, včetně cesta ke spustitelnému souboru a parametry, které musí být volána s.

Yeoman vytvoří balíček aplikace s příslušnou aplikaci a souborů manifestu spolu s instalaci a odinstalaci skripty.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Ručně zabalení a nasazení existující spustitelný soubor
Proces ručně balení spustitelný soubor hosta je založena na následujících obecných kroků:

1. Vytvořte strukturu adresáře balíčku.
2. Přidejte kód a konfigurační soubory aplikace.
3. Upravte soubor manifestu služby.
4. Upravte soubor manifestu aplikace.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Vytvořit strukturu adresáře balíčku
Můžete spustit tak, že vytvoříte strukturu adresáře, jak je popsáno v předchozí části "Aplikace balíčku souboru struktura."

### <a name="add-the-applications-code-and-configuration-files"></a>Přidání kódu a konfigurační soubory aplikace
Po vytvoření strukturu adresáře, můžete přidat kód aplikace a konfigurační soubory v adresáři kódu a konfigurace. Můžete také vytvořit další adresáře nebo podadresáře v adresáři kódu nebo konfigurace.

Service Fabric nemá `xcopy` obsahu kořenového adresáře aplikace, takže není k dispozici žádné předdefinované struktura používat jiné než vytváření dva hlavní adresáře, kód a nastavení. (Můžete si vybrat odlišné názvy podle potřeby. Další podrobnosti najdete v další části.)

> [!NOTE]
> Ujistěte se, jestli jste zahrnuli všechny soubory a závislosti, které aplikace potřebuje. Service Fabric zkopíruje obsah balíčku aplikace na všech uzlech v clusteru, kde se chystáte služby aplikace nasadit. Tento balíček měl obsahovat všechny kód, který aplikace je potřeba spustit. Nepředpokládejte, že závislosti jsou již nainstalovány.
>
>

### <a name="edit-the-service-manifest-file"></a>Upravit soubor manifestu služby
Dalším krokem je upravit soubor manifestu služby zahrnout tyto informace:

* Název typu služby. Toto je ID, které Service Fabric používá k identifikaci služby.
* Příkaz sloužící ke spuštění aplikace (ExeHost).
* Žádný skript, který je potřeba spustit k nastavení aplikace (SetupEntrypoint).

Tady je příklad `ServiceManifest.xml` souboru:

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

V následujících částech projít různé části souboru, který je potřeba aktualizovat.

#### <a name="update-servicetypes"></a>Aktualizace ServiceTypes
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Můžete vybrat libovolný název, který chcete použít pro `ServiceTypeName`. Hodnota se používá v `ApplicationManifest.xml` souboru k identifikaci služby.
* Zadejte `UseImplicitHost="true"`. Tento atribut informuje Service Fabric, že služba je založen na samostatná aplikace, tak, aby všechny musí Service Fabric se spustí jako proces a monitorovat jeho stav.

#### <a name="update-codepackage"></a>CodePackage aktualizace
CodePackage element určuje umístění (a verzi) kódu služby.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` Element se používá k určení názvu adresáře v balíčku aplikace, která obsahuje kód služby. `CodePackage` má také `version` atribut. To lze použít k určení verze kódu a můžete také potenciálně použít k upgradu služby kódu s použitím infrastruktury pro správu životního cyklu aplikace v Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Volitelné: Aktualizace SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntryPoint element slouží k určení spustitelného souboru nebo v dávkovém souboru, který se má provést před spuštění kódu služby. Je volitelný krok, takže nemusí být zahrnuty, pokud není žádná inicializace požadována. SetupEntryPoint je proveden při každém restartování služby.

Se jenom jeden SetupEntryPoint, takže skripty instalace musí být seskupeny do jednoho dávkový soubor, pokud instalační program aplikace vyžaduje několik skriptů. SetupEntryPoint může spustit libovolný typ souboru: spustitelné soubory, dávkové soubory a rutiny prostředí PowerShell. Další podrobnosti najdete v tématu [konfigurace SetupEntryPoint](service-fabric-application-runas-security.md).

V předchozím příkladu běží SetupEntryPoint batch soubor s názvem `LaunchConfig.cmd` který je umístěný v `scripts` podadresáři adresáři kódu (za předpokladu, že WorkingFolder element je nastaven na hodnotu základu kódu).

#### <a name="update-entrypoint"></a>EntryPoint aktualizace
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

`EntryPoint` Element v souboru manifestu služby se používá k určení způsobu spuštění služby. `ExeHost` Element Určuje spustitelný soubor (a argumentů), by měl být použitý ke spuštění služby.

* `Program` Určuje název spustitelného souboru, který by měl spustit službu.
* `Arguments` Určuje argumenty, které by měla být předána spustitelný soubor. Může být seznam parametrů s argumenty.
* `WorkingFolder` Určuje pracovní adresář pro proces, který chcete spustit. Můžete určit tří hodnot:
  * `CodeBase` Určuje, že pracovní adresář bude být nastavena na adresář kódu v balíčku aplikace (`Code` adresář v předchozím struktuře souborů).
  * `CodePackage` Určuje, že pracovní adresář bude se nastaví na kořen balíčku aplikace (`GuestService1Pkg` v předchozí struktuře souborů).
    * `Work` Určuje, že soubory jsou umístěny v podadresáři volat pracovní.

WorkingFolder je vhodné nastavit správné pracovní adresář tak, aby relativní cesty mohou být využívána aplikace nebo inicializace skripty.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Aktualizovat koncové body a zaregistrovat u služby DNS pro komunikaci
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
V předchozím příkladu `Endpoint` element určuje koncové body, které aplikace může naslouchat na portu. V tomto příkladu aplikace Node.js naslouchá na protokolu http na portu 3000.

Kromě toho můžete pokládat Service Fabric publikovat tento koncový bod do služby DNS, tak další služby můžete zjistit adresa koncového bodu pro tuto službu. To vám umožňuje komunikovat mezi službami, které jsou hostované spustitelné soubory.
Adresa publikované koncového bodu je ve formátu `UriScheme://IPAddressOrFQDN:Port/PathSuffix`. `UriScheme` a `PathSuffix` jsou volitelných atributů. `IPAddressOrFQDN` je IP adresa nebo plně kvalifikovaný název domény tohoto spustitelného souboru získá umístěny na uzlu, a se vypočítává za vás.

V následujícím příkladu, jakmile nasazení služby v Service Fabric Explorer zobrazí podobná koncový bod `http://10.1.4.92:3000/myapp/` publikována pro instance služby. Nebo pokud se jedná se o místní počítač, zobrazí `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Můžete použít tyto adresy s [reverse proxy](service-fabric-reverseproxy.md) ke komunikaci mezi službami.

### <a name="edit-the-application-manifest-file"></a>Upravit soubor manifestu aplikace
Po konfiguraci `Servicemanifest.xml` souboru, budete muset udělat některé změny `ApplicationManifest.xml` zajistíte, že jsou použité správné služby typu a název souboru.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
V `ServiceManifestImport` elementu, můžete zadat jednu nebo více služeb, které chcete zahrnout do aplikace. Služby jsou odkazovány pomocí `ServiceManifestName`, která určuje název adresáře, kde `ServiceManifest.xml` se nachází soubor.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Nastavení protokolování
Pro spustitelné soubory hosta je vhodné zobrazit protokoly konzoly a zjistěte, pokud aplikace a konfigurační skripty zobrazit všechny chyby.
Přesměrování konzoly se dá nakonfigurovat v `ServiceManifest.xml` souboru pomocí `ConsoleRedirection` elementu.

> [!WARNING]
> Nikdy nepoužívejte konzolu Zásady přesměrování v aplikaci, která je nasazena v produkčním prostředí, protože to může mít vliv převzetí služeb při selhání aplikaci. *Pouze* používejte pro místní vývoj a účely ladění.  
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

`ConsoleRedirection` slouží k přesměrování výstup konzoly (stdout a stderr) do pracovního adresáře. To umožňuje ověřit, zda nejsou žádné chyby při instalaci nebo spuštění aplikace v clusteru Service Fabric.

`FileRetentionCount` Určuje, kolik souborů se ukládají do pracovní adresář. Hodnota 5, například znamená, že soubory protokolu pro předchozí pět spuštěních se ukládají v pracovním adresáři.

`FileMaxSizeInKb` Určuje maximální velikost protokolových souborů.

Ukládání souborů protokolu v jednom ze služby pracovních adresářích. Chcete-li určit, kde jsou umístěny soubory, pomocí Service Fabric Explorer rozhodnout, který uzel služby běží na a pracovní adresář, kterému je používán. Tento proces je popsaná později v tomto článku.

## <a name="deployment"></a>Nasazení
Posledním krokem je [nasazení aplikace](service-fabric-deploy-remove-applications.md). Následující skript prostředí PowerShell ukazuje, jak nasadit aplikace do místního vývojového clusteru a spustit novou službu Service Fabric.

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
> [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před kopírování do úložiště bitové kopie, pokud tento balíček je velký nebo má mnoho souborů. Další informace [zde](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Služba Service Fabric se dá nasadit v různých "konfigurace". Například se můžete nasadit jako jeden nebo více instancí, nebo se můžete nasadit tak, že existuje jedna instance služby v každém uzlu clusteru Service Fabric.

`InstanceCount` Parametr `New-ServiceFabricService` rutiny slouží k určení, kolik instancí služby musí být spuštěna v clusteru Service Fabric. Můžete nastavit `InstanceCount` hodnotu, v závislosti na typu aplikace, kterou nasazujete. Dva nejběžnější scénáře jsou:

* `InstanceCount = "1"`. V takovém případě je pouze jedna instance služby nasadit v clusteru. Scheduler Service Fabric Určuje, který uzel služby se chystáte nasadit na.
* `InstanceCount ="-1"`. V takovém případě jedna instance služby je nasazen na každý uzel v clusteru Service Fabric. Výsledek má jeden (a pouze jeden) instance služby pro každý uzel v clusteru.

Toto je užitečné konfiguraci pro front-endu aplikace (například koncový bod REST), protože klientské aplikace potřebují "připojit" pro všechny uzly v clusteru, aby používaly koncový bod. Tato konfigurace mohou sloužit také když například jsou všechny uzly clusteru Service Fabric připojeni ke službě Vyrovnávání zatížení. Přenosy klienta lze následně distribuovat napříč službou, která běží na všech uzlech v clusteru.

## <a name="check-your-running-application"></a>Zkontrolujte spuštěné aplikace
V Service Fabric Exploreru Identifikujte uzlu, kde je služba spuštěná. V tomto příkladu je spuštěna na Uzel1:

![Uzel, na kterém je spuštěna služba](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Přejděte na uzel a přejděte do aplikace, zobrazí informace o základní uzlu, včetně jeho umístění na disku.

![Umístění na disku](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Pokud přejdete do adresáře pomocí Průzkumníku serveru, můžete najít pracovní adresář a služby do složky protokolů, jak je znázorněno na následujícím snímku obrazovky: 

![Umístění protokolu](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Další postup
V tomto článku jste se naučili, jak zabalit spustitelný soubor hosta a nasadíte ho do Service Fabric. Najdete v následujících článcích související informace a úlohy.

* [Ukázka pro balení a nasazení spustitelný soubor hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), včetně odkaz na zkušební verzi nástroje balení
* [Ukázka dvěma hosta spustitelné soubory (C# a nodejs) komunikaci přes službu Naming pomocí REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Nasazení několika hostujících spustitelných souborů](service-fabric-deploy-multiple-apps.md)
* [Vytvoření první aplikace Service Fabric pomocí sady Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
