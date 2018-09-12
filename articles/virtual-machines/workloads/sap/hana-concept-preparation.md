---
title: Zásady pro zotavení po havárii a přípravu na systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Zásady pro zotavení po havárii a přípravu na systému SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff214460d919eff5c3c1a2e608958673867ddc55
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44392229"
---
# <a name="disaster-recovery-principles"></a>Zásady pro zotavení po havárii

Velké instance HANA nabízí funkce pro zotavení po havárii mezi razítka velká Instance HANA v různých oblastech Azure. Například pokud nasadíte velká Instance HANA jednotky v oblasti USA – Západ, Azure, můžete použít velká Instance HANA jednotky v oblasti USA – východ jako jednotky pro zotavení po havárii. Jak už bylo zmíněno dříve, zotavení po havárii se nenakonfiguruje automaticky, protože platíte za jiné jednotce velká Instance HANA v oblasti zotavení po Havárii vyžaduje. Nastavení zotavení po havárii funguje pro nastavení vertikální i horizontální navýšení kapacity. 

Ve scénářích nasazení, pokud zákazníci používají jednotku v oblasti zotavení po Havárii ke spuštění neprodukční systémy, které používají nainstalovaná instance HANA. Velká Instance HANA jednotka musí mít stejné skladovou Položku SKU pro produkční účely. Následující obrázek ukazuje, jakou konfiguraci disku mezi jednotky serverů v oblasti Azure produkčního prostředí a oblasti pro zotavení po havárii bude vypadat takto:

![Konfigurace nastavení zotavení po Havárii z hlediska disku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak je znázorněno na tomto obrázku přehled, pak musíte pro řazení druhou sadu diskové svazky. Cílové diskové svazky mají stejnou velikost jako produkční svazky pro produkční instance v jednotkách pro zotavení po havárii. Tyto diskové svazky jsou přidružena k jednotce velká Instance HANA serveru v lokalitě zotavení po havárii. Tyto svazky se replikují z oblasti produkční k lokalitě zotavení po Havárii:

- / hana/dat
- / hana/logbackups 
- /Hana/Shared (včetně/usr/sap)

Svazek /hana/log není replikovat, protože není potřeba transakční protokol SAP HANA ve způsobu, jakým se provádí obnovení z těchto svazků. 

Základ pro fungování obnovy po havárii nabízí se funkce replikace úložiště nabízí infrastruktura velká Instance HANA. Funkce, které se používá na straně úložiště není nepřetržitý datový proud změny, které se replikují v asynchronním režimu, protože dojde k změnám svazku úložiště. Místo toho je mechanismus, který vychází ze skutečnosti, že vytváření snímků tyto svazky v pravidelných intervalech. Rozdíl mezi již replikované snímků a nový snímek, která ještě nebyla replikována je pak přeneseny do lokality pro obnovení po havárii do cílové diskové svazky.  Tyto snímky jsou uložené ve svazcích a při selhání pro zotavení po havárii, je nutné obnovit na těchto svazcích.  

První přenos úplná data svazku by měl být předtím, než množství dat menší než rozdíly mezi snímky. V důsledku toho svazky v lokalitě zotavení po Havárii obsahovat každý, snímků svazků v produkční lokality. Nakonec můžete použít tento systém zotavení po Havárii zobrazíte na dřívější stav obnovení ztracených dat bez vrácení zpět v produkčním systému.

Pokud dojde k MCOD nasazení s více nezávislých instancí SAP HANA na jednu jednotku velká Instance HANA, očekává se, že všechny instance SAP HANA se zobrazuje na straně zotavení po Havárii replikace úložiště.

V případech, kdy použít systémové replikace HANA jako funkce vysoké dostupnosti v produkční lokality a pomocí replikace na základě úložiště pro zotavení po Havárii serveru jsou replikované svazky z obou uzlů z primární lokality do instance zotavení po Havárii. V lokalitě zotavení po Havárii tak, aby vyhovovaly replikace z primární i sekundární zotavení po Havárii, je nutné zakoupit další úložiště (stejnou velikost v době primárního uzlu). 



>[!NOTE]
>Funkce replikace úložiště velká Instance HANA je zrcadlení a replikace snímků úložiště. Pokud úložiště snímků nechcete provést, protože počínaje [zálohování a obnovení](#backup-and-restore) části tohoto článku, nesmí být žádné replikace na lokalitu pro obnovení po havárii. Provedením snímku úložiště je předpokladem pro replikaci úložiště do lokality pro obnovení po havárii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Příprava zotavení po havárii
V tomto scénáři máte produkční systém spuštěný na velkých instancích HANA v produkčním prostředí oblasti Azure. Kroky, které následují Předpokládejme, že je identifikátor SID systému HANA "PRD", a zda máte-li se o neprodukční systém spuštěný na velkých instancích HANA v oblasti zotavení po Havárii Azure. K tomu Předpokládejme, že její identifikátor SID je "TST." Tato konfigurace je znázorněna na následujícím obrázku:

![Začátek instalace zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Pokud instance serveru nebyla již byla seřazené sadou dalšího úložiště svazku, SAP HANA v Azure Service Management připojí další sadu svazky jako cíl pro repliku produkčního prostředí, aby velká Instance HANA jednotku, na kterém spouštíte TST HANA instance. K tomuto účelu budete muset poskytnout identifikátor SID instance HANA produkčního prostředí. Poté, co SAP HANA v Azure Service Management potvrdí přílohy tyto svazky, budete muset připojit tyto svazky do jednotky velká Instance HANA.

![Dalším krokem instalace zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Dalším krokem je instalace druhé instance SAP HANA na jednotce velká Instance HANA v oblasti zotavení po Havárii Azure, kde je spuštěn instance TST HANA. Nově instalovaný instance SAP HANA, musí mít stejný identifikátor SID. Uživatelé vytvoření musí mít stejné UID a ID skupiny, která má instance produkčního prostředí. Čtení [zálohování a obnovení](hana-backup-restore.md) podrobnosti. Pokud se instalace úspěšně dokončila, budete muset:

- Provedení kroku 2 Příprava snímků úložiště podle [zálohování a obnovení](hana-backup-restore.md).
- Pokud jste tak ještě neučinili, vytvořte veřejný klíč pro zotavení po Havárii jednotku velká Instance HANA jednotky. Přejděte ke kroku 3 Příprava snímků úložiště podle [zálohování a obnovení](hana-backup-restore.md).
- Zachovat *HANABackupCustomerDetails.txt* pomocí nové HANA instance a testování, zda připojení do úložiště funguje správně.  
- Zastavte nově nainstalovaná instance SAP HANA na jednotce velká Instance HANA v oblasti zotavení po Havárii Azure.
- Odpojit tyto svazky PRD a obraťte se na SAP HANA v Azure Service Management. Svazky nemůže zůstat připojené jednotce, protože nemůže být přístupné současně jako cíl replikace úložiště.  

![Zotavení po Havárii nastavení krok před vytvořením replikace](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Provozní tým vytváří vztah replikace mezi svazky PRD v produkčním prostředí oblasti Azure a PRD svazky v oblasti zotavení po Havárii Azure.

>[!IMPORTANT]
>Svazek /hana/log není replikují, protože to není nutné obnovit replikovaná databáze SAP HANA do konzistentního stavu v lokalitě zotavení po havárii.

V dalším kroku nastavení, nebo upravte plán zálohování snímku úložiště, abyste se dostali RTO a RPO v případě havárie. Chcete-li minimalizovat cíl bodu obnovení, nastavte následující intervaly replikace ve službě velká Instance HANA:
- Pro svazky, které jsou předmětem kombinované snímku (snapshot typ **hana**), sada k replikaci každých 15 minut na svazku cíle ekvivalentní úložiště v lokalitě zotavení po havárii.
- Pro svazek zálohování protokolu transakce (typ snímku **protokoly**), sada k replikaci každé 3 minuty na svazku cíle ekvivalentní úložiště v lokalitě zotavení po havárii.

Chcete-li minimalizovat cíl bodu obnovení, nastavte následující:
- Provést **hana** typ úložiště snímku (naleznete v tématu "krok 7: provést snímky") každých 30 minut na 1 hodinu.
- Proveďte zálohování protokolu transakce SAP HANA každých 5 minut.
- Provést **protokoly** zadejte úložiště snímků každých 5 až 15 minut. Pomocí tohoto intervalu dosáhnout plánovaný bod obnovení přibližně 15 25 minut.

To nastavení, pořadí zálohování protokolů transakcí, snímků úložiště a replikace HANA transakce protokoly zálohy dat svazku a/hana/a /hana/shared (včetně/usr/sap), může vypadat se data zobrazená na tomto obrázku:

 ![Vztah mezi snímek zálohy protokolu transakce a moduly snap zrcadlení na časovou osu](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Pokud chcete dosáhnout ještě lepšího cíle bodu obnovení v případě zotavení po havárii, můžete zkopírovat zálohy protokolu transakce HANA ze SAP HANA v Azure (velké instance) do jiné oblasti Azure. K dosažení této další snížení cíle bodu obnovení, proveďte následující kroky:

1. Proveďte zálohu transakce HANA protokolu možné tak často jako /hana/logbackups.
1. Příkaz rsync použijte ke zkopírování zálohy transakčního protokolu do systému souborů NFS sdílené složky hostované virtuální počítače Azure. Virtuální počítače jsou ve virtuálních sítích Azure v oblasti Azure produkčního prostředí a v oblasti zotavení po Havárii. Budete muset připojit k okruhu produkční velkých instancích HANA připojení k Azure obou virtuálních sítích Azure. Zobrazit na obrázcích v [sítě důležité informace týkající se zotavení po havárii pomocí velkých instancích HANA](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) oddílu. 
1. Udržování zálohy transakčního protokolu v oblasti, ve virtuálním počítači připojeno k systému souborů NFS exportovat úložiště.
1. V případě převzetí služeb při selhání po havárii doplňují zálohy transakčního protokolu, které považujete objemu /hana/logbackups s více nedávno provedených zálohy transakčního protokolu v systému souborů NFS sdílet v lokalitě zotavení po havárii. 
1. Spustíte zálohování protokolu transakcí obnovení poslední zálohy, který může být uložený v oblasti zotavení po Havárii.

Pokud operace velká Instance HANA potvrdit nastavení vztahu replikace a spustit provádění zálohy snímků úložiště, replikaci dat zahájí.

![Zotavení po Havárii nastavení krok před vytvořením replikace](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Jak se postupuje replikace, snímky svazků PRD v oblasti zotavení po Havárii Azure neobnoví. Jenom se ukládají. Pokud jsou svazky připojené v takový stav, představují stavu, který se po instalaci instance PRD SAP HANA v jednotce server v oblasti zotavení po Havárii Azure odpojit svazky. Představují také úložiště záloh, které ještě nebyly obnoveny.

Pokud dojde k selhání, také můžete obnovit starší snímků úložiště namísto nejnovější snímek úložiště.

## <a name="next-steps"></a>Další postup

- Přečtěte si [postup převzetí služeb při selhání pro zotavení po havárii](hana-failover-procedure.md).