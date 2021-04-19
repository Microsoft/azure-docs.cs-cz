---
title: Vytvoření a použití sdílené složky Azure Files na virtuálních počítačích s Windows
description: Vytvořte a použijte sdílenou složku služby soubory Azure v Azure Portal. Připojte ho k virtuálnímu počítači s Windows, připojte se ke sdílené složce souborů a nahrajte soubor do sdílené složky soubory.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 5a3c664f6c6c0532ef915357cfbcbc8228202502
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718178"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Rychlý Start: vytvoření a Správa sdílené složky Azure Files s virtuálními počítači s Windows

Tento článek popisuje základní kroky pro vytvoření a použití sdílené složky Azure Files. V tomto rychlém startu se důrazně naučí, jak rychle nastavovat sdílenou složku Azure Files, abyste se mohli setkat s tím, jak služba funguje. Pokud potřebujete podrobnější pokyny pro vytváření a používání sdílených složek Azure ve vlastním prostředí, přečtěte si téma [použití sdílené složky Azure v systému Windows](storage-how-to-use-files-windows.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Příprava prostředí

V tomto rychlém startu nastavíte následující položky:

- Účet služby Azure Storage a sdílená složka Azure
- Virtuální počítač datacenter Windows serveru 2016

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Než budete moct pracovat se sdílenou složkou Azure, musíte vytvořit účet úložiště Azure. Účet úložiště pro obecné účely v2 poskytuje přístup ke všem službám Azure Storage: objekty blob, soubory, fronty a tabulky. Rychlý Start vytvoří účet úložiště pro obecné účely v2, ale postup vytvoření libovolného typu účtu úložiště je podobný. Účet úložiště může obsahovat neomezený počet sdílených složek. Sdílená složka může obsahovat neomezený počet souborů až do výše maximální kapacity účtu úložiště.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Dále vytvoříte sdílenou složku.

1. Po dokončení nasazení účtu úložiště Azure vyberte **Přejít k prostředku**.
1. V podokně účet úložiště vyberte **sdílení souborů** .

    ![Vyberte Sdílené složky.](./media/storage-files-quick-create-use-windows/click-files.png)

1. Vyberte **+ sdílení souborů**.

    ![Pokud chcete vytvořit novou sdílenou složku, vyberte + Shared File.](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Pojmenujte novou sdílenou složku *qsfileshare*, zadejte "1" pro **kvótu**, vynechte **optimalizovanou transakci** a vyberte **vytvořit**. Kvóta může být maximálně 5 TiB (100 TiB, s povolenými velkými sdílenými složkami), ale pro tento rychlý Start potřebujete jenom 1 GiB.
1. Na místním počítači vytvořte nový soubor txt s názvem *qsTestFile* .
1. Vyberte novou sdílenou složku a potom v umístění sdílené složky vyberte **Odeslat**.

    ![Nahrajte soubor.](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Přejděte do umístění, kde jste vytvořili soubor. txt > vyberte *qsTestFile.txt* > vyberte **Odeslat**.

Zatím jste vytvořili účet úložiště Azure a sdílenou složku s jedním souborem v Azure. V dalším kroku vytvoříte virtuální počítač Azure s Windows serverem 2016 Datacenter, který bude představovat místní server v tomto rychlém startu.

### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

1. Teď rozbalte nabídku na levé straně portálu a v levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Do vyhledávacího pole nad seznamem prostředků **Azure Marketplace** vyhledejte a vyberte **Windows Server 2016 Datacenter**.
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) vyberte skupinu prostředků, kterou jste pro tento rychlý Start vytvořili.

   ![V okně portálu zadejte základní informace o vašem VIRTUÁLNÍm počítači.](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. V části **Podrobnosti instance** název virtuálního počítače *qsVM*.
1. U položek **Oblast**, **Možnosti dostupnosti**, **Image** a **Velikost** ponechte výchozí nastavení.
1. V části **účet správce** přidejte **uživatelské jméno** a zadejte **heslo** pro virtuální počítač.
1. V části **Pravidla portů pro příchozí spojení** vyberte **Povolit vybrané porty** a pak z rozevíracího seznamu vyberte **RDP (3389)** a **HTTP**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**. Vytvoření nového virtuálního počítače bude trvat několik minut.

1. Po dokončení nasazení virtuálního počítače vyberte **Přejít k prostředku**.

Právě jste vytvořili nový virtuální počítač a připojili jste datový disk. Teď je potřeba se k tomuto virtuálnímu počítači připojit.

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

1. Na stránce vlastností virtuálního počítače vyberte **připojit** .

   ![Připojení k virtuálnímu počítači Azure z portálu](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Na stránce **připojit k virtuálnímu počítači** ponechte výchozí možnosti připojit pomocí **IP adresy** přes **číslo portu** *3389* a vyberte **Stáhnout soubor RDP**.
1. Otevřete stažený soubor RDP a po zobrazení výzvy vyberte **připojit** .
1. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno jako *localhost\username*, kde &lt; UserName &gt; je uživatelské jméno správce virtuálního počítače, které jste vytvořili pro virtuální počítač. Zadejte heslo, které jste pro virtuální počítač vytvořili, a pak vyberte **OK**.

   ![Další možnosti](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** nebo **pokračovat** a vytvořte připojení.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapování sdílené složky Azure na jednotku s Windows

1. V Azure Portal přejděte do sdílené složky *qsfileshare* a vyberte **připojit**.
1. Vyberte písmeno jednotky, potom zkopírujte obsah druhého pole a vložte ho do **poznámkového bloku**.

   :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Snímek obrazovky zobrazující obsah pole, které byste měli zkopírovat a vložit do poznámkového bloku" lightbox="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png":::

1. Na virtuálním počítači otevřete **PowerShell** a vložte ho do obsahu **poznámkového bloku** a stisknutím klávesy Enter příkaz spusťte. Měla by namapovat jednotku.

## <a name="create-a-share-snapshot"></a>Vytvoření snímku sdílené složky

Teď, když jste namapovali jednotku, můžete vytvořit snímek.

1. Na portálu přejděte do sdílené složky, vyberte **snímky** a pak vyberte **+ Přidat snímek**.

   ![V části operace vyberte snímky a pak vyberte Přidat snímek.](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Na virtuálním počítači otevřete *qstestfile.txt* a zadejte "Tento soubor byl upraven" > soubor uložte a zavřete.
1. Vytvořte další snímek.

## <a name="browse-a-share-snapshot"></a>Procházet snímek sdílené složky

1. V sdílené složce vyberte **snímky**.
1. V okně **snímky** vyberte první snímek v seznamu.

   ![Vybraný snímek v seznamu časových razítek](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. Otevřete tento snímek a vyberte *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Obnovení ze snímku

1. V okně snímku sdílení souborů klikněte pravým tlačítkem na *qsTestFile* a vyberte tlačítko **obnovit** .

    :::image type="content" source="media/storage-files-quick-create-use-windows/restore-share-snapshot.png" alt-text="Snímek obrazovky s oknem Snapshot (qstestfile) je vybraný, obnovení je zvýrazněné.":::

1. Vyberte **přepsat původní soubor**.

   ![Snímek obrazovky pro obnovení, je vybraný původní soubor přepsat.](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Na virtuálním počítači otevřete soubor. Neupravená verze byla obnovena.

## <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky

1. V sdílené složce vyberte **snímky**.
1. V okně **snímky** vyberte poslední snímek v seznamu a vyberte **Odstranit**.

   ![Snímek obrazovky s oknem snímky, vybraným posledním snímkem, zvýrazněným tlačítkem odstranit](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Použití snímku sdílené složky ve Windows

Stejně jako u místních snímků služby Stínová kopie svazku (VSS) můžete snímky z připojené sdílené složky Azure zobrazit pomocí karty předchozí verze.

1. V Průzkumníku souborů vyhledejte připojenou sdílenou složku.

   ![Připojená sdílená složka v Průzkumníkovi souborů](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Vyberte *qsTestFile.txt* a > klikněte pravým tlačítkem a v nabídce vyberte **vlastnosti** .

   ![Nabídka pro vybraný adresář zobrazená po kliknutí pravým tlačítkem](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Výběrem karty **Předchozí verze** zobrazte seznam snímků sdílené složky pro tento adresář.

1. Vyberte **otevřít** a otevřete snímek.

   ![Karta Předchozí verze](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Obnovení z předchozí verze

1. Vyberte **obnovit**. Tato akce zkopíruje obsah celého adresáře rekurzivně do původního umístění v okamžiku vytvoření snímku sdílené složky.

   ![Tlačítko Obnovit ve zprávě upozornění](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)
    
    > [!NOTE]
    > Pokud se soubor nezměnil, neuvidíte pro tento soubor předchozí verzi, protože tento soubor má stejnou verzi jako snímek. To je konzistentní s tím, jak funguje na souborovém serveru Windows.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití sdílené složky Azure s Windows](storage-how-to-use-files-windows.md)
