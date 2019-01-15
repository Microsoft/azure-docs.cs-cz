---
title: Obnovení dat ze Azure Backup serveru
description: Obnovte data, která jste chránili do trezoru služby Recovery Services z jakékoli Azure Backup Server zaregistrovaný do tohoto trezoru.
services: backup
author: nkolli1
manager: shreeshd
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: adigan
ms.openlocfilehash: 66d78be296d0786626325df36aa133d277131b3b
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267092"
---
# <a name="recover-data-from-azure-backup-server"></a>Obnovení dat z Azure Backup Serveru
Použití Azure Backup serveru k obnovení dat, které jste zálohovali pro trezor služby Recovery Services. Proces pro to tak je integrovaná Konzola pro správu Azure Backup serveru a je podobný postupu obnovení pro jiné komponenty Azure Backup.

> [!NOTE]
> Tento článek týká se to [System Center Data Protection Manager 2012 R2 s UR7 nebo novější](https://support.microsoft.com/en-us/kb/3065246)kombinované s [nejnovější verzi agenta Azure Backup](https://aka.ms/azurebackup_agent).
>
>

Chcete-li obnovit data ze Azure Backup serveru:

1. Z **obnovení** kartu konzoly pro správu Azure Backup Server, klikněte na tlačítko **přidat externí DPM** (v levém horním rohu obrazovky).   
    ![Přidat externí DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Stáhnout nové **přihlašovací údaje trezoru** z trezoru přidružené **Azure Backup serveru** tam, kde probíhá obnovení dat, zvolte Azure Backup serveru ze seznamu serverů Azure Backup zaregistrované pomocí služby Recovery Services trezoru a zadejte **šifrovací heslo** přidružené k serveru, jehož data obnovena.

    ![Přihlašovací údaje externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Pouze servery pro zálohování Azure přidružené k stejnému trezoru registraci můžete obnovit data uživatele toho druhého.
   >
   >

    Po úspěšném přidání externí Azure Backup serveru můžete procházet data z externího serveru a místní Azure Backup serveru z **obnovení** kartu.
3. Seznam dostupných provozních serverech, chráněné službou externí Azure Backup serveru procházením vyberte příslušný zdroj dat.

    ![Procházet externí DPM Server](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Vyberte **měsíci a roce** z **body obnovení** rozevírací seznam, vyberte požadované **datum obnovení** bod obnovení byl vytvořen, a vyberte **Čas obnovení**.

    Seznam souborů a složek se zobrazí v dolním podokně, které můžete procházet a obnovit do libovolného umístění.

    ![Body obnovení serveru externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Klikněte pravým tlačítkem myši klikněte na příslušnou položku a klikněte na tlačítko **obnovit**.

    ![Externí obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Zkontrolujte **obnovení výběru**. Zkontrolujte data a času záložní kopie, Probíhá obnovení, jakož i zdroj, ze kterého byl vytvořen záložní kopie. Pokud je výběr správné, klikněte na tlačítko **zrušit** přejděte zpátky na kartě obnovení vyberte bod obnovení odpovídající. Pokud je výběr správné, klikněte na tlačítko **Další**.

    ![Externí souhrnu obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Vyberte **obnovení do alternativního umístění**. **Procházet** do správného umístění pro obnovení.

    ![Externí aplikace DPM pro obnovení alternativního umístění](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Zvolte si možnost související s **vytvořit kopii**, **přeskočit**, nebo **přepsat**.

   * **Vytvořit kopii** -vytvoří kopii souboru, pokud dojde ke kolizi názvu.
   * **Přeskočit** – Pokud je kolize názvů, nedojde k odstranění souboru, což ponechá původní soubor.
   * **Přepsat** – Pokud je kolize názvů, přepíše stávající kopie souboru.

     Vyberte odpovídající možnost **obnovit zabezpečení**. Můžete použít nastavení zabezpečení cílového počítače, ve kterém se obnovuje data nebo nastavení zabezpečení, které byly pro produkt v době vytvoření bodu obnovení.

     Určení, zda **oznámení** přijde, po úspěšném dokončení obnovení.

     ![Oznámení o obnovení externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. **Souhrn** obrazovky je uveden seznam možností zatím zvolili. Po kliknutí na **"Obnovit"**, data budou obnovena do odpovídající místní umístění.

    ![Souhrn možností obnovení externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Úloha obnovení můžete sledovat v **monitorování** kartu Azure Backup serveru.
   >
   >

    ![Monitorování obnovení](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Můžete kliknout na **vymazat externí DPM** na **obnovení** kartu serveru DPM, odeberete zobrazení externího serveru aplikace DPM.

    ![Vymazat externí DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Řešení potíží s chybové zprávy
| Ne. | Chybová zpráva | Postup při řešení potíží |
|:---:|:--- |:--- |
| 1. |Tento server není registrovaný pro trezor určený přihlašovacími údaji. |**Příčina:** Tato chyba se zobrazí, když vybraný soubor přihlašovacích údajů trezoru nepatří do trezoru služby Recovery Services, které jsou spojené s Azure Backup Server, na kterém dojde k pokusu o obnovení. <br> **Řešení:** Stáhněte si soubor s přihlašovacími údaji trezoru z trezoru služby Recovery Services, na které je registrované Azure Backup serveru. |
| 2. |Buď obnovitelná data nejsou k dispozici, nebo vybraný server není DPM server. |**Příčina:** Nejsou že žádné jiné servery Azure Backup zaregistrované do trezoru služby Recovery Services, nebo na serverech ještě nepřidali metadata nebo vybraný server není Server služby Azure Backup (označuje se také jako Windows Server nebo klienta Windows). <br> **Řešení:** Pokud jsou že servery Azure Backup zaregistrované do trezoru služby Recovery Services, ujistěte se, že je nainstalovaný nejnovější agent Azure Backup. <br>Pokud jsou že servery Azure Backup zaregistrované do trezoru služby Recovery Services, počkejte po instalaci zahájit proces obnovení za den. Noční úlohu odešlete metadata pro všechny chráněné zálohování do cloudu. Data budou k dispozici pro obnovení. |
| 3. |Žádným jiným serverem aplikace DPM je registrované k tomuto trezoru. |**Příčina:** Nejsou žádné další Azure Backup servery, které jsou zaregistrované do trezoru, ze kterého je Probíhá pokus o obnovení.<br>**Řešení:** Pokud jsou že servery Azure Backup zaregistrované do trezoru služby Recovery Services, ujistěte se, že je nainstalovaný nejnovější agent Azure Backup.<br>Pokud jsou že servery Azure Backup zaregistrované do trezoru služby Recovery Services, počkejte po instalaci zahájit proces obnovení za den. Noční úlohu ukládání metadat pro všechny chráněné zálohování do cloudu. Data budou k dispozici pro obnovení. |
| 4. |Zadané šifrovací heslo neodpovídá heslu přidruženému k následujícímu serveru: **<server name>** |**Příčina:** Šifrovací heslo použít Probíhá šifrování dat od Azure Backup Server, který se obnovuje neodpovídá zadané šifrovací heslo. Agent se nemůže data dešifrovat. Proto obnovení není úspěšné.<br>**Řešení:** Zadejte prosím přesné stejné šifrovací heslo přidružené k Azure Backup serveru, jehož data obnovena. |

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Proč nelze přidat externí DPM server po instalaci UR7 a nejnovější verzi agenta Azure Backup?

Pro servery aplikace DPM se zdroji dat, které jsou chráněné do cloudu (s použitím kumulativní aktualizace starší než kumulativní aktualizace 7), musíte počkat aspoň jeden den po instalaci UR7 a nejnovější verzi agenta Azure Backup, aby vám začali **přidat externí DPM server**. Jeden den časové období, je potřeba nahrát metadat skupiny ochrany DPM do Azure. Při prvním průchodu noční úlohu nahraje metadat skupiny ochrany.

### <a name="what-is-the-minimum-version-of-the-microsoft-azure-recovery-services-agent-needed"></a>Co je minimální verze agenta Microsoft Azure Recovery Services potřeby?

Minimální verze systému Microsoft Azure Recovery Services agenta a agenta Azure Backup, nutné k povolení této funkce je 2.0.8719.0.  Chcete-li zobrazit verze agenta: Otevřete ovládací panely **>** všech ovládacích panelů **>** programy a funkce **>** Agent Microsoft Azure Recovery Services. Pokud je verze nižší než 2.0.8719.0, stáhněte a nainstalujte [nejnovější verzi agenta Azure Backup](https://go.microsoft.com/fwLink/?LinkID=288905).

![Vymazat externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## <a name="next-steps"></a>Další kroky:
• [Azure Backup – nejčastější dotazy](backup-azure-backup-faq.md)
