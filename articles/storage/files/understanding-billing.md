---
title: Vysvětlení fakturace služby soubory Azure | Microsoft Docs
description: Přečtěte si, jak interpretovat modely fakturace zřízené a průběžné platby pro sdílené složky Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6bb608492327baae958c32be05d8f2a1bb4dbfbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99226637"
---
# <a name="understand-azure-files-billing"></a>Vysvětlení fakturace souborů Azure
Soubory Azure nabízí dva odlišné modely fakturace: zřízené a průběžné platby. Zřízený model je k dispozici jenom pro sdílené složky Premium, což jsou sdílené složky nasazené v typu **účtu úložiště souborů** . Model průběžných plateb je dostupný jenom pro standardní sdílené složky, což jsou sdílené složky nasazené v typu účtu úložiště pro **obecné účely verze 2 (GPv2)** . Tento článek vysvětluje, jak oba modely pracují, aby vám pomohly pochopit měsíční poplatky za soubory Azure.

Informace o cenách služby soubory Azure najdete na [stránce s cenami souborů Azure](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="storage-units"></a>Jednotky úložiště    
Služba soubory Azure používá pro reprezentaci kapacity úložiště základní 2 jednotky: KiB, MiB, GiB a TiB. Operační systém může nebo nemusí používat stejnou jednotku měření nebo inventarizace.

### <a name="windows"></a>Windows

Operační systém Windows i soubory Azure měří kapacitu úložiště pomocí systému inventury Standard-2, ale při označování jednotek je k dispozici rozdíl. Služba soubory Azure zaměří kapacitu úložiště se základními a 2 jednotkami měření, zatímco systém Windows označí kapacitu úložiště v jednotkách základní-10. Při vytváření sestav kapacity úložiště Windows nepřevede svoji kapacitu úložiště ze základu 2 na Base-10.

|Akronym  |Definice  |Jednotka  |Systém Windows zobrazí jako  |
|---------|---------|---------|---------|
|KiB     |1 024 bajtů         |kibibyte         |KB (kilobajty)         |
|IGMP     |1 024 KiB (1 048 576 bajtů)         |mebibyte         |MB (megabajtů)         |
|GiB     |1024 MiB (1 073 741 824 bajtů)         |gibibajt         |GB (gigabajt)         |
|TiB     |1024 GiB (1 099 511 627 776 bajtů)         |tebibyte         |TB (terabajt)         |

### <a name="macos"></a>macOS

Podívejte se, jak zařízení se systémem [iOS a MacOS nahlásí kapacitu úložiště](https://support.apple.com/HT201402) na webu společnosti Apple a určí, který systém inventury se používá.

### <a name="linux"></a>Linux

Každý operační systém nebo jednotlivý software může použít jiný systém počítání. Informace o tom, jak nahlásit kapacitu úložiště, najdete v dokumentaci.

## <a name="provisioned-model"></a>Zřízený model
Azure Files používá zřízený model pro sdílené složky Premium. V zřízeném podnikovém modelu proaktivně zadáte službě soubory Azure požadavky na úložiště, které se místo toho účtují na základě toho, co využijete. To se podobá zakoupenému hardwaru v místním prostředí, v tom, že když zřizujete sdílenou složku Azure s určitou velikostí úložiště, platíte za toto úložiště bez ohledu na to, jestli ho používáte, nebo ne, stejně jako Vy nezačínáte platit náklady na fyzické médium, když začnete používat místo. Na rozdíl od zakoupení fyzických médií je možné zřízené sdílené složky dynamicky škálovat nahoru nebo dolů v závislosti na charakteristikách výkonu úložiště a vstupně-výstupních operací.

Když zřizujete sdílenou složku Premium, určíte, kolik GiBs vaše zatížení vyžaduje. Každý GiB, který zřídíte, opravňuje k dodatečným vstupně-výstupním operacím a propustnosti s pevným poměrem. Kromě standardních hodnot IOPS, pro které jste si jistí, každá z těchto souborů Premium podporuje rozpínání na nejvyšší úsilí. Vzorce pro vstupně-výstupní operace a propustnost jsou následující:

- Směrného plánu IOPS = 400 + 1 * zřízené GiB. (Až do maximálního počtu 100 000 IOPS).
- Limit počtu shluků = MAX (4000, 3 * základní IOPS).
- Přenosová rychlost = 60 MiB/s + 0,06 * zřízené GiB.
- Rychlost příchozího přenosu dat = 40 MiB/s + 0,04 * zřízené GiB.

Zřízenou velikost sdílené složky lze kdykoli zvýšit, ale lze ji snížit až 24 hodin od posledního zvýšení. Po čekání na 24 hodin bez zvýšení kvóty můžete kvótu sdílení snížit tolikrát, kolikrát chcete, a to až do jejich opětovného zvýšení. Změny v rozsahu IOPS/propustnosti budou platit během několika minut od změny zřízené velikosti.

Velikost zřízené sdílené složky můžete snížit pod použitou GiB. Pokud to uděláte, neztratíte data, ale bude se vám stále účtovat využitá velikost a bude se vám účtovat výkon (počet vstupně-výstupních operací za sekundu, propustnost a shlukové IOPS) zřízené sdílené složky, nikoli použitou velikost.

Následující tabulka ilustruje několik příkladů těchto vzorců pro zřízené velikosti sdílených složek:

|Kapacita (GiB) | Směrný IOPS | Shlukový IOPS | Výstup (MiB/s) | Příchozí přenosy (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Až 4 000     | 66   | 44   |
|500         | 900     | Až 4 000  | 90   | 60   |
|1 024       | 1 424   | Až 4 000   | 122   | 81   |
|5 120       | 5 520   | Až 15 360  | 368   | 245   |
|10 240      | 10 640  | Až 30 720  | 675   | 450   |
|33 792      | 34 192  | Až 100 000 | 2 088 | 1 392   |
|51 200      | 51 600  | Až 100 000 | 3 132 | 2 088   |
|102 400     | 100 000 | Až 100 000 | 6 204 | 4 136   |

Efektivní výkon sdílené složky se vztahuje na omezení sítě počítačů, dostupnou šířku pásma sítě, velikosti v/v, paralelismus, mezi mnoha dalšími faktory. Například na základě interního testování s 8 KiB velikostí vstupně-výstupních operací čtení a zápisu se může jednat o jeden virtuální počítač s Windows bez povolených souborů protokolu SMB, *Standard F16s_v2*, který je připojený ke sdílené složce Premium přes SMB, mohl dosáhnout 20 tisíc čtení IOPS a 15 000 IOPS. S 512 velikostí čtení/zápisu v/v souboru MiB může stejný virtuální počítač dosáhnout propustnosti 1,1 GiB/s a 370 propustnosti příchozího přenosu dat MiB/s. Stejný klient může dosáhnout až \~ 3x výkonu, pokud je ve sdílených složkách na úrovni Premium povolená možnost SMB vícekanálový. Pokud chcete dosáhnout maximálního výkonu, povolte v rámci více virtuálních počítačů vícekanálový a rozprostření zátěže přes protokol [SMB](storage-files-enable-smb-multichannel.md) . Některé běžné problémy s výkonem a řešení problémů najdete v [Průvodci odstraňováním potíží](storage-troubleshooting-files-performance.md) s protokolem [SMB vícekanálový](storage-files-smb-multichannel-performance.md) .

### <a name="bursting"></a>Shlukování
Pokud vaše úloha potřebuje dodatečný výkon pro splnění požadavků na špičku, může vaše sdílená složka použít kredity za sekundu a přejít nad limit počtu IOPS podle směrného plánu, aby bylo možné nabídnout výkon, který musí splňovat požadavky. Soubory úrovně Premium můžou zvýšit zatížení za IOPS až do 4 000 nebo až do násobku tří, podle toho, co je vyšší hodnota. Shlukování je automatizované a funguje na základě úvěrového systému. Shluking funguje na nejvyšší úrovni a limit shluku není zárukou. sdílené složky se můžou *zvýšit až* na maximální dobu od 60 minut.

Kredity se sčítají v rámci shlukového přenosu, kdykoli je přenos pro sdílenou složku pod směrným intervalem IOPS. Například sdílená složka 100 GiB má 500 směrný IOPS. Pokud byl skutečný provoz na sdílené složce 100 vstupně-výstupních operací pro určitý interval 1 sekund, pak je 400 nepoužitelné IOPS v kreditu do intervalu shlukování. Podobně nečinný TiB sdílená složka vyplyne nárůst kreditu na 1 424 IOPS. Tyto kredity se pak použijí později, pokud by operace překročily směrný IOPS.

Pokaždé, když sdílená složka přesáhne základní IOPS a má kredity v intervalu shlukového přenosu, dojde k nárůstu maximální povolené míry shlukové zátěže na nejvyšší úrovni. Sdílené složky můžou pokračovat v nárůstu, dokud budou kredity zbývající, maximálně 60 minut, ale na základě počtu kumulovaných kreditů. Každý v/v za rámec standardních hodnot IOPS spotřebovává jeden kredit a jakmile se všechny kredity spotřebují, bude se tato sdílená hodnota vracet do směrného IOPS.

Sdílené kredity mají tři stavy:

- Probíhá nabíhání, Pokud sdílená složka používá méně než směrný IOPS.
- Odmítnutí, Pokud sdílená složka používá více než IOPS a v režimu shlukování.
- Konstanta: Pokud sdílená složka používá přesně základní IOPS, neúčtují se ani nepoužívají žádné kredity.

Nové sdílené složky začínají úplným počtem kreditů v rámci svého shlukového intervalu. Kredity shluku se neúčtují, pokud se za vstupně-výstupní operace klesne pod základnu IOPS, a to kvůli omezení serveru.

## <a name="pay-as-you-go-model"></a>Model průběžných plateb
Soubory Azure používají pro standardní sdílené složky průběžný obchodní model s průběžnými platbami. V obchodním modelu s průběžnými platbami můžete částku, kterou platíte, určit podle toho, kolik skutečně používáte, a ne podle zřízeného množství. Na vysoké úrovni platíte náklady na množství dat uložených na disku a pak další sadu transakcí na základě využití těchto dat. Model s průběžnými platbami může být nákladově efektivní, protože nemusíte převádět do budoucích požadavků na růst nebo výkon nebo zrušit zřízení, pokud se zatížení na data v průběhu času liší. Na druhé straně může být model průběžných plateb v rámci rozpočtového procesu obtížné naplánovat také, protože model fakturace s průběžnými platbami se řídí spotřebou koncového uživatele.

### <a name="differences-in-standard-tiers"></a>Rozdíly na úrovních Standard
Když vytváříte standardní sdílenou složku, vybíráte mezi transakcemi optimalizovanou, horkou a studenou úrovní. Všechny tři úrovně jsou uloženy na přesně stejném standardním hardwaru úložiště. Hlavním rozdílem pro tyto tři úrovně jsou ceny za úložiště na REST, které jsou nižší v úrovni chladicích složek, a ceny za transakce, které jsou na úrovních chladicích dat vyšší. To znamená, že:

- Transakce optimalizovaná, jak název implikuje, optimalizuje cenu pro úlohy s vysokými transakcemi. Optimalizovaná transakce má nejvyšší cenu za úložiště na REST, ale nejnižší ceny za transakce.
- Horká je pro aktivní úlohy, které nezahrnují velký počet transakcí, a má mírně nižší cenu za úložiště dat, ale mírně vyšší ceny za transakce v porovnání s optimalizovanou transakcí. Můžete si ho představit jako prostřední základ mezi optimalizovanými a studenými úrovněmi transakce.
- Studená optimalizuje cenu pro úlohy, které nemají spoustu aktivit, a nabízí nejnižší cenu za úložiště s nižšími daty, ale nejvyšší ceny za transakce.

Pokud do optimalizované úrovně transakce vložíte úlohu s nečastým využitím, platíte za několik málo minut za měsíc, kdy provedete transakce s vaší sdílenou složkou, ale budete platit vysokou částku za náklady na úložiště dat. Pokud byste chtěli přesunout stejnou sdílenou složku na studenou vrstvu, bude se vám pořád nic za transakce za transakce neúčtují, a to jednoduše proto, že u této úlohy často neprovádíte transakce, ale studená vrstva má mnohem levnější cenu za úložiště dat. Výběr vhodné úrovně pro váš případ použití vám umožní významně snížit náklady. Výběr vhodné úrovně pro váš případ použití vám umožní významně snížit náklady.

Podobně platí, že pokud zadáte úlohu s vysokou dostupností ve studené vrstvě, platíte za transakce spoustu dalších nákladů, ale méně pro náklady na úložiště dat. To může vést k situaci, kdy se zvýšené náklady z ceny za transakce zvyšují z ceny za sníženou cenu za úložiště dat, což vám umožní platit více peněz na studenou dobu, než by to mělo být optimalizované transakce. Je možné, že některé úrovně využití, které jsou na úrovni Hot, jsou nákladově efektivní úrovně, takže studená vrstva bude dražší než optimalizace transakcí.

Vaše zatížení a úroveň aktivity budou určovat nejvyšší úroveň efektivity pro vaši standardní sdílenou složku. Nejlepším způsobem, jak vybrat nejvyšší nákladovou úroveň, je, že se můžete podívat na skutečnou spotřebu prostředků sdílené složky (data uložená, operace zápisu atd.).

### <a name="what-are-transactions"></a>Co jsou transakce?
Transakce jsou operace nebo požadavky na soubory Azure, které umožňují nahrávat, stahovat nebo jinak manipulovat s obsahem sdílené složky. Každá akce provedená u sdílené složky se překládá na jednu nebo více transakcí a na standardních sdílených složkách, které používají model fakturace s průběžnými platbami, které se vztahují na transakční náklady.

Existuje pět základních kategorií transakcí: zapsat, vypsat, číst, jiné a odstranit. Všechny operace prováděné prostřednictvím REST API nebo SMB jsou rozdělené do jedné z těchto 4 kategorií následujícím způsobem:

| Typ operace | Transakce zápisu | Výpis transakcí | Transakce čtení | Jiné transakce | Odstranit transakce |
|-|-|-|-|-|-|
| Operace správy | <ul><li>`CreateShare`</li><li>`SetFileServiceProperties`</li><li>`SetShareMetadata`</li><li>`SetShareProperties`</li></ul> | <ul><li>`ListShares`</li></ul> | <ul><li>`GetFileServiceProperties`</li><li>`GetShareAcl`</li><li>`GetShareMetadata`</li><li>`GetShareProperties`</li><li>`GetShareStats`</li></ul> | | <ul><li>`DeleteShare`</li></ul> |
| Operace s daty | <ul><li>`CopyFile`</li><li>`Create`</li><li>`CreateDirectory`</li><li>`CreateFile`</li><li>`PutRange`</li><li>`PutRangeFromURL`</li><li>`SetDirectoryMetadata`</li><li>`SetFileMetadata`</li><li>`SetFileProperties`</li><li>`SetInfo`</li><li>`SetShareACL`</li><li>`Write`</li><li>`PutFilePermission`</li></ul> | <ul><li>`ListFileRanges`</li><li>`ListFiles`</li><li>`ListHandles`</li></ul>  | <ul><li>`FilePreflightRequest`</li><li>`GetDirectoryMetadata`</li><li>`GetDirectoryProperties`</li><li>`GetFile`</li><li>`GetFileCopyInformation`</li><li>`GetFileMetadata`</li><li>`GetFileProperties`</li><li>`QueryDirectory`</li><li>`QueryInfo`</li><li>`Read`</li><li>`GetFilePermission`</li></ul> | <ul><li>`AbortCopyFile`</li><li>`Cancel`</li><li>`ChangeNotify`</li><li>`Close`</li><li>`Echo`</li><li>`Ioctl`</li><li>`Lock`</li><li>`Logoff`</li><li>`Negotiate`</li><li>`OplockBreak`</li><li>`SessionSetup`</li><li>`TreeConnect`</li><li>`TreeDisconnect`</li><li>`CloseHandles`</li><li>`AcquireFileLease`</li><li>`BreakFileLease`</li><li>`ChangeFileLease`</li><li>`ReleaseFileLease`</li></ul> | <ul><li>`ClearRange`</li><li>`DeleteDirectory`</li></li>`DeleteFile`</li></ul> |

> [!Note]  
> Systém souborů NFS 4,1 je k dispozici pouze pro sdílené složky Premium, které používají model zřízené fakturace. transakce neovlivňují fakturaci za sdílené složky prémiových souborů.

## <a name="see-also"></a>Viz také
- [Stránka s cenami souborů Azure](https://azure.microsoft.com/pricing/details/storage/files/)
- [Plánování nasazení služby soubory Azure](./storage-files-planning.md) a [Plánování nasazení synchronizace souborů Azure](./storage-sync-files-planning.md).
- [Vytvořte sdílenou složku](./storage-how-to-create-file-share.md) a [Nasaďte synchronizace souborů Azure](./storage-sync-files-deployment-guide.md).