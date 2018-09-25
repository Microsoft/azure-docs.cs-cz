---
title: Získání hodnot pro ověřování pomocí služby app - Azure SQL Database | Dokumentace Microsoftu
description: Vytvoření instančního objektu pro přístup k SQL Database z kódu.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: f9b66a430e1ca5b24853a5ff31ce13393b12b0c5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063766"
---
# <a name="get-the-required-values-for-authenticating-an-application-to-access-sql-database-from-code"></a>Získání požadovaných hodnot pro ověření aplikace pro přístup k SQL Database z kódu
Vytvoření a Správa služby SQL Database z kódu je nutné zaregistrovat aplikaci v doméně Azure Active Directory (AAD) v rámci předplatného, ve kterém byly vytvořeny prostředky Azure.

## <a name="create-a-service-principal-to-access-resources-from-an-application"></a>Vytvořit instanční objekt pro přístup k prostředkům z aplikace
Je potřeba mít na nejnovější verzi [prostředí Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) nainstalovaná a spuštěná. Podrobné informace najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs).

Následující skript prostředí PowerShell vytvoří aplikaci Active Directory (AD) a instanční objekt, který potřebujeme k ověření naší aplikace v jazyce C#. Skript vypíše hodnoty potřebné pro předchozí ukázku v jazyce C#. Podrobné informace najdete v tématu [Vytvoření instančního objektu pro přístup k prostředkům pomocí prostředí Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Connect-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvoření databáze SQL pomocí C#](sql-database-get-started-csharp.md)
* [Připojení k SQL Database pomocí ověřování Azure Active Directory](sql-database-aad-authentication.md)

