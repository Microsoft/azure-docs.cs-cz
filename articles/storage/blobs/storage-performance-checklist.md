---
title: Kontrolní seznam pro výkon a škálovatelnost pro úložiště objektů blob – Azure Storage
description: Kontrolní seznam osvědčených postupů pro použití s úložištěm objektů BLOB při vývoji vysoce výkonných aplikací.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 14da8b6cb695703f1881b6b0b9858772bde386c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544747"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Kontrolní seznam pro výkon a škálovatelnost pro úložiště objektů BLOB

Společnost Microsoft vyvinula řadu osvědčených postupů pro vývoj vysoce výkonných aplikací s využitím úložiště objektů BLOB. Tento kontrolní seznam popisuje klíčové postupy, které můžou vývojáři použít k optimalizaci výkonu. Při navrhování aplikace a celého procesu mějte na paměti tyto postupy.

Azure Storage má cíle škálovatelnosti a výkonu pro kapacitu, rychlost transakcí a šířku pásma. Další informace o cílech Azure Storage škálovatelnosti najdete v tématu [škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) a [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](scalability-targets.md).

## <a name="checklist"></a>Kontrolní seznam

Tento článek organizuje osvědčené postupy pro výkon do kontrolního seznamu, který můžete sledovat při vývoji aplikace BLOB Storage.

| Hotovo | Kategorie | Aspekt návrhu |
| --- | --- | --- |
| &nbsp; |Cíle škálovatelnosti |[Můžete navrhnout aplikaci tak, aby nepoužívala více než maximální počet účtů úložiště?](#maximum-number-of-storage-accounts) |
| &nbsp; |Cíle škálovatelnosti |[Nechcete se vyhnout přístupu k kapacitě a omezením transakcí?](#capacity-and-transaction-targets) |
| &nbsp; |Cíle škálovatelnosti |[Má souběžný přístup k jedinému objektu BLOB velký počet klientů?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Cíle škálovatelnosti |[Zůstává vaše aplikace v rámci cílů škálovatelnosti pro jeden objekt BLOB?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Dělení |[Je vaše konvence vytváření názvů navržená tak, aby umožňovala lepší vyrovnávání zatížení?](#partitioning) |
| &nbsp; |Sítě |[Mají zařízení na straně klienta dostatečně velkou šířku pásma a nízkou latenci pro dosažení potřebného výkonu?](#throughput) |
| &nbsp; |Sítě |[Má zařízení na straně klienta vysoce kvalitní síťový odkaz?](#link-quality) |
| &nbsp; |Sítě |[Je klientská aplikace ve stejné oblasti jako účet úložiště?](#location) |
| &nbsp; |Přímý přístup klienta |[Používáte k tomu přímý přístup k Azure Storage pomocí sdílených přístupových podpisů (SAS) a sdílení prostředků mezi zdroji (CORS)?](#sas-and-cors) |
| &nbsp; |Ukládání do mezipaměti |[Ukládá vaše aplikace data, ke kterým dochází často a zřídka se mění?](#reading-data) |
| &nbsp; |Ukládání do mezipaměti |[Provádí vaše aplikace dávkové aktualizace jejich ukládáním do mezipaměti v klientovi a jejich nahráním do větších sad?](#uploading-data-in-batches) |
| &nbsp; |Konfigurace .NET |[Používáte pro optimální výkon .NET Core 2,1 nebo novější?](#use-net-core) |
| &nbsp; |Konfigurace .NET |[Nakonfigurovali jste klienta tak, aby používal dostatečný počet souběžných připojení?](#increase-default-connection-limit) |
| &nbsp; |Konfigurace .NET |[Pro aplikace .NET jste nakonfigurovali .NET pro použití dostatečného počtu vláken?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismu |[Měli byste zajistit, aby byl paralelismu správně ohraničený, takže nebudete přetěžovat možnosti svého klienta nebo se přiblížíte cílům škálovatelnosti?](#unbounded-parallelism) |
| &nbsp; |nástroje |[Používáte nejnovější verze klientských knihoven a nástrojů od společnosti Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Opakování |[Používáte zásady opakování s exponenciálním omezení rychlosti pro omezení chyb a časových limitů?](#timeout-and-server-busy-errors) |
| &nbsp; |Opakování |[Vyloučí vaše aplikace opakované pokusy o neopakující se chyby?](#non-retryable-errors) |
| &nbsp; |Kopírování objektů BLOB |[Kopírujete objekty blob nejúčinnějším způsobem?](#blob-copy-apis) |
| &nbsp; |Kopírování objektů BLOB |[Používáte pro hromadné kopírování nejnovější verzi AzCopy?](#use-azcopy) |
| &nbsp; |Kopírování objektů BLOB |[Používáte pro import velkých objemů dat Azure Data Box rodinu?](#use-azure-data-box) |
| &nbsp; |Distribuce obsahu |[Používáte pro distribuci obsahu síť CDN?](#content-distribution) |
| &nbsp; |Použití metadat |[Ukládáte často používaná metadata o objektech blob v jejich metadatech?](#use-metadata) |
| &nbsp; |Rychlé nahrávání |[Při rychlém nahrání jednoho objektu BLOB se bloky nahrávají paralelně?](#upload-one-large-blob-quickly) |
| &nbsp; |Rychlé nahrávání |[Při rychlém nahrávání mnoha objektů BLOB se nahrávají objekty blob paralelně?](#upload-many-blobs-quickly) |
| &nbsp; |Typ objektu blob |[Používáte objekty blob stránky nebo objekty blob bloku, pokud je to vhodné?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Cíle škálovatelnosti

Pokud vaše aplikace přistupuje k některým cílům škálovatelnosti nebo překročí, může dojít ke zvýšené latenci transakcí nebo omezování. Když aplikace Azure Storage omezí vaši aplikaci, začne služba vracet kódy chyb 503 (zaneprázdněný serverem) nebo 500 (časový limit operace). Zamezení těchto chyb zachováním omezení cílů škálovatelnosti je důležitou součástí zvýšení výkonu aplikace.

Další informace o cílech škálovatelnosti pro Služba front najdete v tématu [Azure Storage cíle škálovatelnosti a výkonu](../queues/scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maximální počet účtů úložiště

Pokud se přiblížíte k maximálnímu počtu účtů úložiště povolených pro určitou kombinaci předplatného nebo oblasti, vyhodnoťte svůj scénář a určete, jestli platí některá z následujících podmínek:

- Používáte účty úložiště k ukládání nespravovaných disků a přidávání těchto disků do virtuálních počítačů (VM)? V tomto scénáři Microsoft doporučuje používat spravované disky. Spravované disky se škálují automaticky a bez nutnosti vytvářet a spravovat jednotlivé účty úložiště. Další informace najdete v tématu [Úvod do služby Azure Managed disks](../../virtual-machines/managed-disks-overview.md) .
- Používáte pro účely izolace dat jeden účet úložiště pro každého zákazníka? V tomto scénáři Microsoft doporučuje použít pro každého zákazníka kontejner objektů BLOB namísto celého účtu úložiště. Azure Storage teď umožňuje přiřadit role Azure na základě jednotlivých kontejnerů. Další informace najdete v tématu [použití Azure Portal k přiřazení role Azure pro přístup k datům objektů BLOB a front](../common/storage-auth-aad-rbac-portal.md).
- Používáte k horizontálních oddílů více účtů úložiště k navýšení příchozích, odchozích a vstupně-výstupních operací za sekundu (IOPS) nebo kapacity? V tomto scénáři Microsoft doporučuje, abyste využili vyšší omezení pro účty úložiště, abyste snížili počet účtů úložiště potřebných pro vaše zatížení, pokud je to možné. Kontaktujte [podporu Azure](https://azure.microsoft.com/support/options/) a požádejte o zvýšená omezení pro váš účet úložiště. Další informace najdete v tématu [oznamujeme větší, vyšší škálování účtů úložiště](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Cíle kapacity a transakce

Pokud se vaše aplikace blíží cílům škálovatelnosti pro jeden účet úložiště, zvažte použití jednoho z následujících přístupů:  

- Pokud vaše aplikace narazí na cíl transakce, zvažte použití účtů úložiště blob bloku, které jsou optimalizovány pro vysoké sazby transakcí a nízké a konzistentní latence. Další informace najdete v tématu [Přehled účtu Azure Storage](../common/storage-account-overview.md).
- Převezměte v úvahu zatížení, které způsobí, že vaše aplikace bude přistupovat k cíli škálovatelnosti nebo nad něj. Můžete ji navrhovat odlišně, abyste mohli používat menší šířku pásma nebo kapacitu nebo méně transakcí?
- Pokud vaše aplikace musí překročit jeden z cílů škálovatelnosti, vytvořte několik účtů úložiště a vytvořte oddíly dat aplikací napříč těmito různými účty úložiště. Pokud použijete tento model, nezapomeňte navrhnout aplikaci, abyste mohli v budoucnu přidat další účty úložiště pro vyrovnávání zatížení. Účty úložiště samy nemají žádné náklady jiné než vaše využití v závislosti na uložených datech, provedených transakcích nebo přenesených datech.
- Pokud se vaše aplikace blíží cílům šířky pásma, zvažte komprimaci dat na straně klienta, aby se snížila šířka pásma nutná k odeslání dat do Azure Storage.
    I když komprimace dat může ušetřit šířku pásma a zvýšit výkon sítě, může mít také negativní vliv na výkon. Vyhodnoťte dopad dalších požadavků na zpracování pro kompresi a dekompresi dat na straně klienta. Mějte na paměti, že ukládání komprimovaných dat může řešit obtíže, protože může být náročnější na zobrazení dat pomocí standardních nástrojů.
- Pokud se vaše aplikace blíží cílům škálovatelnosti, ujistěte se, že používáte exponenciální omezení rychlosti pro opakované pokusy. Doporučujeme, abyste se vyhnuli dosažení cílů škálovatelnosti, a to implementací doporučení popsaných v tomto článku. Použití exponenciálního omezení rychlosti pro opakování však zabrání aplikaci v rychlém opakování, což by mohlo způsobit horší omezení. Další informace najdete v části s názvem [timeout a chyby zaneprázdněnosti serveru](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Současně více klientů přistupujících k jednomu objektu BLOB

Pokud máte velký počet klientů, kteří mají současně přístup k jednomu objektu blob, budete muset vzít v úvahu cíle škálovatelnosti na jeden objekt BLOB i na jeden účet úložiště. Přesný počet klientů, kteří mají přístup k jednomu objektu blob, se liší v závislosti na faktorech, jako je počet klientů žádajících o objekt BLOB současně, velikost objektu BLOB a podmínky sítě.

Pokud je možné objekt BLOB distribuovat prostřednictvím sítě CDN, jako jsou obrázky nebo videa poskytované z webu, můžete použít CDN. Další informace najdete v části s názvem [distribuce obsahu](#content-distribution).

V jiných scénářích, jako jsou například vědecké simulace, kde jsou data tajná, máte dvě možnosti. První z nich je rozložit přístup k vašim úlohám tak, aby se k objektu BLOB získal přístup v době, kdy k němu dojde současně. Alternativně můžete objekt BLOB dočasně zkopírovat do více účtů úložiště a zvýšit tak celkový počet vstupně-výstupních operací na jeden objekt BLOB a napříč účty úložiště. Výsledky se budou lišit v závislosti na chování vaší aplikace, proto nezapomeňte testovat vzory souběžnosti během návrhu.

### <a name="bandwidth-and-operations-per-blob"></a>Šířka pásma a operace na objekt BLOB

Jeden objekt BLOB podporuje až 500 požadavků za sekundu. Pokud máte několik klientů, kteří potřebují číst stejný objekt BLOB a tento limit můžete překročit, zvažte použití účtu bloku BLOB Storage. Účet úložiště objektů blob bloku nabízí vyšší míru požadavků nebo vstupně-výstupní operace za sekundu (IOPS).

K distribuci operací do objektu blob můžete také použít síť pro doručování obsahu (CDN), například Azure CDN. Další informace o Azure CDN najdete v tématu [Azure CDN Overview](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Dělení

Porozumět způsobu, jakým Azure Storage oddíly dat objektů BLOB jsou užitečné pro zvýšení výkonu. Azure Storage může obsluhovat data v jednom oddílu rychleji než data, která jsou rozložená na více oddílů. Pojmenováním objektů blob můžete zlepšit efektivitu požadavků na čtení.

Úložiště objektů BLOB používá schéma dělení na základě rozsahu pro škálování a vyrovnávání zatížení. Každý objekt BLOB má klíč oddílu tvořený úplným názvem objektu BLOB (Account + Container + BLOB). Klíč oddílu se používá k rozdělení dat objektů blob do rozsahů. Rozsahy se pak vyrovnávají zatížením napříč úložištěm objektů BLOB.

Dělení na základě rozsahu znamená, že konvence pojmenování, které používají lexikální řazení (například *mypayroll*, *myperformance*, *myemployees* atd.) nebo časová razítka (*log20160101*, *log20160102*, *log20160102* atd.), jsou pravděpodobnější v tom, že se oddíly společně nacházejí na stejném serveru oddílu. , dokud zvýšené zatížení nevyžaduje, aby byly rozděleny do menších rozsahů. Společné umísťování objektů blob na stejném oddílu serveru vylepšuje výkon, takže důležitou součástí zvýšení výkonu je, že se objekty blob pojmenují způsobem, který je bude efektivně organizovat.

Například všechny objekty BLOB v kontejneru může obsluhovat jeden server, dokud zatížení těchto objektů BLOB nevyžaduje další nové vyrovnávání rozsahů oddílů. Podobně skupina lehce načtených účtů, jejichž názvy jsou uspořádány v lexikálním pořadí, mohou být obsluhovány jediným serverem, dokud zatížení jednoho nebo všech těchto účtů nevyžaduje, aby byly rozděleny mezi servery s více oddíly.

Každá operace vyrovnávání zatížení může mít vliv na latenci volání úložiště během operace. Schopnost obsluhy náhlého nárůstu provozu do oddílu je omezená škálovatelností serveru s jedním oddílem, dokud se operace vyrovnávání zatížení neuvolní a znovu vyrovnává rozsah klíčů oddílu.

Můžete postupovat podle určitých osvědčených postupů a snížit tak četnost těchto operací.  

- Pokud je to možné, používejte velikost objektů BLOB nebo bloků větší než 4 MiB pro účty úložiště Standard a vyšší než 256 KiB pro účty Premium Storage. Větší objekty blob nebo blokové velikosti automaticky aktivují objekty blob bloku s vysokou propustností. Objekty blob bloku s vysokou propustností poskytují vysoce výkonné ingestum, které není ovlivněné pojmenovávání oddílů.
- Projděte si zásadu vytváření názvů, kterou používáte pro účty, kontejnery, objekty blob, tabulky a fronty. Pomocí funkce hash, která nejlépe vyhovuje vašim potřebám, zvažte použití předpony názvů účtů, kontejnerů nebo objektů BLOB se třemi číslicemi.
- Pokud vaše data organizujete pomocí časových razítek nebo číselných identifikátorů, ujistěte se, že nepoužíváte vzor přenosů pouze pro připojení (nebo pouze k předplatnému). Tyto vzory nejsou vhodné pro systém dělení na základě rozsahu. Tyto vzory mohou vést k veškerému provozu v jednom oddílu a omezit systém z efektivního vyrovnávání zatížení.

    Například pokud máte každodenní operace, které používají objekt BLOB s časovým razítkem, jako je například *RRRRMMDD*, pak veškerý provoz pro tuto každodenní operaci bude směrován do jediného objektu blob, který je obsluhován serverem jediného oddílu. Vezměte v úvahu, zda limity pro objekty BLOB a omezení pro jednotlivé oddíly vyhovují vašim potřebám, a v případě potřeby zvažte přerušení této operace do více objektů BLOB. Podobně platí, že pokud ukládáte data časových řad do tabulek, veškerý provoz může být směrován do poslední části oboru názvů Key. Pokud používáte numerická ID, použijte předponu ID s hodnotou hash se třemi číslicemi. Pokud používáte časová razítka, zadáte předponu časového razítka s hodnotou sekund, například *ssyyyymmdd*. Pokud aplikace rutinně provede výpis a dotazování na operace, vyberte funkci hash, která omezí počet dotazů. V některých případech může stačit náhodná předpona.
  
- Další informace o schématu dělení používaného v Azure Storage najdete v článku [Azure Storage: vysoce dostupná služba cloudového úložiště s silnou konzistencí](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

## <a name="networking"></a>Sítě

Omezení fyzické sítě aplikace mohou mít významný dopad na výkon. Následující části popisují některá omezení, která se mohou vyskytnout pro uživatele.  

### <a name="client-network-capability"></a>Schopnost klientské sítě

Šířka pásma a kvalita síťového propojení hrají důležité role v výkonu aplikace, jak je popsáno v následujících částech.

#### <a name="throughput"></a>Propustnost

V případě šířky pásma je problém často funkcemi klienta. Větší instance Azure mají síťové karty s větší kapacitou, takže byste měli zvážit použití větší instance nebo více virtuálních počítačů, pokud potřebujete vyšší omezení sítě z jednoho počítače. Pokud přistupujete Azure Storage z místní aplikace, platí stejné pravidlo: Pochopte síťové možnosti klientského zařízení a připojení k síti pro Azure Storage umístění a buď je Vylepšete podle potřeby, nebo Navrhněte svou aplikaci tak, aby fungovala v jejich schopnostech.

#### <a name="link-quality"></a>Kvalita propojení

Stejně jako u všech využití sítě Pamatujte na to, že síťové podmínky, které mají za následek chyby a ztráty paketů, budou zpomalit efektivní propustnost.  K diagnostice tohoto problému může využít nástroj WireShark nebo NetMon.  

### <a name="location"></a>Umístění

V jakémkoli distribuovaném prostředí je umístění klienta poblíž serveru k dispozici nejlepšího výkonu. Pro přístup k Azure Storage s nejnižší latencí je nejlepší umístění pro vašeho klienta ve stejné oblasti Azure. Například pokud máte webovou aplikaci Azure, která používá Azure Storage, vyhledáte je v rámci jedné oblasti, jako je například USA – západ nebo Asie – jihovýchod. Společné umístění prostředků snižuje latenci a náklady, protože využití šířky pásma v rámci jedné oblasti je zdarma.  

Pokud budou klientské aplikace přistupovat k Azure Storage, ale nejsou hostovány v rámci Azure, jako jsou aplikace pro mobilní zařízení nebo místní podnikové služby, pak hledání účtu úložiště v oblasti blízko těchto klientů může snížit latenci. Pokud jsou vaši klienti široce distribuované (například některé v Severní Amerika a některé v Evropě), zvažte použití jednoho účtu úložiště v každé oblasti. Tento přístup je snazší implementovat, pokud jsou data, která aplikace ukládá, specifická pro jednotlivé uživatele a nevyžadují replikaci dat mezi účty úložiště.

V případě široké distribuce obsahu objektů BLOB použijte síť pro doručování obsahu, například Azure CDN. Další informace o Azure CDN najdete v tématu [Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS a CORS

Předpokládejme, že je nutné autorizovat kód, jako je například JavaScript, který je spuštěn ve webovém prohlížeči uživatele nebo v mobilním telefonu pro přístup k datům v aplikaci Azure Storage. Jednou z možností je vytvořit aplikaci služby, která funguje jako proxy server. Zařízení uživatele se ověřuje pomocí služby, což zase autorizuje přístup k prostředkům Azure Storage. Tímto způsobem se můžete vyhnout vystavení klíčů účtu úložiště na nezabezpečených zařízeních. Tento přístup však přináší značnou režii na aplikaci služby, protože všechna data přenesená mezi zařízením uživatele a Azure Storage musí projít aplikací služby.

Pomocí sdíleného přístupového podpisu (SAS) se můžete vyhnout použití aplikace služby jako proxy serveru pro Azure Storage. Pomocí SAS můžete zařízení uživatele povolit, aby se požadavky přímo na Azure Storage pomocí omezeného přístupového tokenu. Pokud třeba uživatel chce do aplikace nahrát fotografii, aplikace služby může vygenerovat SAS a odeslat ji do zařízení uživatele. Token SAS může udělit oprávnění k zápisu do prostředku Azure Storage v zadaném časovém intervalu, po jehož uplynutí vyprší platnost tokenu SAS. Další informace o SAS najdete v tématu [udělení omezeného přístupu k Azure Storage prostředkům pomocí sdílených přístupových podpisů (SAS)](../common/storage-sas-overview.md).  

Webový prohlížeč obvykle nepovoluje JavaScript na stránce, která je hostována webem v jedné doméně k provádění určitých operací, například operací zápisu, do jiné domény. Tato zásada se označuje jako zásada stejného zdroje, a proto zabraňuje škodlivému skriptu na jedné stránce získat přístup k datům na jiné webové stránce. Zásady stejného původce ale můžou být omezením při sestavování řešení v cloudu. Sdílení prostředků mezi zdroji (CORS) je funkce prohlížeče, která umožňuje cílové doméně komunikovat s prohlížečem, který důvěřuje žádostem pocházejících z zdrojové domény.

Předpokládejme například, že webová aplikace spuštěná v Azure vytvoří požadavek na prostředek na účet Azure Storage. Webová aplikace je zdrojová doména a účet úložiště je cílová doména. CORS můžete nakonfigurovat pro jakoukoli službu Azure Storage Services, která bude komunikovat s webovým prohlížečem, který požadavky ze zdrojové domény důvěřuje Azure Storage. Další informace o CORS najdete v tématu [Podpora sdílení prostředků mezi zdroji (CORS) pro Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
SAS i CORS vám můžou přispět k tomu, abyste se vyhnuli zbytečnému zatížení vaší webové aplikace.  

## <a name="caching"></a>Ukládání do mezipaměti

Ukládání do mezipaměti hraje důležitou roli ve výkonu. Následující části popisují osvědčené postupy pro ukládání do mezipaměti.

### <a name="reading-data"></a>Čtení dat

Obecně platí, že při čtení dat je vhodnější je přečíst dvakrát. Vezměte v úvahu příklad webové aplikace, která získala objekt BLOB 50 MiB z Azure Storage, který slouží jako obsah pro uživatele. V ideálním případě aplikace ukládá objekt BLOB místně na disk a potom načte verzi uloženou v mezipaměti pro následné požadavky uživatele.

Jedním ze způsobů, jak se vyhnout načtení objektu blob, pokud se nezměnilo od doby, kdy byl uložen do mezipaměti, je kvalifikovat operaci GET s podmíněnou hlavičkou pro čas změny. Pokud čas poslední změny následuje po době, kdy byl objekt BLOB uložen do mezipaměti, je objekt BLOB načten a znovu uložen do mezipaměti. V opačném případě je načten objekt BLOB v mezipaměti pro optimální výkon.

Můžete se také rozhodnout navrhnout aplikaci tak, aby se předpokládalo, že objekt BLOB po jeho načtení zůstane po krátké době beze změny. V takovém případě aplikace nemusí kontrolovat, zda byl objekt BLOB během tohoto intervalu změněn.

Data konfigurace, vyhledávací data a další data, která aplikace často používají, jsou vhodnými kandidáty pro ukládání do mezipaměti.  

Další informace o použití podmíněných hlaviček najdete v tématu [určení podmíněných hlaviček pro operace BLOB Service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Nahrávání dat v dávkách

V některých scénářích můžete data agregovat místně a pak je pravidelně nahrávat do dávky místo toho, aby se data nahrála okamžitě. Předpokládejme například, že webová aplikace udržuje soubor protokolu aktivit. Aplikace může nahrát údaje o každé aktivitě, když se stane s tabulkou (která vyžaduje mnoho operací úložiště), nebo může uložit podrobnosti o aktivitě do místního souboru protokolu a pak pravidelně nahrávat všechny podrobnosti o aktivitách jako soubor s oddělovači do objektu BLOB. Pokud je velikost každého záznamu v protokolu 1 KB, můžete nahrát tisíce položek v jedné transakci. Jedna transakce podporuje velikost objektu BLOB až 64 MiB. Vývojář aplikace musí navrhnout, aby bylo možné nasílat selhání klientského zařízení nebo nahrání. Pokud je třeba data aktivity stáhnout pro časový interval, nikoli pro jedinou aktivitu, doporučuje se použít úložiště objektů BLOB pro úložiště tabulek.

## <a name="net-configuration"></a>Konfigurace .NET

Pokud používáte .NET Framework, v této části najdete několik nastavení rychlých konfigurací, pomocí kterých můžete provádět významná vylepšení výkonu.  Pokud používáte jiné jazyky, podívejte se, jestli se ve zvoleném jazyce použijí podobné koncepty.  

### <a name="use-net-core"></a>Použití .NET Core

Vývoj aplikací pro Azure Storage s využitím .NET Core 2,1 nebo novějším vám umožní využít vylepšení výkonu. Pokud je to možné, doporučuje se použít .NET Core 3. x.

Další informace o vylepšení výkonu v .NET Core najdete v následujících blogových příspěvcích:

- [Vylepšení výkonu v .NET Core 3,0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Vylepšení výkonu v .NET Core 2,1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Zvýšit výchozí limit připojení

V rozhraní .NET zvyšuje následující kód výchozí limit připojení (což je obvykle dvě v klientském prostředí nebo desítkě v prostředí serveru) na 100. Obvykle byste měli nastavit hodnotu na přibližně počet vláken používaných vaší aplikací. Před otevřením připojení nastavte limit připojení.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Další programovací jazyky najdete v dokumentaci k určení, jak nastavit limit připojení.  

Další informace najdete v blogovém příspěvku [webové služby: souběžná připojení](/archive/blogs/darrenj/web-services-concurrent-connections).  

### <a name="increase-minimum-number-of-threads"></a>Zvýšení minimálního počtu vláken

Pokud používáte synchronní volání spolu s asynchronními úkoly, můžete zvýšit počet vláken ve fondu vláken:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Další informace naleznete v tématu metoda [fondu vláken. SetMinThreads –](/dotnet/api/system.threading.threadpool.setminthreads) .  

## <a name="unbounded-parallelism"></a>Neohraničené paralelismus

I když paralelismus může být ideální pro výkon, buďte opatrní v používání neohraničené paralelismu, což znamená, že není nijak omezen počet vláken nebo paralelních požadavků. Nezapomeňte omezit paralelní požadavky na nahrávání nebo stahování dat, pro přístup k více oddílům ve stejném účtu úložiště nebo pro přístup k několika položkám ve stejném oddílu. Pokud je paralelismus neohraničený, může vaše aplikace přesáhnout možnosti klientského zařízení nebo cíle škálovatelnosti účtu úložiště, což vede k delší latenci a omezování.  

## <a name="client-libraries-and-tools"></a>Klientské knihovny a nástroje

Nejlepšího výkonu dosáhnete, když budete vždycky používat nejnovější klientské knihovny a nástroje poskytované Microsoftem. Azure Storage klientské knihovny jsou k dispozici pro nejrůznější jazyky. Azure Storage podporuje taky PowerShell a Azure CLI. Microsoft aktivně vyvíjí tyto klientské knihovny a nástroje s ohledem na výkon, udržuje je aktuální s nejnovějšími verzemi služby a zajišťuje interní zpracování mnoha osvědčených postupů výkonu.

## <a name="handle-service-errors"></a>Zpracování chyb služby

Azure Storage vrátí chybu, pokud služba nemůže zpracovat požadavek. Porozumění chybám, které mohou být vráceny Azure Storage v daném scénáři, je užitečné pro optimalizaci výkonu.

### <a name="timeout-and-server-busy-errors"></a>Vypršení časového limitu a chyb zaneprázdněnosti serveru

Azure Storage může aplikaci omezit, pokud se blíží omezením škálovatelnosti. V některých případech nemusí Azure Storage být schopen zpracovat požadavek z důvodu přechodné podmínky. V obou případech může služba vracet chybu 503 (zaneprázdněný serverem) nebo 500 (časový limit). K těmto chybám může dojít také v případě, že je služba vyrovnávající datové oddíly, aby bylo možné dosáhnout vyšší propustnosti. Klientská aplikace by obvykle měla operaci zopakovat, což způsobí jednu z těchto chyb. Pokud však Azure Storage omezuje vaši aplikaci, protože je nad rámec škálovatelnosti, nebo i v případě, že služba nemohla tuto žádost z nějakého důvodu nedokázala zpracovat, agresivní pokusy mohou problém vyřešit. Doporučuje se použít exponenciální back-mailové zásady opakování, přičemž klientské knihovny jsou pro toto chování výchozí. Vaše aplikace se může například opakovat po 2 sekundách, potom 4 sekundy, 10 sekund, 30 sekund a potom zcela. Tímto způsobem vaše aplikace významně snižuje zatížení služby, spíše než exacerbating chování, které by mohlo vést k omezení.  

Chyby připojení se dají opakovat okamžitě, protože nejsou výsledkem omezení a očekává se, že budou přechodné.  

### <a name="non-retryable-errors"></a>Neopakující se chyby

Knihovny klienta zpracovává opakované pokusy s vědomím, které chyby lze opakovat a které nemohou. Nicméně pokud voláte Azure Storage REST API přímo, dojde k chybám, které byste neměli opakovat. Například Chyba 400 (chybný požadavek) indikuje, že klientská aplikace odeslala požadavek, který nebylo možné zpracovat, protože nebyl v očekávané podobě. Při opakovaném odeslání této žádosti se pokaždé pošle stejná odpověď, takže nedojde k žádnému bodu v dalším pokusu. Pokud voláte Azure Storage REST API přímo, pamatujte na potenciální chyby a určete, zda se mají opakovat.

Další informace o kódech chyb Azure Storage naleznete v tématu [stav a chybové kódy](/rest/api/storageservices/status-and-error-codes2).

## <a name="copying-and-moving-blobs"></a>Kopírování a přesouvání objektů BLOB

Azure Storage poskytuje řadu řešení pro kopírování a přesouvání objektů BLOB v rámci účtu úložiště, mezi účty úložiště a mezi místními systémy a cloudem. Tato část popisuje některé z těchto možností z pohledu jejich dopadu na výkon. Informace o efektivním přenosu dat do úložiště objektů BLOB nebo z něj najdete v tématu [Volba řešení Azure pro přenos dat](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>Rozhraní API pro kopírování objektů BLOB

K kopírování objektů BLOB mezi účty úložiště použijte operaci [vložení bloku z adresy URL](/rest/api/storageservices/put-block-from-url) . Tato operace kopíruje data synchronně z libovolného zdroje adresy URL do objektu blob bloku. `Put Block from URL`Při migraci dat mezi účty úložiště může použití operace významně snížit požadovanou šířku pásma. Vzhledem k tomu, že operace kopírování probíhá na straně služby, není nutné data stahovat a znovu nahrávat.

Pokud chcete kopírovat data v rámci stejného účtu úložiště, použijte operaci [Kopírovat objekt BLOB](/rest/api/storageservices/Copy-Blob) . Kopírování dat v rámci stejného účtu úložiště se obvykle dokončí rychle.  

### <a name="use-azcopy"></a>Použití AzCopy

Nástroj příkazového řádku AzCopy je jednoduchá a efektivní možnost pro hromadné přenosy objektů blob do, z a napříč účty úložiště. AzCopy je pro tento scénář optimalizované a může dosáhnout vysoké míry přenosů. AzCopy verze 10 používá `Put Block From URL` operaci ke kopírování dat objektů BLOB napříč účty úložiště. Další informace najdete v tématu [kopírování nebo přesun dat do Azure Storage pomocí nástroje AzCopy v10 za účelem](../common/storage-use-azcopy-v10.md).  

### <a name="use-azure-data-box"></a>Použít Azure Data Box

Pokud chcete importovat velké objemy dat do úložiště objektů blob, zvažte použití řady Azure Data Box pro offline přenosy. Zařízení Data Box poskytnutá společností Microsoft jsou vhodnou volbou pro přesun velkých objemů dat do Azure, pokud jste omezeni časem, dostupností sítě nebo náklady. Další informace najdete v dokumentaci ke [službě Azure Databox](../../databox/index.yml).

## <a name="content-distribution"></a>Distribuce obsahu

V některých případech aplikace potřebuje stejný obsah pro mnoho uživatelů (například ukázkové video, které se používá na domovské stránce webu), umístěného ve stejné nebo několika oblastech. V tomto scénáři použijte Content Delivery Network (CDN), jako je například Azure CDN, a geograficky distribuujte obsah objektu BLOB. Na rozdíl od Azure Storage účtu, který existuje v jedné oblasti a který nemůže doručovat obsah s nízkou latencí do jiných oblastí, Azure CDN používá servery v několika datových centrech po celém světě. CDN navíc může obvykle podporovat mnohem vyšší omezení pro odchozí přenosy než jeden účet úložiště.  

Další informace o Azure CDN najdete v tématu [Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Použití metadat

Blob service podporuje žádosti o hlavu, které mohou zahrnovat vlastnosti nebo metadata objektu BLOB. Například pokud vaše aplikace potřebuje data formátu EXIF (exchangable Image Format) z fotografie, může ji načíst a extrahovat. Chcete-li ušetřit šířku pásma a zvýšit výkon, aplikace může ukládat data Exif v metadatech objektu blob, když aplikace nahraje fotografii. Pak můžete načíst data Exif v metadatech pouze pomocí žádosti HEAD. Načítání pouze metadat a neúplný obsah objektu BLOB šetří značnou šířku pásma a zkracuje dobu zpracování potřebnou k extrakci dat EXIF. Pamatujte, že 8 KiB metadat je možné uložit na objekt BLOB.  

## <a name="upload-blobs-quickly"></a>Rychlé nahrání objektů BLOB

Chcete-li rychle nahrát objekty blob, nejprve určete, zda budete nahrávat jeden objekt BLOB nebo mnoho. Použijte níže uvedené pokyny k určení správné metody, která se má použít v závislosti na vašem scénáři.  

### <a name="upload-one-large-blob-quickly"></a>Rychlé nahrání jednoho velkého objektu BLOB

Aby mohla klientská aplikace rychle nahrát jeden rozsáhlý objekt blob, může nahrávat své bloky nebo stránky paralelně, přičemž je vědoma cílů škálovatelnosti pro jednotlivé objekty BLOB a účtu úložiště jako celku. Klientské knihovny Azure Storage podporují nahrávání paralelně. Pomocí následujících vlastností můžete například určit počet souběžných požadavků povolených v rozhraní .NET nebo Java. Klientské knihovny pro jiné podporované jazyky poskytují podobné možnosti.

- V případě rozhraní .NET nastavte vlastnost [BlobRequestOptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) .
- Pro Java/Android volejte metodu [BlobRequestOptions. setConcurrentRequestCount (Final Integer concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount) .

### <a name="upload-many-blobs-quickly"></a>Rychlé nahrání mnoha objektů BLOB

Pokud chcete rychle nahrát spoustu objektů blob, nahrajte paralelně objekty blob. Paralelní nahrávání je rychlejší než nahrávání izolovaných objektů BLOB s paralelním blokem nahrávání, protože šíří nahrávání napříč více oddíly služby úložiště. AzCopy provádí paralelní nahrávání ve výchozím nastavení a doporučuje se pro tento scénář. Další informace najdete v tématu [Začínáme s AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Zvolit správný typ objektu BLOB

Azure Storage podporuje objekty blob bloku, doplňovací objekty BLOB a objekty blob stránky. Pro daný scénář použití bude váš výběr typu objektu BLOB ovlivnit výkon a škálovatelnost vašeho řešení.

Objekty blob bloku jsou vhodné, pokud chcete efektivně nahrávat velké objemy dat. Například klientská aplikace, která nahrává fotky nebo video do úložiště objektů blob, by měla cílové objekty blob bloku.

Doplňovací objekty blob jsou podobné objektům blob bloku v tom, že se skládají z bloků. Když upravíte doplňovací objekt blob, budou bloky přidány pouze na konec objektu BLOB. Doplňovací objekty blob jsou užitečné pro scénáře, jako je protokolování, když aplikace potřebuje přidat data do existujícího objektu BLOB.

Objekty blob stránky jsou vhodné, pokud aplikace potřebuje pro data provádět náhodné zápisy. Například disky virtuálních počítačů Azure se ukládají jako objekty blob stránky. Další informace najdete v tématu [Principy objektů blob bloku, doplňovacích objektů BLOB a objektů blob stránky](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Další kroky

- [Škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](scalability-targets.md)
- [Škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Stavové a chybové kódy](/rest/api/storageservices/Status-and-Error-Codes2)