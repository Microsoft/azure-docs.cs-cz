---
title: Zálohování a obnovení HANA v SAP HANA v Azure (velké instance) | Microsoft Docs
description: Jak provádět zálohování a obnovení HANA v SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42333a3feae19b6c3c77494f7e843cac1d9bc078
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968124"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>Zálohování a obnovení SAP HANA ve velkých instancích HANA

>[!IMPORTANT]
>Tento článek není náhradou za dokumentaci pro správu SAP HANA ani pro poznámky ke službě SAP. Očekáváme, že budete mít plnou znalost a odbornosti v SAP HANA správě a operacích, zejména pro zálohování, obnovení, vysokou dostupnost a zotavení po havárii. V tomto článku se zobrazí snímky obrazovky z SAP HANA studia. Obsah, struktura a povaha obrazovek nástrojů pro správu SAP a samotných nástrojů se můžou v SAP HANA vydání verze změnit.

Je důležité postupovat podle kroků a procesů pořízených ve vašem prostředí a ve verzích HANA a vydání. Některé procesy popsané v tomto článku jsou zjednodušené pro lepší porozumění. Nejsou určeny k použití jako podrobného postupu pro případné provozní Handbooks. Pokud chcete vytvořit operaci Handbooks pro vaše konfigurace, otestujte a vyzkoušejte procesy a zdokumentujte procesy související s vašimi konkrétní konfigurací. 

Jedním z nejdůležitějších aspektů provozních databází je jejich ochrana před závažnými událostmi. Příčinou těchto událostí může být něco z přírodních havárií až po jednoduché chyby uživatelů.

Zálohování databáze s možností obnovení do libovolného bodu v čase, například před tím, než někdo odstranil kritická data, umožňuje obnovení do stavu, který je co nejblíže k přerušení.

Chcete-li dosáhnout možnosti obnovení, je třeba provést dva typy záloh:

- Zálohy databáze: úplné, přírůstkové nebo rozdílové zálohy
- Zálohy protokolu transakcí

Kromě úplných záloh provedených na úrovni aplikace můžete provádět zálohování pomocí snímků úložiště. Snímky úložiště nenahrazují zálohy protokolu transakcí. Zálohy protokolu transakcí zůstávají důležité k obnovení databáze k určitému bodu v čase nebo k vyprázdnění protokolů z již potvrzených transakcí. Snímky úložiště můžou urychlit obnovení tím, že se rychle poskytne image s přesměrováním. 

SAP HANA v Azure (velké instance) nabízí dvě možnosti zálohování a obnovení:

- **Udělejte to sami (SVÉPOMOCNÁ).** Po zajistěte, aby bylo na disku dostatek místa, proveďte úplné zálohování databáze a protokolu pomocí jedné z následujících metod zálohování na disk. Můžete zálohovat buď přímo na svazky připojené k jednotkám velkých instancí HANA, nebo ke sdíleným složkám systému souborů NFS, které jsou nastavené na virtuálním počítači Azure (VM). V druhém případě si zákazníci nastavili virtuální počítač Linux v Azure, připojí Azure Storage k virtuálnímu počítači a nasdílí úložiště pomocí nakonfigurovaného serveru NFS na daném virtuálním počítači. Pokud provádíte zálohování u svazků, které se přímo připojují ke velkých jednotkám instance HANA, zkopírujte zálohy do účtu úložiště Azure. Tuto akci proveďte po nastavení virtuálního počítače Azure, který exportuje sdílené složky systému souborů NFS, které jsou založené na Azure Storage. Můžete také použít buď trezor Azure Backup, nebo službu Azure studené úložiště. 

   Další možností je použít nástroj pro ochranu dat třetí strany k uložení záloh po jejich zkopírování do účtu služby Azure Storage. Možnost zálohování SVÉPOMOCNÁ může být také nutná pro data, která je třeba uložit po delší dobu pro účely dodržování předpisů a auditování. Ve všech případech se zálohy zkopírují do sdílených složek NFS představovaných virtuálním počítačem a Azure Storage.

- **Funkce zálohování a obnovení infrastruktury** Můžete také využít funkce zálohování a obnovení, které poskytuje základní infrastruktura SAP HANA v Azure (velké instance). Tato možnost splní nutnost zálohování a rychlé obnovení. Zbytek této části popisuje funkce zálohování a obnovení, které jsou nabízeny s velkými instancemi HANA. Tato část také popisuje vztah, který zálohuje a obnovuje do funkce zotavení po havárii nabízených velkými instancemi HANA.

> [!NOTE]
>   Technologie snímků, kterou používá základní infrastruktura velkých instancí HANA, má závislost na SAP HANA snímcích. V tomto okamžiku SAP HANA snímky nefungují ve spojení s více klienty SAP HANAch kontejnerů víceklientské databáze. Pokud je nasazený jenom jeden tenant, SAP HANA snímky fungují a můžete použít tuto metodu.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Použití snímků úložiště SAP HANA v Azure (velké instance)

Infrastruktura úložiště, která je podkladová SAP HANA v Azure (velké instance), podporuje snímky úložiště svazků. Jsou podporovány zálohy i obnovení svazků, a to s následujícími požadavky:

- Namísto úplných záloh databáze se snímky svazků úložiště často probíhají.
- Když se snímek aktivuje přes/Hana/data a/Hana/Shared, což zahrnuje/usr/SAP, vyhájí technologie snímku SAP HANA snímek před spuštěním snímku úložiště. Tento snímek SAP HANA je bod nastavení pro pozdější obnovení protokolu po obnovení snímku úložiště. Aby byl snímek HANA úspěšný, potřebujete aktivní instanci HANA. Ve scénáři HSR se snímek úložiště nepodporuje na aktuálním sekundárním uzlu, kde nejde udělat snímek HANA.
- Po úspěšném spuštění snímku úložiště se snímek SAP HANA odstraní.
- Zálohy protokolu transakcí se často vycházejí a ukládají ve svazku/Hana/logbackups nebo v Azure. Můžete aktivovat svazek/Hana/logbackups, který obsahuje zálohy transakčního protokolu, a vytvořit snímek samostatně. V takovém případě nemusíte spouštět snímek HANA.
- Pokud je nutné obnovit databázi k určitému bodu v čase, požádejte o výpadky v provozu, že Microsoft Azure podporu nebo SAP HANA na obnovení Azure do určitého snímku úložiště. Příkladem je plánované obnovení systému izolovaného prostoru (sandbox) do původního stavu.
- Snímek SAP HANA, který je součástí snímku úložiště, je bod posunu pro použití záloh protokolu transakcí, které byly spuštěny a byly uloženy po pořízení snímku úložiště.
- Tyto zálohy protokolu transakcí jsou pořízeny k obnovení databáze zpět do určitého bodu v čase.

Můžete provádět snímky úložiště, které cílí na tři třídy svazků:

- Kombinovaný snímek přes/Hana/data a/Hana/Shared, který zahrnuje/usr/SAP. Tento snímek vyžaduje vytvoření snímku SAP HANA jako přípravu pro snímek úložiště. Snímek SAP HANA zajišťuje, že se databáze nachází v konzistentním stavu, a to z bodu úložiště zobrazení. V případě procesu obnovení je to bod, který je potřeba nastavit na.
- Samostatný snímek přes/Hana/logbackups.
- Oddíl operačního systému.

Nejnovější skripty a dokumentaci ke snímkům najdete na [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Když stáhnete balíček skriptu Snapshot z [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md), získáte tři soubory. Jeden ze souborů je zdokumentován ve formátu PDF pro poskytované funkce. Po stažení sady nástrojů postupujte podle pokynů v části "získání nástrojů Snapshot Tools".

## <a name="storage-snapshot-considerations"></a>Požadavky na snímky úložiště

>[!NOTE]
>Snímky úložiště spotřebují prostor úložiště, který je přidělený jednotkám velkých instancí HANA. Vezměte v úvahu následující aspekty plánování snímků úložiště a kolik snímků úložiště uchovat. 

Konkrétní mechanismy úložných snímků pro SAP HANA v Azure (velké instance) zahrnují:

- Konkrétní snímek úložiště v okamžiku, kdy je využito, spotřebovává málo úložiště.
- Když se změní obsah dat a obsah v SAP HANA datové soubory se na svazku úložiště změní, musí snímek uchovávat původní obsah bloku a data se změní.
- V důsledku toho se velikost snímku úložiště zvětšuje. Čím delší je snímek, tím větší snímek úložiště se stane.
- Čím více změn provedete na svazku SAP HANA databáze za dobu života snímku úložiště, tím větší spotřebu snímku úložiště.

SAP HANA v Azure (velké instance) se dodává s pevnými velikostmi svazků pro SAP HANA dat a svazků protokolů. Provádění snímků těchto svazků eats do prostoru svazku. Musíte:

- Určete, kdy se mají naplánovat snímky úložiště.
- Monitorujte spotřebu úložného prostoru svazků úložiště. 
- Spravujte počet snímků, které ukládáte. 

Snímky úložiště můžete zakázat, když buď importujete hmotnosti dat, nebo v databázi HANA provádět jiné významné změny. 


Následující části obsahují informace potřebné k provádění těchto snímků a zahrnutí obecných doporučení:

- I když hardware může tolerovat 255 snímků na jeden svazek, je dobré udržet se pod tímto číslem. Doporučení je 250 nebo méně.
- Než snímky úložiště provedete, sledujte a udržujte si přehled o volném místě.
- Snižte počet snímků úložiště na základě volného místa. Můžete snížit počet snímků, které zachováte, nebo můžete svazky zvětšit. Další úložiště můžete objednat v 1 až terabajtu jednotek.
- Při činnostech, jako je přesun dat do SAP HANA pomocí nástrojů SAP Platform Migration Tools (R3load) nebo obnovení databází SAP HANA ze záloh, zakažte na svazku/Hana/data snímky úložiště. 
- Během větších reorganizací SAP HANA tabulek Vyhněte se snímkům úložiště, pokud je to možné.
- Snímky úložiště jsou předpokladem pro využití možností zotavení po havárii SAP HANA v Azure (velké instance).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Předpoklady použití snímků samoobslužného úložiště

Aby se zajistilo, že se skript snímku úspěšně spustí, ujistěte se, že je v operačním systému Linux na serveru velkých instancí HANA nainstalovaný jazyk Perl. Jazyk Perl je předinstalován na jednotce velké instance služby HANA. Chcete-li zjistit verzi jazyka Perl, použijte následující příkaz:

`perl -v`

![Veřejný klíč se zkopíruje spuštěním tohoto příkazu.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Nastavení snímků úložiště

Pokud chcete nastavit snímky úložiště s velkými instancemi HANA, postupujte podle těchto kroků.
1. Ujistěte se, že je v operačním systému Linux na serveru velkých instancí HANA nainstalovaný jazyk Perl.
1. Úpravou \_ konfigurace/etc/ssh/ssh přidejte řádek _Mac HMAC-SHA1_.
1. Pro každou spuštěnou instanci SAP HANA a v případě potřeby vytvořte účet uživatele SAP HANA Backup na hlavním uzlu.
1. Nainstalujte klienta SAP HANA HDB na všechny servery Velké instance SAP HANA.
1. Na prvním Velké instance SAP HANA serveru každé oblasti vytvořte veřejný klíč pro přístup k základní infrastruktuře úložiště, která řídí vytváření snímků.
1. Zkopírujte skripty a konfigurační soubor z [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) do umístění **hdbsql** v instalaci SAP HANA.
1. Upravte soubor *HANABackupDetails.txt* podle potřeby pro příslušné specifikace zákazníka.

Získejte nejnovější skripty snímků a dokumentaci z [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Postup uvedený výše najdete v tématu [Microsoft Snapshot Tools for SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

### <a name="consideration-for-mcod-scenarios"></a>Zvážení pro scénáře MCOD
Pokud spustíte [scénář MCOD](https://launchpad.support.sap.com/#/notes/1681092) s více SAP HANA instancemi na jedné jednotce velkých instancí Hana, máte k dispozici samostatné svazky úložiště pro každou z SAP HANA instancí. Další informace o MDC a dalších požadavcích najdete v části "důležité věci, které je třeba si zapamatovat" v tématu [Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Krok 1: instalace klienta SAP HANA HDB

Operační systém Linux nainstalovaný na SAP HANA v Azure (velké instance) obsahuje složky a skripty potřebné ke spouštění SAP HANA snímků úložiště pro účely zálohování a zotavení po havárii. Podívejte se na další nejnovější verze v [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Je vaší zodpovědností nainstalovat klienta SAP HANA HDB do jednotek velkých instancí HANA při instalaci SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Krok 2: Změna konfigurace/etc/ssh/ssh \_

Tento krok je popsaný v tématu povolení komunikace s úložištěm v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Krok 3: vytvoření veřejného klíče

Pokud chcete povolit přístup k rozhraním snímků úložiště rozsáhlých instancí služby HANA, vytvořte přihlašovací proceduru prostřednictvím veřejného klíče. 

Na prvním SAP HANA na serveru Azure (velké instance) ve vašem tenantovi vytvořte veřejný klíč pro přístup k infrastruktuře úložiště. U veřejného klíče se pro přihlášení k rozhraním snímků úložiště nevyžaduje heslo. Nemusíte také spravovat přihlašovací údaje hesla pomocí veřejného klíče. 

Pokud chcete vygenerovat veřejný klíč, přečtěte si téma povolení komunikace s úložištěm v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-4-create-an-sap-hana-user-account"></a>Krok 4: vytvoření účtu SAP HANAho uživatele

Chcete-li zahájit vytváření SAP HANA snímků, vytvořte uživatelský účet v SAP HANA, který mohou používat skripty pro snímky úložiště. Pro tento účel vytvořte SAP HANA uživatelský účet v rámci aplikace SAP HANA Studio. Uživatel musí být vytvořen pod SYSTEMDB, *nikoli* v rámci databáze SID pro MDC. V prostředí s jedním kontejnerem je uživatel vytvořen v databázi tenanta. Tento účet musí mít oprávnění **ke čtení pro** **správce zálohování** a katalog. 

Pokud chcete nastavit a používat uživatelský účet, přečtěte si téma povolení komunikace s SAP HANA v [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Krok 5: ověření účtu SAP HANAho uživatele

V tomto kroku autorizujete SAP HANA uživatelský účet, který jste vytvořili, aby skripty nemusely odesílat hesla za běhu. Příkaz SAP HANA `hdbuserstore` umožňuje vytvoření klíče uživatele SAP HANA. Klíč je uložen na jednom nebo více SAP HANA uzlech. Klíč uživatele umožňuje uživateli přístup SAP HANA bez nutnosti spravovat hesla v rámci procesu skriptování. Postup skriptování je popsán dále v tomto článku.

>[!IMPORTANT]
>Spusťte tyto příkazy konfigurace se stejným uživatelským kontextem, ve kterém jsou spuštěny příkazy snímku. V opačném případě nebudou příkazy snímku správně fungovat.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Krok 6: získání skriptů snímku, konfigurace snímků a testování konfigurace a připojení

Stáhněte si nejnovější verzi skriptů z [GitHubu](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Způsob instalace skriptů se změnil pomocí verze 4,1 skriptů. Další informace najdete v části "povolení komunikace s SAP HANA" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Přesné pořadí příkazů najdete v tématu "Snadná instalace nástrojů Snapshot Tools (výchozí)" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Doporučujeme použít výchozí instalaci. 

Pokud chcete upgradovat z verze 3. x na 4,1, přečtěte si téma "upgrade existující instalace" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Pokud chcete odinstalovat sadu nástrojů 4,1, přečtěte si téma "Odinstalace nástrojů Snapshot Tools" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Nezapomeňte spustit kroky popsané v tématu "kompletní nastavení nástrojů Snapshot Tools" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Účel různých skriptů a souborů, které byly nainstalovány, jsou popsány v tématu "Jaké jsou tyto nástroje snímků?" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Před konfigurací nástrojů pro snímky se ujistěte, že jste správně nakonfigurovali taky záložní umístění a nastavení pro zálohování HANA. Další informace najdete v části "konfigurace SAP HANA" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Konfigurace sady nástrojů Snapshot je popsána v části config File-HANABackupCustomerDetails.txt v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>Test připojení pomocí SAP HANA

Až všechna konfigurační data vložíte do souboru *HANABackupCustomerDetails.txt* , ověřte, jestli jsou konfigurace pro data instance Hana správné. Použijte skript `testHANAConnection` , který je nezávisle na SAP HANA konfiguraci škálování nebo škálování na více instancí.

Další informace najdete v části "zjištění připojení pomocí SAP HANA-testHANAConnection" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-storage-connectivity"></a>Test připojení úložiště

Dalším krokem testu je zkontrolovat připojení k úložišti na základě dat, která jste umístili do konfiguračního souboru *HANABackupCustomerDetails.txt* . Pak spusťte testovací snímek. Před spuštěním příkazu je `azure_hana_backup` nutné spustit tento test. Posloupnost příkazů pro tento test najdete v části "Kontrola připojení pomocí úložiště – testStorageSnapshotConnection" "v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Po úspěšném přihlášení k rozhraní virtuálního počítače úložiště skript pokračuje ve fázi 2 a vytvoří testovací snímek. Výstup se tady zobrazuje pro konfiguraci SAP HANA škálování na více uzlů.

Pokud se snímek testu úspěšně spustí se skriptem, můžete naplánovat skutečné snímky úložiště. Pokud se to nepodaří, prozkoumejte problémy před tím, než ho přesunete vpřed. Snímek testu by měl zůstat v okolí, dokud se nevytvoří první reálné snímky.


### <a name="step-7-perform-snapshots"></a>Krok 7: provedení snímků

Po dokončení přípravných kroků můžete začít konfigurovat a naplánovat skutečné snímky úložiště. Skript, který má být naplánován, funguje s SAP HANA konfigurací škály nahoru a na více instancí. Pro pravidelné a pravidelné spouštění zálohovacího skriptu Naplánujte skript pomocí nástroje cron. 

Přesný syntax a funkčnost příkazu najdete v části "provedení zálohování snímků azure_hana_backup" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

Po spuštění skriptu se `azure_hana_backup` vytvoří snímek úložiště v následujících třech fázích:

1. Spustí SAP HANA snímek.
1. Spustí snímek úložiště.
1. Odstraní snímek SAP HANA, který byl vytvořen před spuštěním snímku úložiště.

Chcete-li spustit skript, zavolejte ho ze složky spustitelného HDB, do které byl zkopírován. 

Doba uchování je spravována s počtem snímků, které byly odeslány jako parametr při spuštění skriptu. Množství času, které jsou pokryty snímky úložiště, je funkce období provádění a počet snímků odeslaných jako parametr při spuštění skriptu. 

Pokud počet uchovávaných snímků překročí počet, který je pojmenován jako parametr ve volání skriptu, před spuštěním nového snímku se odstraní nejstarší snímek úložiště stejného popisku. Číslo, které udělíte jako poslední parametr volání, je číslo, které můžete použít k řízení počtu uchovávaných snímků. V tomto čísle můžete také nepřímo řídit místo na disku, které se používá pro snímky. 


## <a name="snapshot-strategies"></a>Strategie snímků
Frekvence snímků pro různé typy závisí na tom, zda používáte funkci zotavení po havárii velké instance HANA. Tato funkce spoléhá na snímky úložiště, které mohou vyžadovat speciální doporučení pro četnost a dobu provádění snímků úložiště. 

V následujících doporučeních a doporučeních *se předpokládá, že nepoužíváte* funkci zotavení po havárii, kterou velké instance Hana nabízí. Místo toho můžete snímky úložiště použít k zálohování a být schopné poskytnout obnovení k určitému bodu v čase za posledních 30 dní. V důsledku omezení počtu snímků a prostoru Vezměte v úvahu následující požadavky:

- Čas obnovení pro obnovení k bodu v čase.
- Využité místo.
- Čas bodu obnovení a doby obnovení jsou v důsledku případného zotavení po havárii.
- Konečné spuštění zálohování HANA s využitím úplných databází na discích. Pokaždé, když se provede úplná záloha na disky nebo **backint** , spuštění snímků úložiště se nezdařilo. Pokud máte v úmyslu spouštět zálohy úplné databáze na snímky úložiště, ujistěte se, že je spuštění snímků úložiště během této doby zakázáno.
- Počet snímků na svazek, které jsou omezeny na 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Pokud nepoužíváte funkci zotavení po havárii velkých instancí HANA, doba snímku je méně častá. V takových případech proveďte kombinované snímky na/Hana/data a/Hana/Shared, které zahrnují/usr/SAP, ve 12 hodin nebo ve 24hodinovém období. Ponechte snímky za měsíc. Totéž platí pro snímky svazku zálohy protokolu. Spuštění zálohování protokolu transakcí SAP HANA proti svazku zálohy protokolu probíhá v období 5 až 15 minut.

Plánované snímky úložiště se nejlépe provádějí pomocí cron. Stejný skript použijte pro všechny potřeby zálohování a zotavení po havárii. Upravte vstupy skriptu tak, aby odpovídaly různým požadovaným časům zálohování. Tyto snímky jsou v cron v závislosti na jejich spuštění v různých intervalech naplánované jinak. Může to být každou hodinu, každých 12 hodin, každý den nebo každý týden. 

Následující příklad ukazuje cron plán v/etc/CRONTAB:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
V předchozím příkladu pokrývá hodinový kombinovaný snímek svazky obsahující/Hana/data a/hana/shared/SID, které zahrnují/usr/SAP, umístění. Tento typ snímku použijte pro rychlejší obnovení k určitému bodu v čase během posledních dvou dnů. Na těchto svazcích je také denní snímek. To znamená, že na základě hodinových snímků a čtyř týdnů pokrytí na denní snímky máte dva dny pokrytí. Záložní svazek transakčního protokolu se taky zálohuje denně. Tyto zálohy jsou uchovávány po dobu čtyř týdnů. 

Jak vidíte ve třetí linii crontab, zálohování protokolu transakcí HANA je naplánováno na každých 5 minut. Časy spuštění různých úloh cron, které spouštějí snímky úložiště, se rozdělují. Tímto způsobem se snímky v určitém časovém okamžiku nespustí najednou. 

V následujícím příkladu provedete kombinovaný snímek, který pokrývá svazky obsahující/Hana/data a/hana/shared/SID, včetně/usr/SAP, umístění po hodinách. Tyto snímky zachováte po dobu dvou dnů. Snímky zálohovacích svazků protokolu transakcí běží na základě 5 minut a uchovávají se po dobu čtyř hodin. Stejně jako dřív je naplánováno spuštění zálohy souboru protokolu transakcí HANA každých 5 minut. 

Snímek zálohovacího svazku protokolu transakcí je proveden s prodlevou 2 – minut po zahájení zálohování protokolu transakcí. Za normálních okolností se zálohování SAP HANAho protokolu transakcí dokončí během těchto 2 minut. Stejně jako dřív se svazek obsahující spouštěcí logickou jednotku zálohuje jednou za den snímkem úložiště a uchovává se po dobu čtyř týdnů.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Následující obrázek znázorňuje sekvence předchozího příkladu. Spouštěcí logická jednotka (LUN) je vyloučena.

![Vztah mezi zálohováním a snímky](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA provádí pravidelné zápisy na svazek/Hana/log, aby dokumentoval potvrzené změny v databázi. V pravidelných intervalech SAP HANA zapisuje do svazku/Hana/data úložný bod. Jak je uvedeno v crontab, zálohování protokolu transakcí SAP HANA se spouští každých 5 minut. 

Také vidíte, že se snímek SAP HANA spouští každou hodinu v důsledku aktivace kombinovaného snímku úložiště přes svazky/Hana/data a/hana/shared/SID. Po úspěšném dokončení snímku HANA se spustí kombinovaný snímek úložiště. Podle pokynů v crontab se snímek úložiště na svazku/Hana/Logbackup spouští každých 5 minut přibližně 2 minuty po zálohování protokolu transakcí HANA.

> 

>[!IMPORTANT]
> Použití snímků úložiště pro zálohy SAP HANA je užitečné jenom v případě, že se snímky provádějí ve spojení s SAP HANA zálohování protokolů transakcí. Tyto zálohy protokolu transakcí musí pokrývat časová období mezi snímky úložiště. 

Pokud jste nastavili závazek pro uživatele obnovení k určitému bodu v čase po dobu 30 dnů, budete potřebovat:

- V extrémních případech získáte přístup k snímku kombinovaného úložiště přes/Hana/data a/hana/shared/SID, který je zastaralý na 30 dní. 
- Máte souvislé zálohy transakčních protokolů, které pokrývají dobu mezi libovolnými kombinovanými snímky úložiště. Nejstarší snímek svazku pro zálohování transakčního protokolu tedy musí být starší než 30 dní. Nejedná se o případ, pokud zkopírujete zálohy protokolu transakcí do jiné sdílené složky systému souborů NFS, která je umístěna na Azure Storage. V takovém případě můžete vyžádat staré zálohy protokolu transakcí z této sdílené složky systému souborů NFS.

Chcete-li využít snímky úložiště a případnou replikaci úložiště záloh protokolu transakcí, změňte umístění, do kterého SAP HANA zapisuje zálohy protokolu transakcí. Tuto změnu můžete v HANA Studio udělat. 

I když SAP HANA zálohuje úplné segmenty protokolů automaticky, zadejte interval zálohování protokolu, který chcete deterministický. To platí hlavně v případě, že použijete možnost zotavení po havárii, protože obvykle chcete spouštět zálohy protokolů s deterministické období. V tomto případě je 15 minut nastaveno jako interval zálohování protokolu.

![Plánování SAP HANAch záložních protokolů v SAP HANA studiu](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Můžete také zvolit zálohy, které jsou častěji než každých 15 minut. Častější nastavení se často používá ve spojení s funkcí zotavení po havárii velkých instancí HANA. Někteří zákazníci provádějí zálohy protokolu transakcí každých 5 minut.

Pokud databáze nikdy nebyla zálohována, posledním krokem je provedení zálohy databáze na základě souborů, aby bylo možné vytvořit jedinou položku zálohování, která musí existovat v katalogu záloh. V opačném případě SAP HANA nemůže iniciovat zadané zálohy protokolu.

![Vytvořením zálohy na základě souboru vytvořte jedinou položku zálohování.](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Po prvním úspěšném spuštění snímků úložiště odstraňte snímek testu, který byl spuštěn v kroku 6. Další informace najdete v tématu "odebrání testovacích snímků – removeTestStorageSnapshot" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitorování počtu a velikosti snímků na svazku disku

Na konkrétním svazku úložiště můžete monitorovat počet snímků a spotřebu úložiště těchto snímků. `ls`Příkaz nezobrazuje adresář snímků ani soubory. Příkaz Linux OS `du` zobrazuje podrobnosti o těchto snímcích úložiště, protože jsou uložené na stejných svazcích. Použijte příkaz s následujícími možnostmi:

- `du –sh .snapshot`: Tato možnost poskytuje celkový počet všech snímků v adresáři snímků.
- `du –sh --max-depth=1`: Tato možnost vypíše všechny snímky uložené ve složce **. Snapshot** a velikost každého snímku.
- `du –hc`: Tato možnost poskytuje celkovou velikost používanou všemi snímky.

Pomocí těchto příkazů se ujistěte, že se snímky, které se zabírají a ukládají, nespotřebovávají na svazcích všechna úložiště.

>[!NOTE]
>Snímky spouštěcí logické jednotky nejsou viditelné s předchozími příkazy.

### <a name="get-details-of-snapshots"></a>Získat podrobnosti o snímcích
Pokud chcete získat další informace o snímcích, použijte skript `azure_hana_snapshot_details` . Pokud je v umístění pro zotavení po havárii aktivní server, můžete tento skript spustit v libovolném umístění. Skript poskytuje následující výstup rozepsaný podle jednotlivých svazků, které obsahují snímky: 
   * Velikost celkového počtu snímků ve svazku
   * Následující podrobnosti v každém snímku tohoto svazku: 
      - Název snímku 
      - Vytvořit čas 
      - Velikost snímku
      - Frekvence snímku
      - ID zálohy HANA přidružené k tomuto snímku, pokud je relevantní

Syntaxi příkazů a výstupů najdete v tématu "výpis snímků – azure_hana_snapshot_details" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Snížení počtu snímků na serveru

Jak bylo vysvětleno dříve, můžete snížit počet určitých popisků, které jsou uloženy. Poslední dva parametry příkazu pro zahájení snímku jsou popisek a počet snímků, které chcete zachovat.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

V předchozím příkladu je popisek snímku **dailyhana**. Počet snímků s tímto popiskem, který se má zachovat, je **28**. Při reagování na využití místa na disku můžete chtít snížit počet uložených snímků. Snadný způsob, jak omezit počet snímků na 15, je třeba spustit skript s posledním parametrem nastaveným na **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Pokud spustíte skript s tímto nastavením, bude počet snímků, který obsahuje nový snímek úložiště, 15. Zachová se 15 nejnovějších snímků a odstraní se 15 starších snímků.

 >[!NOTE]
 > Tento skript zmenší počet snímků pouze v případě, že jsou snímky starší než jedna hodina. Skript neodstraní snímky, které jsou staré méně než jednu hodinu. Tato omezení souvisí s volitelnou funkcí zotavení po havárii, která je k dispozici.

Pokud již nechcete uchovávat sadu snímků s předponou zálohy **dailyhana** v příkladech syntaxe, spusťte skript s **0** jako číslo uchování. Odeberou se všechny snímky, které odpovídají danému popisku. Odebrání všech snímků může mít vliv na funkce zotavení po havárii velkých instancí HANA.

Druhou možností, jak odstranit konkrétní snímky, je použít skript `azure_hana_snapshot_delete` . Tento skript je navržený tak, aby odstranil snímek nebo sadu snímků buď pomocí ID zálohy HANA, jak se nachází v HANA Studio, nebo prostřednictvím samotného názvu snímku. V současné době je ID zálohy svázáno pouze se snímky vytvořenými pro typ snímku **Hana** . Zálohování snímků **protokolů** typu a **spuštění** neprovádí SAP HANA snímek, takže pro tyto snímky není k dispozici žádné ID zálohy. Pokud je zadaný název snímku, vyhledá všechny snímky na různých svazcích, které odpovídají zadanému názvu snímku. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Další informace o skriptu najdete v tématu "odstranění snímku-azure_hana_snapshot_delete" v [nástrojích Microsoft Snapshot Tools pro SAP HANA v Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Spusťte skript jako **kořenový** uživatel.

>[!IMPORTANT]
>Pokud máte data, která existují jenom na snímku, který chcete odstranit, po odstranění snímku se tato data ztratí trvale.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Obnovení na úrovni souborů z snímku úložiště

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
U typů snímků **Hana** a **protokolů** můžete přistupovat k snímkům přímo na svazcích v adresáři **. Snapshot** . Pro každý snímek je podadresář. Zkopírujte každý soubor ve stavu, v němž byl v místě snímku, z tohoto podadresáře do skutečné adresářové struktury. 

V aktuální verzi skriptu není k dispozici *žádný* skript pro obnovení pro obnovení snímku jako Samoobslužná služba. Obnovení snímku můžete provést v rámci samoobslužných skriptů pro obnovení po havárii během převzetí služeb při selhání v lokalitě pro obnovení po havárii. Pokud chcete obnovit požadovaný snímek z existujících dostupných snímků, je nutné, abyste se obrátili na provozní tým Microsoftu otevřením žádosti o služby.

>[!NOTE]
>U snímků spouštěcí jednotky LUN nezávisle na typu jednotek velkých instancí HANA nefunguje obnovení jediného souboru. Adresář **. Snapshot** není zveřejněn ve spouštěcí logické jednotce (LUN). 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Obnovit na nejnovější snímek HANA

V případě produkčního prostředí můžete proces obnovování ze snímku úložiště spustit jako incident zákazníka s podporou Microsoft Azure. Pokud se data v produkčním systému odstranila a jediný způsob, jak ji načíst, je to vysoká naléhavost, která se dá obnovit v provozní databázi.

V jiné situaci může být obnovení k určitému bodu v čase nízkou naléhavostí a plánované dny předem. Toto obnovení můžete naplánovat pomocí SAP HANA v Azure místo toho, abyste vyvolali příznak s vysokou prioritou. Můžete například naplánovat upgrade softwaru SAP pomocí nového balíčku pro vylepšení. Pak se budete muset vrátit ke snímku, který představuje stav před upgradem balíčku.

Před odesláním žádosti musíte připravit. SAP HANA v týmu Azure pak může žádost zpracovat a poskytnout obnovené svazky. Následně obnovíte databázi HANA na základě snímků.

Možnosti získání snímku obnoveného pomocí nové sady nástrojů najdete v části "postup obnovení snímku" v [Průvodci ručním obnovením pro SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Při přípravě na požadavek postupujte podle těchto kroků.

1. Rozhodněte, který snímek se má obnovit. Pokud neurčíte jinak, bude obnoven pouze svazek Hana/data. 

1. Vypněte instanci HANA.

   ![Vypnout instanci HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Odpojte datové svazky na jednotlivých uzlech databáze HANA. Pokud jsou datové svazky stále připojené k operačnímu systému, obnovení snímku se nepodaří.

   ![Odpojte datové svazky na všech uzlech databáze HANA.](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Otevřete žádost o podporu Azure a uveďte pokyny k obnovení konkrétního snímku:

   - Během obnovení: SAP HANA ve službě Azure vás vyzve, abyste se účastnili konferenčních hovorů, aby se ujistili, ověřili a zkontrolovali, jestli je obnovený správný snímek úložiště. 

   - Po obnovení: SAP HANA ve službě Azure vás upozorní, až se snímek úložiště obnoví.

1. Po dokončení procesu obnovení znovu připojte všechny datové svazky.

   ![Znovu připojit všechny datové svazky](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Další možností, jak získat SAP HANA datových souborů obnovených z snímku úložiště, je popsán v kroku 7 příručky pro [obnovení pro SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Postup obnovení ze zálohy snímku najdete v tématu [Ruční Průvodce obnovením pro SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Pokud jste snímek obnovili operacemi Microsoftu, nemusíte provádět krok 7.


### <a name="recover-to-another-point-in-time"></a>Obnovit do jiného bodu v čase
Chcete-li provést obnovení k určitému bodu v čase, přečtěte si téma "obnovení databáze k následujícímu časovému okamžiku" v tématu [Ruční Průvodce obnovením pro SAP HANA v Azure ze snímku úložiště](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 





## <a name="snapcenter-integration-in-sap-hana-large-instances"></a>Integrace SnapCenter v SAP HANA velkých instancích

V této části se dozvíte, jak můžou zákazníci používat software NetApp SnapCenter k pořízení snímků, zálohování a SAP HANA obnovení databází hostovaných ve Microsoft Azure HANA velkých instancí (HLI). 

SnapCenter nabízí řešení pro scénáře, včetně zálohování a obnovování, zotavení po havárii (DR) pomocí asynchronní replikace úložiště, replikace systému a klonování systému. V rámci integrace s Velké instance SAP HANA v Azure teď můžou zákazníci používat SnapCenter pro operace zálohování a obnovení.

Další informace naleznete v tématu NetApp TR-4614 a TR-4646 na SnapCenter.

- [SAP HANA zálohování a obnovení pomocí SnapCenter (TR-4614)](https://www.netapp.com/us/media/tr-4614.pdf)
- [SAP HANA zotavení po havárii pomocí replikace úložiště (TR-4646)](https://www.netapp.com/us/media/tr-4646.pdf)
- [SAP HANA HSR s SnapCenter (TR-4719)](https://www.netapp.com/us/media/tr-4719.pdf)
- [Klonování SAP z SnapCenter (TR-4667)](https://www.netapp.com/us/media/tr-4667.pdf)

### <a name="system-requirements-and-prerequisites"></a>Požadavky na systém a požadované součásti

Aby bylo možné spustit SnapCenter v Azure HLI, požadavky na systém zahrnují:
* SnapCenter Server v Azure Windows 2016 nebo novější se 4-vCPU, 16 GB paměti RAM a minimálně 650 GB správy Premium SSD Storage.
* Velké instance SAP HANA systém s 1,5 TB – 24 TB paměti RAM. Pro klonování operací a testů doporučujeme použít dva SAP HANA systémů velkých instancí.

Kroky pro integraci SnapCenter v SAP HANA jsou: 

1. Vyvolejte žádost o lístek podpory pro komunikaci veřejného klíče generovaného uživatelem s týmem společnosti Microsoft. To je nutné k nastavení uživatele SnapCenter pro přístup k systému úložiště.
1. Vytvořte virtuální počítač ve vaší virtuální síti, který má přístup k HLI; Tento virtuální počítač se používá pro SnapCenter. 
1. Stáhněte a nainstalujte SnapCenter. 
1. Operace zálohování a obnovení. 

### <a name="create-a-support-ticket-for-user-role-storage-setup"></a>Vytvoření lístku podpory pro instalaci úložiště role uživatele

1. Otevřete Azure Portal a přejděte na stránku **předplatná** . Po na stránce Předplatná vyberte předplatné SAP HANA, které je zobrazené červeně.

   :::image type="content" source="./media/snapcenter/create-support-case-for-user-role-storage-setup.png" alt-text="Vytvoření případu podpory pro instalaci uživatelského úložiště":::

1. Na stránce SAP HANA předplatné vyberte podstránku **skupiny prostředků** .

   :::image type="content" source="./media/snapcenter/solution-lab-subscription-resource-groups.png" alt-text="Skupina prostředků předplatného řešení testovacího prostředí" lightbox="./media/snapcenter/solution-lab-subscription-resource-groups.png":::

1. Vyberte příslušnou skupinu prostředků v oblasti.

   :::image type="content" source="./media/snapcenter/select-appropriate-resource-group-in-region.png" alt-text="Vyberte vhodnou skupinu prostředků v oblasti." lightbox="./media/snapcenter/select-appropriate-resource-group-in-region.png":::

1. Vyberte položku SKU, která odpovídá SAP HANA ve službě Azure Storage.

   :::image type="content" source="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png" alt-text="Vyberte položku SKU odpovídající SAP HANA" lightbox="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png":::

1. Otevřete novou žádost o **lístek podpory** , která je popsaný červeně.

   :::image type="content" source="./media/snapcenter/open-new-support-ticket-request.png" alt-text="Otevřít nový požadavek lístku podpory":::

1. Na kartě **základy** zadejte pro lístek následující informace:

   * **Typ problému:** Odbornou
   * **Předplatné:** Vaše předplatné
   * **Služba:** SAP HANA velká instance
   * **Prostředek:** Vaše skupina prostředků
   * **Shrnutí:** Zadejte uživatelem generovaný veřejný klíč.
   * **Typ problému:** Konfigurace a nastavení
   * **Podtyp problému:** Nastavení SnapCenter pro HLI


1. V **popisu** lístku podpory zadejte na kartě **Podrobnosti** : 
   
   * Nastavení SnapCenter pro HLI
   * Váš veřejný klíč pro uživatele SnapCenter (SnapCenter. pem) – Podívejte se na příklad vytvoření veřejného klíče.

     :::image type="content" source="./media/snapcenter/new-support-request-details.png" alt-text="Nová žádost o podporu – karta Podrobnosti" lightbox="./media/snapcenter/new-support-request-details.png":::

1. Vyberte **zkontrolovat + vytvořit** a zkontrolujte svůj lístek podpory. 

1. Vygenerujte certifikát pro uživatelské jméno SnapCenter na velké instanci HANA nebo na serveru Linux.

   SnapCenter vyžaduje uživatelské jméno a heslo pro přístup k virtuálnímu počítači úložiště (SVM) a k vytváření snímků databáze HANA. Společnost Microsoft používá veřejný klíč, který vám (zákazníkovi) umožní nastavit heslo pro přístup k systému úložiště.

   ```bash
   openssl req -x509 -nodes -days 1095 -newkey rsa:2048 -keyout snapcenter.key -out snapcenter.pem -subj "/C=US/ST=WA/L=BEL/O=NetApp/CN=snapcenter"
   Generating a 2048 bit RSA private key
   ................................................................................................................................................+++++
   ...............................+++++
   writing new private key to 'snapcenter.key'
   -----

   sollabsjct31:~ # ls -l cl25*
   -rw-r--r-- 1 root root 1704 Jul 22 09:59 snapcenter.key
   -rw-r--r-- 1 root root 1253 Jul 22 09:59 snapcenter.pem

   ```

1. Připojte soubor snapcenter. pem k lístku podpory a pak vyberte **vytvořit** .

   Po odeslání certifikátu veřejného klíče nastaví Microsoft uživatelské jméno SnapCenter pro vašeho tenanta společně s IP adresou SVM.   

1. Po přijetí SVM IP adresy nastavte heslo pro přístup k SVM, které ovládáte.

   Následuje příklad volání REST (dokumentace) z velké instance HANA nebo virtuálního počítače ve virtuální síti, který má přístup k prostředí velkých instancí HANA a použije se k nastavení hesla.

   ```bash
   curl --cert snapcenter.pem --key snapcenter.key -X POST -k "https://10.0.40.11/api/security/authentication/password" -d '{"name":"snapcenter","password":"test1234"}'
   ```

   Zajistěte, aby v systému HANA DB nebyla aktivní žádná proměnná proxy serveru.

   ```bash
   sollabsjct31:/tmp # unset http_proxy
   sollabsjct31:/tmp # unset https_proxy
   ```

### <a name="download-and-install-snapcenter"></a>Stažení a instalace SnapCenter
Když je teď uživatelské jméno nastavené tak, aby SnapCenter přístup k úložnému systému, použijete uživatelské jméno SnapCenter ke konfiguraci SnapCenter po jeho instalaci. 

Než nainstalujete SnapCenter, přečtěte SAP HANA si téma [zálohování a obnovení s SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf) a definujte strategii zálohování. 

1. Přihlaste se k [NetApp](https://mysupport.netapp.com) a [Stáhněte](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmysupport.netapp.com%2Fsite%2Fproducts%2Fall%2Fdetails%2Fsnapcenter%2Fdownloads-tab&data=02%7C01%7Cmadhukan%40microsoft.com%7Ca53f5e2f245a4e36933008d816efbb54%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637284566603265503&sdata=TOANWNYoAr1q5z1opu70%2FUDPHjluvovqR9AKplYpcpk%3D&reserved=0) si nejnovější verzi SnapCenter.

1. Nainstalujte SnapCenter na virtuální počítač s Windows Azure.

   Instalační program zkontroluje požadavky virtuálního počítače. 

   >[!IMPORTANT]
   >Věnujte pozornost velikosti virtuálního počítače, zejména ve větších prostředích.

1. Nakonfigurujte přihlašovací údaje uživatele pro SnapCenter. Ve výchozím nastavení naplní přihlašovací údaje uživatele systému Windows použité pro instalaci aplikace. 

   :::image type="content" source="media/snapcenter/installation-user-inputs-dialog.png" alt-text="Dialog uživatelských vstupů při instalaci"::: 

1. Když spustíte relaci, uloží se výjimka zabezpečení a spustí se grafické rozhraní (GUI).

1. Přihlaste se k SnapCenter na virtuálním počítači ( https://snapcenter-vm:8146) pomocí přihlašovacích údajů Windows nakonfigurujte prostředí.


### <a name="set-up-the-storage-system"></a>Nastavení systému úložiště

1. V SnapCenter vyberte **systém úložiště** a pak vyberte **+ Nová**. 

   :::image type="content" source="./media/snapcenter/snapcenter-storage-connections-window.png" alt-text="SnapCenter připojení úložiště" lightbox="./media/snapcenter/snapcenter-storage-connections-window.png":::

   Výchozí hodnota je jedna SVM na tenanta. Pokud má zákazník více tenantů nebo HLIs ve více oblastech, doporučujeme nakonfigurovat všechny SVMs v SnapCenter.

1. V části přidat systém úložiště zadejte informace o systému úložiště, který chcete přidat, uživatelské jméno a heslo SnapCenter a pak vyberte **Odeslat**.

   :::image type="content" source="./media/snapcenter/new-storage-connection.png" alt-text="Nové připojení úložiště":::

   >[!NOTE]
   >Výchozí hodnota je jedna SVM na tenanta.  Pokud existuje více tenantů, pak doporučujeme nakonfigurovat všechny SVMs zde v SnapCenter. 

1. V SnapCenter vyberte **hostitelé** a vyberte **+ Přidat** a nastavte modul plug-in HANA a hostitele databáze Hana.  Nejnovější verze SnapCenter detekuje databázi HANA na hostiteli automaticky.

   :::image type="content" source="media/snapcenter/managed-hosts-new-host.png" alt-text="V SnapCenter vyberte hostitelé a pak vyberte Přidat." lightbox="media/snapcenter/managed-hosts-new-host.png":::

1. Zadejte informace pro nového hostitele:
   1. Vyberte operační systém pro typ hostitele.
   1. Zadejte název hostitele virtuálního počítače SnapCenter.
   1. Zadejte přihlašovací údaje, které chcete použít.
   1. Vyberte možnosti **Microsoft Windows** a **SAP HANA** a pak vyberte **Odeslat**.

   :::image type="content" source="media/snapcenter/add-new-host-operating-system-credentials.png" alt-text="Informace pro nového hostitele":::

   >[!IMPORTANT]
   >Než budete moct nainstalovat první uzel, umožňuje SnapCenter instalovat moduly plug-in do databáze bez oprávnění uživatele root.  Informace o tom, jak povolit nekořenového uživatele, najdete v tématu [Přidání nekořenového uživatele a konfigurace oprávnění sudo](https://library.netapp.com/ecmdocs/ECMLP2590889/html/GUID-A3EEB5FC-242B-4C2C-B407-510E48A8F131.html).

1. Zkontrolujte podrobnosti o hostiteli a vyberte **Odeslat** a nainstalujte modul plug-in na server SnapCenter.

1. Po instalaci modulu plug-in v SnapCenter vyberte **hostitelé** a potom vyberte **+ Přidat** a přidejte uzel Hana.

   :::image type="content" source="media/snapcenter/add-hana-node.png" alt-text="Přidat uzel HANA" lightbox="media/snapcenter/add-hana-node.png":::

1. Zadejte informace pro uzel HANA:
   1. Vyberte operační systém pro typ hostitele.
   1. Zadejte název hostitele nebo IP adresu HANA DB.
   1. Tuto možnost vyberte **+** , pokud chcete přidat pověření nakonfigurovaná v operačním systému hostitele Hana DB a pak vyberte **OK**.
   1. Vyberte **SAP HANA** a pak vyberte **Odeslat**.

   :::image type="content" source="media/snapcenter/add-hana-node-details.png" alt-text="Podrobnosti o SAP HANA uzlu":::

1. Potvrďte otisk prstu a vyberte **Potvrdit a odeslat**.

   :::image type="content" source="media/snapcenter/confirm-submit-fingerprint.png" alt-text="Potvrzení a odeslání otisku prstu":::

1. V uzlu Hana v části systémová databáze vyberte **zabezpečení**  >  **Uživatelé**  >  **SNAPCENTER** a vytvořte uživatele SNAPCENTER.

   :::image type="content" source="media/snapcenter/create-snapcenter-user-hana-system-db.png" alt-text="Vytvoření uživatele SnapCenter v HANA (systémová databáze)":::



### <a name="auto-discovery"></a>Automatické zjišťování
SnapCenter 4,3 ve výchozím nastavení povoluje funkci automatického zjišťování.  Pro instance HANA s nakonfigurovanou replikací systému HANA (HSR) se nepodporuje automatické zjišťování. Instanci musíte do serveru SnapCenter přidat ručně.


### <a name="hana-setup-manual"></a>Instalace HANA (ruční)
Pokud jste nakonfigurovali HSR, musíte systém nakonfigurovat ručně.  

1. V SnapCenter vyberte **prostředky** a **San Hana** (v horní části) a pak vyberte **+ Přidat SAP HANA Database** (napravo).

   :::image type="content" source="media/snapcenter/manual-hana-setup.png" alt-text="Ruční instalace HANA" lightbox="media/snapcenter/manual-hana-setup.png":::

1. Zadejte podrobnosti prostředků uživatele správce HANA nakonfigurovaného na hostiteli se systémem Linux nebo na hostiteli, kde jsou moduly plug-in nainstalovány. Záloha bude spravována z modulu plug-in v systému Linux.

   :::image type="content" source="media/snapcenter/provide-resource-details-sap-hana-database.png" alt-text="Zadejte podrobnosti prostředků uživatele správce HANA nakonfigurovaného na hostiteli se systémem Linux.":::

1. Vyberte datový svazek, pro který potřebujete pořídit snímky, vyberte **Uložit** a pak vyberte **Dokončit**.

   :::image type="content" source="media/snapcenter/provide-storage-footprint.png" alt-text="Vyberte datový svazek, pro který potřebujete pořídit snímky, vyberte Uložit a pak vyberte Dokončit.":::

### <a name="create-a-snapshot-policy"></a>Vytvoření zásady snímku

Předtím, než použijete SnapCenter k zálohování prostředků databáze SAP HANA, je nutné vytvořit zásadu zálohování pro prostředek nebo skupinu prostředků, které chcete zálohovat. Během procesu vytváření zásady snímků budete mít možnost konfigurovat příkazy pre/post a speciální klíče SSL. Informace o tom, jak vytvořit zásady snímků, najdete v tématu [vytváření zásad zálohování pro databáze SAP HANA](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).

1. V SnapCenter vyberte **prostředky** a potom vyberte databázi.

   :::image type="content" source="media/snapcenter/select-database-create-policy.png" alt-text="V SnapCenter vyberte prostředky a potom vyberte databázi.":::

1. Použijte pracovní postup Průvodce konfigurací ke konfiguraci plánovače snímků.

   :::image type="content" source="media/snapcenter/follow-workflow-configuration-wizard.png" alt-text="Použijte pracovní postup Průvodce konfigurací ke konfiguraci plánovače snímků." lightbox="media/snapcenter/follow-workflow-configuration-wizard.png":::

1. Zadejte možnosti pro konfiguraci příkazů pre/post a speciálních klíčů SSL.  V tomto příkladu nepoužíváme žádná speciální nastavení.

   :::image type="content" source="media/snapcenter/configuration-options-pre-post-commands.png" alt-text="Zadejte možnosti pro konfiguraci příkazů před odesláním a speciálních klíčů SSL." lightbox="media/snapcenter/configuration-options-pre-post-commands.png":::

1. Vyberte **Přidat** a vytvořte zásadu snímku, která se dá použít taky pro jiné databáze Hana. 

   :::image type="content" source="media/snapcenter/select-one-or-more-policies.png" alt-text="Vyberte Přidat a vytvořte zásadu snímku, která se dá použít taky pro jiné databáze HANA.":::

1. Zadejte název zásady a popis.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy.png" alt-text="Zadejte název zásady a popis.":::


1. Vyberte typ a četnost zálohování.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-settings.png" alt-text="Vyberte typ a četnost zálohování.":::

1. Nakonfigurujte **nastavení uchovávání záloh na vyžádání**.  V našem příkladu nastavujeme uchovávání na tři kopie snímků, aby byly zachovány.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-retention-settings.png" alt-text="Nakonfigurujte nastavení uchovávání záloh na vyžádání.":::

1. Nakonfigurujte **Nastavení hodinového uchovávání**. 

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-hourly-retention-settings.png" alt-text="Nakonfigurujte nastavení hodinového uchovávání.":::

1. Pokud je nakonfigurované nastavení SnapMirror, **po vytvoření místní kopie snímku vyberte aktualizovat SnapMirror**.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-snapmirror.png" alt-text="Pokud se vyžaduje SnapMirror, vyberte aktualizovat SnapMirror po vytvoření místní kopie snímku.":::

1. Vyberte **Dokončit** a zkontrolujte souhrn nových zásad zálohování. 
1. V části **Konfigurovat plán** vyberte **Přidat**.

   :::image type="content" source="media/snapcenter/configure-schedules-for-selected-policies.png" alt-text="V části Konfigurovat plán vyberte Přidat.":::

1. Vyberte **počáteční datum**, datum **vypršení platnosti** a četnost.

   :::image type="content" source="media/snapcenter/add-schedules-for-policy.png" alt-text="Vyberte počáteční datum, datum vypršení platnosti a četnost.":::

1. Zadejte podrobnosti e-mailu pro oznámení.

   :::image type="content" source="media/snapcenter/backup-policy-notification-settings.png" alt-text="Zadejte podrobnosti e-mailu pro oznámení.":::

1.  Kliknutím na **Dokončit** vytvořte zásadu zálohování.

### <a name="disable-ems-message-to-netapp-autosupport"></a>Zakázat zprávu EMS pro NetApp autosupport
Ve výchozím nastavení je shromažďování dat EMS povolené a spouští se každých sedm dní od data instalace.  Shromažďování dat můžete zakázat pomocí rutiny prostředí PowerShell `Disable-SmDataCollectionEms` .

1. V PowerShellu navažte relaci s SnapCenter.

   ```powershell
   Open-SmConnection
   ```

1. Přihlaste se pomocí svých přihlašovacích údajů.
1. Zakáže shromažďování zpráv EMS.

   ```powershell
   Disable-SmCollectionEms
   ```

### <a name="restore-database-after-crash"></a>Obnovit databázi po chybě
K obnovení databáze můžete použít SnapCenter.  V této části se podíváme na nejdůležitější kroky, ale další informace najdete v tématu [SAP HANA zálohování a obnovení pomocí SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).


1. Zastavte databázi a odstraňte všechny soubory databáze.

   ```
   su - h31adm
   > sapcontrol -nr 00 -function StopSystem
   StopSystem
   OK
   > sapcontrol -nr 00 -function GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   hdbdaemon, HDB Daemon, GRAY, Stopped, , , 35902
 
   ```

1. Odpojte svazek databáze.

   ```bash
   unmount /hana/data/H31/mnt00001
   ```


1. Obnovte soubory databáze prostřednictvím SnapCenter.  Vyberte databázi a pak vyberte **obnovit**.  

   :::image type="content" source="media/snapcenter/restore-database-via-snapcenter.png" alt-text="Vyberte databázi a vyberte obnovit." lightbox="media/snapcenter/restore-database-via-snapcenter.png":::

1. Vyberte typ obnovení.  V našem příkladu obnovíme kompletní prostředek. 

   :::image type="content" source="media/snapcenter/restore-database-select-restore-type.png" alt-text="Vyberte typ obnovení.":::

   >[!NOTE]
   >Ve výchozím nastavení nemusíte zadávat příkazy pro místní obnovení z snímku na disku. 

   >[!TIP]
   >Pokud chcete v rámci svazku obnovit konkrétní logickou jednotku, vyberte **úroveň souboru**.

1. Sledujte pracovní postup prostřednictvím Průvodce konfigurací.
   
   SnapCenter obnoví data do původního umístění, aby bylo možné spustit proces obnovení v HANA. Navíc vzhledem k tomu, že SnapCenter nemůže změnit katalog záloh (databáze je mimo provoz), zobrazí se upozornění.

   :::image type="content" source="media/snapcenter/restore-database-job-details-warning.png" alt-text="Vzhledem k tomu, že SnapCenter nemůže změnit katalog záloh, zobrazí se upozornění. ":::

1. Vzhledem k tomu, že jsou všechny soubory databáze obnoveny, spusťte proces obnovení v HANA. V Hana Studio v části **systémy** klikněte pravým tlačítkem na databázi systému a vyberte **zálohování a obnovení**  >  **Systémová databáze**.

   :::image type="content" source="media/snapcenter/hana-studio-backup-recovery.png" alt-text="Zahajte proces obnovení v HANA.":::

1. Vyberte typ obnovení.

   :::image type="content" source="media/snapcenter/restore-database-select-recovery-type.png" alt-text="Vyberte typ obnovení.":::

1. Vyberte umístění katalogu záloh.

   :::image type="content" source="media/snapcenter/restore-database-select-location-backup-catalog.png" alt-text="Vyberte umístění katalogu záloh.":::

1. Vyberte zálohu pro obnovení databáze SAP HANA.

   :::image type="content" source="media/snapcenter/restore-database-select-backup.png" alt-text="Vyberte zálohu pro obnovení databáze SAP HANA.":::

   Po obnovení databáze se zobrazí zpráva s **časem obnovení** a **obnovením do razítka umístění protokolu** .

1. V části **systémy** klikněte pravým tlačítkem na databázi systému a vyberte **zálohování a obnovení**  >  **databáze tenanta**.
1. Dokončete obnovení databáze tenanta podle pracovního postupu Průvodce. 

Další informace o obnovení databáze najdete v tématu [SAP HANA zálohování a obnovení pomocí SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).


### <a name="non-database-backups"></a>Zálohy, které nejsou databázemi
Můžete obnovit nedatové svazky, například síťovou sdílenou složku (/Hana/Shared) nebo zálohu operačního systému.  Další informace o obnovení svazku, který není datovým svazkem, najdete v tématu [SAP HANA zálohování a obnovení pomocí SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf).

### <a name="sap-hana-system-cloning"></a>Klonování systému SAP HANA

Než budete moct klonovat, musíte mít nainstalovanou stejnou verzi HANA jako zdrojová databáze. Identifikátor SID a ID se mohou lišit. 

:::image type="content" source="media/snapcenter/system-cloning-diagram.png" alt-text="Klonování systému SAP HANA" lightbox="media/snapcenter/system-cloning-diagram.png" border="false":::

1. Vytvoření úložiště uživatelů databáze HANA pro databázi H34 z/usr/sap/H34/HDB40.

   ```
   hdbuserstore set H34KEY sollabsjct34:34013 system manager
   ```
 
1. Zakažte bránu firewall.

   ```bash
   systemctl disable SuSEfirewall2
   systemctl stop  SuSEfirewall2
   ```

1. Nainstalujte sadu Java SDK.

   ```bash
   zypper in java-1_8_0-openjdk
   ```

1. Do SnapCenter přidejte cílového hostitele, na kterém bude klon připojen. Další informace najdete v tématu [přidání hostitelů a instalace balíčků modulů plug-in na vzdálených hostitelích](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html).
   1. Zadejte informace pro přihlašovací údaje spustit jako, které chcete přidat. 
   1. Vyberte hostitelský operační systém a zadejte informace o hostiteli.
   1. V části **moduly plug-in, které chcete nainstalovat** vyberte verzi, zadejte cestu instalace a vyberte **SAP HANA**.
   1. Vyberte **ověřit** a spusťte kontrolu před instalací.

1. Zastavte HANA a odpojte starý datový svazek.  Klon budete připojovat z SnapCenter.  

   ```bash
   sapcontrol -nr 40 -function StopSystem
   umount /hana/data/H34/mnt00001

   ```
 1. Vytvořte konfiguraci a soubory skriptu prostředí pro cíl.
 
    ```bash
    mkdir /NetApp
    chmod 777 /NetApp
    cd NetApp
    chmod 777 sc-system-refresh-H34.cfg
    chmod 777 sc-system-refresh.sh

    ```

    >[!TIP]
    >Z SnapCenter můžete kopírovat skripty z [klonování SAP](https://www.netapp.com/us/media/tr-4667.pdf).

1. Upravte konfigurační soubor. 

   ```bash
   vi sc-system-refresh-H34.cfg
   ```

   * HANA_ARCHITECTURE = "MDC_single_tenant"
   * KEY = "H34KEY"
   * TIME_OUT_START = 18
   * TIME_OUT_STOP = 18
   * INSTANCENO = "40"
   * STORAGE = "10.250.101.33"

1. Upravte soubor skriptu prostředí.

   ```bash
   vi sc-system-refresh.sh
   ```  

   * VERBOSE = ne
   * MY_NAME = " `basename $0` "
   * BASE_SCRIPT_DIR = " `dirname $0` "
   * MOUNT_OPTIONS = "RW, příčné = 4, tvrdý, Timeo = 600, rsize = 1048576, wsize = 1048576, intr, noatime, možností NOLOCK"

1. Spusťte klonování z procesu zálohování. Vyberte hostitele, pro který chcete vytvořit klon. 

   >[!NOTE]
   >Další informace najdete v tématu [klonování ze zálohy](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

1. V části **skripty** zadejte následující:

   * **Mount – příkaz:** /NetApp/SC-System-Refresh.sh Mount H34% hana_data_h31_mnt00001_t250_vol_Clone
   * **Příkaz po klonování:** /NetApp/SC-System-Refresh.sh Recover H34

1. Zakázat (uzamknout) automatické připojení v/etc/fstab, protože datový svazek předinstalované databáze není nezbytný. 

   ```bash
   vi /etc/fstab
   ```

### <a name="delete-a-clone"></a>Odstranění klonu

Klon můžete odstranit, pokud již není nutný. Další informace najdete v tématu [odstranění klonů](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html).

Příkazy, které se použijí ke spuštění před odstraněním klonu, jsou:
* **Odstranění předem naklonování:** /NetApp/SC-System-Refresh.sh vypnout H34
* **Odpojit:** /NetApp/SC-System-Refresh.sh umount H34

Tyto příkazy umožňují SnapCenter databázi Showdown, odpojí svazek a odstraní položku fstab.  Pak se FlexClone odstraní. 

### <a name="cloning-database-logfile"></a>Klonování databázového souboru protokolu

```   
20190502025323###sollabsjct34###sc-system-refresh.sh: Adding entry in /etc/fstab.
20190502025323###sollabsjct34###sc-system-refresh.sh: 10.250.101.31:/Sc21186309-ee57-41a3-8584-8210297f791d /hana/data/H34/mnt00001 nfs rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
20190502025323###sollabsjct34###sc-system-refresh.sh: Mounting data volume.
20190502025323###sollabsjct34###sc-system-refresh.sh: mount /hana/data/H34/mnt00001
20190502025323###sollabsjct34###sc-system-refresh.sh: Data volume mounted successfully.
20190502025323###sollabsjct34###sc-system-refresh.sh: chown -R h34adm:sapsys /hana/data/H34/mnt00001
20190502025333###sollabsjct34###sc-system-refresh.sh: Recover system database.
20190502025333###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/HDB40/exe/Python/bin/python /usr/sap/H34/HDB40/exe/python_support/recoverSys.py --command "RECOVER DATA USING SNAPSHOT CLEAR LOG"
[140278542735104, 0.005] >> starting recoverSys (at Thu May  2 02:53:33 2019)
[140278542735104, 0.005] args: ()
[140278542735104, 0.005] keys: {'command': 'RECOVER DATA USING SNAPSHOT CLEAR LOG'}
recoverSys started: ============2019-05-02 02:53:33 ============
testing master: sollabsjct34
sollabsjct34 is master
shutdown database, timeout is 120
stop system
stop system: sollabsjct34
stopping system: 2019-05-02 02:53:33
stopped system: 2019-05-02 02:53:33
creating file recoverInstance.sql
restart database
restart master nameserver: 2019-05-02 02:53:38
start system: sollabsjct34
2019-05-02T02:53:59-07:00  P010976      16a77f6c8a2 INFO    RECOVERY state of service: nameserver, sollabsjct34:34001, volume: 1, RecoveryPrepared
recoverSys finished successfully: 2019-05-02 02:54:00
[140278542735104, 26.490] 0
[140278542735104, 26.490] << ending recoverSys, rc = 0 (RC_TEST_OK), after 26.485 secs
20190502025400###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is started ....
20190502025400###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025410###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025420###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025430###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025440###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025451###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502025451###sollabsjct34###sc-system-refresh.sh: SAP HANA database is started.
20190502025451###sollabsjct34###sc-system-refresh.sh: Recover tenant database H34.
20190502025451###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/SYS/exe/hdb/hdbsql -U H34KEY RECOVER DATA FOR H34 USING SNAPSHOT CLEAR LOG
0 rows affected (overall time 69.584135 sec; server time 69.582835 sec)
20190502025600###sollabsjct34###sc-system-refresh.sh: Checking availability of Indexserver for tenant H34.
20190502025601###sollabsjct34###sc-system-refresh.sh: Recovery of tenant database H34 succesfully finished.
20190502025601###sollabsjct34###sc-system-refresh.sh: Status: GREEN
Deleting the DB Clone – Logfile
20190502030312###sollabsjct34###sc-system-refresh.sh: Stopping HANA database.
20190502030312###sollabsjct34###sc-system-refresh.sh: sapcontrol -nr 40 -function StopSystem HDB

02.05.2019 03:03:12
StopSystem
OK
20190502030312###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is stopped ....
20190502030312###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030322###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030332###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030342###sollabsjct34###sc-system-refresh.sh: Status:  GRAY
20190502030342###sollabsjct34###sc-system-refresh.sh: SAP HANA database is stopped.
20190502030347###sollabsjct34###sc-system-refresh.sh: Unmounting data volume.
20190502030347###sollabsjct34###sc-system-refresh.sh: Junction path: Sc21186309-ee57-41a3-8584-8210297f791d
20190502030347###sollabsjct34###sc-system-refresh.sh: umount /hana/data/H34/mnt00001
20190502030347###sollabsjct34###sc-system-refresh.sh: Deleting /etc/fstab entry.
20190502030347###sollabsjct34###sc-system-refresh.sh: Data volume unmounted successfully.

```

### <a name="uninstall-snapcenter-plug-ins-package-for-linux"></a>Odinstalace balíčku modulů plug-in SnapCenter pro Linux

Balíček modulů plug-in pro Linux můžete odinstalovat z příkazového řádku. Vzhledem k tomu, že automatické nasazení očekává nový systém, je snadné modul plug-in odinstalovat.  

>[!NOTE]
>Možná budete muset odinstalovat starší verzi modulu plug-in ručně. 

Odinstalujte moduly plug-in.

```bash
cd /opt/NetApp/snapcenter/spl/installation/plugins
./uninstall
```

Nejnovější modul plug-in HANA teď můžete nainstalovat na nový uzel tak, že vyberete **Odeslat** v SnapCenter. 




## <a name="next-steps"></a>Další kroky
- Viz [Principy a příprava zotavení po havárii](hana-concept-preparation.md).
