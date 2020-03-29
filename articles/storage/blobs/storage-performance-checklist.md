---
title: Kontrolní seznam výkonu a škálovatelnosti pro úložiště objektů Blob – Azure Storage
description: Kontrolní seznam osvědčených postupů pro použití s úložištěm objektů Blob při vývoji vysoce výkonných aplikací.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: e4103f8360f6fa80470b0f8002a61f8ac903bd8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255428"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Kontrolní seznam výkonu a škálovatelnosti pro úložiště objektů Blob

Společnost Microsoft vyvinula řadu osvědčených postupů pro vývoj vysoce výkonných aplikací s úložištěm objektů Blob. Tento kontrolní seznam identifikuje klíčové postupy, které mohou vývojáři sledovat pro optimalizaci výkonu. Mějte tyto postupy na paměti při navrhování aplikace a v průběhu celého procesu.

Azure Storage má škálovatelnost a výkonnostní cíle pro kapacitu, rychlost transakcí a šířku pásma. Další informace o cílech škálovatelnosti úložiště Azure najdete v tématu [Škálovatelnost a cíle výkonu pro standardní účty úložiště](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) a [škálovatelnost a cíle výkonu pro úložiště objektů Blob](scalability-targets.md).

## <a name="checklist"></a>Kontrolní seznam

Tento článek uspořádá osvědčené postupy pro výkon do kontrolního seznamu, který můžete sledovat při vývoji aplikace úložiště objektů Blob.

| Hotovo | Kategorie | Posouzení návrhu |
| --- | --- | --- |
| &nbsp; |Cíle škálovatelnosti |[Můžete navrhnout aplikaci tak, aby používala maximálně maximální počet účtů úložiště?](#maximum-number-of-storage-accounts) |
| &nbsp; |Cíle škálovatelnosti |[Vyhýbáte se blížícím se kapacitním a transakčním limitům?](#capacity-and-transaction-targets) |
| &nbsp; |Cíle škálovatelnosti |[Je velký počet klientů přístup k jeden objekt blob současně?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Cíle škálovatelnosti |[Zůstává vaše aplikace v rámci cílů škálovatelnosti pro jeden objekt blob?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Dělení |[Je konvence pojmenování navržena tak, aby umožňovala lepší vyrovnávání zatížení?](#partitioning) |
| &nbsp; |Síťové služby |[Mají zařízení na straně klienta dostatečně vysokou šířku pásma a nízkou latenci, aby dosáhla potřebného výkonu?](#throughput) |
| &nbsp; |Síťové služby |[Mají zařízení na straně klienta vysoce kvalitní síťové spojení?](#link-quality) |
| &nbsp; |Síťové služby |[Je klientská aplikace ve stejné oblasti jako účet úložiště?](#location) |
| &nbsp; |Přímý přístup klientů |[Používáte sdílené přístupové podpisy (SAS) a sdílení prostředků mezi zdroji (CORS) k povolení přímého přístupu k Azure Storage?](#sas-and-cors) |
| &nbsp; |Ukládání do mezipaměti |[Jsou data mezipaměti aplikace často přístupná a zřídka se mění?](#reading-data) |
| &nbsp; |Ukládání do mezipaměti |[Je vaše aplikace dávkování aktualizace jejich ukládání do mezipaměti na klienta a potom jejich nahrání ve větších sadách?](#uploading-data-in-batches) |
| &nbsp; |Konfigurace rozhraní .NET |[Používáte rozhraní .NET Core 2.1 nebo novější pro optimální výkon?](#use-net-core) |
| &nbsp; |Konfigurace rozhraní .NET |[Nakonfigurovali jste klienta tak, aby používal dostatečný počet souběžných připojení?](#increase-default-connection-limit) |
| &nbsp; |Konfigurace rozhraní .NET |[Nakonfigurovali jste rozhraní .NET pro použití dostatečného počtu podprocesů v aplikacích .NET?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelnost |[Ujistili jste se, že paralelismus je vhodně ohraničen, abyste nepřetížili schopnosti klienta nebo nepřiblížili se k cílům škálovatelnosti?](#unbounded-parallelism) |
| &nbsp; |Nástroje |[Používáte nejnovější verze klientských knihoven a nástrojů poskytovaných společností Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Opakování |[Používáte zásadu opakování s exponenciálním zpětným omezením pro omezení chyb a časového nastavení?](#timeout-and-server-busy-errors) |
| &nbsp; |Opakování |[Vyhýbá se vaše aplikace opakování neopakovatelných chyb?](#non-retryable-errors) |
| &nbsp; |Kopírování objektů BLOB |[Kopírujete objekty BLOB co nejefektivnějším způsobem?](#blob-copy-apis) |
| &nbsp; |Kopírování objektů BLOB |[Používáte nejnovější verzi AzCopy pro operace hromadného kopírování?](#use-azcopy) |
| &nbsp; |Kopírování objektů BLOB |[Používáte rodinu datových schrápů Azure pro import velkých objemů dat?](#use-azure-data-box) |
| &nbsp; |Distribuce obsahu |[Používáte CDN pro distribuci obsahu?](#content-distribution) |
| &nbsp; |Použití metadat |[Ukládáte často používaná metadata o objektech BLOB do jejich metadat?](#use-metadata) |
| &nbsp; |Rychlé nahrávání |[Když se pokoušíte rychle nahrát jeden objekt blob, nahráváte bloky paralelně?](#upload-one-large-blob-quickly) |
| &nbsp; |Rychlé nahrávání |[Když se snažíte rychle nahrát mnoho objektů BLOB, nahráváte objekty BLOB paralelně?](#upload-many-blobs-quickly) |
| &nbsp; |Typ objektu blob |[Používáte objekty BLOB stránky nebo objekty BLOB bloku, pokud je to vhodné?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Cíle škálovatelnosti

Pokud vaše aplikace přistupuje nebo překračuje některý z cílů škálovatelnosti, může dojít ke zvýšení latence transakcí nebo omezení. Když Azure Storage omezí vaši aplikaci, služba začne vracet kódy chyb 503 (Server zaneprázdněn) nebo 500 (časový limit operace). Vyhnout se tyto chyby tím, že zůstane v mezích cíle škálovatelnosti je důležitou součástí zvýšení výkonu vaší aplikace.

Další informace o cílech škálovatelnosti pro službu Frontnajdete v [tématu Škálovatelnost úložiště Azure a cíle výkonu](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximální počet účtů úložiště

Pokud se blížíte maximálnímu počtu účtů úložiště povoleným pro konkrétní kombinaci předplatného nebo oblasti, vyhodnoťte svůj scénář a zjistěte, zda platí některá z následujících podmínek:

- Používáte účty úložiště k ukládání nespravovaných disků a přidávání těchto disků do virtuálních počítačů?. V tomto scénáři společnost Microsoft doporučuje používat spravované disky. Spravované disky se pro vás škálují automaticky a bez nutnosti vytvářet a spravovat jednotlivé účty úložiště. Další informace najdete [v tématu Úvod do spravovaných disků Azure](../../virtual-machines/windows/managed-disks-overview.md)
- Používáte jeden účet úložiště na zákazníka za účelem izolace dat? V tomto scénáři společnost Microsoft doporučuje používat kontejner objektů blob pro každého zákazníka namísto celého účtu úložiště. Azure Storage teď umožňuje přiřadit role řízení přístupu na základě rolí (RBAC) na základě kontejneru. Další informace najdete [v tématu Udělení přístupu k datům objektů blob Azure a fronty s RBAC na webu Azure Portal](../common/storage-auth-aad-rbac-portal.md).
- Používáte více účtů úložiště k úlomku ke zvýšení příchozího přenosu dat, odchozího přenosu dat, vstupně-výstupních operací za sekundu (VOPS) nebo kapacity? V tomto scénáři společnost Microsoft doporučuje využít zvýšené limity pro účty úložiště snížit počet účtů úložiště požadovaných pro vaše úlohy, pokud je to možné. Obraťte se na [podporu Azure](https://azure.microsoft.com/support/options/) a požádejte o zvýšení limitů pro váš účet úložiště. Další informace najdete [v tématu Oznámení větší, vyšší škálování účty úložiště](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Kapacitní a transakční cíle

Pokud se vaše aplikace blíží cílům škálovatelnosti pro jeden účet úložiště, zvažte přijetí jednoho z následujících přístupů:  

- Pokud vaše aplikace dosáhne cíle transakce, zvažte použití účtů úložiště objektů blob bloku, které jsou optimalizované pro vysoké transakční sazby a nízkou a konzistentní latenci. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).
- Znovu zvažte zatížení, které způsobí, že vaše aplikace přístup nebo překročit cíl škálovatelnosti. Můžete jej navrhnout jinak, abyste využili menší šířku pásma nebo kapacitu nebo méně transakcí?
- Pokud vaše aplikace musí překročit jeden z cílů škálovatelnosti, vytvořte více účtů úložiště a rozdělte data aplikace mezi tyto účty více úložišť. Pokud použijete tento vzor, nezapomeňte navrhnout aplikaci tak, aby v budoucnu bylo možné přidat další účty úložiště pro vyrovnávání zatížení. Samotné účty úložiště nemají žádné jiné náklady než vaše použití, pokud jde o uložená data, provedené transakce nebo přenesená data.
- Pokud se vaše aplikace blíží cílům šířky pásma, zvažte kompresi dat na straně klienta, abyste snížili šířku pásma potřebnou k odeslání dat do Služby Azure Storage.
    Zatímco komprese dat může ušetřit šířku pásma a zlepšit výkon sítě, může mít také negativní vliv na výkon. Vyhodnoťte dopad na výkon dalších požadavků na zpracování komprese a dekomprese dat na straně klienta. Mějte na paměti, že ukládání komprimovaných dat může ztížit řešení potíží, protože zobrazení dat pomocí standardních nástrojů může být náročnější.
- Pokud se vaše aplikace blíží cíle škálovatelnosti, ujistěte se, že používáte exponenciální backoff pro opakování. Je nejlepší se pokusit vyhnout dosažení cílů škálovatelnosti implementací doporučení popsaných v tomto článku. Použití exponenciálního backoff pro opakování však zabrání vaší aplikaci z opakování rychle, což by mohlo zhoršit omezení. Další informace naleznete v části s názvem [Chyby časového nastavení a zaneprázdnění serveru](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Více klientů, kteří přistupují k jednomu objektu blob současně

Pokud máte velký počet klientů přístup k jednomu objektu blob současně, budete muset zvážit cíle škálovatelnosti na objekt blob a na účet úložiště. Přesný počet klientů, kteří mají přístup k jednomu objektu blob, se bude lišit v závislosti na faktorech, jako je počet klientů požadujících objekt blob současně, velikost objektu blob a podmínky sítě.

Pokud lze objekt blob distribuovat prostřednictvím sítě CDN, jako jsou obrázky nebo videa zobrazovaná z webu, můžete použít disk CDN. Další informace naleznete v části s názvem [Distribuce obsahu](#content-distribution).

V jiných scénářích, jako jsou vědecké simulace, kde jsou data důvěrná, máte dvě možnosti. Prvním z vás je rozložit přístup k úlohě tak, aby objekt blob přistupoval po dobu a současně přistupovat k objektu blob. Případně můžete dočasně zkopírovat objekt blob do více účtů úložiště zvýšit celkový počet vibep na objekt blob a napříč účty úložiště. Výsledky se budou lišit v závislosti na chování vaší aplikace, proto nezapomeňte otestovat vzory souběžnosti během návrhu.

### <a name="bandwidth-and-operations-per-blob"></a>Šířka pásma a operace na objekt blob

Jeden objekt blob podporuje až 500 požadavků za sekundu. Pokud máte více klientů, kteří potřebují číst stejný objekt blob a můžete překročit tento limit, zvažte použití účtu úložiště objektů blob bloku. Účet úložiště objektů blob bloku poskytuje vyšší míru požadavků nebo vstupně-no operace za sekundu (VSTUPNĚ-To).

Můžete také použít síť pro doručování obsahu (CDN), jako je Azure CDN k distribuci operací na objektblob. Další informace o Azure CDN najdete v [tématu Přehled Azure CDN](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Dělení

Pochopení toho, jak Azure Storage oddíly dat blob je užitečné pro zvýšení výkonu. Azure Storage může obsluhovat data v jednom oddílu rychleji než data, která zahrnuje více oddílů. Vhodným pojmenováním objektů BLOB můžete zvýšit efektivitu požadavků na čtení.

Úložiště objektů blob používá schéma oddílů založené na rozsahu pro škálování a vyrovnávání zatížení. Každý objekt blob má klíč oddílu složený z úplného názvu objektu blob (účet + kontejner + objekt blob). Klíč oddílu se používá k rozdělení dat objektů blob do rozsahů. Rozsahy jsou pak vyrovnávání zatížení v rámci úložiště objektů Blob.

Dělení na základě rozsahu znamená, že konvence pojmenování, které používají lexikální řazení (například *mypayroll*, *myperformance*, *myemployees*, etc.) nebo časová razítka (*log20160101*, *log20160102*, *log20160102*, atd.) mají větší pravděpodobnost, že oddíly budou umístěny společně na stejném serveru oddílů. , dokud zvýšené zatížení nevyžaduje, aby byly rozděleny do menších rozsahů. Co-locating objekty BLOB na stejném serveru oddílů zvyšuje výkon, takže důležitou součástí zvýšení výkonu zahrnuje pojmenování objektů BLOB způsobem, který je co nejefektivněji organizuje.

Například všechny objekty BLOB v rámci kontejneru může být obsluhována jeden server, dokud zatížení těchto objektů BLOB vyžaduje další vyvážení rozsahů oddílů. Podobně skupina lehce načtených účtů s jejich názvy uspořádanými v lexikálním pořadí může být obsluhována jedním serverem, dokud zatížení jednoho nebo všech těchto účtů nevyžaduje jejich rozdělení mezi více serverů oddílů.

Každá operace vyrovnávání zatížení může mít vliv na latenci volání úložiště během operace. Schopnost služby zpracovat náhlý nárůst provozu na oddíl je omezena škálovatelností jednoho serveru oddílu, dokud operace vyrovnávání zatížení nespustí a znovu nevyváží rozsah klíčů oddílu.

Můžete postupovat podle některých doporučených postupů ke snížení četnosti těchto operací.  

- Pokud je to možné, použijte objekt blob nebo velikosti bloků větší než 4 MiB pro účty standardníúložiště a větší než 256 KiB pro účty úložiště premium. Větší velikosti objektů blob nebo bloků automaticky aktivují objekty BLOB bloku s vysokou propustností. Objekty BLOB bloku s vysokou propustností poskytují vysoce výkonnější ingestování, které není ovlivněno pojmenováním oddílu.
- Zkontrolujte konvenci pojmenování, kterou používáte pro účty, kontejnery, objekty BLOB, tabulky a fronty. Zvažte předponou názvy účtů, kontejnerů nebo objektů blob s třímístným hodnotou hash pomocí funkce hash, která nejlépe vyhovuje vašim potřebám.
- Pokud data uspořádáte pomocí časových razítek nebo číselných identifikátorů, ujistěte se, že nepoužíváte pouze připojit (nebo pouze předškolní) vzor provozu. Tyto vzory nejsou vhodné pro systém dělení na základě rozsahu. Tyto vzory může vést k veškerý provoz jít do jednoho oddílu a omezení systému z efektivně vyrovnávání zatížení.

    Například pokud máte denní operace, které používají objekt blob s časovým razítkem, jako je *například yyyymmdd*, pak veškerý provoz pro tuto denní operaci je směrován na jeden objekt blob, který je obsluhován serverem s jedním oddílem. Zvažte, zda limity pro každý objekt blob a omezení na oddíl odpovídají vašim potřebám, a zvažte rozdělení této operace na více objektů BLOB v případě potřeby. Podobně pokud ukládáte data časových řad do tabulek, veškerý provoz může být směrován do poslední části oboru názvů klíčů. Pokud používáte číselná ID, předponu ID s třímístným hodnotou hash. Pokud používáte časová razítka, předpona časové razítko s hodnotou sekund, například *ssyyyymdd .* Pokud vaše aplikace běžně provádí výpis a dotazování operace, zvolte funkci hash, která omezí počet dotazů. V některých případech může být dostatečná náhodná předpona.
  
- Další informace o schématu dělení používaném ve službě Azure Storage najdete v [tématu Azure Storage: Vysoce dostupná služba cloudového úložiště se silnou konzistencí](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

## <a name="networking"></a>Síťové služby

Fyzická síťová omezení aplikace může mít významný dopad na výkon. V následujících částech jsou popsána některá omezení, se kterými se mohou uživatelé setkat.  

### <a name="client-network-capability"></a>Možnosti klientské sítě

Šířka pásma a kvalita síťového propojení hrají důležitou roli ve výkonu aplikace, jak je popsáno v následujících částech.

#### <a name="throughput"></a>Propustnost

Pro šířku pásma, problém je často možnosti klienta. Větší instance Azure mají síťové karty s větší kapacitou, takže byste měli zvážit použití větší instance nebo více virtuálních počítačů, pokud potřebujete vyšší limity sítě z jednoho počítače. Pokud přistupujete k Azure Storage z místní aplikace, platí stejné pravidlo: porozumět síťovým možnostem klientského zařízení a síťovému připojení k umístění Azure Storage a buď je podle potřeby vylepšit, nebo navrhnout své aplikace pro práci v rámci svých schopností.

#### <a name="link-quality"></a>Kvalita propojení

Stejně jako u všech síťových použití mějte na paměti, že síťové podmínky, které vedou k chybám a ztrátě paketů, zpomalí efektivní propustnost.  Použití WireShark nebo NetMon může pomoci při diagnostice tohoto problému.  

### <a name="location"></a>Umístění

V jakémkoli distribuovaném prostředí poskytuje umístění klienta v blízkosti serveru nejlepší výkon. Pro přístup k Azure Storage s nejnižší latencí, nejlepší umístění pro vašeho klienta je ve stejné oblasti Azure. Například pokud máte webovou aplikaci Azure, která používá Azure Storage, pak vyhledejte oba v rámci jedné oblasti, jako je například USA – západ nebo Asie – jihovýchod. Co-locating prostředky snižuje latenci a náklady, protože využití šířky pásma v rámci jedné oblasti je zdarma.  

Pokud klientské aplikace budou přistupovat k Azure Storage, ale nejsou hostované v rámci Azure, jako jsou aplikace pro mobilní zařízení nebo místní podnikové služby, pak umístění účtu úložiště v oblasti v blízkosti těchto klientů může snížit latenci. Pokud jsou vaši klienti široce distribuováni (například někteří v Severní Americe a někteří v Evropě), zvažte použití jednoho účtu úložiště pro každý region. Tento přístup je jednodušší implementovat, pokud data, která aplikace ukládá, jsou specifická pro jednotlivé uživatele a nevyžaduje replikaci dat mezi účty úložiště.

Pro širokou distribuci obsahu objektů blob použijte síť doručování obsahu, jako je Azure CDN. Další informace o Azure CDN najdete v [tématu Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS a CORS

Předpokládejme, že potřebujete autorizovat kód, jako je JavaScript, který běží ve webovém prohlížeči uživatele nebo v aplikaci pro mobilní telefon pro přístup k datům ve službě Azure Storage. Jedním z přístupů je vytvoření aplikace služby, která funguje jako proxy server. Zařízení uživatele se ověřuje pomocí služby, která zase autorizuje přístup k prostředkům Azure Storage. Tímto způsobem se můžete vyhnout vystavení klíčů účtu úložiště na nezabezpečených zařízeních. Tento přístup však klade významné režie na aplikaci služby, protože všechna data přenesená mezi zařízením uživatele a Azure Storage musí projít aplikací služby.

Můžete se vyhnout použití aplikace služby jako proxy pro Azure Storage pomocí sdílených přístupových podpisů (SAS). Pomocí SAS můžete povolit zařízení uživatele, aby požadavky přímo do Služby Azure Storage pomocí tokenu omezený přístup. Například pokud uživatel chce nahrát fotografii do vaší aplikace, pak aplikace služby můžete vygenerovat SAS a odeslat do zařízení uživatele. Token SAS můžete udělit oprávnění k zápisu do prostředku úložiště Azure pro zadaný časový interval, po kterém vyprší platnost tokenu SAS. Další informace o SAS najdete [v tématu udělení omezeného přístupu k prostředkům Úložiště Azure pomocí sdílených přístupových podpisů (SAS).](../common/storage-sas-overview.md)  

Webový prohlížeč obvykle nepovolí JavaScript na stránce, která je hostována webem v jedné doméně, provádět určité operace, například operace zápisu, do jiné domény. Tato zásada, známá jako zásady stejného původu, zabraňuje škodlivému skriptu na jedné stránce získat přístup k datům na jiné webové stránce. Zásady stejného původu však může být omezení při vytváření řešení v cloudu. Sdílení prostředků mezi zdroji (CORS) je funkce prohlížeče, která umožňuje cílové doméně komunikovat s prohlížečem, kterému důvěřuje požadavkům pocházejícím ze zdrojové domény.

Předpokládejme například, že webová aplikace spuštěná v Azure vytvoří požadavek na prostředek pro účet úložiště Azure. Webová aplikace je zdrojová doména a účet úložiště je cílová doména. Cors můžete nakonfigurovat pro kteroukoli službu Azure Storage tak, aby komunikovala s webovým prohlížečem, že požadavky ze zdrojové domény jsou službou Azure Storage důvěryhodné. Další informace o CORS najdete v [tématu podpora sdílení prostředků mezi zdroji (CORS) pro Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
SAS i CORS vám mohou pomoci vyhnout se zbytečnému zatížení webové aplikace.  

## <a name="caching"></a>Ukládání do mezipaměti

Ukládání do mezipaměti hraje důležitou roli ve výkonu. Následující části popisují doporučené postupy ukládání do mezipaměti.

### <a name="reading-data"></a>Čtení dat

Obecně platí, že čtení dat jednou je vhodnější než čtení dvakrát. Vezměme si příklad webové aplikace, která načetla 50 MiB objektu blob z Azure Storage sloužit jako obsah pro uživatele. V ideálním případě aplikace ukládá objekt blob místně na disk a potom načte verzi uloženou v mezipaměti pro následné požadavky uživatelů.

Jedním ze způsobů, jak se vyhnout načítání objektu blob, pokud nebyl změněn, protože byl uložen do mezipaměti, je kvalifikovat operaci GET s podmíněnou hlavičkou pro čas změny. Pokud poslední upravený čas je po době, kdy byl objekt blob uložen do mezipaměti, pak objekt blob je načten a znovu do mezipaměti. V opačném případě je objekt blob v mezipaměti načten pro optimální výkon.

Můžete se také rozhodnout navrhnout aplikaci předpokládat, že objekt blob zůstane beze změny po krátkou dobu po načtení. V takovém případě aplikace není nutné zkontrolovat, zda objekt blob byl změněn během tohoto intervalu.

Konfigurační data, vyhledávací data a další data, která aplikace často používá, jsou vhodné kandidáty pro ukládání do mezipaměti.  

Další informace o použití podmíněných záhlaví naleznete [v tématu Určení podmíněných záhlaví pro operace služby objektů Blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Nahrávání dat v dávkách

V některých případech můžete agregovat data místně a pak je pravidelně nahrávat v dávce namísto okamžitého nahrávání jednotlivých dat. Předpokládejme například, že webová aplikace uchovává soubor protokolu aktivit. Aplikace může buď nahrát podrobnosti o každé aktivitě, jak se to stane do tabulky (což vyžaduje mnoho operací úložiště), nebo může uložit podrobnosti o aktivitě do místního souboru protokolu a pak pravidelně odesílat všechny podrobnosti o aktivitě jako oddělený soubor do objektu blob. Pokud má každá položka protokolu velikost 1 KB, můžete v jedné transakci nahrát tisíce položek. Jedna transakce podporuje nahrávání objektu BLOB o velikosti až 64 MiB. Vývojář aplikace musí navrhnout možnost selhání klientského zařízení nebo nahrávání. Pokud je potřeba data aktivity stáhnout na časový interval, nikoli pro jednu aktivitu, doporučuje se používat úložiště objektů Blob přes úložiště table.

## <a name="net-configuration"></a>Konfigurace rozhraní .NET

Pokud používáte rozhraní .NET Framework, tato část obsahuje několik nastavení rychlé konfigurace, které můžete použít k významnému zlepšení výkonu.  Pokud používáte jiné jazyky, zkontrolujte, zda podobné koncepty platí ve zvoleném jazyce.  

### <a name="use-net-core"></a>Použití jádra rozhraní .NET

Vyvíjejte aplikace azure storage s rozhraním .NET Core 2.1 nebo novějším a využijte výhod vylepšení výkonu. Pokud je to možné, doporučujeme použít rozhraní .NET Core 3.x.

Další informace o vylepšení výkonu v .NET Core najdete v následujících příspěvcích blogu:

- [Vylepšení výkonu v rozhraní .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Vylepšení výkonu v .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Zvýšení výchozího limitu připojení

V rozhraní .NET následující kód zvyšuje výchozí limit připojení (což je obvykle dva v klientském prostředí nebo deset v prostředí serveru) na 100. Obvykle byste měli nastavit hodnotu přibližně počet podprocesů používaných vaší aplikací. Před otevřením připojení nastavte limit připojení.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Další programovací jazyky naleznete v dokumentaci k určení, jak nastavit limit připojení.  

Další informace naleznete v příspěvku blogu [Web Services: Souběžná připojení](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Zvýšení minimálního počtu vláken

Pokud používáte synchronní volání společně s asynchronními úlohami, můžete zvýšit počet vláken ve fondu vláken:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Další informace naleznete [threadpool.setminthreads](/dotnet/api/system.threading.threadpool.setminthreads) metoda.  

## <a name="unbounded-parallelism"></a>Neohraničený paralelismus

Zatímco paralelismus může být skvělé pro výkon, buďte opatrní při použití neohraničené paralelismu, což znamená, že neexistuje žádné omezení vynuceno na počet podprocesů nebo paralelní požadavky. Nezapomeňte omezit paralelní požadavky na nahrávání nebo stahování dat, přístup k více oddílů ve stejném účtu úložiště nebo přístup k více položek ve stejném oddílu. Pokud je paralelismus neomezený, vaše aplikace může překročit možnosti klientského zařízení nebo cíle škálovatelnosti účtu úložiště, což vede k delší latenci a omezení.  

## <a name="client-libraries-and-tools"></a>Klientské knihovny a nástroje

Pro dosažení nejlepšího výkonu vždy používejte nejnovější klientské knihovny a nástroje poskytované společností Microsoft. Klientské knihovny Azure Storage jsou k dispozici pro různé jazyky. Azure Storage taky podporuje PowerShell a Azure CLI. Společnost Microsoft aktivně vyvíjí tyto klientské knihovny a nástroje s ohledem na výkon, udržuje je v aktuálním stavu s nejnovějšími verzemi služeb a zajišťuje, že interně zpracovávají mnoho osvědčených postupů výkonu. Další informace najdete v [referenční dokumentaci k úložišti Azure](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Zpracování chyb služby

Azure Storage vrátí chybu, když služba nemůže zpracovat požadavek. Pochopení chyb, které mohou být vráceny službou Azure Storage v daném scénáři, je užitečné pro optimalizaci výkonu.

### <a name="timeout-and-server-busy-errors"></a>Chyby časového oběhu a zaneprázdnění serveru

Azure Storage může omezit vaši aplikaci, pokud se blíží omezení škálovatelnosti. V některých případech Azure Storage nemusí být schopen zpracovat požadavek z důvodu některých přechodný stav. V obou případech může služba vrátit chybu 503 (Server Busy) nebo 500 (Timeout). Tyto chyby může dojít také v případě, že služba je vyvážení datových oddílů, aby vyšší propustnost. Klientská aplikace by měla obvykle opakovat operaci, která způsobí jednu z těchto chyb. Pokud však Azure Storage je omezení vaší aplikace, protože překračuje cíle škálovatelnosti, nebo i v případě, že služba nebyla schopna obsluhovat požadavek z nějakého jiného důvodu, agresivní opakování může problém zhoršit. Pomocí exponenciální zpět vypnout zásady opakování se doporučuje a klientské knihovny výchozí toto chování. Aplikace může například opakovat po 2 sekundách, pak 4 sekundách, pak 10 sekundách, pak 30 sekundách a pak se úplně vzdát. Tímto způsobem aplikace výrazně snižuje jeho zatížení služby, spíše než zhoršuje chování, které by mohly vést k omezení.  

Chyby připojení lze zopakovat okamžitě, protože nejsou výsledkem omezení a očekává se, že přechodné.  

### <a name="non-retryable-errors"></a>Neopakovatelné chyby

Klientské knihovny zpracovávají opakování s vědomím, které chyby lze opakovat a které nelze. Pokud však voláte rozhraní API azure storage REST, existují některé chyby, které byste neměli opakovat. Například chyba 400 (Chybný požadavek) označuje, že klientská aplikace odeslala požadavek, který nemohl být zpracován, protože nebyl v očekávané podobě. Opětovné odeslání tohoto požadavku výsledky stejnou odpověď pokaždé, takže nemá smysl při jeho opakování. Pokud voláte rozhraní API azure storage REST api přímo, uvědomte si potenciální chyby a zda by měly být opakovány.

Další informace o chybových kódech Služby Azure Storage najdete v [tématu Stavové a chybové kódy](/rest/api/storageservices/status-and-error-codes2).

## <a name="copying-and-moving-blobs"></a>Kopírování a přesouvání objektů BLOB

Azure Storage poskytuje řadu řešení pro kopírování a přesouvání objektů BLOB v rámci účtu úložiště, mezi účty úložiště a mezi místními systémy a cloudem. Tato část popisuje některé z těchto možností z hlediska jejich vliv na výkon. Informace o efektivním přenosu dat do úložiště objektů Blob nebo z nich najdete v [tématu Volba řešení Azure pro přenos dat](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>Api pro kopírování objektů Blob

Chcete-li kopírovat objekty BLOB napříč účty úložiště, použijte operaci [Adresa URL Put Block From.](/rest/api/storageservices/put-block-from-url) Tato operace synchronně kopíruje data z libovolného zdroje adresy URL do objektu blob bloku. Použití `Put Block from URL` operace může výrazně snížit požadovanou šířku pásma při migraci dat mezi účty úložiště. Vzhledem k tomu, že operace kopírování probíhá na straně služby, není nutné stahovat a znovu nahrávat data.

Chcete-li kopírovat data v rámci stejného účtu úložiště, použijte operaci [kopírování objektů blob.](/rest/api/storageservices/Copy-Blob) Kopírování dat v rámci stejného účtu úložiště se obvykle dokončí rychle.  

### <a name="use-azcopy"></a>Použití AzCopy

Nástroj příkazového řádku AzCopy je jednoduchá a efektivní možnost hromadného přenosu objektů BLOB do, z a mezi účty úložiště. AzCopy je optimalizován pro tento scénář a může dosáhnout vysokých přenosových rychlostí. AzCopy verze 10 `Put Block From URL` používá operaci ke kopírování dat objektů blob napříč účty úložiště. Další informace najdete v [tématu Kopírování nebo přesunutí dat do Služby Azure Pomocí AzCopy v10](/azure/storage/common/storage-use-azcopy-v10).  

### <a name="use-azure-data-box"></a>Použití datové schránky Azure

Při importu velkých objemů dat do úložiště objektů Blob zvažte použití řady Azure Data Box pro offline přenosy. Zařízení Data Box dodaná microsoftem jsou dobrou volbou pro přesun velkého množství dat do Azure, když jste omezeni časem, dostupností sítě nebo náklady. Další informace najdete v [dokumentaci k datovému boxu Azure](/azure/databox/).

## <a name="content-distribution"></a>Distribuce obsahu

Někdy aplikace musí sloužit stejný obsah pro mnoho uživatelů (například ukázkové video produktu používané na domovské stránce webu), které se nachází ve stejné nebo více oblastech. V tomto scénáři použijte síť pro doručování obsahu (CDN), jako je Azure CDN k distribuci obsahu objektů blob geograficky. Na rozdíl od účtu Azure Storage, který existuje v jedné oblasti a který nemůže poskytovat obsah s nízkou latencí do jiných oblastí, Azure CDN používá servery ve více datových centrech po celém světě. Kromě toho cdn obvykle podporuje mnohem vyšší omezení odchozího přenosu než jeden účet úložiště.  

Další informace o Azure CDN najdete v [tématu Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Použití metadat

Služba objektu blob podporuje požadavky HEAD, které mohou obsahovat vlastnosti objektu blob nebo metadata. Pokud například vaše aplikace potřebuje data Exif (vyměnitelný formát obrázku) z fotografie, může fotografii načíst a extrahovat. Chcete-li ušetřit šířku pásma a zvýšit výkon, aplikace můžete uložit data Exif v metadatech objektu blob při odeslání fotografie. Potom můžete načíst data Exif v metadatech pouze pomocí požadavku HEAD. Načítání jenom metadat a ne úplného obsahu objektu blob šetří významnou šířku pásma a zkracuje dobu zpracování potřebnou k extrahování dat Exif. Mějte na paměti, že 8 KiB metadat a lze uložit na objekt blob.  

## <a name="upload-blobs-quickly"></a>Rychlé nahrávání objektů BLOB

Chcete-li rychle nahrávat objekty BLOB, nejprve zjistěte, zda budete nahrávat jeden nebo více objektů blob. Pomocí níže uvedených pokynů určete správnou metodu, která se má použít v závislosti na vašem scénáři.  

### <a name="upload-one-large-blob-quickly"></a>Rychlé nahrání jedné velké kapky

Chcete-li rychle nahrát jeden velký objekt blob, klientská aplikace můžete nahrát své bloky nebo stránky paralelně, s ohledem na škálovatelnost cíle pro jednotlivé objekty BLOB a účet úložiště jako celek. Klientské knihovny Azure Storage podporují paralelní nahrávání. Následující vlastnosti můžete například použít k určení počtu souběžných požadavků povolených v rozhraní .NET nebo Java. Klientské knihovny pro jiné podporované jazyky poskytují podobné možnosti.

- Pro rozhraní .NET nastavte vlastnost [BlobRequestOptions.ParallelOperationThreadCount.](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)
- Pro Jazyk Java/Android volejte metodu [BlobRequestOptions.setConcurrentRequestCount(final Integer concurrentRequestCount).](/java/api/com.microsoft.azure.storage.blob._blob_request_options.setconcurrentrequestcount)

### <a name="upload-many-blobs-quickly"></a>Rychlé nahrání mnoha objektů BLOB

Chcete-li rychle nahrát mnoho objektů BLOB, nahrajte objekty BLOB paralelně. Paralelní nahrávání je rychlejší než nahrávání jednotlivých objektů BLOB najednou s paralelním nahráváním bloků, protože se šíří nahrávání mezi více oddíly služby úložiště. AzCopy provádí nahrávání paralelně ve výchozím nastavení a je doporučeno pro tento scénář. Další informace naleznete [v tématu Začínáme s AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Výběr správného typu objektu blob

Azure Storage podporuje objekty BLOB bloku, připojit objekty BLOB a objekty BLOB stránky. Pro daný scénář použití váš výběr typu objektu blob ovlivní výkon a škálovatelnost vašeho řešení.

Objekty BLOB bloku jsou vhodné, pokud chcete efektivně nahrávat velké objemy dat. Například klientská aplikace, která nahrává fotky nebo video do úložiště objektů Blob, by se zaměřila na objekty BLOB bloku.

Připojit objekty BLOB jsou podobné objekty BLOB bloku v tom, že se skládají z bloků. Když upravíte objekt blob připojení, bloky se přidají pouze na konec objektu blob. Připojit objekty BLOB jsou užitečné pro scénáře, jako je protokolování, když aplikace potřebuje přidat data do existujícího objektu blob.

Objekty BLOB stránky jsou vhodné, pokud aplikace potřebuje provádět náhodné zápisy na data. Například disky virtuálních počítačů Azure jsou uloženy jako objekty BLOB stránky. Další informace naleznete [v tématu Principy objektů BLOB bloků, objektů BLOBloH a objektů BLOB stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Další kroky

- [Škálovatelnost a výkonnostní cíle pro úložiště objektů Blob](scalability-targets.md)
- [Škálovatelnost a výkonnostní cíle pro účty standardního úložiště](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Stavové a chybové kódy](/rest/api/storageservices/Status-and-Error-Codes2)
