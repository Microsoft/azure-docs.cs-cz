---
title: Kurz – zálohování SAP HANA databází na virtuálních počítačích Azure
description: V tomto kurzu se naučíte zálohovat SAP HANA databáze běžící na virtuálním počítači Azure do trezoru služby Azure Backup Recovery Services.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 7bb836e92ce35869996725cb63f2d3808b570fa1
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684062"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Kurz: zálohování SAP HANA databází ve virtuálním počítači Azure

V tomto kurzu se dozvíte, jak zálohovat SAP HANA databáze běžící na virtuálních počítačích Azure do trezoru služby Azure Backup Recovery Services. V tomto článku se dozvíte, jak:

> [!div class="checklist"]
>
> * Vytvoření a konfigurace trezoru
> * Vyhledat databáze
> * Konfigurace zálohování

[Tady](sap-hana-backup-support-matrix.md#scenario-support) jsou všechny scénáře, které momentálně podporujeme.

>[!NOTE]
>Od 1. srpna 2020 je SAP HANA zálohování pro RHEL (7,4, 7,6, 7,7 & 8,1) všeobecně dostupné.

## <a name="prerequisites"></a>Požadavky

Před konfigurací zálohování se ujistěte, že jste provedli následující kroky:

* Identifikujte nebo vytvořte [Recovery Services trezor](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) ve stejné oblasti a předplatném jako virtuální počítač s SAP HANA.
* Umožněte připojení z virtuálního počítače k Internetu, aby bylo možné získat přístup k Azure, jak je popsáno níže v postupu [Nastavení síťového připojení](#set-up-network-connectivity) .
* Zajistěte, aby celková délka názvu virtuálního počítače SAP HANA serveru a názvu skupiny prostředků nepřesáhla 84 znaků pro Azure Resource Manager (ARM_ virtuální počítače (a 77 znaků pro klasické virtuální počítače). Toto omezení je způsobeno tím, že některé znaky jsou vyhrazené službou.
* Klíč by měl existovat v **hdbuserstore** , který splňuje následující kritéria:
  * Měl by se nacházet ve výchozím **hdbuserstore**. Výchozí je účet, `<sid>adm` pod kterým je nainstalovaná SAP HANA.
  * V případě MDC by měl klíč ukazovat na port SQL **názvový server**. V případě SDC by měl odkazovat na port SQL **INDEXSERVER**
  * Měl by obsahovat přihlašovací údaje k přidávání a odstraňování uživatelů.
  * Upozorňujeme, že tento klíč se dá po úspěšném spuštění předzálohovacího skriptu odstranit.
* Spusťte skript konfigurace zálohování SAP HANA (předregistrující skript) ve virtuálním počítači, kde je nainstalovaná verze HANA, jako uživatel root. [Tento skript](https://aka.ms/scriptforpermsonhana) NAČTE systém Hana, který je připravený k zálohování. Další informace o skriptu před registrací najdete v části [co je to skript](#what-the-pre-registration-script-does) pro předběžnou registraci.
* Pokud vaše instalace HANA používá soukromé koncové body, spusťte [předregistrační skript](https://aka.ms/scriptforpermsonhana) s parametrem *-sn* nebo *--Skip-Network-Checks* .

>[!NOTE]
>Skript předregistrování nainstaluje SAP HANA **unixODBC234** pro úlohy, které běží na RHEL (7,4, 7,6 a 7,7), a **unixODBC** pro RHEL 8,1. [Tento balíček je umístěný v RHEL for SAP Hana (pro úložiště RPM (RHEL 7 Server) Update Services for SAP Solutions ()](https://access.redhat.com/solutions/5094721).  Pro bitovou kopii Azure Marketplace RHEL úložiště **rhui-RHEL-SAP-HANA-for-RHEL-7-Server-rhui-e4s-RPM**.

## <a name="set-up-network-connectivity"></a>Nastavení připojení k síti

Pro všechny operace vyžaduje databáze SAP HANA běžící na virtuálním počítači Azure připojení ke službě Azure Backup, Azure Storage a Azure Active Directory. Toho lze dosáhnout použitím privátních koncových bodů nebo povolením přístupu k požadovaným veřejným IP adresám nebo plně kvalifikovanému názvu domény. Pokud nepovolíte správné připojení k požadovaným službám Azure, může dojít k selhání operací, jako je zjišťování databáze, konfigurace zálohování, provádění zálohování a obnovování dat.

V následující tabulce jsou uvedeny různé alternativy, které můžete použít při navazování připojení:

| **Možnost**                        | **Výhody**                                               | **Nevýhody**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Soukromé koncové body                 | Povolení zálohování přes privátní IP adresy uvnitř virtuální sítě  <br><br>   Podrobné řízení na straně sítě a trezoru | Má za následek standardní [náklady](https://azure.microsoft.com/pricing/details/private-link/) na soukromý koncový bod |
| Značky služby NSG                  | Jednodušší Správa jako změny rozsahu se sloučí automaticky.   <br><br>   Žádné další náklady | Dá se použít jenom s skupin zabezpečení sítě  <br><br>    Poskytuje přístup k celé službě. |
| Azure Firewall značek plně kvalifikovaného názvu domény          | Jednodušší Správa, protože jsou automaticky spravovány požadované plně kvalifikované názvy domén | Dá se použít jenom s Azure Firewall.                         |
| Povolení přístupu k plně kvalifikovanému názvu domény služby/IP adresám | Žádné další náklady   <br><br>  Funguje se všemi zařízeními a branami zabezpečení sítě. | Pro získání pøístupu k široké škále IP adres nebo plně kvalifikovaných názvů domén může být potřeba.   |
| Použití proxy serveru HTTP                 | Přístup k virtuálním počítačům jediným bodem z Internetu                       | Další náklady na spuštění virtuálního počítače s využitím softwaru proxy         |

Další podrobnosti o použití těchto možností jsou sdílené níže:

### <a name="private-endpoints"></a>Soukromé koncové body

Soukromé koncové body umožňují zabezpečené připojení ze serverů ve virtuální síti do trezoru Recovery Services. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor. Síťový provoz mezi prostředky uvnitř virtuální sítě a trezoru se přenáší přes virtuální síť a privátní odkaz na páteřní síť Microsoftu. Tím se eliminuje riziko z veřejného Internetu. [Tady](./private-endpoints.md)si můžete přečíst další informace o privátních koncových bodech pro Azure Backup.

### <a name="nsg-tags"></a>Značky NSG

Pokud používáte skupiny zabezpečení sítě (NSG), pomocí značky služby *AzureBackup* povolte odchozí přístup k Azure Backup. Kromě značky Azure Backup musíte také umožňovat připojení k ověřování a přenosu dat vytvořením podobných [pravidel NSG](../virtual-network/network-security-groups-overview.md#service-tags) pro Azure AD (*azureactivedirectory selhala*) a Azure Storage (*úložiště*). Následující kroky popisují proces vytvoření pravidla pro Azure Backup značku:

1. Ve **všech službách** klikněte na **skupiny zabezpečení sítě** a vyberte skupinu zabezpečení sítě.

1. V části **Nastavení** vyberte **odchozí pravidla zabezpečení** .

1. Vyberte možnost **Přidat**. Zadejte všechny požadované podrobnosti pro vytvoření nového pravidla, jak je popsáno v [Nastavení pravidla zabezpečení](../virtual-network/manage-network-security-group.md#security-rule-settings). Ujistěte se, že možnost **cíl** je nastavená na *příznak služby* a **cílová značka služby** je nastavená na *AzureBackup*.

1. Vyberte **Přidat**  a uložte nově vytvořené odchozí pravidlo zabezpečení.

Podobně můžete vytvořit [NSG odchozí pravidla zabezpečení](../virtual-network/network-security-groups-overview.md#service-tags) pro Azure Storage a Azure AD. Další informace o značkách služby najdete v [tomto článku](../virtual-network/service-tags-overview.md).

### <a name="azure-firewall-tags"></a>Značky Azure Firewall

Pokud používáte Azure Firewall, vytvořte pravidlo aplikace pomocí [značky plně kvalifikovaného názvu domény Azure firewall](../firewall/fqdn-tags.md) *AzureBackup* . To umožňuje všem odchozím přístupům Azure Backup.

### <a name="allow-access-to-service-ip-ranges"></a>Povolení přístupu k rozsahům IP adres služby

Pokud se rozhodnete, že povolíte IP adresy služby Access, přečtěte si [zde](https://www.microsoft.com/download/confirmation.aspx?id=56519)dostupné ROZSAHy IP adres v souboru JSON. Bude potřeba, abyste povolili přístup k IP adresám, které odpovídají Azure Backup, Azure Storage a Azure Active Directory.

### <a name="allow-access-to-service-fqdns"></a>Povolení přístupu k plně kvalifikovanému názvu domény služby

K povolení přístupu k požadovaným službám z vašich serverů můžete použít taky tyto plně kvalifikované názvy domény:

| Služba    | Názvy domén, které se mají použít                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Služba Azure AD      | V souladu s [tímto článkem](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) povolte přístup k plně kvalifikovanému názvu domény v oddílech 56 a 59. |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>Použití proxy server HTTP ke směrování provozu

Při zálohování databáze SAP HANA běžící na virtuálním počítači Azure používá rozšíření zálohování na virtuálním počítači rozhraní API HTTPS k posílání příkazů pro správu do Azure Backup a dat do Azure Storage. Rozšíření zálohování používá pro ověřování taky službu Azure AD. Přesměrujte provoz rozšíření zálohování pro tyto tři služby přes proxy server HTTP. Pro povolení přístupu k požadovaným službám použijte seznam IP adres a plně kvalifikovaných názvů domén uvedených výše. Ověřené proxy servery se nepodporují.

## <a name="what-the-pre-registration-script-does"></a>Co skript pro předběžnou registraci dělá

Spuštění předregistračního skriptu provede následující funkce:

* Na základě distribuce systému Linux skript nainstaluje nebo aktualizuje všechny potřebné balíčky, které vyžaduje agent Azure Backup.
* Provádí odchozí kontroly připojení k síti pomocí Azure Backup serverů a závislých služeb, jako je Azure Active Directory a Azure Storage.
* Přihlásí se do systému HANA pomocí klíče uživatele uvedeného v rámci [požadavků](#prerequisites). Uživatelský klíč se používá k vytvoření zálohovacího uživatele (AZUREWLBACKUPHANAUSER) v systému HANA a **klíč uživatele lze odstranit po úspěšném spuštění skriptu před registrací**.
* K AZUREWLBACKUPHANAUSER se přiřazují tyto požadované role a oprávnění:
  * Pro MDC: správce databáze a správce zálohování (od HANA 2,0 SPS05 a vyšší): vytvoření nových databází během obnovování.
  * Pro SDC: Správce zálohování: pro vytvoření nových databází během obnovování.
  * ČTENÍ katalogu: pro čtení katalogu záloh.
  * SAP_INTERNAL_HANA_SUPPORT: pro přístup k několika soukromým tabulkám. Vyžaduje se jenom pro verze SDC a MDC pod HANA 2,0 SPS04 rev 46. To není vyžadováno pro HANA 2,0 SPS04 rev 46 a novější, protože od týmu HANA získáváme požadované informace z veřejných tabulek nyní s opravou od týmu HANA.
* Skript přidá klíč do **hdbuserstore** pro AZUREWLBACKUPHANAUSER pro modul plug-in pro zálohování Hana pro zpracování všech operací (databázové dotazy, operace obnovení, konfigurace a spuštění zálohování).

>[!NOTE]
> Klíč uživatele, který je uveden jako součást [požadavků](#prerequisites) , můžete explicitně předat jako parametr do skriptu před registrací: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Pokud chcete zjistit, jaké další parametry skript akceptuje, použijte příkaz. `bash msawb-plugin-config-com-sap-hana.sh --help`

Pro potvrzení vytvoření klíče spusťte na počítači HANA příkaz HDBSQL s přihlašovacími údaji SIDADM:

```hdbsql
hdbuserstore list
```

Výstup příkazu by měl zobrazovat klíč {SID} {DBNAME} s uživatelem zobrazeným jako AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Ujistěte se, že je v systému k dispozici jedinečná sada souborů SSFS `/usr/sap/{SID}/home/.hdb/` . V této cestě by měla být jenom jedna složka.

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Recovery Services trezor je entita, která ukládá zálohy a body obnovení vytvořené v průběhu času. Trezor Recovery Services obsahuje také zásady zálohování, které jsou přidruženy k chráněným virtuálním počítačům.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se ke svému předplatnému na webu [Azure Portal](https://portal.azure.com/).

2. V nabídce vlevo vyberte **všechny služby** .

   ![Vyberte Všechny služby.](./media/tutorial-backup-sap-hana-db/all-services.png)

3. V dialogovém okně **Všechny služby** zadejte **Recovery Services**. Seznam prostředků se filtruje podle vašeho zadání. Ze seznamu prostředků vyberte **Trezory služby Recovery Services**.

   ![Vybrat Recovery Services trezory](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Na řídicím panelu trezorů **Recovery Services** vyberte **Přidat**.

   ![Přidat Recovery Services trezor](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Otevře se dialogové okno **Trezor služby Recovery Services**. Zadejte hodnoty pro **název, předplatné, skupinu prostředků** a **umístění** .

   ![Vytvoření trezoru služby Recovery Services](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Název**: název slouží k identifikaci trezoru Recovery Services a musí být jedinečný pro předplatné Azure. Zadejte název, který obsahuje alespoň 2 znaky, ale ne více než 50 znaků. Název musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky. Pro tento kurz jsme použili název **SAPHanaVault**.
   * **Předplatné:** Vyberte předplatné, které chcete použít. Pokud jste členem jenom jednoho předplatného, název se zobrazí. Pokud si nejste jisti, které předplatné použít, použijte výchozí (navrhované) předplatné. Více možností je dostupných, jen pokud je váš pracovní nebo školní účet přidružený k více předplatným Azure. V tomto příkladu jsme použili předplatné předplatného **řešení SAP HANA Solution Lab** .
   * **Skupina prostředků:** Použijte existující skupinu prostředků, nebo vytvořte novou. Tady jsme použili **SAPHANADemo**.<br>
   Pokud chcete zobrazit seznam dostupných skupin prostředků ve vašem předplatném, vyberte **použít existující** a pak v rozevíracím seznamu vyberte prostředek. Pokud chcete vytvořit novou skupinu prostředků, vyberte **Vytvořit novou** a zadejte název. Úplné informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](../azure-resource-manager/management/overview.md).
   * **Location** (Umístění): Vyberte zeměpisnou oblast trezoru. Trezor musí být ve stejné oblasti jako virtuální počítač se spuštěným SAP HANA. Použili jsme **východní USA 2**.

5. Vyberte **zkontrolovat + vytvořit**.

   ![Vyberte zkontrolovat & vytvořit.](./media/tutorial-backup-sap-hana-db/review-create.png)

Nyní je vytvořen Recovery Services trezor.

## <a name="discover-the-databases"></a>Zjištění databází

1. V trezoru v **Začínáme** vyberte **zálohování**. V aplikaci **kde je spuštěná vaše úloha?** vyberte **na virtuálním počítači Azure SAP HANA**.
2. Vyberte **Spustit zjišťování**. Tím se iniciuje zjišťování nechráněných virtuálních počítačů se systémem Linux v oblasti trezoru. Zobrazí se virtuální počítač Azure, který chcete chránit.
3. V části **vybrat Virtual Machines** vyberte odkaz pro stažení skriptu, který poskytuje oprávnění pro službu Azure Backup pro přístup k SAP HANA virtuálním počítačům pro zjištění databáze.
4. Spusťte skript na virtuálním počítači hostujícím SAP HANA databázi, kterou chcete zálohovat.
5. Po spuštění skriptu na virtuálním počítači vyberte v části **vybrat Virtual Machines** virtuální počítač. Pak vyberte **Vyhledat databáze**.
6. Azure Backup zjistí všechny databáze SAP HANA na virtuálním počítači. Během zjišťování Azure Backup registruje virtuální počítač s trezorem a na virtuálním počítači nainstaluje rozšíření. V databázi není nainstalován žádný agent.

   ![Zjištění databází](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurace zálohování

Teď, když se hledají databáze, které chceme zálohovat, pojďme povolit zálohování.

1. Vyberte **Konfigurovat zálohu**.

   ![Konfigurace zálohování](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. V **položce vyberte položky, které chcete zálohovat** vyberte jednu nebo více databází, které chcete chránit, a pak vyberte **OK**.

   ![Vyberte položky, které chcete zálohovat.](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. V části **zásady zálohování > vyberte zásady zálohování**, vytvořte nové zásady zálohování pro databáze, a to v souladu s pokyny v následující části.

   ![Zvolit zásady zálohování](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Po vytvoření zásady v **nabídce zálohování** vyberte **Povolit zálohování**.

   ![Vyberte povolit zálohování.](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Sledujte průběh konfigurace zálohování v oblasti **oznámení** na portálu.

## <a name="creating-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy se zálohují zálohy a jak dlouho se uchovávají.

* Zásady se vytvářejí na úrovni trezoru.
* Stejné zásady zálohování může používat více trezorů, ale je potřeba je pro každý trezor použít zvlášť.

Nastavení zásad určete následujícím způsobem:

1. Do pole **Název zásad** zadejte název nových zásad. V takovém případě zadejte **SAPHANA**.

   ![Zadat název pro nové zásady](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. V **zásadách úplného zálohování** vyberte **četnost zálohování**. Můžete zvolit **každý den** nebo **každý týden**. Pro tento kurz jsme zvolili **denní** zálohování.

   ![Výběr četnosti zálohování](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. V části **Rozsah uchování** nakonfigurujte nastavení uchovávání pro úplnou zálohu.
   * Ve výchozím nastavení jsou vybrány všechny možnosti. Vymažte všechny limity rozsahu uchování, které nechcete používat, a nastavte ty, které chcete provést.
   * Minimální doba uchování pro jakýkoli typ zálohy (úplný/rozdíl/protokol) je sedm dní.
   * Body obnovení se označují pro uchovávání na základě jejich rozsahu uchovávání. Například pokud vyberete denní úplné zálohování, každý den se aktivuje pouze jedno úplné zálohování.
   * Záloha pro určitý den je označená a zachovaná na základě rozsahu a nastavení týdenního uchování.
   * Měsíční a roční rozsahy uchovávání se chovají podobným způsobem.
4. V nabídce **Zásady úplného zálohování** výběrem možnosti **OK** přijměte nastavení.
5. Pak vyberte **rozdílové zálohování** a přidejte rozdílovou zásadu.
6. V části **Zásady rozdílového zálohování** výběrem možnosti **Povolit** otevřete ovládací prvky frekvence a uchovávání. Povolili jsme rozdílové zálohování každé **neděle** v **2:00**, které se uchovává po dobu **30 dnů**.

   ![Zásady rozdílového zálohování](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Přírůstkové zálohování není aktuálně podporováno.
   >

7. Výběrem možnosti **OK** zásady uložte a vraťte se do hlavní nabídky **Zásady zálohování**.
8. Vyberte **zálohování protokolu** a přidejte zásady zálohování transakčního protokolu.
   * **Zálohování protokolu** je ve výchozím nastavení nastaveno na hodnotu **Povolit**. Toto nejde zakázat, protože SAP HANA spravuje všechny zálohy protokolů.
   * Nastavili jsme **2 hodiny** jako plán zálohování a **15 dní** doby uchování.

    ![Zásady zálohování protokolů](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Zálohování protokolů začíná proudem až po úspěšném úplném úplném zálohování.
   >

9. Výběrem možnosti **OK** zásady uložte a vraťte se do hlavní nabídky **Zásady zálohování**.
10. Po dokončení definování zásad zálohování vyberte **OK**.

Úspěšně jste nakonfigurovali zálohy pro vaše SAP HANA databáze.

## <a name="next-steps"></a>Další kroky

* Naučte se [spouštět zálohy na vyžádání v SAP HANA databázích běžících na virtuálních počítačích Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup) .
* Informace o tom, jak [obnovit SAP HANA databáze běžící na virtuálních počítačích Azure](sap-hana-db-restore.md)
* Naučte se [spravovat SAP HANA databází zálohovaných pomocí Azure Backup](sap-hana-db-manage.md)
* Naučte se [řešit běžné problémy při zálohování SAP HANA databází](backup-azure-sap-hana-database-troubleshoot.md) .