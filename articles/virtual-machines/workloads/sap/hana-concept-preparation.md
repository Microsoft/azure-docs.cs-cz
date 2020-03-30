---
title: Zásady zotavení po havárii a příprava na SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Zásady zotavení po havárii a příprava na SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d52f871de75a7f7d34016b040e44d6f1623fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101260"
---
# <a name="disaster-recovery-principles"></a>Zásady zotavení po havárii

Velké instance HANA nabízejí funkci zotavení po havárii mezi razítky velké instance HANA v různých oblastech Azure. Pokud například nasadíte jednotky velké instance HANA v oblasti Azure – západ USA, můžete jednotky velké instance HANA v oblasti USA – východ použít jako jednotky zotavení po havárii. Jak již bylo zmíněno dříve, zotavení po havárii není nakonfigurováno automaticky, protože vyžaduje, abyste zaplatili za jinou jednotku velké instance HANA v oblasti zotavení po Havárii. Nastavení zotavení po havárii funguje pro škálování i horizontální navýšení kapacity. 

Ve scénářích, které byly dosud nasazeny, zákazníci používají jednotku v oblasti zotavení po Havárii ke spuštění neprodukčních systémů, které používají nainstalovanou instanci HANA. Jednotka velké instance HANA musí být stejné skladové položky jako skladová položka používaná pro výrobní účely. Následující obrázek ukazuje, jak vypadá konfigurace disku mezi jednotkou serveru v produkční oblasti Azure a oblastí zotavení po havárii:

![Konfigurace nastavení zotavení po havárii z diskového hlediska](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak je znázorněno na tomto přehledu grafiky, je třeba objednat druhou sadu svazků disku. Cílové diskové svazky mají stejnou velikost jako objemy výroby pro produkční instanci v jednotkách zotavení po havárii. Tyto diskové svazky jsou přidruženy k jednotce serveru velké instance HANA v lokalitě pro zotavení po havárii. Následující svazky jsou replikovány z produkční oblasti do lokality zotavení po Havárii:

- /hana/data
- /hana/logbackups 
- /hana/shared (včetně /usr/sap)

Svazek /hana/log není replikován, protože protokol transakcí SAP HANA není potřeba způsobem, jakým se provádí obnovení z těchto svazků. 

Základem nabízené funkce zotavení po havárii je funkce replikace úložiště, kterou nabízí infrastruktura velké instance HANA. Funkce, která se používá na straně úložiště není konstantní proud změn, které se replikují asynchronním způsobem jako změny dojít ke svazku úložiště. Místo toho je mechanismus, který závisí na skutečnosti, že snímky těchto svazků jsou vytvářeny pravidelně. Rozdíl mezi již replikovaný snímek a nový snímek, který ještě není replikován je pak převedena do lokality zotavení po havárii do cílových diskových svazků.  Tyto snímky jsou uloženy na svazcích a pokud dojde k převzetí služeb při selhání zotavení po havárii, je třeba je obnovit na těchto svazcích.  

První přenos úplných dat svazku by měl být dříve, než se množství dat stane menším než rozdíly mezi snímky. V důsledku toho svazky v lokalitě zotavení po Havárii obsahují každý snímek svazku provedený chod v produkčním lokalitě. Nakonec můžete tento systém zotavení po havárii použít k získání dřívějšího stavu k obnovení ztracených dat bez vrácení produkčního systému.

Pokud je nasazení MCOD s více nezávislých instancí SAP HANA na jedné jednotce velké instance HANA, očekává se, že všechny instance SAP HANA jsou stále replikovány úložiště na straně zotavení po Havárii.

V případech, kdy používáte replikaci systému HANA jako funkci s vysokou dostupností v produkční lokalitě a používáte replikaci založenou na úložišti pro lokalitu zotavení po Havárii, jsou replikovány svazky obou uzlů z primární lokality do instance zotavení po Havárii. Je nutné zakoupit další úložiště (stejnou velikost jako primární uzel) v lokalitě zotavení po Havárii, aby bylo možné replikovat replikaci z primární i sekundární do zotavení po havárii. 



>[!NOTE]
>Funkce replikace úložiště velké instance HANA je zrcadlení a replikace snímků úložiště. Pokud neprovedete snímky úložiště, jak je uvedeno v části Zálohování a obnovení tohoto článku, nemůže existovat žádná replikace lokality pro zotavení po havárii. Spuštění snímku úložiště je předpokladem pro replikaci úložiště do lokality zotavení po havárii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Příprava scénáře zotavení po havárii
V tomto scénáři máte produkční systém spuštěný na velké instance HANA v oblasti produkčníazure. Pro následující kroky předpokládejme, že SID tohoto systému HANA je "PRD" a že máte neprodukční systém spuštěný na velkých instancích HANA v oblasti Dr Azure. Pro druhé, předpokládejme, že jeho SID je "TST." Následující obrázek ukazuje tuto konfiguraci:

![Zahájení instalace zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Pokud instance serveru ještě nebyla objednána s další sadou svazků úložiště, SAP HANA ve službě Azure Service Management připojí další sadu svazků jako cíl pro produkční repliku k jednotce velké instance HANA, na které spouštěte TST Hana instance. Za tímto účelem je třeba zadat SID vaší produkční instance HANA. Poté, co SAP HANA ve správě služeb Azure potvrdí připojení těchto svazků, je třeba připojit tyto svazky do jednotky velké instance HANA.

![Další krok nastavení zotavení po havárii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Dalším krokem je instalace druhé instance SAP HANA do jednotky velké instance HANA v oblasti Azure zotavení po Havárii, kde spustíte instanci TST HANA. Nově nainstalovaná instance SAP HANA musí mít stejný SID. Vytvoření uživatelé musí mít stejné ID UID a ID skupiny, které má produkční instance. Podrobnosti [nasviťte na zálohování a obnovení.](hana-backup-restore.md) Pokud byla instalace úspěšná, je třeba:

- Spusťte krok 2 přípravy snímku úložiště popsanév [části Zálohování a obnovení](hana-backup-restore.md).
- Pokud jste tak ještě neučinili, vytvořte veřejný klíč pro jednotku DR jednotky velké instance HANA. Viz krok 3 přípravy snímku úložiště popsané v [části Zálohování a obnovení](hana-backup-restore.md).
- Udržovat *HANABackupCustomerDetails.txt* s novou instanci HANA a otestovat, zda připojení do úložiště funguje správně.  
- Zastavte nově nainstalovanou instanci SAP HANA v jednotce velké instance HANA v oblasti Azure zotavení po Havárii.
- Odpojte tyto svazky PRD a obraťte se na SAP HANA ve službě Azure Service Management. Svazky nemohou zůstat připojené k jednotce, protože nemohou být přístupné a zároveň fungují jako cíl replikace úložiště.  

![Krok nastavení zotavení po havárii před navázáním replikace](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Provozní tým vytvoří vztah replikace mezi svazky PRD v oblasti produkčního Azure a svazky PRD v oblasti Azure zotavení po Havárii.

>[!IMPORTANT]
>Svazek /hana/log není replikován, protože není nutné obnovit replikovanou databázi SAP HANA do konzistentního stavu v lokalitě pro zotavení po havárii.

Dále nastavte nebo upravte plán zálohování snímků úložiště, abyste se v případě katastrofy dostali k rto a rpo. Chcete-li minimalizovat cíl bodu obnovení, nastavte ve službě Velká instance HANA následující intervaly replikace:
- Pro svazky, na které se vztahuje kombinovaný snímek (typ snímku **hana**), nastavte replikovat každých 15 minut na ekvivalentní cíle svazku úložiště v lokalitě zotavení po havárii.
- Pro svazek zálohování protokolu transakcí **(protokoly**typu snímek ) nastavte replikaci každé 3 minuty na ekvivalentní cíle svazku úložiště v lokalitě zotavení po havárii.

Chcete-li minimalizovat cíl bodu obnovení, nastavte následující:
- Proveďte snímek úložiště typu **hana** (viz "Krok 7: Provedení snímků") každých 30 minut až 1 hodinu.
- Proveďte zálohy protokolu transakcí SAP HANA každých 5 minut.
- Proveďte snímek úložiště typu **protokoly** každých 5-15 minut. S tímto intervalem, můžete dosáhnout RPO kolem 15-25 minut.

S tímto nastavením může vypadat posloupnost záloh protokolu transakcí, snímků úložiště a replikace svazku zálohování protokolu transakcí HANA a /hana/data a /hana/shared (includes /usr/sap) jako data zobrazená na této grafice:

 ![Vztah mezi snap zálohování protokolu transakcí a přichycení zrcadlo na časové ose](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Chcete-li dosáhnout ještě lepší RPO v případě zotavení po havárii, můžete zkopírovat zálohy protokolu transakcí HANA z SAP HANA v Azure (velké instance) do jiné oblasti Azure. Chcete-li dosáhnout dalšího snížení rpo, proveďte následující kroky:

1. Zálohujte transakční protokol HANA co nejčastěji na /hana/logbackups.
1. Pomocí rsync zkopírujte zálohy protokolů transakcí do virtuálních počítačů Azure hostovaných službou NFS. Virtuální počítače jsou ve virtuálních sítích Azure v produkční oblasti Azure a v oblastech zotavení po Havárii. Je potřeba připojit obě virtuální sítě Azure k okruhu spojujícímu produkční velké instance HANA s Azure. Podívejte se na grafiku v [aspekty sítě pro zotavení po havárii s HANA velké instance](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) části. 
1. Udržujte zálohy transakčníprotokol v oblasti ve virtuálním počítače připojeném k úložišti exportovaného systému souborů NFS.
1. V případě převzetí služeb při selhání případě doplnit zálohy protokolu transakcí najdete na svazku /hana/logbackups s více nedávno provedené zálohy protokolu transakcí na sdílené sfs v lokalitě zotavení po havárii. 
1. Spusťte zálohu transakčního protokolu a obnovte nejnovější zálohu, která může být uložena do oblasti zotavení po Havárii.

Když operace velké instance HANA potvrďte nastavení vztahu replikace a spustíte zálohování snímků úložiště spuštění, začne replikace dat.

![Krok nastavení zotavení po havárii před navázáním replikace](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Jak pokračuje replikace, snímky na svazcích PRD v oblastech Azure zotavení po Havárii nejsou obnoveny. Jsou uloženy pouze. Pokud jsou svazky připojeny v takovém stavu, představují stav, ve kterém jste tyto svazky odmontovali po instalaci instance PRD SAP HANA v serverové jednotce v oblasti Azure zotavení po Havárii. Představují také zálohy úložiště, které ještě nejsou obnoveny.

Pokud je převzetí služeb při selhání, můžete také obnovit na starší snímek úložiště namísto nejnovější snímek úložiště.

## <a name="next-steps"></a>Další kroky

- Odkazovat [zotavení po havárii převzetí služeb při selhání postup](hana-failover-procedure.md).
