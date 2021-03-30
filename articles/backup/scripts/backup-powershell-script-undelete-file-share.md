---
title: PowerShellový skript – zrušení odstranění odstraněné sdílené složky
description: Naučte se, jak pomocí skriptu Azure PowerShell obnovit omylem odstraněnou sdílenou složku.
ms.topic: sample
ms.date: 02/02/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 99cd980e13b3b9b9a7a450520218c76ccf26fd69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89079859"
---
# <a name="powershell-script-to-undelete-an-accidentally-deleted-file-share"></a>Skript PowerShellu pro zrušení odstranění omylem odstraněné sdílené složky

Tento skript vám pomůže obnovit sdílenou složku, pokud jste ji omylem odstranili. Funkce zabezpečení odstranit pro sdílené složky nabízí možnost zrušit odstranění sdílené složky během období uchování po dobu 14 dnů, což umožňuje obnovení obsahu sdílené složky, snímků a bodů obnovení. Další informace o obnovitelném odstranění najdete na tomto [odkazu](../soft-delete-azure-file-share.md).

## <a name="sample-script"></a>Ukázkový skript

```powershell
#Import-Module Az.Storage -MinimumVersion 1.7.0 -Scope Local
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $FileShareName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId,
        [Parameter(Mandatory=$False)][System.Boolean] $ListOption,
        [Parameter(Mandatory=$False)][System.String] $DeletedShareVersion
    )

Function Restore-DeletedFileShare
{
    Param(
        [Parameter(Mandatory=$True)][Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext] $Context,
        [Parameter(Mandatory=$True)][System.String] $FileShareName,
        [Parameter(Mandatory=$False)][System.String] $DeletedShareVersion
        )

    if ([string]::IsNullOrWhiteSpace($FileShareName))
    {
        Write-Error "Please specify the required input parameter: FileShareName" -ErrorAction Stop
    }

    $FileShareName = $FileShareName.ToLowerInvariant()

    Write-Verbose "Restoring a file share with the name: $FileShareName" -Verbose


    Write-Information -MessageData "Started: Creating SASToken to List File Shares" -InformationAction Continue

    $listToken = New-AzStorageAccountSASToken -Context $Context -Service File -ResourceType Service -Permission "l" -Protocol HttpsOrHttp -StartTime (Get-Date).AddHours(-1) -ExpiryTime (Get-Date).AddHours(1)

    Write-Information -MessageData "Completed: Creating SASToken to List File Shares" -InformationAction Continue

    Write-Information -MessageData "Started: Listing File Shares to find the deleted file share" -InformationAction Continue

    $listSharesUrl = [string]::Concat($Context.FileEndPoint, "?include=metadata,deleted&comp=list&api-version=2019-10-10&", $listToken.Substring(1))

    $listSharesResponse = Invoke-WebRequest $listSharesUrl -Method "GET" -Verbose

    if ($listSharesResponse.StatusCode -ne 200)
    {
        Write-Error "Request to list file shares failed." -ErrorAction Stop
    }

    Write-Verbose $listSharesResponse.RawContent -Verbose

    $listSharesResponseContent = $listSharesResponse.Content.Substring(3)

    Write-Information -MessageData "Completed: Listing File Shares to find the deleted file share" -InformationAction Continue

    Write-Information -MessageData "Started: Search for a deleted file share with the specified name" -InformationAction Continue

    $deletedFileShares = Select-Xml -Content $listSharesResponseContent -XPath "/EnumerationResults/Shares/Share[Deleted=""true"" and Name=""$FileShareName""]"

    $matchedCount = 0
    $deletedShareVersions = New-Object System.Collections.Generic.List[string]

    foreach($share in $deletedFileShares)
    {
        if($matchedCount -eq 0)
        {
          Write-Verbose $share.Node.InnerXml -Verbose

          Write-Information -MessageData "Completed: Search for a deleted file share with the specified name And Found versions" -InformationAction Continue
        }

        $shareVer = $share.Node.Item("Version").InnerText
        $shareDelTime = $share.Node.Item("Properties").Item("DeletedTime").InnerText
        $retDays = $share.Node.Item("Properties").Item("RemainingRetentionDays").InnerText

        $deletedShareVersions.Add($share.Node.Item("Version").InnerText)

        Write-Information -MessageData "DeletedVersion: $shareVer, DeletedTime: $shareDelTime, RemainingRetentionDays: $retDays"  -InformationAction Continue

        $matchedCount++
    }

    if($ListOption -eq $True)
    {
       return;
    }

    if ($matchedCount -eq 0)
    {
        Write-Error "Deleted file share with the specified name was not found." -ErrorAction Stop
    }
    elseif($matchedCount -eq 1 -and ([string]::IsNullOrWhiteSpace($DeletedShareVersion) -or $deletedShareVersions.Contains($DeletedShareVersion)))
    {
      $DeletedShareVersion = $deletedShareVersions
    }
    elseif ($matchedCount -gt 1)
    {
      if ([string]::IsNullOrWhiteSpace($DeletedShareVersion) -or !$deletedShareVersions.Contains($DeletedShareVersion))
      {
        Write-Error "More than one share with the specified name was found. Please specify a valid DeletedShareVersion parameter from above possible values." -ErrorAction Stop
      }
    }

    Write-Information -MessageData "Completed: Search for a deleted file share with the specified name And Found version: $DeletedShareVersion" -InformationAction Continue

    Write-Information -MessageData "Started: Creating SASToken to Restore File Share" -InformationAction Continue

    $restoreToken = New-AzStorageAccountSASToken -Context $Context -Service File -ResourceType Container -Permission "w" -Protocol HttpsOrHttp -StartTime (Get-Date).AddHours(-1) -ExpiryTime (Get-Date).AddHours(1)

    Write-Information -MessageData "Completed: Creating SASToken to Restore File Share" -InformationAction Continue

    Write-Information -MessageData "Started: Restore File Share" -InformationAction Continue

 $restoreShareUrl = [string]::Concat($Context.FileEndPoint, $FileShareName, "?restype=share&comp=undelete&api-version=2019-10-10&", $restoreToken.Substring(1))

    $restoreHeaders = @{"x-ms-deleted-share-name" = $FileShareName; "x-ms-deleted-share-version" = $DeletedShareVersion}

    $restoreResponse = Invoke-WebRequest $restoreShareUrl -Headers $restoreHeaders -Method "PUT" -Verbose

    if ($restoreResponse.StatusCode -ne 201)
    {
        Write-Error "Request to restore a file share failed." -ErrorAction Stop
    }

    Write-Verbose $restoreResponse.RawContent -Verbose

    Write-Information -MessageData "Completed: Restore File Share" -InformationAction Continue
}

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$sa = Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName


Restore-DeletedFileShare $sa.Context $FileShareName $DeletedShareVersion
```

## <a name="how-to-use-the-script-in-different-scenarios"></a>Jak používat skript v různých scénářích

### <a name="prerequisites"></a>Požadavky

1. Před spuštěním skriptu nainstalujte nejnovější Azure PowerShell AZ modules from [tohoto odkazu](/powershell/azure/install-az-ps) .
2. Následující podrobnosti si ponechte, protože je budete muset předat jako hodnoty pro různé parametry skriptu:

    * **-SubscriptionId** -ID předplatného, kde se nachází sdílená složka.
    * **-ResourceGroupName** -skupina prostředků účtu úložiště, který hostuje sdílenou složku.
    * **-StorageAccountName** – název účtu úložiště, který hostuje sdílenou složku.
    * **-Název sdíleného** souboru sdílené složky, která se má obnovit

### <a name="execution-steps"></a>Kroky provedení

1. Uložte skript výše na svém počítači s názvem, který jste vybrali. V tomto příkladu jsme ho uložili jako *Undelete.ps1*
2. Spusťte skript podle scénáře, který vyhovuje vašim požadavkům.

#### <a name="scenario-1"></a>Scénář 1

Neexistují žádné více odstraněných verzí se stejným názvem, jako má sdílená složka, kterou se pokoušíte obnovit.

Následující příklad odstraní sdílenou složku *share1* , která se nachází v účtu úložiště *afsshare*.

```powershell
   .\UnDelete.ps1 -ResourceGroupName afsshare -StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1
```

Výstup by měl zobrazovat zprávu `Completed:Restore File Share`

#### <a name="scenario-2"></a>Scénář 2

Existuje několik odstraněných verzí se stejným názvem, jako má sdílená složka, kterou se pokoušíte obnovit.

V následujícím příkladu se odstraní verze sdílené složky *share1*

##### <a name="step-1"></a>Krok 1

Spusťte skript následujícím způsobem zadáním názvu sdílené složky.

```PowerShell
   .\UnDelete.ps1 -ResourceGroupName afsshare -StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1
```

```Output
Completed: Search for a deleted file share with the specified name and Found versions
DeletedVersion: 01D5D7F77ACC7864, DeletedTime: Fri, 31 Jan 2020 05:30:33 GMT, RemainingRetentionDays: 14
DeletedVersion: 01D5D7F7A76CAF42, DeletedTime: Fri, 31 Jan 2020 05:31:25 GMT, RemainingRetentionDays: 14
Restore-DeletedFileShare : More than one share with the specified name was found. Please specify a valid DeletedShareVersion parameter from above possible values.
```

##### <a name="step-2"></a>Krok 2

Vyberte verzi z výstupu kroku 1, kterou chcete obnovit, a předejte ji jako hodnotu pro parametr **-DeletedShareVersion** .

Následující příklad odstraní verzi *01D5D7F77ACC7864* sdílené složky *share1* .

```powershell
   .\UnDelete.ps1 -ResourceGroupName afsshare-StorageAccountName afsshare -SubscriptionId f75d8d8b-6735-4697-82e1-1a7a3ff0d5d4 -FileShareName share1 -DeletedShareVersion 01D5D7F77ACC7864
```
