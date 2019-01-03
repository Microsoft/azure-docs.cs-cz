---
title: Běžné otázky – zotavení po havárii Azure do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek shrnuje běžné otázky při nastavování zotavení po havárii virtuálních počítačů Azure do aonther oblasti Azure pomocí Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969943"
---
# <a name="common-questions---azure-to-azure-replication"></a>Časté otázky – replikace z Azure do Azure

Tento článek obsahuje odpovědi na běžné dotazy, které můžeme vidět, pokud nasazení zotavení po havárii virtuálních počítačů Azure do jiné oblasti Azure. Pokud po přečtení tohoto článku máte dotazy, zveřejněte na [fóru Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Obecné
### <a name="how-is-site-recovery-priced"></a>Jak se účtuje Site Recovery?
Kontrola [ceny za Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) podrobnosti.

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>Jak nakonfigurovat Site Recovery na virtuálních počítačích Azure. Co jsou doporučené postupy?
1. [Vysvětlení architektury Azure do Azure](azure-to-azure-architecture.md)
2. [Zkontrolujte podporované a nepodporované konfigurace](azure-to-azure-support-matrix.md)
3. [Nastavení zotavení po havárii pro virtuální počítače Azure](azure-to-azure-how-to-enable-replication.md)
4. [Spuštění testovacího převzetí služeb při selhání](azure-to-azure-tutorial-dr-drill.md)
5. [Převzetí služeb při selhání a navrácení služeb po obnovení do primární oblasti](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Replikace

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>Můžete replikovat Azure virtuální počítače povolit šifrování na disku?
Ano, je možné replikovat. Přečtěte si [článku](azure-to-azure-how-to-enable-replication-ade-vms.md) povolit virtuální počítače s povolenou replikací z Azure disk encryption (ADE). Mějte prosím na paměti, že jsou Azure Site Recovery aktuálně podporuje pouze virtuální počítače Azure s Windows operačním systémem a povoleno šifrování s aplikací Azure AD.

### <a name="can-i-replicate-vms-to-another-subscription"></a>Můžete replikovat virtuální počítače do jiného předplatného?
Ano, můžete replikovat virtuální počítače Azure do jiného předplatného se ve stejném tenantovi Azure Active Directory.
Konfigurace zotavení po Havárii [napříč předplatnými](https://azure.microsoft.com/blog/cross-subscription-dr) je jednoduché. Můžete vybrat jiné předplatné v době replikace.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Můžete replikovat virtuální počítače Azure zóny připnuté do jiné oblasti.
Ano, můžete [replikace virtuálních počítačů zóny připnuté](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) do jiné oblasti.

### <a name="can-i-exclude-disks"></a>Vyloučení disků

Ano, můžete vyloučit disky v době provedení ochrany pomocí prostředí PowerShell. Přečtěte si [pokyny k powershellu](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) pro vyloučení disku

###<a name="how-often-can-i-replicate-to-azure"></a>Jak často můžete replikovat do Azure?
Replikace je souvislý při replikaci virtuálních počítačů Azure do jiné oblasti Azure. Zkontrolujte, [Azure do Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process) architektura replikace pochopit podrobnosti.

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>Můžete replikovat virtuální počítače v rámci stejné oblasti? Je třeba to při migraci virtuálních počítačů?
Řešení zotavení po Havárii Azure do Azure nelze použít pro replikaci virtuálních počítačů v rámci stejné oblasti.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>Můžete replikovat virtuální počítače do libovolné oblasti Azure?
Pomocí služby Site Recovery můžete replikovat a obnovovat virtuální počítače mezi všechny dvou oblastí ve stejné zeměpisné clusteru. Geografické clustery jsou definovány dodržujte při tom suverenita a data latence. Další informace najdete v tématu Site Recovery [systém podpory replikace z oblasti](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support).

### <a name="does-site-recovery-require-internet-connectivity"></a>Site Recovery vyžaduje připojení k Internetu?

Site Recovery Ne, nevyžaduje připojení k Internetu, ale přístup k adresám URL služby Site Recovery a IP rozsahy jak je uvedeno v tomto [článku](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)

## <a name="replication-policy"></a>Zásady replikace

### <a name="what-is-a-replication-policy"></a>Co jsou zásady replikace?
Definuje nastavení pro obnovení bodu uchování historie a aplikace konzistentní frekvence pořizování snímků. Ve výchozím nastavení Azure Site Recovery vytvoří novou zásadu replikace s výchozím nastavením ' 24 hodin pro uchování bodu obnovení a "60 minut, než se frekvence snímků konzistentní vzhledem k aplikacím aplikace.

### <a name="what-is-crash-consistent-recovery-point"></a>Co je bod obnovení konzistentní pro případ chyby?
Bod obnovení konzistentní pro případ chyby představuje data na disku, jako kdyby došlo k chybě virtuální počítač nebo napájecí kabel byl stažen ze serveru v době pořízení snímku. Neměl by zahrnovat cokoli, co byl v paměti při pořízení snímku. V současné době většina aplikací můžete obnovit také ze snímky konzistentní s havárií. Bod obnovení konzistentní při selhání je dostatečně obvykle pro žádné databáze operační systémy a aplikace jako souborové servery, servery DHCP, tiskových serverů a tak dále.

### <a name="what-is-application-consistent-recovery-point"></a>Co je bod obnovení s konzistentní aplikací? 
Body obnovení konzistentní se vytvoří z konzistentní snímky, které zaznamenávají stejná data jako snímky konzistentní při selhání, a uveďte všechna data v paměti a všechny probíhající transakce. Kvůli další obsah snímky konzistentními se nejvíce podílejí a trvat nejdéle provádět. Body obnovení konzistentní se doporučují pro databázi operačních systémů a aplikací, jako jsou SQL.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak se body obnovení vygenerovat a uložit?
Pochopit, jak Site Recovery vytvoří body obnovení umožní trochu zásad replikace, který má okno uchování 24 hodin a snímek konzistentní vzhledem k aplikacím frekvence aplikace 1 hodina bodu obnovení.
Selhání konzistentního bodu každých 5 minut a uživatel nemá žádný ovládací prvek, který chcete změnit četnost odesílání nastavení vytvoří Site Recovery. Proto se za posledních hodinová uživatel bude mít 12 bodů konzistentní pro případ chyby a 1 bod konzistentní vzhledem k aplikaci lze vybírat. V průběhu času, Site Recovery vyřadí všechny body obnovení za poslední 1 hodinu a uložit pouze jeden obnovení bodu za hodinu.
Pro ilustraci v následujícím snímku obrazovky:


1. Dobu kratší než poslední 1 hodinu se body obnovení s frekvencí 5 minut.
2. Pro čas za poslední 1 hodinu vidíme, že se ukládají pouze jeden bod obnovení za hodinu.

  ![generování body obnovení](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Jak daleko mohou obnovit?
Nejstarší bod obnovení, která vám pomůže je 72 hodin.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>Co se stane, pokud mám zásady replikace 24 hodin a neexistuje žádné body generování obnovení z důvodu problému déle než 24 hodin. Je Moje předchozí obnovení se vyřazují body?
Ne, Site Recovery zachovají všechny předchozí body obnovení v tomto případě. 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Po povolení replikace na virtuálním počítači, jak změním zásady replikace? 
Přejděte na trezor Site Recovery > infrastruktura Site Recovery > Zásady replikace. Vyberte zásadu, kterou chcete upravit a uložit změny. Všechny změny se uplatní na všechny stávající replikace příliš. 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>Všechny body obnovení jsou úplnou kopii virtuálního počítače nebo rozdílové?
V případě počáteční replikace první bod obnovení, který získá vygenerována bude mít úplnou kopii a všechny body obnovení po sobě jdoucích bude mít rozdílové změny.

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>Prodloužení interval uchovávání bodů obnovení zvyšuje požadavky náklady na úložiště?
Ano, je-li zvýšit dobu uchování 24 hodin až 72 hodin uložte Site Recovery se body obnovení pro přidání 48 hodin, které se účtují vám poplatky za úložiště. Například pokud je bod obnovení jednoho rozdílové změny na 10 GB a za gigabajt je 0.16 $ za měsíc, pak by se jednat o $1.6 * 48 další poplatky za měsíc.

## <a name="failover"></a>Převzetí služeb při selhání

### <a name="is-failover-automatic"></a>Je převzetí služeb při selhání automatické?

Převzetí služeb při selhání není automatické. Zahajte převzetí služeb při selhání s jedním kliknutím na portálu nebo pomocí Site Recovery [Powershellu](azure-to-azure-powershell.md) k aktivaci převzetí služeb při selhání. 

### <a name="can-i-retain-public-ip-address-after-failover"></a>Můžete zachovat veřejné IP adresy po převzetí služeb při selhání?

Veřejnou IP adresu produkční aplikace **nelze uchovávat v převzetí služeb při selhání**. Úlohy, které aktivují jako součást procesu převzetí služeb při selhání musí být přiřazena veřejná IP adresa Azure prostředek k dispozici v cílové oblasti. Tento krok lze provést buď ručně, nebo je automatické s plány obnovení. Přečtěte si [článku](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan) přiřadit veřejnou IP adresu pomocí plán obnovení.  

### <a name="can-i-retain-private-ip-address-during-failover"></a>Během převzetí služeb při selhání, zachovat privátní IP adresu?
Ano, můžete zachovat privátní IP adresu. Ve výchozím nastavení Pokud povolíte obnovení po havárii pro virtuální počítače Azure Site Recovery vytvoří cílové prostředky na základě zdroje prostředků nastavení. Pro Azure: virtuální počítače nakonfigurované se statickými IP adresami Site Recovery se pokusí zřízení stejnou IP adresu pro cílový virtuální počítač, pokud není používán. Přečtěte si [článku](site-recovery-retain-ip-azure-vm-failover.md) chcete zachovat privátní IP adresou v různých podmínkách.

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>Po převzetí služeb při selhání server stejnou IP adresu jako zdrojový virtuální počítač nemá. Proč je přiřadit novou IP adresu?

Site Recovery se pokusí zadat IP adresu na jak kapacita systému dovolí v okamžiku převzetí služeb při selhání. V případě, že je převáděna jiným virtuálním počítačem, nastavit je jako cíl další dostupnou IP adresu. Úplné vysvětlení způsobu, jakým Site Recovery zpracovává adresování [k tomuto článku.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms)

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>Co dělá nejnovější verzi (nejnižší cíl bodu obnovení) obnovení odkazuje znamená, že?
Tato možnost nejprve zpracuje všechna data, ke které byl odeslán do služby Site Recovery, chcete-li vytvořit bod obnovení pro každý virtuální počítač před přebírání služeb při selhání se. Tato možnost poskytuje nejnižší cíl bodu obnovení bodu obnovení (rpo), protože virtuální počítač vytvořen po převzetí služeb při selhání bude mít všechna data do Site Recovery replikovala při aktivaci převzetí služeb při selhání.

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>Má (nejnižší cíl bodu obnovení) nejnovější body obnovení dopad na RTO převzetí služeb při selhání?
Ano, Site Recovery bude zpracovávat všechny čekající data před přebírání služeb při selhání, tuto možnost mít vyšší hodnotu RTO porovnání s ostatními.

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>Co dělá nejnovější zpracované možnost ve střední body obnovení?
Tato možnost převezme služby při selhání všech virtuálních počítačů v plánu do nejnovějšího bodu obnovení zpracovanému službou Site Recovery. Pokud chcete zobrazit nejnovější obnovení bodu pro konkrétní virtuální počítač, zkontrolujte nejnovější body obnovení v nastavení virtuálního počítače. Tato možnost poskytuje nízkou plánovanou dobu obnovení (RTO), protože se neztrácí žádný čas zpracováním nezpracovaných dat.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Pokud replikuji mezi dvěma oblastmi Azure co se stane, když můj primární oblasti dojde k nečekanému výpadku?
Můžete aktivovat převzetí služeb při selhání po výpadek. Site Recovery nepotřebuje připojení z primární oblasti provést převzetí služeb při selhání.

## <a name="recovery-plan"></a>Plán obnovení

### <a name="what-is-a-recovery-plan-"></a>Co je plán obnovení?
Plány obnovení ve službě Site Recovery orchestrovat převzetí služeb při selhání virtuálních počítačů. Umožňuje konzistentně přesné, opakovatelných a automatizovaných provést obnovení. Plán obnovení řeší následující požadavky pro uživatele:

- Tento převzetí služeb při selhání definuje skupinu virtuálních počítačů dohromady.
- Definování závislosti mezi virtuálními počítači, takže aplikace se zobrazí přesně.
- Automatizace obnovení spolu s vlastní ručně prováděné akce tak, aby úlohy než převzetí služeb při selhání virtuálních počítačů můžete také dosáhnout.

[Další informace](site-recovery-create-recovery-plans.md) plány obnovení.

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>Jak nepodporuje řazení je dosaženo plánu obnovení?

V plánu obnovení můžete vytvořit více skupin, abyste dosáhli sekvencování. Každé skupiny převzetí služeb při selhání najednou, což znamená, že virtuální počítače, které jsou součástí stejné skupiny se převzetí služeb při selhání společně jinou skupinu. Zkontrolujte, jak [modelu aplikace v plánu obnovení.](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps) 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Jak zjistím RTO plánu obnovení?
Pokud chcete zkontrolovat RTO plán obnovení, testovací převzetí služeb při selhání plánu obnovení a přejděte do úlohy Site Recovery.
Například jak je znázorněno níže, SAP, testovací plán obnovení trvalo 8 sekund 59 minut převzetí služeb při selhání všech virtuálních počítačů a provádět všechny akce zadané.

  ![Chyba com](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>Můžete přidat runbooky služby automation do plánu obnovení?
Ano, můžete integrovat runbooky Azure automation do plánu obnovení. [Další informace](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>Operace opětovného zapnutí ochrany a navrácení služeb po obnovení 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>Automaticky se chrání po převzetím služeb z primární oblasti do oblasti pro zotavení po havárii nepodporuje virtuální počítače v oblasti zotavení po Havárii?
Ne, když jste [převzetí služeb při selhání](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) virtuálních počítačů Azure z jedné oblasti do jiného, virtuální počítače spustit v oblasti zotavení po Havárii v nechráněném stavu. Chcete-li navrácení služeb po obnovení virtuálních počítačů do primární oblasti, budete muset [znovunastavení ochrany](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) virtuální počítače v sekundární oblasti.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>Během opětovného nastavování ochrany se Site Recovery replikovat kompletní data ze sekundární oblasti do primární oblasti?
To závisí na situaci, například pokud zdrojové oblasti virtuálních počítačů existují a jsou synchronizovány pouze změny provedené mezi zdrojový disk a cílový disk. Rozdíly jsou vypočítané porovnáním obou discích tak a pak přeneseny. To obvykle trvat několik hodin. Přečtěte si [článku]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) další podrobnosti o tom, co se stane během opětovného nastavování ochrany.

### <a name="how-much-time-does-it-take-to-failback"></a>Kolik času udělá provést navrácení služeb po obnovení?
Po dokončení opětovného nastavování ochrany obvykle je množství času, podobný převzetí služeb při selhání z primární oblasti do sekundární oblasti. 

## <a name="security"></a>Zabezpečení
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Posílají se replikační data do služby Site Recovery?
Ne, Site Recovery nezachycuje replikovaná data se nepodporuje a nemá žádné informace o co běží na virtuálních počítačích. Do služby Site Recovery se odesílají jen metadata, která jsou nezbytná k orchestraci replikace a převzetí služeb při selhání.  
Site Recovery je ISO 27001: 2013, 27018, HIPAA, DPA certifikace a probíhá SOC2 a FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>Šifruje Site Recovery replikaci?
Ano, jak šifrování během přenosu a [šifrování v Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) jsou podporovány.

## <a name="next-steps"></a>Další postup
* [Kontrola](azure-to-azure-support-matrix.md) požadavky na podporu.
* [Nastavit](azure-to-azure-tutorial-enable-replication.md) replikací z Azure do Azure.
