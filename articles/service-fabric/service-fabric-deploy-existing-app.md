---
title: Nasazení existujícího spustitelného souboru do Azure Service Fabric
description: Naučte se, jak zabalit existující aplikaci jako spustitelný soubor hosta, takže se dá nasadit do clusteru Service Fabric.
ms.topic: conceptual
ms.date: 07/02/2017
ms.openlocfilehash: cdbc965d0e8ec4a8f42fbe438b8ac6ddfe05a1b3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75377102"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Zabalit a nasadit existující spustitelný soubor pro Service Fabric
Při balení existující spustitelný soubor jako [hostující spustitelný soubor](service-fabric-guest-executables-introduction.md)můžete zvolit buď použití šablony projektu sady Visual Studio, nebo [Vytvoření balíčku aplikace ručně](#manually). Pomocí sady Visual Studio je vytvořena šablona balíčku aplikace a soubory manifestu, které jsou vytvořeny novou šablonou projektu za vás.

> [!TIP]
> Nejjednodušší způsob, jak zabalit existující spustitelný soubor systému Windows do služby, je použít sadu Visual Studio a systém Linux pro použití Yeoman
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Použití sady Visual Studio k zabalení a nasazení stávajícího spustitelného souboru
Visual Studio poskytuje šablonu služby Service Fabric, která vám umožní nasadit spustitelný soubor hosta do clusteru Service Fabric.

1. Vyberte **soubor** > **Nový projekt**a vytvořte Service Fabric aplikaci.
2. Jako šablonu služby vyberte **spustitelný soubor typu Host** .
3. Klikněte na **Procházet** a vyberte složku s vaším spustitelným souborem a vyplňte zbývající parametry a vytvořte službu.
   * *Chování balíčku kódu*. Může být nastaveno na zkopírování veškerého obsahu vaší složky do projektu aplikace Visual Studio, což je užitečné, pokud se spustitelný soubor nemění. Pokud očekáváte, že se spustitelný soubor změní a chcete, aby se dynamicky vybrala nová sestavení, můžete místo toho použít odkaz na složku. Můžete použít propojené složky při vytváření projektu aplikace v aplikaci Visual Studio. Tato vazba odkazuje na zdrojové umístění v rámci projektu, což vám umožní aktualizovat spustitelný soubor hosta ve zdrojovém cílovém umístění. Tyto aktualizace se stanou součástí balíčku aplikace při sestavení.
   * *Program* Určuje spustitelný soubor, který se má spustit pro spuštění služby.
   * *Argumenty* určují argumenty, které se mají předat spustitelnému souboru. Může se jednat o seznam parametrů s argumenty.
   * *WorkingFolder* určuje pracovní adresář pro proces, který bude spuštěn. Můžete zadat tři hodnoty:
     * `CodeBase` určuje, zda bude pracovní adresář nastaven na adresář kódu v balíčku aplikace (adresář`Code` zobrazený v předchozí struktuře souborů).
     * `CodePackage` určuje, zda bude pracovní adresář nastaven na kořen balíčku aplikace (`GuestService1Pkg` zobrazený v předchozí struktuře souborů).
     * `Work` určuje, že soubory jsou umístěny v podadresáři s názvem Work.
4. Zadejte název služby a klikněte na **OK**.
5. Pokud vaše služba potřebuje koncový bod pro komunikaci, můžete teď do souboru ServiceManifest. XML přidat protokol, port a typ. Například: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. Pomocí ladění řešení v aplikaci Visual Studio teď můžete použít akci balíčku a publikování pro svůj místní cluster. Až budete připraveni, můžete aplikaci publikovat do vzdáleného clusteru nebo vrátit se změnami řešení do správy zdrojového kódu.
7. Přečtěte si, jak [Spustit aplikaci](#check-your-running-application) , abyste viděli, jak zobrazit službu spustitelných souborů hosta běžící v Service Fabric Explorer.

Ukázkový návod najdete v tématu [Vytvoření první spustitelné aplikace hosta pomocí sady Visual Studio](quickstart-guest-app.md).

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Zabalení a nasazení stávajícího spustitelného souboru v systému Linux pomocí Yeoman

Postup vytvoření a nasazení spustitelného souboru hosta v systému Linux je stejný jako nasazení aplikace CSharp nebo Java.

1. V terminálu zadejte `yo azuresfguest`.
2. Pojmenujte svoji aplikaci.
3. Pojmenujte službu a zadejte podrobnosti včetně cesty ke spustitelnému souboru a parametrů, které musí být vyvolány pomocí.

Yeoman vytvoří balíček aplikace s odpovídající aplikací a soubory manifestu spolu s instalačními a odinstalačními skripty.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Ruční zabalení a nasazení stávajícího spustitelného souboru
Proces ručního balení spustitelného souboru hosta je založen na následujících obecných krocích:

1. Vytvořte adresářovou strukturu balíčku.
2. Přidejte kód a konfigurační soubory aplikace.
3. Upravte soubor manifestu služby.
4. Upravte soubor manifestu aplikace.

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](https://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>Vytvořit adresářovou strukturu balíčku
Můžete začít vytvořením adresářové struktury, jak je popsáno v části [balení aplikace Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Přidat kód a konfigurační soubory aplikace
Po vytvoření adresářové struktury můžete přidat kód a konfigurační soubory aplikace do adresářů kódu a konfigurace. Můžete také vytvořit další adresáře nebo podadresáře v adresáři Code nebo config.

Service Fabric provede `xcopy` obsahu kořenového adresáře aplikace, takže není k dispozici žádná předdefinovaná struktura, která by používala více než vytváření dvou hlavních adresářů, kódů a nastavení. (Pokud chcete, můžete vybrat jiné názvy. Další podrobnosti najdete v další části.)

> [!NOTE]
> Ujistěte se, že jste zahrnuli všechny soubory a závislosti, které aplikace potřebuje. Service Fabric kopíruje obsah balíčku aplikace na všech uzlech v clusteru, kde se budou nasazovat služby aplikace. Balíček by měl obsahovat veškerý kód, který aplikace potřebuje ke spuštění. Nepředpokládá se, že závislosti jsou již nainstalovány.
>
>

### <a name="edit-the-service-manifest-file"></a>Upravit soubor manifestu služby
Dalším krokem je upravit soubor manifestu služby tak, aby obsahoval následující informace:

* Název typu služby. Toto je ID, které Service Fabric používá k identifikaci služby.
* Příkaz, který se má použít ke spuštění aplikace (ExeHost).
* Libovolný skript, který je třeba spustit pro nastavení aplikace (SetupEntrypoint).

Následuje příklad souboru `ServiceManifest.xml`:

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

Následující oddíly přecházejí do různých částí souboru, které je třeba aktualizovat.

#### <a name="update-servicetypes"></a>Aktualizovat ServiceType
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Můžete si vybrat libovolný název, který chcete `ServiceTypeName`. Hodnota se používá v souboru `ApplicationManifest.xml` k identifikaci služby.
* Zadejte `UseImplicitHost="true"`. Tento atribut oznamuje Service Fabric, že je služba založená na samostatné aplikaci, takže všechny Service Fabric musí udělat, aby je spouštěla jako proces a sledovala svůj stav.

#### <a name="update-codepackage"></a>Aktualizovat CodePackage
Element CodePackage určuje umístění (a verzi) kódu služby.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Element `Name` slouží k zadání názvu adresáře v balíčku aplikace, který obsahuje kód služby. `CodePackage` má také atribut `version`. To lze použít k určení verze kódu a lze jej také použít k upgradu kódu služby pomocí infrastruktury správy životního cyklu aplikací v Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Volitelné: aktualizace SetupEntrypoint
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Element SetupEntryPoint slouží k určení libovolného spustitelného souboru nebo dávkového souboru, který by měl být proveden před spuštěním kódu služby. Je to volitelný krok, takže není nutné ho zahrnovat, pokud není nutná inicializace. SetupEntryPoint se spustí pokaždé, když se služba restartuje.

Existuje pouze jeden SetupEntryPoint, takže instalační skripty musí být seskupeny do jednoho dávkového souboru, pokud instalace aplikace vyžaduje více skriptů. SetupEntryPoint může spustit jakýkoli typ souboru: spustitelné soubory, dávkové soubory a rutiny prostředí PowerShell. Další podrobnosti najdete v tématu [Konfigurace SetupEntryPoint](service-fabric-application-runas-security.md).

V předchozím příkladu SetupEntryPoint spustí dávkový soubor s názvem `LaunchConfig.cmd`, který je umístěn v podadresáři `scripts` adresáře kódu (za předpokladu, že element WorkingFolder je nastaven na základ kódu).

#### <a name="update-entrypoint"></a>Aktualizovat vstupní bod
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Element `EntryPoint` v souboru manifestu služby slouží k určení způsobu spuštění služby.

Element `ExeHost` Určuje spustitelný soubor (a argumenty), které by měly být použity ke spuštění služby. Volitelně můžete přidat atribut `IsExternalExecutable="true"` pro `ExeHost` k označení toho, že program je externí spustitelný soubor mimo balíček kódu. Například, `<ExeHost IsExternalExecutable="true">`.

* `Program` Určuje název spustitelného souboru, který by měl službu spustit.
* `Arguments` Určuje argumenty, které mají být předány do spustitelného souboru. Může se jednat o seznam parametrů s argumenty.
* `WorkingFolder` určuje pracovní adresář pro proces, který bude spuštěn. Můžete zadat tři hodnoty:
  * `CodeBase` určuje, zda bude pracovní adresář nastaven na adresář kódu v balíčku aplikace (`Code` adresář v předchozí struktuře souborů).
  * `CodePackage` určuje, zda bude pracovní adresář nastaven na kořen balíčku aplikace (`GuestService1Pkg` v předchozí struktuře souborů).
    * `Work` určuje, že soubory jsou umístěny v podadresáři s názvem Work.

WorkingFolder je užitečné pro nastavení správného pracovního adresáře, aby bylo možné použít relativní cesty buď v rámci aplikace, nebo inicializačních skriptů.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Aktualizace koncových bodů a registrace u Naming Service pro komunikaci
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
V předchozím příkladu prvek `Endpoint` určuje koncové body, na kterých může aplikace naslouchat. V tomto příkladu aplikace Node. js naslouchá na http na portu 3000.

Kromě toho můžete požádat Service Fabric o publikování tohoto koncového bodu do Naming Service, aby ostatní služby mohly zjistit adresu koncového bodu pro tuto službu. Díky tomu budete moci komunikovat mezi službami, které jsou spustitelnými soubory hosta.
Adresa publikovaného koncového bodu má `UriScheme://IPAddressOrFQDN:Port/PathSuffix`ve formátu. `UriScheme` a `PathSuffix` jsou volitelné atributy. `IPAddressOrFQDN` je IP adresa nebo plně kvalifikovaný název domény uzlu, na který se tento spustitelný soubor umístí, a vypočítává se za vás.

V následujícím příkladu se po nasazení služby v Service Fabric Explorer zobrazí koncový bod podobný `http://10.1.4.92:3000/myapp/` publikované instanci služby. Nebo pokud se jedná o místní počítač, zobrazí se `http://localhost:3000/myapp/`.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
Pomocí těchto adres se [reverzním proxy](service-fabric-reverseproxy.md) můžete komunikovat mezi službami.

### <a name="edit-the-application-manifest-file"></a>Upravit soubor manifestu aplikace
Po nakonfigurování souboru `Servicemanifest.xml` musíte provést nějaké změny v souboru `ApplicationManifest.xml`, aby se zajistilo, že bude použit správný typ služby a název.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
V prvku `ServiceManifestImport` můžete zadat jednu nebo více služeb, které chcete do aplikace zahrnout. Na služby se odkazuje pomocí `ServiceManifestName`, která určuje název adresáře, ve kterém se nachází `ServiceManifest.xml` soubor.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Nastavení protokolování
U spustitelných souborů hosta je užitečné, abyste viděli protokoly konzoly, abyste zjistili, jestli se v aplikacích a konfiguračních skriptech zobrazují nějaké chyby.
Přesměrování konzoly lze nakonfigurovat v souboru `ServiceManifest.xml` pomocí elementu `ConsoleRedirection`.

> [!WARNING]
> Nikdy nepoužívejte zásady přesměrování konzoly v aplikaci, která je nasazená v produkčním prostředí, protože to může mít vliv na převzetí služeb při selhání aplikace. Tato operace se používá *pouze* pro účely místního vývoje a ladění.  
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

`ConsoleRedirection` lze použít k přesměrování výstupu konzoly (stdout i stderr) do pracovního adresáře. Tato možnost umožňuje ověřit, že během instalace nebo provádění aplikace v clusteru Service Fabric nejsou žádné chyby.

`FileRetentionCount` určuje, kolik souborů je uloženo v pracovním adresáři. Hodnota 5 například znamená, že soubory protokolu pro předchozích pět spuštění jsou uloženy v pracovním adresáři.

`FileMaxSizeInKb` určuje maximální velikost souborů protokolu.

Soubory protokolu jsou uloženy v jednom z pracovních adresářů služby. Chcete-li zjistit, kde jsou soubory umístěny, použijte Service Fabric Explorer k určení uzlu, na kterém je služba spuštěna, a používaného pracovního adresáře. Tento postup je popsaný dále v tomto článku.

## <a name="deployment"></a>Nasazení
Posledním krokem je [nasazení aplikace](service-fabric-deploy-remove-applications.md). Následující skript prostředí PowerShell ukazuje, jak nasadit aplikaci do místního vývojového clusteru a spustit novou službu Service Fabric.

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
> [Zkomprimujte balíček](service-fabric-package-apps.md#compress-a-package) před kopírováním do úložiště imagí, pokud je balíček velký nebo obsahuje mnoho souborů. Další informace si můžete přečíst [zde](service-fabric-deploy-remove-applications.md#upload-the-application-package).
>

Službu Service Fabric je možné nasadit v různých "konfiguracích". Může být například nasazena jako jediná nebo více instancí nebo může být nasazena takovým způsobem, že v každém uzlu Service Fabric clusteru existuje jedna instance služby.

Parametr `InstanceCount` `New-ServiceFabricService` rutiny se používá k určení, kolik instancí služby by mělo být spuštěno v clusteru Service Fabric. Můžete nastavit `InstanceCount` hodnotu v závislosti na typu aplikace, kterou nasazujete. Mezi nejběžnější scénáře patří:

* `InstanceCount = "1"`. V tomto případě je v clusteru nasazena pouze jedna instance služby. Plánovač Service Fabric určuje, na kterém uzlu bude služba nasazena.
* `InstanceCount ="-1"`. V takovém případě se jedna instance služby nasadí na každý uzel v clusteru Service Fabric. Výsledek má jednu (a jenom jednu) instanci služby pro každý uzel v clusteru.

Tato konfigurace je užitečná pro aplikace front-end (například koncový bod REST), protože klientské aplikace potřebují připojit k jakémukoli uzlu v clusteru, aby používaly koncový bod. Tato konfigurace se dá použít i v případě, že jsou například všechny uzly Service Fabric clusteru připojené k nástroji pro vyrovnávání zatížení. Provoz klienta je pak možné distribuovat přes službu, která běží na všech uzlech v clusteru.

## <a name="check-your-running-application"></a>Kontrolovat spuštěnou aplikaci
V Service Fabric Explorer určete uzel, ve kterém je služba spuštěná. V tomto příkladu se spouští na Uzel1:

![Uzel, ve kterém je spuštěná služba](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Pokud přejdete na uzel a přejdete na aplikaci, zobrazí se základní informace o uzlu včetně umístění na disku.

![Umístění na disku](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Pokud přejdete do adresáře pomocí Průzkumník serveru, můžete najít pracovní adresář a složku protokolu služby, jak je znázorněno na následujícím snímku obrazovky: 

![Umístění protokolu](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Další kroky
V tomto článku jste se naučili, jak zabalit spustitelný soubor hosta a nasadit ho do Service Fabric. Související informace a úlohy najdete v následujících článcích.

* [Ukázka balení a nasazení spustitelného souboru hosta](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), včetně odkazu na předběžnou verzi nástroje pro balení
* [Ukázka dvou spustitelných souborů hostaC# (a NodeJS), které komunikují přes službu pojmenování pomocí REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Nasazení několika hostujících spustitelných souborů](service-fabric-deploy-multiple-apps.md)
* [Vytvoření první aplikace Service Fabric pomocí sady Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
