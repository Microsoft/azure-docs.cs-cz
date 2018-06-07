---
title: Obnovení dat ze serveru Azure Backup
description: Obnovte data, která jste chránili do trezoru služeb zotavení z jakéhokoli serveru zálohování Azure zaregistrovat do tohoto trezoru.
services: backup
author: nkolli1
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: adigan
ms.openlocfilehash: 8559532f873e8073e736f881374fec1c080d08c3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604399"
---
# <a name="recover-data-from-azure-backup-server"></a>Obnovení dat z Azure Backup Serveru
Azure Backup Server můžete použít k obnovení dat, které jste zálohovali do trezoru služeb zotavení. Proces pro to, je integrována do konzoly pro správu serveru Azure Backup a je podobný postupu obnovení pro jiné komponenty Azure Backup.

> [!NOTE]
> Tento článek se použije pro [System Center Data Protection Manager 2012 R2 s kumulativní aktualizací 7 nebo novější] (https://support.microsoft.com/en-us/kb/3065246), kombinované s [nejnovější verze agenta Azure Backup](http://aka.ms/azurebackup_agent).
>
>

Chcete-li obnovit data ze serveru Azure Backup:

1. Z **obnovení** kartu konzoly pro správu serveru Azure Backup, klikněte na tlačítko **přidat externí DPM** (v levém horním rohu obrazovky).   
    ![Přidat externí DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Stažení nové **přihlašovací údaje trezoru** z trezoru přidružené **serveru Azure Backup** tam, kde se obnovuje data, zvolte serveru Azure Backup Server ze seznamu serverů pro zálohování Azure zaregistrována trezor služeb zotavení a zadejte **šifrovací přístupové heslo** přidružené k serveru, jehož data obnovena.

    ![Přihlašovací údaje externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Pouze servery zálohování Azure přidružený ke stejnému trezoru registrace můžete obnovit data druhé strany.
   >
   >

    Po úspěšném přidání externího serveru Azure Backup můžete procházet data z externího serveru a místní Server Azure Backup z **obnovení** kartě.
3. Procházet seznam dostupných provozních serverů, které jsou chráněny externího serveru zálohování Azure a vyberte odpovídající zdroj dat.

    ![Procházet externí DPM Server](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Vyberte **měsíci a roce** z **body obnovení** rozevírací nabídku, vyberte požadované **obnovení datum** pro vytvoření bodu obnovení a vyberte možnost **čas obnovení**.

    V dolním podokně, které můžete procházet a obnovit do libovolného umístění, zobrazí se seznam souborů a složek.

    ![Body obnovení serveru externí aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Klikněte pravým tlačítkem na příslušnou položku a klikněte na tlačítko **obnovit**.

    ![Externí obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Zkontrolujte **Obnovit výběr**. Zkontrolujte data a času záložní kopie obnovuje, jakož i zdroj, ze kterého byl vytvořen záložní kopii. Pokud není v pořádku výběr, klikněte na tlačítko **zrušit** přejděte zpět na kartu obnovení vyberte bod obnovení odpovídající. Pokud je výběr správné, klikněte na tlačítko **Další**.

    ![Externí souhrnu obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Vyberte **obnovit do alternativního umístění**. **Procházet** do správného umístění pro obnovení.

    ![Externí DPM alternativní umístění pro obnovení](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Zvolte možnost související s **vytvořit kopii**, **přeskočit**, nebo **přepsat**.

   * **Vytvořit kopii** -vytvoří kopii souboru, pokud je kolize názvů.
   * **Přeskočit** – Pokud je kolize názvů, nedojde k odstranění souboru, což ponechá původní soubor.
   * **Přepsat** – Pokud je kolize názvů, přepíše existující kopii souboru.

     Vyberte odpovídající možnost **obnovit zabezpečení**. Můžete použít nastavení zabezpečení cílového počítače, kde se obnovuje data nebo nastavení zabezpečení, které byly pro produkt v době vytvoření bodu obnovení.

     Identifikovat zda **oznámení** se odesílají, jednou úspěšně dokončí obnovení.

     ![Externí DPM obnovení oznámení](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. **Souhrn** obrazovky je uveden seznam možností, pokud vybraná. Po kliknutí na tlačítko **'Obnovit'**, data budou obnovena do odpovídající místní umístění.

    ![Souhrn možnosti obnovení externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Úlohu obnovení můžete sledovat v **monitorování** kartě serveru Azure Backup.
   >
   >

    ![Monitorování obnovení](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Můžete kliknout na **vymazat externí DPM** na **obnovení** kartě serveru aplikace DPM odebrat zobrazení externího serveru aplikace DPM.

    ![Vymazat externí DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Řešení potíží s chybové zprávy
| Ne. | Chybová zpráva | Postup při řešení potíží |
|:---:|:--- |:--- |
| 1. |Tento server není registrovaný k úložišti určenému přihlašovacími údaji úložiště. |**Příčina:** tato chyba se zobrazí, když soubor s přihlašovacími údaji trezoru vybrané nepatří do trezoru služeb zotavení přidružené k serveru Azure Backup Server, na kterém dojde k pokusu o obnovení. <br> **Řešení:** stažení souboru přihlašovacích údajů trezoru služeb zotavení trezoru serveru Azure Backup je zaregistrovaný. |
| 2. |Buď obnovitelná data nejsou k dispozici nebo vybraný server není DPM server. |**Příčina:** nejsou zaregistrované žádné jiné servery Azure zálohování do trezoru služeb zotavení, nebo servery ještě jste zatím neodeslali metadata nebo vybraný server není Server Azure Backup (neboli systému Windows Server nebo klienta Windows). <br> **Řešení:** Pokud existují, ostatní servery zálohování Azure zaregistrovat do trezoru služeb zotavení, zkontrolujte, zda je nainstalován nejnovější agent Azure Backup. <br>Pokud existují další servery zálohování Azure zaregistrován do trezoru služeb zotavení, počkejte denně po instalaci zahájíte proces obnovení. Na noční úlohu odešlete metadata pro všechny chráněné zálohování do cloudu. Data budou k dispozici pro obnovení. |
| 3. |Žádný jiný server DPM je zaregistrován k tomuto úložišti. |**Příčina:** nejsou žádné další Azure Backup servery, které jsou registrovány k trezoru, ze kterého je probíhají pokusy o obnovení.<br>**Řešení:** Pokud existují, ostatní servery zálohování Azure zaregistrovat do trezoru služeb zotavení, zkontrolujte, zda je nainstalován nejnovější agent Azure Backup.<br>Pokud existují další servery zálohování Azure zaregistrován do trezoru služeb zotavení, počkejte denně po instalaci zahájíte proces obnovení. Na noční úlohu ukládání metadata pro všechny chráněné zálohování do cloudu. Data budou k dispozici pro obnovení. |
| 4. |Zadaná šifrovací přístupové heslo neodpovídá přístupovému heslu přidruženému k následujícímu serveru: **<server name>** |**Příčina:** používá při šifrování dat ze serveru Azure Backup dat, který obnovuje šifrovací přístupové heslo neodpovídá zadané šifrovací přístupové heslo. Agent se nepodařilo dešifrovat data. Proto se obnovení nezdaří.<br>**Řešení:** zadejte přesně stejný šifrovací přístupové heslo přidružené k serveru Azure Backup, jejichž data obnovena. |

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Proč nelze přidat externí server aplikace DPM po instalaci kumulativní aktualizací 7 a nejnovější verze agenta Azure Backup?

Pro servery aplikace DPM se zdroji dat, které jsou chráněné do cloudu (s použitím kumulativní aktualizace starší než kumulativní aktualizace 7), je nutné počkat alespoň jeden den po instalaci kumulativní aktualizací 7 a nejnovější agenta Azure Backup, spusťte **přidat externí DPM serveru**. K nahrání metadata skupiny ochrany DPM do Azure je nutné za jeden den časové období. Metadata skupiny ochrany nahraje poprvé prostřednictvím noční úlohu.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>Co je minimální verze agenta služeb zotavení Microsoft Azure potřeba?

Minimální verze agenta služeb zotavení Microsoft Azure nebo agenta Azure Backup potřebné k povolení této funkce je 2.0.8719.0.  Chcete-li zobrazit verze agenta: Otevřete ovládací panely **>** všechny položky **>** programy a funkce **>** agenta služeb zotavení Microsoft Azure. Pokud verze je menší než 2.0.8719.0, stáhněte a nainstalujte [nejnovější verze agenta Azure Backup](https://go.microsoft.com/fwLink/?LinkID=288905).

![Vymazat externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Další kroky:
• [Azure Backup – nejčastější dotazy](backup-azure-backup-faq.md)
