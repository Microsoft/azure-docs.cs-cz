---
title: Připojení Azure file storage z virtuálního počítače Windows Azure | Dokumentace Microsoftu
description: Store souboru v cloudu s Azure file storage a připojte svou cloudovou sdílenou z virtuálního počítače Azure (VM).
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
ms.subservice: files
ms.openlocfilehash: d79a2d33157d9e2a6f56eb18d061f71a7dcd635c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462235"
---
# <a name="use-azure-file-shares-with-windows-vms"></a>Použití sdílených složek Azure s virtuálními počítači Windows 

Sdílené složky Azure můžete použít jako způsob, jak ukládat a přistupovat k souborům z vašeho virtuálního počítače. Můžete například uložit skript nebo konfiguračního souboru aplikace, které chcete, aby všechny virtuální počítače do sdílené složky. V tomto článku ukážeme, jak vytvořit a připojit sdílené složky Azure a jak nahrávání a stahování souborů.

## <a name="connect-to-a-file-share-from-a-vm"></a>Připojení ke sdílené složce z virtuálního počítače

Této části se předpokládá, že už máte sdílenou složku, která se chcete připojit. Pokud je potřeba ho vytvořit, naleznete v tématu [vytvoření sdílené složky](#create-a-file-share) dále v tomto článku.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na tlačítko **účty úložiště**.
3. Vyberte svůj účet úložiště
4. V **přehled** stránce v části **služby**vyberte **soubory**.
5. Vybrat sdílenou složku nebo klikněte na tlačítko **+ sdílená** vytvořit novou sdílenou složku používat.
6. Klikněte na tlačítko **připojit** otevřete stránku, která ukazuje syntaxi příkazového řádku pro připojení sdílené složky z Windows nebo Linux.
7. V **písmeno jednotky**, vyberte písmeno, který chcete použít k identifikaci jednotce.
8. Zvolte který syntaxi a klikněte na tlačítko kopírování na pravé straně zkopírujte do schránky. Vložte některá místo, kde můžete snadno k němu přístup. 
8. Připojte se ke svému virtuálnímu počítači a otevřete příkazový řádek.
9. Vložením syntaxe upravené připojení a spuštění **Enter**.
10. Po vytvoření připojení se zobrazí zpráva **příkaz byl úspěšně dokončen.**
11. Zkontrolujte připojení tak, že zadáte písmeno jednotky můžete přepínat na tuto jednotku a pak zadejte **dir** zobrazit obsah sdílené složky.



## <a name="create-a-file-share"></a>Vytvoření sdílené složky 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na tlačítko **účty úložiště**.
3. Vyberte svůj účet úložiště
4. V **přehled** stránce v části **služby**vyberte **soubory**.
5. Na stránce služby souborů, klikněte na **+ sdílená**.
6. Zadejte název sdílené složky souborů. Názvy sdílených složek můžete použít malá písmena, číslice a spojovníky. Název nemůže začínat spojovníkem a nemůže použít víc po sobě jdoucí pomlčky. 
7. Zadejte omezení velikosti souboru může být, až 5120 GB.
8. Klikněte na tlačítko **OK** k vytvoření sdílené složky.
   
## <a name="upload-files"></a>Nahrání souborů
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na tlačítko **účty úložiště**.
3. Vyberte svůj účet úložiště
4. V **přehled** stránce v části **služby**vyberte **soubory**.
5. Vyberte sdílenou složku.
6. Klikněte na tlačítko **nahrát** otevřít **nahrávání souborů** stránky.
7. Klikněte na ikonu složky a přejděte do místního systému souborů pro soubor k odeslání.   
8. Klikněte na tlačítko **nahrát** pro nahrání souboru do sdílené složky.

## <a name="download-files"></a>Stažení souborů
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce vlevo klikněte na tlačítko **účty úložiště**.
3. Vyberte svůj účet úložiště
4. V **přehled** stránce v části **služby**vyberte **soubory**.
5. Vyberte sdílenou složku.
6. Klikněte pravým tlačítkem na soubor a zvolte **Stáhnout** ho můžete stáhnout do svého místního počítače.
   

## <a name="next-steps"></a>Další postup

Můžete také vytvořit a spravovat sdílené složky pomocí Powershellu. Další informace najdete v tématu [Začínáme s Azure File storage ve Windows](../../storage/files/storage-dotnet-how-to-use-files.md).
