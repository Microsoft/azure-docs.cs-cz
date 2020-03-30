---
title: Balíček aplikace Azure Service Fabric
description: Přečtěte si o balení aplikace Azure Service Fabric a jak se připravit na nasazení do clusteru.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 7c99eec28ac06ecf666d6dda1015f889841a5dbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258340"
---
# <a name="package-an-application"></a>Balení aplikace

Tento článek popisuje, jak zabalit aplikaci Service Fabric a připravit ji k nasazení.

## <a name="package-layout"></a>Rozložení balíčku

Manifest aplikace, jeden nebo více manifestů služby a další soubory nezbytných balíčků musí být uspořádány v určitém rozložení pro nasazení do clusteru Service Fabric. Příklad manifestů v tomto článku by musel být uspořádán v následující adresářové struktuře:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

Složky jsou **pojmenovány** tak, aby odpovídaly name atributy každého odpovídajícího prvku. Například pokud manifest služby obsahuje dva balíčky kódu s názvy **MyCodeA** a **MyCodeB**, pak dvě složky se stejnými názvy bude obsahovat potřebné binární soubory pro každý balíček kódu.

## <a name="use-setupentrypoint"></a>Použít instalační program EntryPoint

Typické scénáře pro použití **SetupEntryPoint** jsou v případě, že potřebujete spustit spustitelný soubor před spuštěním služby nebo potřebujete provést operaci se zvýšenými oprávněními. Například:

* Nastavení a inicializace proměnných prostředí, které potřebuje spustitelný soubor služby. Není omezena pouze na spustitelné soubory napsané prostřednictvím programovacích modelů Service Fabric. Například npm.exe potřebuje některé proměnné prostředí nakonfigurované pro nasazení aplikace node.js.
* Nastavení řízení přístupu instalací certifikátů zabezpečení.

Další informace o konfiguraci **instalačního programu SetupEntryPoint**naleznete [v tématu Konfigurace zásad pro vstupní bod nastavení služby](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurace

### <a name="build-a-package-by-using-visual-studio"></a>Vytvoření balíčku pomocí sady Visual Studio

Pokud jste k vytvoření aplikace použili Visual Studio, můžete pomocí příkazu *Package* automaticky vytvořit balíček, který odpovídá výše popsanému rozložení.

Chcete-li vytvořit balíček, klepněte pravým tlačítkem myši na projekt aplikace v *Průzkumníku řešení* a zvolte příkaz **Balíček:**

![Balení aplikace pomocí sady Visual Studio][vs-package-command]

Po dokončení balení můžete najít umístění balíčku v okně **Výstup.** Ke kroku balení dojde automaticky při nasazení nebo ladění aplikace v sadě Visual Studio.

### <a name="build-a-package-by-command-line"></a>Vytvoření balíčku pomocí příkazového řádku

Je také možné programově zabalit do `msbuild.exe`aplikace pomocí . Pod kapotou visual studio běží, takže výstup je stejný.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Otestujte balíček

Strukturu balíčku můžete ověřit místně prostřednictvím prostředí PowerShell pomocí příkazu [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)
Tento příkaz kontroluje problémy analýzy manifestu a ověřuje všechny odkazy. Tento příkaz pouze ověřuje strukturální správnost adresářů a souborů v balíčku.
Neověřuje žádný obsah kódu nebo datového balíčku kromě kontroly, zda jsou k dispozici všechny potřebné soubory.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Tato chyba ukazuje, že v balíčku kódu chybí soubor *MySetup.bat* odkazovaný v manifestu služby **SetupEntryPoint.** Po přidání chybějícího souboru projde ověření aplikace:

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
True
```

Pokud má vaše aplikace definované [parametry aplikace,](service-fabric-manage-multiple-environment-app-configuration.md) můžete je předat v [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) pro správné ověření.

Pokud znáte cluster, kde bude aplikace nasazena, doporučujeme `ImageStoreConnectionString` předat parametr. V tomto případě je balíček také ověřen proti předchozí verze aplikace, které jsou již spuštěny v clusteru. Ověření může například zjistit, zda byl balíček se stejnou verzí, ale jiný obsah již nasazen.  

Jakmile je aplikace správně zabalena a předá ověření, zvažte kompresi balíčku pro rychlejší operace nasazení.

## <a name="compress-a-package"></a>Komprese balíčku

Pokud je balíček velký nebo obsahuje mnoho souborů, můžete jej komprimovat pro rychlejší nasazení. Komprese snižuje počet souborů a velikost balíčku.
U komprimovaného balíčku aplikace [může nahrávání balíčku aplikace](service-fabric-deploy-remove-applications.md#upload-the-application-package) trvat déle ve srovnání s nahráním nekomprimovaného balíčku, zejména pokud se komprese provádí jako součást kopie. S [kompresí, registrace](service-fabric-deploy-remove-applications.md#register-the-application-package) a [zrušení registrace typu aplikace](service-fabric-deploy-remove-applications.md#unregister-an-application-type) jsou rychlejší.

Mechanismus nasazení je stejný pro komprimované a nekomprimované balíčky. Pokud je balíček komprimován, je uložen jako takový v úložišti bitových obrázků clusteru a je nekomprimovaný na uzlu před spuštěním aplikace.
Komprese nahradí platný balíček Service Fabric komprimovocí. Složka musí povolit oprávnění k zápisu. Spuštění komprese na již komprimovaném balíčku nepřináší žádné změny.

Balíček můžete komprimovat spuštěním příkazu Powershell `CompressPackage` [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) s přepínačem. Balíček můžete rozbalit stejným příkazem `UncompressPackage` pomocí přepínače.

Následující příkaz komprimuje balíček bez kopírování do úložiště obrázků. Komprimovaný balíček můžete podle potřeby zkopírovat do jednoho nebo více clusterů Service `SkipCopy` Fabric pomocí [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) bez příznaku.
Balíček nyní obsahuje zip `code`soubory `config`pro `data` , a balíčky. Manifest aplikace a manifesty služby nejsou zip, protože jsou potřebné pro mnoho vnitřních operací. Například sdílení balíčků, název typu aplikace a extrakce verze pro určitá ověření, všechny potřebují přístup k manifestům. Zipování manifesty by tyto operace neefektivní.

```
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -CompressPackage -SkipCopy
tree /f .\MyApplicationType
```

```Output
D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
       ServiceManifest.xml
       MyCode.zip
       MyConfig.zip
       MyData.zip

```

Alternativně můžete komprimovat a zkopírovat balíček s [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) v jednom kroku.
Pokud je balíček velký, zadejte dostatečně vysoký časový čas, aby čas pro kompresi balíčku a nahrávání do clusteru.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Interně Service Fabric vypočítá kontrolní součty pro balíčky aplikací pro ověření. Při použití komprese jsou kontrolní součty vypočítány na zip verze každého balíčku. Generování nového zip ze stejného balíčku aplikace vytvoří různé kontrolní součty. Chcete-li zabránit chybám ověření, použijte [zřizování rozdílů](service-fabric-application-upgrade-advanced.md). S touto volbou nezahrnejte nezměněné balíčky v nové verzi. Místo toho na ně odkazovat přímo z nové služby manifestu.

Pokud rozdíl zřizování není možnost a je nutné zahrnout `code`balíčky, generovat nové verze pro , `config`a `data` balíčky, aby se zabránilo neshody kontrolního součtu. Generování nových verzí pro nezměněné balíčky je nezbytné při použití komprimovaného balíčku, bez ohledu na to, zda předchozí verze používá kompresi či nikoli.

Balíček je nyní správně zabalen, ověřen a komprimován (v případě potřeby), takže je připraven k [nasazení](service-fabric-deploy-remove-applications.md) do jednoho nebo více clusterů Service Fabric.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Komprese balíčků při nasazování pomocí sady Visual Studio

Můžete dát aplikaci Visual Studio pokyn ke `CopyPackageParameters` kompresi balíčků při `CompressPackage` nasazení `true`přidáním prvku do profilu publikování a nastavením atributu na .

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Vytvoření sfpkg

Počínaje verzí 6.1 service fabric umožňuje zřizování z externího úložiště.
S touto volbou balíček aplikace není nutné zkopírovat do úložiště obrázků. Místo toho můžete `sfpkg` vytvořit a nahrát do externího úložiště a při zřizování pak poskytnout identifikátor URI pro stahování službě Service Fabric. Stejný balíček lze zřídit do více clusterů. Zřizování z externího úložiště šetří čas potřebný ke zkopírování balíčku do každého clusteru.

Soubor `sfpkg` je zip, který obsahuje počáteční balíček aplikace a má příponu ".sfpkg".
Uvnitř zipu může být balíček aplikace komprimován nebo nekomprimován. Komprese balíčku aplikace uvnitř zip se provádí na úrovni kódu, konfigurace a datového balíčku, jak [již bylo zmíněno dříve](service-fabric-package-apps.md#compress-a-package).

Chcete-li `sfpkg`vytvořit , začněte se složkou, která obsahuje původní balíček aplikace, komprimovaný nebo ne. Potom použijte jakýkoli nástroj pro zip složku s příponou ".sfpkg". Použijte například [Soubor ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

Musí `sfpkg` být odeslána do externího úložiště mimo pásmo, mimo Service Fabric. Externí úložiště může být libovolné úložiště, které zveřejňuje koncový bod REST http nebo https. Během zřizování Service Fabric provede operaci `sfpkg` GET ke stažení balíčku aplikace, takže úložiště musí povolit přístup ke čtení pro balíček.

Chcete-li zřídit balíček, použijte externí ustanovení, které vyžaduje informace o použití identifikátoru URI pro stahování a typu aplikace.

>[!NOTE]
> Zřizování na základě relativní cesty úložiště `sfpkg` bitové kopie aktuálně nepodporuje soubory. Proto `sfpkg` by neměly být zkopírovány do úložiště obrázků.

## <a name="next-steps"></a>Další kroky

[Nasazení a odebrání aplikací][10] popisuje, jak spravovat instance aplikací pomocí PowerShellu.

[Správa parametrů aplikace pro více prostředí][11] popisuje, jak konfigurovat parametry a proměnné prostředí pro různé instance aplikace.

[Konfigurace zásad zabezpečení pro vaši aplikaci][12] popisuje, jak spustit služby v rámci zásad zabezpečení k omezení přístupu.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md