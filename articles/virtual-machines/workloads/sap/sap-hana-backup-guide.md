---
title: Příručce zálohování pro SAP HANA ve virtuálních počítačích Azure | Microsoft Docs
description: Zálohování Příručka pro SAP HANA obsahuje dvě hlavní možnosti zálohování pro SAP HANA na virtuálních počítačích Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 9346ed6b26b4fa4a7875354153b6847938253d1e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657005"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Průvodce zálohováním pro SAP HANA na Azure Virtual Machines

## <a name="getting-started"></a>Začínáme

Průvodce zálohování pro SAP HANA běžících na virtuálních počítačích Azure se popisují pouze témata specifické pro Azure. Pro obecné SAP HANA související položky zálohování, naleznete v dokumentaci SAP HANA (viz _zálohování dokumentace SAP HANA_ dále v tomto článku).

Existují dvě hlavní zálohování možnosti pro SAP HANA na virtuálních počítačích Azure je aktivní tohoto článku:

- HANA zálohování do systému souborů v virtuální počítač Linux Azure (viz [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md))
- HANA zálohování na základě úložiště snímků ručně pomocí funkce snímku objektu blob úložiště Azure nebo služby zálohování Azure (viz [SAP HANA zálohování podle úložiště snímků](sap-hana-backup-storage-snapshots.md))

SAP HANA nabízí zálohu rozhraní API, které umožňuje zálohování nástroje třetích stran pro integraci přímo SAP HANA. (Který není v rámci oboru této příručky.) Neexistuje žádná přímá integrace SAP HANA pomocí služby Azure Backup k dispozici pravé na základě na toto rozhraní API.

SAP HANA oficiálně podporované u virtuálního počítače Azure typu GS5 jako jedna instance s další omezení pro úlohy OLAP (viz [najít platformy IaaS certifikované](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) na webu SAP). Tento článek se aktualizuje jako nové nabídky pro SAP HANA v Azure k dispozici.

Je také SAP HANA hybridní řešení k dispozici na Azure, kde SAP HANA běží nevirtuálním na fyzických serverech. Ale tento průvodce zálohování SAP HANA Azure popisuje čistý prostředí Azure, kde SAP HANA běží ve virtuálním počítači Azure, ne SAP HANA systémem &quot;velké instancí.&quot; V tématu [přehled SAP HANA (velké instance) a architektura v Azure](hana-overview-architecture.md) Další informace o tomto řešení zálohování na &quot;velké instancí&quot; podle úložiště snímků.

Obecné informace o produktech SAP podporován na platformě Azure můžete najít v [1928533 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1928533).

Následující tři údaje poskytnout přehled SAP HANA možnosti zálohování pomocí nativního možnosti Azure aktuálně a také se zobrazují tři možných scénářů budoucí zálohy. Související články [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) a [SAP HANA zálohování podle úložiště snímků](sap-hana-backup-storage-snapshots.md) popisují tyto možnosti podrobněji, včetně velikosti a výkonové požadavky SAP HANA zálohování, které jsou více terabajtů velikost.

![Následující obrázek ukazuje existují dvě možnosti pro ukládání aktuální stav virtuálního počítače](media/sap-hana-backup-guide/image001.png)

Následující obrázek ukazuje možnost ukládání aktuální stav virtuálního počítače prostřednictvím služby Azure Backup nebo ruční snímek disky virtuálních počítačů. S tímto přístupem, jeden nemá&#39;t muset spravovat zálohy SAP HANA. Výzvy scénáře snímku disku je konzistence systému souborů a k dispozici stav disku konzistentní s aplikací. Konzistence tématu je popsán v části _konzistenci dat SAP HANA při pořizování snímků úložiště_ dále v tomto článku. Omezení služby Azure Backup související s SAP HANA zálohy a možnosti jsou popsány i později v tomto článku.

![Následující obrázek ukazuje, že ve virtuálním počítači zálohování souborů možnosti za vyjádření SAP HANA](media/sap-hana-backup-guide/image002.png)

Následující obrázek ukazuje možnosti trvá zálohu souboru SAP HANA ve virtuálním počítači a pak ukládání záložních souborů HANA někde jinde pomocí různých nástrojů. Zálohování HANA vyžaduje více času, než řešení zálohování založené na snímku, ale má výhody týkající se integrity a konzistence. Další podrobnosti jsou uvedeny dále v tomto článku.

![Tento obrázek znázorňuje potenciální budoucí zálohy scénář SAP HANA](media/sap-hana-backup-guide/image003.png)

Tento obrázek znázorňuje potenciální budoucí zálohy scénář SAP HANA. Pokud SAP HANA povolený umožňoval vytvářet zálohy z sekundární replikace, by přidat další možnosti pro strategii zálohování. Aktuálně není možné podle post na stránkách Wiki SAP HANA:

_&quot;Je možné provést zálohování na sekundární straně?_

_Ne, teď můžete pouze trvat dat a protokolu zálohování na primární straně. Pokud je povoleno automatické protokolu zálohování, po převzetí na sekundární stranu zálohy protokolu automaticky se zapíšou existuje.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Prostředky SAP HANA zálohování

### <a name="sap-hana-backup-documentation"></a>Zálohování dokumentace SAP HANA

- [Úvod do správy SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Plánování zálohování a obnovení strategie](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Plánování zálohování HANA pomocí ABAP DBACOCKPIT](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Plán zálohování dat (SAP HANA řídícího panelu)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Nejčastější dotazy o SAP HANA zálohování v [1642148 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1642148)
- Nejčastější dotazy o SAP HANA snímky databáze a úložiště v [2039883 Poznámka SAP](https://launchpad.support.sap.com/#/notes/2039883)
- Systémy souborů síť není vhodný pro zálohování a obnovení v [1820529 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Proč SAP HANA zálohování?

Azure storage nabízí dostupnost a spolehlivost mimo pole (viz [Úvod do Microsoft Azure Storage](../../../storage/common/storage-introduction.md) Další informace o úložišti Azure).

Minimum pro &quot;zálohování&quot; je moct spolehnout na Azure SLA, udržování SAP HANA souborů protokolu a data na disky Azure připojené k serveru SAP HANA virtuálních počítačů. Tento postup popisuje selhání virtuálních počítačů, ale není možné škody SAP HANA data a soubory protokolu nebo logické chyby jako odstraněním dat nebo soubory omylem odstraněný. Zálohy jsou rovněž vyžadovány pro dodržování předpisů nebo právních důvodů. Stručně řečeno je vždy nutné pro SAP HANA zálohy.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Jak ověřit správnost zálohování SAP HANA
Při použití úložiště snímků, se doporučuje spustit test obnovení v jiném systému. Tento přístup poskytuje způsob, jak zajistěte, aby zálohu správná a interní procesy pro zálohování a obnovení pracovní podle očekávání. Přestože se významným vlivem mezní místně, je mnohem snazší provést v cloudu dočasně poskytnutím potřebné prostředky pro tento účel.

Zachovat na paměti, díky jednoduché obnovení a kontrole, jestli je HANA a spouštění není dostatečná. V ideálním případě jeden měli spustit kontrolu konzistence tabulky a ujistěte se, že je v pořádku obnovenou databázi. SAP HANA nabízí několik druhů kontroly konzistence, které jsou popsané v [1977584 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1977584).

Informace o kontrole konzistence tabulku je možné také najít na webu SAP na [tabulky a kontrol konzistence katalogu](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Obnovení testu pro standardních souborových záloh, není nutné. Existují dvě SAP HANA nástroje, které pomáhají ke kontrole, kterou zálohy lze použít pro obnovení: hdbbackupdiag a hdbbackupcheck. V tématu [Ruční kontrola zda obnovení je možné](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) Další informace o těchto nástrojů.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Výhody a nevýhody HANA zálohování versus úložiště snímků

SAP nemá&#39;t poskytnout preference buď HANA zálohování versus úložiště snímků. Vypíše jejich výhody a nevýhody, takže jeden můžete určit, který se použije v závislosti na situaci a technologií úložiště k dispozici (viz [plánování vaše zálohování a obnovení strategie](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

V Azure, mějte na paměti skutečnost, že objekt blob systému Azure snímku funkce nemá&#39;konzistence systému souborů záruku t (najdete v části [snímky použití objektů blob v prostředí PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). V další části _konzistenci dat SAP HANA při pořizování snímků úložiště_, popisuje některé aspekty týkající se této funkce.

Kromě toho je nutné vysvětlení fakturace důsledků při práci často s snímky objektů blob, jak je popsáno v tomto článku: [pochopení jak snímky nabíhat poplatky](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– ho neběží&#39;t jako zřejmé jako pomocí Azure virtuální disky.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA konzistenci dat, když se vezme úložiště snímků

Systém a aplikace konzistence souborů je komplexní problém při pořizování snímků úložiště. Nejjednodušší způsob, jak se vyhnout problémům by mohla být vypnout SAP HANA, nebo může být i celý virtuální počítač. Vypnutí může být doable s ukázku nebo prototypu nebo i vývojového systému, ale není možnost pro produkční systému.

V Azure, a jeden má mějte na paměti, že objekt blob systému Azure snímku funkce nemá&#39;konzistence systému souborů záruku t. Funguje bez problémů ale pomocí snímku SAP HANA funkce, dokud související se situací je jenom jeden virtuální disk. Ale i s jeden disk, musí být vráceny další položky. [SAP Poznámka 2039883](https://launchpad.support.sap.com/#/notes/2039883) obsahuje důležité informace o zálohování SAP HANA pomocí snímků úložiště. Například uvádí, že pomocí systému souborů XFS, je nutné spustit **xfs\_freeze** před zahájením snímek úložiště, který chcete zaručit konzistenci (najdete v části [xfs\_freeze(8) - stránka man Linux](https://linux.die.net/man/8/xfs_freeze) podrobnosti o **xfs\_freeze**).

Téma konzistence se změní i další náročné, v případě, kde jeden soubor systému zahrnuje několik disky nebo svazky. Například s využitím mdadm nebo LVM a prokládání. Poznámka SAP zmíněné stavy:

_&quot;Ale mějte na paměti, která systém úložiště musí zaručit konzistenci vstupně-výstupních operací při vytváření snímku úložiště za SAP HANA datový svazek, tj. snímkování svazku SAP HANA specifických dat služby musí být atomické operace.&quot;_

Za předpokladu, že je systém souborů XFS pokrývání uzlů čtyři Azure virtuální disky, následující kroky obsahují konzistentní snímek, který představuje HANA datové oblasti:

- Příprava HANA snímku
- Freeze – systém souborů (například použít **xfs\_freeze**)
- Vytvářet všechny snímky nezbytné objektů blob v Azure
- Uvolní systému souborů
- Potvrďte snímku HANA

Doporučuje se použít uvedený postup ve všech případech být v bezpečí, bez ohledu na to, které systém souborů. Nebo pokud se jedná o jediný disk nebo prokládání, prostřednictvím mdadm nebo LVM na více disků.

Je důležité pro potvrzení HANA snímku. Z důvodu &quot;kopie při zápisu,&quot; SAP HANA nemusí vyžadovat další místo na disku v to snímku Příprava režimu. Ho&#39;s také není možné spustit nové zálohování, dokud je potvrzen snímku SAP HANA.

Služba Azure Backup používá rozšíření virtuálního počítače Azure, abyste dbali konzistence systému souborů. Tato rozšíření virtuálního počítače nejsou k dispozici pro samostatné použití. Jeden má stále ke správě konzistence SAP HANA. Najdete v souvisejícím článku [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) Další informace.

### <a name="sap-hana-backup-scheduling-strategy"></a>Plánování strategie SAP HANA zálohování

Článek SAP HANA [plánování vaše zálohování a obnovení strategie](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) stavy základní plán k zálohování:

- Úložiště snímků (denní)
- Zálohování kompletní datový soubor nebo bacint formátu (jednou týdně)
- Automatické protokolu zálohování

Volitelně můžete jeden může přejít zcela bez snímků úložiště; může být nahrazen HANA rozdílové zálohy, jako přírůstkové nebo rozdílové zálohy (viz [rozdílové zálohy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

Průvodce správou HANA obsahuje seznam příklad. Navrhování jeden obnovit SAP HANA k určitému bodu v čase pomocí následující posloupnosti zálohování:

1. Úplná záloha
2. Rozdílovou zálohu
3. Přírůstkové zálohování 1
4. Přírůstkové zálohování 2
5. Zálohy protokolů

O přesný plán, kdy a jak často má proběhnout konkrétní typ zálohy, není možné poskytnout obecných pokynů – je příliš specifické pro zákazníka a závisí na tom, kolik dat změnách v systému. Jeden základní ze strany SAP, které můžete zobrazit jako obecné pokyny, doporučuje se zpřístupnění jeden úplné HANA zálohování jednou za týden.
Týká se zálohy protokolu, naleznete v dokumentaci k SAP HANA [zálohy protokolu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP taky doporučuje provádění některých housekeeping zálohování katalogu zabránit růstu nekonečnou (najdete v části [spouštění pro katalog zálohování a zálohování úložiště](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Konfigurační soubory SAP HANA

Jak je uvedeno v části Nejčastější dotazy v [1642148 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1642148), SAP HANA konfigurační soubory, které nejsou součástí standardní HANA zálohování. Nejsou nezbytně nutné, aby obnovení systému. Konfigurace HANA může po obnovení ručně změnit. V případě, že jeden chtěli získat stejné vlastní konfigurace během procesu obnovení, je nutné zálohovat konfigurační soubory HANA samostatně.

Pokud se má vyhrazený systém zálohování souborů HANA standardní HANA zálohy, jeden může také zkopírujte konfigurační soubory na stejném systém zálohování souborů a potom všechno, co společně zkopírujte do cíl konečné úložiště jako nástrojů úložiště objektů blob.

### <a name="sap-hana-cockpit"></a>Řídící panel SAP HANA

Řídící panel SAP HANA nabízí možnost, monitorování a správa SAP HANA prostřednictvím prohlížeče. Také umožňuje zpracování SAP HANA záloh a proto je lze použít jako alternativu k SAP HANA Studio a ABAP DBACOCKPIT (viz [řídící panel SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) Další informace).

![Následující obrázek ukazuje obrazovce pro správu databáze SAP HANA řídící panel](media/sap-hana-backup-guide/image004.png)

Následující obrázek ukazuje obrazovce pro správu databáze SAP HANA řídicí panel a dlaždici zálohování na levé straně. Zobrazuje dlaždici zálohování vyžaduje příslušných uživatelských oprávnění pro účet pro přihlášení.

![Zálohy lze sledovat v řídícím SAP HANA době, kdy jsou probíhající](media/sap-hana-backup-guide/image005.png)

Zálohování se dá sledovat v řídícím SAP HANA jsou probíhající a po jeho dokončení zálohování podrobnosti jsou k dispozici.

![Příklad použití Firefox na virtuální počítač Azure SLES 12 Gnome plochy](media/sap-hana-backup-guide/image006.png)

Na předchozích snímcích obrazovky nebyly provedeny z virtuálního počítače Windows Azure. Tato funkce je příklad pomocí prohlížeče Firefox na virtuální počítač Azure SLES 12 Gnome plochy. Zobrazuje možnost definovat plánů zálohování SAP HANA v řídícím SAP HANA. Jako jeden, můžete také zjistit, navrhuje datum a čas jako předpona pro záložní soubory. V nástroji SAP HANA Studio výchozí předpona je &quot;COMPLETE\_DATA\_zálohování&quot; při provádění úplnou zálohu souborů. Doporučuje se použít jedinečnou předponu.

### <a name="sap-hana-backup-encryption"></a>Zálohu šifrovacího SAP HANA

SAP HANA nabízí šifrování dat a protokolu. Pokud nejsou šifrovaná data SAP HANA a protokolu, pak záloh nejsou šifrované. Je na zákazníkovi, který chcete použít k šifrování záloh SAP HANA určitou formu řešení třetí strany. V tématu [dat a šifrování svazku protokolu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) na další informace o šifrování SAP HANA.

V Microsoft Azure může zákazník použít funkci šifrování virtuálních počítačů IaaS k šifrování. Například jedna může použít vyhrazený datových disků připojených k virtuálnímu počítači, které se používají k ukládání záloh SAP HANA, ujistěte se, kopie těchto disků.

Služba Azure Backup dokáže zpracovat šifrované virtuálních počítačů/disků (viz [šifrované zálohování a obnovení virtuálních počítačů s Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Další možností by mohla být udržovat virtuální počítač SAP HANA a jeho disky bez šifrování a ukládání záložních souborů SAP HANA v účtu úložiště, pro které bylo povoleno šifrování (viz [šifrování služby úložiště Azure pro Data v klidovém stavu](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Nastavení testu

### <a name="test-virtual-machine-on-azure"></a>Testovací virtuální počítač na platformě Azure

Instalace SAP HANA virtuální počítač Azure GS5 byl použit pro následující testy zálohování nebo obnovení.

![Následující obrázek ukazuje součástí Azure přehled portálu HANA testovací virtuální počítač](media/sap-hana-backup-guide/image007.png)

Následující obrázek ukazuje součástí Azure přehled portálu HANA testovací virtuální počítač.

### <a name="test-backup-size"></a>Velikost zálohování testu

![Tento obrázek byl odebrán z konzoly pro zálohování v HANA Studio a zobrazuje velikost záložní soubor 229 GB pro HANA index server](media/sap-hana-backup-guide/image008.png)

Fiktivní tabulka byla naplněna s daty se získat velikost celková data zálohování více než 200 GB odvození data realistické výkonu. Obrázek byl odebrán z konzoly pro zálohování v HANA Studio a zobrazí se záložní soubor velikost 229 GB pro HANA index server. Pro testy byl použit výchozí zálohování předponu "COMPLETE_DATA_BACKUP" v SAP HANA Studio. V systémech skutečné produkční nesmí být definován užitečnější předpony. Řídící panel SAP HANA navrhuje datum a čas.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testování nástroj pro kopírování souborů přímo do úložiště Azure

K přenosu SAP HANA záložní soubory přímo do úložiště objektů blob v Azure nebo sdílené složky Azure, byl nástroj blobxfer použít, protože podporuje obě cíle a lze ji snadno integrovat do skripty pro automatizaci z důvodu jeho rozhraní příkazového řádku. Nástroj blobxfer je k dispozici na [Githubu](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Odhad velikosti zálohy testu

Je důležité odhad zálohování velikost SAP HANA. Tento odhad pomáhá zlepšit výkon definováním záložní soubor maximální velikost pro počet záložních souborů z důvodu paralelismus během kopírování souboru. (Tyto informace jsou vysvětleny dále v tomto článku). Jeden musí také rozhodnout, zda chcete provést úplnou zálohu nebo rozdílové zálohy (přírůstkové nebo rozdílovou).

Naštěstí je jednoduchý příkaz SQL, který odhadne velikost záložní soubory: **vyberte \* z M\_zálohování\_velikost\_odhady** (najdete v části [při odhadu v systému souborů pro zálohování dat](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Výstup tohoto příkazu SQL odpovídá téměř úplně stejné skutečná velikost úplná záloha na disku](media/sap-hana-backup-guide/image009.png)

Výstup tohoto příkazu SQL pro testovací systém odpovídá téměř úplně stejné skutečná velikost úplná záloha na disku.

### <a name="test-hana-backup-file-size"></a>Test HANA záložní soubor velikost

![Konzole zálohování HANA Studio umožňuje jednu pro omezení maximální velikost HANA záložní soubory](media/sap-hana-backup-guide/image010.png)

Konzole zálohování HANA Studio umožňuje jednu pro omezení maximální velikost HANA záložní soubory. V ukázkové prostředí tato funkce umožňuje získat několik menších souborů zálohování místo jeden záložní soubor 230 GB. Menší velikost souborů má významný dopad na výkon (najdete v souvisejícím článku [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Souhrn

Založené na výsledky testů, které následující tabulky popisují výhody a nevýhody řešení pro zálohování databáze SAP HANA běžících na virtuálních počítačích Azure.

**Zálohování SAP HANA do systému souborů a pak zkopírujte záložní soubory do konečného umístění zálohování**

|Řešení                                           |Odborníci na                                 |Nevýhody                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Zachovat HANA zálohování na disky virtuálních počítačů                      |Žádné další správu úsilí     |Eats místní místa na disku virtuálního počítače           |
|Blobxfer nástroj a zkopírujte záložní soubory do úložiště objektů blob |Paralelismus chcete zkopírovat soubory, možnost použití úložiště na studených objektů blob | Další nástroje údržby a vlastní skriptování | 
|Kopírování objektu BLOB prostřednictvím prostředí Powershell nebo rozhraní příkazového řádku                    |Žádné další nástroje, které jsou nezbytné, můžete nakonfigurovat pomocí prostředí Azure Powershell nebo rozhraní příkazového řádku |Proces ručního nastavení, zákazník musí postará o skriptování a správu zkopírovaných objektů blob pro obnovení|
|Zkopírujte do sdílené složky NFS                                  |Po zpracování záložní soubory na jiných virtuálních počítačů bez dopadu na serveru HANA|Kopírování pomalé|
|Blobxfer kopírovat do služby Azure souborů                |Nebude vyžadovat značné množství místa na místní disky virtuálních počítačů|Žádné přímé zápisu podporu omezením HANA zálohování, velikost sdílené složky aktuálně na 5 TB|
|Azure Backup Agent                                 | Bude upřednostňované řešení         | Momentálně není k dispozici v systému Linux    |



**Zálohování SAP HANA podle úložiště snímků**

|Řešení                                           |Odborníci na                                 |Nevýhody                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Služby zálohování Azure                               | Umožňuje zálohování virtuálních počítačů podle snímky objektů blob | Pokud nepoužíváte obnovení na úrovni souborů, vyžaduje vytvoření nového virtuálního počítače pro obnovení procesu, který pak znamená potřeba nový licenční klíč SAP HANA|
|Snímky ruční objektů blob                              | Flexibilita pro vytvoření a obnovení konkrétní disky virtuálních počítačů, aniž byste museli měnit jedinečné ID virtuálního počítače|Všechny ručního nastavení, které je nutné provést zákazníkem|

## <a name="next-steps"></a>Další postup
* [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) popisuje možnost zálohování na základě souborů.
* [SAP HANA zálohování podle úložiště snímků](sap-hana-backup-storage-snapshots.md) popisuje zálohování možnosti založené na snímku úložiště.
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA v Azure (velké instance) naleznete v tématu [SAP HANA (velké instance) vysoké dostupnosti a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
