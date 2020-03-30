---
title: Vytvoření a použití sdílené složky Azure Files na virtuálních počítačích s Windows
description: V tomto rychlém startu nastavíte sdílené složky Azure Files na webu Azure Portal a připojíte ho k virtuálnímu počítači s Windows. Připojíte se ke sdílené složce Soubory a nahrajete soubor do sdílené složky Soubory. Potom pořizovat snímek sdílené složky Soubory, upravit soubor ve sdílené složce Soubory a obnovit předchozí snímek sdílené složky soubory.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bbab0ee2eefe6e86c150d5bddab4f8e91a7c92d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75463912"
---
# <a name="quickstart-create-and-manage-azure-files-share-with-windows-virtual-machines"></a>Úvodní příručka: Vytváření a správa sdílení souborů Azure s virtuálními počítači s Windows

Článek ukazuje základní kroky pro vytváření a používání sdílené složky Azure Files. V tomto rychlém startu je kladen důraz na rychlé nastavení sdílené složky Souborů Azure, abyste mohli zaznamenat, jak služba funguje. Pokud potřebujete podrobnější pokyny pro vytváření a používání sdílených složek Azure ve vlastním prostředí, přečtěte si část [Použití sdílené složky Azure s Windows](storage-how-to-use-files-windows.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Příprava prostředí

V tomto rychlém startu nastavíte následující položky:

- Účet úložiště Azure a sdílená složka Azure
- Virtuální ms a virtuální msa datového centra Windows Serveru 2016

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Než budete moci pracovat se sdílenou složkou Azure, musíte vytvořit účet úložiště Azure. Účet úložiště pro obecné účely v2 poskytuje přístup ke všem službám Azure Storage: objektům BLOB, souborům, frontám a tabulkám. Rychlý start vytvoří účet úložiště pro obecné účely v2, ale kroky k vytvoření libovolného typu účtu úložiště jsou podobné. Účet úložiště může obsahovat neomezený počet sdílených složek. Sdílená složka může obsahovat neomezený počet souborů až do výše maximální kapacity účtu úložiště.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Dále vytvoříte sdílenou složku.

1. Po dokončení nasazení účtu úložiště Azure vyberte **Přejít na prostředek**.
1. V podokně účtu úložiště vyberte **Soubory.**

    ![Vybrat soubory](./media/storage-files-quick-create-use-windows/click-files.png)

1. Vyberte **Sdílená složka**.

    ![Výběr tlačítka Přidat sdílení souborů](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Pojmenujte novou sdílenou složku *qsfileshare* > zadejte "1" pro **> kvóty** vyberte **Vytvořit**. Kvóta může být maximálně 5 TiB, ale pro tento rychlý start potřebujete pouze 1 GiB.
1. Vytvořte nový soubor txt s názvem *qsTestFile* v místním počítači.
1. Vyberte novou sdílenou složku a v umístění sdílené složky vyberte **Nahrát**.

    ![Nahrání souboru](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Přejděte do umístění, ve kterém jste vytvořili soubor TXT > vyberte *možnost qsTestFile.txt* > vyberte **možnost Nahrát**.

Zatím jste vytvořili účet úložiště Azure a sdílení souborů s jedním souborem v něm v Azure. Dále vytvoříte virtuální počítač Azure s Datovým centrem Windows Server 2016, který bude v tomto rychlém startu reprezentovat místní server.

### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače

1. Teď rozbalte nabídku na levé straně portálu a v levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Ve vyhledávacím poli nad seznamem prostředků **Azure Marketplace** vyhledejte a vyberte Windows **Server 2016 Datacenter**a pak zvolte **Vytvořit**.
1. Na kartě **Základy** vyberte v části **Podrobnosti projektu**skupinu zdrojů, kterou jste pro tento rychlý start vytvořili.

   ![Zadání základních informací o virtuálním počítači v okně portálu](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. V **části Podrobnosti instance**pojmenujte virtuální ho *virtuálního virtuálního virtuálního data v qsVM*.
1. U položek **Oblast**, **Možnosti dostupnosti**, **Image** a **Velikost** ponechte výchozí nastavení.
1. V části **Účet správce**přidejte *VMadmin* jako **uživatelské jméno** a zadejte **heslo** pro virtuální ho.
1. V části **Pravidla portů pro příchozí spojení** vyberte **Povolit vybrané porty** a pak z rozevíracího seznamu vyberte **RDP (3389)** a **HTTP**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**. Vytvoření nového virtuálního počítače bude trvat několik minut.

1. Po dokončení nasazení virtuálního počítače vyberte **Přejít na prostředek**.

Právě jste vytvořili nový virtuální počítač a připojili jste datový disk. Teď je potřeba se k tomuto virtuálnímu počítači připojit.

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

1. Na stránce vlastností virtuálního počítače vyberte **Připojit.**

   ![Připojení k virtuálnímu počítači Azure z portálu](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. Na stránce **Připojit k virtuálnímu počítači** ponechte výchozí možnosti pro připojení pomocí **IP adresy** přes **port číslo** *3389* a vyberte **Stáhnout soubor RDP**.
1. Otevřete stažený soubor RDP a po zobrazení výzvy vyberte **Připojit.**
1. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno jako *localhost\username*, kde &lt;uživatelské jméno&gt; je uživatelské jméno správce virtuálního počítače, které jste vytvořili pro virtuální počítač. Zadejte heslo, které jste pro virtuální počítač vytvořili, a pak vyberte **OK**.

   ![Další možnosti](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Chcete-li vytvořit připojení, vyberte **Ano** nebo **Pokračovat.**

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapování sdílené složky Azure na jednotku s Windows

1. Na webu Azure Portal přejděte na *soubor qsfileshare* a vyberte **Připojit**.
1. Zkopírujte obsah druhého pole a vložte jej do **poznámkového bloku**.

   ![Cesta UNC z podokna Připojit služby Soubory Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. Ve virtuálním počítači otevřete **Průzkumníka souborů** a v okně vyberte **Tento počítač.** Tento výběr změní nabídky dostupné na pásu karet. V nabídce **Počítač** vyberte **Možnost Mapovat síťovou jednotku**.
1. Vyberte písmeno jednotky a zadejte cestu UNC. Pokud jste v tomto rychlém startu postupovali podle návrhů na pojmenování, zkopírujte **Notepad** * \\z programu Poznámkový blok soubor qsstorageacct.file.core.windows.net\qsfileshare* .

   Zkontrolujte, zda jsou zaškrtnuta obě zaškrtávací políčka.

   ![Snímek obrazovky s dialogovým oknem Připojit síťovou jednotku](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Vyberte **Finish** (Dokončit).
1. V dialogovém okně **Zabezpečení systému Windows:**

   - Z programu Poznámkový blok zkopírujte název účtu úložiště před připraveným službou AZURE\ a vložte jej do dialogového okna **Zabezpečení systému Windows** jako uživatelské jméno. Pokud jste v tomto rychlém startu postupovali podle návrhů na pojmenování, zkopírujte *azure\qsstorageacct*.
   - Z programu Poznámkový blok zkopírujte klíč účtu úložiště a vložte jej do dialogového okna **Zabezpečení systému Windows** jako heslo.

      ![Cesta UNC z podokna Připojit služby Soubory Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Vytvoření snímku sdílené složky

Teď, když jste namapovali jednotku, můžete vytvořit snímek.

1. Na portálu přejděte na sdílenou složku a vyberte **Vytvořit snímek**.

   ![Vytvoření snímku](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Ve virtuálním provozu otevřete *soubor qstestfile.txt* a zadejte "tento soubor byl změněn" > Uložte a zavřete soubor.
1. Vytvořte další snímek.

## <a name="browse-a-share-snapshot"></a>Procházení snímku sdílené složky

1. Ve sdílené složce vyberte **Zobrazit snímky**.
1. V podokně **Snímky sdílené složky** vyberte první snímek v seznamu.

   ![Vybraný snímek v seznamu časových razítek](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. V podokně pro tento snímek vyberte *soubor qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Obnovení ze snímku

1. V okně snímku sdílené složky klepněte pravým tlačítkem myši na *soubor qsTestFile*a vyberte tlačítko **Obnovit.**
1. Vyberte **Přepsat původní soubor**.

   ![Tlačítka pro stažení a obnovení](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Ve virtuálním provozu otevřete soubor. Nezměněná verze byla obnovena.

## <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky

1. Ve sdílené složce vyberte **Zobrazit snímky**.
1. V podokně **Snímky sdílené složky** vyberte poslední snímek v seznamu a klepněte na **tlačítko Odstranit**.

   ![Tlačítko Odstranit](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Použití snímku sdílené složky ve Windows

Stejně jako u místních snímků VSS můžete zobrazit snímky z připojené sdílené složky Azure pomocí karty Předchozí verze.

1. V Průzkumníkovi souborů vyhledejte připojenou sdílenou složku.

   ![Připojená sdílená složka v Průzkumníkovi souborů](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Vyberte *soubor qsTestFile.txt* a > klepněte pravým tlačítkem myši a v nabídce vyberte **vlastnosti.**

   ![Nabídka pro vybraný adresář zobrazená po kliknutí pravým tlačítkem](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Výběrem karty **Předchozí verze** zobrazte seznam snímků sdílené složky pro tento adresář.

1. Výběrem **možnosti Otevřít** otevřete snímek.

   ![Karta Předchozí verze](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Obnovení z předchozí verze

1. Vyberte **obnovit**. Tato akce rekurzivně zkopíruje obsah celého adresáře do původního umístění v době, kdy byl vytvořen snímek sdílené složky.

   ![Tlačítko Obnovit ve](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png) varovné zprávě Poznámka:Pokud se soubor nezměnil, nezobrazí se předchozí verze tohoto souboru, protože tento soubor má stejnou verzi jako snímek. To je v souladu s tím, jak to funguje na souborovém serveru systému Windows.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití sdílené složky Azure s Windows](storage-how-to-use-files-windows.md)
