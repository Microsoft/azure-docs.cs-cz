---
title: HANA zálohování a obnovení na systému SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: Proveďte HANA zálohování a obnovení na systému SAP HANA v Azure (velké instance)
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
ms.openlocfilehash: 7c03a7e5763f580bf1e17232a5850064710c8227
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098316"
---
# <a name="backup-and-restore"></a>Zálohování a obnovení

>[!IMPORTANT]
>Tato dokumentace je nahrazen dokumentace správy SAP HANA nebo SAP poznámky. Očekává se, zda má čtečka důkladného porozumění a odborných znalostí v SAP HANA správu a provoz, zejména s tématy zálohování, obnovení, vysokou dostupnost a zotavení po havárii. V této dokumentaci se zobrazí snímky obrazovky ze SAP HANA Studio. Obsah, struktura a povaze obrazovky nástroje pro správu SAP a nástrojů, samotné se mohou změnit ze SAP HANA verzí.

Je důležité věnovat kroky a postupy provést ve vašem prostředí a s HANA verzí a verzí. Některé procesy popsané v této dokumentaci je zjednodušená pro lepší porozumění obecné a neměly by má být použit jako podrobný postup pro konečný výsledek operace příruček. Pokud chcete vytvořit operaci příruček pro konkrétní konfiguraci, musíte pro testování a výkon vašich procesů a zdokumentujte procesy související s konkrétní konfigurací. 

Jeden z nejdůležitějších aspektů do provozní databáze je chránit před katastrofickými událostmi. Příčinu tyto události můžou být čímkoli od přírodními katastrofami na jednoduché uživatelské chyby.

Zálohování databáze, možnost obnovit do libovolného bodu v čase (například než někdo odstraní důležitá data), umožňuje obnovení do stavu, ve kterém je co nejblíže způsobu, jakým byl před narušení.

Dva typy zálohování je nutné provést k dosažení možnost, chcete-li obnovit:

- Zálohování databází: Plný, přírůstkové nebo rozdílové zálohy
- Zálohy transakčního protokolu

Kromě zálohování úplné databáze provádí na úrovni aplikace můžete provádět zálohování pomocí snímků úložiště. Snímky úložiště nenahrazují zálohy transakčního protokolu. Zálohy transakčního protokolu zůstanou důležité obnovení databáze do určitého bodu v čase nebo na prázdný protokoly z již potvrzené transakce. Snímků úložiště lze však rychlejší zotavení rychle poskytnutím vpřed snímek databáze. 

SAP HANA v Azure (velké instance) nabízí dvě možnosti zálohování a obnovení:

- Sestavili (DIY). Po zajištění, že je dostatek místa na disku, proveďte úplné zálohování databáze a protokolu pomocí jedné z následujících metod zálohování disku. Můžete zálohovat přímo na svazky připojené jednotky velká Instance HANA, nebo do sdílené složky NFS (Network File), které jsou nastavené v Azure virtuální počítač (VM). V takovém případě zákazníci nastavení virtuálního počítače s Linuxem v Azure, připojit k virtuálnímu počítači Azure Storage a sdílejí úložiště přes nakonfigurovaným serverem systému souborů NFS v tomto virtuálním počítači. Pokud provádíte zálohování u svazků, které přímo připojit k jednotky velká Instance HANA, musíte zkopírovat zálohy do účtu služby Azure storage (po nastavení virtuálního počítače Azure, který exportuje sdílených složek NFS, které jsou založené na Azure Storage). Můžete také použít trezoru záloh Azure nebo Azure studeného úložiště. 

   Další možností je použít nástroj na ochranu dat třetí strany a uložte zálohy po zkopírování do účtu služby Azure storage. Rozběhněte zcela možnost zálohování může být také nezbytné pro data, která potřebujete ukládat pro delší dobu pro účely auditování a dodržování předpisů. Ve všech případech se zálohy jsou zkopírovány do sdílené složky NFS představovány virtuálního počítače a služby Azure Storage.

- Infrastruktura zálohování a obnovení. Můžete také použít funkci zálohování a obnovení, který poskytuje základní infrastruktury SAP HANA v Azure (velké instance). Tato možnost splňuje potřeby zálohování a rychlé obnovení. Zbývající část adresy funkce zálohování a obnovení, které se nabízí s velkých instancích HANA. Tato část se věnuje také relace zálohování a obnovení po havárii má nabízí funkce obnovení ve velkých instancích HANA.

> [!NOTE]
>   Snímek technologie, která používá základní infrastruktury velkých instancích HANA obsahuje závislost na snímky SAP HANA. V tomto okamžiku SAP HANA snímků nebudou fungovat ve spojení s více tenanty kontejnerů víceklientské databáze SAP HANA. Pokud jen pro nasazení jednoho klienta pracovní snímky SAP HANA a tuto metodu můžete použít.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Pomocí snímků úložiště SAP Hana v Azure (velké instance)

Úložiště infrastruktury základních SAP HANA v Azure (velké instance) podporuje úložiště snímků svazků. Zálohování a obnovení svazků se podporuje, s následující aspekty:

- Místo úplné zálohy databáze pocházejí snímků svazků úložiště často.
- Při aktivaci snímku /hana/data a /hana/shared (zahrnuje /usr/sap) svazky, technologie snímku zahájí SAP HANA snímku, než se provede snímků úložiště. Tento snímek SAP HANA je bod instalace pro konečné protokolu obnovení po obnovení snímku úložiště. Pro HANA snímek úspěšný potřebujete aktivní instance HANA.  Ve scénáři HSR snímek úložiště nepodporuje aktuální sekundárního uzlu, kde HANA snímku nelze provést.
- Po snímku úložiště byl úspěšně proveden, je odstranit snímek SAP HANA.
- Zálohy transakčního protokolu často pocházejí a jsou uloženy ve svazku /hana/logbackups nebo v Azure. Můžete aktivovat /hana/logbackups svazek, který obsahuje zálohy transakčního protokolu na pořízení snímku samostatně. V takovém případě není potřeba provést HANA snímku.
- Pokud musíte obnovit databázi do určitého bodu v čase, požádat o tuto podporu Microsoft Azure (pro produkční výpadek) nebo SAP HANA v Azure obnovení do určité úložiště snímku. Příkladem je plánované obnovení systému izolovaného prostoru do původního stavu.
- Snímek SAP HANA, který je součástí úložiště snímku je posunu bod pro aplikování záloh protokolů transakcí, které byly provedeny a uložena po pořízení snímku úložiště.
- Tyto zálohy protokolu transakcí se přesunete na obnovení databáze zpět do určitého bodu v čase.

Můžete provádět úložiště snímků, které cílí na tři třídy svazků:

- Kombinované snímku/hana/daty a /hana/shared (včetně/usr/sap). Tento snímek vyžaduje vytvoření snímku SAP HANA jako příprava pro snímek úložiště. Snímek SAP HANA zajišťuje, že databáze je v konzistentním stavu z hlediska úložiště. Na provoz a že pro obnovení zpracování, který je bod, který má nastavit.
- Samostatný snímek přes/hana/logbackups.
- Oddíl s operačním systémem.

Získat nejnovější snímek skripty a dokumentaci k [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Při stahování balíčku skriptu snímků z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0), získáte tři soubory, z nichž jedna je PDF dokumentaci pro funkce k dispozici. Ujistěte se, že budete pokračovat podél podle pokynů v kapitole 'Získávání nástroje snapshot' při stahování sady nástrojů.

## <a name="storage-snapshot-considerations"></a>Aspekty volby úložiště snímku

>[!NOTE]
>Úložiště snímků využívání prostoru úložiště, který byl přidělen do jednotky velká Instance HANA. Je potřeba zvážit následující aspekty plánování úložiště snímků a kolik snímků úložiště udržovat. 

Konkrétní mechanismus úložiště snímků pro SAP HANA v Azure (velké instance), které patří:

- Konkrétní úložiště snímku (v bodě v čase, kdy je provedena) využívá malé úložiště.
- Jako data změny obsahu a obsahu v SAP HANA data, která změnit soubory na svazku úložiště snímku potřebuje pro uložení původního obsahu bloku, jakož i změny.
- Snímek úložiště v důsledku toho zvětší velikost. Čím déle existuje snímku, stává čím větší úložiště snímku.
- Další změny provedené svazku databáze SAP HANA během životního cyklu úložiště snímků, tím větší využití místa na snímku úložiště.

SAP HANA v Azure (velké instance) se dodává s velikostí pevný svazek pro SAP HANA svazky dat a protokolů. Provádí se snímky těchto svazků eats do místo ve svazku. Je nutné určit, kdy se má naplánovat snímků úložiště. Budete také muset monitorovat využití prostoru úložiště svazky, stejně jako správu počet snímků, které ukládáte. Při importu hmotnosti dat nebo provádět jiné významné změny v databázi HANA, můžete zakázat snímků úložiště. 


Následující části obsahují informace pro provedení tyto snímky, včetně obecná doporučení:

- I když hardware udržet 255 snímků na jeden svazek, mají zůstat dobře pod tuto hodnotu. Doporučení je 250 nebo méně.
- Před provedením úložiště snímků, monitorovat a udržovat přehled o volného místa.
- Snížení počtu snímků úložiště podle volného místa. Můžete snížit počet snímků, které je zachovat, nebo je možné rozšířit svazky. Můžete si objednat další úložiště v jednotkách 1 terabajt.
- Během aktivity, například přesun dat do SAP HANA s nástroji pro migraci platformy SAP (R3load) nebo obnovení ze zálohy databáze SAP HANA zakažte snímků úložiště na svazku /hana/data. 
- Během větší uspořádání tabulek SAP HANA, snímků úložiště mělo by se vyhnout, pokud je to možné.
- Snímky úložiště jsou předpokladem pro využití výhod po havárii možnosti zotavení systému SAP HANA v Azure (velké instance).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Předpoklady pro použití snímků samoobslužné služby storage

Chcete-li zajistit, že se snímek skript spustí úspěšně, ujistěte se, že Perl je nainstalován na operační systém Linux na velkých instancích HANA serveru. Perl dodává s předinstalovanou na velká Instance HANA jednotky. Pokud chcete zkontrolovat verzi jazyka Perl, použijte následující příkaz:

`perl -v`

![Veřejný klíč se zkopíruje spuštěním tohoto příkazu](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Nastavení úložiště snímků

Nastavení úložiště snímků s velkých instancích HANA, postupujte podle těchto kroků:
1. Ujistěte se, zda Perl je nainstalován operační systém Linux na velkých instancích HANA serveru.
1. Upravit/etc/ssh/ssh\_config přidejte řádek _Mac hmac – sha1_.
1. Vytvořte záložní uživatelský účet SAP HANA na hlavní uzel pro každou instanci SAP HANA, které používáte, pokud je k dispozici.
1. Instalace klienta SAP HANA HDB ve všech serverech velké instance SAP HANA.
1. Na prvním serveru velké instance SAP HANA každé oblasti vytvořte veřejný klíč pro přístup k základní infrastrukturu úložiště, který řídí vytvoření snímku.
1. Skripty a konfigurační soubor z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) umístění **hdbsql** instalace SAP HANA.
1. Upravit *HANABackupDetails.txt* souboru podle potřeby pro specifikace příslušného zákazníka.

Získat nejnovější snímek skripty a dokumentaci k [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Podrobné kroky uvedené výše, najdete v tématu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

### <a name="consideration-for-mcod-scenarios"></a>Pečlivě zvážit u MCOD scénáře
Pokud používáte [MCOD scénář](https://launchpad.support.sap.com/#/notes/1681092) s více instancí SAP HANA na jednu jednotku velká Instance HANA, budete mít různé svazky, které jsou zřízené pro jednotlivé instance SAP HANA. Podrobnosti o MDC a další důležité informace, zkontrolujte [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) kapitoly **"Hlavně třeba si zapamatovat"**.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Instalace klienta SAP HANA HDB

Operační systém Linux nainstalován na systému SAP HANA v Azure (velké instance) obsahuje složky a skripty potřebné k provedení snímků úložiště SAP HANA pro účely obnovení zálohování a po havárii. Kontrola pro novější verze v [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Nejnovější verze skriptů je 4.0. Jiné skripty může mít jiné vedlejší verze v rámci stejné hlavní verze.

Je vaší odpovědností, abyste instalaci klienta SAP HANA HDB na jednotkách velká Instance HANA při instalaci SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Krok 2: Změnit/etc/ssh/ssh\_config

Tento krok je podrobně popsaný v pod kontrolou pro novější verze v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) v kapitole **"enable (Povolit komunikaci s úložištěm)**


### <a name="step-3-create-a-public-key"></a>Krok 3: Vytvoření veřejného klíče

Pokud chcete povolit přístup k rozhraní storage snímku tenanta velká Instance HANA, potřebujete vytvořit proceduru přihlášení prostřednictvím veřejného klíče. Na první SAP HANA v Azure (velké instance) serveru ve vašem tenantovi vytvořte veřejný klíč, který se má použít pro přístup k infrastruktuře úložiště. Veřejný klíč zajistí, že pro přihlášení k rozhraní úložiště snímku není vyžadováno heslo. Vytvoření veřejného klíče také znamená, že není potřeba udržovat heslo přihlašovacích údajů. Přesné kroky způsobu generování veřejného klíče je popsána v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) v kapitole **"enable (Povolit komunikaci s úložištěm)**


### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Vytvořit uživatelský účet SAP HANA

K zahájení vytváření snímků SAP HANA, musíte vytvořit uživatelský účet v SAP HANA, můžete použít skripty snímků úložiště. Vytvořte uživatelský účet SAP HANA v rámci SAP HANA Studio pro tento účel. Uživatel musí být vytvořená v rámci SYSTEMDB a není v databázi identifikátor SID pro MDC. V prostředí jednoho kontejneru se vytvoří uživatele databáze tenanta. Tento účet musí mít následující oprávnění: **Správce zálohování** a **katalogu čtení**. Přesné kroky k nastavení uživatele a pomocí uživatele, přečtěte si [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) v kapitole **"Povolit komunikaci se SAP HANA.**


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: Povolit uživatelský účet SAP HANA

V tomto kroku povolíte SAP HANA uživatelský účet, který jste vytvořili, tak, aby skripty nemusíte odeslání hesla za běhu. Příkaz SAP HANA `hdbuserstore` umožňuje vytvořit uživatelský klíč SAP HANA, který je uložený na jeden nebo více uzlů SAP HANA. Uživatelský klíč mu umožní přístup SAP HANA bez nutnosti spravovat hesla z v rámci skriptovací procesu. Skriptovací proces je popsán dále v tomto článku.

>[!IMPORTANT]
>Pomocí stejného uživatelského kontextu, které jsou spouštěny příkazy snímku v spuštěním těchto příkazů konfigurace. V opačném případě příkazy snímku nemůže správně fungovat.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: Získání skriptů snímku, konfigurace snímků a testování konfigurací a připojením

Stáhněte si nejnovější verzi skripty z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Způsob, jakým tyto skripty se chystáte nainstalovat majorly změnila verze 4.0 skripty. Podrobnosti o, najdete v článku [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) v kapitole **"Povolit komunikaci se SAP HANA.**

Přesné pořadí příkazů najdete v kapitole **"Snadnou instalaci nástroje snímku (výchozí)"** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Doporučujeme použití výchozí instalace. Pokud chcete provést upgrade z verze 3.x na 4.0, zkontrolujte v části **"Upgrade existující instalace"** z [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). Pro odinstalaci sady nástrojů 4.0, postupujte podle pokynů v **"Odinstalace nástroje snapshot"** v [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Nezapomeňte k provedení kroků popsaných v **"Dokončit nastavení snímku nástroje"** z [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Účelem jiné skripty a soubory jako máte nainstalovanou uvedená v seznamu a podrobně popisuje **"Co jsou tyto nástroje snímku?"** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Před konfigurací nástroje snímku se ujistěte, že je také HANA záložní umístění a nastavení správnou konfiguraci jak je popsáno v **"Konfigurace SAP HANA"** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Konfigurace sady nástrojů snímku je podrobně popsány v **konfigurační soubor – HANABackupCustomerDetails.txt** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

#### <a name="testing-connectivity-with-sap-hana"></a>Testování připojení k SAP HANA

Po uvedení všech konfiguračních dat do *HANABackupCustomerDetails.txt* soubor, zkontrolujte, zda jsou správná HANA instance data konfigurace. Pomocí skriptu `testHANAConnection`, který je nezávislý na SAP HANA vertikální nebo horizontální navýšení kapacity konfigurací.

Podrobnosti najdete v tématu **"Zkontrolujte připojení k SAP HANA – testHANAConnection"** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf)

#### <a name="testing-storage-connectivity"></a>Testování připojení úložiště

Dalším krokem testu je ke kontrole připojení k úložišti na základě dat vložte do *HANABackupCustomerDetails.txt* konfigurační soubor a následné provádění testu snímku. Před spuštěním `azure_hana_backup` příkaz, musíte spustit tento test. Sekvence příkazů pro tento test je uveden v **"Zkontrolujte připojení s úložištěm - testStorageSnapshotConnection"** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Po úspěšném přihlášení do rozhraní úložiště virtuálního počítače skript pokračuje fáze 2 a vytvoří snímek testu. Výstup je znázorněna zde pro tříuzlový horizontální navýšení kapacity konfigurací SAP HANA:

Pokud test snímek byl úspěšně proveden pomocí skriptu, můžete přejít pomocí plánování skutečného úložiště snímků. Pokud není úspěšné, prozkoumejte problémy před v budoucnu. Snímek testu by mělo zůstat kolem až do dokončení prvních skutečné snímků.


### <a name="step-7-perform-snapshots"></a>Krok 7: Provedení snímky

Po dokončení přípravné kroky, chcete začít konfigurovat a naplánovat skutečného úložiště snímků. Skript, který chcete naplánovat funguje vertikálně navýšit kapacitu a horizontální navýšení kapacity konfigurací SAP HANA. Pro pravidelné a pravidelné spouštění skriptu zálohování plánování skriptu pomocí nástroje cron. 

Syntaxi příkazů a funkce, najdete v článku **"Provést snímek zálohy - azure_hana_backup"** dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).  

Provádění skriptu `azure_hana_backup` vytvoří úložiště snímků tyto tři fáze:

1. Spustí snímek SAP HANA
1. Spustí snímků úložiště
1. Odstraní snímek SAP HANA, který byl vytvořen před spuštěním snímků úložiště

Spustit skript, jeho volání z složku HDB spustitelného souboru, do kterého byl zkopírován. 

Doba uchování je možné spravovat pomocí počet snímků, které jsou odeslány jako parametr při spuštění skriptu. Množství času, která je předmětem snímků úložiště je funkce doby spuštění a počet snímků odeslat jako parametr při spuštění skriptu. Pokud počet snímků, které jsou zachovány překročí číslo, které jsou pojmenovány jako parametr ve volání skriptu, odstraní se nejstarší úložiště snímku stejný jmenovce před provedením nový snímek. Číslo je zadat jako poslední parametr volání je číslo vám pomůže určit počet snímků, které jsou zachovány. S tímto číslem můžete taky řídit, nepřímo, místo na disku použité pro snímky. 


## <a name="snapshot-strategies"></a>Strategie snímku
Frekvence snímků pro různé typy, které závisí na, jestli používáte funkce zotavení po havárii velká Instance HANA. Tato funkce spoléhá na snímcích úložiště, které můžou vyžadovat zvláštní doporučení pro četnost a provádění období snímků úložiště. 

Důležité informace a doporučení, které následují, předpokladem je, že provedete *není* použít funkci obnovení po havárii, která nabízí velké instance HANA. Místo toho použijte snímků úložiště záloh a být schopen zajistit obnovení bodu v čase za posledních 30 dní. Zadané omezení počtu snímků a místa, zákazníci mají považovat za následující požadavky:

- Čas obnovení pro obnovení bodu v čase.
- Využité místo.
- Bod obnovení a cíle plánované doby obnovení potenciální zotavení po havárii.
- Konečný výsledek spuštění zálohování úplné databáze HANA proti disky. Pokaždé, když zálohu celé databáze proti disky nebo **backint** rozhraní se provádí, se nezdaří spuštění snímků úložiště. Pokud máte v plánu provést úplné zálohy nad úložiště snímků, ujistěte se, že během této doby je zakázána provádění snímků úložiště.
- Počet snímků na jeden svazek (omezená na 250).

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Pro zákazníky, kteří nepoužívají funkce zotavení po havárii velkých instancích HANA doba snímku je méně často. V takových případech zákazníkům provádět kombinované snímky v /hana/data a /hana/shared (zahrnuje /usr/sap) v obdobích 12 hodin nebo 24 hodin a udržují snímky po dobu jednoho měsíce. Totéž platí se snímky záložní svazek protokolu. Spuštění SAP HANA zálohy protokolu transakce proti záložní svazek s protokolem však dochází v období 5 až 15 minut.

Snímky naplánované úložiště se nejlépe provádí pomocí procesu cron. Použijte stejný skript pro všechny zálohy a potřeb obnovení po havárii a úpravě vstupů skript tak, aby odpovídaly různých požadované časy zálohování. Tyto snímky jsou všechny naplánované odlišně v procesu cron v závislosti na jejich doba provádění: po hodinách, 12 hodin, denní nebo týdenní. 

Následuje příklad plán cron v/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
V předchozím příkladu se po hodinách kombinované snímku, pokrývající svazky, které obsahují data/hana/a /hana/shared/SID (včetně/usr/sap) umístění. Pomocí tohoto typu snímku pro rychlejší obnovení bodu v čase během posledních dvou dnů. Na těchto svazcích je navíc denní snímek. Ano máte dva dny pokrytí podle hodinových snímky a čtyři týdny pokrytí podle denní snímky. Kromě toho svazek zálohování protokolu transakcí zálohovat denně. Tyto zálohy se uchovávají i čtyři týdny. Jak vidíte na třetím řádku crontab, zálohování protokolu transakcí HANA je naplánován ke spuštění každých 5 minut. Rozloženo jsou časy zahájení cron různé úlohy, které jsou spouštěny snímků úložiště, takže tyto snímky nejsou provedeny všechny najednou v určitém bodě v čase. 

V následujícím příkladu je provést kombinované snímků, které pokrývá svazků, které obsahují/hana/dat a /hana/shared/SID (včetně/usr/sap) umístění po hodinách. Tyto snímky se zachovat po dobu dvou dnů. Snímky svazků zálohování protokolu transakce jsou spouštěny v intervalech 5 minut a uchovávají po dobu 4 hodin. Jako dříve, zálohování protokolu transakcí HANA je naplánován ke spuštění každých 5 minut. Snímku svazku pro zálohování protokolu transakcí se provádí s prodlevou 2 minuty, po spuštění zálohy transakčního protokolu. V rámci těchto 2 minuty by měla dokončit zálohování protokolu transakcí SAP HANA za normálních okolností. Jako dříve, svazek, který obsahuje spouštěcí logické jednotky je zajištěná jednou denně snímku úložiště a uchovávají po dobu čtyř týdnů.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Pořadí předchozí příklad, s výjimkou spouštěcí logická jednotka je znázorněný na následujícím obrázku:

![Vztah mezi zálohy a snímky](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA provádí pravidelné zápisů ve službě /hana/log svazek k dokumentaci potvrzené změny do databáze. SAP HANA v pravidelných intervalech, zapíše uloženého bodu do svazku /hana/data. Jak je uvedeno v crontab je provést zálohování protokolu transakcí SAP HANA každých 5 minut. Uvidíte také, že SAP HANA snímku provádí každou hodinu v důsledku aktivací snímku kombinované úložiště přes /hana/data a /hana/shared/SID svazky. Po úspěšném HANA snímku, je proveden kombinované úložiště snímku. Podle pokynů v crontab, snímků úložiště na svazku /hana/logbackup provádí každých 5 minut, po zálohování protokolu transakcí HANA přibližně 2 minut.

> 

>[!IMPORTANT]
> Použití snímků úložiště pro zálohování SAP HANA je cenné pouze v případě, že snímky jsou prováděny současně se zálohami protokolů transakcí SAP HANA. Tyto zálohy transakčního protokolu muset zabývat časová období mezi snímky úložiště. 

Pokud jste nastavili závazku uživatelům obnovení bodu v čase za 30 dní, budete muset:

- V extrémních případech se přístup k kombinované úložiště snímků /hana/data a /hana/shared/SID, které se 30 dnů.
- Mají zálohy souvislých transakčního protokolu, které se týkají čas mezi jakékoli snímky, kombinované úložiště. Ano nejstarší snímku svazku pro zálohování protokolu transakce musí být 30 dnů. Toto není tento případ, když zkopírujete zálohy transakčního protokolu do jiné sdílené složky systému souborů NFS, který je umístěný ve službě Azure storage. V takovém případě si mohou vyžádat starší zálohy protokolu transakce z této sdílené složky systému souborů NFS.

Abyste využili výhod úložiště snímků a replikaci konečné úložiště zálohy transakčního protokolu, budete muset změnit umístění, ke kterému SAP HANA zapíše zálohy transakčního protokolu. Provedení této změny v HANA Studio. I když SAP HANA zálohuje celý protokol segmenty automaticky, měli byste určit interval zálohování protokolu a být deterministický. To platí zejména při použití možnosti zotavení po havárii, vzhledem k tomu obvykle chcete provést zálohování protokolu s dobou deterministický. V následujících případech se 15 minut nastavit jako interval zálohování protokolů.

![Naplánovat protokoly zálohování SAP HANA v SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Můžete použít taky zálohy, které se častěji než každých 15 minut. Častější nastavení se často používá ve spojení s funkcemi pro zotavení po havárii velkých instancích HANA. Někteří zákazníci provést zálohování protokolu transakcí každých 5 minut.  

Pokud databáze obsahuje byly dosud nezálohovali, posledním krokem je provedení zálohy databáze na základě souborů k vytvoření jedné položky zálohování, která musí existovat v rámci katalogu záloh. SAP HANA v opačném případě nemůžete zahájit zálohování zadaný protokol.

![Vytvořit zálohu souborů k vytvoření jedné položky zálohování](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Poté, co vaše první úspěšná úložiště snímků, že byly provedeny, budete muset odstranit snímek testu provedeného v kroku 6. Čtení **"Odebrat test snímky - removeTestStorageSnapshot"** v dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) podrobnosti. 


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorování počtu a velikosti snímků na diskovém svazku

Na konkrétní úložiště svazku můžete sledovat počet snímků a využití úložiště těchto snímků. `ls` Příkaz nezobrazí snímek adresáře nebo souborů. Nicméně příkaz operačních systémů Linux `du` zobrazuje podrobnosti o těchto snímků úložiště, protože se ukládají na stejném svazku. Tento příkaz lze použít s těmito možnostmi:

- `du –sh .snapshot`: Tato možnost poskytuje celkový počet všechny snímky v adresáři snímku.
- `du –sh --max-depth=1`: Tato možnost uvádí všechny snímky, které jsou uloženy v **.snapshot** složky a velikost jednotlivých snímků.
- `du –hc`: Tato možnost poskytuje celkovou velikost používané všechny snímky.

Používejte tyto příkazy, abyste měli jistotu, že snímky, které jsou přijatá a uložená nejsou využívání všech úložiště na svazcích.

>[!NOTE]
>Snímky spuštění logické jednotky nejsou viditelné pomocí předchozích příkazů.

### <a name="getting-details-of-snapshots"></a>Získávání podrobností snímků
Chcete-li získat více podrobností o snímky, můžete použít také skript `azure_hana_snapshot_details`. Tento skript můžete spustit v některém umístění, pokud je aktivní server v umístění pro obnovení po havárii. Skript obsahuje následující výstup rozdělit podle každý svazek, který obsahuje snímky: 
   * Velikost celkový počet snímků na svazku
   * Následující podrobnosti v jednotlivých snímků v tomto svazku: 
      - Název snímku 
      - Vytvořit čas 
      - Velikost snímku
      - Frekvence snímku
      - ID HANA zálohování přidružené k tomuto snímku, pokud je to potřeba

Syntaxi příkazu a výstupy kontrolu **"V seznamu snímků - azure_hana_snapshot_details"** v dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf). 



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Snížení počtu snímků na serveru

Jak jsme vysvětlili výše, můžete snížit počet některé popisky snímky, které ukládáte. Poslední dva parametry tohoto příkazu k zahájení snímku se popisek a počet snímků, které chcete zachovat.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

V předchozím příkladu je popisek snímku **dailyhana** a je počet snímků s tímto popiskem mají být uchovány **28**. Jak vám odpoví na využití místa na disku, můžete snížit počet uložené snímky. Je snadný způsob, jak snížit počet snímků na 15, například pro spuštění skriptu s posledním parametrem nastaveným na **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Pokud spouštíte skript s tímto nastavením, je počet snímků, včetně nové úložiště snímků, 15. 15 nejnovější snímky jsou uchovávány a 15 starší snímky se odstraní.

 >[!NOTE]
 > Tento skript snižuje počet snímků, pouze pokud existuje více než 1 hodina staré snímky. Skript nedojde k odstranění snímků, které jsou menší než 1 hodinu stará. Tato omezení se vztahují k funkce zotavení po havárii volitelné nabízené.

Pokud už nechcete zachovat sadu snímků zálohy předponou **dailyhana** v příklady syntaxe, můžete spustit skript s **0** jako číslo uchovávání informací. Potom se odeberou všechny snímky odpovídající tohoto popisku. Odebírá se přístup ke všem snímkům může ovlivnit možnosti velkých instancích HANA funkce zotavení po havárii.

Druhá možnost odstranit konkrétní snímků je skript `azure_hana_snapshot_delete`. Tento skript slouží k odstranění snímku nebo sadu snímky, buď pomocí ID zálohy HANA jak se nachází v HANA Studio, nebo prostřednictvím samotný název snímku. V současné době se zálohování ID pouze váže na snímky vytvořené pro **hana** typ snímku. Snímek zálohy typu **protokoly** a **spouštěcí** neprovádějte snímek SAP HANA, a proto neexistuje žádné ID zálohy pro tyto snímky. Pokud je zadán název snímku, hledá všechny snímky v různých svazcích, které odpovídají názvu zadané snímku. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Podrobnosti o skriptu najdete **snímek - azure_hana_snapshot_delete "odstranit"** v dokumentu [Microsoft snapshot nástroje pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf).

Spuštění skriptu jako uživatel **kořenové**.

>[!IMPORTANT]
>Pokud data, která existuje pouze na snímku odstraňujete, po odstranění snímku, že dojde ke ztrátě dat navždy.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>Obnovení na úrovni souboru ze snímku úložiště

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Pro typ snímku **hana** a **protokoly**, dostanete snímků přímo na svazky v **.snapshot** adresáře. Je podadresář pro jednotlivé snímky. Můžete zkopírovat každý soubor ve stavu, ve kterém se v okamžiku snímků z podadresář do tohoto adresáře struktury. V aktuální verzi souboru, který je **ne** obnovení skript ve formě pro obnovení snímku samoobslužné funkce (v případě, že obnovení snímku lze provést, protože část samoobslužných služeb zotavení po Havárii skripty v lokalitě zotavení po Havárii během převzetí služeb při selhání). Provozní tým Microsoft musíte požádat tak, že otevřete žádost o služby k obnovení požadovaného snímku z existující snímky k dispozici.

>[!NOTE]
>Obnovení jednotlivých souborů nefunguje pro snímky spuštění logické jednotky nezávislé na typ jednotek velká Instance HANA. **.Snapshot** adresář není vystaveno v spouštěcí logickou jednotku. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Obnovit na nejnovější snímek HANA

V případě produkčního dolů lze inicializovat procesem obnovení ze snímku úložiště jako incident zákazníků s podporu Microsoft Azure. Pokud data byla odstraněna v produkční systém, a k obnovení provozní databáze je jediný způsob, jak ho načíst je na vás vysokou naléhavostí.

Obnovení bodu v čase v jiné situaci, může být s nízkou naléhavost a plánované dny předem. Můžete naplánovat toto obnovení se SAP HANA v Azure místo zvyšování příznak s vysokou prioritou. Například může být plánujete upgrade softwaru SAP s použitím nového balíčku rozšíření. Pak budete muset obnovit snímek, který představuje stav před upgradem balíček rozšíření.

Před odesláním požadavku, je nutné připravit. SAP HANA v Azure týmu můžete žádost zpracovat a poskytují obnovené svazky. Následně obnovíte databázi HANA podle snímků.

Možnosti, jak získat snímek obnovit s novou sadu nástrojů, které jsou popsány v části **"Postup obnovení snímku"** dokumentu [ruční obnovení Průvodce pro SAP HANA, v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Následující ukazuje, jak připravit pro požadavek:

1. Rozhodněte, které snímek, který chcete obnovit. Pokud dáte pokyn, jinak se obnoví pouze hana/datový svazek. 

1. Vypnutí HANA instance.

   ![Vypnutí HANA instance](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odpojte objemy dat v každém uzlu databáze HANA. Pokud datové svazky se pořád připojený k operačnímu systému, obnovení snímku se nezdaří.
   ![Odpojte objemy dat v každém uzlu databáze HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otevřete žádost o podporu Azure a zahrňte pokyny k obnovení konkrétní snímek.

   - Během obnovení: SAP HANA v Azure může požádat o jeho účasti na konferenci pro zajištění koordinace, ověřování a potvrzení, že je obnovit snímek správné úložiště. 

   - Po obnovení: SAP HANA ve službě Azure vás upozorní, když obnovení snímku úložiště.

1. Po dokončení procesu obnovení znovu připojte všechny datové svazky.

   ![Znovu připojit všechny datové svazky](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Další možností, jak získat například SAP HANA datové soubory obnovit ze snímku úložiště je uvedeno v kroku 7 dokumentu [ruční obnovení Průvodce pro SAP HANA, v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf).

Dokument [ruční obnovení Průvodce pro SAP HANA, v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) znázorňuje posloupnost obnovení ze zálohy snímku. Pomocí této dokumentace k provádění obnovení. 

>[!Note]
>Krok 7 není nutné spustit, pokud máte snímek obnovit operacemi Microsoft.


### <a name="recover-to-another-point-in-time"></a>Obnovení do jiného bodu v čase
Dokument [ruční obnovení Průvodce pro SAP HANA, v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) znázorňuje posloupnost obnovení do určitého bodu v čase v části **"Obnovte databázi do následující bod v čase"**. Pomocí této dokumentace k provádění obnovení do určitého bodu v čase. 


## <a name="next-steps"></a>Další postup
- Zobrazit [zásady zotavení po havárii a příprava](hana-concept-preparation.md).
