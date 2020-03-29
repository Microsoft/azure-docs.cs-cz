---
title: Nasazení azure service fabric s infrastrukturovým klientem
description: Pomocí infrastruktury klienta API nasadit a odebrat aplikace v Service Fabric.
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 25b874d1be8ab50d8076ff8fe9423c8cc0187512
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75376966"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Nasazení a odebrání aplikací pomocí infrastruktury
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [Rozhraní API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Jakmile [je typ aplikace zabalen][10], je připraven k nasazení do clusteru Azure Service Fabric. Nasazení zahrnuje následující tři kroky:

1. Nahrát balíček aplikace do úložiště obrázků
2. Registrace typu přihlášky
3. Odebrání balíčku aplikace z úložiště obrázků
4. Vytvoření instance aplikace

Po nasazení aplikace a spuštění instance v clusteru můžete odstranit instanci aplikace a její typ aplikace. Úplně odebrat aplikaci z clusteru pomocí následujících kroků:

1. Odebrání (nebo odstranění) spuštěné instance aplikace
2. Zrušení registrace typu aplikace, pokud jej již nepotřebujete

Pokud používáte Visual Studio pro nasazení a ladění aplikací v clusteru místního vývoje, všechny předchozí kroky jsou zpracovány automaticky prostřednictvím skriptu prostředí PowerShell.  Tento skript se nachází ve složce *Skripty* aplikačního projektu. Tento článek obsahuje informace o tom, co tento skript dělá, takže můžete provést stejné operace mimo Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Připojení ke clusteru
Připojte se ke clusteru vytvořením instance [FabricClient](/dotnet/api/system.fabric.fabricclient) před spuštěním některého z příkladů kódu v tomto článku. Příklady připojení ke clusteru pro místní rozvoj nebo vzdálenému clusteru nebo clusteru zabezpečenému pomocí služby Azure Active Directory, certifikátů X509 nebo Služby Active Directory v [tématu Připojení k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Chcete-li se připojit ke clusteru místního vývoje, spusťte následující příklad:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Nahrát balíček aplikace
Předpokládejme, že vytvoříte a zabalíte aplikaci s názvem *MyApplication* v sadě Visual Studio. Ve výchozím nastavení je název typu aplikace uvedený v souboru ApplicationManifest.xml "MyApplicationType".  Balíček aplikace, který obsahuje nezbytný manifest aplikace, manifesty služby a balíčky code/config/data, je umístěn v *jazyce C:\Users\&lt;uživatelské jméno&gt;\Documents\Documents\Visual Studio 2019\Projects\MyApplication\MyApplication\pkg\Debug*.

Nahrání balíčku aplikace umístí do umístění, které je přístupné komponenty interní service fabric. Service Fabric ověří balíček aplikace během registrace balíčku aplikace. Pokud však chcete ověřit balíček aplikace místně (tj. před odesláním), použijte rutinu [Test-ServiceFabricApplicationPackage.](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps)

Rozhraní [COPYApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API nahraje balíček aplikace do úložiště bitových kopií clusteru. 

Pokud je balíček aplikace velký nebo obsahuje mnoho souborů, můžete [jej komprimovat](service-fabric-package-apps.md#compress-a-package) a zkopírovat do úložiště obrázků pomocí prostředí PowerShell. Komprese zmenšuje velikost a počet souborů.

Další informace o připojovacím řetězci úložiště bitových obrázků a připojovacířetězec úložiště bitových obrázků naleznete v [tématu Vysvětlení připojovacího řetězce úložiště bitových](service-fabric-image-store-connection-string.md) obrázků.

## <a name="register-the-application-package"></a>Registrace balíčku aplikace
Typ aplikace a verze deklarované v manifestu aplikace budou k dispozici pro použití při registraci balíčku aplikace. Systém přečte balíček nahraný v předchozím kroku, ověří balíček, zpracuje obsah balíčku a zkopíruje zpracovaný balíček do umístění interního systému.  

Rozhraní [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API registruje typ aplikace v clusteru a zpřístupní jej pro nasazení.

Rozhraní [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API poskytuje informace o všech úspěšně registrovaných typech aplikací. Toto rozhraní API můžete použít k určení, kdy je registrace provedena.

## <a name="remove-an-application-package-from-the-image-store"></a>Odebrání balíčku aplikace z úložiště obrázků
Doporučujeme odebrat balíček aplikace po úspěšné registraci aplikace.  Odstraněním balíčků aplikací z úložiště bitové kopie uvolníte systémové prostředky.  Udržování nepoužívaných balíčků aplikací spotřebovává diskové úložiště a vede k problémům s výkonem aplikace. Odstraňte balíček aplikace z úložiště bitových obrázků pomocí rozhraní [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API.

## <a name="create-an-application-instance"></a>Vytvoření instance aplikace
Můžete vytvořit instanci aplikace z libovolného typu aplikace, která byla úspěšně zaregistrována pomocí [rozhraní CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API. Název každé aplikace musí začínat schématem *"fabric:"* a musí být jedinečný pro každou instanci aplikace (v rámci clusteru). Všechny výchozí služby definované v manifestu aplikace cílového typu aplikace jsou také vytvořeny.

Pro libovolnou verzi registrovaného typu aplikace lze vytvořit více instancí aplikace. Každá instance aplikace běží izolovaně, s vlastním pracovním adresářem a sadou procesů.

Chcete-li zjistit, které pojmenované aplikace a služby jsou spuštěny v clusteru, spusťte pravidla API [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) a [GetServiceListAsync.](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)

## <a name="create-a-service-instance"></a>Vytvoření instance služby
Pomocí rozhraní [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API můžete vytvořit instanci služby z typu služby.  Pokud je služba deklarována jako výchozí služba v manifestu aplikace, služba je vytvořena instance při vytvoření instance aplikace.  Volání [rozhraní CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API pro službu, která je již vytvořena instance vrátí výjimku typu FabricException. Výjimka bude obsahovat kód chyby s hodnotou FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Odebrání instance služby
Pokud instance služby již není potřeba, můžete ji odebrat z spuštěné instance aplikace voláním rozhraní [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API.  

> [!WARNING]
> Tuto operaci nelze vrátit zpět a stav služby nelze obnovit.

## <a name="remove-an-application-instance"></a>Odebrání instance aplikace
Pokud instance aplikace již není potřeba, můžete ji trvale odebrat podle názvu pomocí rozhraní [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automaticky odebere všechny služby, které patří do aplikace také, trvale odebere všechny stav služby.

> [!WARNING]
> Tuto operaci nelze vrátit zpět a stav aplikace nelze obnovit.

## <a name="unregister-an-application-type"></a>Zrušení registrace typu aplikace
Pokud konkrétní verze typu aplikace již není potřeba, měli byste zrušit registraci této konkrétní verze typu aplikace pomocí [rozhraní Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) API. Zrušení registrace nepoužívaných verzí typů aplikací uvolní úložný prostor používaný úložištěm bitových bitů. Verze typu aplikace může být zrušena, pokud žádné aplikace jsou instance proti této verzi typu aplikace. Typ aplikace také může mít žádné čekající upgrady aplikace odkazují na tuto verzi typu aplikace.

## <a name="troubleshooting"></a>Řešení potíží
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage žádá o ImageStoreConnectionString
Prostředí Service Fabric SDK by již mělo mít nastaveny správné výchozí hodnoty. Ale v případě potřeby ImageStoreConnectionString pro všechny příkazy by mělodpovídat hodnotu, která používá cluster Service Fabric. V manifestu clusteru můžete najít řetězec ImageStoreConnectionString načtený pomocí příkazů [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest?view=azureservicefabricps) a Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
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
Problém: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API časový limit pro velký balíček aplikace (pořadí GB).
Zkuste:
- Zadejte větší časový rozsah pro [metodu CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) s parametrem. `timeout` Ve výchozím nastavení je časový limit 30 minut.
- Zkontrolujte síťové připojení mezi zdrojovým počítačem a clusterem. Pokud je připojení pomalé, zvažte použití počítače s lepším síťovým připojením.
Pokud se klientský počítač nachází v jiné oblasti než cluster, zvažte použití klientského počítače v bližší nebo stejné oblasti jako cluster.
- Zkontrolujte, zda se dosáhává externí omezení. Například když úložiště bitových obrázků je nakonfigurován pro použití úložiště Azure, nahrávání může být omezena.

Problém: Nahrát balíček úspěšně dokončena, ale [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) ROZHRANÍ API časový mat Zkuste:
- Před kopírováním do úložiště obrázků [balíček zkomprimujte.](service-fabric-package-apps.md#compress-a-package)
Komprese snižuje velikost a počet souborů, což zase snižuje množství provozu a práce, které musí service fabric provést. Operace nahrávání může být pomalejší (zejména pokud zahrnete čas komprese), ale zaregistrovat a zrušit registraci typu aplikace jsou rychlejší.
- Zadejte větší časový rozsah pro [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API s parametrem. `timeout`

### <a name="deploy-application-package-with-many-files"></a>Nasazení balíčku aplikace s mnoha soubory
Problém: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) časový čas pro balíček aplikace s mnoha soubory (řádit tisíce).
Zkuste:
- Před kopírováním do úložiště obrázků [balíček zkomprimujte.](service-fabric-package-apps.md#compress-a-package) Komprese snižuje počet souborů.
- Zadejte větší časový rozsah pro `timeout` [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) s parametrem.

## <a name="code-example"></a>Příklad kódu
Následující příklad zkopíruje balíček aplikace do úložiště obrázků a zřídí typ aplikace. Potom příklad vytvoří instanci aplikace a vytvoří instanci služby. Nakonec příklad odebere instanci aplikace, odvolá typ aplikace a odstraní balíček aplikace z úložiště obrázků.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Reflection;
using System.Threading.Tasks;

using System.Fabric;
using System.Fabric.Description;
using System.Threading;

namespace ServiceFabricAppLifecycle
{
class Program
{
static void Main(string[] args)
{

    string clusterConnection = "localhost:19000";
    string appName = "fabric:/MyApplication";
    string appType = "MyApplicationType";
    string appVersion = "1.0.0";
    string serviceName = "fabric:/MyApplication/Stateless1";
    string imageStoreConnectionString = "file:C:\\SfDevCluster\\Data\\ImageStoreShare";
    string packagePathInImageStore = "MyApplication";
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2019\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
    string serviceType = "Stateless1Type";

    // Connect to the cluster.
    FabricClient fabricClient = new FabricClient(clusterConnection);

    // Copy the application package to a location in the image store
    try
    {
        fabricClient.ApplicationManager.CopyApplicationPackage(imageStoreConnectionString, packagePath, packagePathInImageStore);
        Console.WriteLine("Application package copied to {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package copy to Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Provision the application.  "MyApplicationV1" is the folder in the image store where the application package is located. 
    // The application type with name "MyApplicationType" and version "1.0.0" (both are found in the application manifest) 
    // is now registered in the cluster.            
    try
    {
        fabricClient.ApplicationManager.ProvisionApplicationAsync(packagePathInImageStore).Wait();

        Console.WriteLine("Provisioned application type {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Provision Application Type failed:");

        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete the application package from a location in the image store.
    try
    {
        fabricClient.ApplicationManager.RemoveApplicationPackage(imageStoreConnectionString, packagePathInImageStore);
        Console.WriteLine("Application package removed from {0}", packagePathInImageStore);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Application package removal from Image Store failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    //  Create the application instance.
    try
    {
        ApplicationDescription appDesc = new ApplicationDescription(new Uri(appName), appType, appVersion);
        fabricClient.ApplicationManager.CreateApplicationAsync(appDesc).Wait();
        Console.WriteLine("Created application instance of type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Create the stateless service description.  For stateful services, use a StatefulServiceDescription object.
    StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
    serviceDescription.ApplicationName = new Uri(appName);
    serviceDescription.InstanceCount = 1;
    serviceDescription.PartitionSchemeDescription = new SingletonPartitionSchemeDescription();
    serviceDescription.ServiceName = new Uri(serviceName);
    serviceDescription.ServiceTypeName = serviceType;

    // Create the service instance.  If the service is declared as a default service in the ApplicationManifest.xml,
    // the service instance is already running and this call will fail.
    try
    {
        fabricClient.ServiceManager.CreateServiceAsync(serviceDescription).Wait();
        Console.WriteLine("Created service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("CreateService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete a service instance.
    try
    {
        DeleteServiceDescription deleteServiceDescription = new DeleteServiceDescription(new Uri(serviceName));

        fabricClient.ServiceManager.DeleteServiceAsync(deleteServiceDescription);
        Console.WriteLine("Deleted service instance {0}", serviceName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteService failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Delete an application instance from the application type.
    try
    {
        DeleteApplicationDescription deleteApplicationDescription = new DeleteApplicationDescription(new Uri(appName));

        fabricClient.ApplicationManager.DeleteApplicationAsync(deleteApplicationDescription).Wait();
        Console.WriteLine("Deleted application instance {0}", appName);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("DeleteApplication failed.");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    // Un-provision the application type.
    try
    {
        fabricClient.ApplicationManager.UnprovisionApplicationAsync(appType, appVersion).Wait();
        Console.WriteLine("Un-provisioned application type {0}, version {1}", appType, appVersion);
    }
    catch (AggregateException ae)
    {
        Console.WriteLine("Un-provision application type failed: ");
        foreach (Exception ex in ae.InnerExceptions)
        {
            Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
        }
    }

    Console.WriteLine("Hit enter...");
    Console.Read();
}        
}
}

```

## <a name="next-steps"></a>Další kroky
[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

[Úvod stavu service fabric](service-fabric-health-introduction.md)

[Diagnostika a řešení potíží se službou Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelování aplikace v service fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
