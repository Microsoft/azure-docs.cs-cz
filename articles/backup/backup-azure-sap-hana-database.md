---
title: Zálohování databáze SAP HANA do Azure s využitím Azure Backup
description: V tomto článku se dozvíte, jak zálohovat databázi SAP HANA do virtuálních počítačů Azure pomocí služby Azure Backup.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: b808038c9b973cbf4ba9e0b2e54d97bd41664297
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378249"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Zálohování databází SAP HANA na virtuálních počítačích Azure

SAP HANA databáze jsou kritické úlohy, které vyžadují nízký cíl bodu obnovení (RPO) a dlouhodobé uchovávání. SAP HANA databáze běžící na virtuálních počítačích Azure můžete zálohovat pomocí [Azure Backup](backup-overview.md).

V tomto článku se dozvíte, jak zálohovat SAP HANA databází, které běží na virtuálních počítačích Azure, do trezoru služby Azure Backup Recovery Services.

V tomto článku se dozvíte, jak:
> [!div class="checklist"]
>
> * Vytvoření a konfigurace trezoru
> * Vyhledat databáze
> * Konfigurace zálohování
> * Spuštění úlohy zálohování na vyžádání

>[!NOTE]
>Od 1. srpna 2020 je SAP HANA zálohování pro RHEL (7,4, 7,6, 7,7 & 8,1) všeobecně dostupné.

>[!NOTE]
>**Obnovitelné odstranění pro SQL Server na virtuálním počítači Azure a obnovitelné odstranění pro SAP HANA v úlohách virtuálních počítačů Azure** je teď dostupné ve verzi Preview.<br>
>Pokud si chcete zaregistrovat verzi Preview, napište nám na [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

## <a name="prerequisites"></a>Požadavky

Informace o [požadavcích](tutorial-backup-sap-hana-db.md#prerequisites) a o [tom, co skript](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) pro předběžnou registraci zahrnuje oddíly k nastavení databáze pro zálohování.

### <a name="establish-network-connectivity"></a>Navázat připojení k síti

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

#### <a name="private-endpoints"></a>Soukromé koncové body

Soukromé koncové body umožňují zabezpečené připojení ze serverů ve virtuální síti do trezoru Recovery Services. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro váš trezor. Síťový provoz mezi prostředky uvnitř virtuální sítě a trezoru se přenáší přes virtuální síť a privátní odkaz na páteřní síť Microsoftu. Tím se eliminuje riziko z veřejného Internetu. [Tady](./private-endpoints.md)si můžete přečíst další informace o privátních koncových bodech pro Azure Backup.

#### <a name="nsg-tags"></a>Značky NSG

Pokud používáte skupiny zabezpečení sítě (NSG), pomocí značky služby *AzureBackup* povolte odchozí přístup k Azure Backup. Kromě značky Azure Backup je také potřeba, abyste umožnili připojení k ověřování a přenosu dat vytvořením podobných [pravidel NSG](../virtual-network/security-overview.md#service-tags) pro *Azure AD* a *Azure Storage*.  Následující kroky popisují proces vytvoření pravidla pro Azure Backup značku:

1. Ve **všech službách**klikněte na **skupiny zabezpečení sítě** a vyberte skupinu zabezpečení sítě.

1. V části **Nastavení**vyberte **odchozí pravidla zabezpečení** .

1. Vyberte **Přidat**. Zadejte všechny požadované podrobnosti pro vytvoření nového pravidla, jak je popsáno v [Nastavení pravidla zabezpečení](../virtual-network/manage-network-security-group.md#security-rule-settings). Ujistěte se, že možnost **cíl** je nastavená na *příznak služby* a **cílová značka služby** je nastavená na *AzureBackup*.

1. Vyberte **Přidat**  a uložte nově vytvořené odchozí pravidlo zabezpečení.

Podobně můžete vytvořit NSG odchozí pravidla zabezpečení pro Azure Storage a Azure AD. Další informace o značkách služby najdete v [tomto článku](../virtual-network/service-tags-overview.md).

#### <a name="azure-firewall-tags"></a>Značky Azure Firewall

Pokud používáte Azure Firewall, vytvořte pravidlo aplikace pomocí [značky plně kvalifikovaného názvu domény Azure firewall](../firewall/fqdn-tags.md) *AzureBackup* . To umožňuje všem odchozím přístupům Azure Backup.

#### <a name="allow-access-to-service-ip-ranges"></a>Povolení přístupu k rozsahům IP adres služby

Pokud se rozhodnete, že povolíte IP adresy služby Access, přečtěte si [zde](https://www.microsoft.com/download/confirmation.aspx?id=56519)dostupné ROZSAHy IP adres v souboru JSON. Bude potřeba, abyste povolili přístup k IP adresám, které odpovídají Azure Backup, Azure Storage a Azure Active Directory.

#### <a name="allow-access-to-service-fqdns"></a>Povolení přístupu k plně kvalifikovanému názvu domény služby

K povolení přístupu k požadovaným službám z vašich serverů můžete použít taky tyto plně kvalifikované názvy domény:

| Služba    | Názvy domén, které se mají použít                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Služba Azure AD      | V souladu s [tímto článkem](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) povolte přístup k plně kvalifikovanému názvu domény v oddílech 56 a 59. |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>Použití proxy server HTTP ke směrování provozu

Při zálohování databáze SAP HANA běžící na virtuálním počítači Azure používá rozšíření zálohování na virtuálním počítači rozhraní API HTTPS k posílání příkazů pro správu do Azure Backup a dat do Azure Storage. Rozšíření zálohování používá pro ověřování taky službu Azure AD. Přesměrujte provoz rozšíření zálohování pro tyto tři služby prostřednictvím proxy serveru HTTP. Pro povolení přístupu k požadovaným službám použijte seznam IP adres a plně kvalifikovaných názvů domén uvedených výše. Ověřené proxy servery se nepodporují.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Zjištění databází

1. V trezoru v **Začínáme**vyberte **zálohování**. V aplikaci **kde je spuštěná vaše úloha?** vyberte **na virtuálním počítači Azure SAP HANA**.
2. Vyberte **Spustit zjišťování**. Tím se iniciuje zjišťování nechráněných virtuálních počítačů se systémem Linux v oblasti trezoru.

   * Po zjištění se na portálu zobrazí nechráněné virtuální počítače uvedené podle názvu a skupiny prostředků.
   * Pokud virtuální počítač není uvedený podle očekávání, ověřte, jestli je už zálohovaný v trezoru.
   * Několik virtuálních počítačů může mít stejný název, ale patří do různých skupin prostředků.

3. V části **vybrat Virtual Machines**vyberte odkaz pro stažení skriptu, který poskytuje oprávnění pro službu Azure Backup pro přístup k SAP HANA virtuálním počítačům pro zjištění databáze.
4. Spusťte skript na každém virtuálním počítači, který je hostitelem SAP HANA databází, které chcete zálohovat.
5. Po spuštění skriptu na virtuálních počítačích vyberte v části **vybrat Virtual Machines**virtuální počítače. Pak vyberte **Vyhledat databáze**.
6. Azure Backup zjistí všechny databáze SAP HANA na virtuálním počítači. Během zjišťování Azure Backup registruje virtuální počítač s trezorem a na virtuálním počítači nainstaluje rozšíření. V databázi není nainstalován žádný agent.

    ![Zjišťování SAP HANAch databází](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Konfigurace zálohování  

Nyní povolte zálohování.

1. V kroku 2 vyberte **Konfigurovat zálohu**.

    ![Konfigurace zálohování](./media/backup-azure-sap-hana-database/configure-backup.png)
2. V části **Vyberte položky, které chcete zálohovat**vyberte všechny databáze, které chcete chránit > **OK**.

    ![Vyberte položky, které chcete zálohovat.](./media/backup-azure-sap-hana-database/select-items.png)
3. V části **zásady zálohování**  >  **vyberte zásady zálohování**, vytvořte nové zásady zálohování pro databáze, a to v souladu s pokyny uvedenými níže.

    ![Zvolit zásady zálohování](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Po vytvoření zásady v nabídce **zálohování** vyberte **Povolit zálohování**.

    ![Povolit zálohování](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Sledujte průběh konfigurace zálohování v oblasti **oznámení** na portálu.

### <a name="create-a-backup-policy"></a>Vytvoření zásady zálohování

Zásady zálohování definují, kdy se zálohují zálohy a jak dlouho se uchovávají.

* Zásada se vytvoří na úrovni trezoru.
* Několik trezorů může používat stejné zásady zálohování, ale je nutné použít zásady zálohování pro každý trezor.

>[!NOTE]
>Azure Backup se při zálohování databáze SAP HANA běžící na virtuálním počítači Azure automaticky neupraví na letní čas při ukládání.
>
>Zásadu podle potřeby upravte ručně.

Nastavení zásad určete následujícím způsobem:

1. Do pole **název zásady**zadejte název nové zásady.

   ![Zadejte název zásady.](./media/backup-azure-sap-hana-database/policy-name.png)
2. V **zásadách úplného zálohování**vyberte **četnost záloh**, zvolte **denně** nebo **týdně**.
   * **Denně**: vyberte hodinu a časové pásmo, ve kterém se spustí úloha zálohování.
       * Musíte spustit úplnou zálohu. Tuto možnost nemůžete vypnout.
       * Pro zobrazení zásady vyberte **úplné zálohování** .
       * Pro každodenní úplné zálohování nemůžete vytvořit rozdílové zálohy.
   * **Týdně**: vyberte den v týdnu, hodinu a časové pásmo, ve kterém se úloha zálohování spustí.

   ![Vybrat četnost zálohování](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. V části **Rozsah uchování**nakonfigurujte nastavení uchovávání pro úplnou zálohu.
    * Ve výchozím nastavení jsou vybrány všechny možnosti. Vymažte všechny limity rozsahu uchování, které nechcete používat, a nastavte ty, které chcete provést.
    * Minimální doba uchování pro jakýkoli typ zálohy (úplný/rozdíl/protokol) je sedm dní.
    * Body obnovení jsou označeny pro uchování na základě jejich rozsahu uchovávání. Pokud například vyberete denní úplnou zálohu, spustí se každý den jenom jedno úplné zálohování.
    * Záloha pro určitý den je označená a zachovaná na základě rozsahu a nastavení týdenního uchování.
    * Měsíční a roční rozsah uchování se chová podobným způsobem.

4. V nabídce **zásady úplného zálohování** vyberte **OK** a přijměte nastavení.
5. Pokud chcete přidat rozdílové zásady, vyberte **rozdílové zálohování** .
6. V části **rozdílová zásada zálohování**vyberte **Povolit** a otevřete tak ovládací prvky četnost a uchování.
    * Ve většině případů můžete aktivovat jednu rozdílovou zálohu za den.
    * Rozdílové zálohy je možné uchovávat maximálně po dobu 180 dnů. Pokud budete potřebovat delší dobu uchovávání, je nutné použít úplné zálohování.

    ![Zásady rozdílového zálohování](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Přírůstkové zálohování není aktuálně podporováno.

7. Výběrem **OK** zásadu uložíte a vrátíte se do nabídky hlavní **zásady zálohování** .
8. Vyberte **zálohování protokolu** a přidejte zásady zálohování transakčního protokolu.
    * V případě **zálohování protokolu**vyberte **Povolit**.  Toto nejde zakázat, protože SAP HANA spravuje všechny zálohy protokolů.
    * Nastavte četnost a ovládací prvky uchování.

    > [!NOTE]
    > Zálohy protokolu se začnou přesměrovat až po úspěšném úplném zálohování.

9. Výběrem **OK** zásadu uložíte a vrátíte se do nabídky hlavní **zásady zálohování** .
10. Po dokončení definování zásad zálohování vyberte **OK**.

> [!NOTE]
> Každá záloha protokolu je zřetězena k předchozí úplné záloze, aby mohla tvořit řetěz obnovení. Tato úplná záloha se zachová, dokud neuplyne doba uchovávání poslední zálohy protokolu. To může znamenat, že úplná záloha se uchovává po dobu dalších let, aby se zajistilo, že se všechny protokoly mají obnovit. Řekněme, že uživatel má týdenní úplnou zálohu, denní rozdílovou a 2 hodinový protokol. Všechny z nich se uchovávají po dobu 30 dnů. Ale každý týden plný může být skutečně vyčištěn nebo odstraněn až po nejbližším úplném zálohování, tj. po dobu 30 až 7 dnů. Například týdenní úplné zálohování nastane na 16. listopadu. Podle zásad uchovávání informací by se měla uchovávat až do prosince 16. Poslední záloha protokolu pro tuto úplnou zálohu proběhne před dalším naplánovaným úplným 22. listopadu. Dokud nebude tento protokol k dispozici do prosince 22, nelze odstranit jeho plný 16. To znamená, že do prosince 22 se zachovají až do 16. listopadu.

## <a name="run-an-on-demand-backup"></a>Spuštění zálohování na vyžádání

Zálohy se spouštějí v souladu s plánem zásad. Zálohu můžete spustit na vyžádání následujícím způsobem:

1. V nabídce trezoru vyberte **zálohované položky**.
2. V části **zálohované položky**vyberte virtuální počítač, na kterém běží databáze SAP HANA, a pak vyberte **Zálohovat nyní**.
3. V části **Zálohovat nyní**vyberte typ zálohy, kterou chcete provést. Pak vyberte **OK**. Tato záloha se zachová v závislosti na zásadách přidružených k této zálohované položce.
4. Monitorujte oznámení na portálu. Průběh úlohy můžete monitorovat na řídicím panelu trezoru > probíhající **úlohy zálohování**  >  **In progress**. V závislosti na velikosti databáze může vytváření prvotní zálohy chvíli trvat.

Ve výchozím nastavení je uchovávání záloh na vyžádání 45 dní.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Spustit zálohování SAP HANA studia na databázi s povoleným Azure Backup

Pokud chcete použít místní zálohu (pomocí HANA studia) databáze, která se zálohuje pomocí Azure Backup, udělejte toto:

1. Počkejte na dokončení všech úplných záloh nebo zálohování protokolů databáze. Podívejte se na stav v SAP HANA studiu nebo řídicím panelu.
1. Zakažte zálohování protokolů a nastavte Katalog zálohování na systém souborů pro příslušnou databázi.
1. Provedete to tak, že dvakrát kliknete na konfigurace **SystemDB**  >  **Configuration**  >  **vybrat databázový**  >  **filtr (protokol)**.
1. Nastavte **enable_auto_log_backup** na **ne**.
1. Nastavte **log_backup_using_backint** na **false**.
1. Nastavte **catalog_backup_using_backint** na **false**.
1. Proveďte úplné zálohování databáze na vyžádání.
1. Počkejte na dokončení úplného zálohování a zálohování katalogu.
1. Vrátí předchozí nastavení zpátky do těch pro Azure:
    * Nastavte **enable_auto_log_backup** na **Ano**.
    * Nastavte **log_backup_using_backint** na **hodnotu true**.
    * Nastavte **catalog_backup_using_backint** na **hodnotu true**.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [obnovit SAP HANA databáze běžící na virtuálních počítačích Azure](./sap-hana-db-restore.md)
* Naučte se [spravovat SAP HANA databází zálohovaných pomocí Azure Backup](./sap-hana-db-manage.md)
