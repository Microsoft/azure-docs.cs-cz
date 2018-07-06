---
title: Průvodce zálohováním pro SAP HANA v Azure Virtual Machines | Dokumentace Microsoftu
description: Průvodce zálohováním pro SAP HANA obsahuje dvě hlavní možnosti zálohování pro SAP HANA na virtuálních počítačích Azure
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
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 9d72bc885bdaaed521042df236dd722b80533186
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866997"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Průvodce zálohováním pro SAP HANA na Azure Virtual Machines

## <a name="getting-started"></a>Začínáme

Průvodce zálohováním pro SAP HANA běžících na virtuálních počítačích Azure se pouze popisují témata týkající se Azure. Obecné SAP HANA zálohování související položky, najdete v dokumentaci k SAP HANA (viz _dokumentace ke službě backup SAP HANA_ dále v tomto článku).

Hlavním cílem tohoto článku je na dvě hlavní zálohování možnosti pro SAP HANA na virtuálních počítačích Azure:

- Zálohování HANA k systému souborů v Azure virtuálního počítače s Linuxem (viz [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md))
- Zálohování HANA podle snímků úložiště ručně pomocí funkce snímku objektu blob Azure storage nebo Azure Backup Service (viz [zálohování SAP HANA založené na snímcích úložiště](sap-hana-backup-storage-snapshots.md))

SAP HANA nabízí zálohování rozhraní API, které umožňuje zálohování nástroje třetích stran integrovat SAP HANA. (Který není v rámci oboru této příručky.) Neexistuje žádná přímá integrace systému SAP HANA pomocí služby Azure Backup k dispozici pravé na základě tohoto rozhraní API.

SAP HANA se oficiálně podporuje různé typy virtuálních počítačů Azure, jako je Azure řady M-Series. Pro úplný seznam SAP HANA certifikované virtuální počítače Azure, podívejte se na [najít certifikací platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). V tomto článku se bude aktualizovat nové nabídky pro SAP HANA v Azure k dispozici.

Existuje také o hybridní řešení SAP HANA k dispozici v Azure, kde SAP HANA běží nevirtualizovaných na fyzických serverech. Ale tento průvodce zálohování SAP HANA Azure pokrývá čisté prostředí Azure, kde běží SAP HANA ve Virtuálním počítači Azure, ne SAP HANA běží na &quot;velké instance.&quot; Zobrazit [architektura v Azure a SAP HANA (velké instance) přehled](hana-overview-architecture.md) Další informace o tomto řešení zálohování na &quot;velké instance&quot; založené na snímcích úložiště.

Obecné informace týkající se produktů SAP v Azure nepodporuje můžete najít v [SAP 1928533 Poznámka](https://launchpad.support.sap.com/#/notes/1928533).

Následující tři údaje poskytnout přehled o možnostech zálohování SAP HANA pomocí nativních možností Azure aktuálně a také zobrazit tří možných scénářů budoucí zálohy. Související články [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) a [zálohování SAP HANA založené na snímcích úložiště](sap-hana-backup-storage-snapshots.md) popis těchto možností podrobněji, včetně velikosti a posouzení výkonu pro SAP HANA zálohování, které jsou více terabajty velikosti.

![Tento obrázek ukazuje dvě možnosti k uložení aktuálního stavu virtuálního počítače](media/sap-hana-backup-guide/image001.png)

Tento obrázek ukazuje možnost Uložit aktuální stav virtuálního počítače přes službu Azure Backup nebo ruční snímků disků virtuálních počítačů. Díky tomuto přístupu jednoho kódu&#39;nemá pro správu zálohování SAP HANA. Výzvy scénář snímku disku je konzistence systému souborů a stavu konzistentní disku. V části je popisováno téma konzistence _konzistence dat SAP HANA při pořizování snímků úložiště_ dále v tomto článku. Možnosti a omezení týkající se zálohování SAP HANA služby Azure Backup jsou také popsány dále v tomto článku.

![Tento obrázek ukazuje, že zálohování ve virtuálním počítači souborů možnosti pro pořízení SAP HANA](media/sap-hana-backup-guide/image002.png)

Tento obrázek ukazuje možnosti pro vytvoření zálohy souboru SAP HANA ve virtuálním počítači a potom ukládání záložních souborů HANA někde jinde pomocí různých nástrojů. Vytvoření zálohy HANA vyžaduje více času než řešení založených na snímcích zálohování, ale má výhody, pokud jde o integritu a konzistenci. Další podrobnosti jsou uvedeny dále v tomto článku.

![Tento obrázek ukazuje potenciální budoucích zálohování scénář SAP HANA](media/sap-hana-backup-guide/image003.png)

Tento obrázek ukazuje potenciální budoucích zálohování scénář SAP HANA. Pokud SAP HANA může pořízení zálohy ze sekundární replikaci, je přidat další možnosti pro strategii zálohování. Aktuálně není možné podle příspěvek ve SAP HANA Wiki:

_&quot;Je možné provést zálohování na sekundární straně?_

_Ne, aktuálně můžete vzít data a jako hlavní strana zálohu protokolu. Pokud je povoleno automatické protokolu zálohování, po převzetí sekundární stranu zálohy protokolu bude automaticky zapsat existuje.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Prostředky SAP HANA zálohování

### <a name="sap-hana-backup-documentation"></a>Dokumentace ke službě backup SAP HANA

- [Úvod k SAP HANA správy](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Plánování zálohování a obnovení strategie](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Naplánování zálohování HANA pomocí ABAP DBACOCKPIT](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Plán zálohování dat (SAP HANA řídicí panel)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Nejčastější dotazy týkající se SAP HANA zálohování v [1642148 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1642148)
- Nejčastější dotazy týkající se SAP HANA database a storage snímky v [2039883 Poznámka SAP](https://launchpad.support.sap.com/#/notes/2039883)
- Nevhodný síťový systémy souborů pro zálohování a obnovení v [1820529 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Výhody řešení SAP HANA zálohování?

Azure storage nabízí dostupnost a spolehlivost úprav (naleznete v tématu [Úvod do Microsoft Azure Storage](../../../storage/common/storage-introduction.md) Další informace o službě Azure storage).

Minimum pro &quot;zálohování&quot; je přináší setrvávání u smlouvách SLA v Azure, udržování SAP HANA souborů protokolu a data na virtuální pevné disky Azure připojené k serveru SAP HANA virtuálního počítače. Tento přístup se věnuje selhání virtuálních počítačů, ale není možné poškození dat SAP HANA a soubory protokolu nebo logické chyby, jako je odstranění dat nebo souborů náhodná. Zálohy jsou také vyžadovat dodržování předpisů nebo právních důvodů. Stručně řečeno není vždy nutné pro zálohování SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Postup pro ověření správnosti zálohování SAP HANA
Při použití úložiště snímků, se doporučuje spustit testovací obnovení v jiném systému. Tento přístup poskytuje způsob, jak Ujistěte se, že zálohy správné a interní procesy pro zálohování a obnovení práce podle očekávání. Když je významným vlivem mezní místně, je mnohem jednodušší provádět v cloudu tím, že dočasně poskytuje prostředky potřebné pro tento účel.

Zachovat v paměti, která provádění snadného obnovení a kontroly, jestli je HANA a spouštění není dostatečná. V ideálním případě jeden by měl spustit kontrolu konzistence tabulky ujistit se, že je v pořádku obnovené databáze. SAP HANA nabízí několik druhů kontroly konzistence je popsáno v [1977584 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1977584).

Informace o kontrole konzistence tabulky najdete také na webu SAP na [tabulky a kontrolu konzistence katalogu](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

U standardních souborových záloh testovací obnovení není nutné. Existují dva nástroje SAP HANA, které pomáhají zjistit, kterou zálohu lze použít pro obnovení: hdbbackupdiag a hdbbackupcheck. Zobrazit [ruční kontrolu, jestli obnovení je možné](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) pro další informace o těchto nástrojích.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Výhody a nevýhody zálohování HANA a snímků úložiště

SAP kódu&#39;t dát přednost buď zálohování HANA a snímek úložiště. Vypíše jejich výhody a nevýhody, takže jeden můžete určit, která bude použita v závislosti na situaci a technologie úložiště k dispozici (viz [plánování Backup a Recovery strategie](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

V Azure, mějte na paměti postup využívající fakt, že Azure blob snapshot kódu funkce&#39;konzistence systému souborů se zárukou t (naleznete v tématu [snímky objektů blob pomocí prostředí PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). Další části _konzistence dat SAP HANA při pořizování snímků úložiště_, tento článek popisuje některé důležité informace týkající se této funkce.

Kromě toho je nutné při nepochopení plného fakturační při práci často se snímky objektů blob, jak je popsáno v tomto článku: [Principy jak snímky nabíhání poplatků za](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– to není&#39;t jako zřejmé jako pomocí Azure virtual disky.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Konzistence dat SAP HANA při pořizování snímků úložiště

Konzistence souborů systém a aplikace je komplexní problém při pořizování snímků úložiště. Nejjednodušší způsob, jak se vyhnout problémům by vypnutí SAP HANA, nebo možná i celý virtuální počítač. Je možné, že se na ukázku nebo prototyp nebo dokonce i vývoj systému doable vypnutí, ale není možnost pro produkční systém.

V Azure, jedna má mějte na paměti, že Azure blob snapshot kódu funkce&#39;konzistence systému souborů se zárukou t. Zátěži funguje ale pomocí snímek SAP HANA funkce, jako je potřebný jenom jeden virtuální disk. Ale i s jedním diskem, musí být vráceny další položky. [SAP Poznámka 2039883](https://launchpad.support.sap.com/#/notes/2039883) obsahuje důležité informace o zálohování SAP HANA pomocí snímků úložiště. Například uvádí, že pomocí systému souborů XFS, je nutné spustit **xfs\_ukotvit** před zahájením snímek úložiště, který chcete zaručit konzistenci (naleznete v tématu [xfs\_freeze(8) - Linux man stránky ](https://linux.die.net/man/8/xfs_freeze) podrobnosti o **xfs\_ukotvit**).

Téma konzistence čím dál i složitější v případě, kdy jeden soubor systému zahrnuje několik disků a svazků. Například můžete pomocí mdadm nebo LVM a prokládání. SAP Note zmíněné stavy:

_&quot;Ale mějte na paměti, která úložný systém musí zaručit konzistenci vstupně-výstupních operací při vytváření snímku úložiště za objem dat SAP HANA, to znamená snímkování svazku SAP HANA specifických dat služby musí být atomickou operaci.&quot;_

Za předpokladu, že je systém souborů XFS pokrývání uzlů Azure čtyři virtuální disky, následující kroky obsahují snímek konzistentní vzhledem k aplikacím, který představuje oblasti dat HANA:

- Příprava HANA snímku
- Ukotvit v systému souborů (například použít **xfs\_ukotvit**)
- Vytvořit snímky všechny nezbytné objektů blob v Azure
- Uvolnit systému souborů
- Potvrďte HANA snímku

Doporučení je použití výše uvedeného postupu ve všech případech v bezpečí, bez ohledu na to, které systém souborů. Nebo pokud se jedná o jediný disk nebo prokládání, prostřednictvím mdadm nebo LVM na několik disků.

Je důležité pro potvrzení HANA snímku. Z důvodu &quot;kopie při zápisu,&quot; SAP HANA nemusí potřebovat další místo na disku v tento snímek Příprava režimu. To&#39;s také nelze spustit nové zálohování, dokud není potvrzené snímek SAP HANA.

Služba Azure Backup využívá rozšíření virtuálního počítače Azure k zajištění konzistence systému souborů. Nejsou k dispozici pro samostatné použití těchto rozšíření virtuálních počítačů. Jeden má stále ke správě konzistence SAP HANA. Najdete v příslušném článku [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) Další informace.

### <a name="sap-hana-backup-scheduling-strategy"></a>Plánování strategie zálohování SAP HANA

SAP HANA článku [plánování Backup a Recovery strategie](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) uvádí základní plán k zálohování:

- Úložiště snímků (denní)
- Úplná data zálohování pomocí formátu souboru nebo bacint (jednou týdně)
- Zálohy automatického protokolování

Volitelně můžete jeden může přejít zcela bez snímků úložiště; může být nahrazen HANA rozdílové zálohy, jako je přírůstková nebo rozdílové zálohy (viz [rozdílové zálohy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

Příručka věnovaná HANA poskytuje příklad seznamu. Navrhuje jeden obnovit SAP HANA k určitému bodu v čase s použitím následující posloupnost zálohování:

1. Úplná záloha
2. Rozdílová záloha
3. Přírůstkové zálohování 1
4. Přírůstkové zálohování 2
5. Zálohování protokolu

Pokud jde o přesnou plán, kdy a jak často se stane konkrétní typ zálohy, není možné poskytnout obecných pokynů – je moc specifické pro zákazníka a závisí na tom, kolik dat změn v systému. Jeden základní ze strany SAP, což uvidíte jako obecné pokyny, doporučujeme vytvořit jeden úplné HANA zálohování jednou za týden.
Týká se zálohy protokolu, naleznete v dokumentaci k SAP HANA [zálohy protokolu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP také doporučuje provádění některých údržbu katalog záloh zachovat neomezeně narůstat (viz [spouštění pro katalog záloh a úložiště pro zálohování](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>SAP HANA konfiguračních souborů

Jak je uvedeno v části Nejčastější dotazy v [1642148 Poznámka SAP](https://launchpad.support.sap.com/#/notes/1642148), SAP HANA konfigurační soubory, které nejsou součástí standardní zálohování HANA. Nejsou nezbytné pro obnovení systému. Konfigurace HANA může změnit ručně po obnovení. V případě, že jeden chce získat stejné vlastní konfigurace během procesu obnovení, je nutné zálohovat konfigurační soubory HANA samostatně.

Jsou-li standardní HANA zálohy na vyhrazený systém HANA záložní soubor, jeden může také zkopírujte konfigurační soubory do stejného systému zálohování souborů a pak všechno dohromady zkopírovat do cíle konečné úložiště jako studené úložiště objektů blob.

### <a name="sap-hana-cockpit"></a>Řídicí panel SAP HANA

SAP HANA nově nabízí možnost monitorovat a spravovat SAP HANA přes prohlížeč. Také umožňuje zpracování zálohování SAP HANA a proto může sloužit jako alternativu k SAP HANA Studio a ABAP DBACOCKPIT (naleznete v tématu [řídicí panel SAP HANA](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) Další informace).

![Tento obrázek ukazuje obrazovce správy na řídicí panel databáze SAP HANA](media/sap-hana-backup-guide/image004.png)

Tento obrázek ukazuje obrazovce správy na řídicí panel databáze SAP HANA a zálohování dlaždice na levé straně. Zobrazuje dlaždici zálohování vyžaduje odpovídající uživatelská oprávnění pro účet pro přihlášení.

![Zálohy lze sledovat v SAP HANA řídicího panelu, pokud nejsou probíhající](media/sap-hana-backup-guide/image005.png)

Zálohy můžete sledovat v SAP HANA řídicí panel, zatímco jsou probíhající a po dokončení, jsou k dispozici všechny podrobnosti zálohování.

![Příklad použití Firefox na virtuálním počítači Azure SLES 12 Desktop Gnome](media/sap-hana-backup-guide/image006.png)

Na předchozích snímcích obrazovky nebyly provedeny z virtuálního počítače Windows Azure. Tohle je příklad použití na virtuálním počítači Azure SLES 12 Desktop Gnome Firefox. Zobrazuje možnost definovat plány zálohování SAP HANA v řídícím SAP HANA. Během jednoho můžete také vidět, navrhuje data a času jako předpona pro záložní soubory. V SAP HANA Studio výchozí předponou je &quot;COMPLETE\_DATA\_zálohování&quot; při provádění úplnou zálohu souborů. Doporučuje se použít jedinečnou předponu.

### <a name="sap-hana-backup-encryption"></a>Šifrování zálohování SAP HANA

SAP HANA nabízí šifrování dat a protokolů. Pokud nejsou šifrovaná data systému SAP HANA a protokolů, pak záloh nejsou šifrované. Záleží zákazníkovi použít nějaký způsob řešení třetí strany pro šifrování záloh SAP HANA. Zobrazit [dat a šifrování svazku protokolu](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) a zjistěte další informace o šifrování SAP HANA.

V Microsoft Azure může zákazník pomocí funkce šifrování virtuálních počítačů IaaS k šifrování. Například jeden použít vyhrazený datových disků připojených k virtuálnímu počítači, které se používají k ukládání záloh SAP HANA a vytvářet kopie z těchto disků.

Služba Azure Backup dokáže zpracovat šifrovaných virtuálních počítačů/disků (viz [zálohování a obnovení šifrovaných virtuálních počítačů pomocí služby Azure Backup](../../../backup/backup-azure-vms-encryption.md)).

Další možností je udržovat virtuální počítač SAP HANA a jeho disků bez šifrování a ukládání záložních souborů SAP HANA v účtu úložiště, pro které bylo povoleno šifrování (viz [šifrování služby Azure Storage pro neaktivní uložená Data](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Nastavení testu

### <a name="test-virtual-machine-on-azure"></a>Testovací virtuální počítač v Azure

Aby bylo možné provést našich testováních, instalace SAP HANA ve Virtuálním počítači GS5 Azure byl použit pro následující testy zálohování a obnovení. Zásady jsou stejné jako u virtuálních počítačů řady M-Series.

![Tento obrázek ukazuje část Přehled webu Azure portal pro HANA testovacího virtuálního počítače](media/sap-hana-backup-guide/image007.png)

Tento obrázek ukazuje část Přehled webu Azure portal pro HANA testovacího virtuálního počítače.

### <a name="test-backup-size"></a>Velikost zálohy testu

![Na tomto obrázku byla získána z konzole zálohování v HANA Studio a zobrazuje velikost záložního souboru 229 GB pro server indexu HANA](media/sap-hana-backup-guide/image008.png)

Fiktivní byl se vyplní data, abyste získali velikost celkové množství dat zálohování více než 200 GB pro odvození data realistické výkonu. Na obrázku byla získána z konzole zálohování v HANA Studio a zobrazuje velikost záložního souboru 229 GB pro server indexu HANA. Pro testy se použil výchozí zálohování předponu "COMPLETE_DATA_BACKUP" v SAP HANA Studio. Skutečné produkční systémy musí být definován předponu užitečnější. Řídicí panel SAP HANA navrhuje data a času.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testovací nástroje pro kopírování souborů přímo do služby Azure storage

Přenos souborů zálohování SAP HANA přímo do úložiště objektů blob v Azure nebo sdílené složky Azure, byl nástroj blobxfer použít, protože podporuje obě cíle a je možné snadno integrovat do skriptů pro automatizaci z důvodu jeho rozhraní příkazového řádku. Nástroj blobxfer je k dispozici na [Githubu](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Odhad velikosti zálohy testu

Je důležité pro odhad velikosti zálohy systému SAP HANA. Tento odhad pomáhá zlepšit výkon tak, že definujete velikost maximální záložní soubor pro počet záložních souborů, protože během kopírování souboru. (Tyto informace jsou vysvětleny dále v tomto článku). Také jeden musíte rozhodnout, jestli se má provést úplnou zálohu nebo rozdílové zálohy (přírůstkové, nebo rozdílové).

Naštěstí je jednoduché příkaz SQL, který odhaduje velikost zálohování souborů: **vyberte \* z M\_zálohování\_velikost\_odhady** (naleznete v tématu [odhad Prostor nutný v systému souborů pro zálohování dat](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![Výstup tohoto příkazu SQL odpovídající téměř úplně stejné skutečné velikosti úplná zálohování na disk](media/sap-hana-backup-guide/image009.png)

Výstup tohoto příkazu SQL u testovacího systému, odpovídá prakticky skutečná velikost úplná data zálohy na disku.

### <a name="test-hana-backup-file-size"></a>Velikost záložního souboru HANA testu

![Konzola zálohování HANA Studio umožňuje z nich se má omezení maximální velikost souboru HANA záložních souborů](media/sap-hana-backup-guide/image010.png)

Konzola zálohování HANA Studio umožňuje z nich se má omezení maximální velikost souboru HANA záložních souborů. V ukázkové prostředí tato funkce umožňuje získat více záložních souborů menší místo jednoho 230 GB záložní soubor. Menší velikost souboru má významný dopad na výkon (najdete v příslušném článku [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Souhrn

Podle následující tabulky popisují výhody a nevýhody řešení pro zálohování databáze SAP HANA na virtuálních počítačích Azure s výsledky testu.

**Zálohování SAP HANA do systému souborů a poté zkopírujte záložní soubory do konečného místa určení zálohování**

|Řešení                                           |V oblasti IT                                 |Nevýhody                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Zachovat HANA zálohování na disky virtuálních počítačů                      |Žádné další správa aktivit     |Eats místní místa na disku virtuálního počítače           |
|Nástroj Blobxfer zkopírujte záložní soubory do úložiště objektů blob |Paralelismus, aby se zkopírovat víc souborů, možnost použití cool blob storage | Údržba další nástroje a vlastní skripty | 
|Kopírování objektu BLOB prostřednictvím Powershellu nebo rozhraní příkazového řádku                    |Žádné další nástroje potřebné, můžete nakonfigurovat pomocí Azure Powershellu nebo rozhraní příkazového řádku |ruční proces zákazník má k zajištění skriptování a správu zkopírovaných objektů blob pro obnovení|
|Zkopírujte do sdílené složky systému souborů NFS                                  |Následné zpracování souborů zálohy na jiný virtuální počítač bez dopadu na serveru HANA|Pomalý proces kopírování|
|Kopírovat Blobxfer na službu Azure File                |Nebude vyžadovat značné množství místa na místní disky virtuálních počítačů|Žádné přímé zápisu podpory podle HANA zálohování, omezení velikosti sdílené složky aktuálně na 5 TB|
|Azure Backup Agent                                 | Bude preferovaným řešením         | Aktuálně není k dispozici v Linuxu    |



**Zálohování SAP HANA založené na snímcích úložiště**

|Řešení                                           |V oblasti IT                                 |Nevýhody                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Služba Azure Backup                               | Umožňuje zálohování virtuálních počítačů podle snímky objektů blob | Pokud nepoužíváte obnovení na úrovni souboru, vyžaduje vytvoření nového virtuálního počítače pro proces obnovení, který potom vyplývá potřeba nový licenční klíč SAP HANA|
|Snímky objektů blob ručně                              | Možnost vytvoření a obnovení konkrétní disky virtuálních počítačů beze změny jedinečné ID virtuálního počítače|Všechny ručně prováděné činnosti, což je třeba provést zákazníkem|

## <a name="next-steps"></a>Další postup
* [SAP HANA Azure Backup na úrovni souborů](sap-hana-backup-file-level.md) popisuje souborové záložní možnost.
* [Zálohování SAP HANA založené na snímcích úložiště](sap-hana-backup-storage-snapshots.md) popisuje zálohování možnost založených na snímcích úložiště.
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
