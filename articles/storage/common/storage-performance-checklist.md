---
title: Kontrolní seznam pro výkon a škálovatelnost Azure Storage | Microsoft Docs
description: Kontrolní seznam osvědčených postupů pro použití s Azure Storage při vývoji výkonných aplikací.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ee216bd4d6994179e347465c30039f2f8e293c85
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233021"
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Kontrolní seznam pro výkon a škálovatelnost Microsoft Azure Storage

Od vydání služby Microsoft Azure Storage Services vyvinula společnost Microsoft řadu osvědčených postupů pro používání těchto služeb takovým způsobem. Tento článek slouží k sjednocení nejdůležitějších z nich do seznamu ve stylu kontrolního seznamu. Záměrem tohoto článku je pomáhat vývojářům aplikací ověřit, že používají osvědčené postupy s Azure Storage a které jim pomohou identifikovat další osvědčené postupy, které by měly zvážit jejich přijetí. Tento článek se nepokusí o pokrytí všech možných optimalizací výkonu a škálovatelnosti – nezahrnuje ty, které jsou v jejich dopadu malé nebo nejsou v podstatě použitelné. V případě, že se chování aplikace dá předpovědět během návrhu, je užitečné, abyste si předešli tomu, abyste se vyhnuli návrhům, které budou fungovat v případě problémů s výkonem.  

Každý vývojář aplikace, který používá Azure Storage, by měl trvat čas přečíst si tento článek a ověřit, že se aplikace řídí jednotlivými osvědčenými postupy uvedenými níže.  

## <a name="checklist"></a>Kontrolní seznam

Tento článek uspořádá osvědčené postupy do následujících skupin. Osvědčené postupy platné pro:  

* Všechny Azure Storage služby (objekty blob, tabulky, fronty a soubory)
* Objekty blob
* Tabulky
* Fronty  

| Hotovo | Oblast | Kategorie | Otázka |
| --- | --- | --- | --- |
| &nbsp; | Všechny služby |Cíle škálovatelnosti |[Je vaše aplikace navržená tak, aby se zabránilo přístupu k cílům škálovatelnosti?](#subheading1) |
| &nbsp; | Všechny služby |Cíle škálovatelnosti |[Je vaše konvence vytváření názvů navržená tak, aby umožňovala lepší vyrovnávání zatížení?](#subheading47) |
| &nbsp; | Všechny služby |Sítě |[Mají zařízení na straně klienta dostatečně velkou šířku pásma a nízkou latenci pro dosažení potřebného výkonu?](#subheading2) |
| &nbsp; | Všechny služby |Sítě |[Mají zařízení na straně klienta dostatečně kvalitní odkaz?](#subheading3) |
| &nbsp; | Všechny služby |Sítě |[Je klientská aplikace umístěná v blízkosti účtu úložiště?](#subheading4) |
| &nbsp; | Všechny služby |Distribuce obsahu |[Používáte pro distribuci obsahu síť CDN?](#subheading5) |
| &nbsp; | Všechny služby |Přímý přístup klienta |[Používáte SAS a CORS k povolení přímého přístupu k úložišti místo proxy?](#subheading6) |
| &nbsp; | Všechny služby |Caching |[Ukládá vaše aplikace data do mezipaměti, která se opakovaně používají a mění zřídka?](#subheading7) |
| &nbsp; | Všechny služby |Caching |[Provádí vaše aplikace dávkové aktualizace (ukládání do mezipaměti na straně klienta a následné nahrávání do větších sad)?](#subheading8) |
| &nbsp; | Všechny služby |Konfigurace .NET |[Nakonfigurovali jste klienta tak, aby používal dostatečný počet souběžných připojení?](#subheading9) |
| &nbsp; | Všechny služby |Konfigurace .NET |[Nakonfigurovali jste .NET pro použití dostatečného počtu vláken?](#subheading10) |
| &nbsp; | Všechny služby |Konfigurace .NET |[Používáte rozhraní .NET 4,5 nebo novější, které má vylepšené shromažďování paměti?](#subheading11) |
| &nbsp; | Všechny služby |Paralelismu |[Měli byste zajistit, aby byl paralelismu správně ohraničený, takže nebudete mít možnost natěžovat své klientské funkce ani cíle škálovatelnosti?](#subheading12) |
| &nbsp; | Všechny služby |Nástroje |[Používáte nejnovější verzi Microsoft pro klientské knihovny a nástroje?](#subheading13) |
| &nbsp; | Všechny služby |Opakování |[Používáte exponenciální omezení rychlosti zásady opakování pro omezení chyb a časových limitů?](#subheading14) |
| &nbsp; | Všechny služby |Opakování |[Vyloučí vaše aplikace opakované pokusy o neopakující se chyby?](#subheading15) |
| &nbsp; | Objekty blob |Cíle škálovatelnosti |[Máte k jednomu objektu souběžně velký počet klientů?](#subheading46) |
| &nbsp; | Objekty blob |Cíle škálovatelnosti |[Zůstává vaše aplikace v rámci cíle šířky pásma nebo operace škálovatelnosti pro jeden objekt BLOB?](#subheading16) |
| &nbsp; | Objekty blob |Kopírování objektů BLOB |[Probíhá kopírování objektů BLOB účinným způsobem?](#subheading17) |
| &nbsp; | Objekty blob |Kopírování objektů BLOB |[Používáte pro hromadné kopírování objektů BLOB AzCopy?](#subheading18) |
| &nbsp; | Objekty blob |Kopírování objektů BLOB |[Používáte pro přenos velkých objemů dat Azure import/export?](#subheading19) |
| &nbsp; | Objekty blob |Použití metadat |[Ukládáte často používaná metadata o objektech blob v jejich metadatech?](#subheading20) |
| &nbsp; | Objekty blob |Rychlé nahrávání |[Při rychlém nahrání jednoho objektu BLOB se bloky nahrávají paralelně?](#subheading21) |
| &nbsp; | Objekty blob |Rychlé nahrávání |[Při rychlém nahrávání mnoha objektů BLOB se nahrávají objekty blob paralelně?](#subheading22) |
| &nbsp; | Objekty blob |Správný typ objektu BLOB |[Používáte objekty blob stránky nebo objekty blob bloku, pokud je to vhodné?](#subheading23) |
| &nbsp; | Tabulky |Cíle škálovatelnosti |[Blížíte se k cílům škálovatelnosti pro entity za sekundu?](#subheading24) |
| &nbsp; | Tabulky |Konfiguraci |[Používáte pro žádosti o tabulku JSON?](#subheading25) |
| &nbsp; | Tabulky |Konfiguraci |[Vypnuli jste Nagle pro zlepšení výkonu malých požadavků?](#subheading26) |
| &nbsp; | Tabulky |Tabulky a oddíly |[Správně jste rozdělili data?](#subheading27) |
| &nbsp; | Tabulky |Aktivní oddíly |[Nechcete se vyhnout pouze připojeným vzorům jenom pro připojení a k předřazení?](#subheading28) |
| &nbsp; | Tabulky |Aktivní oddíly |[Jsou vložení nebo aktualizace rozloženy mezi mnoho oddílů?](#subheading29) |
| &nbsp; | Tabulky |Obor dotazu |[Navrhli jste schéma, aby bylo možné používat dotazy bodů ve většině případů, a dotazy tabulek, které se mají používat zřídka?](#subheading30) |
| &nbsp; | Tabulky |Hustota dotazů |[Budou dotazy obvykle prohledávat a vracet pouze řádky, které bude aplikace používat?](#subheading31) |
| &nbsp; | Tabulky |Omezení vrácených dat |[Používáte filtrování, abyste se vyhnuli vrácení nepotřebných entit?](#subheading32) |
| &nbsp; | Tabulky |Omezení vrácených dat |[Používáte projekci k tomu, abyste se vyhnuli vrácení vlastností, které nejsou potřeba?](#subheading33) |
| &nbsp; | Tabulky |Denormalizace |[Vyzkoušeli jste Denormalizovaná data, abyste se vyhnuli neefektivním dotazům nebo vícenásobným požadavkům na čtení při pokusu o získání dat?](#subheading34) |
| &nbsp; | Tabulky |Vložit/aktualizovat/odstranit |[Vytváříte dávkování požadavků, které musí být transakční, nebo je lze provést ve stejnou dobu, abyste snížili zpáteční cesty?](#subheading35) |
| &nbsp; | Tabulky |Vložit/aktualizovat/odstranit |[Nemůžete načítat entitu jenom k určení toho, jestli se má volat vložení nebo aktualizace?](#subheading36) |
| &nbsp; | Tabulky |Vložit/aktualizovat/odstranit |[Měli jste v potaz ukládání řady dat, které se často načítají v jedné entitě jako vlastnosti místo více entit?](#subheading37) |
| &nbsp; | Tabulky |Vložit/aktualizovat/odstranit |[Pro entity, které se vždycky načítají společně a můžou být napsané v dávkách (například data časových řad), jste se domnívali používat objekty blob místo tabulek?](#subheading38) |
| &nbsp; | Fronty |Cíle škálovatelnosti |[Blížíte se k cílům škálovatelnosti pro zprávy za sekundu?](#subheading39) |
| &nbsp; | Fronty |Konfiguraci |[Vypnuli jste Nagle pro zlepšení výkonu malých požadavků?](#subheading40) |
| &nbsp; | Fronty |Velikost zprávy |[Mají vaše zprávy kompresi pro zlepšení výkonu fronty?](#subheading41) |
| &nbsp; | Fronty |Hromadné načtení |[Načítáte více zpráv v rámci jedné operace "Get"?](#subheading42) |
| &nbsp; | Fronty |Frekvence cyklického dotazování |[Je dotazování na často dostatečně časté, aby se snížila zjištěná latence vaší aplikace?](#subheading43) |
| &nbsp; | Fronty |Aktualizovat zprávu |[Používáte UpdateMessage k ukládání průběhu zpracování zpráv, nemusíte při výskytu chyby znovu zpracovávat celou zprávu?](#subheading44) |
| &nbsp; | Fronty |Architektura |[Používáte fronty k zajištění větší škálovatelnosti celé aplikace tím, že zachováte dlouhodobě spuštěné úlohy ze kritické cesty a škálování pak nezávisle na sobě?](#subheading45) |

## <a name="allservices"></a>Všechny služby

V této části jsou uvedené osvědčené postupy, které se vztahují na použití kterékoli z Azure Storage Services (objekty blob, tabulky, fronty nebo soubory).  

### <a name="subheading1"></a>Cíle škálovatelnosti

Azure Storage má limit 250 účtů úložiště na jednu oblast a předplatné. Pokud dosáhnete tohoto limitu, v dané kombinaci předplatného a oblasti již nebudete moct vytvářet účty úložiště.

Každá z Azure Storage služeb má cíle škálovatelnosti pro kapacitu (GB), rychlost transakcí a šířku pásma. Pokud vaše aplikace přistupuje k některým cílům škálovatelnosti nebo překročí, může dojít ke zvýšené latenci transakcí nebo omezování. Když služba úložiště omezí vaši aplikaci, začne služba pro některé transakce úložiště vracet chybové kódy "503 server zaneprázdněn" nebo "500" časový limit operace. V této části se zabývá obecným přístupem k řešení cílů škálovatelnosti a konkrétních cílů škálovatelnosti šířky pásma. Později oddíly, které se týkají jednotlivých služeb úložiště, projednávají cíle škálovatelnosti v kontextu této konkrétní služby:  

* [Šířka pásma a požadavky objektů blob za sekundu](#subheading16)
* [Entity tabulek za sekundu](#subheading24)
* [Zprávy fronty za sekundu](#subheading39)  

#### <a name="sub1bandwidth"></a>Cíl škálovatelnosti šířky pásma pro všechny služby

V době psaní jsou cíle šířky pásma v USA pro geograficky redundantní úložiště (GRS) 10 Gigabit za sekundu (GB/s) pro příchozí přenosy (data odesílaná do účtu úložiště) a 20 GB/s pro odchozí přenosy (data odesílaná z účtu úložiště). Pro účet místně redundantního úložiště (LRS) jsou limity vyšší – 20 GB/s pro příchozí přenos dat a 30 GB/s pro odchozí přenosy.  Limity mezinárodní šířky pásma můžou být nižší a dají se najít na [stránce cíle škálovatelnosti](https://msdn.microsoft.com/library/azure/dn249410.aspx).  Další informace o možnostech redundance úložiště najdete v odkazech v tématu užitečné prostředky níže.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Co dělat při přístupu k cíli škálovatelnosti

Pokud se blížíte omezení účtů úložiště, můžete mít v konkrétní kombinaci předplatného nebo oblasti, vyhodnotit svoji aplikaci a používání účtů úložiště a zjistit, jestli platí kterákoli z těchto podmínek.

* Používání účtů úložiště jako nespravovaných disků a přidávání disků do virtuálních počítačů. V tomto scénáři doporučujeme používat [spravované disky](../../virtual-machines/windows/managed-disks-overview.md), protože při zpracování škálovatelnosti disků úložiště nebudete muset vytvářet a spravovat jednotlivé účty úložiště.
* Použití jednoho účtu úložiště na základě jednotlivých zákazníků pro účely izolace dat. V tomto scénáři doporučujeme použít kontejnery úložiště pro každého zákazníka a nikoli celý účet úložiště. Azure Storage teď umožňuje zadat řízení přístupu na základě role na [základě](storage-auth-aad-rbac-portal.md)jednotlivých kontejnerů.
* Použití více účtů úložiště k horizontálních oddílů k zajištění větší škálovatelnosti příchozího přenosu dat/vstupně-výstupních operací/IOPS/kapacity Pokud je to možné, doporučujeme, abyste využili [vyšší limity](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) standardních účtů úložiště, abyste snížili počet účtů úložiště potřebných pro vaše zatížení.

Pokud se vaše aplikace blíží cílům škálovatelnosti pro jeden účet úložiště, zvažte použití jednoho z následujících přístupů:  

* Převezměte v úvahu zatížení, které způsobí, že vaše aplikace bude přistupovat k cíli škálovatelnosti nebo nad něj. Můžete ji navrhovat odlišně, abyste mohli používat menší šířku pásma nebo kapacitu nebo méně transakcí?
* Pokud aplikace musí překročit jeden z cílů škálovatelnosti, měli byste vytvořit několik účtů úložiště a rozdělit data aplikací napříč různými účty úložiště. Pokud použijete tento model, nezapomeňte navrhnout aplikaci, abyste mohli v budoucnu přidat další účty úložiště pro vyrovnávání zatížení. V době psaní může mít každé předplatné Azure až 250 účtů úložiště v každé oblasti (při nasazení s modelem Azure Resource Manager).  Účty úložiště také nemají žádné náklady jiné než vaše využití v závislosti na uložených datech, provedených transakcích nebo přenesených datech.
* Pokud vaše aplikace narazí na cíle šířky pásma, zvažte komprimaci dat v klientovi, aby se snížila šířka pásma potřebná k odeslání dat do služby úložiště.  I když to může ušetřit šířku pásma a zvýšit výkon sítě, může to mít taky negativní dopad.  Měli byste vyhodnotit dopad na výkon, protože se jedná o dodatečné požadavky na zpracování pro komprimaci a dekomprimaci dat v klientovi. Kromě toho může ukládání komprimovaných dat ztížit obtíže při řešení problémů, protože může být obtížnější zobrazit uložená data pomocí standardních nástrojů.
* Pokud vaše aplikace narazí na cíle škálovatelnosti, ujistěte se, že pro opakované pokusy používáte exponenciální omezení rychlosti (viz pokusy o [opakování](#subheading14)).  Je lepší, abyste se ujistili, že nikdy nebudete mít přístup k cílům škálovatelnosti (pomocí jedné z výše uvedených metod), ale to zajistí, aby vaše aplikace neprováděla rychlejší opakování, což snižuje omezení.  

#### <a name="useful-resources"></a>Užitečné materiály

Následující odkazy poskytují další podrobnosti o cílech škálovatelnosti:

* Informace o cílech škálovatelnosti najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle](storage-scalability-targets.md) .
* Informace o možnostech redundance úložiště najdete v tématu [Azure Storage replikace](storage-redundancy.md) a příspěvek blogu [Azure Storage možnosti redundance a geograficky redundantní úložiště s přístupem pro čtení](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) .
* Aktuální informace o cenách služeb Azure najdete v tématu [ceny Azure](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Konvence vytváření názvů oddílů

Azure Storage používá schéma dělení na základě rozsahu pro škálování a vyrovnávání zatížení systému. Klíč oddílu (Account + Container + BLOB) se používá k rozdělení dat do rozsahů a tyto rozsahy se v systému vyrovnávají pomocí zatížení. To znamená, že konvence pojmenování, jako je lexikální řazení (například *mypayroll*, *myperformance*, *myemployees*atd.) nebo použití časových razítek (*log20160101*, *log20160102*, *log20160102*atd.), budou poskytovat k oddílům, které se můžou nacházet na stejném serveru oddílu, patří, až do chvíle, kdy operace vyrovnávání zatížení rozdělí do menších rozsahů. Například všechny objekty BLOB v kontejneru může obsluhovat jeden server, dokud zatížení těchto objektů BLOB nevyžaduje další opětovné vyrovnávání rozsahů oddílů. Podobně skupina lehce načtených účtů, jejichž názvy jsou uspořádány v lexikálním pořadí, mohou být obsluhovány jediným serverem, dokud zatížení jednoho nebo všech těchto účtů nevyžaduje, aby byly rozděleny mezi více oddílů serverů. Každá operace vyrovnávání zatížení může mít vliv na latenci volání úložiště během operace. Schopnost zpracování náhlého nárůstu provozu do oddílu je omezená škálovatelností serveru s jedním oddílem, dokud se operace vyrovnávání zatížení neuvolní, a znovu vyrovná rozsah klíčů oddílu.

Můžete postupovat podle určitých osvědčených postupů a snížit tak četnost těchto operací.  

* Pokud je to možné, použijte větší velikost objektu PUT a velikosti bloku Put (více než 4 MiB pro účty úrovně Premium a vyšší než 256 KiB pro účty Premium) k aktivaci služby HTBB (High-propustnost) objektů BLOB. HTBB poskytuje vysoce výkonné ingestování, které není ovlivněné pojmenovávání oddílů.
* Projděte si zásadu vytváření názvů, kterou používáte pro účty, kontejnery, objekty blob, tabulky a fronty, a to pečlivě. Pomocí funkce hash, která nejlépe vyhovuje vašim potřebám, zvažte použití předpony názvů účtů, kontejnerů nebo objektů BLOB s použitím hodnoty hash se třemi číslicemi.  
* Pokud vaše data organizujete pomocí časových razítek nebo číselných identifikátorů, musíte se ujistit, že nepoužíváte vzory přenosů jenom pro připojení (nebo jenom k předplatnému). Tyto vzory nejsou vhodné pro systém dělení na základě rozsahu a můžou vést ke všem přenosům na jeden oddíl a omezením systému z efektivního vyrovnávání zatížení. Například pokud máte každodenní operace, které používají objekt BLOB s časovým razítkem, jako je *RRRRMMDD*, pak veškerý provoz pro tuto každodenní operaci bude směrován na jeden objekt, který je obsluhován jedním oddílovým serverem. Podívejte se, jestli omezení podle počtu objektů BLOB a omezení na oddíly vyhovují vašim potřebám, a v případě potřeby zvažte rozdělení této operace do více objektů BLOB. Podobně platí, že pokud ukládáte data časových řad do tabulek, veškerý provoz může být směrován do poslední části oboru názvů Key. Pokud je nutné použít časová razítka nebo číselná ID, použijte předponu ID s hodnotou hash na 3 číslice nebo v případě časových razítek předpony sekund v době, jako je například *ssyyyymmdd*. Pokud jsou operace výpisu a dotazování prováděny rutinou, vyberte funkci hash, která omezí počet dotazů. V jiných případech může stačit náhodná předpona.  
* Další informace o schématu dělení používaného v Azure Storage najdete v článku [Azure Storage: Vysoce dostupná služba cloudového úložiště se silnými](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf)konzistencí.

### <a name="networking"></a>Sítě

I když rozhraní API volá, často omezení fyzické sítě aplikace mají významný dopad na výkon. Následující popis popisuje některá omezení, která se mohou vyskytnout pro uživatele.  

#### <a name="client-network-capability"></a>Schopnost klientské sítě

##### <a name="subheading2"></a>Zvyšují

V případě šířky pásma je problém často funkcemi klienta. Například když může jeden účet úložiště zvládnout 10 GB/s nebo více příchozích přenosů (viz [cíle škálovatelnosti šířky pásma](#sub1bandwidth)), rychlost sítě v malé instanci role Azure Worker je schopná jenom přibližně 100 MB/s. Větší instance Azure mají síťové karty s větší kapacitou, takže byste měli zvážit použití větší instance nebo více virtuálních počítačů, pokud potřebujete vyšší omezení sítě z jednoho počítače. Pokud ke službě úložiště přistupujete z místní aplikace, platí stejné pravidlo: Pochopte síťové možnosti klientského zařízení a síťové připojení k Azure Storage umístění a buď je Vylepšete podle potřeby, nebo Navrhněte aplikace, která bude fungovat v rámci svých schopností.  

##### <a name="subheading3"></a>Kvalita propojení

Stejně jako u jakéhokoli využití sítě mějte na paměti, že síťové podmínky, které mají za následek chyby a ztráty paketů, zpomalují efektivní propustnost.  K diagnostice tohoto problému může využít nástroj WireShark nebo NetMon.  

##### <a name="useful-resources"></a>Užitečné materiály

Další informace o velikostech virtuálních počítačů a přidělených šířce pásma najdete v tématu velikosti virtuálních počítačů s [Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [velikosti virtuálních počítačů](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)se systémem Linux.  

#### <a name="subheading4"></a>Oblasti

V jakémkoli distribuovaném prostředí je umístění klienta poblíž serveru k dispozici nejlepšího výkonu. Pro přístup k Azure Storage s nejnižší latencí je nejlepší umístění pro vašeho klienta ve stejné oblasti Azure. Pokud máte například web Azure, který používá Azure Storage, měli byste je umístit v rámci jedné oblasti (například USA – západ nebo Asie – jihovýchod). Tím se snižuje latence a náklady – v době psaní je využití šířky pásma v rámci jedné oblasti zdarma.  

Pokud vaše klientské aplikace nejsou hostovány v rámci Azure (například aplikace pro mobilní zařízení nebo místní podnikové služby), pak znovu umístěte účet úložiště v oblasti poblíž zařízení, která k němu budou mít přístup, a bude obecně snižovat latenci. Pokud jsou vaši klienti široce distribuované (například některé v Severní Amerika a některé v Evropě), měli byste zvážit použití více účtů úložiště: jeden umístěný v oblasti Severní Ameriky a jeden v evropské oblasti. To vám pomůže snížit latenci pro uživatele v obou oblastech. Tento přístup je snazší implementovat, pokud jsou data, která aplikace ukládá, specifická pro jednotlivé uživatele a nevyžadují replikaci dat mezi účty úložiště.  Pro širokou distribuci obsahu doporučujeme síť CDN – další podrobnosti najdete v další části.  

### <a name="subheading5"></a>Distribuce obsahu

V některých případech aplikace potřebuje stejný obsah pro mnoho uživatelů (například ukázkové video, které se používá na domovské stránce webu), a to ve stejné nebo několika oblastech. V tomto scénáři byste měli použít Content Delivery Network (CDN), jako je Azure CDN, a CDN by jako zdroj dat používalo službu Azure Storage. Na rozdíl od Azure Storage účtu, který existuje v jedné oblasti a který nemůže doručovat obsah s nízkou latencí do jiných oblastí, Azure CDN používá servery v několika datových centrech po celém světě. CDN navíc může obvykle podporovat mnohem vyšší omezení pro odchozí přenosy než jeden účet úložiště.  

Další informace o Azure CDN najdete v tématu [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Použití SAS a CORS

Pokud potřebujete autorizovat kód jako JavaScript ve webovém prohlížeči nebo mobilním telefonu uživatele pro přístup k datům v Azure Storage, je jedním z přístupů použití aplikace ve webové roli jako proxy: zařízení uživatele se ověřuje pomocí webové role. , což zase autorizuje přístup k prostředkům úložiště. Tímto způsobem se můžete vyhnout vystavení klíčů účtu úložiště na nezabezpečených zařízeních. Tím se ale významně zvýší režie webové role, protože všechna data přenesená mezi zařízením uživatele a službou úložiště musí procházet webovou rolí. Můžete se vyhnout použití webové role jako proxy serveru pro službu úložiště pomocí sdíleného přístupového podpisu (SAS), někdy ve spojení s hlavičkou sdílení prostředků mezi zdroji (CORS). Pomocí SAS můžete zařízení uživatele umožnit, aby prostřednictvím omezeného přístupového tokenu zavedla požadavky přímo do služby úložiště. Pokud chce například uživatel odeslat fotografii do aplikace, může vaše webová role vygenerovat a odeslat do zařízení uživatele token SAS, který uděluje oprávnění k zápisu do konkrétního objektu BLOB nebo kontejneru po dobu následujících 30 minut (po jejichž uplynutí vyprší platnost tokenu SAS).

V normálním případě prohlížeč neumožní JavaScriptu na stránce hostované webem v jedné doméně provádět konkrétní operace, jako je například "PUT" do jiné domény. Pokud například hostete webovou roli na adrese "contosomarketing.cloudapp.net" a chcete použít JavaScript na straně klienta k nahrání objektu blob do účtu úložiště na adrese "contosoproducts.blob.core.windows.net", "prohlížeče" stejné zásady původu "zakazují tuto operaci. CORS je funkce prohlížeče, která umožňuje cílové doméně (v tomto případě účtu úložiště) komunikovat s prohlížečem, který důvěřuje žádostem pocházejících z zdrojové domény (v tomto případě webová role).  

Obě tyto technologie vám pomohou vyhnout se zbytečnému zatížení vaší webové aplikace (a kritickým místům).  

#### <a name="useful-resources"></a>Užitečné materiály

Další informace o SAS najdete v tématu [signatury sdíleného přístupu, část 1: Princip modelu](../storage-dotnet-shared-access-signature-part-1.md)SAS.  

Další informace o CORS najdete v tématu [Podpora sdílení prostředků mezi zdroji (CORS) pro služby Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Caching

#### <a name="subheading7"></a>Získávání dat

Obecně platí, že získání dat ze služby je lepší, než je pokaždé. Vezměte v úvahu příklad webové aplikace MVC spuštěné ve webové roli, která už od služby úložiště získala objekt BLOB 50 MB, který slouží jako obsah pro uživatele. Aplikace pak může tento stejný objekt BLOB načíst pokaždé, když si ho uživatel požádá, nebo ho může lokálně uložit na disk a znovu použít verzi v mezipaměti pro následné požadavky uživatele. Kromě toho, když uživatel požádá o data, může aplikace vystavit s podmíněnou hlavičkou pro čas změny, což by zabránilo získání celého objektu blob, pokud nebyl upraven. Stejný vzor můžete použít pro práci s entitami tabulky.  

V některých případech se můžete rozhodnout, že aplikace může předpokládat, že objekt BLOB zůstane po jeho načtení platný po krátkou dobu a že během této doby aplikace nemusí kontrolovat, jestli se objekt BLOB změnil.

Konfigurace, vyhledávání a další data, která aplikace vždycky používá, jsou skvělými kandidáty pro ukládání do mezipaměti.  

Další informace o podmíněných souborech ke stažení najdete v tématu [určení podmíněných hlaviček pro operace BLOB Service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

#### <a name="subheading8"></a>Nahrávání dat v dávkách

V některých scénářích aplikací můžete data agregovat místně a pak je pravidelně nahrávat do dávky místo toho, aby se data nahrála okamžitě. Webová aplikace může například uchovávat soubor protokolu aktivit: aplikace může buď Odeslat podrobnosti o každé aktivitě, když se stane jako entita tabulky (což vyžaduje mnoho operací úložiště), nebo může uložit podrobnosti o aktivitě do místního souboru protokolu a pak pravidelně Nahrajte všechny podrobnosti o aktivitách jako soubor s oddělovači do objektu BLOB. Pokud je velikost každého záznamu v protokolu 1 KB, můžete nahrát tisíce v rámci jedné transakce "Put blob" (v jedné transakci můžete nahrát objekt BLOB o velikosti až 64 MB). Pokud dojde k selhání místního počítače před nahráním, budete potenciálně přijít o některá data protokolu: vývojář aplikace musí navrhnout možnost pro případ selhání klientského zařízení nebo nahrávání.  Pokud je třeba data aktivity stáhnout pro časové intervaly (ne jen pro jednu aktivitu), jsou objekty blob pro tabulky doporučeny.

### <a name="net-configuration"></a>Konfigurace .NET

Pokud používáte .NET Framework, v této části najdete několik nastavení rychlých konfigurací, pomocí kterých můžete provádět významná vylepšení výkonu.  Pokud používáte jiné jazyky, podívejte se, jestli se ve zvoleném jazyce použijí podobné koncepty.  

#### <a name="subheading9"></a>Zvýšit výchozí limit připojení

V rozhraní .NET zvyšuje následující kód výchozí limit připojení (obvykle 2 v klientském prostředí nebo 10 v prostředí serveru) na 100. Obvykle byste měli nastavit hodnotu na přibližně počet vláken používaných vaší aplikací.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Před otevřením připojení je nutné nastavit limit připojení.  

Další programovací jazyky najdete v dokumentaci k příslušnému jazyku, kde zjistíte, jak nastavit limit připojení.  

Další informace najdete v blogovém příspěvku [webové služby: Souběžná](https://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx)připojení.  

#### <a name="subheading10"></a>Zvýšit minimální počet vláken při použití synchronního kódu s asynchronními úkoly

Tento kód zvýší minimální počet vláken ve fondu vláken:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Další informace naleznete v tématu [Metoda fondu vláken. SetMinThreads –](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Využijte pro uvolňování paměti .NET 4,5 a vyšší.

Pro klientskou aplikaci použijte .NET 4,5 nebo novější, abyste mohli využít vylepšení výkonu při uvolňování paměti serveru.

Další informace najdete v článku [Přehled vylepšení výkonu v .net 4,5](https://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Neohraničené paralelismus

I když paralelismus může být skvělý pro výkon, buďte opatrní na používání nevázaných paralelismu (bez omezení počtu vláken a paralelních požadavků), aby bylo možné nahrávat nebo stahovat data pomocí více pracovních procesů k přístupu k několika oddílům (kontejnerům, frontám nebo oddíly tabulky) ve stejném účtu úložiště nebo pro přístup k několika položkám ve stejném oddílu. Pokud je paralelismus neohraničený, může vaše aplikace přesáhnout možnosti klientského zařízení nebo cíle škálovatelnosti účtu úložiště, což vede k delší latenci a omezování.  

### <a name="subheading13"></a>Klientské knihovny a nástroje pro úložiště

Vždy používejte nejnovější poskytnuté klientské knihovny a nástroje od společnosti Microsoft. V době psaní jsou k dispozici klientské knihovny pro rozhraní .NET, Windows Phone, prostředí Windows Runtime, Java a C++také pro knihovny verze Preview pro jiné jazyky. Kromě toho společnost Microsoft vydala rutiny prostředí PowerShell a příkazy rozhraní příkazového řádku Azure pro práci s Azure Storage. Microsoft aktivně vyvíjí tyto nástroje s ohledem na výkon, udržuje je aktuální s nejnovějšími verzemi služby a zajišťuje interní zpracování mnoha osvědčených postupů výkonu.  

### <a name="retries"></a>Opakování

#### <a name="subheading14"></a>Omezení a chyby zaneprázdněnosti serveru

V některých případech může služba úložiště aplikaci omezit nebo nemusí být schopná tuto žádost jednoduše zpracovat, protože se jedná o přechodný stav a vrátí zprávu "503 server zaneprázdněn" nebo "500 časový limit".  K tomu může dojít, pokud se vaše aplikace blíží jakémukoli cíli škálovatelnosti nebo pokud systém znovu vyrovnává vaše dělená data, aby bylo možné dosáhnout vyšší propustnosti.  Klientská aplikace by obvykle měla operaci zopakovat, což způsobí chybu: pokus o stejný požadavek později může být úspěšný. Pokud však služba úložiště omezuje vaši aplikaci, protože přesahuje cíle škálovatelnosti, nebo i v případě, že se služba nemohla z nějakého důvodu vyhovět. Z tohoto důvodu byste měli použít exponenciální zpět (u tohoto chování výchozí klientské knihovny). Vaše aplikace se může například opakovat po 2 sekundách, potom 4 sekundy, 10 sekund, 30 sekund a potom zcela. Výsledkem tohoto chování je, že aplikace významně snižuje zatížení služby, a ne exacerbating žádné problémy.  

Chyby připojení se dají opakovat okamžitě, protože nejsou výsledkem omezení a očekává se, že budou přechodné.  

#### <a name="subheading15"></a>Neopakující se chyby

Klientské knihovny jsou vědomy, které chyby jsou znovu možné a které nejsou. Pokud však píšete vlastní kód proti REST API úložiště, pamatujte, že došlo k chybám, které byste neměli opakovat: například odpověď 400 (chybná žádost) indikuje, že klientská aplikace odeslala požadavek, který nebylo možné zpracovat, protože nebyl v očekávané podobě. Po opětovném odeslání této žádosti se pokaždé pošle stejná odpověď, takže nedojde k žádnému bodu v dalším pokusu. Pokud píšete vlastní kód proti REST API úložiště, mějte na paměti, co znamenají chybové kódy a správný způsob, jak opakovat (nebo ne) pro každý z nich.  

#### <a name="useful-resources"></a>Užitečné materiály

Další informace o kódech chyb úložiště najdete v tématu [stav a chybové kódy](https://msdn.microsoft.com/library/azure/dd179382.aspx) na webu Microsoft Azure.  

## <a name="blobs"></a>Objekty blob

Kromě osvědčených postupů pro všechny popsané [služby](#allservices) byly následující osvědčené postupy uplatněny konkrétně na BLOB Service.  

### <a name="blob-specific-scalability-targets"></a>Cíle škálovatelnosti specifické pro objekt BLOB

#### <a name="subheading46"></a>Více klientů přistupujících k jednomu objektu současně

Pokud máte velký počet klientů, kteří přistupují k jednomu objektu souběžně, budete muset vzít v úvahu cíle škálovatelnosti na objekt a účet úložiště. Přesný počet klientů, kteří mají přístup k jednomu objektu, se liší v závislosti na faktorech, jako je například počet klientů požadujících objekt současně, velikost objektu, stav sítě atd.

Pokud je možné objekt distribuovat prostřednictvím sítě CDN, jako jsou obrázky nebo videa poskytované z webu, měli byste použít CDN. Podívejte se [sem](#subheading5).

V jiných scénářích, jako jsou například vědecké simulace, u kterých jsou data důvěrná, máte dvě možnosti. První z nich je rozložit přístup k vašemu úlohám tak, aby byl objekt přístupný v době, kdy k němu dojde současně. Alternativně můžete objekt dočasně zkopírovat do více účtů úložiště a tím zvýšit celkový počet IOPS na jeden objekt a napříč účty úložiště. V případě omezeného testování jsme zjistili, že kolem 25 virtuálních počítačů by se mohlo současně stáhnout 100 GB objektů BLOB s GB (každý virtuální počítač byl virtuálního ke stažení pomocí 32 vláken). Pokud jste měli 100, které klienti potřebují k přístupu k objektu, nejdřív ho zkopírujte do druhého účtu úložiště a pak napřed přístup k prvnímu objektu BLOB prvním virtuálním počítačům 50 a druhým virtuálním počítačům 50 přístup k druhému objektu BLOB. Výsledky se budou lišit v závislosti na chování vaší aplikace, takže byste je měli během návrhu otestovat. 

#### <a name="subheading16"></a>Šířka pásma a operace na objekt BLOB

Jeden objekt blob můžete číst nebo zapisovat až do maximálního počtu 60 MB/s (přibližně 480 MB/s, což překračuje možnosti mnoha sítí na straně klienta (včetně fyzické síťové karty v klientském zařízení). Kromě toho jeden objekt BLOB podporuje až 500 požadavků za sekundu. Pokud máte několik klientů, kteří potřebují číst stejný objekt BLOB a možná tato omezení překročíte, měli byste zvážit použití sítě CDN pro distribuci objektu BLOB.  

Další informace o cílové propustnosti objektů BLOB najdete v tématu [Azure Storage škálovatelnost a výkonnostní cíle](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopírování a přesouvání objektů BLOB

#### <a name="subheading17"></a>Kopírovat objekt BLOB

REST API úložiště verze 2012-02-12 představila užitečnou možnost kopírování objektů BLOB mezi účty: klientská aplikace může dát službě úložiště pokyn ke zkopírování objektu BLOB z jiného zdroje (případně v jiném účtu úložiště) a pak tuto službu umožní provést. asynchronní kopírování. To může významně snížit šířku pásma potřebnou pro aplikaci při migraci dat z jiných účtů úložiště, protože není nutné data stahovat a nahrávat.  

To však znamená, že při kopírování mezi účty úložiště není nijak zaručena žádná časová prodleva, kdy se kopie dokončí. Pokud vaše aplikace potřebuje dokončit kopírování objektů BLOB v rámci vašeho ovládacího prvku, může být lepší zkopírovat objekt BLOB tak, že ho stáhne do virtuálního počítače a pak ho nahraje do cílového umístění.  V případě úplné předvídatelnosti v takové situaci se ujistěte, že je kopie prováděna pomocí virtuálního počítače spuštěného ve stejné oblasti Azure, jinak může dojít k ovlivnění výkonu vaší sítě (a pravděpodobně bude).  Navíc můžete monitorovat průběh asynchronní kopie programově.  

Kopie v rámci stejného účtu úložiště jsou obvykle rychle dokončeny.  

Další informace najdete v tématu [kopírování objektu BLOB](https://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Použití AzCopy

Tým Azure Storage vydal nástroj příkazového řádku "AzCopy", který je určen pro usnadnění hromadného přenosu mnoha objektů blob do, z a napříč účty úložiště.  Tento nástroj je optimalizován pro tento scénář a může dosáhnout vysokého přenosu.  Jeho použití se doporučuje pro scénáře hromadného nahrávání, stahování a kopírování. Další informace o tom, jak ho stáhnout, najdete v tématu [přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Služba import/export Azure

Pro velké objemy dat (více než 1 TB) nabízí Azure Storage službu import/export, která umožňuje nahrávání a stahování z úložiště objektů BLOB prostřednictvím přenosu pevných disků.  Data můžete umístit na pevný disk a odeslat ho do Microsoftu pro nahrání nebo Odeslat prázdný pevný disk Microsoftu pro stahování dat.  Další informace najdete v článku o [použití služby Microsoft Azure Import/Export k přenosu dat do služby Blob Storage](../storage-import-export-service.md).  To může být mnohem efektivnější než nahrávání nebo stahování tohoto objemu dat přes síť.  

### <a name="subheading20"></a>Použití metadat

Blob service podporuje žádosti o hlavní informace, které můžou zahrnovat metadata o objektu BLOB. Například pokud vaše aplikace potřebovala data EXIF z fotografie, může ji načíst a extrahovat. Chcete-li ušetřit šířku pásma a zvýšit výkon, může vaše aplikace ukládat data EXIF v metadatech objektu blob, když aplikace nahrála fotografii: můžete načíst data EXIF v metadatech pouze pomocí hlavní žádosti, ušetřit značnou šířku pásma a čas zpracování potřebný k extrakci dat EXIF při každém čtení objektu BLOB. To by bylo užitečné ve scénářích, kdy potřebujete pouze metadata a nikoli úplný obsah objektu BLOB.  Na jeden objekt BLOB se dá ukládat jenom 8 KB metadat (služba nepřijímá požadavek na uložení více než této), takže pokud se data nevejdou do této velikosti, možná nebudete moct tento přístup použít.  

### <a name="rapid-uploading"></a>Rychlé nahrávání

Chcete-li rychle nahrát objekty blob, je první otázkou, kterou je třeba odeslat: máte jeden objekt BLOB nebo mnoho?  Použijte níže uvedené pokyny k určení správné metody, která se má použít v závislosti na vašem scénáři.  

#### <a name="subheading21"></a>Rychlé nahrávání jednoho velkého objektu BLOB

Aby bylo možné rychle nahrát jeden rozsáhlý objekt blob, vaše klientské aplikace by měly nahrávat své bloky nebo stránky paralelně (s vědomím cílů škálovatelnosti pro jednotlivé objekty BLOB a účtu úložiště jako celku).  Oficiální klientské knihovny pro úložiště Microsoft RTM RTM (.NET, Java) mají možnost to udělat.  Pro každou z knihoven použijte následující zadaný objekt/vlastnost k nastavení úrovně souběžnosti:  

* .NET: Nastavte ParallelOperationThreadCount na objekt BlobRequestOptions, který se má použít.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Použijte parallelOperationThreadCount buď na základě možností žádosti, nebo na Blob service.
* C++: Použijte metodu blob_request_options:: set_parallelism_factor.

#### <a name="subheading22"></a>Rychlé nahrávání mnoha objektů BLOB

Pokud chcete rychle nahrát spoustu objektů blob, nahrajte paralelně objekty blob. To je rychlejší než nahrávání izolovaných objektů BLOB pomocí paralelního blokování nahrávání, protože je rozdělené do několika oddílů služby úložiště. Jeden objekt BLOB podporuje jenom propustnost 60 MB za sekundu (přibližně 480 MB/s). V době psaní LRS účet na bázi US podporuje příchozí přenos dat až 20 GB/s, což je mnohem víc než propustnost podporovaná jednotlivými objekty blob.  [AzCopy](#subheading18) provádí paralelní nahrávání ve výchozím nastavení a doporučuje se pro tento scénář.  

### <a name="subheading23"></a>Výběr správného typu objektu BLOB

Azure Storage podporuje dva typy objektů BLOB: objekty blob *stránky* a objekty blob *bloku* . Pro daný scénář použití bude váš výběr typu objektu BLOB ovlivnit výkon a škálovatelnost vašeho řešení. Objekty blob bloku jsou vhodné, pokud chcete efektivně nahrávat velké objemy dat: klientská aplikace může například potřebovat nahrávat fotky nebo video do úložiště objektů BLOB. Objekty blob stránky jsou vhodné, pokud aplikace potřebuje pro data provádět náhodné zápisy. třeba virtuální pevné disky Azure se ukládají jako objekty blob stránky.  

Další informace najdete v tématu [Principy objektů blob bloku, doplňovacích objektů BLOB a objektů blob stránky](https://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabulky

Kromě osvědčených postupů pro všechny popsané [služby](#allservices) byly následující osvědčené postupy uplatněny konkrétně pro službu Table Service.  

### <a name="subheading24"></a>Cíle škálovatelnosti specifické pro tabulku

Kromě omezení šířky pásma celého účtu úložiště mají tabulky následující konkrétní omezení škálovatelnosti.  Systém vyrovnává zatížení, protože se zvyšuje objem provozu, ale v případě, že váš provoz má náhlé výpadky, možná nebudete moci okamžitě získat tento objem propustnosti.  Pokud váš vzor obsahuje shluky, měli byste očekávat, že v průběhu nárůstu zatížení a při automatickém načítání vyrovnávání zatížení tabulky vyčerpáte časové limity nebo časový limit.  Rozkládání na pomalejších systémech má lepší výsledky, protože systémový čas pro správné vyrovnávání zatížení.  

#### <a name="entities-per-second-storage-account"></a>Entity za sekundu (účet úložiště)

Omezení škálovatelnosti pro přístup k tabulkám je až 20 000 entit (1 KB každý) za sekundu pro účet.  Obecně platí, že každou entitu, která je vložená, aktualizovaná, Odstraněná nebo se vyhledá na tomto cíli.  Dávková vložení obsahující 100 entit by tedy bylo možné počítat jako jednotky 100.  Dotaz, který kontroluje 1000 entit a vrátí hodnotu 5, by byl počet entit 1000.  

#### <a name="entities-per-second-partition"></a>Entity za sekundu (oddíl)

V rámci jednoho oddílu je cíl škálovatelnosti pro přístup k tabulkám 2 000 entit (1 KB každý) za sekundu, a to za použití stejného počítání, jak je popsáno v předchozí části.  

### <a name="configuration"></a>Konfiguraci

V této části je uvedeno několik nastavení rychlých konfigurací, která můžete použít k výraznému zlepšení výkonu služby Table Service:  

#### <a name="subheading25"></a>Použít JSON

Počínaje službou Storage verze 2013-08-15 podporuje služba Table Service místo formátu AtomPub založeného na jazyce XML pro přenos dat tabulky pomocí JSON. To může snížit velikost datových vytížení až o 75% a může významně zlepšit výkon aplikace.

Další informace najdete v tabulkách post [Microsoft Azure: Představení formátu](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) JSON a [datové části pro operace služby Table Service](https://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Zakázat Nagle

Algoritmus Nagle se v sítích TCP/IP široce implementuje jako prostředek pro zlepšení výkonu sítě. Není ale optimální pro všechny okolnosti (například vysoce interaktivní prostředí). Pro Azure Storage má algoritmus Nagle negativní dopad na výkon požadavků na služby Table a Queue a pokud je to možné, měli byste ho zakázat.

### <a name="schema"></a>Schéma

Způsob reprezentace a dotazování dat je největší jednoduchý faktor, který ovlivňuje výkon služby Table Service. I když je každá aplikace odlišná, Tato část popisuje některé obecné osvědčené postupy, které se týkají:  

* Návrh tabulky
* Efektivní dotazy
* Efektivní aktualizace dat  

#### <a name="subheading27"></a>Tabulky a oddíly

Tabulky jsou rozděleny na oddíly. Každá entita uložená v oddílu sdílí stejný klíč oddílu a má jedinečný klíč řádku k identifikaci v rámci tohoto oddílu. Oddíly poskytují výhody, ale také zavádí omezení škálovatelnosti.  

* Výhody: Entity ve stejném oddílu můžete aktualizovat v jedné, atomické transakci Batch, která obsahuje až 100 samostatných operací úložiště (limit 4 MB celkové velikosti). Za předpokladu, že stejný počet entit, které se mají načíst, můžete také data v rámci jednoho oddílu efektivněji pokládat rychleji než data, která zahrnují oddíly (i když si přečtete další doporučení k dotazování na data tabulky).
* Omezení škálovatelnosti: Přístup k entitám uloženým v jednom oddílu nelze vyrovnávat, protože oddíly podporují atomické transakce dávky. Z tohoto důvodu je cíl škálovatelnosti pro jednotlivý oddíl tabulky nižší než služba Table Service jako celek.  

Z důvodu těchto vlastností tabulek a oddílů byste měli přijmout následující principy návrhu:  

* Data, která klientská aplikace často aktualizovala nebo se dotazují ve stejné logické jednotce práce, by měla být umístěná ve stejném oddílu.  To může být způsobeno tím, že aplikace agreguje zápisy, nebo proto, že chcete využít výhod atomických operací služby Batch.  Data v jednom oddílu taky můžou být efektivněji dotazována v jednom dotazu než data napříč oddíly.
* Data, která klientská aplikace nevloží, neaktualizují ani dotazují ve stejné logické jednotce práce (jeden dotaz nebo aktualizace dávky), by se měla nacházet v samostatných oddílech.  Důležité je upozornit, že počet klíčů oddílů v jedné tabulce není nijak omezený, takže miliony klíčů oddílů nepředstavuje problém a nebude mít vliv na výkon.  Například pokud je vaše aplikace oblíbeným webem s přihlašovacími údaji uživatele, můžete použít ID uživatele, protože klíč oddílu může být dobrou volbou.  

#### <a name="hot-partitions"></a>Aktivní oddíly

Aktivní oddíl je jeden, který přijímá neúměrné procento provozu k účtu a nedá se vyrovnávat zatížení, protože se jedná o jeden oddíl.  Obecně platí, že aktivní oddíly jsou vytvořeny jedním ze dvou způsobů:  

##### <a name="subheading28"></a>Jenom připojit a předřadit jenom vzory

Vzor "pouze pro připojení" je ten, kde se zvyšuje a snižuje objem provozu do daného PK, a to v závislosti na aktuálním čase.  Příkladem je, že vaše aplikace použila aktuální datum jako klíč oddílu pro data protokolu.  Výsledkem je to, že všechna vložení přecházejí na poslední oddíl v tabulce a systém nemůže vyrovnávat zatížení, protože všechny zápisy budou na konci tabulky.  Pokud objem provozu do tohoto oddílu překročí cíl škálovatelnosti na úrovni oddílu, výsledkem bude omezení.  Je lepší zajistit, aby se provoz odesílal do několika oddílů, aby bylo možné vyrovnávat zatížení u požadavků v tabulce.  

##### <a name="subheading29"></a>Data s vysokým provozem

Pokud vaše schéma vytváření oddílů vede k jednomu oddílu, který má pouze data, která jsou mnohem více používána než ostatní oddíly, může se také zobrazit omezení, protože tento oddíl přistupuje k cíli škálovatelnosti pro jeden oddíl.  Je lepší, abyste se ujistili, že vaše schéma oddílu nedosahuje žádného jednoho oddílu, který se blíží cílům škálovatelnosti.  

#### <a name="querying"></a>Dotazování

Tato část popisuje osvědčené postupy pro dotazování služby Table Service.  

##### <a name="subheading30"></a>Obor dotazu

Existuje několik způsobů, jak zadat rozsah entit pro dotaz.  Následuje diskuze o použití jednotlivých.  

Obecně se vyhněte kontrolám (dotazy větší než jedna entita), ale pokud je potřeba je zkontrolovat, zkuste uspořádat data, aby vaše prohledávání načetla potřebná data bez kontroly a vrácení důležitých množství entit, které nepotřebujete.  

###### <a name="point-queries"></a>Dotazy na bod

Dotaz na bod načte přesně jednu entitu. K tomu je potřeba zadat klíč oddílu a klíč řádku entity, která se má načíst. Tyto dotazy jsou efektivní a měli byste je používat všude, kde je to možné.  

###### <a name="partition-queries"></a>Dotazy na oddíly

Dotaz na oddíl je dotaz, který načte sadu dat, která sdílí společný klíč oddílu. Obvykle dotaz určuje rozsah hodnot klíčů řádků nebo rozsah hodnot pro některé vlastnosti entity kromě klíče oddílu. Jsou to méně efektivní než dotazy na body a měly by se používat jenom zřídka.  

###### <a name="table-queries"></a>Dotazy tabulky

Dotaz na tabulku je dotaz, který načte sadu entit, které nesdílejí společný klíč oddílu. Tyto dotazy nejsou efektivní a je třeba se jim vyhnout, pokud je to možné.  

##### <a name="subheading31"></a>Hustota dotazů

Dalším klíčovým faktorem v efektivitě dotazu je počet vrácených entit ve srovnání s počtem zkontrolovaných entit, které prohledaly vrácenou sadu. Pokud vaše aplikace provede dotaz na tabulku s filtrem pro hodnotu vlastnosti, která je jenom 1% sdílených dat, dotaz vyhledá entity 100 pro každou entitu, kterou vrátí. Cíle škálovatelnosti tabulky popsané dříve byly spojeny s počtem prověřených entit a nikoli počtem vrácených entit: nízká hustota dotazu může snadno způsobit omezení aplikace tabulkového oddělení, protože musí prověřit celou řadu entit. Načtěte hledanou entitu.  Další informace o tom, [](#subheading34) jak se tomuto postupu vyhnout, najdete v níže uvedené části o denormalizaci.  

##### <a name="limiting-the-amount-of-data-returned"></a>Omezení množství vrácených dat

###### <a name="subheading32"></a>Jakou

Pokud víte, že dotaz vrátí entity, které v klientské aplikaci nepotřebujete, zvažte použití filtru ke snížení velikosti vrácené sady. I když se entitám, které nejsou vraceny klientovi, stále počítá s omezeními škálovatelnosti, výkon aplikace se zvýší z důvodu snížené velikosti datové části sítě a omezeného počtu entit, které musí klientská aplikace zpracovat.  Viz výše Poznámka o [hustotě dotazů](#subheading31), ale cíle škálovatelnosti se vztahují k počtu prověřených entit, takže dotaz, který filtruje mnoho entit, může i nadále omezovat omezení i v případě, že se vrátí několik entit.  

###### <a name="subheading33"></a>Třetího

Pokud vaše klientská aplikace potřebuje jenom omezenou sadu vlastností z entit v tabulce, můžete použít projekci k omezení velikosti vrácené datové sady. Stejně jako u filtrování to pomáhá snižovat zatížení sítě a zpracování klientů.  

##### <a name="subheading34"></a>Denormalizace

Na rozdíl od práce s relačními databázemi osvědčené postupy pro efektivní dotazování dat tabulek vedou k denormalizaci vašich dat. To znamená, že duplikují stejná data ve více entitách (jeden pro každý klíč, který můžete použít k vyhledání dat), abyste minimalizovali počet entit, které musí dotaz vyhledat, aby se vyhledala data, která potřebuje, a nemuseli kontrolovat velký počet entit, aby bylo možné najít data aplikace. likace potřeby.  Například na webu elektronického obchodování můžete chtít najít objednávku podle ID zákazníka (dát mi objednávky tohoto zákazníka) a podle data (dát mi do objednávky datum).  V Table Storage je nejlepší ukládat entitu (nebo odkaz na ni) dvakrát – jednou s názvem tabulky, PK a vých, aby se usnadnilo hledání podle zákaznického ID, a to jednou, aby bylo snazší ho najít podle data.  

#### <a name="insertupdatedelete"></a>Vložit/aktualizovat/odstranit

Tato část popisuje osvědčené postupy pro úpravu entit uložených ve službě Table Service.  

##### <a name="subheading35"></a>Dávkování

Transakce Batch se označují jako transakce skupin entit (ETG) v Azure Storage; všechny operace v rámci ETG musí být v jednom oddílu v jedné tabulce. Pokud je to možné, použijte ETGs k provádění vkládání, aktualizací a odstraňování v dávkách. Tím se sníží počet zpátečních cest z klientské aplikace na server, což snižuje počet fakturovatelných transakcí (ETG se počítá jako jediná transakce pro účely fakturace a může obsahovat až 100 operací úložiště) a povoluje atomické aktualizace (vše operace jsou úspěšné nebo všechny selžou v rámci ETG. Prostředí s vysokou latencí, jako jsou například mobilní zařízení, budou významně těžit z používání ETGs.  

##### <a name="subheading36"></a>Upsert

Pokud je to možné, používejte **Upsert** operace tabulky. Existují dva typy **Upsert**, z nichž obě můžou být efektivnější než tradiční operace **vkládání** a **aktualizace** :  

* **InsertOrMerge**: Tuto možnost použijte, pokud chcete nahrát podmnožinu vlastností entity, ale nevíte, zda entita již existuje. Pokud entita existuje, toto volání aktualizuje vlastnosti zahrnuté v operaci **Upsert** a ponechá všechny existující vlastnosti tak, jak jsou, pokud entita neexistuje, vloží novou entitu. To se podobá použití projekce v dotazu, v tom, že potřebujete pouze nahrát vlastnosti, které se mění.
* **InsertOrReplace**: Tuto možnost použijte, když chcete nahrát zcela novou entitu, ale nejste si jistí, jestli už existuje. Tuto skutečnost byste měli použít, pouze pokud víte, že nově nahraná entita je zcela správná, protože zcela přepíše starou entitu. Například chcete aktualizovat entitu, která uchovává aktuální umístění uživatele bez ohledu na to, zda aplikace dříve uložila data o umístění pro daného uživatele. nová entita umístění je dokončena a z žádné předchozí entity nepotřebujete žádné informace.

##### <a name="subheading37"></a>Ukládání datových řad do jedné entity

V některých případech aplikace ukládá řadu dat, která často potřebují k načtení všech najednou: aplikace může například sledovat využití CPU v čase, aby bylo možné vykreslit postupný graf dat za posledních 24 hodin. Jedním z možností je mít jednu entitu tabulky za hodinu, přičemž každá entita představuje určitou hodinu a za tuto hodinu bude ukládat využití CPU. Aby bylo možné tato data vykreslit, musí aplikace načíst entity, které mají data v posledních 24 hodinách.  

Alternativně může vaše aplikace uložit využití CPU pro každou hodinu jako samostatnou vlastnost jedné entity: Chcete-li aktualizovat každou hodinu, může aplikace použít jedno volání **Upsert InsertOrMerge** k aktualizaci hodnoty pro poslední hodinu. Aby bylo možné sestavovat data, aplikace potřebuje pouze načíst jednu entitu namísto 24 a vytvořit tak efektivní dotaz (viz část diskuze o [oboru dotazu](#subheading30)).

##### <a name="subheading38"></a>Ukládání strukturovaných dat v objektech blob

Někdy se strukturovaná data často považují za ta, která by měla jít v tabulkách, ale rozsahy entit se vždycky načítají dohromady a dají se vložit do dávky.  Dobrým příkladem je soubor protokolu.  V takovém případě můžete dávkovat několik minut protokolů, vkládat je a pak vždy načítajíme několik minut protokolu současně.  V takovém případě je lepší používat objekty blob místo tabulek, protože můžete významně snížit počet zapsaných nebo vrácených objektů a také obvykle počet požadavků, které je potřeba provést.  

## <a name="queues"></a>Fronty

Kromě osvědčených postupů pro všechny popsané [služby](#allservices) se tyto osvědčené postupy vztahují konkrétně na službu fronty.  

### <a name="subheading39"></a>Omezení škálovatelnosti

Jedna fronta může zpracovávat přibližně 2 000 zpráv (1 KB každý) za sekundu (každou AddMessage, GetMessage a DeleteMessage Count jako zprávu). Pokud je to pro vaši aplikaci nedostatečné, měli byste použít více front a rozprostřít mezi nimi zprávy.  

Prohlédněte si současné cíle škálovatelnosti při [Azure Storage škálovatelnosti a výkonu](storage-scalability-targets.md).  

### <a name="subheading40"></a>Zakázat Nagle

Podívejte se na část týkající se konfigurace tabulky, která se zabývá Nagle algoritmem – Nagle algoritmus je všeobecně špatný pro výkon požadavků front a Vy byste ho měli zakázat.  

### <a name="subheading41"></a>Velikost zprávy

Zmenšení výkonu fronty a škálovatelnosti při zvýšení velikosti zprávy Měli byste umístit jenom informace, které přijímač potřebuje ve zprávě.  

### <a name="subheading42"></a>Dávkové načítání

V jedné operaci můžete načíst až 32 zpráv z fronty. To může snížit počet zpětných přenosů z klientské aplikace, což je zvláště užitečné pro prostředí, jako jsou například mobilní zařízení s vysokou latencí.  

### <a name="subheading43"></a>Interval cyklického dotazování fronty

Většina aplikací se dotazuje na zprávy z fronty, což může být jeden z největších zdrojů transakcí pro danou aplikaci. Výběr intervalu cyklického dotazování v případě, že by dotaz příliš často mohl způsobit, že vaše aplikace bude přistupovat k cílům škálovatelnosti pro danou frontu. V 200 000 transakcích pro $0,01 (v době psaní) se ale jeden procesor dotazuje jednou za měsíc za měsíc, takže náklady nejsou obvykle faktorem, který ovlivňuje zvolený interval cyklického dotazování.  

Aktuální informace o nákladech najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>Aktualizovat zprávu

Pomocí operace **Aktualizovat zprávu** můžete prodloužit časový limit neviditelnosti nebo aktualizovat informace o stavu zprávy. I když je to výkonné, mějte na paměti, že každá operace **zprávy o aktualizaci** se počítá směrem k cíli škálovatelnosti. Může to ale být mnohem efektivnější přístup, než když máte pracovní postup, který projde úlohu z jedné fronty do další, protože je dokončený každý krok úlohy. Pomocí operace **Aktualizovat zprávu** umožňuje aplikaci uložit stav úlohy do zprávy a potom pokračovat v práci, místo aby se zpráva znovu zařadila do fronty pro další krok úlohy pokaždé, když se krok dokončí.  

Další informace najdete v článku [postupy: Změní obsah zprávy](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message)ve frontě.  

### <a name="subheading45"></a>Architektura aplikace

Fronty byste měli použít k zajištění škálovatelnosti architektury aplikace. Následující seznam uvádí některé způsoby, jak můžete používat fronty pro lepší škálovatelnost aplikace:  

* Fronty můžete použít k vytvoření nevyřízených položek práce pro zpracování a vyhlazení úloh ve vaší aplikaci. Můžete například zařadit požadavky uživatelů do fronty, aby prováděly práci náročné na procesor, jako je například změna velikosti nahraných obrázků.
* Pomocí front můžete oddělit části aplikace, abyste je mohli škálovat nezávisle. Webový front-end může například umístit výsledky průzkumu z uživatelů do fronty pro pozdější analýzu a úložiště. V případě potřeby můžete přidat další instance role pracovního procesu pro zpracování dat fronty.  

## <a name="conclusion"></a>Závěr

Tento článek popisuje některé nejběžnější a osvědčené postupy pro optimalizaci výkonu při použití Azure Storage. Všem vývojářům aplikací doporučujeme, aby svoje aplikace vyhodnotili ve světle výše uvedených postupů a zvážili jednotlivá doporučení, která aplikacím využívajícím Azure Storage zajišťují skvělý výkon.
