---
title: Zálohování Azure zásobníku souborů a aplikací
description: Zálohování a obnovení zásobník Azure souborů a aplikací pro vaše prostředí zásobníku Azure pomocí Azure Backup.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: adigan
ms.openlocfilehash: 7baaa29d205c09daaeeebf44a4bad338913dcad9
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248856"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Zálohování souborů a aplikací v Azure zásobníku
Azure Backup můžete použít k ochraně (nebo zálohování) souborům a aplikacím v Azure zásobníku. K zálohování souborů a aplikací, nainstalujte Microsoft Azure Backup Server jako virtuální počítač spuštěný v Azure zásobníku. Všechny aplikace spuštěné na jakýkoli server zásobník Azure ve stejné virtuální síti můžete chránit. Po instalaci Azure Backup Server, přidejte disky Azure zvýšit místní úložiště, které jsou k dispozici pro krátkodobé zálohování. Azure Backup Server používá úložiště Azure pro dlouhodobé uchovávání.

> [!NOTE]
> I když jsou podobné serveru Azure Backup a System Center Data Protection Manager (DPM), aplikace DPM není podporována pro použití s Azure zásobníku.
>

Tento článek nepopisuje instalace serveru Azure Backup v prostředí Azure zásobníku. K instalaci serveru Azure Backup v zásobníku Azure, najdete v článku [Příprava zálohování úloh pomocí serveru Azure Backup](backup-mabs-install-azure-stack.md).


## <a name="back-up-azure-stack-vm-file-data-to-azure"></a>Virtuální počítač Azure zásobníku souboru data zálohovat na Azure

Konfigurace serveru Azure Backup chránit virtuální počítače IaaS, otevřete konzolu serveru Azure Backup. Použijete konzolu ke konfiguraci skupin ochrany a k ochraně dat na virtuálních počítačích.

1. V konzole serveru Azure Backup, klikněte na **ochrany** a na panelu nástrojů klikněte na tlačítko **nový** otevřete **vytvořením nové skupiny ochrany** průvodce.

   ![Konfigurace ochrany v konzole serveru Azure Backup](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    To může trvat několik sekund, než průvodce otevřete. Jakmile Průvodce se otevře, klikněte na **Další** pro přechod **vybrat typ skupiny ochrany** obrazovky.

   ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na **vybrat typ skupiny ochrany** obrazovky, zvolte **servery** a klikněte na tlačítko **Další**.

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    **Vybrat členy skupiny** obrazovky otevře. 

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. V **vybrat členy skupiny** obrazovky, klikněte na tlačítko **+** rozbalte seznam podřízených položek. Pro všechny položky, které chcete chránit zaškrtněte políčko. Jakmile nebyly vybrány všechny položky, klikněte na možnost **Další**.

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Společnost Microsoft doporučuje, třeba umisťovat všechny virtuální počítače, které budou sdílet zásady ochrany, do skupiny ochrany jeden. Úplné informace o plánování a nasazení skupin ochrany, najdete v článku System Center DPM [nasazení skupin ochrany](https://docs.microsoft.com/en-us/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. V **vyberte způsob ochrany dat** obrazovky, zadejte název pro skupinu ochrany. Zaškrtněte políčko **chci krátkodobou ochranu pomocí:** a **chci online ochranu**. Klikněte na **Další**.

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Chcete-li vybrat **chci online ochranu**, musíte nejdřív vybrat **chci krátkodobou ochranu pomocí:** disku. Azure Backup Server nelze chránit na pásku, aby disk je pouze volbu pro krátkodobou ochranu.

5. V **zadat krátkodobé cíle** obrazovky, určit, jak dlouho chcete zachovat body obnovení, které jsou uloženy na disk a kdy se mají ukládat přírůstkové zálohy. Klikněte na **Další**.

    > [!IMPORTANT]
    > Měli byste **není** zachovat provozní obnovení (zálohování) data na disky připojené k Azure Backup Server pro více než pět dní.
    >

    ![Otevře se Průvodce novou skupinou ochrany](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Místo výběru intervalu pro přírůstkové zálohy, ke spuštění expresního úplného zálohování před každým naplánovaným bodem obnovení, klikněte na tlačítko **těsně před bodem obnovení**. Pokud chráníte úlohy aplikací, Azure Backup Server vytvoří body obnovení na plán frekvence synchronizace (za předpokladu, že aplikace podporuje přírůstkové zálohování). Pokud aplikace nepodporuje přírůstkové zálohování, spustí se Azure Backup Server expresní úplné zálohování.

    Pro **souboru body obnovení**, určete, kdy se k vytvoření bodů obnovení. Klikněte na tlačítko **upravit** nastavit časy a dny v týdnu, kdy se vytváří body obnovení.

6. V **zkontrolovat přidělení disku** obrazovky, přečtěte si místo disku fondu úložiště přidělené pro skupinu ochrany.

    **Celková velikost dat** je velikost dat, které chcete zálohovat a **místo, které se má zřídit na disku** na serveru Azure Backup je doporučené místo pro skupinu ochrany. Azure Backup Server zvolí ideální zálohování svazku, na základě nastavení. Však můžete upravit možnosti zálohování svazku v podrobnostech přidělení disku. Pro úlohy v rozevírací nabídce vyberte upřednostňovaný úložiště. Vaše úpravy změnit hodnoty pro celkové úložiště a volný úložný prostor v podokně dostupné úložiště disku. Underprovisioned prostor je velikost úložiště, které serveru Azure Backup naznačuje, že přidáte do svazku, chcete-li pokračovat pomocí zálohování bez problémů v budoucnu.

7. V **vyberte způsob vytvoření repliky**vyberte, jak chcete zpracovat počáteční úplná data replikace. Pokud se rozhodnete replikaci přes síť, Azure doporučuje že vybrat dobu mimo špičku. Pro velké objemy dat nebo ne úplně optimální síťové podmínky zvažte replikaci dat offline pomocí vyměnitelného média.

8. V **zvolte možnosti kontroly konzistence**vyberte, jak chcete automatizovat kontroly konzistence. Povolení kontroly konzistence se spustit pouze v případě, že nekonzistentní replikaci dat nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, spusťte ruční kontrolu kdykoli podle:
    * V **ochrany** serveru Azure Backup konzoly, klikněte pravým tlačítkem na skupinu ochrany a vyberte **provést kontrolu konzistence**.

9. Pokud chcete zálohovat do Azure, na **zadejte data pro online ochranu** stránky ujistěte, že jsou vybrána úlohy, které chcete zálohovat do Azure.

10. V **zadejte plán online zálohování**, zadejte, kdy má vzniknout přírůstkové zálohování do Azure. 

    Můžete naplánovat zálohování spouštět každý den, týden nebo měsíc a rok a čas a datum, kdy by měly být spuštěny. Zálohování může dojít, až dvakrát za den. Pokaždé, když úloha zálohování se spustí, data bodu obnovení se vytvoří v Azure z kopie zálohovaná data uložená na disku Azure Backup Server.

11. V **zadejte zásady online uchovávání dat.**, zadejte v Azure jsou uchovány jak body obnovení vytvořené ze zálohy denně nebo týdně nebo měsíčně nebo ročně.

12. V **zvolte online replikace**, zadejte, jak proběhne počáteční úplná replikace dat. 

    Můžete replikovat přes síť, nebo proveďte offline zálohování (offline synchronizace replik indexů). Zálohování offline používá [funkce Import úložiště Azure](./backup-azure-backup-import-export.md).

13. Na **Souhrn**, zkontrolujte nastavení. Když kliknete na tlačítko **vytvořit skupinu**, dojde k počáteční replikaci dat. Po replikaci dat dokončení, na **stav** stránky, zobrazuje stav skupiny ochrany jako **OK**. Úlohu prvotního zálohování se provede záloha podle ochranu skupiny nastavení.

Otázky, které je třeba, když si odpovíte: jak můžete rozšíření diskového úložiště Azure zásobníku krátkodobé ukládání na disk. Jaké jsou pokyny, které je třeba volat out vysvětlením krátkodobý disk úložiště?

## <a name="recover-file-data"></a>Obnovení dat souboru

Můžete obnovit data do virtuálního počítače pomocí serveru Azure Backup konzoly.

1. V konzole serveru Azure Backup na navigačním panelu klikněte na tlačítko **obnovení** a vyhledejte data, kterou chcete obnovit. V podokně výsledků data vyberte.

2. Kalendářních dat tučným písmem v kalendáři v části bodů obnovení, znamenat, že body obnovení jsou k dispozici. Vyberte datum pro bod obnovení pro obnovení.

3. V **obnovitelných položek** podokně, vyberte položku, kterou chcete obnovit.

4. V **akce** podokně klikněte na tlačítko **obnovit** otevřete Průvodce obnovením.

5. Data můžete obnovit takto:

    * **Obnovit na původní umístění** -li klientský počítač je připojený prostřednictvím sítě VPN, tato možnost není vhodná. Místo toho použít alternativní umístění a potom zkopírovat data z tohoto umístění.
    * **Obnovit do alternativního umístění**

6. Zadejte možnosti obnovení:

    * Pro **chování obnovení existující verze**, vyberte **vytvořit kopii**, **přeskočit**, nebo **přepsat**. Přepsání je dostupná až po obnovení do původního umístění.
    * Pro **obnovit zabezpečení**, zvolte **použít nastavení cílového počítače** nebo **použít nastavení zabezpečení verze bodu obnovení**.
    * Pro **omezení využití šířky pásma**, klikněte na tlačítko **upravit** povolit omezení využití šířky pásma.
    * Vyberte **založené na síti SAN povolit obnovení pomocí snímků hardwaru** použít snímky hardwaru založené na síti SAN pro rychlejší obnovení. Tato možnost je platná pouze v případě, že máte síť SAN, kde se zapnutou funkcí snímků hardwaru. Chcete-li zapisovatelné bodu obnovení, musí být schopna vytvářet klony a rozdělovat je síť SAN. Chráněný virtuální počítač a Azure Backup Server musí být připojen k téže síti SAN.
    * **Oznámení** klikněte na tlačítko **odeslat e-mail po dokončení obnovení**, a zadejte příjemce, kteří budou obdrží oznámení. E-mailové adresy oddělujte čárkami.
    * Po provedení výběr, klikněte na **další**

7. Zkontrolujte nastavení obnovení a klikněte na tlačítko **obnovit**. 

    > [!Note] 
    > Když probíhá úloha obnovení, se zruší veškeré úlohy synchronizace pro vybrané položky obnovení.
    >

Pokud používáte moderní zálohování úložiště (MB), se nepodporuje obnovení koncového uživatele souborového serveru (EUR). Souborový Server EUR má závislost na Stínová kopie svazku Service (VSS), který nepoužívá moderní úložiště záloh. Pokud je povoleno EUR, použijte následující kroky k obnovení dat:

1. Přejděte do chráněné soubory a klikněte pravým tlačítkem na název souboru a vyberte **vlastnosti**.

2. Na **vlastnosti** nabídky, klikněte na tlačítko **předchozí verze** a vyberte verzi, kterou chcete obnovit.



## <a name="register-azure-backup-server-with-a-vault"></a>Zaregistrujte Azure Backup Server k trezoru
Zadejte kroky pro znázorňující postup:

1. Otevřete trezor služeb zotavení.
2. Klikněte na tlačítko infrastruktury zálohování.
3. Servery pro správu zálohování zobrazení.

## <a name="see-also"></a>Další informace najdete v tématech
Informace o používání serveru Azure Backup k ochraně jiné úlohy najdete v následujících článcích:
- [Zálohování farmy služby SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Zálohování serveru SQL](backup-azure-sql-mabs.md)
