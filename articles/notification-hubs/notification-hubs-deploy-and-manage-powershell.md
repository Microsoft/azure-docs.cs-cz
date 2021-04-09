---
title: Nasazení a Správa Notification Hubs pomocí prostředí PowerShell
description: Vytvoření a Správa Notification Hubs pomocí prostředí PowerShell pro automatizaci
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
ms.openlocfilehash: 4534584144f54618d7f3dd39cf5e40bc0464fb21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454981"
---
# <a name="deploy-and-manage-notification-hubs-using-powershell"></a>Nasazení a Správa centra oznámení pomocí PowerShellu

## <a name="overview"></a>Přehled

V tomto článku se dozvíte, jak pomocí PowerShellu vytvořit a spravovat Azure Notification Hubs. V tomto článku jsou uvedené následující běžné úlohy automatizace.

- Vytvoření centra oznámení
- Nastavit přihlašovací údaje

Pokud také potřebujete vytvořit nový obor názvů služby Service Bus pro vaše centra oznámení, přečtěte si téma [správa Service Bus pomocí PowerShellu](../service-bus-messaging/service-bus-manage-with-ps.md).

Správa Center oznámení není podporovaná přímo rutinami, které jsou součástí Azure PowerShell. Nejlepším přístupem z PowerShellu je odkazování na sestavení Microsoft.Azure.NotificationHubs.dll. Sestavení je distribuováno pomocí [Microsoft Azure Notification Hubs balíčku NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Azure je platforma založená na předplatném. Další informace o získání předplatného najdete v tématu [možnosti nákupu], [nabídky členů]nebo [bezplatné zkušební verze].
- Počítač se Azure PowerShell. Pokyny najdete v tématu [instalace a konfigurace Azure PowerShell].
- Obecné porozumění skriptům PowerShellu, balíčkům NuGet a .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Zahrnutí odkazu na sestavení .NET pro Service Bus

Správa Azure Notification Hubs ještě není součástí rutin PowerShellu v Azure PowerShell. Pokud chcete zřídit centra oznámení, můžete použít klienta .NET, který je k dispozici v [balíčku Microsoft Azure Notification Hubs NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Nejprve zajistěte, aby váš skript mohl najít **Microsoft.Azure.NotificationHubs.dll** sestavení, které je nainstalováno jako balíček NuGet v projektu sady Visual Studio. Aby bylo možné tento skript flexibilní, provede následující kroky:

1. Určuje cestu, ve které byla vyvolána.
2. Projde cestu, dokud nenajde složku s názvem `packages` . Tato složka se vytvoří při instalaci balíčků NuGet pro projekty sady Visual Studio.
3. Rekurzivně vyhledá `packages` složku pro sestavení s názvem `Microsoft.Azure.NotificationHubs.dll` .
4. Odkazuje na sestavení tak, aby byly typy k dispozici pro pozdější použití.

Tady je postup implementace těchto kroků ve skriptu PowerShellu:

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

Chcete-li zřídit Notification Hubs, vytvořte instanci třídy [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ze sady SDK.

Pomocí rutiny [Get-AzureSBAuthorizationRule] , která je součástí nástroje Azure PowerShell, můžete načíst autorizační pravidlo, které slouží k zadání připojovacího řetězce. Odkaz na `NamespaceManager` instanci je uložen v `$NamespaceManager` proměnné. `$NamespaceManager` slouží ke zřízení Centra oznámení.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager=[Microsoft.Azure.NotificationHubs.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-a-new-notification-hub"></a>Zřizování nového centra oznámení

K zřízení nového centra oznámení použijte rozhraní [.NET API pro Notification Hubs].

V této části skriptu nastavíte čtyři místní proměnné.

1. `$Namespace`: Nastavte na název oboru názvů, ve kterém chcete vytvořit centrum oznámení.
2. `$Path`: Nastavte tuto cestu na název nového centra oznámení.  Například "MyHub".
3. `$WnsPackageSid`: Toto nastavte na SID balíčku aplikace pro Windows z [Windows Dev Center](https://developer.microsoft.com/en-us/windows).
4. `$WnsSecretkey`: Nastavte tajný klíč pro aplikaci pro Windows z [Windows Dev Center](https://developer.microsoft.com/en-us/windows).

Tyto proměnné se používají pro připojení k vašemu oboru názvů a vytvoření nového centra oznámení nakonfigurovaného pro zpracování oznámení služby Windows Notification Services (WNS) s přihlašovacími údaji WNS pro aplikaci pro Windows. Informace o získání identifikátoru SID balíčku a tajného klíče najdete v kurzu [Začínáme Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) .

- Fragment skriptu používá `NamespaceManager` objekt ke kontrole, zda centrum oznámení identifikované `$Path` existuje.
- Pokud neexistuje, skript vytvoří `NotificationHubDescription` s přihlašovacími údaji WNS a předá ho `NamespaceManager` `CreateNotificationHub` metodě třídy.

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

## <a name="additional-resources"></a>Další materiály

- [Správa služby Service Bus pomocí PowerShellu](../service-bus-messaging/service-bus-manage-with-ps.md)
- [Postup vytvoření Service Bus front, témat a odběrů pomocí skriptu PowerShellu](/archive/blogs/paolos/how-to-create-service-bus-queues-topics-and-subscriptions-using-a-powershell-script)
- [Postup vytvoření oboru názvů Service Bus a centra událostí pomocí skriptu PowerShellu](/archive/blogs/paolos/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script)

Některé připravené skripty jsou k dispozici také ke stažení:

- [Service Bus skripty PowerShellu](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

[Možnosti nákupu]: https://azure.microsoft.com/pricing/purchase-options/
[Nabídky členů]: https://azure.microsoft.com/pricing/member-offers/
[Bezplatná zkušební verze]: https://azure.microsoft.com/pricing/free-trial/
[Instalace a konfigurace Azure PowerShell]: /powershell/azure/
[Rozhraní .NET API pro Notification Hubs]: /dotnet/api/overview/azure/notification-hubs
[Get-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/get-azuresbnamespace
[New-AzureSBNamespace]: /powershell/module/servicemanagement/azure.service/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: /powershell/module/servicemanagement/azure.service/get-azuresbauthorizationrule
