---
title: Nasazení aplikace Azure Service Fabric | Dokumentace Microsoftu
description: Rozhraní API FabricClient použijte k nasazení a odebrat aplikace v Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: b120ffbf-f1e3-4b26-a492-347c29f8f66b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/19/2018
ms.author: aljo
ms.openlocfilehash: 408ef5abeed238a2bf4437bea0b77b6768961f53
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661152"
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

Jednou [je zabalená aplikace typu][10], byl připraven k nasazení do clusteru Azure Service Fabric. Nasazení zahrnuje následující tři kroky:

1. Nahrání balíčku aplikace do úložiště imagí
2. Zaregistrujte typ aplikace
3. Odeberte balíček aplikace z úložiště imagí
4. Vytvoření instance aplikace

Po nasazení aplikace a instance běží v clusteru, můžete odstranit instance aplikace a její typ aplikace. K úplnému odebrání aplikace z clusteru zahrnuje následující kroky:

1. Odebrání (nebo odstranění) spuštěná instance aplikace
2. Zrušte registraci typu aplikace, pokud ho už nepotřebují

Pokud používáte sadu Visual Studio pro nasazování a ladění aplikací ve vašem místním vývojovém clusteru, všechny předchozí kroky jsou automaticky zpracovány pomocí skriptu prostředí PowerShell.  Tento skript se nachází v *skripty* složky projektu aplikace. Tento článek obsahuje pozadí co skript dělá, takže můžete provádět stejné operace mimo sadu Visual Studio. 
 
## <a name="connect-to-the-cluster"></a>Připojení ke clusteru
Připojte se ke clusteru tak, že vytvoříte [FabricClient](/dotnet/api/system.fabric.fabricclient) instance předtím, než spustíte všechny příklady kódu v tomto článku. Příklady připojení k místním vývojovém clusteru nebo vzdálený cluster nebo cluster zabezpečený pomocí Azure Active Directory, X509 certifikáty, nebo si zobrazte Windows Active Directory [připojit k zabezpečenému clusteru](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-the-fabricclient-apis). Pro připojení k místním vývojovém clusteru, spusťte následující příkaz:

```csharp
// Connect to the local cluster.
FabricClient fabricClient = new FabricClient();
```

## <a name="upload-the-application-package"></a>Nahrání balíčku aplikace
Předpokládejme, že sestavit a zabalit aplikaci s názvem *MyApplication* v sadě Visual Studio. Ve výchozím nastavení je název typu aplikace uvedené v souboru ApplicationManifest.xml "MyApplicationType".  Balíček aplikace, který obsahuje manifest nezbytné aplikace, manifesty služby a balíčků kódu/config/dat, se nachází v *C:\Users\&lt; uživatelské jméno&gt;\Documents\Visual Studio 2017\Projects\ MyApplication\MyApplication\pkg\Debug*.

Nahrává se balíček aplikace vloží do umístění, které je přístupné pro interní komponenty Service Fabric. Balíček aplikace Service Fabric ověřuje při registraci balíčku aplikace. Pokud chcete ověřit balíček aplikace místně (tj. před nahráním), ale použít [testovací ServiceFabricApplicationPackage](/powershell/module/servicefabric/test-servicefabricapplicationpackage?view=azureservicefabricps) rutiny.

[CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) rozhraní API odešle balíček aplikace do úložiště imagí clusteru. 

Pokud balíček aplikace je velký nebo má mnoho souborů, můžete si [je komprimovat](service-fabric-package-apps.md#compress-a-package) a zkopírujte ho do úložiště imagí pomocí Powershellu. Komprese snižuje velikost a počet souborů.

Zobrazit [pochopit připojovací řetězec úložiště image](service-fabric-image-store-connection-string.md) doplňující informace o úložiště image store a image uložit připojovací řetězec.

## <a name="register-the-application-package"></a>Registrace balíčku aplikace
Typ aplikace a verze deklarované v manifestu aplikace, které jsou k dispozici pro použití při registraci balíčku aplikace. Systém přečte balíček nahráli v předchozím kroku, ověří balíček, zpracuje obsah balíčku a zkopíruje zpracovaných balíček do umístění interního systému.  

[ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) registrů rozhraní API aplikace do clusteru zadejte a zpřístupní ji pro nasazení.

[GetApplicationTypeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationtypelistasync) rozhraní API poskytuje informace o všech typech aplikací byl úspěšně zaregistrován. Toto rozhraní API můžete použít k určení, kdy se provádí registraci.

## <a name="remove-an-application-package-from-the-image-store"></a>Odeberte balíček aplikace z úložiště imagí
Doporučujeme odebrat balíček aplikace po úspěšné registraci aplikace.  Odstranění balíčků aplikací z úložiště image store uvolnit systémové prostředky.  Udržování balíčky nepoužívané aplikace využívá diskové úložiště a vede k problémům s výkonem aplikací. Odstranit balíček aplikace z úložiště image pomocí [RemoveApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage) rozhraní API.

## <a name="create-an-application-instance"></a>Vytvoření instance aplikace
Můžete vytvořit instanci aplikace z jakéhokoli typu aplikace, který byl úspěšně zaregistrován pomocí [CreateApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync) rozhraní API. Název každé aplikace musí začínat *"prostředků infrastruktury:"* schéma a musí být jedinečná pro každou instanci aplikace (v rámci clusteru). Žádné výchozí služby definované v manifestu aplikace typ cílové aplikace se také vytvoří.

Několik instancí aplikace lze vytvořit danou verzi typu registrované aplikaci. Každá instance aplikace běží izolovaně, s vlastní pracovní adresář a sadu procesů.

Pokud chcete zobrazit, které s názvem aplikace a služby jsou spuštěny v clusteru, spusťte [GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync) a [GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync) rozhraní API.

## <a name="create-a-service-instance"></a>Vytvoření instance služby
Můžete vytvořit instanci služby pomocí typu služby [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) rozhraní API.  Pokud služba je deklarován jako výchozí služba v manifestu aplikace, služby je vytvořena, když je vytvořena instance aplikace.  Volání [CreateServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) vrátí výjimku typu FabricException obsahující kód chyby s hodnotou FabricErrorCode.ServiceAlreadyExists rozhraní API pro službu, která je již vytvořena instance.

## <a name="remove-a-service-instance"></a>Odebrání instance služby
Pokud instance služby je už nepotřebujete, můžete ho odebrat z běžící instance aplikace při volání [DeleteServiceAsync](/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync) rozhraní API.  

> [!WARNING]
> Tato operace je nevratná a stav služby nelze obnovit.

## <a name="remove-an-application-instance"></a>Odebrání instance aplikace
Když instance aplikace už je nepotřebujete, můžete trvale odebrat ji pomocí názvu [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) rozhraní API. [DeleteApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync) automaticky odstraní všechny služby, které patří k aplikaci stejně, trvale odebrat všechny služby stavu.

> [!WARNING]
> Tato operace je nevratná a nedá se obnovit stav aplikace.

## <a name="unregister-an-application-type"></a>Zrušte registraci typu aplikace
Pokud konkrétní verzi typu aplikace je už nepotřebujete, měli byste zrušit registraci této konkrétní verzi typu aplikace pomocí [Unregister-ServiceFabricApplicationType](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync) rozhraní API. Zrušení registrace nevyužité verze typů aplikace uvolní prostor úložiště využitý úložiště image store. Verze typu aplikace, můžete se zrušit registraci instance se vytvářejí aplikace nejsou pro tuto verzi typu aplikace a žádné upgrady čekající aplikace se odkazuje na tuto verzi typu aplikace.

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
<ClusterManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="Server-Default-SingleNode" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">

    [...]

    <Section Name="Management">
      <Parameter Name="ImageStoreConnectionString" Value="file:D:\ServiceFabric\Data\ImageStore" />
    </Section>

    [...]
```

Zobrazit [pochopit připojovací řetězec úložiště image](service-fabric-image-store-connection-string.md) doplňující informace o úložiště image store a image uložit připojovací řetězec.

### <a name="deploy-large-application-package"></a>Nasazení balíčku velké aplikace
Problém: [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) API vyprší časový limit pro velké aplikace balíčku (pořadí GB).
Zkuste:
- Zadejte většího časového limitu pro [CopyApplicationPackage](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage) metody s `timeout` parametru. Ve výchozím nastavení je časový limit 30 minut.
- Zkontrolujte síťové připojení mezi zdrojovým počítačem a clusteru. Pokud je pomalé připojení, zvažte použití na počítači s lepší síťové připojení.
Pokud klientský počítač je v jiné oblasti než cluster, zvažte použití klientského počítače v oblasti blíže nebo stejný jako cluster.
- Zkontrolujte, jestli nenarážíte externí omezení šířky pásma. Například pokud je úložiště image store je konfigurován pro použití služby azure storage, může omezení nahrávání.

Problém: Nahrání balíčku se nedokončil úspěšně, ale [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API vyprší časový limit. Zkuste:
- [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před zkopírováním do úložiště imagí.
Komprese snižuje velikost a počet souborů, která zase omezuje objem přenosů a pracovat v daných prostředcích služby musíte provést. Časový limit operace odeslání může být pomalejší (zejména pokud zahrnete komprese čas), ale jsou rychlejší registrace a zrušení registrace typu aplikace.
- Zadejte většího časového limitu pro [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) API s využitím `timeout` parametru.

### <a name="deploy-application-package-with-many-files"></a>Nasazení balíčku aplikace s velkým množstvím souborů
Problém: [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) vyprší časový limit pro balíček aplikace s velkým množstvím souborů (pořadí tisíc).
Zkuste:
- [Komprimovat balíček](service-fabric-package-apps.md#compress-a-package) před zkopírováním do úložiště imagí. Komprese snižuje počet souborů.
- Zadejte většího časového limitu pro [ProvisionApplicationAsync](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync) s `timeout` parametru.

## <a name="code-example"></a>Příklad kódu
Následující příklad zkopíruje balíček aplikace do úložiště imagí, zřídí typu aplikace, vytvoří instanci aplikace, vytvoří instanci služby, odebere instance aplikace, zrušení ustanovení typ aplikace a odstraní balíček aplikace z úložiště imagí.

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

[Diagnostika a řešení potíží služby Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Modelování aplikace v Service Fabric](service-fabric-application-model.md)

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-package-apps.md
[11]: service-fabric-application-upgrade.md
