---
title: Obnovení dat z Azure Backup Server
description: Obnovte data, která jste chránili do trezoru Recovery Services, z jakéhokoli Azure Backup Server zaregistrovaného v tomto trezoru.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 0cce45fbaaedad4cfcee0cda768aae2fa294312b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002966"
---
# <a name="recover-data-from-azure-backup-server"></a>Obnovení dat z Azure Backup Serveru

Pomocí Azure Backup Server můžete obnovit data, která jste zálohovali do trezoru Recovery Services. Tento postup je integrovaný do konzoly pro správu Azure Backup Server a je podobný jako pracovní postup obnovení pro jiné součásti Azure Backup.

> [!NOTE]
> Tento článek se týká nástroje [System Center Data Protection Manager 2012 R2 s UR7 nebo novějším](https://support.microsoft.com/kb/3065246)v kombinaci s [nejnovějším agentem Azure Backup](https://aka.ms/azurebackup_agent).
>
>

Postup obnovení dat z Azure Backup Server:

1. Na kartě **obnovení** v konzole pro správu Azure Backup server vyberte **Přidat externí DPM** (v levém horním rohu obrazovky).

    ![Přidat externí DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Stáhněte si nové **přihlašovací údaje trezoru** z trezoru přidruženého k **Azure Backup Server** , kde se mají data obnovovat, vyberte Azure Backup server ze seznamu Azure Backup serverů zaregistrovaných v úložišti Recovery Services a poskytněte **Šifrovací heslo** přidružené k serveru, jehož data se obnovují.

    ![Externí přihlašovací údaje DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Jenom Azure Backup servery přidružené ke stejnému trezoru registrací můžou obnovovat jednotlivá data.
   >
   >

    Po úspěšném přidání externích Azure Backup Server můžete procházet data externího serveru a místní Azure Backup Server na kartě **obnovení** .
3. Projděte si seznam dostupných produkčních serverů chráněných externím Azure Backup Server a vyberte příslušný zdroj dat.

    ![Procházet externí server DPM](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. V rozevíracím seznamu **bodů obnovení** vyberte **měsíc a rok** , vyberte požadované **Datum obnovení** při vytvoření bodu obnovení a vyberte **čas obnovení**.

    V dolním podokně se zobrazí seznam souborů a složek, které se dají Procházet a obnovovat do libovolného umístění.

    ![Body obnovení externího serveru DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Klikněte pravým tlačítkem na příslušnou položku a vyberte **obnovit**.

    ![Externí obnovení DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Zkontrolujte **Výběr obnovení**. Ověřte data a čas obnovení záložní kopie a také zdroj, ze kterého byla vytvořena záložní kopie. Pokud je výběr nesprávný, kliknutím na **tlačítko Storno** přejděte zpět na kartu obnovení a vyberte příslušný bod obnovení. Pokud je výběr správný, vyberte **Další**.

    ![Souhrn obnovení externí aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Vyberte **obnovit do alternativního umístění**. **Přejděte** do správného umístění pro obnovení.

    ![Alternativní umístění pro obnovení externí aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Vyberte možnost související s **vytvořením kopie**, **přeskočení** nebo **přepsání**.

   * **Vytvořit kopii** – vytvoří kopii souboru, pokud dojde ke kolizi názvů.
   * **Přeskočit** – Pokud dojde ke kolizi názvů, neobnovuje soubor, což ponechá původní soubor.
   * **Přepsat** – Pokud dojde ke kolizi názvů, přepíše existující kopii souboru.

     Vyberte odpovídající možnost pro **obnovení zabezpečení**. Můžete použít nastavení zabezpečení cílového počítače, ve kterém se data obnovují, nebo nastavení zabezpečení, která byla platná pro produkt v době vytvoření bodu obnovení.

     Určete, zda bude odesláno **oznámení** po úspěšném dokončení obnovení.

     ![Oznámení o externím obnovení DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Na obrazovce **souhrnu** jsou uvedené možnosti, které zatím byly vybrány. Po výběru **obnovení** se data obnoví do příslušného místního umístění.

    ![Souhrn možností externích obnovení DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Úlohu obnovení lze monitorovat na kartě **monitorování** Azure Backup Server.
   >
   >

    ![Monitorování obnovení](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Můžete vybrat možnost **Vymazat externí DPM** na kartě **obnovení** serveru DPM a odebrat tak zobrazení externího serveru DPM.

    ![Vymazat externí DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Řešení potíží s chybovými zprávami

| Ne. | Chybová zpráva | Postup při řešení potíží |
|:---:|:--- |:--- |
| 1. |Tento server není zaregistrován do trezoru určeného přihlašovacími údaji trezoru. |**Příčina:** Tato chyba se zobrazí, když vybraný soubor s přihlašovacími údaji trezoru nepatří do trezoru Recovery Services přidruženého k Azure Backup Server, na kterých se provádí obnovení. <br> **Řešení:** Stáhněte si soubor s přihlašovacími údaji trezoru z Recovery Services trezoru, do kterého je Azure Backup Server zaregistrovaný. |
| 2. |Buď obnovitelná data nejsou k dispozici, nebo zvolený server není server DPM. |**Příčina:** Do trezoru Recovery Services nejsou zaregistrovány žádné další Azure Backup servery, nebo servery ještě neodeslaly metadata, nebo vybraný server není Azure Backup Server (pomocí Windows serveru nebo klienta Windows). <br> **Řešení:** Pokud jsou v trezoru Recovery Services zaregistrované další Azure Backup servery, ujistěte se, že je nainstalovaný nejnovější agent Azure Backup. <br>Pokud jsou v trezoru Recovery Services zaregistrovány jiné Azure Backup servery, počkejte po instalaci za účelem spuštění procesu obnovení dne. Noční úloha odešle metadata pro všechna chráněná zálohování do cloudu. Data budou k dispozici pro obnovení. |
| 3. |K tomuto úložišti není registrovaný žádný další server DPM. |**Příčina:** Nejsou k dispozici žádné další Azure Backup servery, které jsou registrovány do trezoru, ze kterého Probíhá obnovení.<br>**Řešení:** Pokud jsou v trezoru Recovery Services zaregistrované další Azure Backup servery, ujistěte se, že je nainstalovaný nejnovější agent Azure Backup.<br>Pokud jsou v trezoru Recovery Services zaregistrovány jiné Azure Backup servery, počkejte po instalaci za účelem spuštění procesu obnovení dne. Noční úloha nahrává metadata pro všechny chráněné zálohy do cloudu. Data budou k dispozici pro obnovení. |
| 4. |Zadané šifrovací heslo se neshoduje s heslem přidruženým k následujícímu serveru: **\<server name>** |**Příčina:** Šifrovací heslo použité v procesu šifrování dat z obnovených dat Azure Backup Server neodpovídá zadanému šifrovacímu heslu. Agent nemůže data dešifrovat, takže obnovení nebude úspěšné.<br>**Řešení:** Poskytněte přesně stejné šifrovací heslo, které je přidružené k Azure Backup Server, jejichž data se obnovují. |

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

* [Běžné dotazy](backup-azure-vm-backup-faq.md) k zálohování virtuálních počítačů Azure
* [Běžné dotazy](backup-azure-file-folder-backup-faq.md) týkající se agenta Azure Backup
