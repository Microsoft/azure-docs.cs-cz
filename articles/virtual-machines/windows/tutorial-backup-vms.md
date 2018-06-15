---
title: Kurz zálohování virtuálních počítačů s Windows na portálu Azure Portal | Microsoft Docs
description: V tomto kurzu se naučíte, jak využít Azure Portal k ochraně virtuálních počítačů s Windows pomocí Azure Backup.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6ae014597a89c75e4426715227bbb19f1e98a438
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189285"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Kurz: Zálohování a obnova souborů pro virtuální počítače s Windows v Azure

Svá data můžete chránit prováděním záloh v pravidelných intervalech. Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo určité soubory. Tento článek vysvětluje, jak obnovit jeden soubor na virtuální počítač s Windows Serverem and IIS. Pokud ještě nemáte virtuální počítač, který byste mohli použít, můžete si ho vytvořit pomocí postupu v [rychlém startu pro Windows](quick-create-portal.md). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zálohy virtuálního počítače
> * Naplánování denního zálohování
> * Obnovení souboru ze zálohy

## <a name="backup-overview"></a>Přehled služby Backup

Když služba Azure Backup zahájí úlohu zálohování, aktivuje rozšíření zálohování, které pořídí snímek v daném okamžiku. Služba Azure Backup využívá rozšíření _VMSnapshot_. Toto rozšíření se nainstaluje při prvním zálohování virtuálního počítače, pokud je tento virtuální počítač spuštěný. Pokud virtuální počítač není spuštěný, služba Backup pořídí snímek základního úložiště (protože aplikace neprovádí žádné zápisy, když je virtuální počítač zastavený).

Při pořizování snímku virtuálních počítačů Windows se služba zálohování sladí se službou Stínová kopie svazku k získání konzistentního snímku disků virtuálního počítače. Jakmile služba Azure Backup pořídí snímek, data se přenesou do trezoru. Pro maximalizaci efektivity služba identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování.

Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.


## <a name="create-a-backup"></a>Vytvoření zálohy
Vytvořte jednoduché plánované denní zálohování do trezoru služby Recovery Services. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce na levé straně vyberte **Virtuální počítače**. 
3. V seznamu vyberte virtuální počítač, který chcete zálohovat.
4. V okně virtuálního počítače v části **Nastavení** klikněte na **Zálohování**. Otevře se okno **Povolit zálohování**.
5. V části **Trezor služby Recovery Services** klikněte na **Vytvořit nový** a zadejte název nového trezoru. Nový trezor se vytvoří ve stejné skupině prostředků a stejném umístění jako virtuální počítač.
6. Klikněte na **Zásady zálohování**. Pro účely tohoto příkladu ponechte výchozí hodnoty a klikněte na **OK**.
7. V okně **Povolit zálohování** klikněte na **Povolit zálohování**. Tím se vytvoří denní zálohování na základě výchozího plánu.
10. Pokud chcete vytvořit prvotní bod obnovení, v okně **Zálohování** klikněte na **Zálohovat nyní**.
11. V okně **Zálohovat nyní** klikněte na ikonu kalendáře, pomocí ovládacího prvku kalendáře vyberte poslední den uchování tohoto bodu obnovení a klikněte na **Zálohovat**.
12. V okně **Zálohování** vašeho virtuálního počítače se zobrazí počet dokončených bodů obnovení.

    ![Body obnovení](./media/tutorial-backup-vms/backup-complete.png)
    
První zálohování trvá přibližně 20 minut. Po dokončení zálohování přejděte k další části tohoto kurzu.

## <a name="recover-a-file"></a>Obnovení souboru

Pokud omylem odstraníte nebo změníte soubor, můžete ho pomocí obnovení souborů obnovit z úložiště záloh. Obnovení souborů připojí pomocí skriptu spuštěného na virtuálním počítači bod obnovení jako místní disk. Tyto disky zůstanou připojené po dobu 12 hodin, abyste z nich mohli zkopírovat soubory a obnovit je do virtuálního počítače.  

V tomto příkladu ukážeme, jak obnovit soubor s obrázkem, který se používá na výchozí webové stránce IIS. 

1. Otevřete prohlížeč a připojte se na IP adresu virtuálního počítače, aby se zobrazila výchozí stránka IIS.

    ![Výchozí webová stránka IIS](./media/tutorial-backup-vms/iis-working.png)

2. Připojte se k virtuálnímu počítači.
3. Ve virtuálním počítači otevřete **Průzkumníka souborů**, přejděte na \inetpub\wwwroot a odstraňte soubor **iisstart.png**.
4. Na svém místním počítači aktualizujte prohlížeč. Výchozí stránka IIS je pryč.

    ![Výchozí webová stránka IIS](./media/tutorial-backup-vms/iis-broken.png)

5. V místním počítači otevřete novou kartu a přejděte na [Azure Portal](https://portal.azure.com).
6. V nabídce vlevo vyberte **Virtuální počítače** a ze seznamu vyberte daný virtuální počítač.
8. V okně virtuálního počítače v části **Nastavení** klikněte na **Zálohování**. Otevře se okno **Zálohování**. 
9. V nabídce v horní části okna vyberte **Obnovení souborů**. Otevře se okno **Obnovení souborů**.
10. V části **Krok 1: Výběr bodu obnovení** vyberte z rozevíracího seznamu bod obnovení.
11. V části **Krok 2: Stažení skriptu pro procházení a obnovení souborů** klikněte na tlačítko **Stáhnout spustitelný soubor**. Uložte soubor do složky **Stažené soubory**.
12. V místním počítači otevřete **Průzkumníka souborů**, přejděte do složky **Stažené soubory** a zkopírujte na stažený soubor .exe. Název virtuálního počítače je uvedený na začátku názvu souboru. 
13. Ve virtuálním počítači (připojeném protokolem RDP) vložte soubor .exe na plochu. 
14. Přejdete na plochu virtuálního počítače a poklikejte na daný sobor .exe. Spustí se příkazový řádek. Připojte bod obnovení jako sdílenou složku, ke které máte přístup. Po vytvoření sdílené složky zavřete zadáním **q** příkazový řádek.
15. Ve virtuálním počítači otevřete **Průzkumníka souborů** a přejděte na písmeno jednotky použité pro sdílenou složku.
16. Přejděte do \inetpub\wwwroot a zkopírujte **iisstart.png** ze sdílené složky a vložte ho do \inetpub\wwwroot. Příklad: Zkopírujte F:\inetpub\wwwroot\iisstart.png a vložte ho do c:\inetpub\wwwroot k obnovení souboru.
17. Na místním počítači otevřete kartu prohlížeče, kde jste připojeni k IP adrese virtuálního počítače s výchozí stránkou IIS. Stisknutím Ctrl + F5 aktualizujte stránku v prohlížeči. Teď byste měli vidět, že se obrázek obnovil.
18. Na svém místním počítači se vraťte na kartu prohlížeče s webem Azure Portal a v části **Krok 3: Odpojení disků po obnovení** klikněte na tlačítko **Odpojit disky**. Pokud tento krok zapomenete provést, připojení k přípojnému bodu se automaticky ukončí po 12 hodinách. Po těchto 12 hodinách musíte pro vytvoření nového přípojného bodu stáhnout nový skript.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření zálohy virtuálního počítače
> * Naplánování denního zálohování
> * Obnovení souboru ze zálohy

V dalším kurzu najdete informace o monitorování virtuálních počítačů.

> [!div class="nextstepaction"]
> [Řízení virtuálních počítačů](tutorial-govern-resources.md)









