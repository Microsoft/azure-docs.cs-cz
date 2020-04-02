---
title: Nasazení existujícího spustitelného souboru do azure service fabric
description: Zjistěte, jak zabalit existující aplikaci jako spustitelný soubor hosta, aby ji bylo možné nasadit do clusteru Service Fabric.
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c6c6bc0369593c177b74261da1fd8c15dd73fcb3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520491"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Balíček a nasazení existujícího spustitelného souboru do service fabric

Při balení existující ho spustitelný soubor jako [spustitelný soubor hosta](service-fabric-guest-executables-introduction.md), můžete zvolit buď použít šablonu projektu sady Visual Studio nebo [vytvořit balíček aplikace ručně](#manually). Pomocí sady Visual Studio jsou struktura balíčku aplikace a soubory manifestu vytvořeny novou šablonou projektu za vás.

> [!TIP]
> Nejjednodušší způsob, jak zabalit existující spustitelný soubor systému Windows do služby, je použít Visual Studio a na Linuxu k použití Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Použití sady Visual Studio k balení a nasazení existujícího spustitelného souboru

Visual Studio poskytuje šablonu služby Service Fabric, která vám pomůže nasadit spustitelný soubor hosta do clusteru Service Fabric.

1. Zvolte **Soubor** > **nový projekt**a vytvořte aplikaci Service Fabric.
2. Jako šablonu služby zvolte **Spustitelný soubor** hosta.
3. Kliknutím na **Procházet** vyberte složku s spustitelným souborem a vyplňte zbývající parametry a vytvořte službu.
   * *Chování balíčku kódu*. Lze nastavit zkopírovat veškerý obsah složky do aplikace Visual Studio Project, což je užitečné, pokud se spustitelný soubor nezmění. Pokud očekáváte, že se spustitelný soubor změní a chcete možnost dynamicky vybírat nová sestavení, můžete místo toho vytvořit odkaz na složku. Propojené složky můžete použít při vytváření aplikačního projektu v sadě Visual Studio. To to odkazy na umístění zdroje z v rámci projektu, takže je možné aktualizovat spustitelný soubor hosta v jeho zdrojovém cíli. Tyto aktualizace se stanou součástí balíčku aplikace na sestavení.
   * *Program* určuje spustitelný soubor, který by měl být spuštěn ke spuštění služby.
   * *Argumenty* určuje argumenty, které by měly být předány spustitelnému souboru. Může to být seznam parametrů s argumenty.
   * *WorkingFolder* určuje pracovní adresář pro proces, který bude spuštěn. Můžete zadat tři hodnoty:
     * `CodeBase`určuje, že pracovní adresář bude nastaven na adresář kódu v`Code` balíčku aplikace (adresář zobrazený v předchozí struktuře souborů).
     * `CodePackage`určuje, že pracovní adresář bude nastaven na kořenový adresář`GuestService1Pkg` balíčku aplikace (zobrazený v předchozí struktuře souborů).
     * `Work`určuje, že soubory jsou umístěny v podadresáři nazývaném práce.
4. Zadejte název služby a klikněte na **OK**.
5. Pokud vaše služba potřebuje koncový bod pro komunikaci, můžete nyní přidat protokol, port a typ do souboru ServiceManifest.xml. Například: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Nyní můžete použít balíček a publikovat akci proti místnímu clusteru laděním řešení v sadě Visual Studio. Až budete připraveni, můžete publikovat aplikaci do vzdáleného clusteru nebo vrácení řešení se změnami do správy zdrojového kódu.
7. Přečtěte si [zkontrolujte spuštěnou aplikaci](#check-your-running-application) a zjistěte, jak zobrazit spustitelnou službu hosta spuštěnou v aplikaci Service Fabric Explorer.

Příklad návodu najdete v [tématu Vytvoření první spustitelné aplikace hosta pomocí sady Visual Studio](quickstart-guest-app.md).

### <a name="packaging-multiple-executables-with-visual-studio"></a>Balení více spustitelných souborů pomocí sady Visual Studio

Pomocí sady Visual Studio můžete vytvořit balíček aplikace, který obsahuje více spustitelných souborů hosta. Po přidání prvního spustitelného souboru hosta klikněte pravým tlačítkem myši na projekt aplikace a vyberte **službu Add->New Service Fabric** a přidejte do řešení druhý spustitelný projekt hosta.

> [!NOTE]
> Pokud se rozhodnete propojit zdroj v projektu sady Visual Studio, sestavení řešení Sady Visual Studio, zajistí, že balíček aplikace je aktuální se změnami ve zdroji.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Použití Yeoman zabalit a nasadit existující spustitelný soubor na Linuxu

Postup pro vytvoření a nasazení spustitelného souboru hosta v Linuxu je stejný jako nasazení aplikace csharp nebo java.

1. V terminálu zadejte `yo azuresfguest`.
2. Pojmenujte svoji aplikaci.
3. Pojmenujte službu a zadejte podrobnosti včetně cesty ke spustitelnému souboru a parametrů, které musí být vyvolány.

Yeoman vytvoří balíček aplikace s příslušnou aplikací a soubory manifestu spolu s instalací a odinstalací skriptů.

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Balení více spustitelných souborů pomocí Yeoman na Linuxu

Pokud chcete přidat další službu do aplikace již vytvořené pomocí `yo`, proveďte následující kroky:

1. Změňte adresář na kořenovou složku stávající aplikace.  Například `cd ~/YeomanSamples/MyApplication`, pokud `MyApplication` je aplikace vytvořená pomocí Yeomanu.
2. Spusťte `yo azuresfguest:AddService` a poskytněte potřebné podrobnosti.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Ruční balíček a nasazení existujícího spustitelného souboru

Proces ručního balení spustitelného souboru hosta je založen na následujících obecných krocích:

1. Vytvořte adresářovou strukturu balíčku.
2. Přidejte kód aplikace a konfigurační soubory.
3. Upravte soubor manifestu služby.
4. Upravte soubor manifestu aplikace.

### <a name="create-the-package-directory-structure"></a>Vytvoření adresářové struktury balíčku

Můžete začít vytvořením adresářové struktury, jak je popsáno v [package a Azure Service Fabric App](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Přidání kódu aplikace a konfiguračních souborů

Po vytvoření adresářové struktury můžete přidat kód aplikace a konfigurační soubory pod kód a konfigurační adresáře. Pod kódem nebo config adresáři můžete také vytvořit další adresáře nebo podadresáře.

Service Fabric `xcopy` provádí obsah kořenového adresáře aplikace, takže neexistuje žádná předdefinovaná struktura, kterou by bylo možné použít, než vytvořit dva horní adresáře, kód a nastavení. (Můžete si vybrat různé názvy, pokud chcete. Další podrobnosti jsou v další části.)

> [!NOTE]
> Ujistěte se, že zahrnete všechny soubory a závislosti, které aplikace potřebuje. Service Fabric zkopíruje obsah balíčku aplikace na všech uzlech v clusteru, kde se budou nasadit služby aplikace. Balíček by měl obsahovat veškerý kód, který aplikace potřebuje ke spuštění. Nepředpokládejte, že závislosti jsou již nainstalovány.
>
>

### <a name="edit-the-service-manifest-file"></a>Úprava souboru manifestu služby

Dalším krokem je úprava souboru manifestu služby tak, aby obsahoval následující informace:

* Název typu služby. Toto je ID, které Service Fabric používá k identifikaci služby.
* Příkaz, který chcete použít ke spuštění aplikace (ExeHost).
* Jakýkoli skript, který je třeba spustit k nastavení aplikace (SetupEntrypoint).

Následuje příklad souboru: `ServiceManifest.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

V následujících částech přejděte různé části souboru, které je třeba aktualizovat.

#### <a name="update-servicetypes"></a>Aktualizovat typy služeb

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Můžete si vybrat libovolný název, který chcete pro . `ServiceTypeName` Hodnota se používá `ApplicationManifest.xml` v souboru k identifikaci služby.
* Zadejte `UseImplicitHost="true"`. Tento atribut říká Service Fabric, že služba je založena na samostatné aplikaci, takže všechny Service Fabric musí udělat, je spustit jako proces a sledovat jeho stav.

#### <a name="update-codepackage"></a>Aktualizovat balíček kódu
CodePackage Prvek určuje umístění (a verze) kódu služby.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Prvek `Name` se používá k určení názvu adresáře v balíčku aplikace, který obsahuje kód služby. `CodePackage`má také `version` atribut. To lze použít k určení verze kódu a lze také potenciálně použít k upgradu kódu služby pomocí infrastruktury správy životního cyklu aplikace v Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Volitelné: Aktualizace instalačního bodu

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Element SetupEntryPoint se používá k určení libovolného spustitelného nebo dávkového souboru, který by měl být spuštěn před spuštěním kódu služby. Jedná se o volitelný krok, takže nemusí být zahrnut, pokud není vyžadována inicializace. SetupEntryPoint je spuštěn při každém restartování služby.

Existuje pouze jeden setupEntryPoint, takže instalační skripty musí být seskupeny do jednoho dávkového souboru, pokud nastavení aplikace vyžaduje více skriptů. SetupEntryPoint může spustit libovolný typ souboru: spustitelné soubory, dávkové soubory a rutiny prostředí PowerShell. Další podrobnosti naleznete v [tématu Configure SetupEntryPoint](service-fabric-application-runas-security.md).

V předchozím příkladu instalační program SetupEntryPoint `LaunchConfig.cmd` spustí dávkový `scripts` soubor s názvem umístěný v podadresáři adresáře kódu (za předpokladu, že prvek WorkingFolder je nastaven na CodeBase).

#### <a name="update-entrypoint"></a>Aktualizovat entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Prvek `EntryPoint` v souboru manifestu služby se používá k určení způsobu spuštění služby.

Prvek `ExeHost` určuje spustitelný soubor (a argumenty), který by měl být použit ke spuštění služby. Volitelně `IsExternalExecutable="true"` můžete přidat atribut `ExeHost` k označení, že program je externí spustitelný soubor mimo balíček kódu. Například, `<ExeHost IsExternalExecutable="true">`.

* `Program`určuje název spustitelného souboru, který by měl službu spustit.
* `Arguments`určuje argumenty, které by měly být předány spustitelnému souboru. Může to být seznam parametrů s argumenty.
* `WorkingFolder`určuje pracovní adresář pro proces, který bude spuštěn. Můžete zadat tři hodnoty:
  * `CodeBase`určuje, že pracovní adresář bude nastaven na adresář kódu v`Code` balíčku aplikace (adresář v předchozí struktuře souborů).
  * `CodePackage`určuje, že pracovní adresář bude nastaven na kořenový adresář`GuestService1Pkg` balíčku aplikace (v předchozí struktuře souborů).
    * `Work`určuje, že soubory jsou umístěny v podadresáři nazývaném práce.

WorkingFolder je užitečné nastavit správný pracovní adresář tak, aby relativní cesty mohou být použity aplikace nebo inicializační skripty.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Aktualizace koncových bodů a registrace pomocí služby pojmenování pro komunikaci

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```

V předchozím příkladu `Endpoint` prvek určuje koncové body, které aplikace může poslouchat. V tomto příkladu aplikace Node.js naslouchá na http na portu 3000.

Dále můžete požádat Service Fabric publikovat tento koncový bod do pojmenování služby, aby ostatní služby můžete zjistit adresu koncového bodu této služby. To umožňuje komunikovat mezi službami, které jsou spustitelné soubory hosta.
Publikovaná adresa koncového bodu `UriScheme://IPAddressOrFQDN:Port/PathSuffix`je formuláře . `UriScheme`a `PathSuffix` jsou volitelné atributy. `IPAddressOrFQDN`je IP adresa nebo plně kvalifikovaný název domény uzlu, na který je tento spustitelný soubor umístěn, a vypočítá se za vás.

V následujícím příkladu po nasazení služby se v aplikaci Service `http://10.1.4.92:3000/myapp/` Fabric Explorer zobrazí koncový bod podobný publikované pro instanci služby. Nebo pokud se jedná o `http://localhost:3000/myapp/`místní počítač, vidíte .

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```

Tyto adresy s [reverzníproxy](service-fabric-reverseproxy.md) můžete použít ke komunikaci mezi službami.

### <a name="edit-the-application-manifest-file"></a>Úprava souboru manifestu aplikace

Po konfiguraci `Servicemanifest.xml` souboru je třeba provést některé `ApplicationManifest.xml` změny v souboru, abyste zajistili, že bude použit správný typ a název služby.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

V `ServiceManifestImport` elementu můžete zadat jednu nebo více služeb, které chcete zahrnout do aplikace. Služby jsou `ServiceManifestName`odkazovány s , který určuje `ServiceManifest.xml` název adresáře, kde je soubor umístěn.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Nastavení protokolování

U spustitelných souborů hosta je užitečné zobrazit protokoly konzoly a zjistit, zda skripty aplikace a konfigurace zobrazují nějaké chyby.
Přesměrování konzoly lze nakonfigurovat v souboru `ServiceManifest.xml` pomocí `ConsoleRedirection` prvku.

> [!WARNING]
> Nikdy nepoužívejte zásady přesměrování konzoly v aplikaci, která je nasazena v produkčním prostředí, protože to může ovlivnit převzetí služeb při selhání aplikace. Používejte *pouze* pro účely místního vývoje a ladění.  
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

`ConsoleRedirection`lze použít k přesměrování výstupu konzoly (stdout i stderr) do pracovního adresáře. To poskytuje možnost ověřit, že neexistují žádné chyby během instalace nebo spuštění aplikace v clusteru Service Fabric.

`FileRetentionCount`určuje, kolik souborů je uloženo v pracovním adresáři. Hodnota 5, například znamená, že soubory protokolu pro předchozích pět spuštění jsou uloženy v pracovním adresáři.

`FileMaxSizeInKb`určuje maximální velikost souborů protokolu.

Soubory protokolu jsou uloženy v jednom z pracovních adresářů služby. Chcete-li zjistit, kde jsou soubory umístěny, použijte Service Fabric Explorer k určení, který uzel je služba spuštěna na a který pracovní adresář se používá. Tento proces je popsán dále v tomto článku.

## <a name="deployment"></a>Nasazení

Posledním krokem je [nasazení aplikace](service-fabric-deploy-remove-applications.md). Následující skript prostředí PowerShell ukazuje, jak nasadit aplikaci do clusteru místního vývoje a spustit novou službu Service Fabric.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před kopírováním do úložiště obrázků, pokud balíček je velký nebo má mnoho souborů. Přečtěte si více [zde](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Služba Service Fabric lze nasadit v různých "konfiguracích". Například může být nasazen jako jeden nebo více instancí nebo může být nasazen takovým způsobem, že existuje jedna instance služby na každém uzlu clusteru Service Fabric.

Parametr `InstanceCount` `New-ServiceFabricService` rutiny se používá k určení, kolik instancí služby by měla být spuštěna v clusteru Service Fabric. Můžete nastavit `InstanceCount` hodnotu, v závislosti na typu aplikace, kterou nasazujete. Dva nejběžnější scénáře jsou:

* `InstanceCount = "1"`. V tomto případě je v clusteru nasazena pouze jedna instance služby. Plánovač Service Fabric určuje, na kterém uzlu bude služba nasazena.
* `InstanceCount ="-1"`. V tomto případě je jedna instance služby nasazena na každém uzlu v clusteru Service Fabric. Výsledkem je jedna (a pouze jedna) instance služby pro každý uzel v clusteru.

To je užitečná konfigurace pro front-endové aplikace (například koncový bod REST), protože klientské aplikace se musí "připojit" k libovolnému uzlu v clusteru, aby mohly koncový bod používat. Tuto konfiguraci lze také použít, když jsou například všechny uzly clusteru Service Fabric připojeny k nástroji pro vyrovnávání zatížení. Provoz klienta pak lze distribuovat napříč službou, která je spuštěna na všech uzlech v clusteru.

## <a name="check-your-running-application"></a>Kontrola spuštěné aplikace
V Průzkumníku prostředků infrastruktury služeb identifikujte uzel, ve kterém je služba spuštěna. V tomto příkladu běží na Node1:

![Uzel, ve kterém je spuštěna služba](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Pokud přejdete do uzlu a přejdete k aplikaci, zobrazí se základní informace o uzlu, včetně jeho umístění na disku.

![Umístění na disku](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Pokud do adresáře přejdete pomocí Průzkumníka serveru, najdete pracovní adresář a složku protokolu služby, jak je znázorněno na následujícím snímku obrazovky:

![Umístění logu](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak zabalit spustitelný soubor hosta a nasadit jej do Service Fabric. Související informace a úkoly naleznete v následujících článcích.

* [Ukázka pro balení a nasazení spustitelného souboru hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), včetně odkazu na předběžnou verzi balicího nástroje
* [Ukázka dvou spustitelných souborů hosta (C# a nodejs), které komunikují prostřednictvím služby Pojmenování pomocí rest](https://github.com/Azure-Samples/service-fabric-containers)
* [Vytvoření první aplikace Service Fabric pomocí sady Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
