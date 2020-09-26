---
title: Jak funguje migrace Hyper-V v Azure Migrate?
description: Další informace o migraci technologie Hyper-V pomocí Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 390a8a49e9a47ee5e6845d85fe4fe02f514708e8
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362193"
---
# <a name="how-does-hyper-v-replication-work"></a>Jak funguje replikace Hyper-V?

Tento článek poskytuje přehled architektury a procesů, které se používají při migraci virtuálních počítačů Hyper-V pomocí nástroje pro migraci Azure Migrate serveru.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální centrum pro sledování zjišťování, hodnocení a migrace vašich místních aplikací a úloh a virtuálních a veřejných cloudových virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro posuzování a migraci i nabídky nezávislého výrobce softwaru (ISV) od jiných výrobců.

## <a name="agentless-migration"></a>Migrace bez agentů

Nástroj pro migraci Azure Migrate serveru poskytuje replikaci bez agentů pro místní virtuální počítače Hyper-V s využitím pracovního postupu migrace optimalizovaného pro Hyper-V. Softwarový agent nainstalujete jenom na hostitele Hyper-V nebo na uzly clusteru. Na virtuálních počítačích Hyper-V není potřeba nic instalovat.

## <a name="server-migration-and-azure-site-recovery"></a>Migrace serveru a Azure Site Recovery

Migrace Azure Migrate serveru je nástroj pro migraci místních úloh a cloudových virtuálních počítačů do Azure. Site Recovery je nástroj pro zotavení po havárii. Tyto nástroje sdílí některé společné technologické komponenty, které se používají pro replikaci dat, ale slouží k různým účelům. 


## <a name="architectural-components"></a>Komponenty architektury

![Diagram znázorňuje zdrojovou síť Hyper-V s datovým kanálem H T T P S, na Microsoft Azure s podrobnostmi popsanými v tabulce.](./media/hyper-v-replication-architecture/architecture.png)



**Komponenta** | **Nasazení** | 
--- | --- 
**Zprostředkovatel replikace** | Poskytovatel Microsoft Azure Site Recovery je nainstalovaný na hostitelích Hyper-V a zaregistrovaný pomocí migrace serveru migrace Azure.<br/> Zprostředkovatel orchestruje replikaci pro virtuální počítače Hyper-V.
**Agent Recovery Services** | Agent služby obnovení Microsoft Azure zpracovává replikaci dat. Spolupracuje se zprostředkovatelem a replikuje data z virtuálních počítačů Hyper-V do Azure.<br/> Replikovaná data se nahrají do účtu úložiště ve vašem předplatném Azure. Nástroj pro migraci serveru zpracuje replikovaná data a použije je na disky replik v rámci předplatného. Disky repliky se používají k vytvoření virtuálních počítačů Azure při migraci.

- Komponenty se instalují pomocí jediného instalačního souboru, který se stáhne z Azure Migrate migrace serveru na portálu.
- Zprostředkovatel a zařízení používají odchozí připojení portu HTTPS 443 ke komunikaci s migrací serveru Azure Migrate.
- Komunikace mezi poskytovatelem a agentem je zabezpečená a šifrovaná.


## <a name="replication-process"></a>Proces replikace

1. Pokud povolíte replikaci pro virtuální počítač Hyper-V, začne počáteční replikace.
2. Povede se snímek virtuálního počítače Hyper-V.
3. Virtuální pevné disky virtuálního počítače se replikují jednou po jednom, dokud se všechny nezkopírují do Azure. Čas počáteční replikace závisí na velikosti virtuálního počítače a šířce pásma sítě.
4. Změny disků, ke kterým došlo během počáteční replikace, jsou sledovány pomocí repliky technologie Hyper-V a uloženy v souborech protokolu (soubory HRL).
    - Soubory protokolu jsou ve stejné složce jako disky.
    - Každý disk má přidružený soubor HRL, který se odesílá do sekundárního úložiště.
    - Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní a spustí se rozdílová replikace.
5. Přírůstkové změny disků jsou sledovány v souborech HRL. Agenti Recovery Services pravidelně odesílají protokoly replikace do účtu služby Azure Storage.


## <a name="performance-and-scaling"></a>Výkon a škálování

Výkon replikace pro Hyper-V je ovlivněný faktory, které zahrnují velikost virtuálního počítače, rychlost změny dat (změny) virtuálních počítačů, dostupné místo na hostiteli Hyper-V pro úložiště souborů protokolu, nahrávání šířky pásma pro data replikace a cílové úložiště v Azure.

- Pokud provádíte replikaci více počítačů najednou, použijte [Plánovač nasazení služby Azure Site Recovery](../site-recovery/hyper-v-deployment-planner-overview.md) pro Hyper-V, abyste mohli optimalizovat replikaci.
- Plánování replikace technologie Hyper-V a distribuce replikace přes účty úložiště Azure v souladu s kapacitou.

### <a name="control-upload-throughput"></a>Řízení propustnosti odesílání

Šířku pásma používanou k nahrávání dat do Azure můžete omezit na každého hostitele Hyper-V. Dej si pozor. Pokud nastavíte příliš nízkou hodnotu, bude mít nepříznivý vliv na replikaci a zpoždění migrace.


1. Přihlaste se k hostiteli nebo uzlu clusteru technologie Hyper-V.
2. Spuštěním složky **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**otevřete modul snap-in Windows Azure Backup MMC.
3. V modulu snap-in vyberte **změnit vlastnosti**.
4. V případě **omezení**vyberte možnost **Povolit omezování šířky pásma internetu u operací zálohování**. Nastavte limity pro pracovní a nepracovní dobu. Platné rozsahy jsou od 512 do 1 023 MB/s.
I

### <a name="influence-upload-efficiency"></a>Efektivita nahrávání vlivu

Pokud máte volnou šířku pásma pro replikaci a chcete zvýšit nahrávání, můžete zvýšit počet vláken přidělených pro úlohu odeslání následujícím způsobem:

1. Otevřete registr pomocí příkazu regedit.
2. Přejděte na Key HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Zvyšte hodnotu počtu vláken používaných pro nahrávání dat pro každý virtuální počítač repliky. Výchozí hodnota je 4 a maximální hodnota je 32. 




## <a name="next-steps"></a>Další kroky

Vyzkoušejte [migraci technologie Hyper-V](tutorial-migrate-hyper-v.md) pomocí migrace serveru Azure Migrate.
