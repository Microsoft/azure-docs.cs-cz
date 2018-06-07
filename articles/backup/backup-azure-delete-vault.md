---
title: Odstranit trezor služeb zotavení v Azure.
description: Tento článek vysvětluje, jak odstranit trezor služeb zotavení. Článek obsahuje kroky řešení potíží, když se pokuste odstranit trezor, ale nemůže.
services: service-name
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: markgal
ms.openlocfilehash: 844a70aa6fe003c6ad5816aaec9c32db9104c620
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605336"
---
# <a name="delete-a-recovery-services-vault"></a>Odstranění trezoru služby Recovery Services
Tento článek vysvětluje, jak odstranit trezor služeb zotavení na portálu Azure. Pokud jste měli trezory Backup, byla převedena do trezory služeb zotavení.   

Odstranění trezoru služeb zotavení je jednoduchý proces - *zadaný Trezor neobsahuje žádné prostředky*. Než budete moct odstranit trezor služeb zotavení, musíte odebrat nebo odstranit všechny prostředky v trezoru. Pokud se pokusíte odstranit trezoru, který obsahuje prostředky, dojde k chybě jako na následujícím obrázku:

![Chyba při odstraňování trezoru](./media/backup-azure-delete-vault/vault-deletion-error.png) <br/>

Dokud jste vymazali prostředky z trezoru, kliknutím na tlačítko **opakujte** vytváří ke stejné chybě. Pokud jste se zablokuje na tato chybová zpráva, klikněte na tlačítko **zrušit** a pomocí následujících kroků můžete odstranit prostředky v trezoru.

## <a name="removing-items-from-a-vault-protecting-a-vm"></a>Odebírání položek z trezoru ochranu virtuálního počítače
Pokud již máte otevřete trezor služeb zotavení, pokračujte druhém kroku.

1. Otevřete portál Azure a na řídicím panelu otevřete úložišti, které chcete odstranit.

   Pokud nemáte trezor služeb zotavení připnuli k řídicímu panelu, v nabídce centra klikněte na tlačítko **více služeb** a v seznamu prostředků zadejte **služeb zotavení**. Seznam se průběžně filtruje podle zadávaného textu. Klikněte na **Trezory služby Recovery Services**.

   ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-delete-vault/open-recovery-services-vault.png) <br/>

   Zobrazí se seznam trezorů služeb zotavení. V seznamu vyberte trezor, které chcete odstranit.

   ![Vyberte možnost trezoru ze seznamu](./media/backup-azure-work-with-vaults/choose-vault-to-delete.png)
2. V zobrazení trezoru, podívejte se na **Essentials** podokně. Pokud chcete odstranit trezor, nesmí být žádné chráněné položky. Pokud **zálohování položek** nebo **zálohování serverů pro správu** nezobrazovat nule, je nutné odebrat tyto položky. Úložiště nelze odstranit, pokud obsahuje data.

    ![Podívejte se na chráněných položkách v podokně Essentials](./media/backup-azure-delete-vault/contoso-bkpvault-settings.png)

    Virtuální počítače a soubory a složky jsou považovány za zálohování položek a jsou uvedeny v **zálohování položek** plochu podokna Essentials. DPM server je uveden v **serveru pro správu zálohování** plochu podokna Essentials. **Replikované položky** se týkají služby Azure Site Recovery.
3. Pokud chcete začít, odebrání chráněné položky z trezoru, zjistí položky v trezoru. V řídícím panelu trezoru klikněte na **nastavení**a potom klikněte na **zálohování položek** otevření této nabídky.

    ![Vyberte možnost trezoru ze seznamu](./media/backup-azure-delete-vault/open-settings-and-backup-items.png)

    **Zálohování položek** má samostatné seznamy, v závislosti na typu položky nabídky: virtuální počítače Azure nebo složek souborů (viz obrázek). Výchozí typ položky seznamu zobrazí je virtuálních počítačích Azure. Chcete-li zobrazit seznam položek souborů složek v trezoru, vyberte **složek souborů** z rozevírací nabídky.
4. Před odstraněním položky z trezoru ochranu virtuálního počítače, musíte zastavit úlohu zálohování položky a odstranit data bodu obnovení. Pro každou položku v trezoru postupujte takto:

    a. Na **položky zálohování** nabídky, klikněte pravým tlačítkem položku a v místní nabídce vyberte **zastavení zálohování**.

    ![zastavení úlohy zálohování](./media/backup-azure-delete-vault/stop-the-backup-process.png)

    Otevře se nabídka zastavení zálohování.

    b. Na **zastavení zálohování** nabídce z **zvolte možnost** nabídce vyberte možnost **odstranit záložní Data** > zadejte název položky > a klikněte na tlačítko **zastavení zálohování**.

    Zadejte název položky, chcete-li ověřit, že chcete odstranit. **Zastavení zálohování** tlačítko aktivuje po ověření položky. Pokud nevidíte dialogovém okně zadejte název položky zálohování, jste zvolili **zachovat zálohovaná Data** možnost.

    ![Odstranit záložní data](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

    Volitelně můžete zadat důvod, proč se odstranit data a přidání komentářů. Po kliknutí na tlačítko **zastavení zálohování**, povolit odstranit úlohu provést před pokusem o odstranění trezoru. Pokud chcete ověřit, zda byla úloha dokončena, zkontrolujte zprávy Azure ![odstranit záložní data](./media/backup-azure-delete-vault/messages.png). <br/>
    Po dokončení úlohy služby odešle zprávu: byla zastavena procesu zálohování a zálohování dat byla odstraněna.

    c. Po odstranění položky v seznamu na **zálohování položek** nabídky, klikněte na tlačítko **aktualizovat** zobrazit zbývající položky v trezoru.

      ![Odstranit záložní data](./media/backup-azure-delete-vault/empty-items-list.png)

      Pokud v seznamu nejsou žádné položky, přejděte na **Essentials** podokně v nabídce trezoru služeb zotavení. Nesmí být žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky** uvedené. Pokud stále se zobrazí položky v trezoru, vraťte se ke kroku tři a zvolte jiný položky typu seznamu.  
5. Když na panelu nástrojů trezoru nejsou žádné další položky, klikněte na tlačítko **odstranit**.

    ![Odstranit záložní data](./media/backup-azure-delete-vault/delete-vault.png)
6. Chcete-li ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**.

    Odstranění trezoru a vrátí portálu **nový** nabídky služby.

## <a name="what-if-i-stopped-the-backup-process-but-retained-the-data"></a>Co když jsem zastavit proces zálohování ale uchovávají data?
Pokud je ale omylem zastavit proces zálohování *uchovávají* data, je nutné odstranit zálohovaná data před odstraněním trezoru. Odstranit záložní data:

1. Na **položky zálohování** nabídky, klikněte pravým tlačítkem položku a v místní nabídce klikněte na tlačítko **odstranit záložní data**.

    ![Odstranit záložní data](./media/backup-azure-delete-vault/delete-backup-data-menu.png)

    **Odstranit Data zálohování** otevře se nabídka.
2. Na **odstranit Data zálohování** nabídky, zadejte název položky a klikněte na tlačítko **odstranit**.

    ![Odstranit záložní data](./media/backup-azure-delete-vault/delete-retained-vault.png)

    Jakmile jste odstranili data, vraťte se ke kroku 4c a pokračujte v procesu.

## <a name="delete-a-vault-used-to-protect-a-dpm-server"></a>Odstranění úložiště používá k ochraně serveru aplikace DPM
Před odstraněním trezoru použít také k ochraně serveru aplikace DPM, musíte vymazat všechny body obnovení, které byly vytvořeny a potom zrušit registraci serveru z trezoru.

Chcete-li odstranit data související s skupinu ochrany:

1. V konzole správce aplikace DPM klikněte na **ochrany** > vyberte skupinu ochrany > vyberte člena skupiny ochrany > a na pásu karet nástroje klikněte na tlačítko **odebrat**.

  Vyberte člena skupiny ochrany k aktivaci **odebrat** tlačítko pásu karet nástroje. V příkladu je člen **dummyvm9**. Chcete-li vybrat několik členů skupiny ochrany, podržte stisknutou klávesu Ctrl při kliknutí na členy.

    ![Odstranit záložní data](./media/backup-azure-delete-vault/az-portal-delete-protection-group.png)

    **Zastavit ochranu** otevře se dialogové okno.
2. V **zastavit ochranu** dialogovém okně, vyberte **odstranit chráněná data**a klikněte na tlačítko **zastavit ochranu**.

    ![Odstranit záložní data](./media/backup-azure-delete-vault/delete-dpm-protection-group.png)

    Pokud chcete odstranit trezor, musíte vymazat nebo trezor chráněných dat odstranit. Pokud máte mnoho body obnovení a data ve skupině ochrany, se může trvat několik minut odstranit data. **Zastavit ochranu** dialogové okno zobrazí po dokončení úlohy.

    ![Odstranit záložní data](./media/backup-azure-delete-vault/success-deleting-protection-group.png)
3. Dál tento proces pro všechny členy všech skupin ochrany.

    Odeberte všechny chráněné skupiny dat a ochrany.
4. Po odstranění všechny členy ze skupiny ochrany, přejděte na portál Azure. Otevřete řídícím panelu trezoru a ověřte, zda žádná **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky**. Na panelu nástrojů trezoru, klikněte na tlačítko **odstranit**.

    ![Odstranit záložní data](./media/backup-azure-delete-vault/delete-vault.png)

    Pokud jsou servery pro správu zálohování registrovaný k úložišti, nelze odstranit trezor i v případě, že neexistují žádná data v trezoru. Pokud jste odstranili servery pro správu zálohování přidruženého k úložišti, ale nejsou uvedeny v servery **Essentials** podokně, najdete v části [najít servery pro správu zálohování registrovaný k úložišti](backup-azure-delete-vault.md#find-the-backup-management-servers-registered-to-the-vault).
5. Chcete-li ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**.

    Odstranění trezoru a vrátí portálu **nový** nabídky služby.

## <a name="delete-a-vault-used-to-protect-a-production-server"></a>Odstranění úložiště používá k ochraně provozním serveru
Před odstraněním trezoru použít také k ochraně provozním serveru, musíte odstranit, nebo zrušte registraci serveru z trezoru.

Chcete-li odstranit přidruženého k úložišti provozním serveru:

1. Na portálu Azure otevřete řídícím panelu trezoru a klikněte na tlačítko **nastavení** > **infrastruktura zálohování** > **produkční servery**.

    ![Otevřete nabídku provozních serverech](./media/backup-azure-delete-vault/delete-production-server.png)

    **Produkční servery** nabídky k otevření a všech provozních serverů v trezoru.

    ![Seznam provozních serverech](./media/backup-azure-delete-vault/list-of-production-servers.png)
2. Na **provozních serverů** nabídky, klikněte pravým tlačítkem na server a klikněte na tlačítko **odstranit**.

    ![Odstranit provozním serveru ](./media/backup-azure-delete-vault/delete-server-on-production-server-blade.png)

    **Odstranit** otevře se nabídka.

    ![Odstranit provozním serveru ](./media/backup-azure-delete-vault/delete-blade.png)
3. Na **odstranit** nabídce potvrďte název serveru a klikněte na tlačítko **odstranit**. Je třeba správně zadat název serveru, a aktivovat **odstranit** tlačítko.

    Po odstranění trezoru, obdržíte zprávu s oznámením, že trezor byl odstraněn. Po odstranění všech serverech v trezoru, přejděte zpět do podokna Essentials na řídicím panelu trezoru.
4. Na řídicím panelu trezoru, zkontrolujte, že žádné **zálohování položek**, **zálohování serverů pro správu**, nebo **replikované položky**. Na panelu nástrojů trezoru, klikněte na tlačítko **odstranit**.
5. Chcete-li ověřit, že chcete trezor odstranit, klikněte na tlačítko **Ano**.

    Odstranění trezoru a vrátí portálu **nový** nabídky služby.

## <a name="find-the-backup-management-servers-registered-to-the-vault"></a>Najít servery pro správu zálohování registrovaný k úložišti
Pokud máte více serverů registrovaný k trezoru, může být obtížné mějte na paměti, je. Zobrazit servery registrovaný k úložišti a odstranit:

1. Otevřete řídícím panelu trezoru.
2. V **Essentials** podokně klikněte na tlačítko **nastavení** otevření této nabídky.

    ![Otevřete nabídku nastavení](./media/backup-azure-delete-vault/backup-vault-click-settings.png)
3. Na **nastavení** nabídky, klikněte na tlačítko **infrastruktura zálohování**.
4. Na **infrastruktura zálohování** nabídky, klikněte na tlačítko **servery pro správu zálohování**. Otevře se nabídka servery pro správu zálohování.

    ![seznam serverů pro správu zálohování](./media/backup-azure-delete-vault/list-of-backup-management-servers.png)
5. Odstranění serveru ze seznamu, klikněte pravým tlačítkem na název serveru a pak klikněte na **odstranit**.
    **Odstranit** otevře se nabídka.
6. Na **odstranit** nabídky, zadejte název serveru. Pokud je dlouhý název, můžete zkopírovat a vložit ze seznamu serverů pro správu zálohování. Potom klikněte na **Odstranit**.  
