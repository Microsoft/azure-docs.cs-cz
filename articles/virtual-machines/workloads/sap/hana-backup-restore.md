---
title: Záloha a obnovení HANA v SYSTÉMU SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Jak provést zálohování a obnovení HANA v SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72430078"
---
# <a name="backup-and-restore"></a>Zálohování a obnovení

>[!IMPORTANT]
>Tento článek není náhradou za dokumentaci pro správu SAP HANA nebo poznámky SAP. Očekáváme, že máte solidní znalosti a odborné znalosti v sap HANA správy a operací, zejména pro zálohování, obnovení, vysokou dostupnost a zotavení po havárii. V tomto článku jsou zobrazeny snímky obrazovky z SAP HANA Studio. Obsah, struktura a povaha obrazovek nástrojů pro správu SAP a samotných nástrojů se mohou změnit z vydání SAP HANA na vydání.

Je důležité, abyste provedli kroky a procesy provedené ve vašem prostředí a s verzemi a verzemi HANA. Některé procesy popsané v tomto článku jsou zjednodušeny pro lepší obecné pochopení. Nejsou určeny k použití jako podrobné kroky pro případné operace příručky. Chcete-li vytvořit provozní příručky pro vaše konfigurace, otestujte a procvičujte své procesy a dokumentujte procesy související s vašimi konkrétními konfiguracemi. 

Jedním z nejdůležitějších aspektů provozních databází je jejich ochrana před katastrofickými událostmi. Příčinou těchto událostí může být cokoli od přírodních katastrof až po jednoduché uživatelské chyby.

Zálohování databáze, se schopností obnovit ji do libovolného bodu v čase, například před tím, než někdo odstranil důležitá data, umožňuje obnovení do stavu, který je co nejblíže způsobu, jakým byl před přerušením.

K dosažení možnosti obnovení musí být provedeny dva typy záloh:

- Zálohy databáze: Úplné, přírůstkové nebo rozdílové zálohy
- Zálohy transakčních protokolů

Kromě záloh celé databáze prováděných na úrovni aplikace můžete provádět zálohování se snímky úložiště. Snímky úložiště nenahrazují zálohy protokolu transakcí. Zálohy protokolu transakcí zůstávají důležité obnovit databázi do určitého bodu v čase nebo vyprázdnit protokoly z již potvrzených transakcí. Snímky úložiště můžete urychlit obnovení tím, že rychle poskytuje obrázek převrácení databáze. 

SAP HANA v Azure (velké instance) nabízí dvě možnosti zálohování a obnovení:

- **Udělej to sám (DIY).** Poté, co se ujistíte, že je dostatek místa na disku, proveďte úplné zálohování databáze a protokolování pomocí jedné z následujících metod zálohování disku. Můžete zálohovat buď přímo na svazky připojené k jednotkám velké instance HANA nebo ke sdíleným položkám služby NFS, které jsou nastavené ve virtuálním počítači Azure (VM). V druhém případě zákazníci nastavit virtuální počítač s Linuxem v Azure, připojit Azure Storage k virtuálnímu počítači a sdílet úložiště prostřednictvím nakonfigurovaného serveru NFS v tomto virtuálním počítači. Pokud provedete zálohování proti svazkům, které se přímo připojují k jednotkám velké instance HANA, zkopírujte zálohy do účtu úložiště Azure. To po nastavení virtuálního počítače Azure, který exportuje sdílené složky služby NFS, které jsou založené na Azure Storage. Můžete také použít trezor Azure Backup nebo azure cold storage. 

   Další možností je použití nástroje ochrany dat jiného výrobce k uložení záloh po jejich zkopírování do účtu úložiště Azure. Možnost zálohování diy může být také nezbytná pro data, která potřebujete uložit po delší dobu pro účely dodržování předpisů a auditování. Ve všech případech se zálohy zkopírují do sdílených složek systému souborů NFS reprezentovaných prostřednictvím virtuálního počítače a úložiště Azure.

- **Funkce zálohování a obnovení infrastruktury.** Můžete také použít funkce zálohování a obnovení, které základní infrastruktury SAP HANA v Azure (velké instance) poskytuje. Tato možnost splňuje potřebu zálohování a rychlé obnovení. Zbytek této části řeší funkce zálohování a obnovení, které jsou nabízeny s hana velké instance. Tato část také popisuje vztah, který zálohování a obnovení mají na funkci zotavení po havárii, kterou nabízí velké instance HANA.

> [!NOTE]
>   Technologie snímek, který se používá základní infrastruktury hana velké instance má závislost na snímky SAP HANA. V tomto okamžiku snímky SAP HANA nefungují ve spojení s více tenanty SAP HANA víceklientské databázové kontejnery. Pokud je nasazen pouze jeden klient, snímky SAP HANA fungují a můžete použít tuto metodu.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Použití snímků úložiště SAP HANA v Azure (velké instance)

Infrastruktura úložiště, která je základem SAP HANA v Azure (velké instance), podporuje snímky svazků úložiště. Zálohování a obnovení svazků je podporováno s následujícími poznámkami:

- Namísto úplné zálohy databáze, snímky svazku úložiště jsou pořizovat často.
- Při spuštění snímku přes /hana/data a /hana/shared, který zahrnuje /usr/sap, svazky, technologie snímek zahájí snímek SAP HANA před spuštěním snímku úložiště. Tento snímek SAP HANA je instalační bod pro případné obnovení protokolu po obnovení snímku úložiště. Aby byl snímek HANA úspěšný, potřebujete aktivní instanci HANA. Ve scénáři HSR snímek úložiště není podporována na aktuální sekundární uzel, kde nelze provést snímek HANA.
- Po úspěšném spuštění snímku úložiště snímek SAP HANA snímek je odstraněn.
- Zálohy transakčníprotokol jsou často převzaty a uloženy ve svazku /hana/logbackups nebo v Azure. Můžete aktivovat svazek /hana/logbackups, který obsahuje zálohy protokolu transakcí a pořizovat snímek samostatně. V takovém případě není nutné spustit snímek HANA.
- Pokud je nutné obnovit databázi do určitého bodu v čase, pro výpadek produkčního prostředí, požádejte, aby Podpora Microsoft Azure nebo SAP HANA v Azure obnovit na určitý snímek úložiště. Příkladem je plánovaná obnova izolovaného prostoru do původního stavu.
- Snímek SAP HANA, který je součástí snímku úložiště, je bod odsazení pro použití záloh protokolu transakcí, které byly spuštěny a byly uloženy po pořízení snímku úložiště.
- Tyto zálohy transakční protokol jsou převzaty k obnovení databáze zpět do určitého bodu v čase.

Můžete provádět snímky úložiště, které cílí na tři třídy svazků:

- Kombinovaný snímek přes /hana/data a /hana/shared, který zahrnuje /usr/sap. Tento snímek vyžaduje vytvoření snímku SAP HANA jako příprava na snímek úložiště. Snímek SAP HANA zajišťuje, že databáze je v konzistentním stavu z hlediska úložiště. Pro proces obnovení, to je bod nastavit na.
- Samostatný snímek přes /hana/logbackups.
- Oddíl operačního systému.

Chcete-li získat nejnovější snímek skripty a dokumentaci, viz [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Když si stáhnete balíček skriptu snímku z [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md), získáte tři soubory. Jeden ze souborů je dokumentován v PDF pro poskytnuté funkce. Po stažení sady nástrojů postupujte podle pokynů v části Získání nástrojů pro snímek.

## <a name="storage-snapshot-considerations"></a>Důležité informace o snímku úložiště

>[!NOTE]
>Snímky úložiště spotřebovávají úložný prostor, který je přidělen jednotkám velké instance HANA. Zvažte následující aspekty plánování snímků úložiště a kolik snímků úložiště zachovat. 

Konkrétní mechaniky snímků úložiště pro SAP HANA v Azure (velké instance) zahrnují:

- Konkrétní snímek úložiště v okamžiku, kdy je přijata spotřebovává malé úložiště.
- Jako změny obsahu dat a obsah v datových souborech SAP HANA změnit na svazku úložiště, snímek musí uložit původní obsah bloku a změny dat.
- V důsledku toho se zvětšuje velikost snímku úložiště. Čím déle snímek existuje, tím větší snímek úložiště se stane.
- Čím více změn, které jsou provedeny na svazku databáze SAP HANA po dobu životnosti snímku úložiště, tím větší je spotřeba místa snímku úložiště.

SAP HANA v Azure (velké instance) je dodáván s pevnými velikostmi svazků pro data SAP HANA a svazky protokolu. Provádění snímků těchto svazků se nažere do prostoru svazku. Musíte:

- Určete, kdy naplánovat snímky úložiště.
- Sledujte spotřebu místa svazků úložiště. 
- Spravujte počet snímků, které ukládáte. 

Snímky úložiště můžete zakázat při importu mas dat nebo provedení jiných významných změn v databázi HANA. 


Následující části poskytují informace pro provádění těchto snímků a obsahují obecná doporučení:

- Přestože hardware může udržet 255 snímků na svazek, chcete zůstat hluboko pod tímto číslem. Doporučení je 250 nebo méně.
- Před provedením snímků úložiště sledujte a sledujte volné místo.
- Snižte počet snímků úložiště na základě volného místa. Můžete snížit počet snímků, které chcete zachovat, nebo můžete rozšířit svazky. Můžete si objednat další úložiště v jednotkách 1 terabajtů.
- Během aktivit, jako je přesun dat do SAP HANA pomocí nástrojů pro migraci platformy SAP (R3load) nebo obnovení databází SAP HANA ze záloh, zakažte snímky úložiště na svazku /hana/data. 
- Během větší reorganizace sap hana tabulek, vyhnout se snímky úložiště, pokud je to možné.
- Snímky úložiště jsou předpokladem pro využití možností zotavení po havárii SAP HANA v Azure (velké instance).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Předpoklady pro použití samoobslužných snímků úložiště

Chcete-li se ujistit, že skript snímku běží úspěšně, ujistěte se, že Perl je nainstalován na operačním systému Linux na serveru hana velké instance. Perl je předinstalován na jednotce velké instance HANA. Chcete-li zkontrolovat verzi perlu, použijte následující příkaz:

`perl -v`

![Veřejný klíč je zkopírován spuštěním tohoto příkazu.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Nastavení snímků úložiště

Chcete-li nastavit snímky úložiště pomocí velkých instancí HANA, postupujte takto.
1. Ujistěte se, že je Perl nainstalován v operačním systému Linux na serveru velkých instancí HANA.
1. Upravte konfigura)/etc/ssh/ssh\_config, aby se přidala _čára MACs hmac-sha1_.
1. Vytvořte záložní uživatelský účet SAP HANA v hlavním uzlu pro každou instanci SAP HANA, kterou spustíte, pokud je to možné.
1. Nainstalujte klienta SAP HANA HDB na všechny servery VELKÝCH INStanCí SAP HANA.
1. Na prvním serveru SAP HANA Large Instances v každé oblasti vytvořte veřejný klíč pro přístup k základní infrastruktuře úložiště, která řídí vytváření snímků.
1. Zkopírujte skripty a konfigurační soubor z [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) do umístění **hdbsql** v instalaci SAP HANA.
1. Podle potřeby upravte soubor *HANABackupDetails.txt* podle příslušných specifikací zákazníka.

Získejte nejnovější snímky skripty a dokumentaci z [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Kroky uvedené výše najdete v [tématu Nástroje pro snímek Microsoftu pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Zohlednění scénářů MCOD
Pokud spustíte [scénář MCOD](https://launchpad.support.sap.com/#/notes/1681092) s více instancemi SAP HANA na jedné jednotce velké instance HANA, máte zřízeny samostatné svazky úložiště pro každou instanci SAP HANA. Další informace o MDC a další důležité informace naleznete v části Důležité věci k zapamatování v [nástrojích snímek Microsoftpro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: Instalace klienta SAP HANA HDB

Operační systém Linux nainstalovaný na SAP HANA v Azure (velké instance) obsahuje složky a skripty potřebné ke spuštění snímků úložiště SAP HANA pro účely zálohování a zotavení po havárii. Zkontrolujte novější verze na [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Je vaší odpovědností nainstalovat klienta SAP HANA HDB na jednotky velké instance HANA při instalaci SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Krok 2: Změna konfigurace /etc/ssh/ssh\_

Tento krok je popsán v "Povolit komunikaci s úložištěm" v [nástrojích snímek Microsoft pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Krok 3: Vytvoření veřejného klíče

Chcete-li povolit přístup k rozhraní snímku úložiště vašeho tenanta velké instance HANA, vytvořte postup přihlášení prostřednictvím veřejného klíče. 

Na prvním serveru SAP HANA v Azure (velké instance) ve vašem tenantovi vytvořte veřejný klíč pro přístup k infrastruktuře úložiště. S veřejným klíčem není nutné heslo k přihlášení k rozhraní mašle úložiště snímek. Také není nutné udržovat přihlašovací údaje hesla s veřejným klíčem. 

Pokud chcete generovat veřejný klíč, přečtěte si informace o povolení komunikace s úložištěm v [nástrojích snímků Microsoftu pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: Vytvoření uživatelského účtu SAP HANA

Chcete-li spustit vytváření snímků SAP HANA, vytvořte uživatelský účet v SAP HANA, který mohou používat skripty snímků úložiště. Vytvořte pro tento účel uživatelský účet SAP HANA v rámci aplikace SAP HANA Studio. Uživatel musí být vytvořen v rámci SYSTEMDB a *nikoli* v databázi SID pro MDC. V prostředí jednoho kontejneru je uživatel vytvořen v databázi klienta. Tento účet musí mít oprávnění **správce zálohování** a **čtení katalogu.** 

Pokud chcete nastavit a používat uživatelský účet, přečtěte si informace o povolení komunikace se SAP HANA na [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: Autorizace uživatelského účtu SAP HANA

V tomto kroku autorizujete uživatelský účet SAP HANA, který jste vytvořili, aby skripty nemusely odesílat hesla za běhu. Příkaz `hdbuserstore` SAP HANA umožňuje vytvoření uživatelského klíče SAP HANA. Klíč je uložen na jednom nebo více uzlech SAP HANA. Uživatelský klíč umožňuje uživateli přístup sap HANA bez nutnosti spravovat hesla z v rámci procesu skriptování. Skriptovací proces je popsána dále v tomto článku.

>[!IMPORTANT]
>Spusťte tyto konfigurační příkazy se stejným kontextem uživatele, ve které jsou spuštěny příkazy snímku. V opačném případě nebudou příkazy snímku fungovat správně.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: Získejte skripty snímků, nakonfigurujte snímky a otestujte konfiguraci a připojení

Stáhněte si nejnovější verzi skriptů z [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). Způsob instalace skriptů se změnil s verzí skriptů 4.1. Další informace naleznete v tématu "Povolit komunikaci se SAP HANA" v [nástrojích snímků Microsoftu pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Přesné pořadí příkazů naleznete v části Snadná instalace nástrojů snímků (výchozí)" v [nástrojích snímek Microsoft pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Doporučujeme použít výchozí instalaci. 

Pokud chcete upgradovat z verze 3.x na 4.1, přečtěte si informace o inovaci existující instalace v [nástrojích pro snímek Microsoftu pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)v Azure . Chcete-li odinstalovat sadu nástrojů 4.1, přečtěte si část "Odinstalace nástrojů pro snímek snímků" v [nástrojích snímků Microsoftu pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)v Azure .

Nezapomeňte spustit kroky popsané v části "Kompletní nastavení nástrojů snímek" v [nástrojích snímek Microsoft pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Účel různých skriptů a souborů při instalaci je popsán v části "Jaké jsou tyto nástroje pro snímky?" v [nástrojích Microsoft snímek pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Před konfigurací nástroje snímek, ujistěte se, že jste také nakonfigurovali hana zálohovací umístění a nastavení správně. Další informace naleznete v tématu "SAP HANA Configuration" v [nástrojích snímků Microsoftu pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)v Azure .

Konfigurace sady nástrojů snímek je popsána v části "Config file - HANABackupCustomerDetails.txt" v [nástrojích microsoft snapshot pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)v Azure .

#### <a name="test-connectivity-with-sap-hana"></a>Testování připojení pomocí SAP HANA

Po umístění všech konfiguračních dat do souboru *HANABackupCustomerDetails.txt* zkontrolujte, zda jsou konfigurace správné pro data instance HANA. Použijte skript `testHANAConnection`, který je nezávislý na konfiguraci SAP HANA scale-up nebo scale-out.

Další informace naleznete v tématu Kontrola připojení s SAP HANA - testHANAConnection " v [nástrojích snímek Microsoft pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Testování připojení úložiště

Dalším testovacím krokem je kontrola připojení k úložišti na základě dat, která vložíte do konfiguračního souboru *HANABackupCustomerDetails.txt.* Potom spusťte snímek testu. Před spuštěním `azure_hana_backup` příkazu je nutné spustit tento test. Pořadí příkazů pro tento test najdete v tématu "Kontrola připojení s úložištěm – testStorageSnapshotConnection", v [nástrojích snímek Microsoftu pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)v Azure .

Po úspěšném přihlášení k rozhraní virtuálního počítače úložiště skript pokračuje s fází 2 a vytvoří snímek testu. Výstup je zde zobrazen pro konfiguraci škálování na třech uzlových výstupech SAP HANA.

Pokud testovací snímek úspěšně spustí se skriptem, můžete naplánovat skutečné snímky úložiště. Pokud není úspěšná, prozkoumejte problémy dříve, než se posunete vpřed. Snímek testu by měl zůstat až do první skutečné snímky jsou provedeny.


### <a name="step-7-perform-snapshots"></a>Krok 7: Provádění snímků

Po dokončení přípravných kroků můžete začít konfigurovat a naplánovat skutečné snímky úložiště. Skript, který má být naplánován, pracuje s konfiguracemi SAP HANA scale-up a scale-out. Pro pravidelné a pravidelné provádění zálohovacího skriptu naplánujte skript pomocí nástroje cron. 

Pro přesnou syntaxi příkazu a funkce, najdete v části "Provést zálohování snímků - azure_hana_backup" v [nástrojích snímek Microsoft pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Při spuštění `azure_hana_backup` skriptu vytvoří snímek úložiště v následujících třech fázích:

1. Spustí snímek SAP HANA.
1. Spustí snímek úložiště.
1. Odebere snímek SAP HANA, který byl vytvořen před spuštěním snímku úložiště.

Chcete-li skript spustit, zavolejte jej ze složky spustitelného souboru HDB, do které byl zkopírován. 

Doba uchování je spravována s počtem snímků, které jsou odeslány jako parametr při spuštění skriptu. Množství času, který je pokrytsnímky úložiště je funkce období provádění a počet snímků odeslaných jako parametr při spuštění skriptu. 

Pokud počet snímků, které jsou uchovávány překročí číslo, které jsou pojmenovány jako parametr ve volání skriptu, nejstarší snímek úložiště stejného popisku je odstraněn před spuštěním nového snímku. Číslo, které zadáváte jako poslední parametr volání, je číslo, které můžete použít k řízení počtu snímků, které jsou uchovávány. Pomocí tohoto čísla můžete také nepřímo řídit místo na disku, které se používá pro snímky. 


## <a name="snapshot-strategies"></a>Strategie snímků
Frekvence snímků pro různé typy závisí na tom, zda používáte hana velké instance zotavení po havárii funkce. Tato funkce závisí na snímky úložiště, které mohou vyžadovat zvláštní doporučení pro frekvence a spuštění období snímků úložiště. 

V úvahách a doporučeních, které následují, předpokladem je, že *nepoužíváte* funkce zotavení po havárii, které hana velké instance nabízí. Místo toho použijete snímky úložiště k zálohování a budete moci poskytnout obnovení v čase za posledních 30 dní. Vzhledem k omezení počtu snímků a prostoru zvažte následující požadavky:

- Doba obnovení pro obnovení v čase.
- Využité místo.
- Bod obnovení a cíle doby obnovení pro potenciální zotavení z havárie.
- Případné spuštění záloh celé databáze HANA proti diskům. Vždy, když je provedena záloha celé databáze proti disky nebo **backint** rozhraní, provádění snímků úložiště se nezdaří. Pokud máte v plánu spustit zálohy plné databáze nad snímky úložiště, ujistěte se, že provádění snímků úložiště je zakázáno během této doby.
- Počet snímků na svazek, který je omezen na 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Pokud nepoužíváte funkce zotavení po havárii hana velké instance, snímek období je méně časté. V takových případech proveďte kombinované snímky na /hana/data a /hana/shared, který zahrnuje /usr/sap, v 12 hodin nebo 24 hodin. Uchovávejte snímky po dobu jednoho měsíce. Totéž platí pro snímky svazku zálohování protokolu. Spuštění záloh protokolu transakcí SAP HANA proti svazku zálohování protokolu dochází v 5 minut až 15 minut období.

Naplánované snímky úložiště se nejlépe provádějí pomocí cronu. Stejný skript používejte pro všechny zálohy a potřeby zotavení po havárii. Upravte vstupy skriptu tak, aby odpovídaly různým požadovaným časům zálohování. Tyto snímky jsou naplánovány odlišně v cron v závislosti na jejich době provádění. To může být každou hodinu, každých 12 hodin, denně, nebo týdně. 

Následující příklad ukazuje cron plán v /etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
V předchozím příkladu hodinový kombinovaný snímek pokrývá svazky, které obsahují /hana/data a /hana/shared/SID, který zahrnuje /usr/sap, umístění. Tento typ snímku použijte pro rychlejší obnovení bodu v čase v posledních dvou dnech. K dispozici je také denní snímek na těchto svazcích. Takže máte dva dny pokrytí podle hodinových snímků plus čtyři týdny pokrytí denními snímky. Svazek zálohování protokolu transakcí je také zálohován denně. Tyto zálohy jsou uchovávány po dobu čtyř týdnů. 

Jak vidíte ve třetím řádku crontab, zálohování protokolu transakcí HANA je naplánováno spuštění každých 5 minut. Počáteční časy různých úloh cron, které spouštějí snímky úložiště, jsou rozloženy. Tímto způsobem snímky nespustí najednou najednou v určitém okamžiku v čase. 

V následujícím příkladu provedete kombinovaný snímek, který pokrývá svazky, které obsahují /hana/data a /hana/shared/SID, který zahrnuje /usr/sap, umístění na hodinovém základě. Ty momentky si necháváš dva dny. Snímky svazků zálohování protokolu transakcí spustit na základě 5 minut a jsou uchovávány po dobu čtyř hodin. Stejně jako dříve je naplánováno spuštění zálohy souboru protokolu transakcí HANA každých 5 minut. 

Snímek svazku zálohování protokolu transakcí se provádí s 2 minut zpoždění po spuštění zálohy protokolu transakcí. Za normálních okolností zálohování protokolu transakcí SAP HANA dokončí během těchto 2 minut. Stejně jako dříve je svazek, který obsahuje spouštěcí logickou jednotku, zálohován jednou denně snímkem úložiště a je uchováván po dobu čtyř týdnů.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Následující obrázek znázorňuje sekvence předchozího příkladu. Spouštěcí logická jednotka je vyloučena.

![Vztah mezi zálohami a snímky](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA provádí pravidelné zápisy proti svazku /hana/log dokumentovat potvrzené změny v databázi. V pravidelných intervalech SAP HANA zapisuje uložený bod do svazku /hana/data. Jak je uvedeno v crontab záloha sap hana transakční protokol běží každých 5 minut. 

Také uvidíte, že snímek SAP HANA běží každou hodinu v důsledku aktivaci snímku kombinovanéúložiště přes /hana/data a /hana/shared/SID svazky. Po úspěšném snímku HANA, spustí snímek kombinované úložiště. Podle pokynů v crontab, snímek úložiště na svazku /hana/logbackup se spustí každých 5 minut, přibližně 2 minuty po zálohování protokolu transakcí HANA.

> 

>[!IMPORTANT]
> Použití snímků úložiště pro zálohy SAP HANA je cenné pouze v případě, že snímky jsou prováděny ve spojení se zálohami protokolu transakcí SAP HANA. Tyto zálohy protokolu transakcí je třeba pokrýt časové období mezi snímky úložiště. 

Pokud jste nastavili závazek vůči uživatelům 30denního obnovení v čase, musíte:

- Přístup k kombinované úložiště snímek přes /hana/data a /hana/shared/SID, který je 30 dní starý, v extrémních případech. 
- Mají souvislé zálohy protokolu transakcí, které pokrývají čas mezi libovolným snímky kombinovaného úložiště. Takže nejstarší snímek svazku zálohování transakční protokol musí být 30 dní starý. To není případ, pokud zkopírujete zálohy protokolu transakcí do jiné sdílené složky nfs, který je umístěn na Azure Storage. V takovém případě můžete vyžádat staré zálohy transakčníprotokol z této sdílené složky systému souborů NFS.

Chcete-li těžit ze snapshotů úložiště a případné replikace úložiště záloh protokolu transakcí, změňte umístění, do kterého SAP HANA zapisuje zálohy protokolu transakcí. Tuto změnu můžete provést v HANA Studio. 

Přestože SAP HANA zálohuje úplné segmenty protokolu automaticky, zadejte interval zálohování protokolu, který má být deterministický. To platí zejména při použití možnosti zotavení po havárii, protože obvykle chcete spustit zálohy protokolu s deterministickým obdobím. V následujícím případě je jako interval zálohování protokolu nastaveno 15 minut.

![Naplánovat protokoly zálohování SAP HANA v SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Můžete také zvolit zálohy, které jsou častější než každých 15 minut. Častější nastavení se často používá ve spojení s funkcí zotavení po havárii hana velké instance. Někteří zákazníci provádět zálohování protokolu transakcí každých 5 minut.

Pokud databáze nebyla nikdy zálohována, posledním krokem je provedení zálohy databáze založené na souborech a vytvoření jedné zálohovací položky, která musí existovat v rámci katalogu záloh. V opačném případě SAP HANA nelze zahájit zadané zálohprotokolu.

![Vytvoření zálohy založené na souborech pro vytvoření jedné zálohovací položky](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po spuštění první úspěšné snímky úložiště odstraňte snímek testu, který běžel v kroku 6. Další informace naleznete v tématu "Remove test snapshots – removeTestStorageSnapshot" v [nástrojích snímek Microsoftu pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Sledování počtu a velikosti snímků na svazku disku

Na určitém svazku úložiště můžete sledovat počet snímků a spotřebu úložiště těchto snímků. Příkaz `ls` nezobrazuje adresář nebo soubory snímku. Příkaz `du` operačního systému Linux zobrazuje podrobnosti o těchto snímcích úložiště, protože jsou uloženy na stejných svazcích. Použijte příkaz s následujícími možnostmi:

- `du –sh .snapshot`: Tato možnost poskytuje celkem všechny snímky v adresáři snímek.
- `du –sh --max-depth=1`: Tato možnost uvádí všechny snímky, které jsou uloženy ve složce **.snapshot** a velikost každého snímku.
- `du –hc`: Tato možnost poskytuje celkovou velikost používanou všemi snímky.

Pomocí těchto příkazů se ujistěte, že snímky, které jsou pořízeny a uloženy nespotřebovávají veškeré úložiště na svazcích.

>[!NOTE]
>Snímky spouštěcí logické jednotky nejsou viditelné pomocí předchozích příkazů.

### <a name="get-details-of-snapshots"></a>Získejte podrobnosti o snímcích
Chcete-li získat další podrobnosti `azure_hana_snapshot_details`o snímky, použijte skript . Tento skript můžete spustit v obou umístěních, pokud je v umístění zotavení po havárii aktivní server. Skript poskytuje následující výstup, rozdělený podle každého svazku, který obsahuje snímky: 
   * Velikost celkového počtu snímků ve svazku
   * Následující podrobnosti v každém snímku v tomto svazku: 
      - Název snímku 
      - Vytvořit čas 
      - Velikost snímku
      - Frekvence snímku
      - HANA ID zálohování přidružené k tomuto snímku, pokud je to relevantní

Syntaxe příkazu a výstupů najdete v části Seznam snímků – azure_hana_snapshot_details" v [nástrojích snímků Microsoftu pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)v Azure . 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Snížení počtu snímků na serveru

Jak již bylo vysvětleno, můžete snížit počet určitých popisků snímků, které ukládáte. Poslední dva parametry příkazu k zahájení snímku jsou popisek a počet snímků, které chcete zachovat.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

V předchozím příkladu je popisek snímku **dailyhana**. Počet snímků s tímto popiskem, které mají být uchovávány, je **28**. Při reakci na spotřebu místa na disku můžete snížit počet uložených snímků. Snadný způsob, jak snížit počet snímků na 15, například, je spustit skript s posledním parametrem nastaveným na **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Pokud spustíte skript s tímto nastavením, počet snímků, který zahrnuje nový snímek úložiště, je 15. 15 nejnovější snímky jsou zachovány a 15 starší snímky jsou odstraněny.

 >[!NOTE]
 > Tento skript snižuje počet snímků pouze v případě, že jsou snímky starší než jedna hodina. Skript neodstraní snímky, které jsou méně než jednu hodinu staré. Tato omezení se vztahují k volitelné funkce zotavení po havárii nabízené.

Pokud již nechcete udržovat sadu snímků s předponou zálohování **dailyhana** v příkladech syntaxe, spusťte skript s **0** jako číslo uchování. Všechny snímky, které odpovídají tomuto popisku jsou pak odebrány. Odebrání všech snímků může ovlivnit možnosti funkce zotavení po havárii hana velké instance.

Druhou možností pro odstranění konkrétních snímků `azure_hana_snapshot_delete`je použití skriptu . Tento skript je určen k odstranění snímku nebo sadu snímků pomocí ID zálohy HANA, jak je uvedeno v HANA Studio nebo prostřednictvím samotného názvu snímku. V současné době id zálohy je vázána pouze na snímky vytvořené pro typ snímku **hana.** Zálohy snímků **protokolů** typu a **spuštění** neprovádějí snímek SAP HANA, takže pro tyto snímky není k dispozici žádné ID zálohy. Pokud je zadán název snímku, vyhledá všechny snímky na různých svazcích, které odpovídají zadanému názvu snímku. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Další informace o skriptu najdete v tématu "Odstranit snímek - azure_hana_snapshot_delete" v [nástrojích snímek Microsoft pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Spusťte skript jako **kořen**uživatele .

>[!IMPORTANT]
>Pokud existují data, která existuje pouze na snímku, který chcete odstranit, po odstranění snímku, tato data se navždy ztratí.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Obnovení na úrovni souboru ze snímku úložiště

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Pro typy snímků **hana** a **protokoly**, můžete přistupovat k snímky přímo na svazcích v adresáři **.snapshot.** Pro každý snímek je podadresář. Zkopírujte každý soubor ve stavu, ve který se nachází v bodě snímku z tohoto podadresáře, do skutečné adresářové struktury. 

V aktuální verzi skriptu neexistuje *žádný* skript pro obnovení snímku k dispozici jako samoobslužné. Obnovení snímku lze provést jako součást samoobslužných skriptů pro zotavení po havárii v lokalitě zotavení po havárii během převzetí služeb při selhání. Chcete-li obnovit požadovaný snímek z existujících dostupných snímků, musíte kontaktovat operační tým společnosti Microsoft otevřením žádosti o službu.

>[!NOTE]
>Obnovení jednoho souboru nefunguje pro snímky spouštěcí logické jednotky nezávisle na typu jednotek velké instance HANA. Adresář **.snapshot** není v spouštěcí logické jednotce vystaven. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Obnovit nejnovější snímek HANA

Ve scénáři produkčního prostředí lze proces obnovení ze snímku úložiště spustit jako incident zákazníka s podporou Microsoft Azure. Je to vysoce naléhavá záležitost, pokud byla data odstraněna v produkčním systému a jediný způsob, jak je načíst, je obnovit produkční databázi.

V jiné situaci může být zotavení v okamžiku nízké naléhavosti a plánované dny předem. Toto obnovení můžete naplánovat pomocí SAP HANA v Azure namísto zvýšení příznaku s vysokou prioritou. Můžete například naplánovat upgrade softwaru SAP použitím nového balíčku vylepšení. Potom je třeba vrátit k snímek, který představuje stav před upgrade balíčku vylepšení.

Před odesláním žádosti se musíte připravit. Sap HANA v týmu Azure pak můžete zpracovat požadavek a poskytnout obnovené svazky. Poté obnovíte databázi HANA na základě snímků.

Možnosti obnovení snímku pomocí nové sady nástrojů najdete v tématu "Jak obnovit snímek" v [příručce ruční obnovení pro SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Chcete-li se připravit na požadavek, postupujte takto.

1. Rozhodněte, který snímek chcete obnovit. Pokud nedáte pokyn jinak, obnoví se pouze svazek hana/data. 

1. Vypněte instanci HANA.

   ![Vypnutí instance HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odpojte datové svazky v každém uzlu databáze HANA. Pokud jsou datové svazky stále připojeny k operačnímu systému, obnovení snímku se nezdaří.

   ![Odpojit datové svazky v každém uzlu databáze HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otevřete žádost o podporu Azure a uveďte pokyny k obnovení konkrétního snímku:

   - Během obnovení: SAP HANA ve službě Azure service vás může požádat o účast na konferenčním hovoru koordinovat, ověřovat a potvrzovat, že je obnoven správný snímek úložiště. 

   - Po obnovení: SAP HANA ve službě Azure vás upozorní při obnovení snímku úložiště.

1. Po dokončení procesu obnovení znovu připojte všechny svazky dat.

   ![Opětovné připojení všech datových svazků](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Další možnost získání například datových souborů SAP HANA, které byly obnoveny ze snímku úložiště, je popsána v kroku 7 v [příručce pro ruční obnovení pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)v Azure ze snímku úložiště .

Obnovení ze zálohy snímek, najdete [v tématu ruční obnovení průvodce SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Pokud byl váš snímek obnoven operacemi společnosti Microsoft, není nutné provést krok 7.


### <a name="recover-to-another-point-in-time"></a>Obnovit do jiného bodu v čase
Chcete-li obnovit do určitého bodu v čase, naleznete v tématu "Obnovení databáze do následujícího bodu v čase" v [příručce ruční obnovení pro SAP HANA](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)v Azure ze snímku úložiště . 


## <a name="next-steps"></a>Další kroky
- Viz [Zásady zotavení po havárii a příprava](hana-concept-preparation.md).
