---
title: Zálohování souborů v zásobníku virtuálních počítačů Azure.
description: Použití Azure Backup k zálohování a obnovení souborů Azure Stack a aplikace do prostředí Azure Stack.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 67d79f2aa41bab8a14d693098538d22ffeb05a4e
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55301471"
---
# <a name="back-up-files-on-azure-stack"></a>Zálohování souborů ve službě Azure Stack
Azure Backup vám umožní chránit (nebo zobrazit souhrny) souborů a aplikací ve službě Azure Stack. K zálohování souborů a aplikací, nainstalujte Microsoft Azure Backup Server jako virtuální počítač spuštěný ve službě Azure Stack. Můžete chránit soubory na libovolném serveru služby Azure Stack ve stejné virtuální síti. Jakmile nainstalujete Azure Backup Server, přidání disků pro zvýšení místní úložiště k dispozici pro krátkodobé zálohování dat v Azure. Azure Backup serveru využívá úložiště Azure pro dlouhodobé uchovávání.

> [!NOTE]
> I když Azure Backup Server a System Center Data Protection Manager (DPM) jsou podobné, aplikace DPM není podporována pro použití se službou Azure Stack.
>

Tento článek nepopisuje instalace Azure Backup serveru v prostředí Azure Stack. Pokud chcete nainstalovat Azure Backup serveru ve službě Azure Stack, najdete v článku, [instalace Azure Backup serveru](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Zálohování souborů a složek v zásobníku virtuálních počítačů Azure do Azure

Ke konfiguraci serveru Azure Backup při ochraně souborů ve službě Azure Stack virtual machines, otevřete konzolu Azure Backup serveru. Konfigurace skupin ochrany a chránit data na virtuálních počítačích budete pomocí konzoly.

1. V konzole Azure Backup serveru klikněte na **ochrany** a na panelu nástrojů klikněte na tlačítko **nový** otevřít **vytvořením nové skupiny ochrany** průvodce.

   ![Konfigurace ochrany v konzole Azure Backup serveru](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Může trvat několik sekund, než průvodce otevřete. Jakmile se otevře průvodce, klikněte na tlačítko **Další** k přechodu na **vybrat typ skupiny ochrany** obrazovky.

   ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na **vybrat typ skupiny ochrany** obrazovce **servery** a klikněte na tlačítko **Další**.

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    **Vybrat členy skupiny** otevře se obrazovka. 

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. V **vybrat členy skupiny** obrazovce, klikněte na tlačítko **+** rozbalte seznam podřízených položek. Pro všechny položky, které chcete chránit zaškrtněte políčko. Jakmile máte vybrané všechny položky, klikněte na tlačítko **Další**.

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Společnost Microsoft doporučuje uvedení všechna data, která budou sdílet zásady ochrany, do jednoho ochranné skupiny. Podrobnější informace o plánování a nasazení skupin ochrany, naleznete v článku System Center DPM [nasazení skupin ochrany](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. V **vybrat způsob ochrany dat** zadejte název pro skupinu ochrany. Zaškrtněte políčko pro **chci krátkodobou ochranu pomocí:** a **chci online ochranu**. Klikněte na **Další**.

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Chcete-li vybrat **chci online ochranu**, musíte nejprve vybrat **chci krátkodobou ochranu pomocí:** Disk. Azure Backup Server neposkytuje ochranu na pásku, tedy pouze volbu pro krátkodobou ochranu pomocí disku.

5. V **zadat krátkodobé cíle** obrazovky, určit, jak dlouho chcete zachovat body obnovení, který je uložen na disk a kdy se má uložit přírůstkové zálohování. Klikněte na **Další**.

    > [!IMPORTANT]
    > Měli byste **není** zachovat provozní obnovení (backup) data na disky připojené k Azure Backup Server více než pět dní.
    >

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Místo výběru intervalu pro přírůstkové zálohy, aby se spustila expresní úplné zálohování těsně před každou naplánovaným bodem obnovení, klikněte na tlačítko **těsně před bodem obnovení**. Pokud chráníte úlohy aplikací, Azure Backup serveru vytvoří body obnovení za plán frekvence synchronizace (za předpokladu, že aplikace podporuje přírůstkové zálohování). Pokud aplikace nepodporuje přírůstkové zálohování, Azure Backup Server se spustí pomocí expresního úplného zálohování.

    Pro **body obnovení souboru**, určete, kdy se k vytvoření bodů obnovení. Klikněte na tlačítko **změnit** nastavení doby a dny v týdnu, kdy se vytváří body obnovení.

6. V **Kontrola přidělení disku** obrazovky, přečtěte si místo disku fondu úložiště přidělené pro skupinu ochrany.

    **Celková velikost dat** je velikost dat, které chcete zálohovat a **místo, které se mají zřídit na disku** na Azure Backup serveru je doporučené místo pro skupinu ochrany. Azure Backup Server vybere ideální záložní svazek, na základě nastavení. Však můžete upravit možnosti záložního svazku v podrobnosti přidělení disku. Pro úlohy vyberte požadované úložiště v rozevírací nabídce. Vaše úpravy změňte hodnoty pro volného úložiště a celkový úložiště v podokně dostupné diskové úložiště. Nevytížené místo představuje velikost úložiště, které Azure Backup serveru navrhuje že přidat do svazku, aby v budoucnu hladce pokračovat v zálohování.

7. V **vybrat způsob vytvoření repliky**, vyberte, jakým způsobem chcete počáteční úplné replikace dat. Pokud se rozhodnete pro replikaci přes síť, Azure doporučuje že vybrat dobu mimo špičku. Pro velké objemy dat nebo ne úplně optimální síťové podmínky zvažte replikaci dat pomocí vyměnitelného média.

8. V **vyberte nastavení kontroly konzistence**vyberte, jak chcete automatizovat kontroly konzistence. Povolení kontroly konzistence spustit jenom v případě, že replikace dat přestane být konzistentní, nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, kdykoli spusťte ruční kontrolu:
    * V **ochrany** oblasti konzoly Azure Backup Server, klikněte pravým tlačítkem na skupinu ochrany a vyberte **provést kontrolu konzistence**.

9. Pokud se rozhodnete pro zálohování do Azure, na **zadat data online ochrany** stránky Ujistěte se, že jsou vybrané úlohy, které chcete zálohovat do Azure.

10. V **zadat plán online zálohování**, určete, kdy by se uplatňují přírůstkové zálohování do Azure. 

    Můžete naplánovat zálohování každý den/týden/měsíc/rok a čas/datum, kdy se mají spustit. Zálohování se může spouštět až dvakrát denně. Pokaždé, když se spouští úloha zálohování, bodem obnovení dat se vytvoří v Azure z kopie zálohovaných dat uložených na disku Azure Backup Server.

11. V **zadat zásady online uchovávání dat**, určete, jak se vytváření bodů obnovení z denních, týdenních nebo měsíčních/ročních záloh uchovávaných v Azure.

12. V **zvolit online replikace**, určete, jak proběhne počáteční úplné replikace dat. 

13. Na **Souhrn**, zkontrolujte nastavení. Po kliknutí na **vytvořit skupinu**, dojde k počáteční data replikace. Při replikaci dat dokončí, dále **stav** stránce ukazovat stav skupiny ochrany **OK**. Úlohy prvotního zálohování dojde k přírůstkovému ochranu skupiny nastavení.

## <a name="recover-file-data"></a>Obnovení dat souboru

Pomocí konzoly Azure Backup serveru k obnovení dat k virtuálnímu počítači.

1. V konzole Azure Backup serveru na navigačním panelu klikněte na tlačítko **obnovení** a vyhledejte data, kterou chcete obnovit. V podokně výsledků data vyberte.

2. Kalendářních dat tučným písmem v kalendáři v sekci bodů obnovení, označuje, že se body obnovení jsou k dispozici. Vyberte datum pro obnovení.

3. V **obnovitelná položka** podokně, vyberte položku, kterou chcete obnovit.

4. V **akce** podokně klikněte na tlačítko **obnovit** otevřete Průvodce obnovením.

5. Data můžete obnovit následujícím způsobem:

    * **Obnovit do původního umístění** -li klientský počítač je připojený prostřednictvím sítě VPN, tato možnost není vhodná. Místo toho použijte alternativní umístění a potom zkopírujte data z tohoto umístění.
    * **Obnovit do alternativního umístění**

6. Zadejte možnosti obnovení:

    * Pro **chování obnovení existující verze**vyberte **vytvořit kopii**, **přeskočit**, nebo **přepsat**. Přepsat je dostupná jenom při obnovení do původního umístění.
    * Pro **obnovit zabezpečení**, zvolte **použít nastavení cílového počítače** nebo **použít nastavení zabezpečení verze bodu obnovení**.
    * Pro **omezení využití šířky pásma**, klikněte na tlačítko **změnit** zapnete omezení využití šířky pásma sítě.
    * **Oznámení** klikněte na tlačítko **po dokončení obnovy zaslat e-mail**, a zadejte příjemce, kterým bude oznámení zasláno. E-mailové adresy oddělujte čárkami.
    * Po provedení výběry, klikněte na tlačítko **další**

7. Zkontrolujte nastavení obnovení a klikněte na tlačítko **obnovit**. 

    > [!Note] 
    > Zatímco probíhá úloha obnovení, se zruší veškeré úlohy synchronizace pro vybrané položky obnovení.
    >

Pokud při použití moderního úložiště zálohování (MB), souborového serveru obnovení koncového uživatele (EUR) se nepodporuje. Soubor serveru obnovení koncového uživatele má závislost na Stínová kopie svazku Service (VSS), které moderní úložiště záloh nepoužívá. Pokud je povolené EUR, následujícím postupem obnovit data:

1. Přejděte do chráněné soubory a klikněte pravým tlačítkem na název souboru a vyberte **vlastnosti**.

2. Na **vlastnosti** nabídky, klikněte na tlačítko **předchozí verze** a zvolte verzi, kterou chcete obnovit.

## <a name="view-azure-backup-server-with-a-vault"></a>Zobrazení Azure Backup serveru k trezoru
Pokud chcete zobrazit entity Azure Backup serveru na webu Azure Portal, provedením následujících kroků:
1. Otevřete trezor služby Recovery Services.
2. Klikněte na infrastrukturu pro zálohování.
3. Zobrazit servery správy zálohování.

## <a name="see-also"></a>Další informace najdete v tématech
Informace o použití Azure Backup serveru k ochraně jiných úloh naleznete v následujících článcích:
- [Zálohování Sharepointové farmy](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Zálohování SQL serveru](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
