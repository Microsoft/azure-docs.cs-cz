---
title: Zásady zotavení po havárii a příprava na SAP HANA v Azure (velké instance) | Microsoft Docs
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "70101260"
---
# <a name="disaster-recovery-principles"></a>Principy zotavení po havárii

Velké instance HANA nabízí funkce zotavení po havárii mezi velkými razítky instancí HANA v různých oblastech Azure. Pokud například nasadíte velké jednotky instancí HANA v USA – západ oblasti Azure, můžete použít jednotky velkých instancí HANA v USA – východ oblasti jako jednotky pro zotavení po havárii. Jak bylo zmíněno dříve, zotavení po havárii není automaticky nakonfigurováno, protože vyžaduje, abyste zaplatili za jinou jednotku velkých instancí HANA v oblasti zotavení po havárii. Nastavení zotavení po havárii funguje pro horizontální navýšení kapacity i pro nastavení horizontálního navýšení kapacity. 

Ve scénářích nasazených doposud zákazníci používají jednotku v oblasti zotavení po havárii ke spouštění neprodukčních systémů, které používají nainstalovanou instanci HANA. Jednotka velké instance HANA musí být stejná jako SKU, která se používá pro produkční účely. Následující obrázek ukazuje, jak konfigurace disku mezi jednotkou serveru v produkční oblasti Azure a oblastí zotavení po havárii vypadá takto:

![Konfigurace instalace DR z místa na disku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak je znázorněno na obrázku tohoto přehledu, je třeba seřadit druhou sadu diskových svazků. Svazky cílového disku mají stejnou velikost jako produkční svazky pro instanci výroby v jednotkách zotavení po havárii. Tyto diskové svazky jsou spojené s jednotkou serveru velké instance HANA na webu pro zotavení po havárii. Následující svazky se replikují z produkční oblasti do lokality DR:

- /hana/data
- /hana/logbackups 
- /Hana/Shared (zahrnuje/usr/SAP)

Svazek/Hana/log není replikovaný, protože protokol transakcí SAP HANA není potřeba v tom, jak se provádí obnovení z těchto svazků. 

Nabízená funkce zotavení po havárii je základem funkce replikace úložiště nabízené infrastrukturou velkých instancí HANA. Funkce, která se používá na straně úložiště, není konstantní datový proud změn, které se replikují asynchronně, protože změny svazku úložiště probíhají. Místo toho je to mechanismus, který spoléhá na skutečnost, že snímky těchto svazků jsou pravidelně vytvářeny. Rozdíl mezi již replikovaným snímkem a novým snímkem, který ještě není replikovaný, se pak přenese do lokality pro zotavení po havárii do cílových diskových svazků.  Tyto snímky se ukládají na svazcích a v případě převzetí služeb při selhání v případě zotavení po havárii je nutné na těchto svazcích obnovit.  

První přenos úplných dat svazku by měl být před tím, než se objem dat zmenší na rozdíl mezi snímky. V důsledku toho svazky na webu DR obsahují všechny snímky svazků provedené v provozní lokalitě. Nakonec můžete pomocí tohoto systému DR přejít k dřívějšímu stavu a obnovit ztracená data, aniž byste museli vrátit provozní systém.

Pokud existuje nasazení MCOD s více nezávisle SAP HANA instancemi na jedné jednotce velké instance HANA, očekává se, že všechny instance SAP HANA na straně zotavení replikují úložiště.

V případech, kdy použijete replikaci systému HANA jako funkci vysoké dostupnosti v produkčním webu a použijete replikaci založenou na úložištích pro lokalitu DR, replikují se svazky obou uzlů z primární lokality do instance zotavení po havárii. Další úložiště (stejná velikost jako primární uzel) se musí koupit v lokalitě DR, aby se pokryla replikace z primárního i sekundárního na server DR. 



>[!NOTE]
>Funkce replikace úložiště s velkou instancí HANA je zrcadlení a replikuje snímky úložiště. Pokud neprovedete snímky úložiště, které byly zavedeny v části zálohování a obnovení tohoto článku, nemůžete mít žádnou replikaci na lokalitu pro zotavení po havárii. Spuštění snímku úložiště je předpokladem pro replikaci úložiště na lokalitu pro zotavení po havárii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Příprava scénáře zotavení po havárii
V tomto scénáři máte produkční systém běžící na rozsáhlých instancích služby HANA v produkční oblasti Azure. V následujících krocích se předpokládá, že identifikátor SID tohoto systému HANA je "PRD" a že máte neprodukční systém spuštěný v rozsáhlých instancích služby HANA v oblasti Azure DR. V takovém případě se předpokládá, že jeho identifikátor SID je "TST". Tato konfigurace je znázorněna na následujícím obrázku:

![Spuštění instalace DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Pokud se instance serveru ještě neobjednala s dodatečnou nastavenou svazkem úložiště, SAP HANA ve službě Azure Service Management připojí další sadu svazků jako cíl pro provozní repliku do jednotky velkých instancí HANA, na které běží instance TST HANA. Pro tento účel musíte zadat identifikátor SID vaší provozní instance HANA. Po SAP HANA na Azure Service Management potvrdí, že se tyto svazky připojí, musíte tyto svazky připojit k jednotce velké instance HANA.

![Další krok instalace DR](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Dalším krokem je instalace druhé instance SAP HANA na velké jednotce instance HANA v oblasti Azure v systému DR, kde spustíte instanci služby TST HANA. Nově nainstalovaná SAP HANA instance musí mít stejný identifikátor SID. Uživatelé, kteří vytvořili, musí mít stejné UID a ID skupiny jako provozní instance. Podrobnosti najdete v tématu [zálohování a obnovení](hana-backup-restore.md) . Pokud byla instalace úspěšná, musíte:

- Proveďte krok 2 pro přípravu snímku úložiště, který je popsaný v tématu [zálohování a obnovení](hana-backup-restore.md).
- Pokud jste tak ještě neučinili, vytvořte veřejný klíč pro jednotku velkých instancí HANA jednotky pro zotavení po havárii. Viz krok 3 přípravy snímku úložiště, který je popsaný v tématu [zálohování a obnovení](hana-backup-restore.md).
- Udržujte *HANABackupCustomerDetails.txt* s novou instancí Hana a otestujete, jestli připojení k úložišti funguje správně.  
- Zastavte nově nainstalovanou instanci SAP HANA na jednotce velkých instancí HANA v oblasti Azure DR.
- Odpojte tyto svazky PRD a obraťte se SAP HANA na správu služeb Azure. Svazky nemůžou zůstat připojené k jednotce, protože nemůžou být dostupné, když budou fungovat jako cíl replikace úložiště.  

![Krok instalace DR před vytvořením replikace](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Provozní tým vytvoří vztah replikace mezi PRD svazky v produkční oblasti Azure a PRD svazky v oblasti Azure DR.

>[!IMPORTANT]
>Svazek/Hana/log není replikovaný, protože není nutné obnovovat replikovanou databázi SAP HANA do konzistentního stavu na webu pro zotavení po havárii.

V dalším kroku nastavte nebo upravte plán zálohování snímků úložiště, abyste se dostali k vašemu RTO a RPO v případě havárie. Chcete-li minimalizovat cíl bodu obnovení, nastavte následující intervaly replikace ve službě velká instance HANA:
- Pro svazky zahrnuté do kombinovaného snímku (typ snímku **Hana**) nastavte hodnotu replikovat každých 15 minut na ekvivalentní cíle svazku úložiště na webu pro zotavení po havárii.
- Pro svazek zálohy protokolu transakcí ( **protokoly**typu snímku) nastavte na hodnotu replikovat každé 3 minuty na ekvivalentní cíle svazku úložiště v lokalitě pro obnovení po havárii.

Chcete-li minimalizovat cíl bodu obnovení, nastavte následující:
- Proveďte snímek úložiště typu **Hana** (viz "krok 7: provádět snímky") každých 30 minut až 1 hodinu.
- Proveďte SAP HANA zálohy protokolu transakcí každých 5 minut.
- Do **protokolů** zadejte snímek úložiště každých 5-15 minut. Po uplynutí tohoto intervalu dosáhnete cílení na přibližně 15-25 minut.

V rámci tohoto nastavení může posloupnost zálohování protokolů transakcí, snímků úložiště a replikace záložního svazku protokolu transakcí HANA a/Hana/data a/Hana/Shared (zahrnuje/usr/SAP) vypadat jako data zobrazená na tomto obrázku:

 ![Vztah mezi snímkem zálohování protokolu transakcí a zrcadlem pro přichycení na časové ose](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Pokud chcete dosáhnout ještě lepšího bodu obnovení v případě zotavení po havárii, můžete zkopírovat zálohy protokolu transakcí HANA z SAP HANA v Azure (velké instance) do jiné oblasti Azure. K dosažení tohoto dalšího omezení RPO proveďte následující kroky:

1. Zálohujte protokol transakcí HANA tak často, jak je možné/Hana/logbackups.
1. Pomocí rsync zkopírujte zálohy protokolu transakcí do virtuálních počítačů Azure hostovaných v rámci sdílené složky systému souborů NFS. Virtuální počítače jsou ve virtuálních sítích Azure v produkční oblasti Azure a v oblastech zotavení po havárii. Musíte připojit virtuální sítě Azure k okruhu připojujícím se velké instance provozní HANA do Azure. Přečtěte si část s [informacemi o sítích při zotavení po havárii s velkými instancemi Hana](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) . 
1. Ponechejte zálohy transakčních protokolů v oblasti ve virtuálním počítači připojeném k exportovanému úložišti NFS.
1. V případě převzetí služeb při selhání v případě zotavení po havárii doplňte zálohy protokolu transakcí, které najdete na svazku/Hana/logbackups, s použitím dalších nedávno pořízených záloh protokolu transakcí ve sdílené složce NFS na webu pro zotavení po havárii. 
1. Spusťte zálohu protokolu transakcí, která se má obnovit do poslední zálohy, kterou je možné uložit do oblasti zotavení po havárii.

Když operace velkých instancí HANA potvrdí instalaci replikačního vztahu a zahájíte zálohování snímků úložiště spouštění, zahájí se replikace dat.

![Krok instalace DR před vytvořením replikace](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

V průběhu replikace nebudou obnoveny snímky na svazcích PRD v oblastech Azure DR. Jsou uloženy pouze. Pokud jsou svazky připojeny v takovém stavu, představují stav, ve kterém jste tyto svazky odpojili po instalaci instance PRD SAP HANA v serverové jednotce v oblasti Azure DR. Představují taky zálohy úložiště, které se ještě neobnovily.

Pokud dojde k převzetí služeb při selhání, můžete také zvolit obnovení do staršího snímku úložiště místo nejnovějšího snímku úložiště.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [postup převzetí služeb při selhání zotavení po havárii](hana-failover-procedure.md)
