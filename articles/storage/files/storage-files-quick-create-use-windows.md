---
title: Vytvoření a použití sdílené složky Azure Files na virtuálních počítačích s Windows
description: Vytvořte a použijte sdílenou složku služby soubory Azure v Azure Portal. Připojte ho k virtuálnímu počítači s Windows, připojte se ke sdílené složce souborů a nahrajte soubor do sdílené složky soubory.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4c5629f80c37c9f79dc9a39c4d8304acbee9679d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489570"
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
1. V podokně účet úložiště vyberte **soubory** .

    ![Vybrat soubory](./media/storage-files-quick-create-use-windows/click-files.png)

1. Vyberte **sdílení souborů**.

    ![Vyberte tlačítko Přidat sdílení souborů.](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Pojmenujte novou sdílenou složku > *qsfileshare* zadejte "1" **kvót** > vyberte **vytvořit**. Kvóta může být maximálně 5 TiB, ale pro tento rychlý Start stačí pouze 1 GiB.
1. Na místním počítači vytvořte nový soubor txt s názvem *qsTestFile* .
1. Vyberte novou sdílenou složku a potom v umístění sdílené složky vyberte **Odeslat**.

    ![Nahrání souboru](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Přejděte do umístění, kde jste vytvořili soubor. txt > vyberte *qsTestFile.txt* > vyberte **Odeslat**.

Zatím jste vytvořili účet úložiště Azure a sdílenou složku s jedním souborem v Azure. V dalším kroku vytvoříte virtuální počítač Azure s Windows serverem 2016 Datacenter, který bude představovat místní server v tomto rychlém startu.

### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

1. Teď rozbalte nabídku na levé straně portálu a v levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Do vyhledávacího pole nad seznamem prostředků **Azure Marketplace** vyhledejte a vyberte **Windows Server 2016 Datacenter** a pak zvolte **vytvořit**.
1. Na kartě **základy** v části **Project Details (podrobnosti projektu**) vyberte skupinu prostředků, kterou jste pro tento rychlý Start vytvořili.

   ![Zadání základních informací o virtuálním počítači v okně portálu](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. V části **Podrobnosti instance** název virtuálního počítače *qsVM*.
1. U položek **Oblast**, **Možnosti dostupnosti**, **Image** a **Velikost** ponechte výchozí nastavení.
1. V části **účet správce** přidejte *VMadmin* jako **uživatelské jméno** a zadejte **heslo** pro virtuální počítač.
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
1. Zkopírujte obsah druhého pole a vložte ho do **poznámkového bloku**.

   ![Snímek obrazovky zobrazující obsah pole sekund, které byste měli zkopírovat a vložit do poznámkového bloku](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. Na virtuálním počítači otevřete **Průzkumníka souborů** a v okně vyberte **Tento počítač** . Tato volba změní nabídky, které jsou k dispozici na pásu karet. V nabídce **počítač** vyberte **mapovat síťovou jednotku**.
1. Vyberte písmeno jednotky a zadejte cestu UNC. Pokud jste sledovali návrhy pojmenování v tomto rychlém startu, zkopírujte z **poznámkového bloku** *\\ qsstorageacct. File. Core. Windows. net\qsfileshare* .

   Ujistěte se, že jsou zaškrtnuta obě políčka.

   ![Snímek obrazovky s dialogovým oknem Připojit síťovou jednotku](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Vyberte **Dokončit**.
1. V dialogovém okně **zabezpečení systému Windows** :

   - V programu Poznámkový blok zkopírujte název účtu úložiště s AZURE a vložte ho do dialogového okna **zabezpečení systému Windows** jako uživatelské jméno. Pokud jste sledovali návrhy pojmenování v tomto rychlém startu, zkopírujte *AZURE\qsstorageacct*.
   - V programu Poznámkový blok zkopírujte klíč účtu úložiště a vložte ho do dialogového okna **zabezpečení systému Windows** jako heslo.

      ![Cesta UNC z podokna Připojit služby Soubory Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Vytvoření snímku sdílené složky

Teď, když jste namapovali jednotku, můžete vytvořit snímek.

1. Na portálu přejděte do sdílené složky a vyberte **vytvořit snímek**.

   ![Vytvoření snímku](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Na virtuálním počítači otevřete *qstestfile.txt* a zadejte "Tento soubor byl upraven" > soubor uložte a zavřete.
1. Vytvořte další snímek.

## <a name="browse-a-share-snapshot"></a>Procházet snímek sdílené složky

1. Ve sdílené složce vyberte **Zobrazit snímky**.
1. V podokně **snímky sdílení souborů** vyberte první snímek v seznamu.

   ![Vybraný snímek v seznamu časových razítek](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. V podokně pro tento snímek vyberte *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Obnovení ze snímku

1. V okně snímku sdílení souborů klikněte pravým tlačítkem na *qsTestFile* a vyberte tlačítko **obnovit** .
1. Vyberte **přepsat původní soubor**.

   ![Tlačítka pro stažení a obnovení](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Na virtuálním počítači otevřete soubor. Neupravená verze byla obnovena.

## <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky

1. Ve sdílené složce vyberte **Zobrazit snímky**.
1. V podokně **snímky sdílení souborů** vyberte v seznamu poslední snímek a klikněte na **Odstranit**.

   ![Tlačítko Odstranit](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

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

   ![Tlačítko Obnovit ve zprávě upozornění ](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png) : Pokud se soubor nezměnil, neuvidíte pro tento soubor předchozí verzi, protože tento soubor má stejnou verzi jako snímek. To je konzistentní s tím, jak funguje na souborovém serveru Windows.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití sdílené složky Azure s Windows](storage-how-to-use-files-windows.md)
