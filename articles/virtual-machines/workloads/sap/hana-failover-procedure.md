---
title: Postup převzetí služeb při selhání HANA na lokalitu po havárii pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Jak provést převzetí služeb při selhání na lokalitu pro zotavení po havárii pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1daba0819e0164602ae78ece87421834f36fb09a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967818"
---
# <a name="disaster-recovery-failover-procedure"></a>Postup převzetí služeb při selhání pro zotavení po havárii


>[!IMPORTANT]
>Tento článek není náhradou za dokumentaci pro správu SAP HANA ani pro poznámky ke službě SAP. Očekáváme, že budete mít plnou znalost a odbornosti v SAP HANA správě a operacích, zejména pro zálohování, obnovení, vysokou dostupnost a zotavení po havárii (DR). V tomto článku se zobrazí snímky obrazovky z SAP HANA studia. Obsah, struktura a povaha obrazovek nástrojů pro správu SAP a samotných nástrojů se můžou v SAP HANA vydání verze změnit.

Při převzetí služeb při selhání na lokalitu DR je třeba vzít v úvahu dva případy:

- K tomu, abyste se mohli vrátit k nejnovějšímu stavu dat, potřebujete databázi SAP HANA. V tomto případě je k dispozici samoobslužný skript, pomocí kterého můžete převzetí služeb při selhání provést bez nutnosti kontaktovat společnost Microsoft. Pro navrácení služeb po obnovení musíte pracovat s Microsoftem.
- Chcete obnovit snímek úložiště, který není posledním replikovaným snímkem. V takovém případě potřebujete spolupracovat s Microsoftem. 

>[!NOTE]
>Následující kroky je nutné provést na jednotce velké instance HANA, která představuje jednotku DR. 
 
Pokud chcete provést obnovení na nejnovější replikované snímky úložiště, postupujte podle kroků v tématu "úplné převzetí služeb při selhání v programu DR – azure_hana_dr_failover" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Pokud chcete více instancí SAP HANA převzít při selhání, spusťte příkaz azure_hana_dr_failover několikrát. V případě vyžádání zadejte identifikátor SID SAP HANA, který chcete převzít a obnovit. 


Převzetí služeb při selhání můžete testovat i bez dopadu na skutečný vztah replikace. Pokud chcete provést test převzetí služeb při selhání, postupujte podle kroků v tématu "provedení testu zotavení po převzetí služeb při selhání azure_hana_test_dr_failover" v tématu [Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

>[!IMPORTANT]
>Nespouštějte *žádné* provozní transakce v instanci, kterou jste vytvořili v lokalitě Dr, prostřednictvím procesu **testování převzetí služeb při selhání**. Příkaz azure_hana_test_dr_failover vytvoří sadu svazků, které nemají žádný vztah k primární lokalitě. V důsledku toho *není* možné synchronizaci zpět do primární lokality. 

Pokud chcete otestovat více instancí SAP HANA, spusťte skript několikrát. V případě vyžádání zadejte identifikátor SID SAP HANA instance, kterou chcete otestovat pro převzetí služeb při selhání. 

>[!NOTE]
>Pokud potřebujete převzít služby při selhání na webu DR, abyste mohli zachránit některá data, která byla před hodinami Odstraněná, a potřebujete nastavit svazky DR na dřívější snímek, použije se tento postup. 

1. Vypněte nevýrobní instanci HANA na jednotce zotavení po havárii velkých instancí HANA, které používáte. Nainstalují se neaktivní provozní instance HANA.
1. Ujistěte se, že nejsou spuštěné žádné SAP HANA procesy. Pro tuto kontrolu použijte následující příkaz:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      Výstup by měl Ukázat **hdbdaemon** proces v zastaveném stavu a žádné další procesy Hana ve spuštěném nebo spuštěném stavu.
1. Určete, který název snímku nebo ID zálohy SAP HANA chcete obnovit lokalitu pro zotavení po havárii. V reálných případech zotavení po havárii je tento snímek obvykle posledním snímkem. Pokud potřebujete obnovit ztracená data, vyberte předchozí snímek.
1. Kontaktujte podporu Azure prostřednictvím vysoce prioritní žádosti o podporu. Požádejte o obnovení tohoto snímku s názvem a datem snímku nebo ID zálohy HANA na webu DR. Ve výchozím nastavení se na straně operace obnoví pouze svazek/Hana/data. Pokud chcete, aby svazky/Hana/logbackups byly také, je nutné, abyste je konkrétně nastavili. *Neobnovujte svazek/Hana/Shared.* Místo toho po opětovném připojení svazku/Hana/Shared pro PRD vyberte konkrétní soubory, například global.ini ven z adresáře **. Snapshot** a jejích podadresářů. 

   Na straně operací dojde k následujícím krokům:

   a. Replikace snímků z produkčního objemu do svazků pro zotavení po havárii je zastavena. K tomuto přerušení mohlo dojít, pokud je výpadek v provozní lokalitě důvodem, proč je nutné provést postup zotavení po havárii.
   
   b. Název snímku nebo snímek úložiště s ID zálohy, který jste zvolili, je obnoven na svazcích pro zotavení po havárii.
   
   c. Po obnovení jsou svazky pro zotavení po havárii k dispozici, aby byly v oblasti zotavení po havárii připojené k jednotkám velkých instancí HANA.
      
1. Připojte svazky zotavení po havárii k jednotce velkých instancí HANA v lokalitě pro obnovení po havárii. 
1. Spusťte instanci neaktivní SAP HANA výroby.
1. Pokud jste se rozhodli Kopírovat protokoly zálohování protokolu transakcí, aby se snížil čas bodu obnovení, slučte zálohy protokolu transakcí do nově připojeného adresáře/Hana/logbackups DR. Nepřepisujte stávající zálohy. Zkopírujte novější zálohy, které nebyly replikovány s poslední replikací snímku úložiště.
1. Z snímků, které se nereplikují do svazku/hana/shared/PRD v oblasti Azure, můžete také obnovit jednotlivé soubory.

Následující kroky ukazují, jak obnovit SAP HANA instanci výroby na základě obnoveného snímku úložiště a záloh protokolu transakcí, které jsou k dispozici.

1. Umístění zálohy můžete změnit na **/Hana/logbackups** pomocí nástroje SAP HANA Studio.

   ![Změna umístění zálohy pro obnovení po havárii](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA prohledává umístění záložních souborů a navrhuje poslední zálohu protokolu transakcí, do které se má obnovení provést. Kontrola může trvat několik minut, než se zobrazí obrazovka podobná této:

   ![Seznam záloh protokolu transakcí pro zotavení po havárii](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Upravte některá výchozí nastavení:

      - Zrušte zaškrtnutí políčka **použít rozdílové zálohy**.
      - Vyberte možnost **inicializovat oblast protokolu**.

   ![Nastavení oblasti inicializovat protokol](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Vyberte **Dokončit**.

   ![Dokončení obnovení DR](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Okno průběh, podobně jako v tomto příkladu, by se mělo zobrazit. Mějte na paměti, že tento příklad je obnovení konfigurace SAP HANA horizontálního navýšení kapacity se třemi uzly.

![Průběh obnovování](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Pokud obnovení přestane reagovat na obrazovce **dokončení** a nezobrazuje obrazovku průběh, zkontrolujte, zda jsou spuštěny všechny instance SAP HANA v pracovních uzlech. V případě potřeby spusťte instance SAP HANA ručně.


## <a name="failback-from-a-dr-to-a-production-site"></a>Navrácení služeb po obnovení z zotavení po havárii do provozní lokality
Navrácení služeb po obnovení z programu zotavení po havárii do provozní lokality. Pojďme se podívat na scénář, ve kterém se převzetí služeb při selhání v lokalitě pro zotavení po havárii způsobilo v produkční oblasti Azure, a ne na tom, co by bylo nutné obnovovat ztracená data. 

V lokalitě pro obnovení po havárii jste provozoval provozní úlohy SAP pro určitou dobu. V případě, že jsou problémy v produkčním webu vyřešeny, budete chtít provést navrácení služeb po obnovení do produkčního webu. Vzhledem k tomu, že nemůžete přijít o data, krok zpátky do produkční lokality zahrnuje několik kroků a uzavře spolupráci s SAP HANA v rámci Azure Operations Team. Můžete aktivovat provozní tým, aby po vyřešení problémů zahájil synchronizaci zpět do produkční lokality.

Postupujte takto:

1. Služba SAP HANA v rámci Azure Operations Team získá Trigger, který synchronizuje úložné svazky úložiště od svazků úložiště pro zotavení po havárii, které teď reprezentují stav výroby. V tomto stavu je jednotka velkých instancí HANA v produkčním webu vypnutá.
1. SAP HANA v Azure Operations týmu sleduje replikaci a zajišťuje, aby se zachytila předtím, než vás informují.
1. Vypínáte aplikace, které používají instanci provozní HANA, na webu pro zotavení po havárii. Pak provedete zálohování protokolu transakcí HANA. V dalším kroku zastavíte instanci HANA, která je spuštěná v jednotkách velkých instancí HANA, na webu pro zotavení po havárii.
1. Po ukončení instance HANA, která je spuštěná v jednotce velké instance služby HANA v lokalitě pro obnovení po havárii, bude provozní tým ručně synchronizovat svazky disku znovu.
1. V SAP HANA v rámci Azure Operations Team se znovu spustí jednotka velká instance HANA v produkčním webu. Předají se vám na vás. Ujistěte se, že je instance SAP HANA v době spuštění jednotky velkých instancí HANA ve stavu vypnutí.
1. Provedete stejné kroky obnovení databáze, které jste provedli při převzetí služeb při selhání v lokalitě pro obnovení po havárii.

## <a name="monitor-disaster-recovery-replication"></a>Monitorování replikace zotavení po havárii

Pokud chcete monitorovat stav procesu replikace úložiště, spusťte skript `azure_hana_replication_status` . Tento příkaz musí být spuštěn z jednotky, která je spuštěna v umístění zotavení po havárii, aby fungovala podle očekávání. Příkaz nefunguje bez ohledu na to, jestli je replikace aktivní. Příkaz se dá spustit pro každou jednotku velkých instancí v tenantovi v umístění pro zotavení po havárii. Nedá se použít k získání podrobných informací o spouštěcím svazku. 

Další informace o příkazu a jeho výstupu najdete v tématu "získání stavu replikace DR-azure_hana_replication_status" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf).


## <a name="next-steps"></a>Další kroky
- Podívejte [se na téma monitorování a řešení potíží ze strany Hana](hana-monitor-troubleshoot.md).
