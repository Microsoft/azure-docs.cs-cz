---
services: virtual-machines
title: Nastavení prostředí PowerShell
author: JoeDavies-MSFT
solutions: ''
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
ms.openlocfilehash: be5089b825a873006696eb074ed4355f415e680b
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319521"
---
## <a name="setting-up-powershell"></a>Nastavení prostředí PowerShell
Než budete moct použít Azure PowerShell, postupujte podle těchto kroků.

### <a name="verify-powershell-versions"></a>Ověření verze prostředí PowerShell
Než budete moct použít prostředí Windows PowerShell, musíte mít Windows PowerShell, verze 3.0 nebo 4.0. Pokud chcete vyhledat verzi prostředí Windows PowerShell, zadejte tento příkaz na příkazovém řádku prostředí Windows PowerShell.

    $PSVersionTable

Měli byste vidět zhruba takhle.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Ověřte, zda hodnota **PSVersion** je 3.0 nebo 4.0. K instalaci kompatibilní verze, najdete v části [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) nebo [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855).

Měli byste také mít prostředí Azure PowerShell verze 0.8.0 nebo novější. Můžete zkontrolovat verzi prostředí Azure PowerShell, který jste nainstalovali pomocí tohoto příkazu na příkazovém řádku prostředí Azure PowerShell.

    Get-Module azure | format-table version

Měli byste vidět zhruba takhle.

    Version
    -------
    0.8.16.1

Pokyny a odkaz na nejnovější verzi naleznete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Nastavení předplatného a účtu Azure
Pokud nemáte předplatné Azure, můžete si aktivovat vaší [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

Otevřete příkazový řádek prostředí Azure PowerShell a přihlášení do Azure pomocí tohoto příkazu.

    Add-AzureAccount

Pokud máte víc předplatných Azure, můžete seznam vašich předplatných Azure pomocí tohoto příkazu.

    Get-AzureSubscription

Získáte tento typ informací:

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Aktuální předplatné Azure můžete nastavit tak, že spustíte tyto příkazy v příkazovém řádku prostředí Azure PowerShell. Nahraďte všechna data v uvozovkách, včetně < a > znaků, se správným názvem.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Další informace o předplatných Azure a účty, najdete v části [postupy: připojení k vašemu předplatnému](/powershell/azureps-cmdlets-docs#Connect).

