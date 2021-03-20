---
title: Nasazení Azure Service Fabric pomocí FabricClient
description: Pomocí rozhraní FabricClient API můžete nasazovat a odebírat aplikace v Service Fabric.
ms.topic: conceptual
ms.date: 01/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 343a37c983b1d64a4b1986913d9d6fd648a113fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785545"
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Nasazení a odebrání aplikací pomocí FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [Rozhraní API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Jakmile je [Typ aplikace zabalený][10], je připravený k nasazení do clusteru Azure Service Fabric. Nasazení zahrnuje následující tři kroky:

1. Nahrání balíčku aplikace do úložiště imagí
2. Registrace typu aplikace
3. Odebrat balíček aplikace z úložiště imagí
4. Vytvoření instance aplikace

Po nasazení aplikace a spuštění instance v clusteru můžete instanci aplikace a její typ aplikace odstranit. Pomocí následujících kroků úplně odeberte aplikaci z clusteru:

1. Odebrat (nebo odstranit) spuštěnou instanci aplikace
2. Zruší registraci typu aplikace, pokud ji už nepotřebujete.

Pokud používáte Visual Studio pro nasazení a ladění aplikací v místním vývojovém clusteru, všechny předchozí kroky se zpracují automaticky prostřednictvím skriptu PowerShellu.  Tento skript se nachází ve složce *Scripts* projektu aplikace. Tento článek poskytuje základní informace o tom, co skript dělá, abyste mohli provádět stejné operace mimo sadu Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Připojení ke clusteru
Připojte se ke clusteru vytvořením instance [FabricClient](/dotnet/api/system.fabric.fabricclient) před spuštěním libovolných příkladů kódu v tomto článku. Příklady připojení k místnímu clusteru pro vývoj nebo ke vzdálenému clusteru nebo clusteru zabezpečeného pomocí Azure Active Directory, certifikátů x509 nebo služby Windows Active Directory najdete v tématu [připojení k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Pokud se chcete připojit k místnímu vývojovému clusteru, spusťte následující příklad:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Nahrání balíčku aplikace
Předpokládejme, že sestavíte a zabalíte aplikaci s názvem *MyApplication* v aplikaci Visual Studio. Ve výchozím nastavení je název typu aplikace uvedený v ApplicationManifest.xml "MyApplicationType".  Balíček aplikace, který obsahuje nezbytný manifest aplikace, manifesty služeb a balíčky Code/config/data, se nachází v části *C:\Users \& lt; UserName &gt; \Documents\Visual Studio 2019 \ Projects\MyApplication\MyApplication\pkg\Debug*.

Nahráním balíčku aplikace se umístí do umístění, které je přístupné pro interní Service Fabric komponenty. Service Fabric ověří balíček aplikace při registraci balíčku aplikace. Pokud však chcete ověřit balíček aplikace místně (tj. před odesláním), použijte rutinu [test-ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage) .

Rozhraní API [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) odesílá balíček aplikace do úložiště imagí clusteru. 

Pokud je balíček aplikace velký nebo má mnoho souborů, můžete [ho zkomprimovat](service-fabric-package-apps.md#compress-a-package) a zkopírovat do úložiště imagí pomocí PowerShellu. Komprese zmenšuje velikost a počet souborů.

Další informace o úložišti imagí a připojovacím řetězci pro úložiště imagí najdete v tématu [vysvětlení připojovacího řetězce úložiště imagí](service-fabric-image-store-connection-string.md) .

## <a name="register-the-application-package"></a>Registrace balíčku aplikace
Typ a verze aplikace deklarované v manifestu aplikace budou k dispozici pro použití při registraci balíčku aplikace. Systém přečte balíček nahraný v předchozím kroku, ověří balíček, zpracuje obsah balíčku a zkopíruje zpracovaný balíček do interního systémového umístění.  

Rozhraní [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API registruje typ aplikace v clusteru a zpřístupňuje je pro nasazení.

Rozhraní [GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) API poskytuje informace o všech úspěšně registrovaných typech aplikací. Pomocí tohoto rozhraní API můžete určit, kdy se má registrace provést.

## <a name="remove-an-application-package-from-the-image-store"></a>Odebrání balíčku aplikace z úložiště imagí
Doporučuje se odebrat balíček aplikace po úspěšné registraci aplikace.  Odstranění balíčků aplikace z úložiště imagí uvolní systémové prostředky.  Udržování nepoužívaných balíčků aplikací spotřebovává diskové úložiště a vede k problémům s výkonem aplikací. Odstraňte balíček aplikace z úložiště imagí pomocí rozhraní [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) API.

## <a name="create-an-application-instance"></a>Vytvoření instance aplikace
Můžete vytvořit instanci aplikace z libovolného typu aplikace, který byl úspěšně zaregistrován pomocí rozhraní [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) API. Název každé aplikace musí začínat schématem *"Fabric:"* a musí být jedinečný pro každou instanci aplikace (v rámci clusteru). Vytvoří se také všechny výchozí služby definované v manifestu aplikace typu cílové aplikace.

Pro libovolnou danou verzi registrovaného typu aplikace lze vytvořit více instancí aplikace. Každá instance aplikace se spouští izolovaně s vlastním pracovním adresářem a sadou procesů.

Pokud chcete zjistit, které pojmenované aplikace a služby běží v clusteru, spusťte rozhraní API [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) a [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) .

## <a name="create-a-service-instance"></a>Vytvoření instance služby
Můžete vytvořit instanci služby z typu služby pomocí rozhraní [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API.  Je-li služba deklarována jako výchozí služba v manifestu aplikace, je vytvořena instance služby při vytvoření instance aplikace.  Volání rozhraní [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) API pro službu, která již má vytvořenou instanci, vrátí výjimku typu FabricException. Výjimka bude obsahovat kód chyby s hodnotou FabricErrorCode. ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Odebrání instance služby
Pokud již instance služby nepotřebujete, můžete ji odebrat z běžící instance aplikace voláním rozhraní [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) API.  

> [!WARNING]
> Tuto operaci nelze vrátit zpět a stav služby nelze obnovit.

## <a name="remove-an-application-instance"></a>Odebrání instance aplikace
Pokud už instanci aplikace nepotřebujete, můžete ji trvale odebrat pomocí rozhraní [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automaticky odebere všechny služby, které patří do aplikace, a trvale odebere celý stav služby.

> [!WARNING]
> Tuto operaci nelze vrátit zpět a stav aplikace nelze obnovit.

## <a name="unregister-an-application-type"></a>Zrušení registrace typu aplikace
Pokud již určitou verzi typu aplikace nepotřebujete, měli byste zrušit registraci konkrétní verze typu aplikace pomocí rozhraní API pro [zrušení registrace](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) . Rušení registrace nepoužívaných verzí typů aplikací uvolní prostor úložiště používaný úložištěm imagí. U verze typu aplikace se může zrušit registrace, pokud se nevytváří instance žádné aplikace proti této verzi typu aplikace. Také typ aplikace nemůže mít žádné probíhající upgrady aplikace, na které se odkazuje na verzi typu aplikace.

## <a name="troubleshooting"></a>Poradce při potížích
### <a name="copy-servicefabricapplicationpackage-asks-for-an-imagestoreconnectionstring"></a>Copy-ServiceFabricApplicationPackage požádá o ImageStoreConnectionString
Prostředí Service Fabric SDK by již mělo mít nastavené správné výchozí hodnoty. V případě potřeby by ale ImageStoreConnectionString pro všechny příkazy měly odpovídat hodnotě, kterou používá Cluster Service Fabric. ImageStoreConnectionString můžete najít v manifestu clusteru, který jste získali pomocí příkazů [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest) a Get-ImageStoreConnectionStringFromClusterManifest:

```powershell
PS C:\> Get-ImageStoreConnectionStringFromClusterManifest(Get-ServiceFabricClusterManifest)
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
Problém: vyprší časový limit rozhraní [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API pro velký balíček aplikace (v řádu GB).
Vyzkoušejte:
- Zadejte větší časový limit pro metodu [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) s `timeout` parametrem. Ve výchozím nastavení je časový limit 30 minut.
- Ověřte síťové připojení mezi zdrojovým počítačem a clusterem. Pokud je připojení pomalé, zvažte použití počítače s lepším připojením k síti.
Pokud je klientský počítač v jiné oblasti než cluster, zvažte použití klientského počítače v bližší nebo stejné oblasti jako cluster.
- Ověřte, jestli nepoužíváte externí omezení. Pokud je třeba úložiště imagí nakonfigurované tak, aby používalo úložiště Azure, může být nahrávání omezené.

Problém: nahrání balíčku se úspěšně dokončilo, ale vyprší časový limit rozhraní [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API. Zkuste
- Před kopírováním do úložiště imagí [balíček Zkomprimujte](service-fabric-package-apps.md#compress-a-package) .
Komprese zmenšuje velikost a počet souborů. tím se snižuje objem provozu a Service Fabric musí probíhat. Operace nahrávání může být pomalejší (obzvláště pokud zahrnete dobu komprimace), ale registraci a zrušení registrace typu aplikace jsou rychlejší.
- Zadejte větší časový limit pro rozhraní API [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) s `timeout` parametrem.

### <a name="deploy-application-package-with-many-files"></a>Nasazení balíčku aplikace s mnoha soubory
Problém: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) časový limit pro balíček aplikace s mnoha soubory (pořadí tisíců).
Vyzkoušejte:
- Před kopírováním do úložiště imagí [balíček Zkomprimujte](service-fabric-package-apps.md#compress-a-package) . Komprese snižuje počet souborů.
- Zadejte větší časový limit pro [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) s `timeout` parametrem.

## <a name="code-example"></a>Příklad kódu
Následující příklad zkopíruje balíček aplikace do úložiště imagí a zřídí typ aplikace. Pak příklad vytvoří instanci aplikace a vytvoří instanci služby. Nakonec příklad odebere instanci aplikace, zruší ustanovení typu aplikace a odstraní balíček aplikace z úložiště imagí.

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

[Úvod do Service Fabricho stavu](service-fabric-health-introduction.md)

[Diagnostika a řešení potíží se službou Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelování aplikace v Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
