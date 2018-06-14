---
title: Připojení Azure úložiště file z virtuálního počítače Windows Azure | Microsoft Docs
description: Uložte soubor v cloudu s Azure file storage a připojte svou cloudovou sdílenou z Azure virtuálního počítače (VM).
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 8d537bdc882487784baef9f693e4677c76d3bd8d
ms.sourcegitcommit: 2e540e6acb953b1294d364f70aee73deaf047441
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2018
ms.locfileid: "27577547"
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Použít sdílené složky Azure s virtuálními počítači Windows 

Sdílené složky Azure můžete použít jako způsob, jak ukládat a přistupovat k souborům z virtuálního počítače. Můžete například uložit skript nebo konfiguračního souboru aplikace, které chcete všechny virtuální počítače do sdílené složky. V tomto článku jsme ukážeme, jak vytvořit a připojit sdílenou složku Azure a jak pro nahrávání a stahování souborů.

## <a name="connect-to-a-file-share-from-a-vm"></a>Připojení ke sdílené složce z virtuálního počítače

V této části se předpokládá, že už máte sdílenou složku, kterou chcete připojit k. Pokud potřebujete vytvořit, přečtěte si téma [vytvoření sdílené složky](#create-a-file-share) dále v tomto článku.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na tlačítko **účty úložiště**.
3. Vyberte svůj účet úložiště
4. V **přehled** v části **služby**, vyberte **soubory**.
5. Vyberte sdílenou složku nebo klikněte na tlačítko **+ sdílená** k vytvoření nové sdílené složky používat.
6. Klikněte na tlačítko **Connect** otevřete stránku, která ukazuje syntaxi příkazového řádku pro připojení sdílené složky z Windows nebo Linux.
7. V **písmeno jednotky**, vyberte písmeno, který chcete použít k identifikaci jednotku.
8. Vyberte, které syntaxe použít a vyberte tlačítko Kopírovat na pravé straně a zkopírujte ho do schránky. Vložte jej některém místě, kde můžete snadno k němu přístup. 
8. Připojte k virtuálnímu počítači a otevřete příkazový řádek.
9. Vložte v syntaxi upravená připojení a stiskněte tlačítko **Enter**.
10. Po vytvoření připojení, se zobrazí zpráva **příkaz úspěšně dokončil.**
11. Zkontrolujte připojení a to zadáním písmeno jednotky přepnout na tuto jednotku a pak zadejte **dir** zobrazí se obsah sdílené složky.



## <a name="create-a-file-share"></a>Vytvoření sdílené složky 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na tlačítko **účty úložiště**.
3. Vyberte svůj účet úložiště
4. V **přehled** v části **služby**, vyberte **soubory**.
5. Na stránce služby souborů, klikněte na tlačítko **+ sdílená**.
6. Zadejte název sdílené složky souborů. Názvy sdílených složek můžete použít, malá písmena, číslice a jeden pomlčky. Název nemůže začínat pomlčkou a nemůže používat více po sobě jdoucí pomlčky. 
7. Vyplňte omezení na tom, jak velký soubor může být, až 5120 GB.
8. Klikněte na tlačítko **OK** vytvoření sdílené složky.
   
## <a name="upload-files"></a>Nahrání souborů
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na tlačítko **účty úložiště**.
3. Vyberte svůj účet úložiště
4. V **přehled** v části **služby**, vyberte **soubory**.
5. Vyberte sdílenou složku.
6. Klikněte na tlačítko **nahrát** otevřete **nahrání souborů** stránky.
7. Klikněte na ikonu složky a přejděte do místního systému souborů pro soubor k odeslání.   
8. Klikněte na tlačítko **nahrát** nahrát soubor do sdílené složky.

## <a name="download-files"></a>Stažení souborů
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na tlačítko **účty úložiště**.
3. Vyberte svůj účet úložiště
4. V **přehled** v části **služby**, vyberte **soubory**.
5. Vyberte sdílenou složku.
6. Klikněte pravým tlačítkem na soubor a vyberte **Stáhnout** se stáhne do místního počítače.
   

## <a name="next-steps"></a>Další postup

Můžete také vytvořit a spravovat sdílené složky pomocí prostředí PowerShell. Další informace najdete v tématu [Začínáme s Azure File storage ve Windows](../../storage/files/storage-dotnet-how-to-use-files.md).
