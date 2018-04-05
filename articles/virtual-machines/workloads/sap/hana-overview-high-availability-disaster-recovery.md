---
title: Vysoká dostupnost a zotavení po havárii systému SAP HANA v Azure (velké instance) | Microsoft Docs
description: Vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: timlt
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/01/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6c939e0fb59c7fce2c1c34aca1b77bd0b8cec0c5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Velké instance SAP HANA vysoké dostupnosti a zotavení po havárii v Azure 

>[!IMPORTANT]
>Tato dokumentace žádná náhrada není dostupná dokumentace správy SAP HANA nebo SAP poznámky. Očekává se, zda má čtečka solidní znalosti a odborných znalosti ve SAP HANA Správa a provoz, zejména s tématy zálohování, obnovení, vysokou dostupnost a zotavení po havárii. V této dokumentaci jsou uvedeny snímky obrazovky ze SAP HANA Studio. Obsah, struktura a povaha obrazovky SAP nástroje pro správu a nástroje, sami může změnit z SAP HANA verze na verzi.

Je důležité věnovat kroky a procesy prováděné ve vašem prostředí a s HANA verze a verze. Některé procesy popsané v této dokumentaci jsou jednodušší pro lepší porozumění obecné a nejsou určen pro použití jako podrobné pokyny pro případné operace příruček. Pokud chcete vytvořit operaci příruček pro vaše konfigurace, musíte pro testování a prověření procesů a procesů, související s konkrétní konfigurace. 


Vysoká dostupnost a zotavení po havárii (DR) jsou klíčové aspekty vašeho důležité SAP HANA spuštěných na serveru Azure (velké instance). Je důležité pro práci s SAP, vaše systémový Integrátor nebo Microsoft správně architektury a implementovat správné vysokou dostupnost a strategie zotavení po havárii. Také je potřeba vzít v úvahu plánovaného bodu obnovení (RPO) a cíli času obnovení, které jsou specifické pro vaše prostředí.

Společnost Microsoft podporuje některé funkce vysoké dostupnosti SAP HANA velké instancemi HANA. Tyto funkce patří:

- **Replikace úložiště**: schopnost systému úložiště replikovat všechna data do jiné Instance velké HANA razítko v jiné oblasti Azure. SAP HANA funguje nezávisle na tuto metodu. Tato funkce je výchozí mechanismus pro obnovení po havárii, které je poskytovat velké instance HANA.
- **Replikace systému HANA**: [replikace všech dat v SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) na samostatném systému SAP HANA. Plánovanou dobu obnovení je minimalizován prostřednictvím replikace dat v pravidelných intervalech. SAP HANA podporuje asynchronní, synchronní režimy v paměti a synchronní. Synchronním režimu se používá pouze pro SAP HANA systémy, které jsou ve stejném datovém centru nebo méně než 100 km od sebe. V aktuální návrhu HANA velké Instance razítka replikaci systému HANA lze pro zajištění vysoké dostupnosti v rámci pouze jedna oblast. Replikace systému HANA vyžaduje reverzní proxy server jiného výrobce nebo směrování součást pro konfigurace zotavení po havárii do jiné oblasti Azure. 
- **Automatické převzetí služeb při selhání hostitele**: místní selhání obnovení řešení pro SAP HANA, který představuje alternativu k replikaci HANA systému. Pokud hlavní uzel nebude k dispozici, můžete nakonfigurovat jeden nebo více uzlů SAP HANA pohotovostní v režimu Škálováním na více systémů, SAP HANA automaticky převezme do pohotovostního uzlu.

SAP HANA v Azure (velké instance) je k dispozici v dvou oblastech Azure v tři geopolitické oblasti (USA, Austrálie a Evropě). Dva oblastem v geopolitické oblasti, které jsou hostiteli HANA velké Instance razítka jsou připojené k okruhy samostatné vyhrazenou síť. Ty se používají pro replikaci snímků úložiště k poskytování metody obnovení po havárii. Replikace nebyl určen ve výchozím nastavení, ale není nastaven pro zákazníky, kteří pořadí funkci obnovení po havárii. Replikace úložiště je závislá na využití úložiště snímků pro velké instance HANA. Není možné vybrat jako oblasti zotavení po Havárii, která je v jiné oblasti geopolitické oblasti Azure. 

V následující tabulce jsou uvedeny aktuálně podporované vysokou dostupnost a po havárii obnovení metody a kombinace:

| Scénář v HANA velké instancí | Jako možnost vysoké dostupnosti | Možnost obnovení po havárii | Komentáře |
| --- | --- | --- | --- |
| Jeden uzel | Není k dispozici. | Instalační program vyhrazené zotavení po Havárii.<br /> Instalační program Multipurpose zotavení po Havárii. | |
| Automatické převzetí služeb při selhání hostitele: N + m<br /> včetně 1 + 1 | Chcete-li to možné s v úsporném režimu trvá aktivní roli.<br /> HANA řídí přepínač role. | Instalační program vyhrazené zotavení po Havárii.<br /> Instalační program Multipurpose zotavení po Havárii.<br /> Zotavení po Havárii synchronizace pomocí replikace úložiště. | Sady svazků HANA jsou připojené ke všem uzlům (n + m).<br /> Zotavení po Havárii lokalita musí mít stejný počet uzlů. |
| Replikace systému HANA | Chcete-li to možné primární nebo sekundární instalačního programu.<br /> Sekundární přesune do primární role v případě převzetí služeb při selhání.<br /> Převzetí služeb při selhání řízení HANA systému replikace a operačního systému. | Instalační program vyhrazené zotavení po Havárii.<br /> Instalační program Multipurpose zotavení po Havárii.<br /> Zotavení po Havárii synchronizace pomocí replikace úložiště.<br /> Zotavení po Havárii pomocí replikace systému HANA ještě není možné bez komponenty jiných výrobců. | Samostatnou sadu diskové svazky jsou připojené do každého uzlu.<br /> Pouze diskové svazky sekundární replika v produkční lokality replikovaly do umístění, zotavení po Havárii.<br /> Na webu zotavení po Havárii je požadován jednu sadu svazky. | 

Instalace s vyhrazenou zotavení po Havárii je, kde není jednotka HANA velké Instance v lokalitě zotavení po Havárii používaný ke spuštění jakékoli úlohy nebo testovacím systému. Jednotka je pasivní a nasazuje pouze v případě, že je provést převzetí služeb po havárii. Tento instalační program, když není upřednostňovaný volba pro mnoho zákazníků.

> [!NOTE]
> [Nasazení SAP HANA MCOD](https://launchpad.support.sap.com/#/notes/1681092) (více instancí HANA na jednu jednotku) jako překrytí práce scénáře s vysokou DOSTUPNOSTÍ a zotavení po Havárii metody uvedené v tabulce. Výjimkou je použití replikace systému HANA pomocí clusteru služby automatické převzetí služeb při selhání podle kardiostimulátor. Takovém případě podporuje pouze jednu instanci HANA na jednotku. Pro [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) nasazení, pouze nezaložené úložiště HA a zotavení po Havárii metody fungovat, pokud je nasazený víc než jednoho klienta. Nasazení jednoho klienta jsou platné všechny metody uvedené.  

Víceúčelové instalace zotavení po Havárii je, kde je spuštěna Instance HANA velké jednotky na webu zotavení po Havárii mimo produkční zatížení. V případě havárie, vypnutí systému mimo produkční připojit sady replikované úložiště (Další) svazek a pak spusťte instance HANA produkční. Většina zákazníkům, kteří používají funkci obnovení po havárii HANA velké Instance pomocí této konfigurace. 


Další informace o vysoké dostupnosti SAP HANA najdete v těchto článcích SAP: 

- [SAP HANA vysokou dostupnost dokument White Paper](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Příručka pro správu SAP HANA](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [SAP HANA Academy Video o replikaci systému SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP podporu Poznámka #1999880 – nejčastější dotazy na replikaci systému SAP HANA](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP podporu Poznámka #2165547 – SAP HANA zpět nahoru a obnovení v rámci prostředí replikace systému SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [Pro Exchange hardwaru minimální nebo nula. výpadků SAP podporu Poznámka #1984882 – pomocí replikace systému SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Aspekty sítě pro zotavení po havárii s instancemi velké HANA

Abyste mohli využívat funkci obnovení po havárii HANA velké instancí, budete muset navrhnout síťové připojení k dvou oblastech Azure. Budete potřebovat připojení okruh Azure ExpressRoute z místně v vaši hlavní oblast Azure a další okruh připojení z místního pro vaši oblast pro obnovení po havárii. Tato míra vztahuje na situaci, kdy dojde k problému v oblasti Azure, včetně umístění Microsoft Enterprise Edge směrovač (MSEE).

Jako druhá míra můžete propojit virtuální sítě Azure, která se připojují k SAP HANA v Azure (velké instance) v jedné oblasti s okruhem ExpressRoute, který se připojuje HANA velké instance v jiné oblasti. Pomocí této *křížové připojení*, služby spuštěné v Azure virtuální síť v oblast 1 můžete připojit k instanci HANA velké jednotky v oblasti 2 a naopak. Tato míra řeší případ, ve kterém jenom jeden MSEE umístění, která se připojuje k vaší místní umístění pomocí Azure přejde do režimu offline.

Odolné konfigurace případech zotavení po havárii je znázorněný na následujícím obrázku:

![Optimální konfigurace pro zotavení po havárii](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>Další požadavky s replikací úložiště HANA velké instance pro zotavení po havárii

Kromě předchozích požadavků instalace služby obnovení po havárii s instancemi velké HANA musíte:

- Pořadí SAP HANA na Azure (velké instance) SKU stejnou velikost jako provozním SKU a nasadit je v oblasti obnovení po havárii. V aktuální zákaznických nasazení tyto instance slouží ke spuštění instancí HANA nevýrobní prostředí. Tyto konfigurace se označují jako *víceúčelových zotavení po Havárii nastavení*.   
- Pořadí další úložiště na webu zotavení po Havárii pro každou z vaší SAP HANA na SKU Azure (velké instance), které chcete obnovit v lokalitě pro obnovení po havárii. Nákup dalšího úložiště umožňuje přidělit svazky úložiště. Svazky, které jsou cílem replikace úložiště z provozním oblasti Azure do zotavení po havárii oblast Azure, kterou můžete přidělit.

 

## <a name="backup-and-restore"></a>Zálohování a obnovení

Jedním z nejdůležitějších aspektů do provozní databáze je je chránit před závažné události. Příčinu tyto události může být cokoli z přírodní katastrofy chyby jednoduché uživatele.

Zálohování databáze, možnost obnovit do libovolného bodu v čase (například před odstraněním někdo důležitá data), umožňuje obnovení stavu, který je co nejblíže ke byl před přerušení.

Pro dosažení co nejlepších výsledků se musí provádět dva typy zálohování:

- K zálohování databáze: úplná, přírůstkové nebo rozdílové zálohy
- Zálohování transakčního protokolu

Kromě zálohování databáze úplné provést na úrovni aplikace může provést zálohování pomocí snímků úložiště. Úložiště snímků nenahrazují zálohování transakčního protokolu. Zálohování transakčního protokolu zůstat důležité obnovit databázi do určité míry v čase nebo prázdný protokoly z již potvrzené transakce. Úložiště snímků však mohou urychlit obnovení rychle díky dopředné bitové kopie databáze. 

SAP HANA v Azure (velké instance) nabízí dvě možnosti pro zálohování a obnovení:

- Samoobslužné (DIY). Po můžete vypočítat zkontrolujte, zda že je dostatek místa na disku, proveďte úplné databáze a záloh protokolu pomocí jedné z následujících metod zálohování disku. Můžete zálohovat buď přímo na svazky připojené jednotky HANA velké instanci, nebo do sdílené složky NFS (Network File), se nastavují v Azure virtuální počítač (VM). V takovém případě zákazníků nastavení virtuálního počítače s Linuxem v Azure, připojte k virtuálnímu počítači Azure Storage a sdílené složky úložiště prostřednictvím nakonfigurovaný server systému souborů NFS v tohoto virtuálního počítače. Pokud provádíte zálohování pro svazky, které přímo připojit k instanci HANA velké jednotky, budete muset zkopírovat zálohování k účtu úložiště Azure (po nastavení virtuálního počítače Azure, který exportuje sdílených složek NFS, které jsou založeny na Azure Storage). Můžete také použít záložním trezoru Azure nebo Azure studené úložiště. 

   Další možností je použít nástroj ochrany dat třetích stran a uložte zálohy po se kopírují do účtu úložiště Azure. DIY možnost zálohování může být také nutné pro data, která je potřeba uložit na delší dobu pro dodržování předpisů a auditování. Ve všech případech zálohování se zkopírují do sdílené složky NFS reprezentované prostřednictvím virtuálních počítačů a úložiště Azure.

- Infrastruktura zálohování a obnovení funkce. Můžete také použít zálohování a obnovení funkce, která poskytuje základní infrastruktura SAP HANA v Azure (velké instance). Tato možnost splňuje potřeby zálohování a rychlé obnovení. Zbývající část tohoto oddílu řeší funkce zálohování a obnovení, která se nabízí velké instancemi HANA. Tato část také obsahuje vztah zálohování a obnovení po havárii je funkci obnovení, které nabízí velké instance HANA.

>   [!NOTE]
>   Snímek technologie, která se používá základní infrastruktura velké instancí HANA má závislost na snímky SAP HANA. V tomto okamžiku SAP HANA snímky nefungují ve spojení s více klienty kontejnerů víceklientské databázi SAP HANA. Pokud jenom jednoho klienta je nasazená, fungují SAP HANA snímky a tuto metodu můžete použít.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Pomocí snímků úložiště SAP HANA v Azure (velké instance)

Infrastrukturu úložiště základní SAP HANA v Azure (velké instance) podporuje úložiště snímků svazků. Zálohování a obnovení svazků se podporuje, s následující aspekty:

- Místo databáze úplné zálohy snímků svazků úložiště přesměrováni na základě časté.
- Při spouštění snímku přes /hana/data a /hana/shared (zahrnuje /usr/sap) svazky, technologie snímku zahájí SAP HANA snímek před spuštěním snímku úložiště. Tento snímek SAP HANA je bod instalační program pro případné protokolu obnovení po obnovení snímku úložiště.
- Po snímku úložiště byl úspěšně proveden, je odstraněn snímek SAP HANA.
- Zálohování transakčního protokolu jsou často provést a jsou uložené ve svazku /hana/logbackups nebo v Azure. Můžete aktivovat /hana/logbackups svazku, který obsahuje zálohy protokolu transakcí, které chcete pořízení snímku samostatně. V takovém případě není potřeba provést HANA snímku.
- Pokud je nutné obnovit databázi do určité míry v čase, žádosti o tuto podporu Microsoft Azure (pro produkční výpadek) nebo SAP HANA na Azure Service Management obnovení do snímku určité úložiště. Příkladem je plánované obnovení systému izolovaného prostoru do původního stavu.
- SAP HANA snímek, který je součástí úložiště snímků je bod posunutí pro použití zálohování transakčního protokolu, které byly provedeny a uložené po pořízení snímku úložiště.
- Tyto zálohy protokolu transakcí se provádějí k obnovení databázi zpět do určité míry v čase.

Můžete provést úložiště snímků cílení na tři třídy svazků:

- Kombinovaná snímek nad/hana/daty a /hana/shared (zahrnuje/usr/sap). Tento snímek vyžaduje vytvoření snímku SAP HANA jako příprava pro vytvoření snímku úložiště. Snímek SAP HANA zajišťuje, že databáze je v konzistentním stavu z hlediska úložiště. A že pro obnovení zpracování tedy bod nastavit až na.
- Samostatné snímek přes/hana/logbackups.
- Oddíl s operačním systémem.


### <a name="storage-snapshot-considerations"></a>Aspekty volby úložiště snímků

>[!NOTE]
>Úložiště snímků využívat prostor úložiště, který byl přidělen do Instance HANA velké jednotky. Je třeba zvážit následující aspekty plánování úložiště snímků a kolik úložiště snímků chcete zachovat. 

Konkrétní mechanismů úložiště snímků pro SAP HANA v Azure (velké instance) patří:

- Konkrétní úložiště snímků (v bodě v čase, kdy se provede) využívá malé úložiště.
- Jako obsahu změny dat a obsah SAP HANA data, která soubory změnit na svazek úložiště je potřeba ukládat původní obsah bloku, jakož i změny dat snímku.
- V důsledku toho snímku úložiště nárůstu velikosti. Čím delší snímku existuje, tím větší bude snímku úložiště.
- Další změny, které jsou vytvářeny svazku databáze SAP HANA průběhu životnosti snímku úložiště, tím větší spotřebu prostor úložiště snímků.

SAP HANA v Azure (velké instance) se dodává s pevný svazek velikosti pro svazky protokolu a data SAP HANA. Provádění snímky tyto svazky eats do místo ve svazku. Budete muset určit, kdy se při plánování úložiště snímků. Musíte taky monitorovat využívání místa svazků úložiště, a také spravovat počet snímků, které jsou uloženy. Úložiště snímků můžete zakázat při importu hmotností dat nebo provést jiné významné změny databáze HANA. 


Následující části obsahují informace pro provedení tyto snímky, včetně obecná doporučení:

- I když hardware tolerovat 255 snímků na svazku, budete chtít zůstat dobře pod tuto hodnotu.
- Před provedením úložiště snímků, monitorování a sledování volného místa.
- Nižší počet snímků úložiště podle volného místa. Můžete snížit počet snímků, které můžete zachovat, nebo můžete rozšířit svazky. Další úložiště můžete uspořádat v jednotkách 1 terabajt.
- Během aktivity, například přesun dat do SAP HANA s nástroji pro migraci platformy SAP (R3load) nebo při obnovování databáze SAP HANA ze zálohy zakažte úložiště snímků na /hana/data svazku. 
- Během větší uspořádání SAP HANA tabulek úložiště snímků je nutno, pokud je to možné.
- Úložiště snímků jsou předpokladem pro využívat výhod po havárii možnosti obnovení SAP HANA v Azure (velké instance).

### <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Předpoklady pro pomocí samoobslužné služby úložiště snímků

Aby bylo zajištěno, že se snímek skript spustí úspěšně, ujistěte se, že Perl je nainstalován na operační systém Linux na HANA velké instance serveru. Perl obsahuje předem nainstalovaná na vaší Instance HANA velké jednotky. Pokud chcete zkontrolovat verzi Perl, použijte následující příkaz:

`perl -v`

![Veřejný klíč se zkopíruje spuštěním tohoto příkazu](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


### <a name="set-up-storage-snapshots"></a>Nastavení úložiště snímků

Nastavení úložiště snímků velké instancemi HANA, postupujte takto:
1. Ujistěte se, zda Perl je nainstalován na operační systém Linux na HANA velké instance serveru.
2. Upravit/etc/ssh/ssh\_konfigurace přidat řádek _Mac hmac-sha1_.
3. Vytvoření účtu SAP HANA zálohování uživatele na hlavní uzel pro každou instanci SAP HANA, kterou používáte, pokud je k dispozici.
4. Nainstalujte klienta SAP HANA HDB ve všech serverech velké instance SAP HANA.
5. Na prvním serveru SAP HANA velké instancí každé oblasti vytvořte veřejný klíč pro přístup k podkladové infrastruktury úložiště, který řídí vytváření snímku.
6. Zkopírujte skripty a konfigurační soubor z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts) umístění **hdbsql** v instalaci SAP HANA.
7. Změnit *HANABackupDetails.txt* souboru podle potřeby pro specifikace odpovídající zákazníka.

### <a name="consideration-for-mcod-scenarios"></a>Aspekt MCOD scénáře
Pokud používáte [MCOD scénář](https://launchpad.support.sap.com/#/notes/1681092) s více instancemi SAP HANA na jednu jednotku HANA velké Instance, máte zřízené pro každou z instance SAP HANA svazky samostatné úložiště. V aktuální verzi snímku samoobslužné služby automation nelze zahájit samostatné snímky v každé HANA instance systému ID (SID). Funkce nabízí kontroluje registrovaných instancí SAP HANA serveru v konfiguračním souboru (viz dále v tomto článku) a spouští souběžné snímků svazků všech instancí registrované na jednotce.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Instalace klienta SAP HANA HDB

Operační systém Linux nainstalován na SAP HANA v Azure (velké instance) obsahuje složky a skripty potřebné k provedení SAP HANA úložiště snímků pro zálohování a po havárii pro účely obnovení. Zkontrolujte novější verze v [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts). Nejnovější verzi z skriptů je 3.x. Jiné skripty může mít různé vedlejší verze v rámci stejná hlavní verze.

>[!IMPORTANT]
>Při přesouvání z verze 2.1 verzi 3.0 skripty, Všimněte si, že došlo ke změně struktury konfiguračního souboru a některé syntaxe. Zobrazit popisky v určité části. 

Je vaší povinností k instalaci klienta SAP HANA HDB na jednotkách HANA velké instanci při instalaci SAP HANA.

### <a name="step-2-change-the-etcsshsshconfig"></a>Krok 2: Změnit/etc/ssh/ssh\_konfigurace

Změna `/etc/ssh/ssh_config` přidáním _Mac hmac-sha1_ řádek, jak je vidět tady:
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

### <a name="step-3-create-a-public-key"></a>Krok 3: Vytvoření veřejný klíč

Povolit přístup k rozhraní úložiště snímků klienta služby HANA velké Instance, budete muset vytvořit proceduru přihlášení prostřednictvím veřejného klíče. Na první SAP HANA na serveru Azure (velké instance) ve vašem klientovi vytvořte veřejný klíč, který se má použít pro přístup k infrastruktuře úložiště. Veřejný klíč zajistí, že pro přihlášení k rozhraní úložiště snímku není vyžadováno heslo. Vytvoření veřejný klíč také znamená, že není potřeba spravovat hesla pověření. V systému Linux na SAP HANA velké instance serveru spusťte následující příkaz k vygenerování veřejný klíč:
```
  ssh-keygen –t dsa –b 1024
```
Nové umístění je **_/root/.ssh/id\_dsa.pub**. Nezadávejte vlastní heslo, jinak je nutné zadat heslo při každém přihlášení. Místo toho vyberte **Enter** dvakrát možnosti odeberete požadavek "Zadejte heslo" pro přihlášení.

Ujistěte se, že veřejný klíč byl opraven podle očekávání změnou složky, které mají **/root/.ssh/** a pak provádění `ls` příkaz. Pokud je klíč přítomen, můžete ji zkopírovat tak, že spustíte následující příkaz:

![Veřejný klíč se zkopíruje spuštěním tohoto příkazu](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

V tomto okamžiku obraťte SAP HANA na Azure Service Management a poskytnout veřejný klíč. Zástupce služeb používá veřejný klíč a zaregistrujte ho v základní infrastrukturu úložiště, která je ubírat pro vašeho klienta HANA velké Instance.

### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Vytvoření účtu uživatele SAP HANA

Zahájíte vytváření snímků SAP HANA musíte vytvořit uživatelský účet v SAP HANA, který můžete použít skripty snímku úložiště. Pro tento účel vytvořte uživatelský účet SAP HANA studia SAP HANA. Uživatel musí být vytvořeny pod SYSTEMDB a není v databázi SID. Tento účet musí mít následující oprávnění: **správce zálohování** a **katalogu čtení**. V tomto příkladu je uživatelské jméno **SCADMIN**. Název uživatelského účtu v HANA Studio vytvořili rozlišuje velká a malá písmena. Je nutné vybrat **ne** pro vyžádání uživatelům změnit heslo na jeho příštím přihlášení.

![Vytvoření uživatele v HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

Pokud používáte MCOD nasazení s více instancemi SAP HANA na jednu jednotku, budete muset tento krok opakujte pro všechny instance SAP HANA.

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: Autorizace uživatelský účet SAP HANA

V tomto kroku autorizovat SAP HANA uživatelský účet, který jste vytvořili, takže skripty nemusíte odeslání hesla za běhu. Příkaz SAP HANA `hdbuserstore` umožňuje vytváření SAP HANA uživatelský klíč, který je uložený na jeden nebo více uzlů SAP HANA. Uživatelský klíč umožňuje uživateli přístup SAP HANA bez nutnosti Správa hesel z v rámci procesu skriptování. Proces skriptování je popsána dále v tomto článku.

>[!IMPORTANT]
>Spusťte následující příkaz jako `root`. Skript, jinak hodnota nemůže pracovat správně.

Zadejte `hdbuserstore` příkaz takto:

**Pro instalaci MDC HANA**
```
hdbuserstore set <key> <host>:<3[instance]15> <user> <password>
```

**Pro instalaci MDC HANA**
```
hdbuserstore set <key> <host>:<3[instance]13> <user> <password>
```

V následujícím příkladu je uživatel **SCADMIN01**, název hostitele je **lhanad01**, a číslo instance se **01**:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Pokud použijete k nasazení HANA MCOD s více instancí SAP HANA na jednu jednotku, budete muset krok opakujte pro všechny instance SAP HANA a příslušné zálohování uživatele na jednotce.

Pokud máte SAP HANA konfiguraci škálovaného na více systémů, budete muset spravovat všechny skriptování z jednoho serveru. V tomto příkladu klíč SAP HANA **SCADMIN01** musí být změněna pro každého hostitele způsobem, který ukazuje hostitele, který souvisí se klíč. Změnit účet záloh SAP HANA číslem instance databáze HANA. Klíč musí mít oprávnění správce na hostiteli, ke které je přiřazen, a zálohování uživatele pro konfigurace Škálováním na více systémů, musíte mít přístupová práva na všechny instance SAP HANA. Za předpokladu, že tři uzly Škálováním na více systémů mít názvy **lhanad01**, **lhanad02**, a **lhanad03**, sekvence příkazů vypadá takto:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad02:30115 SCADMIN <password>
hdbuserstore set SCADMIN01 lhanad03:30115 SCADMIN <password>
```

### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: Získání snímku skripty, konfigurace snímků a testování v konfiguraci a připojení

Stáhněte si nejnovější verzi skripty z [Githubu](https://github.com/Azure/hana-large-instances-self-service-scripts). Zkopírujte stažený skripty a textový soubor do pracovní adresář pro **hdbsql**. Pro aktuální HANA instalace, je tento adresář v následujícím formátu: /hana/shared/D01/exe/linuxx86\_64/hdb. 
``` 
azure_hana_backup.pl 
azure_hana_replication_status.pl 
azure_hana_snapshot_details.pl 
azure_hana_snapshot_delete.pl 
testHANAConnection.pl 
testStorageSnapshotConnection.pl 
removeTestStorageSnapshot.pl
azure_hana_dr_failover.pl
azure_hana_dr_failover.pl 
HANABackupCustomerDetails.txt 
``` 

Při plánování práce s tyto skripty: 

- Pokud pokynů Microsoft Operations nikdy upravit skripty.
- Dotaz, k úpravě skriptu nebo soubor parametru a vždy používejte Linux textového editoru, například "vi", není systému Windows editor, například Poznámkový blok. Pomocí editoru Windows může dojít k poškození formát souboru.
- Vždy používejte nejnovější skripty. Nejnovější verzi můžete stáhnout z webu GitHub.
- Použijte stejnou verzi nástroje skriptů ve povahu.
- Testování skriptů a mít možnost se o parametry vyžadované a výstup skriptu před použitím je přímo v systému výroby.
- Neměnit název přípojného bodu serveru vytváří Microsoft Operations. Tyto skripty se spoléhají na tyto standardní přípojné body, aby byly dostupné pro úspěšné provedení.


Účelem jiné skripty a soubory vypadá takto:

- **Azure\_hana\_backup.pl**: Tento skript je naplánován s nástroj plánování procesu Cron Linux ke spuštění úložiště snímků na HANA data a sdílené svazky, svazek logbackups/hana/nebo operačního systému.
- **Azure\_hana\_replikace\_status.pl**: Tento skript poskytuje základní podrobnosti kolem stav replikace z pracoviště na lokalitě pro obnovení po havárii. Monitorování skriptu a zajistit, že replikace probíhá, a zobrazuje velikost položek, které jsou právě replikován. Také obsahuje pokyny, pokud replikace trvá příliš dlouho, nebo pokud na odkaz je vypnutý.
- **Azure\_hana\_snímku\_details.pl**: Tento skript obsahuje seznam základní podrobnosti o všechny snímky, na jeden svazek, které existují ve vašem prostředí. Tento skript spustíte na primárním serveru nebo na jednotce serveru v umístění pro obnovení po havárii. Skript obsahuje následující informace, členěné podle každý svazek, který obsahuje snímky:
   * Velikost celkový počet snímků na svazku
   * Následující podrobnosti v jednotlivých snímků v tomto svazku: 
      - Název snímku 
      - Vytvořit čas 
      - Velikost snímku
      - Frekvence snímků
      - ID zálohování HANA přidružené tento snímek, pokud je to důležité
- **Azure\_hana\_snímku\_delete.pl**: Tento skript odstraní snímku úložiště nebo u sady snímků. Můžete buď zálohování ID SAP HANA jak se nachází v HANA Studio, nebo název snímku úložiště. V současné době zálohování ID je vázaný jenom na snímky vytvořené pro HANA data/log/sdílených svazků. Jinak Pokud je zadaný Identifikátor snímku, usiluje všechny snímky, které odpovídají zadané snímku ID.  
- **testHANAConnection.pl**: Tento skript Otestuje připojení k instanci SAP HANA a je nutný k nastavení úložiště snímků.
- **testStorageSnapshotConnection.pl**: Tento skript má dva účely. Nejprve zajišťuje, že velké Instance HANA jednotka, ve které spouští skripty má přístup k přiřazené úložiště virtuálního počítače a rozhraní úložiště snímků instancí HANA velké. Druhým účelem je vytvořit dočasný snímků pro HANA instance, kterou zkoušíte. Tento skript by měl spustit pro všechny instance HANA na serveru, aby zálohování skripty fungovat podle očekávání.
- **removeTestStorageSnapshot.pl**: Tento skript odstraní testovací snímek vytvořen pomocí skriptu **testStorageSnapshotConnection.pl**.
- **Azure\_hana\_zotavení po havárii\_failover.pl**: Tento skript spustí selhání zotavení po Havárii do jiné oblasti. Skript je třeba provést na jednotce HANA velké Instance v oblasti zotavení po Havárii, nebo na jednotce chcete převzetí služeb při selhání. Tento skript ukončen úložiště replikace z primární straně na sekundární stranu, obnoví nejnovější snímku na svazcích, zotavení po Havárii a přípojné body pro DR svazky.
- **Azure\_hana\_testování\_zotavení po havárii\_failover.pl**: Tento skript provede testovací převzetí služeb do lokality zotavení po Havárii. Na rozdíl od skript azure_hana_dr_failover.pl spuštění tohoto přerušit úložiště replikace z primárního na sekundární. Místo toho klony svazky replikované úložiště jsou vytvořené na straně zotavení po Havárii a přípojné body klonovaný svazky jsou k dispozici. 
- **HANABackupCustomerDetails.txt**: Tento soubor je upravitelnými konfiguračního souboru, který budete muset upravit přizpůsobit konfiguraci SAP HANA. *HANABackupCustomerDetails.txt* soubor je soubor řízení a konfigurace pro skript, který běží úložiště snímků. Upravte soubor pro účely a instalační program. Zobrazí **název zálohy úložiště** a **úložiště IP adresu** ze SAP HANA na Azure Service Management při nasazení vaší instance. Pořadí, nelze změnit, řazení, nebo mezer všech proměnných v tomto souboru. Pokud tak učiníte, skripty nebudou pracovat správně. Kromě toho obdržíte adresu IP hlavní uzel nebo uzel škálování (Pokud Škálováním na více systémů) ze SAP HANA na Azure Service Management. Víte také číslo HANA instance, který získáte při instalaci SAP HANA. Teď je potřeba přidat název zálohy do konfiguračního souboru.

Pro nasazení škálování nebo Škálováním na více systémů konfigurační soubor vypadat jako v následujícím příkladu, po zadání názvu serveru jednotka HANA velké Instance a IP adresu serveru. Pokud používáte SAP HANA systému replikace, použijte virtuální IP adresu konfigurace replikace systému HANA. Vyplňte všechna potřebná pole pro každý SAP HANA SID, které chcete zálohovat nebo obnovit.

Můžete také Zakomentovat řádky instancí, které nechcete použít k zálohování dobu přidáním "#" před povinné pole. Nepotřebujete také zadejte všechny instance SAP HANA, které jsou obsaženy na serveru, pokud není nutné zálohovat nebo obnovit tuto konkrétní instanci. Formát je potřeba uchovat pro všechna pole, nebo všechny skripty throw chybovou zprávu a ukončuje skript. Další požadované řádky žádné SID informace, které nepoužíváte po poslední instance SAP HANA používá, můžete odstranit. Všechny řádky musí být buď vyplněno, označeno jako komentář nebo odstranit.

>[!IMPORTANT]
>Struktura souboru změnit s přechodem z verze 2.1 na verze 3.0. Pokud chcete použít skripty verze 3.0, budete muset přizpůsobit Struktura konfiguračního souboru. 


```
HANA Server Name: testing01
HANA Server IP Address: 172.18.18.50
```

Pro každou instanci, která nakonfigurujete na jednotce HANA velké Instance nebo pro škálovatelnou konfiguraci musíte definovat data následujícím způsobem:

    
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
Pro škálování a konfigurace replikace systému HANA opakujte tuto konfiguraci v každém uzlu. Toto opatření zajišťuje, že v případě selhání zálohování a případné úložiště replikace pokračovat v práci.   

Po uvedení všech konfiguračních dat do *HANABackupCustomerDetails.txt* soubor, zkontrolujte, zda jsou pro HANA instance data správná konfigurace. Pomocí tohoto skriptu `testHANAConnection.pl`, která je nezávislá konfigurace aplikace SAP HANA škálování nebo Škálováním na více systémů.

```
testHANAConnection.pl
```

Pokud máte SAP HANA škálovatelnou konfiguraci, zajistěte, aby hlavní instance HANA přístup k požadované HANA servery a instancí. Neexistují žádné parametry testu skriptu, ale je nutné přidat data do *HANABackupCustomerDetails.txt* konfiguračního souboru pro skript, který chcete spustit správně. Jenom kódy chyb prostředí příkaz se vrátí, takže není možné pro skript, který chcete chyby, zkontrolujte všechny instance. I tak skript zadejte některé užitečné komentáře můžete znovu zkontrolovat.

Chcete-li spustit skript, zadejte následující příkaz:
```
 ./testHANAConnection.pl
```
Pokud skript úspěšně získá stav instance HANA, zobrazí se zpráva, že HANA připojení bylo úspěšné.


Dalším krokem testovací je zkontrolujte připojení k úložišti na základě dat vložíte do *HANABackupCustomerDetails.txt* konfigurace souboru a potom spusťte test snímku. Před spuštěním `azure_hana_backup.pl` skriptu, je nutné spustit tento test. Pokud svazek obsahuje žádné snímky, není možné určit, zda svazek je prázdný nebo pokud dojde selhání SSH k získání podrobných informací o snímek. Z tohoto důvodu se skript spustí dva kroky:

- Ověří, jestli jsou dostupné pro skripty k provedení snímků úložiště virtuálního počítače a rozhraní klienta.
- Vytvoří snímek testu nebo fiktivní, pro každý svazek HANA instance.

Z tohoto důvodu je zahrnuta jako argument HANA instance. Pokud se nezdaří spuštění, není možné zajistit kontrolu chyb pro připojení k úložišti. I v případě, že se nezobrazí žádná chyba kontrola, skript poskytuje užitečné pomocné parametry.

1. Spuštění řady příkazů k provedení tento test:

   ```
   ssh <StorageUserName>@<StorageIP>
   ```

   Uživatelské jméno úložiště a IP adresu úložiště byly zadány pro vás na předání Instance HANA velké jednotky.

2. Spusťte skript testu:
   ```
    ./testStorageSnapshotConnection.pl <HANA SID>
   ```

Skript se pokusí přihlásit k úložišti pomocí veřejný klíč zadaný v předchozích krocích instalační program a s daty, které jsou nakonfigurované v *HANABackupCustomerDetails.txt* souboru. Pokud přihlášení je úspěšné, zobrazí se následující obsah:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Pokud dojde k problémům s připojením ke konzole úložiště, výstup vypadá takto:

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

Po úspěšného přihlášení k rozhraní úložiště virtuálních počítačů skript pokračuje fáze 2 a vytvoří snímek testu. Pro konfigurací Škálováním na více systémů tři uzly SAP HANA jsou zde uvedeny výstup:

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

Pokud snímek test byl úspěšně proveden pomocí skriptu, můžete pokračovat konfigurace skutečné úložiště snímků. Pokud není úspěšné, prozkoumejte problémy než budete pokračovat dál. Test snímku by měl zůstávají kolem, dokud se provádějí první skutečné snímky.


### <a name="step-7-perform-snapshots"></a>Krok 7: Provedení snímků

Po dokončení přípravné kroky, můžete začít konfigurovat konfiguraci skutečné úložiště snímků. Skript, který chcete naplánovat funguje s konfigurací škálování zatížení a škálování SAP HANA. Pro pravidelné a běžné provádění skriptu zálohování naplánujte pomocí nástroje cron skript. 

Můžete vytvořit tři typy zálohy snímků:
- **HANA**: kombinované snímku zálohování, ve kterém svazků, které obsahují/hana/data a/hana/sdílené (který obsahuje také /usr/sap) jsou předmětem koordinované snímku. Jeden soubor obnovit je možné z tento snímek.
- **Protokoly**: zálohy snímku svazku logbackups/hana /. Aktivuje se, že žádný snímek HANA spustit tento snímek úložiště. Tento svazek úložiště měl by obsahovat zálohování transakčního protokolu SAP HANA. Toto omezení protokolu růst a zabránit ztrátě dat. probíhají častěji. Jeden soubor obnovit je možné z tento snímek. Nemáte nižší četnost v části 3 minut.
- **Spouštěcí**: snímku svazku, který obsahuje počet spouštěcí logické jednotky (LUN) velké instance HANA. Záloha snímku je možné pouze pomocí typu I SKU z HANA velké instancí. Obnovení nelze provést jedním souborem ze snímku svazku, který obsahuje spouštěcí logické jednotky.


>[!NOTE]
> Syntaxe volání pro tyto tři typy snímků změnit s přechodem na verzi 3.0 skripty, které podporují MCOD nasazení. Není nutné specifikovat už SID HANA instance. Musíte zajistit, že instance SAP HANA jednotky jsou nakonfigurovaná v konfiguračním souboru *HANABackupCustomerDetails.txt*.

>[!NOTE]
> Při prvním spuštění skriptu, se může zobrazovat v prostředí více SID neočekávaným chybám. Znovu spustit skript opravy problému.



Nové syntaxe volání pro provádění snímky úložiště pomocí skriptu *azure_hana_backup.pl* vypadá podobně jako tento:

```
HANA backup covering /hana/data and /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot <HANA Large Instance Type> <snapshot_prefix> <snapshot_frequency> <number of snapshots retained>

```

Podrobnosti o parametrech jsou následující: 

- První parametr charakterizuje typ zálohy snímku. Povolené hodnoty jsou **hana**, **protokoly**, a **spouštěcí**. 
- Parametr **<HANA Large Instance Type>** je potřebné pro spouštěcí svazek pouze zálohy. Jsou závislé na tuto jednotku HANA velké Instance dvě platné hodnoty "TypeI" nebo "TypeII". Chcete-li zjistit, jaké typy vaše jednotka je naleznete v tématu [přehled SAP HANA (velké instance) a architektura v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture).  
- Parametr **< snapshot_prefix >** je snímek nebo zálohování popisek pro typ snímku. Má dva účely: jeden je pro vás zadejte jeho název, abyste věděli, co tyto snímky jsou o. Druhým účelem je pro skript *azure\_hana\_backup.pl* k určení počtu snímků úložiště, které jsou uchovány v rámci tohoto konkrétní popisku. Pokud naplánujete dvě zálohy snímků úložiště stejného typu (jako **hana**), se dvou různých štítky a definujte, by měly být udržovány 30 snímků pro jednotlivé, v níž se 60 úložiště snímků svazků vliv. 
- Parametr **< snapshot_frequency >** je vyhrazeno pro budoucí vývoj a nemá žádný vliv. Nastavte na hodnotu "3min" při provádění zálohy typu **protokolu**a "15 minut" při provádění jiné typy zálohování.
- Parametr **<number of snapshots retained>** definuje míru uchování snímky nepřímo definováním počet snímků se stejnou předponou snímku (štítek). Tento parametr je důležité pro naplánované spuštění prostřednictvím procesu cron. Pokud počet snímků se stejnou snapshot_prefix překračuje počet poskytují tohoto parametru, je před provedením nový snímek úložiště odstranění nejstaršího snímku.

V případě škálovatelnou skript provede další kontroly k zajištění, že máte přístup všechny servery HANA. Skript také zkontroluje, jestli všechny instance HANA vrátit příslušný stav instancí předtím, než se vytvoří snímek SAP HANA. Snímek SAP HANA následuje snímku úložiště.

Provádění skriptu `azure_hana_backup.pl` vytvoří úložiště snímku v těchto tří fází:

1. Spustí na snímku SAP HANA
2. Provede snímku úložiště
3. Odebere SAP HANA snímek, který byl vytvořen před spuštěním úložiště snímků

Chcete-li spustit skript, volejte z složku HDB spustitelného souboru, do které jste zkopírovali ho. 

Doba uchování je možné spravovat pomocí počet snímků, které se odešlou jako parametr při spuštění skriptu. Množství času, která je předmětem úložiště snímků je funkce, doba provádění a počet snímků odeslána jako parametr při spouštění skriptu. Pokud počet snímků, které jsou zachovány překračuje počet, který je pojmenován jako parametr ve volání skriptu, je odstraněn nejstarší snímek úložiště stejných štítků, před provedením nový snímek. Počet dáváte jako poslední parametr volání je číslo můžete použít k řízení počet snímků, které jsou zachovány. S tímto číslem můžete taky řídit, nepřímo, místo na disku využité pro snímky. 

> [!NOTE]
>Jakmile změníte štítek, počítání znovu spustí. Potřebujete mít přísné v označování, tak vaše snímky nebude odstraněn omylem.

### <a name="snapshot-strategies"></a>Strategie snímku
Frekvence snímků pro různé typy závisí na tom, zda používáte funkci obnovení po havárii HANA velké Instance. Tato funkce využívá snímky, úložiště, které můžou vyžadovat zvláštní doporučení pro četnost a provádění období úložiště snímků. 

Důležité informace a doporučení, které následují, předpokladem je, abyste provedli *není* používat funkce obnovení po havárii, které nabízí velké instance HANA. Místo toho použijte snímky úložiště záloh a poskytnout obnovení bodu v čase za posledních 30 dní. Zadané omezení počtu snímků a adresní prostor, mají zákazníci za následující požadavky:

- Čas obnovení pro obnovení bodu v čase.
- Místo na použít.
- Bod obnovení a cíli doby obnovení pro potenciální obnovení po havárii.
- Závěrečné provádění HANA databáze úplné zálohování na disky. Vždy, když zálohu celé databáze proti disky nebo **backint** rozhraní se provádí, provádění úložiště snímků selže. Pokud máte v plánu provést zálohování úplné databáze nad úložiště snímků, ujistěte se, že během této doby je zakázána provádění úložiště snímků.
- Počet snímků na každý svazek (omezeným na 255).


Pro zákazníky, kteří nepoužívají funkci obnovení po havárii HANA velké instancí je méně častá období snímku. V takových případech zákazníkům provádět kombinované snímky na /hana/data a /hana/shared (zahrnuje /usr/sap) v období 12 hodin nebo 24 hodin a jejich udržovat snímky dobu jednoho měsíce. Platí to i s snímky zálohování svazku protokolu. Spuštění zálohování transakčního protokolu SAP HANA na zálohování svazku protokolu se však dojde v 5 minut období 15 minut.

Snímky naplánované úložiště se nejlépe provádí pomocí procesu cron. Pomocí stejného skriptu pro všechny zálohy a potřebovat obnovení po havárii a úpravě vstupů skript tak, aby odpovídaly různých požadovaný čas zálohování. Tyto snímky jsou všechny naplánované jinak v procesu cron v závislosti na jejich čas spuštění: hodinové, 12 hodin, denní nebo týdenní. 

Následuje příklad plán cron v/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 46
10 00 * * *  ./azure_hana_backup.pl hana dailyhana 15min 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log dailylogback 3min 28
30 00 * * *  ./azure_hana_backup.pl boot TypeI dailyboot 15min 28
```
V předchozím příkladu je hodinové kombinované snímků, které pokrývá svazky, které obsahují data/hana/a /hana/shared (zahrnuje/usr/sap) umístění. Tento typ snímku pro rychlejší obnovení bodu v čase použijte v posledních dvou dnů. Kromě toho je denní snímek na tyto svazky. Ano máte dva dny pokrytí podle hodinové snímky plus čtyři týdny pokrytí podle denní snímky. Kromě toho svazek zálohování protokolu transakcí zálohovat denně. Tyto zálohy jsou zachovány i čtyři týdny. Jak vidíte v ve třetím řádku crontab, zálohování transakčního protokolu HANA je naplánován ke spuštění každých 5 minut. Časy zahájení procesu cron různých úloh, které provést úložiště snímků rozkládají tak, aby tyto snímky nebudou provedeny všechny najednou v určitém místě v čase. 

V následujícím příkladu můžete provést kombinovaný snímek, který popisuje svazků, které obsahují/hana/data a/hana/sdílené (včetně/usr/sap) umístění hodinu. Tyto snímky ponechat dva dny. Snímky svazků zálohování protokolu transakcí jsou spouštěny na základě 5 minut a jsou v 4 hodiny. Jako dříve, zálohování soubor protokolu transakcí HANA je naplánován ke spuštění každých 5 minut. Snímek svazku pro zálohování protokolu transakcí se provádí s zpožděním 2 minut po spuštění zálohování protokolu transakcí. V rámci 2 minuty třeba za normálních okolností dokončit zálohování protokolu transakcí SAP HANA. Jako dříve, svazku, který obsahuje spouštěcí LUN zálohovat jednou denně pomocí snímků úložiště a je udržováno čtyři týdny.

```
10 0-23 * * * ./azure_hana_backup.pl hana hourlyhana 15min 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log logback 3min 48
30 00 * * *  ./azure_hana_backup.pl boot TypeII dailyboot 15min 28
```

Sekvence na předchozí příklad, s výjimkou spouštěcí logické jednotky je znázorněný na následujícím obrázku:

![Vztah mezi zálohy a snímky](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA provede regulární zápisu svazku /hana/log dokumentu potvrzené změny do databáze. V pravidelných intervalech zapíše SAP HANA uloženého bodu do svazku /hana/data. Jak je uvedeno v crontab je provést zálohování protokolu transakcí SAP HANA každých 5 minut. Můžete také zjistit, že SAP HANA snímku se spustí každou hodinu v důsledku spuštění snímku kombinované úložiště přes /hana/data a /hana/shared svazky. Po úspěšné HANA snímku se spustí kombinované úložiště snímku. Podle pokynů v crontab, snímku úložiště na svazku /hana/logbackup se spustí každých 5 minut, po zálohování protokolu transakcí HANA přibližně 2 minut.

> [!NOTE]
>Při plánování úložiště zálohy snímků na dva uzly replikaci systému HANA instalace, musíte zajistit, že se nepřekrývají spuštěních snímků záloh mezi dvěma uzly. SAP HANA má omezení řešení vždy pouze jeden HANA snímku. Protože HANA snímku je základní součástí zálohy snímku úspěšné úložiště, je třeba zajistit, že se úložiště snímku na primární a sekundární uzel a případné třetí uzel vypršel časový limit vedle sebe navzájem.


>[!IMPORTANT]
> Používání úložiště snímků pro SAP HANA zálohy se hodí v situaci, jenom v případě, že snímky jsou prováděny ve spojení s zálohování transakčního protokolu SAP HANA. Tyto zálohy protokolu transakcí, třeba tak, aby pokrývalo časových období mezi snímky úložiště. 

Pokud jste nastavili závazek uživatelům v okamžiku obnovení 30 dní, budete muset:

- Ve výjimečných případech přístup k kombinované úložiště snímku přes /hana/data a /hana/shared, který je 30 dní.
- Máte zálohování souvislý transakčního protokolu, které se týkají čas mezi jakékoli snímky, kombinované úložiště. Ano nejstarší snímku svazku pro zálohování protokolu transakcí musí být 30 dní. Nejedná se případě, pokud zkopírujete zálohování transakčního protokolu do jiné složky systému souborů NFS, který je umístěný na úložiště Azure. V takovém případě může vyžádat starší zálohy protokolu transakcí z této sdílené složky NFS.

Abyste mohli využívat výhod úložiště snímků a replikace případné úložiště zálohování transakčního protokolu, musíte změnit umístění, do které SAP HANA zapíše zálohování transakčního protokolu. Provedení této změny můžete v HANA Studio. I když SAP HANA zálohuje protokolu úplné segmenty automaticky, musíte zadat intervalu zálohování protokolu být deterministický. To platí hlavně při použití možnosti obnovení po havárii, vzhledem k tomu obvykle chcete provést zálohování protokolu s dobou deterministický. V následujícím případě 15 minut se nastavují jako interval zálohování protokolů.

![Plán zálohování SAP HANA přihlásí SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Můžete také zálohování, které jsou častější, než každých 15 minut. Častější nastavení se často používá ve spojení s funkcemi pro zotavení po havárii velké instancí HANA. Někteří zákazníci provést zálohování protokolu transakcí každých 5 minut.  

Pokud databázi nikdy byla vytvořena záloha, v posledním kroku je provést zálohu databáze na základě souborů k vytvoření jedné zálohování položku, která musí existovat v rámci katalogu zálohování. SAP HANA nelze zahájit, jinak hodnota zadaného protokolu zálohování.

![Vytvořit zálohu na základě souborů k vytvoření jedné položky zálohování](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po provedení mít vaše první úspěšné úložiště snímků, můžete odstranit testovací snímek, který byl proveden v kroku 6. Chcete-li tak učinit, spusťte skript `removeTestStorageSnapshot.pl`:
```
./removeTestStorageSnapshot.pl <hana instance>
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

Ve svazku konkrétní úložiště můžete sledovat počet snímků a spotřebu úložiště těchto snímků. `ls` Příkaz nezobrazí snímek adresáře nebo soubory. Ale příkaz operační systém Linux `du` obsahuje podrobnosti o těchto snímků úložiště, protože jsou uložené ve stejném svazcích. Příkaz lze použít tyto možnosti:

- `du –sh .snapshot`: Tato možnost poskytuje celkem všechny snímky v adresáři snímku.
- `du –sh --max-depth=1`: Tato možnost zobrazí všechny snímky, které jsou uloženy v **.snapshot** složku a velikosti jednotlivých snímků.
- `du –hc`: Tato možnost poskytuje celkovou velikost používané všechny snímky.

Pomocí těchto příkazů a ujistěte se, že snímky, které jsou provedeny a uloženy nejsou využívá všechny úložiště na svazcích.

>[!NOTE]
>Snímky spouštěcí LUN nejsou viditelné s předchozích příkazů.

### <a name="getting-details-of-snapshots"></a>Získávání podrobností snímků
K získání dalších podrobností na snímky, můžete také použít skript `azure_hana_snapshot_details.pl`. Tento skript můžete spustit v některém umístění, pokud je aktivní server v umístění pro obnovení po havárii. Skript poskytuje následující výstup, členěné podle každý svazek, který obsahuje snímky: 
   * Velikost celkový počet snímků na svazku
   * Následující podrobnosti v jednotlivých snímků v tomto svazku: 
      - Název snímku 
      - Vytvořit čas 
      - Velikost snímku
      - Frekvence snímků
      - ID zálohování HANA přidružené tento snímek, pokud je to důležité

Následuje příklad syntaxe provádění skriptu:

```
./azure_hana_snapshot_details.pl 
```

Protože skript se pokusí načíst ID zálohování HANA, musí se připojit k instanci SAP HANA. Toto připojení vyžaduje konfiguračního souboru *HANABackupCustomerDetails.txt* správně nastavit. Výstup dvě snímků na svazku může vypadat následovně:

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
Pro typy snímků **hana** a **protokoly**, dostanete snímky přímo na svazky v **.snapshot** adresáře. Není podadresáři pro všechny snímky. Můžete zkopírovat každý soubor ve stavu, ve kterém se nacházel v místě snímek z podadresář do skutečné adresářové struktury.

>[!NOTE]
>Obnovení jedním souborem nefunguje pro snímky spouštěcí nezávislé na typ Instance HANA velké jednotky LUN. **.Snapshot** directory nebude vystavena ve spouštěcí logické jednotky. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Omezení počtu snímků na serveru

Jak je popsáno výše, můžete snížit počet určité popisky snímků, které jsou uloženy. Poslední dva parametry tohoto příkazu zahájíte snímek jsou popisek a počet snímků, které chcete zachovat.

```
./azure_hana_backup.pl hana dailyhana 15min 28
```

V předchozím příkladu popisek snímku je **dailyhana** a počet snímků s tímto štítkem pro zachování **28**. Jak můžete reagovat na využívání místa na disku, můžete snížit počet uložené snímky. Je snadný způsob, jak snížit počet snímků na 15, například pro spuštění skriptu s poslední parametr nastavit na **15**:

```
./azure_hana_backup.pl hana dailyhana 15min 15
```

Pokud spustíte skript s tímto nastavením, počet snímků, včetně nový snímek úložiště je 15. 15 posledních snímků jsou zachovány a 15 starší snímky jsou odstraněny.

 >[!NOTE]
 > Tento skript snižuje počet snímků, pouze pokud existuje více než 1 hodinu stará snímky. Skript nedojde k odstranění snímků, které jsou menší než 1 hodinu stará. Tato omezení se vztahují k obnovení po havárii volitelné funkce nabízené.

Pokud již nechcete udržovat sadu snímky s zálohování popisek **hanadaily** příklady syntaxe, můžete spustit skript s **0** jako číslo uchovávání informací. Potom se odeberou všechny snímky odpovídající tohoto popisku. Odebrání všechny snímky však může ovlivnit možnosti velké instancí HANA funkci obnovení po havárii.

Druhá možnost odstranit konkrétní snímků je skript `azure_hana_snapshot_delete.pl`. Tento skript je určena k odstranění snímků nebo sadu snímky buď pomocí zálohování ID HANA jak se nachází v HANA Studio, nebo prostřednictvím samotný název snímku. V současné době zálohování ID pouze svázané s snímky vytvořené pro **hana** typu snímku. Snímek zálohy typu **protokoly** a **spouštěcí** neprovádět SAP HANA snímku a proto není žádné zálohování ID, která se má najít pro tyto snímky. Pokud je zadán název snímku, hledá všechny snímky v různých svazcích, které odpovídají názvu zadané snímku. 

Volání skriptu musíte zadat identifikátor SID instance HANA pomocí syntaxe volání skriptu:

```
./azure_hana_snapshot_delete.pl <SID>

```

Spuštění skriptu jako uživatel **kořenové**.

Pokud vyberete snímek, můžete odstranit jednotlivých snímků jednotlivě. Nejdřív zadejte svazek, který obsahuje snímek a potom zadejte název snímku. Pokud snímku na tento svazek existuje a je více než 1 hodinu stará, je odstraněn. Názvy svazku a názvy snímků můžete najít spuštěním `azure_hana_snapshot_details` skriptu. 

>[!IMPORTANT]
>Pokud existuje data, která existuje pouze na snímek, který odstraňujete, po odstranění snímku, dojde ke ztrátě dat navždy.

   

### <a name="recover-to-the-most-recent-hana-snapshot"></a>Obnovit na poslední snímek HANA

V případě produkční rozbalovací lze zahájit proces obnovení ze snímku úložiště jako zákazník incidentu s Microsoft Azure Support. Bude stačit vysokou naléhavost, pokud byla data odstraněna v produkční systému a je jediný způsob, jak načíst ho obnovit produkční databázi.

Obnovení bodu v čase v jiné situaci, může být nízkou naléhavost a plánované dnů předem. Toto obnovení s SAP HANA můžete naplánovat na Azure Service Management místo vyvolání příznak s vysokou prioritou. Například je může plánování k upgradu softwaru SAP použitím nový balíček vylepšení. Pak budete muset obnovit snímek, který představuje stav před upgradem vylepšení balíčku.

Než odešlete požadavek, je nutné připravit. SAP HANA týmu Azure Service Management můžete žádost zpracovat a poskytují obnovené svazky. Potom můžete obnovit databázi HANA podle snímky. 

Následující ukazuje, jak připravit na žádost:

>[!NOTE]
>Uživatelské rozhraní se můžou lišit od následující snímky obrazovky, v závislosti na verzi SAP HANA, který používáte.

1. Rozhodněte, které snímek pro obnovení. Pouze hana nebo datový svazek je obnoven, pokud dáte pokyn jinak. 

2. Vypněte instanci HANA.

 ![Vypnout instanci HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Odpojte objemy dat v každém uzlu HANA databáze. Pokud datové svazky se pořád připojený k operačního systému, obnovení snímku se nezdaří.
 ![Odpojte svazky dat v každém uzlu databáze HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Otevřete žádost o podporu Azure a obsahovat pokyny k obnovení snímku konkrétní.

 - Během obnovení: SAP HANA na Azure Service Management může vás k účasti na konferenci pro zajištění spolupráce, ověření a potvrzení, že se snímek správné úložiště obnoví. 

 - Po obnovení: SAP HANA na Azure Service Management vás upozorní, jakmile byla obnovena snímku úložiště.

5. Po dokončení procesu obnovení je znovu připojte všechny datové svazky.

 ![Znovu připojte všechny datové svazky](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Možnosti obnovení v rámci SAP HANA Studio, vyberte, pokud nepocházejí automaticky po opětovném připojení k databázi HANA přes SAP HANA Studio. Následující příklad ukazuje obnovení poslední snímek HANA. Úložiště snímků Vloží jednu HANA snímku. Pokud obnovíte poslední snímek úložiště, by mělo být nejnovější HANA snímku. (Pokud obnovujete na starší snímku úložiště, budete muset najít HANA snímku na základě doby pořízení snímku úložiště.)

 ![Vyberte možnosti obnovení studia SAP HANA](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Vyberte **obnovit databázi do konkrétních dat zálohování nebo úložiště snímků**.

 ![Zadejte typ obnovení okna](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Vyberte **zadejte zálohování bez katalogu**.

 ![Zadejte umístění zálohy okna](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. V **cílový typ** seznamu, vyberte **snímku**.

 ![Zadejte zálohy obnovit okna](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Vyberte **Dokončit** zahájíte proces obnovení.

 ![Vyberte "Dokončit" zahájíte proces obnovení](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. Databázi HANA je obnovit a obnovit HANA snímek, který je součástí úložiště snímku.

 ![Databáze HANA je obnovit a obnovit snímek HANA](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recover-to-the-most-recent-state"></a>Obnovení na nejnovější stav

Následující proces obnoví HANA snímek, který je součástí úložiště snímku. Obnoví pak zálohování transakčního protokolu nejnovější stavu databázi před obnovením snímku úložiště.

>[!IMPORTANT]
>Než budete pokračovat, ujistěte se, že máte úplný a souvislý řetězec zálohy protokolu transakcí. Aktuální stav databáze nelze obnovit bez tyto zálohy.

1. Dokončete kroky 1 – 6 v [obnovit poslední snímek HANA](#recovering-to-the-most-recent-hana-snapshot).

2. Vyberte **obnovit databázi do stavu nejnovější**.

 ![Vyberte možnost "Obnovit databázi do stavu poslední"](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Zadejte umístění poslední zálohy protokolu HANA. Umístění musí obsahovat všechny HANA zálohování transakčního protokolu ze snímku HANA nejnovější stav.

 ![Zadejte umístění poslední zálohy protokolu HANA](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Vyberte zálohu jako základ, ze kterého chcete obnovit databázi. V tomto příkladu je HANA snímku na snímku obrazovky HANA snímek, který je zahrnutý ve snímku úložiště. 

 ![Vyberte zálohu jako základ, ze kterého chcete obnovit databázi](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Vymazat **použití rozdílové zálohy** zaškrtávací políčko, pokud nejsou k dispozici rozdílů mezi časem HANA snímku a nejnovější stav.

 ![Zrušte zaškrtnutí políčka "Použití rozdílové zálohy", pokud neexistuje žádné rozdíly](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Na obrazovce Přehled vyberte **Dokončit** spustíte proces obnovení.

 ![Klikněte na tlačítko "Dokončit" na obrazovce Přehled](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recover-to-another-point-in-time"></a>Obnovení do jiného bodu v čase
Obnovit do bodu v čase mezi HANA snímku (zahrnutá ve snímku úložiště) a ten, který je novější než obnovení bodu v čase HANA snímku, proveďte následující kroky:

1. Ujistěte se, že máte všechny zálohy protokolu transakcí ze snímku HANA dobu, kterou chcete obnovit.
2. Začněte postup uvedený v [obnovení na nejnovější stav](#recovering-to-the-most-recent-state).
3. V kroku 2 postupu, v **zadejte typ obnovení** vyberte **obnovit databázi do následující bodu v čase**a pak zadejte bod v čase. 
4. Proveďte kroky 3 až 6.

### <a name="monitor-the-execution-of-snapshots"></a>Monitorování provádění snímků

Jak používáte úložiště snímků HANA velké instancí, musíte také monitorování provádění těchto snímků. Skript, který provede snímku úložiště zapíše výstup do souboru a uloží ho do stejného umístění jako tyto skripty. Samostatný soubor je napsán pro každý úložiště snímků. Výstup každého souboru zobrazuje v různých fázích, že se snímek skript spustí:

1. Vyhledá svazky, které je třeba pro vytvoření snímku.
2. Vyhledá snímky převzaty z těchto svazků.
3. Odstraní případné existující snímky tak, aby odpovídaly počet snímků, které zadáte.
4. Vytvoří snímek SAP HANA.
5. Vytvoří snímek úložiště přes svazky.
6. Odstraní snímku SAP HANA.
7. Přejmenuje nejnovější snímku do **.0**.

Nejdůležitější část identifikuje soubor cab skriptu je tuto část:
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
Zobrazí se od této ukázky jak skript záznamy vytvoření snímku HANA. V případě Škálováním na více systémů je tento proces zahájit v hlavní uzel. Hlavní uzel zahájí synchronní vytváření snímků SAP HANA na všechny uzly pracovního procesu. Potom pořízení snímku úložiště. Po úspěšné provedení snímků úložiště je odstranění snímku HANA. Odstranění snímku HANA je zahájena z hlavního uzlu.


## <a name="disaster-recovery-principles"></a>Zásady obnovení po havárii
Velké instancí HANA nabízejí je funkce obnovení po havárii mezi razítka HANA velké Instance v různých oblastech Azure. Například při nasazování velkých Instance HANA jednotky v oblasti USA – západ Azure, můžete použít instanci HANA velké jednotky v oblasti USA – východ jako jednotky obnovení po havárii. Jak už bylo zmíněno dříve, zotavení po havárii není konfigurován automaticky, protože se vyžaduje, abyste platit pro jiné jednotce HANA velké Instance v oblasti zotavení po Havárii. Instalační program pro zotavení po havárii funguje pro nastavení škálování, jakož i Škálováním na více systémů. 

Ve scénářích nasazení, pokud zákazníci používat jednotku v oblasti zotavení po Havárii spustit mimo produkční systémy, které používají nainstalovaná instance HANA. Instance HANA velké jednotky musí být stejné verze SKU jako SKU používá pro produkční účely. Následující obrázek ukazuje, jaké konfigurace disku mezi jednotky serverů v oblasti Azure produkční a oblasti obnovení po havárii vypadá takto:

![Konfigurace nastavení zotavení po Havárii z hlediska disku](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Jak je znázorněno na obrázku přehled, musíte pak pořadí druhou sadu diskové svazky. Cílové diskové svazky mají stejnou velikost jako produkční svazky pro instanci provozní v jednotkách obnovení po havárii. Tyto diskové svazky jsou přidruženy k jednotka HANA velké instanci serveru v lokalitě pro obnovení po havárii. Následující svazky se replikují z oblasti produkční k webu zotavení po Havárii:

- / hana/dat
- / hana/logbackups 
- /Hana/Shared (zahrnuje/usr/sap)

/Hana/log svazek není replikují, protože transakčního protokolu SAP HANA není potřeba takovým způsobem, který je provést obnovení z těchto svazků. 

Základ pro funkci obnovení po havárii nabízí se funkce replikace úložiště nabízí infrastruktura HANA velké Instance. Funkce, která se používá na straně úložiště není nepřetržitý datový proud změny, které se replikují v asynchronním režimu, protože změny dojít ke svazku úložiště. Místo toho je mechanismus, který závisí na skutečnost, že jsou vytvořeny snímky tyto svazky v pravidelných intervalech. Rozdíl mezi již replikované snímku a nový snímek, který ještě nebyla replikována je pak přeneseny na lokalitě pro obnovení po havárii na cílové diskové svazky.  Tyto snímky jsou uložené ve svazcích a v případě selhání obnovení po havárii je nutné obnovit na těchto svazcích.  

První přenos dokončení dat svazku by měl být než množství dat bude menší než rozdílů mezi snímky. V důsledku toho svazky v lokalitě zotavení po Havárii obsahovat každých snímků svazku v produkční lokality provést. Nakonec můžete daný systém zotavení po Havárii získat starší stav obnovení ztracených dat bez vrácení zpět systému výroby.

V případě MCOD nasazení s více instancí SAP HANA nezávislé na jednu jednotku HANA velké Instance očekává se, že jsou všechny instance SAP HANA získávání úložiště replikovat na straně pro zotavení po Havárii.

V případech, kde používáte replikaci HANA systému jako funkce vysoké dostupnosti v produkční lokality replikují se jenom svazky instance vrstvy 2 (nebo replika). Tato konfigurace může vést ke zpoždění replikace úložiště k webu zotavení po Havárii, pokud udržovat nebo vypnout jednotka serveru sekundární repliky (úroveň 2) nebo instance SAP HANA v této jednotce. 


>[!IMPORTANT]
>Stejně jako u vícevrstvé replikace systému HANA, vypnutí jednotky vrstvy 2 HANA instance nebo server blokuje replikace na lokalitu pro zotavení po havárii při použití funkce obnovení po havárii HANA velké Instance.


>[!NOTE]
>Funkce replikace úložiště HANA velké Instance je zrcadlení a replikace úložiště snímků. V případě úložiště snímků zavedeném ve [zálohování a obnovení](#backup-and-restore) části tohoto článku, nesmí být žádné replikace na lokalitu obnovení po havárii. Provádění snímku úložiště je předpokladem pro replikaci úložiště na lokalitě pro obnovení po havárii.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Příprava zotavení po havárii
V tomto scénáři máte produkční systému, který používá u velkých instancí HANA v produkčním prostředí oblast Azure. Kroky, které následují Předpokládejme, že identifikátor SID tohoto systému HANA je "PRD", a zda máte testovacím systému, který používá u velkých instancí HANA v oblasti Azure zotavení po Havárii. K tomu Předpokládejme, že jeho SID je "TST." Následující obrázek znázorňuje tuto konfiguraci:

![Spuštění instalačního programu zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Pokud instance serveru má již byly uspořádány sadou další úložiště svazku, SAP HANA na Azure Service Management připojí další sadu svazky jako cíl pro repliku produkčního na velké Instance HANA jednotku, na kterém spouštíte TST HANA instance. K tomuto účelu budete muset zadat identifikátor SID instance HANA produkční. Poté, co SAP HANA na Azure Service Management potvrdí připojení tyto svazky, musíte tyto svazky na jednotku, velké Instance HANA připojte.

![Dalším krokem instalace zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Dalším krokem je pro vás k instalaci druhé instance SAP HANA na jednotce HANA velké Instance v oblasti Azure zotavení po Havárii, kdy je spuštěna instance TST HANA. Nově instalovaný instance SAP HANA musí mít stejný identifikátor SID. Uživatelé vytvořili musí mít stejný UID a ID skupiny, která má instance produkční. Pokud instalace byla úspěšná, budete muset:

- Spusťte krok 2 ze snímku přípravy úložiště popsané výše v tomto článku.
- Pokud jste tak ještě neučinili, vytvořte veřejný klíč pro zotavení po Havárii jednotku Instance HANA velké jednotky. Viz krok 3 přípravy snímku úložiště popsané výše v tomto článku.
- Udržovat *HANABackupCustomerDetails.txt* s nové HANA instance a testovací jestli připojení do úložiště funguje správně.  
- Zastavte nově nainstalovaná instance SAP HANA na jednotce HANA velké Instance v oblasti Azure zotavení po Havárii.
- Odpojit tyto svazky PRD a obraťte se na Azure Service Management SAP HANA. Svazky nelze zůstanou připojené k jednotce, protože nesmí být dostupný při fungování jako cíl replikace úložiště.  

![Zotavení po Havárii kroku před navázáním replikace](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Provozní tým vytváří vztah replikace mezi svazky PRD v produkčním prostředí oblast Azure a PRD svazky v oblasti Azure zotavení po Havárii.

>[!IMPORTANT]
>/Hana/log svazek není replikují, protože není nutné obnovit databázi SAP HANA replikované do konzistentního stavu v lokalitě pro obnovení po havárii.

V dalším kroku nastavit nebo upravit plán zálohování snímku úložiště, abyste se dostali na RTO a plánovaný bod obnovení v případě, že po havárii. Chcete-li minimalizovat plánovaného bodu obnovení, nastavte následující intervaly replikace v HANA velké Instance služby:
- Pro svazky, které jsou předmětem kombinované snímku (snímek typ **hana**), sadu k replikaci každých 15 minut k cílům svazku ekvivalentní úložiště v lokalitě pro obnovení po havárii.
- Pro svazek zálohování protokolu transakcí (snímek typ **protokoly**), nastaven na replikaci každé 3 minuty, k cílům svazku ekvivalentní úložiště v lokalitě pro obnovení po havárii.

Chcete-li minimalizovat plánovaného bodu obnovení, následující nastavení:
- Provedení **hana** typ úložiště snímků (najdete v části "krok 7: provedení snímků") každých 30 minut na 1 hodinu.
- Proveďte zálohování protokolu transakcí SAP HANA každých 5 minut.
- Provedení **protokoly** zadejte úložiště snímku každých 5 až 15 minut. Pomocí tohoto období interval dosáhnout RPO přibližně 15 25 minut.

Toto nastavení, pořadí zálohování transakčního protokolu, úložiště snímků a replikace transakce HANA protokolu zálohování svazku a/hana/data a /hana/shared (zahrnuje/usr/sap) může vypadat například data zobrazená na obrázku:

 ![Vztah mezi snímek zálohy protokolu transakcí a snap zrcadlení na časová osa](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

K dosažení i lepším plánovaným bodem obnovení v případě zotavení po havárii, můžete zkopírovat zálohování transakčního protokolu HANA z SAP HANA v Azure (velké instance) oblasti Azure. K dosažení této další snížení plánovaný bod obnovení, proveďte následující kroky:

1. Zálohování transakce HANA protokolu možné se často jako /hana/logbackups.
2. Pomocí rsync zkopírujte zálohování transakčního protokolu do systému souborů NFS sdílené složky hostované virtuální počítače Azure. Virtuální počítače jsou ve virtuálních sítí Azure, v oblasti Azure produkční a v oblastech zotavení po Havárii. Budete muset připojit obě Azure virtuální sítě k okruhu provozní HANA velké instance připojení k Azure. V tématu grafiky ve [sítě důležité informace pro zotavení po havárii s instancemi velké HANA](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) části. 
3. Zajistit zálohování transakčního protokolu v oblasti ve virtuálním počítači připojit k systému souborů NFS, exportovat úložiště.
4. V případě převzetí služeb při selhání po havárii doplňují zálohování transakčního protokolu, které můžete najít na svazku /hana/logbackups s více nedávno provedených zálohování transakčního protokolu v systému souborů NFS sdílet v lokalitě pro obnovení po havárii. 
5. Spustíte zálohování protokolu transakcí k obnovení poslední zálohy, který může být uložený přes oblasti zotavení po Havárii.

Když HANA velké Instance operace potvrzení instalace vztah replikace a spustit zálohy snímků provádění úložiště, replikaci dat začne.

![Zotavení po Havárii kroku před navázáním replikace](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Jako postupuje replikace, snímky na svazcích PRD v oblastech Azure zotavení po Havárii nejsou obnoveny. Pouze jsou uloženy. Pokud jsou svazky připojené v takový stav, představují stavu, ve kterém můžete odpojit tyto svazky po instalaci instance PRD SAP HANA v jednotce serveru v oblasti Azure zotavení po Havárii. Také představují úložiště záloh, které ještě nejsou obnoveny.

V případě selhání také můžete k obnovení na starší snímku úložiště místo nejnovější snímku úložiště.

## <a name="disaster-recovery-failover-procedure"></a>Postup převzetí služeb při selhání pro zotavení po havárii
Existují dva případy vzít v úvahu při přebírání služeb při selhání lokality zotavení po Havárii:

- Je nutné databázi SAP HANA se vrátíte k nejnovější stav data. V takovém případě není samoobslužné služby skript, pomocí kterého můžete provádět převzetí služeb při selhání bez nutnosti kontaktování společnosti Microsoft. Ale pro navrácení služeb po obnovení, musíte spolupracovat s Microsoftem.
- Chcete obnovit do úložiště snímek, který není nejnovější replikované snímku. V takovém případě musíte spolupracovat s Microsoftem. 

>[!NOTE]
>Tyto kroky je nutné provést u velkých Instance HANA jednotky, která představuje jednotku zotavení po Havárii. 
 
Pokud chcete obnovit nejnovější replikované úložiště snímků, proveďte následující kroky: 

1. Vypněte mimo produkční instanci HANA na jednotku obnovení po havárii HANA velké instancí, který používáte. Je to proto, že je předem nainstalovaná spících HANA produkční instance.
2. Ujistěte se, zda jsou spuštěny žádné procesy SAP HANA. Použijte následující příkaz pro tuto kontrolu: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Výstup by měl zobrazit, můžete **hdbdaemon** procesů v zastaveném stavu a žádné jiné procesy HANA ve stavu spuštěná nebo spuštěna.
3. Na jednotce velké Instance HANA lokality zotavení po Havárii, spusťte skript *azure_hana_dr_failover.pl*. Skript se žádostí o SAP HANA SID pro obnovení. Vyžádání, zadejte v jedné nebo jediný SAP HANA SID, které jsou replikované a se udržuje v *HANABackupCustomerDetails.txt* souborů na jednotce HANA velké Instance v lokalitě zotavení po Havárii. 

      Pokud chcete mít více instancí SAP HANA převzetí služeb při selhání, budete muset spustit skript několikrát. Vyžádání, zadejte SID SAP HANA chcete převzetí služeb při selhání a obnovení. Skript po dokončení se zobrazí seznam přípojných bodů svazků, které jsou přidány do Instance HANA velké jednotky. Tento seznam obsahuje také obnovené svazky zotavení po Havárii.

4. Svazky obnovení po havárii obnovené připojte pomocí příkazů operačního systému Linux na jednotku, velké Instance HANA v lokalitě pro obnovení po havárii. 
6. Spuštění spících produkční instance SAP HANA.
7. Pokud jste zvolili pro kopírování protokolů transakcí zálohování protokolů zkrátit čas plánovaný bod obnovení, budete muset do adresáře logbackups nově připojené zotavení po Havárii/hana/sloučit tyto zálohy protokolu transakcí. Nepřepisovat existující zálohy. Novější záložní kopie, které se nezreplikovaly s nejnovější replikace úložiště snímku.
8. Můžete také obnovit jednu soubory ze snímků, které mají být replikovány do svazku /hana/shared/PRD v oblasti Azure zotavení po Havárii. 

Převzetí služeb při selhání zotavení po Havárii můžete otestovat i bez dopadu na vztah skutečné replikace. Pokud chcete provést testovací převzetí služeb, postupujte podle předchozích kroků 1 a 2 a potom pokračujte následující krok 3.

>[!IMPORTANT]
>Proveďte *není* spustit žádné produkční transakce na instanci, kterou jste vytvořili v lokalitě zotavení po Havárii procesem **testování převzetí služeb při selhání** pomocí skriptu zavedená v kroku 3. Tento příkaz vytvoří sadu svazky, které nemají žádný vztah k primární lokalitě. V důsledku toho synchronizace zpět do primární lokality je *není* možné. 

Krok 3 pro test převzetí služeb při selhání:

Na jednotce velké Instance HANA lokality zotavení po Havárii, spusťte skript **azure_hana_test_dr_failover.pl**. Tento skript je *není* zastavení vztah replikace mezi primární lokalitou a lokalitou zotavení po Havárii. Místo toho je tento skript klonování svazky úložiště zotavení po Havárii. Po úspěšném klonování klonovaný svazky jsou obnovena poslední snímku a pak připojena k jednotce zotavení po Havárii. Skript se žádostí o SAP HANA SID pro obnovení. Zadejte jeden nebo jediný SAP HANA SID, které jsou replikované a se udržuje v *HANABackupCustomerDetails.txt* souborů na jednotce HANA velké Instance v lokalitě zotavení po Havárii. 

Pokud chcete mít více instancí SAP HANA otestovat, budete muset spustit skript několikrát. Vyžádání, zadejte identifikátor SID SAP HANA instance, kterou chcete provést testovací převzetí služeb při selhání. Po dokončení skriptu zobrazuje seznam přípojných bodů svazků, které jsou přidány do Instance HANA velké jednotky. Tento seznam obsahuje také klonovaný svazky zotavení po Havárii.

Pokračujte ke kroku 4.

   >[!NOTE]
   >Pokud potřebujete převzetí služeb při selhání lokality zotavení po Havárii záchrana data, která byla odstraněna hodinami a je třeba zotavení po Havárii svazky, které mají být nastavena na starší snímku, tento postup platí. 

4. Vypněte mimo produkční instanci HANA na jednotku obnovení po havárii HANA velké instancí, který používáte. Je to proto, že je předem nainstalovaná spících HANA produkční instance.
5. Ujistěte se, zda jsou spuštěny žádné procesy SAP HANA. Použijte následující příkaz pro tuto kontrolu: `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`. Výstup by měl zobrazit, můžete **hdbdaemon** procesů v zastaveném stavu a žádné jiné procesy HANA ve stavu spuštěná nebo spuštěna.
6. Určí, do které SAP HANA zálohování ID nebo název snímku chcete mít obnovení lokality obnovení po havárii. V případech obnovení po havárii skutečné tento snímek je obvykle nejnovější snímku. Pokud potřebujete obnovit ke ztrátě dat, vyberte starší snímku.
7. Obraťte se na podporu Azure prostřednictvím žádosti o podporu s vysokou prioritou. Požádejte o obnovení tohoto snímku (s názvem a datum snímku) nebo ID zálohování HANA na webu zotavení po Havárii. Výchozí hodnota je, že na straně operations obnoví/hana/datový svazek pouze. Pokud chcete mít/hana/logbackups svazky a, musíte výslovně uvádějí, že. *Neobnovujte /hana/shared svazku.* Místo toho byste měli zvolit konkrétní soubory jako global.ini mimo **.snapshot** adresáře a jeho podadresářů po opětovném připojení/hana/sdílený svazek clusteru pro PRD. 

   Na straně operace provedeny následující kroky:

   a. Replikaci snímků z svazek provozního svazky obnovení po havárii je zastavena. Tato doba přerušení může již došlo Pokud výpadku v produkčním je z důvodu, že je potřeba provést postup pro zotavení po havárii.
   
   b. Úložiště snímků název nebo snímku s zálohování ID, které jste zvolili, je obnovena na svazcích obnovení po havárii.
   
   c. Po obnovení je možné připojit k instanci HANA velké jednotky v oblasti obnovení po havárii svazky obnovení po havárii.
      
8. Připojte svazky obnovení po havárii na jednotku HANA velké Instance v lokalitě pro obnovení po havárii. 
9. Spuštění spících produkční instance SAP HANA.
10. Pokud jste zvolili pro kopírování protokolů transakcí zálohování protokolů zkrátit čas plánovaný bod obnovení, budete muset do adresáře logbackups nově připojené zotavení po Havárii/hana/sloučit tyto zálohy protokolu transakcí. Nepřepisovat existující zálohy. Novější záložní kopie, které se nezreplikovaly s nejnovější replikace úložiště snímku.
11. Můžete také obnovit jednu soubory ze snímků, které mají být replikovány do svazku /hana/shared/PRD v oblasti Azure zotavení po Havárii.

Další pořadí kroků zahrnuje obnovení instance SAP HANA produkční na základě obnovené úložiště snímků a zálohování transakčního protokolu, které jsou k dispozici:

1. Změna umístění zálohy na **/hana/logbackups** pomocí SAP HANA Studio.
   ![Změna umístění zálohy pro zotavení po Havárii obnovení](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

2. SAP HANA kontrol prostřednictvím umístění zálohování souborů a navrhne nejnovější zálohy protokolu transakcí k obnovení. Kontrola může trvat několik minut, dokud se na obrazovce, jako se zobrazí následující: ![seznam zálohování transakčního protokolu pro zotavení po Havárii obnovení](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

3. Upravte některé výchozí nastavení:

      - Zrušte **používat rozdílové zálohy**.
      - Vyberte **inicializovat oblasti protokolu**.

   ![Nastavení protokolu oblasti inicializovat](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

4. Vyberte **Finish** (Dokončit).

   ![Dokončení obnovení zotavení po Havárii](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Okno Průběh, jak ukazuje tady by se zobrazit. Mějte na paměti, že v příkladu je obnovení po havárii obnovení konfigurace SAP HANA tři uzly Škálováním na více systémů.

![Průběh obnovení](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Pokud se zdá, že obnovení přestane reagovat na **Dokončit** obrazovky a neobsahuje zobrazit obrazovka průběhu, zkontrolujte, zda jsou spuštěny všechny instance SAP HANA na pracovních uzlech. V případě potřeby spusťte ručně instance SAP HANA.


### <a name="failback-from-a-dr-to-a-production-site"></a>Navrácení služeb po obnovení z zotavení po Havárii pro produkční lokality
Pro produkční lokality se může selhat zpět z zotavení po Havárii. Podívejme se na scénář způsobila převzetí služeb při selhání do lokalitě pro obnovení po havárii problémy v produkčním prostředí oblast Azure a ne vaše potřeba obnovit ztracené data. Jste už běží vaše úlohy produkční SAP chvíli v lokalitě pro obnovení po havárii. Jak se řeší problémy v produkční lokality, budete chtít navrácení služeb po obnovení provozního webu. Protože dojde ke ztrátě dat, krok zpět do pracoviště zahrnuje několik kroků a úzké spolupráci s SAP HANA týmu operace v Azure. Je pro vás k aktivaci provozní tým spustit synchronizaci zpět na pracoviště po vyřešení problémů.

Toto je posloupnost kroků:

1. SAP HANA na Azure provozní tým získá aktivační událost pro synchronizaci svazky úložiště produkční ze svazků úložiště pro zotavení po havárii, které teď představují provozního stavu. Tento stav Instance HANA velké jednotky v produkční lokality je vypnutý.
2. SAP HANA na Azure provozní tým monitoruje replikace a zajišťuje, že ho je zachycena před vás informuje o.
3. Vypnout aplikace, které používají provozní HANA Instance v lokalitě pro obnovení po havárii. Poté provedete zálohování protokolu transakcí HANA. V dalším kroku zastavíte HANA instance spuštěné v instanci HANA velké jednotky v lokalitě pro obnovení po havárii.
4. Po ukončení HANA instance spuštěné v instanci HANA velké jednotky v lokalitě pro obnovení po havárii, provozní tým ručně provede synchronizaci diskové svazky.
5. SAP HANA na Azure provozní tým znovu spustí instanci HANA velké jednotky v pracoviště a předá pro vás. Můžete zajistěte, aby byl instance SAP HANA ve stavu vypnutí v době spuštění jednotky HANA velké Instance.
6. Můžete provést stejný postup obnovení databáze, stejně jako při dříve přebírání služeb při selhání lokalitě pro obnovení po havárii.

### <a name="monitor-disaster-recovery-replication"></a>Monitorování replikace pro zotavení po havárii

Stav replikace úložiště průběh můžete sledovat spuštěním skriptu `azure_hana_replication_status.pl`. Tento skript musí spustit z jednotky spuštěné v umístění pro obnovení po havárii fungovat podle očekávání. Skript funguje bez ohledu na to, zda je replikace aktivní. Skript lze spustit pro každou jednotku velké Instance HANA klienta služby v umístění pro obnovení po havárii. Nelze získat podrobnosti o spouštěcí svazek.

Volání skriptu pomocí tohoto příkazu:
```
./replication_status.pl <HANA SID>
```

Výstup je rozdělit, dle svazku do následujících částí:  

- Stav odkazu
- Aktuální aktivity replikace
- Replikovat nejnovější snímku 
- Velikost nejnovější snímku
- Aktuální prodleva mezi snímků (mezi poslední dokončené snímku replikace a teď)

Stav odkazu zobrazuje jako **Active** Pokud propojení mezi umístění je mimo provoz nebo je aktuálně probíhající převzetím služeb při selhání. Aktivity replikace řeší, zda žádná data, je právě replikován nebo nečinné, nebo zda jiné aktivity se právě děje na odkaz. Poslední snímek replikovat objevit pouze jako `snapmirror…`. Velikost poslední snímek se následně zobrazí. Nakonec se zobrazí prodleva. Po dokončení replikace prodleva představuje čas z naplánované replikaci do. Prodleva může být větší než hodinu pro replikaci dat, zejména v počáteční replikace, i když má spustit replikaci. Časové zpoždění dál zvýšit, dokud nebude dokončeno probíhající replikace.

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












