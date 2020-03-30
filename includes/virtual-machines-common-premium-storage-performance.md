---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 88b19257a6a7d335e6a928a9eaf7526fbfd5b02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75942790"
---
## <a name="application-performance-indicators"></a>Ukazatele výkonnosti aplikace

Posuzujeme, zda aplikace funguje dobře nebo nepomocí ukazatelů výkonu, jako je, jak rychle aplikace zpracovává požadavek uživatele, kolik dat aplikace zpracovává na požadavek, kolik požadavků je zpracování aplikace v konkrétní doba, jak dlouho musí uživatel čekat na odpověď po odeslání žádosti. Technické termíny pro tyto ukazatele výkonu jsou VOPS, Propustnost nebo šířka pásma a Latence.

V této části budeme diskutovat o společných ukazatelích výkonu v kontextu úložiště Premium. V následující části Shromažďování požadavků na aplikace se dozvíte, jak měřit tyto ukazatele výkonu pro vaši aplikaci. Později v optimalizaci výkonu aplikací se dozvíte o faktorech ovlivňujících tyto ukazatele výkonu a doporučení pro jejich optimalizaci.

## <a name="iops"></a>IOPS

VOPS nebo vstupní a výstupní operace za sekundu je počet požadavků, které vaše aplikace odesílá na disky úložiště za jednu sekundu. Operace vstupu a výstupu může být čtení nebo zápis, sekvenční nebo náhodné. Online zpracování transakcí (OLTP) aplikace, jako je on-line maloobchodní webové stránky je třeba zpracovat mnoho souběžných požadavků uživatelů okamžitě. Požadavky uživatelů jsou vložení a aktualizace intenzivní databázové transakce, které aplikace musí zpracovat rychle. Proto OLTP aplikace vyžadují velmi vysoké vipoje. Tyto aplikace zpracovávají miliony malých a náhodných požadavků vi. Pokud máte takovou aplikaci, musíte navrhnout infrastrukturu aplikace pro optimalizaci pro viposlužby. V další části *Optimalizace výkonu aplikace*podrobně popisujeme všechny faktory, které je třeba zvážit, abyste získali vysoké viposlužby.

Když ke svému virtuálnímu počítači s vysokou kapacitou připojíte disk úložiště úrovně Premium, Azure pro vás zřídí zaručenou hodnotu IOPS podle specifikace disku. Například disk P50 zřídí 7500 IOPS. Každý virtuální počítač s vysokou kapacitou má také konkrétní limit IOPS, který dokáže plnit. Například standardní virtuální měn GS5 má limit 80 000 viops.

## <a name="throughput"></a>Propustnost

Propustnost nebo šířka pásma je množství dat, které aplikace odesílá na disky úložiště v zadaném intervalu. Pokud vaše aplikace provádí operace vstupu a výstupu s velkými velikostmi jednotek vstupně-výstupníjednotky, vyžaduje vysokou propustnost. Aplikace datového skladu mají tendenci vydávat operace náročné na skenování, které přistupují k velkým částem dat najednou a běžně provádějí hromadné operace. Jinými slovy, tyto aplikace vyžadují vyšší propustnost. Pokud máte takovou aplikaci, musíte navrhnout její infrastrukturu pro optimalizaci propustnost. V další části podrobně diskutujeme o faktorech, které musíte naladit, abyste toho dosáhli.

Když připojíte disk úložiště premium k virtuálnímu počítači s vysokým měřítkem, Azure zřídí propustnost podle této specifikace disku. Například disk P50 zřídí propustnost 250 MB za sekundu. Každý virtuální počítač s vysokou kapacitou má také konkrétní limit propustnosti, který dokáže plnit. Například virtuální počítač Standard GS5 má maximální propustnost 2000 MB za sekundu.

Existuje vztah mezi propustností a vipou, jak je znázorněno v níže uvedeném vzorci.

![Vztah vipovodů a propustnost](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Proto je důležité určit optimální propustnost a hodnoty VOPS, které vaše aplikace vyžaduje. Jak se pokusíte optimalizovat jeden, druhý také dostane ovlivněny. V pozdější části *Optimalizace výkonu aplikací*budeme diskutovat podrobněji o optimalizaci IOPS a propustnost.

## <a name="latency"></a>Latence

Latence je čas potřebný k aplikaci přijímat jeden požadavek, odeslat na disky úložiště a odeslat odpověď klientovi. Toto je kritické měřítko výkonu aplikace kromě vipoje a propustnost. Latence disk úložiště premium je čas potřebný k načtení informací pro požadavek a sdělit zpět do aplikace. Úložiště Premium poskytuje konzistentní nízké latence. Prémiové disky jsou navrženy tak, aby poskytovaly jednociferné milisekundové latence pro většinu operací vi. Pokud povolíte ukládání hostitelů Jen pro čtení do mezipaměti na disky úložiště premium, můžete získat mnohem nižší latenci čtení. Budeme diskutovat o ukládání disků do mezipaměti podrobněji v pozdější části o *optimalizaci výkonu aplikací*.

Při optimalizaci aplikace získat vyšší viposlužby a propustnost, bude mít vliv na latenci vaší aplikace. Po optimalizaci výkonu aplikace vždy vyhodnoťte latenci aplikace, abyste se vyhnuli neočekávanému chování s vysokou latencí.

Následující operace roviny ovládacího prvku na spravovaných discích mohou zahrnovat přesun disku z jednoho umístění úložiště do jiného. To je řízena prostřednictvím kopie dat na pozadí, která může trvat několik hodin na dokončení, obvykle méně než 24 hodin v závislosti na množství dat na discích. Během této doby může aplikace zaznamenat vyšší než obvyklou latenci čtení, protože některé čtení může být přesměrováno do původního umístění a může trvat déle. Neexistuje žádný vliv na latenci zápisu během tohoto období.

- Aktualizujte typ úložiště.
- Odpojte a připojte disk z jednoho virtuálního počítače do druhého.
- Vytvořte spravovaný disk z virtuálního pevného disku.
- Vytvořte spravovaný disk ze snímku.
- Převod nespravovaných disků na spravované disky

## <a name="performance-application-checklist-for-disks"></a>Kontrolní seznam aplikací výkonu pro disky

Prvním krokem při navrhování vysoce výkonných aplikací spuštěných ve službě Azure Premium Storage je pochopení požadavků na výkon vaší aplikace. Po shromáždění požadavků na výkon můžete optimalizovat aplikaci tak, aby bylo dosaženo co nejoptimálnějšího výkonu.

V předchozí části jsme vysvětlili společné ukazatele výkonu, VOPS, propustnost a latence. Je nutné určit, které z těchto ukazatelů výkonu jsou důležité pro vaši aplikaci poskytovat požadované uživatelské prostředí. Například vysoké vipoje nejdůležitější oltp aplikace zpracování miliony transakcí v sekundě. Vzhledem k tomu, vysoká propustnost je důležité pro aplikace datového skladu zpracování velkého množství dat v sekundě. Extrémně nízká latence je zásadní pro aplikace v reálném čase, jako jsou webové stránky pro streamování živého videa.

Dále změřte požadavky na maximální výkon vaší aplikace po celou dobu její životnosti. Jako začátek použijte níže uvedený ukázkový kontrolní seznam. Zaznamenejte maximální požadavky na výkon během normálních, špičkových a mimohodinových pracovních vytížení. Určením požadavků pro všechny úrovně úloh budete moci určit celkový požadavek na výkon vaší aplikace. Například normální pracovní vytížení webových stránek elektronického obchodu budou transakce, které slouží po většinu dní v roce. Špičkou pracovní zátěže webových stránek budou transakce, které slouží během prázdnin nebo speciálních prodejních akcí. Zatížení ve špičce se obvykle dochází po omezenou dobu, ale může vyžadovat, aby vaše aplikace škálovat dvakrát nebo vícekrát jeho normální provoz. Zjistěte požadavky na 50 percentilů, 90 percentilů a 99 percentilů. To pomáhá odfiltrovat všechny odlehlé hodnoty v požadavcích na výkon a můžete zaměřit své úsilí na optimalizaci pro správné hodnoty.

## <a name="application-performance-requirements-checklist"></a>Kontrolní seznam požadavků na výkon aplikace

| **Požadavky na výkonnost** | **50 percentilů** | **90 percentilů** | **99 Percentil** |
| --- | --- | --- | --- |
| Max. Transakce za sekundu | | | |
| % operací čtení | | | |
| % operací zápisu | | | |
| % náhodných operací | | | |
| % sekvenčních operací | | | |
| Velikost požadavku vi. | | | |
| Průměrná propustnost | | | |
| Max. Propustnost | | | |
| Min. Latence | | | |
| Průměrná latence | | | |
| Max. Procesor | | | |
| Průměrný procesor | | | |
| Max. Memory (Paměť) | | | |
| Průměrná paměť | | | |
| Hloubka fronty | | | |

> [!NOTE]
> Měli byste zvážit škálování těchto čísel na základě očekávaného budoucího růstu vaší aplikace. Je vhodné plánovat růst dopředu, protože by mohlo být těžší změnit infrastrukturu pro pozdější zlepšení výkonu.

Pokud máte existující aplikaci a chcete přejít do úložiště Premium, nejprve vytvořte kontrolní seznam výše pro existující aplikaci. Potom vytvořte prototyp vaší aplikace v úložišti Premium storage a navrhněte aplikaci na základě pokynů popsaných v *části Optimalizace výkonu aplikací* v pozdější části tohoto dokumentu. V dalším článku jsou popsány nástroje, které můžete použít ke shromáždění měření výkonu.

### <a name="counters-to-measure-application-performance-requirements"></a>Čítače pro měření požadavků na výkon aplikace

Nejlepší způsob, jak měřit požadavky na výkon vaší aplikace, je použití nástrojů pro sledování výkonu poskytovaných operačním systémem serveru. Můžete použít PerfMon pro Windows a iostat pro Linux. Tyto nástroje zachycují čítače odpovídající každému rozměru vysvětlenému ve výše uvedené části. Je nutné zachytit hodnoty těchto čítačů, když vaše aplikace běží jeho normální, špička a mimo pracovní dobu úlohy.

Čítače PerfMon jsou k dispozici pro procesor, paměť a každý logický disk a fyzický disk serveru. Při použití disky úložiště premium s virtuálním počítačem, čítače fyzické disky jsou pro každý disk úložiště premium a logické čítače disků jsou pro každý svazek vytvořený na disky úložiště premium. Je nutné zachytit hodnoty pro disky, které hostují úlohy aplikace. Pokud existuje mapování jeden na jednoho mezi logickými a fyzickými disky, můžete odkazovat na čítače fyzických disků; jinak odkazovat na čítače logických disků. V Linuxu generuje příkaz iostat zprávu o využití procesoru a disku. Sestava využití disku poskytuje statistiky pro fyzické zařízení nebo oddíl. Pokud máte databázový server s daty a protokoly na samostatných discích, shromážděte tato data pro oba disky. Níže uvedená tabulka popisuje čítače disků, procesorů a paměti:

| Čítač | Popis | Perfmon | Iostat (Iostat) |
| --- | --- | --- | --- |
| **VOPS nebo transakce za sekundu** |Počet vstupně-va/v požadavků vydaných na disk úložiště za sekundu. |Čtení disku/s <br> Zápisy na disk/s |Tps <br> r/s <br> w/s |
| **Čtení a zápisy na disku** |% operací čtení a zápisu provedených na disku. |% času čtení disku <br> % doby zápisu na disk |r/s <br> w/s |
| **Propustnost** |Množství dat přečtených z disku nebo zapsaných na disk za sekundu. |Čtení bajtů disku/s <br> Bajty zápisu disku/s |kB_read/s <br> kB_wrtn/s |
| **Latence** |Celkový čas dokončení požadavku vi. |Průměrný disk ový sk/čtení <br> Průměrný disk sec/Zápis |await <br> svctm |
| **Velikost vo** |Velikost vstupně-va/v požadavky problémy s disky úložiště. |Průměrný počet bajtů disku a čtení <br> Průměrné bajty disku/zápis |avgrq-sz |
| **Hloubka fronty** |Počet nevyřízených vstupně-va/v požadavků čekajících na čtení nebo zápis na disk úložiště. |Aktuální délka fronty disku |avgqu-sz |
| **Max. Paměti** |Velikost paměti potřebné ke hladkému spuštění aplikace |% potvrzených bajtů při použití |Použití vmstatu |
| **Max. Cpu** |Množství procesoru potřebného ke hladkému spuštění aplikace |% času procesoru |%util |

Další informace o [iostat](https://linux.die.net/man/1/iostat) a [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Optimalizace výkonu aplikací

Hlavní faktory, které ovlivňují výkon aplikace spuštěné v úložišti Premium jsou Povaha požadavků vo, velikost virtuálního počítače, velikost disku, Počet disků, ukládání disků do mezipaměti, multithreading a hloubka fronty. Některé z těchto faktorů můžete ovládat pomocí knoflíků poskytovaných systémem. Většina aplikací nemusí poskytnout možnost změnit velikost vi a hloubku fronty přímo. Například pokud používáte SQL Server, nelze zvolit velikost vo a hloubku fronty. SQL Server zvolí optimální velikost vo a hodnoty hloubky fronty pro dosažení nejvyššího výkonu. Je důležité pochopit účinky obou typů faktorů na výkon aplikace, takže můžete zřídit příslušné prostředky pro splnění potřeb výkonu.

V této části naleznete kontrolní seznam požadavků aplikace, který jste vytvořili, a zjistěte, kolik potřebujete k optimalizaci výkonu aplikace. Na základě toho budete moci určit, které faktory z této části budete muset naladit. Chcete-li sledovat vliv každého faktoru na výkon vaší aplikace, spusťte nástroje pro srovnávání v nastavení aplikace. Postup spuštění běžných srovnávacích nástrojů ve Windows a virtuálních počítačích s Windows a Linuxem najdete v článku benchmarkingu, který je na konci propojen.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Přehled optimalizace vipovodů, propustnosti a latence

Níže uvedená tabulka shrnuje faktory výkonu a kroky potřebné k optimalizaci vstupně-výstupních operací, propustnosti a latence. Části následující tento souhrn bude popisovat každý faktor je mnohem hlubší.

Další informace o velikostech virtuálních počítačů a o viposlužbách, propustnosti a latenci dostupné pro každý typ virtuálního počítače najdete v [tématu Velikosti virtuálních počítačů v Linuxu](../articles/virtual-machines/linux/sizes.md) nebo [velikosti virtuálních počítačových virtuálních počítačích s Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Propustnost** | **Latence** |
| --- | --- | --- | --- |
| **Ukázkový scénář** |Podniková aplikace OLTP vyžadující velmi vysoké transakce za sekundu. |Aplikace enterprise data skladování, která zpracovává velké množství dat. |Téměř v reálném čase aplikace, které vyžadují okamžité odpovědi na požadavky uživatelů, jako je online hraní. |
| Výkonnostní faktory | &nbsp; | &nbsp; | &nbsp; |
| **Velikost vo** |Menší velikost vi. |Větší velikost vio na výnosy vyšší propustnost. | &nbsp;|
| **Velikost virtuálního počítače** |Použijte velikost virtuálního počítače, která nabízí viops větší než vaše požadavky na aplikaci. |Použijte velikost virtuálního počítače s omezením propustností větší než vaše požadavky na aplikaci. |Použijte velikost virtuálního počítače, která nabízí omezení škálování větší než vaše požadavky na aplikaci. |
| **Velikost disku** |Použijte velikost disku, který nabízí viops větší než vaše požadavky na aplikaci. |Použijte velikost disku s omezením propustností větší než je požadavek aplikace. |Použijte velikost disku, který nabízí omezení měřítka větší než vaše požadavky na aplikaci. |
| **Omezení velikosti virtuálního počítače a disku** |Limit videa VOPS zvolené velikosti virtuálního počítače by měl být větší než celkový vipos řízený disky úložiště k němu připojené. |Limit propustnosti zvolené velikosti virtuálního počítače by měl být větší než celková propustnost řízená disky úložiště premium, které jsou k němu připojeny. |Limity škálování zvolené velikosti virtuálního počítače musí být větší než celkové limity škálování připojených disků úložiště premium. |
| **Ukládání do mezipaměti disku** |Povolte mezipaměť jen pro čtení na disky úložiště premium s operacemi Pro čtení těžkých operací, abyste získali vyšší funkce Read IOPS. | &nbsp; |Povolte mezipaměť jen pro čtení na disky úložiště premium s náročné operace připraven získat velmi nízké latence čtení. |
| **Prokládání disku** |Použijte více disků a protřepejte je dohromady, abyste získali kombinovaný vyšší limit viops a propustnost. Kombinovaný limit na virtuální počítače by měl být vyšší než kombinované limity připojených disků premium. | &nbsp; | &nbsp; |
| **Velikost pruhu** |Menší velikost prokládání pro náhodný malý vzor VO, který je vidět v aplikacích OLTP. Například použijte velikost proklápěcí 64 KB pro sql server OLTP aplikace. |Větší velikost proužku pro sekvenční velký vzor vi vapo u aplikací datového skladu. Například použijte velikost prokládání 256 kB pro aplikaci datového skladu serveru SQL Server. | &nbsp; |
| **Multithreading** |Pomocí multithreadingu můžete do úložiště Premium vysunout vyšší počet požadavků, což povede k vyšším viponám a propustnostem. Například na serveru SQL Server nastavit vysokou hodnotu MAXDOP přidělit více procesorů sql serveru. | &nbsp; | &nbsp; |
| **Hloubka fronty** |Větší hloubka fronty poskytuje vyšší viops. |Větší hloubka fronty poskytuje vyšší propustnost. |Menší hloubka fronty poskytuje nižší latence. |

## <a name="nature-of-io-requests"></a>Povaha žádostí o vo.

Požadavek vstupně-výstupních operací je jednotka operace vstupu a výstupu, kterou bude vaše aplikace provádět. Identifikace povahy vifičné žádosti, náhodné nebo sekvenční, čtení nebo zápis, malé nebo velké, vám pomůže určit požadavky na výkon vaší aplikace. Je důležité pochopit povahu žádostí o va), aby se správná rozhodnutí při navrhování infrastruktury aplikace. Vasinové vatesty musí být rozloženy rovnoměrně, aby bylo dosaženo co nejlepšího výkonu.

Velikost vi je jedním z nejdůležitějších faktorů. Velikost vstupně-výstupních operací je velikost požadavku na operaci vstupu a výstupu vygenerovaný vaší aplikací. Velikost vod má významný vliv na výkon, zejména na VOPS a šířku pásma, které je aplikace schopna dosáhnout. Následující vzorec ukazuje vztah mezi VOPS, vstupně-diagramem a šířkou pásma/propustností.  
    ![](media/premium-storage-performance/image1.png)

Některé aplikace umožňují změnit jejich velikost vi, zatímco některé aplikace ne. Například SQL Server určuje optimální velikost vi o sobě a neposkytuje uživatelům žádné knoflíky změnit. Na druhé straně oracle poskytuje parametr s názvem [DB\_BLOCK\_SIZE,](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) pomocí kterého můžete nakonfigurovat velikost požadavku vstupně-v.i. databáze.

Pokud používáte aplikaci, která neumožňuje změnit velikost vi, použijte pokyny v tomto článku k optimalizaci výkonu klíčových ukazatelů výkonu, který je nejvíce relevantní pro vaši aplikaci. Například:

* Aplikace OLTP generuje miliony malých a náhodných požadavků vi. Chcete-li zpracovat tyto typy požadavků vi, musíte navrhnout infrastrukturu aplikace získat vyšší vipoje.  
* Aplikace datového skladu generuje velké a sekvenční vi požadavky. Chcete-li zpracovat tyto typy požadavků vi, musíte navrhnout infrastrukturu aplikace získat vyšší šířku pásma nebo propustnost.

Pokud používáte aplikaci, která umožňuje změnit velikost vi, použijte toto pravidlo pro velikost vo kromě jiných pokynů pro výkon,

* Menší velikost vipro získat vyšší viops. Například 8 KB pro aplikaci OLTP.  
* Větší velikost vstupně-pásma pro získání vyšší šířky pásma/propustnost. Například 1024 KB pro aplikaci datového skladu.

Zde je příklad, jak můžete vypočítat VOPS a propustnost/šířku pásma pro vaši aplikaci. Zvažte aplikaci pomocí disku P30. Maximální vstupně-úspěchy a propustnost/šířka pásma, kterých může disk P30 dosáhnout, je 5000 VOPS a 200 MB za sekundu. Nyní, pokud vaše aplikace vyžaduje maximální VOPS z disku P30 a používáte menší velikost IO, jako je 8 KB, výsledná šířka pásma, kterou budete moci získat, je 40 MB za sekundu. Pokud však vaše aplikace vyžaduje maximální propustnost/šířku pásma z disku P30 a použijete větší velikost vstupně-up, jako je 1024 KB, výsledné iOPS bude menší, 200 VOPS. Proto vylaďte velikost vstupně-up tak, aby splňovala požadavky vstupně-up vaší aplikace i propustnost/šířka pásma. Následující tabulka shrnuje různé velikosti vod a jejich odpovídající vipony a propustnost pro disk P30.

| Požadavek na aplikaci | Velikost vstupně-va/vod | IOPS | Propustnost/šířka pásma |
| --- | --- | --- | --- |
| Maximální počet vstupně-výstupních operací za sekundu |8 kB |5 000 |40 MB za sekundu |
| Maximální propustnost |1024 KB |200 |200 MB za sekundu |
| Maximální propustnost + vysoká vipova |64 kB |3,200 |200 MB za sekundu |
| Maximální iOPS + vysoká propustnost |32 KB |5 000 |160 MB za sekundu |

Chcete-li získat iOPS a šířku pásma vyšší než maximální hodnota jednoho disku úložiště premium, použijte více disků premium prokládaných dohromady. Například proužek dva disky P30 získat kombinované IOPS 10 000 VOPS nebo kombinované propustnost 400 MB za sekundu. Jak je vysvětleno v další části, musíte použít velikost virtuálního počítače, který podporuje kombinované vipony disku a propustnost.

> [!NOTE]
> Jak zvýšit buď IOPS nebo propustnost další také zvyšuje, ujistěte se, že nedosáhnete propustnosti nebo vipos omezení disku nebo virtuálního počítače při zvýšení jeden jeden.

Chcete-li sledovat vliv velikosti vi na výkon aplikace, můžete spustit nástroje pro srovnávání na vašem virtuálním počítači a na disky. Vytvořte více testovacích běhů a použijte různé velikosti vodpro každý běh zobrazit dopad. Další podrobnosti naleznete v článku benchmarkingu, který je na konci propojen.

## <a name="high-scale-vm-sizes"></a>Velikosti virtuálních počítače ve vysokém měřítku

Když začnete navrhovat aplikaci, jedna z prvních věcí, kterou musíte udělat, je vybrat virtuální počítač, který bude hostovat vaši aplikaci. Úložiště Premium storage je dodáváno s velikostmi virtuálních počítačů ve velkém měřítku, které můžou spouštět aplikace vyžadující vyšší výpočetní výkon a vysoký výkon vstupně-videa místního disku. Tyto virtuální počítače poskytují rychlejší procesory, vyšší poměr paměti k jádru a jednotku SSD (SSD) pro místní disk. Příklady virtuálních zařízení ve vysokém měřítku podporujících úložiště Premium jsou virtuální zařízení řady DS a GS.

Virtuální počítače ve vysokém měřítku jsou k dispozici v různých velikostech s různým počtem jader procesoru, paměti, operačního systému a dočasné velikosti disku. Každá velikost virtuálního počítače má také maximální počet datových disků, které můžete připojit k virtuálnímu počítače. Proto vybraná velikost virtuálního počítače bude mít vliv na to, kolik zpracování, paměti a kapacity úložiště je k dispozici pro vaši aplikaci. Ovlivňuje také náklady na výpočetní prostředky a úložiště. Níže jsou například specifikace největší velikosti virtuálního počítače v řadě DS a řady GS:

| Velikost virtuálního počítače | Procesorová jádra | Memory (Paměť) | Velikosti disků virtuálního počítače | Max. datové disky | Velikost mezipaměti | IOPS | Omezení vion mezipaměti šířky pásma |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Místní SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB za sekundu |4 000 vipos a 33 MB za sekundu |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Místní SSD = 896 GB |64 |4224 GB |80 000 IOPS <br> 2 000 MB za sekundu |5 000 vibepů a 50 MB za sekundu |

Úplný seznam všech dostupných velikostí virtuálních aplikací Azure najdete v části [Velikosti virtuálních aplikací windows](../articles/virtual-machines/windows/sizes.md) nebo [virtuálních počítačích s Linuxem](../articles/virtual-machines/linux/sizes.md). Zvolte velikost virtuálního počítače, která může splňovat a škálovat podle požadovaných požadavků na výkon aplikace. Kromě toho při výběru velikostí virtuálních počítače vezměte v úvahu následující důležité aspekty.

*Omezení měřítka*  
Maximální limity viposlužby na virtuální virtuální počítače a na disk se liší a jsou na sobě nezávislé. Ujistěte se, že aplikace řídí viops v rámci omezení virtuálního počítače, stejně jako prémie disky k němu připojené. V opačném případě dojde k omezení výkonu aplikace.

Předpokládejme například, že požadavek aplikace je maximálně 4 000 vipos. K dosažení tohoto cíle zřídíte disk P30 na virtuálním počítači DS1. Disk P30 může dodat až 5 000 IOPS. Virtuální měnový virtuální mon to však omezuje na 3 200 VOPS. V důsledku toho bude výkon aplikace omezen limitem virtuálního volání na 3 200 viops a bude snížen výkon. Chcete-li této situaci zabránit, zvolte virtuální modul a velikost disku, které budou splňovat požadavky aplikace.

*Náklady na provoz*  
V mnoha případech je možné, že celkové náklady na provoz pomocí úložiště Premium jsou nižší než při použití standardního úložiště.

Zvažte například aplikaci vyžadující 16 000 viops. K dosažení tohoto výkonu budete\_potřebovat virtuální počítač Standard D14 Azure IaaS, který může poskytnout maximální vigbs 16 000 pomocí 32 disků se standardním úložištěm 1 TB. Každý disk se standardním úložištěm o velikosti 1 TB může dosáhnout maximálně 500 vs. Odhadované náklady na tento virtuální virtuální mon za měsíc budou 1 570 USD. Měsíční náklady na 32 standardních paměťových disků budou $1,638. Odhadované celkové měsíční náklady budou 3 208 USD.

Pokud jste však hostovali stejnou aplikaci v úložišti Premium Storage, budete potřebovat menší velikost virtuálního počítače a méně disků úložiště premium, čímž se sníží celkové náklady. Standardní\_virtuální modul DS13 může splnit požadavek 16 000 VOPS pomocí čtyř disků P30. Virtuální modul DS13 má maximální vipony 25 600 a každý disk P30 má maximální vipony 5 000. Celkově lze dosáhnout této konfigurace 5 000 x 4 = 20 000 VOPS. Odhadované náklady na tento virtuální virtuální mon to měsíc budou 1 003 USD. Měsíční náklady na čtyři p30 premium úložné disky bude 544,34 dolarů. Odhadované celkové měsíční náklady budou 1 544 USD.

Níže uvedená tabulka shrnuje rozdělení nákladů tohoto scénáře pro standardní a prémiové úložiště.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Náklady na virtuální virtuální měna za měsíc** |$1,570.58 (Standardní\_D14) |$1,003.66 (Standardní\_DS13) |
| **Náklady na disky za měsíc** |$1,638.40 (32 x 1 TB disky) |544,34 dolarů (4 x P30 disky) |
| **Celkové náklady za měsíc** |3 208,98 USD |1 544,34 USD |

*Distribuce Linuxu*  

S Azure Premium Storage získáte stejnou úroveň výkonu pro virtuální počítače se systémem Windows a Linux. Podporujeme mnoho chutí distribucí Linuxu, a můžete vidět kompletní seznam [zde](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Je důležité si uvědomit, že různé distribuce jsou vhodnější pro různé typy úloh. Uvidíte různé úrovně výkonu v závislosti na distro vaše úlohy běží na. Otestujte distribuce Linuxu s vaší aplikací a vyberte si ten, který funguje nejlépe.

Při spuštění Linuxu s úložištěm Premium, zkontrolujte nejnovější aktualizace o požadovaných ovladačích, abyste zajistili vysoký výkon.

## <a name="premium-storage-disk-sizes"></a>Velikosti disků úložiště Premium

Azure Premium Storage nabízí různé velikosti, takže si můžete vybrat ten, který nejlépe vyhovuje vašim potřebám. Každá velikost disku má jiné omezení měřítka pro vstupně-up, šířku pásma a úložiště. V závislosti na požadavcích na aplikaci a velikosti virtuálního počítače ve velkém měřítku zvolte správnou velikost diskového disku úložiště Premium. V následující tabulce jsou uvedeny velikosti disků a jejich možnosti. Velikosti P4, P6, P15, P60 a P80 jsou v současné době podporovány pouze pro spravované disky.

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Počet vybraných disků závisí na zvolené velikosti disku. Ke splnění požadavků aplikace můžete použít jeden disk P50 nebo více disků P10. Při rozhodování vezměte v úvahu níže uvedené aspekty.

*Omezení škálování (VOPS a propustnost)*  
Omezení vstupně-up a propustnost každé velikosti disku Premium se liší a nezávisle na omezení škálování virtuálních počítačů. Ujistěte se, že celkové vstupně-upa a propustnost z disků jsou v mezích škálování zvolené velikosti virtuálního počítače.

Například pokud požadavek aplikace je maximálně 250 MB/s propustnost a používáte virtuální modul DS4 s jedním diskem P30. Virtuální ms DS4 může udělit propustnost až 256 MB/s. Jeden disk P30 má však limit propustnost 200 MB/s. V důsledku toho bude aplikace omezena na 200 MB/s z důvodu omezení disku. Chcete-li tento limit překonat, zřizujte více než jeden datový disk virtuálnímu počítači nebo změňte velikost disků na P40 nebo P50.

> [!NOTE]
> Čtení obsluhovaná mezipamětí nejsou zahrnuta v seznamu vstupně-sad ů disku a propustnost, a proto nepodléhá omezením disku. Mezipaměť má samostatné vipony a limit propustnost na virtuální hod.
>
> Například zpočátku vaše čtení a zápisy jsou 60 MB/s a 40 MB/s v uvedeném pořadí. V průběhu času se mezipaměť zahřeje a slouží stále více a více čtení z mezipaměti. Potom můžete získat vyšší propustnost zápisu z disku.

*Počet disků*  
Posouzením požadavků na aplikaci určete počet disků, které budete potřebovat. Každá velikost virtuálního počítače má také omezení počtu disků, které můžete připojit k virtuálnímu počítači. Obvykle se jedná o dvojnásobek počtu jader. Ujistěte se, že velikost virtuálního počítače, kterou zvolíte, podporuje potřebný počet disků.

Nezapomeňte, že disky úložiště Premium mají vyšší výkon ve srovnání s disky standard storage. Proto pokud migrujete aplikaci z virtuálního počítače Azure IaaS pomocí standardního úložiště do úložiště Premium, budete pravděpodobně potřebovat méně disků premium k dosažení stejného nebo vyššího výkonu pro vaši aplikaci.

## <a name="disk-caching"></a>Mezipaměť disku

Virtuální počítače s vysokým měřítkem, které využívají Azure Premium Storage, mají vícevrstvou technologii ukládání do mezipaměti nazvanou BlobCache. BlobCache používá kombinaci paměti VIRTUAL Machine RAM a místního ssd pro ukládání do mezipaměti. Tato mezipaměť je k dispozici pro trvalé disky úložiště Premium a místní disky virtuálního počítače. Ve výchozím nastavení je toto nastavení mezipaměti nastaveno na čtení a zápis pro disky operačního systému a jen pro čtení pro datové disky hostované v úložišti Premium. Při ukládání disků do mezipaměti na disky úložiště Premium mohou virtuální počítače ve vysokém měřítku dosáhnout extrémně vysoké úrovně výkonu, která přesahuje základní výkon disku.

> [!WARNING]
> Ukládání disků do mezipaměti není podporováno pro disky 4 TiB a větší. Pokud více disků jsou připojeny k virtuálnímu počítači, každý disk, který je menší než 4 TiB bude podporovat ukládání do mezipaměti.
>
> Při změně nastavení mezipaměti disku Azure se cílový disk odpojí a znovu připojí. Pokud se jedná o disk operačního systému, virtuální modul se restartuje. Před změnou nastavení mezipaměti disku zastavte všechny aplikace nebo služby, které by mohly být tímto přerušením ovlivněny.

Další informace o tom, jak blobCache funguje, najdete v příspěvku blogu Uvnitř [úložiště Azure Premium](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) Storage.

Je důležité povolit mezipaměť na pravé sadě disků. Zda byste měli povolit ukládání disku do mezipaměti na disku premium nebo ne, bude záviset na vzoru pracovního vytížení, který bude disk zpracovávat. Níže uvedená tabulka ukazuje výchozí nastavení mezipaměti pro operační disky a datové disky.

| **Typ disku** | **Výchozí nastavení mezipaměti** |
| --- | --- |
| Disk OS |ReadWrite |
| Datový disk |ReadOnly |

Níže jsou uvedeny doporučené nastavení mezipaměti disku pro datové disky,

| **Nastavení mezipaměti disku** | **doporučení, kdy toto nastavení použít** |
| --- | --- |
| Žádný |Konfigurace mezipaměti hostitele jako Žádná pro disky s vysokým obsahem zápisu a pro zápis. |
| ReadOnly |Konfigurace mezipaměti hostitele jako jen pro čtení pro disky jen pro čtení a pro čtení. |
| ReadWrite |Nakonfigurujte mezipaměť hostitele jako ReadWrite pouze v případě, že vaše aplikace správně zpracovává zápis dat uložených v mezipaměti na trvalé disky v případě potřeby. |

*Readonly*  
Konfigurací ukládání do mezipaměti Pouze pro čtení na datových discích úložiště Premium můžete dosáhnout nízké latence čtení a získat velmi vysoké funkce Pro čtení viopů a propustnosti pro vaši aplikaci. To je způsobeno dvěma důvody,

1. Čtení provedená z mezipaměti, která je v paměti virtuálního počítače a místní ssd disk, jsou mnohem rychlejší než čtení z datového disku, který je v úložišti objektů blob Azure.  
1. Úložiště Premium nepočítá čtení podávané z mezipaměti, směrem k vstupně-toops disku a propustnost. Proto vaše aplikace je schopna dosáhnout vyšší celkové vipony a propustnost.

*Readwrite*  
Ve výchozím nastavení mají disky operačního systému povoleno ukládání do mezipaměti ReadWrite. Nedávno jsme přidali podporu pro ukládání do mezipaměti ReadWrite na datových discích. Pokud používáte ukládání do mezipaměti ReadWrite, musíte mít správný způsob zápisu dat z mezipaměti na trvalé disky. Například SQL Server zpracovává zápis dat v mezipaměti na disky trvaléúložiště samostatně. Použití mezipaměti ReadWrite s aplikací, která nezpracovává trvalé požadované údaje může vést ke ztrátě dat, pokud dojde k chybě virtuálního virtuálního serveru.

*Žádné*  
V současné době **None** je podporována pouze na datových discích. Není podporována na discích operačního systému. Pokud na disku operačního systému nastavíte **žádné,** přepíše to interně a nastaví na **readonly**.

Jako příklad můžete použít tyto pokyny pro SQL Server spuštěný na úložišti Premium následujícím způsobem,

1. Nakonfigurujte mezipaměť "ReadOnly" na disky úložiště premium, které hostují datové soubory.  
   a.  Rychlé čtení z mezipaměti snižuje dobu dotazu serveru SQL Server, protože datové stránky jsou načteny mnohem rychleji z mezipaměti ve srovnání přímo z datových disků.  
   b.  Obsluha čtení z mezipaměti znamená, že je k dispozici další propustnost z paměťových disků premium. SQL Server můžete použít tuto další propustnost pro načítání více datových stránek a další operace, jako je zálohování a obnovení, dávkové zatížení a index znovu.  
1. Nakonfigurujte mezipaměť "Žádný" na disky úložiště premium, které jsou hostující soubory protokolu.  
   a.  Soubory protokolu mají především operace náročné na zápis. Proto nemají prospěch z mezipaměti Jen pro čtení.

## <a name="optimize-performance-on-linux-vms"></a>Optimalizace výkonu na virtuálních počítačích s Linuxem

Pro všechny prémiové disky SSD nebo ultra disky s mezipamětí nastavenou na **ReadOnly** nebo **None**je nutné zakázat "bariéry" při připojení systému souborů. V tomto scénáři nepotřebujete překážky, protože zápisy na disky úložiště premium jsou pro tato nastavení mezipaměti trvalé. Po úspěšném dokončení požadavku na zápis byla data zapsána do trvalého úložiště. Chcete-li zakázat "bariéry", použijte jednu z následujících metod. Vyberte ten pro systém souborů:
  
* Pro **reiserFS**, zakázat bariéry, použijte možnost `barrier=none` připojení. (Chcete-li povolit `barrier=flush`bariéry, použijte .)
* Chcete-li zakázat bariéry **ext3/ext4**, použijte možnost `barrier=0` připojení. (Chcete-li povolit `barrier=1`bariéry, použijte .)
* Pro **XFS**, chcete-li `nobarrier` zakázat bariéry, použijte možnost připojení. (Chcete-li povolit `barrier`bariéry, použijte .)
* Pro disky úložiště premium s mezipamětí nastavenou na **ReadWrite**povolte překážky pro odolnost zápisu.
* Aby popisky svazku přetrvávaly po restartování virtuálního počítače, je nutné aktualizovat /etc/fstab s odkazy na disky s univerzálně jedinečným identifikátorem (UUID). Další informace najdete [v tématu Přidání spravovaného disku do virtuálního počítače s Linuxem](../articles/virtual-machines/linux/add-disk.md).

Následující linuxové distribuce byly ověřeny pro prémiové SSD. Pro lepší výkon a stabilitu s prémiovými ssd disy doporučujeme upgradovat virtuální počítače na jednu z těchto verzí nebo novější. 

Některé verze vyžadují nejnovější linuxové integrační služby (LIS), v4.0, pro Azure. Chcete-li stáhnout a nainstalovat distribuci, postupujte podle odkazu uvedeného v následující tabulce. Přidáváme obrázky do seznamu, jak jsme kompletní ověření. Naše ověření ukazují, že výkon se u jednotlivých obrázků liší. Výkon závisí na charakteristikách pracovního vytížení a nastavení image. Různé image jsou vyladěny pro různé druhy úloh.

| Distribuce | Version | Podporované jádro | Podrobnosti |
| --- | --- | --- | --- |
| Ubuntu | 12.04 nebo novější| 3.2.0-75.110+ | &nbsp; |
| Ubuntu | 14.04 nebo novější| 3.13.0-44.73+  | &nbsp; |
| Debian | 7.x, 8.x nebo novější| 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12 nebo novější| 3.12.36-38.1+ | &nbsp; |
| SUSE | SLES 11 SP4 nebo novější| 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+ nebo novější| 3.18.4+ | &nbsp; |
| CentOS | 6.5, 6.6, 6.7, 7.0 nebo novější| &nbsp; | [Vyžaduje se LIS4](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Viz poznámka v další části* |
| CentOS | 7.1+ nebo novější| 3.10.0-229.1.2.el7+ | [DOPORUČENO LIS4](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Viz poznámka v další části* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+, nebo novější | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+nebo novější | &nbsp; | UEK4 nebo RHCK |
| Oracle | 7.0-7.1 nebo novější | &nbsp; | UEK4 nebo RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |
| Oracle | 6.4-6.7 nebo novější | &nbsp; | UEK4 nebo RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |

### <a name="lis-drivers-for-openlogic-centos"></a>Lis ovladače pro OpenLogic CentOS

Pokud používáte virtuální aplikace OpenLogic CentOS, nainstalujte nejnovější ovladače následujícím příkazem:

```
sudo yum remove hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
sudo reboot
```

V některých případech výše uvedený příkaz také inovuje jádro. Pokud je vyžadována aktualizace jádra, budete možná muset po restartu znovu spustit výše uvedené příkazy, abyste plně nainstalovali balíček Microsoft-Hyper-v.


## <a name="disk-striping"></a>Prokládání disků

Když je virtuální počítače ve velkém měřítku připojen s několika virtuálními disky úložiště premium, disky lze prokládat dohromady a agregovat jejich IOP, šířku pásma a úložnou kapacitu.

V systému Windows můžete použít prostory úložiště k prokládání disků dohromady. Je nutné nakonfigurovat jeden sloupec pro každý disk ve fondu. V opačném případě může být celkový výkon proklápěcího svazku nižší, než bylo očekáváno, z důvodu nerovnoměrného rozložení provozu na discích.

Důležité: Pomocí ui Správce serveru můžete nastavit celkový počet sloupců až 8 proprokládaný svazek. Při připojování více než osm disků vytvořte svazek pomocí prostředí PowerShell. Pomocí prostředí PowerShell můžete nastavit počet sloupců rovnající se počtu disků. Například pokud existuje 16 disků v jedné prokládané sadě; zadejte 16 sloupců v parametru *NumberOfColumns* rutiny *New-VirtualDisk* PowerShell.

Na Linuxu použijte nástroj MDADM k prokládání disků dohromady. Podrobné kroky týkající se prokládání disků na Linuxu naleznete v [části Configure Software RAID on Linux](../articles/virtual-machines/linux/configure-raid.md).

*Velikost pruhu*  
Důležitou konfigurací prokládání disku je velikost proklápění. Velikost proklápění nebo velikost bloku je nejmenší blok dat, který aplikace může adresovat na proklápěči svazku. Velikost proklápěcího objektu závisí na typu aplikace a jejím vzoru požadavku. Pokud zvolíte nesprávnou velikost prokládání, může to vést k vychýlení vi.

Například pokud vo požadavek generovaný vaší aplikací je větší než velikost proužku disku, systém úložiště zapíše přes hranice prokládanýjednotky na více než jeden disk. Když je čas na přístup k těmto datům, bude muset hledat přes více než jeden proužkovací jednotky k dokončení požadavku. Kumulativní účinek takového chování může vést k podstatnému snížení výkonu. Na druhou stranu pokud velikost požadavku vo je menší než velikost prokládání a pokud je náhodné povahy, požadavky vo vod může sečíst na stejném disku způsobuje kritické místo a nakonec snižuje výkon vi.

V závislosti na typu úlohy, kterou vaše aplikace spouštěla, zvolte vhodnou velikost proklápěcího objektu. Pro náhodné malé požadavky vi použijte menší velikost proužku. Vzhledem k tomu, že pro velké sekvenční vodících požadavků použít větší velikost prokládání. Zjistěte doporučení velikosti proužků pro aplikaci, kterou budete provozovat v úložišti Premium. Pro SQL Server nakonfigurujte velikost proklápěcí 64 KB pro úlohy OLTP a 256 KB pro úlohy datových skladů. Další informace najdete [v tématu Doporučené postupy pro výkon pro SQL Server na virtuálních počítačích Azure.](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance)

> [!NOTE]
> Můžete protáhnout maximálně 32 disků úložiště premium na virtuálním počítači řady DS a 64 disků úložiště premium na virtuálním počítači řady GS.

## <a name="multi-threading"></a>Vícevláknové

Azure navrhl platformu Premium Storage tak, aby byla masivně paralelní. Aplikace s více vlákny proto dosahuje mnohem vyššího výkonu než aplikace s jedním podprocesem. Vícevláknová aplikace rozdělí své úkoly na více vláken a zvyšuje efektivitu jejího provádění využitím prostředků virtuálního počítače a disku na maximum.

Například pokud vaše aplikace běží na jednom virtuálním počítači jádra pomocí dvou vláken, procesor můžete přepínat mezi dvěma vlákny k dosažení účinnosti. Zatímco jedno vlákno čeká na dokončení vi. Tímto způsobem dvě vlákna může dosáhnout více než jedno vlákno by. Pokud má virtuální virtuální msvíce než jedno jádro, dále snižuje dobu chodu, protože každé jádro může provádět úlohy paralelně.

Pravděpodobně nebude možné změnit způsob, jakým aplikace mimo polici implementuje jedno podprocesy nebo více vláken. Sql Server je například schopen zpracování více procesorů a vícejádrových. Sql Server však rozhodne, za jakých podmínek bude využívat jeden nebo více vláken ke zpracování dotazu. Může spouštět dotazy a vytvářet indexy pomocí více vláken. Pro dotaz, který zahrnuje spojování velkých tabulek a řazení dat před návratem k uživateli, SQL Server bude pravděpodobně používat více vláken. Uživatel však nemůže řídit, zda SQL Server spustí dotaz pomocí jednoho vlákna nebo více vláken.

Existují nastavení konfigurace, které můžete změnit ovlivnit toto vícevláknové nebo paralelní zpracování aplikace. Například v případě SQL Server je maximální stupeň konfigurace paralelismu. Toto nastavení s názvem MAXDOP umožňuje konfigurovat maximální počet procesorů, které může SQL Server použít při paralelním zpracování. Maxdop můžete nakonfigurovat pro jednotlivé dotazy nebo operace indexu. To je výhodné, pokud chcete vyvážit prostředky systému pro aplikaci kritickou pro výkon.

Řekněme například, že vaše aplikace pomocí SQL Server provádí velký dotaz a operace indexu současně. Předpokládejme, že jste chtěli, aby operace indexu byla výkonnější ve srovnání s velkým dotazem. V takovém případě můžete nastavit hodnotu MAXDOP operace indexu, která má být vyšší než hodnota MAXDOP pro dotaz. Tímto způsobem SQL Server má větší počet procesorů, které může využít pro operaci indexu ve srovnání s počtem procesorů, které může vyhradit velký dotaz. Nezapomeňte, že nemáte kontrolu nad počtem podprocesů, které bude SQL Server používat pro každou operaci. Můžete řídit maximální počet procesorů vyhrazených pro více vláken.

Další informace o [stupně paralelismu](https://technet.microsoft.com/library/ms188611.aspx) v SQL Server. Zjistěte taková nastavení, která ovlivňují vícevláknové v aplikaci a jejich konfigurace pro optimalizaci výkonu.

## <a name="queue-depth"></a>Hloubka fronty

Hloubka fronty nebo délka fronty nebo velikost fronty je počet čekajících vsazených požadavků v systému. Hodnota hloubky fronty určuje, kolik vi operací může aplikace zařadit, které disky úložiště budou zpracovávat. Ovlivňuje všechny tři ukazatele výkonu aplikace, které jsme probrali v tomto článku viz., IOPS, propustnost a latence.

Hloubka fronty a vícevláknové operace spolu úzce souvisejí. Hodnota Hloubka fronty označuje, kolik více vláken lze dosáhnout aplikací. Pokud hloubka fronty je velký, aplikace můžete spustit více operací současně, jinými slovy více více vláken. Pokud hloubka fronty je malý, i když aplikace je více vlákna, nebude mít dostatek požadavků seřazených pro souběžné spuštění.

Aplikace mimo regál obvykle neumožňují změnit hloubku fronty, protože pokud je nastavena nesprávně, způsobí více škody než užitku. Aplikace nastaví správnou hodnotu hloubky fronty, aby získaly optimální výkon. Je však důležité pochopit tento koncept, abyste mohli řešit problémy s výkonem vaší aplikace. Můžete také sledovat účinky hloubky fronty spuštěním nástrojů pro srovnávání ve vašem systému.

Některé aplikace poskytují nastavení, která ovlivňují hloubku fronty. Například maxdop (maximální stupeň paralelismu) nastavení v SQL Server je vysvětleno v předchozí části. MAXDOP je způsob, jak ovlivnit hloubku fronty a více vláken, i když přímo nezmění hodnotu hloubky fronty serveru SQL Server.

*Vysoká hloubka fronty*  
Vysoká hloubka fronty zařazuje další operace na disku. Disk zná další požadavek ve frontě předem. V důsledku toho může disk naplánovat operace předem a zpracovat je v optimálním pořadí. Vzhledem k tomu, že aplikace odesílá další požadavky na disk, disk může zpracovat více paralelních vi. Nakonec aplikace bude moci dosáhnout vyšší vipoje. Vzhledem k tomu, že aplikace zpracovává více požadavků, zvyšuje se také celková propustnost aplikace.

Aplikace může obvykle dosáhnout maximální propustnosti s 8-16+ nevyřízených vi na připojený disk. Pokud hloubka fronty je jedna, aplikace není tlačí dostatek vi do systému a bude zpracovávat menší množství v daném období. Jinými slovy, menší propustnost.

Například v SQL Server, nastavení hodnoty MAXDOP pro dotaz na "4" informuje SQL Server, že můžete použít až čtyři jádra ke spuštění dotazu. SQL Server určí, co je nejlepší hodnota hloubky fronty a počet jader pro spuštění dotazu.

*Optimální hloubka fronty*  
Velmi vysoká hodnota hloubky fronty má také své nevýhody. Pokud je hodnota hloubky fronty příliš vysoká, aplikace se pokusí řídit velmi vysoké vipony. Pokud aplikace má trvalé disky s dostatečným zřízeným viops, to může negativně ovlivnit latence aplikace. Následující vzorec ukazuje vztah mezi viopy, latencí a hloubkou fronty.  
    ![](media/premium-storage-performance/image6.png)

Hloubku fronty byste neměli konfigurovat na žádnou vysokou hodnotu, ale na optimální hodnotu, která může poskytovat dostatek viopů pro aplikaci bez ovlivnění latence. Například pokud latence aplikace musí být 1 milisekunda, hloubka fronty potřebné k dosažení 5 000 VOPS je QD = 5000 x 0,001 = 5.

*Hloubka fronty pro prokládaný svazek*  
Prokládaný svazek udržujte dostatečně vysokou hloubku fronty tak, aby každý disk měl hloubku fronty ve špičce jednotlivě. Zvažte například aplikaci, která tlačí hloubku fronty 2 a jsou čtyři disky v pruhu. Dva požadavky vi přejde na dva disky a zbývající dva disky budou nečinnosti. Proto nakonfigurujte hloubku fronty tak, aby všechny disky mohly být zaneprázdněny. Níže uvedený vzorec ukazuje, jak určit hloubku fronty prokládaných svazků.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Throttling

Azure Premium Storage zřídí zadaný počet viops a propustnost v závislosti na velikosti virtuálních počítačů a velikosti disků, které zvolíte. Kdykoli se vaše aplikace pokusí řídit VOPS nebo Propustnost nad tyto limity toho, co virtuální počítač nebo disk zvládne, úložiště Premium ho omezí. To se projevuje ve formě snížení výkonu ve vaší aplikaci. To může znamenat vyšší latenci, nižší propustnost nebo nižší vipony. Pokud premium storage není omezení, vaše aplikace může zcela selhat překročením co jeho prostředky jsou schopny dosáhnout. Tak, aby se zabránilo problémům s výkonem z důvodu omezení, vždy zřídit dostatečné prostředky pro vaši aplikaci. Vezměte v úvahu, co jsme diskutovali v části velikosti virtuálních počítačů a disk velikosti výše. Benchmarking je nejlepší způsob, jak zjistit, jaké prostředky budete potřebovat k hostování aplikace.

## <a name="next-steps"></a>Další kroky
