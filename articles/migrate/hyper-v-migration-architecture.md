---
title: Jak funguje migrace Hyper-V s migrací serveru migrace Azure? | Dokumenty Microsoft
description: Poskytuje přehled migrace technologie Hyper-V do Azure migrovat migrace serveru
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811710"
---
# <a name="how-does-hyper-v-replication-work"></a>Jak funguje replikace Hyper-V?

Tento článek obsahuje přehled architektury a procesy používané při migraci virtuálních počítačů Hyper-V pomocí nástroje Azure Migrate serveru migrace.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, vyhodnocení a migraci místních aplikací a úloh a soukromého a veřejného cloudu virtuálních počítačů do Azure. Centrum poskytuje Azure Migrate nástroje pro vyhodnocení a migrace, jakož i nabídky softwaru třetích stran výrobce (ISV).

## <a name="agentless-migration"></a>Migrace bez agenta

Nástroj pro migraci serveru migrace Azure poskytuje bez agentů replikace místních virtuálních počítačů Hyper-V, pomocí pracovní postup migrace, který je optimalizovaný pro Hyper-V. Instalace softwaru agenta jenom na hostitelích Hyper-V nebo uzlech clusteru. Nic musí být nainstalovaný na virtuálních počítačích Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migrace serveru a Azure Site Recovery

Migrace serveru do Azure migrovat je nástroj pro migraci místních úloh a virtuální počítače založené na cloudu, do Azure. Site Recovery je nástroj pro obnovení po havárii. Nástroje sdílet některé běžné součásti technologii používanou k replikaci dat, ale slouží k jiným účelům. 


## <a name="architectural-components"></a>Komponenty architektury

![Architektura](./media/hyper-v-replication-architecture/architecture.png)



**Komponenta** | **Nasazení** | 
--- | --- 
**Zprostředkovatel replikace** | Zprostředkovatele Microsoft Azure Site Recovery je nainstalovaný na hostitelích Hyper-V a zaregistrovaného na migraci serveru migrace Azure.<br/> Zprostředkovatel orchestruje replikaci pro virtuální počítače Hyper-V.
**Recovery Services agent** | Agent služby Microsoft Azure Recovery zpracovává replikační data. Funguje s daným poskytovatelem a replikace dat z virtuálních počítačů Hyper-V do Azure.<br/> Replikovaná data se nahraje do účtu úložiště ve vašem předplatném Azure. Migrace serveru nástroj procesy replikovaná data a platí pro disky repliky v rámci předplatného. Disky repliky se používají k vytvoření virtuálních počítačů Azure po migraci.

- Součásti instaluje jeden instalační soubor, stáhnout z Azure Migrate migrace serveru na portálu.
- Zprostředkovatel a zařízení využívat odchozí připojení port 443 protokolu HTTPS pro komunikaci s Azure Migrate migrace serveru.
- Komunikace z poskytovatele i agenta je zabezpečená a šifrovaná.


## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro virtuální počítač Hyper-V, počáteční replikace začne.
2. Se pořídí snímek virtuálního počítače Hyper-V.
3. Virtuální pevné disky na virtuálním počítači jsou replikované jeden po druhém, dokud se všechny nezkopírují do Azure. Čas počáteční replikace závisí na velikosti virtuálního počítače a šířky pásma sítě.
4. Změny na disku, ke kterým dochází při počáteční replikace jsou sledovány pomocí repliky technologie Hyper-V a uloženy v souborech protokolu (hrl soubory).
    - Soubory protokolů jsou ve stejné složce jako disky.
    - Každý disk má přidružený hrl soubor, který se odešle do sekundárního úložiště.
    - Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se snímek virtuálního počítače odstraní a zahájí rozdílová replikace.
5. Disk přírůstkové změny jsou sledovány v hrl soubory. Protokoly replikace se pravidelně nahraje do účtu služby Azure storage pomocí agenta služby Recovery Services.


## <a name="performance-and-scaling"></a>Výkon a škálování

Výkon replikace pro Hyper-V je ovlivněno faktorů, které zahrnují velikost virtuálního počítače, četnost změn dat (změn) virtuálních počítačů, dostupné místo na hostiteli Hyper-V pro úložiště souborů protokolu, nahrání šířku pásma pro replikaci dat a úložiště v cíli v Azure.

- Pokud replikujete více počítačů současně, použijte [Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) pro Hyper-V, které vám pomůžou optimalizovat replikaci.
- Plánování vašeho replikace Hyper-V a distribuovat replikace do účtů úložiště Azure v souladu s kapacitou.

### <a name="control-upload-throughput"></a>Propustnost odesílání ovládacího prvku

Můžete omezit šířku pásma používaného k odesílání dat do Azure na každém hostiteli Hyper-V. Dej si pozor. Pokud nastavíte příliš nízké hodnoty bude mít nepříznivý dopad replikace a zpoždění migrace.


1. Přihlaste se k uzlu hostitele nebo clusteru Hyper-V.
2. Spustit **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, chcete-li otevřít modul snap-in Windows Azure Backup konzoly MMC.
3. V modulu snap-in, vyberte **změnit vlastnosti**.
4. V **omezování**vyberte **Povolit omezování šířky pásma Internetu u operací zálohování**. Nastavte limity pro pracovní a nepracovní dobu. Platné rozsahy jsou 512 kB/s na 1,023 MB/s.
I

### <a name="influence-upload-efficiency"></a>Vliv nahrávání efektivity

Pokud máte přebytečné šířky pásma pro replikaci a chcete zvýšit nahrávání, můžete zvýšit počet vláken přidělené pro úlohu nahrávání následujícím způsobem:

1. Otevřete registr s Regedit.
2. Přejděte na klíč HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Zvyšte hodnotu pro počet vláken pro nahrávání dat pro každý replikující virtuální počítač. Výchozí hodnota je 4 a maximální hodnota je 32. 




## <a name="next-steps"></a>Další postup

Vyzkoušejte si [migrace Hyper-V](tutorial-migrate-hyper-v.md) pomocí Azure Migrate serveru migrace.
