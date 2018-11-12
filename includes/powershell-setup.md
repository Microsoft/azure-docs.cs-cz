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
ms.openlocfilehash: b96e8e6e31817f6d261f41dbf3b3047dd49c29ba
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258241"
---
## <a name="setting-up-powershell"></a>Nastavení prostředí PowerShell
Předtím, než budete moct použít Azure PowerShell, postupujte podle těchto kroků.

### <a name="verify-powershell-versions"></a>Ověření verze prostředí PowerShell
Než budete moct použít prostředí Windows PowerShell, musíte mít Windows PowerShell, verze 3.0 nebo 4.0. Pokud chcete zjistit verzi prostředí Windows PowerShell, zadejte tento příkaz na příkazovém řádku prostředí Windows PowerShell.

    $PSVersionTable

By měl vypadat přibližně takto.

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

Ověřte, že hodnota **PSVersion** 3.0 nebo 4.0. Pokud chcete nainstalovat kompatibilní verze, najdete v článku [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) nebo [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

Měli byste také Azure PowerShell verze 0.8.0 nebo novější. Můžete zkontrolovat verzi Azure Powershellu, který jste nainstalovali pomocí tohoto příkazu na příkazovém řádku prostředí Azure PowerShell.

    Get-Module azure | format-table version

By měl vypadat přibližně takto.

    Version
    -------
    0.8.16.1

Pokyny a odkaz na nejnovější verzi najdete v tématu [instalace a konfigurace prostředí Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="set-your-azure-account-and-subscription"></a>Nastavení předplatného a účtu Azure
Pokud ještě nemáte předplatné Azure, můžete si aktivovat váš [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo se zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

Otevřete příkazový řádek Azure Powershellu a přihlaste se k Azure pomocí tohoto příkazu.

    Add-AzureAccount

Pokud máte více předplatných Azure, můžete vytvořit seznam vašich předplatných Azure pomocí tohoto příkazu.

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

Spuštěním těchto příkazů na příkazovém řádku prostředí Azure PowerShell můžete nastavit aktuální předplatné Azure. Nahradit vše, co v uvozovkách, včetně < a > znaků, ten se správným názvem.

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Další informace o účtů a předplatných Azure, najdete v části [postupy: připojení k vašemu předplatnému](/powershell/azureps-cmdlets-docs#Connect).

