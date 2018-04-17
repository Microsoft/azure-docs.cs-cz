# <a name="azure-premium-storage-design-for-high-performance"></a>Úložiště Azure Premium: Návrh vysoce výkonné
## <a name="overview"></a>Přehled
Tento článek obsahuje pokyny pro vytváření vysoce výkonné aplikace pomocí Azure Premium Storage. Můžete použít pokyny v tomto dokumentu v kombinaci s osvědčené postupy z hlediska výkonu pro technologie, které používá vaše aplikace. Pro ilustraci podle pokynů, jsme použili SQL Server běžící na Storage úrovně Premium jako příklad v tomto dokumentu.

Když jsme adres scénáře výkonu pro vrstvy úložiště v tomto článku, musíte se k optimalizaci aplikační vrstvu. Například pokud hostujete farmy služby SharePoint na Azure Premium Storage, můžete použít příklady systému SQL Server z tohoto článku za účelem optimalizace databázového serveru. Kromě toho Optimalizujte webového serveru a získat většina výkonu se aplikační server farmy služby SharePoint.

Tento článek vám pomůže odpovědí následující běžné otázky o optimalizaci výkonu aplikace na Azure Premium Storage

* Postupy: měření výkonu vaší aplikace?  
* Proč se zobrazuje očekávané vysoký výkon?  
* Faktory, které mají vliv na výkon vaší aplikace na Storage úrovně Premium?  
* Jak se tyto faktory ovlivňují výkon aplikace na Storage úrovně Premium  
* Jak můžete můžete optimalizovat pro IOPS, šířky pásma a latencí?  

Uvádíme těchto pokynů speciálně pro Storage úrovně Premium, protože úlohy běžící na Storage úrovně Premium jsou vysoce citlivých výkonu. Kde je to vhodné uvádíme příklady. Některé z těchto pokynů můžete použít k aplikacím spuštěným na virtuální počítače IaaS s disky úložiště Standard Storage.

Než začnete, pokud začínáte používat úložiště úrovně Premium, nejdřív přečíst [úložiště Premium: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../articles/virtual-machines/windows/premium-storage.md) a [a cíle výkonnosti služby Azure Storage Scalability](../articles/storage/common/storage-scalability-targets.md) články.

## <a name="application-performance-indicators"></a>Ukazatele výkonu aplikace
Jsme vyhodnocení, zda aplikace pracuje správně nebo není pomocí, jako jsou ukazatele výkonu, jak rychle aplikace je zpracování požadavku uživatele, kolik dat aplikace zpracovává každý požadavek, kolik požadavků je aplikaci zpracování v určitou dobu dobu, jak dlouho má uživatel k čekat na odpověď po odeslání žádosti. Technické podmínky pro tyto ukazatele výkonu jsou IOP, propustnosti nebo šířky pásma a latence.

V této části se budeme zabývat běžné ukazatele výkonu v rámci služby Storage úrovně Premium. V následující části, shromažďování požadavky na aplikace, se dozvíte, jak k měření tyto ukazatele výkonu pro vaši aplikaci. Později v optimalizace výkonu aplikací se dozvíte o faktorů, které ovlivňují tyto ukazatele výkonu a doporučení k optimalizaci je.

## <a name="iops"></a>IOPS
IOPS je počet požadavků, které vaše aplikace odesílá do disky úložiště za jednu sekundu. Vstupně výstupní operace by mohl číst nebo zapisovat sekvenční nebo náhodné. OLTP aplikace jako webem online prodejní nutné okamžitě zpracovávat požadavky na velký počet souběžných uživatelů. Žádosti uživatelů jsou vložení a aktualizace náročné databázové transakce, které aplikace musí rychle zpracovávají. Proto OLTP aplikace vyžadují velmi vysoký IOPS. Takové aplikace zpracovává miliony požadavků malé a náhodných vstupně-výstupní operace. Pokud máte takové aplikace, je třeba navrhnout infrastruktury aplikace za účelem optimalizace iops. V části novější *optimalizace výkonu aplikace*, probereme podrobně všech faktorů, které musíte zvážit, chcete-li získat vysokou IOPS.

Když připojíte disk úložiště premium vaší vysoké škálování virtuálních počítačů, Azure poskytuje pro vás zaručenou počet IOPS podle specifikace disku. Například P50 disk zřídí 7500 IOPS. Každý měřítko velikost virtuálního počítače má také konkrétní limit IOPS, která dokáže odolat. Například standardní virtuální počítač GS5 má 80 000 IOPS omezit.

## <a name="throughput"></a>Propustnost
Propustnost nebo šířky pásma je množství dat, který aplikace odesílá na discích úložiště v určeném intervalu. Pokud vaše aplikace provádí vstupně-výstupních operací s velikostí jednotky velké vstupně-výstupní operace, vyžaduje Vysoká propustnost. Datového skladu aplikace mívají k vydávání kontroly náročné operace, které přístup velká část dat najednou a běžně provádět hromadné operace. Jinými slovy tyto aplikace vyžadují vyšší propustnost. Pokud máte takové aplikace, je nutné vytvořit svoji infrastrukturu optimalizovat pro propustnost. V další části probereme podrobně faktory musí ladit dosáhnout.

Když připojíte disk úložiště premium vysoké škálování virtuálních počítačů, Azure zřizuje propustnost podle specifikace tohoto disku. Například P50 disk zřídí 250 MB na druhý disk propustnost. Každý měřítko velikost virtuálního počítače má také jako konkrétní omezení propustnosti, které dokáže odolat. Například standardní GS5 virtuální počítač má maximální propustnost 2 000 MB za sekundu. 

Je vztah mezi propustnost a IOPS, jak je znázorněno v následující vzorec.

![](media/premium-storage-performance/image1.png)

Proto je důležité určit optimální propustnosti a IOPS hodnoty, které vaše aplikace vyžaduje. Jak se pokusíte optimalizovat jeden, druhý také získá vliv. V další části *optimalizace výkonu aplikace*, se budeme zabývat v podrobnější informace o optimalizaci IOPS a propustnosti.

## <a name="latency"></a>Latence
Latence je doba potřebná aplikace pro příjem jedné žádosti, odešle disky úložiště a odesílání odpověď klientovi. Toto je důležité míra výkonu aplikace kromě IOPS a propustnosti. Latence disk úložiště premium je čas potřebný k získat informace o požadavku a komunikaci zpět do vaší aplikace. Storage úrovně Premium poskytuje trvale nízké latence. Pokud povolíte ukládání do mezipaměti na discích úložiště premium hostitele jen pro čtení, můžete získat mnohem nižší latenci pro čtení. Se budeme zabývat ukládání do mezipaměti disku podrobněji v pozdější části na *optimalizace výkonu aplikace*.

Optimalizovat aplikace k získání vyšší propustnost a IOPS, bude mít vliv latence vaší aplikace. Po vyladění výkonu aplikací, vždy vyhodnoceny latence aplikace, aby se zabránilo chování neočekávané vysokou latencí.

## <a name="gather-application-performance-requirements"></a>Shromáždění požadavků na výkon aplikace
Prvním krokem při navrhování vysoký výkon aplikací běžících na Azure Premium Storage je pochopit požadavky na výkon vaší aplikace. Po shromáždění požadavků na výkon, můžete optimalizovat aplikace zajistit optimální výkon.

V předchozí části jsme vysvětlení najdete běžné ukazatele výkonu, IOP, propustnosti a latence. Musíte určit, které tyto ukazatele výkonu jsou důležité k vaší aplikaci k poskytování požadované uživatelské prostředí. Například vysoké IOPS důležitý většina aplikací OLTP zpracování miliony transakcí za sekundu. Vzhledem k tomu, Vysoká propustnost je velmi důležitá pro datový sklad aplikací zpracování velkých objemů dat za sekundu. Velmi nízkou latenci je zásadní pro aplikace v reálném čase jako živé video streamování weby.

V dalším kroku měření požadavky na maximální výkon vaší aplikace v průběhu své životnosti. Kontrolní seznam ukázka níže použijte jako spuštění. Zaznamenejte požadavkům na maximální výkon při normální, tečky zatížení ve špičce a počítačem nepracujete. Tím, že určíte požadavky pro všechny úrovně zatížení, bude možné určit, požadavek na celkový výkon vaší aplikace. Běžné pracovní náplně webem elektronického obchodování například bude transakcí, které slouží během většina dnů v roce. Zatížení ve špičce webu bude transakcí, které slouží během sváteční sezóny nebo speciální prodej události. Zatížení ve špičce je obvykle zkušeného po omezenou dobu, ale může vyžadovat aplikace škálovat dvěma či více krát jeho normální provoz. Zjistíte 50. percentil, 90 percentilu a 99 percentilu požadavky. To pomáhá vyfiltrovat odlehlé všechny hodnoty v požadavcích na výkon a vaše úsilí můžete soustředit na optimalizace pro správné hodnoty.

**Kontrolní seznam požadavků výkonu aplikace**

| **Požadavky na výkon** | **50. percentil** | **90 percentilu** | **99 percentilu** |
| --- | --- | --- | --- |
| Max. Transakce za sekundu | | | |
| Operace čtení % | | | |
| Operace zápisu % | | | |
| % Náhodné operace | | | |
| Sekvenční operace % | | | |
| Velikost požadavku vstupně-výstupní operace | | | |
| Průměrná propustnost | | | |
| Max. Propustnost | | | |
| Min. Latence | | | |
| Průměrná latence | | | |
| Max. Procesor | | | |
| Průměr CPU | | | |
| Max. Memory (Paměť) | | | |
| Průměrná paměti | | | |
| Hloubka fronty | | | |

> [!NOTE]
> Měli byste zvážit škálování tato čísla podle očekávané budoucímu růstu vaší aplikace. Je vhodné naplánovat růstu předem, protože by mohlo být těžší infrastrukturu pro zlepšení výkonu později změnit.
>
>

Pokud máte existující aplikaci a chcete přejít na Storage úrovně Premium, nejprve vytvoříte kontrolní seznam výše pro existující aplikace. Potom sestavení prototyp vaší aplikace na Storage úrovně Premium a navrhněte aplikaci podle pokynů popsaných v *optimalizace výkonu aplikace* v další části tohoto dokumentu. Další část popisuje nástroje, které můžete použít ke shromažďování měření výkonu.

Vytvořte kontrolní seznam podobná stávající aplikaci pro prototypu. Pomocí nástrojů Benchmarking můžete simulovat úlohy a měření výkonu na prototypu aplikaci. Projděte část o [Benchmarking](#benchmarking) Další informace. Pomocí tohoto postupu, abyste mohli ověřit, zda úložiště Premium můžete odpovídat nebo překročí maximální vašim požadavkům na výkon aplikace. Poté můžete implementovat stejné pokyny pro produkční aplikace.

### <a name="counters-to-measure-application-performance-requirements"></a>Čítače k měření výkonu požadavky na aplikace
Nejlepší způsob, jak měřit požadavky na výkon vaší aplikace, je použití nástroje Sledování výkonu poskytované operačního systému serveru. Můžete použít nástroj PerfMon pro systém Windows a iostat pro Linux. Tyto nástroje zachytit čítače odpovídající každá míra popsané v části výše. Hodnoty těchto čítačů nutné zaznamenat, když aplikace běží jeho normální, zatížení ve špičce a počítačem nepracujete.

Tyto čítače PerfMon jsou k dispozici pro procesor, paměť a každý logický disk a fyzický disk serveru. Pokud používáte prémiové disky úložiště v případě virtuálních počítačů, čítače fyzického disku se pro každý disk úložiště premium a čítače logický disk se pro každý svazek na discích úložiště premium vytvoří. Nutné zaznamenat určité hodnoty pro disky, které hostují vaše úlohy aplikace. Pokud dojde k jedné mapování mezi logické a fyzické disky, mohou odkazovat na fyzický disk čítače; v opačném případě naleznete čítače logického disku. V systému Linux příkaz iostat generuje sestavy využití procesoru a disku. Sestava využití disku poskytuje statistické údaje za fyzického zařízení nebo oddíl. Pokud máte databázový server s protokolu a data na různých discích, shromážděte tato data pro oba disky. Následující tabulka popisuje čítače pro disky, procesoru a paměti:

| Čítač | Popis | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS nebo transakcí za sekundu** |Počet vstupně-výstupní požadavky vydané úložiště disku za sekundu. |Čtení disku/s <br> Zápis disku/s |TPS <br> r/s <br> w/s |
| **Disk čtení a zápisu** |% čtení a zápisu operace provedené na disku. |Čas čtení disku v % <br> Čas zápisu disku v % |r/s <br> w/s |
| **Propustnost** |Množství dat číst nebo zapisovat na disk za sekundu. |Bajty čtení z disku/s <br> Bajty zapisování na disk/s |kB_read/s <br> kB_wrtn/s |
| **Latence** |Celková doba nutná k dokončení žádosti o vstupně-výstupní operace disku. |Průměrná doba disku/čtení <br> Doba průměrná disku/zápis |await <br> svctm |
| **Velikost vstupně-výstupní operace** |Velikost vstupně-výstupních požadavků problémy na discích úložiště. |Průměrná disku bajtů/čtení <br> Průměrná disku bajtů/zápis |avgrq sz |
| **Hloubka fronty** |Počet nezpracovaných vstupně-výstupních požadavků čekání na čtení formuláře nebo zapsaných na disk úložiště. |Aktuální délka fronty disku |avgqu sz |
| **Max. Paměť** |Množství paměti nutné ke spuštění aplikace bez problémů |% Využití potvrzených bajtů |Použití vmstat |
| **Max. VYUŽITÍ PROCESORU** |Velikost procesoru potřebné ke spuštění aplikace bez problémů |% Času procesoru |% util |

Další informace o [iostat](https://linux.die.net/man/1/iostat) a [PerfMon](https://msdn.microsoft.com/library/aa645516.aspx).

## <a name="optimizing-application-performance"></a>Optimalizace výkonu aplikace
Hlavní faktory, které mají vliv na výkon aplikace běžící na Storage úrovně Premium jsou povaze z vstupně-výstupní požadavky, velikost virtuálního počítače, velikost disku, počet disků, ukládání do mezipaměti na disku, Multithreading a hloubku fronty. Můžete ovládat některé tyto faktory s knoflíky poskytované systémem. Většina aplikací nemusí poskytnout možnost ke změně velikosti vstupně-výstupní operace a hloubku fronty přímo. Například pokud používáte systém SQL Server, nemůžete hloubka velikost a fronty vstupně-výstupní operace. SQL Server vybere optimální vstupně-výstupní operace velikost fronty hloubka hodnoty a získat většina výkonu. Je důležité pochopit účinky oba typy faktory na výkon aplikace tak, aby můžete zřídit příslušné prostředky ke splnění požadavkům na výkon.

V této části naleznete kontrolní seznam požadavků na aplikaci, kterou jste vytvořili, k identifikaci, kolik potřebujete optimalizovat výkon aplikace. Podle toho, který, bude možné určit faktory, které z této části budete muset vyladit. Chcete-li určující účinky každý faktor na výkon aplikace, spusťte testu typovou úlohou nástroje na instalace aplikace. Odkazovat [Benchmarking](#Benchmarking) na konci tohoto článku kroky, jak spustit běžné nástroje pro testu typovou úlohou ve Windows a virtuální počítače s Linuxem.

### <a name="optimizing-iops-throughput-and-latency-at-a-glance"></a>Optimalizace IOP, propustnosti a latence na první pohled
Následující tabulka shrnuje všechny faktory výkonu a kroky k optimalizaci IOP, propustnosti a latence. V oddílech Toto shrnutí bude popisují každou. faktor je mnohem víc hloubka.

| &nbsp; | **IOPS** | **Propustnost** | **Latence** |
| --- | --- | --- | --- |
| **Příklad scénáře** |Enterprise OLTP aplikace, které vyžadují velmi vysoký transakce za druhá míra. |Enterprise datového skladu aplikace zpracování velkých objemů dat. |Téměř v reálném čase aplikace vyžadující rychlých odpovědí na požadavky na uživatele jako hraní online her. |
| Faktory výkonu | &nbsp; | &nbsp; | &nbsp; |
| **Velikost vstupně-výstupní operace** |Menší velikost vstupně-výstupní operace dosáhnout vyšší IOPS. |Větší velikost vstupně-výstupní operace dosáhnout vyšší propustnost. | &nbsp;|
| **Velikost virtuálního počítače** |Použijte velikost virtuálního počítače, který nabízí IOPS větší než požadavků vaší aplikace. Zobrazit velikosti virtuálních počítačů a jejich IOPS omezení. |Velikost virtuálního počítače pomocí omezení propustnosti větší než požadavků vaší aplikace. Zobrazit velikosti virtuálních počítačů a jejich propustnost omezení. |Použijte velikost virtuálního počítače, že nabízí škálování omezení větší než požadavků vaší aplikace. Zobrazit velikosti virtuálních počítačů a jejich omezení sem. |
| **Velikost disku** |Použijte velikost disku, který nabízí IOPS větší než požadavků vaší aplikace. Zobrazit velikosti disků a jejich IOPS omezení. |Velikost disku pomocí omezení propustnosti větší než požadavků vaší aplikace. Zobrazit velikosti disků a jejich propustnost omezení. |Použijte velikost disku, že nabízí škálování omezení větší než požadavků vaší aplikace. Zobrazit velikosti disků a jejich omezení sem. |
| **Virtuální počítač a limity škálování disku** |Limit IOPS vybraná velikost virtuálního počítače musí být větší než celkový počet IOPS doprovází připojené disky úložiště premium. |Propustnost limit vybraná velikost virtuálního počítače musí být větší než celková propustnost doprovází připojené disky úložiště premium. |Limity škálování vybraná velikost virtuálního počítače musí být větší limity škálování celkový disky úložiště připojené premium. |
| **Ukládání do mezipaměti na disku** |Povolte mezipaměť jen pro čtení na discích úložiště premium s velkou operace čtení získat vyšší IOPS pro čtení. | &nbsp; |Povolte mezipaměť jen pro čtení na discích úložiště premium s připravené velkou operacemi získat čtení velmi nízkou latenci. |
| **Prokládání disků** |Použití více disků a rozkládají je společně se získat kombinovaný vyšší limit IOPS a propustnosti. Všimněte si, že kombinovaný limit na jednu virtuální počítač musí být vyšší než kombinované omezení připojené prémiové disky. | &nbsp; | &nbsp; |
| **Velikost stripe** |Menší velikost stripe pro náhodné malé vstupně-výstupní operace vzor vidět v aplikacích OLTP. Například pro aplikaci SQL Server OLTP použijte stripe velikost 64KB. |Větší velikost stripe pro sekvenční velké vstupně-výstupní operace vzor vidět v aplikacích datového skladu. Například použijte velikost 256KB prokládání pro aplikaci SQL Server datového skladu. | &nbsp; |
| **Více vláken** |Použití více vláken tak, aby nabízel vyšší počet požadavků, které do úložiště úrovně Premium, který povede k vyšší IOPS a propustnosti. Například na serveru SQL Server nastavit na vysokou hodnotu MAXDOP přidělit více procesorů k systému SQL Server. | &nbsp; | &nbsp; |
| **Hloubka fronty** |Větší hloubky fronty dosáhnout vyšší IOPS. |Větší hloubky fronty dosáhnout vyšší propustnost. |Menší hloubka fronty vypočítá nižší latenci. |

## <a name="nature-of-io-requests"></a>Povaha vstupně-výstupní požadavky
Žádost o vstupně-výstupní operace je jednotka vstupně výstupní operace, které vaše aplikace bude provádět. Identifikace povaha vstupně-výstupní požadavky, náhodných nebo sekvenčních, číst nebo zapisovat, malý nebo velký, bude vám pomohou určit požadavky na výkon vaší aplikace. Je velmi důležité k pochopení povahy požadavků vstupně-výstupní operace, udělat správné rozhodnutí při navrhování infrastruktury aplikace.

Velikost vstupně-výstupní operace je jedním z důležitých faktorů. Velikost vstupně-výstupní operace je velikost vstupně výstupní operace žádosti vygenerované vaší aplikace. Velikost vstupně-výstupní operace má značný vliv na výkon, hlavně na IOPS a šířky pásma, aby bylo možné dosáhnout aplikace. Následující vzorec znázorňuje vztah mezi IOPS, velikost vstupně-výstupní operace a šířky pásma nebo propustnosti.  
    ![](media/premium-storage-performance/image1.png)

Některé aplikace umožňují změnit jejich velikost vstupně-výstupní operace při některé aplikace nepodporují. Například SQL Server určuje optimální velikost vstupně-výstupní operace sám a neposkytuje uživatelé všechny knoflíky ho můžete změnit. Na druhé straně Oracle poskytuje parametr s názvem [DB\_BLOKOVAT\_velikost](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) pomocí které můžete nakonfigurovat velikost žádosti vstupně-výstupní operace databáze.

Pokud používáte aplikaci, která neumožňuje měnit velikost vstupně-výstupní operace, optimalizovat výkon klíčového ukazatele výkonu, který je nejdůležitější do vaší aplikace pomocí pokynů v tomto článku. Například:

* OLTP aplikace vygeneruje miliony požadavků malé a náhodných vstupně-výstupní operace. Ke zpracování těchto typu vstupně-výstupní požadavky, je třeba navrhnout infrastrukturu aplikace tak, aby získat vyšší IOPS.  
* Datového skladu aplikace generuje velké a sekvenčních vstupně-výstupní požadavky. Ke zpracování těchto typu vstupně-výstupní požadavky, je třeba navrhnout infrastrukturu aplikace tak, aby získat vyšší šířku pásma nebo propustnost.

Pokud používáte aplikaci, která vám umožní změnit velikost vstupně-výstupní operace, použijte toto pravidlo pro velikost vstupně-výstupní operace kromě jiné pokyny výkonu

* Chcete-li získat vyšší IOPS menší velikost vstupně-výstupní operace. Například 8 KB pro aplikaci OLTP.  
* Chcete-li získat vyšší šířku pásma nebo propustnost větší velikost vstupně-výstupní operace. Například 1 024 KB datového skladu aplikace.

Tady je příklad na tom, jak můžete vypočítat IOPS a propustnost nebo šířky pásma pro vaši aplikaci. Vezměte v úvahu aplikace pomocí P30 disku. Maximální počet, který můžete dosáhnout IOPS a propustnost disku P30 šířky pásma je 5000 IOPS a 200 MB za sekundu v uvedeném pořadí. Nyní Pokud vaše aplikace vyžaduje maximální IOPS z disku P30 a použít menší velikost vstupně-výstupní operace, jako je 8 KB, výsledná šířka pásma, nebudete moct získat je 40 MB za sekundu. Ale pokud vaše aplikace vyžaduje maximální propustnosti nebo šířky pásma z disku P30 a použijete větší velikost vstupně-výstupní operace, jako je 1024 KB, výsledná IOPS bude menší, 200 IOPS. Tedy vylaďte velikost vstupně-výstupní operace tak, že splňuje požadavek IOPS a propustnost nebo šířky obě aplikace. Následující tabulka shrnuje různé velikosti vstupně-výstupní operace a jejich odpovídající IOPS a propustnost pro P30 disk.

| Požadavků aplikace | Velikost vstupně-výstupních operací | IOPS | Propustnost nebo šířky pásma |
| --- | --- | --- | --- |
| Maximální počet vstupně-výstupních operací za sekundu |8 kB |5 000 |40 MB za sekundu |
| Maximální propustnost |1024 kB |200 |200 MB za sekundu |
| Maximální propustnost + vysoké IOPS |64 kB |3,200 |200 MB za sekundu |
| Maximální IOPS + Vysoká propustnost |32 KB. |5 000 |160 MB za sekundu |

IOPS a šířky pásma, vyšší než maximální hodnota disku úložiště jednoho premium, použijte více prémiové disky rozdělená společně. Například stripe dva P30 disky a k získání součet IOPS v 10 000 IOPS nebo kombinované propustnost 400 MB za sekundu. Jak je vysvětleno v další části, je nutné použít velikost virtuálního počítače, který podporuje kombinovaná IOPS a propustnost disku.

> [!NOTE]
> Jako zvýšíte IOPS nebo dalších také zvyšuje propustnost, ujistěte se, že že není stisknutí tlačítka propustnost nebo IOPS omezení disk nebo virtuální počítač při zvýšení buď jedné.
>
>

Účastnit účinky velikost vstupů/výstupů na výkon aplikace, můžete spustit testu typovou úlohou nástroje na virtuální počítač a disky. Vytvořit více testovacích bězích a použít jinou velikost vstupně-výstupní operace při každém spuštění zobrazíte dopad. Odkazovat [Benchmarking](#Benchmarking) na konci tohoto článku Další podrobnosti.

## <a name="high-scale-vm-sizes"></a>Měřítko velikosti virtuálních počítačů
Když spustíte navrhování aplikace, jeden z nejdůležitějších věcí, které chcete je, vyberte virtuální počítač pro hostování vaší aplikace. Storage úrovně Premium se dodává s velikosti vysoké škálování virtuálního počítače, které můžete spustit aplikace vyžadující vyšší výpočetní výkon a vysokou místního disku vstupně-výstupní výkon. Tyto virtuální počítače zadejte rychlejších procesorů vyšší poměr paměti jádra a Solid-State jednotky SSD (Solid-State Drive) pro místní disk. Příklady vysoké virtuálních počítačů škálování. podpora Storage úrovně Premium jsou řady DS, DSv2 a GS virtuálních počítačů.

Vysoká škálování virtuálních počítačů jsou k dispozici v různých velikostech s různým počtem jader procesoru, paměti, operačního systému a velikost dočasné disku. Každý velikost virtuálního počítače má také maximální počet datových disků, které lze připojit k virtuálnímu počítači. Proto zvolené velikost virtuálního počítače bude mít vliv na tom, kolik zpracování, paměti, a kapacitu úložiště je k dispozici pro vaši aplikaci. Taky ovlivňuje výpočetní a náklady na úložiště. V následující tabulce jsou specifikace největší velikost virtuálního počítače v řady DS, DSv2 series a GS řady:

| Velikost virtuálního počítače | Procesorová jádra | Memory (Paměť) | Velikosti disků virtuálních počítačů | Max. Datové disky | Velikost mezipaměti | IOPS | Omezení šířky pásma vstupně-výstupní mezipaměti |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OPERAČNÍHO SYSTÉMU = 1023 GB <br> Místní SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB za sekundu |4000 IOPS a 33 MB za sekundu |
| Standard_GS5 |32 |448 GB |OPERAČNÍHO SYSTÉMU = 1023 GB <br> Místní SSD = 896 GB |64 |4224 GB |80 000 IOPS <br> 2 000 MB za sekundu |5 000 IOPS a 50 MB za sekundu |

Pokud chcete zobrazit úplný seznam všech dostupných velikostí virtuálního počítače Azure, najdete [velikosti virtuálních počítačů Windows](../articles/virtual-machines/windows/sizes.md) nebo [velikosti virtuálního počítače s Linuxem](../articles/virtual-machines/linux/sizes.md). Zvolte velikost virtuálního počítače, která může splňovat a škálovat vašim požadavkům na výkon požadované aplikace. Kromě toho vzít v úvahu následující důležité zvážit při výběru velikosti virtuálních počítačů.

*Limity škálování*  
Maximální limit IOPS na virtuální počítač a na disk je jiné a nezávisle na sobě navzájem. Ujistěte se, že aplikace je v rámci virtuálního počítače a také na prémiových discích připojených k němu řídí IOPS. Výkon aplikace, jinak bude mít omezení.

Jako příklad předpokládejme, že požadavek na aplikaci je delší než 4 000 IOPS. Jak toho docílit, zřídíte P30 disku na virtuálním počítači DS1. P30 disk doručovat až 5000 IOPS. DS1 virtuální počítač je však omezená na 3,200 IOPS. V důsledku toho výkonu aplikací bude ovlivněna limitu virtuálních počítačů v 3,200 IOPS a bude snížení výkonu. K této situaci zabránit, zvolte velikost virtuálního počítače a disku, která budou obě splňovat požadavky na aplikace.

*Náklady na operace*  
V mnoha případech je možné, že vaše celkové náklady na použití služby Premium Storage operace nižší než při použití standardní úložiště.

Představte si třeba aplikace, které vyžadují 16 000 IOPS. K dosažení tohoto výkonu, budete potřebovat standardní\_virtuálního počítače Azure IaaS D14, které je možné maximální IOPS 16,000 pomocí 32 disků standardní úložiště 1 TB. Každý disk 1TB úložiště standard storage můžete dosáhnout maximálně 500 IOPS. Odhadované náklady tohoto virtuálního počítače za měsíc bude 1,570 $. Měsíční náklady na 32 disků standardní úložiště bude 1,638 $. Odhadované celkové měsíční náklady na bude 3,208 $.

Ale pokud budete hostovaný stejnou aplikaci na Storage úrovně Premium, budete potřebovat menší velikost virtuálního počítače a méně disky úložiště premium, proto snižuje celkové náklady. Standardní\_DS13 virtuálních počítačů můžete splňovat požadavek na 16 000 IOPS pomocí čtyř P30 disků. Virtuální počítač DS13 má maximální IOPS 25,600 a každý disk P30 maximální IOPS 5 000. Celkově platí, tuto konfiguraci můžete dosáhnout 5 000 × 4 = 20 000 IOPS. Odhadované náklady tohoto virtuálního počítače za měsíc bude 1,003 $. Měsíční náklady na čtyři disky úložiště premium P30 bude 544.34 $. Odhadované celkové měsíční náklady na bude 1,544 $.

Následující tabulka shrnuje rozpis nákladů tohoto scénáře pro Standard a Premium Storage.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Náklady virtuálního počítače za měsíc** |$1,570.58 (standardní\_D14) |$1,003.66 (standardní\_DS13) |
| **Ceny disků za měsíc** |1,638.40 (disky 32 × 1 TB) |544.34 (4 disky x P30) |
| **Celkové náklady za měsíc** |$3,208.98 |$1,544.34 |

*Distribucích systému Linux*  

S Azure Premium Storage získáte stejnou úroveň výkonu pro virtuální počítače s Windows a Linux. Podporujeme mnoho typů distribucích systému Linux, a můžete zobrazit seznam [zde](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Je důležité si uvědomit, že jiný distribucích jsou vhodnější pro různé typy úloh. Zobrazí se různé úrovně výkonu v závislosti na distro, které vaše úlohy běží na. Testování distribucích systému Linux s vaší aplikací a zvolit tu nejvhodnější.

Pokud Premium Storage s Linuxem, zkontrolujte nejnovější aktualizace o požadované ovladače zajistit vysoký výkon.

## <a name="premium-storage-disk-sizes"></a>Velikosti disků úložiště Premium
Azure Premium Storage nabízí aktuálně sedm velikosti disku. Velikost každého disku může mít jiné měřítko pro IOPS, šířky pásma a úložiště. Zvolte právo velikost disku úložiště Premium v závislosti na požadavky na aplikace a vysokým Škálováním velikost virtuálního počítače. Následující tabulka uvádí velikosti sedm disků a jejich funkce. P4 a P6 velikosti jsou aktuálně podporovány pouze pro spravované disky.

| Disky typu Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Velikost disku           | 32 GB | 64 GB | 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| Vstupně-výstupní operace za sekundu / disk       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Propustnost / disk | 25 MB za sekundu  | 50 MB za sekundu  | 100 MB za sekundu | 150 MB za sekundu | 200 MB za sekundu | 250 MB za sekundu | 250 MB za sekundu | 


Kolik disků, které zvolíte, závisí na disku velikost zvolená. Můžete použít jeden disk P50 nebo více disků P10 ke splnění požadavků vaší aplikace. Zohlednit při rozhodování níže uvedené důležité informace o účtu.

*Limity škálování (IOPS a propustnost)*  
IOPS a propustnost limity velikosti disku každého Premium je jiné a nezávislé z limity škálování virtuálních počítačů. Ujistěte se, že celkový počet IOPS a propustnost z disků jsou v souladu s limity škálování zvolené velikost virtuálního počítače.

Například, pokud požadavek na aplikaci je maximálně 250 MB za sekundu, propustnosti a používáte DS4 virtuální počítač s jediný disk P30. Virtuální počítač DS4 mohou poskytnout propustnost až 256 MB/s. Jediný disk P30 má však propustnost limit 200 MB/s. Aplikace v důsledku toho budou být omezené na 200 MB/s z důvodu omezení disku. K překonání tento limit, přidělení více než jeden datové disky na virtuální počítač nebo změnit jeho velikost vaše disky P40 nebo P50.

> [!NOTE]
> Čtení obsloužených mezipaměti nejsou zahrnuty v disku IOPS a propustnost, proto není předmět mezních hodnot disku. Mezipaměť obsahuje samostatné IOPS a propustnost limitu na virtuální počítač.
>
> Například původně čtení a zápisu jsou 60MB/s a 40MB za sekundu v uvedeném pořadí. V průběhu času mezipaměti warms a slouží více a více čtení z mezipaměti. Potom můžete získat zápisu vyšší propustnost z disku.
>
>

*Počet disků*  
Určete počet disky, které budete potřebovat tím, že posoudí požadavky aplikací. Limit velikost každého virtuálního počítače je také na počet disků, které lze připojit k virtuálnímu počítači. Obvykle je to dvojnásobný počet jader. Ujistěte se, že velikost virtuálního počítače, který zvolíte můžete podporují počet disků, které jsou potřeba.

Pamatujte si, že se úložiště Premium disky mají vyšší výkonnosti ve srovnání s disky standardní úložiště. Proto pokud migrujete aplikace z virtuálního počítače Azure IaaS pomocí standardního úložiště do úložiště úrovně Premium, budete pravděpodobně potřebovat méně prémiové disky k dosažení stejný nebo i vyšší výkon pro vaši aplikaci.

## <a name="disk-caching"></a>Ukládání do mezipaměti na disku
Vysoká škálování virtuálních počítačů, které využívají Storage úrovně Premium mají vícevrstvé ukládání do mezipaměti technologie názvem BlobCache. BlobCache používá kombinaci paměti RAM virtuálního počítače a místní SSD pro ukládání do mezipaměti. Tato mezipaměť je k dispozici pro trvalé disky úložiště Premium a místní disky virtuálních počítačů. Ve výchozím nastavení toto nastavení mezipaměti nastavena pro čtení a zápis pro disky operačního systému a jen pro čtení pro datové disky hostované na Storage úrovně Premium. S disků na discích úložiště Premium povoleno ukládání do mezipaměti, můžete dosáhnout vysoké škálování virtuálních počítačů velmi vysokou úroveň výkonu, které překračují základní výkon disku.

> [!WARNING]
> Změna nastavení mezipaměti Azure disku odpojí a znovu připojí cílový disk. Pokud je disk operačního systému, virtuální počítač se restartuje. Zastavte všechny aplikace a služby, které by mohly mít dopad tento přerušení před změnou nastavení mezipaměti na disku.
>
>

Další informace o tom, jak BlobCache funguje, najdete v tématu uvnitř [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) příspěvku na blogu.

Je potřeba povolit mezipaměť na správnou sadu disků. Tom, jestli by měl povolit ukládání do mezipaměti na disku na premium disk nebo nebude závisí na vzor zatížení tohoto disku bude zpracovávat. Následující tabulka uvádí výchozí nastavení mezipaměti pro disky operačního systému a Data.

| **Typ disku** | **Výchozí nastavení mezipaměti** |
| --- | --- |
| Disk OS |ReadWrite |
| Datový disk |Žádný |

Následují nastavení mezipaměti doporučené disku pro datové disky

| **Nastavení ukládání do mezipaměti na disku** | **Doporučení ohledně použití tohoto nastavení** |
| --- | --- |
| Žádný |Konfigurace mezipaměti hostitele jako None jen pro zápis a zápis náročné disků. |
| Jen pro čtení |Konfigurace mezipaměti hostitele jako jen pro čtení pro disky jen pro čtení a zápisu pro čtení. |
| ReadWrite |Konfigurace mezipaměti hostitele jako ReadWrite pouze v případě, že vaše aplikace zpracovává správně zápis data uložená v mezipaměti do trvalé disků v případě potřeby. |

*Jen pro čtení*  
Konfigurace ukládání do mezipaměti na Storage úrovně Premium data disky určené jen pro čtení, můžete dosáhnout nízkou latenci pro čtení a získat velmi vysoké IOPS pro čtení a propustnost pro vaši aplikaci. Toto je z důvodu ze dvou důvodů

1. Čtení provést z mezipaměti, která je na paměť virtuálního počítače a místní SSD, je mnohem rychlejší než čtení z disku data, která funguje ve službě Azure blob storage.  
2. Storage úrovně Premium nepočítá čtení z mezipaměti směrem disku IOPS a propustnosti. Aplikace je proto můžete dosáhnout vyšší celkový počet IOPS a propustnosti.

*ReadWrite*  
Ve výchozím nastavení mají disky operačního systému, ReadWrite povoleno ukládání do mezipaměti. Nedávno jsme doplnili podporu pro ukládání do mezipaměti na data i disky v režimu ReadWrite. Pokud používáte ReadWrite ukládání do mezipaměti, musí mít správný způsob, jak zapsat data z mezipaměti na trvalé disky. Například systému SQL Server zpracovává zápis data uložená v mezipaměti do trvalého úložiště disky svoje vlastní. ReadWrite mezipaměti pomocí aplikace, která zpracovává uložením požadovaná data může způsobit ztrátu dat, pokud dojde k chybě virtuálního počítače.

Například můžete použít tyto pokyny pro SQL Server běžící na Storage úrovně Premium provedením následujících akcí,

1. Konfigurace mezipaměti "Jen pro čtení" na discích úložiště premium hostování datových souborů.  
   a.  Vzhledem k tomu, že jsou data stránky mnohem rychleji načteny z mezipaměti dobu dotazu systému SQL Server ve srovnání s přímo z dat disky bude rychlý přečte z mezipaměti nižší.  
   b.  Obsluhuje čtení z mezipaměti, znamená, že není k dispozici z premium datových disků ke zvýšení propustnosti. SQL Server můžete použít tento ke zvýšení propustnosti směrem načítání více datových stránek a další operace, jako je zálohování a obnovení, dávky zatížení, a znovu sestaví index.  
2. Konfigurace "Žádný" v mezipaměti na discích úložiště premium hostování souborů protokolu.  
   a.  Soubory protokolů mají především zápisu náročná operace. Proto že nejsou nijak přínosné mezipaměti jen pro čtení.

## <a name="disk-striping"></a>Prokládání disků
Při vysoké měřítka, které je připojený virtuální počítač s několik disků trvalé úložiště premium, můžete disky rozdělená společně k agregaci jejich IOPs, šířky pásma a kapacity úložiště.

V systému Windows můžete prostory úložiště na disky stripe společně. Je nutné nakonfigurovat jeden sloupec pro každý disk ve fondu. Celkový výkon prokládaný svazek, jinak hodnota může být nižší, než se očekávalo, z důvodu nevyrovnaná distribuce přenosů mezi disky.

Důležité: Pomocí uživatelského rozhraní správce serveru, můžete nastavit celkový počet sloupců až 8 pro prokládaný svazek. Při připojení více než 8 disky, použijte PowerShell k vytvoření svazku. Pomocí prostředí PowerShell, můžete nastavit počet sloupců rovná počet disků. Například, pokud existují 16 disků v sadě jeden stripe; Zadejte 16 sloupců v *NumberOfColumns* parametr *New-VirtualDisk* rutiny prostředí PowerShell.

V systému Linux použijte nástroj MDADM na disky stripe společně. Podrobné pokyny k proložení disků v systému Linux naleznete v [konfigurace RAID softwaru v systému Linux](../articles/virtual-machines/linux/configure-raid.md).

*Velikost stripe*  
Důležité konfigurační v prokládání disků je velikost stripe. Velikost pruhu nebo velikost bloku je nejmenší bloků dat, které aplikace můžete vyřešit na prokládané svazku. Velikost prokládání, které můžete konfigurovat závisí na typu aplikace a jeho žádost vzor. Pokud si zvolíte velikost nesprávný stripe, může vést k chybné zarovnání vstupně-výstupní operace, což vede k snížení výkonu aplikace.

Například pokud požadavek vstupně-výstupní operace generované aplikace je větší, než je velikost disku stripe, systém úložiště zapíše ho napříč hranicemi jednotky stripe na více než jeden disk. Když je na čase přístup k datům, bude mít k vyhledání napříč více než jedné jednotky stripe dokončit žádost. Kumulativní účinek tohoto chování může způsobit snížení výkonu. Na druhé straně Pokud je menší než velikost stripe velikost žádosti vstupně-výstupní operace, a pokud je náhodný ve své podstatě, vstupně-výstupní operace žádosti může dohromady na stejném disku, problémové místo a nakonec docházelo k omezení výkonu vstupně-výstupní operace.

V závislosti na typu úloh, které vaše aplikace běží zvolte velikost příslušné stripe. Pro náhodné malé vstupně-výstupní požadavky použijte menší velikost stripe. Zatímco požadavky pro velkých sekvenčních vstupně-výstupní operace používat větší velikost stripe. Získat doporučení velikost stripe pro aplikaci, že budete používat na Storage úrovně Premium. Pro systém SQL Server nakonfigurujte stripe velikosti 64KB pro úlohy OLTP a 256KB pro úlohy datového skladu. V tématu [osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) Další informace.

> [!NOTE]
> Můžete společně rozkládají maximálně 32 prémiové disky úložiště na řady DS virtuálních počítačů a 64 prémiové disky úložiště na řadu GS virtuálních počítačů.
>
>

## <a name="multi-threading"></a>Více vláken
Azure Premium Storage platformy jako massively parallel určený. Vícevláknové aplikace proto dosahuje mnohem vyšší výkon než na jednovláknový aplikaci. Vícevláknové aplikace rozdělí si její úkoly napříč více vláken a zvyšuje efektivitu jeho spuštění s využitím virtuálních počítačů a diskové prostředky na maximum.

Například pokud aplikace běží na jednom jádru virtuálního počítače pomocí dvěma vlákny, procesoru přepínat mezi dvěma vlákny k dosažení efektivitu. Při jedno vlákno čeká na disku vstupně-výstupní operace dokončit, procesoru přepnout na jiné vlákno. Tímto způsobem můžete provádět dvěma vlákny více než jedno vlákno by. Pokud virtuální počítač má víc než jednoho jádra, další sníží běhu vzhledem k tomu, že každý základní paralelně můžete provádět úlohy.

Nelze změnit způsob dodávaných aplikace implementuje jeden vláken nebo více vláken. SQL Server je třeba, umožňuje zpracovávat více procesorů a s více jádry. SQL Server však rozhodne, za jakých podmínek využije jeden nebo více podprocesů ke zpracování dotazu. Může spouštět dotazy a vytvářet indexy používající více vláken. Pro daný dotaz, který zahrnuje připojení velké tabulky a řazení dat před vrácením uživateli SQL Server pravděpodobně používat více vláken. Uživatel však nemůže řídit, jestli SQL Server provede dotaz pomocí jedním vláknem nebo více vláken.

Existují nastavení konfigurace, které můžete změnit k ovlivnění to více vláken nebo paralelní zpracování aplikace. V případě systému SQL Server je třeba maximální úroveň paralelismus konfigurace. Toto nastavení nazývá MAXDOP, umožňuje nakonfigurovat maximální počet procesorů, které systém SQL Server můžete použít při paralelní zpracování. MAXDOP můžete nakonfigurovat pro jednotlivé dotazy nebo operace indexu. To je užitečné, když chcete vyrovnávat prostředky systému pro kritické aplikace výkonu.

Řekněme například, že vaše aplikace pomocí systému SQL Server spouští velké dotazu a operace indexu ve stejnou dobu. Předpokládejme, že chtěli byste operace indexu jako další původce ve srovnání s velké dotazu. V takovém případě můžete nastavit hodnotu MAXDOP operace indexu vyšší než hodnota MAXDOP pro dotaz. Tímto způsobem, SQL Server má větší počet procesorů, které můžete využít pro operaci indexu ve srovnání s počet procesorů, které můžete vyhradit pro velké dotaz. Pamatujte si, že se nebudete řídit počet vláken, který bude používat systém SQL Server pro každou operaci. Můžete řídit maximální počet procesorů, které je určené pro více vláken.

Další informace o [stupňů paralelismus](https://technet.microsoft.com/library/ms188611.aspx) v systému SQL Server. Zjistěte taková nastavení, které ovlivňují více vláken ve vaší aplikace a jejich konfigurace za účelem optimalizace výkonu.

## <a name="queue-depth"></a>Hloubka fronty
Hloubka fronty nebo délka fronty nebo velikost fronty je počet čekajících požadavků vstupně-výstupní operace v systému. Hodnota hloubku fronty určuje, kolik vstupně-výstupní operace aplikace můžete zarovnat, který bude zpracovávat disky úložiště. Ovlivňuje všechny ukazatele výkonu tři aplikace, které jsme popsané v této článku to IOP, propustnosti a latence.

Fronty hloubkou a více vláken jsou úzce souvisejí. Hloubka fronty hodnota udává, kolik více vláken může dosáhnout aplikací. Pokud je hloubkou fronty velký, aplikace může provést další operace souběžně, jinými slovy, více vláken. Pokud je hloubkou fronty malé, i když je aplikace Vícevláknová, nebude mít dostatek požadavky srovnáte pro souběžné provádění.

Obvykle se běžně dostupného aplikace neumožňují budete muset změnit hloubku fronty, protože pokud nastavte nesprávně provede další škodu než funkční. Aplikace bude nastavit správné hodnotu hloubky fronty, abyste získali optimální výkon. Je ale důležité si uvědomit tento koncept, takže můžete řešit problémy s výkonem s vaší aplikací. Můžete také sledovat důsledky hloubku fronty spuštěním testu typovou úlohou nástrojů v systému.

Některé aplikace zadejte nastavení k ovlivnění hloubku fronty. Například nastavení MAXDOP (maximální stupně paralelního zpracování) v systému SQL Server popsané v předchozí části. MAXDOP je způsob, jak ovlivnit hloubka fronty a více vláken, i když přímo nemění hodnotu hloubky fronty systému SQL Server.

*Hloubka fronty vysoké*  
Hloubka fronty vysoké řádků do více operací na disku. Na disku, bude znát další požadavek v příslušné fronty předem. V důsledku toho je disk můžete naplánovat operace předem a jejich zpracování v optimální pořadí. Vzhledem k tomu, že aplikace odesílá další požadavky na disk, disk může zpracovat více paralelních IOs. Nakonec aplikace se nebude moci dosáhnout vyšší IOPS. Vzhledem k tomu, že aplikace je zpracování více požadavků, také zvyšuje celkovou propustnost aplikace.

Obvykle se aplikace můžete dosáhnout maximální propustnost s 8-16 + nezpracovaných vstupně-výstupních na připojený disk. Pokud hloubce fronty, aplikace není dostatek IOs vkládání do systému a zpracuje menší množství v daném časovém období. Jinými slovy méně propustnost.

Například v systému SQL Server, nastavení hodnoty MAXDOP dotaz, který "4" informuje systému SQL Server, až čtyři jader může použít k provedení dotazu. SQL Server lze určit, co je nejlepší hodnotu hloubky fronty a počet jader pro spuštění dotazu.

*Optimální hloubky fronty*  
Fronty velmi vysokou hodnotu hloubky má také jeho nevýhody. Pokud hodnotu hloubky fronty je příliš vysoká, aplikace se pokusí jednotka velmi vysokou IOPS. Pokud aplikace obsahuje trvalé disků se dostatečná zřízené IOPS, může to mít negativní vliv latencí aplikací. Následující vzorec znázorňuje vztah mezi IOPS, latence a hloubku fronty.  
    ![](media/premium-storage-performance/image6.png)

Hloubka fronty byste neměli konfigurovat žádné vysokou hodnotu, ale na optimální hodnotu, která může poskytnout dostatek IOPS pro aplikaci, aniž by to ovlivnilo latenci. Například pokud latence aplikace musí být 1 milisekundu, hloubku fronty, které jsou potřebné k dosažení 5 000 IOPS se hloubka fronty = 5000 x 0,001 = 5.

*Hloubka fronty pro prokládané svazek*  
Pro svazek prokládané udržovat tak, aby každý disk má ve špičce hloubce fronty jednotlivě hloubce fronty dostatečně vysoký. Představte si třeba aplikace, který by vložil hloubce fronty 2 a v stripe je 4 disky. Dva požadavky vstupně-výstupní operace přejde na dva disky a zbývající dva disky bude nečinnosti. Proto konfigurovat hloubku fronty tak, aby všechny disky může být zaneprázdněn. Vzorec níže ukazuje, jak určit hloubku fronty prokládané svazky.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Throttling
Azure Premium Storage zřizuje zadaný počet IOPS a propustnost v závislosti na velikosti virtuálních počítačů a velikosti disků, které zvolíte. Kdykoliv se aplikace pokusí o jednotka IOPS nebo propustnosti nad těchto omezení co virtuálního počítače nebo disk může zpracovat, budou omezení úložiště Premium ho. To manifesty ve formě snížení výkonu ve vaší aplikaci. To může znamenat vyšší latence, snížit propustnost nebo snižte IOPS. Pokud Storage úrovně Premium není omezení, vaše aplikace může úplně nezdaří podle překročení, jaké jsou schopné dosáhnout její prostředky. Tak aby se zabránilo problémům s výkonem kvůli omezování, vždy zřídit dostatečné prostředky pro vaši aplikaci. Vzít v úvahu, co jsme probírali v částech velikosti disku výše a velikosti virtuálních počítačů. Srovnávací testy je nejlepší způsob, jak zjistit, jaké prostředky, je nutné kvůli hostování vaší aplikace.

## <a name="benchmarking"></a>Srovnávací testy
Srovnávací testy je proces simulaci různé úlohy ve vaší aplikaci a měření výkonu aplikace pro jednotlivá zatížení. Pomocí kroků popsaných v předchozí části, jste shromáždili na požadavcích na výkon aplikace. Při spuštění testu typovou úlohou nástroje na virtuální počítače, který je hostitelem aplikace, můžete určit úrovně výkonu, které aplikace můžete dosáhnout Storage úrovně Premium. V této části Poskytujeme vám příklady srovnávací testy standardní virtuální počítač DS14 zřizovat s disky Azure Premium Storage.

Použili jsme běžných testu typovou úlohou nástrojů Iometer a FIO, pro systém Windows a Linux v uvedeném pořadí. Tyto nástroje vytvořit více vláken simulaci provozní jako zatížení a měřit výkon systému. Pomocí nástrojů můžete také nakonfigurovat parametry jako velikost a fronty bloku hloubku, který obvykle se nelze změnit pro aplikaci. To vám dává větší flexibilitu při jednotka maximální výkon v měřítko zřizovat s prémiové disky pro různé typy úloh aplikací virtuálního počítače. Další informace o jednotlivých vzorová analytická pomůcka navštivte [Iometer](http://www.iometer.org/) a [FIO](http://freecode.com/projects/fio).

Podle níže uvedených příkladech, vytvořte standardní DS14 virtuální počítač a připojte 11 Storage úrovně Premium disky na virtuální počítač. 11 disků nakonfigurujte 10 disky s hostitelem ukládání do mezipaměti jako "Žádný" a rozkládají je do svazku názvem NoCacheWrites. Konfigurace hostitele ukládání do mezipaměti jako "Jen pro čtení" na zbývající disk a vytvořte svazek volána čtení z mezipaměti s tento disk. Pomocí tohoto nastavení, bude moci zobrazit maximální výkon čtení a zápis z standardní DS14 virtuálního počítače. Podrobné informace o postupu vytvoření virtuálního počítače DS14 s prémiové disky, přejděte na [vytvoření a použití účtu Premium Storage pro datový disk virtuálního počítače](../articles/virtual-machines/windows/premium-storage.md).

*Zahájení práce s mezipaměti*  
Disk s použití mezipaměti u hostitele jen pro čtení budou moci poskytnout IOPS vyšší než maximální disku. Chcete-li získat tuto maximální rychlost čtení z mezipaměti hostitele, nejprve je musí tedy mezipaměti tento disk. Tím se zajistí, že čtení IOs, které vzorová analytická pomůcka vyvolají na čtení z mezipaměti svazku ve skutečnosti přístupů do mezipaměti a nikoli disk přímo. Výsledek přístupů do mezipaměti v další IOPS z jedné mezipaměti povolena disku.

> **Důležité:**  
> Mezipaměti můžete musí tedy před spuštěním srovnávací testy, pokaždé, když je virtuální počítač restartovat.
>
>

#### <a name="iometer"></a>Iometer
[Stáhněte si nástroj Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) ve virtuálním počítači.

*Testovací soubor*  
Iometer používá testovací soubor, který je uložený na svazku, na kterém budete spouštět testu typovou úlohou test. Ho jednotky čte a zapisuje na tento testovací soubor k měření disku IOPS a propustnosti. Iometer vytvoří tento testovací soubor, pokud jste nezadali. Vytvořte 200GB testovací soubor s názvem iobw.tst na čtení z mezipaměti a NoCacheWrites svazky.

*Specifikace přístup*  
Specifikace, požádat o vstupně-výstupní operace velikost % pro čtení a zápis, % náhodných nebo sekvenčních jsou nakonfigurovány pomocí karty "Specifikace Access" v Iometer. Vytvořte specifikaci přístup pro jednotlivé scénáře popsané dole. Vytvoření specifikace přístup a "Uložit" s odpovídajícím názvem jako – RandomWrites\_8 kB, RandomReads\_8 kB. Vyberte odpovídající specifikaci při spuštění scénáře testu.

Níže je uveden příklad specifikací přístup pro scénář maximální IOPS zápisu  
    ![](media/premium-storage-performance/image8.png)

*Specifikace maximální IOPS testu*  
K předvedení maximální IOPs, použijte menší velikost žádosti. Použijte 8 kb velikost požadavku a vytvoření specifikace náhodných zápisů a čtení.

| Specifikace přístup | Velikost požadavku | Náhodné % | % Pro čtení |
| --- | --- | --- | --- |
| RandomWrites\_8 kb |8 KB |100 |0 |
| RandomReads\_8 kb |8 KB |100 |100 |

*Specifikace maximální propustnost testu*  
K předvedení maximální propustnost, použijte větší velikost žádosti. Použijte 64 tisíc velikost požadavku a vytvoření specifikace náhodných zápisů a čtení.

| Specifikace přístup | Velikost požadavku | Náhodné % | % Pro čtení |
| --- | --- | --- | --- |
| RandomWrites\_64 kb / s |64 KB |100 |0 |
| RandomReads\_64 kb / s |64 KB |100 |100 |

*Spouštění Iometer testu*  
Proveďte následující postup tedy mezipaměti

1. Vytvořte dva specifikace přístup s hodnoty zobrazené níže,

   | Název | Velikost požadavku | Náhodné % | % Pro čtení |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1MB |100 |0 |
   | RandomReads\_1 MB |1MB |100 |100 |
2. Spusťte test Iometer pro inicializaci mezipaměti disku s následujícími parametry. Pomocí tří pracovních vláken pro cílový svazek a hloubce fronty 128. Nastavte "Běh" doba trvání testu na 2hrs na kartě "Testování instalace".

   | Scénář | Cílový svazek | Název | Doba trvání |
   | --- | --- | --- | --- |
   | Inicializovat Disk mezipaměti |Čtení z mezipaměti |RandomWrites\_1 MB |2hrs |
3. Spusťte test Iometer pro zahájení práce s disku mezipaměti s následujícími parametry. Pomocí tří pracovních vláken pro cílový svazek a hloubce fronty 128. Nastavte "Běh" doba trvání testu na 2hrs na kartě "Testování instalace".

   | Scénář | Cílový svazek | Název | Doba trvání |
   | --- | --- | --- | --- |
   | Horké až diskové mezipaměti |Čtení z mezipaměti |RandomReads\_1 MB |2hrs |

Po diskové mezipaměti je provozní teplotu, pokračujte testovací scénáře uvedené níže. Pokud chcete spustit Iometer test, použijte aspoň tři pracovních vláken pro **každý** cíle svazku. Pro každý pracovní vlákno vyberte cílový svazek, nastavte hloubku fronty a vyberte jednu z uložené testovací specifikace, jak je znázorněno v následující tabulce, ke spuštění odpovídající scénáře testu. V tabulce také jsou očekávané výsledky pro IOPS a propustnost při spuštění tyto testy. Ve všech scénářích se používá malé velikost vstupně-výstupní operace 8 kB a hloubce fronty vysoké 128.

| Testovací scénář | Cílový svazek | Název | Výsledek |
| --- | --- | --- | --- |
| Max. Čtení IOPS |Čtení z mezipaměti |RandomWrites\_8 kb |50 000 IOPS |
| Max. Zápis IOPS |NoCacheWrites |RandomReads\_8 kb |64 000 IOPS |
| Max. Součet IOPS |Čtení z mezipaměti |RandomWrites\_8 kb |100 000 IOPS |
| NoCacheWrites |RandomReads\_8 kb | &nbsp; | &nbsp; |
| Max. Čtení MB/s |Čtení z mezipaměti |RandomWrites\_64 kb / s |524 MB/s |
| Max. Zápis MB/s |NoCacheWrites |RandomReads\_64 kb / s |524 MB/s |
| Kombinovaná MB/s |Čtení z mezipaměti |RandomWrites\_64 kb / s |1 000 MB/s |
| NoCacheWrites |RandomReads\_64 kb / s | &nbsp; | &nbsp; |

Tady jsou snímky obrazovky Iometer výsledky testu pro kombinované scénáře IOPS a propustnosti.

*Kombinovaná čtení a zápisy maximální IOPS.*  
![](media/premium-storage-performance/image9.png)

*Kombinovaná čtení a zápisy maximální propustnost*  
![](media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO
FIO je nástroj oblíbených do testu výkonnosti úložiště na virtuálních počítačích systému Linux. Má flexibilitu a vyberte jiný vstupně-výstupní operace velikostí, sekvenční nebo náhodné čtení a zápisu. Se vytvoří pracovní vlákna a procesy provést zadaný vstupně-výstupních operací. Zadaný typ vstupně-výstupních operací každý pracovní vlákno musíte provést pomocí úlohy souborů. Jsme vytvořili jeden soubor úlohy pro scénář předvedené v příkladech níže. Specifikace v těchto souborech úlohy otestovat různé úlohy běžící na Storage úrovně Premium, můžete změnit. V příkladech se používá standardní DS 14 virtuální počítač spuštěný **Ubuntu**. Použít stejné nastavení, které jsou popsané na začátku [srovnávací testy části](#Benchmarking) a záložním vytvoří mezipaměť před spuštěním testů typovou úlohou.

Než začnete, [stáhnout FIO](https://github.com/axboe/fio) a nainstalujte ji na virtuálním počítači.

Spusťte následující příkaz pro Ubuntu,

```
apt-get install fio
```

Pro řízení operace čtení na discích použijeme čtyři pracovních vláken pro řízení operace zápisu a čtyři pracovních vláken. Pracovníci zápisu se řízení provozu na svazku "nocache", který obsahuje 10 disky s mezipamětí nastaven na "Žádný". Pracovní procesy pro čtení se řízení provozu na svazku "readcache", který má 1 disk s mezipaměti nastaven na "Jen pro čtení".

*Maximální zápis IOPS*  
Vytvořte soubor úlohy s následující specifikace získat maximální IOPS zápisu. Název "fiowrite.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Všimněte si, postupujte podle klíče věcí, které jsou v souladu s pokyny pro návrh popsané v předchozích částech. Tyto specifikace jsou nezbytné k řízení maximální IOPS,  

* Hloubka fronty vysoké 256.  
* Velikost malé bloku 8KB.  
* Více vláken provádění náhodných zápisů.

Spusťte následující příkaz, který ji FIO test pro 30 sekund,  

```
sudo fio --runtime 30 fiowrite.ini
```

Při spuštění testu, nebudete moci zobrazit počet zápisu IOPS virtuálního počítače a poskytujete prémiové disky. Jak znázorňuje následující ukázka, virtuální počítač DS14 doručování jeho zápisu maximální limit IOPS 50 000 IOPS.  
    ![](media/premium-storage-performance/image11.png)

*Čtení maximální IOPS*  
Vytvořte soubor úlohy s následující specifikace získat maximální IOPS pro čtení. Název "fioread.ini".

```
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Všimněte si, postupujte podle klíče věcí, které jsou v souladu s pokyny pro návrh popsané v předchozích částech. Tyto specifikace jsou nezbytné k řízení maximální IOPS,

* Hloubka fronty vysoké 256.  
* Velikost malé bloku 8KB.  
* Více vláken provádění náhodných zápisů.

Spusťte následující příkaz, který ji FIO test pro 30 sekund,

```
sudo fio --runtime 30 fioread.ini
```

Při spuštění testu, nebudete moci zobrazit počet čtení IOPS virtuálního počítače a poskytujete prémiové disky. Jak znázorňuje následující ukázka, virtuální počítač DS14 doručování více než 64 000 IOPS pro čtení. Toto je kombinací disku a výkon mezipaměti.  
    ![](media/premium-storage-performance/image12.png)

*Maximální počet čtení a zápisu IOPS*  
Vytvoření souboru úlohy pomocí následující specifikace získat maximální kombinaci oprávnění ke čtení a zápis IOPS. Název "fioreadwrite.ini".

```
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Všimněte si, postupujte podle klíče věcí, které jsou v souladu s pokyny pro návrh popsané v předchozích částech. Tyto specifikace jsou nezbytné k řízení maximální IOPS,

* Hloubka fronty vysoké 128.  
* Velikost bloku malé 4KB.  
* Více vláken provádění náhodných čte a zapisuje.

Spusťte následující příkaz, který ji FIO test pro 30 sekund,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Při spuštění testu, nebudete moci zobrazit počet kombinované pro čtení a zápisu IOPS virtuálního počítače a poskytujete prémiové disky. Jak znázorňuje následující ukázka, virtuální počítač DS14 doručování více než 100 000 kombinované oprávnění ke čtení a zápis IOPS. Toto je kombinací disku a výkon mezipaměti.  
    ![](media/premium-storage-performance/image13.png)

*Nejvyšší možná propustnost*  
Získat maximální kombinovat pro čtení a zápisu propustnosti, použít větší velikost bloku a hloubku fronty velké s více vlákny provádění čtení a zápisy. Můžete použít velikost bloku 64KB a hloubku fronty 128.

## <a name="next-steps"></a>Další kroky
Další informace o službě Azure Premium Storage:

* 
  [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../articles/virtual-machines/windows/premium-storage.md)  

Pro uživatele systému SQL Server, přečtěte si článek na nejlepší postupy z hlediska výkonu pro SQL Server:

* [Osvědčené postupy z hlediska výkonu pro SQL Server na virtuálních počítačích Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Storage úrovně Premium poskytuje nejvyšší výkon pro SQL Server ve virtuálním počítači Azure](http://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)
