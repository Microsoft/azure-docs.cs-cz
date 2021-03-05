---
title: Vyberte Synchronizace souborů Azure zásady cloudové vrstvy | Microsoft Docs
description: Podrobnosti o tom, co je potřeba vzít v úvahu při výběru Synchronizace souborů Azure zásad vytváření vrstev cloudu
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/24/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2ed1b8162c49ccc26cb98dd02897a9c40f809d14
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204380"
---
# <a name="choose-cloud-tiering-policies"></a>Zvolit zásady cloudového vrstvení

Tento článek poskytuje pokyny pro uživatele, kteří vyberou a upravují zásady cloudových vrstev. Před čtením tohoto článku se ujistěte, že rozumíte tomu, jak funguje cloudová vrstva. Základní informace o vrstvení cloudu najdete v tématu [pochopení synchronizace souborů Azureho vrstvení cloudu](storage-sync-cloud-tiering-overview.md). Podrobné vysvětlení zásad cloudových vrstev s příklady najdete v tématu [synchronizace souborů Azure zásad clouding](storage-sync-cloud-tiering-policy.md).

## <a name="limitations"></a>Omezení
- Ve svazku systému Windows není podporováno vrstvení cloudu.

- Pokud máte kvótu pro správce prostředků na úrovni svazku, můžete i nadále povolit vrstvení cloudu. Jakmile nastavíte kvótu správce prostředků souborového serveru, rozhraní API pro zobrazení volného místa, které se nazývá, automaticky nahlásí volné místo na svazku podle nastavení kvóty. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Minimální velikost souboru pro soubor na vrstvu

U agentů verze 9 a novějších je minimální velikost souboru na vrstvu založená na velikosti clusteru systému souborů. Minimální velikost souboru způsobilá pro vytváření vrstev cloudu se počítá podle dvojnásobku velikosti clusteru a minimálně 8 KB. Následující tabulka ilustruje minimální velikosti souborů, které je možné rozvrstvit, na základě velikosti clusteru svazku:

|Velikost clusteru svazků (bajty) |Soubory této velikosti nebo větší lze převrstveny  |
|----------------------------|---------|
|4 KB nebo menší (4096)      | 8 kB    |
|8 KB (8192)                 | 16 kB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 kB   |
|64 KB (65536)    | 128 kB  |

Velikosti clusterů až do 64 KB se v současné době podporují, ale u větších velikostí nefunguje vrstva cloudu.

Všechny systémy souborů používané systémem Windows organizují pevný disk na základě velikosti clusteru (označované také jako velikost alokační jednotky). Velikost clusteru představuje nejmenší množství místa na disku, které lze použít k uložení souboru. Když velikosti souborů nejdou na sudý násobek velikosti clusteru, je potřeba použít další místo pro uložení souboru do další násobky velikosti clusteru.

Synchronizace souborů Azure se podporuje na svazcích NTFS s Windows Serverem 2012 R2 a novějším. Následující tabulka popisuje výchozí velikosti clusterů při vytváření nového svazku NTFS s Windows serverem 2019.

|Velikost svazku    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB – 16 TB   | 4 kB                |
|16TB – 32 TB   | 8 kB                |
|32 TB – 64 TB   | 16 kB               |
|64 TB – 128 TB  | 32 KB               |
|128TB – 256 TB | 64 KB (starší verze) |
|256 TB – 512 TB| 128 kB              |
|512 TB – 1 PB  | 256 kB              |
|1 PB – 2 PB    | 512 kB              |
|2 TB – 4 PB    | 1024 KB             |
|4 TB – 8 TB    | 2048 KB (maximální velikost)  |
|> 8 TB         | nepodporováno       |

Při vytváření svazku je možné ručně naformátovat svazek s jinou velikostí clusteru. Pokud svazek vychází ze starší verze Windows, můžou se výchozí velikosti clusterů lišit také. [Tento článek obsahuje další podrobnosti o výchozích velikostech clusteru.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) I v případě, že zvolíte velikost clusteru menší než 4 KB, limit 8 KB jako nejmenší velikost souboru, který může být vrstvený, bude stále platit. (I v případě, že by technicky 2x velikost clusteru byla rovna méně než 8 KB)

Důvod absolutního minima se nachází v způsobu, jakým systém souborů NTFS ukládá extrémně malé soubory – 1 KB až 4 soubory velikosti KB. V závislosti na dalších parametrech svazku je možné, že malé soubory nejsou uloženy v clusteru na disku vůbec. Je možné, že budete chtít ukládat takové soubory přímo do hlavní tabulky souborů svazku nebo do záznamu MFT. Bod rozboru vrstvy cloudu se vždycky ukládá na disk a zabírá přesně jeden cluster. Vrstvení takových malých souborů může skončit bez úspory místa. V extrémních případech může dokonce dokončit větší místo s povoleným vytvářením vrstev cloudu. Aby se předešlo ochraně, nejmenší velikost souboru, na který se vrstva cloudu bude nacházet, je 8 KB až o 4 KB nebo menší velikost clusteru. 

## <a name="selecting-your-initial-policies"></a>Výběr vašich počátečních zásad

Obecně platí, že když povolíte vrstvení cloudu na koncovém bodu serveru, měli byste pro každý koncový bod serveru vytvořit jednu místní virtuální jednotku. Izolování koncového bodu serveru usnadňuje pochopení způsobu, jakým funguje vrstva cloudu, a patřičně upraví zásady. Synchronizace souborů Azure ale funguje i v případě, že máte na stejné jednotce více koncových bodů serveru. Podrobnosti najdete v části [více koncových bodů serveru v místním svazku](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) . Doporučujeme vám také, abyste při prvním povolení vrstvení cloudu zachovali zásady data zakázané a volné místo na svazku přibližně o 10% až 20%. Pro většinu svazků souborového serveru je obvykle nejlepší volbou 20% volného místa na svazku.

Pro zjednodušení a jasně pochopit, jak budou položky vrstveny, doporučujeme, abyste primárně nastavili zásady volného místa svazku a zachovali vaše zásady data v případě potřeby. Doporučujeme to proto, že většina zákazníků si vyplní místní mezipaměť co nejvíce aktivních souborů a navrství zbytek ke cloudu. Zásady data ale můžou být užitečné, pokud chcete aktivně uvolnit místo na místním disku a víte, že se soubory na koncovém bodu serveru přistupovaly po uplynutí počtu dnů zadaných v zásadách kalendářních dat. nemusíte je uchovávat místně. Nastavení zásad data uvolňuje cennou kapacitu místního disku pro ostatní koncové body na stejném svazku pro ukládání více souborů do mezipaměti.

Po nastavení zásad Sledujte odchozí přenosy a upravte obě zásady odpovídajícím způsobem. Doporučujeme zejména, abyste zjistili, jak velikost volání **vrstev cloudu** a **Velikost navracení do vrstev cloudu vyvolá** metrikami aplikace v Azure monitor. Informace o tom, jak monitorovat odchozí data, najdete v tématu [monitorování vrstvení cloudu](storage-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>Úprava zásad

Pokud se množství souborů trvale odvolají z Azure, které je větší, než budete potřebovat, možná budete mít k dispozici více souborů Hot, než je místo, kam je chcete uložit na svazek na místním serveru. Pokud je to možné, zvyšte velikost svého místního svazku, nebo snižte procento zásad volného místa ve svazku v malých přírůstcích. Snížení procentuální hodnoty volného místa na svazku může mít také negativní následky. Vyšší počet změn ve vaší datové sadě vyžaduje více volného místa – pro nové soubory a navrácení "studených" souborů. Vrstvení se provádí v průběhu až jedné hodiny a pak potřebuje dobu zpracování, což je důvod, proč byste měli mít na svazku vždy dost volného místa.

Udržování většího množství dat znamená snížení nákladů na výstup, protože v Azure bude vráceno méně souborů, ale také vyžaduje větší množství místních úložišť, která se týkají vlastní ceny. 

Při úpravách zásad pro volné místo svazku se množství dat, která byste měli zachovat v místním prostředí, určuje pomocí následujících faktorů: šířky pásma, vzoru přístupu k datové sadě a rozpočtu. U připojení s nízkou šířkou pásma budete možná chtít, abyste měli k dispozici více místních dat, abyste uživatelům zajistili minimální prodlevu. V opačném případě je můžete v daném období založit na míru četnosti změn. Příklad: Pokud víte, že 10% velikosti datové sady 1 TB nebo se k ní aktivně otevírá každý měsíc, možná budete chtít zachovat 100 GB místní, aby nedošlo k často nevracení souborů se změnami. Pokud je svazek 2 TB, měli byste zachovat 5% (nebo 100 GB) místní, což znamená, že zbývající 95% je procento volného místa na svazku. Měli byste ale přidat vyrovnávací paměť pro tečky s vyšším počtem změn – jinými slovy, počínaje větším procentem volného místa na svazku a pak ji v případě potřeby upravit později.

## <a name="standard-operating-procedures"></a>Standardní operační postupy

- Při první migraci do služby soubory Azure prostřednictvím Synchronizace souborů Azure je vrstva cloudu závislá na počátečním nahrání.
- Vrstvení cloudu kontroluje dodržování zásad na úrovni volného místa a data každých 60 minut.
- Použití přepínače/LFSM v nástroji Robocopy při migraci souborů umožní synchronizaci souborů a vrstvení cloudu za účelem vytvoření prostoru během počátečního nahrávání. 
- Pokud dojde k vrstvení před vytvořením heatmapu, soubory budou vrstveny podle naposledy upraveného časového razítka.

## <a name="next-steps"></a>Další kroky
* [Plánování nasazení Synchronizace souborů Azure](storage-sync-files-planning.md)
