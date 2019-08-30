---
title: Rychlé zprovoznění Microsoft Azure Data Box Disku | Microsoft Docs
description: Pomocí tohoto rychlého zprovoznění můžete rychle nasadit svůj Disk Azure Data Box pomocí webu Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: 68ceeb2847fa03cd7c50eeb015cca5b84af085f2
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172739"
---
::: zone target="docs"

# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Rychlý start: Nasazení Azure Data Box Disk pomocí Azure Portal

::: zone-end

::: zone target="chromeless"

## <a name="get-started-with-azure-data-box-disk"></a>Začínáme s Azure Data Box Disk 

::: zone-end

::: zone target="docs"

Toto rychlé zprovoznění popisuje, jak nasadit Azure Data Box Disk pomocí webu Azure Portal. Obsahuje například postup rychlého vytvoření objednávky, přijetí disků, vybalení, připojení a zkopírování dat na disky, aby se mohla nahrát do Azure.

Podrobné pokyny k [nasazení a sledování najdete v tématu Kurz: Pořadí Azure Data Box Disk](data-box-disk-deploy-ordered.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

::: zone-end

::: zone target="chromeless"

Tato příručka vás provede jednotlivými kroky při použití Azure Data Box Disk v Azure Portal. Tato příručka vám pomůže zodpovědět následující otázky.

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
> - **Kontrola požadavků**: Ověřte počet disků a kabelů, operační systém a další software.
> - **Připojit a odemknout**: Připojte zařízení a odemkněte disk ke zkopírování dat.
> - **Zkopírujte data na disk a ověřte**: Zkopírujte data na disky do předvytvořených složek.
> - **Vrátit disky**: Vraťte disky do datového centra Azure, kde se data nahrají do svého účtu úložiště.
> - **Ověřte data v Azure**: Než odstraníte data ze zdrojového serveru, ověřte, že se data nahrála do vašeho účtu úložiště.

::: zone-end


::: zone target="docs"

## <a name="order"></a>Pořadí

Tento krok trvá přibližně 5 minut.

1. Na webu Azure Portal vytvořte nový prostředek Azure Data Box. 
2. Vyberte předplatné, ve kterém je tato služba povolená, a zvolte typ přenosu **Import**. Do pole **Zdrojová země** zadejte zemi, kde se data nacházejí, a v poli **Cílová oblast Azure** zvolte oblast, kam se mají data přenést.
3. Vyberte **Data Box Disk**. Maximální kapacita řešení je 35 TB a v případě většího množství dat můžete zadat víc objednávek.  
4. Zadejte podrobnosti objednávky a informace o dodání. Pokud je služba dostupná ve vaší oblasti, zadejte adresy pro poslání e-mailu s oznámením, zkontrolujte souhrn a vytvořte objednávku.

Po vytvoření objednávky proběhne příprava disků k odeslání.

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
    3. Spusťte odemykací nástroj Data Box Disku a zadejte klíč. Při vložení dalšího disku vždy znovu spusťte odemykací nástroj a zadejte klíč. **K odemknutí disku nepoužívejte dialogové okno nástroje BitLocker ani klíč nástroje BitLocker.** Další informace o tom, jak odemknout disky, najdete v tématu [odemčení disků v klientovi Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) nebo [odemčení disků v klientovi](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)se systémem Linux.
    4. Nástroj zobrazí písmeno jednotky přiřazené k danému disku. Poznamenejte si písmeno diskové jednotky. Budete ho potřebovat v dalších krocích.

## <a name="copy-data-and-validate"></a>Kopírování dat a ověření

Doba trvání této operace závisí na množství dat.

1. Jednotka obsahuje složky *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk*a *DataBoxDiskImport* . Přetáhněte data ke kopírování, která chcete importovat jako objekty blob bloku, do složky *BlockBlob*. Podobně přetáhněte data, jako jsou VHD/VHDX, do složky *PageBlob* a příslušná data *AzureFile*. Zkopírujte virtuální pevné disky, které chcete nahrát jako spravované disky, do složky v rámci *ManagedDisk*.

    V účtu služby Azure Storage se pro každou podsložku ve složkách *BlockBlob* a *PageBlob* vytvoří zvláštní kontejner. Pro podsložku v rámci *AzureFile*se vytvoří sdílená složka.

    Všechny soubory ve složkách *BlockBlob* a *PageBlob* se zkopírují do výchozího kontejneru `$root` v účtu Azure Storage. Zkopírujte soubory do složky v rámci *AzureFile*. Všechny soubory zkopírované přímo do složky *AzureFile* selžou a nahrají se jako objekty blob bloku.

    > [!NOTE]
    > - Všechny kontejnery, objekty BLOB a soubory by měly odpovídat konvencím pojmenování [Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Při nedodržení těchto pravidel se odesílání dat do Azure nezdaří.
    > - Zajistěte, aby soubory nepřesáhly ~ 4,75 TiB pro objekty blob bloku, ~ 8 TiB pro objekty blob stránky a ~ 1 TiB pro soubory Azure.

2. **(Volitelné, ale doporučené)** Až se kopie dokončí, důrazně doporučujeme, abyste měli aspoň na výběr, `DataBoxDiskValidation.cmd` který jste zadali ve složce *DataBoxDiskImport* , a vyberte možnost 1 pro ověření souborů. Doporučujeme také, abyste tuto dobu vybrali a pomocí možnosti 2 vygenerovali kontrolní součty pro ověření (může trvat i čas v závislosti na velikosti dat). Tyto kroky minimalizují riziko případného selhání při nahrávání dat do Azure.
3. Jednotku bezpečně odeberte.

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
