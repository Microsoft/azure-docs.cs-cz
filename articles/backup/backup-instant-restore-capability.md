---
title: Upgrade na V2 zásobník záloh virtuálních počítačů Azure
description: Upgrade procesu a nejčastější dotazy pro zásobník záloh virtuálních počítačů, modelu nasazení Resource Manager
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 10/3/2018
ms.author: trinadhk
ms.openlocfilehash: adb6898d9b7f4c0272085828778096816489a18d
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201793"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Získejte lepší zálohování a obnovení výkonu pomocí funkce Azure Backup rychlé obnovení

> [!NOTE]
> Na základě zpětné vazby přijetí od uživatelů, který **zásobník záloh virtuálních počítačů V2** je matoucí, pomocí služby Azure stack, jsme přejmenovali na **rychlé obnovení** díky tomu upgraduje a lepší prostředí.

> [!IMPORTANT]
> Po obdržení kladné odpovědi z našich uživatelů již s upgradovaný výkonu, Rozhodli jsme k upgradu všech našich uživatelů na funkce Rychlé obnovení. Pokud chcete upgradovat, není nic dělat-end vašeho. **Od února 2019**, začneme zavádět tuto funkci regiony.
>
>

Nový model pro rychlé obnovení obsahuje následující vylepšení funkcí:

* Možnost používat snímkům pořízeným jako součást, která je k dispozici pro obnovení bez čekání na přenos dat do trezoru na dokončení úlohy zálohování. Snižuje dobu čekání pro snímky ke zkopírování do trezoru před aktivace operace obnovení.
* Zkracuje dobu zálohování a obnovení tak, že zachová snímky místně, ve výchozím nastavení dva dny. Tento trezor výchozí je možné konfigurovat na libovolnou hodnotu mezi 1 až 5 dnů uvolní.
* Podporuje disk o velikosti až 4 TB.
* Podporuje disky SSD na úrovni Standard.
* Možnost používat nespravovaného virtuálního počítače na původní účty úložiště (na disk), při obnovování. Tato schopnost existuje i v případě, že virtuální počítač obsahuje disky, které jsou distribuovány mezi různými účty úložiště. Urychluje operace obnovení pro celou řadu konfigurací virtuálních počítačů.


## <a name="whats-new-in-this-feature"></a>Co je nového v této funkci

V současné době úloha zálohování se skládá ze dvou fází:

1.  Pořízení snímku virtuálního počítače.
2.  Snímek virtuálního počítače přenosu do trezoru služby Azure Recovery Services.

Vytvoření bodu obnovení se považuje za pouze po dokončení fáze 1 a 2. Jako součást tohoto upgradu je bod obnovení vytvořen, jakmile dokončení snímku a tohoto bodu obnovení snímku typu je možné provést obnovení pomocí stejný tok obnovení. Tento bod obnovení na portálu Azure portal můžete identifikovat pomocí "snímku" jako typ bodu obnovení a po snímku se přenesou do trezoru, typ bodu obnovení se změní na "snímek a trezor."

![Úloha zálohování ve virtuálním počítači zásobník záloh modelu nasazení Resource Manager – úložiště a trezoru](./media/backup-azure-vms/instant-rp-flow.png)

Ve výchozím nastavení snímky zůstanou zachovány dva dny. Tato funkce umožňuje operace obnovení z těchto snímků existuje řezem dolů časů obnovení. Snižuje čas, který se vyžaduje k transformaci a kopírování dat z trezoru zpět do uživatelského účtu úložiště pro scénáře nespravovaný disk a pro uživatele spravovaného disku a vytvoří spravovaných disků z dat služby Recovery Services.

## <a name="feature-considerations"></a>Důležité informace o funkci

* Snímky se ukládají společně s disky, a zvýšit tak vytvoření bodu obnovení a to ke zrychlení operací obnovení. V důsledku toho se zobrazí náklady na úložiště, které odpovídají snímkům pořízeným během tohoto období.
* Přírůstkové snímky se ukládají jako objekty BLOB stránky. Všichni uživatelé používali nespravované disky se vám účtuje snímky uloženým v jejich účtu místní úložiště. Protože kolekce bodů obnovení, který se používá záloh virtuálních počítačů spravovaných pomocí snímky objektů blob na základní úrovni úložiště, za spravované disky se zobrazí náklady odpovídající do objektu blob ceny snímků a jsou přírůstkové.
* Pro účty služby premium storage snímkům pořízeným pro okamžité obnovení počet bodů vůči limit 10 TB přidělené místo.
* Získáte možnost konfigurace uchovávání snímku na základě potřeb obnovení. V závislosti na požadavku můžete nastavit uchování snímků pro minimálně jeden den v okně zásady zálohování, jak je popsáno níže. To může pomoct ušetřit náklady pro ukládání snímků.

> [!NOTE]
>
Pomocí této rychlé obnovení upgradu, doba uchovávání snímek u všech zákazníků **(nové i stávající obojí je součástí)** se nastaví na výchozí hodnotu dvou dnů. Však můžete nastavit dobu trvání podle vašich požadavků na libovolnou hodnotu mezi 1 až 5 dnů.
>
>

## <a name="cost-impact"></a>Cenově dopad

Přírůstkovými snímky se ukládají v účtu úložiště Virtuálního počítače, které se používají pro rychlé obnovení. Přírůstkový snímek znamená, že místo obsazené snímku je rovna místo obsazené stránky, které jsou zapsány po vytvoření snímku. Fakturace je stále za GB je stejný, jak je uvedeno v využité místo obsazena snímek a cena za GB [stránce s cenami](https://azure.microsoft.com/pricing/details/managed-disks/).


## <a name="upgrading-to-instant-restore"></a>Upgrade na rychlé obnovení
Pokud použijete Azure portal, uvidíte oznámení na řídicím panelu trezoru. Toto oznámení se týká podporu velkých disků a zvýšení rychlosti zálohování a obnovení. Případně můžete přejít na stránku vlastností zobrazíte možnost upgrade trezoru.

![Úloha zálohování v modelu nasazení Resource Manager zásobník záloh virtuálních počítačů – podpora oznámení](./media/backup-azure-vms/instant-rp-banner.png)

Otevření obrazovky pro upgrade na rychlé obnovení, klikněte na banner.

![Úloha zálohování v zásobníku zálohování virtuálních počítačů modelu nasazení Resource Manager – upgrade](./media/backup-azure-vms/instant-rp.png)

## <a name="upcoming-changes"></a>Připravované změny

### <a name="configure-snapshot-retention-using-azure-portal"></a>Konfigurace uchovávání snímek pomocí webu Azure portal

Upgradovaný uživatelů na webu Azure Portal uvidíte pole přidá **zásady zálohování virtuálních počítačů** okně v části **rychlé obnovení** části. Doba uchování snímků z můžete změnit **zásady zálohování virtuálního počítače** okno pro všechny virtuální počítače přidružené k určité zásady zálohování.


![Úloha zálohování v rychlé obnovení nasazení](./media/backup-azure-vms/instant-rp-banner1.png)


## <a name="upgrade-to-instant-restore-using-powershell"></a>Upgrade provést rychlé obnovení pomocí prostředí PowerShell

Pokud nebudou automaticky aktualizovány, ještě a chcete samoobslužné spuštění následujících rutin se zvýšenými oprávněními terminálu prostředí PowerShell:

1.  Přihlaste se ke svému účtu Azure:

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Vyberte předplatné, pro kterou chcete zaregistrovat:

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Zaregistrujte toto předplatné:

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="upgrade-to-instant-restore-using-cli"></a>Upgrade provést rychlé obnovení pomocí rozhraní příkazového řádku

Spusťte následující příkazy z prostředí:

1.  Přihlaste se ke svému účtu Azure:

    ```
    az login
    ```

2.  Vyberte předplatné, pro kterou chcete zaregistrovat:

    ```
    az account set --subscription "Subscription Name"
    ```

3.  Zaregistrujte toto předplatné:

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>Ověřte, že upgrade se úspěšně dokončila

### <a name="powershell"></a>PowerShell
Prostředí PowerShell terminálu se zvýšenými oprávněními spusťte následující rutinu:

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>Rozhraní příkazového řádku
Z prostředí spusťte následující příkaz:

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Když je ve stavu "Registrováno", je vaše předplatné upgradovat na rychlé obnovení.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Jaké jsou náklady na důsledcích okamžitá obnova?
Snímky se ukládají společně s disky zrychlit vytvoření bodu obnovení a obnovení operací. V důsledku toho se zobrazí náklady na úložiště, které odpovídají uchování snímků vybrali jako součást zásad zálohování virtuálního počítače.

### <a name="does-snapshot-retention-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>Zvýší uchování snímků omezení počtu snímků účet úložiště úrovně premium ve velikosti 10 TB?
Ne, celkový počet omezení počtu snímků za úložiště účet zůstává na 10 TB.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>V účtech Premium Storage snímkům pořízeným okamžité obnovení bodu zabírat omezení počtu snímků 10 TB?
Ano, pro účty služby premium storage, snímkům pořízeným okamžité obnovení bodu zabírat přidělené 10 TB místa.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Jak funguje uchování snímků během období 5 dní?
Každý den nový snímek je převzata, a pak jednotlivým pět přírůstkových snímků. Velikost snímku závisí na četnosti změn dat, která je ve většině případů přibližně 2 – 5 %.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Rychlé obnovení snímku je přírůstkový snímek nebo úplné snímku?
Snímkům pořízeným jako součást funkce Rychlé obnovení jsou přírůstkových snímků.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Výpočet zvýšení přibližné náklady z důvodu funkce Rychlé obnovení
To závisí na četnosti změn virtuálního počítače. Stabilního stavu, můžete předpokládat, zvýšení nákladů je = snímku dobu uchování * denní četnost změn na virtuálním počítači * náklady na úložiště za GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Pokud je typ obnovení pro bod obnovení "Snímek a trezor" a provádět operace obnovení, který typ obnovení, který se použije?
Pokud "snímek a trezor" je typ obnovení, obnovení bude automaticky provedeno z místní snímek, který se bude porovnávat mnohem rychlejší obnovení provést z trezoru.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Co se stane, když vyberu doba uchování bodu obnovení (vrstva 2) menší než doba uchování snímků (1jádrové)?
Nový model neumožňuje odstranění bodu obnovení (úroveň 2) Pokud se odstraní snímek (1jádrové). Momentálně podporujeme sedmidenní dobu uchování pro odstranění snímku (1jádrové), takže bodu obnovení (úroveň 2) není podporována uchovávají po dobu kratší než sedm dnů. Doporučujeme, abyste plánování období uchování obnovení bodu (úroveň 2) větší než sedm dnů.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Proč je můj snímku existující i potom, co sada uchování období v zásady zálohování?
Pokud bod obnovení obsahuje snímky a, který je k dispozici nejnovější RP se uchovávají až do doby, které není další dostupnou úspěšnou zálohu. To je podle navrženého GC zásad ještě dnes, který zmocňuje nejméně jeden nejnovější předávající strana často být vždy k dispozici, v případě, že všechny zálohy, dále na selhat z důvodu chyby ve virtuálním počítači. V běžných scénářích RPs vyčistily ve 48 hodin po vypršení jejich platnosti.
