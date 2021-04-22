---
title: Rychlé zprovoznění Microsoft Azure Data Box Disku | Microsoft Docs
description: Pomocí tohoto rychlého zprovoznění můžete rychle nasadit svůj Disk Azure Data Box pomocí webu Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 11/04/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 241b7c0c07d1fbaa6a43c6be4b264424612f538a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869036"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Rychlý start: Nasazení Azure Data Box Disku pomocí webu Azure Portal

Toto rychlé zprovoznění popisuje, jak nasadit Azure Data Box Disk pomocí webu Azure Portal. Obsahuje například postup rychlého vytvoření objednávky, přijetí disků, vybalení, připojení a zkopírování dat na disky, aby se mohla nahrát do Azure.

Podrobné pokyny k nasazení a sledování najdete v článku [Kurz: Objednání Azure Data Box Disku](data-box-disk-deploy-ordered.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F&preserve-view=true).

::: zone-end

::: zone target="chromeless"

Tato příručka vás provede používáním Azure Data Box Disku na webu Azure Portal. Tato příručka vám pomůže zodpovědět následující otázky.

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Požadavky

Než začnete:

- Ujistěte se, že je ve vašem předplatném povolená služba Azure Data Box. Pokud chcete ve svém předplatném povolit tuto službu, [zaregistrovat si ji](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

::: zone-end

::: zone target="chromeless"

> [!div class="checklist"]
>
> - **Kontrola požadavků:** Zkontrolujte počet disků a kabelů, operační systém a další software.
> - **Připojení a odemknutí:** Připojte zařízení a odemkněte disky, abyste mohli zkopírovat data.
> - **Kopírování dat na disk a jejich ověření:** Zkopírujte data do předem vytvořených složek na discích.
> - **Vrácení disků:** Vraťte disky do datacentra Azure, kde se data nahrají do vašeho účtu úložiště.
> - **Ověření dat v Azure:** Před odstraněním dat ze zdrojového datového serveru ověřte, že se nahrála do vašeho účtu úložiště.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Objednání

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Tento krok trvá přibližně 5 minut.

1. Na webu Azure Portal vytvořte nový prostředek Azure Data Box. 
2. Vyberte předplatné, ve kterém je tato služba povolená, a zvolte typ přenosu **Import**. Do pole **Zdrojová země** zadejte zemi, kde se data nacházejí, a v poli **Cílová oblast Azure** zvolte oblast, kam se mají data přenést.
3. Vyberte **Data Box Disk**. Maximální kapacita řešení je 35 TB a v případě většího množství dat můžete zadat víc objednávek.  
4. Zadejte podrobnosti objednávky a informace o dodání. Pokud je služba dostupná ve vaší oblasti, zadejte adresy pro poslání e-mailu s oznámením, zkontrolujte souhrn a vytvořte objednávku.

Po vytvoření objednávky proběhne příprava disků k odeslání.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí těchto příkazů rozhraní příkazového řádku Azure můžete vytvořit úlohu Data Box Disk.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. Spusťte příkaz [az group create](/cli/azure/group#az_group_create) a vytvořte skupinu prostředků, nebo použijte už existující skupinu prostředků:

   ```azurecli
   az group create --name databox-rg --location westus
   ```

1. Použijte příkaz [az storage account create](/cli/azure/storage/account#az_storage_account_create) a vytvořte účet úložiště, nebo použijte už existující účet úložiště:

   ```azurecli
   az storage account create --resource-group databox-rg --name databoxtestsa
   ```

1. Spusťte příkaz [az databox job create](/cli/azure/databox/job#az_databox_job_create) pro vytvoření úlohy Data Box se SKU DataBoxDisk:

   ```azurecli
   az databox job create --resource-group databox-rg --name databoxdisk-job \
       --location westus --sku DataBoxDisk --contact-name "Jim Gan" --phone=4085555555 \
       –-city Sunnyvale --email-list JimGan@contoso.com --street-address1 "1020 Enterprise Way" \
       --postal-code 94089 --country US --state-or-province CA \
       --storage-account databoxtestsa --expected-data-size 1
   ```

1. Pro aktualizaci úlohy spusťte příkaz [az databox job update](/cli/azure/databox/job#az_databox_job_update) jako v tomto příkladě, kde měníte e-mail a jméno kontaktu:

   ```azurecli
   az databox job update -g databox-rg --name databox-job --contact-name "Robert Anic" --email-list RobertAnic@contoso.com
   ```

   Pro získání informací o úloze spusťte [az databox job show](/cli/azure/databox/job#az_databox_job_show):

   ```azurecli
   az databox job show --resource-group databox-rg --name databox-job
   ```

   Pro zobrazení všech úloh Data Box pro skupinu prostředků použijte příkaz [az databox job list]( /cli/azure/databox/job#az_databox_job_list):

   ```azurecli
   az databox job list --resource-group databox-rg
   ```

   Pro zrušení úlohy spusťte příkaz [az databox job cancel](/cli/azure/databox/job#az_databox_job_cancel):

   ```azurecli
   az databox job cancel –resource-group databox-rg --name databox-job --reason "Cancel job."
   ```

   Pro odstranění úlohy spusťte příkaz [az databox job delete](/cli/azure/databox/job#az_databox_job_delete):

   ```azurecli
   az databox job delete –resource-group databox-rg --name databox-job
   ```

1. Pro vypsání přihlašovacích údajů pro úlohu Data Box použijte příkaz [az databox job list-credentials]( /cli/azure/databox/job#az_databox_job_list_credentials):

   ```azurecli
   az databox job list-credentials --resource-group "databox-rg" --name "databoxdisk-job"
   ```

Po vytvoření objednávky je zařízení připravené k odeslání.

---

## <a name="unpack"></a>Vybalení

Tento krok trvá přibližně 5 minut.

Data Box Disk se zasílá v balení UPS Express Box. Otevření krabici a zjistěte, jestli obsahuje tyto položky:

- 1 až 5 disků USB zabalených v bublinkové folii
- Jeden připojovací kabel na každý disk
- Expediční štítek na zpáteční zásilku

## <a name="connect-and-unlock"></a>Připojení a odemknutí

Tento krok trvá přibližně 5 minut.

1. Pomocí přiloženého kabelu připojte disk k počítači s podporovanou verzí Windows/Linuxu. Další informace o podporovaných verzích operačního systému najdete v článku [Systémové požadavky pro službu Azure Data Box](data-box-disk-system-requirements.md). 
2. Odemknutí disku:

    1. Na webu Azure Portal přejděte na **Obecné > Detaily zařízení** a získejte přístupový klíč.
    2. Na počítači sloužícím ke kopírování dat na disky stáhněte a rozbalte odemykací nástroj Data Box Disku specifický pro operační systém. 
    3. Spusťte odemykací nástroj Data Box Disku a zadejte klíč. Při vložení dalšího disku vždy znovu spusťte odemykací nástroj a zadejte klíč. **K odemknutí disku nepoužívejte dialogové okno nástroje BitLocker ani klíč nástroje BitLocker.** Další informace o odemykání disků najdete v tématu věnovaném [odemknutí disků v klientovi Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) nebo [odemknutí disků v klientovi Linuxu](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. Nástroj zobrazí písmeno jednotky přiřazené k danému disku. Poznamenejte si písmeno diskové jednotky. Budete ho potřebovat v dalších krocích.

## <a name="copy-data-and-validate"></a>Kopírování dat a ověření

Doba trvání této operace závisí na množství dat.

1. Jednotka obsahuje složky *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk* a *DataBoxDiskImport*. Přetáhněte data ke kopírování, která chcete importovat jako objekty blob bloku, do složky *BlockBlob*. Data jako VHD/VHDX podobně zkopírujte do složky *PageBlob* a odpovídající data do složky *AzureFile*. Virtuální pevné disky, které chcete nahrát jako spravované disky, zkopírujte do složky ve složce *ManagedDisk*.

    V účtu služby Azure Storage se pro každou podsložku ve složkách *BlockBlob* a *PageBlob* vytvoří zvláštní kontejner. Pro podsložku ve složce *AzureFile* se vytvoří sdílená složka.

    Všechny soubory ve složkách *BlockBlob* a *PageBlob* se zkopírují do výchozího kontejneru `$root` v účtu Azure Storage. Zkopírujte soubory do složky ve složce *AzureFile*. Všechny soubory zkopírované přímo do složky *AzureFile* selžou a nahrají se jako objekty blob bloku.

    > [!NOTE]
    > - Všechny kontejnery, objekty blob a soubory by měly být v souladu se [zásadami vytváření názvů v Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Při nedodržení těchto pravidel se odesílání dat do Azure nezdaří.
    > - Ujistěte se, že velikost souborů nepřekračuje ~4,75 TiB v případě objektů blob bloku, ~8 TiB v případě objektů blob stránky a ~1 TiB v případě souborů Azure.

2. **(Volitelné, ale doporučuje se)** Po dokončení kopírování důrazně doporučujeme ověřit soubory minimálně tím, že spustíte skript `DataBoxDiskValidation.cmd` umístěný ve složce *DataBoxDiskImport* a vyberete možnost 1. Pokud vám to čas dovolí, doporučujeme použít také možnost 2, aby se vygenerovaly také kontrolní součty k ověření (v závislosti na velikosti dat to může nějakou dobu trvat). Těmito kroky se minimalizuje riziko případných selhání při nahrávání dat do Azure.
3. Bezpečně odeberte jednotku.

## <a name="ship-to-azure"></a>Odeslání do Azure

Tento krok trvá 5 až 7 minut.

1. Všechny disky společně vložte do původního obalu. Použijte přiložený expediční štítek. Pokud se popisek poškodil nebo ztratil, stáhněte si ho z portálu. Přejděte na **Přehled** a na panelu příkazů klikněte na **Stáhnout expediční štítek**.
2. Zavezte zapečetěný balíček na pobočku dopravce.  

Služba Data Box Disk odešle e-mailové oznámení a aktualizuje stav objednávky na webu Azure Portal.

## <a name="verify-your-data"></a>Ověření dat

Doba trvání této operace závisí na množství dat.

1. Když se Data Box Disk připojí k síti v datovém centru Azure, automaticky se zahájí nahrávání dat do Azure.
2. Jakmile se kopírování dat dokončí, služba Azure Data Box vám přes web Azure Portal pošle oznámení.
    
    1. Zkontrolujte všechna selhání v protokolech chyb a proveďte potřebné kroky.
    2. Než odstraníte data ze zdroje, ujistěte se, že je máte v účtech úložiště.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Dokončení tohoto kroku trvá 2 až 3 minuty.

Na závěr můžete objednávku Data Boxu zrušit a potom odstranit.

- Před zpracováním objednávky můžete objednávku Data Boxu zrušit na webu Azure Portal. Po zpracování objednávky už se objednávka zrušit nedá. Průběh objednávky bude pokračovat až do fáze Dokončeno.

    Pokud chcete objednávku zrušit, přejděte do části **Přehled** a na panelu příkazů klikněte na **Zrušit**.  

- Jakmile se na webu Azure Portal objeví stav **Dokončeno** nebo **Zrušeno**, můžete objednávku odstranit.

    Pokud chcete odstranit objednávku, přejděte do části **Přehled** a na panelu příkazů klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém zprovoznění jste nasadili Azure Data Box Disk, který umožňuje importovat data do Azure. Další informace o správě Azure Data Box Disku získáte v následujícímu kurzu:

> [!div class="nextstepaction"]
> [Použití webu Azure Portal ke správě Data Box Disku](data-box-portal-ui-admin.md)

::: zone-end
