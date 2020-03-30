---
title: Hana převzetí služeb při selhání postup na lokalitu havárie pro SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Jak provést převzetí služeb při selhání lokality zotavení po havárii pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fe3ee79318ab9fdc9f2c0e9585051439b76b5cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617142"
---
# <a name="disaster-recovery-failover-procedure"></a>Postup převzetí služeb při selhání pro zotavení po havárii


>[!IMPORTANT]
>Tento článek není náhradou za dokumentaci pro správu SAP HANA nebo poznámky SAP. Očekáváme, že máte solidní znalosti a odborné znalosti v sap hana správy a operací, zejména pro zálohování, obnovení, vysokou dostupnost a zotavení po havárii (DR). V tomto článku jsou zobrazeny snímky obrazovky z SAP HANA Studio. Obsah, struktura a povaha obrazovek nástrojů pro správu SAP a samotných nástrojů se mohou změnit z vydání SAP HANA na vydání.

Při převzetí služeb při selhání na webu zotavení po Havárii je třeba zvážit dva případy:

- Potřebujete databázi SAP HANA, abyste se vrátili k nejnovějšímu stavu dat. V tomto případě existuje samoobslužný skript, pomocí kterého můžete provést převzetí služeb při selhání bez nutnosti kontaktovat společnost Microsoft. Pro navrácení služeb po selhání je třeba pracovat se společností Microsoft.
- Chcete obnovit snímek úložiště, který není nejnovější replikovaný snímek. V takovém případě musíte pracovat se společností Microsoft. 

>[!NOTE]
>Následující kroky musí být provedeny na jednotce velké instance HANA, která představuje jednotku zotavení po havárii. 
 
Chcete-li obnovit nejnovější snímky replikovaného úložiště, postupujte podle pokynů v části Provedení úplného převzetí služeb při selhání - azure_hana_dr_failover" v [nástrojích snímků Microsoftu pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)v Azure . 

Pokud chcete mít více sap hana instance se nezdařilo, spusťte příkaz azure_hana_dr_failover několikrát. Na požádání zadejte SAP HANA SID, který chcete přepojit na selhání a obnovit. 


Převzetí služeb při selhání zotavení po havárii můžete také otestovat bez ovlivnění skutečného vztahu replikace. Chcete-li provést test převzetí služeb při selhání, postupujte podle kroků v části Provedení testu převzetí služeb při selhání zotavení po havárii – azure_hana_test_dr_failover" v [nástrojích snímek Microsoftu pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)v Azure . 

>[!IMPORTANT]
>*Nespouštějte* žádné produkční transakce na instanci, kterou jste vytvořili na webu zotavení po Havárii, a to prostřednictvím procesu **testování převzetí služeb při selhání**. Příkaz azure_hana_test_dr_failover vytvoří sadu svazků, které nemají žádný vztah k primární lokalitě. V důsledku toho *není* synchronizace zpět do primární lokality možná. 

Pokud chcete mít více sap hana instance k testování, spusťte skript několikrát. Na požádání zadejte SAP HANA SID instance, kterou chcete otestovat pro převzetí služeb při selhání. 

>[!NOTE]
>Pokud potřebujete převzetí služeb při selhání na webu zotavení po Havárii k záchraně některých dat, která byla odstraněna před několika hodinami a potřebujete svazky zotavení po havárii nastavit na dřívější snímek, použije se tento postup. 

1. Vypněte neprodukční instanci HANA na jednotce zotavení po havárii hana velké instance, které používáte. Spící instance výroby HANA je předinstalována.
1. Ujistěte se, že jsou spuštěny žádné procesy SAP HANA. Pro tuto kontrolu použijte následující příkaz:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      Výstup by měl zobrazit proces **hdbdaemon** v zastaveném stavu a žádné jiné procesy HANA v spuštěném nebo spuštěném stavu.
1. Určete, do kterého názvu snímku nebo ID zálohy SAP HANA chcete obnovit lokalitu pro zotavení po havárii. V případech zotavení po havárii skutečné zotavení po havárii tento snímek je obvykle nejnovější snímek. Pokud potřebujete obnovit ztracená data, vyberte dřívější snímek.
1. Kontaktujte podporu Azure prostřednictvím žádosti o podporu s vysokou prioritou. Požádejte o obnovení tohoto snímku s názvem a datem snímku nebo ID zálohy HANA na webu zotavení po Havárii. Výchozí hodnota je, že na straně operace obnoví pouze svazek /hana/data. Pokud chcete mít /hana/logbackups svazky příliš, je třeba konkrétně uvést, že. *Neobnovte svazek /hana/shared.* Místo toho zvolte konkrétní soubory, jako je global.ini z adresáře **.snapshot** a jeho podadresářů po opětovném připojení /hana/sdílený svazek pro PRD. 

   Na straně operací dochází k následujícím krokům:

   a. Replikace snímků z produkčního svazku na svazky zotavení po havárii je zastavena. K tomuto narušení mohlo již dojít, pokud je výpadek v produkčním místě důvodem, proč je nutné provést postup zotavení po havárii.
   
   b. Název snímku úložiště nebo snímek s ID zálohy, které jste zvolili, se obnoví na svazcích zotavení po havárii.
   
   c. Po obnovení jsou svazky zotavení po havárii k dispozici pro montáž do jednotek velké instance HANA v oblasti zotavení po havárii.
      
1. Připojte svazky zotavení po havárii do jednotky velké instance HANA v lokalitě zotavení po havárii. 
1. Spusťte spící instanci výroby SAP HANA.
1. Pokud jste se rozhodli zkopírovat protokoly zálohování protokolu transakcí, abyste zkrátili dobu rpo, sloučíte zálohy transakčních protokolů do nově připojeného adresáře DR /hana/logbackups. Nepřepisujte existující zálohy. Zkopírujte novější zálohy, které nebyly replikovány s nejnovější replikací snímku úložiště.
1. Můžete také obnovit jednotlivé soubory ze snímků, které nebyly replikovány na svazek /hana/shared/PRD v oblasti Azure zotavení po Havárii.

Následující kroky ukazují, jak obnovit produkční instanci SAP HANA na základě snímku obnoveného úložiště a záloh protokolu transakcí, které jsou k dispozici.

1. Změňte umístění zálohy na **/hana/logbackups** pomocí SAP HANA Studio.

   ![Změna umístění zálohy pro zotavení po havárii](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA prohledá umístění záložních souborů a navrhne nejnovější zálohu protokolu transakcí, kterou chcete obnovit. Skenování může trvat několik minut, než se zobrazí následující obrazovka:

   ![Seznam záloh transakčních protokolů pro obnovení zotavení po havárii](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Upravte některá výchozí nastavení:

      - Zrušte **zaškrtnutí možnosti Použít rozdílové zálohy**.
      - Vyberte **možnost Inicializovat oblast protokolu**.

   ![Nastavení oblasti protokolu inicializovat](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Vyberte **Finish** (Dokončit).

   ![Dokončení obnovení zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Mělo by se zobrazit okno průběhu, jako je uvedeno zde. Mějte na paměti, že příkladem je obnovení zotavení po havárii konfigurace SAP HANA se třemi uznami.

![Obnovení průběhu](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Pokud obnovení přestane reagovat na obrazovce **Dokončení** a nezobrazuje obrazovku průběhu, potvrďte, že jsou spuštěny všechny instance SAP HANA na pracovních uzlech. V případě potřeby spusťte instance SAP HANA ručně.


## <a name="failback-from-a-dr-to-a-production-site"></a>Navrácení služeb po selhání z dr. na výrobní pracoviště
Můžete navrácení služeb po selhání z dr. do výrobního pracoviště. Podívejme se na scénář, ve kterém převzetí služeb při selhání do lokality zotavení po havárii byla způsobena problémy v produkční oblasti Azure a ne vaše potřeba obnovit ztracená data. 

V lokalitě pro zotavení po havárii jste nějakou dobu spouštěli produkční úlohy SAP. Po vyřešení problémů ve výrobním pracovišti chcete vrátit do produkčního závodu. Vzhledem k tomu, že nemůžete ztratit data, krok zpět do produkčního webu zahrnuje několik kroků a úzkou spolupráci s sap HANA v provozním týmu Azure. Je na vás, abyste aktivovali operační tým a začali se synchronizovat zpět do produkčního pracoviště po vyřešení problémů.

Postupujte následovně:

1. Operační tým SAP HANA v Azure získá aktivační událost pro synchronizaci svazků produkčního úložiště z svazků úložiště pro zotavení po havárii, které nyní představují stav výroby. V tomto stavu hana velká instance jednotka ve výrobním pracovišti je vypnut.
1. Operační tým SAP HANA v Azure monitoruje replikaci a zajišťuje, že je zachycena dříve, než vás informují.
1. Vypnete aplikace, které používají produkční instanci HANA v lokalitě pro zotavení po havárii. Potom provést záloha protokolu transakcí HANA. Dále zastavíte instanci HANA, která je spuštěna v jednotkách velké instance HANA v lokalitě zotavení po havárii.
1. Po ukončení instance HANA, která je spuštěna v jednotce velké instance HANA v lokalitě zotavení po havárii, operační tým znovu ručně synchronizuje svazky disku.
1. Operační tým SAP HANA v Azure znovu spustí jednotku velké instance HANA v produkčním pracovišti. Předají ti to. Ujistěte se, že instance SAP HANA je ve stavu vypnutí v době spuštění jednotky velké instance HANA.
1. Provedete stejné kroky obnovení databáze, které jste provedli při předchozím převzetí služby při selhání webu pro zotavení po havárii.

## <a name="monitor-disaster-recovery-replication"></a>Sledování replikace zotavení po havárii

Chcete-li sledovat stav průběhu replikace `azure_hana_replication_status`úložiště, spusťte skript . Tento příkaz musí být spuštěn z jednotky, která běží v umístění zotavení po havárii fungovat podle očekávání. Příkaz funguje bez ohledu na to, zda je replikace aktivní. Příkaz lze spustit pro každou jednotku velké instance HANA vašeho tenanta v umístění zotavení po havárii. Nelze jej použít k získání podrobností o spouštěcím svazku. 

Další informace o příkazu a jeho výstupu najdete v tématu "Získání stavu replikace ZOTAVENÍ po celé azure_hana_replication_status" v [nástrojích snímků Microsoftu pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.2/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.2.1.pdf)v Azure .


## <a name="next-steps"></a>Další kroky
- Viz [Sledování a řešení potíží ze strany HANA](hana-monitor-troubleshoot.md).
