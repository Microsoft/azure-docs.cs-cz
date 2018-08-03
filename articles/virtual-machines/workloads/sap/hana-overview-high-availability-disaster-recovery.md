---
title: Vysoká dostupnost a zotavení po havárii SAP Hana v Azure (velké instance) | Dokumentace Microsoftu
description: Vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance)
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
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 554991c7c0f11a095a11ae24dbb693a1a3ba50fd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430116"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Velké instance SAP HANA vysokou dostupnost a zotavení po havárii v Azure 

>[!IMPORTANT]
>Tato dokumentace je nahrazen dokumentace správy SAP HANA nebo SAP poznámky. Očekává se, zda má čtečka důkladného porozumění a odborných znalostí v SAP HANA správu a provoz, zejména s tématy zálohování, obnovení, vysokou dostupnost a zotavení po havárii. V této dokumentaci se zobrazí snímky obrazovky ze SAP HANA Studio. Obsah, struktura a povaze obrazovky nástroje pro správu SAP a nástrojů, samotné se mohou změnit ze SAP HANA verzí.

Je důležité věnovat kroky a postupy provést ve vašem prostředí a s HANA verzí a verzí. Některé procesy popsané v této dokumentaci je zjednodušená pro lepší porozumění obecné a neměly by má být použit jako podrobný postup pro konečný výsledek operace příruček. Pokud chcete vytvořit operaci příruček pro konkrétní konfiguraci, musíte pro testování a výkon vašich procesů a zdokumentujte procesy související s konkrétní konfigurací. 


Vysoká dostupnost a zotavení po havárii (DR) jsou důležité aspekty spuštění klíčových SAP HANA v Azure (velké instance) serveru. Je důležité pro práci s SAP, systémoví integrátoři nebo Microsoft správně navrhovat a implementovat strategie zotavení po havárii a správné s vysokou dostupností. Je také důležité vzít v úvahu plánovaného bodu obnovení (RPO) a plánovaná doba obnovení, které jsou specifické pro vaše prostředí.

Společnost Microsoft podporuje některé možnosti vysoké dostupnosti SAP HANA s velkých instancích HANA. Mezi tyto možnosti patří:

- **Replikace úložiště mezi**: systém úložiště možnost replikace všech dat na jiné razítko velká Instance HANA v jiné oblasti Azure. SAP HANA funguje nezávisle na tuto metodu. Tato funkce je výchozí mechanismus pro zotavení po havárii nabízí pro velké instance HANA.
- **Systémové replikace HANA**: [replikace všech dat v SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) do samostatného systému SAP HANA. Plánovaná doba obnovení je minimalizován prostřednictvím replikace dat v pravidelných intervalech. SAP HANA podporuje asynchronní, která je synchronní režimy v paměti a synchronní. Synchronním režimu se používá pouze pro systémy SAP HANA, které jsou ve stejném datacentru nebo menší než 100 km od sebe. V aktuální návrhu razítka velká Instance HANA je možné pro zajištění vysoké dostupnosti v rámci jedné oblasti jenom systémové replikace HANA. Systémové replikace HANA vyžaduje reverzní proxy server jiného výrobce nebo směrování komponenty pro konfigurace zotavení po havárii do jiné oblasti Azure. 
- **Hostování – automatické převzetí služeb při selhání**: místní selhání obnovení řešení pro SAP HANA, které je alternativou k HANA system replication. Pokud hlavní uzel stane nedostupným, nakonfigurujte jednu nebo více pohotovostní SAP HANA uzly v režimu horizontální navýšení kapacity a SAP HANA automaticky převezme služby při selhání pohotovostní uzel.

SAP HANA v Azure (velké instance) se nabízí ve dvou oblastech Azure čtyři geopolitické oblasti (USA, Austrálie, Evropa a Japonsko). Dvě oblasti v geopolitické oblasti, které jsou hostiteli velká Instance HANA razítka jsou připojené k samostatné vyhrazené sítě okruhů. Ty se používají pro replikaci snímků úložiště zadali způsoby zotavení po havárii. Replikace nedojde ve výchozím nastavení, ale je nastavit pro zákazníky, kteří pořadí funkce zotavení po havárii. Replikace úložiště je závislý na použití snímků úložiště pro velké instance HANA. Není možné vybrat oblasti Azure jako, který je v jiné oblasti geopolitické oblasti zotavení po Havárii. 

Následující tabulka uvádí aktuálně se podporují vysokou dostupnost a obnovení metody a kombinace:

| Scénář ve velkých instancích HANA | Možnost vysoké dostupnosti | Možnost obnovení po havárii | Komentáře |
| --- | --- | --- | --- |
| Jeden uzel | Není k dispozici. | Vyhrazené nastavení zotavení po Havárii.<br /> Nastavení zotavení po Havárii Multipurpose. | |
| Hostování – automatické převzetí služeb při selhání: horizontální navýšení kapacity (s nebo bez pohotovostní)<br /> včetně 1 + 1 | Možná s úsporný režim s ohledem aktivní roli.<br /> HANA řídí přepínač role. | Vyhrazené nastavení zotavení po Havárii.<br /> Nastavení zotavení po Havárii Multipurpose.<br /> Synchronizace zotavení po Havárii pomocí replikace úložiště. | HANA svazek sady jsou připojeny ke všem uzlům.<br /> Zotavení po Havárii lokalita musí mít stejný počet uzlů. |
| Systémové replikace HANA | Možná se primární nebo sekundární instalační program.<br /> Přesune sekundární do primární role v případě převzetí služeb při selhání.<br /> Systémové replikace HANA a operačního systému řízení převzetí služeb při selhání. | Vyhrazené nastavení zotavení po Havárii.<br /> Nastavení zotavení po Havárii Multipurpose.<br /> Synchronizace zotavení po Havárii pomocí replikace úložiště.<br /> Zotavení po Havárii s využitím systémové replikace HANA ještě není možná bez komponenty třetích stran. | Samostatnou sadu pro diskové svazky jsou připojené k jednotlivým uzlům.<br /> Pouze diskové svazky sekundární replika v produkční lokality se replikují do umístění pro zotavení po Havárii.<br /> Jedna sada svazků vyžádáním v lokalitě zotavení po Havárii. | 

Vyhrazené zotavení po Havárii instalační program, ve kterém se nepoužívá velká Instance HANA jednotek v lokalitě zotavení po Havárii pro spouštění jiných úloh nebo testovacím systému. Jednotka je pasivní činnost a nasazuje se pouze v případě převzetí služeb při havárii provádí. Toto nastavení však není upřednostňovaný volba pro mnoho zákazníků.

Přečtěte si [HLI Podporované scénáře](hana-supported-scenario.md) další úložiště rozložení a sítě ethernet a podrobnosti o vaší architektuře.

> [!NOTE]
> [Nasazení SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (více instancí HANA na jednu jednotku) jako zakreslovat pracovní scénáře s vysokou dostupnost a zotavení po Havárii metody uvedené v tabulce. Výjimka je použití systémové replikace HANA s clusterem automatické převzetí služeb při selhání podle Pacemaker. Takovém podporuje pouze jednu instanci HANA na jednotku. Pro [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) nasazení, pouze mimo úložiště – metody založené na vysokou dostupnost a zotavení po Havárii fungovat, pokud je nasazení více než jednoho tenanta. Pomocí jednoho tenanta, nasazení jsou platné všechny metody, které jsou uvedeny.  

Multipurpose nastavení zotavení po Havárii je, kde velká Instance HANA jednotek v lokalitě zotavení po Havárii běží mimo produkční úlohy. V případě havárie, vypnout mimo produkční systém připojit replikované úložiště (Další) svazek sady a spusťte instance HANA produkčního prostředí. Většina zákazníků, kteří používají funkce zotavení po havárii velká Instance HANA pomocí této konfigurace. 


Další informace o vysoké dostupnosti SAP HANA můžete najít v těchto článcích SAP: 

- [Dokument White Paper o vysokou dostupnost v SAP HANA](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Příručka věnovaná SAP HANA](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy Video o systémové replikace SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [Poznámka: podpora #1999880 – nejčastější dotazy k SAP HANA System Replication SAP](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP Support Poznámka #2165547 – SAP HANA zpět nahoru a obnovení v rámci prostředí replikace systému SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Poznámka: podpora #1984882 – použití SAP HANA System Replication SAP pro výměnu hardwaru s minimální nebo nulové výpadky](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Požadavky sítě pro zotavení po havárii pomocí velké instance HANA

Abyste mohli využívat funkce zotavení po havárii velkých instancích HANA, budete muset navrhnout síťové připojení ke dvěma oblastmi Azure. Budete potřebovat připojení okruhu Azure ExpressRoute z místně ve vaší hlavní oblasti Azure a další připojení okruhu z místního vaší oblasti pro zotavení po havárii. Tato míra popisuje situaci, ve kterém dojde k problému v oblasti Azure, včetně Microsoft Enterprise Edge směrovač (MSEE) umístění.

Druhá míra budete moct připojit virtuální sítě Azure, které se připojují k SAP HANA v Azure (velké instance) v jedné oblasti s okruhem ExpressRoute, který se připojuje velké instance HANA v jiné oblasti. S tímto *křížové připojení*, služby spuštěné v Azure virtuální síť v oblast 1 může připojit k velká Instance HANA jednotky v oblasti 2 a naopak. Tato míra řeší případ, ve kterém pouze jeden z míst směrovači MSEE, která se připojuje vaše místní umístění s využitím Azure přejde do režimu offline.

Odolného configuration případech zotavení po havárii je znázorněný následujícím obrázku:

![Optimální konfiguraci zotavení po havárii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Další požadavky na velkých instancích HANA úložiště replikaci pro zotavení po havárii

Kromě předchozích požadavků pro nastavení zotavení po havárii s velkých instancích HANA musíte mít:

- Pořadí SAP HANA v Azure (velké instance) SKU pro stejnou velikost jako skladové položky v produkčním prostředí a nasaďte je do oblasti pro zotavení po havárii. V aktuální nasazení zákazníků tyto instance se používají ke spuštění instance HANA-li se o neprodukční. Tyto konfigurace se označují jako *multipurpose nastavení zotavení po Havárii*.   
- Objednat další úložiště v lokalitě zotavení po Havárii pro každou SAP HANA v Azure (velké instance) skladové položky, které chcete obnovit v lokalitě zotavení po havárii. Nakupování dalších úložišť vám umožní přidělit svazky úložiště. Můžete přidělit svazků, které jsou cílem replikace úložiště z oblasti Azure v produkčním prostředí na zotavení po havárii oblasti Azure.
- V případě, kde máte HSR nastavený na primární a nastavení replikace úložiště založené na lokalitě zotavení po Havárii, je nutné zakoupit další úložiště v lokalitě zotavení po Havárii tak i primární a sekundární uzly data získá replikují do lokality pro zotavení po Havárii.

 

## <a name="backup-and-restore"></a>Zálohování a obnovení

Jeden z nejdůležitějších aspektů do provozní databáze je chránit před katastrofickými událostmi. Příčinu tyto události můžou být čímkoli od přírodními katastrofami na jednoduché uživatelské chyby.

Zálohování databáze, možnost obnovit do libovolného bodu v čase (například než někdo odstraní důležitá data), umožňuje obnovení do stavu, ve kterém je co nejblíže způsobu, jakým byl před narušení.

Pro dosažení co nejlepších výsledků se musí provádět dva typy záloh:

- Zálohování databáze: úplné, přírůstkové nebo rozdílové zálohy
- Zálohy transakčního protokolu

Kromě zálohování úplné databáze provádí na úrovni aplikace můžete provádět zálohování pomocí snímků úložiště. Snímky úložiště nenahrazují zálohy transakčního protokolu. Zálohy transakčního protokolu zůstanou důležité obnovení databáze do určitého bodu v čase nebo na prázdný protokoly z již potvrzené transakce. Snímků úložiště lze však rychlejší zotavení rychle poskytnutím vpřed snímek databáze. 

SAP HANA v Azure (velké instance) nabízí dvě možnosti zálohování a obnovení:

- Sestavili (DIY). Po výpočtu zajistit, že není dostatek místa na disku, proveďte kompletní databáze a zálohování protokolů pomocí jedné z následujících metod zálohování disku. Můžete zálohovat přímo na svazky připojené jednotky velká Instance HANA, nebo do sdílené složky NFS (Network File), které jsou nastavené v Azure virtuální počítač (VM). V takovém případě zákazníci nastavení virtuálního počítače s Linuxem v Azure, připojit k virtuálnímu počítači Azure Storage a sdílejí úložiště přes nakonfigurovaným serverem systému souborů NFS v tomto virtuálním počítači. Pokud provádíte zálohování u svazků, které přímo připojit k jednotky velká Instance HANA, musíte zkopírovat zálohy do účtu služby Azure storage (po nastavení virtuálního počítače Azure, který exportuje sdílených složek NFS, které jsou založené na Azure Storage). Můžete také použít trezoru záloh Azure nebo Azure studeného úložiště. 

   Další možností je použít nástroj na ochranu dat třetí strany a uložte zálohy po zkopírování do účtu služby Azure storage. Rozběhněte zcela možnost zálohování může být také nezbytné pro data, která potřebujete ukládat pro delší dobu pro účely auditování a dodržování předpisů. Ve všech případech se zálohy jsou zkopírovány do sdílené složky NFS představovány virtuálního počítače a služby Azure Storage.

- Infrastruktura zálohování a obnovení. Můžete také použít funkci zálohování a obnovení, který poskytuje základní infrastruktury SAP HANA v Azure (velké instance). Tato možnost splňuje potřeby zálohování a rychlé obnovení. Zbývající část adresy funkce zálohování a obnovení, které se nabízí s velkých instancích HANA. Tato část se věnuje také relace zálohování a obnovení po havárii má nabízí funkce obnovení ve velkých instancích HANA.

>   [!NOTE]
>   Snímek technologie, která používá základní infrastruktury velkých instancích HANA obsahuje závislost na snímky SAP HANA. V tomto okamžiku SAP HANA snímků nebudou fungovat ve spojení s více tenanty kontejnerů víceklientské databáze SAP HANA. Pokud jen pro nasazení jednoho klienta pracovní snímky SAP HANA a tuto metodu můžete použít.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Pomocí snímků úložiště SAP Hana v Azure (velké instance)

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

Získat nejnovější snímek skripty a dokumentaci k [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts). 

### <a name="storage-snapshot-considerations"></a>Aspekty volby úložiště snímku

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

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Předpoklady pro použití snímků samoobslužné služby storage

Chcete-li zajistit, že se snímek skript spustí úspěšně, ujistěte se, že Perl je nainstalován na operační systém Linux na velkých instancích HANA serveru. Perl dodává s předinstalovanou na velká Instance HANA jednotky. Pokud chcete zkontrolovat verzi jazyka Perl, použijte následující příkaz:

`perl -v`

![Veřejný klíč se zkopíruje spuštěním tohoto příkazu](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>Nastavení úložiště snímků

Nastavení úložiště snímků s velkých instancích HANA, postupujte podle těchto kroků:
1. Ujistěte se, zda Perl je nainstalován operační systém Linux na velkých instancích HANA serveru.
1. Upravit/etc/ssh/ssh\_config přidejte řádek _Mac hmac – sha1_.
1. Vytvořte záložní uživatelský účet SAP HANA na hlavní uzel pro každou instanci SAP HANA, které používáte, pokud je k dispozici.
1. Instalace klienta SAP HANA HDB ve všech serverech velké instance SAP HANA.
1. Na prvním serveru velké instance SAP HANA každé oblasti vytvořte veřejný klíč pro přístup k základní infrastrukturu úložiště, který řídí vytvoření snímku.
1. Skripty a konfigurační soubor z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts) umístění **hdbsql** instalace SAP HANA.
1. Upravit *HANABackupDetails.txt* souboru podle potřeby pro specifikace příslušného zákazníka.

Získat nejnovější snímek skripty a dokumentaci k [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts). 

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

### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Vytvoření uživatelského účtu SAP HANA

K zahájení vytváření snímků SAP HANA, musíte vytvořit uživatelský účet v SAP HANA, můžete použít skripty snímků úložiště. Vytvořte uživatelský účet SAP HANA v rámci SAP HANA Studio pro tento účel. Uživatel musí být vytvořená v rámci SYSTEMDB a není v databázi identifikátor SID pro MDC. V prostředí jednoho kontejneru je uživatel v rámci databáze tenantů. Tento účet musí mít následující oprávnění: **správce zálohování** a **katalogu čtení**. V tomto příkladu je uživatelské jméno **SCADMIN**. Název uživatelského účtu v HANA Studio vytvořili rozlišuje velká a malá písmena. Je nutné vybrat **ne** pro by uživatel musel změnit heslo při jeho příštím přihlášení.

![Vytvoření uživatele v HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Pokud používáte MCOD nasazení s více instancí SAP HANA na jednu jednotku, musíte tento krok opakujte pro každou instanci SAP HANA.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: Autorizaci uživatelského účtu SAP HANA

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

- **Azure\_hana\_backup.pl**: Tento skript je naplánován pomocí nástroje pro plánování procesu Cron systému Linux ke spuštění úložiště snímků pro dat HANA a sdílených svazků, svazky logbackups/hana/nebo operačního systému.
- **Azure\_hana\_replikace\_status.pl**: Tento skript nabízí základní podrobnosti o stavu replikace ze produkční lokality do lokality pro obnovení po havárii. Skript monitoruje zajistíte, že probíhá replikace a zobrazuje velikost položek, které se replikují. Také obsahuje pokyny, pokud replikace trvá moc dlouho, nebo pokud odkaz je mimo provoz.
- **Azure\_hana\_snímku\_details.pl**: Tento skript obsahuje seznam základními podrobnostmi o všechny snímky, jeden svazek, které existují ve vašem prostředí. Tento skript můžete spustit na primárním serveru nebo na jednotce serveru v umístění pro obnovení po havárii. Skript obsahuje následující informace rozepsané podle jednotlivých svazek, který obsahuje snímky:
   * Velikost celkový počet snímků na svazku
   * Následující podrobnosti v jednotlivých snímků v tomto svazku: 
      - Název snímku 
      - Vytvořit čas 
      - Velikost snímku
      - Frekvence snímku
      - ID HANA zálohování přidružené k tomuto snímku, pokud je to potřeba
- **Azure\_hana\_snímku\_delete.pl**: Tento skript odstraní snímek úložiště nebo sadu snímky. Můžete použít buď ID zálohování SAP HANA jak se nachází v HANA Studio, nebo název snímku úložiště. ID zálohy je v současné době pouze vázán na snímky vytvořené pro HANA data/log/sdílené svazky. Jinak Pokud je zadaný Identifikátor snímku, usiluje všechny snímky, které odpovídají zadané snímku ID.  
- **testHANAConnection.pl**: Tento skript Otestuje připojení k instanci SAP HANA a je nutný k nastavení úložiště snímků.
- **testStorageSnapshotConnection.pl**: Tento skript má dva účely. Za prvé zajišťuje, že velká Instance HANA jednotky, které spouští skripty má přístup k přiřazené úložiště virtuálního počítače a na rozhraní úložiště snímku velkých instancích HANA. Druhým účelem je vytvořit dočasný snímků pro instance HANA, kterou testujete. Tento skript by měl spustit pro každou instanci HANA na serveru k zajištění, že skripty zálohování fungovat podle očekávání.
- **removeTestStorageSnapshot.pl**: Tento skript odstraní snímek testu vytvořený pomocí skriptu **testStorageSnapshotConnection.pl**.
- **Azure\_hana\_zotavení po havárii\_failover.pl**: Tento skript spustí zotavení po Havárii převzetí služeb při selhání do jiné oblasti. Je nutné provést na jednotce velká Instance HANA v oblasti zotavení po Havárii, nebo na jednotku, kterou chcete pro převzetí služeb při selhání. Tento skript zastaví se replikace úložiště z primární strany na stranu sekundární, obnoví nejnovější snímek na svazcích zotavení po Havárii a poskytuje přípojné body pro DR svazky.
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


### <a name="step-7-perform-snapshots"></a>Krok 7: Proveďte snímky

Po dokončení postupu přípravy, můžete začít konfigurovat konfiguraci skutečného úložiště snímků. Skript, který chcete naplánovat funguje vertikálně navýšit kapacitu a horizontální navýšení kapacity konfigurací SAP HANA. Pro pravidelné a pravidelné spouštění skriptu zálohování plánování skriptu pomocí nástroje cron. 

Můžete vytvořit tři typy zálohy snímků:
- **HANA**: zálohy kombinované snímku, ve kterém svazků, které obsahují/hana/dat a/hana/sdílené (který obsahuje také /usr/sap) se vztahují koordinované snímku. Obnovení jednoho souboru je možné z tohoto snímku.
- **Protokoly**: zálohy snímku svazku logbackups/hana /. Žádný snímek HANA se aktivuje, chcete-li spustit tento snímek úložiště. Tento svazek úložiště je určená tak, aby obsahovala zálohy protokolu transakce SAP HANA. Toto omezení protokolu růst a zabránit ztrátě dat probíhají častěji. Obnovení jednoho souboru je možné z tohoto snímku. Není nižší četnost méně než 3 minut.
- **Spouštěcí**: snímku svazku, který obsahuje velké instance HANA spouštěcí číslo logické jednotky (LUN). Tato záloha snímku je možné jenom s typu I skladové položky z velkých instancích HANA. Obnovení nelze provést jediný soubor ze snímku svazku, který obsahuje spouštěcí logickou jednotku.


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

### <a name="snapshot-strategies"></a>Strategie snímku
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
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
V předchozím příkladu se po hodinách kombinované snímku, pokrývající svazky, které obsahují data/hana/a /hana/shared (včetně/usr/sap) umístění. Pomocí tohoto typu snímku pro rychlejší obnovení bodu v čase během posledních dvou dnů. Na těchto svazcích je navíc denní snímek. Ano máte dva dny pokrytí podle hodinových snímky a čtyři týdny pokrytí podle denní snímky. Kromě toho svazek zálohování protokolu transakcí zálohovat denně. Tyto zálohy se uchovávají i čtyři týdny. Jak vidíte na třetím řádku crontab, zálohování protokolu transakcí HANA je naplánován ke spuštění každých 5 minut. Rozloženo jsou časy zahájení cron různé úlohy, které jsou spouštěny snímků úložiště, takže tyto snímky nejsou provedeny všechny najednou v určitém bodě v čase. 

V následujícím příkladu je provést kombinované snímků, které pokrývá svazků, které obsahují/hana/dat a/hana/sdílené (včetně/usr/sap) umístění po hodinách. Tyto snímky se zachovat po dobu dvou dnů. Snímky svazků zálohování protokolu transakce jsou spouštěny v intervalech 5 minut a uchovávají po dobu 4 hodin. Jako dříve, zálohování protokolu transakcí HANA je naplánován ke spuštění každých 5 minut. Snímku svazku pro zálohování protokolu transakcí se provádí s prodlevou 2 minuty, po spuštění zálohy transakčního protokolu. V rámci těchto 2 minuty by měla dokončit zálohování protokolu transakcí SAP HANA za normálních okolností. Jako dříve, svazek, který obsahuje spouštěcí logické jednotky je zajištěná jednou denně snímku úložiště a uchovávají po dobu čtyř týdnů.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
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
- `du –sh --max-depth=1`: Uvádí všechny snímky, které jsou uloženy v tuto možnost **.snapshot** složky a velikost jednotlivých snímků.
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


### <a name="file-level-restore-from-a-storage-snapshot"></a>Obnovení na úrovni souboru ze snímku úložiště
Pro typ snímku **hana** a **protokoly**, dostanete snímků přímo na svazky v **.snapshot** adresáře. Je podadresář pro jednotlivé snímky. Můžete zkopírovat každý soubor ve stavu, ve kterém se v okamžiku snímků z podadresář do tohoto adresáře struktury. V aktuální verzi souboru, který je **ne** obnovení skript ve formě pro obnovení snímku samoobslužné funkce (v případě, že obnovení snímku lze provést, protože část samoobslužných služeb zotavení po Havárii skripty v lokalitě zotavení po Havárii během převzetí služeb při selhání). Provozní tým Microsoft musíte požádat tak, že otevřete žádost o služby k obnovení požadovaného snímku z existující snímky k dispozici.

>[!NOTE]
>Obnovení jednotlivých souborů nefunguje pro snímky spuštění logické jednotky nezávislé na typ jednotek velká Instance HANA. **.Snapshot** adresář není vystaveno v spouštěcí logickou jednotku. 


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

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>Obnovit na nejnovější snímek HANA

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

1. Proveďte kroky 1 až 6 v [obnovit na nejnovější snímek HANA](#recovering-to-the-most-recent-hana-snapshot).

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
1. Začněte postup v části [obnovit na nejnovější stav](#recovering-to-the-most-recent-state).
1. V kroku 2 postupu v **zadejte typ obnovení** okně **obnovit databázi do následující bod v čase**a pak určete bod v čase. 
1. Proveďte kroky 3 až 6.

### <a name="monitor-the-execution-of-snapshots"></a>Sledujte provádění pracovního snímky

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


## <a name="disaster-recovery-principles"></a>Zásady pro zotavení po havárii
Velké instance HANA nabízí funkce pro zotavení po havárii mezi razítka velká Instance HANA v různých oblastech Azure. Například pokud nasadíte velká Instance HANA jednotky v oblasti USA – Západ, Azure, můžete použít velká Instance HANA jednotky v oblasti USA – východ jako jednotky pro zotavení po havárii. Jak už bylo zmíněno dříve, zotavení po havárii se nenakonfiguruje automaticky, protože platíte za jiné jednotce velká Instance HANA v oblasti zotavení po Havárii vyžaduje. Nastavení zotavení po havárii funguje pro nastavení vertikální i horizontální navýšení kapacity. 

Ve scénářích nasazení, pokud zákazníci používají jednotku v oblasti zotavení po Havárii ke spuštění neprodukční systémy, které používají nainstalovaná instance HANA. Velká Instance HANA jednotka musí mít stejné skladovou Položku SKU pro produkční účely. Následující obrázek ukazuje, jakou konfiguraci disku mezi jednotky serverů v oblasti Azure produkčního prostředí a oblasti pro zotavení po havárii bude vypadat takto:

![Konfigurace nastavení zotavení po Havárii z hlediska disku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak je znázorněno na tomto obrázku přehled, pak musíte pro řazení druhou sadu diskové svazky. Cílové diskové svazky mají stejnou velikost jako produkční svazky pro produkční instance v jednotkách pro zotavení po havárii. Tyto diskové svazky jsou přidružena k jednotce velká Instance HANA serveru v lokalitě zotavení po havárii. Tyto svazky se replikují z oblasti produkční k lokalitě zotavení po Havárii:

- / hana/dat
- / hana/logbackups 
- /Hana/Shared (včetně/usr/sap)

Svazek /hana/log není replikovat, protože není potřeba transakční protokol SAP HANA ve způsobu, jakým se provádí obnovení z těchto svazků. 

Základ pro fungování obnovy po havárii nabízí se funkce replikace úložiště nabízí infrastruktura velká Instance HANA. Funkce, které se používá na straně úložiště není nepřetržitý datový proud změny, které se replikují v asynchronním režimu, protože dojde k změnám svazku úložiště. Místo toho je mechanismus, který vychází ze skutečnosti, že vytváření snímků tyto svazky v pravidelných intervalech. Rozdíl mezi již replikované snímků a nový snímek, která ještě nebyla replikována je pak přeneseny do lokality pro obnovení po havárii do cílové diskové svazky.  Tyto snímky jsou uložené ve svazcích a v případě selhání pro zotavení po havárii, je nutné obnovit na těchto svazcích.  

První přenos úplná data svazku by měl být předtím, než množství dat menší než rozdíly mezi snímky. V důsledku toho svazky v lokalitě zotavení po Havárii obsahovat každý, snímků svazků v produkční lokality. Nakonec můžete použít tento systém zotavení po Havárii zobrazíte na dřívější stav obnovení ztracených dat bez vrácení zpět v produkčním systému.

V případě MCOD nasazení s více nezávislých instancí SAP HANA na jednu jednotku velká Instance HANA očekává se, že všechny instance SAP HANA se zobrazuje na straně zotavení po Havárii replikace úložiště.

V případech, kdy použít systémové replikace HANA jako funkce vysoké dostupnosti v produkční lokality a pomocí replikace na základě úložiště pro zotavení po Havárii serveru jsou replikované svazky z obou uzlů z primární lokality do instance zotavení po Havárii. V lokalitě zotavení po Havárii tak, aby vyhovovaly replikace z primární i sekundární zotavení po Havárii, je nutné zakoupit další úložiště (stejnou velikost v době primárního uzlu). 



>[!NOTE]
>Funkce replikace úložiště velká Instance HANA je zrcadlení a replikace snímků úložiště. Pokud úložiště snímků nechcete provést, protože počínaje [zálohování a obnovení](#backup-and-restore) části tohoto článku, nesmí být žádné replikace na lokalitu pro obnovení po havárii. Provedením snímku úložiště je předpokladem pro replikaci úložiště do lokality pro obnovení po havárii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Příprava zotavení po havárii
V tomto scénáři máte produkční systém spuštěný na velkých instancích HANA v produkčním prostředí oblasti Azure. Kroky, které následují Předpokládejme, že je identifikátor SID systému HANA "PRD", a zda máte-li se o neprodukční systém spuštěný na velkých instancích HANA v oblasti zotavení po Havárii Azure. K tomu Předpokládejme, že její identifikátor SID je "TST." Tato konfigurace je znázorněna na následujícím obrázku:

![Začátek instalace zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Pokud instance serveru nebyla již byla seřazené sadou dalšího úložiště svazku, SAP HANA v Azure Service Management připojí další sadu svazky jako cíl pro repliku produkčního prostředí, aby velká Instance HANA jednotku, na kterém spouštíte TST HANA instance. K tomuto účelu budete muset poskytnout identifikátor SID instance HANA produkčního prostředí. Poté, co SAP HANA v Azure Service Management potvrdí přílohy tyto svazky, budete muset připojit tyto svazky do jednotky velká Instance HANA.

![Dalším krokem instalace zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Dalším krokem je instalace druhé instance SAP HANA na jednotce velká Instance HANA v oblasti zotavení po Havárii Azure, kde je spuštěn instance TST HANA. Nově instalovaný instance SAP HANA, musí mít stejný identifikátor SID. Uživatelé vytvoření musí mít stejné UID a ID skupiny, která má instance produkčního prostředí. Pokud se instalace úspěšně dokončila, budete muset:

- Provedení kroku 2 Příprava snímků úložiště popsané dříve v tomto článku.
- Pokud jste tak ještě neučinili, vytvořte veřejný klíč pro zotavení po Havárii jednotku velká Instance HANA jednotky. Přejděte ke kroku 3 Příprava snímků úložiště popsané dříve v tomto článku.
- Zachovat *HANABackupCustomerDetails.txt* pomocí nové HANA instance a testování, zda připojení do úložiště funguje správně.  
- Zastavte nově nainstalovaná instance SAP HANA na jednotce velká Instance HANA v oblasti zotavení po Havárii Azure.
- Odpojit tyto svazky PRD a obraťte se na SAP HANA v Azure Service Management. Svazky nemůže zůstat připojené jednotce, protože nemůže být přístupné současně jako cíl replikace úložiště.  

![Zotavení po Havárii nastavení krok před vytvořením replikace](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Provozní tým vytváří vztah replikace mezi svazky PRD v produkčním prostředí oblasti Azure a PRD svazky v oblasti zotavení po Havárii Azure.

>[!IMPORTANT]
>Svazek /hana/log není replikují, protože to není nutné obnovit replikovaná databáze SAP HANA do konzistentního stavu v lokalitě zotavení po havárii.

V dalším kroku nastavit nebo upravit plán zálohování snímku úložiště, abyste se dostali RTO a RPO v případě havárie. Chcete-li minimalizovat cíl bodu obnovení, nastavte následující intervaly replikace ve službě velká Instance HANA:
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

V případě selhání je také možné obnovit starší snímků úložiště namísto nejnovější snímek úložiště.

## <a name="disaster-recovery-failover-procedure"></a>Postup převzetí služeb při selhání pro zotavení po havárii
Existují dva případy, které je třeba zvážit při převzetí služeb při selhání pro zotavení po Havárii serveru:

- Budete potřebovat databázi SAP HANA se vrátíte do stavu nejnovější data. V takovém případě je samoobslužné skript, pomocí kterého můžete provádět převzetí služeb při selhání, aniž byste museli kontaktovat společnost Microsoft. Ale pro navrácení služeb po obnovení, musíte spolupracovat s Microsoftem.
- Chcete obnovit na snímek úložiště, který není nejnovější replikované snímku. V takovém případě musíte spolupracovat s Microsoftem. 

>[!NOTE]
>Následující kroky je potřeba spustit na jednotce velká Instance HANA, která představuje jednotku zotavení po Havárii. 
 
Pokud chcete obnovit nejnovější replikované úložiště snímků, postupujte následovně: 

1. Vypněte mimo produkční instance HANA na jednotce velkých instancích HANA, který používáte pro zotavení po havárii. Je to proto, že se nachází předinstalovaným neaktivní produkční instance HANA.
1. Ujistěte se, že jsou spuštěny žádné procesy SAP HANA. Použijte následující příkaz k této kontrole: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Výstup by měl zobrazit **hdbdaemon** zpracovat v zastaveném stavu a žádné další procesy HANA ve stavu spuštěná nebo spuštěna.
1. Na jednotce velká Instance HANA zotavení po Havárii serveru, spusťte tento skript *azure_hana_dr_failover.pl*. Skript žádá SAP HANA SID pro obnovení. Při požadavku na, zadejte v jednom nebo pouze SID SAP HANA, replikace, který je spravován v *HANABackupCustomerDetails.txt* souborů na jednotce velká Instance HANA na webu pro zotavení po Havárii. 

      Pokud chcete mít více instancí SAP HANA převzetí služeb při selhání, budete muset několikrát spusťte skript. Při požadavku na, zadejte v SAP HANA SID chcete převzetí služeb při selhání a obnovení. Skript po dokončení se zobrazí seznam přípojných bodů svazků, které jsou přidány do jednotky velká Instance HANA. Tento seznam obsahuje také obnovené svazky zotavení po Havárii.

1. Připojte svazky pro obnovení po havárii obnovené pomocí příkazů operačního systému Linux k jednotce velká Instance HANA v lokalitě zotavení po havárii. 
1. Spuštění neaktivní produkční instance SAP HANA.
1. Pokud jste zvolili pro kopírování protokolů transakcí zálohování protokolů zkrátit čas cíle bodu obnovení, musíte sloučit tyto zálohy transakčního protokolu do adresáře logbackups nově připojené zotavení po Havárii/hana /. Nepřepisujte stávající zálohy. Zkopírujte novější zálohy, které se nezreplikovaly s nejnovější replikace úložiště snímku.
1. Můžete také obnovit jednotlivé soubory mimo snímky, které se replikují do svazku /hana/shared/PRD v oblasti zotavení po Havárii Azure. 

Převzetí služeb při selhání zotavení po Havárii můžete otestovat také bez dopadu na vztah skutečné replikace. Pokud chcete provést testovací převzetí služeb, postupujte podle předchozích kroků 1 a 2 a potom pokračujte v následujícím kroku 3.

>[!IMPORTANT]
>Proveďte *není* spouštět jakékoli transakce produkční instance, kterou jste vytvořili v lokalitě zotavení po Havárii procesem **testování převzetí služeb při selhání** se skriptem zavedené v kroku 3. Tento příkaz vytvoří sadu svazky, které nemají žádný vztah k primární lokalitě. V důsledku toho se synchronizace zpět do primární lokality *není* je to možné. 

Krok 3 pro testovací převzetí služeb při selhání:

Na jednotce velká Instance HANA zotavení po Havárii serveru, spusťte tento skript **azure_hana_test_dr_failover.pl**. Tento skript je *není* zastavuje se tak vztah replikace mezi primární lokalitou a lokalitou zotavení po Havárii. Tento skript je místo, klonování svazků úložiště zotavení po Havárii. Po úspěšném klonování naklonované svazky jsou obnoveny do stavu nejnovější snímek a pak připojena k jednotce zotavení po Havárii. Skript žádá SAP HANA SID pro obnovení. Zadejte jeden nebo pouze SID SAP HANA, replikace, který je spravován v *HANABackupCustomerDetails.txt* souborů na jednotce velká Instance HANA na webu pro zotavení po Havárii. 

Pokud chcete mít více instancí SAP HANA otestovat, budete muset několikrát spusťte skript. Při požadavku na, zadejte v SAP HANA SID instanci, kterou chcete testovat převzetí služeb při selhání. Po dokončení skriptu se seznamem přípojných bodů svazků, které jsou přidány do jednotky velká Instance HANA. Tento seznam obsahuje také naklonované svazky zotavení po Havárii.

Pokračujte ke kroku 4.

   >[!NOTE]
   >Pokud je potřeba převzetí služeb při selhání na web zotavení po Havárii a zachránit nějaká data, která byla odstraněna hodinami a musí být nastaveno na předchozí snímek svazcích zotavení po Havárii, tento postup platí. 

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

1. SAP HANA prohledává umístění zálohování souborů a navrhne nejnovější zálohy protokolu transakcí obnovení. Kontrola může trvat několik minut, dokud jako se zobrazí následující obrazovka: ![seznam zálohy transakčního protokolu pro zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Upravte některé výchozí nastavení:

      - Vymazat **pomocí rozdílových záloh**.
      - Vyberte **inicializovat protokol oblasti**.

   ![Nastavení oblasti inicializace protokolu](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Vyberte **Finish** (Dokončit).

   ![Dokončení obnovení zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Průběh, jako je popsaný tady, by se zobrazit. Mějte na paměti, že v příkladu je obnovení pro zotavení po havárii konfigurace škálování na více instancí SAP HANA třemi uzly.

![Průběh obnovení](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Pokud obnovení zdá se, že přestane reagovat na **Dokončit** obrazovky a nemá zobrazit obrazovka průběhu, ověřte, zda jsou spuštěny všechny instance SAP HANA na pracovních uzlech. V případě potřeby spusťte ručně instance systému SAP HANA.


### <a name="failback-from-a-dr-to-a-production-site"></a>Navrácení služeb po obnovení z zotavení po Havárii pro produkční lokality
Může selhat obnovení z zotavení po Havárii pro produkční lokality. Podívejme se na scénář, ve kterém se způsobilo převzetí služeb při selhání do lokality pro obnovení po havárii problémy v produkčním prostředí oblasti Azure a ne potřebou obnovení ztracených dat. Je už běží vaše produkční úlohy SAP nějakou v lokalitě zotavení po havárii. Jako jsou vyřešené problémy v produkční lokality, chcete po obnovení zpět na svůj provozní server. Vzhledem k tomu, že nemůže dojít ke ztrátě dat, krok zpátky do produkční lokality zahrnuje několik kroků a úzké spolupráci s SAP HANA v Azure provozního týmu. To je na vás k aktivaci provozní tým zahájení synchronizace zpět na pracoviště, jakmile jsou vyřešeny problémy.

Toto je posloupnost kroků:

1. SAP HANA v Azure provozní tým získá aktivační událost synchronizace produkční svazky úložiště ze svazků úložiště obnovení po havárii, které nyní představují provozního stavu. V tomto stavu je vypnout velká Instance HANA jednotku v produkční lokality.
1. SAP HANA v Azure provozní tým sleduje replikace a zajišťuje, že to je zachycena před oznamující.
1. Vypnutí aplikace, které používají produkční HANA Instance v lokalitě zotavení po havárii. Pak provést zálohování protokolu transakcí HANA. V dalším kroku zastavit instanci HANA spuštěné na velká Instance HANA jednotky v lokalitě zotavení po havárii.
1. Po instanci HANA spuštěné v jednotce velká Instance HANA v lokalitě zotavení po havárii je vypnutý, provozní tým ruční synchronizaci diskové svazky.
1. SAP HANA v Azure provozní tým znovu spustí velká Instance HANA jednotka v produkčním a předá ho pro vás. Zajistíte tím, že je ve stavu vypnutí instance SAP HANA v době spuštění jednotky velká Instance HANA.
1. Můžete provést stejný postup obnovení databáze, stejně jako při dříve převzetí služeb při selhání do lokality pro obnovení po havárii.

### <a name="monitor-disaster-recovery-replication"></a>Monitorování replikace pro zotavení po havárii

Stav průběhu replikace úložiště můžete monitorovat spuštěním skriptu `azure_hana_replication_status.pl`. Tento skript musí být spuštěn z jednotky spuštění v umístění pro obnovení po havárii fungovat podle očekávání. Skript funguje bez ohledu na to, zda je replikace aktivní. Skript můžete spustit pro každou jednotku velká Instance HANA vašeho tenanta v umístění pro obnovení po havárii. Nelze získat informace o spouštěcím svazku.

Volání skriptu pomocí tohoto příkazu:
```
./replication_status.pl <HANA SID>
```

Výstup je rozdělena, podle objemu do následujících částí:  

- Stav propojení
- Aktuální aktivity replikace
- Nejnovější snímek replikují 
- Velikost nejnovější snímek
- Aktuální prodleva mezi snímky (mezi poslední dokončené snímku replikace a teď)

Stav odkazu zobrazuje jako **aktivní** Pokud propojení mezi umístěními je mimo provoz nebo události aktuálně probíhající převzetí služeb při selhání. Aktivity replikace řeší, zda je v tuto chvíli nereplikují žádná data, nebo je v nečinnosti, nebo pokud ostatní aktivity se právě děje na odkaz. Poslední snímky replikovaných objevit pouze jako `snapmirror…`. Velikost poslední snímek se následně zobrazí. Nakonec se zobrazí prodlevu. Po dokončení replikace prodleva představuje čas z naplánované replikaci do. I v případě, že má spustit replikaci, může být větší než hodinu pro replikaci dat, zejména v počáteční replikaci se prodlevou. Časové zpoždění i nadále zvyšovat, dokud probíhající replikaci.

Následuje příklad výstupu:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












