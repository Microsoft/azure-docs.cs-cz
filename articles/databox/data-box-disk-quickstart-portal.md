---
title: Rychlé zprovoznění Microsoft Azure Data Box Disku | Microsoft Docs
description: Pomocí tohoto rychlého zprovoznění můžete rychle nasadit svůj Disk Azure Data Box pomocí webu Azure Portal
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: quickstart
ms.date: 09/07/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to quickly deploy Data Box Disk so as to import data into Azure.
ms.openlocfilehash: eb5cf5c6eb210e8bd26c7c074ef6f499e5d4c83d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740744"
---
# <a name="quickstart-deploy-azure-data-box-disk-using-the-azure-portal-preview"></a>Rychlé zprovoznění: Nasazení Azure Data Box Disku pomocí webu Azure Portal (Preview)

Toto rychlé zprovoznění popisuje, jak nasadit Azure Data Box Disk pomocí webu Azure Portal. Obsahuje například postup rychlého vytvoření objednávky, přijetí disků, vybalení, připojení a zkopírování dat na disky, aby se mohla nahrát do Azure. 

Podrobné pokyny k nasazení a sledování najdete v článku [Kurz: Objednání Azure Data Box Disku](data-box-disk-deploy-ordered.md). 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT]
> Data Box Disk je ve verzi Preview. Před nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze platformy Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Než začnete:

- Ujistěte se, že je ve vašem předplatném povolená služba Azure Data Box. Pokud chcete ve svém předplatném povolit tuto službu, [zaregistrovat si ji](http://aka.ms/azuredataboxfromdiskdocs).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [http://aka.ms/azuredataboxfromdiskdocs](http://aka.ms/azuredataboxfromdiskdocs).

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
    3. Spusťte odemykací nástroj Data Box Disku a zadejte klíč. Při vložení dalšího disku vždy znovu spusťte odemykací nástroj a zadejte klíč. **K odemknutí disku nepoužívejte dialogové okno nástroje BitLocker ani klíč nástroje BitLocker.** Další informace o odemykání disků najdete v článku o [odemknutí disků v klientovi pro Windows]() nebo [odemknutí disků v klientovi pro Linux]().
    4. Nástroj zobrazí písmeno jednotky přiřazené k danému disku. Poznamenejte si písmeno diskové jednotky. Budete ho potřebovat v dalších krocích.

## <a name="copy-data-and-validate"></a>Kopírování dat a ověření

Doba trvání této operace závisí na množství dat. 

1. Jednotka obsahuje složky *PageBlob*, *BlockBlob* a *DataBoxDiskImport*. Přetáhněte data ke kopírování, která chcete importovat jako objekty blob bloku, do složky *BlockBlob*. Data jako VHD/VHDX podobně zkopírujte do složky *PageBlob*.

    V účtu služby Azure Storage se pro každou podsložku ve složkách *BlockBlob* a *PageBlob* vytvoří zvláštní kontejner. Všechny soubory ve složkách *BlockBlob* a *PageBlob* se zkopírují do výchozího kontejneru `$root` v účtu Azure Storage.

    > [!NOTE] 
    > - Všechny kontejnery a objekty blob by měly být v souladu se [zásadami vytváření názvů v Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Při nedodržení těchto pravidel se odesílání dat do Azure nezdaří.
    > - Ujistěte se, že velikost souborů nepřekračuje ~4,75 TiB v případě objektů blob bloku a 8 TiB v případě objektů blob stránky.

2. (Volitelné) Po dokončení kopírování doporučujeme vygenerovat kontrolní součty pro ověření spuštěním souboru `DataBoxDiskValidation.cmd`, který se nachází ve složce *DataBoxDiskImport*. V závislosti na velikosti dat může tento krok nějakou dobu trvat. 
3. Odpojte jednotku. 


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


