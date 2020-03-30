---
title: Obnovení dat ze zálohovacího serveru Azure
description: Obnovte data, která jste chránili do trezoru služby Recovery Services, z libovolného serveru Zálohování Azure registrovaného v tomto trezoru.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 2a89697899fc244848854978de4b25e79ef6f184
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173499"
---
# <a name="recover-data-from-azure-backup-server"></a>Obnovení dat z Azure Backup Serveru

Pomocí serveru Azure Backup Server můžete obnovit data, která jste zálohovali do trezoru služby Recovery Services. Proces pro tento postup je integrován do konzoly pro správu serveru Zálohování Azure a je podobný pracovnímu postupu obnovení pro ostatní součásti Azure Backup.

> [!NOTE]
> Tento článek je použitelný pro [Správce ochrany dat System Center 2012 R2 s UR7 nebo novějším](https://support.microsoft.com/kb/3065246)v kombinaci s [nejnovějším agentem Azure Backup](https://aka.ms/azurebackup_agent).
>
>

Obnovení dat ze zálohovacího serveru Azure:

1. Na kartě **Obnovení** konzoly pro správu serveru Zálohování Azure klikněte na **tlačítko Přidat externí dpm** (v levém horním rohu obrazovky).

    ![Přidat externí dpm](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Stáhněte si nová **pověření trezoru** z trezoru přidruženého k **Azure Backup Server,** kde se data obnovují, zvolte Server zálohování Azure ze seznamu serverů zálohování Azure registrovaných v trezoru služby Recovery Services a zadejte **šifrovací přístupové heslo** přidružené k serveru, jehož data se obnovují.

    ![Externí pověření aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Data si můžou vzájemně obnovovat jenom servery Azure Backup, které jsou přidruženy ke stejnému úložišti registrace.
   >
   >

    Po úspěšném přidání externího serveru Zálohování Azure můžete procházet data externího serveru a místního serveru Zálohování Azure na kartě **Obnovení.**
3. Prohlédněte si dostupný seznam produkčních serverů chráněných externím serverem Azure Backup Server a vyberte příslušný zdroj dat.

    ![Procházení externího serveru DPM](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Vyberte **měsíc a rok** z rozevíracího souboru Body **obnovení,** vyberte požadované **datum obnovení,** pro kdy byl bod obnovení vytvořen, a vyberte **čas obnovení**.

    V dolním podokně se zobrazí seznam souborů a složek, který lze procházet a obnovovat do libovolného umístění.

    ![Externí body obnovení serveru DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Klepněte pravým tlačítkem myši na příslušnou položku a klepněte na příkaz **Obnovit**.

    ![Externí obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Zkontrolujte **výběr obnovení**. Ověřte data a čas obnovení záložní kopie a také zdroj, ze kterého byla vytvořena záložní kopie. Pokud je výběr nesprávný, klepnutím na **tlačítko Storno** přejděte zpět na kartu obnovení a vyberte příslušný bod obnovení. Pokud je výběr správný, klepněte na tlačítko **Další**.

    ![Externí souhrn obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Vyberte **Obnovit do alternativního umístění**. **Vyhledejte** správné umístění pro obnovení.

    ![Externí alternativní umístění obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Zvolte možnost související s **vytvořením kopie**, **Přeskočit**nebo **Přepsat**.

   * **Vytvořit kopii** - vytvoří kopii souboru, pokud dojde ke kolizi názvů.
   * **Přeskočit** - pokud dojde ke kolizi názvů, neobnoví soubor, který opustí původní soubor.
   * **Přepsat** - pokud dojde ke kolizi názvů, přepíše existující kopii souboru.

     Zvolte příslušnou možnost **obnovení zabezpečení**. Můžete použít nastavení zabezpečení cílového počítače, ve kterém jsou data obnovována, nebo nastavení zabezpečení, která byla použitelná pro produkt v době vytvoření bodu obnovení.

     Určete, zda **je** oznámení odesláno po úspěšném dokončení obnovení.

     ![Externí oznámení o obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. Na obrazovce **Souhrn** jsou uvedeny dosud vybrané možnosti. Po klepnutí na tlačítko **Obnovit**se data obnoví do příslušného místního umístění.

    ![Souhrn externích možností obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Úlohu obnovení lze sledovat na kartě **Monitorování** na serveru Azure Backup Server.
   >
   >

    ![Sledování obnovení](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Klepnutím na tlačítko **Vymazat externí dpm** na kartě **Obnovení** serveru DPM můžete odebrat zobrazení externího serveru DPM.

    ![Vymazat externí dpm](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Poradce při potížích s chybovými zprávami

| Ne. | Chybová zpráva | Postup při řešení potíží |
|:---:|:--- |:--- |
| 1. |Tento server není zaregistrován do úložiště určeného pověřením úschovny. |**Příčina:** Tato chyba se zobrazí, když vybraný soubor pověření úschovny nepatří do trezoru služby Recovery Services přidruženého k serveru Zálohování Azure, na kterém se pokoušíte o obnovení. <br> **Rozlišení:** Stáhněte si soubor přihlašovacích údajů trezoru z trezoru služby Recovery Services, do kterého je zaregistrován server Zálohování Azure. |
| 2. |Obnovitelná data nejsou k dispozici nebo vybraný server není serverem DPM. |**Příčina:** V trezoru služby Recovery Services nejsou registrovány žádné jiné servery Zálohování Azure nebo servery ještě nenahrály metadata nebo vybraný server není serverem Azure Backup (pomocí Windows Serveru nebo Klienta Windows). <br> **Rozlišení:** Pokud existují další servery zálohování Azure registrované do trezoru služby Recovery Services, ujistěte se, že je nainstalován nejnovější agent azure backup. <br>Pokud existují další servery zálohování Azure registrované do trezoru služby Recovery Services, počkejte den po instalaci a spusťte proces obnovení. Noční úloha nahraje metadata pro všechny chráněné zálohy do cloudu. Data budou k dispozici pro obnovení. |
| 3. |Do tohoto trezoru není registrován žádný jiný server DPM. |**Příčina:** Neexistují žádné další servery zálohování Azure, které jsou registrovány do úložiště, ze kterého se pokouší tenicovat obnovení.<br>**Rozlišení:** Pokud existují další servery zálohování Azure registrované do trezoru služby Recovery Services, ujistěte se, že je nainstalován nejnovější agent azure backup.<br>Pokud existují další servery zálohování Azure registrované do trezoru služby Recovery Services, počkejte den po instalaci a spusťte proces obnovení. Noční úloha nahraje metadata pro všechny chráněné zálohy do cloudu. Data budou k dispozici pro obnovení. |
| 4. |Zakalené šifrovací heslo se neshoduje s přístupovou frází přidruženou k následujícímu serveru: ** \<název serveru>** |**Příčina:** Šifrovací přístupové heslo používané při šifrování dat z dat serveru Zálohování Azure, která se obnovují, neodpovídá poskytnuté šifrovací heštlově. Agent nemůže dešifrovat data. Proto zotavení selže.<br>**Rozlišení:** Zadejte přesně stejné šifrovací přístupové heslo přidružené k serveru Azure Backup Server, jehož data se obnovují. |

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

* [Časté otázky týkající](backup-azure-vm-backup-faq.md) se zálohování virtuálních počítačů Azure
* [Běžné dotazy](backup-azure-file-folder-backup-faq.md) týkající se agenta Azure Backup
