---
title: Ukázkové skripty PowerShellu
description: Příklady, které ukazují, jak používat front-end prostřednictvím skriptů PowerShellu
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 53f09dd042543ebc433e9d9cbbd65db332e8fb6d
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518207"
---
# <a name="example-powershell-scripts"></a>Ukázkové skripty PowerShellu

Vzdálené vykreslování Azure poskytuje následující dvě rozhraní REST API:

- [REST API převodu](../how-tos/conversion/conversion-rest-api.md)
- [REST API relace](../how-tos/session-rest-api.md)

[Úložiště ukázek ARR](https://github.com/Azure/azure-remote-rendering) obsahuje ukázkové skripty ve složce *Scripts* pro interakci s rozhraními API REST služby. Tento článek popisuje jejich použití.

> [!TIP]
> Pro interakci se službou je k dispozici také [nástroj založený na uživatelském rozhraní s názvem ARRT](azure-remote-rendering-asset-tool.md) , což je pohodlnější alternativa k používání skriptů. ![ARRT](./media/azure-remote-rendering-asset-tool.png "Snímek obrazovky ARRT")

> [!CAUTION]
> Příliš časté volání funkcí REST API způsobí, že server bude omezovat a vracet chybu nakonec. ID kódu chyby HTTP v tomto případě je 429 (příliš mnoho požadavků). Jako pravidlo pro palec by se měla **mezi následnými voláními čekat 5-10 sekund**.

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit ukázkové skripty, budete potřebovat instalaci funkcí [Azure PowerShell](/powershell/azure/).

1. Nainstalujte Azure PowerShell:
    1. Otevřete okno PowerShellu s právy správce.
    1. Spouštěl `Install-Module -Name Az -AllowClobber`

1. Pokud se zobrazí chyby týkající se spouštění skriptů, ujistěte se, že jsou správně nastavené [zásady spouštění](/powershell/module/microsoft.powershell.core/about/about_execution_policies) :
    1. Otevřete okno PowerShellu s právy správce.
    1. Spouštěl `Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Příprava účtu Azure Storage](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Přihlaste se ke svému předplatnému, které obsahuje účet vzdáleného vykreslování Azure:
    1. Otevřete okno PowerShellu.
    1. Spusťte: `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

    > [!NOTE]
    > V případě, že má vaše organizace více než jedno předplatné, možná budete muset zadat SubscriptionId a argumenty tenanta. Vyhledejte podrobnosti v [dokumentaci Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Stáhněte složku *skripty* z [úložiště GitHub pro vzdálené vykreslování Azure](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Konfigurační soubor

Vedle souborů je `.ps1` `arrconfig.json` potřeba vyplnit:

```json
{
    "accountSettings": {
        "arrAccountId": "<fill in the account ID from the Azure Portal>",
        "arrAccountKey": "<fill in the account key from the Azure Portal>",
        "region": "<select from available regions>"
    },
    "renderingSessionSettings": {
        "vmSize": "<select standard or premium>",
        "maxLeaseTime": "<hh:mm:ss>"
    },
  "assetConversionSettings": {
    "resourceGroup": "<resource group which contains the storage account you created, only needed when uploading or generating SAS>",
    "storageAccountName": "<name of the storage account you created>",
    "blobInputContainerName": "<input container inside the storage container>",
    "blobOutputContainerName": "<output container inside the storage container>",
    "localAssetDirectoryPath": "<fill in a path to a local directory containing your asset (and files referenced from it like textures)>",
    "inputFolderPath": "<optional: base folderpath in the input container for asset upload. uses / as dir separator>",
    "inputAssetPath": "<the path to the asset under inputcontainer/inputfolderpath pointing to the input asset e.g. box.fbx>",
    "outputFolderPath": "<optional: base folderpath in the output container - the converted asset and log files will be placed here>",
    "outputAssetFileName": "<optional: filename for the converted asset, this will be placed in the output container under the outputpath>"
  }
}
```

> [!CAUTION]
> Ujistěte se, že jsou v cestě LocalAssetDirectoryPath správně uvozená zpětná lomítka, pomocí dvojitých zpětných lomítek: \\ \\ a na všech ostatních cestách, jako je InputFolderPath a inputAssetPath, použijte lomítka (/).

> [!CAUTION]
> Volitelné hodnoty musí být vyplněny nebo je nutné úplně odebrat klíč a hodnotu. Například pokud nepoužijete  `"outputAssetFileName"` parametr, je nutné odstranit celý řádek uvnitř `arrconfig.json` .

### <a name="accountsettings"></a>accountSettings

`arrAccountId`Informace o a `arrAccountKey` najdete v tématu [Vytvoření účtu vzdáleného vykreslování Azure](../how-tos/create-an-account.md).
`region`Zobrazí [seznam dostupných oblastí](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Tato struktura musí být vyplněna, pokud chcete spustit **RenderingSession.ps1**:

- **vmSize:** Vybere velikost virtuálního počítače. Vyberte [*úroveň Standard*](../reference/vm-sizes.md) nebo [*Premium*](../reference/vm-sizes.md). Vypnutí relací vykreslování, když už je nepotřebujete
- **maxLeaseTime:** Doba, po kterou chcete virtuální počítač zapůjčit. Po vypršení platnosti zapůjčení se vypne. Čas zapůjčení lze později prodloužit (viz níže).

### <a name="assetconversionsettings"></a>assetConversionSettings

Tato struktura musí být vyplněna, pokud chcete spustit **Conversion.ps1**.

Podrobnosti najdete v tématu [Příprava účtu Azure Storage](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Skript: RenderingSession.ps1

Tento skript slouží k vytváření, dotazování a zastavování relací vykreslování.

> [!IMPORTANT]
> Ujistěte se, že jste vyplnili části *accountSettings* a *renderingSessionSettings* v arrconfig.jsna.

### <a name="create-a-rendering-session"></a>Vytvoření relace vykreslování

Normální použití s plně vyplněným arrconfig.js:

```PowerShell
.\RenderingSession.ps1
```

Skript zavolá [REST API správy relace](../how-tos/session-rest-api.md) , aby VYVOLAL virtuální počítač pro vykreslování se zadaným nastavením. Po úspěšném načtení bude *identifikátor SessionID* načten. Pak bude dotazovat vlastnosti relace až do chvíle, kdy je relace připravena nebo dojde k chybě.

Použití **alternativního konfiguračního** souboru:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

**Jednotlivá nastavení** můžete z konfiguračního souboru přepsat:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Chcete-li **Spustit pouze relaci bez cyklického dotazování**, můžete použít:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

*Identifikátor SessionID* , který skript načte, musí být předán většině dalších příkazů relace.

### <a name="retrieve-session-properties"></a>Načíst vlastnosti relace

Chcete-li získat vlastnosti relace, spusťte příkaz:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Použijte `-Poll` k čekání, až bude relace *připravena* , nebo došlo k chybě.

### <a name="list-active-sessions"></a>Výpis aktivních relací

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Zastavení relace

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Změnit vlastnosti relace

V tuto chvíli podporujeme jenom změnu maxLeaseTime relace.

> [!NOTE]
> Doba zapůjčení se vždy počítá od okamžiku, kdy byl virtuální počítač relace původně vytvořen. Aby bylo možné zapůjčení relace prodloužit o jinou hodinu, zvyšte *maxLeaseTime* o jednu hodinu.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Skript: Conversion.ps1

Tento skript slouží k převodu vstupních modelů do formátu runtime specifického pro vzdálené vykreslování Azure.

> [!IMPORTANT]
> Ujistěte se, že jste vyplnili části *accountSettings* a *assetConversionSettings* v arrconfig.jsna.

Skript ukazuje dvě možnosti použití účtů úložiště se službou:

- Účet úložiště propojený s účtem vzdáleného vykreslování Azure
- Poskytnutí přístupu k úložišti prostřednictvím sdílených přístupových podpisů (SAS)

### <a name="linked-storage-account"></a>Propojený účet úložiště

Po úplném vyplňování arrconfig.jsa propojení účtu úložiště můžete použít následující příkaz. Odkaz na účet úložiště je popsaný v tématu [Vytvoření účtu](../how-tos/create-an-account.md#link-storage-accounts).

Použití propojeného účtu úložiště je preferovaným způsobem, jak používat službu převodu, protože není potřeba generovat sdílené přístupové podpisy.

```PowerShell
.\Conversion.ps1
```

1. Nahrajte všechny soubory obsažené v souboru `assetConversionSettings.modelLocation` do vstupního kontejneru objektů BLOB pod daným `inputFolderPath` ...
1. Zavolejte [REST API převodu modelu](../how-tos/conversion/conversion-rest-api.md) , aby se aktivoval [Převod modelu](../how-tos/conversion/model-conversion.md) .
1. Cyklické dotazování na stav převodu, dokud převod není úspěšný nebo neúspěšný.
1. Podrobnosti výstupu převedených umístění souboru (účet úložiště, výstupní kontejner, cesta k souboru v kontejneru).

### <a name="access-to-storage-via-shared-access-signatures"></a>Přístup k úložišti prostřednictvím sdílených přístupových podpisů

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Tato akce:

1. Nahrajte místní soubor z do `assetConversionSettings.localAssetDirectoryPath` vstupního kontejneru objektů BLOB.
1. Vygenerujte identifikátor URI SAS pro vstupní kontejner.
1. Vygenerujte identifikátor URI SAS pro výstupní kontejner.
1. Zavolejte [REST API převodu modelu](../how-tos/conversion/conversion-rest-api.md) , aby se aktivoval [Převod modelu](../how-tos/conversion/model-conversion.md).
1. Cyklické dotazování na stav převodu, dokud převod není úspěšný nebo neúspěšný.
1. Podrobnosti výstupu převedených umístění souboru (účet úložiště, výstupní kontejner, cesta k souboru v kontejneru).
1. Vytvoří výstup identifikátoru URI SAS pro převedený model ve výstupním kontejneru objektů BLOB.

### <a name="additional-command-line-options"></a>Další možnosti příkazového řádku

Použití **alternativního konfiguračního** souboru:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Chcete-li **Spustit převod modelu pouze bez cyklického dotazování**, můžete použít:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

**Jednotlivá nastavení** můžete z konfiguračního souboru přepsat pomocí následujících přepínačů příkazového řádku:

* **ID:** ConversionId se používá s GetConversionStatus
* **ArrAccountId:** ArrAccountId of accountSettings
* **ArrAccountKey:** override pro ArrAccountKey of accountSettings
* **Oblast:** přepsání pro oblast accountSettings
* **ResourceName:** override pro zdroj dat assetConversionSettings
* **StorageAccountName:** override pro StorageAccountName of assetConversionSettings
* **BlobInputContainerName:** override pro BlobInputContainer of assetConversionSettings
* **LocalAssetDirectoryPath:** override pro LocalAssetDirectoryPath of assetConversionSettings
* **InputAssetPath:** override pro InputAssetPath of assetConversionSettings
* **BlobOutputContainerName:** override pro BlobOutputContainerName of assetConversionSettings
* **OutputFolderPath:** override pro OutputFolderPath of assetConversionSettings
* **OutputAssetFileName:** override pro OutputAssetFileName of assetConversionSettings

Například můžete zkombinovat určitý počet z těchto možností:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Spuštění jednotlivých fází převodu

Pokud chcete spustit jednotlivé kroky procesu, můžete použít:

Nahrajte jenom data z daného LocalAssetDirectoryPath.

```PowerShell
.\Conversion.ps1 -Upload
```

Jenom spustit proces převodu modelu, který už je nahraný do úložiště objektů BLOB (nespouštět nahrávání, nedotazovat se na stav převodu), skript vrátí *conversionId*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

A můžete načíst stav převodu tohoto převodu pomocí:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Použijte `-Poll` k čekání na převod, nebo došlo k chybě.

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
- [Rychlý start: Převod modelu pro vykreslování](../quickstarts/convert-model.md)
- [Převod modelu](../how-tos/conversion/model-conversion.md)