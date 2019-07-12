---
title: HANA zálohování a obnovení na systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Proveďte HANA zálohování a obnovení na systému SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e64c243e38c43c5eb543c3e2ec96d7cf8413cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709779"
---
# <a name="backup-and-restore"></a>Zálohování a obnovení

>[!IMPORTANT]
>Tento článek není náhradou za dokumentaci správu SAP HANA nebo SAP poznámky. Očekáváme, že máte důkladného porozumění a odborných znalostí v SAP HANA správu a provoz, zejména pro zálohování, obnovení, vysokou dostupnost a zotavení po havárii. V tomto článku se zobrazí snímky obrazovky ze SAP HANA Studio. Obsah, struktura a povaze obrazovky nástroje pro správu SAP a nástrojů, samotné se mohou změnit ze SAP HANA verzí.

Je důležité věnovat kroky a postupy provést ve vašem prostředí a s HANA verzí a verzí. Některé procesy popsané v tomto článku jsou zjednodušené pro lepší porozumění Obecné. Nejsou určeny pro použití jako podrobný postup pro konečný výsledek operace příruček. Pokud chcete vytvořit operaci příruček pro konkrétní konfiguraci, testování a výkon vašich procesů a zdokumentujte procesy související s konkrétní konfigurací. 

Jedním z nejdůležitějších aspektů provozní databáze je chránit před katastrofickými událostmi. Příčinu tyto události můžou být čímkoli od přírodními katastrofami na jednoduché uživatelské chyby.

Zálohování databáze, možnost obnovit do libovolného bodu v čase, například před někdo odstranil důležitých dat, umožňuje obnovení do stavu, ve kterém je jako nejblíže způsobu, jakým byl před narušení.

Dva typy zálohy je nutné provést k dosažení možnosti obnovení:

- Zálohování databází: Plný, přírůstkové nebo rozdílové zálohy
- Zálohy transakčního protokolu

Kromě zálohování úplné databáze provádí na úrovni aplikace můžete provádět zálohování pomocí snímků úložiště. Snímky úložiště nemáte nahradit zálohy transakčního protokolu. Zálohy transakčního protokolu zůstanou důležité obnovení databáze do určitého bodu v čase nebo na prázdný protokoly z již potvrzené transakce. Snímky úložiště urychlit obnovení rychle poskytnutím vpřed snímek databáze. 

SAP HANA v Azure (velké instance) nabízí dvě možnosti zálohování a obnovení:

- **To udělat sami (DIY).** Po můžete mít jistotu, že je dostatek místa na disku, proveďte kompletní databáze a zálohování protokolů pomocí jedné z následujících metod zálohování disku. Můžete zálohovat přímo na svazky připojené sdílené složky NFS, které jsou nastaveny v Azure virtuální počítač (VM) nebo velká Instance HANA jednotek. V takovém případě zákazníci nastavení virtuálního počítače s Linuxem v Azure, připojit k virtuálnímu počítači Azure Storage a sdílejí úložiště přes nakonfigurovaným serverem systému souborů NFS v tomto virtuálním počítači. Pokud provádíte zálohování u svazků, které přímo připojit k velká Instance HANA jednotky, zkopírujte zálohy do účtu služby Azure storage. Tomu se po nastavení virtuálního počítače Azure, který exportuje sdílených složek NFS, které jsou založené na Azure Storage. Můžete také použít Azure Backup vault nebo Azure studeného úložiště. 

   Další možností je použít nástroj na ochranu dat třetí strany a uložte zálohy po nezkopírují do účtu služby Azure storage. Rozběhněte zcela možnost zálohování také může být nezbytné pro data, která potřebujete ukládat pro delší dobu pro účely auditování a dodržování předpisů. Ve všech případech se zálohy jsou zkopírovány do sdílené složky NFS představovány virtuálního počítače a služby Azure Storage.

- **Infrastruktura zálohování a obnovení.** Také můžete použít zálohování a obnovení funkce, které poskytuje základní infrastruktury SAP HANA v Azure (velké instance). Tato možnost splňuje potřeby zálohování a rychlé obnovení. Zbývající část adresy funkce zálohování a obnovení, které se nabízí s velkých instancích HANA. Tato část platí i pro relaci, třeba zálohování a obnovení po havárii ve velkých instancích HANA nabízí funkce obnovení.

> [!NOTE]
>   Snímek technologie, která používá základní infrastruktury velkých instancích HANA obsahuje závislost na snímky SAP HANA. V tomto okamžiku SAP HANA snímky nefunguje ve spojení s více tenanty kontejnerů víceklientské databáze SAP HANA. Pokud jen pro nasazení jednoho klienta pracovní snímky SAP HANA a použijete tuto metodu.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Použití snímků úložiště SAP Hana v Azure (velké instance)

Úložiště infrastruktury základních SAP HANA v Azure (velké instance) podporuje úložiště snímků svazků. Zálohování a obnovení svazků se podporuje, s následující aspekty:

- Místo úplné zálohy databáze pocházejí snímků svazků úložiště často.
- Pokud snímek se aktivuje přes /hana/data a /hana/shared, zahrnující /usr/sap, svazky, technologie snímku zahájí SAP HANA snímek před běží snímek úložiště. Tento snímek SAP HANA je bod instalace pro konečné protokolu obnovení po obnovení snímku úložiště. Pro HANA snímek úspěšný potřebujete aktivní instance HANA. Ve scénáři HSR snímek úložiště nepodporuje aktuální sekundární uzel, ve kterém nelze provést na snímku HANA.
- Po úspěšném spuštění snímků úložiště SAP HANA snímek odstraní.
- Zálohy transakčního protokolu jsou často přijatá a uložená ve svazku /hana/logbackups nebo v Azure. Můžete aktivovat /hana/logbackups svazek, který obsahuje zálohy transakčního protokolu na pořízení snímku samostatně. V takovém případě není nutné ke spuštění HANA snímku.
- Pokud musí obnovení databáze do určitého bodu v čase pro produkční výpadek, žádosti o tuto podporu Microsoft Azure nebo SAP HANA na Azure obnovení do určité úložiště snímku. Příkladem je plánované obnovení systému izolovaného prostoru do původního stavu.
- Snímek SAP HANA, který je součástí úložiště snímku je posunu bod pro aplikování záloh protokolů transakcí, které byl spuštěn a ukládaly po pořízení snímku úložiště.
- Tyto zálohy protokolu transakcí se přesunete na obnovení databáze zpět do určitého bodu v čase.

Můžete provádět snímků úložiště, které se zaměřují tři třídy svazků:

- Kombinované snímku/hana/daty a/hana/sdílené, která zahrnuje /usr/sap. Tento snímek vyžaduje vytvoření snímku SAP HANA jako příprava pro snímek úložiště. Snímek SAP HANA se zajistí, že databáze je v konzistentním stavu z hlediska úložiště. Pro proces obnovení, který je bod na nastavit.
- Samostatný snímek přes/hana/logbackups.
- Oddíl s operačním systémem.

Pokud chcete získat nejnovější snímek skripty a dokumentaci, naleznete v tématu [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Při stahování balíčku skriptu snímků z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), získáte tři soubory. Jeden ze souborů je popsána ve formátu PDF pro funkce k dispozici. Poté, co si stáhnete sadu nástrojů, postupujte podle pokynů v "získat nástroje snapshot."

## <a name="storage-snapshot-considerations"></a>Aspekty volby úložiště snímku

>[!NOTE]
>Úložiště snímků využívání prostoru úložiště, který je přidělen velká Instance HANA jednotky. Vezměte v úvahu následující aspekty plánování úložiště snímků a kolik snímků úložiště udržovat. 

Konkrétní mechanismus úložiště snímků pro SAP HANA v Azure (velké instance), které patří:

- Konkrétní úložiště snímku v bodě v čase, kdy je přijata využívá malé úložiště.
- Jako data změny obsahu a obsahu v SAP HANA data, která změnit soubory na svazku úložiště snímku potřebuje pro uložení původního obsahu bloku a změny.
- Snímek úložiště v důsledku toho zvětší velikost. Čím déle existuje snímku, stává čím větší úložiště snímku.
- Další změny provedené svazku databáze SAP HANA během životního cyklu úložiště snímků, tím větší využití místa na snímku úložiště.

SAP HANA v Azure (velké instance) se dodává s velikostí pevný svazek pro SAP HANA svazky dat a protokolů. Provádí se snímky těchto svazků eats do místo ve svazku. Budete muset:

- Určuje, kdy chcete naplánovat snímků úložiště.
- Monitorujte využití prostoru úložiště svazky. 
- Spravujte počet snímků, které ukládáte. 

Při importu hmotnosti dat nebo provádět jiné významné změny v databázi HANA, můžete zakázat snímků úložiště. 


Následující části poskytují informace pro provedení tyto snímky a zahrnují obecná doporučení:

- I když hardware udržet 255 snímků na jeden svazek, budete chtít zůstat dobře pod tuto hodnotu. Doporučení je 250 nebo méně.
- Před provedením úložiště snímků, monitorovat a udržovat přehled o volného místa.
- Snížení počtu snímků úložiště podle volného místa. Můžete snížit počet snímků, které je zachovat, nebo je možné rozšířit svazky. Můžete si objednat další úložiště v jednotkách 1 terabajt.
- Během aktivity, například přesun dat do SAP HANA s nástroji pro migraci platformy SAP (R3load) nebo obnovení ze zálohy databáze SAP HANA zakažte snímků úložiště na svazku /hana/data. 
- Během větší uspořádání tabulek SAP HANA Pokud je to možné vyhnout se snímků úložiště.
- Snímky úložiště jsou předpokladem pro využití výhod po havárii možnosti zotavení systému SAP HANA v Azure (velké instance).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Předpoklady pro použití snímků samoobslužné služby storage

Ujistěte se, že úspěšném spuštění skriptu snímku, ujistěte se, že Perl je nainstalován na operační systém Linux na velkých instancích HANA serveru. Perl předinstalovaného na velká Instance HANA jednotky. Pokud chcete zkontrolovat verzi jazyka Perl, použijte následující příkaz:

`perl -v`

![Veřejný klíč se zkopíruje spuštěním tohoto příkazu](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Nastavení úložiště snímků

Nastavení úložiště snímků s velkých instancích HANA, postupujte podle těchto kroků.
1. Ujistěte se, zda Perl je nainstalován operační systém Linux na velkých instancích HANA serveru.
1. Upravit/etc/ssh/ssh\_config přidejte řádek _Mac hmac – sha1_.
1. Vytvořte záložní uživatelský účet SAP HANA na hlavní uzel pro každou instanci SAP HANA, který spouštíte, pokud je k dispozici.
1. Instalace klienta SAP HANA HDB ve všech serverech velké instance SAP HANA.
1. Na prvním serveru velké instance SAP HANA každé oblasti vytvořte veřejný klíč pro přístup k základní infrastrukturu úložiště, který řídí vytvoření snímku.
1. Skripty a konfigurační soubor z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) umístění **hdbsql** instalace SAP HANA.
1. Upravit *HANABackupDetails.txt* souboru podle potřeby pro specifikace příslušného zákazníka.

Získat nejnovější snímek skripty a dokumentaci k [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Pokyny uvedené dříve, najdete v článku [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

### <a name="consideration-for-mcod-scenarios"></a>Pečlivě zvážit u MCOD scénáře
Při spuštění [MCOD scénář](https://launchpad.support.sap.com/#/notes/1681092) s více instancí SAP HANA na jednu jednotku velká Instance HANA, budete mít různé svazky, které jsou zřízené pro jednotlivé instance SAP HANA. Další informace o MDC a další důležité informace, najdete v části "Hlavně třeba si zapamatovat" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Instalace klienta SAP HANA HDB

Operační systém Linux nainstalován na systému SAP HANA v Azure (velké instance) obsahuje složky a skripty potřebné ke spuštění obnovení účely snímků úložiště SAP HANA pro zálohování a po havárii. Kontrola pro novější verze v [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Nejnovější verze skriptů je 4.0. Jiné skripty může mít jiné vedlejší verze v rámci stejné hlavní verze.

Je vaší odpovědností, abyste instalaci klienta SAP HANA HDB na jednotkách velká Instance HANA v průběhu instalace SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Krok 2: Změnit/etc/ssh/ssh\_config

Tento krok je popsaný v "Povolit komunikaci s úložištěm" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-3-create-a-public-key"></a>Krok 3: Vytvoření veřejného klíče

Pokud chcete povolit přístup k rozhraní storage snímku tenanta velká Instance HANA, vytvořte postup přihlášení přes veřejný klíč. 

Na první SAP HANA v Azure (velké instance) serveru ve vašem tenantovi vytvořte veřejný klíč pro přístup k infrastruktuře úložiště. S veřejným klíčem není vyžadováno heslo pro přihlášení k rozhraní úložiště snímku. Také není nutné udržovat přihlašovacího hesla s veřejným klíčem. 

Generování veřejného klíče, najdete v sekci "Povolit komunikaci s úložištěm" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).


### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Vytvořit uživatelský účet SAP HANA

Spusťte vytváření snímků SAP HANA, vytvořte účet uživatele v SAP HANA, můžete použít skripty snímků úložiště. Vytvořte uživatelský účet SAP HANA v rámci SAP HANA Studio pro tento účel. Uživatel musí vytvořit v části SYSTEMDB a *není* v databázi identifikátor SID pro MDC. V prostředí jednoho kontejneru se vytvoří uživatele databáze tenanta. Tento účet musí mít **správce zálohování** a **katalogu čtení** oprávnění. 

Nastavit a používat účet uživatele, najdete v sekci "Povolit komunikaci se SAP HANA" v [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: Povolit uživatelský účet SAP HANA

V tomto kroku povolíte SAP HANA uživatelský účet, který jste vytvořili, tak, aby skripty nemusíte odeslání hesla za běhu. Příkaz SAP HANA `hdbuserstore` umožňuje vytvoření klíče uživatele SAP HANA. Klíč je uložen na jeden nebo více uzlů SAP HANA. Uživatelský klíč mu umožní přístup SAP HANA bez nutnosti spravovat hesla z v rámci skriptovací procesu. Skriptovací proces je popsán dále v tomto článku.

>[!IMPORTANT]
>Pomocí stejného uživatelského kontextu, které jsou spouštěny příkazy snímku v spuštěním těchto příkazů konfigurace. Příkazy snímku, jinak nebude správně fungovat.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: Získání skriptů snímku, konfigurace snímků a testování konfigurací a připojením

Stáhněte si nejnovější verzi skripty z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Změnit způsob, jakým tyto skripty jsou nainstalované verze 4.0 skripty. Další informace najdete v tématu "Povolit komunikaci se SAP HANA" [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Přesné pořadí příkazů najdete v části "Snadnou instalaci nástroje snímku (výchozí)" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Doporučujeme používat výchozí instalaci. 

Upgrade z verze 3.x na 4.0, naleznete v části "Upgrade existující instalace" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Chcete-li odinstalovat sadu nástrojů verze 4.0, naleznete v části "Odinstalace nástroje snapshot" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Nezapomeňte spustit postup popsaný v "Dokončit nastavení snímku nástroje" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Účelem jiné skripty a soubory podle jejich proběhla úspěšně, je popsaná v "Co jsou tyto nástroje snímku?" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Před konfigurací nástroje snímku, ujistěte se, že také nakonfigurovat záložní umístění HANA a nastavení správně. Další informace najdete v tématu "SAP HANA konfigurace" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Konfigurace sady nástrojů snímku je popsána v "Konfigurační soubor - HANABackupCustomerDetails.txt" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-connectivity-with-sap-hana"></a>Test připojení se SAP HANA

Po uvedení všech konfiguračních dat do *HANABackupCustomerDetails.txt* soubor, zkontrolujte, zda jsou správná HANA instance data konfigurace. Pomocí skriptu `testHANAConnection`, který je nezávislý na SAP HANA vertikální nebo horizontální navýšení kapacity konfigurací.

Další informace najdete v části "Kontrola připojení k SAP HANA – testHANAConnection" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="test-storage-connectivity"></a>Test připojení úložiště

Dalším krokem testu je ke kontrole připojení k úložišti na základě dat vložte do *HANABackupCustomerDetails.txt* konfigurační soubor. Spusťte test snímku. Před spuštěním `azure_hana_backup` příkaz, musíte spustit tento test. Sekvence příkazů pro tento test, najdete v části "Kontrola připojení storage – testStorageSnapshotConnection" "v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Po úspěšném přihlášení do rozhraní úložiště virtuálního počítače skript pokračuje fáze 2 a vytvoří snímek testu. Výstup je znázorněna zde pro horizontální navýšení kapacity konfigurací SAP HANA třemi uzly.

Pokud test snímku úspěšném spuštění skriptu můžete naplánovat skutečného úložiště snímků. Pokud není úspěšné, prozkoumejte předtím, než budete pokračovat následujícími problémy. Snímek testu by mělo zůstat kolem až do dokončení prvních skutečné snímků.


### <a name="step-7-perform-snapshots"></a>Krok 7: Provedení snímky

Po dokončení přípravné kroky, chcete začít konfigurovat a naplánovat skutečného úložiště snímků. Skript, který chcete naplánovat funguje vertikálně navýšit kapacitu a horizontální navýšení kapacity konfigurací SAP HANA. Pro pravidelné a pravidelné spouštění skriptu zálohování plánování skriptu pomocí nástroje cron. 

Syntaxe příkazu přesně a funkci, naleznete v části "Provést snímek zálohy - azure_hana_backup" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 

Pokud skript `azure_hana_backup` běží, se vytvoří úložiště snímků tyto tři fáze:

1. Spustí se snímek SAP HANA.
1. Běží snímek úložiště.
1. Odstraní snímek SAP HANA, který byl vytvořen před spustili snímků úložiště.

Pro spuštění skriptu, jeho volání z složku HDB spustitelného souboru, do kterého byl zkopírován. 

Doba uchování je možné spravovat pomocí počet snímků, které jsou odeslány jako parametr při spuštění skriptu. Množství času, která je předmětem snímků úložiště je funkce doby spuštění a počet snímků odeslat jako parametr při spuštění skriptu. 

Pokud počet snímků, které jsou zachovány překročí číslo, které jsou pojmenovány jako parametr ve volání skriptu, odstraní se nejstarší úložiště snímku stejný jmenovce předtím, než spustí nový snímek. Číslo je zadat jako poslední parametr volání je číslo vám pomůže určit počet snímků, které jsou zachovány. S tímto číslem také můžete řídit, nepřímo, místo na disku, který se používá pro snímky. 


## <a name="snapshot-strategies"></a>Strategie snímku
Frekvence snímků pro různé typy, které závisí na, jestli používáte funkce zotavení po havárii velká Instance HANA. Tato funkce spoléhá na snímcích úložiště, které můžou vyžadovat zvláštní doporučení pro četnost a provádění období snímků úložiště. 

Důležité informace a doporučení, které následují, předpokladem je, že provedete *není* použít funkci obnovení po havárii, která nabízí velké instance HANA. Místo toho použijte snímků úložiště záloh a být schopen zajistit obnovení bodu v čase za posledních 30 dní. Zadané omezení počtu snímků a místa, vezměte v úvahu následující požadavky:

- Čas obnovení pro obnovení bodu v čase.
- Využité místo.
- Bod obnovení a cíle plánované doby obnovení potenciální zotavení po havárii.
- Konečný výsledek spuštění zálohování úplné databáze HANA proti disky. Pokaždé, když zálohu celé databáze proti disky nebo **backint** rozhraní se provádí, se nezdaří spuštění snímků úložiště. Pokud budete chtít provádět zálohování úplné databáze nad úložiště snímků, ujistěte se, že během této doby je zakázána provádění snímků úložiště.
- Počet snímků na jeden svazek, který je omezená na 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Pokud nepoužíváte funkce zotavení po havárii velkých instancích HANA, doba snímku je méně často. V takových případech proveďte kombinované snímky v /hana/data a /hana/shared, která zahrnuje /usr/sap, v období 12 hodin nebo 24 hodin. Snímky zachovat po dobu jednoho měsíce. Totéž platí pro snímky záložní svazek protokolu. Spuštění SAP HANA zálohy protokolu transakce proti záložní svazek s protokolem 5 minut probíhá do 15 minut období.

Snímky naplánované úložiště se nejlépe provádí pomocí procesu cron. Použijte stejný skript pro všechny zálohy a potřeb obnovení po havárii. Upravit skript tak, aby odpovídaly různé vstupy požadované časy zálohování. Tyto snímky jsou všechny naplánovat jinak než v procesu cron v závislosti na jejich spuštění. Může být po hodinách, každých 12 hodin denně nebo týdně. 

Následující příklad ukazuje plán cron v /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
V předchozím příkladu se věnuje hodinové kombinované snímků svazků, které obsahují /hana/data a /hana/shared/SID, která zahrnuje /usr/sap, umístění. Pomocí tohoto typu snímku pro rychlejší obnovení bodu v čase během posledních dvou dnů. Je také denní snímek na těchto svazcích. Ano máte dva dny pokrytí podle hodinových snímky a čtyři týdny pokrytí podle denní snímky. Záložní svazek protokolu transakcí také zálohování denně. Tyto zálohy jsou udržovány pro čtyři týdny. 

Jak vidíte na třetím řádku crontab, zálohování protokolu transakcí HANA je naplánováno spuštění každých 5 minut. Čas zahájení úloh různých cron, na kterých běží snímků úložiště jsou rozloženo. Tímto způsobem snímky nejdou spustit všechny najednou v určitém bodě v čase. 

V následujícím příkladu je provést kombinované snímků, které pokrývá svazků, které obsahují /hana/data a /hana/shared/SID, která zahrnuje /usr/sap, umístění v hodinách. Tyto snímky se zachovat po dobu dvou dnů. Snímky svazků zálohování protokolu transakcí spustit na po 5 minutách a uchovávají po dobu čtyř hodin. Jako dříve, zálohování protokolu transakcí HANA je naplánované spuštění každých 5 minut. 

Snímku svazku pro zálohování protokolu transakcí se provádí s prodlevou 2 minuty, po spuštění zálohy transakčního protokolu. Za normálních okolností dokončení zálohování protokolu transakcí SAP HANA v rámci těchto 2 minut. Jako dříve, svazek, který obsahuje spouštěcí logické jednotky je zajištěná jednou denně snímku úložiště a uchovávají po dobu čtyř týdnů.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Pořadí v předchozím příkladu je znázorněný na následujícím obrázku. Spouštěcí logická jednotka je vyloučený.

![Vztah mezi zálohy a snímky](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA provádí pravidelné zápisů ve službě /hana/log svazek k dokumentaci potvrzené změny do databáze. SAP HANA v pravidelných intervalech, zapíše uloženého bodu do svazku /hana/data. Jak je uvedeno v crontab zálohování protokolu transakcí SAP HANA spustí každých 5 minut. 

Uvidíte také, že SAP HANA snímku spouští každou hodinu v důsledku aktivací kombinované úložiště snímků přes /hana/data a /hana/shared/SID svazky. Po úspěšném snímku HANA kombinované úložiště snímků spuštění. Podle pokynů v crontab, snímků úložiště na svazku /hana/logbackup spustí každých 5 minut, po zálohování protokolu transakcí HANA přibližně 2 minut.

> 

>[!IMPORTANT]
> Použití snímků úložiště pro zálohování SAP HANA je cenné pouze v případě, že snímky jsou prováděny současně se zálohami protokolů transakcí SAP HANA. Tyto zálohy transakčního protokolu muset zabývat časová období mezi snímky úložiště. 

Pokud jste nastavili závazku uživatelům obnovení bodu v čase za 30 dní, budete muset:

- Přístup k snímku kombinované úložiště přes /hana/data a /hana/shared/SID, které se 30 dnů, v extrémních případech. 
- Mají zálohy souvislých transakčního protokolu, které se týkají čas mezi jakékoli snímky, kombinované úložiště. Ano nejstarší snímku svazku pro zálohování protokolu transakce musí být 30 dnů. To neplatí, pokud kopírujete zálohy transakčního protokolu do jiné sdílené složky systému souborů NFS, který je umístěný ve službě Azure Storage. V takovém případě si mohou vyžádat starší zálohy protokolu transakce z této sdílené složky systému souborů NFS.

Výhody úložiště snímků a replikaci konečné úložiště zálohy transakčního protokolu, změňte umístění, ke kterému SAP HANA zapíše zálohy transakčního protokolu. Provedení této změny v HANA Studio. 

I když se SAP HANA zálohuje celý protokol segmenty automaticky, zadejte interval zálohování protokolu být deterministický. To platí zejména při použití možnosti pro zotavení po havárii vzhledem k tomu obvykle chcete spustit zálohování protokolu s deterministickou období. V následujícím případě 15 minut je nastavena jako interval zálohování protokolů.

![Naplánovat protokoly zálohování SAP HANA v SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Můžete také zvolit zálohy, které se častěji než každých 15 minut. Častější nastavení se často používá ve spojení s funkcemi pro zotavení po havárii velkých instancích HANA. Někteří zákazníci provést zálohování protokolu transakcí každých 5 minut.

Pokud databáze obsahuje byly dosud nezálohovali, posledním krokem je provedení zálohy databáze na základě souborů k vytvoření jedné položky zálohování, která musí existovat v rámci katalogu záloh. SAP HANA v opačném případě nemůžete zahájit zálohování zadaný protokol.

![Vytvořit zálohu souborů k vytvoření jedné položky zálohování](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po spuštění vaší první úspěšná úložiště snímků, odstranění snímku test, který spustil v kroku 6. Další informace najdete v tématu "Odebrat test snímky - removeTestStorageSnapshot" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorování počtu a velikosti snímků na diskovém svazku

Na konkrétní úložiště svazku můžete sledovat počet snímků a využití úložiště těchto snímků. `ls` Příkaz nezobrazí snímek adresáře nebo souborů. Příkaz operačních systémů Linux `du` zobrazuje podrobnosti o těchto snímků úložiště, protože jsou uloženy na stejném svazku. Použití příkazu s následujícími možnostmi:

- `du –sh .snapshot`: Tato možnost poskytuje celkový počet všechny snímky v adresáři snímku.
- `du –sh --max-depth=1`: Tato možnost uvádí všechny snímky, které jsou uloženy v **.snapshot** složky a velikost jednotlivých snímků.
- `du –hc`: Tato možnost poskytuje celkovou velikost používané všechny snímky.

Používejte tyto příkazy, abyste měli jistotu, že snímky, které jsou přijatá a uložená nekladou nadměrné nároky veškeré úložiště na svazcích.

>[!NOTE]
>Snímky spuštění logické jednotky nejsou viditelné pomocí předchozích příkazů.

### <a name="get-details-of-snapshots"></a>Získat podrobnosti o snímků
Chcete-li získat více podrobností o snímky, použijte skript `azure_hana_snapshot_details`. Tento skript můžete spustit v některém umístění, pokud je aktivní server v umístění pro obnovení po havárii. Skript obsahuje následující výstup rozdělit podle každý svazek, který obsahuje snímky: 
   * Velikost celkový počet snímků na svazku
   * Následující podrobnosti v jednotlivých snímků v tomto svazku: 
      - Název snímku 
      - Čas vytvoření 
      - Velikost snímku
      - Frekvence snímku
      - ID HANA zálohování přidružené k tomuto snímku, pokud je to potřeba

Syntaxe příkazu a výstupy, najdete v části "Výpis snímků - azure_hana_snapshot_details" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Snížit počet snímků na serveru

Jak bylo vysvětleno dříve můžete snížit počet některé popisky snímky, které ukládáte. Poslední dva parametry tohoto příkazu k zahájení snímku se popisek a počet snímků, které chcete zachovat.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

V předchozím příkladu je popisek snímku **dailyhana**. Počet snímků s tímto popiskem uchovávat **28**. Jak vám odpoví na využití místa na disku, můžete snížit počet uložené snímky. Je snadný způsob, jak snížit počet snímků na 15, například pro spuštění skriptu s posledním parametrem nastaveným na **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Pokud spouštíte skript s tímto nastavením, je počet snímků, která zahrnuje nový snímek úložiště, 15. 15 nejnovější snímky jsou uchovávány a 15 starší snímky se odstraní.

 >[!NOTE]
 > Tento skript snižuje počet snímků, pouze v případě, že existují snímky více než jednu hodinu stará. Skript nedojde k odstranění snímků, které jsou méně než hodinu stará. Tato omezení se vztahují k funkce zotavení po havárii volitelné nabízené.

Pokud už nechcete zachovat sadu snímků zálohy předponou **dailyhana** v příklady syntaxe, spusťte skript s **0** jako číslo uchovávání informací. Potom se odeberou všechny snímky, které odpovídají tohoto popisku. Odebírá se přístup ke všem snímkům může ovlivnit možnosti velkých instancích HANA funkce zotavení po havárii.

Druhá možnost odstranit konkrétní snímků je skript `azure_hana_snapshot_delete`. Tento skript slouží k odstranění snímku nebo sadu snímky, buď pomocí ID zálohy HANA jak se nachází v HANA Studio nebo prostřednictvím samotný název snímku. V současné době se zálohování ID pouze váže na snímky vytvořené pro **hana** typ snímku. Snímek zálohy typu **protokoly** a **spouštěcí** neprovádí SAP HANA snímku, takže není žádné ID zálohy pro tyto snímky. Pokud je zadán název snímku, hledá všechny snímky v různých svazcích, které odpovídají názvu zadané snímku. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Další informace o skriptu najdete v tématu "Odstranit snímek - azure_hana_snapshot_delete" v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Spusťte skript jako uživatel **kořenové**.

>[!IMPORTANT]
>Pokud se data, která existuje pouze na snímku máte v úmyslu odstranit po odstranění snímku, že dojde ke ztrátě dat navždy.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Obnovení na úrovni souboru ze snímku úložiště

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Pro typ snímku **hana** a **protokoly**, dostanete snímků přímo na svazky v **.snapshot** adresáře. Je podadresář pro jednotlivé snímky. Zkopíruje všechny soubory ve stavu, ve kterém se v okamžiku snímků z podadresář do tohoto adresáře struktury. 

V aktuální verzi skriptu je *žádné* skriptu pro obnovení snímku jako samoobslužné obnovení. Obnovení snímku lze provést jako součást skripty pro zotavení po havárii samoobslužné služby v lokalitě zotavení po havárii během převzetí služeb při selhání. Požadovaného snímku obnovit z existujícího k dispozici snímky, musíte kontaktovat provozní tým Microsoft tak, že otevřete žádost o službu.

>[!NOTE]
>Obnovení jednotlivých souborů nefunguje pro snímky spuštění logické jednotky nezávislé na typ jednotek velká Instance HANA. **.Snapshot** adresář není vystaveno v spouštěcí logickou jednotku. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Obnovit na nejnovější snímek HANA

V případě produkčního dolů lze spustit procesem obnovení ze snímku úložiště jako incident zákazníků s podporu Microsoft Azure. Je na vás vysokou naléhavost, pokud byla odstraněna data v produkčním prostředí systému a k obnovení provozní databáze je jediný způsob, jak ho načíst.

Obnovení bodu v čase v jiné situaci, může být s nízkou naléhavost a plánované dny předem. Můžete naplánovat toto obnovení se SAP HANA v Azure místo zvyšování příznak s vysokou prioritou. Například může Naplánujte upgrade softwaru SAP s použitím nového rozšíření balíčku. Pak budete muset obnovit snímek, který představuje stav před upgradem balíček rozšíření.

Před odesláním požadavku, je nutné připravit. SAP HANA v Azure týmu můžete žádost zpracovat a poskytují obnovené svazky. Následně obnovíte databázi HANA podle snímků.

Možnosti pro získání snímku obnovit s novou sadu nástrojů, naleznete v části "Jak obnovit snímek" v [ruční obnovení Průvodce pro SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Příprava pro žádosti, postupujte takto.

1. Rozhodněte, které snímek, který chcete obnovit. Pokud dáte pokyn, jinak se obnoví pouze hana/datový svazek. 

1. Vypnutí HANA instance.

   ![Vypnutí HANA instance](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odpojte objemy dat v každém uzlu databáze HANA. Pokud datové svazky se pořád připojený k operačnímu systému, obnovení snímku se nezdaří.

   ![Odpojte objemy dat v každém uzlu databáze HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otevřete žádost o podporu Azure a zahrňte pokyny k obnovení konkrétní snímek:

   - Během obnovení: SAP HANA v Azure Service může požádat o jeho účasti na konferenci koordinovat, ověřit a potvrdit, že je obnovit snímek správné úložiště. 

   - Po obnovení: SAP HANA ve službě Azure vás upozorní, když je obnovení snímku úložiště.

1. Po dokončení procesu obnovení znovu připojte všechny datové svazky.

   ![Znovu připojit všechny datové svazky](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Další možností, jak získat například SAP HANA datové soubory obnovit ze snímku úložiště, jsou uvedené v kroku 7 v [ruční obnovení Průvodce pro SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Chcete-li obnovit ze zálohy snímku, přečtěte si téma [ruční obnovení Průvodce pro SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 

>[!Note]
>Pokud vaše snímku se obnovila operacemi Microsoft, nemusíte kroku 7.


### <a name="recover-to-another-point-in-time"></a>Obnovení do jiného bodu v čase
Obnovit do určitého bodu v čase, najdete v sekci "Obnovit databázi do následující bod v čase" v [ruční obnovení Průvodce pro SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf). 


## <a name="next-steps"></a>Další postup
- Zobrazit [zásady zotavení po havárii a příprava](hana-concept-preparation.md).
