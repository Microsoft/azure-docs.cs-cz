---
title: Služba Azure FPGFA Attestation
description: Služba ověření identity pro virtuální počítače NP-Series.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: ab9c9c6b9d908e86912565ba43cec665432aeda5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389617"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>FPGA ověření identity pro virtuální počítače Azure NP-Series (Preview)

Služba FPGA Attestation provádí řadu ověření v souboru kontrolního bodu návrhu (označované jako "Netlist") vygenerované sadou nástrojů Xilinx a vytvoří soubor, který obsahuje ověřený obrázek (označovaný jako "Bitstream"), který se dá načíst do karty Xilinx U250 FPGA ve virtuálním počítači s.  

## <a name="prerequisites"></a>Požadavky  

Budete potřebovat předplatné Azure a účet Azure Storage. Předplatné vám umožní přístup k Azure a účet úložiště se používá k ukládání Netlist a výstupních souborů služby ověření identity.  

Poskytujeme skriptům PowerShellu a Bash, které odesílají požadavky na ověření identity.   Skripty používají rozhraní příkazového řádku Azure CLI, které může běžet v systému Windows a Linux. PowerShell může běžet v systémech Windows, Linux a macOS.  

Stažení Azure CLI (povinné):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

PowerShell pro Windows, Linux a macOS stažení (jenom pro skripty PowerShellu):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

Abyste mohli odeslat službě ověření identity, budete muset mít svého tenanta a ID předplatného autorizaci. Přejděte https://aka.ms/AzureFPGAAttestationPreview na žádost o přístup. 

## <a name="building-your-design-for-attestation"></a>Vytváření návrhu pro ověření identity  

Preferovaná sada nástrojů Xilinx pro vytváření návrhů je Vitis 2020,2. Netlist soubory, které byly vytvořeny pomocí starší verze sady nástrojů a jsou stále kompatibilní s 2020,2 lze použít. Ujistěte se, že jste načetli správné prostředí pro sestavení. Aktuálně podporovaná verze je xilinx_u250_gen3x16_xdma_2_1_202010_1. Podpůrné soubory je možné stáhnout z Xilinx Alveo Lounge. 

Chcete-li vytvořit soubor xclbin, který obsahuje Netlist namísto Bitstream, je nutné zahrnout následující argument do objektu Vitis (v + + cmd line).   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Přihlášení do Azure  

Před provedením jakýchkoli operací s Azure je nutné se přihlásit do Azure a nastavit předplatné, které je autorizováno pro volání služby. ```az login``` ```az account set –s <Sub ID or Name>``` Pro tento účel použijte příkazy a. Zde jsou popsány další informace o tomto procesu:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Na příkazovém řádku použijte možnost přihlásit se interaktivně nebo přihlásit pomocí přihlašovacích údajů.  

## <a name="creating-a-storage-account-and-blob-container"></a>Vytvoření účtu úložiště a kontejneru objektů BLOB  

Váš soubor Netlist se musí nahrát do kontejneru objektů BLOB služby Azure Storage pro přístup službou ověření identity.  

Další informace o vytvoření účtu, kontejneru a nahrání Netlist jako objektu blob do tohoto kontejneru najdete na této stránce: [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](/azure/storage/blobs/storage-quickstart-blobs-cli) .  

K tomuto taky můžete použít taky Azure Portal.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Nahrání souboru Netlist do Azure Blob Storage  

Existuje několik způsobů, jak soubor zkopírovat; Příklad použití rutiny AZ Storage upload je uveden níže. Příkazy AZ se spouštějí v systémech Linux a Windows. Můžete zvolit libovolný název pro název objektu blob, ale nezapomeňte zachovat rozšíření xclbin. 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Stažení skriptů pro ověření identity  

Ověřovací skripty je možné stáhnout z následujícího kontejneru Azure Storage BLOB:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

Soubor zip obsahuje dva skripty PowerShellu, jeden k odeslání a druhý pro monitorování, zatímco třetí soubor je bash skript, který provádí obě funkce.  

## <a name="running-the-attestation-scripts"></a>Spuštění skriptů pro ověření identity  

Chcete-li spustit skripty, budete muset zadat název svého účtu úložiště, název kontejneru objektů blob, kde je uložen soubor Netlist a název souboru netlist. Budete také muset vytvořit sdílený přístupový podpis služby (SAS), který uděluje přístup pro čtení a zápis do kontejneru (ne do Netlist). Toto SAS používá služba ověření identity k vytvoření místní kopie souboru Netlist a ke zpětnému zápisu výsledných výstupních souborů procesu ověření do vašeho kontejneru.  

Přehled sdílených přístupových podpisů najdete tady s konkrétními informacemi o SAS, které služba je tady k dispozici. Stránka SAS služby obsahuje důležitou opatrnost při ochraně generovaného SAS.  Přečtěte si upozornění a porozumět nutnosti chránit SAS před škodlivým nebo nezamýšleným použitím.  

SAS pro svůj kontejner můžete vygenerovat pomocí rutiny AZ Storage Container Generate-SAS. Zadejte čas vypršení platnosti ve formátu UTC, který je nejméně pár hodin po době odeslání; přibližně 6 hodin by měl být větší než přiměřený.  

Pokud chcete použít virtuální adresáře, musíte zahrnout hierarchii adresářů jako součást argumentu Container. Například pokud máte kontejner nazvaný "netlists" a máte virtuální adresář s názvem "Image1", který obsahuje objekt BLOB Netlist, zadali byste jako název kontejneru "netlists/Image1". Přidejte další názvy adresářů a určete hlubší hierarchii. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>Kontrola stavu odeslání  

Služba ověření identity vrátí ID orchestrace vašeho odeslání. Skripty pro odeslání automaticky začnou sledovat odeslání při cyklickém dotazování na dokončení. ID orchestrace představuje primární způsob, jak můžeme zjistit, co se stalo s vaším odesláním, takže pokud máte problém, zachovejte si ho. V případě referenčních bodů trvá ověření přibližně 30 minut, než se dokončí u malého souboru Netlist (300MB ve velikosti). soubor 1,6 GB trval hodinu. 

Skript Monitor-Validation.ps1 můžete kdykoli zavolat, chcete-li získat stav a výsledky ověření identity, a jako argument zadejte ID orchestrace:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

Alternativně můžete odeslat požadavek HTTP POST do koncového bodu služby ověření identity:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

Text žádosti by měl obsahovat ID vašeho předplatného, ID tenanta a ID orchestrace vaší žádosti o ověření identity:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Kroky po ověření

Služba přepíše svůj výstup zpátky do kontejneru. Pokud úspěšné ověření projde, váš kontejner bude mít původní soubor Netlist (ABC. xclbin), soubor s Bitstream (ABC. bit. xclbin), soubor, který identifikuje soukromé umístění vašich uložených Bitstream (ABC. Azure. xclbin) a čtyři soubory protokolu: jeden pro proces spuštění (abc-log.txt) a jeden pro tři paralelní fáze, které provádí ověření. Jsou pojmenovány * logPhaseX.txt, kde X je číslo pro danou fázi. Ve vašem VIRTUÁLNÍm počítači se používá Azure. xclbin k signalizaci odeslání ověřené image do U250. 

Pokud se ověření nepovedlo, zapíše se soubor Error-*. txt, který indikuje, který krok selhal. Pokud protokol chyb indikuje, že ověření identity selhalo, ověřte také soubory protokolu. Pokud nám chcete kontaktovat podporu, nezapomeňte zahrnout všechny tyto soubory jako součást žádosti o podporu spolu s ID orchestrace.  

Můžete použít Azure Portal k vytvoření kontejneru i k nahrávání Netlist a stahování souborů bitstream a protokolů. Odeslání žádosti o ověření identity a monitorování jejího pokroku prostřednictvím portálu není momentálně podporované a musí se provádět přes skripty, jak je popsáno výše. 

