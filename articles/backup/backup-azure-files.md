---
title: Zálohování sdílených složek Azure
description: Tento článek podrobně popisuje, jak zálohovat a obnovovat sdílené složky Azure, a vysvětluje úlohy správy.
services: backup
author: markgalioto
ms.author: markgal
ms.date: 3/23/2018
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 9697bd5a55a5cfcdcd6958f8baff85e55c880c87
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287656"
---
# <a name="back-up-azure-file-shares"></a>Zálohování sdílených složek Azure
Tento článek vysvětluje, jak pomocí webu Azure Portal zálohovat a obnovovat [sdílené složky Azure](../storage/files/storage-files-introduction.md).

V této příručce se naučíte:
> [!div class="checklist"]
> * Konfigurace trezoru služby Recovery Services pro zálohování Souborů Azure
> * Spuštění úlohy zálohování na vyžádání pro vytvoření bodu obnovení
> * Obnovení souboru nebo souborů z bodu obnovení
> * Správa úloh zálohování
> * Zastavení ochrany Souborů Azure
> * Odstranění zálohovaných dat

## <a name="prerequisites"></a>Požadavky
Než budete moct zálohovat sdílenou složku Azure, ujistěte se, že se nachází v jednom z [podporovaných typů účtu úložiště](backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Po ověření můžete chránit své sdílené složky.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Omezení zálohování sdílených složek Azure během období Preview
Zálohování sdílených složek Azure je ve verzi Preview. Následující scénáře zálohování se nepodporují u sdílených složek Azure:
- Nemůžete chránit sdílené složky Azure v účtech úložiště s replikací do [geograficky redundantního úložiště jen pro čtení](../storage/common/storage-redundancy-grs.md) (RA-GRS)*.
- Nemůžete chránit sdílené složky Azure v účtech úložiště s povolenými virtuálními sítěmi nebo bránou firewall.
- Pro ochranu souborů Azure pomocí služby Azure Backup není k dispozici PowerShell ani rozhraní příkazového řádku.
- Maximální počet plánovaných záloh je jedna za den.
- Maximální počet záloh na vyžádání jsou čtyři za den.
- Používejte v účtu úložiště [zámky prostředků](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest), abyste zabránili nechtěnému odstranění záloh v trezoru služby Recovery Services.
- Neodstraňujte snímky vytvořené službou Azure Backup. Odstranění snímků může způsobit ztrátu bodů obnovení nebo selhání obnovení.

\*Sdílené složky Azure v účtech úložiště s funkcí replikace do [geograficky redundantního úložiště jen pro čtení](../storage/common/storage-redundancy-grs.md) (RA-GRS) jako GRS a účtované za ceny GRS

Zálohování pro sdílené složky Azure v účtech úložiště s replikací do [zónově redundantního úložiště](../storage/common/storage-redundancy-zrs.md) (ZRS) je aktuálně k dispozici jenom v oblastech Střed USA (CUS) a USA – východ 2 (EUS2)

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurace zálohování sdílené složky Azure
Veškerá zálohovaná data se ukládají v trezorech služby Recovery Services. Tento kurz předpokládá, že už máte vytvořenou sdílenou složku Azure. Zálohování sdílené složky Azure:

1. Vytvořte trezor služby Recovery Services ve stejné oblasti, ve které je vaše sdílená složka. Pokud už trezor máte, otevřete stránku Přehled vašeho trezoru a klikněte na **Zálohování**.

    ![Volba sdílené složky Azure jako cíle zálohování](./media/backup-file-shares/overview-backup-page.png)

2. V nabídce Cíl zálohování v rozevíracím seznamu **Co chcete zálohovat?** zvolte Sdílená složka Azure.

    ![Volba sdílené složky Azure jako cíle zálohování](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Kliknutím na **Zálohovat** nakonfigurujte zálohování sdílené složky Azure do vašeho trezoru služby Recovery Services. 

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/set-backup-goal.png)

    Po přidružení trezoru ke sdílené složce Azure se otevře nabídka Zálohování a zobrazí výzva k výběru účtu úložiště. V nabídce se zobrazí všechny podporované účty úložiště v oblasti, ve které je váš trezor, které ještě nejsou přidružené k žádnému trezoru služby Recovery Services.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/list-of-storage-accounts.png)

4. V seznamu účtů úložiště vyberte účet a klikněte na **OK**. Azure v účtu úložiště vyhledá sdílené složky, které je možné zálohovat. Pokud jste sdílené složky přidali nedávno a v seznamu je nevidíte, chvíli počkejte, než se sdílené složky zobrazí.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/discover-file-shares.png)

5. V seznamu **Sdílené složky** vyberte jednu nebo několik sdílených složek, které chcete zálohovat, a klikněte na **OK**.

6. Po zvolení sdílených složek se nabídka Zálohování přepne na **Zásady zálohování**. V této nabídce buď vyberte existující zásadu zálohování, nebo vytvořte novou, a pak klikněte na **Povolit zálohování**. 

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/apply-backup-policy.png)

    Po vytvoření zásady zálohování se v naplánovaném čase pořídí snímek sdílených složek a po zvolenou dobu se bude uchovávat bod obnovení.

## <a name="create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání
Příležitostně můžete chtít vygenerovat snímek zálohy nebo bod obnovení mimo dobu naplánovanou v zásadě zálohování. Obvyklá doba pro vygenerování zálohy na vyžádání je ihned po konfiguraci zásady zálohování. V závislosti na plánu v zásadě zálohování může pořízení snímku trvat hodiny nebo dny. Pokud chcete svá data chránit před zapojením zásady zálohování, vyvolejte zálohování na vyžádání. Vytvoření zálohy na vyžádání se často vyžaduje před provedením plánovaných změn sdílených složek. 

### <a name="to-create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

1. Otevřete trezor služby Recovery Services obsahující body obnovení sdílené složky a klikněte na **Zálohování položek**. Zobrazí se seznam typů zálohovaných položek.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/list-of-backup-items.png)

2. V seznamu vyberte **Azure Storage (Soubory Azure)**. Zobrazí se seznam sdílených složek Azure.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. V seznamu sdílených složek Azure vyberte požadovanou sdílenou složku. Otevře se nabídka Zálohovaná položka pro vybranou sdílenou složku.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/backup-item-menu.png)

4. V nabídce Zálohovaná položka klikněte na **Zálohovat nyní**. Protože se jedná o úlohu zálohování na vyžádání, není k bodu obnovení přidružená žádná zásada uchovávání. Otevře se dialogové okno **Zálohovat nyní**. Zadejte poslední den, ke kterému chcete uchovávat bod obnovení. 
  
   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Obnovení sdílené složky Azure ze zálohy
Pokud potřebujete obnovit celou sdílenou složku nebo jednotlivé soubory nebo složky z bodu obnovení, přejděte do části Zálohovaná položka, jak je popsáno v předchozí části. Zvolte **Obnovit sdílenou složku** a obnovte celou sdílenou složku k požadovanému bodu v čase. Ze seznamu bodů obnovení, který se zobrazí, vyberte některý bod obnovení, abyste mohli přepsat aktuální sdílenou složku, nebo obnovte tuto sdílenou složku do alternativní sdílené složky ve stejné oblasti.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Obnovení jednotlivých souborů nebo složek ze zálohy sdílených složek Azure
Azure Backup poskytuje možnost procházet bod obnovení v rámci webu Azure Portal. Pokud chcete obnovit požadovaný soubor nebo složku, klikněte na Obnovení souborů na stránce Zálohovaná položka a zvolte některý ze seznamu bodů obnovení. Vyberte cíl obnovení a pak klikněte na **Vybrat soubor**, abyste mohli procházet bod obnovení. Vyberte požadovaný soubor nebo složku a klikněte na **Obnovit**.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Správa záloh sdílených složek Azure

Na stránce **Úlohy zálohování** můžete spustit několik úloh správy záloh sdílených složek, včetně:
- [Monitorování úloh](backup-azure-files.md#monitor-jobs)
- [Vytvoření nové zásady](backup-azure-files.md#create-a-new-policy)
- [Zastavení ochrany sdílené složky](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [Obnovení ochrany sdílené složky](backup-azure-files.md#resume-protection-for-azure-file-share)
- [Odstranění zálohovaných dat](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Monitorování úloh

Průběh všech úloh můžete sledovat na stránce **Úlohy zálohování**.

Otevření stránky **Úlohy zálohování**:

- Otevřete trezor služby Recovery Services, který chcete monitorovat, v nabídce trezoru služby Recovery Services klikněte na **Úlohy** a pak klikněte na **Úlohy zálohování**.
   ![Výběr úlohy, kterou chcete monitorovat](./media/backup-file-shares/open-backup-jobs.png)

    Zobrazí se seznam úloh zálohování a stav jednotlivých úloh.
   ![Výběr úlohy, kterou chcete monitorovat](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Vytvoření nové zásady

Novou zásadu pro zálohování sdílených složek Azure můžete vytvořit v části **Zásady zálohování** trezoru služby Recovery Services. Všechny zásady vytvořené při konfiguraci zálohování sdílených složek se zobrazí s typem zásady Sdílená složka Azure.

Zobrazení existujících zásad zálohování:

- Otevřete požadovaný trezor služby Recovery Services a v nabídce trezoru služby Recovery Services klikněte na **Zásady zálohování**. Vypíšou se všechny zásady zálohování.

   ![Výběr úlohy, kterou chcete monitorovat](./media/backup-file-shares/list-of-backup-policies.png)

Vytvoření nové zásady zálohování:

1. V nabídce trezoru služby Recovery Services klikněte na **Zásady zálohování**.
2. V seznamu zásad zálohování klikněte na **Přidat**.

   ![Výběr úlohy, kterou chcete monitorovat](./media/backup-file-shares/new-backup-policy.png)

3. V nabídce **Přidat** vyberte **Sdílená složka Azure**. Otevře se nabídka Zásady zálohování pro sdílenou složku Azure. Zadejte název zásady, frekvenci zálohování a rozsah uchovávání bodů obnovení. Po nadefinování zásady klikněte na OK.

   ![Výběr úlohy, kterou chcete monitorovat](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Zastavení ochrany sdílené složky Azure

Pokud se rozhodnete zastavit ochranu sdílené složky Azure, zobrazí se výzva, jestli chcete zachovat body obnovení. Ochranu sdílených složek Azure můžete zastavit dvěma způsoby:

- Zastavit všechny budoucí úlohy zálohování a odstranit všechny body obnovení, nebo
- zastavit všechny budoucí úlohy zálohování, ale ponechat body obnovení.

S ponecháním bodů obnovení v úložišti můžou být spojené náklady, protože se zachovají základní snímky vytvořené službou Azure Backup. Výhodou ponechání bodů obnovení však je, že v případě potřeby můžete později sdílenou složku obnovit. Informace o nákladech na ponechání bodů obnovení najdete v podrobnostech o cenách. Pokud se rozhodnete odstranit všechny body obnovení, nebudete moct sdílenou složku obnovit.

Zastavení ochrany sdílené složky Azure:

1. Otevřete trezor služby Recovery Services obsahující body obnovení sdílené složky a klikněte na **Zálohování položek**. Zobrazí se seznam typů zálohovaných položek.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/list-of-backup-items.png) 

2. V seznamu **Typ správy záloh** vyberte **Azure Storage (Soubory Azure)**. Zobrazí se seznam zálohovaných položek pro (Azure Storage (Soubory Azure)).

   ![otevření další nabídky kliknutím na položku](./media/backup-file-shares/azure-file-share-backup-items.png) 

3. V seznamu Zálohované položky (Azure Storage (Soubory Azure)) vyberte zálohovanou položku, kterou chcete zastavit.

4. V položkách sdílené složky Azure klikněte na nabídku **Další** a vyberte **Zastavit zálohování**. 

   ![otevření další nabídky kliknutím na položku](./media/backup-file-shares/stop-backup.png)

5. V nabídce Zastavit zálohování zvolte možnost **Zachovat** nebo **Odstranit zálohovaná data** a klikněte na **Zastavit zálohování**.

   ![otevření další nabídky kliknutím na položku](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Obnovení ochrany sdílené složky Azure

Pokud jste při zastavování ochrany sdílené složky zvolili možnost Zachovat zálohovaná data, pak je možné ochranu obnovit. Pokud jste zvolili možnost **Odstranit zálohovaná data**, pak ochranu sdílené složky obnovit nejde.

Pokud chcete obnovit ochranu sdílené složky, přejděte do části Zálohovaná položka a klikněte na Obnovit zálohování. Otevřou se Zásady zálohování, kde můžete zvolit požadovanou zásadu pro obnovení zálohování.

   ![Výběr úlohy, kterou chcete monitorovat](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Odstranění zálohovaných dat 

Zálohu sdílené složky můžete odstranit během úlohy zastavení zálohování nebo kdykoli po zastavení ochrany. Před odstraněním bodů obnovení dokonce může být užitečné několik dnů nebo týdnů počkat. Na rozdíl od obnovování bodů obnovení nemůžete při odstraňování zálohovaných dat zvolit konkrétní body obnovení, které se mají odstranit. Pokud se rozhodnete odstranit zálohovaná data, odstraníte všechny body obnovení přidružené k příslušné položce.

Následující postup předpokládá, že se úloha zálohování virtuálního počítače zastavila. Po zastavení úlohy zálohování budou na řídicím panelu Zálohovaná položka dostupné možnosti Obnovit zálohování a Odstranit zálohovaná data. Klikněte na Odstranit zálohovaná data a zadáním názvu sdílené složky potvrďte odstranění. Volitelně zadejte Důvod odstranění nebo Komentář.

## <a name="see-also"></a>Viz také
Další informace o sdílených složkách Azure najdete tady:
- [Nejčastější dotazy k zálohování sdílených složek Azure](backup-azure-files-faq.md)
- [Řešení potíží se zálohováním sdílených složek Azure](troubleshoot-azure-files.md)
