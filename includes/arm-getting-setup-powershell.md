---
author: sdwheeler
ms.service: azure-powershell
ms.topic: include
ms.date: 11/09/2018
ms.author: sewhee
ms.openlocfilehash: b93722147959881dfbffbb47b7a3e4b140044525
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56246793"
---
## <a name="setting-up-powershell-for-resource-manager-templates"></a>Nastavení prostředí PowerShell pro šablony Resource Manageru
Před použitím Azure Powershellu s Resource Managerem, musíte mít práva prostředí Windows PowerShell a verze prostředí Azure PowerShell.

### <a name="verify-powershell-versions"></a>Ověření verze prostředí PowerShell
Ověřte, že máte prostředí Windows PowerShell verze 3.0 nebo 4.0. Pokud chcete zjistit verzi prostředí Windows PowerShell, zadejte tento příkaz na příkazovém řádku prostředí Windows PowerShell.

    $PSVersionTable

Získáte tento typ informací:

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2


Ověřte, že hodnota **PSVersion** 3.0 nebo 4.0. Pokud ne, přečtěte si téma [Windows Management Framework 3.0](https://www.microsoft.com/download/details.aspx?id=34595) nebo [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).

### <a name="set-your-azure-account-and-subscription"></a>Nastavení předplatného a účtu Azure
Pokud ještě nemáte předplatné Azure, můžete si aktivovat váš [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo se zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

Otevřete příkazový řádek Azure Powershellu a přihlaste se k Azure pomocí tohoto příkazu.

    Connect-AzureRmAccount

Pokud máte více předplatných Azure, můžete vytvořit seznam vašich předplatných Azure pomocí tohoto příkazu.

    Get-AzureRmSubscription

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

    $subscr="<SubscriptionName from the display of Get-AzureRmSubscription>"
    Select-AzureRmSubscription -SubscriptionName $subscr -Current

Další informace o účtů a předplatných Azure, najdete v části [jak: Připojení k vašemu předplatnému](/powershell/azureps-cmdlets-docs).

