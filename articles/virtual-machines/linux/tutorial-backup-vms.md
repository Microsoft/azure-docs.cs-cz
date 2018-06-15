---
title: Kurz zálohování virtuálních počítačů s Linuxem na portálu Azure | Microsoft Docs
description: V tomto kurzu se naučíte, jak využít portál Azure k ochraně virtuálních počítačů s Linuxem pomocí Azure Backup.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c91e2b1380e5048fa1dfb7a0e028c88e589cbaa4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32190553"
---
# <a name="tutorial-back-up-and-restore-files-for-linux-virtual-machines-in-azure"></a>Kurz: Zálohování a obnova souborů pro virtuální počítače s Linuxem v Azure

Svá data můžete chránit prováděním záloh v pravidelných intervalech. Azure Backup vytváří body obnovení, které se ukládají v geograficky redundantních trezorech obnovení. Při obnovení z bodu obnovení můžete obnovit celý virtuální počítač nebo určité soubory. Tento článek vysvětluje, jak obnovit jeden soubor na virtuální počítač s Linuxem a serverem NGINX. Pokud ještě nemáte virtuální počítač, který byste mohli použít, můžete si ho vytvořit pomocí postupu v [rychlém startu pro Linux](quick-create-cli.md). V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zálohy virtuálního počítače
> * Naplánování denního zálohování
> * Obnovení souboru ze zálohy

## <a name="backup-overview"></a>Přehled služby Backup

Když služba Azure Backup zahájí zálohování, aktivuje rozšíření zálohování, které pořídí snímek v daném okamžiku. Služba Azure Backup využívá rozšíření _VMSnapshotLinux_ v Linuxu. Toto rozšíření se nainstaluje při prvním zálohování virtuálního počítače, pokud je tento virtuální počítač spuštěný. Pokud virtuální počítač není spuštěný, služba Backup pořídí snímek základního úložiště (protože aplikace neprovádí žádné zápisy, když je virtuální počítač zastavený).

Ve výchozím nastavení služba Azure Backup pořídí zálohu virtuálního počítače s Linuxem konzistentní vzhledem k systému souborů, ale je možné ji nakonfigurovat tak, aby prováděla [zálohování konzistentní vzhledem k aplikacím pomocí rozhraní s předzálohovacími a pozálohovacími skripty](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). Jakmile služba Azure Backup pořídí snímek, data se přenesou do trezoru. Pro maximalizaci efektivity služba identifikuje a přenese pouze bloky dat, které se změnily od posledního zálohování.

Po dokončení přenosu dat se snímek odstraní a vytvoří se bod obnovení.


## <a name="create-a-backup"></a>Vytvoření zálohy
Vytvořte plánované denní zálohování do trezoru služby Recovery Services:

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

## <a name="restore-a-file"></a>Obnovení souboru

Pokud omylem odstraníte nebo změníte soubor, můžete ho pomocí obnovení souborů obnovit z úložiště záloh. Obnovení souborů pomocí skriptu spuštěného na virtuálním počítači připojí bod obnovení jako místní disk. Tyto disky zůstanou připojené po dobu 12 hodin, abyste z nich mohli zkopírovat soubory a obnovit je do virtuálního počítače.  

V tomto příkladu ukazujeme obnovení výchozí webové stránky serveru NGINX /var/www/html/index.nginx-debian.html. Veřejná IP adresa virtuálního počítače v tomto příkladu je *13.69.75.209*. IP adresu svého virtuálního počítače najdete pomocí tohoto příkazu:

 ```bash 
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. Na svém místním počítači otevřete prohlížeč a zadejte do něj veřejnou IP adresu svého virtuálního počítače. Zobrazí se výchozí webová stránka serveru NGINX.

    ![Výchozí webová stránka serveru NGINX](./media/tutorial-backup-vms/nginx-working.png)

1. Připojte se k virtuálnímu počítači přes SSH.

    ```bash
    ssh 13.69.75.209
    ```
2. Odstraňte soubor /var/www/html/index.nginx-debian.html.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. Na svém místním počítači aktualizujte prohlížeč stisknutím Ctrl + F5. Výchozí stránka serveru NGINX bude pryč.

    ![Výchozí webová stránka serveru NGINX](./media/tutorial-backup-vms/nginx-broken.png)
    
1. Na svém místním počítači se přihlaste k webu [Azure Portal](https://portal.azure.com/).
6. V nabídce na levé straně vyberte **Virtuální počítače**. 
7. V seznamu vyberte virtuální počítač.
8. V okně virtuálního počítače v části **Nastavení** klikněte na **Zálohování**. Otevře se okno **Zálohování**. 
9. V nabídce v horní části okna vyberte **Obnovení souborů**. Otevře se okno **Obnovení souborů**.
10. V části **Krok 1: Výběr bodu obnovení** vyberte z rozevíracího seznamu bod obnovení.
11. V části **Krok 2: Stažení skriptu pro procházení a obnovení souborů** klikněte na tlačítko **Stáhnout spustitelný soubor**. Uložte stažený soubor na svůj místní počítač.
7. Kliknutím na **Stáhnout skript** místně stáhněte soubor skriptu.
8. Otevřete příkazový řádek Bash a zadejte následující, přičemž nahraďte *Linux_myVM_05-05-2017.sh* za správnou cestu a název souboru staženého skriptu, *azureuser* za uživatelné jméno pro virtuální počítač a *13.69.75.209* za veřejnou IP adresu vašeho virtuálního počítače.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. Na svém místním počítači otevřete k virtuálnímu počítači připojení SSH.

    ```bash
    ssh 13.69.75.209
    ```
    
10. Na svém virtuálním počítači přidejte oprávnění ke spuštění souboru skriptu.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. Na svém virtuálním počítači spusťte skript, který připojí bod obnovení jako systém souborů.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. Výstup skriptu obsahuje cestu k přípojnému bodu. Výstup vypadá podobně jako tento:

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. Na svém virtuálním počítači zkopírujte výchozí webovou stránku serveru NGINX z přípojného bodu do umístění, ze kterého jste soubor odstranili.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. Na svém místním počítači otevřete kartu prohlížeče, kde jste připojeni k IP adrese virtuálního počítače s výchozí stránkou serveru NGINX. Stisknutím Ctrl + F5 aktualizujte stránku v prohlížeči. Teď by se opět měla zobrazit funkční výchozí stránka.

    ![Výchozí webová stránka serveru NGINX](./media/tutorial-backup-vms/nginx-working.png)

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

