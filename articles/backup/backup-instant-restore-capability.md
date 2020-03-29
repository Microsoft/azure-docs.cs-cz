---
title: Funkce okamžitého obnovení Azure
description: Funkce okamžitého obnovení Azure a nejčastější dotazy pro zásobník zálohování virtuálních počítačů, model nasazení Správce prostředků
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 7cf437c6b20ea6b688e8e93e401cf71ef0260888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705424"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Lepší výkon zálohování a obnovení díky funkci okamžitého obnovení zálohování azure

> [!NOTE]
> Na základě zpětné vazby od uživatelů přejmenováváme **zásobník zálohování virtuálních počítačů V2** na **okamžité obnovení,** abychom snížili nejasnosti s funkcemi Azure Stack.
> Všichni uživatelé zálohování Azure byly nyní upgradovány na **okamžité obnovení**.

Nový model pro okamžité obnovení poskytuje následující vylepšení funkcí:

* Možnost použití snímků pořízených jako součást úlohy zálohování, která je k dispozici pro obnovení bez čekání na dokončení přenosu dat do úložiště. Zkracuje dobu čekání pro snímky zkopírovat do úložiště před aktivací obnovení.
* Zkracuje dobu zálohování a obnovení tím, že uchovává snímky místně, ve výchozím nastavení po dobu dvou dnů. Tato výchozí hodnota uchovávání snímků je konfigurovatelná na libovolnou hodnotu od 1 do 5 dnů.
* Podporuje velikostdisku až 32 TB. Změna velikosti disků není doporučeno Azure Backup.
* Podporuje standardní Disky SSD spolu se standardními disky HDD a disky Premium SSD.
* Možnost používat nespravované účty úložiště virtuálních počítačů (na disk) při obnovení. Tato možnost existuje i v případě, že virtuální modul má disky, které jsou distribuovány mezi účty úložiště. Urychluje operace obnovení pro širokou škálu konfigurací virtuálních počítačů.
* Pro zálohování virtuálních zařízení, které používají úložiště premium, s okamžitým obnovením doporučujeme přidělit *50 %* volného místa z celkového přiděleného úložného prostoru, který je vyžadován **pouze** pro první zálohu. 50 % volného místa není po dokončení první zálohy podmínkou pro zálohování.

## <a name="whats-new-in-this-feature"></a>Co je v této funkci nového

V současné době se úloha zálohování skládá ze dvou fází:

1. Pořízení snímku virtuálního počítače.
2. Přenos snímku virtuálního počítače do trezoru služby Azure Recovery Services.

Bod obnovení se považuje za vytvořený až po dokončení fází 1 a 2. Jako součást tohoto upgradu bod obnovení je vytvořen, jakmile snímek je dokončena a tento bod obnovení typu snímku lze použít k provedení obnovení pomocí stejného toku obnovení. Tento bod obnovení na webu Azure Portal můžete identifikovat pomocí "snímek" jako typu bodu obnovení a po přenosu snímku do trezoru se typ bodu obnovení změní na "snímek a trezor".

![Úloha zálohování v modelu nasazení zásobníku zálohování virtuálních zařízení Resource Manager – úložiště a trezor](./media/backup-azure-vms/instant-rp-flow.png)

Ve výchozím nastavení jsou snímky zachovány po dobu dvou dnů. Tato funkce umožňuje obnovení operace z těchto snímků tam snížením doby obnovení. Zkracuje čas potřebný k transformaci a kopírování dat zpět z úložiště.

## <a name="feature-considerations"></a>Důležité informace o funkcích

* Snímky jsou uloženy spolu s disky pro zvýšení vytváření bodů obnovení a urychlit operace obnovení. V důsledku toho uvidíte náklady na úložiště, které odpovídají snímky pořízené během tohoto období.
* Přírůstkové snímky jsou uloženy jako objekty BLOB stránky. Všichni uživatelé, kteří používají nespravované disky, se účtují za snímky uložené v jejich účtu místního úložiště. Vzhledem k tomu, že kolekce bodů obnovení používané zálohami spravovaného virtuálního počítače používají snímky objektů blob na základní úrovni úložiště, u spravovaných disků se zobrazí náklady odpovídající cenám snímků objektů blob a jsou přírůstkové.
* U účtů úložiště premium se snímky pořízené pro body okamžitého obnovení započítávají do limitu 10 TB přiděleného místa.
* Získáte možnost konfigurovat uchovávání snímků na základě potřeb obnovení. V závislosti na požadavku můžete nastavit uchovávání snímků na minimálně jeden den v okně zásad zálohování, jak je vysvětleno níže. To vám pomůže ušetřit náklady na uchovávání snímků, pokud neprovádíte obnovení často.
* Jedná se o jednosměrný upgrade, po upgradu na okamžité obnovení se nemůžete vrátit.

>[!NOTE]
>Při tomto okamžitém obnovení upgradu bude doba uchovávání snímků všech zákazníků **(nových i stávajících zahrnutých)** nastavena na výchozí hodnotu dvou dnů. Dobu trvání však můžete nastavit podle požadavku na libovolnou hodnotu od 1 do 5 dnů.

## <a name="cost-impact"></a>Dopad na náklady

Přírůstkové snímky se ukládají v účtu úložiště virtuálního počítače, který se používá pro okamžité obnovení. Přírůstkový snímek znamená, že místo obsazené snímek se rovná místo obsazené stránky, které jsou zapsány po snímek byl vytvořen. Fakturace je stále pro za GB využité místo obsazené snímek a cena za GB je stejná jako je uvedeno na [stránce ceny](https://azure.microsoft.com/pricing/details/managed-disks/). U virtuálních počítačů, které používají nespravované disky, snímky lze zobrazit v nabídce pro soubor Virtuálního pevného disku každého disku. Pro spravované disky jsou snímky uloženy v prostředku kolekce bodů obnovení v určené skupině prostředků a samotné snímky nejsou přímo viditelné.

>[!NOTE]
> Uchovávání snímků je pro týdenní zásady fixováno na 5 dní.

## <a name="configure-snapshot-retention"></a>Konfigurace uchovávání snímků

### <a name="using-azure-portal"></a>Pomocí webu Azure Portal

Na webu Azure Portal se v okně Zásad zálohování virtuálních zařízení v části **Okamžité obnovení** zobrazí pole přidané v okně **Zásady zálohování virtuálních** zařízení. Dobu uchovávání snímků můžete změnit z okna **Zásad zálohování virtuálních zařízení** pro všechny virtuální počítače přidružené k konkrétní zásadě zálohování.

![Možnost okamžitého obnovení](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

>[!NOTE]
> Od prostředí Az PowerShell verze 1.6.0 můžete aktualizovat dobu okamžitého obnovení snímku v zásadách pomocí prostředí PowerShell

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Výchozí uchovávání snímků pro každou zásadu je nastavena na dva dny. Uživatel může změnit hodnotu na minimálně 1 a maximálně pět dní. Pro týdenní zásady uchovávání snímků je stanovena na pět dní.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Jaké jsou náklady důsledky okamžité obnovení?

Snímky jsou uloženy spolu s disky, aby se urychlilo vytváření bodů obnovení a obnovení operací. V důsledku toho uvidíte náklady na úložiště, které odpovídají uchovávání snímků vybrané jako součást zásad zálohování virtuálních počítače.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>V účtech úložiště Premium zabírají snímky pořízené pro bod okamžitého obnovení limit snímků 10 TB?

Ano, pro účty úložiště premium snímky pořízené pro bod okamžité obnovení zabírají 10 TB přiděleného místa snímku.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Jak funguje uchovávání snímků během pětidenního období?

Každý den je pořízen nový snímek, pak existuje pět jednotlivých přírůstkové snímky. Velikost snímku závisí na konve dat, které jsou ve většině případů kolem 2%-7%.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Je snímek okamžitého obnovení přírůstkový snímek nebo úplný snímek?

Snímky pořízené jako součást funkce okamžitého obnovení jsou přírůstkové snímky.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Jak lze vypočítat přibližné zvýšení nákladů v důsledku funkce okamžitého obnovení?

Záleží na konve virtuálního počítači. V ustáleném stavu můžete předpokládat, že zvýšení nákladů je = doba uchovávání snímků denní konve na náklady na úložiště virtuálního počítače za GB.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Pokud je typ obnovení pro bod obnovení "Snímek a trezor" a provést operaci obnovení, který typ obnovení se použije?

Pokud je typ obnovení "snímek a trezor", obnovení se automaticky provede z místního snímku, který bude mnohem rychlejší ve srovnání s obnovením provedeném z úložiště.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Co se stane, když zvolím dobu uchování bodu obnovení (úroveň 2) menší než doba uchování snímku (Tier1)?

Nový model neumožňuje odstranění bodu obnovení (Tier2), pokud snímek (Tier1) je odstraněn. Doporučujeme naplánovat bod obnovení (Tier2) retenční období větší než období uchování snímku.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Proč můj snímek existuje i po nastavené retenční období v zásadách zálohování?

Pokud bod obnovení má snímek a to je nejnovější RP k dispozici, je zachována až do doby, kdy je další úspěšné zálohování. To je podle navržených zásad "uvolňování paměti" (GC) dnes, která nařizuje alespoň jeden nejnovější RP být vždy přítomen v případě, že všechny zálohy dále na selhání z důvodu problému ve virtuálním počítači. V normálních případech jsou rps vyčištěny maximálně 24 hodin po vypršení jejich platnosti.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Nepotřebuji funkci okamžitého obnovení. Může být zakázán?

Funkce okamžitého obnovení je povolena pro všechny a nelze ji zakázat. Můžete snížit uchovávání snímků na minimálně jeden den.

>[!NOTE]
> **Azure Backup teď podporuje selektivní zálohování a obnovení disku pomocí řešení zálohování virtuálního počítače Azure.**
>
>Azure Backup dnes podporuje zálohování všech disků (operačního systému a dat) ve virtuálním počítači společně pomocí řešení zálohování virtuálního počítače. S funkcí vyloučit disk, získáte možnost zálohovat jeden nebo několik z mnoha datových disků ve virtuálním počítače. To poskytuje efektivní a nákladově efektivní řešení pro potřeby zálohování a obnovení. Každý bod obnovení obsahuje data disků zahrnutých do operace zálohování, což dále umožňuje obnovit podmnožinu disků z daného bodu obnovení během operace obnovení. To platí pro obnovení ze snímku a úložiště.
>
>**Chcete-li se zaregistrovat do náhledu, napište nám naAskAzureBackupTeam@microsoft.com**
