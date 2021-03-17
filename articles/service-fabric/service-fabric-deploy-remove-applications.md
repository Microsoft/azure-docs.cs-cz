---
title: Nasazení Azure Service Fabric s využitím PowerShellu
description: Přečtěte si o odebrání a nasazení aplikací v Azure Service Fabric a o tom, jak provádět tyto akce v prostředí PowerShell.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: d428a51c0bc224ca8706403ae176d46f1db82a32
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785443"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Nasazení a odebrání aplikací pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [Rozhraní API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Jakmile je [Typ aplikace zabalený][10], je připravený k nasazení do clusteru Azure Service Fabric. Nasazení zahrnuje následující tři kroky:

1. Nahrajte balíček aplikace do úložiště imagí.
2. Zaregistrujte typ aplikace s relativní cestou k úložišti imagí.
3. Vytvořte instanci aplikace.

Jakmile již není nasazená aplikace požadována, můžete instanci aplikace a její typ aplikace odstranit. Chcete-li zcela odebrat aplikaci z clusteru, postupujte následovně:

1. Odeberte (nebo odstraňte) spuštěnou instanci aplikace.
2. Zrušte registraci typu aplikace, pokud ji už nepotřebujete.
3. Odeberte balíček aplikace z úložiště imagí.

Pokud používáte Visual Studio pro nasazení a ladění aplikací v místním vývojovém clusteru, všechny předchozí kroky se zpracují automaticky prostřednictvím skriptu PowerShellu.  Tento skript se nachází ve složce *Scripts* projektu aplikace. Tento článek poskytuje základní informace o tom, co skript dělá, abyste mohli provádět stejné operace mimo sadu Visual Studio. 

Dalším způsobem, jak nasadit aplikaci, je použití externího zřizování. Balíček aplikace může být [zabalený jako `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) a nahraný do externího úložiště. V takovém případě není nutné nahrávat do úložiště imagí. Nasazení vyžaduje následující kroky:

1. Nahrajte `sfpkg` do externího úložiště. Externím úložištěm může být jakékoli úložiště, které zveřejňuje koncový bod HTTP nebo HTTPS s REST.
2. Zaregistrujte typ aplikace pomocí externího identifikátoru URI pro stažení a informací o typu aplikace.
2. Vytvořte instanci aplikace.

Pro vyčištění odeberte instance aplikace a zrušte registraci typu aplikace. Vzhledem k tomu, že balíček nebyl zkopírován do úložiště imagí, neexistuje dočasné umístění pro vyčištění. Zřizování z externího úložiště je dostupné od verze 6,1 Service Fabric.

>[!NOTE]
> Visual Studio v současné době nepodporuje externí zřizování.

 

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Před spuštěním příkazů PowerShellu v tomto článku se pomocí příkazu [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) připojte ke clusteru Service Fabric. Pokud se chcete připojit k místnímu vývojovému clusteru, spusťte následující příkaz:

```powershell
Connect-ServiceFabricCluster
```

Příklady připojení ke vzdálenému clusteru nebo clusteru zabezpečeného pomocí Azure Active Directory, certifikátů x509 nebo Windows Active Directory najdete v tématu [připojení k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Nahrání balíčku aplikace

Nahráním balíčku aplikace se umístí do umístění, které je přístupné pro interní Service Fabric komponenty.
Pokud chcete balíček aplikace ověřit lokálně, použijte rutinu [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .

Příkaz [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) nahraje balíček aplikace do úložiště imagí clusteru.

Předpokládejme, že sestavíte a zabalíte aplikaci s názvem *MyApplication* v aplikaci Visual Studio 2015. Ve výchozím nastavení je název typu aplikace uvedený v ApplicationManifest.xml "MyApplicationType".  Balíček aplikace, který obsahuje nezbytný manifest aplikace, manifesty služeb a balíčky Code/config/data, se nachází v části *C:\Users \<username\> \Documents\Visual Studio 2015 \ Projects\MyApplication\MyApplication\pkg\Debug*. 

Následující příkaz vypíše obsah balíčku aplikace:

```powershell
$path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
│   ApplicationManifest.xml
│
└───Stateless1Pkg
    │   ServiceManifest.xml
    │
    ├───Code
    │       Microsoft.ServiceFabric.Data.dll
    │       Microsoft.ServiceFabric.Data.Interfaces.dll
    │       Microsoft.ServiceFabric.Internal.dll
    │       Microsoft.ServiceFabric.Internal.Strings.dll
    │       Microsoft.ServiceFabric.Services.dll
    │       ServiceFabricServiceModel.dll
    │       Stateless1.exe
    │       Stateless1.exe.config
    │       Stateless1.pdb
    │       System.Fabric.dll
    │       System.Fabric.Strings.dll
    │
    └───Config
            Settings.xml
```

Pokud je balíček aplikace velký nebo má mnoho souborů, můžete [ho zkomprimovat](service-fabric-package-apps.md#compress-a-package). Komprese zmenšuje velikost a počet souborů.
Výsledkem je rychlejší registrace a zrušení registrace typu aplikace. Čas nahrávání může být v současné době pomalejší, zejména pokud zadáte čas komprimace balíčku. 

Pro komprimaci balíčku použijte stejný příkaz [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) . Kompresi lze provést odděleně od nahrávání pomocí `SkipCopy` příznaku nebo spolu s operací nahrávání. Použití komprese u komprimovaného balíčku je no-op.
K dekomprimaci komprimovaného balíčku použijte stejný příkaz [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) s `UncompressPackage` přepínačem.

Následující rutina zkomprimuje balíček bez jeho zkopírování do úložiště imagí. Balíček teď obsahuje soubory zip pro `Code` `Config` balíčky a. Aplikace a manifesty služby nevyužívají příkaz zip, protože jsou nutné pro mnoho interních operací (například sdílení balíčků, název typu aplikace a extrakci verzí pro určitá ověření). Zipování manifesty by tyto operace neefektivně vedly.

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
tree /f $path
```

```Output
Folder PATH listing for volume OSDisk
Volume serial number is 0459-2393
C:\USERS\USER\DOCUMENTS\VISUAL STUDIO 2015\PROJECTS\MYAPPLICATION\MYAPPLICATION\PKG\DEBUG
|   ApplicationManifest.xml
|
└───Stateless1Pkg
       Code.zip
       Config.zip
       ServiceManifest.xml
```

U rozsáhlých balíčků aplikací komprese trvá déle. Nejlepších výsledků dosáhnete, když použijete rychlou jednotku SSD. Doba komprimace a velikost komprimovaného balíčku se také liší v závislosti na obsahu balíčku.
Zde je například statistika komprese pro některé balíčky, které ukazují počáteční a komprimovanou velikost balíčku s časem komprese.

|Počáteční velikost (MB)|Počet souborů|Čas komprese|Velikost komprimovaného balíčku (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2 048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Jakmile je balíček komprimovaný, dá se v případě potřeby nahrát do jednoho nebo víc Service Fabric clusterů. Mechanismus nasazení je stejný pro komprimované a nekomprimované balíčky. Komprimované balíčky se ukládají jako v úložišti imagí clusteru. Před spuštěním aplikace jsou balíčky na uzlu nekomprimovány.


Následující příklad nahraje balíček do úložiště imagí do složky s názvem "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Pokud nezadáte parametr *-ApplicationPackagePathInImageStore* , balíček aplikace se zkopíruje do složky ladění v úložišti imagí.

>[!NOTE]
>Rutina **copy-ServiceFabricApplicationPackage** automaticky rozpozná vhodný připojovací řetězec úložiště imagí, pokud je relace PowerShellu připojená k Service Fabricmu clusteru. U Service Fabric verzí starších než 5,6 je nutné explicitně zadat argument **-ImageStoreConnectionString** .
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>K získání připojovacího řetězce úložiště imagí slouží rutina **Get-ImageStoreConnectionStringFromClusterManifest** , která je součástí modulu Service Fabric SDK PowerShellu.  Chcete-li importovat modul SDK, spusťte příkaz:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Další informace o úložišti imagí a připojovacím řetězci pro úložiště imagí najdete v tématu [vysvětlení připojovacího řetězce úložiště imagí](service-fabric-image-store-connection-string.md) .
>
>
>

Čas potřebný k nahrání balíčku se liší v závislosti na několika faktorech. Některé z těchto faktorů jsou počet souborů v balíčku, velikost balíčku a velikosti souborů. Rychlost při nahrávání ovlivňuje rychlost sítě mezi zdrojovým a Service Fabric clusterem. Výchozí časový limit pro [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) je 30 minut.
V závislosti na popsaných faktorech může být nutné tento časový limit prodloužit. Pokud komprimujete balíček ve volání kopírování, je nutné také zvážit dobu komprimace.



## <a name="register-the-application-package"></a>Registrace balíčku aplikace

Typ a verze aplikace deklarované v manifestu aplikace budou k dispozici pro použití při registraci balíčku aplikace. Systém přečte balíček nahraný v předchozím kroku, ověří balíček, zpracuje obsah balíčku a zkopíruje zpracovaný balíček do interního systémového umístění.  

Spusťte rutinu [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) , která zaregistruje typ aplikace v clusteru a zpřístupní ji k nasazení:

### <a name="register-the-application-package-copied-to-image-store"></a>Zaregistrovat balíček aplikace zkopírovaný do úložiště imagí

Pokud byl balíček dříve zkopírován do úložiště imagí, operace registrace Určuje relativní cestu v úložišti imagí.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" je složka v úložišti imagí, kde se nachází balíček aplikace. Typ aplikace s názvem "MyApplicationType" a verze "1.0.0" (oba jsou v manifestu aplikace nalezeny) je nyní registrován v clusteru.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Zaregistrovat balíček aplikace zkopírovaný do externího úložiště

Od verze Service Fabric 6,1 poskytuje zřízení podporu ke stažení balíčku z externího úložiště. Identifikátor URI pro stahování představuje cestu k [ `sfpkg` balíčku aplikace](service-fabric-package-apps.md#create-an-sfpkg) , ze které lze balíček aplikace stáhnout pomocí protokolů HTTP nebo HTTPS. Balíček musí být dříve nahrán do tohoto externího umístění. Identifikátor URI musí umožnit přístup pro čtení, aby Service Fabric mohl soubor stáhnout. `sfpkg`Soubor musí mít příponu ". sfpkg". Operace zřizování by měla zahrnovat informace o typu aplikace, jak se nachází v manifestu aplikace.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

Příkaz [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) se vrátí až po úspěšném zaregistrování balíčku aplikace systémem. Jak dlouho trvá registrace závisí na velikosti a obsahu balíčku aplikace. V případě potřeby lze použít parametr **-TimeoutSec** k poskytnutí delšího časového limitu (výchozí časový limit je 60 sekund).

Pokud máte velký balíček aplikace nebo máte vypršení časových limitů, použijte parametr **-Async** . Příkaz vrátí, když cluster přijme příkaz Register. Operace Register v případě potřeby pokračuje.
Příkaz [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) vypíše verze typu aplikace a jejich stav registrace. Pomocí tohoto příkazu můžete určit, kdy se má registrace provést.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Odebrání balíčku aplikace z úložiště imagí

Pokud byl balíček zkopírován do úložiště imagí, měli byste jej po úspěšné registraci aplikace odebrat z dočasného umístění. Odstranění balíčků aplikace z úložiště imagí uvolní systémové prostředky. Udržování nepoužívaných balíčků aplikací spotřebovává diskové úložiště a vede k problémům s výkonem aplikací.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Vytvoření aplikace

Pomocí rutiny [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication) můžete vytvořit instanci aplikace z jakékoli verze typu aplikace, která byla úspěšně zaregistrována. Název každé aplikace musí začínat schématem *"Fabric:"* a musí být jedinečný pro každou instanci aplikace. Vytvoří se také všechny výchozí služby definované v manifestu aplikace typu cílové aplikace.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Pro libovolnou danou verzi registrovaného typu aplikace lze vytvořit více instancí aplikace. Každá instance aplikace se spouští izolovaně s vlastním pracovním adresářem a procesem.

Pokud chcete zjistit, které pojmenované aplikace a služby běží v clusteru, spusťte rutiny [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) a [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice) :

```powershell
Get-ServiceFabricApplication  
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}
```

```powershell
Get-ServiceFabricApplication | Get-ServiceFabricService
```

```Output
ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Odebrání aplikace

Pokud už instanci aplikace nepotřebujete, můžete ji trvale odebrat pomocí rutiny [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) . [Příkaz Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication) automaticky odebere všechny služby, které patří do aplikace, a trvale odebere celý stav služby. 

> [!WARNING]
> Tuto operaci nelze vrátit zpět a stav aplikace nelze obnovit.

```powershell
Remove-ServiceFabricApplication fabric:/MyApp
```

```Output
Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded
```

```powershell
Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Zrušení registrace typu aplikace

Pokud již určitou verzi typu aplikace nepotřebujete, měli byste zrušit registraci typu aplikace pomocí rutiny [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) . Zrušení registrace nepoužívaných typů aplikací uvolní prostor úložiště používaný úložištěm imagí odebráním souborů typu aplikace. Zrušení registrace typu aplikace neodebere balíček aplikace zkopírovaný do dočasného umístění úložiště imagí, pokud bylo použito kopírování do úložiště imagí. Je možné zrušit registraci typu aplikace, pokud se na ni nevytváří instance žádné aplikace a na ni neodkazují žádné probíhající upgrady aplikací.

Spuštěním rutiny [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) zobrazíte typy aplikací, které jsou aktuálně zaregistrované v clusteru:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Chcete-li zrušit registraci konkrétního typu aplikace, spusťte příkaz [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) :

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Odstraňování potíží

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage požádá o ImageStoreConnectionString

Prostředí Service Fabric SDK by již mělo mít nastavené správné výchozí hodnoty. V případě potřeby by ale ImageStoreConnectionString pro všechny příkazy měly odpovídat hodnotě, kterou používá Cluster Service Fabric. ImageStoreConnectionString můžete najít v manifestu clusteru, který jste získali pomocí příkazů [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) a Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

K získání připojovacího řetězce úložiště imagí slouží rutina **Get-ImageStoreConnectionStringFromClusterManifest** , která je součástí modulu Service Fabric SDK PowerShellu.  Chcete-li importovat modul SDK, spusťte příkaz:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString se nachází v manifestu clusteru:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Další informace o úložišti imagí a připojovacím řetězci pro úložiště imagí najdete v tématu [vysvětlení připojovacího řetězce úložiště imagí](service-fabric-image-store-connection-string.md) .

### <a name="deploy-large-application-package"></a>Nasadit balíček velkých aplikací

Problém: [ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) vyprší doba kopírování pro velký balíček aplikace (pořadí GB).
Vyzkoušejte:
- Zadejte větší časový limit pro příkaz [copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) s `TimeoutSec` parametrem. Ve výchozím nastavení je časový limit 30 minut.
- Ověřte síťové připojení mezi zdrojovým počítačem a clusterem. Pokud je připojení pomalé, zvažte použití počítače s lepším připojením k síti.
Pokud je klientský počítač v jiné oblasti než cluster, zvažte použití klientského počítače v bližší nebo stejné oblasti jako cluster.
- Ověřte, jestli nepoužíváte externí omezení. Pokud je třeba úložiště imagí nakonfigurované tak, aby používalo úložiště Azure, může být nahrávání omezené.

Problém: nahrání balíčku se úspěšně dokončilo, ale vypršel časový limit [pro ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) . Zkuste
- Před kopírováním do úložiště imagí [balíček Zkomprimujte](service-fabric-package-apps.md#compress-a-package) .
Komprese zmenšuje velikost a počet souborů. tím se snižuje objem provozu a Service Fabric musí probíhat. Operace nahrávání může být pomalejší (obzvláště pokud zahrnete dobu komprimace), ale zaregistrujte a zrušte registraci typu aplikace jsou rychlejší.
- Zadejte větší časový limit pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) s `TimeoutSec` parametrem.
- Zadejte `Async` přepínač pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype). Příkaz vrátí, když cluster přijme příkaz a registrace typu aplikace pokračuje asynchronně. Z tohoto důvodu není nutné v tomto případě zadat vyšší časový limit. Příkaz [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) vypíše všechny úspěšně registrované verze typu aplikace a jejich stav registrace. Pomocí tohoto příkazu můžete určit, kdy se má registrace provést.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Nasazení balíčku aplikace s mnoha soubory

Problém: [ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) vyprší časový limit pro balíček aplikace s mnoha soubory (pořadí tisíců).
Vyzkoušejte:
- Před kopírováním do úložiště imagí [balíček Zkomprimujte](service-fabric-package-apps.md#compress-a-package) . Komprese snižuje počet souborů.
- Zadejte větší časový limit pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) s `TimeoutSec` parametrem.
- Zadejte `Async` přepínač pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype). Příkaz vrátí, když cluster přijme příkaz a registrace typu aplikace pokračuje asynchronně.
Z tohoto důvodu není nutné v tomto případě zadat vyšší časový limit. Příkaz [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) vypíše všechny úspěšně registrované verze typu aplikace a jejich stav registrace. Pomocí tohoto příkazu můžete určit, kdy se má registrace provést.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Další kroky

[Balení aplikace](service-fabric-package-apps.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

[Úvod do Service Fabricho stavu](service-fabric-health-introduction.md)

[Diagnostika a řešení potíží se službou Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelování aplikace v Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
