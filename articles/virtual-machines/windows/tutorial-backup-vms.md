---
title: Zálohování virtuálních počítačů Azure Windows | Microsoft Docs
description: Virtuální počítače Windows Chraňte pomocí zálohování pomocí služby Azure Backup.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 12859bf967cf8de1b57ab9dfd5c0bd080806f2eb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="back-up-windows-virtual-machines-in-azure"></a>Zálohovat virtuální počítače s Windows v Azure

Svá data můžete chránit prováděním záloh v pravidelných intervalech. Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo jenom určité soubory. Tento článek vysvětluje, jak obnovit jeden soubor k virtuálnímu počítači spuštěný Windows Server a službu IIS. Pokud ještě nemáte virtuální počítač používat, můžete vytvořit jeden pomocí [rychlé spuštění Windows](quick-create-portal.md). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zálohy virtuálního počítače
> * Naplánování denního zálohování
> * Obnovení souboru ze zálohy




## <a name="backup-overview"></a>Přehled služby Backup

Když služba Azure Backup zahájí úlohu zálohování, aktivuje rozšíření zálohování k pořízení snímku v daném okamžiku. Použití služby Azure Backup _VMSnapshot_ rozšíření. Toto rozšíření se nainstaluje při prvním zálohování virtuálního počítače, pokud je tento virtuální počítač spuštěný. Pokud virtuální počítač není spuštěný, služba Backup pořídí snímek základního úložiště (protože aplikace neprovádí žádné zápisy, když je virtuální počítač zastavený).

Při pořizování snímku virtuálních počítačů Windows, služba zálohování koordinuje s Stínová kopie svazku Service (VSS) získat konzistentního snímku disky virtuálního počítače. Jakmile služba Azure Backup pořídí snímek, data se přenesou do trezoru. Pro maximalizaci efektivity služba identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování.

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
12. V okně **Zálohování** pro váš virtuální počítač se zobrazí počet dokončených bodů obnovení.

    ![Body obnovení](./media/tutorial-backup-vms/backup-complete.png)
    
První zálohování trvá přibližně 20 minut. Po dokončení zálohování přejděte k další části tohoto kurzu.

## <a name="recover-a-file"></a>Obnovit soubor

Pokud omylem odstraníte nebo změníte soubor, můžete ho pomocí obnovení souborů obnovit z úložiště záloh. Obnovení souborů používá skript, který běží na virtuální počítač, přípojný bod obnovení jako místní disk. Tyto disky zůstanou připojené po dobu 12 hodin, abyste z nich mohli zkopírovat soubory a obnovit je do virtuálního počítače.  

V tomto příkladu ukážeme, jak k obnovení souboru bitové kopie, který se používá v výchozí webové stránky pro službu IIS. 

1. Otevřete prohlížeč a připojte na adresu IP virtuálního počítače, který zobrazit výchozí stránka služby IIS.

    ![Výchozí služba IIS webové stránky](./media/tutorial-backup-vms/iis-working.png)

2. Připojte k virtuálnímu počítači.
3. Na virtuální počítač, otevřete **Průzkumníka souborů** , přejděte na \inetpub\wwwroot, odstraňte soubor **iisstart.png**.
4. V místním počítači aktualizujte prohlížeč a zjistíte, že je pryč bitovou kopii na výchozí stránka služby IIS.

    ![Výchozí služba IIS webové stránky](./media/tutorial-backup-vms/iis-broken.png)

5. V místním počítači, otevřete novou kartu a přejděte [portál Azure](https://portal.azure.com).
6. V nabídce na levé straně vyberte **virtuální počítače** a vyberte virtuální počítač ze seznamu.
8. V okně virtuálního počítače v části **Nastavení** klikněte na **Zálohování**. Otevře se okno **Zálohování**. 
9. V nabídce v horní části okna vyberte **Obnovení souborů**. Otevře se okno **Obnovení souborů**.
10. V části **Krok 1: Výběr bodu obnovení** vyberte z rozevíracího seznamu bod obnovení.
11. V části **Krok 2: Stažení skriptu pro procházení a obnovení souborů** klikněte na tlačítko **Stáhnout spustitelný soubor**. Uložení souboru do vaší **stáhne** složky.
12. V místním počítači, otevřete **Průzkumníka souborů** a přejděte do vaší **stáhne** složky a zkopírujte na stažený soubor .exe. Název souboru bude obsahovat předponu název vašeho virtuálního počítače. 
13. Na vašem virtuálním počítači (přes připojení RDP) vložte soubor .exe na ploše virtuálního počítače. 
14. Přejděte na ploše virtuálního počítače a dvakrát klikněte na .exe. Se spustí příkazový řádek a pak připojte jako sdílené složky, který je k dispozici bod obnovení. Po dokončení vytváření sdílené složky, zadejte **q** zavřete příkazovém řádku.
15. Na vašem virtuálním počítači otevřete **Průzkumníka souborů** a přejděte na písmeno jednotky, která byla použita pro sdílené složky.
16. Přejděte do \inetpub\wwwroot a zkopírujte **iisstart.png** ze souboru sdílet a vložte jej do \inetpub\wwwroot. Například F:\inetpub\wwwroot\iisstart.png zkopírujte a vložte jej do c:\inetpub\wwwroot k obnovení souboru.
17. V místním počítači otevřete kartu prohlížeče, kde jste připojeni k IP adresu virtuálního počítače zobrazující výchozí stránka služby IIS. Stisknutím Ctrl + F5 aktualizujte stránku v prohlížeči. Teď byste měli vidět, že byla obnovena bitovou kopii.
18. Na svém místním počítači se vraťte na kartu prohlížeče s webem Azure Portal a v části **Krok 3: Odpojení disků po obnovení** klikněte na tlačítko **Odpojit disky**. Pokud zapomenete tento krok, připojení k přípojný bod je automaticky zavřít po 12 hodinách. Po těchto 12 hodinách musíte pro vytvoření nového přípojného bodu stáhnout nový skript.


## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření zálohy virtuálního počítače
> * Naplánování denního zálohování
> * Obnovení souboru ze zálohy

V dalším kurzu najdete informace o monitorování virtuálních počítačů.

> [!div class="nextstepaction"]
> [Řízení virtuálních počítačů](tutorial-govern-resources.md)









