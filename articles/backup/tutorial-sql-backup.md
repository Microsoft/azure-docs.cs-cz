---
title: Kurz – Zálohování databází SQL Serveru do Azure
description: V tomto kurzu se dozvíte, jak zálohovat databázi SQL Serveru na virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services.
ms.topic: tutorial
ms.date: 06/18/2019
ms.openlocfilehash: 17a8472da2595c08cb198baaf853faf110a619fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88612448"
---
# <a name="back-up-a-sql-server-database-in-an-azure-vm"></a>Zálohování databáze SQL Serveru na virtuálním počítači Azure

V tomto kurzu se dozvíte, jak zálohovat databázi SQL Serveru na virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services. V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Vytvoření a konfigurace trezoru
> * Zjišťování databází a nastavení zálohování
> * Nastavení automatické ochrany databází
> * Spuštění zálohování na vyžádání

## <a name="prerequisites"></a>Požadavky

Před zálohováním databáze SQL Serveru zkontrolujte následující podmínky:

1. Identifikujte nebo [vytvořte](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) trezor služby Recovery Services ve stejné oblasti nebo stejném národním prostředí jako virtuální počítač, který je hostitelem instance SQL Serveru.
2. [Zkontrolujte oprávnění virtuálního počítače](backup-azure-sql-database.md#set-vm-permissions) potřebná k zálohování databází SQL.
3. Ověřte, že je virtuální počítač [připojený k síti](backup-sql-server-database-azure-vms.md#establish-network-connectivity).
4. Zkontrolujte, že jsou databáze SQL Serveru pojmenované v souladu s [pokyny pro vytváření názvů](#verify-database-naming-guidelines-for-azure-backup) pro službu Azure Backup.
5. Ověřte, že pro databázi nemáte povolené žádné jiné řešení zálohování. Před nastavením tohoto scénáře zakažte jakékoli jiné zálohování SQL Serveru. Službu Azure Backup můžete povolit pro virtuální počítač Azure i pro databázi SQL Serveru na tomto virtuálním počítači, aniž by došlo ke konfliktu.

### <a name="establish-network-connectivity"></a>Navázání připojení k síti

Virtuální počítač s SQL Serverem pro všechny operace musí mít připojení k veřejným IP adresám Azure. Bez připojení k těmto veřejným IP adresám operace virtuálního počítače (zjišťování databází, konfigurace zálohování, plánování zálohování, obnovení z bodů obnovení atd.) selžou. Navažte připojení s využitím některé z těchto možností:

* **Povolení rozsahů IP adres datacenter Azure:** Povolte ve stahování [rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653). Pokud chcete získat přístup ke skupině zabezpečení sítě (NSG), použijte rutinu **Set-AzureNetworkSecurityRule**.
* **Nasazení proxy serveru HTTP pro směrování provozu:** Pokud zálohujete databázi SQL Serveru na virtuálním počítači Azure, rozšíření zálohování virtuálního počítače k odesílání příkazů pro správu do služby Azure Backup a dat do služby Azure Storage používá rozhraní HTTPS API. Rozšíření zálohování využívá také Azure Active Directory (Azure AD) k ověřování. Přesměrujte provoz rozšíření zálohování pro tyto tři služby přes proxy server HTTP. Rozšíření jsou jediné komponenty, které mají nakonfigurovaný přístup k veřejnému internetu.

Každá možnost má své výhody a nevýhody.

**Možnost** | **Výhody** | **Nevýhody**
--- | --- | ---
Povolení rozsahů IP adres | Bez dalších nákladů | Správa je složitá, protože rozsahy IP adres se v průběhu času mění. <br/><br/> Poskytuje přístup k celé platformě Azure, ne jen ke službě Azure Storage.
Použití proxy serveru HTTP   | Na proxy serveru je možné nastavit podrobné řízení adres URL úložiště. <br/><br/> Jediný internetový přístupový bod k virtuálním počítačům. <br/><br/> Nepodléhá změnám IP adres Azure. | Další náklady na provoz virtuálního počítače se softwarem proxy serveru

### <a name="set-vm-permissions"></a>Nastavení oprávnění virtuálního počítače

Azure Backup při konfiguraci zálohování databáze SQL Serveru provede několik věcí:

* Přidá rozšíření **AzureBackupWindowsWorkload**.
* Pro účely zjišťování databází na virtuálním počítači Azure Backup vytvoří účet **NT SERVICE\AzureWLBackupPluginSvc**. Tento účet se používá k zálohování a obnovení a vyžaduje oprávnění správce systému SQL.
* Azure Backup využívá účet **NT AUTHORITY\SYSTEM** ke zjišťování a dotazování databází, takže tento účet musí mít možnost veřejného přihlášení k SQL.

Pokud jste virtuální počítač s SQL Serverem nevytvořili z Azure Marketplace, může se zobrazit chyba **UserErrorSQLNoSysadminMembership**. Pokud k tomu dojde, [postupujte podle těchto pokynů](backup-azure-sql-database.md#set-vm-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Ověření dodržování pokynů pro vytváření názvů databází pro službu Azure Backup

V názvech databází nepoužívejte následující:

* Mezery na začátku nebo na konci
* Vykřičník (!) na konci
* Pravá hranatá závorka (])
* Řetězec „F:\“ na začátku

Pro nepodporované znaky v tabulkách Azure jsou k dispozici aliasy, ale doporučujeme je nepoužívat. [Přečtěte si další informace](/rest/api/storageservices/understanding-the-table-service-data-model).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Zjišťování databází SQL Serveru

Zjistěte databáze na virtuálním počítači.

1. Na webu [Azure Portal](https://portal.azure.com) otevřete trezor služby Recovery Services, který používáte k zálohování databáze.

2. Na řídicím panelu **trezoru služby Recovery Services** vyberte **Zálohování**.

   ![Výběr možnosti Zálohování a otevření nabídky Cíl zálohování](./media/backup-azure-sql-database/open-backup-menu.png)

3. V části **Cíl zálohování** nastavte možnost **Kde je spuštěná vaše úloha?** na hodnotu **Azure** (výchozí hodnota).

4. V části **Co chcete zálohovat?** vyberte **SQL Server na virtuálním počítači Azure**.

    ![Výběr SQL Serveru na virtuálním počítači Azure pro zálohování](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. V části **Cíl zálohování** > **Zjistit databáze na virtuálních počítačích** vyberte **Spustit zjišťování** a vyhledejte v předplatném nechráněné virtuální počítače. V závislosti na počtu nechráněných virtuálních počítačů v předplatném to může chvíli trvat.

   * Po zjišťování by se v seznamu měly zobrazit nechráněné virtuální počítače uvedené podle názvu a skupiny prostředků.
   * Pokud se virtuální počítač navzdory očekávání nezobrazí, zkontrolujte, jestli již není v nějakém trezoru zálohovaný.
   * Několik virtuálních počítačů může mít stejný název, ale v takovém případě patří do různých skupin prostředků.

     ![Čekající zálohování při vyhledávání databází na virtuálních počítačích](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. V seznamu virtuálních počítačů vyberte virtuální počítač s databází SQL Serveru a vyberte **Zjistit databáze**.

7. Zjišťování databází můžete sledovat v oblasti **Oznámení**. Dokončení této úlohy může v závislosti na počtu databází na virtuálním počítači chvíli trvat. Po zjištění vybraných databází se zobrazí zpráva o úspěchu.

    ![Zpráva o úspěchu nasazení](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup zjistí všechny databáze SQL Serveru na virtuálním počítači. Během zjišťování se na pozadí děje následující:

    * Azure Backup zaregistruje virtuální počítač v trezoru pro zálohování úloh. Všechny databáze na zaregistrovaném virtuálním počítači je možné zálohovat pouze do tohoto trezoru.
    * Azure Backup na virtuální počítač nainstaluje rozšíření **AzureBackupWindowsWorkload**. Pro databázi SQL se žádný agent nenainstaluje.
    * Azure Backup na virtuálním počítači vytvoří účet služby **NT Service\AzureWLBackupPluginSvc**.
      * Tento účet služby používají všechny operace zálohování a obnovení.
      * Účet služby **NT Service\AzureWLBackupPluginSvc** musí mít oprávnění správce systému SQL. Na všech virtuálních počítačích s SQL Serverem vytvořených na Azure Marketplace je nainstalované rozšíření **SqlIaaSExtension**. Rozšíření **AzureBackupWindowsWorkload** s využitím rozšíření **SQLIaaSExtension** automaticky získá požadovaná oprávnění.
    * Pokud jste virtuální počítač nevytvořili z marketplace, rozšíření **SqlIaaSExtension** na něm není nainstalované a operace zjišťování selže s chybovou zprávou **UserErrorSQLNoSysAdminMembership**. Pokud chcete tento problém vyřešit, postupujte podle těchto [pokynů](backup-azure-sql-database.md#set-vm-permissions).

        ![Výběr virtuálního počítače a databáze](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

Následujícím způsobem nakonfigurujte zálohování:

1. V části **Cíl zálohování** vyberte **Nakonfigurovat zálohování**.

   ![Výběr možnost Nakonfigurovat zálohování](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Vyberte **Nakonfigurovat zálohování** a zobrazí se podokno **Vybrat položky, které se mají zálohovat**. Zobrazí se seznam všech zaregistrovaných skupin dostupnosti a samostatných SQL Serverů. Kliknutím na dvojitou šipku v levé části řádku zobrazíte všechny nechráněné databáze v dané instanci nebo skupině dostupnosti AlwaysOn.  

    ![Zobrazení všech instancí SQL Serveru se samostatnými databázemi](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Vyberte všechny databáze, které chcete chránit, a klikněte na **OK**.

   ![Ochrana databází](./media/backup-azure-sql-database/select-database-to-protect.png)

   Ve službě Azure Backup je za účelem optimalizace zatížení zálohování nastavený maximální počet databází v jedné úloze zálohování na 50.

     * Případně můžete povolit automatickou ochranu celé instance nebo skupiny dostupnosti AlwaysOn, a to výběrem možnosti **ON** (Zapnuto) v odpovídající rozevírací nabídce ve sloupci **AUTOPROTECT** (Automatická ochrana). Funkce automatické ochrany vedle toho, že umožňuje zajistit ochranu všech stávajících databází najednou, také automaticky chrání všechny nové databáze, které se do dané instance nebo skupiny dostupnosti přidají v budoucnu.  

4. Výběrem možnosti **OK** otevřete podokno **Zásady zálohování**.

    ![Povolení automatické ochrany skupiny dostupnosti AlwaysOn](./media/backup-azure-sql-database/enable-auto-protection.png)

5. V části **Vybrat zásady zálohování** vyberte zásady a pak vyberte **OK**.

   * Vyberte výchozí zásady: HourlyLogBackup.
   * Zvolte existující zásady zálohování vytvořené dříve pro SQL.
   * Definujte nové zásady na základě vašeho cíle bodu obnovení (RPO) a rozsahu uchovávání.

     ![Výběr zásad zálohování](./media/backup-azure-sql-database/select-backup-policy.png)

6. V nabídce **Zálohování** vyberte **Povolit zálohování**.

    ![Povolení zvolených zásad zálohování](./media/backup-azure-sql-database/enable-backup-button.png)

7. Průběh konfigurace můžete sledovat na portálu v oblasti **Oznámení**.

    ![Oznamovací oblast](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Vytvoření zásad zálohování

Zásady zálohování definují, kdy se zálohy vytvářejí a jak dlouho se uchovávají.

* Zásady se vytvářejí na úrovni trezoru.
* Stejné zásady zálohování může používat více trezorů, ale je potřeba je pro každý trezor použít zvlášť.
* Při vytváření zásad zálohování je výchozím nastavením denní úplné zálohování.
* Můžete přidat rozdílové zálohování, ale pouze pokud nakonfigurujete týdenní úplné zálohování.
* [Přečtěte si](backup-architecture.md#sql-server-backup-types) o různých typech zásad zálohování.

Vytvoření zásad zálohování:

1. V trezoru vyberte **Zásady zálohování** > **Přidat**.
2. V nabídce **Přidat** jako typ zásad vyberte **SQL Server na virtuálním počítači Azure**.

   ![Výběr typu nových zásad zálohování](./media/backup-azure-sql-database/policy-type-details.png)

3. Do pole **Název zásad** zadejte název nových zásad.
4. V části **Zásady úplného zálohování** vyberte **frekvenci zálohování** – zvolte **Denní** nebo **Týdenní**.

   * Pokud zvolíte možnost **Denní**, vyberte hodinu a časové pásmo zahájení úlohy zálohování.
   * Možnost **úplného zálohování** nejde vypnout, proto musíte provádět úplné zálohování.
   * Zásady zobrazíte výběrem možnosti **Úplné zálohování**.
   * Pro denní úplné zálohování nemůžete vytvořit rozdílové zálohování.
   * Pokud zvolíte možnost **Týdenní**, vyberte den v týdnu, hodinu a časové pásmo zahájení úlohy zálohování.

     ![Pole nových zásad zálohování](./media/backup-azure-sql-database/full-backup-policy.png)  

5. V části **Rozsah uchovávání** jsou ve výchozím nastavení vybrané všechny možnosti. Zrušte zaškrtnutí u všech limitů rozsahu uchovávání, které nechcete použít, a nastavte intervaly, které chcete použít.

    * Minimální rozsah uchovávání pro všechny typy zálohování (úplné zálohování, rozdílové zálohování, zálohování protokolů) je sedm dnů.
    * Body obnovení se označují pro uchovávání na základě jejich rozsahu uchovávání. Například pokud vyberete denní úplné zálohování, každý den se aktivuje pouze jedno úplné zálohování.
    * Zálohy pro konkrétní dny se označují a uchovávají na základě týdenního rozsahu uchovávání a nastavení týdenního uchovávání.
    * Měsíční a roční rozsahy uchovávání se chovají podobným způsobem.

   ![Nastavení intervalu rozsahu uchovávání](./media/backup-azure-sql-database/retention-range-interval.png)

6. V nabídce **Zásady úplného zálohování** výběrem možnosti **OK** přijměte nastavení.
7. Pokud chcete přidat zásady rozdílového zálohování, vyberte **Rozdílové zálohování**.

   ![Nastavení intervalu rozsahu uchovávání](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Otevření nabídky zásad rozdílového zálohování](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. V části **Zásady rozdílového zálohování** výběrem možnosti **Povolit** otevřete ovládací prvky frekvence a uchovávání.

    * Maximálně můžete aktivovat jedno rozdílové zálohování za den.
    * Rozdílové zálohy je možné uchovávat maximálně po dobu 180 dnů. Pokud potřebujete delší dobu uchovávání, musíte použít úplné zálohování.

9. Výběrem možnosti **OK** zásady uložte a vraťte se do hlavní nabídky **Zásady zálohování**.

10. Pokud chcete přidat zásady zálohování transakčních protokolů, vyberte **Zálohování protokolů**.
11. V části **Zálohování protokolů** vyberte **Povolit** a pak nastavte ovládací prvky frekvence a uchovávání. Zálohování protokolů může probíhat až každých 15 minut a zálohy protokolů je možné uchovávat po dobu až 35 dnů.
12. Výběrem možnosti **OK** zásady uložte a vraťte se do hlavní nabídky **Zásady zálohování**.

    ![Úprava zásad zálohování protokolů](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. V nabídce **Zásady zálohování** zvolte, jestli se má povolit **Komprese záloh SQL**.
    * Komprese je ve výchozím nastavení zakázaná.
    * Na straně back-endu služba Azure Backup používá nativní kompresi záloh SQL.

14. Po dokončení úprav zásad zálohování vyberte **OK**.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

1. Ve vašem trezoru služby Recovery Services zvolte Zálohované položky.
2. Vyberte SQL na virtuálním počítači Azure.
3. Klikněte pravým tlačítkem na databázi a zvolte Zálohovat.
4. Zvolte typ zálohování (úplné zálohování, rozdílové zálohování, zálohování protokolů nebo úplné zálohování jenom kopírováním) a povolte nebo zakažte kompresi.
5. Výběrem možnosti OK zahajte zálohování.
6. Pokud chcete úlohu zálohování sledovat, přejděte do vašeho trezoru služby Recovery Services a zvolte Úlohy zálohování.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
>
> * Vytvoření a konfigurace trezoru
> * Zjišťování databází a nastavení zálohování
> * Nastavení automatické ochrany databází
> * Spuštění zálohování na vyžádání

Přejděte k dalšímu kurzu, kde obnovíte virtuální počítač Azure z disku.

> [!div class="nextstepaction"]
> [Obnovení databází SQL Serveru na virtuálních počítačích Azure](./restore-sql-database-azure-vm.md)
