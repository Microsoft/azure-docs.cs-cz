---
title: Rychlý start, vytvořit a používat sdílené složky Azure pro Windows | Dokumentace Microsoftu
description: V tomto rychlém startu můžete vytvořit a používat sdílené složky Azure pro Windows.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: wgries
ms.component: files
ms.openlocfilehash: 141a8c9d63d3f0fd615ec0648b15c669f28f7118
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663991"
---
# <a name="quickstart-create-and-use-an-azure-file-share-for-windows"></a>Rychlý start: Vytvoření a použití sdílené složky Azure pro Windows
Tento článek ukazuje základní kroky pro vytvoření a použití sdílené složky Azure. V tomto rychlém startu důraz je o rychle nastavení sdílené složky Azure, takže můžete vyzkoušet, jak služba funguje. Pokud potřebujete podrobnější pokyny pro vytváření a používání Azure sdílených položek ve vašem prostředí, najdete v článku [sdílenými složkami Azure pomocí služby Windows](storage-how-to-use-files-windows.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure
Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="prepare-your-environment"></a>Příprava prostředí
Před vytvořením sdílené složky Azure budete muset nastavit následující položky pro tento rychlý start:

- Účet úložiště Azure a sdílené složky Azure
- Virtuální počítač Windows Server 2016 Datacenter

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Než začnete pracovat se sdílenými složkami Azure, budete muset vytvořit účet úložiště Azure. Účet úložiště je sdílený fond úložiště, ve kterém můžete nasazovat sdílené složky Azure nebo jiné prostředky úložiště, jako jsou objekty blob nebo fronty. Účet úložiště může obsahovat neomezený počet sdílených složek. Sdílená složka může obsahovat neomezený počet souborů až do výše maximální kapacity účtu úložiště.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Dále vytvoříte sdílenou složku.

1. Po dokončení nasazení účtu úložiště Azure vyberte **přejít k prostředku**.
1. Vyberte **soubory** z podokna účtu úložiště.

    ![Vyberte soubory](./media/storage-files-quick-create-use-windows/click-files.png)

1. Vyberte **+ sdílená složka,**.

    ![Vyberte tlačítko Přidat sdílenou složku souboru](./media/storage-files-quick-create-use-windows/create-file-share.png)

1. Název nové sdílené složky *qsfileshare* > zadejte "1" **kvóty** > vyberte **vytvořit**. Kvóta může být maximálně 5 TiB, ale potřebujete jenom 1 GB pro tento rychlý start.
1. Vytvořte nový soubor txt *qsTestFile* na místním počítači.
1. Vyberte novou sdílenou složku a potom na umístění sdílené složky souboru **nahrát**.

    ![Nahrání souboru](./media/storage-files-quick-create-use-windows/create-file-share-portal5.png)

1. Přejděte do umístění, ve které jste vytvořili soubor .txt > vyberte *qsTestFile.txt* > vyberte **nahrát**.

Zatím jste vytvořili účet úložiště Azure a sdílenou složku s jedním souborem v něm v Azure. Dále vytvoříte virtuální počítač Azure s Windows Server 2016 Datacenter znázornit na místním serveru v rámci tohoto rychlého startu.

### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače
1. Teď rozbalte nabídku na levé straně portálu a v levém horním rohu webu Azure Portal zvolte **Vytvořit prostředek**.
1. Ve vyhledávacím poli nad seznamem prostředků **Azure Marketplace** vyhledejte a vyberte **Windows Server 2016 Datacenter** a pak zvolte **Vytvořit**.
1. V **Základy** ve skupině **podrobnosti o projektu**, vyberte skupinu prostředků, kterou jste vytvořili pro tento rychlý start.

   ![Zadání základních informací o virtuálním počítači v okně portálu](./media/storage-files-quick-create-use-windows/vm-resource-group-and-subscription.png)

1. V části **Instance podrobnosti**, pojmenujte virtuální počítač *qsVM*.
1. U položek **Oblast**, **Možnosti dostupnosti**, **Image** a **Velikost** ponechte výchozí nastavení.
1. V části **účet správce**, přidejte *VMadmin* jako **uživatelské jméno** a zadejte **heslo** pro virtuální počítač.
1. V části **Pravidla portů pro příchozí spojení** vyberte **Povolit vybrané porty** a pak z rozevíracího seznamu vyberte **RDP (3389)** a **HTTP**.
1. Vyberte **Zkontrolovat a vytvořit**.
1. Vyberte **Vytvořit**. Vytvoření nového virtuálního počítače bude trvat několik minut.

1. Po dokončení nasazení virtuálního počítače vyberte **přejít k prostředku**.

Právě jste vytvořili nový virtuální počítač a připojili jste datový disk. Teď je potřeba se k tomuto virtuálnímu počítači připojit.

### <a name="connect-to-your-vm"></a>Připojení k virtuálnímu počítači

1. Vyberte **připojit** na stránce vlastností virtuálního počítače.

   ![Připojení k virtuálnímu počítači Azure z portálu](./media/storage-files-quick-create-use-windows/connect-vm.png)

1. V **připojit k virtuálnímu počítači** stránce, ponechte výchozí nastavení pro připojení s **IP adresu** přes **číslo portu** *3389* a vyberte **Soubor stáhnout RDP**.
1. Otevřete stažený soubor RDP a vyberte **připojit** po zobrazení výzvy.
1. V okně **Zabezpečení systému Windows** vyberte **Další možnosti** a pak **Použít jiný účet**. Zadejte uživatelské jméno jako *localhost\username*, kde &lt;uživatelské jméno&gt; je uživatelské jméno správce virtuálního počítače je vytvořený pro virtuální počítač. Zadejte heslo, které jste vytvořili pro virtuální počítač a pak vyberte **OK**.

   ![Další možnosti](./media/storage-files-quick-create-use-windows/local-host2.png)

1. Během procesu přihlášení se může zobrazit upozornění certifikátu. Vyberte **Ano** nebo **pokračovat** k vytvoření připojení.

## <a name="map-the-azure-file-share-to-a-windows-drive"></a>Mapování sdílené složky Azure na jednotce Windows

1. Na webu Azure Portal, přejděte *qsfileshare* sdílení souborů a vyberte **připojit**.
1. Obsah do druhého pole zkopírujte a vložte ji **Poznámkový blok**.

   ![Cesta UNC z podokna Připojit služby Soubory Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect2.png)

1. Ve virtuálním počítači, otevřete **Průzkumníka souborů** a vyberte **tento počítač** v okně. Tato volba se změní v nabídkách, které jsou k dispozici na na pásu karet. Na **počítače** nabídce vyberte možnost **připojit síťovou jednotku**.
1. Vyberte písmeno jednotky a zadejte cestu UNC. Pokud jste postupovali podle návrhy pojmenování v rámci tohoto rychlého startu, zkopírujte  *\\qsstorageacct.file.core.windows.net\qsfileshare* z **Poznámkový blok**.

   Ujistěte se, že jsou kontrolovány obou políček.

   ![Snímek obrazovky s dialogovým oknem Připojit síťovou jednotku](./media/storage-files-quick-create-use-windows/mountonwindows10.png)

1. Vyberte **Finish** (Dokončit).
1. V **zabezpečení Windows** dialogové okno:

   - V programu Poznámkový blok, zkopírujte hodnotu název účtu úložiště předponou AZURE\ a vložte ji **zabezpečení Windows** dialogové okno jako uživatelské jméno. Pokud jste postupovali podle návrhy pojmenování v rámci tohoto rychlého startu, zkopírujte *AZURE\qsstorageacct*.
   - V programu Poznámkový blok, zkopírujte klíč účtu úložiště a vložte ji **zabezpečení Windows** dialogové okno jako heslo.

      ![Cesta UNC z podokna Připojit služby Soubory Azure](./media/storage-files-quick-create-use-windows/portal_netuse_connect3.png)

## <a name="create-a-share-snapshot"></a>Vytvořit snímek sdílené složky
Teď, když jste změnili na jednotce, můžete vytvořit snímek.

1. Na portálu přejděte do sdílené složky a vyberte **vytvořit snímek**.

   ![Vytvoření snímku](./media/storage-files-quick-create-use-windows/create-snapshot.png)

1. Ve virtuálním počítači, otevřete *qstestfile.txt* a zadejte "Tento soubor byl změněn" > uložte a zavřete soubor.
1. Vytváření jiného snímku.

## <a name="browse-a-share-snapshot"></a>Procházet snímek sdílené složky

1. Ve sdílené složce, vyberte **zobrazit snímky**.
1. Na **snímky sdílené složky** podokně, vyberte první snímek v seznamu.

   ![Vybraný snímek v seznamu časová razítka](./media/storage-files-quick-create-use-windows/snapshot-list.png)

1. V podokně pro tento snímek vyberte *qsTestFile.txt*.

## <a name="restore-from-a-snapshot"></a>Obnovení ze snímku

1. Z portálu, vyberte *qsTestFile* > vyberte **obnovení** tlačítko.
1. Vyberte **přepsat původní soubor**.

   ![Stahování a obnovení tlačítka](./media/storage-files-quick-create-use-windows/snapshot-download-restore-portal.png)

1. Ve virtuálním počítači otevřete soubor. Bylo obnoveno verzí bez úprav.

## <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky

1. Ve sdílené složce, vyberte **zobrazit snímky**.
1. Na **snímky sdílené složky** podokně, vyberte poslední snímek v seznamu a klikněte na tlačítko **odstranit**.

   ![Tlačítko Odstranit](./media/storage-files-quick-create-use-windows/portal-snapshots-delete.png)

## <a name="use-a-share-snapshot-in-windows"></a>Použití snímku sdílené složky ve Windows
Stejně jako místní snímky VSS, se zobrazí snímky z připojené sdílené složky Azure pomocí karty předchozí verze.

1. V Průzkumníku souborů vyhledejte připojené sdílené složky.

   ![Připojené sdílené složky v Průzkumníku souborů](./media/storage-files-quick-create-use-windows/snapshot-windows-mount.png)

1. Vyberte *qsTestFile.txt* a > klikněte pravým tlačítkem a vyberte **vlastnosti** z nabídky.

   ![Nabídka pro vybraný adresář zobrazená po kliknutí pravým tlačítkem](./media/storage-files-quick-create-use-windows/snapshot-windows-previous-versions.png)

1. Výběrem karty **Předchozí verze** zobrazte seznam snímků sdílené složky pro tento adresář.

1. Vyberte **otevřete** otevřete snímku.

   ![Karta Předchozí verze](./media/storage-files-quick-create-use-windows/snapshot-windows-list.png)

## <a name="restore-from-a-previous-version"></a>Obnovení z předchozí verze

1. Vyberte **obnovení**. Tato akce zkopíruje obsah rekurzivně celý adresář do původního umístění v době, kdy byl vytvořen snímek sdílené složky.

   ![Obnovit tlačítko v upozornění](./media/storage-files-quick-create-use-windows/snapshot-windows-restore.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použití sdílené složky Azure s Windows](storage-how-to-use-files-windows.md)