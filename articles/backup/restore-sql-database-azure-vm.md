---
title: Obnovit zálohování databází systému SQL Server na Virtuálním počítači Azure pomocí služby Azure Backup | Dokumentace Microsoftu
description: Tento článek popisuje, jak obnovit databáze systému SQL Server běžící na Virtuálním počítači Azure, které se zálohují v Azure Backup
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 3571eb2471f9b3f06eb509937fd11866b4e0caa8
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430805"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Obnovení databází serveru SQL Server na virtuálních počítačích Azure 


Tento článek popisuje, jak obnovit databázi systému SQL Server běžící na Virtuálním počítači Azure, která se zálohovala do trezoru služby Azure Backup Recovery Services se [Azure Backup](backup-overview.md) služby.


> [!NOTE]
> Zálohování databází systému SQl Server běžící na Virtuálním počítači Azure pomocí služby Azure Backup je momentálně ve verzi public preview.


Tento článek popisuje, jak obnovit databáze systému SQL Server. Pokud jste nenakonfigurovali zálohování databází, postupujte podle pokynů v [v tomto článku](backup-azure-sql-database.md)



## <a name="about-restoring-databases"></a>O obnovení databází

Azure Backup můžete obnovit databáze systému SQL Server běžící na virtuálních počítačích Azure následujícím způsobem:

- Obnovení na konkrétní datum nebo čas (sekundy), pomocí zálohování transakčního protokolu. Azure Backup automaticky určuje odpovídající úplné rozdílové zálohy a řetězec zálohy protokolu, které jsou nutné k obnovení na základě vybraného času.
- Obnovit konkrétního úplnou nebo rozdílovou zálohu k obnovení pro konkrétní bod obnovení, spíše než určený čas.


### <a name="prerequisites"></a>Požadavky

Před obnovením databáze vezměte na vědomí následující:

- Databázi můžete obnovit do instance systému SQL Server ve stejné oblasti Azure.
- Cílový server musí být zaregistrovaný do stejného trezoru jako zdroj.
- Obnovení databáze šifrovaná transparentní šifrování dat na jiný Server SQL, musíte si nejdřív [obnovení certifikátu na cílový server](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Předtím, než aktivujete obnovení databázi "master", spusťte instanci systému SQL Server v režimu jednoho uživatele s možností spuštění **-m AzureWorkloadBackup**.
    - Hodnota pro **-m** je název klienta.
    - K otevření připojení je povoleno pouze název zadaného klienta.
- Pro všechny systémové databáze (model, hlavní, msdb) zastavte službu Agent serveru SQL před aktivaci obnovení.
- Zavřete všechny aplikace, které může pokusit o krádež připojení ke kterékoli z těchto databází.

## <a name="restore-a-database"></a>Obnovení databáze

Chcete-li obnovit musíte mít následující oprávnění:

* **Operátor zálohování** oprávnění v trezoru, který provádíte obnovení.
* **Přispěvatel (zápis)** přístup k tomuto zdroji virtuální počítač, který se zálohuje.
* **Přispěvatel (zápis)** přístup k cílovému virtuálnímu počítači:
    - Pokud se obnovení do stejného virtuálního počítače bude zdrojový virtuální počítač.
    - Pokud se obnovení do alternativního umístění bude nový cílový virtuální počítač. 

Obnovte takto:
1. Otevřete trezor, ve které je zaregistrovaný virtuální počítač s SQL serverem.
2. Na řídicím panelu trezoru v části **využití**vyberte **zálohování položek**.

    ![Otevřete nabídku zálohování položek](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. V **zálohování položek**v části **typu správy zálohování**vyberte **SQL na virtuálním počítači Azure**.

    ![Vyberte SQL na virtuálním počítači Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Vyberte databáze, kterou chcete obnovit.

    ![Vyberte databáze, kterou chcete obnovit](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Projděte si v nabídce databáze. Poskytuje informace o zálohování databáze, včetně: 

    * Nejstarší a nejnovější body obnovení.
    * Zálohování stavu protokolu za posledních 24 hodin pro databáze v režimu úplný a hromadně protokolovaného obnovení, pokud je nakonfigurovaná pro zálohování transakčních protokolů.

6. Klikněte na tlačítko **Restore DB**. 

    ![Vyberte obnovit databázi](./media/backup-azure-sql-database/restore-db-button.png)

7. V **obnovit konfiguraci**, zadejte, kam chcete obnovit data, která mají:
    - **Alternativní umístění**: Obnovení databáze do alternativního umístění a zachovat původní zdrojové databáze.
    - **Přepsat databázi**: Obnovení dat na stejnou instanci systému SQL Server jako původní zdroj. Účinek této možnosti je přepisovat původní databázi.

    > [!Important]
    > Pokud vybraná databáze patří do skupiny vždy na dostupnost, SQL Server nepovoluje databáze, kterou chcete přepsat. Pouze **alternativního umístění** je k dispozici.
    >

    ![Obnovení nabídky konfigurace](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Obnovení do alternativního umístění

1. V **obnovit konfiguraci** nabídky v části **kam obnovit**vyberte **alternativního umístění**.
2. Vyberte název serveru SQL Server a instance, do kterých chcete databázi obnovit.
3. V **název databáze obnovit** zadejte název cílové databáze.
4. Pokud je k dispozici, vyberte **přepsat, pokud ve vybrané instanci SQL existuje databáze se stejným názvem**.
5. Klikněte na **OK**.

    ![Zadejte hodnoty pro konfiguraci obnovení nabídky](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. V **vyberte bod obnovení**vyberte, jestli se má [obnovení k určitému bodu v čase](#restore-to-a-specific-point-in-time), nebo chcete-li obnovit [konkrétní bod obnovení](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Obnovení bodu v čase je k dispozici pouze pro zálohování protokolů pro databáze s režimem úplný a hromadně protokolovaného obnovení. 


### <a name="restore-and-overwrite"></a>Obnovení a přepsání

1. V **obnovit konfiguraci** nabídky v části **kam obnovit**vyberte **přepsat DB** > **OK**.

    ![Vyberte možnost přepsat databázi](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. V **vyberte bod obnovení**vyberte ** protokoly (časový okamžik) [obnovení k určitému bodu v čase](#restore-to-a-specific-point-in-time), nebo **úplné a rozdílové** obnovíte [konkrétní bod obnovení](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > Obnovení bodu v čase je k dispozici pouze pro zálohování protokolů pro databáze s modelem úplný a hromadně protokolovaného obnovení. 




    
### <a name="restore-to-a-specific-point-in-time"></a>Obnovení k určitému bodu v čase

Pokud jste vybrali **protokoly (časový okamžik)** jako typ obnovení, postupujte takto:

1.  V části **obnovit data a času**, vyberte mini kalendář. Na **kalendáře**tučně označená data mají body obnovení a je zvýrazněn aktuální datum.
2. Vyberte datum se body obnovení. Data bez body obnovení nelze vybrat.

    ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. Vyberte datum a časové osy grafu zobrazuje dostupné body obnovení v průběžné rozsahu.
4. Zadejte čas pro obnovení pomocí časové osy grafu nebo vyberte čas. Pak klikněte na **OK**.

    ![Otevřít kalendář](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. Na **Upřesnit konfiguraci** nabídky, pokud chcete ponechat databázi funkční po obnovení, povolte **obnovit s NORECOVERY**.
5. Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadejte novou cílovou cestu.
6. Klikněte na **OK**.

    ![Nabídka pokročilou konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. Na **obnovení** nabídce vyberte možnost **obnovení** spustit úlohu obnovení.
8. Sledovat průběh na obnovení **oznámení** oblasti, nebo vyberte **obnovení úlohy** v nabídce databáze.

    ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>Obnovení do bodu obnovení konkrétní

Pokud jste vybrali **úplné a rozdílové** jako typ obnovení, postupujte takto:


1. Vyberte bod obnovení ze seznamu a klikněte na tlačítko **OK** postup bodu obnovení.

    ![Vyberte úplný bod obnovení](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. Na **Upřesnit konfiguraci** nabídky, pokud chcete ponechat databázi funkční po obnovení, povolte **obnovit s NORECOVERY**.
3. Pokud chcete změnit umístění pro obnovení na cílovém serveru, zadejte novou cílovou cestu. 
4. Klikněte na **OK**.

    ![Nabídka Upřesnit konfiguraci](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. Na **obnovení** nabídce vyberte možnost **obnovení** spustit úlohu obnovení.
8. Sledovat průběh na obnovení **oznámení** oblasti, nebo vyberte **obnovení úlohy** v nabídce databáze.

    ![Průběh úlohy obnovení](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>Další postup

[Spravovat a monitorovat](manage-monitor-sql-database-backup.md) zálohovat databáze SQL serveru pomocí Azure Backup.
