---
title: Nasazení aplikace Azure Service Fabric | Microsoft Docs
description: Postup nasazení a odebrat aplikace v Service Fabric pomocí prostředí PowerShell.
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
ms.openlocfilehash: 0fa7bd1135c099f853b9a3bb66661c0a57a0f7eb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271412"
---
# <a name="deploy-and-remove-applications-using-powershell"></a>Nasazení a odebírat aplikace pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [Rozhraní API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)

<br/>

Jednou [typu aplikaci vytvořen balíček][10], je připraven k nasazení do clusteru služby Azure Service Fabric. Nasazení zahrnuje následující tři kroky:

1. Nahrání balíčku aplikace do úložiště bitové kopie.
2. Registrace typu aplikace s relativní cestou úložiště bitové kopie.
3. Vytvořte instanci aplikace.

Jakmile nasazené aplikace se už nevyžaduje, můžete odstranit instanci aplikace a její typ aplikace. Chcete-li úplně odebrat z clusteru zahrnuje následující kroky:

1. Odebrat (nebo odstranit) spuštěné instance aplikace.
2. Zrušení registrace typu aplikace, pokud ji už nepotřebujete.
3. Odebrání balíčku aplikace z úložiště bitových kopií.

Pokud používáte Visual Studio pro nasazování a ladění aplikací na místní vývojový cluster, všechny předchozí kroky jsou zpracovávány automaticky pomocí skriptu prostředí PowerShell.  Tento skript se nachází v *skripty* složku projekt aplikace. Tento článek obsahuje pozadí na co skriptu je to proto, že můžete provádět stejné operace mimo Visual Studio. 

Jiný způsob, jak nasadit aplikaci je pomocí externí zřizování. Balíček aplikace může být [zabalené jako `sfpkg` ](service-fabric-package-apps.md#create-an-sfpkg) a nahranému v externím obchodu. V tomto případě není třeba nahrávání do úložiště bitové kopie. Nasazení třeba takto:

1. Nahrát `sfpkg` k externím obchodu. Externí úložiště může být jakékoli úložiště, který zveřejňuje koncový bod REST protokolu http nebo https.
2. Registrace typu aplikace pomocí externí identifikátor URI stažení a informace o typu aplikace.
2. Vytvořte instanci aplikace.

Pro čištění odeberte instancemi aplikace a zrušení registrace typu aplikace. Protože balíček nebyl zkopírován do úložiště bitové kopie, neexistuje žádný dočasné umístění pro vyčištění. Zřizování z externího úložiště je dostupné od verze 6.1 Service Fabric.

>[!NOTE]
> Visual Studio v současné době nepodporuje externí zřizování.

 
## <a name="connect-to-the-cluster"></a>Připojení ke clusteru
Než spustíte všechny příkazy prostředí PowerShell v tomto článku, vždy spustit pomocí [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) se připojit ke clusteru Service Fabric. Pro připojení k místní vývojový cluster, spusťte následující:

```powershell
PS C:\>Connect-ServiceFabricCluster
```

Příklady připojující vzdáleného clusteru nebo clusteru zabezpečené pomocí Azure Active Directory, X509 certifikáty nebo služby Windows Active Directory najdete v tématu [připojení ke clusteru zabezpečené](service-fabric-connect-to-secure-cluster.md).

## <a name="upload-the-application-package"></a>Nahrání balíčku aplikace
Nahrávání balíčku aplikace vloží ho do umístění, která je přístupná pomocí interní komponenty Service Fabric.
Pokud chcete ověřit balíček aplikace místně, použijte [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) rutiny.

[Kopie ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) příkaz odesílá balíček aplikace do úložiště bitových kopií clusteru.

Předpokládejme, že sestavení a balíček aplikace s názvem *Moje_aplikace* ve Visual Studiu 2015. Ve výchozím nastavení je název typu aplikace uvedené v ApplicationManifest.xml "MyApplicationType".  Balíček aplikace, která obsahuje manifest nezbytné aplikace, služby manifesty a balíčků kódu/config/data, se nachází v *C:\Users\<uživatelské jméno\>\Documents\Visual Studio 2015\Projects\ MyApplication\MyApplication\pkg\Debug*. 

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

Pokud balíček aplikace je velký nebo má mnoho souborů, můžete [je komprimovat](service-fabric-package-apps.md#compress-a-package). Komprese snižuje velikost a počet souborů.
Vedlejším účinkem je to registrace a zrušení registrace typu aplikace je rychlejší. Čas odeslání může být pomalejší aktuálně, zvláště pokud zahrnete čas komprimovat balíčku. 

Pokud chcete komprimovat balíček, použijte stejný [kopie ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) příkaz. Komprese stačí samostatné z nahrávání, pomocí `SkipCopy` příznak nebo společně s operace odesílání. Použití komprese na zkomprimovaného balíčku je žádná operace.
Chcete-li dekomprimovat zkomprimovaného balíčku, použijte stejný [kopie ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) s `UncompressPackage` přepínače.

Následující rutiny komprimaci balíček bez kopírování do úložiště bitové kopie. Nyní zahrnuje komprimované soubory pro balíček `Code` a `Config` balíčky. Aplikace a služby manifestech nejsou ZIP, protože jsou potřebné pro mnoho interní operace (jako je balíček sdílení, aplikace zadejte název a verze extrakce pro určité ověření). Pomocí formátu ZIP manifesty by proveďte, tyto operace neefektivní.

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

Pro velkých balíčků aplikací komprese trvá určitou dobu. Nejlepších výsledků dosáhnete pomocí rychlé jednotky SSD. Časy komprese a velikost zkomprimovaného balíčku se také liší podle obsah balíčku.
Zde je například komprese statistiku pro některé balíčky, které vykazují počáteční a velikost zkomprimovaného balíčku s časem komprese.

|Počáteční velikost (MB)|Počet souborů|Komprese čas|Zkomprimovaného balíčku velikost (MB)|
|----------------:|---------:|---------------:|---------------------------:|
|100|100|00:00:03.3547592|60|
|512|100|00:00:16.3850303|307|
|1024|500|00:00:32.5907950|615|
|2 048|1000|00:01:04.3775554|1231|
|5012|100|00:02:45.2951288|3074|

Jakmile je komprimovaný balíček, může být nahrán do jeden nebo více clusterů Service Fabric, podle potřeby. Tento mechanismus nasazení je stejný pro komprimované a nekomprimované balíčky. Komprimované balíčky jsou uloženy jako takový v úložišti clusteru bitové kopie. Balíčky jsou nekomprimovaným na uzlu před spuštěním aplikace.


Následujícím příkladu se uloží balíček do úložiště bitové kopie do složky s názvem "MyApplicationV1":

```powershell
PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -TimeoutSec 1800
```

Pokud nezadáte *- ApplicationPackagePathInImageStore* parametr, balíček aplikace se zkopíruje do složky "Debug" v úložišti bitové kopie.

>[!NOTE]
>**Kopírování ServiceFabricApplicationPackage** automaticky zjistí připojovací řetězec úložiště příslušné bitové kopie, pokud je relace prostředí PowerShell připojené ke clusteru Service Fabric. Pro Service Fabric verze starší než 5.6 **- parametr ImageStoreConnectionString** je třeba explicitně zadat argument.
>
>```powershell
>PS C:\> Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -ApplicationPackagePathInImageStore MyApplicationV1 -ImageStoreConnectionString (Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)) -TimeoutSec 1800
>```
>
>**Get-ImageStoreConnectionStringFromClusterManifest** rutiny, která je součástí modulu Service Fabric SDK PowerShell, se použije k získání připojovacího řetězce úložiště bitové kopie.  Chcete-li importovat modul SDK, spusťte:
>
>```powershell
>Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
>```
>
>V tématu [pochopit připojovací řetězec úložiště bitové kopie](service-fabric-image-store-connection-string.md) doplňující informace o úložiště image store a bitové kopie připojovací řetězec uložit.
>
>
>

Čas potřebný k nahrání balíčku se liší v závislosti na několika faktorech. Některé tyto faktory jsou počet souborů v balíčku, balíčku velikosti a velikosti souborů. Rychlost sítě mezi zdrojovým počítačem a cluster Service Fabric se zároveň ovlivňuje čas odeslání. Výchozí hodnota časového limitu pro [kopie ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) je 30 minut.
V závislosti na popisuje faktory možná budete muset zvýšit časový limit. Pokud jsou komprese balíček ve volání kopie, musíte také vzít v úvahu dobu komprese.



## <a name="register-the-application-package"></a>Registrace balíčku aplikace
Typ aplikace a verze deklarované v manifestu aplikace k dispozici pro použití při registraci balíčku aplikace. Systém přečte nahraný v předchozím kroku balíček, ověří balíčku, zpracuje obsah balíčku a zkopíruje zpracovaná balíček do umístění interní systému.  

Spustit [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) rutiny registrace typu aplikace v clusteru a zpřístupní ji pro nasazení:

### <a name="register-the-application-package-copied-to-image-store"></a>Registrace balíčku aplikace zkopírovat do úložiště bitové kopie
Pokud balíček jste dříve zkopírovali do úložiště bitové kopie, registrace operaci určuje relativní cestu v úložišti bitové kopie.

```powershell
PS C:\> Register-ServiceFabricApplicationType -ApplicationPathInImageStore MyApplicationV1
Register application type succeeded
```

"MyApplicationV1" je složka, v úložišti bitové kopie, kde se nachází balíček aplikace. Typ aplikace s názvem "MyApplicationType" a verzí "1.0.0" (jak se nacházejí v manifestu aplikace) je nyní zaregistrovaná v clusteru.

### <a name="register-the-application-package-copied-to-an-external-store"></a>Registrace balíčku aplikace zkopírovat do externího úložiště
Od verze Service Fabric verze 6.1, zřídit podporuje stažení balíčku z externího úložiště. Stahování URI představuje cestu k [ `sfpkg` balíčku aplikace](service-fabric-package-apps.md#create-an-sfpkg) odkud balíčku aplikace si můžete stáhnout pomocí protokolů HTTP nebo HTTPS. Balíček musí mít předtím nahrála do tohoto externího umístění. Identifikátor URI musí umožňovat přístup pro čtení, takže Service Fabric můžete stáhnout soubor. `sfpkg` Souboru musí mít příponu ".sfpkg". Operaci zřizování by měla obsahovat informace o typu aplikace, jak se nachází v manifestu aplikace.

```
PS C:\> Register-ServiceFabricApplicationType -ApplicationPackageDownloadUri "https://sftestresources.blob.core.windows.net:443/sfpkgholder/MyAppPackage.sfpkg" -ApplicationTypeName MyApp -ApplicationTypeVersion V1 -Async
```

[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) příkaz vrátí jenom po systém úspěšně zaregistrovala balíčku aplikace. Jak dlouho trvá registrace závisí na velikosti a obsah balíčku aplikace. V případě potřeby **- TimeoutSec** parametr lze zadat delší časový limit (výchozí hodnota časového limitu je 60 sekund).

Pokud máte velké aplikací, balíčků nebo pokud dochází k vypršení časových limitů, použijte **- asynchronní** parametr. Příkaz vrátí hodnotu, pokud clusteru přijme příkaz registrace. Operaci registrace pokračuje podle potřeby.
[Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) příkaz vypíše verze typů aplikace a jejich stav registrace. Tento příkaz slouží k určení, kdy se provádí registraci.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

## <a name="remove-an-application-package-from-the-image-store"></a>Balíček aplikace odebrat z úložiště bitových kopií
Pokud balíček byl zkopírován do úložiště bitové kopie, byste měli odebrat ji z dočasné umístění po úspěšném zaregistrování aplikace. Odstranění balíčky aplikací z úložiště image store uvolnit systémové prostředky. Udržování balíčků nepoužívané aplikace využívá diskového úložiště a vede k problémům s výkonem aplikace.

```powershell
PS C:\>Remove-ServiceFabricApplicationPackage -ApplicationPackagePathInImageStore MyApplicationV1
```

## <a name="create-the-application"></a>Vytvoření aplikace
Můžete vytvořit instanci aplikace z libovolné verze typu aplikace, který byl úspěšně zaregistrován pomocí [New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) rutiny. Název každé aplikace musí začínat *"fabric:"* scheme a musí být jedinečný pro každou instanci aplikace. Žádné výchozí služby definované v manifestu aplikace cílového typu aplikace jsou také vytvářeny.

```powershell
PS C:\> New-ServiceFabricApplication fabric:/MyApp MyApplicationType 1.0.0

ApplicationName        : fabric:/MyApp
ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
ApplicationParameters  : {}
```
Pro danou verzi typu zaregistrovanou aplikaci lze vytvořit více instancí aplikace. Každá instance aplikace spouští v izolaci, s vlastní pracovní adresář a proces.

Pokud chcete zobrazit, které s názvem aplikace a služby běží v clusteru, spusťte [Get-ServiceFabricApplication](/powershell/servicefabric/vlatest/get-servicefabricapplication) a [Get-ServiceFabricService](/powershell/module/servicefabric/get-servicefabricservice?view=azureservicefabricps) rutiny:

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
Instance aplikace už je potřeba, trvale odstraníte ho pomocí názvu [odebrat ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) rutiny. [Odebrat ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) automaticky odstraní všechny služby, které patří do aplikace také a trvale odebrat všechny služby stavu. 

> [!WARNING]
> Tato operace se nedá vrátit a nelze ji obnovit stav aplikace.

```powershell
PS C:\> Remove-ServiceFabricApplication fabric:/MyApp

Confirm
Continue with this operation?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
Remove application instance succeeded

PS C:\> Get-ServiceFabricApplication
```

## <a name="unregister-an-application-type"></a>Typ aplikace se zrušit registraci
Pokud konkrétní verzi typ aplikace se už nepotřebuje, by měl zrušení registrace typu aplikace pomocí [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) rutiny. Rušení registrace aplikace nepoužívá typy uvolní prostor úložiště používá úložiště bitových kopií a to odebráním souborů typu aplikace. Rušení registrace aplikace typu neodebere balíček aplikace, které jsou zkopírovány do dočasného umístění úložiště bitové kopie, pokud byl použit kopírování do úložiště bitové kopie. Typ aplikace může být registrace, dokud u ní jsou vytvořeny žádné aplikace a žádné čekající na vyřízení aplikace upgrady na něj odkazují.

Spustit [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) zobrazíte typy aplikací, které jsou aktuálně registrované v clusteru:

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

Spustit [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) se zrušit registraci typu konkrétní aplikaci:

```powershell
PS C:\> Unregister-ServiceFabricApplicationType MyApplicationType 1.0.0
```

## <a name="troubleshooting"></a>Řešení potíží
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Kopírování ServiceFabricApplicationPackage požádá o parametr ImageStoreConnectionString
Prostředí Service Fabric SDK byste již měli mít správný výchozí hodnoty nastavení. Ale v případě potřeby parametr ImageStoreConnectionString pro všechny příkazy musí odpovídat hodnotě používající cluster Service Fabric. Parametr ImageStoreConnectionString můžete najít v manifestu clusteru pomocí [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) a příkazy Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
```

**Get-ImageStoreConnectionStringFromClusterManifest** rutiny, která je součástí modulu Service Fabric SDK PowerShell, se použije k získání připojovacího řetězce úložiště bitové kopie.  Chcete-li importovat modul SDK, spusťte:

```powershell
Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
```

V manifestu clusteru se nachází parametr ImageStoreConnectionString:

```xml
<ClusterManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

V tématu [pochopit připojovací řetězec úložiště bitové kopie](service-fabric-image-store-connection-string.md) doplňující informace o úložiště image store a bitové kopie připojovací řetězec uložit.

### <a name="deploy-large-application-package"></a>Nasazení balíčku velké aplikace
Problém: [kopie ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) časového limitu pro balíček rozsáhlé aplikace (pořadí GB).
Vyzkoušejte:
- Zadat delší časový limit pro [kopie ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) příkazu s `TimeoutSec` parametr. Ve výchozím nastavení je časový limit 30 minut.
- Zkontrolujte síťové připojení mezi zdrojovým počítačem a clusteru. Pokud připojení je pomalý, zvažte použití na počítači s lepší síťové připojení.
Pokud klientský počítač je v jiné oblasti než clusteru, zvažte použití klientský počítač v oblasti blíže nebo stejné jako cluster.
- Zkontrolujte, pokud jste nedosáhli externí omezení. Například pokud je úložiště image store je nakonfigurovaná pro použití úložiště azure, nahrávání může být omezena.

Problém: Nahrávání balíček úspěšně dokončit, ale [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) vyprší časový limit. Vyzkoušejte:
- [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před kopírování do úložiště bitové kopie.
Komprese snižuje velikost a počet souborů, který pak dále snižuje objem provozu a fungovat této Service Fabric musí provést. Operace odesílání může být pomalejší (obzvláště pokud zahrnete komprese čas), ale registrace a zrušení registrace typu aplikace je rychlejší.
- Zadat delší časový limit pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) s `TimeoutSec` parametr.
- Zadejte `Async` přepínač pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Příkaz vrátí, když clusteru přijme příkaz a registrace typu aplikace asynchronně. Z tohoto důvodu je nutné v tomto případě specifikovat vyšší vypršení časového limitu. [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) příkaz vypíše všechny verze typů byl úspěšně zaregistrován aplikace a jejich stav registrace. Tento příkaz slouží k určení, kdy se provádí registraci.

```powershell
PS C:\> Get-ServiceFabricApplicationType

ApplicationTypeName    : MyApplicationType
ApplicationTypeVersion : 1.0.0
Status                 : Available
DefaultParameters      : { "Stateless1_InstanceCount" = "-1" }
```

### <a name="deploy-application-package-with-many-files"></a>Nasazení balíčku aplikace s mnoho souborů
Problém: [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) časového limitu pro balíček aplikace s mnoha souborech (pořadí tisíc).
Vyzkoušejte:
- [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před kopírování do úložiště bitové kopie. Komprese snižuje počet souborů.
- Zadat delší časový limit pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) s `TimeoutSec` parametr.
- Zadejte `Async` přepínač pro [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps). Příkaz vrátí, když clusteru přijme příkaz a registrace typu aplikace asynchronně.
Z tohoto důvodu je nutné v tomto případě specifikovat vyšší vypršení časového limitu. [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) příkaz vypíše všechny verze typů byl úspěšně zaregistrován aplikace a jejich stav registrace. Tento příkaz slouží k určení, kdy se provádí registraci.

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

[Diagnostika a řešení služby Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Model aplikace v Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
