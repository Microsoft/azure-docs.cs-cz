---
title: Povolení a vytváření velkých sdílených složek – soubory Azure
description: V tomto článku se dozvíte, jak povolit a vytvářet velké sdílené složky.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518418"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Jak povolit a vytvořit velké sdílené složky souborů

Původně se standardní sdílené složky mohly škálovat až na 5 TiB, teď s velkými sdílenými složkami, můžou se škálovat až na 100 TiB. Ve výchozím nastavení se sdílené složky Premium škálují až na 100 TiB. 

Aby bylo možné škálovat až 100 TiB pomocí standardních sdílených složek, musíte povolit, aby váš účet úložiště používal velké sdílené složky. Můžete buď povolit existující účet, nebo vytvořit nový účet, abyste mohli používat velké sdílené složky.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="restrictions"></a>Omezení

Účty s povolenými velkými sdílenými složkami podporují LRS nebo ZRS. V současné době nepodporují GZRS, GRS nebo RA-GRS žádné účty s povolenými velkými sdílenými složkami. Povolení velkých sdílených složek na účtu je nevratný proces, jakmile je povolený, nejde váš účet převést na GZRS, GRS nebo RA-GRS.

## <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště

Přihlaste se na web [Azure Portal](https://portal.azure.com).

1. Na webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **Účty úložiště**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Účty úložiště**.
1. V okně **Účty úložiště**, které se zobrazí, zvolte **Přidat**.
1. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
1. Pod polem **Skupina prostředků** vyberte **Vytvořit novou**. Zadejte název nové skupiny prostředků, jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky znázorňující postup vytvoření skupiny prostředků na portálu](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název také musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.
1. Vyberte umístění účtu úložiště a ujistěte se, že se jedná [o jednu z oblastí podporovaných pro LFS](storage-files-planning.md#regional-availability).
1. Nastavte replikaci buď na **místně redundantní úložiště** , nebo na **redundantní úložiště v zóně**.
1. Tato pole nechte nastavená na výchozí hodnoty:

   |Pole  |Hodnota  |
   |---------|---------|
   |Model nasazení     |Správce prostředků         |
   |Výkon     |Úroveň Standard         |
   |Account kind (Druh účtu)     |StorageV2 (obecné účely v2)         |
   |Vrstva přístupu     |Hot         |

1. Vyberte **Upřesnit** a zaškrtněte políčko **povoleno** pro **velké sdílené složky**.
1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.

    ![Large-File-Shares-Advanced-Enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Vyberte **Create** (Vytvořit).

## <a name="enable-on-existing-account"></a>Povolit pro existující účet

U stávajících účtů můžete také povolit velké sdílené složky. Pokud to uděláte, účet už nebude možné převést na GZRS, GRS nebo RA-GRS. Tato volba je na tomto účtu nevratná.

1. Otevřete [Azure Portal](https://portal.azure.com) a přejděte k účtu úložiště, na kterém chcete povolit velké sdílené složky.
1. Otevřete účet úložiště a vyberte **Konfigurace**.
1. Zaškrtněte políčko **povoleno** u **velkých sdílených složek**a pak vyberte Uložit.
1. Vyberte **Přehled** a vyberte **aktualizovat**.

![Enable-large-File-Shares-on-existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

V účtu úložiště jste teď povolili velké sdílené složky.

Pokud se zobrazí následující chyba: "pro tento účet ještě nejsou k dispozici velké sdílené složky." Můžete buď počkat určitou dobu, protože vaše oblast je pravděpodobně uprostřed dokončení zavedení, nebo pokud máte naléhavé požadavky, obraťte se na podporu.

## <a name="create-a-large-file-share"></a>Vytvoření velké sdílené složky

Vytváření velkých sdílených složek je skoro stejné jako vytvoření standardní sdílené složky. Hlavním rozdílem je, že můžete nastavit kvótu až 100 TiB.

1. Z účtu úložiště vyberte **sdílené složky**.
1. Vyberte **+ sdílení souborů**.
1. Zadejte název sdílené složky a (volitelně) velikost kvóty, kterou chcete, až 100 TiB, a pak vyberte **vytvořit**. 

![Large-File-Shares-Create-Share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>Rozbalit existující sdílené složky

Až v účtu úložiště povolíte velké sdílené složky, můžete rozšířit stávající sdílené složky na vyšší kvótu.

1. Z účtu úložiště vyberte **sdílené složky**.
1. Klikněte pravým tlačítkem na sdílenou složku a vyberte možnost **kvóta**.
1. Zadejte novou velikost, kterou si přejete, a pak vyberte **OK**.

![Update-large-File-Share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>Další kroky

* [Připojení ke sdílené složce a její připojení – Windows](storage-how-to-use-files-windows.md)
* [Připojení ke sdílené složce a její připojení – Linux](../storage-how-to-use-files-linux.md)
* [Připojení ke sdílené složce a její připojení – macOS](storage-how-to-use-files-mac.md)