---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e2dc82ee49b240fe562f02b38c4991c644c010d3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56334036"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure premium storage: návrh pro vysoký výkon

Tento článek obsahuje pokyny pro vytváření vysoce výkonné aplikace využívající Azure Premium Storage. Můžete použít pokyny uvedené v tomto dokumentu v kombinaci s osvědčené postupy z hlediska výkonu pro technologie, které používá vaše aplikace. Pro ilustraci, pokyny, jsme pomocí SQL serveru běžícího na Premium Storage jako příklad v tomto dokumentu.

Když jsme situacích výkonu pro vrstvy úložiště v tomto článku, je potřeba optimalizovat aplikační vrstvu. Například pokud hostujete Sharepointovou farmu na Azure Premium Storage, můžete použít SQL Server příklady v tomto článku optimalizovat databázového serveru. Kromě toho Optimalizujte webový server farmy služby SharePoint a aplikační server většina výkon.

V tomto článku se vám pomohou zodpovědět následující běžné otázky týkající se optimalizace výkonu aplikace na Azure Premium Storage

* Postupy: měření výkonu vaší aplikace?  
* Proč se zobrazuje očekávaný vysoký výkon?  
* Faktory, které ovlivňují výkon vašich aplikací ve službě Storage úrovně Premium?  
* Jak tyto faktory mají vliv na výkon vaší aplikace ve službě Storage úrovně Premium?  
* Optimalizace můžete pro vstupně-výstupních operací, šířky pásma a latencí?  

Nabízíme tyto pokyny konkrétně pro Premium Storage, protože úlohy běžící ve službě Storage úrovně Premium jsou vysoce citlivé na výkon. Uvádíme příklady, kde je to vhodné. Můžete také použít některé z těchto pokynů pro aplikace běžící na virtuálních počítačích IaaS s disky Storage úrovně Standard.

> [!NOTE]
> V některých případech možný problém výkonu disků je ve skutečnosti sítě kritickým bodem. V takových situacích doporučujeme optimalizovat vaše [výkon sítě](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md).
> Pokud váš virtuální počítač podporuje akcelerované síťové služby, by měl se ujistěte, zda že je povoleno. Pokud není povolená, můžete ji povolit na již nasazených virtuálních počítačů na obou [Windows](../articles/virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) a [Linux](../articles/virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Než začnete, pokud jste ještě na Premium Storage, nejdřív přečíst [vyberte typ, který Azure disk pro virtuální počítače IaaS](../articles/virtual-machines/windows/disks-types.md) a [Azure Storage škálovatelnost a cíle výkonnosti](../articles/storage/common/storage-scalability-targets.md) článků.

## <a name="application-performance-indicators"></a>Ukazatele výkonu aplikace

Vyhodnocení, jestli si aplikace vede dobře nebo není pomocí výkonnostních ukazatelů, jako je, jak rychle aplikace zpracovává žádost uživatele, kolik dat zpracovává aplikace každý požadavek, kolik požadavků aplikace zpracovává v konkrétní časový úsek, jak dlouho musí uživatel čekat na odpověď po odeslání žádosti. Technické podmínky pro tyto ukazatele výkonu jsou vstupně-výstupních operací, propustnost nebo šířku pásma a latencí.

V této části se budeme zabývat běžných ukazatelů výkonu v rámci služby Premium Storage. V následující části požadavky aplikací na shromažďování, se dozvíte, jak vyhodnocovat tyto ukazatele výkonu pro vaši aplikaci. Později v optimalizace výkonu aplikace se dozvíte o faktorech, které mají vliv tyto ukazatele výkonu a doporučení k optimalizaci je.

## <a name="iops"></a>IOPS

Vstupně-výstupních operací nebo vstupně výstupní operace za sekundu, je počet požadavků, které vaše aplikace odesílá do disky úložiště v jedné sekundy. Vstupně výstupní operace může číst nebo zapisovat sekvenční nebo náhodné. Online zpracování transakcí (OLTP) aplikace, jako je online maloobchodních webech potřeba zpracovat okamžitě mnoho souběžných požadavků uživatelů. Požadavky uživatelů jsou vložení a aktualizujte náročné databázové transakce, které musí aplikace rychle zpracovávají. Proto OLTP aplikace vyžadují velmi vysoké vstupně-výstupních operací. Tyto žádosti zpracovat miliony požadavků malé a náhodných vstupně-výstupních operací. Pokud máte takovou aplikaci, je třeba navrhnout aplikační infrastruktury pro optimalizaci pro vstupně-výstupních operací. V pozdější části *optimalizace výkonu aplikace*, si popíšeme podrobně všech faktorů, které musíte zvážit, chcete-li získat vysoké vstupně-výstupních operací.

Když připojíte disk úložiště úrovně premium k vaší velké škálování virtuálních počítačů, Azure zřídí pro vás zaručené počet vstupně-výstupních operací podle specifikace disku. Například P50 disk zřídí 7500 vstupně-výstupních operací. Každý měřítko velikosti virtuálního počítače má také určený limit vstupně-výstupních operací, který může tolerovat. Virtuální počítače GS5 Standard má například 80 000 vstupně-výstupních operací omezte.

## <a name="throughput"></a>Propustnost

Propustnost nebo šířku pásma je množství dat, které vaše aplikace odesílá do disky úložiště v zadaném intervalu. Pokud aplikace provádí vstupně výstupní operace s velké velikosti jednotky vstupně-výstupních operací, vyžaduje vysokou propustnost. Datový sklad aplikace mají tendenci k vydávání operace kontroly náročné na prostředky, které využívají velké části dat najednou a běžně provádět hromadné operace. Jinými slovy tyto aplikace vyžaduje vyšší propustnost. Pokud máte takovou aplikaci, je třeba navrhnout jeho infrastruktury za účelem optimalizace propustnosti. V další části si popíšeme podrobně faktorů, třeba vyladit dosáhnout.

Když připojíte k velké škálování virtuálních počítačů, Azure ustanovení propustnost podle specifikaci disku disk úložiště úrovně premium. Například P50 disk zřídí 250 MB na druhý propustnost disku. Každý měřítko velikosti virtuálního počítače má také jako limit konkrétní propustnosti, který může tolerovat. Například virtuální počítače GS5 Standard má maximální propustností 2 000 MB za sekundu.

Existuje vztah mezi propustnost a vstupně-výstupních operací, jak je znázorněno v následujícím vzorci.

![Vztah vstupně-výstupních operací a propustnosti](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Proto je důležité určit optimální propustnosti a hodnoty vstupně-výstupních operací, které vaše aplikace vyžaduje. Při pokusu o optimalizaci jedné druhé také získá vliv. V další části *optimalizace výkonu aplikace*, se budeme zabývat v další podrobnosti o optimalizaci IOPS a propustnost.

## <a name="latency"></a>Latence

Latence je dlouho trvá, aplikace pro příjem jednoho požadavku, odeslání na discích úložiště a odeslat odpověď do klienta. To je důležité míra výkon vaší aplikace kromě IOPS a propustnost. Latence disk úložiště úrovně premium je čas potřebný k načtení informací pro požadavek a komunikaci zpět do aplikace. Premium Storage poskytuje konsistentní nízkou latencí. Disky Premium jsou určená k poskytnutí latence v řádu milisekund pro většinu operací vstupně-výstupních operací. Pokud povolíte jen pro čtení hostitele ukládání do mezipaměti na disky premium storage, získáte mnohem nižší latence čtení. Pojednává o používání mezipaměti disku podrobněji v pozdější části na *optimalizace výkonu aplikace*.

Pokud chcete optimalizovat vaše aplikace, abyste získali lepší vstupně-výstupních operací a vyšší propustnost, bude mít vliv na latenci vaší aplikace. Po ladění výkonu aplikace, vždy vyhodnoceny latence aplikace, aby se zabránilo neočekávaným vysokou latencí chování.

# <a name="performance-application-checklist-for-disks"></a>Kontrolní seznam aplikace výkonu pro disky

Prvním krokem při návrhu vysoce výkonné aplikace běžící na Azure Premium Storage je pochopení požadavky na výkon vaší aplikace. Poté, co jste shromáždili požadavky na výkon, můžete optimalizovat výkon své aplikace pro dosažení optimálního výkonu.

V předchozí části jsme vysvětlení běžných ukazatelů výkonu, vstupně-výstupních operací, propustnosti a latence. Musíte určit, které tyto ukazatele výkonu jsou důležité pro vaši aplikaci pro zajištění požadovaného uživatelské prostředí. Například vysoké IOPS nejvíc aplikacím OLTP zpracování milionů transakcí za sekundu. Vzhledem k tomu, Vysoká propustnost je velmi důležité pro datový sklad aplikace zpracování velkého objemu dat za sekundu. Mimořádně nízkou latencí je zásadní pro aplikace v reálném čase, jako je živé video streamování websites.

Potom změřte požadavky na maximální výkon vaší aplikace v průběhu svého životního cyklu. Ukázka kontrolního seznamu níže použijte jako spuštění. Požadavky na maximální výkon během normální, ve špičce a době mimo špičku úlohy období si poznamenejte. Identifikaci požadavků pro všechny úrovně zatížení, budete moct určit požadavek na celkový výkon vaší aplikace. Běžné úlohy web pro elektronické obchodování například bude transakcí, které slouží během většina dní v roce. Zátěž ve špičce na webu budou transakcí, které slouží během sváteční období nebo speciální prodej události. Ve špičce je obvykle zkušení po omezenou dobu, ale můžou vyžadovat, aby škálování dvakrát nebo vícekrát normálním provozu vaší aplikace. Přečtěte si 50. percentil 90. percentil a požadavky na 99. percentilu. To pomáhá odfiltrovat všechny odlehlé hodnoty v části požadavky na výkon a můžete se soustředit své úsilí na optimalizaci pro správné hodnoty.

## <a name="application-performance-requirements-checklist"></a>Kontrolní seznam požadavků na výkon aplikace

| **Požadavky na výkon** | **50. percentil** | **90. percentil** | **99. percentilu** |
| --- | --- | --- | --- |
| Max. Transakce za sekundu | | | |
| Operace čtení % | | | |
| Operace zápisu % | | | |
| % Náhodné operace | | | |
| Sekvenční operace % | | | |
| Velikost požadavku vstupně-výstupních operací | | | |
| Průměrná propustnost | | | |
| Max. Propustnost | | | |
| Min. Latence | | | |
| Průměrná latence | | | |
| Max. Procesor | | | |
| Průměr CPU | | | |
| Max. Memory (Paměť) | | | |
| Průměrné paměti | | | |
| Hloubka fronty | | | |

> [!NOTE]
> Měli byste zvážit škálování tato čísla podle očekávané budoucí růst vaší aplikace. Je vhodné počítali s růstem předem, protože by mohlo být těžší infrastrukturu pro zlepšení výkonu později změnit.

Pokud máte existující aplikaci a chcete přejít na Premium Storage, nejprve sestavte kontrolní seznam výše pro existující aplikace. Potom, sestavíte prototyp vaší aplikace ve službě Storage úrovně Premium a navrhněte aplikaci podle pokynů popsaných v *optimalizace výkonu aplikace* v další části tohoto dokumentu. Další článek popisuje nástroje, které vám umožní získat měření výkonu.

### <a name="counters-to-measure-application-performance-requirements"></a>Čítače na míru požadavkům na výkon aplikace

Nejlepší způsob, jak měřit požadavky na výkon vaší aplikace, je použití nástroje Sledování výkonu, které jsou k dispozici v operačním systému serveru. Nástroj PerfMon pro Windows a iostat můžete použít pro Linux. Tyto nástroje zaznamenávají čítače odpovídající každá míra je vysvětleno výše v části. Hodnoty těchto čítačů musí zachytit, když vaše aplikace běží její normální, zatížení ve špičce a době mimo špičku.

Čítače výkonu jsou k dispozici pro procesor, paměť a každý logický disk a fyzický disk serveru. Pokud použijete disky storage úrovně premium s virtuálním Počítačem, jsou čítače Fyzický disk pro každý disk storage úrovně premium a čítače logický disk se pro každý svazek na disky premium storage vytvoří. Hodnoty pro disky, které hostují úlohy vaší aplikace musí zachytit. Pokud existuje mapování 1: 1 mezi logické a fyzické disky, můžete odkazovat na fyzický disk čítačů; v opačném případě odkazují na logický disk čítače. V Linuxu příkaz iostat vygeneruje sestavu využití procesoru a disku. Sestava využití disku poskytuje statistické údaje na fyzické zařízení nebo oddíl. Pokud máte databázový server pomocí protokolu a data na různých discích, shromažďování těchto dat pro oba disky. Následující tabulka popisuje čítače pro disky, procesoru a paměti:

| Čítač | Popis | PerfMon | iostat |
| --- | --- | --- | --- |
| **Vstupně-výstupních operací nebo transakcí za sekundu** |Počet vstupně-výstupní požadavky vydané na disk úložiště za sekundu. |Čtení disku/s <br> Zápis disku/s |TPS <br> r/s <br> w/s |
| **Zápisy a čtení disku** |% Čtení a zápisu operace provedené na disku. |Čas čtení disku v % <br> Čas zápisu disku v % |r/s <br> w/s |
| **Propustnost** |Objem dat číst nebo zapsat na disk za sekundu. |Bajty čtení z disku/s <br> Bajty zapisování na disk/s |kB_read/s <br> kB_wrtn/s |
| **Latence** |Celková doba nutná k dokončení žádosti o vstupně-výstupní operace disku. |Průměrná doba disku/čtení <br> Průměrná doba disku/zápis |operátor await <br> svctm |
| **Velikost vstupně-výstupních operací** |Velikost vstupně-výstupních požadavků problémy, které disky úložiště. |Průměrná disku/přečtených bajtů <br> Průměrná disku bajtů/zápis |avgrq-sz |
| **Hloubka fronty** |Počet nezpracovaných vstupně-výstupních operací žádostí o čekání číst nebo zapsat na disk úložiště. |Aktuální délka fronty disku |avgqu sz |
| **Max. Paměť** |Objem paměti vyžadované ke spuštění aplikace hladce |% Využití potvrzených bajtů |Použití vmstat |
| **Max. CPU** |Velikost procesoru potřebné ke spuštění aplikace hladce |% Času procesoru |% využití |

Další informace o [iostat](https://linux.die.net/man/1/iostat) a [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Optimalizace výkonu aplikace

Hlavní faktory, které mají vliv na výkon aplikace běžící na Premium Storage jsou povahy z vstupně-výstupní operace žádosti, velikost virtuálního počítače, velikost disků, počtu disků, používání mezipaměti disku, Multithreading a hloubka fronty. Některé z těchto faktorů může ovládacím prvkem knoflíky poskytované systémem. Většina aplikací nemusí být možné přímo měnit velikost vstupně-výstupní operace a hloubka fronty. Například pokud používáte systém SQL Server, nemůžete hloubky velikost a fronty vstupně-výstupních operací. SQL Server vybere optimální vstupně-výstupních operací velikost fronty hloubky hodnoty a většina výkon. Je potřeba vysvětlit dopady oba typy faktory na výkon vašich aplikací tak, že zřídíte příslušných prostředků podle potřeb výkonu.

V této části se odkazují na kontrolní požadavky na aplikace, kterou jste vytvořili, chcete-li určit, kolik je potřeba optimalizovat výkon vašich aplikací. Na základě toho budou moct určit faktory, které z této části budete muset vyladit. Určující každý faktor vliv na výkon vašich aplikací, nástrojích pro srovnávací testy spouštět instalace aplikace. Odkazovat [Benchmarking](#Benchmarking) oddílu na konci tohoto článku najdete postup spouštění běžných nástrojích pro srovnávací testy na Windows a virtuální počítače s Linuxem.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Optimalizace vstupně-výstupních operací, propustnosti a latence na první pohled

Následující tabulka shrnuje výkon faktorů a kroky potřebnými k optimalizaci vstupně-výstupních operací, propustnosti a latence. V částech po toto shrnutí bude popisují každou. faktor je mnohem podrobněji.

Další informace o velikostech virtuálních počítačů a na vstupně-výstupních operací, propustnosti a latence, které jsou k dispozici pro každý typ virtuálního počítače, naleznete v tématu [velikosti virtuálního počítače s Linuxem](../articles/virtual-machines/linux/sizes.md) nebo [velikosti virtuálních počítačů s Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **VSTUPNĚ-VÝSTUPNÍCH OPERACÍ** | **Propustnost** | **Latence** |
| --- | --- | --- | --- |
| **Příklad scénáře** |Enterprise OLTP aplikace, které vyžadují velmi vysokou transakce za druhé. |Podnikových datových skladů aplikace zpracování velkého objemu dat. |Téměř v reálném čase aplikace, které potřebují rychlých odpovědí na požadavky uživatelů, jako je online hry. |
| Faktory výkonu | &nbsp; | &nbsp; | &nbsp; |
| **Velikost vstupně-výstupních operací** |Menší velikost vstupně-výstupní operace poskytuje vyšší vstupně-výstupních operací. |Větší velikost vstupně-výstupní operace poskytuje vyšší propustnost. | &nbsp;|
| **Velikost virtuálního počítače** |Použijte velikost virtuálního počítače, který nabízí vstupně-výstupních operací větší než požadavků vašich aplikací. |Velikost virtuálního počítače pomocí limit propustnosti větší než požadavků vašich aplikací. |Použijte velikost virtuálního počítače, že nabízí škálovat omezení větší než požadavků vašich aplikací. |
| **Velikost disku** |Použijte velikost disku, který nabízí vstupně-výstupních operací větší než požadavků vašich aplikací. |Velikost disku pomocí limit propustnosti větší než požadavků vašich aplikací. |Použijte velikost disku, že nabízí škálovat omezení větší než požadavků vašich aplikací. |
| **Virtuální počítač a limity škálování disku** |Limit vstupně-výstupních operací vybrali velikost virtuálního počítače musí být větší než celkový počet IOPS využitím disky premium storage k němu připojená. |Limit propustnosti zvolené velikosti virtuálních počítačů musí být větší než celkové propustnosti využitím disky premium storage k němu připojená. |Limity škálování zvolené velikosti virtuálních počítačů musí být větší než limity škálování celkový počet disků připojených premium storage. |
| **Používání mezipaměti disku** |Povolte mezipaměť jen pro čtení na disky premium storage s náročné operace čtení se získat vyšší vstupně-výstupních operací čtení. | &nbsp; |Povolte mezipaměť jen pro čtení na disky premium storage připraveno náročné operace zobrazíte čtení velmi nízkou latencí. |
| **Prokládání disků** |Použít několik disků a prokládané je dohromady a získat kombinované vyšší limit IOPS a propustnost. Všimněte si, že kombinovaný limit na virtuální počítač by měl být vyšší než kombinované omezení připojené prémiové disky. | &nbsp; | &nbsp; |
| **Velikost platformy stripe** |Menší velikost stripe pro náhodné malé vstupně-výstupních operací známému z aplikace s online zpracováním transakcí. Stripe velikosti 64KB například pomocí aplikace SQL serveru OLTP. |Větší velikost stripe pro sekvenční velké vstupně-výstupních operací známému z datového skladu aplikací. Velikost 256KB prokládání například pomocí aplikace SQL Server Data warehouse. | &nbsp; |
| **Multithreading** |Pomocí multithreadingu tak, aby nabízel vyšší počet požadavků na Premium Storage, který může vést k vyšší IOPS a propustnost. Například v systému SQL Server nastavit vysokou hodnotou MAXDOP přidělit více procesorů k systému SQL Server. | &nbsp; | &nbsp; |
| **Hloubka fronty** |Větší hloubky fronty poskytuje vyšší vstupně-výstupních operací. |Větší hloubky fronty poskytuje vyšší propustnost. |Menší hloubka fronty vrací nižší latenci. |

## <a name="nature-of-io-requests"></a>Povaha požadavků na vstupně-výstupních operací

Požadavek na vstupně-výstupních operací je jednotka vstupně výstupní operace, která vaše aplikace bude provádět. Identifikace povaze vstupně-výstupní požadavky, náhodných nebo sekvenčních, čtení nebo zápisu, malé nebo velké, vám pomůže určit požadavky na výkon vaší aplikace. Je velmi důležité pro pochopení povahy požadavků na vstupně-výstupních operací na dělat správná rozhodnutí při navrhování vaší aplikační infrastruktury.

Velikost vstupně-výstupních operací je jedním z důležitých faktorů. Velikost vstupně-výstupních operací je velikost vstupně výstupní operace žádosti vygenerované aplikace. Velikost vstupně-výstupní operace má významný dopad na výkon, zejména u IOPS a šířky pásma, která je možné zajistit aplikace. Tento vzorec znázorňuje vztah mezi IOPS, vstupně-výstupní operace velikosti a šířka pásma nebo propustnosti.  
    ![](media/premium-storage-performance/image1.png)

Některé aplikace umožňují měnit jejich velikost vstupně-výstupní operace, zatímco některé aplikace nepodporují. Například SQL Server určuje optimální velikost vstupně-výstupních operací, samotného a neposkytuje uživatelům všechny knoflíky ho změnit. Na druhé straně společnost Oracle nabízí parametr s názvem [DB\_BLOKOVAT\_velikost](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) pomocí které se dají nakonfigurovat velikost požadavku vstupně-výstupních operací databáze.

Pokud používáte aplikaci, která nepovoluje můžete změnit velikost vstupně-výstupní operace, použijte pokyny v tomto článku za účelem optimalizace výkonu klíčového ukazatele výkonu, které jsou nejdůležitější pro vaši aplikaci. Například:

* Aplikace online zpracováním transakcí generuje miliony požadavků malé a náhodných vstupně-výstupních operací. Pro zpracování těchto typů požadavků na vstupně-výstupních operací, je třeba navrhnout získat vyšší vstupně-výstupních operací vaší aplikační infrastruktury.  
* Datové sklady aplikace generuje velké a sekvenčních vstupně-výstupní operace žádosti. Pro zpracování těchto typů požadavků na vstupně-výstupních operací, je třeba navrhnout infrastrukturu aplikací získat vyšší šířku pásma nebo propustnosti.

Pokud používáte aplikaci, která umožňuje změnit velikost vstupně-výstupní operace, použijte toto pravidlo pro velikost vstupně-výstupní operace kromě jiných pravidel výkonu

* Chcete-li získat vyšší vstupně-výstupních operací menší velikost vstupně-výstupních operací. Třeba 8 KB pro aplikace online zpracováním transakcí.  
* Chcete-li získat vyšší šířku pásma nebo propustnosti větší velikost vstupně-výstupních operací. Například 1 024 KB pro aplikaci data warehouse.

Tady je příklad jak vypočítat počet IOPS a propustnost nebo šířku pásma pro vaši aplikaci. Vezměte v úvahu aplikace pomocí disků P30. Maximum, které můžete dosáhnout vstupně-výstupních operací a propustnosti disku P30 nebo šířka pásma je 5000 vstupně-výstupních operací a 200 MB za sekundu v uvedeném pořadí. Nyní Pokud vaše aplikace vyžaduje maximální vstupně-výstupních operací z disků P30 a použijte menší velikost vstupně-výstupní operace, například 8 KB, výsledný šířky pásma bude moct získat je 40 MB za sekundu. Nicméně pokud vaše aplikace vyžaduje maximální propustnost nebo šířku pásma z disků P30, a použít větší velikost vstupně-výstupních operací, jako je 1 024 KB, výsledný vstupně-výstupních operací bude menší, 200 vstupně-výstupních operací. Proto se Optimalizujte velikost vstupně-výstupních operací tak, že splňuje požadavek na vstupně-výstupních operací a propustnosti nebo šířka pásma obě aplikace. Následující tabulka shrnuje různé velikosti vstupně-výstupní operace a jejich odpovídající vstupně-výstupních operací a propustnosti disku P30.

| Požadavek na aplikaci | Velikost vstupně-výstupních operací | IOPS | Propustnost nebo šířku pásma |
| --- | --- | --- | --- |
| Maximální počet vstupně-výstupních operací za sekundu |8 kB |5 000 |40 MB za sekundu |
| Maximální propustnost |1024 kB |200 |200 MB za sekundu |
| Maximální propustnost + vysoké vstupně-výstupních operací |64 kB |3,200 |200 MB za sekundu |
| Maximální počet vstupně-výstupních operací a Vysoká propustnost |32 KB. |5 000 |160 MB za sekundu |

IOPS a šířky pásma, vyšší než maximální hodnota disk úložiště jedné úrovně premium získáte použijte několik disků premium prokládané společně. Například prokládanou dva disky P30 zobrazíte součet IOPS počet 10 000 IOPS nebo kombinované propustnost 400 MB za sekundu. Jak je vysvětleno v další části, je nutné použít velikost virtuálního počítače, který podporuje kombinovaná vstupně-výstupních operací a propustnosti disku.

> [!NOTE]
> Zvyšuje se vstupně-výstupních operací nebo druhé se taky vyznačuje větší propustnost, zkontrolujte, zda že není dosáhnete propustnost nebo limity vstupně-výstupních operací disku nebo virtuálního počítače při zvýšení jednu.

Pokud chcete určující účinky velikost vstupně-výstupních operací na výkon aplikace, můžete spustit nástrojích pro srovnávací testy na virtuální počítač a disky. Vytvořit více testovacích běhů a zobrazení dopadů použít jinou velikost vstupně-výstupních operací pro každé spuštění. Odkazovat [Benchmarking](#Benchmarking) části na konci tohoto článku najdete další podrobnosti.

## <a name="high-scale-vm-sizes"></a>Velikosti virtuálních počítačů s vysokou škálovatelností

Když se pustíte do návrhu aplikace, jedním z první kroky, které je, vyberte virtuální počítač pro hostování vaší aplikace. Premium Storage se dodává s vysokou virtuálního škálování virtuálního počítače velikostí, mezi které můžete spustit aplikace, které potřebují vyšší výpočetní výkon a místní disk vysoké vstupně-výstupní výkon. Tyto virtuální počítače mají rychlejší procesory, vyšší poměr paměti k jádrům a Solid-State jednotky SSD (Solid-State Drive) pro místní disk. Vysoká škálování virtuálních počítačů služby Premium Storage podporuje příklady řady DS, DSv2 a GS virtuálních počítačů.

Vysoká škálování virtuálních počítačů jsou k dispozici v různých velikostech s různým počtem jader procesoru, paměti, operačního systému a velikost dočasného disku. Všechny velikosti virtuálních počítačů má také maximální počet datových disků, které lze připojit k virtuálnímu počítači. Proto se zvolenou velikost virtuálního počítače bude mít vliv na tom, kolik zpracování, paměť, a kapacita úložiště je k dispozici pro vaši aplikaci. Ovlivní také výpočetní prostředky a náklady na úložiště. V následující tabulce jsou specifikace největší možnou velikost virtuálního počítače DS-series, DSv2 series a GS-series:

| Velikost virtuálního počítače | Procesorová jádra | Memory (Paměť) | Velikosti disku virtuálního počítače | Max. Datové disky | Velikost mezipaměti | IOPS | Omezení šířky pásma vstupně-výstupní mezipaměti |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Místní disk SSD 224 GB = |32 |576 GB |50 000 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ <br> 512 MB za sekundu |4 000 vstupně-výstupních operací a 33 MB za sekundu |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Místní disk SSD 896 GB = |64 |4224 GB |80 000 VSTUPNĚ-VÝSTUPNÍCH OPERACÍ <br> 2 000 MB za sekundu |5 000 IOPS a přes 50 MB za sekundu |

Chcete-li zobrazit úplný seznam všech dostupných velikostí virtuálních počítačů Azure, přečtěte si [velikosti virtuálních počítačů s Windows](../articles/virtual-machines/windows/sizes.md) nebo [velikosti virtuálního počítače s Linuxem](../articles/virtual-machines/linux/sizes.md). Zvolte velikost virtuálního počítače, která může splňovat a škálujte vaše požadavky na výkon požadovanou aplikaci. Kromě toho vezměte v úvahu následující důležité informace při volbě velikosti virtuálních počítačů.

*Limity škálování*  
Maximální limity IOPS na virtuální počítač a na disku jsou rozdílné a vzájemně nezávislé. Ujistěte se, že aplikace je v rámci virtuálního počítače, stejně jako na prémiových discích připojených k němu řízení vstupně-výstupních operací. Výkon aplikace, jinak dojde k omezení šířky pásma.

Jako příklad předpokládejme, že požadavek na aplikaci je maximálně 4 000 vstupně-výstupních operací. Za tím účelem můžete zřídit P30 disku na virtuálním počítači zdroj dat DS1. Až 5000 vstupně-výstupních operací může poskytovat disků P30. Virtuální počítač DS1 je však omezená na 3,200 vstupně-výstupních operací. V důsledku toho výkonu aplikací bude ovlivněna limitu virtuálních počítačů na 3,200 vstupně-výstupních operací a budou důvodem sníženého výkonu. K této situaci zabránit, zvolte velikost virtuálního počítače a disk, který bude i požadavkům aplikace.

*Náklady na operace*  
V mnoha případech je možné, že vaše celkové náklady na operace použití služby Premium Storage je nižší než při použití úložiště úrovně Standard.

Představte si třeba vyžadování 16 000 vstupně-výstupních operací aplikace. K dosažení tohoto výkonu, budete potřebovat standardní\_virtuálních počítačů Azure IaaS D14, který můžete poskytnout maximální IOPS 16 000 32 disky storage úrovně standard 1 TB. Každý disk 1TB úložiště úrovně standard může dosáhnout maximálně 500 IOPS. Odhadované náklady na tento virtuální počítač za měsíc bude 1,570 $. Měsíční náklady na 32 disky storage úrovně standard budou 1,638 $. Odhadované celkové měsíční náklady budou 3,208 $.

Ale pokud budete hostovaný stejné aplikace na Premium Storage, budete potřebovat menší velikost virtuálního počítače a méně disky storage úrovně premium, čímž se sníží celkové náklady. Standardní\_DS13 virtuální počítač dokáže naplnit požadavek na 16 000 vstupně-výstupních operací pomocí čtyř disků P30. Virtuální počítač DS13 má maximální IOPS 25,600 a každý P30 disk má maximální IOPS 5 000. Celkově tuto konfiguraci můžete dosáhnout 5 000 × 4 = 20 000 IOPS. Odhadované náklady na tento virtuální počítač za měsíc bude 1,003 $. Měsíční náklady na čtyři disky P30 premium storage budou 544.34 $. Odhadované celkové měsíční náklady budou 1,544 $.

Následující tabulka shrnuje rozpis nákladů tohoto scénáře pro Standard a Premium Storage.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Cena za virtuální počítač za měsíc** |$1,570.58 (standardní\_D14) |$1,003.66 (standardní\_DS13) |
| **Ceny disků za měsíc** |1,638.40 (32 x 1 TB disky) |544.34 (4 disky x P30) |
| **Celkové náklady za měsíc** |$3,208.98 |$1,544.34 |

*Distribuce Linuxu*  

Díky Azure Premium Storage budete mít stejnou úroveň výkonu pro virtuální počítače s Windows a Linux. Podporuje mnoho typů distribuce Linuxu, a zobrazí se úplný seznam [tady](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Je důležité si uvědomit, že různé distribuce jsou vhodnější pro různé druhy úloh. Zobrazí se různé úrovně výkonu v závislosti na distribuce, které je spuštěná vaše úloha na. Otestujte distribuce Linuxu s vaší aplikací a vyberte si ten nejvhodnější.

Pokud se službou Premium Storage s Linuxem, zkontrolujte nejnovější informace o požadované ovladače kvůli vysokému výkonu.

## <a name="premium-storage-disk-sizes"></a>Velikosti disků úložiště úrovně Premium

Azure Premium Storage nabízí osm GA velikosti disků a třech velikostech disků, které jsou aktuálně ve verzi preview. Velikost každého disku má limit různých škálování pro vstupně-výstupních operací, šířku pásma a úložiště. Volby nejvhodnější velikosti disků úložiště úrovně Premium v závislosti na požadavcích aplikace a vysokou škálovatelností velikost virtuálního počítače. Následující tabulka ukazuje velikosti jedenáct disků a jejich funkce. Velikosti P4 P6, P15, P60, P70 a P80 jsou aktuálně podporuje jenom pro službu Managed Disks.

| Typ disky Premium  | P4    | P6    | P10   | P15 | P20   | P30   | P40   | P50   | P60   | P70   | P80   |
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 32 GiB | 64 GiB | 128 GiB| 256 GiB| 512 GB            | 1 024 GiB (1 TiB)    | 2 048 GiB (2 TiB)    | 4 095 GiB (4 TiB)    | 8 192 GiB (8 TiB)    | 16,384 GiB (16 TiB)    | 32 767 giB (32 GB)    |
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12 500              | 15 000              | 20,000              |
| Propustnost / disk | 25 MiB za sekundu  | 50 MiB za sekundu  | 100 MiB za sekundu |125 MiB za sekundu | 150 MiB za sekundu | 200 MiB za sekundu | 250 MiB za sekundu | 250 MiB za sekundu | 480 MiB za sekundu | 750 MiB za sekundu | 750 MiB za sekundu |

Kolik disků, které zvolíte, závisí na disku velikost zvolená. Můžete použít jeden disk P50 nebo více disků P10 pro splnění požadavků vašich aplikací. Zohlednit při rozhodování níže uvedené důležité informace o účtu.

*Limity škálování (IOPS a propustnost)*  
Omezení IOPS a propustnost jednotlivé velikosti disků úrovně Premium je jiné a nezávisle z limity škálování virtuálních počítačů. Ujistěte se, že celkový počet IOPS a propustnost z disků jsou v rámci limity škálování zvolené velikosti virtuálních počítačů.

Například, pokud požadavek na aplikaci je maximálně 250 MB za sekundu, propustnosti a používáte DS4 virtuálního počítače s jedním diskem P30. DS4 virtuálního počítače můžete udělit až 256 MB za sekundu propustnosti. Jeden disk P30 však má limit propustnosti 200 MB/s. Aplikace v důsledku toho se omezovat na 200 MB za sekundu kvůli omezení disku. K překonání tohoto omezení, zřízení více datových disků k virtuálnímu počítači nebo změna velikosti disků P40 nebo P50.

> [!NOTE]
> Čtení obsluhuje mezipaměti nejsou součástí diskové vstupně-výstupních operací a propustnosti, proto nevztahuje se na ně omezení disku. Mezipaměť má samostatné limit IOPS a propustnost na virtuální počítač.
>
> Zpočátku operací čtení a zápisu jsou například 60MB za sekundu a 40MB za sekundu v uvedeném pořadí. V průběhu času mezipaměti warms a obsluhuje více a více čtení z mezipaměti. Potom můžete získat zápisu vyšší propustnost disku.

*Počet disků*  
Určete počet disků, které budete potřebovat tím, že posoudí požadavky aplikace. Všechny velikosti virtuálních počítačů má také omezený počet disků, které můžete připojit k virtuálnímu počítači. Obvykle je to dvojnásobný počet jader. Ujistěte se, že velikost virtuálního počítače, které zvolíte podporují počet disků.

Mějte na paměti, disků Premium Storage mají vyšší výkon funkce ve srovnání s disky Storage úrovně Standard. Proto pokud provádíte migraci aplikace z virtuálního počítače Azure IaaS pomocí úložiště úrovně Standard na Premium Storage, budete pravděpodobně potřebovat méně disky úrovně premium, abyste dosáhli stejné nebo vyšší výkon pro vaše aplikace.

## <a name="disk-caching"></a>Používání mezipaměti disku

Vysoké škálování virtuálních počítačů, které využívají Azure Premium Storage mají vícevrstvé technologií ukládání do mezipaměti volá BlobCache. BlobCache používá kombinaci RAM virtuálního počítače a místní disk SSD pro ukládání do mezipaměti. Tato mezipaměť je dostupná pro trvalé disky úrovně Premium Storage a místní disky virtuálních počítačů. Ve výchozím nastavení toto nastavení mezipaměti je nastavena pro čtení a zápis pro disky s operačním systémem a jen pro čtení pro datové disky hostované na Premium Storage. Pomocí disku na disky Storage úrovně Premium povolené ukládání do mezipaměti, virtuální počítače s vysokou škálovatelností může dosáhnout velmi vysokou úroveň výkonu, které přesahují základní výkon disku.

> [!WARNING]
> Ukládání do mezipaměti disku je podporována pouze pro disky o velikosti až 4 TB.
> Změna nastavení mezipaměti disku Azure odpojí a znovu připojí cílový disk. Pokud je disk s operačním systémem, je virtuální počítač restartoval. Zastavte všechny aplikace nebo služby, které by mohly mít dopad této přerušení před změnou nastavení mezipaměti disku.

Další informace o tom, jak BlobCache funguje, najdete v tématu uvnitř [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blogový příspěvek.

Je třeba povolit mezipaměť na správnou sadu disky. Určuje, zda by měl povolit ukládání do mezipaměti na disku na disk úrovně premium nebo nebude závisí na vzor zatížení tento disk bude zpracovávat. Následující tabulka ukazuje výchozí nastavení mezipaměti pro disky s operačním systémem a daty.

| **Typ disku** | **Výchozí nastavení mezipaměti** |
| --- | --- |
| Disk OS |ReadWrite |
| Datový disk |ReadOnly |

Toto jsou doporučené diskové nastavení mezipaměti pro datové disky

| **Nastavení mezipaměti na disku** | **Doporučení ohledně použití tohoto nastavení** |
| --- | --- |
| Žádný |Konfigurace mezipaměti hostitele jako žádné disky jen pro zápis a náročné na zápis. |
| ReadOnly |Konfigurace mezipaměti hostitele jako jen pro čtení pro disky jen pro čtení a pro čtení i zápis. |
| ReadWrite |Konfigurace mezipaměti hostitele jako ReadWrite pouze v případě, že vaše aplikace správně zpracovává zápis dat uložených v mezipaměti do trvalé disky v případě potřeby. |

*ReadOnly*  
Konfigurace ukládání do mezipaměti na Premium Storage data disky určené jen pro čtení, můžete dosáhnout nízké latence čtení a získat velmi vysokou propustnost a vstupně-výstupních operací čtení pro vaši aplikaci. Toto je z důvodu dva důvody

1. Čtení provést z mezipaměti, který se nachází na paměť virtuálního počítače a místní disk SSD, je mnohem rychlejší než čtení z datového disku, který je v úložišti objektů blob v Azure.  
1. Storage úrovně Premium, nepočítají čtení obsluhovat z mezipaměti na disku vstupně-výstupních operací a propustnosti. Vaše aplikace je proto možné zajistit vyšší celkový počet IOPS a propustnost.

*ReadWrite*  
Disky s operačním systémem mají ve výchozím nastavení povoleno ukládání do mezipaměti ReadWrite. Nedávno jsme přidali podporu pro ukládání do mezipaměti na datové disky a ReadWrite. Pokud používáte ReadWrite ukládání do mezipaměti, musí mít správný způsob, jak zapisovat data z mezipaměti na trvalé disky. Například SQL Server provede zápis dat uložených v mezipaměti pro disky s trvalým úložištěm sama o sobě. Použití mezipaměti ReadWrite do aplikace, která nezpracovává uchování požadovaná data může způsobit ztrátu dat, pokud dojde k chybě virtuální počítač.

Jako příklad, můžete použít tyto pokyny pro SQL Server běžící na Premium Storage pomocí tohoto postupu,

1. Konfigurace mezipaměti "Jen pro čtení" na disky premium storage, který je hostitelem datové soubory.  
   a.  Rychlý čte z mezipaměti nižší, že čas dotazu systému SQL Server, od datových stránek z mezipaměti načtou mnohem rychleji v porovnání s přímo z dat disky.  
   b.  Obsluha čtení z mezipaměti, znamená, že je k dispozici z datových disků premium větší propustnost. Pomocí této větší propustnost směrem k načtení další stránky dat a další operace, jako je zálohování a obnovení, batch zatížení systému SQL Server a znovu sestaví index.  
1. Konfigurace "None" do mezipaměti na disky premium storage, který je hostitelem souborů protokolu.  
   a.  Soubory protokolů mají primárně operace náročné na zápis. Proto že nejsou nijak přínosné mezipaměti jen pro čtení.

### <a name="optimize-performance-on-linux-vms"></a>Optimalizace výkonu na virtuální počítače s Linuxem

Pro všechny disky premium SSD nebo ultra disky s mezipamětí nastavena na **jen pro čtení** nebo **žádný**, při připojení systému souborů je nutné zakázat "překážky". Překážky v tomto scénáři není nutné, protože trvalého pro tato nastavení mezipaměti zápisy na disky premium storage. Až se žádost o zápis úspěšně dokončí, data se zapsala do trvalého úložiště. Zakázat "překážky", použijte jednu z následujících metod. Vyberte si metodu pro systém souborů:
  
* Pro **reiserFS**, chcete-li zakázat bariéry, použijte `barrier=none` možnost připojit. (Chcete-li povolit bariéry, použijte `barrier=flush`.)
* Pro **ext3/ext4**, chcete-li zakázat bariéry, použijte `barrier=0` možnost připojit. (Chcete-li povolit bariéry, použijte `barrier=1`.)
* Pro **XFS**, chcete-li zakázat bariéry, použijte `nobarrier` možnost připojit. (Chcete-li povolit bariéry, použijte `barrier`.)
* Storage úrovně premium disky s mezipamětí nastavena na hodnotu **ReadWrite**, povolte bariéry zápisu kvůli vyšší odolnosti.
* Pro jmenovky svazku se zachovat po restartování virtuálního počítače je nutné aktualizovat /etc/fstab s univerzálně jedinečným identifikátorem (UUID) odkazy na disky. Další informace najdete v tématu [přidání spravovaného disku do virtuálního počítače s Linuxem](../articles/virtual-machines/linux/add-disk.md).

Následující Linuxových distribucích ověřily pro premium SSD. Pro lepší výkon a stabilita s SSD disky úrovně premium doporučujeme upgradovat vaše virtuální počítače na jednu z těchto verzí nebo novější. 

Některé verze nejnovější Linux Integration Services (LIS), v4.0, vyžadují pro Azure. Stáhnout a nainstalovat distribuční, odkazu uvedené v následující tabulce. Můžeme přidat Image do seznamu jsme dokončení ověření. Naše ověření ukazují, že se mění výkon zvýšením každé bitové kopie. Výkon závisí na charakteristikách úloh a nastavení bitové kopie. Jiné Image je vyladěná pro různé druhy úloh.

| Distribuce | Verze | Podporované jádra | Podrobnosti |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x, 8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 vyžaduje](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Další informace v poznámce v další části* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [Doporučené LIS4](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Další informace v poznámce v další části* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 nebo RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 nebo RHCK plánovaným bodem obnovení kratším[LIS verze 4.1 a vyšší](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 nebo RHCK plánovaným bodem obnovení kratším[LIS verze 4.1 a vyšší](https://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |

## <a name="lis-drivers-for-openlogic-centos"></a>Ovladače služby LIS OpenLogic CentOS

Pokud používáte OpenLogic CentOS virtuální počítače, spusťte následující příkaz k instalaci nejnovější ovladače:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Pokud chcete aktivovat nové ovladače, restartujte virtuální počítač.

## <a name="disk-striping"></a>Prokládání disků

Při velkém měřítku, které virtuální počítač je připojený s několik trvalých disků premium storage, disky můžete společně prokládané agregovat jejich vstupně-výstupních operací, šířky pásma a kapacity úložiště.

Na Windows můžete prostory úložiště stripe disků najednou. Musíte nakonfigurovat jeden sloupec pro každý disk ve fondu. V opačném případě celkový výkon prokládaný svazek může být nižší, než se očekávalo, z důvodu nerovnoměrné distribuci provozu discích.

Důležité: Pomocí uživatelského rozhraní správce serveru, můžete nastavit celkový počet sloupců až 8 pro prokládané svazky. Při připojování víc než 8 disky, použití Powershellu k vytvoření svazku. Pomocí prostředí PowerShell, můžete nastavit počet sloupců rovná počtu disků. Například, pokud existují 16 disků v jedné prokládané sady; Zadejte 16 sloupců v *NumberOfColumns* parametr *New-VirtualDisk* rutiny Powershellu.

V Linuxu použijte nástroj MDADM stripe disků najednou. Podrobný postup na prokládání disků v Linuxu najdete [konfigurace softwaru diskového pole RAID v Linuxu](../articles/virtual-machines/linux/configure-raid.md).

*Velikost platformy stripe*  
Důležité konfigurační v prokládání disků je velikost stripe. Stripe velikost nebo velikost bloku je nejmenší bloků dat, která aplikace může vyřešit prokládané svazku. Velikost prokládání, které nakonfigurujete závisí na typu aplikace a způsobu požadavku. Pokud se rozhodnete velikost nesprávné prokládání, může vést k chybné vstupně-výstupních operací, což vede ke snížení výkonu vaší aplikace.

Například pokud požadavek v/v úrovně vygenerované aplikaci je větší než velikost disku stripe, úložný systém zapíše ji napříč prokládanými hranicemi jednotky na více než jeden disk. Až nastane čas přistupovat k těmto datům, bude mít hledání napříč více než jedné jednotky stripe dokončit žádost. Kumulativní efekt takové chování může způsobit snížení výkonu. Na druhé straně Pokud žádost o velikost vstupně-výstupních operací je menší než velikost prokládání a je náhodné, požadavků vstupně-výstupních operací může přidat na stejném disku, způsobuje kritický bod a nakonec došlo ke snížení výkonu vstupně-výstupních operací.

V závislosti na typu úlohy, která vaše aplikace běží, vyberte velikosti odpovídající stripe. Pro náhodné malé vstupně-výstupních operací požadavky použijte menší velikost stripe. Zatímco požadavky velkých sekvenčních vstupně-výstupních operací použijte větší velikost stripe. Přečtěte si doporučené velikosti stripe pro aplikaci, že poběží na Premium Storage. Pro SQL Server nakonfigurujte stripe velikosti 64KB pro úlohy OLTP a 256KB u úloh datových skladů. Zobrazit [osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) Další informace.

> [!NOTE]
> Můžete společně prokládanou maximálně 32 disky storage úrovně premium na DS-series virtuálních počítačů a 64 disky storage úrovně premium na GS-series virtuálních počítačů.

## <a name="multi-threading"></a>Více vláken

Azure je navržená tak, platforma Premium Storage bude masivně paralelní. Proto vícevláknové aplikace dosáhne mnohem vyšší výkon než jednovláknová aplikace. Vícevláknové aplikace rozděluje její úkoly napříč několika vlákny a zvyšuje efektivitu spuštění s využitím virtuálních počítačů a diskové prostředky na maximum.

Například pokud vaše aplikace běží na jednojádrový virtuálního počítače pomocí dvěma vlákny, CPU můžete přepínat mezi těmito dvěma vlákny k dosažení efektivity. Zatímco jedno vlákno čeká na disk vstupně-výstupních operací na dokončení, CPU můžete přepnout na vlákno. Tímto způsobem můžete provádět dvěma vlákny více než jedním vláknem a sdíleli. Pokud virtuální počítač má více než jedno jádro, další sníží doba spuštění od každé jádro můžete spustit paralelně úloh.

Není možné změnit způsob, jak předem připravená aplikace implementuje jednotné dělení na vlákna nebo více vláken. Například je schopna zpracovávat více procesorů a více jader serveru SQL Server. SQL Server však rozhodne, za jakých podmínek se bude využívat jeden nebo více vláken zpracování dotazu. To můžete spouštět dotazy a vytváření indexů pomocí více vláken. Dotaz, který zahrnuje spojování velkých tabulek a řazení dat před vrácením uživateli SQL Server pravděpodobně používat více vláken. Uživatel však nelze určit, jestli SQL Server spouští dotaz pomocí jednoho vlákna nebo více vláken.

Existují nastavení konfigurace, které je možné změnit to ovlivnit více vláken nebo paralelní zpracování žádosti. V případě systému SQL Server je třeba maximální míru paralelismu konfigurace. Toto nastavení nazývá MAXDOP, umožňuje nakonfigurovat maximální počet procesorů, které systém SQL Server můžete použít při paralelní zpracování. Můžete nakonfigurovat MAXDOP pro jednotlivé dotazy nebo operace indexu. To je užitečné, když chcete vyrovnávat zatížení prostředků systému pro kritické aplikace výkonu.

Řekněme například, že aplikace SQL Server pomocí provádí velké dotazy a operace indexu ve stejnou dobu. Předpokládejme, že jste si přáli operace indexu bude výkonnější ve srovnání s velké dotazu. V takovém případě můžete nastavit hodnotu MAXDOP operace indexu vyšší než hodnota MAXDOP pro dotaz. Tímto způsobem, SQL Server má větší počet procesorů, které můžete využít pro operaci indexu v porovnání s počtem procesorů, které můžete vyhradit pro velké dotazu. Mějte na paměti, není řídí počet vláken, která bude používat systém SQL Server pro každou operaci. Můžete určit maximální počet procesorů, které jsou vyhrazené pro více vláken.

Další informace o [stupňů paralelismu](https://technet.microsoft.com/library/ms188611.aspx) v systému SQL Server. Přečtěte si tato nastavení, které ovlivňují více vláken v aplikaci a jejich konfigurace za účelem optimalizace výkonu.

## <a name="queue-depth"></a>Hloubka fronty

Hloubka fronty nebo délka fronty nebo velikost fronty je počet čekajících požadavků na vstupně-výstupní operace v systému. Hodnota hloubky fronty určuje počet operací vstupně-výstupních operací vaší aplikace můžete zarovnejte, který bude zpracovávat disky úložiště. To má vliv na všechny ukazatele výkonu tři aplikace, které jsme probírali v tomto článku tvořeno vstupně-výstupních operací, propustnosti a latence.

Fronty hloubky a ukryta jsou úzce související. Hloubka fronty hodnota udává, kolik více vláken může aplikace dosáhnout. Při velké hloubce fronty aplikace mohou být další operace prováděna současně, jinými slovy, více vláken. Je-li hloubka fronty je malý, i když je aplikace Vícevláknová, nebude mít dostatek požadavky zarovnány pro souběžné spouštění.

Obvykle vypnout police aplikace neumožňují budete muset změnit hloubka fronty, protože pokud nastavené nesprávně provede další poškození než v pořádku. Aplikace nastaví správná hodnota hloubky fronty, abyste získali optimální výkon. Je důležité pochopit tento koncept, takže můžete řešit problémy s výkonem s vaší aplikací. Můžete také sledovat účinky hloubka fronty spuštěním nástrojích pro srovnávací testy ve vašem systému.

Některé aplikace zadejte nastavení k ovlivnění hloubka fronty. Nastavení MAXDOP (maximální volnost paralelismu) v systému SQL Server je popsáno v předchozí části. MAXDOP je způsob, jak ovlivnit hloubka fronty a více vláken, i když přímo nezmění hodnotu hloubku fronty na serveru SQL Server.

*Hloubka fronty vysoká*  
Hloubka fronty vysokou zarovnán další operace na disku. Disk ví další požadavek do fronty předem. Disk v důsledku toho můžete naplánovat operace předem domluvili a jejich zpracování optimální postupně. Protože aplikace odesílá více požadavků na disk, disk může zpracovat více paralelních IOs. Nakonec aplikace bude moct dosáhnout vyšší vstupně-výstupních operací. Protože aplikace zpracovává více požadavků, také zvyšuje celkovou propustnost aplikace.

Obvykle, může aplikace dosáhnout maximální propustnost s 8 – 16 + nezpracovaných vstupně na připojený disk. Pokud hloubka fronty je jedna věc, aplikace není dostatek IOs nahráním do systému a zpracuje menší množství v daném časovém období. Jinými slovy méně propustnost.

Například v systému SQL Server, nastavení MAXDOP hodnotu pro dotaz na "4" informuje o SQL serveru, až čtyři jádra může použít k provedení dotazu. SQL Server určí, co je nejlepší poměr hloubky fronty a počet jader pro provedení dotazu.

*Optimální hloubky fronty*  
Hodnota hloubky fronty velmi vysoké má také jejích nevýhod. Pokud hodnota hloubky fronty je příliš vysoká, aplikace se pokusí jednotka velmi vysoké vstupně-výstupních operací. Pokud aplikace nemá trvalé disky s dostatečnou zřízené IOPS, to mít nepříznivý vliv latence aplikace. Za vzorec znázorňuje vztah mezi vstupně-výstupních operací, latence a hloubka fronty.  
    ![](media/premium-storage-performance/image6.png)

Hloubka fronty byste neměli konfigurovat na vysokou hodnotu, ale na optimální hodnotu, která můžete dodávat dost vstupně-výstupních operací aplikace bez vlivu latence. Například pokud latence aplikace musí být 1 milisekunda, hloubka fronty potřebná k dosažení 5000 vstupně-výstupních operací je, hloubka fronty = 5000 x 0,001 = 5.

*Hloubka fronty pro prokládané svazky*  
Prokládané svazku udržujte hloubka dostatečně vysoká fronty tak, aby každý disk má hloubka fronty ve špičce jednotlivě. Představte si třeba aplikaci, která odesílá hloubka fronty 2 a obsahují 4 disky zapisují prokládaně. Dva požadavky vstupně-výstupních operací budou moct dva disky a bude zbývající dva disky nečinné. Proto konfigurace hloubka fronty tak, aby všechny disky můžou být zaneprázdněn. Vzorec níže ukazuje, jak určit hloubku fronty prokládané svazky.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Throttling

Azure Premium Storage ustanovení zadaný počet IOPS a propustnost v závislosti na velikosti virtuálních počítačů a velikosti disků, které zvolíte. Kdykoli se aplikace pokusí Centrum umožňující prosazovat vstupně-výstupních operací nebo propustnost vyšší než tato omezení, co virtuální počítač nebo disku můžete pracovat, se omezení služby Premium Storage je. To manifesty ve formě snížení výkonu v aplikaci. To může znamenat vyšší latencí, snížení propustnosti nebo nižší vstupně-výstupních operací. Pokud není omezení služby Premium Storage, vaše aplikace může selhat zcela podle překročení, jaké jsou její prostředky schopné dosáhnout. Tak aby se zabránilo problémům s výkonem z důvodu omezení, vždy zřídit dostatek prostředků pro vaši aplikaci. Vzít v úvahu, co jsme probírali v sekcích velikosti disku výše a velikosti virtuálních počítačů. Srovnávací testy je nejlepší způsob, jak zjistit, jaké prostředky, které potřebujete k hostování vaší aplikace.

## <a name="next-steps"></a>Další postup

Další informace o typech disků k dispozici:

* [Vyberte typ disku](../articles/virtual-machines/windows/disks-types.md)  

Pro uživatele systému SQL Server, přečtěte si články o osvědčené postupy z hlediska výkonu pro SQL Server:

* [Osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage poskytuje nejvyšší výkonu pro SQL Server na virtuálním počítači Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
