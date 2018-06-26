---
title: Zálohování farmy služby SharePoint v Azure zásobníku
description: Zálohování a obnovení dat služby SharePoint v zásobníku Azure pomocí serveru Azure Backup. Tento článek obsahuje informace pro konfiguraci farmy služby SharePoint tak, že požadovaná data se uloží v Azure. Chráněná data služby SharePoint můžete obnovit z disku nebo z Azure.
services: backup
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 6/8/2018
ms.author: pullabhk
ms.openlocfilehash: 309e817426fff1eb877ab02ae9aa16ddc8f5cf16
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751893"
---
# <a name="back-up-a-sharepoint-farm-on-azure-stack"></a>Zálohování farmy služby SharePoint v Azure zásobníku
Můžete zálohovat farmy služby SharePoint v zásobníku Azure do služby Microsoft Azure pomocí služby Microsoft Azure Backup Server (MABS) v mnohem stejným způsobem, který zálohujete jiných zdrojů dat. Azure Backup poskytuje flexibilitu při plán zálohování k vytvoření denní, týdenní, měsíční nebo roční zálohu odkazuje a poskytuje možnosti zásad uchovávání informací pro různé body zálohy. Poskytuje taky možnost k uložení kopie místního disku pro rychlé cíle doba obnovení (RTO) a slouží k uložení kopie do Azure pro ekonomické, dlouhodobé uchovávání.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Podporované verze služby SharePoint a související scénáře ochrany
Zálohování Azure pro MABS podporuje následující scénáře:

| Úloha | Verze | Nasazení služby SharePoint | Ochrana a obnovení |
| --- | --- | --- | --- | --- | --- |
| SharePoint |SharePoint 2013, SharePoint 2010 a SharePoint 2007 SharePoint 3.0 |SharePoint nasazená jako fyzický server nebo virtuální počítač technologie Hyper-V nebo VMware <br> -------------- <br> Technologie AlwaysOn serveru SQL | Ochrana farmy služby SharePoint možnosti obnovení: farma obnovení, databáze a soubor nebo položka seznamu z bodů obnovení disku.  Obnovení z bodů obnovení Azure farmy a databáze. |

## <a name="before-you-start"></a>Než začnete
Existuje několik věcí, které je potřeba potvrdit před Zálohování farmy služby SharePoint do Azure.

### <a name="prerequisites"></a>Požadavky
Než budete pokračovat, ujistěte se, že máte [nainstalované a připravené serveru Azure Backup](backup-mabs-install-azure-stack.md) chránit úlohy.

### <a name="protection-agent"></a>Agent ochrany
Agent Azure Backup musí být nainstalován na serveru, na kterém běží SharePoint, serverech se systémem SQL Server a všechny ostatní servery, které jsou součástí farmy služby SharePoint. Další informace o tom, jak nastavit agenta ochrany najdete v tématu [instalace agenta ochrany](https://technet.microsoft.com/library/hh758034\(v=sc.12\).aspx).  Jedinou výjimkou je, že instalujete agenta pouze na jednom webovém serveru front-end (WFE). Azure Backup Server musí agenta na jednom serveru WFE pouze sloužit jako vstupní bod pro ochranu.

### <a name="sharepoint-farm"></a>Farmy služby SharePoint
Pro každých 10 milionů položek ve farmě musí být alespoň 2 GB místa na svazku, kde je umístěna složka MABS. Tento prostor je nezbytné pro generování katalogu. Pro MABS obnovit konkrétní položky (kolekce webů, weby, seznamy, knihovny dokumentů, složky, jednotlivé dokumenty a položky seznamu) generování katalogu vytvoří seznam adres URL, které jsou obsaženy v jednotlivých databázích obsahu. Seznam adres URL můžete zobrazit v podokně obnovitelných položek **obnovení** oblasti konzoly pro správu MABS úlohy.

### <a name="sql-server"></a>SQL Server
Azure Backup Server se spouští jako účet LocalSystem. Zálohování databází systému SQL Server, musí MABS oprávnění sysadmin na tento účet pro server, který se systémem SQL Server. Nastavte NT AUTHORITY\SYSTEM na *sysadmin* na serveru, který je spuštěn SQL Server předtím, než ho zálohovat.

Pokud farmy služby SharePoint databáze systému SQL Server, které jsou nakonfigurovány s aliasy systému SQL Server, nainstalujte komponenty klienta systému SQL Server na front-end webovém serveru, který bude chránit MABS.

### <a name="whats-not-supported"></a>Co není podporováno
* MABS, který chrání farmy služby SharePoint nechrání indexy hledání nebo databáze aplikace služby. Musíte konfigurovat ochranu pro tyto databáze samostatně.
* MABS neposkytuje zálohování databází serveru SQL služby SharePoint, které jsou hostované na sdílených složkách škálovatelného souborového serveru (SOFS).

## <a name="configure-sharepoint-protection"></a>Konfigurace ochrany Sharepointu
Než MABS můžete použít k ochraně služby SharePoint, musíte nakonfigurovat službu SharePoint VSS Writer (WSS Writer service) pomocí **ConfigureSharePoint.exe**.

Můžete najít **ConfigureSharePoint.exe** ve složce [Instalační cesta MABS] \bin na front-end webovém serveru. Tento nástroj poskytuje agenta ochrany s přihlašovacími údaji pro farmu služby SharePoint. Spustíte ji na jeden server WFE. Pokud máte více serverů WFE, vyberte pouze jeden, když konfigurujete skupinu ochrany.

### <a name="to-configure-the-sharepoint-vss-writer-service"></a>Konfigurace služby SharePoint VSS Writer
1. Na serveru WFE, na příkazovém řádku přejděte do \bin\ [umístění instalace MABS]
2. Zadejte ConfigureSharePoint - EnableSharePointProtection.
3. Zadejte přihlašovací údaje správce farmy. Tento účet by měl být členem místní skupiny správců na serveru WFE. Pokud není správcem farmy místní správce, udělte následující oprávnění na serveru WFE:
   * Udělte skupině WSS_Admin_WPG úplnou kontrolu ke složce aplikace DPM (% Program Files%\Microsoft Azure Backup\DPM).
   * Udělte oprávnění ke čtení skupině WSS_Admin_WPG ke klíči registru aplikace DPM (HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager).

> [!NOTE]
> Budete muset znovu spustit ConfigureSharePoint.exe vždy, když dojde ke změně v pověřeních správce farmy.
>
>

## <a name="back-up-a-sharepoint-farm-by-using-mabs"></a>Zálohování farmy služby SharePoint pomocí MABS
Po nakonfigurování MABS a farmy služby SharePoint, jak je popsáno dříve, můžete pomocí MABS chráněný službou SharePoint.

### <a name="to-protect-a-sharepoint-farm"></a>Chcete-li chránit farmu služby SharePoint
1. Z **ochrany** kartu konzoly pro správu MABS, klikněte na tlačítko **nový**.
    ![Nové karty ochrana](./media/backup-azure-backup-sharepoint/dpm-new-protection-tab.png)
2. Na **vybrat typ skupiny ochrany** stránky **vytvořením nové skupiny ochrany** průvodce, vyberte **servery**a potom klikněte na **Další**.

    ![Typ skupiny ochrany vyberte](./media/backup-azure-backup-sharepoint/select-protection-group-type.png)
3. Na **vybrat členy skupiny** obrazovky, zaškrtněte políčko pro server SharePoint, které chcete chránit a klikněte na tlačítko **Další**.

    ![Vybrat členy skupiny](./media/backup-azure-backup-sharepoint/select-group-members2.png)

   > [!NOTE]
   > S nainstalovaným agentem ochrany můžete zobrazit serveru v průvodci. MABS také ukazuje jeho strukturu. Protože jste spustili ConfigureSharePoint.exe, MABS komunikuje se službou SharePoint VSS Writer service a její odpovídající databáze systému SQL Server a rozpozná strukturu farmy služby SharePoint, přidružených databázích obsahu a všechny odpovídající položky.
   >
   >
4. Na **vyberte způsob ochrany dat** stránky, zadejte název **skupiny ochrany**a vyberte upřednostňovanou *metody ochrany*. Klikněte na **Další**.

    ![Vyberte způsob ochrany dat](./media/backup-azure-backup-sharepoint/select-data-protection-method1.png)

   > [!NOTE]
   > Způsob ochrany disku pomáhá cíle krátkou dobu obnovení.
   >
   >
5. Na **zadat krátkodobé cíle** vyberte upřednostňovanou **rozsah uchování** a zjistíte, kdy chcete vytváření záloh každý.

    ![Určení krátkodobých cílů](./media/backup-azure-backup-sharepoint/specify-short-term-goals2.png)

   > [!NOTE]
   > Protože obnovení je nejčastěji požadované pro data, která je menší než pět dní, jsme vybrali rozsahem uchování 5 dní na disku a zajistit, že zálohování probíhá při mimo provozní hodiny, v tomto příkladu.
   >
   >
6. Zkontrolujte místo přidělené pro skupinu ochrany na disku fondu úložiště a potom na tlačítko **Další**.
7. Pro každou skupinu ochrany MABS přiděluje místo na disku k uložení a správě repliky. V tomto okamžiku MABS, musíte vytvořit kopii vybraná data. Vyberte, jak a kdy chcete repliku vytvořit a pak klikněte na tlačítko **Další**.

    ![Vyberte způsob vytvoření repliky](./media/backup-azure-backup-sharepoint/choose-replica-creation-method.png)

   > [!NOTE]
   > Abyste měli jistotu, že není uskutečněn síťový provoz, vyberte dobu mimo provozní hodiny.
   >
   >
8. MABS zajistíte integritu dat provedením kontroly konzistence na replice. Existují dvě možnosti k dispozici. Můžete definovat plán, který chcete spustit kontrolu konzistence, nebo aplikace DPM můžete spustit kontrolu konzistence na replice automaticky vždy, když se stane nekonzistentní. Vyberte požadovanou možnost a pak klikněte na tlačítko **Další**.

    ![Kontrola konzistence](./media/backup-azure-backup-sharepoint/consistency-check.png)
9. Na **zadat Data Online ochrany** vyberte farmy služby SharePoint, který chcete chránit a pak klikněte na tlačítko **Další**.

    ![Aplikace DPM Protection1 služby SharePoint](./media/backup-azure-backup-sharepoint/select-online-protection1.png)
10. Na **zadejte plán Online zálohování** vyberte upřednostňovaný plán a pak klikněte na tlačítko **Další**.

    ![Online_backup_schedule](./media/backup-azure-backup-sharepoint/specify-online-backup-schedule.png)

    > [!NOTE]
    > MABS poskytuje maximálně dvě denní zálohování do Azure ze pak k dispozici nejnovější disku bod zálohy. Zálohování Azure můžete také ovládat velikost šířky pásma sítě WAN, který lze použít pro zálohování ve špičce a špičku pomocí [omezení sítě Azure Backup](https://azure.microsoft.com/documentation/articles/backup-configure-vault/#enable-network-throttling).
    >
    >
11. V závislosti na plán zálohování, který jste vybrali, na **zadejte zásady uchovávání Online** vyberte zásady uchovávání informací pro denní, týdenní, měsíční a roční body zálohy.

    ![Online_retention_policy](./media/backup-azure-backup-sharepoint/specify-online-retention.png)

    > [!NOTE]
    > MABS používá schéma uchovávání historických. otec SYN ve které je možné vybrat rozdílné zásady pro různé body zálohy.
    >
    >
12. Podobně jako u disku, repliku bodu počáteční odkaz musí být vytvořen v Azure. Vyberte upřednostňovanou možnost vytvořit kopii prvotní zálohy do Azure, a potom klikněte na **Další**.

    ![Online_replica](./media/backup-azure-backup-sharepoint/online-replication.png)
13. Zkontrolujte vybrané nastavení na **Souhrn** a pak klikněte na tlačítko **vytvořit skupinu**. Zobrazí se zpráva o úspěšném provedení a po vytvoření skupiny ochrany.

    ![Souhrn](./media/backup-azure-backup-sharepoint/summary.png)

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>Obnovení položky služby SharePoint z disku pomocí MABS
V následujícím příkladu *položky obnovení služby SharePoint* omylem odstraněný a je potřeba obnovit.
![Protection4 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Otevřete **konzole správce aplikace DPM**. V jsou uvedeny všechny farmy služby SharePoint, které jsou chráněné službou DPM **ochrany** kartě.

    ![Protection3 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Chcete-li začít obnovili položku, vyberte **obnovení** kartě.

    ![Protection5 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. Můžete hledat SharePoint pro *položky obnovení služby SharePoint* pomocí vyhledávání na základě zástupný znak v rámci obnovení bodu rozsahu.

    ![Protection6 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Vyberte bod obnovení odpovídající ve výsledcích hledání, klikněte pravým tlačítkem položku a pak vyberte **obnovit**.
5. Také můžete procházet různé body obnovení a vyberte databázi nebo položku, kterou chcete obnovit. Vyberte **datum > čas obnovení**a potom vyberte správný **databáze > farmy služby SharePoint > bod obnovení > položky**.

    ![Protection7 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Klikněte pravým tlačítkem položku a pak vyberte **obnovit** otevřete **Průvodce obnovením**. Klikněte na **Další**.

    ![Revidovat výběr obnovení](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Vyberte typ obnovení, který chcete provést a potom klikněte na **Další**.

    ![Typ obnovení](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > Výběr **obnovit na původní** v příkladu obnoví položka k původní web služby SharePoint.
   >
   >
8. Vyberte **proces obnovení** , kterou chcete použít.

   * Vyberte **obnovit bez použití farmy obnovení** Pokud farmy služby SharePoint se nezměnila a je stejná jako bod obnovení, který se obnovuje.
   * Vyberte **obnovit použití farmy obnovení** Pokud od vytvoření bodu obnovení změnila farmy služby SharePoint.

     ![Proces obnovení](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Zadejte pracovní umístění instance SQL serveru k obnovení databáze dočasně a zadejte pracovní sdílené složky na MABS a na serveru, na kterém běží SharePoint o obnovení položky.

    ![Pracovní Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    MABS připojí databázi obsahu, který je hostitelem položky služby SharePoint dočasné instanci systému SQL Server. Z databáze obsahu obnoví položku a vloží ho na pracovní umístění souboru na MABS. Položku obnovení, který je teď na pracovní umístění musí být exportovány do pracovní umístění na farmě služby SharePoint.

    ![Pracovní Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Vyberte **nastavte možnosti obnovení**a použít nastavení zabezpečení pro farmu služby SharePoint nebo použít nastavení zabezpečení bodu obnovení. Klikněte na **Další**.

    ![Možnosti obnovení](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Můžete k omezení využití šířky pásma sítě. Tím se minimalizují dopad na provozním serveru během pracovní doby.
    >
    >
11. Zkontrolujte souhrnné informace a pak klikněte na **obnovit** zahájíte obnovení souboru.

    ![Obnovení souhrn](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Nyní vybrat **monitorování** ve **konzoly pro správu MABS** zobrazíte **stav** obnovení.

    ![Stav obnovení](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > Soubor je nyní obnovit. Můžete obnovit web služby SharePoint k obnovené najdete v souboru.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Obnovení databáze služby SharePoint z Azure pomocí aplikace DPM
1. Pokud chcete obnovit databázi obsahu služby SharePoint, procházet různé body obnovení (jak je uvedeno výše) a vyberte bod obnovení, který chcete obnovit.

    ![Protection8 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Dvakrát klikněte na bod obnovení služby SharePoint zobrazíte dostupné informace o katalogu služby SharePoint.

   > [!NOTE]
   > Protože pro dlouhodobé uchovávání v Azure je chráněn farmy služby SharePoint, nejsou dostupné na MABS žádné informace katalogu (metadata). V důsledku toho vždy, když databáze obsahu služby SharePoint v daném okamžiku je možné obnovit, budete muset znovu katalogu farmy služby SharePoint.
   >
   >
3. Klikněte na tlačítko **opětovného zařazení do katalogu**.

    ![Protection10 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    **Provést novou katalogizaci cloudu** otevře se okno stav.

    ![Protection11 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po dokončení do katalogu se stav změní na *úspěch*. Klikněte na **Zavřít**.

    ![Protection12 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Klikněte na objekt SharePoint ukazuje MABS **obnovení** karty lze získat strukturu databázi obsahu. Klikněte pravým tlačítkem položku a pak klikněte na **obnovit**.

    ![Protection13 MABS služby SharePoint](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Postupujte v tomto okamžiku [kroky obnovení dříve v tomto článku](#restore-a-sharepoint-item-from-disk-using-dpm) k obnovení databázi obsahu služby SharePoint z disku.

## <a name="faqs"></a>Nejčastější dotazy
Otázka: je možné obnovit položky služby SharePoint do původního umístění, pokud je služba SharePoint nakonfigurována pomocí technologie AlwaysOn serveru SQL (s ochrany na disku)?<br>
Odpověď: Ano, položka je možné obnovit do původního webu služby SharePoint.

Otázka: je možné obnovit databázi služby SharePoint do původního umístění, pokud je služba SharePoint nakonfigurována pomocí technologie AlwaysOn serveru SQL?<br>
Odpověď: protože databáze služby SharePoint jsou konfigurované v SQL AlwaysOn, nemůže být upraven Pokud skupina dostupnosti je odebrána. V důsledku toho MABS nelze obnovit databázi do původního umístění. Můžete obnovit databázi systému SQL Server do jiné instance systému SQL Server.

## <a name="next-steps"></a>Další kroky

Najdete v článku [zálohovat soubory a aplikace](backup-mabs-files-applications-azure-stack.md) článku.
Najdete v článku [zálohování systému SQL Server v Azure zásobníku](backup-mabs-sql-azure-stack.md) článku.
