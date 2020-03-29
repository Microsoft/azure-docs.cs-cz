---
title: Nasazení a správa center oznámení pomocí PowerShellu
description: Jak vytvořit a spravovat centra oznámení pomocí PowerShellu pro automatizaci
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 7c58f2c8-0399-42bc-9e1e-a7f073426451
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 863fdb445cce41f0fe4cbee63a3d6198c0a79339
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264640"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Nasazení a správa center oznámení pomocí PowerShellu

## <a name="overview"></a>Přehled

Tento článek ukazuje, jak používat vytvořit a spravovat centra oznámení Azure pomocí PowerShellu. Následující běžné úlohy automatizace jsou uvedeny v tomto článku.

- Vytvoření centra oznámení
- Nastavit přihlašovací údaje

Pokud potřebujete také vytvořit nový obor názvů služby Service Bus pro centra oznámení, přečtěte si informace [o správě service busu pomocí prostředí PowerShell](../service-bus-messaging/service-bus-powershell-how-to-provision.md).

Správa centra oznámení není podporována přímo rutiny součástí Azure PowerShell. Nejlepší přístup z Prostředí PowerShell je odkazovat na sestavení Microsoft.Azure.NotificationHubs.dll. Sestavení je distribuováno s [balíčkem Microsoft Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Azure je platforma založená na předplatném. Další informace o získání předplatného naleznete v [tématech Možnosti nákupu], [Členské nabídky]nebo [Bezplatná zkušební verze].
- Počítač s Azure PowerShellem. Pokyny najdete v [tématu Instalace a konfigurace Azure PowerShellu].
- Obecné pochopení skriptů prostředí PowerShell, balíčků NuGet a rozhraní .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Včetně odkazu na sestavení .NET pro service bus

Správa Center oznámení Azure ještě není součástí rutin PowerShellu v Azure PowerShellu. Chcete-li zřídit centra oznámení, můžete použít klienta .NET uvedené v [balíčku Microsoft Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Nejprve se ujistěte, že váš skript můžete najít sestavení **Microsoft.Azure.NotificationHubs.dll,** který je nainstalován jako balíček NuGet v projektu Visual Studio. Chcete-li být flexibilní, skript provádí tyto kroky:

1. Určuje cestu, ve které byla vyvolána.
2. Projíždí cestou, dokud `packages`nenajde složku s názvem . Tato složka je vytvořena při instalaci balíčků NuGet pro projekty sady Visual Studio.
3. Rekurzivně hledá `packages` ve složce `Microsoft.Azure.NotificationHubs.dll`sestavení s názvem .
4. Odkazuje na sestavení tak, aby typy jsou k dispozici pro pozdější použití.

Tyto kroky se implementují ve skriptu Prostředí PowerShell takto:

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

## <a name="create-the-namespacemanager-class"></a>Vytvoření `NamespaceManager` třídy

Chcete-li zřídit centra oznámení, vytvořte instanci třídy [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager?view=azure-dotnet) ze sady SDK.

Rutina [Get-AzureSBAuthorizationRule,] která je součástí prostředí Azure PowerShell, můžete použít k načtení autorizačního pravidla, které se používá k poskytnutí připojovacího řetězce. Odkaz na `NamespaceManager` instanci je `$NamespaceManager` uložen v proměnné. `$NamespaceManager`se používá k zřízení centra oznámení.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Zřizování nového centra oznámení

Chcete-li zřídit nové centrum oznámení, použijte [rozhraní .NET API pro centra oznámení].

V této části skriptu nastavíte čtyři místní proměnné.

1. `$Namespace`: Nastavte to na název oboru názvů, ve kterém chcete vytvořit centrum oznámení.
2. `$Path`: Nastavte tuto cestu na název nového centra oznámení.  Například "MyHub".
3. `$WnsPackageSid`: Nastavte tuto položku na balíček SID aplikace pro Windows z [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Nastavte tento klíč na tajný klíč aplikace pro Windows z [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Tyto proměnné se používají k připojení k oboru názvů a k vytvoření nového centra oznámení nakonfigurovaného pro zpracování oznámení služby Windows Notification Services (WNS) s pověřeními služby WNS pro aplikaci pro Windows. Informace o získání balíčku SID a tajný klíč naleznete v tématu [Začínáme s oznámení centra](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) kurzu.

- Fragment skriptu `NamespaceManager` používá objekt ke kontrole, zda `$Path` existuje centrum oznámení identifikované.
- Pokud neexistuje, skript vytvoří `NotificationHubDescription` s pověřeními WNS a `NamespaceManager` předá ji metodě třídy. `CreateNotificationHub`

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

## <a name="additional-resources"></a>Další zdroje

- [Správa služby Service Bus pomocí PowerShellu](../service-bus-messaging/service-bus-powershell-how-to-provision.md)
- [Jak vytvořit fronty, témata a odběry služby Service Bus pomocí skriptu PowerShellu](https://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Jak vytvořit obor názvů service bus a centrum událostí pomocí skriptu Prostředí PowerShell](https://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Některé ready-made skripty jsou také k dispozici ke stažení:

- [Skripty prostředí PowerShell service bus](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Možnosti nákupu]: https://azure.microsoft.com/pricing/purchase-options/
[Členské nabídky]: https://azure.microsoft.com/pricing/member-offers/
[Zkušební verze zdarma]: https://azure.microsoft.com/pricing/free-trial/
[Instalace a konfigurace Azure PowerShellu]: /powershell/azureps-cmdlets-docs
[Rozhraní .NET API pro centra oznámení]: https://docs.microsoft.com/dotnet/api/overview/azure/notification-hubs?view=azure-dotnet
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbnamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azuresbauthorizationrule
