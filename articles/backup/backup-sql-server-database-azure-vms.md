---
title: Zálohování databází SQL Serveru ve virtuálních počítačích Azure
description: V tomto článku se dozvíte, jak zálohovat SQL Server databáze na virtuálních počítačích Azure pomocí Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: faf5ffd65f9b3133c504413201d58aee988af71a
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248103"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Zálohování databází SQL Serveru ve virtuálních počítačích Azure

SQL Server databáze jsou kritické úlohy, které vyžadují nízký cíl bodu obnovení (RPO) a dlouhodobé uchovávání. SQL Server databáze běžící na virtuálních počítačích Azure můžete zálohovat pomocí [Azure Backup](backup-overview.md).

Tento článek popisuje, jak zálohovat databázi SQL Server, která běží na virtuálním počítači Azure, do trezoru služby Azure Backup Recovery Services.

V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Vytvořte a nakonfigurujte trezor.
> * Objevte databáze a nastavte zálohy.
> * Nastavte automatickou ochranu pro databáze.

>[!NOTE]
>**Obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a obnovitelné odstranění pro SAP HANA v úlohách virtuálních počítačů Azure** je teď dostupné ve verzi Preview.<br>
>Pokud si chcete zaregistrovat verzi Preview, napište nám naAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Požadavky

Před zálohováním SQL Server databáze ověřte následující kritéria:

1. Identifikujte nebo vytvořte [Recovery Services trezor](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) ve stejné oblasti a předplatném jako virtuální počítač hostující instanci SQL Server.
2. Ověřte, že virtuální počítač má [připojení k síti](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
3. Ujistěte se, že SQL Server databáze dodržují [pravidla pro pojmenovávání databází pro Azure Backup](#database-naming-guidelines-for-azure-backup).
4. Ověřte, že nemáte pro databázi povolená žádná další zálohovací řešení. Před zálohováním databáze zakažte všechny ostatní SQL Server zálohy.

> [!NOTE]
> Můžete povolit Azure Backup pro virtuální počítač Azure a také pro SQL Server databáze běžící na virtuálním počítači bez konfliktu.

### <a name="establish-network-connectivity"></a>Navázat připojení k síti

Pro všechny operace vyžaduje SQL Server virtuální počítač připojení ke službě Azure Backup, Azure Storage a Azure Active Directory. Toho lze dosáhnout použitím privátních koncových bodů nebo povolením přístupu k požadovaným veřejným IP adresám nebo plně kvalifikovanému názvu domény. Pokud nepovolíte správné připojení k požadovaným službám Azure, může dojít k selhání operací, jako je zjišťování databáze, konfigurace zálohování, provádění zálohování a obnovování dat.

V následující tabulce jsou uvedeny různé alternativy, které můžete použít při navazování připojení:

| **Možnost**                        | **Výhody**                                               | **Nevýhody**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Soukromé koncové body                 | Povolení zálohování přes privátní IP adresy uvnitř virtuální sítě  <br><br>   Podrobné řízení na straně sítě a trezoru | Má za následek standardní [náklady](https://azure.microsoft.com/pricing/details/private-link/) na soukromý koncový bod |
| Značky služby NSG                  | Jednodušší Správa jako změny rozsahu se sloučí automaticky.   <br><br>   Žádné další náklady | Dá se použít jenom s skupin zabezpečení sítě  <br><br>    Poskytuje přístup k celé službě. |
| Azure Firewall značek plně kvalifikovaného názvu domény          | Jednodušší Správa, protože jsou automaticky spravovány požadované plně kvalifikované názvy domén | Dá se použít jenom s Azure Firewall.                         |
| Povolení přístupu k plně kvalifikovanému názvu domény služby/IP adresám | Žádné další náklady   <br><br>  Funguje se všemi zařízeními a branami zabezpečení sítě. | Pro získání pøístupu k široké škále IP adres nebo plně kvalifikovaných názvů domén může být potřeba.   |
| Použití proxy serveru HTTP                 | Přístup k virtuálním počítačům jediným bodem z Internetu                       | Další náklady na spuštění virtuálního počítače s využitím softwaru proxy         |

Další podrobnosti o použití těchto možností jsou sdílené níže:

#### <a name="private-endpoints"></a>Soukromé koncové body

Soukromé koncové body umožňují zabezpečené připojení ze serverů ve virtuální síti do trezoru Recovery Services. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor. Síťový provoz mezi prostředky uvnitř virtuální sítě a trezoru se přenáší přes virtuální síť a privátní odkaz na páteřní síť Microsoftu. Tím se eliminuje riziko z veřejného Internetu. [Tady](https://docs.microsoft.com/azure/backup/private-endpoints)si můžete přečíst další informace o privátních koncových bodech pro Azure Backup.

#### <a name="nsg-tags"></a>Značky NSG

Pokud používáte skupiny zabezpečení sítě (NSG), pomocí značky služby *AzureBackup* povolte odchozí přístup k Azure Backup. Kromě značky Azure Backup je také potřeba, abyste umožnili připojení k ověřování a přenosu dat vytvořením podobných [pravidel NSG](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) pro *Azure AD* a *Azure Storage*.  Následující kroky popisují proces vytvoření pravidla pro Azure Backup značku:

1. Ve **všech službách**klikněte na **skupiny zabezpečení sítě** a vyberte skupinu zabezpečení sítě.

1. V části **Nastavení**vyberte **odchozí pravidla zabezpečení** .

1. Vyberte **Přidat**. Zadejte všechny požadované podrobnosti pro vytvoření nového pravidla, jak je popsáno v [Nastavení pravidla zabezpečení](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Ujistěte se, že možnost **cíl** je nastavená na *příznak služby* a **cílová značka služby** je nastavená na *AzureBackup*.

1. Kliknutím na **Přidat** uložte nově vytvořené odchozí pravidlo zabezpečení.

Podobně můžete vytvořit NSG odchozí pravidla zabezpečení pro Azure Storage a Azure AD.

#### <a name="azure-firewall-tags"></a>Značky Azure Firewall

Pokud používáte Azure Firewall, vytvořte pravidlo aplikace pomocí [značky plně kvalifikovaného názvu domény Azure firewall](https://docs.microsoft.com/azure/firewall/fqdn-tags) *AzureBackup* . To umožňuje všem odchozím přístupům Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Povolení přístupu k rozsahům IP adres služby

Pokud se rozhodnete, že povolíte IP adresy služby Access, přečtěte si [zde](https://www.microsoft.com/download/confirmation.aspx?id=56519)dostupné ROZSAHy IP adres v souboru JSON. Bude potřeba, abyste povolili přístup k IP adresám, které odpovídají Azure Backup, Azure Storage a Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Povolení přístupu k plně kvalifikovanému názvu domény služby

K povolení přístupu k požadovaným službám z vašich serverů můžete použít taky tyto plně kvalifikované názvy domény:

| Služba    | Názvy domén, které se mají použít                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Služba Azure AD      | V souladu s [tímto článkem](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) povolte přístup k plně kvalifikovanému názvu domény v oddílech 56 a 59. |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Použití proxy server HTTP ke směrování provozu

Při zálohování databáze SQL Server na virtuálním počítači Azure používá rozšíření zálohování na virtuálním počítači rozhraní API HTTPS k posílání příkazů pro správu do Azure Backup a dat do Azure Storage. Rozšíření zálohování používá pro ověřování taky službu Azure AD. Přesměrujte provoz rozšíření zálohování pro tyto tři služby prostřednictvím proxy serveru HTTP. Pro povolení přístupu k požadovaným službám použijte seznam IP adres a plně kvalifikovaných názvů domén uvedených výše. Ověřené proxy servery se nepodporují.

### <a name="database-naming-guidelines-for-azure-backup"></a>Pokyny pro pojmenovávání databází pro Azure Backup

Vyhněte se použití následujících prvků v názvech databází:

* Koncové a úvodní mezery
* Koncové znaky vykřičníku (!)
* Pravá hranatá závorka (])
* Středník;
* Lomítkem (/)

Aliasing je k dispozici pro nepodporované znaky, ale doporučujeme je vyhnout. Další informace najdete v tématu [Vysvětlení datového modelu služby Table Storage](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

>[!NOTE]
>Operace **Konfigurace ochrany** pro databáze se speciálními znaky, jako je "+" nebo "&" v názvu není podporována. Můžete buď změnit název databáze, nebo povolit **automatickou ochranu**, která může úspěšně chránit tyto databáze.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjišťování databází SQL Serveru

Jak zjišťovat databáze běžící na virtuálním počítači:

1. V [Azure Portal](https://portal.azure.com)otevřete Recovery Services trezor, který používáte k zálohování databáze.

2. Na řídicím panelu **trezoru Recovery Services** vyberte **zálohování**.

   ![Výběrem zálohovat otevřete nabídku cíl zálohování.](./media/backup-azure-sql-database/open-backup-menu.png)

3. V nastavení **cíl zálohování**nastavte, **kde je vaše úloha spuštěná?** do **Azure**.

4. V **Možnosti co chcete zálohovat**vyberte **SQL Server na virtuálním počítači Azure**.

    ![Vyberte SQL Server na virtuálním počítači Azure pro zálohování.](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. V části **cíl zálohování**ve  >  **virtuálním počítači Najděte databáze**, vyberte **Spustit zjišťování** a vyhledejte nechráněné virtuální počítače v předplatném. Toto hledání může chvíli trvat, v závislosti na počtu nechráněných virtuálních počítačů v předplatném.

   * Nechráněné virtuální počítače by se měly zobrazit v seznamu po zjištění, které jsou uvedené podle názvu a skupiny prostředků.
   * Pokud se virtuální počítač nezobrazuje podle očekávání, podívejte se, jestli už je zálohovaný v trezoru.
   * Stejný název může mít víc virtuálních počítačů, ale patří do různých skupin prostředků.

     ![Při hledání databáze na virtuálních počítačích čeká na zálohování.](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. V seznamu virtuálních počítačů vyberte virtuální počítač, na kterém běží SQL Server databáze > **zjistit databáze**.

7. Sledujte zjišťování databáze v **oznámeních**. Doba potřebná pro tuto akci závisí na počtu databází virtuálních počítačů. Když jsou vybrané databáze zjištěny, zobrazí se zpráva o úspěchu.

    ![Zpráva o úspěšném nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup zjistí všechny databáze SQL Server na virtuálním počítači. Během zjišťování dochází na pozadí k následujícím prvkům:

    * Azure Backup registruje virtuální počítač s trezorem pro zálohování úloh. Všechny databáze na zaregistrovaném virtuálním počítači se dají zálohovat jenom do tohoto trezoru.
    * Azure Backup nainstaluje na virtuální počítač rozšíření AzureBackupWindowsWorkload. V databázi SQL není nainstalován žádný agent.
    * Azure Backup vytvoří účet služby NT Service\AzureWLBackupPluginSvc ve virtuálním počítači.
      * Všechny operace zálohování a obnovení používají účet služby.
      * NT Service\AzureWLBackupPluginSvc vyžaduje oprávnění správce systému SQL. Všechny SQL Server virtuálních počítačů vytvořených na webu Marketplace přináší SqlIaaSExtension nainstalovanou. Rozšíření AzureBackupWindowsWorkload používá k automatickému získání požadovaných oprávnění SQLIaaSExtension.
    * Pokud jste virtuální počítač nevytvořili z webu Marketplace nebo pokud jste na SQL 2008 a 2008 R2, virtuální počítač nemusí mít nainstalovaný SqlIaaSExtension a operace zjišťování se nezdařila s chybovou zprávou UserErrorSQLNoSysAdminMembership. Pokud chcete tento problém vyřešit, postupujte podle pokynů v části [Nastavení oprávnění virtuálních počítačů](backup-azure-sql-database.md#set-vm-permissions).

        ![Vyberte virtuální počítač a databázi.](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

1. V kroku **zálohování cíl**  >  **2: Konfigurace zálohování**vyberte **Konfigurovat zálohu**.

   ![Vyberte konfigurovat zálohu.](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. V části **Vybrat položky, které se mají zálohovat**jsou všechny registrované skupiny dostupnosti a samostatné SQL Server instance. Vyberte šipku vlevo od řádku a rozbalte seznam všech nechráněných databází v této instanci nebo skupině dostupnosti Always On.  

    ![Zobrazení všech instancí SQL Server se samostatnými databázemi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Zvolte všechny databáze, které chcete chránit, a pak vyberte **OK**.

   ![Ochrana databáze](./media/backup-azure-sql-database/select-database-to-protect.png)

   Pro optimalizaci zátěže zálohování Azure Backup nastaví maximální počet databází v jedné úloze zálohování na 50.

     * Chcete-li chránit více než 50 databází, nakonfigurujte více záloh.
     * Pokud chcete [Povolit](#enable-auto-protection) celou instanci nebo skupinu dostupnosti Always On, vyberte v rozevíracím seznamu automaticky **chránit** možnost **zapnuto**a pak vyberte **OK**.

    > [!NOTE]
    > Funkce [automatické ochrany](#enable-auto-protection) umožňuje nejen ochranu na všech existujících databázích najednou, ale také automaticky chrání všechny nové databáze přidané do této instance nebo do skupiny dostupnosti.  

4. Výběrem **OK** otevřete **zásady zálohování**.

    ![Povolení automatické ochrany pro skupinu dostupnosti Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. V **zásadách zálohování**zvolte zásadu a pak vyberte **OK**.

   * Jako HourlyLogBackup vyberte výchozí zásady.
   * Vyberte existující zásadu zálohování, která byla dříve vytvořena pro SQL.
   * Definujte novou zásadu na základě bodu RPO a rozsahu uchování.

     ![Vybrat zásady zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

6. V **zálohování**vyberte **Povolit zálohování**.

    ![Povolit zvolenou zásadu zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

7. Sledujte průběh konfigurace v oblasti **oznámení** na portálu.

    ![Oznamovací oblast](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy se zálohují zálohy a jak dlouho se uchovávají.

* Zásada se vytvoří na úrovni trezoru.
* Několik trezorů může používat stejné zásady zálohování, ale je nutné použít zásady zálohování pro každý trezor.
* Při vytváření zásad zálohování je výchozí denní úplné zálohování.
* Rozdílové zálohování můžete přidat, ale jenom v případě, že nakonfigurujete úplné zálohování na týden.
* Seznamte se [s různými typy zásad zálohování](backup-architecture.md#sql-server-backup-types).

Vytvoření zásady zálohování:

1. V trezoru vyberte **zásady zálohování**  >  **Přidat**.
2. V části **Přidat**vyberte **SQL Server na virtuálním počítači Azure** a definujte typ zásad.

   ![Vyberte typ zásad pro nové zásady zálohování.](./media/backup-azure-sql-database/policy-type-details.png)

3. Do pole **název zásady**zadejte název nové zásady.
4. V **zásadách úplného zálohování**vyberte **četnost zálohování**. Vyberte možnost **denně** nebo **týdně**.

   * V **denní**době vyberte hodiny a časové pásmo, kdy se úloha zálohování spustí.
   * V poli **týdně**vyberte den v týdnu, hodinu a časové pásmo při zahájení úlohy zálohování.
   * Spusťte úplnou zálohu, protože nemůžete vypnout možnost **úplného zálohování** .
   * Pro zobrazení zásady vyberte **úplné zálohování** .
   * Pro každodenní úplné zálohování nemůžete vytvořit rozdílové zálohy.

     ![Nová pole zásad zálohování](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Ve výchozím nastavení je v **rozsahu uchování**vybraná možnost všechny možnosti. Vymažte všechny limity rozsahu uchovávání, které nechcete, a pak nastavte intervaly, které se mají použít.

    * Minimální doba uchování pro jakýkoli typ zálohy (úplný, rozdíl a protokol) je sedm dní.
    * Body obnovení jsou označeny pro uchování na základě jejich rozsahu uchovávání. Pokud například vyberete denní úplnou zálohu, spustí se každý den jenom jedno úplné zálohování.
    * Záloha pro určitý den je označena a zachována v závislosti na týdenním rozsahu uchování a nastavení týdenního uchovávání.
    * Podobným způsobem se chovají měsíční a roční rozsah uchování.

       ![Nastavení intervalu rozsahu uchování](./media/backup-azure-sql-database/retention-range-interval.png)

6. V nabídce **zásady úplného zálohování** vyberte **OK** a přijměte nastavení.
7. Chcete-li přidat zásady rozdílového zálohování, vyberte **rozdílové zálohování**.

   ![Nastavení intervalu rozsahu uchování ](./media/backup-azure-sql-database/retention-range-interval.png)
    ![ otevřete nabídku zásad rozdílového zálohování.](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. V části **rozdílová zásada zálohování**vyberte **Povolit** a otevřete tak ovládací prvky četnost a uchování.

    * Můžete aktivovat jenom jednu rozdílovou zálohu za den.
    * Rozdílové zálohy je možné uchovávat maximálně po dobu 180 dnů. Pro delší dobu uchování použijte úplné zálohování.

9. Výběrem **OK** zásadu uložíte a vrátíte se do nabídky hlavní **zásady zálohování** .

10. Chcete-li přidat zásady zálohování transakčního protokolu, vyberte možnost **zálohování protokolu**.
11. V části **zálohování protokolu**vyberte **Povolit**a pak nastavte četnost a ovládací prvky uchování. Zálohy protokolů se můžou provádět až po dobu 15 minut a můžou se uchovávat až 35 dní.
12. Výběrem **OK** zásadu uložíte a vrátíte se do nabídky hlavní **zásady zálohování** .

    ![Upravit zásady zálohování protokolu](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. V nabídce **zásady zálohování** vyberte, jestli se má povolit **Komprese SQL Backup** nebo ne. Tato možnost je ve výchozím nastavení zakázána. Pokud je povoleno, SQL Server odešle komprimovaný Stream zálohování do infrastruktury virtuálních počítačů (VDI).  Upozorňujeme, že Azure Backup přepisuje výchozí hodnoty na úrovni instance pomocí klauzule COMPRESSION/NO_COMPRESSION v závislosti na hodnotě tohoto ovládacího prvku.

14. Po dokončení úprav zásad zálohování vyberte **OK**.

> [!NOTE]
> Každá záloha protokolu je zřetězena k předchozí úplné záloze, aby mohla tvořit řetěz obnovení. Tato úplná záloha se zachová, dokud neuplyne doba uchovávání poslední zálohy protokolu. To může znamenat, že úplná záloha se uchovává po dobu dalších let, aby se zajistilo, že se všechny protokoly mají obnovit. Předpokládejme, že uživatel má týdenní úplnou zálohu, denní rozdílovou a 2 hodinový protokol. Všechny z nich se uchovávají po dobu 30 dnů. Ale každý týden v plném rozsahu může být skutečně vyčištěný nebo odstraněný až po dokončení dalších úplných záloh, tj. po 30 až 7 dnech. Řekněme, že týdenní úplné zálohování probíhá na 16. listopadu. Podle zásad uchovávání informací by se měla uchovávat až do prosince 16. Poslední záloha protokolu pro tuto úplnou zálohu proběhne před dalším naplánovaným úplným 22. listopadu. Dokud nebude tento protokol k dispozici do prosince 22, nelze odstranit jeho plný 16. To znamená, že do prosince 22 se zachovají až do 16. listopadu.

## <a name="enable-auto-protection"></a>Povolit automatickou ochranu  

Automatickou ochranu můžete povolit, pokud chcete automaticky zálohovat všechny stávající a budoucí databáze na samostatnou instanci SQL Server nebo do skupiny dostupnosti Always On.

* Neexistuje žádné omezení počtu databází, které můžete v jednom okamžiku vybrat pro automatickou ochranu.
* Nemůžete selektivně chránit nebo vyloučit databáze z ochrany v instanci v době, kdy jste povolili automatickou ochranu.
* Pokud už vaše instance obsahuje některé chráněné databáze, zůstane chráněná v příslušných zásadách i po zapnutí automatické ochrany. Všechny nechráněné databáze přidané později budou mít jenom jednu zásadu, kterou definujete v době povolení automatické ochrany, která je uvedená v části **Konfigurace zálohování**. Zásadu přidruženou k databázi s automatickou ochranou ale můžete změnit později.  

Povolení automatické ochrany:

  1. V části **položky k zálohování**vyberte instanci, pro kterou chcete povolit automatickou ochranu.
  2. Vyberte rozevírací seznam v části **AutoProtect**, zvolte **zapnuto**a pak vyberte **OK**.

      ![Povolení automatické ochrany ve skupině dostupnosti](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. Zálohování je nakonfigurované pro všechny databáze dohromady a je možné je sledovat v **úlohách zálohování**.

Pokud potřebujete vypnout automatickou ochranu, vyberte název instance v části **Konfigurace zálohování**a pak vyberte **Zakázat automatické ochrany** pro instanci. Všechny databáze budou nadále zálohovány, ale budoucí databáze nebudou automaticky chráněny.

![Zakázat automatickou ochranu u této instance](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Další kroky

Naučte se:

* [Obnovení zálohovaných SQL Server databází](restore-sql-database-azure-vm.md)
* [Správa zálohovaných SQL Server databází](manage-monitor-sql-database-backup.md)
