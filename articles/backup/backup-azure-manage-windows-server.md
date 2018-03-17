---
title: "Spravovat servery a trezory služeb zotavení Azure | Microsoft Docs"
description: "V tomto článku použijte ke správě trezory služeb zotavení Azure a servery."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: tysonn
ms.assetid: 4eea984b-7ed6-4600-ac60-99d2e9cb6d8a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: markgal
ms.openlocfilehash: 18ab960c3d86f4154f7cbbaa3dd4909b9b16b697
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="monitor-and-manage-azure-recovery-services-vaults-and-servers-for-windows-machines"></a>Monitorování a správa serverů a trezorů služby Azure Recovery Services pro počítače s Windows

Tento článek obsahuje přehled zálohování monitorování a Správa úloh k dispozici prostřednictvím portálu Azure a Microsoft Azure Backup agent. Tento článek předpokládá už máte předplatné Azure a vytvořili alespoň jeden trezor služeb zotavení.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]


## <a name="open-a-recovery-services-vault"></a>Otevřete trezoru služeb zotavení

Řídícím panelu trezoru služeb zotavení se zobrazuje podrobnosti o nebo atributů trezoru služeb zotavení.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí svého předplatného Azure.
2. Klikněte na **Všechny služby**. 

3. Chcete otevřít trezoru služeb zotavení. V dialogovém okně začněte psát **služeb zotavení**. Seznam se průběžně filtruje podle zadávaného textu. Klikněte na tlačítko **trezory služeb zotavení** k zobrazení seznamu trezorů služeb zotavení v rámci vašeho předplatného.

     ![Otevřete seznam krok trezory služeb zotavení 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png) <br/>

    Otevře se seznam trezorů služeb zotavení.

    ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. Ze seznamu trezorů vyberte název trezoru služeb zotavení, který chcete otevřít. Otevře se nabídce řídícího panelu trezoru služeb zotavení.

    ![panelu trezoru služeb zotavení](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Teď, když máte otevřený trezor služeb zotavení, vyzkoušejte některý z monitorování nebo Správa úloh.

## <a name="monitor-backup-jobs-and-alerts"></a>Úlohy zálohování monitorování a výstrahy

Úlohy a výstrahy na řídicím panelu trezoru služeb zotavení, kde uvidíte můžete monitorovat:

* Podrobnosti výstrahy zálohy
* Soubory a složky, jakož i Azure virtuální počítače chráněné v cloudu
* Celkový úložný využívat v Azure
* Stav úlohy zálohování

![Úlohy zálohování řídicí panel](./media/backup-azure-manage-windows-server/dashboard-tiles.png)

Kliknutím na tlačítko informace v každé z těchto dlaždic otevře přidružené nabídky kde budete spravovat související úlohy.

Z horní části řídicího panelu:

* Nastavení – poskytuje přístup k dispozici úlohy zálohování.
* Zálohování – pomáhá zálohujete nové soubory a složky (nebo virtuálních počítačích Azure) do trezoru služeb zotavení.
* Odstraňte – Pokud obnovení trezor služeb se už používá, odstraňte jej pro uvolnění prostoru úložiště. Odstranění je aktivní jenom po odstranění všech chráněných serverech z trezoru.

![Úlohy zálohování řídicí panel](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## <a name="alerts-for-backups-using-azure-backup-agent"></a>Výstrahy pro zálohování pomocí agenta Azure backup:
| Úroveň výstrahy | Zasílání upozornění |
| --- | --- |
| Kritická | pro selhání zálohování, obnovení selhání a odložené odstranění tj, když se někdo zastaví ochranu s odstranit data |
| Upozornění | pro zálohování dokončeno s varováním (Pokud < 100 soubory nejsou zálohovány kvůli problémům s poškození a > 1000000 soubory jsou zálohovány úspěšně) |
| Informační | v současné době jsou k dispozici pro Azure backup agent informační výstrahu. |

## <a name="manage-backup-alerts"></a>Správa výstrah zálohování
Klikněte na tlačítko **zálohování výstrahy** dlaždici otevřete **zálohování výstrahy** nabídky a spravovat výstrahy.

![Výstrahy zálohy](./media/backup-azure-manage-windows-server/manage-backup-alerts.png)

Na dlaždici zálohování výstrahy zobrazuje číslo:

* kritické výstrahy nepřeložené za posledních 24 hodin
* upozorňující výstrahy nepřeložené za posledních 24 hodin

Klikněte na odkaz zobrazíte **zálohování výstrahy** nabídce s filtrované zobrazení tyto výstrahy (kritický nebo upozornění).

V nabídce zálohování výstrahy můžete:

* Vyberte příslušné informace, které chcete zahrnout do upozornění.

    ![Zvolit sloupce](./media/backup-azure-manage-windows-server/choose-alerts-colunms.png)
* Filtrování výstrah pro závažnost, stav a časy zahájení a ukončení.

    ![Filtrování výstrah](./media/backup-azure-manage-windows-server/filter-alerts.png)
* Konfigurace oznámení pro závažností, četnosti a příjemce, a také zapnout výstrahy nebo vypnout.

    ![Filtrování výstrah](./media/backup-azure-manage-windows-server/configure-notifications.png)

Pokud **za výstrahy** je vybrán jako **oznámení** dojde k frekvenci, bez seskupení nebo snížení e-mailů. Každá výstraha výsledkem jedno oznámení (výchozí nastavení) a řešení e-mail je odeslán okamžitě.

Pokud **hodinové Digest** je vybrán jako **oznámení** frekvence e-mail je odeslán uživateli vysvětlením nevyřešené výstrahy byly vygenerovány za poslední hodinu. E-mailu s řešení odeslání na konci za hodinu.

Můžete odesílat upozornění pro následující úrovně závažnosti:

* Kritické
* upozornění
* Informace

Deaktivovat výstrahy s **deaktivovat** tlačítko v nabídce podrobnosti úlohy. Po kliknutí na tlačítko deaktivovat, můžete zadat poznámky k řešení.

Vyberte sloupce, které se mají zobrazit jako součást výstraha s **zvolit sloupce** tlačítko.

> [!NOTE]
> Z **nastavení** nabídce Spravovat výstrahy zálohy výběrem **monitorování a sestavy > Výstrahy a události > výstrahy zálohy** a pak levým na **filtru** nebo  **Konfigurace oznámení**.
>
>

## <a name="manage-backup-items"></a>Správa zálohování položek
Správa místního zálohování je teď dostupná v portálu pro správu. V části zálohování řídicího panelu **položky zálohování** dlaždice se zobrazuje číslo položky zálohování chráněných do trezoru.

Klikněte na tlačítko **složek souborů** v zálohování položek dlaždici.

![Dlaždice položky zálohování](./media/backup-azure-manage-windows-server/backup-items-tile.png)

Otevře se nabídka položky zálohování s filtrem nastavena na soubor a složka, kde uvidíte každé konkrétní zálohy položka v seznamu.

![Zálohování položek](./media/backup-azure-manage-windows-server/backup-item-list.png)

Pokud vyberete konkrétní zálohování položku ze seznamu, zobrazí se základní podrobnosti o této položce.

> [!NOTE]
> Z **nastavení** nabídce spravovat soubory a složky tak, že vyberete **chráněné položky > Zálohování položek** a potom vyberete **složek souborů** z rozevírací nabídky.
>
>

![Zálohování položek z nastavení](./media/backup-azure-manage-windows-server/backup-files-and-folders.png)

## <a name="manage-backup-jobs"></a>Správa úloh zálohování
Úlohy zálohování pro místní (Pokud je na místním serveru zálohování do Azure) a jsou viditelné v řídicím panelu zálohování Azure.

V části zálohování řídicí panel dlaždice úlohy zálohování znázorňuje počet úloh:

* probíhá
* selhání v posledních 24 hodin.

Chcete-li spravovat vaše úlohy zálohování, klikněte na tlačítko **úlohy zálohování** dlaždice, které se otevře v nabídce úlohy zálohování.

![Zálohování položek z nastavení](./media/backup-azure-manage-windows-server/backup-jobs.png)

Upravte informace k dispozici v nabídce úlohy zálohování se **zvolit sloupce** tlačítka v horní části stránky.

Použití **filtru** tlačítko můžete vybrat mezi soubory a složky a záloha virtuálního počítače Azure.

Pokud nevidíte zálohování souborů a složek, klikněte na tlačítko **filtru** tlačítka v horní části stránky a vyberte **soubory a složky** nabídce typu položky.

> [!NOTE]
> Z **nastavení** nabídce Spravovat úlohy zálohování tak, že vyberete **monitorování a sestavy > úlohy > úlohy zálohování** a potom vyberete **složek souborů** z rozevíracího seznamu nabídky.
>
>

## <a name="monitor-backup-usage"></a>Sledování využití zálohování
V části zálohování řídicího panelu zobrazí dlaždice využití zálohování úložiště využívat v Azure. Využití úložiště je k dispozici pro:

* Využití úložiště LRS cloudu přidruženého k úložišti
* Využití úložiště GRS cloudu přidruženého k úložišti

## <a name="manage-your-production-servers"></a>Spravovat vaše produkční servery
Chcete-li spravovat provozních serverů, klikněte na tlačítko **nastavení**.

V části Správa klikněte na **infrastruktura zálohování > produkční servery**.

Seznamy nabídek provozních serverů všechny dostupné provozních serverů. Klikněte na server v seznamu a otevřete tak podrobnosti serveru.

![Chráněné položky](./media/backup-azure-manage-windows-server/production-server-list.png)


## <a name="open-the-azure-backup-agent"></a>Otevřete agenta Azure Backup
Otevřete **Microsoft Azure Backup agent** (můžete najít tak, že váš počítač pro *Microsoft Azure Backup*).

![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/snap-in-search.png)

Z **akce** k dispozici na pravé straně konzole agenta zálohování, můžete provádět následující úlohy správy:

* Register Server
* Plán zálohování
* Zálohovat nyní
* Změnit vlastnosti

![Microsoft Azure Backup agent konzoly akce](./media/backup-azure-manage-windows-server/console-actions.png)

> [!NOTE]
> K **obnovit Data**, najdete v části [obnovit soubory do systému Windows server nebo klientský počítač systému Windows](backup-azure-restore-windows-server.md).
>
>

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="modify-the-backup-schedule"></a>Upravte plán zálohování
1. V agentovi nástroje Microsoft Azure Backup klikněte na tlačítko **plánem zálohování**.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/schedule-backup.png)
2. V **Průvodce plánem zálohování** ponechte **provádět změny položky zálohování nebo časy** možnost a klikněte na tlačítko **Další**.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
3. Pokud chcete přidat nebo změnit položky, na **výběr položek k zálohování** obrazovce klikněte na tlačítko **přidat položky**.

    Můžete také nastavit **nastavení vyloučení** z této stránky v průvodci. Pokud chcete vyloučit soubory nebo typy souborů, přečtěte si postup pro přidání [nastavení vyloučení](#manage-exclusion-settings).
4. Vyberte soubory a složky, které chcete zálohovat a klikněte na tlačítko **nevadí**.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/add-items-modify.png)
5. Zadejte **plán zálohování** a klikněte na tlačítko **Další**.

    Můžete naplánovat denní (probíhající maximálně 3krát denně) nebo týdenní zálohování.

    ![Položky k zálohování z Windows Serveru](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

   > [!NOTE]
   > Zadat plán zálohování je podrobně popsány v tomto [článku](backup-azure-backup-cloud-as-tape.md).
   >

6. Vyberte **zásady uchovávání informací** pro záložní kopii a klikněte na tlačítko **Další**.

    ![Položky k zálohování z Windows Serveru](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)
7. Na **potvrzení** obrazovky Zkontrolujte zadané informace a klikněte na tlačítko **Dokončit**.
8. Až průvodce dokončí vytváření **plán zálohování**, klikněte na tlačítko **Zavřít**.

    Po úpravě ochrany, můžete ověřit, že zálohy spouštějí správně přechodem na **úlohy** kartě a potvrzující, že změny se projeví v úloh zálohování.

## <a name="enable-network-throttling"></a>Povolit omezení šířky pásma sítě

Agent Azure Backup poskytuje omezování karta, která umožňuje řídit použití šířky pásma sítě při přenosu dat. Tento ovládací prvek může být užitečné, pokud potřebujete zálohovat data v pracovní době, ale nechcete, aby proces zálohování narušoval ostatní internetový provoz. Omezování dat přenos platí pro zálohování a obnovení aktivity.  

Povolit omezení:

1. V **agenta zálohování**, klikněte na tlačítko **změnit vlastnosti**.
2. Na ** omezení karty, vyberte **povolit využití šířky pásma Internetu u operací zálohování omezení**.

    ![Omezení šířky pásma sítě](./media/backup-azure-manage-windows-server/throttling-dialog.png)

    Jakmile jste povolili omezování, zadejte povolenou šířky pásma pro přenos zálohovaných dat během **pracovní hodiny** a **nepracovní hodiny**.

    Hodnoty šířky pásma začít až 512 kilobitů za sekundu (Kbps) a můžete přejít až 1023 MB za sekundu (Mbps). Můžete také určit spuštění a dokončení pro **pracovní hodiny**, a které dny v týdnu, jsou považovány za pracovních dnů. Mimo pracovní dobu v určené době se považuje za mimo pracovní dobu.
3. Klikněte na **OK**.

## <a name="manage-exclusion-settings"></a>Spravovat nastavení vyloučení
1. Otevřete **Microsoft Azure Backup agent** (najdete ho tak, že váš počítač pro *Microsoft Azure Backup*).

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/snap-in-search.png)
2. V agentovi nástroje Microsoft Azure Backup klikněte na tlačítko **plánem zálohování**.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/schedule-backup.png)
3. V ponechejte Průvodce plánem zálohování **provádět změny položky zálohování nebo časy** možnost a klikněte na tlačítko **Další**.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)
4. Klikněte na tlačítko **nastavení vyloučení**.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/exclusion-settings.png)
5. Klikněte na tlačítko **Přidat vyloučení**.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/add-exclusion.png)
6. Vyberte umístění a potom klikněte na **OK**.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/exclusion-location.png)
7. Přidat příponu souboru v **typ souboru** pole.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Přidání rozšíření MP3

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Chcete-li přidat jiné rozšíření, klikněte na tlačítko **Přidat vyloučení** a zadejte jinou příponu typu souboru (Přidání rozšíření .jpeg).

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/exclude-jpg.png)
8. Pokud jste přidali všechna rozšíření, klikněte na tlačítko **OK**.
9. Pomocí Průvodce plánem zálohování nadále pokračovat kliknutím **Další** dokud **potvrzovací stránku**, pak klikněte na tlačítko **Dokončit**.

    ![Plán zálohování Windows serveru](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
**Q1. Úloha zálohování stav zobrazuje jako dokončené v aplikace Azure backup agent, proč není ho získat projeví okamžitě v portálu?**

Odpověď 1. Existuje na maximální zpoždění 15 minut mezi stav úlohy zálohování se v Azure backup agent a portálu Azure.

**Q.2 při selhání úlohy zálohování, jak dlouho trvá vygeneroval výstrahu?**

A.2 výstraha se vyvolá v rámci 20 minut selhání zálohování Azure.

**Q3. Je případ, kdy e-mailu neodešle, pokud jsou nakonfigurované oznámení?**

Odpověď 3. Níže jsou oznámení nebude odeslána aby výstrahy nepůsobily rušivě. v případech:

* Pokud oznámení jsou nakonfigurovány každou hodinu a výstraha je vyvolána a vyřešit v rámci hodiny
* Úloha je zrušena.
* Druhý úloha zálohování se nezdařila, protože probíhá úloha původní zálohování.

## <a name="troubleshooting-monitoring-issues"></a>Monitorování řešení potíží
**Problém:** úlohy a výstrahy z agenta Azure Backup se nezobrazí na portálu.

**Řešení potíží:** procesu ```OBRecoveryServicesManagementAgent```, odešle data úlohy a výstrahy do služby Azure Backup. Příležitostně tento proces může zablokovat nebo vypnutí.

1. Chcete-li ověřit, proces neběží, otevřete **Správce úloh** a zkontrolujte, zda ```OBRecoveryServicesManagementAgent``` proces běží.
2. Za předpokladu, že proces neběží, otevřete **ovládací panely** a procházet seznam služeb. Spustit nebo restartovat **správy agenta služeb zotavení Microsoft Azure**.

    Další informace vyhledejte protokoly na:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Například:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Další postup
* [Obnovení systému Windows Server nebo klienta Windows z Azure](backup-azure-restore-windows-server.md)
* Další informace o zálohování Azure, najdete v části [Přehled zálohování Azure](backup-introduction-to-azure-backup.md)
* Přejděte [fórum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933)
