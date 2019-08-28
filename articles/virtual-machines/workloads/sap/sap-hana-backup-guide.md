---
title: Průvodce zálohováním pro SAP HANA v Azure Virtual Machines | Microsoft Docs
description: Průvodce zálohováním pro SAP HANA poskytuje dvě hlavní možnosti zálohování pro SAP HANA na virtuálních počítačích Azure.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 05a4b8e8034e1c354a4209244694aeb2fc2c6007
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70078750"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Průvodce zálohováním pro SAP HANA na Azure Virtual Machines

## <a name="getting-started"></a>Začínáme

Průvodce zálohováním pro SAP HANA běžící na virtuálních počítačích Azure bude popsat jenom Témata specifická pro Azure. Obecné položky související s SAP HANA zálohování najdete v dokumentaci k SAP HANA (viz _dokumentace SAP HANA zálohování_ dále v tomto článku).

Tento článek se zaměřuje na dvě hlavní možnosti zálohování SAP HANA na virtuálních počítačích Azure:

- Zálohování HANA do systému souborů na virtuálním počítači Azure Linux (viz [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md))
- Zálohování HANA na základě snímků úložiště pomocí funkce snímku objektu BLOB služby Azure Storage ručně nebo Azure Backup služby (viz [SAP HANA zálohování na základě snímků úložiště](sap-hana-backup-storage-snapshots.md))

SAP HANA nabízí záložní rozhraní API, které umožňuje nástrojům pro zálohování třetích stran integrovat přímo s SAP HANA. (To není v rozsahu tohoto průvodce.) Neexistuje žádná přímá integrace SAP HANA se službou Azure Backup, která je teď dostupná na základě tohoto rozhraní API.

SAP HANA se oficiálně podporuje u různých typů virtuálních počítačů Azure, jako je Azure M-Series. Úplný seznam SAP HANA certifikovaných virtuálních počítačů Azure najdete v části [vyhledání certifikovaných platforem IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Tento článek bude aktualizován, jakmile budou k dispozici nové nabídky pro SAP HANA v Azure.

V Azure je k dispozici také SAP HANA hybridní řešení, ve kterém SAP HANA spouští nevirtualizované na fyzických serverech. Tato SAP HANA příručka pro Azure Backup se ale zabývá čistě prostředím Azure, ve kterém SAP HANA běží na virtuálním počítači Azure, &quot;a ne SAP HANA spouštění na velkých instancích.&quot; Další informace o tomto řešení zálohování na &quot;velkých instancích&quot; založených na snímcích úložiště najdete v tématu [SAP Hana (velké instance) přehled a architektura v Azure](hana-overview-architecture.md) .

Obecné informace o produktech SAP podporovaných v Azure najdete v poznámkách ke [sap 1928533](https://launchpad.support.sap.com/#/notes/1928533).

Následující tři číslice poskytují přehled o SAP HANA možností zálohování pomocí nativních možností Azure a také nabízí tři možné budoucí scénáře zálohování. Související články [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) a [zálohování SAP HANA na základě snímků úložiště](sap-hana-backup-storage-snapshots.md) popíší tyto možnosti podrobněji, včetně požadavků na velikost a výkon pro SAP HANA zálohování s více terabajty.

![Tento obrázek ukazuje dvě možnosti uložení aktuálního stavu virtuálního počítače.](media/sap-hana-backup-guide/image001.png)

Na tomto obrázku vidíte možnost uložení aktuálního stavu virtuálního počítače, a to buď prostřednictvím služby Azure Backup, nebo ručním snímkem disků virtuálních počítačů. Díky tomuto přístupu je potřeba,&#39;aby jeden nemusel spravovat SAP HANA zálohy. Výzvou ke scénáři snímku disku je konzistence systému souborů a stav disku konzistentního vzhledem k aplikacím. Téma o konzistenci je popsáno v části _SAP HANA konzistenci dat při pořizování snímků úložiště_ dále v tomto článku. Možnosti a omezení služby Azure Backup související se zálohováním SAP HANA jsou také popsány dále v tomto článku.

![Tento obrázek ukazuje možnosti pro převzetí SAP HANAho zálohování souborů uvnitř virtuálního počítače.](media/sap-hana-backup-guide/image002.png)

Na tomto obrázku jsou uvedené možnosti pro pořizování záloh souborů SAP HANA v rámci virtuálního počítače a jejich ukládání do záložních souborů HANA jinde pomocí různých nástrojů. Pořízení zálohy HANA vyžaduje více času než řešení zálohování založené na snímcích, ale má i výhody týkající se integrity a konzistence. Další podrobnosti najdete dále v tomto článku.

![Na tomto obrázku je znázorněný možný budoucí scénář zálohování SAP HANA.](media/sap-hana-backup-guide/image003.png)

Na tomto obrázku je znázorněný možný budoucí scénář zálohování SAP HANA. Pokud SAP HANA povolit vytváření záloh z sekundární replikace, přidá další možnosti pro strategie zálohování. V současné době není možné provést na základě příspěvku na wikiwebu SAP HANA:

_&quot;Je možné vytvářet zálohy na sekundární straně?_

_Ne, v současné době můžete provádět zálohování dat a protokolů jenom na primární straně. Pokud je povoleno automatické zálohování protokolu, po převzetí na sekundární stranu budou do tohoto pole zapsány i zálohy protokolu.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Prostředky SAP pro zálohování HANA

### <a name="sap-hana-backup-documentation"></a>Dokumentace ke službě SAP HANA Backup

- [Úvod do správy SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Plánování strategie zálohování a obnovení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Naplánování zálohování HANA pomocí ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Plánování zálohování dat (řídicí panel SAP HANA)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Nejčastější dotazy týkající se SAP HANA Backup v tématu [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Nejčastější dotazy týkající se SAP HANA databází a snímků úložiště v tématu [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nevhodné síťové systémy souborů pro zálohování a obnovení v [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Proč SAP HANA zálohování?

Služba Azure Storage nabízí dostupnost a spolehlivost mimo pole. Další informace o službě Azure Storage najdete v tématu [Úvod do Microsoft Azure Storage](../../../storage/common/storage-introduction.md) .

Minimální pro &quot;zálohování&quot; je spoléhat na službu Azure SLA, která zachovává SAP HANA dat a souborů protokolů na discích Azure připojených k virtuálnímu počítači s SAP HANA serverem. Tento přístup pokrývá selhání virtuálních počítačů, ale ne potenciální škodu SAP HANA dat a souborů protokolu nebo logické chyby, jako je odstranění dat nebo souborů po havárii. Pro dodržování předpisů nebo právních důvodů se vyžadují i zálohy. V krátkém případě je vždy potřeba zálohovat SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Ověření správnosti SAP HANA zálohy
Při použití snímků úložiště se doporučuje spustit obnovení testů v jiném systému. Tento přístup poskytuje způsob, jak zajistit, aby záloha byla správná, a interní procesy zálohování a obnovy fungují podle očekávání. I když se jedná o významné prahové hodnoty v místním prostředí, je mnohem jednodušší provést v cloudu dočasné poskytování nezbytných prostředků pro tento účel.

Mějte na paměti, že při jednoduchém obnovení a kontrole, jestli je HANA v provozu, není dostatečná. V ideálním případě by měl jedna z nich spustit kontrolu konzistence tabulky, aby bylo zajištěno, že obnovená databáze bude dostačit. SAP HANA nabízí několik druhů kontrol konzistence popsaných v tématu [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informace o kontrole konzistence tabulky najdete také na webu SAP v části [kontroly konzistence tabulky a katalogu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Pro standardní zálohy souborů není nutné obnovení testu. Existují dva SAP HANA nástroje, které vám pomůžou ověřit, která záloha se dá použít k obnovení: hdbbackupdiag a hdbbackupcheck. Další informace o těchto nástrojích najdete v tématu [Ruční kontrola, jestli je možné obnovit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) .

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Specialisté a nevýhody zálohování HANA oproti snímku úložiště

&#39;SAP neumožňuje předvolbu pro zálohování Hana oproti snímku úložiště. Uvádí jejich specialisty a nevýhody, takže je možné určit, která z nich se má použít v závislosti na situaci a dostupné technologii úložiště (viz [Plánování strategie zálohování a obnovení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

V Azure si pamatujte na skutečnost, že funkce&#39;snímku Azure Blob nezaručuje konzistenci systému souborů (viz [použití snímků objektů BLOB pomocí PowerShellu](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). Další část _SAP HANA konzistenci dat při pořízení snímků úložiště_, popisuje některé informace týkající se této funkce.

Kromě toho je potřeba pochopit dopad fakturace při častých pracích s snímky objektů blob, jak je popsáno v tomto článku: Porozumět tomu, [jak](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)se na snímcích&#39;vypočítávají poplatky – nejedná se o nezřetelné použití virtuálních disků Azure.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA konzistenci dat při pořízení snímků úložiště

Konzistence systému souborů a aplikací je složitý problém při pořízení snímků úložiště. Nejjednodušší způsob, jak se vyhnout problémům, je vypnout SAP HANA, nebo možná i celý virtuální počítač. Vypnutí může být doable s využitím ukázky nebo prototypu nebo dokonce vývojového systému, ale nejedná se o možnost pro produkční systém.

V Azure je potřeba mít na paměti, že funkce&#39;snímku Azure Blob nezaručuje konzistenci systému souborů. Funguje ale s využitím funkce snímku SAP HANA, pokud je k dispozici pouze jeden virtuální disk. Ale i s jedním diskem, musí být zaškrtnuté další položky. [Poznámka 2039883 pro SAP](https://launchpad.support.sap.com/#/notes/2039883) obsahuje důležité informace o SAP HANA zálohování prostřednictvím snímků úložiště. Například uvádí, že se systémem souborů XFS je nutné před spuštěním snímku úložiště spustit zablokování **XFS\_** , aby se zajistila konzistence (viz [XFS\_zablokované (8) – Linux Man)](https://linux.die.net/man/8/xfs_freeze) , kde najdete podrobnosti o **XFS\_** zablokovat).

Téma konzistence se v případě, že jeden souborový systém pokrývá více disků/svazků, bude ještě náročnější. Například pomocí mdadm nebo LVM a proložení. Poznámka SAP uvedená výše uvádí:

_&quot;Mějte ale na paměti, že systém úložiště musí zaručit konzistenci vstupu/výstupu při vytváření snímku úložiště na SAP HANA objem dat, to znamená, že snapshotting SAP HANA objemu dat specifických pro službu musí být atomická operace.&quot;_

Za předpokladu, že existuje souborový systém XFS se čtyřmi virtuálními disky Azure, následující kroky poskytují konzistentní snímek, který představuje datovou oblast HANA:

- Příprava snímků HANA
- Zablokovat systém souborů (například použít zablokování **XFS\_** )
- Vytvoření všech potřebných snímků objektů BLOB v Azure
- Uvolnit systém souborů
- Potvrdit snímek HANA

Doporučením je použití výše uvedeného postupu ve všech případech na bezpečné straně bez ohledu na to, který systém souborů. Nebo pokud se jedná o jeden disk nebo prokládání přes mdadm nebo LVM na více discích.

Je důležité potvrdit snímek HANA. Vzhledem k tomu &quot;, že SAP HANA kopírování při zápisu&quot; , nemusí v tomto režimu přípravy snímku vyžadovat další místo na disku. &#39;Není taky možné zahájit nové zálohy, dokud se nepotvrdí SAP HANA snímku.

Služba Azure Backup používá rozšíření virtuálních počítačů Azure, aby se postaral o konzistenci systému souborů. Tato rozšíření virtuálních počítačů nejsou k dispozici pro samostatné použití. Pořád musí spravovat konzistenci SAP HANA. Další informace najdete v souvisejícím článku [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) .

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA strategie plánování zálohování

Článek SAP HANA [Plánování strategie zálohování a obnovení](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) uvádí základní plán zálohování:

- Snímek úložiště (denně)
- Úplné zálohování dat pomocí souboru nebo formátu bacint (jednou za týden)
- Automatické zálohování protokolů

Volitelně může být jeden úplně zcela bez snímků úložiště. můžou být nahrazeny rozdílovým zálohováním HANA, jako jsou přírůstkové nebo rozdílové zálohy (viz [rozdílové zálohy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

Příklad seznamu najdete v Průvodci správou HANA. Navrhuje, aby se jeden SAP HANA obnovení k určitému bodu v čase používal v následujícím pořadí zálohování:

1. Úplné zálohování dat
2. Rozdílové zálohování
3. Přírůstkové zálohování 1
4. Přírůstkové zálohování 2
5. Zálohování protokolů

V souvislosti s přesným plánem podle toho, kdy a jak často by se měl provést konkrétní typ zálohování, není možné poskytnout obecné pokyny – je to příliš specifické pro zákazníka a závisí na tom, kolik dat v systému dochází k změnám. Jedno ze základních doporučení ze strany SAP, které se dá zobrazit jako obecné pokyny, je udělat jednu úplnou zálohu v HANA jednou týdně.
Informace o zálohování protokolů najdete v tématu [zálohování protokolu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)SAP HANA dokumentaci.

SAP také doporučuje, abyste údržbu katalog záloh, aby se zajistilo jeho nekonečnému růstu (viz [údržbu pro zálohování katalogu a úložiště záloh](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>SAP HANA konfigurační soubory

Jak je uvedeno v tématu Nejčastější dotazy v tématu [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148), konfigurační soubory SAP HANA nejsou součástí zálohy Standard Hana. Není nutné obnovovat systém. Konfiguraci HANA můžete po obnovení změnit ručně. V případě, že byste chtěli během procesu obnovení získat stejnou vlastní konfiguraci, je nutné zálohovat konfigurační soubory HANA samostatně.

Pokud standardní zálohy HANA přecházejí do vyhrazeného záložního souborového systému HANA, může jedna z nich taky zkopírovat konfigurační soubory do stejného záložního systému souborů a pak vše zkopírovat do konečného cíle úložiště, jako je studená služba BLOB Storage.

### <a name="sap-hana-cockpit"></a>Řídicí panel SAP HANA

Řídicí panel SAP HANA nabízí možnost monitorování a správy SAP HANA přes prohlížeč. Umožňuje také zpracování SAP HANAch záloh, a proto je možné ji použít jako alternativu k SAP HANA studiu a ABAP DBACOCKPIT (Další informace najdete v tématu [řídicí panel SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) .)

![Tento obrázek ukazuje obrazovku správy databáze řídicího panelu SAP HANA](media/sap-hana-backup-guide/image004.png)

Na tomto obrázku vidíte obrazovku pro správu databáze řídicího panelu SAP HANA a na levé straně na dlaždici zálohování. Zobrazení dlaždice zálohování vyžaduje odpovídající uživatelská oprávnění pro přihlašovací účet.

![Zálohy je možné monitorovat v SAP HANA řídicím panelu, když jsou probíhající.](media/sap-hana-backup-guide/image005.png)

Zálohy je možné monitorovat v SAP HANA řídicím panelu, zatímco jsou v jejich průběhu a po jejím dokončení jsou k dispozici všechny podrobnosti o zálohování.

![Příklad použití aplikace Firefox na virtuálním počítači Azure s SLES 12 s plochou GNOME](media/sap-hana-backup-guide/image006.png)

Předchozí snímky obrazovky se vytvořily z virtuálního počítače Azure s Windows. Toto je příklad použití aplikace Firefox na virtuálním počítači Azure s SLES 12 s plochou GNOME. V řídicím panelu SAP HANA se zobrazí možnost definovat SAP HANA plány zálohování. V takovém případě se může zobrazit také datum a čas jako předpona pro záložní soubory. &quot;V SAP HANA studiu je výchozí předponou úplné\_zálohování&quot; dat\_při úplném zálohování souborů. Doporučuje se použít jedinečnou předponu.

### <a name="sap-hana-backup-encryption"></a>Šifrování zálohování SAP HANA

SAP HANA nabízí šifrování dat a protokolů. Pokud SAP HANA data a protokol nejsou zašifrované, zálohy se také nešifrují. Aby bylo možné zašifrovat SAP HANA zálohy, je zákazníkem pro použití některé formy řešení třetí strany. Další informace o šifrování SAP HANA najdete v tématu [šifrování objemu dat a protokolů](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) .

V Microsoft Azure může zákazník použít funkci šifrování virtuálního počítače IaaS k šifrování. Například jedna by mohla použít vyhrazené datové disky připojené k virtuálnímu počítači, které se používají k ukládání SAP HANAch záloh, a pak vytvořit kopie těchto disků.

Služba Azure Backup může zpracovávat šifrované virtuální počítače/disky (viz [Postup zálohování a obnovení šifrovaných virtuálních počítačů pomocí Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Další možností je zachovat SAP HANA virtuální počítač a jeho disky bez šifrování a uložit záložní soubory SAP HANA do účtu úložiště, pro který bylo šifrování povolené (viz téma [šifrování služby Azure Storage pro](../../../storage/common/storage-service-encryption.md)neaktivní uložená data).

## <a name="test-setup"></a>Nastavení testu

### <a name="test-virtual-machine-on-azure"></a>Testovací virtuální počítač v Azure

K provedení našich testů se SAP HANA instalace na virtuálním počítači Azure GS5 použila pro následující testy zálohování nebo obnovení. Zásady jsou stejné jako u virtuálních počítačů řady M-Series.

![Tento obrázek ukazuje část přehledu Azure Portal pro testovací virtuální počítač HANA.](media/sap-hana-backup-guide/image007.png)

Tento obrázek ukazuje část přehledu Azure Portal pro testovací virtuální počítač HANA.

### <a name="test-backup-size"></a>Velikost zálohy testu

![Tento obrázek pocházel z konzoly Backup v HANA studiu a zobrazuje záložní soubor o velikosti 229 GB pro Server indexu HANA.](media/sap-hana-backup-guide/image008.png)

Fiktivní tabulka byla naplněna daty, aby bylo možné získat celkovou velikost zálohy dat větší než 200 GB pro odvození reálných dat o výkonu. Obrázek byl vytvořen z konzoly Backup v HANA Studio a zobrazuje záložní soubor o velikosti 229 GB pro Server indexu HANA. Pro testy se použila výchozí předpona zálohy "COMPLETE_DATA_BACKUP" v SAP HANA studiu. V reálných produkčních systémech by se měla definovat užitečnější předpona. SAP HANA řídící panel navrhuje datum a čas.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testovací nástroj pro kopírování souborů přímo do Azure Storage

Pokud chcete přenášet SAP HANA záložních souborů přímo do úložiště objektů BLOB v Azure nebo sdílených složek Azure, použil se nástroj blobxfer, protože podporuje oba cíle a dá se snadno integrovat do skriptů Automation z důvodu jeho rozhraní příkazového řádku. Nástroj blobxfer je k dispozici [](https://github.com/Azure/blobxfer)na GitHubu.

### <a name="test-backup-size-estimation"></a>Odhad velikosti zálohy testu

Je důležité odhadnout velikost SAP HANA pro zálohování. Tento odhad pomáhá zvýšit výkon tím, že definuje maximální velikost záložního souboru pro určitý počet záložních souborů, a to v důsledku paralelismu během kopírování souborů. (Tyto podrobnosti jsou vysvětleny dále v tomto článku.) Jedna z těchto hodnot musí také rozhodnout, zda provést úplnou zálohu nebo rozdílovou zálohu (přírůstkové nebo rozdílové).

Naštěstí je k dispozici jednoduchý příkaz SQL, který odhadne velikost záložních souborů: **Výběr \* z M\_odhadů\_velikosti\_zálohování** (viz [odhad místa potřebného v systému souborů pro data Zálohování](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Výstup tohoto příkazu SQL odpovídá téměř přesně skutečné velikosti úplných záloh dat na disku.](media/sap-hana-backup-guide/image009.png)

Výstup tohoto příkazu SQL pro testovací systém odpovídá téměř přesně skutečné velikosti úplných záloh dat na disku.

### <a name="test-hana-backup-file-size"></a>Test velikosti záložního souboru HANA

![Konzola zálohování HANA Studio umožňuje jednomu omezit maximální velikost souborů záložních souborů HANA.](media/sap-hana-backup-guide/image010.png)

Konzola zálohování HANA Studio umožňuje jednu omezit maximální velikost souborů záložních souborů HANA. V ukázkovém prostředí Tato funkce umožňuje získat více menších záložních souborů místo 1 230 GB záložního souboru. Zmenšení velikosti souboru má významný dopad na výkon (Další informace najdete v souvisejícím článku [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Souhrn

Na základě výsledků testů jsou v následujících tabulkách uvedeny profesionály a nevýhody řešení pro zálohování SAP HANA databáze běžící na virtuálních počítačích Azure.

**Zálohování SAP HANA do systému souborů a kopírování záložních souborů až do konečného cíle zálohování**

|Řešení                                           |V oblasti IT                                 |Nevýhody                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Udržování záloh HANA na discích virtuálních počítačů                      |Žádné další úsilí na správu     |Eats místo na disku místního virtuálního počítače           |
|Nástroj Blobxfer pro kopírování záložních souborů do úložiště objektů BLOB |Paralelismus pro kopírování více souborů, volba pro použití studeného úložiště objektů BLOB | Další Údržba nástrojů a vlastní skriptování | 
|Kopírování objektů BLOB prostřednictvím PowerShellu nebo rozhraní příkazového řádku                    |K dispozici není žádný další nástroj, který by bylo možné provést prostřednictvím Azure PowerShellu nebo CLI. |ruční proces musí zákazník postarat o skriptování a správu kopírovaných objektů BLOB pro obnovení.|
|Kopírovat do sdílené složky systému souborů NFS                                  |Následné zpracování záložních souborů na jiném virtuálním počítači bez dopadu na server HANA|Pomalý proces kopírování|
|Blobxfer kopírování do souborové služby v Azure                |Nejíste místo na místních discích virtuálních počítačů|Žádná podpora přímých zápisů pomocí zálohování HANA, omezení velikosti sdílené složky je aktuálně na 5 TB.|
|Agent Azure Backup                                 | Bude preferované řešení         | V současnosti není k dispozici v systému Linux    |



**Zálohování SAP HANA na základě snímků úložiště**

|Řešení                                           |V oblasti IT                                 |Nevýhody                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Služba Azure Backup                               | Povoluje zálohování virtuálních počítačů na základě snímků objektů BLOB. | Když nepoužíváte obnovení na úrovni souborů, vyžaduje se vytvoření nového virtuálního počítače pro proces obnovení, který pak bude zaznamená nutnost nového licenčního klíče SAP HANA.|
|Ruční snímky objektů BLOB                              | Flexibilita při vytváření a obnovování konkrétních disků virtuálních počítačů bez změny jedinečného ID virtuálního počítače|Veškerá ruční práce, kterou musí provést zákazník|

## <a name="next-steps"></a>Další postup
* [SAP HANA Azure Backup na úrovni souboru](sap-hana-backup-file-level.md) popisuje možnost zálohování na základě souborů.
* [SAP HANA zálohování na základě snímků úložiště](sap-hana-backup-storage-snapshots.md) popisuje možnost zálohování na základě snímků úložiště.
* Informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA v Azure (velké instance), najdete v tématu [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
