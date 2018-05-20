---
title: Nasazení aplikace Azure Service Fabric | Microsoft Docs
description: Rozhraní API FabricClient použijte k nasazení a odebrat aplikace v Service Fabric.
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
ms.openlocfilehash: 50c487e6bad2a009b4f719d44b129ba860432e28
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="deploy-and-remove-applications-using-fabricclient"></a>Nasazení a odebírat aplikace pomocí FabricClient
> [!div class="op_single_selector"]
> * [Resource Manager](service-fabric-application-arm-resource.md)
> * [PowerShell](service-fabric-deploy-remove-applications.md)
> * [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
> * [Rozhraní API FabricClient](service-fabric-deploy-remove-applications-fabricclient.md)
> 
> 

<br/>

Jednou [typu aplikaci vytvořen balíček][10], je připraven k nasazení do clusteru služby Azure Service Fabric. Nasazení zahrnuje následující tři kroky:

1. Nahrání balíčku aplikace do úložiště bitové kopie
2. Registrace typu aplikace
3. Odebrání balíčku aplikace z úložiště bitových kopií
4. Vytvoření instance aplikace

Jakmile je aplikace nasazená a je spuštěna instance v clusteru, můžete odstranit instanci aplikace a její typ aplikace. Chcete-li úplně odebrat z clusteru zahrnuje následující kroky:

1. Odebrat (nebo odstranit) spuštěné instance aplikace
2. Zrušení registrace typu aplikace, pokud již nepotřebujete

Pokud používáte Visual Studio pro nasazování a ladění aplikací na místní vývojový cluster, všechny předchozí kroky jsou zpracovávány automaticky pomocí skriptu prostředí PowerShell.  Tento skript se nachází v *skripty* složku projekt aplikace. Tento článek obsahuje pozadí na co skriptu je to proto, že můžete provádět stejné operace mimo Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Připojení ke clusteru
Připojte se ke clusteru tak, že vytvoříte [FabricClient](/dotnet/api/system.fabric.fabricclient) instance předtím, než spustíte jakoukoli příklady kódu v tomto článku. Příklady připojení místního vývojového clusteru nebo vzdálený cluster nebo clusteru zabezpečené pomocí Azure Active Directory, X509 certifikáty nebo služby Windows Active Directory najdete v tématu [připojení ke clusteru zabezpečené](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Pro připojení k místní vývojový cluster, spusťte následující:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Nahrání balíčku aplikace
Předpokládejme, že sestavení a balíček aplikace s názvem *Moje_aplikace* v sadě Visual Studio. Ve výchozím nastavení je název typu aplikace uvedené v ApplicationManifest.xml "MyApplicationType".  Balíček aplikace, která obsahuje manifest nezbytné aplikace, služby manifesty a balíčků kódu/config/data, se nachází v *C:\Users\&lt; uživatelské jméno&gt;\Documents\Visual Studio 2017\Projects\MyApplication\MyApplication\pkg\Debug*.

Nahrávání balíčku aplikace vloží ho do umístění, která je přístupná pomocí interní komponenty Service Fabric. Service Fabric ověřuje balíčku aplikace během registrace balíčku aplikace. Pokud chcete ověřit balíček aplikace místně (tj. před nahráním), ale použít [Test ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) rutiny.

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) rozhraní API odesílá balíček aplikace do úložiště bitových kopií clusteru. 

Pokud balíček aplikace je velký nebo má mnoho souborů, můžete [je komprimovat](service-fabric-package-apps.md#compress-a-package) a zkopírujte jej do úložiště bitové kopie pomocí prostředí PowerShell. Komprese snižuje velikost a počet souborů.

V tématu [pochopit připojovací řetězec úložiště bitové kopie](service-fabric-image-store-connection-string.md) doplňující informace o úložiště image store a bitové kopie připojovací řetězec uložit.

## <a name="register-the-application-package"></a>Registrace balíčku aplikace
Typ aplikace a verze deklarované v manifestu aplikace k dispozici pro použití při registraci balíčku aplikace. Systém přečte nahraný v předchozím kroku balíček, ověří balíčku, zpracuje obsah balíčku a zkopíruje zpracovaná balíček do umístění interní systému.  

[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) registry rozhraní API aplikace, zadejte v clusteru a k dispozici pro nasazení.

[GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) rozhraní API poskytuje informace o všech typech aplikací byl úspěšně zaregistrován. Toto rozhraní API můžete použít k určení, kdy se provádí registraci.

## <a name="remove-an-application-package-from-the-image-store"></a>Balíček aplikace odebrat z úložiště bitových kopií
Doporučujeme odebrat balíček aplikace, po úspěšném zaregistrování aplikace.  Odstranění balíčky aplikací z úložiště image store uvolnit systémové prostředky.  Udržování balíčků nepoužívané aplikace využívá diskového úložiště a vede k problémům s výkonem aplikace. Odstranění balíčku aplikace z úložiště pomocí bitové kopie [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) rozhraní API.

## <a name="create-an-application-instance"></a>Vytvoření instance aplikace
Můžete vytvořit instanci aplikace z jakéhokoli typu aplikace, který byl úspěšně zaregistrován pomocí [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) rozhraní API. Název každé aplikace musí začínat *"fabric:"* scheme a musí být jedinečný pro každou instanci aplikace (v rámci clusteru). Žádné výchozí služby definované v manifestu aplikace cílového typu aplikace jsou také vytvářeny.

Pro danou verzi typu zaregistrovanou aplikaci lze vytvořit více instancí aplikace. Každá instance aplikace spouští v izolaci, s vlastní pracovní adresář a sadu procesy.

Pokud chcete zobrazit, které s názvem aplikace a služby běží v clusteru, spusťte [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) a [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) rozhraní API.

## <a name="create-a-service-instance"></a>Vytvoření instance služby
Můžete vytvořit instanci služby z typu služby pomocí [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) rozhraní API.  Pokud služba je deklarován jako výchozí služba v manifestu aplikace, je vytvořena při vytváření instance aplikace instance služby.  Volání [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) rozhraní API pro službu, která je již vytvořena instance vrátí k výjimce typu FabricException obsahující chybový kód s hodnotou FabricErrorCode.ServiceAlreadyExists.

## <a name="remove-a-service-instance"></a>Odebrat instanci služby
Pokud instance služby již nepotřebujete, můžete jej odebrat z spuštění instance aplikace voláním [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) rozhraní API.  

> [!WARNING]
> Tato operace se nedá vrátit a nelze ji obnovit stav služby.

## <a name="remove-an-application-instance"></a>Odebrání instance aplikace
Instance aplikace už je potřeba, trvale odstraníte ho pomocí názvu [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) rozhraní API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automaticky odstraní všechny služby, které patří do aplikace také a trvale odebrat všechny služby stavu.

> [!WARNING]
> Tato operace se nedá vrátit a nelze ji obnovit stav aplikace.

## <a name="unregister-an-application-type"></a>Typ aplikace se zrušit registraci
Pokud konkrétní verzi typ aplikace se už nepotřebuje, by měl zrušit registraci této konkrétní verze typu aplikace pomocí [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) rozhraní API. Zrušení registrace nepoužívané verze typů aplikace uvolní prostor úložiště používá úložiště bitových kopií. Verze typu aplikace může být zrušit registraci, dokud instance žádné aplikace. u této verze typu aplikace a nebyl upgradován čekající aplikace odkazují na tuto verzi typ aplikace.

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
Problém: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) rozhraní API časového limitu pro balíček rozsáhlé aplikace (pořadí GB).
Vyzkoušejte:
- Zadat delší časový limit pro [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) metody s `timeout` parametr. Ve výchozím nastavení je časový limit 30 minut.
- Zkontrolujte síťové připojení mezi zdrojovým počítačem a clusteru. Pokud připojení je pomalý, zvažte použití na počítači s lepší síťové připojení.
Pokud klientský počítač je v jiné oblasti než clusteru, zvažte použití klientský počítač v oblasti blíže nebo stejné jako cluster.
- Zkontrolujte, pokud jste nedosáhli externí omezení. Například pokud je úložiště image store je nakonfigurovaná pro použití úložiště azure, nahrávání může být omezena.

Problém: Nahrávání balíček úspěšně dokončit, ale [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) rozhraní API časového limitu. Vyzkoušejte:
- [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před kopírování do úložiště bitové kopie.
Komprese snižuje velikost a počet souborů, který pak dále snižuje objem provozu a fungovat této Service Fabric musí provést. Operace odesílání může být pomalejší (obzvláště pokud zahrnete komprese čas), ale registrace a zrušení registrace typu aplikace je rychlejší.
- Zadat delší časový limit pro [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) rozhraní API pomocí `timeout` parametr.

### <a name="deploy-application-package-with-many-files"></a>Nasazení balíčku aplikace s mnoho souborů
Problém: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) časového limitu pro balíček aplikace s mnoha souborech (pořadí tisíc).
Vyzkoušejte:
- [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před kopírování do úložiště bitové kopie. Komprese snižuje počet souborů.
- Zadat delší časový limit pro [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) s `timeout` parametr.

## <a name="code-example"></a>Příklad kódu
Následující příklad zkopíruje do úložiště bitové kopie, balíček aplikace zřizuje typ aplikace, vytvoří instanci aplikace, vytvoří instanci služby, odebere instance aplikace na zrušení zřizuje typu aplikace a odstranění balíčku aplikace z úložiště bitových kopií.

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
    string packagePath = "C:\\Users\\username\\Documents\\Visual Studio 2017\\Projects\\MyApplication\\MyApplication\\pkg\\Debug";
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

## <a name="next-steps"></a>Další postup
[Upgrade aplikace Service Fabric](service-fabric-application-upgrade.md)

[Úvod stavu Service Fabric](service-fabric-health-introduction.md)

[Diagnostika a řešení služby Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Model aplikace v Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
