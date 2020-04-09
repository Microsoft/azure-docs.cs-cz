---
title: Ukázkové skripty PowerShellu
description: Příklady, které ukazují, jak používat front-end pomocí skriptů prostředí PowerShell
author: florianborn71
ms.author: flborn
ms.date: 02/12/2020
ms.topic: sample
ms.openlocfilehash: c45d2fc34ccbab6d813f12563678d036f9f35753
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891488"
---
# <a name="example-powershell-scripts"></a>Ukázkové skripty PowerShellu

Vzdálené vykreslování Azure poskytuje následující dvě rest api:

- [Rozhraní REST pro konverzi](../how-tos/conversion/conversion-rest-api.md)
- [Rozhraní REST RELACE API](../how-tos/session-rest-api.md)

[Úložiště ukázek ARR](https://github.com/Azure/azure-remote-rendering) obsahuje ukázkové skripty ve složce *Skripty* pro interakci s rest API služby. Tento článek popisuje jejich použití.

## <a name="prerequisites"></a>Požadavky

Chcete-li spustit ukázkové skripty, potřebujete funkční nastavení [Azure PowerShellu](https://docs.microsoft.com/powershell/azure/).

1. Nainstalujte Azure PowerShell:
    1. Otevření PowerShellu s právy správce
    1. Spustit:`Install-Module -Name Az -AllowClobber`

1. Pokud se vám při spouštění skriptů přijdou k chybám, ujistěte se, že jsou [zásady spuštění](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) správně nastaveny:
    1. Otevření PowerShellu s právy správce
    1. Spustit:`Set-ExecutionPolicy -ExecutionPolicy Unrestricted`

1. [Příprava účtu Azure Storage](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts)

1. Přihlaste se k předplatnému obsahujícímu váš účet vzdáleného vykreslování Azure:
    1. Otevření PowerShellu
    1. Spustit: `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

> [!NOTE]
> V případě, že vaše organizace má více než jedno předplatné, budete muset zadat SubscriptionId a Tenant argumenty. Podrobnosti naleznete v [dokumentaci connect-azaccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).

1. Stáhněte složku *Skripty* z [úložiště GithHub vzdáleného vykreslování Azure](https://github.com/Azure/azure-remote-rendering).

## <a name="configuration-file"></a>Konfigurační soubor

Vedle `.ps1` souborů je `arrconfig.json` třeba vyplnit:

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
> Ujistěte se, že správně uniknout zpětná lomítka v cestě\\\\LocalAssetDirectoryPath pomocí dvojité zpětná lomítka: " " a použít lomítka "/" ve všech ostatních cestách, jako je inputFolderPath a inputAssetPath.

### <a name="accountsettings"></a>nastavení účtu

For `arrAccountId` `arrAccountKey`a , najdete [v tématu Vytvoření účtu vzdáleného vykreslování Azure](../how-tos/create-an-account.md).
Prohlédněte `region` si [seznam dostupných oblastí](../reference/regions.md).

### <a name="renderingsessionsettings"></a>renderingSessionSettings

Tato struktura musí být vyplněna, pokud chcete spustit **RenderingSession.ps1**.

- **velikost vmVelikost:** Vybere velikost virtuálního počítače. Vyberte *standardní* nebo *prémii*. Vypněte relace vykreslování, když už je nepotřebujete.
- **maxLeaseTime:** Doba, po kterou chcete zapůjčení virtuálního_ virtuálního montova. Bude ukončena po vypršení platnosti zapůjčení. Doba zapůjčení může být prodloužena později (viz níže).

### <a name="assetconversionsettings"></a>assetConversionSettings

Tato struktura musí být vyplněna, pokud chcete spustit **Conversion.ps1**.

Podrobnosti najdete [v tématu Příprava účtu Azure Storage](../how-tos/conversion/blob-storage.md#prepare-azure-storage-accounts).

## <a name="script-renderingsessionps1"></a>Skript: RenderingSession.ps1

Tento skript se používá k vytvoření, dotazování a zastavení vykreslování relací.

> [!IMPORTANT]
> Ujistěte se, že jste vyplnili *accountSettings* a *renderingSettings* oddíly v arrconfig.json.

### <a name="create-a-rendering-session"></a>Vytvoření relace vykreslování

Běžné použití s plně vyplněným souborem arrconfig.json:

```PowerShell
.\RenderingSession.ps1
```

Skript bude volat [rozhraní REST API pro správu relací,](../how-tos/session-rest-api.md) aby se spárovat virtuální virtuální počítače vykreslování se zadaným nastavením. Na úspěch načte *sessionId*. Potom bude dotazování vlastnosti relace, dokud relace je připraven nebo došlo k chybě.

Použití **alternativního konfiguračního** souboru:

```PowerShell
.\RenderingSession.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Jednotlivá nastavení můžete **přepsat** z konfiguračního souboru:

```PowerShell
.\RenderingSession.ps1 -Region <region> -VmSize <vmsize> -MaxLeaseTime <hh:mm:ss>
```

Chcete-li zahájit relaci pouze **bez dotazování**, můžete použít:

```PowerShell
.\RenderingSession.ps1 -CreateSession
```

*SessionId,* že skript načte musí být předány většině ostatních příkazů relace.

### <a name="retrieve-session-properties"></a>Načíst vlastnosti relace

Chcete-li získat vlastnosti relace, spusťte:

```PowerShell
.\RenderingSession.ps1 -GetSessionProperties -Id <sessionID> [-Poll]
```

Slouží `-Poll` k počkejte, dokud relace není *připravena* nebo dojde k chybě.

### <a name="list-active-sessions"></a>Seznam aktivních relací

```PowerShell
.\RenderingSession.ps1 -GetSessions
```

### <a name="stop-a-session"></a>Zastavení relace

```PowerShell
.\RenderingSession.ps1 -StopSession -Id <sessionID>
```

### <a name="change-session-properties"></a>Změna vlastností relace

V současné době podporujeme pouze změnu maxLeaseTime relace.

> [!NOTE]
> Doba zapůjčení se vždy počítá od okamžiku, kdy byl virtuální virtuální hotel relace původně vytvořen. Takže prodloužit zapůjčení relace o další hodinu, zvýšit *maxLeaseTime* o jednu hodinu.

```PowerShell
.\RenderingSession.ps1 -UpdateSession -Id <sessionID> -MaxLeaseTime <hh:mm:ss>
```

## <a name="script-conversionps1"></a>Skript: Conversion.ps1

Tento skript se používá k převodu vstupních modelů do specifického runtime formátu Azure Remote Rendering.

> [!IMPORTANT]
> Ujistěte se, že jste vyplnili oddíly *accountSettings* a *assetConversionSettings* v souborech arrconfig.json.

Skript ukazuje dvě možnosti použití účtů úložiště se službou:

- Účet úložiště propojený s účtem vzdáleného vykreslování Azure
- Poskytování přístupu k úložišti prostřednictvím sdílených přístupových podpisů (SAS)

### <a name="linked-storage-account"></a>Účet propojeného úložiště

Po úplném vyplnění souboru arrconfig.json a propojení účtu úložiště můžete použít následující příkaz. Propojení účtu úložiště je popsáno v článku [Vytvoření účtu](../how-tos/create-an-account.md#link-storage-accounts).

Použití propojeného účtu úložiště je upřednostňovaný způsob použití služby převodu, protože není nutné generovat sdílené přístupové podpisy.

```PowerShell
.\Conversion.ps1
```

1. Nahrát všechny soubory `assetConversionSettings.modelLocation` obsažené ve vstupním kontejneru objektů blob pod daným`inputFolderPath`
1. Volání [modelu konverze REST API](../how-tos/conversion/conversion-rest-api.md) zahájit [převod modelu](../how-tos/conversion/model-conversion.md)
1. Dotazování na stav převodu, dokud se převod nepodaří nebo neselhal.
1. Podrobnosti o umístění převedeného souboru (účet úložiště, výstupní kontejner, cesta k souboru v kontejneru)

### <a name="access-to-storage-via-shared-access-signatures"></a>Přístup k úložišti prostřednictvím sdílených přístupových podpisů

```PowerShell
.\Conversion.ps1 -UseContainerSas
```

Tímto:

1. Nahrání místního `assetConversionSettings.localAssetDirectoryPath` souboru ze vstupního kontejneru objektů blob
1. Generovat identifikátor URI SAS pro vstupní kontejner
1. Generovat identifikátor URI SAS pro výstupní kontejner
1. Volání [modelu konverze REST API](../how-tos/conversion/conversion-rest-api.md) zahájit [převod modelu](../how-tos/conversion/model-conversion.md)
1. Dotazování na stav převodu, dokud se převod nepodaří nebo neselhal.
1. Podrobnosti o umístění převedeného souboru (účet úložiště, výstupní kontejner, cesta k souboru v kontejneru)
1. Výstup identifikátoru URI SAS do převedeného modelu ve výstupním kontejneru objektů blob

### <a name="additional-command-line-options"></a>Další možnosti příkazového řádku

Použití **alternativního konfiguračního** souboru:

```PowerShell
.\Conversion.ps1 -ConfigFile D:\arr\myotherconfigFile.json
```

Chcete-li **spustit pouze převod modelu bez dotazování**, můžete použít:

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

**Jednotlivá nastavení** z konfiguračního souboru můžete přepsat pomocí následujících přepínačů příkazového řádku:

* **Id:** Id převodu používané s Stavem GetConversionStatus
* **ArrAccountId:** arrAccountId účtuNastavení
* **ArrAccountKey:** přepsání pro arrAccountKey účtuNastavení
* **Oblast:** přepsání pro oblast účtuNastavení
* **ResourceGroup:** přepsání pro resourceGroup of assetConversionSettings
* **StorageAccountName:** přepsání pro storageAccountName assetConversionSettings
* **BlobInputContainerName:** přepsání pro objekt blobInputContainer assetConversionSettings
* **LocalAssetDirectoryPath:** přepsání pro localAssetDirectoryPath assetConversionSettings
* **InputAssetPath:** přepsání pro inputAssetPath assetConversionSettings
* **Objekt BlobOutputContainerName:** přepsání objektu blobOutputContainerName assetConversionSettings
* **OutputFolderPath:** přepsání pro outputFolderPath assetConversionSettings
* **OutputAssetFileName:** přepsání pro outputAssetFileName assetConversionSettings

Můžete například kombinovat řadu daných možností, jako je tento:

```PowerShell
.\Conversion.ps1 -LocalAssetDirectoryPath "C:\\models\\box" -InputAssetPath box.fbx -OutputFolderPath another/converted/box -OutputAssetFileName newConversionBox.arrAsset
```

### <a name="run-the-individual-conversion-stages"></a>Spuštění jednotlivých fází převodu

Pokud chcete spustit jednotlivé kroky procesu, můžete použít:

Pouze nahrát data z daného LocalAssetDirectoryPath

```PowerShell
.\Conversion.ps1 -Upload
```

Spusťte pouze proces převodu modelu, který už byl nahrán do úložiště objektů blob (nespouštějte nahrát, nedotazujte se na stav převodu) Skript vrátí *id převodu*.

```PowerShell
.\Conversion.ps1 -ConvertAsset
```

A stav konverze této konverze můžete získat pomocí následujících částek:

```PowerShell
.\Conversion.ps1 -GetConversionStatus -Id <conversionId> [-Poll]
```

Slouží `-Poll` k počkejte, až dojde k převodu nebo dojde k chybě.

## <a name="next-steps"></a>Další kroky

- [Úvodní příručka: Vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
- [Úvodní příručka: Převod modelu pro vykreslování](../quickstarts/convert-model.md)
- [Převod modelu](../how-tos/conversion/model-conversion.md)
