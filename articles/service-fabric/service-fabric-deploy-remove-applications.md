---
title: Nasazení Azure Service Fabric s PowerShellem
description: Přečtěte si o odebrání a nasazení aplikací ve službě Azure Fabric a o tom, jak tyto akce provádět v Powershellu.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: e3fdd194f2949f1246e991968e02b3278f33f7db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282507"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Nasazení a odebrání aplikací pomocí PowerShellu

> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [Rozhraní API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Jakmile [je typ aplikace zabalen][10], je připraven k nasazení do clusteru Azure Service Fabric. Nasazení zahrnuje následující tři kroky:

1. Nahrajte balíček aplikace do úložiště obrázků.
2. Zaregistrujte typ aplikace s relativní cestou úložiště obrázků.
3. Vytvořte instanci aplikace.

Jakmile nasazené aplikace již není vyžadována, můžete odstranit instanci aplikace a její typ aplikace. Chcete-li zcela odebrat aplikaci z clusteru zahrnuje následující kroky:

1. Odeberte (nebo odstraňte) spuštěnou instanci aplikace.
2. Pokud už jej nepotřebujete, odregistrute typ aplikace.
3. Odeberte balíček aplikace z úložiště obrázků.

Pokud používáte Visual Studio pro nasazení a ladění aplikací v clusteru místního vývoje, všechny předchozí kroky jsou zpracovány automaticky prostřednictvím skriptu prostředí PowerShell.  Tento skript se nachází ve složce *Skripty* aplikačního projektu. Tento článek obsahuje informace o tom, co tento skript dělá, takže můžete provádět stejné operace mimo Visual Studio. 

Dalším způsobem nasazení aplikace je pomocí externího poskytování. Balíček aplikace může být [zabalen jako `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) a odeslán do externího úložiště. V takovém případě není potřeba nahrát do obchodu s obrázky. Nasazení vyžaduje následující kroky:

1. Nahrajte `sfpkg` jej do externího obchodu. Externí úložiště může být libovolné úložiště, které zveřejňuje koncový bod REST http nebo https.
2. Zaregistrujte typ aplikace pomocí externího identifikátoru URI pro stahování a informací o typu aplikace.
2. Vytvořte instanci aplikace.

Chcete-li vyčistit, odeberte instance aplikace a odregistrujte typ aplikace. Vzhledem k tomu, že balíček nebyl zkopírován do úložiště obrázků, neexistuje žádné dočasné umístění k vyčištění. Zřizování z externího úložiště je k dispozici počínaje Service Fabric verze 6.1.

>[!NOTE]
> Visual Studio aktuálně nepodporuje externí poskytování.

 

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

Před spuštěním libovolných příkazů prostředí PowerShell v tomto článku vždy začněte pomocí [connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) pro připojení ke clusteru Service Fabric. Chcete-li se připojit ke clusteru místního vývoje, spusťte následující kroky:

```powershell
Connect-ServiceFabricCluster
```

Příklady připojení ke vzdálenému clusteru nebo clusteru zabezpečenému pomocí služby Azure Active Directory, certifikátů X509 nebo Služby Windows Active Directory najdete v [tématu Připojení k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Nahrát balíček aplikace

Nahrání balíčku aplikace umístí do umístění, které je přístupné interní součásti Service Fabric.
Pokud chcete ověřit balíček aplikace místně, použijte [rutinu Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)

Příkaz [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) nahraje balíček aplikace do úložiště bitových kopií clusteru.

Předpokládejme, že vytvoříte a zabalíte aplikaci s názvem *MyApplication* v sadě Visual Studio 2015. Ve výchozím nastavení je název typu aplikace uvedený v souboru ApplicationManifest.xml "MyApplicationType".  Balíček aplikace, který obsahuje nezbytný manifest aplikace, manifesty služby a balíčky code/config/data, je umístěn v *jazyce C:\Users\<username\>\Documents\Visual Studio 2015\Projects\MyApplication\MyApplication\pkg\Debug*. 

Následující příkaz uvádí obsah balíčku aplikace:

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

Pokud je balíček aplikace velký a/nebo obsahuje mnoho souborů, můžete [jej komprimovat](service-fabric-package-apps.md#compress-a-package). Komprese zmenšuje velikost a počet souborů.
Vedlejším účinkem je, že registrace a zrušení registrace typu aplikace jsou rychlejší. Doba nahrávání může být v současné době pomalejší, zejména pokud zahrnete čas komprese balíčku. 

Chcete-li komprimovat balíček, použijte stejný příkaz [Copy-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) Kompresi lze provést odděleně od `SkipCopy` nahrávání, pomocí příznaku nebo společně s operací nahrávání. Použití komprese na komprimovaný balíček je no-op.
Chcete-li rozbalit komprimovaný balíček, použijte stejný příkaz `UncompressPackage` [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) s přepínačem.

Následující rutina komprimuje balíček bez kopírování do úložiště obrázků. Balíček nyní obsahuje zip `Code` soubory `Config` pro a balíčky. Aplikace a manifesty služby nejsou zip, protože jsou potřebné pro mnoho interních operací (jako je sdílení balíčků, název typu aplikace a extrakce verze pro určitá ověření). Zipování manifesty by tyto operace neefektivní.

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

U velkých balíčků aplikací komprese nějakou dobu trvá. Nejlepších výsledků dosáhnete, když použijete rychlou jednotku SSD. Časy komprese a velikost komprimovaného balíčku se také liší v závislosti na obsahu balíčku.
Zde je například statistika komprese pro některé balíčky, které zobrazují počáteční a komprimovované velikosti balíčku s časem komprese.

|Počáteční velikost (MB)|Počet souborů|Čas komprese|Velikost komprimovaného balíku (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2 048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Jakmile je balíček komprimován, lze jej podle potřeby odeslat do jednoho nebo více clusterů Service Fabric. Mechanismus nasazení je stejný pro komprimované a nekomprimované balíčky. Komprimované balíčky jsou uloženy jako takové v úložišti bitových obrázků clusteru. Balíčky jsou nekomprimované na uzlu, před spuštěním aplikace.


Následující příklad odešle balíček do úložiště obrázků do složky s názvem "MyApplicationV1":

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Pokud nezadáte parametr *-ApplicationPackagePathInImageStore,* balíček aplikace se zkopíruje do složky "Ladění" v úložišti obrázků.

>[!NOTE]
>**Copy-ServiceFabricApplicationPackage** automaticky rozpozná příslušný připojovací řetězec úložiště bitových kopií, pokud je relace prostředí PowerShell připojena ke clusteru Service Fabric. Pro verze Service Fabric starší než 5.6 musí být explicitně poskytnut argument **-ImageStoreConnectionString.**
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>Rutina **Get-ImageStoreConnectionStringFromClusterManifest,** která je součástí modulu Service Fabric SDK PowerShell, se používá k získání připojovacího řetězce úložiště bitové kopie.  Chcete-li importovat modul SDK, spusťte:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>Další informace o připojovacím řetězci úložiště bitových obrázků a připojovacířetězec úložiště bitových obrázků naleznete v [tématu Vysvětlení připojovacího řetězce úložiště bitových](service-fabric-image-store-connection-string.md) obrázků.
>
>
>

Čas potřebný k nahrání balíčku se liší v závislosti na více faktorech. Některé z těchto faktorů jsou počet souborů v balíčku, velikost balíčku a velikosti souborů. Rychlost sítě mezi zdrojovým počítačem a clusterem Service Fabric má také vliv na čas nahrávání. Výchozí časový limit pro [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) je 30 minut.
V závislosti na popsaných faktorech může být pravděpodobně třeba zvýšit časový čas. Pokud komprimujete balíček ve volání kopie, musíte také zvážit čas komprese.



## <a name="register-the-application-package"></a>Registrace balíčku aplikace

Typ aplikace a verze deklarované v manifestu aplikace budou k dispozici pro použití při registraci balíčku aplikace. Systém přečte balíček nahraný v předchozím kroku, ověří balíček, zpracuje obsah balíčku a zkopíruje zpracovaný balíček do umístění interního systému.  

Spusťte rutinu [Register-ServiceFabricApplicationType,](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) abyste zaregistrovali typ aplikace v clusteru a zpřístupnite jej pro nasazení:

### <a name="register-the-application-package-copied-to-image-store"></a>Registrace balíčku aplikace zkopírovaného do úložiště obrázků

Když byl balíček dříve zkopírován do úložiště obrázků, operace registru určuje relativní cestu v úložišti obrázků.

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
```

```Output
Register application type succeeded
```

"MyApplicationV1" je složka v úložišti obrázků, kde je umístěn balíček aplikace. Typ aplikace s názvem "MyApplicationType" a verze "1.0.0" (oba jsou nalezeny v manifestu aplikace) je nyní registrován v clusteru.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registrace balíčku aplikace zkopírovaného do externího úložiště

Počínaje Service Fabric verze 6.1, zřízení podporuje stahování balíčku z externího úložiště. Identifikátor URI ke stažení představuje cestu k [ `sfpkg` balíčku aplikace,](service-fabric-package-apps.md#create-an-sfpkg) ze kterého lze balíček aplikace stáhnout pomocí protokolů HTTP nebo HTTPS. Balíček musí být dříve odeslán do tohoto externího umístění. Identifikátor URI musí umožňovat přístup ke čtení, aby aplikace Service Fabric mohla soubor stáhnout. Soubor `sfpkg` musí mít příponu ".sfpkg". Operace zřízení by měla obsahovat informace o typu aplikace, jak je uvedeno v manifestu aplikace.

```powershell
Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

Příkaz [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) vrátí pouze po úspěšném zaregistrování balíčku aplikace systémem. Jak dlouho trvá registrace, závisí na velikosti a obsahu balíčku aplikace. V případě potřeby parametr **-TimeoutSec** lze použít k zadání delší časový limit (výchozí časový limit je 60 sekund).

Pokud máte velký balíček aplikace nebo pokud dochází k časovým časům, použijte parametr **-Async.** Příkaz se vrátí, když cluster přijme příkaz register. Operace registru pokračuje podle potřeby.
Příkaz [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) uvádí verze typu aplikace a jejich stav registrace. Tento příkaz můžete použít k určení, kdy je registrace provedena.

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Odebrání balíčku aplikace z úložiště obrázků

Pokud byl balíček zkopírován do úložiště obrázků, měli byste jej odebrat z dočasného umístění po úspěšné registraci aplikace. Odstraněním balíčků aplikací z úložiště bitové kopie uvolníte systémové prostředky. Udržování nepoužívaných balíčků aplikací spotřebovává diskové úložiště a vede k problémům s výkonem aplikace.

```powershell
Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Vytvoření aplikace

Můžete vytvořit instanci aplikace z libovolné verze typu aplikace, která byla úspěšně zaregistrována pomocí rutiny [New-ServiceFabricApplication.](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) Název každé aplikace musí začínat schématem *"fabric:"* a musí být jedinečný pro každou instanci aplikace. Všechny výchozí služby definované v manifestu aplikace cílového typu aplikace jsou také vytvořeny.

```powershell
New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0
```

```Output
ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```

Pro libovolnou verzi registrovaného typu aplikace lze vytvořit více instancí aplikace. Každá instance aplikace běží izolovaně, s vlastním pracovním adresářem a procesem.

Chcete-li zjistit, které pojmenované aplikace a služby jsou spuštěny v clusteru, spusťte rutiny [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) a [Get-ServiceFabricService:](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps)

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

Pokud instance aplikace již není potřeba, můžete trvale odebrat podle názvu pomocí rutiny [Remove-ServiceFabricApplication.](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automaticky odebere všechny služby, které patří do aplikace také, trvale odebere všechny stav služby. 

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

Pokud konkrétní verze typu aplikace již není potřeba, měli byste zrušit registraci typu aplikace pomocí rutiny [Unregister-ServiceFabricApplicationType.](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) Zrušení registrace nepoužívaných typů aplikací uvolní úložný prostor používaný úložištěm bitových obrázků odebráním souborů typu aplikace. Zrušení registrace typu aplikace neodebere balíček aplikace zkopírovaný do dočasného umístění úložiště obrázků, pokud byla použita kopie do úložiště obrázků. Typ aplikace může být neregistrovaný, pokud proti němu nejsou vytvořena žádná instance žádné aplikace a žádné čekající upgrady aplikací na něj odkazují.

Spuštěním [příkazu Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) zobrazíte typy aplikací aktuálně registrované v clusteru:

```powershell
Get-ServiceFabricApplicationType
```

```Output
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Spuštěním [příkazu Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) zrušíte registraci určitého typu aplikace:

```powershell
Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Řešení potíží

### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage žádá o ImageStoreConnectionString

Prostředí Service Fabric SDK by již mělo mít nastaveny správné výchozí hodnoty. Ale v případě potřeby ImageStoreConnectionString pro všechny příkazy by mělodpovídat hodnotu, která používá cluster Service Fabric. V manifestu clusteru můžete najít řetězec ImageStoreConnectionString načtený pomocí příkazů [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) a Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

Rutina **Get-ImageStoreConnectionStringFromClusterManifest,** která je součástí modulu Service Fabric SDK PowerShell, se používá k získání připojovacího řetězce úložiště bitové kopie.  Chcete-li importovat modul SDK, spusťte:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

V manifestu clusteru je nalezen řetězec ImageStoreConnectionString:

```xml
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Další informace o připojovacím řetězci úložiště bitových obrázků a připojovacířetězec úložiště bitových obrázků naleznete v [tématu Vysvětlení připojovacího řetězce úložiště bitových](service-fabric-image-store-connection-string.md) obrázků.

### <a name="deploy-large-application-package"></a>Nasazení velkého balíčku aplikací

Problém: [Copy-ServiceFabricApplicationPackage časový](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) limit pro velký balíček aplikace (pořadí GB).
Zkuste:
- Zadejte větší časový rozsah pro [příkaz Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) s parametrem. `TimeoutSec` Ve výchozím nastavení je časový limit 30 minut.
- Zkontrolujte síťové připojení mezi zdrojovým počítačem a clusterem. Pokud je připojení pomalé, zvažte použití počítače s lepším síťovým připojením.
Pokud se klientský počítač nachází v jiné oblasti než cluster, zvažte použití klientského počítače v bližší nebo stejné oblasti jako cluster.
- Zkontrolujte, zda se dosáhává externí omezení. Například když úložiště bitových obrázků je nakonfigurován pro použití úložiště Azure, nahrávání může být omezena.

Problém: Nahrát balíček úspěšně dokončen, ale [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) časový mísa. Zkuste:
- Před kopírováním do úložiště obrázků [balíček zkomprimujte.](service-fabric-package-apps.md#compress-a-package)
Komprese snižuje velikost a počet souborů, což zase snižuje množství provozu a práce, které musí service fabric provést. Operace nahrávání může být pomalejší (zejména pokud zahrnete čas komprese), ale zaregistrovat a zrušit registraci typu aplikace jsou rychlejší.
- Zadejte větší časový rozsah pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) s parametrem. `TimeoutSec`
- Zadejte `Async` přepínač pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Příkaz se vrátí, když cluster přijme příkaz a registrace typu aplikace pokračuje asynchronně. Z tohoto důvodu není nutné v tomto případě zadávat vyšší časový čas. Příkaz [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) obsahuje seznam všech úspěšně registrovaných verzí typu aplikace a jejich stav registrace. Tento příkaz můžete použít k určení, kdy je registrace provedena.

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

Problém: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) časový čas pro balíček aplikace s mnoha soubory (řádově tisíců).
Zkuste:
- Před kopírováním do úložiště obrázků [balíček zkomprimujte.](service-fabric-package-apps.md#compress-a-package) Komprese snižuje počet souborů.
- Zadejte větší časový rozsah pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) s parametrem. `TimeoutSec`
- Zadejte `Async` přepínač pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Příkaz se vrátí, když cluster přijme příkaz a registrace typu aplikace pokračuje asynchronně.
Z tohoto důvodu není nutné v tomto případě zadávat vyšší časový čas. Příkaz [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) obsahuje seznam všech úspěšně registrovaných verzí typu aplikace a jejich stav registrace. Tento příkaz můžete použít k určení, kdy je registrace provedena.

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

[Úvod stavu service fabric](service-fabric-health-introduction.md)

[Diagnostika a řešení potíží se službou Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelování aplikace v service fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md