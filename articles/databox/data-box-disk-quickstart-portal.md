---
title: Rychlé zprovoznění Microsoft Azure Data Box Disku | Microsoft Docs
description: Pomocí tohoto rychlého zprovoznění můžete rychle nasadit svůj Disk Azure Data Box pomocí webu Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 02/26/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: 298c5f718d10a318ff241cf000f24b5484485489
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57408577"
---
# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal"></a>Rychlý start: Nasazení disku Azure Data Box pomocí webu Azure portal

Toto rychlé zprovoznění popisuje, jak nasadit Azure Data Box Disk pomocí webu Azure Portal. Obsahuje například postup rychlého vytvoření objednávky, přijetí disků, vybalení, připojení a zkopírování dat na disky, aby se mohla nahrát do Azure.

Pro podrobné podrobný postup nasazení a pokyny pro sledování, přejděte na [kurzu: Objednávka disku Azure Data Box](data-box-disk-deploy-ordered.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Než začnete:

- Ujistěte se, že je ve vašem předplatném povolená služba Azure Data Box. Pokud chcete ve svém předplatném povolit tuto službu, [zaregistrovat si ji](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [http://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs).

## <a name="order"></a>Objednání

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
    3. Spusťte odemykací nástroj Data Box Disku a zadejte klíč. Při vložení dalšího disku vždy znovu spusťte odemykací nástroj a zadejte klíč. **K odemknutí disku nepoužívejte dialogové okno nástroje BitLocker ani klíč nástroje BitLocker.** Další informace o tom, jak disky odemknout, přejděte na [odemknout disky na klientovi Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) nebo [odemknout disků v klientovi Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).
    4. Nástroj zobrazí písmeno jednotky přiřazené k danému disku. Poznamenejte si písmeno diskové jednotky. Budete ho potřebovat v dalších krocích.

## <a name="copy-data-and-validate"></a>Kopírování dat a ověření

Doba trvání této operace závisí na množství dat.

1. Daná jednotka *PageBlob*, *BlockBlob*, *AzureFile*, *ManagedDisk*, a *DataBoxDiskImport* složek. Přetáhněte data ke kopírování, která chcete importovat jako objekty blob bloku, do složky *BlockBlob*. Podobně, přetáhněte a umístěte data, jako jsou VHD/VHDX na *PageBlob* složky a dat pro *AzureFile*. Zkopírovat virtuální pevné disky, které chcete nahrát jako spravovaných disků do složky v *ManagedDisk*.

    V účtu služby Azure Storage se pro každou podsložku ve složkách *BlockBlob* a *PageBlob* vytvoří zvláštní kontejner. Vytvoření sdílené složky pro dílčí složku ve složce *AzureFile*.

    Všechny soubory ve složkách *BlockBlob* a *PageBlob* se zkopírují do výchozího kontejneru `$root` v účtu Azure Storage. Zkopírujte soubory do složky v rámci *AzureFile*. Všechny soubory zkopírovány přímo *AzureFile* složky selhání a jsou odeslány jako objekty BLOB bloku.

    > [!NOTE]
    > - Všechny kontejnery, objekty BLOB a soubory by měly odpovídat [zásady vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Při nedodržení těchto pravidel se odesílání dat do Azure nezdaří.
    > - Ujistěte se, že soubory nepřekročí ~4.75 TB pro objekty BLOB bloku a 8 TB pro objekty BLOB stránky, ~ 1 TB pro soubory Azure.

2. **(Volitelné, ale doporučené)**  Po dokončení kopírování, důrazně doporučujeme přinejmenším spustit `DataBoxDiskValidation.cmd` součástí *DataBoxDiskImport* složky a vyberte možnost 1 ověření souborů. Doporučujeme také tento čas umožňující také generovat kontrolních součtů pro ověření použijete možnost 2 (může chvíli trvat v závislosti na velikosti dat). Tyto kroky minimalizovat pravděpodobnost selhání při odesílání dat do Azure.
3. Na jednotce bezpečně odeberte.

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

## <a name="next-steps"></a>Další postup

V tomto rychlém zprovoznění jste nasadili Azure Data Box Disk, který umožňuje importovat data do Azure. Další informace o správě Azure Data Box Disku získáte v následujícímu kurzu:

> [!div class="nextstepaction"]
> [Použití webu Azure Portal ke správě Data Box Disku](data-box-portal-ui-admin.md)
