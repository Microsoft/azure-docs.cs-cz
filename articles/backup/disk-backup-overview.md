---
title: Přehled zálohování disku Azure
description: Přečtěte si o řešení zálohování disku Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: fea0dd9d01bdc7c31d724cedd89d1fe6891c650a
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2021
ms.locfileid: "98557531"
---
# <a name="overview-of-azure-disk-backup-in-preview"></a>Přehled služby Azure disk Backup (ve verzi Preview)

>[!IMPORTANT]
>Služba Azure disk Backup je ve verzi Preview bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Informace o dostupnosti oblastí najdete v tématu [matice podpory](disk-backup-support-matrix.md).
>
>[Vyplňte tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) a zaregistrujte se do verze Preview.

Zálohování na disk Azure je nativní cloudové řešení zálohování, které chrání vaše data ve službě Managed disks. Je to jednoduché, bezpečné a nákladově efektivní řešení, které umožňuje nakonfigurovat ochranu pro spravované disky v několika krocích. Zaručuje, že můžete obnovit data v případě havárie.

Azure disk Backup nabízí řešení klíč, které poskytuje správu životního cyklu snímků pro spravované disky tím, že automatizuje pravidelné vytváření snímků a uchovává je po nakonfigurovanou dobu pomocí zásad zálohování. Můžete spravovat snímky disků s nulovými náklady na infrastrukturu a nemusíte mít vlastní skriptování ani nároky na správu. Jedná se o řešení zálohování konzistentní vzhledem k selháním, které využívá přírůstkové zálohování spravovaného disku pomocí [přírůstkových snímků](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots) s podporou více záloh za den. Je to také řešení bez agentů a nemá vliv na výkon produkčních aplikací. Podporuje zálohování a obnovení disků s operačním systémem i datové disky (včetně sdílených disků), ať už jsou aktuálně připojené ke spuštěnému virtuálnímu počítači Azure.

Pokud vyžadujete zálohování virtuálního počítače konzistentní vzhledem k aplikacím, včetně datových disků, nebo možností obnovení celého virtuálního počítače ze zálohy, obnovení souboru nebo složky nebo obnovení do sekundární oblasti, použijte řešení [zálohování virtuálních počítačů Azure](backup-azure-vms-introduction.md) . Azure Backup nabízí souběžnou podporu pro zálohování spravovaných disků pomocí zálohování disku kromě řešení [zálohování virtuálních počítačů Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) . To je užitečné, když budete potřebovat jednorázově konzistentní zálohy virtuálních počítačů, které jsou konzistentní vzhledem k aplikacím, a taky častější zálohování disků s operačním systémem nebo konkrétního datového disku, který je v souladu s chybou, a nemá vliv na výkon produkční aplikace.

Služba Azure disk Backup je integrovaná do služby Backup Center, která v Azure poskytuje **jednotné jednotné prostředí pro správu** , které umožňuje podnikům řídit, monitorovat, provozovat a analyzovat zálohy ve velkém měřítku.

## <a name="key-benefits-of-disk-backup"></a>Klíčové výhody zálohování disku

Azure disk Backup je řešení konzistentní bez agentů a neúspěšného selhání, které používá [přírůstkové snímky](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots) a nabízí následující výhody:

- Častější a rychlé zálohování bez přerušení virtuálního počítače.
- Nemá vliv na výkon produkční aplikace.
- Žádné problémy se zabezpečením, protože nevyžadují spouštění vlastních skriptů nebo instalace agentů.
- Cenově výhodné řešení pro zálohování konkrétních disků v porovnání s zálohováním celého virtuálního počítače.

Řešení zálohování disku Azure je užitečné v následujících scénářích:

- Nutnost častých záloh za den bez quiescent aplikací
- Aplikace spuštěné v clusteru: zápis do sdílených disků v clusterech s podporou převzetí služeb při selhání Windows serveru i v clusterech systému Linux
- Konkrétní nutnost zálohování bez agentů z důvodu zabezpečení nebo vlivu na výkon aplikace
- Zálohování virtuálního počítače s konzistentní aplikací se nedá provést, protože obchodní aplikace nepodporují služba Stínová kopie svazku (VSS).

Službu Azure disk Backup zvažte ve scénářích, kde:

- kritická aplikace je spuštěná na virtuálním počítači Azure, který požaduje více záloh za den, aby splňovala cíl bodu obnovení, ale bez dopadu na produkční prostředí nebo výkon aplikace.
- vaše organizace nebo oborové nařízení omezuje instalaci agentů z důvodu obav zabezpečení
- spuštění vlastních předzálohovacích skriptů a vyvolání zablokování a rozmrazení na virtuálních počítačích se systémem Linux pro získání zálohy konzistentní s aplikacemi přináší nepatřičná režie na produkční úlohy.
- aplikace s využitím kontejnerů, které běží na službě Azure Kubernetes (AKS cluster), používají jako trvalé úložiště spravované disky. V současné době je nutné zálohovat spravovaný disk prostřednictvím skriptů automatizace, které je obtížné spravovat.
- spravovaný disk je držitelem důležitých podnikových dat, používá se jako sdílená složka nebo obsahuje záložní soubory databáze a vy chcete optimalizovat náklady na zálohování, protože se neinvesticí do zálohování virtuálních počítačů Azure.
- Máte spoustu virtuálních počítačů s jedním diskem s operačním systémem Linux a Windows (tj. virtuální počítač, který má jenom disk s operačním systémem a žádné připojené datové disky), který hostuje servery webserver nebo State, a slouží jako přípravné prostředí s nastavením konfigurace aplikace a k ochraně disku s operačním systémem potřebujete nákladově efektivní řešení zálohování. Například pro aktivaci rychlého zálohování na vyžádání před upgradem nebo opravou virtuálního počítače
- na virtuálním počítači je spuštěná konfigurace operačního systému, kterou řešení zálohování virtuálních počítačů Azure nepodporuje (například Windows 2008 32-bit Server).

## <a name="how-the-backup-and-restore-process-works"></a>Jak proces zálohování a obnovení funguje

- Prvním krokem při konfiguraci zálohování sdílených složek Azure je vytvoření [trezoru záloh](backup-vault-overview.md). Trezor poskytuje konsolidované zobrazení záloh konfigurovaných napříč různými úlohami.

- Pak vytvořte zásady zálohování, které vám umožní nakonfigurovat četnost zálohování a dobu uchování.

- Pokud chcete nakonfigurovat zálohování, použijte úložiště záloh, přiřaďte zásadu zálohování, vyberte spravovaný disk, který se má zálohovat, a zadejte skupinu prostředků, ve které se mají snímky ukládat a spravovat. Azure Backup automaticky spustí naplánované úlohy zálohování, které vytvoří přírůstkový snímek disku podle četnosti zálohování. Starší snímky se odstraní v závislosti na době uchování, kterou určuje zásada zálohování.

- Azure Backup používá [přírůstkové snímky](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) spravovaného disku. Přírůstkové snímky představují cenově výhodné zálohování spravovaných disků v čase, které se fakturují za rozdílové změny na disku od posledního snímku. Vždycky se ukládají do nejefektivnějšího úložiště na úrovni Standard HDD bez ohledu na typ úložiště nadřazených disků. První snímek disku zabere použitou velikost disku a po sobě jdoucí přírůstkové snímky uloží na disk rozdílové změny od posledního snímku.

- Jakmile nakonfigurujete zálohu spravovaného disku, vytvoří se instance zálohování v trezoru záloh. Pomocí instance Backup můžete najít stav operací zálohování, aktivovat zálohování na vyžádání a provádět operace obnovení. Můžete také zobrazit stav zálohování napříč několika trezory a instancemi zálohování pomocí centra zálohování, které poskytuje jediné podokno se skleněným zobrazením.

- Chcete-li obnovit, stačí vybrat bod obnovení, ze kterého chcete disk obnovit. Zadejte skupinu prostředků, ve které se má obnovený disk vytvořit ze snímku. Azure Backup poskytuje možnosti okamžitého obnovení, protože snímky se ukládají místně v rámci vašeho předplatného.

- Úložiště záloh používá spravovanou identitu pro přístup k dalším prostředkům Azure. Ke konfiguraci zálohy spravovaného disku a obnovení z minulé zálohy vyžaduje spravovaná identita trezoru služby Backup sadu oprávnění na zdrojovém disku, skupinu prostředků snímku, kde se vytvářejí a spravují snímky, a cílovou skupinu prostředků, ve které chcete zálohu obnovit. Pomocí řízení přístupu na základě role Azure (RBAC) můžete udělit oprávnění ke spravované identitě. Spravovaná identita je instanční objekt speciálního typu, který se dá použít jenom s prostředky Azure. Přečtěte si další informace o [spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

- Služba Azure disk Backup v současné době podporuje provozní zálohování spravovaných disků a nekopíruje zálohy do úložiště trezorů záloh. Podrobný seznam podporovaných a nepodporovaných scénářů a dostupnost oblastí najdete v tabulce [podpory](disk-backup-support-matrix.md).

## <a name="pricing"></a>Ceny

Azure Backup nabízí řešení pro správu životního cyklu snímků pro ochranu disků Azure. Snímky disku vytvořené pomocí Azure Backup se ukládají do skupiny prostředků v rámci vašeho předplatného Azure a účtují poplatky za **úložiště snímků** . Další podrobnosti o cenách snímků najdete na stránce s [cenami služby Managed disk](https://azure.microsoft.com/pricing/details/managed-disks/) . Vzhledem k tomu, že se snímky nekopírují do trezoru služby Backup, Azure Backup neúčtuje poplatek za **chráněnou instanci** a náklady na **úložiště zálohy** se nepoužijí. Kromě toho přírůstkové snímky zabírají rozdílové změny od posledního snímku a jsou vždy uložené ve standardním úložišti bez ohledu na typ úložiště nadřazených disků a účtují se podle ceny standardního úložiště. Díky tomu bude Azure disk Backup finančně efektivním řešením.

## <a name="next-steps"></a>Další kroky

- [Matice podpory Azure disk Backup](disk-backup-support-matrix.md)
