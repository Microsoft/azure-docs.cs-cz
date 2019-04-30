---
title: Postup převzetí služeb při selhání HANA k lokalitě zotavení po havárii pro SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Jak provést převzetí služeb při selhání do lokality pro zotavení po havárii pro SAP HANA v Azure (velké instance)
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
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 76d8bb816bdf229d13a49fa61337899a8bf29ecd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098282"
---
# <a name="disaster-recovery-failover-procedure"></a>Postup převzetí služeb při selhání pro zotavení po havárii


>[!IMPORTANT]
>Tato dokumentace je nahrazen dokumentace správy SAP HANA nebo SAP poznámky. Očekává se, zda má čtečka důkladného porozumění a odborných znalostí v SAP HANA správu a provoz, zejména s tématy zálohování, obnovení, vysokou dostupnost a zotavení po havárii. V této dokumentaci se zobrazí snímky obrazovky ze SAP HANA Studio. Obsah, struktura a povaze obrazovky nástroje pro správu SAP a nástrojů, samotné se mohou změnit ze SAP HANA verzí.

Existují dva případy, které je třeba zvážit při převzetí služeb při selhání pro zotavení po Havárii serveru:

- Budete potřebovat databázi SAP HANA se vrátíte do stavu nejnovější data. V takovém případě je samoobslužné skript, pomocí kterého můžete provádět převzetí služeb při selhání, aniž byste museli kontaktovat společnost Microsoft. Ale pro navrácení služeb po obnovení, musíte spolupracovat s Microsoftem.
- Chcete obnovit na snímek úložiště, který není nejnovější replikované snímku. V takovém případě musíte spolupracovat s Microsoftem. 

>[!NOTE]
>Následující kroky je potřeba spustit na jednotce velká Instance HANA, která představuje jednotku zotavení po Havárii. 
 
Pokud chcete obnovit nejnovější replikované úložiště snímků, proveďte kroky, jak je uvedeno v části **"Převzetí služeb při úplné zotavení po Havárii – azure_hana_dr_failover"** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure ](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Pokud chcete mít více instancí SAP HANA převzetí služeb při selhání, budete muset spustit příkaz azure_hana_dr_failover několikrát. Při požadavku na, zadejte v SAP HANA SID chcete převzetí služeb při selhání a obnovení. 


Převzetí služeb při selhání zotavení po Havárii můžete otestovat také bez dopadu na vztah skutečné replikace. Pokud chcete provést testovací převzetí služeb, postupujte podle kroků v **"Proveďte test, zotavení po Havárii převzetí služeb při selhání – azure_hana_test_dr_failover"** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

>[!IMPORTANT]
>Proveďte *není* spouštět jakékoli transakce produkční instance, kterou jste vytvořili v lokalitě zotavení po Havárii procesem **testování převzetí služeb při selhání**. Tento příkaz azure_hana_test_dr_failover vytvoří sadu svazky, které nemají žádný vztah k primární lokalitě. V důsledku toho se synchronizace zpět do primární lokality *není* je to možné. 

Pokud chcete mít více instancí SAP HANA otestovat, budete muset několikrát spusťte skript. Při požadavku na, zadejte v SAP HANA SID instanci, kterou chcete testovat převzetí služeb při selhání. 

>[!NOTE]
>Pokud je potřeba k lokalitě zotavení po Havárii zachránit nějaká data, která byla odstraněna hodinami, a potřebují mít možnost zotavení po Havárii svazky, které chcete nastavit na předchozí snímek převzetí služeb při selhání, tento postup platí. 

1. Vypněte mimo produkční instance HANA na jednotce velkých instancích HANA, který používáte pro zotavení po havárii. Je to proto, že se nachází předinstalovaným neaktivní produkční instance HANA.
1. Ujistěte se, že jsou spuštěny žádné procesy SAP HANA. Použijte následující příkaz k této kontrole: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Výstup by měl zobrazit **hdbdaemon** zpracovat v zastaveném stavu a žádné další procesy HANA ve stavu spuštěná nebo spuštěna.
1. Určení, které název snímku nebo ID zálohování SAP HANA chcete obnovit lokalitu pro obnovení po havárii. V případech, obnovení po havárii skutečné tento snímek je obvykle nejnovější snímek. Pokud je potřeba obnovení ztracených dat, vyberte předchozí snímek.
1. Obraťte se na podporu Azure prostřednictvím žádosti o podporu s vysokou prioritou. Požádejte o obnovení tohoto snímku (s name a date snímku) nebo ID HANA zálohy v lokalitě zotavení po Havárii. Výchozí hodnota je, že na straně operace obnoví/hana/datový svazek pouze. Pokud chcete mít/hana/logbackups svazky stejně, musíte výslovně uvádějí, že. *Neobnovujte /hana/shared svazku.* Místo toho byste měli zvolit konkrétní soubory, jako je global.ini z celkového počtu **.snapshot** adresáři a jeho podadresářích po opětovném připojení/hana/sdílený svazek pro PRD. 

   Na straně operace jsou provedeny následující kroky:

   a. Replikace snímků z objem výroby svazky pro obnovení po havárii je zastavená. Toto narušení může již došlo při výpadku lokality produkčním prostředí z důvodu, že je potřeba provést postup pro zotavení po havárii.
   
   b. Úložiště název snímku nebo pořízení snímku s ID zálohy jste zvolili, je obnovena na svazcích pro zotavení po havárii.
   
   c. Po obnovení je možné připojit s jednotkami velká Instance HANA v oblasti pro zotavení po havárii svazky pro obnovení po havárii.
      
1. Připojte svazky pro obnovení po havárii k jednotce velká Instance HANA v lokalitě zotavení po havárii. 
1. Spuštění neaktivní produkční instance SAP HANA.
1. Pokud jste zvolili pro kopírování protokolů transakcí zálohování protokolů zkrátit čas cíle bodu obnovení, musíte sloučit tyto zálohy transakčního protokolu do adresáře logbackups nově připojené zotavení po Havárii/hana /. Nepřepisujte stávající zálohy. Zkopírujte novější zálohy, které se nezreplikovaly s nejnovější replikace úložiště snímku.
1. Můžete také obnovit jednotlivé soubory mimo snímky, které se replikují do svazku /hana/shared/PRD v oblasti zotavení po Havárii Azure.

Další postupně jednotlivé kroky zahrnuje obnovení provozní instance SAP HANA na základě snímek obnovené úložiště a zálohování protokolů transakcí, které jsou k dispozici:

1. Změnit umístění zálohy pro **/hana/logbackups** s použitím sady Studio SAP HANA.
   ![Změna umístění zálohy pro zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA prohledává umístění zálohování souborů a navrhne nejnovější zálohy protokolu transakcí obnovení. Kontrola může trvat několik minut, dokud na obrazovce jako se zobrazí následující: ![Seznam zálohy transakčního protokolu pro zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Upravte některé výchozí nastavení:

      - Vymazat **pomocí rozdílových záloh**.
      - Vyberte **inicializovat protokol oblasti**.

   ![Nastavení oblasti inicializace protokolu](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Vyberte **Finish** (Dokončit).

   ![Dokončení obnovení zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Průběh, jako je popsaný tady, by se zobrazit. Mějte na paměti, že v příkladu je obnovení pro zotavení po havárii konfigurace škálování na více instancí SAP HANA třemi uzly.

![Průběh obnovení](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Pokud obnovení zdá se, že přestane reagovat na **Dokončit** obrazovky a nemá zobrazit obrazovka průběhu, ověřte, zda jsou spuštěny všechny instance SAP HANA na pracovních uzlech. V případě potřeby spusťte ručně instance systému SAP HANA.


## <a name="failback-from-a-dr-to-a-production-site"></a>Navrácení služeb po obnovení z zotavení po Havárii pro produkční lokality
Může selhat obnovení z zotavení po Havárii pro produkční lokality. Podívejme se na scénář, ve kterém se způsobilo převzetí služeb při selhání do lokality pro obnovení po havárii problémy v produkčním prostředí oblasti Azure a ne potřebou obnovení ztracených dat. Je už běží vaše produkční úlohy SAP nějakou v lokalitě zotavení po havárii. Jako jsou vyřešené problémy v produkční lokality, chcete po obnovení zpět na svůj provozní server. Vzhledem k tomu, že nemůže dojít ke ztrátě dat, krok zpátky do produkční lokality zahrnuje několik kroků a úzké spolupráci s SAP HANA v Azure provozního týmu. To je na vás k aktivaci provozní tým zahájení synchronizace zpět na pracoviště, jakmile jsou vyřešeny problémy.

Toto je posloupnost kroků:

1. SAP HANA v Azure provozní tým získá aktivační událost synchronizace produkční svazky úložiště ze svazků úložiště obnovení po havárii, které nyní představují provozního stavu. V tomto stavu je vypnout velká Instance HANA jednotku v produkční lokality.
1. SAP HANA v Azure provozní tým sleduje replikace a zajišťuje, že to je zachycena před oznamující.
1. Vypnutí aplikace, které používají produkční HANA Instance v lokalitě zotavení po havárii. Pak provést zálohování protokolu transakcí HANA. V dalším kroku zastavit instanci HANA spuštěné na velká Instance HANA jednotky v lokalitě zotavení po havárii.
1. Po instanci HANA spuštěné v jednotce velká Instance HANA v lokalitě zotavení po havárii je vypnutý, provozní tým ruční synchronizaci diskové svazky.
1. SAP HANA v Azure provozní tým znovu spustí velká Instance HANA jednotka v produkčním a předá ho pro vás. Zajistíte tím, že je ve stavu vypnutí instance SAP HANA v době spuštění jednotky velká Instance HANA.
1. Můžete provést stejný postup obnovení databáze, stejně jako při dříve převzetí služeb při selhání do lokality pro obnovení po havárii.

## <a name="monitor-disaster-recovery-replication"></a>Monitorování replikace pro zotavení po havárii

Stav průběhu replikace úložiště můžete monitorovat spuštěním skriptu `azure_hana_replication_status`. Tento příkaz musí spustit z jednotky spuštění v umístění pro obnovení po havárii fungovat podle očekávání. Příkaz funguje bez ohledu na to, zda je replikace aktivní. Tento příkaz lze spustit pro každou jednotku velká Instance HANA vašeho tenanta v umístění pro obnovení po havárii. Nelze získat informace o spouštěcím svazku. Podrobnosti o příkazu a její výstup. Přečtěte si **"Získat stav replikace pro DR - azure_hana_replication_status"** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


## <a name="next-steps"></a>Další postup
- Odkazovat na [monitorování a řešení potíží s ze strany HANA](hana-monitor-troubleshoot.md).
