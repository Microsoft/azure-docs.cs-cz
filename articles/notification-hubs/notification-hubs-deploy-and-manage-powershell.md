---
title: Nasazení a správa Notification Hubs pomocí PowerShellu
description: Jak vytvořit a spravovat pomocí prostředí PowerShell pro automatizaci centra oznámení
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
ms.openlocfilehash: d2350d8021925278d6362c8227d408476a569319
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Nasazení a správa Notification Hubs pomocí PowerShellu
## <a name="overview"></a>Přehled
Tento článek ukazuje, jak používat vytvořit a spravovat Azure Notification Hubs pomocí prostředí PowerShell. V tomto tématu jsou uvedeny následující běžné úlohy automatizace.

* Vytvoření centra oznámení
* Nastavení přihlašovacích údajů

Pokud potřebujete vytvořit nový obor názvů sběrnice služby pro vaše centra oznámení, přečtěte si téma [spravovat služby Service Bus pomocí prostředí PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Správa centra oznámení není podporována přímo pomocí rutin obsažených v prostředí Azure PowerShell. Nejlepším postupem z prostředí PowerShell se tak, aby odkazovaly Microsoft.Azure.NotificationHubs.dll sestavení. Sestavení je distribuován s [balíček Microsoft Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Azure je platforma, na základě předplatného. Další informace o získání předplatného najdete v tématu [možnostech nákupu], [nabízí člen], nebo [bezplatné zkušební verze].
* Počítač s prostředím Azure PowerShell. Pokyny najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell].
* Obecné znalosti skriptů prostředí PowerShell, balíčků NuGet a rozhraní .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Včetně odkaz na sestavení rozhraní .NET pro Service Bus
Správa Azure Notification Hubs ještě není součástí rutin prostředí PowerShell v prostředí Azure PowerShell. Pokud chcete zřídit centra oznámení, můžete použít klient .NET, které jsou součástí [balíček Microsoft Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Nejprve zkontrolujte, zda váš skript můžete najít **Microsoft.Azure.NotificationHubs.dll** sestavení, která je nainstalována jako balíčku NuGet v projektu sady Visual Studio. Chcete-li být flexibilní, skript provede tyto kroky:

1. Určuje cestu, kde byla vyvolána.
2. Prochází cestu, dokud nenajde složku s názvem `packages`. Tato složka se vytvoří při instalaci balíčků NuGet pro projekty v sadě Visual Studio.
3. Rekurzivní hledání `packages` složku pro sestavení s názvem **Microsoft.Azure.NotificationHubs.dll**.
4. Odkazuje na sestavení tak, aby typy jsou k dispozici pro pozdější použití.

Zde je, jak tyto kroky jsou implementované ve skriptu prostředí PowerShell:

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

## <a name="create-the-namespacemanager-class"></a>Vytvořte třídu NamespaceManager
Pokud chcete zřídit Notification Hubs, vytvořte instanci [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.namespacemanager.aspx) třída ze sady SDK. 

Můžete použít [Get-AzureSBAuthorizationRule] rutiny zahrnuté do prostředí Azure PowerShell k načtení autorizační pravidlo, které slouží k poskytování připojovací řetězec. Odkaz na `NamespaceManager` instance je uložen v `$NamespaceManager` proměnné. `$NamespaceManager` slouží ke zřízení centra oznámení.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```


## <a name="provisioning-a-new-notification-hub"></a>Zřizování nového centra oznámení
Chcete-li zřídit nového centra oznámení, použijte [rozhraní API .NET pro centra oznámení].

V této části skriptu nastavíte čtyři lokální proměnné. 

1. `$Namespace` : Nastavením název oboru názvů, kde chcete vytvořit centrum oznámení.
2. `$Path` : Nastavte tuto cestu na název nového centra oznámení.  Například "MyHub".    
3. `$WnsPackageSid` : Tuto možnost nastavíte na identifikátor SID pro aplikace pro Windows balíčku z [Centrum vývojářů pro Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).
4. `$WnsSecretkey`: Tuto možnost nastavíte na tajný klíč pro aplikace pro Windows z [Centrum vývojářů pro Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409).

Tyto proměnné se používají k připojení k oboru názvů a vytvoření nového centra oznámení pro zpracování oznámení služby oznámení Windows (WNS) s přihlašovacími údaji WNS pro aplikaci systému Windows nakonfigurována. Informace o získání balíčku SID a tajný klíč najdete [Začínáme s Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kurzu. 

* Fragment skriptu používá `NamespaceManager` objekt, který chcete zkontrolovat, pokud centra oznámení identifikovaný `$Path` existuje.
* Pokud neexistuje, vytvoří skript `NotificationHubDescription` s WNS přihlašovací údaje a předejte to `NamespaceManager` třída `CreateNotificationHub` metoda.

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
* [Správa služby Service Bus pomocí prostředí PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
* [Postup vytvoření fronty, témata a odběry pomocí skriptu prostředí PowerShell služby Service Bus](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Postup vytvoření Namespace Service Bus a centra událostí pomocí skriptu prostředí PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Některé připravených skripty jsou také k dispozici ke stažení:

* [Skripty prostředí PowerShell služby Service Bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[možnostech nákupu]: http://azure.microsoft.com/pricing/purchase-options/
[nabízí člen]: http://azure.microsoft.com/pricing/member-offers/
[bezplatné zkušební verze]: http://azure.microsoft.com/pricing/free-trial/
[nainstalovat a nakonfigurovat Azure PowerShell]: /powershell/azureps-cmdlets-docs
[rozhraní API .NET pro centra oznámení]: https://msdn.microsoft.com/library/azure/mt414893.aspx
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx

