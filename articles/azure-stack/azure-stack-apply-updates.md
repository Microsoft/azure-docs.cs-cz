---
title: Aktualizace v zásobníku Azure | Microsoft Docs
description: Zjistěte, jak importovat a nainstalovat balíčky aktualizací společnosti Microsoft pro systém Azure zásobníku integrované.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: mabrigg
ms.openlocfilehash: 0f23216c6aced60dc651a0f10179281bc9a29c2c
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
ms.locfileid: "29802647"
---
# <a name="apply-updates-in-azure-stack"></a>Aktualizace v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy*

Jako operátor zásobník Azure můžete použít služby Microsoft update, které balíčky pro zásobník Azure pomocí aktualizace dlaždici na portálu správce. Musí stáhnout balíček aktualizace Microsoft, můžete importovat soubory balíčku do protokolů Azure a pak instalaci balíčku aktualizace. 

## <a name="download-the-update-package"></a>Stáhněte si balíček aktualizace

Je-li balíček aktualizací společnosti Microsoft pro zásobník Azure k dispozici, stáhněte si balíček do umístění, který je dosažitelný z Azure zásobníku a zkontrolovat obsah balíčku. Balíček aktualizace se obvykle skládá z následujících souborů:

- Samorozbalovací *PackageName*soubor .exe. Tento soubor obsahuje datové části pro aktualizaci, například nejnovější kumulativní aktualizace pro systém Windows Server.   
- Odpovídající *PackageName*.bin soubory. Tyto soubory poskytují komprese datové části, který je spojen s *PackageName*soubor .exe. 
- Soubor Metadata.xml. Tento soubor obsahuje základní informace o aktualizaci, třeba vydavatele, název, požadovaných součástí, velikost a cesta URL podpory.

## <a name="import-and-install-updates"></a>Importovat a instalovat aktualizace

Následující postup ukazuje, jak importovat a nainstalovat balíčky aktualizací na portálu správce.

> [!IMPORTANT]
> Důrazně doporučujeme upozornit uživatele žádné operace údržby a naplánovat normální údržbu jiný pracovní dobu co nejvíc. Operace údržby může mít vliv na portálu operace a úlohy uživatele.

1. V portálu správce, vyberte **další služby**. Potom v části **Data + úložiště** kategorie, vyberte **účty úložiště**. (Nebo do pole Filtr začněte zadávat text **účty úložiště**a vyberte jej.)

    ![Ukazuje, kde najít na portálu účtů úložiště](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. Do pole filtru zadejte **aktualizace**a vyberte **updateadminaccount** účet úložiště.

    ![Ukazuje, jak hledat updateadminaccount](media/azure-stack-apply-updates/ApplyUpdates2.png)

3. V úložišti účet podrobnosti najdete v části **služby**, vyberte **objekty BLOB**.
 
    ![Ukazuje, jak získat na objekty BLOB pro účet úložiště](media/azure-stack-apply-updates/ApplyUpdates3.png) 
 
4. V části **služba objektů Blob**, vyberte **+ kontejner** vytvořit kontejner. Zadejte název (například *. aktualizace 1709*) a potom vyberte **OK**.
 
     ![Ukazuje, jak přidat do kontejneru v účtu úložiště](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Po vytvoření kontejneru, klikněte na název kontejneru a pak klikněte na tlačítko **nahrát** chcete nahrát soubory balíčku do kontejneru.
 
    ![Ukazuje, jak nahrát soubory balíčku](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. V části **nahrávání blob**, klikněte na ikonu složky, vyhledejte soubor .exe balíček aktualizace a pak klikněte na tlačítko **otevřete** v okně Průzkumníka souborů.
  
7. V části **nahrávání blob**, klikněte na tlačítko **nahrát**. 
 
    ![Ukazuje, kde kvůli nahrání každého souboru balíčku](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Opakujte kroky 6 a 7 pro *PackageName*.bin a Metadata.xml soubory. Neimportujte dodatečné Notice.txt soubor, pokud zahrnuty.
9. Až budete hotoví, můžete zkontrolovat oznámení (ikonu zvonku v pravém horním rohu portálu). Oznámení by měl znamenat, že dokončení nahrávání. 
10. Přejděte zpět na aktualizace dlaždice na řídicím panelu. Na dlaždici by měl znamenat, že aktualizace k dispozici. Kliknutím na dlaždici Zkontrolujte nově přidané aktualizace balíčku.
11. K instalaci aktualizace, vyberte balíček, který je označen jako **připraven** a buď klikněte pravým tlačítkem na balíček a vyberte **nyní aktualizovat**, nebo klikněte na tlačítko **nyní aktualizovat** akce v horní .
12. Když kliknete na instalaci balíčku aktualizace, můžete zobrazit stav na **podrobnosti o spuštění aktualizace** oblasti. Tady můžete také kliknout na **stažení protokolů o úplné** ke stažení souborů protokolu.
13. Po dokončení aktualizace dlaždici aktualizace zobrazí aktualizovaná verze zásobník Azure.

## <a name="next-steps"></a>Další postup

- [Správa aktualizací v přehledu Azure zásobníku](azure-stack-updates.md)
- [Údržba zásad Azure zásobníku](azure-stack-servicing-policy.md)
