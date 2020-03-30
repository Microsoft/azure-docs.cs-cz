---
title: Jak migrace technologie Hyper-V funguje v Azure?
description: Další informace o migraci technologie Hyper-V pomocí migrace Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185877"
---
# <a name="how-does-hyper-v-replication-work"></a>Jak replikace technologie Hyper-V funguje?

Tento článek obsahuje přehled architektury a procesů používaných při migraci virtuálních počítačích Hyper-V pomocí nástroje migrace serveru Azure.

[Azure Migrate](migrate-services-overview.md) poskytuje centrální rozbočovač pro sledování zjišťování, hodnocení a migrace místních aplikací a úloh a virtuálních počítačích privátního/veřejného cloudu do Azure. Centrum poskytuje nástroje pro migraci Azure pro hodnocení a migraci, stejně jako nabídky nezávislého dodavatele softwaru (ISV) třetích stran.

## <a name="agentless-migration"></a>Migrace bez agenta

Nástroj migrace serveru Azure poskytuje replikaci bez agenta pro místní virtuální počítače Hyper-V pomocí pracovního postupu migrace, který je optimalizovaný pro technologie Hyper-V. Softwarový agent se instaluje pouze na hostitele hyper-V nebo uzly clusteru. Na virtuálních počítačích Hyper-V není nic nutné nainstalovat.

## <a name="server-migration-and-azure-site-recovery"></a>Migrace serveru a obnovení webu Azure

Migrace migrace serveru Azure je nástroj pro migraci místních úloh a cloudových virtuálních počítačích do Azure. Site Recovery je nástroj pro zotavení po havárii. Nástroje sdílejí některé běžné technologické součásti používané pro replikaci dat, ale slouží různým účelům. 


## <a name="architectural-components"></a>Komponenty architektury

![Architektura](./media/hyper-v-replication-architecture/architecture.png)



**Komponenta** | **Nasazení** | 
--- | --- 
**Zprostředkovatel replikace** | Poskytovatel obnovení webu Microsoft Azure je nainstalovaný na hostitelích Hyper-V a zaregistrovaný u migrace serveru Migrace Azure.<br/> Zprostředkovatel orchestruje replikaci pro virtuální aplikace Hyper-V.
**Agent služby obnovení** | Agent služby Microsoft Azure Recovery Service zpracovává replikaci dat. Spolupracuje s poskytovatelem replikovat data z virtuálních počítačů Hyper-V do Azure.<br/> Replikovaná data se nahrají do účtu úložiště ve vašem předplatném Azure. Nástroj pro migraci serveru zpracuje replikovaná data a použije je na repliky disků v předplatném. Repliky disky se používají k vytvoření virtuálních počítačů Azure při migraci.

- Komponenty se instalují jedním instalačním souborem staženým z migrace serveru Azure na portálu.
- Zprostředkovatel a zařízení používají odchozí připojení https port 443 ke komunikaci s migrací serveru Azure.
- Komunikace od poskytovatele a agenta je zabezpečená a šifrovaná.


## <a name="replication-process"></a>Proces replikace

1. Když povolíte replikaci pro virtuální virtuální vyvěsíte, začne počáteční replikace.
2. Pořídil se snímek virtuálního počítače Hyper-V.
3. Virtuální počítače na virtuálním počítači se replikují jeden po druhém, dokud se všechny nezkopírují do Azure. Počáteční doba replikace závisí na velikosti virtuálního počítače a šířce pásma sítě.
4. Změny disku, ke kterým dochází během počáteční replikace, jsou sledovány pomocí repliky technologie Hyper-V a uloženy v souborech protokolu (hrl files).
    - Soubory protokolu jsou ve stejné složce jako disky.
    - Každý disk má přidružený soubor HRL, který je odeslán do sekundárního úložiště.
    - Soubory snímků a protokolů spotřebovávají prostředky disku v době, kdy probíhá počáteční replikace.
4. Po dokončení počáteční replikace se odstraní snímek virtuálního počítače a začne rozdílová replikace.
5. Přírůstkové změny disku jsou sledovány v hrl souborech. Protokoly replikace se pravidelně nahrávají do účtu úložiště Azure agentem služby recovery Services.


## <a name="performance-and-scaling"></a>Výkon a škálování

Výkon replikace pro Hyper-V je ovlivněn faktory, které zahrnují velikost virtuálního počítače, rychlost změny dat (konve) virtuálních počítačů, dostupné místo na hostiteli Hyper-V pro ukládání souborů protokolu, šířku pásma pro replikaci a cílové úložiště v Azure.

- Pokud replikujete více počítačů současně, použijte [Plánovač nasazení obnovení webu Azure](../site-recovery/hyper-v-deployment-planner-overview.md) pro technologie Hyper-V, který vám pomůže optimalizovat replikaci.
- Naplánujte si replikaci Hyper-V a distribuujte replikaci přes účty úložiště Azure v souladu s kapacitou.

### <a name="control-upload-throughput"></a>Řízení propustnost odesílání

Můžete omezit šířku pásma použitou k nahrávání dat do Azure na každém hostiteli Hyper-V. Dej si pozor. Pokud nastavíte hodnoty příliš nízké, bude mít nepříznivý vliv na replikaci a zpoždění migrace.


1. Přihlaste se k hostitelskému nebo clusteru Hyper-V.
2. Spusťte **modul snap-in C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, a otevřete modul snap-in Konzoly mmc zálohování Windows Azure.
3. V modulu snap-in vyberte **Změnit vlastnosti**.
4. V **seznamu Omezení**vyberte možnost **Povolit omezení využití šířky pásma internetu pro operace zálohování**. Nastavte limity pro pracovní dobu a nepracovní dobu. Platné rozsahy jsou od 512 kb/s do 1 023 Mb/s.
I

### <a name="influence-upload-efficiency"></a>Vliv efektivity nahrávání

Pokud máte volnou šířku pásma pro replikaci a chcete zvýšit nahrávání, můžete zvýšit počet vláken přidělených pro úlohu nahrávání následujícím způsobem:

1. Otevřete registr pomocí aplikace Regedit.
2. Přejděte na klíč HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Zvyšte hodnotu počtu vláken použitých pro nahrávání dat pro každý replikující se virtuální počítače. Výchozí hodnota je 4 a maximální hodnota je 32. 




## <a name="next-steps"></a>Další kroky

Vyzkoušejte [migraci Hyper-V](tutorial-migrate-hyper-v.md) pomocí migrace serveru Azure.
