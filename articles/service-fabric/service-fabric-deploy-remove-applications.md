---
title: Nasazení aplikace Azure Service Fabric | Dokumentace Microsoftu
description: Jak nasadit a odebrat aplikace v Service Fabric pomocí Powershellu.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: ryanwi
ms.openlocfilehash: d38ec87fb634e1809959b85f0382935e8a78bf3b
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697160"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Nasazení a odebírat aplikace pomocí Powershellu
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [Rozhraní API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Jednou [je zabalená aplikace typu][10], byl připraven k nasazení do clusteru Azure Service Fabric. Nasazení zahrnuje následující tři kroky:

1. Nahrání balíčku aplikace do úložiště imagí.
2. Zaregistrujte typ aplikace se relativní cesta k obrázku úložiště.
3. Vytvoření instance aplikace.

Jakmile nasazené aplikace se už nevyžaduje, můžete odstranit instance aplikace a její typ aplikace. K úplnému odebrání aplikace z clusteru zahrnuje následující kroky:

1. Odebrání (nebo odstranění) spuštěnou instanci aplikace.
2. Zrušte registraci typu aplikace, pokud ho už nepotřebují.
3. Odeberte balíček aplikace z úložiště imagí.

Pokud používáte sadu Visual Studio pro nasazování a ladění aplikací ve vašem místním vývojovém clusteru, všechny předchozí kroky jsou automaticky zpracovány pomocí skriptu prostředí PowerShell.  Tento skript se nachází v *skripty* složky projektu aplikace. Tento článek obsahuje pozadí co skript dělá, takže můžete provádět stejné operace mimo sadu Visual Studio. 

Dalším způsobem, jak nasadit aplikaci je pomocí externí zřizování. Balíček aplikace může být [zabalené jako `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) a nahráli do externího úložiště. V takovém případě není nutná nahrávání do úložiště imagí. Nasazení vyžaduje následující kroky:

1. Nahrát `sfpkg` k externím úložišti. Externí úložiště může být jakékoli úložiště, který zpřístupňuje koncový bod REST http nebo https.
2. Zaregistrujte typ aplikace pomocí externí identifikátor URI stažení a informace o typu aplikace.
2. Vytvoření instance aplikace.

Pro čištění odeberte instancemi aplikace a zrušte registraci typu aplikace. Protože balíček nebyl zkopírován do úložiště imagí, neexistuje žádný dočasného umístění pro vyčištění. Zřizování z externího úložiště je k dispozici od verze Service Fabric verze 6.1.

>[!NOTE]
> Visual Studio v současné době nepodporuje externí zřizování.

 
## <a name="connect-to-the-cluster"></a>Připojení ke clusteru
Předtím, než spustíte všechny příkazy Powershellu v tomto článku, vždy spustit pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) pro připojení ke clusteru Service Fabric. Pro připojení k místním vývojovém clusteru, spusťte následující příkaz:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Pro příklady připojení ke vzdálené cluster nebo cluster zabezpečený pomocí Azure Active Directory, X509 certifikáty, nebo si zobrazte Windows Active Directory [připojit k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Nahrání balíčku aplikace
Nahrává se balíček aplikace vloží do umístění, které je přístupné pro interní komponenty Service Fabric.
Pokud chcete ověřit balíček aplikace místně, použijte [testovací ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) rutiny.

[Kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) příkaz odešle balíček aplikace do úložiště imagí clusteru.

Předpokládejme, že sestavit a zabalit aplikaci s názvem *MyApplication* v sadě Visual Studio 2015. Ve výchozím nastavení je název typu aplikace uvedené v souboru ApplicationManifest.xml "MyApplicationType".  Balíček aplikace, který obsahuje manifest nezbytné aplikace, manifesty služby a balíčků kódu/config/dat, se nachází v *C:\Users\<uživatelské jméno\>\Documents\Visual Studio 2015\Projects\ MyApplication\MyApplication\pkg\Debug*. 

Následující příkaz vypíše obsah balíčku aplikace:

```powershell
PS C:\> $path = 'C:\Users\<user\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug'
PS C:\> tree /f $path
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

Pokud balíček aplikace je velký nebo má mnoho souborů, můžete si [je komprimovat](service-fabric-package-apps.md#compress-a-package). Komprese snižuje velikost a počet souborů.
Vedlejším účinkem je to registrace a zrušení registrace typu aplikace jsou rychlejší. Čas odeslání může být pomalejší v současné době zejména v případě, že zahrnují čas, aby se komprimoval balíček. 

Aby se komprimoval balíček, použijte stejný [kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) příkazu. Komprese je možné samostatné od nahrávání, díky použití `SkipCopy` příznak, nebo spolu s časový limit operace odeslání. Použití kompresi u zkomprimovaného balíčku je no-op.
Dekomprimovat zkomprimovaného balíčku, použijte stejný [kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) příkazů `UncompressPackage` přepnout.

Následující rutina komprimuje balíček bez kopírování do úložiště imagí. Balíček nyní obsahuje soubory ZIP `Code` a `Config` balíčky. Manifesty služby a aplikace nejsou ZIP, protože jsou potřeba pro mnoho interní operace (jako je balíček sdílení, aplikace typu názvem a verzí extrakce pro určité ověření). Zipování manifesty s žádným tyto operace neefektivní.

```
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage -SkipCopy
PS C:\> tree /f $path
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

U velkých balíčků aplikací komprese trvá určitou dobu. Nejlepších výsledků dosáhnete použijte rychlé jednotku SSD. Časy komprese a velikost zkomprimovaného balíčku také liší v závislosti na obsahu balíčku.
Například tady je statistika komprese pro některé balíčky, které zobrazují počáteční a velikost zkomprimovaného balíčku s časem compression.

|Počáteční velikost (MB)|Počet souborů|Čas komprese|Komprimovaný balíček velikost (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2 048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Po balíčku je komprimován, může být nahrán do jednoho nebo více clusterů Service Fabric, podle potřeby. Mechanismus pro nasazení je stejný pro komprimované a nekomprimované balíčky. Komprimované balíčky v důsledku ukládají do úložiště imagí clusteru. Balíčky jsou nekomprimovaný na uzlu před spuštěním aplikace.


Následující příklad nahraje do složky s názvem "MyApplicationV1" balíček do úložiště imagí:

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Pokud nezadáte *- ApplicationPackagePathInImageStore* parametr, balíček aplikace je zkopírován do složky "Ladění" v úložišti imagí.

>[!NOTE]
>**Kopírování ServiceFabricApplicationPackage** automaticky zjišťovat připojovací řetězec úložiště odpovídající image, pokud relace prostředí PowerShell je připojené ke clusteru Service Fabric. Pro Service Fabric verze starší než 5.6 **- ImageStoreConnectionString** argument musí být explicitně zadat.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>**Get-ImageStoreConnectionStringFromClusterManifest** rutiny, která je součástí modulu Powershellu pro Service Fabric SDK, se používá k získání připojovacího řetězce úložiště bitové kopie.  Pokud chcete importovat modul SDK, spusťte:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Zobrazit [pochopit připojovací řetězec úložiště image](service-fabric-image-store-connection-string.md) doplňující informace o úložiště image store a image uložit připojovací řetězec.
>
>
>

Čas potřebný k nahrání balíčku se liší v závislosti na několika faktorech. Některé z těchto faktorů jsou počet souborů v balíčku, velikost balíčku a velikosti souborů. Rychlost sítě mezi zdrojovým počítačem a cluster Service Fabric také má vliv na dobu nahrávání. Výchozí hodnota časového limitu pro [kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) je 30 minut.
Závisí to na faktorech popsaných bude pravděpodobně nutné zvýšit časový limit. Pokud jsou komprese balíček ve volání kopie, musíte také vzít v úvahu dobu komprese.



## <a name="register-the-application-package"></a>Registrace balíčku aplikace
Typ aplikace a verze deklarované v manifestu aplikace, které jsou k dispozici pro použití při registraci balíčku aplikace. Systém přečte balíček nahráli v předchozím kroku, ověří balíček, zpracuje obsah balíčku a zkopíruje zpracovaných balíček do umístění interního systému.  

Spustit [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) rutiny zaregistrujte typ aplikace v clusteru a zpřístupní ji pro nasazení:

### <a name="register-the-application-package-copied-to-image-store"></a>Registrace balíčku aplikace, které jsou zkopírovány do úložiště imagí
Když balíček jste dříve zkopírovali do úložiště imagí, operace registrace určuje relativní cestu v úložišti imagí.

```powershell
PS C:\> Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" je složka, v úložišti imagí, kde se nachází balíček aplikace. Typ aplikace s názvem "MyApplicationType" a verzí "1.0.0" (obojí se nacházejí v manifestu aplikace) je teď zaregistrované v clusteru.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registrace balíčku aplikace, které jsou zkopírovány do externího úložiště
Spouští se s platformou Service Fabric verze 6.1, zřídíte podporuje stahování balíčku z externího úložiště. Stahování identifikátor URI představuje cestu k [ `sfpkg` balíčku aplikace](service-fabric-package-apps.md#create-an-sfpkg) odkud balíček aplikace si můžete stáhnout pomocí protokolů HTTP nebo HTTPS. Balíček musí mít byl dříve odeslán do tohoto externího umístění. Identifikátor URI musí povolit přístup pro čtení, takže Service Fabric můžete stáhnout soubor. `sfpkg` Souboru musí mít příponu ".sfpkg". Operace zřízení by měla obsahovat informace o typu aplikace, jak se nachází v manifestu aplikace.

```
PS C:\> Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) příkaz vrátí jenom po systém úspěšně zaregistrovala balíčku aplikace. Jak dlouho trvá registrace závisí na velikosti a obsah balíčku aplikace. V případě potřeby **- TimeoutSec** parametr lze použít k zadání delšího časového limitu (výchozí hodnota časového limitu je 60 sekund).

Pokud máte velké aplikaci zabalit, nebo pokud dochází k vypršení časového limitu, použijte **- asynchronní** parametru. Příkaz vrátí hodnotu, pokud cluster přijme příkaz pro registraci. Operace registrace pokračuje podle potřeby.
[Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) příkaz vypíše verze typů aplikací a jejich stav registrace. Tento příkaz slouží k určení, kdy se provádí registraci.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Odeberte balíček aplikace z úložiště imagí
Pokud balíček byl zkopírován do úložiště imagí, by měly odebrat z dočasného umístění po úspěšné registraci aplikace. Odstranění balíčků aplikací z úložiště image store uvolnit systémové prostředky. Udržování balíčky nepoužívané aplikace využívá diskové úložiště a vede k problémům s výkonem aplikací.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Vytvoření aplikace
Můžete vytvořit instanci aplikace z libovolnou verzi typu aplikace, který byl úspěšně zaregistrován pomocí [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) rutiny. Název každé aplikace musí začínat *"prostředků infrastruktury:"* schéma a musí být jedinečná pro každou instanci aplikace. Žádné výchozí služby definované v manifestu aplikace typ cílové aplikace se také vytvoří.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Několik instancí aplikace lze vytvořit danou verzi typu registrované aplikaci. Každá instance aplikace běží izolovaně, s vlastní pracovní adresář a proces.

Pokud chcete zobrazit, které s názvem aplikace a služby jsou spuštěny v clusteru, spusťte [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) a [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) rutiny:

```powershell
PS C:\> Get-ServiceFabricApplication  

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Ok
ApplicationParameters  : {}

PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService

ServiceName            : fabric:/MyApp/Stateless1
ServiceKind            : Stateless
ServiceTypeName        : Stateless1Type
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
ServiceStatus          : Active
HealthState            : Ok
```

## <a name="remove-an-application"></a>Odebrání aplikace
Když instance aplikace už je nepotřebujete, můžete trvale odebrat ji pomocí názvu [odebrat ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) rutiny. [Odebrat ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automaticky odstraní všechny služby, které patří k aplikaci stejně, trvale odebrat všechny služby stavu. 

> [!WARNING]
> Tato operace je nevratná a nedá se obnovit stav aplikace.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Zrušte registraci typu aplikace
Pokud konkrétní verzi typu aplikace je už nepotřebujete, měli byste zrušit registraci typu aplikace pomocí [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) rutiny. Zrušení registrace typů nepoužívané aplikace uvolní prostor úložiště využitý úložiště image store to odebráním souborů typu aplikace. Rušení registrace aplikace typu neodebere zkopírován do dočasného umístění úložiště bitové kopie balíčku aplikace, pokud byl použit kopírování do úložiště imagí. Typ aplikace lze zrušit registraci instance žádné aplikace se vytvářejí pomocí jej a pokud ne čekající aplikace upgrady na něj odkazují.

Spustit [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) zobrazíte typy aplikací, které jsou aktuálně registrované v clusteru:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Spustit [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) ke zrušení registrace konkrétní typ aplikace:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Řešení potíží
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Kopírování ServiceFabricApplicationPackage vyzve k zadání ImageStoreConnectionString
Sada Service Fabric SDK prostředí byste už měli mít správné nastavení výchozí hodnoty. Ale v případě potřeby ImageStoreConnectionString pro všechny příkazy by měl odpovídat hodnotě, která používá cluster Service Fabric. V manifestu clusteru, můžete najít ImageStoreConnectionString načten pomocí možnosti [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) a Get-ImageStoreConnectionStringFromClusterManifest příkazy:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

**Get-ImageStoreConnectionStringFromClusterManifest** rutiny, která je součástí modulu Powershellu pro Service Fabric SDK, se používá k získání připojovacího řetězce úložiště bitové kopie.  Pokud chcete importovat modul SDK, spusťte:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

ImageStoreConnectionString se nachází v manifestu clusteru:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Zobrazit [pochopit připojovací řetězec úložiště image](service-fabric-image-store-connection-string.md) doplňující informace o úložiště image store a image uložit připojovací řetězec.

### <a name="deploy-large-application-package"></a>Nasazení balíčku velké aplikace
Problém: [kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) vyprší časový limit pro velké aplikace balíčku (pořadí GB).
Zkuste:
- Zadejte většího časového limitu pro [kopírování ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) příkazů, `TimeoutSec` parametru. Ve výchozím nastavení je časový limit 30 minut.
- Zkontrolujte síťové připojení mezi zdrojovým počítačem a clusteru. Pokud je pomalé připojení, zvažte použití na počítači s lepší síťové připojení.
Pokud klientský počítač je v jiné oblasti než cluster, zvažte použití klientského počítače v oblasti blíže nebo stejný jako cluster.
- Zkontrolujte, jestli nenarážíte externí omezení šířky pásma. Například pokud je úložiště image store je konfigurován pro použití služby azure storage, může omezení nahrávání.

Problém: Nahrání balíčku se nedokončil úspěšně, ale [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) vyprší časový limit. Zkuste:
- [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před zkopírováním do úložiště imagí.
Komprese snižuje velikost a počet souborů, která zase omezuje objem přenosů a pracovat v daných prostředcích služby musíte provést. Časový limit operace odeslání může být pomalejší (zejména pokud zahrnete komprese čas), ale jsou rychlejší registrace a zrušení registrace typu aplikace.
- Zadejte většího časového limitu pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) s `TimeoutSec` parametru.
- Zadejte `Async` přepnout [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Příkaz vrátí, když přijme příkaz clusteru a registraci typu aplikace asynchronně. Z tohoto důvodu není nutné zadat v tomto případě vyšší vypršení časového limitu. [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) příkaz vypíše všechny verze typu aplikace byl úspěšně zaregistrován a jejich stav registrace. Tento příkaz slouží k určení, kdy se provádí registraci.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Nasazení balíčku aplikace s velkým množstvím souborů
Problém: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) vyprší časový limit pro balíček aplikace s velkým množstvím souborů (pořadí tisíc).
Zkuste:
- [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před zkopírováním do úložiště imagí. Komprese snižuje počet souborů.
- Zadejte většího časového limitu pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) s `TimeoutSec` parametru.
- Zadejte `Async` přepnout [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Příkaz vrátí, když přijme příkaz clusteru a registraci typu aplikace asynchronně.
Z tohoto důvodu není nutné zadat v tomto případě vyšší vypršení časového limitu. [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) příkaz vypíše všechny verze typu aplikace byl úspěšně zaregistrován a jejich stav registrace. Tento příkaz slouží k určení, kdy se provádí registraci.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="next-steps"></a>Další postup
[Balení aplikace](service-fabric-package-apps.md)

[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

[Úvod stavu Service Fabric](service-fabric-health-introduction.md)

[Diagnostika a řešení potíží služby Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelování aplikace v Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
