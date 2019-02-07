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
ms.date: 09/28/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e71e4ea56bfe467e03be59d6a855272baafc4235
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55822727"
---
# <a name="backup-and-restore"></a>Zálohování a obnovení

>[!IMPORTANT]
>Tato dokumentace je nahrazen dokumentace správy SAP HANA nebo SAP poznámky. Očekává se, zda má čtečka důkladného porozumění a odborných znalostí v SAP HANA správu a provoz, zejména s tématy zálohování, obnovení, vysokou dostupnost a zotavení po havárii. V této dokumentaci se zobrazí snímky obrazovky ze SAP HANA Studio. Obsah, struktura a povaze obrazovky nástroje pro správu SAP a nástrojů, samotné se mohou změnit ze SAP HANA verzí.

Je důležité věnovat kroky a postupy provést ve vašem prostředí a s HANA verzí a verzí. Některé procesy popsané v této dokumentaci je zjednodušená pro lepší porozumění obecné a neměly by má být použit jako podrobný postup pro konečný výsledek operace příruček. Pokud chcete vytvořit operaci příruček pro konkrétní konfiguraci, musíte pro testování a výkon vašich procesů a zdokumentujte procesy související s konkrétní konfigurací. 

Jeden z nejdůležitějších aspektů do provozní databáze je chránit před katastrofickými událostmi. Příčinu tyto události můžou být čímkoli od přírodními katastrofami na jednoduché uživatelské chyby.

Zálohování databáze, možnost obnovit do libovolného bodu v čase (například než někdo odstraní důležitá data), umožňuje obnovení do stavu, ve kterém je co nejblíže způsobu, jakým byl před narušení.

Pro dosažení co nejlepších výsledků se musí provádět dva typy záloh:

- Zálohování databází: Plný, přírůstkové nebo rozdílové zálohy
- Zálohy transakčního protokolu

Kromě zálohování úplné databáze provádí na úrovni aplikace můžete provádět zálohování pomocí snímků úložiště. Snímky úložiště nenahrazují zálohy transakčního protokolu. Zálohy transakčního protokolu zůstanou důležité obnovení databáze do určitého bodu v čase nebo na prázdný protokoly z již potvrzené transakce. Snímků úložiště lze však rychlejší zotavení rychle poskytnutím vpřed snímek databáze. 

SAP HANA v Azure (velké instance) nabízí dvě možnosti zálohování a obnovení:

- Sestavili (DIY). Po výpočtu zajistit, že není dostatek místa na disku, proveďte kompletní databáze a zálohování protokolů pomocí jedné z následujících metod zálohování disku. Můžete zálohovat přímo na svazky připojené jednotky velká Instance HANA, nebo do sdílené složky NFS (Network File), které jsou nastavené v Azure virtuální počítač (VM). V takovém případě zákazníci nastavení virtuálního počítače s Linuxem v Azure, připojit k virtuálnímu počítači Azure Storage a sdílejí úložiště přes nakonfigurovaným serverem systému souborů NFS v tomto virtuálním počítači. Pokud provádíte zálohování u svazků, které přímo připojit k jednotky velká Instance HANA, musíte zkopírovat zálohy do účtu služby Azure storage (po nastavení virtuálního počítače Azure, který exportuje sdílených složek NFS, které jsou založené na Azure Storage). Můžete také použít trezoru záloh Azure nebo Azure studeného úložiště. 

   Další možností je použít nástroj na ochranu dat třetí strany a uložte zálohy po zkopírování do účtu služby Azure storage. Rozběhněte zcela možnost zálohování může být také nezbytné pro data, která potřebujete ukládat pro delší dobu pro účely auditování a dodržování předpisů. Ve všech případech se zálohy jsou zkopírovány do sdílené složky NFS představovány virtuálního počítače a služby Azure Storage.

- Infrastruktura zálohování a obnovení. Můžete také použít funkci zálohování a obnovení, který poskytuje základní infrastruktury SAP HANA v Azure (velké instance). Tato možnost splňuje potřeby zálohování a rychlé obnovení. Zbývající část adresy funkce zálohování a obnovení, které se nabízí s velkých instancích HANA. Tato část se věnuje také relace zálohování a obnovení po havárii má nabízí funkce obnovení ve velkých instancích HANA.

>   [!NOTE]
>   Snímek technologie, která používá základní infrastruktury velkých instancích HANA obsahuje závislost na snímky SAP HANA. V tomto okamžiku SAP HANA snímků nebudou fungovat ve spojení s více tenanty kontejnerů víceklientské databáze SAP HANA. Pokud jen pro nasazení jednoho klienta pracovní snímky SAP HANA a tuto metodu můžete použít.

## <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Pomocí snímků úložiště SAP Hana v Azure (velké instance)

Úložiště infrastruktury základních SAP HANA v Azure (velké instance) podporuje úložiště snímků svazků. Zálohování a obnovení svazků se podporuje, s následující aspekty:

- Místo úplné zálohy databáze pocházejí snímků svazků úložiště často.
- Při aktivaci snímku /hana/data a /hana/shared (zahrnuje /usr/sap) svazky, technologie snímku zahájí SAP HANA snímku, než se provede snímků úložiště. Tento snímek SAP HANA je bod instalace pro konečné protokolu obnovení po obnovení snímku úložiště. Pro HANA snímek úspěšný budete potřebovat aktivní instance HANA.  Ve scénáři HSR snímek úložiště nepodporuje aktuální sekundárního uzlu, kde HANA snímku nelze provést.
- Po snímku úložiště byl úspěšně proveden, je odstranit snímek SAP HANA.
- Zálohy transakčního protokolu často pocházejí a jsou uloženy ve svazku /hana/logbackups nebo v Azure. Můžete aktivovat /hana/logbackups svazek, který obsahuje zálohy transakčního protokolu na pořízení snímku samostatně. V takovém případě není potřeba provést HANA snímku.
- Pokud musíte obnovit databázi do určitého bodu v čase, žádosti o tuto podporu Microsoft Azure (pro produkční výpadek) nebo SAP HANA na Azure Service Management obnovení do určité úložiště snímku. Příkladem je plánované obnovení systému izolovaného prostoru do původního stavu.
- Snímek SAP HANA, který je součástí úložiště snímku je posunu bod pro aplikování záloh protokolů transakcí, které byly provedeny a uložena po pořízení snímku úložiště.
- Tyto zálohy protokolu transakcí se přesunete na obnovení databáze zpět do určitého bodu v čase.

Můžete provádět úložiště snímků, které cílí na tři třídy svazků:

- Kombinované snímku/hana/daty a /hana/shared (včetně/usr/sap). Tento snímek vyžaduje vytvoření snímku SAP HANA jako příprava pro snímek úložiště. Snímek SAP HANA zajišťuje, že databáze je v konzistentním stavu z hlediska úložiště. Na provoz a že pro obnovení zpracování, který je bod, který má nastavit.
- Samostatný snímek přes/hana/logbackups.
- Oddíl s operačním systémem.

Získat nejnovější snímek skripty a dokumentaci k [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts). Při stahování balíčku skriptu snímků z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts), získáte v dokumentaci PDF pro skripty jako součást balíčku skriptu. Každý balíček skriptu má svou vlastní dokumentaci PDF.

## <a name="storage-snapshot-considerations"></a>Aspekty volby úložiště snímku

>[!NOTE]
>Úložiště snímků využívání prostoru úložiště, který byl přidělen do jednotky velká Instance HANA. Je potřeba zvážit následující aspekty plánování úložiště snímků a kolik snímků úložiště udržovat. 

Konkrétní mechanismus úložiště snímků pro SAP HANA v Azure (velké instance), které patří:

- Konkrétní úložiště snímku (v bodě v čase, kdy je provedena) využívá malé úložiště.
- Jako data změny obsahu a obsahu v SAP HANA data, která změnit soubory na svazku úložiště snímku potřebuje pro uložení původního obsahu bloku, jakož i změny.
- Snímek úložiště v důsledku toho zvětší velikost. Čím déle existuje snímku, stává čím větší úložiště snímku.
- Další změny provedené svazku databáze SAP HANA během životního cyklu úložiště snímků, tím větší využití místa na snímku úložiště.

SAP HANA v Azure (velké instance) se dodává s velikostí pevný svazek pro SAP HANA svazky dat a protokolů. Provádí se snímky těchto svazků eats do místo ve svazku. Budete muset určit, kdy chcete naplánovat snímků úložiště. Budete také muset monitorovat využití prostoru úložiště svazky, stejně jako správu počet snímků, které ukládáte. Při importu hmotnosti dat nebo provádět jiné významné změny v databázi HANA, můžete zakázat snímků úložiště. 


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
1. Skripty a konfigurační soubor z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts) umístění **hdbsql** instalace SAP HANA.
1. Upravit *HANABackupDetails.txt* souboru podle potřeby pro specifikace příslušného zákazníka.

Získat nejnovější snímek skripty a dokumentaci k [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts). Při stahování balíčku skriptu snímků z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts), získáte v dokumentaci PDF pro skripty jako součást balíčku skriptu. Každý balíček skriptu má svou vlastní dokumentaci PDF.

### <a name="consideration-for-mcod-scenarios"></a>Pečlivě zvážit u MCOD scénáře
Pokud používáte [MCOD scénář](https://launchpad.support.sap.com/#/notes/1681092) s více instancí SAP HANA na jednu jednotku velká Instance HANA, budete mít různé svazky, které jsou zřízené pro jednotlivé instance SAP HANA. V aktuální verzi snímku samoobslužné služby automation nemůžete zahájit samostatnou snímků na všechny systémy instance HANA ID (SID). Funkce přináší kontroly pro registrované instance SAP HANA serveru v konfiguračním souboru (viz dále v tomto článku) a spouští souběžné snímků svazků všechny instance zaregistrované na jednotce.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Instalace klienta SAP HANA HDB

Operační systém Linux nainstalován na systému SAP HANA v Azure (velké instance) obsahuje složky a skripty potřebné k provedení snímků úložiště SAP HANA pro účely obnovení zálohování a po havárii. Kontrola pro novější verze v [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts). Nejnovější verze skriptů je 3.x. Jiné skripty může mít jiné vedlejší verze v rámci stejné hlavní verze.

>[!IMPORTANT]
>Při přesunu z verze 2.1 verzi 3.x skriptů, Všimněte si, že došlo ke změně struktury konfiguračního souboru a některé syntaxe. Zobrazit popisky na určité části. 

Je vaší odpovědností, abyste instalaci klienta SAP HANA HDB na jednotkách velká Instance HANA při instalaci SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Krok 2: Změnit/etc/ssh/ssh\_config

Změna `/etc/ssh/ssh_config` tak, že přidáte _Mac hmac – sha1_ řádku, jak je znázorněno zde:
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>Krok 3: Vytvoření veřejného klíče

Pokud chcete povolit přístup k rozhraní storage snímku tenanta velká Instance HANA, potřebujete vytvořit proceduru přihlášení prostřednictvím veřejného klíče. Na první SAP HANA v Azure (velké instance) serveru ve vašem tenantovi vytvořte veřejný klíč, který se má použít pro přístup k infrastruktuře úložiště. Veřejný klíč zajistí, že pro přihlášení k rozhraní úložiště snímku není vyžadováno heslo. Vytvoření veřejného klíče také znamená, že není potřeba udržovat heslo přihlašovacích údajů. V Linuxu na serveru pro velké instance SAP HANA spusťte následující příkaz pro vytvoření veřejného klíče:
```
  ssh-keygen –t dsa –b 1024
```
Nové umístění **_/root/.ssh/id\_dsa.pub**. Nezadávejte skutečné heslo, jinak budete muset zadat heslo při každém přihlášení. Místo toho vyberte **Enter** dvakrát pro odebrání "heslo" požadavek na přihlášení.

Ujistěte se, že veřejný klíč byl opraven podle očekávání tím, že změníte složky, které mají **/root/.ssh/** a spouštěním `ls` příkazu. Pokud je k dispozici klíč, můžete ji zkopírovat spuštěním následujícího příkazu:

![Veřejný klíč se zkopíruje spuštěním tohoto příkazu](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

V tomto okamžiku obraťte se na SAP HANA v Azure Service Management a poskytnout veřejný klíč. Zástupce služby používá k registraci v základní infrastruktuře úložiště, který je pro vašeho tenanta velká Instance HANA ubírat veřejný klíč.

### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Vytvořit uživatelský účet SAP HANA

K zahájení vytváření snímků SAP HANA, musíte vytvořit uživatelský účet v SAP HANA, můžete použít skripty snímků úložiště. Vytvořte uživatelský účet SAP HANA v rámci SAP HANA Studio pro tento účel. Uživatel musí být vytvořená v rámci SYSTEMDB a není v databázi identifikátor SID pro MDC. V prostředí jednoho kontejneru je uživatel v rámci databáze tenantů. Tento účet musí mít následující oprávnění: **Správce zálohování** a **katalogu čtení**. V tomto příkladu je uživatelské jméno **SCADMIN**. Název uživatelského účtu v HANA Studio vytvořili rozlišuje velká a malá písmena. Je nutné vybrat **ne** pro by uživatel musel změnit heslo při jeho příštím přihlášení.

![Vytvoření uživatele v HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Pokud používáte MCOD nasazení s více instancí SAP HANA na jednu jednotku, musíte tento krok opakujte pro každou instanci SAP HANA.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: Povolit uživatelský účet SAP HANA

V tomto kroku povolíte SAP HANA uživatelský účet, který jste vytvořili, tak, aby skripty nemusíte odeslání hesla za běhu. Příkaz SAP HANA `hdbuserstore` umožňuje vytvořit uživatelský klíč SAP HANA, který je uložený na jeden nebo více uzlů SAP HANA. Uživatelský klíč mu umožní přístup SAP HANA bez nutnosti spravovat hesla z v rámci skriptovací procesu. Skriptovací proces je popsán dále v tomto článku.

>[!IMPORTANT]
>Spusťte následující příkaz v nabídce uživatelského skripty jsou plánované má být proveden. V opačném případě skriptu nemůže pracovat správně.

Zadejte `hdbuserstore` takto:

**Pro nastavení MDC HANA**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**Pro nastavení MDC HANA**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

V následujícím příkladu je uživatel **SCADMIN01**, název hostitele je **lhanad01**, a číslo instance je **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Pokud používáte k HANA MCOD nasazení s více instancí SAP HANA na jednu jednotku, musíte krok opakujte pro každou instanci SAP HANA a příslušné zálohovací uživatele na jednotce.

Pokud máte konfigurace škálování na více instancí SAP HANA, budete muset spravovat všechny skriptování z jednoho serveru. V tomto příkladu, SAP HANA klíč **SCADMIN01** musí být změněn pro každého hostitele způsobem, který ukazuje hostitele, který souvisí s klíči. Změnit účet zálohování SAP HANA s počtem instancí databáze HANA. Klíč musí mít oprávnění správce na hostiteli, ke kterému je přiřazen, a zálohování uživatele pro horizontální navýšení kapacity konfigurací musí mít přístupová práva pro všechny instance SAP HANA. Tři uzly horizontální navýšení kapacity za předpokladu, že mají názvy **lhanad01**, **lhanad02**, a **lhanad03**, sekvence příkazů vypadá takto:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: Získání skriptů snímku, konfigurace snímků a testování konfigurací a připojením

Stáhněte si nejnovější verzi skripty z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts). Zkopírujte stažený skripty a textový soubor do pracovní adresář pro **hdbsql**. Pro aktuální instalace HANA, je tento adresář v následujícím formátu: /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_test_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

Při práci se skripty jazyka Perl: 

- Pokud jste nedostali pokyny od Microsoft Operations nikdy nemění skripty.
- Když se zobrazí výzva k úpravě skriptu nebo soubor parametrů, vždy používejte Linux textového editoru, jako je například "vi" a ne Windows editoru, jako je Poznámkový blok. Pomocí editoru Windows může dojít k poškození souboru formátu.
- Vždy používejte nejnovější skripty. Nejnovější verzi si můžete stáhnout z Githubu.
- Použijte stejnou verzi skriptů napříč krajiny.
- Testovací skripty a blíž se seznámit s požadované parametry a výstup skriptu před přímo jejich používání v produkčním prostředí systému.
- Neměnit název přípojného bodu serveru zajišťovaného pomocí Microsoft Operations. Spolehněte se na tyto standardní přípojné body k dispozici pro úspěšné spuštění těchto skriptů.


Účelem jiné skripty a soubory je následujícím způsobem:

- **azure\_hana\_backup.pl**: Tento skript je naplánován pomocí nástroje pro plánování procesu Cron systému Linux ke spuštění úložiště snímků pro dat HANA a sdílených svazků, svazky /hana/logbackups nebo operačního systému.
- **azure\_hana\_replication\_status.pl**: Tento skript nabízí základní podrobnosti o stavu replikace ze produkční lokality do lokality pro obnovení po havárii. Skript monitoruje zajistíte, že probíhá replikace a zobrazuje velikost položek, které se replikují. Také obsahuje pokyny, pokud replikace trvá moc dlouho, nebo pokud odkaz je mimo provoz.
- **azure\_hana\_snapshot\_details.pl**: Tento skript obsahuje seznam základními podrobnostmi o všechny snímky, jeden svazek, které existují ve vašem prostředí. Tento skript můžete spustit na primárním serveru nebo na jednotce serveru v umístění pro obnovení po havárii. Skript obsahuje následující informace rozepsané podle jednotlivých svazek, který obsahuje snímky:
   * Velikost celkový počet snímků na svazku
   * Následující podrobnosti v jednotlivých snímků v tomto svazku: 
      - Název snímku 
      - Vytvořit čas 
      - Velikost snímku
      - Frekvence snímku
      - ID HANA zálohování přidružené k tomuto snímku, pokud je to potřeba
- **azure\_hana\_snapshot\_delete.pl**: Tento skript odstraní snímek úložiště nebo sadu snímky. Můžete použít buď ID zálohování SAP HANA jak se nachází v HANA Studio, nebo název snímku úložiště. ID zálohy je v současné době pouze vázán na snímky vytvořené pro HANA data/log/sdílené svazky. Jinak Pokud je zadaný Identifikátor snímku, usiluje všechny snímky, které odpovídají zadané snímku ID.  
- **testHANAConnection.pl**: Tento skript Otestuje připojení k instanci SAP HANA a je nutný k nastavení úložiště snímků.
- **testStorageSnapshotConnection.pl**: Tento skript má dva účely. Za prvé zajišťuje, že velká Instance HANA jednotky, které spouští skripty má přístup k přiřazené úložiště virtuálního počítače a na rozhraní úložiště snímku velkých instancích HANA. Druhým účelem je vytvořit dočasný snímků pro instance HANA, kterou testujete. Tento skript by měl spustit pro každou instanci HANA na serveru k zajištění, že skripty zálohování fungovat podle očekávání.
- **removeTestStorageSnapshot.pl**: Tento skript odstraní snímek testu vytvořený pomocí skriptu **testStorageSnapshotConnection.pl**.
- **azure\_hana\_dr\_failover.pl**: Tento skript spustí zotavení po Havárii převzetí služeb při selhání do jiné oblasti. Je nutné provést na jednotce velká Instance HANA v oblasti zotavení po Havárii, nebo na jednotku, kterou chcete pro převzetí služeb při selhání. Tento skript zastaví se replikace úložiště z primární strany na stranu sekundární, obnoví nejnovější snímek na svazcích zotavení po Havárii a poskytuje přípojné body pro DR svazky.
- **Azure\_hana\_testování\_zotavení po havárii\_failover.pl**: Tento skript provádí testovací převzetí služeb při selhání v lokalitě zotavení po Havárii. Na rozdíl od azure_hana_dr_failover.pl skriptu toto spuštění nepřeruší úložiště replikace z primární na sekundární. Místo toho klony svazky replikované úložiště jsou vytvořené na straně zotavení po Havárii a přípojné body naklonované svazků jsou k dispozici. 
- **HANABackupCustomerDetails.txt**: Tento soubor je upravitelná konfigurační soubor, který budete muset upravit pro přizpůsobení konfigurace SAP HANA. *HANABackupCustomerDetails.txt* soubor je ovládací prvek a konfigurační soubor pro skript, který spouští snímků úložiště. Upravte soubor pro účely a instalační program. Zobrazí **název úložiště zálohování** a **úložiště IP adresu** ze SAP HANA v Azure Service Management při nasazení vaší instance. Nelze změnit pořadí, řazení nebo mezery u všech proměnných v tomto souboru. Pokud tak učiníte, skriptů nebudou pracovat správně. Kromě toho se zobrazí IP adresa uzlu vertikálně navýšit kapacitu nebo hlavní uzel (Pokud horizontální navýšení kapacity) ze SAP HANA v Azure Service Management. Znáte i číslo instance HANA, abyste získali během instalace SAP HANA. Teď budete muset přidat název zálohy do konfiguračního souboru.

Vertikální nebo horizontální navýšení kapacity nasazení konfigurační soubor by vypadalo podobně jako v následujícím příkladu po zadání názvu serveru jednotky velká Instance HANA a IP adresu serveru. Vyplňte všechna potřebná pole pro každý SAP HANA identifikátor zabezpečení SID chcete zálohovat nebo obnovit.

Také můžete Zakomentovat řádků z instancí, které nechcete, aby zálohování pro určitou dobu tak, že přidáte "#", před povinné pole. Také není nutné zadat všechny instance SAP HANA, které jsou obsaženy na serveru, pokud není nutné zálohovat nebo obnovit tuto konkrétní instanci. Formát musí uchovají všechna pole, nebo všechny skripty vyvolat chybovou zprávu a ukončí skriptu. Další požadované řádky podrobnosti SID informace, které nepoužíváte po poslední používaných instancí SAP HANA, můžete odstranit. Všechny řádky musí být buď vyplněna, zakomentované nebo odstranit.

>[!IMPORTANT]
>Struktura souboru se změnila s přechodem z verze 2.1 verzi 3.x. Pokud chcete použít verzi skriptů 3.x, budete muset přizpůsobit struktura konfiguračních souborů. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

Pro každou instanci, která nakonfigurujete na jednotce velká Instance HANA, nebo pro škálovatelnou konfiguraci je třeba definovat data následujícím způsobem:

    
```
######***SID #1 Information***#####
SID1: h01
###Provided by Microsoft Operations###
SID1 Storage Backup Name: clt1h01backup
SID1 Storage IP Address: 172.18.18.11
######     Customer Provided    ######
SID1 HANA instance number: 00
SID1 HANA HDBuserstore Name: SCADMINH01
```
Pro horizontální navýšení kapacity a konfigurace HANA System Replication opakujte tuto konfiguraci v každém uzlu. Toto opatření zajišťuje, že v případě selhání zálohování a konečný výsledek úložiště replikace pokračovat v práci.   

Po uvedení všech konfiguračních dat do *HANABackupCustomerDetails.txt* soubor, zkontrolujte, zda jsou správná HANA instance data konfigurace. Pomocí skriptu `testHANAConnection.pl`, který je nezávislý na SAP HANA vertikální nebo horizontální navýšení kapacity konfigurací.

```
testHANAConnection.pl
```

Pokud máte konfigurace škálování na více instancí SAP HANA, ujistěte se, že hlavní instanci HANA má přístup k požadované servery HANA a instancí. Neexistují žádné parametry do skriptu testu, ale je nutné přidat data do *HANABackupCustomerDetails.txt* konfiguračního souboru pro skript, který chcete spustit správně. Pouze kódy chyb shell příkaz jsou vráceny, takže ho není možné pro každou instanci zkontrolujte chybu skriptu. I tak skript poskytuje některé užitečné komentáře můžete zkontrolovat.

Pro spuštění skriptu, zadejte následující příkaz:
```
 ./testHANAConnection.pl
```
Pokud se skript úspěšně získá stav instance HANA, zobrazí zprávu, že připojení HANA bylo úspěšně navázáno.


Dalším krokem testu je ke kontrole připojení k úložišti na základě dat vložte do *HANABackupCustomerDetails.txt* konfigurační soubor a následné provádění testu snímku. Před spuštěním `azure_hana_backup.pl` skriptu, je třeba spustit tento test. Pokud svazek obsahuje žádné snímky, není možné určit, jestli svazek je prázdný, nebo pokud dojde k selhání SSH k získání podrobných informací o snímek. Z tohoto důvodu se skript spustí dva kroky:

- Ověřuje, že je přístupný pro skripty pro spuštění snímků úložiště virtuálního počítače klienta a rozhraní.
- Test nebo dummy, snímek pro každý svazek vytvoří instance HANA.

Z tohoto důvodu je zahrnutý jako argument instanci HANA. Pokud se nezdaří spuštění není možné poskytnout kontrolu chyb pro připojení k úložišti. I v případě, že se nezobrazí žádná chyba kontrolu, skript poskytuje užitečné rady.

1. Spusťte sekvenci příkazů k provedení tohoto testu:

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   Uživatelské jméno úložiště a úložiště IP adresa byla poskytnutá těmto osobám můžete na předání jednotky velká Instance HANA.

1. Spuštění skriptu testu:
   ```
    ./testStorageSnapshotConnection.pl
   ```

Skript se pokusí přihlásit k úložišti pomocí veřejného klíče, které jsou k dispozici v předchozích krocích nastavení a dat konfigurovaných v *HANABackupCustomerDetails.txt* souboru. Pokud přihlášení je úspěšný, zobrazí se následující obsah:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Pokud dochází k potížím při připojování ke konzole úložiště, výstup vypadá takto:

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

Po úspěšném přihlášení do rozhraní úložiště virtuálního počítače skript pokračuje fáze 2 a vytvoří snímek testu. Výstup je znázorněna zde pro tříuzlový horizontální navýšení kapacity konfigurací SAP HANA:

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

Pokud test snímek byl úspěšně proveden pomocí skriptu, budete pokračovat v konfiguraci skutečného úložiště snímků. Pokud není úspěšné, prozkoumejte problémy před v budoucnu. Snímek testu by mělo zůstat kolem až do dokončení prvních skutečné snímků.


### <a name="step-7-perform-snapshots"></a>Krok 7: Provedení snímky

Po dokončení postupu přípravy, můžete začít konfigurovat konfiguraci skutečného úložiště snímků. Skript, který chcete naplánovat funguje vertikálně navýšit kapacitu a horizontální navýšení kapacity konfigurací SAP HANA. Pro pravidelné a pravidelné spouštění skriptu zálohování plánování skriptu pomocí nástroje cron. 

Můžete vytvořit tři typy zálohy snímků:
- **HANA**: Zálohy kombinované snímku, ve kterém svazků, které obsahují/hana/dat a/hana/sdílené (který obsahuje také /usr/sap) se vztahují koordinované snímku. Obnovení jednoho souboru je možné z tohoto snímku.
- **Protokoly**: Zálohy snímku svazku logbackups/hana /. Žádný snímek HANA se aktivuje, chcete-li spustit tento snímek úložiště. Tento svazek úložiště je určená tak, aby obsahovala zálohy protokolu transakce SAP HANA. Toto omezení protokolu růst a zabránit ztrátě dat probíhají častěji. Obnovení jednoho souboru je možné z tohoto snímku. Není nižší četnost méně než 3 minut.
- **Spouštěcí**: Snímek svazku, který obsahuje velké instance HANA spouštěcí číslo logické jednotky (LUN). Tato záloha snímku je možné jenom s typu I skladové položky z velkých instancích HANA. Obnovení nelze provést jediný soubor ze snímku svazku, který obsahuje spouštěcí logickou jednotku.


>[!NOTE]
> Syntaxe volání pro tyto tři typy snímků změnila s přechodem na verzi 3.x skripty, které podporují MCOD nasazení. Není nutné zadat identifikátor SID HANA instance zobrazovat. Budete potřebovat, abyste měli jistotu, že instance systému SAP HANA jednotky jsou nakonfigurované v konfiguračním souboru *HANABackupCustomerDetails.txt*.

>[!NOTE]
> Při prvním spuštění skriptu, může zobrazovat v prostředí s několika SID neočekávaným chybám. Opětovné spuštění skriptu se řeší problém.



Novou syntaxi volání pro provedení snímků úložiště pomocí skriptu *azure_hana_backup.pl* vypadá přibližně takto:

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

Podrobnosti o parametrech jsou následující: 

- První parametr určuje typ zálohy snímku. Povolené hodnoty jsou **hana**, **protokoly**, a **spouštěcí**. 
- Parametr **<HANA Large Instance Type>** je potřebné pro spouštěcí svazek pouze zálohy. Existují dvě platné hodnoty "TypeI" nebo "TypeII" závislé na jednotce velké Instance HANA. Pokud chcete zjistit, jaký typ jednotky je naleznete v tématu [architektura v Azure a SAP HANA (velké instance) přehled](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Parametr **< snapshot_prefix >** je snímek nebo zálohování popisek pro typ snímku. Má dva účely: jeden je pro vás s názvem, abyste věděli, co tyto snímky se týkají. Druhým účelem je pro skript *azure\_hana\_backup.pl* k určení počtu snímků úložiště, které jsou zachovány v rámci tohoto konkrétního popisku. Pokud naplánujete dvě zálohy snímků úložiště stejného typu (například **hana**), dva různé popisky a určit, že by měly být neustále 30 snímků pro každé, skončíte se 60 úložiště snímků svazků vliv. Pouze alfanumerické ("A-Z, a-z, 0-9"), podtržítka ("_") a pomlčka ("-") jsou povolené znaky. 
- Parametr **< snapshot_frequency >** je vyhrazen pro budoucí vývoj a nemá žádný vliv. Nastavte na "3min" při provádění zálohy typu **protokolu**a "15 min" při provádění jiné typy zálohování.
- Parametr **<number of snapshots retained>** definuje uchování snímků nepřímo tak, že definujete počet snímků se stejnou předponou snímku (popisek). Tento parametr je důležité pro naplánované spuštění prostřednictvím procesu cron. Pokud počtu snímků pomocí stejné snapshot_prefix překračuje počet Dal tento parametr, odstraní se nejstarší snímek před spuštěním nový snímek úložiště.

V případě Škálováním skript provede další kontroly k zajištění, zda máte přístup všechny servery pro HANA. Skript také zkontroluje, že všechny instance HANA vrátí příslušný stav instance předtím, než vytvoří snímek SAP HANA. Snímek SAP HANA je následován snímek úložiště.

Provádění skriptu `azure_hana_backup.pl` vytvoří úložiště snímků tyto tři fáze:

1. Spustí snímek SAP HANA
1. Spustí snímků úložiště
1. Odstraní snímek SAP HANA, který byl vytvořen před spuštěním snímků úložiště

Spustit skript, jeho volání z složku HDB spustitelného souboru, do kterého byl zkopírován. 

Doba uchování je možné spravovat pomocí počet snímků, které jsou odeslány jako parametr při spuštění skriptu. Množství času, která je předmětem snímků úložiště je funkce doby spuštění a počet snímků odeslat jako parametr při spuštění skriptu. Pokud počet snímků, které jsou zachovány překročí číslo, které jsou pojmenovány jako parametr ve volání skriptu, odstraní se nejstarší úložiště snímku stejný jmenovce před provedením nový snímek. Číslo je zadat jako poslední parametr volání je číslo vám pomůže určit počet snímků, které jsou zachovány. S tímto číslem můžete taky řídit, nepřímo, místo na disku použité pro snímky. 

> [!NOTE]
>Jakmile změníte popisek, počítání znovu spustí. Je třeba mít přísné popisování, tak vaše snímků není možné omylem odstranit.

## <a name="snapshot-strategies"></a>Strategie snímku
Frekvence snímků pro různé typy, které závisí na, jestli používáte funkce zotavení po havárii velká Instance HANA. Tato funkce spoléhá na snímcích úložiště, které můžou vyžadovat zvláštní doporučení pro četnost a provádění období snímků úložiště. 

Důležité informace a doporučení, které následují, předpokladem je, že provedete *není* použít funkci obnovení po havárii, která nabízí velké instance HANA. Místo toho použijte snímků úložiště záloh a být schopen zajistit obnovení bodu v čase za posledních 30 dní. Zadané omezení počtu snímků a místa, zákazníci mají považovat za následující požadavky:

- Čas obnovení pro obnovení bodu v čase.
- Využité místo.
- Bod obnovení a cíle plánované doby obnovení potenciální zotavení po havárii.
- Konečný výsledek spuštění zálohování úplné databáze HANA proti disky. Pokaždé, když zálohu celé databáze proti disky nebo **backint** rozhraní se provádí, se nezdaří spuštění snímků úložiště. Pokud máte v plánu provést úplné zálohy nad úložiště snímků, ujistěte se, že během této doby je zakázána provádění snímků úložiště.
- Počet snímků na jeden svazek (omezená na 250).


Pro zákazníky, kteří nepoužívají funkce zotavení po havárii velkých instancích HANA doba snímku je méně často. V takových případech zákazníkům provádět kombinované snímky v /hana/data a /hana/shared (zahrnuje /usr/sap) v obdobích 12 hodin nebo 24 hodin a udržují snímky po dobu jednoho měsíce. Totéž platí se snímky záložní svazek protokolu. Spuštění SAP HANA zálohy protokolu transakce proti záložní svazek s protokolem se však dochází v 5minutových období 15 minut.

Snímky naplánované úložiště se nejlépe provádí pomocí procesu cron. Použijte stejný skript pro všechny zálohy a potřeb obnovení po havárii a úpravě vstupů skript tak, aby odpovídaly různých požadované časy zálohování. Tyto snímky jsou všechny naplánované odlišně v procesu cron v závislosti na jejich doba provádění: po hodinách, 12 hodin, denní nebo týdenní. 

Následuje příklad plán cron v/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup.pl logs regularlogback 3min 28
22 12 * * *  ./azure_hana_backup.pl logs dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
V předchozím příkladu se po hodinách kombinované snímku, pokrývající svazky, které obsahují data/hana/a /hana/shared (včetně/usr/sap) umístění. Pomocí tohoto typu snímku pro rychlejší obnovení bodu v čase během posledních dvou dnů. Na těchto svazcích je navíc denní snímek. Ano máte dva dny pokrytí podle hodinových snímky a čtyři týdny pokrytí podle denní snímky. Kromě toho svazek zálohování protokolu transakcí zálohovat denně. Tyto zálohy se uchovávají i čtyři týdny. Jak vidíte na třetím řádku crontab, zálohování protokolu transakcí HANA je naplánován ke spuštění každých 5 minut. Rozloženo jsou časy zahájení cron různé úlohy, které jsou spouštěny snímků úložiště, takže tyto snímky nejsou provedeny všechny najednou v určitém bodě v čase. 

V následujícím příkladu je provést kombinované snímků, které pokrývá svazků, které obsahují/hana/dat a/hana/sdílené (včetně/usr/sap) umístění po hodinách. Tyto snímky se zachovat po dobu dvou dnů. Snímky svazků zálohování protokolu transakce jsou spouštěny v intervalech 5 minut a uchovávají po dobu 4 hodin. Jako dříve, zálohování protokolu transakcí HANA je naplánován ke spuštění každých 5 minut. Snímku svazku pro zálohování protokolu transakcí se provádí s prodlevou 2 minuty, po spuštění zálohy transakčního protokolu. V rámci těchto 2 minuty by měla dokončit zálohování protokolu transakcí SAP HANA za normálních okolností. Jako dříve, svazek, který obsahuje spouštěcí logické jednotky je zajištěná jednou denně snímku úložiště a uchovávají po dobu čtyř týdnů.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup.pl logs regularlogback 3min 28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl logs logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

Pořadí předchozí příklad, s výjimkou spouštěcí logická jednotka je znázorněný na následujícím obrázku:

![Vztah mezi zálohy a snímky](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA provádí pravidelné zápisů ve službě /hana/log svazek k dokumentaci potvrzené změny do databáze. SAP HANA v pravidelných intervalech, zapíše uloženého bodu do svazku /hana/data. Jak je uvedeno v crontab je provést zálohování protokolu transakcí SAP HANA každých 5 minut. Uvidíte také, že SAP HANA snímku provádí každou hodinu v důsledku aktivací snímku kombinované úložiště přes /hana/data a /hana/shared svazky. Po úspěšném HANA snímku, je proveden kombinované úložiště snímku. Podle pokynů v crontab, snímků úložiště na svazku /hana/logbackup provádí každých 5 minut, po zálohování protokolu transakcí HANA přibližně 2 minut.

> 

>[!IMPORTANT]
> Použití snímků úložiště pro zálohování SAP HANA je cenné pouze v případě, že snímky jsou prováděny současně se zálohami protokolů transakcí SAP HANA. Tyto zálohy transakčního protokolu muset zabývat časová období mezi snímky úložiště. 

Pokud jste nastavili závazku uživatelům obnovení bodu v čase za 30 dní, budete muset:

- V extrémních případech se přístup k kombinované úložiště snímků /hana/data a /hana/shared, které se 30 dnů.
- Mají zálohy souvislých transakčního protokolu, které se týkají čas mezi jakékoli snímky, kombinované úložiště. Ano nejstarší snímku svazku pro zálohování protokolu transakce musí být 30 dnů. Toto není tento případ, když zkopírujete zálohy transakčního protokolu do jiné sdílené složky systému souborů NFS, který je umístěný ve službě Azure storage. V takovém případě si mohou vyžádat starší zálohy protokolu transakce z této sdílené složky systému souborů NFS.

Abyste využili výhod úložiště snímků a replikaci konečné úložiště zálohy transakčního protokolu, budete muset změnit umístění, ke kterému SAP HANA zapíše zálohy transakčního protokolu. Provedení této změny v HANA Studio. I když SAP HANA zálohuje celý protokol segmenty automaticky, měli byste určit interval zálohování protokolu a být deterministický. To platí zejména při použití možnosti zotavení po havárii, vzhledem k tomu obvykle chcete provést zálohování protokolu s dobou deterministický. V následujících případech se 15 minut nastavit jako interval zálohování protokolů.

![Naplánovat protokoly zálohování SAP HANA v SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Můžete použít taky zálohy, které se častěji než každých 15 minut. Častější nastavení se často používá ve spojení s funkcemi pro zotavení po havárii velkých instancích HANA. Někteří zákazníci provést zálohování protokolu transakcí každých 5 minut.  

Pokud databáze obsahuje byly dosud nezálohovali, posledním krokem je provedení zálohy databáze na základě souborů k vytvoření jedné položky zálohování, která musí existovat v rámci katalogu záloh. SAP HANA v opačném případě nemůžete zahájit zálohování zadaný protokol.

![Vytvořit zálohu souborů k vytvoření jedné položky zálohování](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po vašich prvních úspěšné úložiště snímků, že byly provedeny, můžete odstranit snímek testu provedeného v kroku 6. Pokud chcete učinit, spusťte skript `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl
```

Následuje příklad výstupu skriptu:
```
Checking Snapshot Status for h80
**********************Checking access to Storage**********************
Storage Snapshot Access successful.
**********************Getting list of volumes that match HANA instance specified**********************
Collecting set of volumes hosting HANA matching pattern *h80* ...
Volume show completed successfully.
Adding volume hana_data_h80_mnt00001_t020_vol to the snapshot list.
Adding volume hana_log_backups_h80_t020_vol to the snapshot list.
Adding volume hana_shared_h80_t020_vol to the snapshot list.
**********************Adding list of snapshots to volume list**********************
Collecting set of snapshots for each volume hosting HANA matching pattern *h80* ...
**********************Displaying Snapshots by Volume**********************
hana_data_h80_mnt00001_t020_vol
Test_HANA_Snapshot.2018-02-06_1753.3
Test_HANA_Snapshot.2018-02-06_1815.2
….
Command completed successfully.
Exiting with return code: 0
Command completed successfully.
```


### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorování počtu a velikosti snímků na diskovém svazku

Na konkrétní úložiště svazku můžete sledovat počet snímků a využití úložiště těchto snímků. `ls` Příkaz nezobrazí snímek adresáře nebo souborů. Nicméně příkaz operačních systémů Linux `du` zobrazuje podrobnosti o těchto snímků úložiště, protože se ukládají na stejném svazku. Tento příkaz lze použít s těmito možnostmi:

- `du –sh .snapshot`: Tato možnost poskytuje celkový počet všechny snímky v adresáři snímku.
- `du –sh --max-depth=1`: Tato možnost uvádí všechny snímky, které jsou uloženy v **.snapshot** složky a velikost jednotlivých snímků.
- `du –hc`: Tato možnost poskytuje celkovou velikost používané všechny snímky.

Používejte tyto příkazy, abyste měli jistotu, že snímky, které jsou přijatá a uložená nejsou využívání všech úložiště na svazcích.

>[!NOTE]
>Snímky spuštění logické jednotky nejsou viditelné pomocí předchozích příkazů.

### <a name="getting-details-of-snapshots"></a>Získávání podrobností snímků
Chcete-li získat více podrobností o snímky, můžete použít také skript `azure_hana_snapshot_details.pl`. Tento skript můžete spustit v některém umístění, pokud je aktivní server v umístění pro obnovení po havárii. Skript obsahuje následující výstup rozdělit podle každý svazek, který obsahuje snímky: 
   * Velikost celkový počet snímků na svazku
   * Následující podrobnosti v jednotlivých snímků v tomto svazku: 
      - Název snímku 
      - Vytvořit čas 
      - Velikost snímku
      - Frekvence snímku
      - ID HANA zálohování přidružené k tomuto snímku, pokud je to potřeba

Následuje příklad syntaxe provádění skriptu:

```
./azure_hana_snapshot_details.pl 
```

Protože se skript pokusí se načíst ID zálohy HANA, je potřeba připojit k instanci SAP HANA. Toto připojení vyžaduje konfiguračního souboru *HANABackupCustomerDetails.txt* správně nastavit. Výstup dvěma snímky ve svazku může vypadat nějak takto:

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```



### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Snížení počtu snímků na serveru

Jak jsme vysvětlili výše, můžete snížit počet některé popisky snímky, které ukládáte. Poslední dva parametry tohoto příkazu k zahájení snímku se popisek a počet snímků, které chcete zachovat.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

V předchozím příkladu je popisek snímku **dailyhana** a je počet snímků s tímto popiskem mají být uchovány **28**. Jak vám odpoví na využití místa na disku, můžete snížit počet uložené snímky. Je snadný způsob, jak snížit počet snímků na 15, například pro spuštění skriptu s posledním parametrem nastaveným na **15**:

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

Pokud spouštíte skript s tímto nastavením, je počet snímků, včetně nové úložiště snímků, 15. 15 nejnovější snímky jsou uchovávány a 15 starší snímky se odstraní.

 >[!NOTE]
 > Tento skript snižuje počet snímků, pouze pokud existuje více než 1 hodina staré snímky. Skript nedojde k odstranění snímků, které jsou menší než 1 hodinu stará. Tato omezení se vztahují k funkce zotavení po havárii volitelné nabízené.

Pokud už nechcete zachovat sadu snímky pomocí zálohování popisek **hanadaily** v příklady syntaxe, můžete spustit skript s **0** jako číslo uchovávání informací. Potom se odeberou všechny snímky odpovídající tohoto popisku. Odebírá se přístup ke všem snímkům může ovlivnit možnosti velkých instancích HANA funkce zotavení po havárii.

Druhá možnost odstranit konkrétní snímků je skript `azure_hana_snapshot_delete.pl`. Tento skript slouží k odstranění snímku nebo sadu snímky, buď pomocí ID zálohy HANA jak se nachází v HANA Studio, nebo prostřednictvím samotný název snímku. V současné době se zálohování ID pouze váže na snímky vytvořené pro **hana** typ snímku. Snímek zálohy typu **protokoly** a **spouštěcí** neprovádějte snímek SAP HANA, a proto neexistuje žádné ID zálohy pro tyto snímky. Pokud je zadán název snímku, hledá všechny snímky v různých svazcích, které odpovídají názvu zadané snímku. 

Volání skriptu musíte zadat identifikátor SID instance HANA pomocí syntaxe volání skriptu:

```
./azure_hana_snapshot_delete.pl <SID>

```

Spuštění skriptu jako uživatel **kořenové**.

Pokud vyberete snímku, můžete odstranit jednotlivých snímků jednotlivě. Nejprve zadejte svazek, který obsahuje snímek a potom zadejte název snímku. Pokud snímek na tento svazek existuje a je více než 1 hodinu stará, je odstranit. Názvy svazků a názvy snímků můžete najít pomocí provádí `azure_hana_snapshot_details` skriptu. 

>[!IMPORTANT]
>Pokud data, která existuje pouze na snímku odstraňujete, po odstranění snímku, že dojde ke ztrátě dat navždy.

  
## <a name="file-level-restore-from-a-storage-snapshot"></a>Obnovení na úrovni souboru ze snímku úložiště
Pro typ snímku **hana** a **protokoly**, dostanete snímků přímo na svazky v **.snapshot** adresáře. Je podadresář pro jednotlivé snímky. Můžete zkopírovat každý soubor ve stavu, ve kterém se v okamžiku snímků z podadresář do tohoto adresáře struktury. V aktuální verzi souboru, který je **ne** obnovení skript ve formě pro obnovení snímku samoobslužné funkce (v případě, že obnovení snímku lze provést, protože část samoobslužných služeb zotavení po Havárii skripty v lokalitě zotavení po Havárii během převzetí služeb při selhání). Provozní tým Microsoft musíte požádat tak, že otevřete žádost o služby k obnovení požadovaného snímku z existující snímky k dispozici.

>[!NOTE]
>Obnovení jednotlivých souborů nefunguje pro snímky spuštění logické jednotky nezávislé na typ jednotek velká Instance HANA. **.Snapshot** adresář není vystaveno v spouštěcí logickou jednotku. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Obnovit na nejnovější snímek HANA

V případě produkčního dolů lze inicializovat procesem obnovení ze snímku úložiště jako incident zákazníků s podporu Microsoft Azure. Pokud data byla odstraněna v produkční systém, a k obnovení provozní databáze je jediný způsob, jak ho načíst je na vás vysokou naléhavostí.

Obnovení bodu v čase v jiné situaci, může být s nízkou naléhavost a plánované dny předem. Můžete naplánovat toto obnovení se SAP HANA v Azure Service Management namísto vyvolání příznak s vysokou prioritou. Například může být plánujete upgrade softwaru SAP s použitím nového balíčku rozšíření. Pak budete muset obnovit snímek, který představuje stav před upgradem balíček rozšíření.

Před odesláním požadavku, je nutné připravit. SAP HANA v Azure Service Management týmu můžete žádost zpracovat a poskytují obnovené svazky. Následně obnovíte databázi HANA podle snímků. 

Následující ukazuje, jak připravit pro požadavek:

>[!NOTE]
>Uživatelské rozhraní se můžou mírně lišit od na následujících snímcích obrazovky, v závislosti na verzi SAP HANA, kterou používáte.

1. Rozhodněte, které snímek, který chcete obnovit. Pokud dáte pokyn, jinak se obnoví pouze hana/datový svazek. 

1. Vypnutí HANA instance.

 ![Vypnutí HANA instance](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odpojte objemy dat v každém uzlu databáze HANA. Pokud datové svazky se pořád připojený k operačnímu systému, obnovení snímku se nezdaří.
 ![Odpojte objemy dat v každém uzlu databáze HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otevřete žádost o podporu Azure a zahrňte pokyny k obnovení konkrétní snímek.

 - Během obnovení: SAP HANA v Azure Service Management může požádat o jeho účasti na konferenci pro zajištění koordinace, ověřování a potvrzení, že je obnovit snímek správné úložiště. 

 - Po obnovení: SAP HANA v Azure Service Management vás upozorní, když obnovení snímku úložiště.

1. Po dokončení procesu obnovení znovu připojte všechny datové svazky.

 ![Znovu připojit všechny datové svazky](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

1. Možnosti obnovení v rámci SAP HANA Studio, vyberte, pokud nepocházejí automaticky při opětovném připojení k databázi HANA přes SAP HANA Studio. Následující příklad ukazuje obnovení na poslední snímek HANA. Snímek úložiště vloží jeden snímek HANA. Pokud obnovujete na nejnovější snímek úložiště, měla by být nejnovější snímek HANA. (Pokud obnovujete na starší snímek úložiště, budete muset najít snímek HANA na základě času pořízení snímku úložiště.)

 ![Vybrat možnosti obnovení v rámci SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

1. Vyberte **obnovit databázi do konkrétního data zálohování nebo úložiště snímku**.

 ![V okně zadejte typ obnovení](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

1. Vyberte **zadejte zálohování bez katalogu**.

 ![V okně zadejte umístění zálohy](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

1. V **cílového typu** seznamu vyberte **snímku**.

 ![Zadejte zálohování a obnovení okno](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

1. Vyberte **Dokončit** chcete zahájit proces obnovení.

 ![Vybrat dokončit"Chcete-li zahájit proces obnovení](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

1. Databáze HANA je obnovit a obnovení do snímku HANA, který je součástí úložiště snímku.

 ![Databáze HANA je obnovení a obnovit na snímek HANA](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>Obnovit do posledního stavu

Následující proces obnoví HANA snímek, který je součástí úložiště snímku. Jeho následnému obnovení zálohy transakčního protokolu nejnovější stav databáze před obnovením snímku úložiště.

>[!IMPORTANT]
>Než budete pokračovat, ujistěte se, že máte kompletní a souvislý řetězec zálohy protokolu transakce. Bez tyto zálohy nejde obnovit aktuální stav databáze.

1. Proveďte kroky 1 až 6 v obnovení na nejnovější snímek HANA.

1. Vyberte **obnovit databázi do stavu posledního**.

 ![Vyberte možnost "Obnovit databázi do posledního stavu"](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

1. Zadejte umístění nejnovější zálohy protokolu HANA. Umístění musí obsahovat všechny HANA zálohy transakčního protokolu ze snímku HANA na nejnovější stav.

 ![Zadejte umístění nejnovější zálohy protokolu HANA](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

1. Výběr zálohy jako základ, ze kterého se má obnovit databázi. V tomto příkladu snímek HANA na snímku obrazovky je snímek HANA, která byla součástí úložiště snímku. 

 ![Výběr zálohy jako základ, ze kterého se má obnovit databázi](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

1. Zrušte **použití rozdílového zálohování** zaškrtávací políčko, pokud neexistují rozdíly mezi časem snímku HANA a nejnovější stav.

 ![Zrušte zaškrtnutí políčka "Použití rozdílové zálohy", pokud neexistuje žádné rozdíly](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

1. Na souhrnné obrazovce vyberte **Dokončit** spustíte proces obnovení.

 ![Na souhrnné obrazovce klepněte na tlačítko "Dokončit"](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>Obnovení do jiného bodu v čase
Chcete-li provést obnovení do bodu v čase mezi HANA snímku (zahrnutý ve snímku úložiště) a ten, který je pozdější než obnovení bodu v čase HANA snímků, postupujte následovně:

1. Ujistěte se, že máte všechny zálohy transakčního protokolu ze snímku HANA po dobu, kterou chcete obnovit.
1. Začněte postup v části obnovení na nejnovější stav.
1. V kroku 2 postupu v **zadejte typ obnovení** okně **obnovit databázi do následující bod v čase**a pak určete bod v čase. 
1. Proveďte kroky 3 až 6.

## <a name="monitor-the-execution-of-snapshots"></a>Sledujte provádění pracovního snímky

Při použití úložiště snímků velkých instancích HANA, musíte také sleduje provádění těchto snímků. Skript, který se spustí snímek úložiště zapíše výstup do souboru a pak ji uloží je do stejného umístění jako tyto skripty. U jednotlivých snímků úložiště je zapsán do samostatného souboru. Ukazuje různé fáze výstup každého souboru, že se spustí skript snímku:

1. Vyhledá svazky, které potřebujete k vytvoření snímku.
1. Vyhledá snímkům pořízeným z těchto svazků.
1. Odstraní případné existující snímky tak, aby odpovídaly počet snímků, které jste zadali.
1. Vytvoří snímek SAP HANA.
1. Vytvoří snímek úložiště přes svazky.
1. Odstraní snímek SAP HANA.
1. Přejmenuje nejnovější snímek do **.0**.

Nejdůležitější součást skriptu cab identifikovat je této části:
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
Zobrazí se od této ukázky jak skript záznamy vytvoření snímku HANA. V tomto případě horizontální navýšení kapacity tento proces zahájit na hlavní uzel. Hlavní uzel zahájí synchronní vytváření snímků SAP HANA na všech pracovních uzlů. Potom se pořídí snímek úložiště. Po úspěšném spuštění snímků úložiště HANA snímek odstraní. Odstranění snímku HANA je zahájeno z hlavního uzlu.


**Další kroky**
- Přečtěte si [zásady zotavení po havárii a příprava](hana-concept-preparation.md).
