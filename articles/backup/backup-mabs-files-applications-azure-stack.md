---
title: Zálohování souborů ve virtuálních počítačích Azure Stack
description: Azure Backup můžete zálohovat a obnovovat soubory a aplikace Azure Stack u svého prostředí Azure Stack.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: 2bcdf7f720708db1487d7d5cdaee41dc93c05728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172318"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Zálohování souborů a aplikací v Azure Stacku

Azure Backup můžete použít k ochraně (nebo zálohování) souborů a aplikací v Azure Stacku. Chcete-li zálohovat soubory a aplikace, nainstalujte Microsoft Azure Backup Server jako virtuální počítač spuštěný v Azure Stacku. Soubory můžete chránit na libovolném serveru Azure Stack ve stejné virtuální síti. Po instalaci serveru Azure Backup Server přidejte disky Azure, abyste zvýšili místní úložiště dostupné pro krátkodobá data zálohování. Azure Backup Server používá úložiště Azure pro dlouhodobé uchovávání.

> [!NOTE]
> Přestože Azure Backup Server a System Center Data Protection Manager (DPM) jsou podobné, DPM není podporovánpro použití s Azure Stack.
>

Tento článek se nevztahuje na instalaci Azure Backup Server v prostředí Azure Stack. Informace o instalaci Serveru Zálohování Azure do azure zásobníku najdete v článku [Instalace serveru Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Zálohování souborů a složek ve virtuálních počítačích Azure Stack do Azure

Pokud chcete nakonfigurovat Server zálohování Azure pro ochranu souborů ve virtuálních počítačích Azure Stack, otevřete konzolu Azure Backup Server. Konzolu použijete ke konfiguraci skupin ochrany a k ochraně dat ve virtuálních počítačích.

1. V konzole Azure Backup Server klikněte na **Ochrana** a na panelu nástrojů otevřete průvodce **Vytvořit novou skupinu ochrany** klepnutím na tlačítko **Nový.**

   ![Konfigurace ochrany v konzole Azure Backup Server](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Otevření průvodce může trvat několik sekund. Po otevření průvodce předejte tlačítko **Další** na obrazovku **Vybrat typ skupiny ochrany.**

   ![Otevře se průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na obrazovce **Vybrat typ skupiny ochrany** zvolte **Servery** a klepněte na tlačítko **Další**.

    ![Otevře se průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Otevře se obrazovka **Vybrat členy skupiny.**

    ![Otevře se průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Na obrazovce Vybrat členy **+** **skupiny** kliknutím rozbalte seznam podpoložek. U všech položek, které chcete chránit, zaškrtněte políčko. Po výběru všech položek klepněte na tlačítko **Další**.

    ![Otevře se průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Společnost Microsoft doporučuje zařadit všechna data, která budou sdílet zásady ochrany, do jedné skupiny ochrany. Úplné informace o plánování a nasazování skupin ochrany naleznete v článku Nasazení [skupin ochrany](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801)v Centru systému .

4. Na obrazovce **Vybrat metodu ochrany dat** zadejte název skupiny ochrany. Zaškrtněte políčko **pro Chci krátkodobou ochranu pomocí:** a **chci online ochranu**. Klikněte na **Další**.

    ![Otevře se průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Chcete-li vybrat **Chci online ochranu**, musíte nejprve vybrat **Chci krátkodobou ochranu pomocí:** Disk. Azure Backup Server nechrání pásku, takže disk je jedinou volbou pro krátkodobou ochranu.

5. Na obrazovce **Zadat krátkodobé cíle** zvolte, jak dlouho se mají body obnovení ukládat na disk a kdy se mají ukládat přírůstkové zálohy. Klikněte na **Další**.

    > [!IMPORTANT]
    > Neměli **not** byste uchovávat data provozního obnovení (zálohování) na discích připojených k serveru Zálohování Azure více než pět dní.
    >

    ![Otevře se průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Chcete-li spustit expresní úplné zálohování těsně před každým plánovaným bodem obnovení, klepněte na tlačítko **Těsně před bodem obnovení,** abyste vybrali interval pro přírůstkové zálohy. Pokud chráníte úlohy aplikací, Azure Backup Server vytvoří body obnovení podle plánu frekvence synchronizace (za předpokladu, že aplikace podporuje přírůstkové zálohování). Pokud aplikace nepodporuje přírůstkové zálohování, Azure Backup Server spustí expresní úplné zálohování.

    V **poli Body obnovení souboru**určete, kdy se mají body obnovení vytvořit. Chcete-li nastavit časy a dny v týdnu, kdy jsou vytvořeny body obnovení, klepněte na **tlačítko Změnit.**

6. Na obrazovce **Zkontrolovat přidělení disku** zkontrolujte místo na disku fondu úložiště přidělené pro skupinu ochrany.

    **Celková velikost dat** je velikost dat, která chcete zálohovat, a **místo na disku, které se má zřídit** na Serveru Zálohování Azure, je doporučené místo pro skupinu ochrany. Azure Backup Server vybere ideální svazek zálohování na základě nastavení. Možnosti záložního svazku ale můžete upravit v části Podrobnosti přidělení disku. V rozevírací nabídce vyberte požadované úložiště pro úlohy. Úpravy, které provedete, změní hodnoty v polích Celková velikost úložiště a Volný úložný prostor v podokně Dostupný úložný prostor na disku. Nezřízené místo je velikost úložiště, které azure backup server doporučuje přidat do svazku, pokračovat v zálohování hladce v budoucnu.

7. V **části Zvolit metodu vytváření replik**vyberte způsob zpracování počáteční úplné replikace dat. Pokud se rozhodnete replikovat v síti, Azure doporučuje zvolit čas mimo špičku. U velkého množství dat nebo menšího než optimálních podmínek v síti zvažte replikaci dat pomocí vyměnitelného média.

8. V možnosti **Vybrat nastavení kontroly konzistence** vyberte, jak chcete kontroly konzistence automatizovat. Povolit kontroly konzistence spustit pouze v případě, že replikace dat stane nekonzistentní nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, spusťte ruční kontrolu kdykoli takto:
    * V oblasti **Ochrany** konzoly Serveru zálohování Azure klepněte pravým tlačítkem myši na skupinu ochrany a vyberte **příkaz Provést kontrolu konzistence**.

9. Pokud se rozhodnete zálohovat do Azure, na **stránce Zadat data ochrany online** ujistěte se, že jsou vybrané úlohy, které chcete zálohovat do Azure.

10. V **poli Zadejte plán zálohování online**, určete, kdy by mělo dojít k přírůstkovému zálohování do Azure.

    Spouštění zálohování můžete naplánovat na každý den, týden, měsíc nebo rok a k datu a v čase, ve kterém se mají spustit. Zálohování se může spouštět až dvakrát denně. Při každém spuštění úlohy zálohování se v Azure vytvoří bod obnovení dat z kopie zálohovaných dat uložených na disku Azure Backup Server.

11. V **poli Zadejte zásady uchovávání informací online**, určete, jak se body obnovení vytvořené z denních/týdenních/měsíčních/ročních záloh uchovávají v Azure.

12. V **části Zvolit online replikaci**určete, jak dojde k počáteční úplné replikaci dat.

13. V **části Souhrn**zkontrolujte nastavení. Po klepnutí na tlačítko **Vytvořit skupinu**dojde k počáteční replikaci dat. Po dokončení replikace dat se na stránce **Stav** zobrazí stav skupiny ochrany jako **OK**. Počáteční úloha zálohování probíhá v souladu s nastavením skupiny ochrany.

## <a name="recover-file-data"></a>Obnovení dat souboru

Pomocí konzoly Azure Backup Server můžete obnovit data do virtuálního počítače.

1. V konzole Azure Backup Server klikněte na navigačním panelu na **Obnovení** a vyhledejte data, která chcete obnovit. V podokně výsledků tato data vyberte.

2. V kalendáři v části body obnovení jsou k dispozici data tučně označují body obnovení. Vyberte datum, které chcete obnovit.

3. V podokně **Obnovitelné položky** vyberte položku, kterou chcete obnovit.

4. Spusťte Průvodce obnovením tak, že v podokně **Akce** kliknete na tlačítko **Obnovit**.

5. Data můžete obnovit následujícím postupem:

    * **Obnovit do původního umístění** – Pokud je klientský počítač připojen přes VPN, tato možnost nefunguje. Místo toho použijte alternativní umístění a zkopírujte data z tohoto umístění.
    * **Obnovit na alternativní místo**

6. Zadejte možnosti obnovení:

    * V **části Existující chování obnovení verze**vyberte možnost Vytvořit **kopii**, **Přeskočit**nebo **Přepsat**. Přepsání je k dispozici pouze při obnovení do původního umístění.
    * V **části Obnovení zabezpečení** **zvolte Použít nastavení cílového počítače** nebo Použít nastavení zabezpečení verze bodu **obnovení**.
    * V **případě omezení využití šířky pásma sítě**klepněte na tlačítko **Změnit** a povolte omezení využití šířky pásma sítě.
    * **Oznámení** Po dokončení obnovení klikněte **na Odeslat e-mail**a zadejte příjemce, kteří obdrží oznámení. E-mailové adresy oddělujte čárkami.
    * Po provedení výběru klikněte na **Další**

7. Zkontrolujte nastavení obnovení a klikněte na tlačítko **Obnovit**.

    >[!Note]
    >Během probíhající úlohy obnovení jsou zrušeny všechny úlohy synchronizace pro vybrané položky obnovení.

Pokud používáte moderní úložiště zálohování (MBS), obnovení koncového uživatele souborového serveru (EUR) není podporováno. Souborový server EUR má závislost na službě VSS (Volume Shadow Copy Service), kterou moderní úložiště zálohování nepoužívá. Pokud je povolena funkce EUR, použijte k obnovení dat následující kroky:

1. Přejděte na chráněné soubory, klepněte pravým tlačítkem myši na název souboru a vyberte **příkaz Vlastnosti**.

2. V nabídce **Vlastnosti** klikněte na **Předchozí verze** a zvolte verzi, kterou chcete obnovit.

## <a name="view-azure-backup-server-with-a-vault"></a>Zobrazení zálohovacího serveru Azure pomocí trezoru

Chcete-li zobrazit entity Azure Backup Server na webu Azure Portal, postupujte takto:

1. Otevřete trezor služby Recovery Services.
2. Klikněte na Záložní infrastruktura.
3. Zobrazení serverů pro správu zálohování.

## <a name="next-steps"></a>Další kroky

Informace o použití Azure Backup Server k ochraně jiných úloh, najdete v jednom z následujících článků:

* [Zálohování sharepointové farmy](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
* [Zálohování serveru SQL](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
