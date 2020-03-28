---
title: Výuka – Zálohování virtuálních počítačů s Windows na webu Azure Portal
description: V tomto kurzu se naučíte, jak využít Azure Portal k ochraně virtuálních počítačů s Windows pomocí Azure Backup.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 89ed0bad2729a9e0983d4ef7f8a53faa4f5426ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79415645"
---
# <a name="tutorial-back-up-and-restore-files-for-windows-virtual-machines-in-azure"></a>Kurz: Zálohování a obnova souborů pro virtuální počítače s Windows v Azure

Svá data můžete chránit prováděním záloh v pravidelných intervalech. Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo určité soubory. Tento článek vysvětluje, jak obnovit jeden soubor na virtuální počítač s Windows Serverem and IIS. Pokud ještě nemáte virtuální počítač, který byste mohli použít, můžete si ho vytvořit pomocí postupu v [rychlém startu pro Windows](quick-create-portal.md). Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Vytvoření zálohy virtuálního počítače
> * Naplánování denního zálohování
> * Obnovení souboru ze zálohy

## <a name="backup-overview"></a>Přehled služby Backup

Když služba Azure Backup zahájí úlohu zálohování, aktivuje rozšíření zálohování, které pořídí snímek v daném okamžiku. Služba Azure Backup používá [rozšíření VMSnapshot](https://docs.microsoft.com/azure/virtual-machines/extensions/vmsnapshot-windows). Toto rozšíření se nainstaluje při prvním zálohování virtuálního počítače, pokud je tento virtuální počítač spuštěný. Pokud virtuální počítač není spuštěný, služba Backup pořídí snímek základního úložiště (protože aplikace neprovádí žádné zápisy, když je virtuální počítač zastavený).

Při pořizování snímku virtuálních počítačů Windows se služba zálohování sladí se službou Stínová kopie svazku k získání konzistentního snímku disků virtuálního počítače. Jakmile služba Azure Backup pořídí snímek, data se přenesou do trezoru. Pro maximalizaci efektivity služba identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování.

Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.

## <a name="create-a-backup"></a>Vytvoření zálohy
Vytvořte jednoduché plánované denní zálohování do trezoru služby Recovery Services. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. V nabídce na levé straně vyberte **Virtuální počítače**. 
1. V seznamu vyberte virtuální počítač, který chcete zálohovat.
1. V okně Virtuální počítač klikněte v části **Operace** na **položku Zálohování**. Otevře se okno **Povolit zálohování.**
1. V části **Trezor služby Recovery Services** klikněte na **Vytvořit nový** a zadejte název nového trezoru. Nový trezor se vytvoří ve stejné skupině prostředků a umístění jako virtuální počítač.
1. V **části Zvolit zásady zálohování**zachovejte výchozí **(Nový) DailyPolicy**a klepněte na tlačítko **Povolit zálohování**.
1. Pokud chcete vytvořit prvotní bod obnovení, v okně **Zálohování** klikněte na **Zálohovat nyní**.
1. V okně **Backup Now** klepněte na ikonu kalendáře, pomocí ovládacího prvku kalendáře zvolte, jak dlouho bude bod obnovení zachován, a klepněte na tlačítko **OK**.
1. V okně **Zálohování** pro virtuální počítač uvidíte počet bodů obnovení, které jsou dokončené.


    ![Body obnovení](./media/tutorial-backup-vms/backup-complete.png)
    
První zálohování trvá přibližně 20 minut. Po dokončení zálohování přejděte k další části tohoto kurzu.

## <a name="recover-a-file"></a>Obnovení souboru

Pokud omylem odstraníte nebo změníte soubor, můžete ho pomocí obnovení souborů obnovit z úložiště záloh. Obnovení souborů připojí pomocí skriptu spuštěného na virtuálním počítači bod obnovení jako místní disk. Tyto disky zůstanou připojené po dobu 12 hodin, abyste z nich mohli zkopírovat soubory a obnovit je do virtuálního počítače.  

V tomto příkladu ukážeme, jak obnovit soubor s obrázkem, který se používá na výchozí webové stránce IIS. 

1. Otevřete prohlížeč a připojte se na IP adresu virtuálního počítače, aby se zobrazila výchozí stránka IIS.

    ![Výchozí webová stránka IIS](./media/tutorial-backup-vms/iis-working.png)

1. Připojte se k virtuálnímu počítači.
1. Ve virtuálním počítači otevřete **Průzkumníka souborů**, přejděte na \inetpub\wwwroot a odstraňte soubor **iisstart.png**.
1. Na svém místním počítači aktualizujte prohlížeč. Výchozí stránka IIS je pryč.

    ![Výchozí webová stránka IIS](./media/tutorial-backup-vms/iis-broken.png)

1. V místním počítači otevřete novou kartu a přejděte na [Azure Portal](https://portal.azure.com).
1. V nabídce vlevo vyberte **Virtuální počítače** a ze seznamu vyberte daný virtuální počítač.
1. V okně Virtuální počítač klikněte v části **Operace** na **položku Zálohování**. Otevře se okno **Zálohování**. 
1. V nabídce v horní části okna vyberte **Obnovení souborů**. Otevře se okno **Obnovení souborů**.
1. V části **Krok 1: Výběr bodu obnovení** vyberte z rozevíracího seznamu bod obnovení.
1. V části **Krok 2: Stažení skriptu pro procházení a obnovení souborů** klikněte na tlačítko **Stáhnout spustitelný soubor**. Zkopírujte heslo k souboru a uložte jej na bezpečném místě.
1. V místním počítači otevřete **Průzkumníka souborů**, přejděte do složky **Stažené soubory** a zkopírujte na stažený soubor .exe. Název virtuálního počítače je uvedený na začátku názvu souboru. 
1. Na vašem virtuálním počítači (pomocí připojení RDP) vložte soubor EXE na plochu virtuálního počítače. 
1. Přejdete na plochu virtuálního počítače a poklikejte na daný sobor .exe. Spustí se příkazový řádek. Program připojí bod obnovení jako sdílenou složku, ke které máte přístup. Po vytvoření sdílené složky zavřete zadáním **q** příkazový řádek.
1. Ve virtuálním počítači otevřete **Průzkumníka souborů** a přejděte na písmeno jednotky použité pro sdílenou složku.
1. Přejděte do \inetpub\wwwroot a zkopírujte **iisstart.png** ze sdílené složky a vložte ho do \inetpub\wwwroot. Příklad: Zkopírujte F:\inetpub\wwwroot\iisstart.png a vložte ho do c:\inetpub\wwwroot k obnovení souboru.
1. Na místním počítači otevřete kartu prohlížeče, kde jste připojeni k IP adrese virtuálního počítače s výchozí stránkou IIS. Stisknutím Ctrl + F5 aktualizujte stránku v prohlížeči. Teď byste měli vidět, že se obrázek obnovil.
1. Na svém místním počítači se vraťte na kartu prohlížeče s webem Azure Portal a v části **Krok 3: Odpojení disků po obnovení** klikněte na tlačítko **Odpojit disky**. Pokud tento krok zapomenete provést, připojení k přípojnému bodu se automaticky ukončí po 12 hodinách. Po uplynutí těchto 12 hodin je třeba stáhnout nový skript a vytvořit tak nový přípojný bod.





## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření zálohy virtuálního počítače
> * Naplánování denního zálohování
> * Obnovení souboru ze zálohy

V dalším kurzu najdete informace o monitorování virtuálních počítačů.

> [!div class="nextstepaction"]
> [Řízení virtuálních počítačů](tutorial-govern-resources.md)









