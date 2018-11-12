---
title: Nasazení a správa Notification Hubs pomocí PowerShellu
description: Vytvoření a správa Notification Hubs pomocí Powershellu pro službu Automation
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 7107c4ebd3f7df5966dd4c66aa8f453bd4f3d1d6
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228347"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Nasazení a správa Notification Hubs pomocí PowerShellu
## <a name="overview"></a>Přehled
Tento článek ukazuje, jak vytvořit a spravovat Azure Notification Hubs pomocí Powershellu. V tomto článku jsou uvedeny následující běžné úlohy automatizace.

* Vytvoření centra oznámení
* Nastavení přihlašovacích údajů

Pokud potřebujete vytvořit nový obor názvů sběrnice služby notification hubs, přečtěte si téma [Správa služby Service Bus pomocí Powershellu](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Správa centra oznámení nepodporuje přímo rutin obsažených v prostředí Azure PowerShell. Nejlepším postupem z Powershellu se odkazovat na sestavení Microsoft.Azure.NotificationHubs.dll. Sestavení je distribuován spolu s [balíček Microsoft Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Azure je platforma založená na předplatném. Další informace o získání předplatného najdete v tématu [možnosti nákupu], [nabídky pro členy], nebo [Bezplatná zkušební verze].
* Počítače s prostředím Azure PowerShell. Pokyny najdete v tématu [Nainstalujte a nakonfigurujte Azure PowerShell].
* Obecné principy skripty prostředí PowerShell, balíčky NuGet a rozhraní .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Zahrnutí odkazu na sestavení .NET pro Service Bus
Správa Azure Notification Hubs zatím není součástí pomocí rutin prostředí PowerShell v prostředí Azure PowerShell. Ke zřízení notification hubs, můžete použít klienta .NET součástí [balíček Microsoft Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Nejprve zkontrolujte, zda váš skript můžete najít **Microsoft.Azure.NotificationHubs.dll** sestavení, který je nainstalován jako balíček NuGet v projektu sady Visual Studio. Aby bylo možné flexibilně, že skript provádí tyto kroky:

1. Určuje cestu, ve kterém byla vyvolána.
2. Vlastní cestu, dokud nenajde složku s názvem `packages`. Tato složka se vytvoří při instalaci balíčků NuGet pro projekty aplikace Visual Studio.
3. Vyhledá rekurzivně `packages` složku pro sestavení s názvem **Microsoft.Azure.NotificationHubs.dll**.
4. Odkazuje na sestavení tak, že typy jsou k dispozici pro pozdější použití.

Zde je, jak tyto kroky jsou implementovány v skriptu prostředí PowerShell:

``` powershell

try
{
    # WARNING: Make sure to reference the latest version of Microsoft.Azure.NotificationHubs.dll
    Write-Output "Adding the [Microsoft.Azure.NotificationHubs.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.Azure.NotificationHubs.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.Azure.NotificationHubs.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.Azure.NotificationHubs.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}
```

## <a name="create-the-namespacemanager-class"></a>Vytvoření třídě NamespaceManager class
Ke zřízení Notification Hubs, vytvoření instance [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) třídy ze sady SDK. 

Můžete použít [Get-AzureSBAuthorizationRule] rutiny zahrnuté v prostředí Azure PowerShell k načtení autorizační pravidlo, které slouží k poskytování připojovací řetězec. Odkaz na `NamespaceManager` instance je uložen v `$NamespaceManager` proměnné. `$NamespaceManager` slouží ke zřízení na centrum oznámení.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Zřízení nového centra oznámení
Chcete-li zřídit nového centra oznámení, použijte [rozhraní .NET API pro Notification Hubs].

V této části souboru, který nastavíte čtyři místní proměnné. 

1. `$Namespace`: Nastavte na název oboru názvů, ve kterém chcete vytvořit centrum oznámení.
2. `$Path`: Nastavte tuto cestu na název nového centra oznámení.  Například "MyHub".    
3. `$WnsPackageSid`: Nastavte na balíček SID pro aplikace pro Windows z [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Nastavte na tajný klíč pro aplikace Windows z [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Tyto proměnné se používají k připojení do svého oboru názvů a vytvoření nového centra oznámení nakonfigurovaný pro zpracování oznámení služby oznámení Windows (WNS) s přihlašovacími údaji služby nabízených oznámení Windows pro aplikace Windows. Informace o získání balíček SID a tajný klíč najdete [Začínáme se službou Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kurzu. 

* Fragment kódu skriptu používá `NamespaceManager` objektu, který chcete zkontrolovat, pokud v centru oznámení identifikovaný `$Path` existuje.
* Pokud neexistuje, vytvoří skript `NotificationHubDescription` službou WNS přihlašovací údaje a předává jej do `NamespaceManager` třídy `CreateNotificationHub` metody.

``` powershell

$Namespace = "<Enter your namespace>"
$Path  = "<Enter a name for your notification hub>"
$WnsPackageSid = "<your package sid>"
$WnsSecretkey = "<enter your secret key>"

$WnsCredential = New-Object -TypeName Microsoft.Azure.NotificationHubs.WnsCredential -ArgumentList $WnsPackageSid,$WnsSecretkey

# Query the namespace
$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

# Check if the namespace already exists
if ($CurrentNamespace)
{
    Write-Output "The namespace [$Namespace] in the [$($CurrentNamespace.Region)] region was found."

    # Create the NamespaceManager object used to create a new notification hub
    $sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
    Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
    $NamespaceManager = [Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
    Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."

    # Check to see if the Notification Hub already exists
    if ($NamespaceManager.NotificationHubExists($Path))
    {
        Write-Output "The [$Path] notification hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Output "Creating the [$Path] notification hub in the [$Namespace] namespace."
        $NHDescription = New-Object -TypeName Microsoft.Azure.NotificationHubs.NotificationHubDescription -ArgumentList $Path;
        $NHDescription.WnsCredential = $WnsCredential;
        $NamespaceManager.CreateNotificationHub($NHDescription);
        Write-Output "The [$Path] notification hub was created in the [$Namespace] namespace."
    }
}
else
{
    Write-Host "The [$Namespace] namespace does not exist."
}
```




## <a name="additional-resources"></a>Další prostředky
* [Správa služby Service Bus pomocí PowerShellu](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
* [Vytvoření fronty, témata a odběry pomocí skriptu prostředí PowerShell služby Service Bus](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Jak vytvořit Namespace služby Service Bus a centra událostí pomocí skriptu prostředí PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Některé předdefinované skripty jsou také k dispozici ke stažení:

* [Skripty prostředí PowerShell služby Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Možnosti nákupu]: http://azure.microsoft.com/pricing/purchase-options/
[Nabídky pro členy]: http://azure.microsoft.com/pricing/member-offers/
[Bezplatná zkušební verze]: http://azure.microsoft.com/pricing/free-trial/
[Nainstalujte a nakonfigurujte Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Rozhraní .NET API pro Notification Hubs]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule

