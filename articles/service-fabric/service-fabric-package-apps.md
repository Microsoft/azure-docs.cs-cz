---
title: Balíček služby Azure Service Fabric app | Dokumentace Microsoftu
description: Popisuje, jak zabalit aplikace před nasazením do clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: b8e66a9d5bba0c48f15b1ccd3f2d47e5405db792
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785594"
---
# <a name="package-an-application"></a>Balení aplikace

Tento článek popisuje, jak balíček aplikace Service Fabric a nastavte ji jako připravené na nasazení.

## <a name="package-layout"></a>Rozložení balíčku

Manifest aplikace, jeden nebo více manifestů služby a další potřebný balíček soubory musí být uspořádány v konkrétním rozložení pro nasazení do clusteru Service Fabric. Manifesty příklad v tomto článku by bylo potřeba být uspořádány do následující adresářovou strukturu:

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

Složky jsou pojmenovány tak, aby odpovídaly **název** atributy každý odpovídající prvek. Například, pokud manifest služby obsaženy dva balíčky kódu s názvy **MyCodeA** a **MyCodeB**, pak dvě složky se stejnými názvy by obsahoval nezbytné binární soubory pro každý balíček kódu.

## <a name="use-setupentrypoint"></a>Použití SetupEntryPoint

Typické scénáře použití **SetupEntryPoint** kdy je potřeba spustit spustitelný soubor před spuštěním služby nebo budete muset provést operaci se zvýšenými oprávněními. Příklad:

* Nastavení a inicializace proměnných prostředí, které musí spustitelný soubor služby. Není omezena pouze spustitelné programy napsané pomocí programovacích modelů Service Fabric. Například npm.exe potřebuje některé proměnné prostředí nakonfigurované pro nasazení aplikace node.js.
* Nastavení řízení přístupu při instalaci certifikátů zabezpečení.

Další informace o tom, jak nakonfigurovat **SetupEntryPoint**, naleznete v tématu [nakonfigurovat zásady pro vstupního bodu nastavení služby](service-fabric-application-runas-security.md)

<a id="Package-App"></a>

## <a name="configure"></a>Konfigurace

### <a name="build-a-package-by-using-visual-studio"></a>Vytvořit balíček pomocí sady Visual Studio

Pokud použijete k vytvoření aplikace Visual Studio 2015, můžete použít příkaz balíček automaticky vytvořit balíček, který odpovídá rozložení je popsáno výše.

Vytvoření balíčku, klikněte pravým tlačítkem na projekt aplikace v Průzkumníku řešení a zvolte příkaz balíček, jak je znázorněno níže:

![Balení aplikace pomocí sady Visual Studio][vs-package-command]

Po dokončení vytváření balíčků můžete najít umístění balíčku v **výstup** okna. Balení kroku dojde automaticky při nasazení nebo ladění aplikace v sadě Visual Studio.

### <a name="build-a-package-by-command-line"></a>Vytvořit balíček pomocí příkazového řádku

Je také možné prostřednictvím kódu programu vytvořit aplikaci pomocí balíček `msbuild.exe`. Pod pokličkou Visual Studio běží, takže výstup je stejný.

```shell
D:\Temp> msbuild HelloWorld.sfproj /t:Package
```

## <a name="test-the-package"></a>Otestovat balíček

Struktura balíčku místně prostřednictvím prostředí PowerShell můžete ověřit pomocí [testovací ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) příkazu.
Tento příkaz vyhledá manifestu analýze problémy a ověřte všechny odkazy. Tento příkaz pouze ověří správnost strukturální adresářů a souborů v balíčku.
Neověřuje jakýkoli obsah balíčku kódu nebo data nad rámec kontroluje se, že jsou k dispozici všechny potřebné soubory.

```powershell
Test-ServiceFabricApplicationPackage .\MyApplicationType
```

```Output
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
```

Tato chyba zobrazuje, který *MySetup.bat* soubor odkazovaný v manifestu služby **SetupEntryPoint** chybí balíček kódu. Po přidání souboru chybí předá ověření aplikací:

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

Pokud má vaše aplikace [parametry aplikace](service-fabric-manage-multiple-environment-app-configuration.md) definována, můžete předat je do [testovací ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) pro správné ověření.

Pokud víte, kam se aplikace nasadí cluster, je doporučeno předat v `ImageStoreConnectionString` parametru. V takovém případě balíček je také ověřován proti předchozí verze této aplikace, které jsou již spuštěny v clusteru. Například ověření může zjistit, jestli balíček se stejnou verzí, ale jiným obsahem již byl nasazen.  

Jakmile aplikace je správně zabalen a předává ověření, vezměte v úvahu komprese balíček pro rychlejší operací nasazení.

## <a name="compress-a-package"></a>Komprimovat balíček

Když balíček je velký nebo má mnoho souborů, můžete je Komprimovat pro rychlejší nasazení. Komprese snižuje počet souborů a velikost balíčku.
Pro aplikace komprimovaný balíček [nahrávání balíčku aplikace](service-fabric-deploy-remove-applications.md#upload-the-application-package) trvat delší dobu, ve srovnání s nahrává se balíček nekomprimované, zejména v případě, že komprese se provádí jako součást kopírování. Komprese [registrace](service-fabric-deploy-remove-applications.md#register-the-application-package) a [rušení registrace typu aplikace](service-fabric-deploy-remove-applications.md#unregister-an-application-type) jsou rychlejší.

Mechanismus pro nasazení je stejný pro komprimované a nekomprimované balíčky. Pokud je komprimovaný balíček, uloží se v důsledku do úložiště imagí clusteru a je nekomprimovaný na uzlu před spuštěním aplikace.
Komprese nahradí platný balíček Service Fabric komprimované verze. Složku musíte povolit oprávnění k zápisu. Balíček už jsou komprimované a systémem komprese vrací žádné změny.

Spuštěním příkazu Powershellu můžete komprimovat balíček [kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) s `CompressPackage` přepnout. Dekomprimovat balíček se stejným příkaz a `UncompressPackage` přepnout.

Následující příkaz komprimuje balíček bez kopírování do úložiště imagí. Komprimovaný balíček můžete zkopírovat do jedné nebo víc clusterů Service Fabric, podle potřeby, pomocí [kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) bez `SkipCopy` příznak.
Balíček nyní obsahuje soubory ZIP `code`, `config`, a `data` balíčky. Manifesty služby a manifest aplikace nejsou ZIP, protože jsou potřeba pro mnoho interní operace. Třeba balíček pro sdílení obsahu, aplikace zadejte název a verzi extrakce pro určité ověření všechny potřebné pro přístup k manifestů. Zipování manifesty s žádným tyto operace neefektivní.

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

Můžete také zkomprimovat a zkopírujte balíček s [kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) v jednom kroku.
Pokud je balíček rozsáhlý, zadejte na nějakou dobu počkat, komprimovat balíček a odeslat do clusteru dostatečně vysoká, vypršel časový limit.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath .\MyApplicationType -ApplicationPackagePathInImageStore MyApplicationType -ImageStoreConnectionString fabric:ImageStore -CompressPackage -TimeoutSec 5400
```

Service Fabric, interně vypočítá kontrolních součtů pro balíčky aplikací pro ověření. Při použití komprese, kontrolní součty se vypočítávají na ZIP verze každého balíčku. Generování nové zip z pokaždé tentýž balíček aplikace vytváří různé kontrolní součty. Chcete-li zabránit chybám ověření, použijte [diff zřizování](service-fabric-application-upgrade-advanced.md). Pomocí této možnosti nezahrnují beze změny balíčky v nové verzi. Místo toho na ně odkazujte přímo z nové manifestu služby.

Pokud zřizování změn není možné zvolit a musí obsahovat balíčky, generovat nové verze pro `code`, `config`, a `data` balíčků, aby se zabránilo neshoda kontrolního součtu. Generování nové verze pro balíčky beze změny je potřeba, pokud zkomprimovaného balíčku se používá, bez ohledu na to, zda předchozí verze používá komprese nebo ne.

Balíček je nyní správně zabaleny, ověřit a komprimované (v případě potřeby), tak, aby byl připravený k [nasazení](service-fabric-deploy-remove-applications.md) do jednoho nebo více clusterů Service Fabric.

### <a name="compress-packages-when-deploying-using-visual-studio"></a>Komprese balíčky při nasazení pomocí sady Visual Studio

Můžete dát pokyn, Visual Studio ke zkomprimování balíčky nasazení, tak, že přidáte `CopyPackageParameters` element do profilu publikování a nastavte `CompressPackage` atribut `true`.

``` xml
    <PublishProfile xmlns="http://schemas.microsoft.com/2015/05/fabrictools">
        <ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com" />
        <ApplicationParameterFile Path="..\ApplicationParameters\Cloud.xml" />
        <CopyPackageParameters CompressPackage="true"/>
    </PublishProfile>
```

## <a name="create-an-sfpkg"></a>Vytvoření sfpkg

Od verze 6.1, Service Fabric umožňuje zřizovat z externího úložiště.
Pomocí této možnosti nemá balíček aplikace chcete zkopírovat do úložiště imagí. Místo toho můžete vytvořit `sfpkg` a nahrajte ho do externího úložiště a potom zadejte identifikátor URI stažení do Service Fabric při zřizování. Pro několik clusterů se dá zřídit stejného balíčku. Zřizování z externího úložiště šetří čas potřebný ke zkopírování balíčku do každého clusteru.

`sfpkg` Je soubor zip, který obsahuje balíček počáteční aplikace a má příponu ".sfpkg".
V souboru zip lze komprimované nebo nekomprimovaný balíčku aplikace. Komprese balíčku aplikace v souboru zip se provádí v kódu, konfigurace a data balíčku úrovně, jako [již bylo zmíněno dříve](service-fabric-package-apps.md#compress-a-package).

Vytvoření `sfpkg`, začínat složku, která obsahuje původní balíček aplikace, nebo není komprimovaná. Potom použijte libovolný nástroj se komprimovat složku s příponou ".sfpkg". Například použít [ZipFile.CreateFromDirectory](https://msdn.microsoft.com/library/hh485721(v=vs.110).aspx).

```csharp
ZipFile.CreateFromDirectory(appPackageDirectoryPath, sfpkgFilePath);
```

`sfpkg` Musí být odeslán do externího úložiště mimo pásmo, mimo Service Fabric. Externí úložiště může být jakékoli úložiště, který zpřístupňuje koncový bod REST http nebo https. Během zřizování, Service Fabric provádí operaci GET ke stažení `sfpkg` balíčku aplikace, takže úložišti musí umožňovat přístup pro čtení pro balíček.

K zajištění balíčku, použijte externí zřizovat, která vyžaduje stažení identifikátor URI a informace o typu aplikace.

>[!NOTE]
> Zřizování na základě image store relativní cesty v současné době nepodporuje `sfpkg` soubory. Proto `sfpkg` neměla kopírovat do úložiště imagí.

## <a name="next-steps"></a>Další postup

[Nasazení a odebírat aplikace] [ 10] popisuje použití Powershellu ke správě instance aplikace

[Správa parametrů aplikací pro víc prostředí] [ 11] popisuje, jak nakonfigurovat parametry a proměnné prostředí pro různé instance aplikace.

[Konfigurovat zásady zabezpečení pro vaši aplikaci] [ 12] popisuje, jak ke spouštění služeb v části zásady zabezpečení pro omezení přístupu.

<!--Image references-->
[vs-package-command]: ./media/service-fabric-package-apps/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md