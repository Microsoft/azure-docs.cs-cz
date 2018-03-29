---
title: Azure Storage výkon a škálovatelnost kontrolní seznam | Microsoft Docs
description: Kontrolní seznam osvědčené postupy pro použití s Azure Storage při vývoji aplikací původce.
services: storage
documentationcenter: ''
author: roygara
manager: jeconnoc
editor: tysonn
ms.assetid: 959d831b-a4fd-4634-a646-0d2c0c462ef8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: rogarana
ms.openlocfilehash: 945289a172270eea56625287baf437fd4b70c7f3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="microsoft-azure-storage-performance-and-scalability-checklist"></a>Kontrolní seznam pro výkon a škálovatelnost Microsoft Azure Storage
## <a name="overview"></a>Přehled
Od vydání služby Microsoft Azure Storage společnost Microsoft vyvinula počet osvědčené postupy pro použití těchto služeb způsobem původce a tento článek slouží pro konsolidaci nejdůležitější z nich v seznamu kontrolní seznam ve stylu. Záměr tohoto článku je, což vývojářům aplikací, ověřte, zda že používají osvědčené postupy s Azure Storage a aby jim pomohl identifikovat ostatní osvědčené postupy zvažte přijetí. Tento článek se nepokusí nepokrývají všechny možné optimalizace výkonu a škálovatelnosti – se vyloučí ty, které jsou v jejich dopad malé nebo široce neplatí. V rozsahu, který můžete předpovědět chování aplikace při návrhu, je vhodné ponechat v paměti již v rané fázi na, aby se zabránilo návrhů, které se spustí do problémy s výkonem.  

Každý vývojář aplikace pomocí Azure Storage má čekat na přečtěte si tento článek a zkontrolujte, že své aplikace dodržovat všechny osvědčené postupy uvedené níže.  

## <a name="checklist"></a>Kontrolní seznam
Tento článek slouží k uspořádání osvědčené postupy do následujících skupin. Osvědčené postupy platí pro:  

* Všechny služby Azure Storage (objekty BLOB, tabulky, fronty a soubory)
* Objekty blob
* Tabulky
* Fronty  

| Hotovo | Oblast | Kategorie | Otázka |
| --- | --- | --- | --- |
| &nbsp; | Všechny služby |Cíle škálovatelnosti |[Je vaše aplikace tak, aby zabránilo blíží cíle škálovatelnosti?](#subheading1) |
| &nbsp; | Všechny služby |Cíle škálovatelnosti |[Zásady vytváření názvů umožňuje lepší vyrovnávání zatížení?](#subheading47) |
| &nbsp; | Všechny služby |Sítě |[Mají klientské straně zařízení dostatečně velkou šířku pásma a nízkou latencí k dosažení výkonu potřeba?](#subheading2) |
| &nbsp; | Všechny služby |Sítě |[Mají klientských zařízení straně odkaz dostatečně vysoký kvality?](#subheading3) |
| &nbsp; | Všechny služby |Sítě |[Klientská aplikace vedle "" účet úložiště?](#subheading4) |
| &nbsp; | Všechny služby |Distribuce obsahu |[Používáte název CDN pro distribuci obsahu?](#subheading5) |
| &nbsp; | Všechny služby |Přímý přístup do klienta |[Používáte umožňující přímý přístup k úložišti místo proxy SAS a CORS?](#subheading6) |
| &nbsp; | Všechny služby |Ukládání do mezipaměti |[Ukládání do mezipaměti data aplikací, který se používá opakovaně a změny je málokdy?](#subheading7) |
| &nbsp; | Všechny služby |Ukládání do mezipaměti |[Je vaše aplikace dávkování aktualizace (je do mezipaměti na straně klienta a pak odesílání větší sad)?](#subheading8) |
| &nbsp; | Všechny služby |Konfigurace rozhraní .NET |[Nakonfigurovali jste vašeho klienta používat dostatečný počet souběžných připojení?](#subheading9) |
| &nbsp; | Všechny služby |Konfigurace rozhraní .NET |[Nakonfigurovali jste .NET použít dostatečný počet vláken?](#subheading10) |
| &nbsp; | Všechny služby |Konfigurace rozhraní .NET |[Používáte rozhraní .NET 4.5 nebo novější, který je vylepšený uvolňování paměti?](#subheading11) |
| &nbsp; | Všechny služby |Paralelismus |[Můžete zajistily paralelismus je tak, že nemáte přetížení klientské možnosti nebo cíle škálovatelnosti ohraničenou správně?](#subheading12) |
| &nbsp; | Všechny služby |Nástroje |[Používáte nejnovější verzi Microsoft klientské knihovny a nástroje jsou poskytovat?](#subheading13) |
| &nbsp; | Všechny služby |Opakování |[Se, že používáte exponenciálního omezení rychlosti zásady omezení chyb a vypršení časových limitů opakování?](#subheading14) |
| &nbsp; | Všechny služby |Opakování |[Je vaše aplikace vyhněte opakování neopakovatelné chyby?](#subheading15) |
| &nbsp; | Objekty blob |Cíle škálovatelnosti |[Máte velký počet klientům přístup k jednoho objektu současně?](#subheading46) |
| &nbsp; | Objekty blob |Cíle škálovatelnosti |[Vaše aplikace nepřekročili cíle škálovatelnosti šířky pásma nebo operací pro jediného objektu blob?](#subheading16) |
| &nbsp; | Objekty blob |Kopírování objektů BLOB |[Můžete kopírování objekty BLOB jsou účinným způsobem?](#subheading17) |
| &nbsp; | Objekty blob |Kopírování objektů BLOB |[Používáte pro hromadné kopie objektů BLOB AzCopy?](#subheading18) |
| &nbsp; | Objekty blob |Kopírování objektů BLOB |[Používáte Azure Import/Export pro přenos velmi velkých objemů dat?](#subheading19) |
| &nbsp; | Objekty blob |Používání metadat |[Jsou ukládání často používaných metadata o objekty BLOB v jejich metadatech?](#subheading20) |
| &nbsp; | Objekty blob |Rychlé nahrávání |[Při pokusu nahrát objekt blob jeden rychle, jsou nahrávání bloky paralelně?](#subheading21) |
| &nbsp; | Objekty blob |Rychlé nahrávání |[Při pokusu o odeslání mnoho objektů BLOB rychle, jsou nahrávání objekty BLOB paralelně?](#subheading22) |
| &nbsp; | Objekty blob |Opravte typu Blob |[Používáte objekty BLOB stránky nebo objekty BLOB bloku v případě nutnosti?](#subheading23) |
| &nbsp; | Tabulky |Cíle škálovatelnosti |[Blíží jsou cíle škálovatelnosti pro entity za sekundu?](#subheading24) |
| &nbsp; | Tabulky |Konfigurace |[Používáte JSON pro své žádosti tabulky?](#subheading25) |
| &nbsp; | Tabulky |Konfigurace |[Jste vypnuli Nagle ke zlepšení výkonu malé požadavků?](#subheading26) |
| &nbsp; | Tabulky |Tabulky a oddíly |[Mít jste správně rozdělena na oddíly svá data?](#subheading27) |
| &nbsp; | Tabulky |Aktivní oddíly |[Zamezení jsou pouze připojení a jen předřazení vzory?](#subheading28) |
| &nbsp; | Tabulky |Aktivní oddíly |[Jsou mnoha oddílů rozloženy vložení nebo aktualizace?](#subheading29) |
| &nbsp; | Tabulky |Obor dotazu |[Byly navrženy schéma umožňující bodu dotazy pro použití ve většině případů a tabulky má být použit pouze?](#subheading30) |
| &nbsp; | Tabulky |Hustotu dotazu |[Proveďte kontrolu obvykle pouze vaše dotazy a vrátí řádky, které vaše aplikace bude používat?](#subheading31) |
| &nbsp; | Tabulky |Omezení vrácená Data |[Používáte filtrování předejdete vrací entity, které nejsou potřebné?](#subheading32) |
| &nbsp; | Tabulky |Omezení vrácená Data |[Aby se zabránilo vracení vlastností, které nejsou potřebné použít projekce?](#subheading33) |
| &nbsp; | Tabulky |Denormalization |[Máte tak, aby se vyhnout neefektivní dotazy nebo víc požadavků na čtení, při pokusu o získání dat nenormalizovanou svá data?](#subheading34) |
| &nbsp; | Tabulky |Insert/Update/Delete |[Jsou můžete dávkování požadavky, které je potřeba mít transakcí, nebo můžete provést ve stejnou dobu ke snížení odezvy?](#subheading35) |
| &nbsp; | Tabulky |Insert/Update/Delete |[Se můžete vyhnout, načítání entity jenom k určení, zda volání vložení nebo aktualizace?](#subheading36) |
| &nbsp; | Tabulky |Insert/Update/Delete |[Zvážení ukládání řadu data, která bude často načten společně v jedné entity jako vlastnosti místo více entit](#subheading37) |
| &nbsp; | Tabulky |Insert/Update/Delete |[Pro entity, které budou načteny vždy společně a může být napsán v dávkách (například časové řady data) ke zvážení použití objektů BLOB místo tabulky](#subheading38) |
| &nbsp; | Fronty |Cíle škálovatelnosti |[Blíží jsou cíle škálovatelnosti zpráv za sekundu?](#subheading39) |
| &nbsp; | Fronty |Konfigurace |[Jste vypnuli Nagle ke zlepšení výkonu malé požadavků?](#subheading40) |
| &nbsp; | Fronty |Velikost zprávy |[Jsou vaše zprávy compact ke zlepšení výkonu fronty?](#subheading41) |
| &nbsp; | Fronty |Hromadné načtení |[Jsou načítání více zpráv v rámci jedné operace "Get"?](#subheading42) |
| &nbsp; | Fronty |Frekvence cyklického dotazování |[Jsou vám dotazování dostatečně často snížit latenci dosahovaný aplikace?](#subheading43) |
| &nbsp; | Fronty |Zpráva aktualizace |[Používáte pro ukládání průběh zpracování zpráv zabraňující museli znovu zpracovat celou zprávu, pokud dojde k chybě UpdateMessage?](#subheading44) |
| &nbsp; | Fronty |Architektura |[Používáte mít větší škálovatelnost celá aplikace tak dlouho běžící úlohy mimo kritické cesty a můžete nezávisle škálovat pak fronty?](#subheading45) |

## <a name="allservices"></a>Všechny služby
Tato část uvádí osvědčené postupy, které platí pro použití služby Azure Storage (objekty BLOB, tabulky, fronty nebo soubory).  

### <a name="subheading1"></a>Cíle škálovatelnosti
Každý služby Azure Storage má cíle škálovatelnosti kapacity (GB), rychlost transakcí a šířky pásma. Pokud vaše aplikace blíží nebo překračuje žádné cíle škálovatelnosti, setkat vyšší transakce latenci nebo omezení. Když úložiště služby omezí generovaný vaší aplikace, služby začne vrátit "503 Server je zaneprázdněný" nebo "500 časový limit operace" kódy chyb pro některé transakce úložiště. Tato část popisuje obecný přístup k zabývající se cíle škálovatelnost a cíle škálovatelnosti šířky pásma na konkrétní. Další oddíly, které pracují s služby jednotlivých úložiště popisují cíle škálovatelnosti v kontextu této konkrétní služby:  

* [Objekt BLOB šířky pásma a počet požadavků za sekundu](#subheading16)
* [Tabulka entity za sekundu](#subheading24)
* [Fronty zpráv za sekundu](#subheading39)  

#### <a name="sub1bandwidth"></a>Cíl škálovatelnost šířky pásma pro všechny služby
V době psaní cílů šířky pásma v USA, pro účet geograficky redundantní úložiště (GRS) jsou 10 gigabity za sekundu (Gbps) pro příjem příchozích dat (dat odeslaných k účtu úložiště) a 20 GB/s pro odchozí (data odeslaná z účtu úložiště). Pro účet místně redundantní úložiště (LRS), omezení jsou vyšší – 20 GB/s pro příjem příchozích dat a 30 GB/s pro odchozí.  Omezení šířky pásma mezinárodní může být nižší a nachází se na naše [stránky cíle škálovatelnosti](http://msdn.microsoft.com/library/azure/dn249410.aspx).  Další informace o možnostech redundance úložiště, najdete v článku odkazy v [užitečné zdroje](#sub1useful) níže.  

#### <a name="what-to-do-when-approaching-a-scalability-target"></a>Co dělat, když se blíží cíl škálovatelnost
Pokud vaše aplikace se blíží cíle škálovatelnosti účtu jedno úložiště, vezměte v úvahu přijetí jednu z následujících postupů:  

* Znovu se zatížením, které způsobí, že aplikace k přístupu nebo překročí cíle škálovatelnosti. Můžete ho jinak na použití menší šířku pásma nebo kapacity nebo méně transakce návrh?
* Pokud aplikace musí být delší než jednoho z cílů škálovatelnost, měli byste vytvořit více účtů úložiště a oddíl data aplikací v těchto více účtů úložiště. Pokud používáte tento vzor, je nutné k návrhu aplikace, abyste mohli přidat další účty úložiště v budoucnosti pro vyrovnávání zatížení. V době psaní každé předplatné Azure může mít až 100 účtů úložiště.  Účty úložiště také mít žádné náklady než vaše využití z hlediska uložených dat, transakcí provedené nebo data přenesená.
* Pokud vaše aplikace dotkne cíle šířky pásma, zvažte, komprese dat v klientovi ke snížení šířky pásma potřebné k odesílání dat služby úložiště.  Všimněte si, že i když to může uložit šířky pásma a zlepšit výkon sítě, může také obsahovat některé negativní dopad.  Byste měli zvážit dopad na výkon to z důvodu dalšího zpracování požadavků pro komprese a dekomprese dat v klientovi. Kromě toho ukládání komprimovaných dat může být obtížné řešení problémů, protože může být obtížné zobrazit uložená data pomocí standardních nástrojů.
* Pokud vaše aplikace dotkne cíle škálovatelnosti, pak se ujistěte, že používáte exponenciálního omezení rychlosti pro opakování (v tématu [opakování](#subheading14)).  Je lepší a ujistěte se, nikdy přístupu cíle škálovatelnosti (pomocí jedné z výše uvedených metod), ale tím bude zajištěno, aby že vaše aplikace nebude právě i nadále pokoušet rychle, provedení omezení horší.  

#### <a name="useful-resources"></a>Užitečné materiály
Následující odkazy obsahují další podrobnosti na cíle škálovatelnosti:

* V tématu [a cíle výkonnosti služby Azure Storage Scalability](storage-scalability-targets.md) informace o cíle škálovatelnosti.
* V tématu [replikace Azure Storage](storage-redundancy.md) a v příspěvku blogu [možnosti redundance úložiště Azure a geograficky redundantní úložiště s přístupem pro čtení](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) informace o možnostech redundance úložiště.
* Aktuální informace o cenách pro služby Azure najdete v tématu [Azure ceny](https://azure.microsoft.com/pricing/overview/).  

### <a name="subheading47"></a>Zásady vytváření názvů oddílu
Azure Storage používá schéma rozdělení oddílů na základě rozsahu vyrovnávání škálování a zatížení systému. Klíč oddílu se používá k oddílu, který data do rozsahů a tyto rozsahy jsou vyrovnávání zatížení sítě v systému. To znamená zásady vytváření názvů jako je například lexikální řazení (např. msftpayroll, msftperformance, msftemployees atd.) nebo pomocí časová razítka (log20160101, log20160102, log20160102 atd.) bude jít o oddíly, které se potenciálně společně umístěný na stejném serveru oddílu, dokud zatížení vyrovnávání operaci rozdělí je na rozsahy menší. Například všech objektů BLOB do kontejneru, je možné dodávat jedním serverem dokud zatížení na tyto objekty BLOB vyžaduje další vyrovnává rozsahy oddílu. Podobně skupinu lehkým načíst účty s jejich názvy uspořádané lexikální pořadí mohou být obsluhovány jeden server, dokud zatížení na jednom nebo všechny tyto účty vyžadují, aby se daly rozdělit mezi několik serverů oddíly. Každé rozložení zátěže operace může mít vliv latence úložiště volání během operace. Systému schopnost zvládat nečekané shluku přenosů dat do oddílu je omezena na škálovatelnost serveru jeden oddíl dokud operace Vyrovnávání zatížení zejména kopance v a znovu vytvoří rovnováhu rozsah klíče oddílu.  

Můžete provést některé z osvědčených postupů ke snížení frekvence těchto operací.  

* Zásady vytváření názvů, který použijete pro účty, kontejnerů, objektů BLOB, tabulek a front, byste měli pečlivě prozkoumejte. Vezměte v úvahu prefixu názvy účtů se hodnota hash 3 číslice pomocí funkce algoritmu hash, který nejlépe vyhovuje vašim potřebám.  
* Pokud můžete uspořádat svá data pomocí funkce časová razítka nebo číselné identifikátory, musíte zajistit, že nepoužíváte vzory přenosů dat připojovacím (nebo jen předřazení). Tyto vzory nejsou vhodné pro rozsah – na základě rozdělení do oddílů systému, a může vést k veškerý provoz, přejdete do jediného oddílu a omezení systému z efektivně Vyrovnávání zatížení. Například pokud máte každodenní operace, které používají objekt blob s časovým razítkem například RRRRMMDD, pak všechny přenosy pro každodenní operace přesměruje na jeden objekt, který obsluhovaného serverem jeden oddíl. Podívejte se na jestli se jednotlivých objektů blob omezení a oddílu omezení podle svých potřeb a vezměte v úvahu rozdělení tuto operaci na více objektů BLOB v případě potřeby. Podobně pokud ukládáte data řady čas v tabulkách, všechny přenosy dat může být přesměruje na poslední část klíče oboru názvů. Pokud musíte použít časová razítka nebo číselné ID, předpona id s hash 3 číslice nebo v případě časová razítka předpony sekund součástí čas, jako je třeba ssyyyymmdd. Pokud výpis a dotazy operace se pravidelně provádí, zvolte funkce algoritmu hash, který omezí vaší počet dotazů. V jiných případech může být náhodné předpony.  
* Pro další informace o schéma rozdělení oddílů použít ve službě Azure Storage, přečtěte si studie sosp [zde](http://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

### <a name="networking"></a>Sítě
Při volání rozhraní API ohledu na to, často omezení fyzické síťové aplikace mít významný dopad na výkon. Následující popisují některá omezení, které uživatelé setkat.  

#### <a name="client-network-capability"></a>Možnost klienta sítě
##### <a name="subheading2"></a>Propustnost
Šířka pásma problém je často možnosti klienta. Například při účet jednoho úložiště může zpracovat 10 GB/s nebo více příjem příchozích dat (viz [cíle škálovatelnosti šířky pásma](#sub1bandwidth)), rychlost sítě v instanci Role pracovního procesu "Malá" Azure je pouze schopná přibližně 100 MB/s. Větší Azure instance mají síťové adaptéry s větší kapacitou, měli byste zvážit použití větší instance nebo více virtuálních počítačů, pokud potřebujete vyšší limity síťové z jednoho počítače. Pokud se přístup k službě úložiště z aplikace na místní, pak platí stejné pravidlo: Seznamte se s možnostmi sítě klientského zařízení a síťové připojení k umístění úložiště Azure a buď zvýšit jejich, protože potřeby nebo návrhu aplikace pro práci v rámci jejich možnosti.  

##### <a name="subheading3"></a>Odkaz kvality
Stejně jako u jakékoli využití sítě, mějte na paměti, že síťové podmínky, což vede k chybám a ztrátě paketů zpomalí efektivní propustnost.  Pomocí WireShark nebo NetMon může pomoci při diagnostice tento problém.  

##### <a name="useful-resources"></a>Užitečné materiály
Další informace o velikosti virtuálních počítačů a přidělené šířky pásma najdete v tématu [velikosti virtuálních počítačů Windows](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) nebo [velikosti virtuálního počítače s Linuxem](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

#### <a name="subheading4"></a>Umístění
V distribuovaném prostředí umístění klienta blízko server poskytuje nejlepší výkon. Pro přístup k úložišti Azure s nejnižší latenci, je nejlepší umístění pro vašeho klienta v rámci stejné oblasti Azure. Například pokud máte webovou stránku Azure, která používá Azure Storage, byste měli vyhledat oba v jedné oblasti (například USA – západ nebo jihovýchodní Asie). Tím se snižuje latence a náklady na – v době psaní, je bezplatná využití šířky pásma v jedné oblasti.  

Pokud váš klient, které aplikace nejsou hostované v rámci Azure (například aplikací pro mobilní zařízení nebo na místní enterprise services), pak znovu umístění účtu úložiště v oblasti blízko zařízení, která bude přistupovat, se obecně sníží latence. Pokud vaši klienti distribuují široce (pro příklad, některé v Severní Americe a některé v Evropě), pak byste měli zvážit použití více účtů úložiště: jeden umístěný v oblasti Severní Ameriky a druhý v Evropského oblast. To vám pomůže snížit latenci pro uživatele v obou oblastí. Tento přístup je obvykle snáze se implementace, pokud aplikace ukládá data specifická pro jednotlivé uživatele a nevyžaduje replikaci dat mezi účty úložiště.  Pro širokou distribuci obsahu doporučuje se název CDN – najdete v části Další podrobnosti.  

### <a name="subheading5"></a>Distribuce obsahu
V některých případech aplikace musí poskytovat stejné obsah na mnoho uživatelů (například produktu ukázkové video použít na domovské stránce webu), umístěný ve stejné nebo více oblastí. V tomto scénáři byste měli používat Content Delivery Network (CDN) jako je například Azure CDN a od CDN by používat úložiště Azure jako počátek data. Na rozdíl od účet úložiště Azure, která existuje v jedné oblasti a který nelze doručování obsahu s nízkou latencí do jiných oblastí používá Azure CDN servery v několika datových centrech po celém světě. Název CDN kromě toho může podporovat obvykle mnohem vyšší limity odchozí než účet jednoho úložiště.  

Další informace o Azure CDN najdete v tématu [Azure CDN](https://azure.microsoft.com/services/cdn/).  

### <a name="subheading6"></a>Pomocí SAS a CORS
Pokud budete potřebovat k autorizaci kódu třeba JavaScript v prohlížeči sítě nebo aplikace na mobilní telefon pro přístup k datům ve službě Azure Storage, jeden z přístupů je použití aplikace v webovou roli jako proxy server: zařízení uživatele ověří webové role, která zase ověří služby úložiště. Tímto způsobem se můžete vyhnout vystavení klíče účtu úložiště na nezabezpečené zařízení. Ale to umístí big režijní náklady na webovou roli protože všech dat přenesených mezi zařízení uživatele a služba úložiště musí projít webovou roli. Používání webové role jako proxy pro službu úložiště pomocí sdíleného přístupového podpisy (SAS), někdy ve spojení s hlavičky sdílení prostředků různých původů (CORS) se můžete vyhnout. Pomocí SAS, můžete povolit na zařízení uživatele provádět požadavky přímo ke službě úložiště prostřednictvím token omezený přístup. Například pokud uživatel chce odeslat fotografii do vaší aplikace, webovou roli můžete vygenerovat a odeslat do zařízení uživatele token SAS, která uděluje oprávnění k zápisu do konkrétní objekt blob nebo kontejner pro další 30 minut (po jejímž tokenu SAS vyprší platnost).

Za normálních okolností neumožní prohlížeče JavaScript na stránce hostované na web jednu doménu pro provádět určité operace, například "Vložit" do jiné domény. Například, pokud hostitelem webovou roli na "contosomarketing.cloudapp.net" a chcete použít nahrát objekt blob do účtu úložiště na "contosoproducts.blob.core.windows.net," v prohlížeči na klientské straně JavaScript "stejné zásady původ" bude nezakazuje tuto operaci. CORS je funkce prohlížeče, která umožňuje cílové doméně (v tomto případě účet úložiště) pro komunikaci se v prohlížeči důvěřovat požadavků pocházejících ve zdrojové doméně (v tomto případě webovou roli).  

Obě tyto technologie vám může pomoct vyhnout nepotřebné zatížení (a kritická místa) na vaši webovou aplikaci.  

#### <a name="useful-resources"></a>Užitečné materiály
Další informace o tokenu SAS naleznete v tématu [sdílené přístupové podpisy, část 1: vysvětlení modelu SAS](../storage-dotnet-shared-access-signature-part-1.md).  

Další informace o CORS, najdete v části [Podpora sdílení prostředků různých původů (CORS) pro služby Azure Storage](http://msdn.microsoft.com/library/azure/dn535601.aspx).  

### <a name="caching"></a>Ukládání do mezipaměti
#### <a name="subheading7"></a>Získání dat
Obecně platí získávání dat ze služby jednou je lepší, než ho získávání dvakrát. Prohlédněte si příklad webová aplikace MVC spuštěna ve webové roli, která již načtena do objektu blob 50MB ze služby storage, která bude sloužit jako obsah pro uživatele. Aplikace může následně načíst tento stejný objekt blob pokaždé, když uživatel požádá, nebo ho může ukládat do mezipaměti je místně na disk a opakovaně používat v mezipaměti verze pro následné požadavky uživatelů. Kromě toho vždy, když uživatel požádá o data, aplikace může dojít, problém s podmíněného hlavičku pro čas změny, které by se tak získání celý objektu blob, pokud byl změněn. Tento stejný vzor můžete použít pro práci s entity tabulky.  

V některých případech se můžete rozhodnout, že vaše aplikace můžete předpokládat, že objektu blob zůstane platný na krátkou dobu, po jejich načtení, a že během tohoto období aplikace nemusí ke kontrole, pokud byla změněna objektu blob.

Konfigurace, vyhledávání a další data, která jsou vždy používá aplikace jsou skvělí kandidáti pro ukládání do mezipaměti.  

Příklad toho, jak získat objekt blob vlastnosti chcete zjistit, datum poslední změny pomocí rozhraní .NET, naleznete v části [sady a vlastnosti načíst a Metadata](../blobs/storage-properties-metadata.md). Další informace o podmíněného soubory ke stažení najdete v tématu [podmíněně aktualizovat místní kopii objektu Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx).  

#### <a name="subheading8"></a>Nahrávání dat v dávkách
V některých případech aplikace můžete agregovat data v místním počítači a pak ho pravidelně nahrajte v dávce místo odesílání každá položka dat okamžitě. Webové aplikace může například zachovat soubor protokolu aktivit: aplikace by buď nahrávat podrobnosti o všech aktivit, jako Odehrává se jako entitu tabulky (který vyžaduje mnoho operace úložiště), nebo může uložte do místního protokolu souboru podrobnosti o aktivitě a pravidelně všechny podrobnosti o aktivitě nahrát jako souboru s oddělovači do objektu blob. Pokud každé položky protokolu 1KB velikostí, můžete nahrát tisícům v rámci jedné transakce "Put Blob" (můžete nahrát objekt blob velikost v rámci jedné transakce až 64MB). Samozřejmě, pokud v místním počítači dojde k chybě před nahrávání, potenciálně ztratíte některá data protokolu: vývojář aplikace musí navrhnout možnost klientské zařízení nebo nahrát selhání.  Pokud data aktivit potřebuje ke stažení pro timespans (pouze jediné aktivity), objekty BLOB se doporučuje namísto tabulky.

### <a name="net-configuration"></a>Konfigurace rozhraní .NET
Pokud používáte rozhraní .NET Framework, této části jsou uvedené několik nastavení rychlé konfigurace, které můžete použít ke zlepšení výkonu.  Pokud používáte další jazyky, zkontrolujte, pokud podobné koncepty použít ve zvoleném jazyce.  

#### <a name="subheading9"></a>Zvyšte výchozí limit připojení
Následující kód v rozhraní .NET, zvyšuje výchozí limitu připojení (což je obvykle 2 v prostředí klienta nebo 10 v prostředí serveru) do 100. Obvykle byste měli nastavit hodnotu přibližně počet vláken, které používá vaše aplikace.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Je nutné nastavit limitu připojení před otevřením všechna připojení.  

Pro jiné programovací jazyky naleznete v dokumentaci k tento jazyk určit, jak se nastavit limit připojení.  

Další informace naleznete v příspěvku blogu [webové služby: souběžných připojení](http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx).  

#### <a name="subheading10"></a>Pokud synchronní kódu pomocí úloh s modifikátorem Async zvětšete fondu vláken Min.
Tento kód se zvýší vláken min fondu vláken:  

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Další informace najdete v tématu [ThreadPool.SetMinThreads metoda](http://msdn.microsoft.com/library/system.threading.threadpool.setminthreads%28v=vs.110%29.aspx).  

#### <a name="subheading11"></a>Uvolňování paměti .NET 4.5, výhod
Pomocí rozhraní .NET 4.5 nebo novější pro klientské aplikace využít k vylepšení výkonu při uvolňování paměti serveru.

Další informace najdete v článku [přehled o vylepšení výkonu v rozhraní .NET 4.5](http://msdn.microsoft.com/magazine/hh882452.aspx).  

### <a name="subheading12"></a>Bez vazby paralelismus
Paralelismus mohou být ideální pro výkon, dávejte pozor, o použití bez vazby paralelismus (bez omezení počtu vláken nebo paralelní požadavky) k odeslání nebo stažení dat, pomocí více pracovníků pro přístup k více oddílů (kontejnery, fronty nebo tabulka oddílů) ve stejném účtu úložiště nebo pro přístup k více položek ve stejném oddílu. Pokud paralelismus bez vazby, vaše aplikace může být vyšší než možnosti klientských zařízení nebo škálovatelnosti účtu úložiště cílem výsledkem delší latence a omezení.  

### <a name="subheading13"></a>Knihovny klienta úložiště a nástroje
Vždy používejte nejnovější Microsoft poskytuje klientské knihovny a nástroje. V době psaní jsou dostupné pro rozhraní .NET, Windows Phone, prostředí Windows Runtime, Java a C++ klientské knihovny, jakož i preview knihovny pro jiné jazyky. Kromě toho společnost Microsoft vydala rutiny prostředí PowerShell a rozhraní příkazového řádku Azure pro práci s Azure Storage. Společnost Microsoft aktivně sama vyvinula tyto nástroje s výkonem na paměti, zajišťuje jejich aktuální pomocí nejnovější verze služby a zajišťuje, že mnoho postupů Principy výkonu se interně zpracovat.  

### <a name="retries"></a>Opakování
#### <a name="subheading14"></a>Throttling/ServerBusy
Služba úložiště v některých případech může omezení aplikace nebo může být jednoduše nemůže obsluhovat požadavek, protože některé přechodného stavu a vrátíte se zpráva "503 Server je zaneprázdněný" nebo "500 časový limit".  Tomu může dojít, pokud vaše aplikace se blíží některé z cíle škálovatelnosti, nebo pokud je systém vyrovnává oddílů dat, aby se povolit pro vyšší propustnost.  Klientská aplikace by měl obvykle opakujte operaci, kterou způsobí, že takové chyby: Pokus stejné žádost později mohlo být úspěšné. Ale pokud službu úložiště je omezení aplikace, protože přesahuje cíle škálovatelnosti, nebo i v případě, že služba nemohla obsluhovat žádosti z jiného důvodu, agresivní opakování obvykle provést nejhorší problém. Z tohoto důvodu byste měli používat exponenciální zpět vypnuto (knihovny výchozí klienta pro toto chování). Vaše aplikace může například opakovat po 2 sekundy, pak 4 a víc sekund, a 10 sekund a potom 30 sekund a úplně uvolňovat. Toto chování je výsledkem aplikace výrazně snížit jeho zátěž služby spíše než ke zhoršení potíže.  

Všimněte si, že k chybám připojení můžete opakovat okamžitě, protože nejsou výsledek omezení a se očekává, že být dočasné.  

#### <a name="subheading15"></a>Neopakovatelné chyby
Knihovny klienta jsou vědět, které chyby jsou možné opakovat a které nejsou. Pokud vytváříte vlastní kód pro REST API úložiště, pamatujte si však některé chyby, které by neměly opakujte: například 400 (Chybný požadavek) v odpovědi označuje, že klientská aplikace odesláno požadavek, který nebylo možné zpracovat, protože nebyla ve formuláři očekávané. Odešlete tento požadavek, bude stejná odpověď pokaždé, když, proto se nenachází žádný bod v jeho opakování. Pokud vytváříte vlastní kód pro REST API úložiště, uvědomte si, co znamená kódy chyb a správný způsob, jak můžete opakovat (nebo ne) pro každý z nich.  

#### <a name="useful-resources"></a>Užitečné materiály
Další informace o chybových kódech úložiště najdete v tématu [stavu a kódy chyb](http://msdn.microsoft.com/library/azure/dd179382.aspx) na webu Microsoft Azure.  

## <a name="blobs"></a>Objekty blob
Kromě osvědčené postupy pro [všechny služby](#allservices) popsané, následující osvědčené postupy, jako se týkají konkrétně služby objektů blob.  

### <a name="blob-specific-scalability-targets"></a>Cíle škálovatelnosti specifické objektů BLOB
#### <a name="subheading46"></a>Víc klientů současně přístup k jednoho objektu.
Pokud máte velký počet klientů přistupovat k jednoho objektu souběžně musíte vzít v úvahu na objekt a úložiště cíle škálovatelnosti účtu. Přesný počet klientů, kteří mohou přistupovat k jednoho objektu budou lišit v závislosti na faktorech, například počet klienty žádající objekt současně, velikost objektu, síťové podmínky atd.

Pokud objekt lze distribuovat pomocí zpracování CDN například obrázky nebo videa z webu potom byste měli použít název CDN. V tématu [zde](#subheading5).

V dalších scénářích, třeba scientific simulace, kde jsou důvěrné máte dvě možnosti. První je rozložte vaše úlohy přístup tak, že během období čas vs přistupuje současně získat přístup k objektu. Alternativně můžete dočasně kopírovat objekt k několika účtům úložiště a zvýšit tak celkový počet IOPS podle objektu a mezi různými účty úložiště. V omezené testování bylo zjištěno, že přibližně 25 virtuálních počítačů může současně stáhnout objekt blob 100GB paralelně (každý virtuální počítač byl paralelním prováděním stahování použití 32 vláken). Pokud jste měli 100 klienty, kteří potřebují přístup k objektu, nejdříve ho zkopírovat do druhého účtu úložiště a pak mít první 50 virtuální počítače přístup k první objekt blob a druhý 50 virtuálních počítačů k druhý objekt blob. Výsledky se liší podle chování vaší aplikace, takže byste měli otestovat při návrhu. 

#### <a name="subheading16"></a>Operace na objektu Blob a šířky pásma
Můžou číst nebo zapisovat do jediného objektu blob v až do maximálního počtu 60 MB za sekundu (Toto je přibližně 480 MB/s překročený možnosti mnoho sítí straně klienta (včetně fyzický síťový adaptér v klientském zařízení). Kromě toho jediného objektu blob podporuje až 500 požadavků za sekundu. Pokud máte více klientů, které je třeba číst stejný objekt blob a může tato omezení překročí, měli byste zvážit použití název CDN pro distribuci objektu blob.  

Další informace o cílové propustnost pro objekty BLOB najdete v tématu [a cíle výkonnosti služby Azure Storage Scalability](storage-scalability-targets.md).  

### <a name="copying-and-moving-blobs"></a>Kopírování a přesouvání objektů BLOB
#### <a name="subheading17"></a>Zkopírování objektu Blob
REST API verze 2012-02-12 úložiště zavedená užitečné možnost kopírovat objekty BLOB na účty: klientské aplikace můžete nastavit, aby služba úložiště zkopírovat objekt blob z jiného zdroje (například ve jiný účet úložiště) a nechat službu provést kopie asynchronně. To může výrazně snížit šířku pásma potřebné pro aplikace, pokud jsou migraci dat z jiné účty úložiště, protože není potřeba stáhnout a odeslat data.  

Jednou z těchto okolností, ale je, že při kopírování souborů mezi účty úložiště, je možné, čas na při kopírování dokončí. Pokud aplikace potřebuje k dokončení kopírování objektu blob rychle ve vaší kontrole, je lepší kopírování objektu blob stahování do virtuálního počítače a odesílá je do cílového umístění.  Pro úplnou předvídatelnost v takovém případě Ujistěte se, že provádí kopie virtuálního počítače s ve stejné oblasti Azure, jinak se síťové podmínky může (a pravděpodobně bude) ovlivnit výkon kopírování.  Kromě toho můžete sledovat průběh asynchronní kopírování prostřednictvím kódu programu.  

Všimněte si, že kopie v rámci stejného účtu úložiště samotné jsou obecně dokončit rychle.  

Další informace najdete v tématu [kopírovat objekt Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx).  

#### <a name="subheading18"></a>Pomocí nástroje AzCopy
Tým služby Azure Storage vydala nástroj příkazového řádku "AzCopy", měl by vám pomoct s hromadného přenosu mnoho objektů BLOB do z a mezi různými účty úložiště.  Tento nástroj je optimalizovaná pro tento scénář a můžete dosáhnout vysoké přenosové rychlosti.  Pro hromadné odesílání, stahování a scénáře kopírováním je podporováno jeho používání. Další informace o něm a stažení najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md).  

#### <a name="subheading19"></a>Služba Azure Import/Export
Pro velmi velké objemy dat (více než 1TB) Azure Storage nabízí službu Import/Export, která umožňuje odesílání a stahování z úložiště objektů blob podle přenosů pevné disky.  Můžete uvést vaše data na pevném disku a odešle společnosti Microsoft pro odeslání nebo odesílat společnosti Microsoft pro stahování dat prázdné pevný disk.  Další informace najdete v tématu [používat službu Microsoft Azure Import/Export přenos dat do úložiště objektů Blob](../storage-import-export-service.md).  To může být mnohem efektivnější než nahrávání nebo stahování tento svazek dat přes síť.  

### <a name="subheading20"></a>Používání metadat
Služba objektů blob podporuje head požadavků, které mohou zahrnovat metadata o objektu blob. Například v případě potřeby EXIF dat mimo fotografie aplikace ho může načíst fotografie a rozbalte ho. Uložte šířky pásma a zvýšit výkon, vaše aplikace může EXIF data ukládat do metadata objektu blob při odeslání aplikace fotografie: můžete pak načíst žádosti EXIF dat v metadatech pomocí pouze HEAD, ukládání významné šířky pásma a doba zpracování potřebné k extrakci EXIF dat jednotliví čas objektu blob je pro čtení. To může být užitečný ve scénářích, kde potřebujete pouze metadata a ne celý obsah objektu blob.  Všimněte si, pouze 8 KB metadat mohou být uloženy na objekt blob (služba nebude přijímat žádosti na úložišti víc než který), takže pokud data v této velikosti nevejde, nebudete moci tuto metodu použijte.  

Příklad, jak získat objekt blob metadat pomocí rozhraní .NET, naleznete v části [sady a vlastnosti načíst a Metadata](../blobs/storage-properties-metadata.md).  

### <a name="uploading-fast"></a>Rychlé nahrávání
Rychlé nahrát objekty BLOB, je první otázku odpovědět: jste odesílání jeden objekt blob nebo mnoho?  Použít níže pokyny k určení správné metoda používat v závislosti na vašem scénáři.  

#### <a name="subheading21"></a>Rychle odesílání jednoho velkého objektu blob
Pokud chcete rychle nahrát jediného velkého objektu blob, měli klientské aplikace nahrávat stránkách paralelně (Probíhá s vědomím cíle škálovatelnosti pro jednotlivé objekty BLOB a účet úložiště jako celek) nebo jeho bloky.  Všimněte si, že oficiální poskytovaný společností Microsoft RTM úložiště klientské knihovny (.NET, Java) mají možnost to udělat.  Pro každou z knihoven, použijte níže zadaný objekt nebo vlastnost nastavit úroveň souběžnosti:  

* Rozhraní .NET: Sada ParallelOperationThreadCount na objekt BlobRequestOptions má být použit.
* Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
* Node.js: Použijte parallelOperationThreadCount na možnosti požadavku nebo služby objektů blob.
* C++: Použijte metodu blob_request_options::set_parallelism_factor.

#### <a name="subheading22"></a>Rychlé nahrávání mnoho objektů BLOB
Pokud chcete rychle nahrát mnoho objektů BLOB, nahrajte objekty BLOB paralelně. Toto je rychlejší než odesílání jeden objekty BLOB současně s paralelní blok nahrávání protože šíří se nahrávání napříč více oddílů služby úložiště. Jediného objektu blob podporuje pouze propustnost 60 MB za sekundu (přibližně 480 MB/s). V době psaní účet na základě USA LRS podporuje až 20 GB/s příjem příchozích dat, což je mnohem víc než propustnost nepodporuje jednotlivých objektů blob.  [AzCopy](#subheading18) ve výchozím nastavení provádí nahrávání paralelně a doporučuje se pro tento scénář.  

### <a name="subheading23"></a>Výběr správného typu objektu blob
Úložiště Azure podporuje dva typy objektů blob: *stránky* objekty BLOB a *bloku* objekty BLOB. Pro scénář dané využití zvoleného typu Objekt blob ovlivní výkon a škálovatelnost řešení. Objekty BLOB bloku jsou vhodné, pokud chcete nahrát velké objemy dat efektivně: například klientskou aplikaci muset nahrát fotografie nebo video do úložiště objektů blob. Objekty BLOB stránky jsou vhodné, pokud aplikace potřebuje k provedení náhodné zápisy na základě dat: například Azure virtuální pevné disky jsou uložené jako objekty BLOB stránky.  

Další informace najdete v tématu [Principy objekty BLOB bloku a doplňovacích objektů BLOB, objekty BLOB stránky](http://msdn.microsoft.com/library/azure/ee691964.aspx).  

## <a name="tables"></a>Tabulky
Kromě osvědčené postupy pro [všechny služby](#allservices) popsané, následující osvědčené postupy, jako se týkají konkrétně služby table.  

### <a name="subheading24"></a>Cíle škálovatelnosti specifické pro tabulky
Kromě omezení šířky pásma celý účet úložiště tabulky mají následující omezení konkrétní škálovatelnost.  Všimněte si, že systém bude Vyrovnávání zatížení jako vaše zvýšení provozu, ale pokud provozu nečekané shluky, nemusí být možné získat tento svazek propustnost okamžitě.  Pokud vaše vzor shluky, měli byste měli vidět omezení nebo vypršení časových limitů během shluků jako úložiště služby automaticky vyrovnává zatížení na tabulku.  Ramping až pomalu obecně má lepší výsledky, protože nabízí systémového času pro vyrovnávání zatížení správně.  

#### <a name="entities-per-second-account"></a>Entity za sekundu (účet)
Škálovatelnost limit pro přístup k tabulky je až 20 000 entity (1KB každé) za sekundu pro účet.  Obecně platí každá entita, která je vložit, aktualizovat, odstranit nebo zkontrolovat počty směrem k této cílové.  Proto by dávkové vložení, který obsahuje 100 entit počítají jako 100 entit.  Dotaz, který vyhledá 1000 entity a vrátí 5 by se počítají jako 1000 entity.  

#### <a name="entities-per-second-partition"></a>Entity za sekundu (oddíl)
Škálovatelnost cíl pro přístup k tabulky v rámci jednoho oddílu, je 2 000 entity (1KB každé) za sekundu, pomocí stejné inventura, jak je popsáno v předchozí části.  

### <a name="configuration"></a>Konfigurace
Tato část obsahuje několik nastavení rychlé konfigurace, které můžete použít k významné zlepšení výkonu služby table.  

#### <a name="subheading25"></a>Použití JSON
Počínaje verzí služby úložiště 2013-08-15, služby table podporuje používání JSON místo formát založený na jazyce XML AtomPub pro přenos dat v tabulce. To může snížit velikost datové části tím, co nejvíce 75 % a může výrazně zlepšit výkon vaší aplikace.

Další informace najdete v příspěvku [tabulek Microsoft Azure: Úvod JSON](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) a [Formát datové části pro operace služby s tabulkou](http://msdn.microsoft.com/library/azure/dn535600.aspx).

#### <a name="subheading26"></a>Nagle vypnuto
Na Nagle algoritmus je široce implementována v sítích TCP/IP jako prostředek k zlepšit výkon sítě. Však není optimální za všech okolností (například vysoce interaktivní prostředí). Pro Azure Storage algoritmus je Nagle má negativní dopad na výkon žádostí o služby tabulky a fronty a zakažte je-li to možné.  

Další informace najdete v tématu naše blogu [na Nagle algoritmus není popisný směrem malé požadavky](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx), která vysvětluje, proč je Nagle algoritmus špatně komunikuje s tabulky a fronty požadavků a ukazuje, jak zakázat v klientské aplikace.  

### <a name="schema"></a>Schéma
Jak představují a dotazování na data je největších jeden faktor, který má vliv na výkon služby table. Při každá aplikace, se liší, tato část popisuje některé obecné osvědčené postupy, které se vztahují k:  

* Návrh tabulky
* Efektivní dotazy
* Aktualizace efektivní dat  

#### <a name="subheading27"></a>Tabulky a oddíly
Tabulky jsou rozděleny do oddílů. Každou entitu uložená v oddílu sdílí stejný klíč oddílu a má řádek jedinečný klíč pro jeho rozpoznání v rámci oddílu. Oddíly výhody, ale také zavádět omezení škálovatelnosti.  

* Výhody: Můžete aktualizovat entity v stejného oddílu v jedinou atomic, batch transakci, která obsahuje až 100 operace samostatné úložiště (limit je 4MB, celková velikost). Za předpokladu, že stejný počet entit, které mají být načteny, se můžete dotazovat i dat v rámci jednoho oddílu efektivnější než data, která rozdělena na oddíly (přestože přečtěte si další doporučení na dotazování na data tabulky).
* Limit škálovatelnosti: přístup k entity, které jsou uložené v jednom oddílu nesmí být vyrovnávání zatížení sítě vzhledem k tomu oddíly podporují atomic dávkové transakce. Z tohoto důvodu škálovatelnost cíl pro jednotlivé tabulky oddíl je nižší než pro službu tabulky jako celek.  

Z důvodu tyto charakteristiky tabulek a oddíly musí přijmout tyto zásady:  

* Data, která klientské aplikace často aktualizovat nebo spustit dotaz ve stejné logické jednotky práce se musí nacházet ve stejném oddílu.  To může být, protože aplikace je agregování zápisy, nebo protože budete chtít využít výhod atomic dávkových operací.  Data v jednom oddílu může být efektivněji dotazována také v jednom dotazu než dat napříč oddíly.
* Data, která klientské aplikace není insert nebo update nebo dotaz, ve stejné logické jednotky práce (jeden dotaz nebo aktualizace batch) by měl být umístěn v samostatné oddíly.  Důležité OneNote je, že neexistuje žádné omezení počtu klíče oddílů v jediné tabulce, tak s miliony klíče oddílů se nejedná o problém a nebude mít vliv na výkon.  Například pokud vaše aplikace je oblíbené webové stránky s přihlášení uživatele, pak pomocí Id uživatele jako klíč oddílu může být dobrou volbou.  

#### <a name="hot-partitions"></a>Aktivní oddíly
Aktivní oddíl je ten, který přijímá nesoustředil příliš velký procento provozu na účet, a nemůže být s vyrovnáváním zatížení se vzhledem k tomu, že je jeden oddíl.  Obecně platí aktivní oddíly jsou vytvořeny jedním ze dvou způsobů:  

##### <a name="subheading28"></a>Připojit pouze a předřazení pouze vzorce
Vzor "Pouze přidat" je jedním kde všechny (nebo téměř všechny) provozu do daného Primárníklíč zvyšuje a snižuje podle aktuální čas.  Příkladem je, pokud vaše aplikace používá jako klíč oddílu pro data protokolu aktuální datum.  Výsledkem je všechny probíhající operace INSERT na poslední oddíl v tabulce a systému nedá vyrovnávat zatížení, protože všechny zápisů budou na konec tabulky.  Pokud svazek provozu pro tento oddíl je větší než cílový oddíl úrovni škálovatelnost, je výsledkem omezení.  Je lepší zajistit, že se odesílá do více oddílů, chcete-li povolit nástroj pro vyrovnávání zatížení žádosti napříč tabulku.  

##### <a name="subheading29"></a>Data vysokým provozem
Pokud vaše schéma rozdělení oddílů výsledkem jeden oddíl, který má jenom data, která je mnohem víc než ostatní oddíly, může se také zobrazit jako omezení, že oddíl blíží cíle škálovatelnosti pro jeden oddíl.  Je lepší, abyste měli jistotu, že vaše schéma oddílu výsledky v žádné jednoho oddílu blíží cíle škálovatelnosti.  

#### <a name="querying"></a>Dotazování
Tato část popisuje osvědčené postupy pro dotazování služby table.  

##### <a name="subheading30"></a>Obor dotazu
Určete rozsah entity k dotazu několika způsoby.  Následuje popis používá jednotlivých.  

Obecně platí Vyhněte se kontroly (dotazy větší než jedna entita), ale pokud je nutné skenování, zkuste organizování vašich dat, aby kontrolách načítat data, která potřebujete bez kontrola nebo vrácení poměrně velké množství entity, které nepotřebujete.  

###### <a name="point-queries"></a>Bod dotazy
Bod dotaz načte přesně jednu entitu. Dělá to pomocí klíč oddílu a klíč řádku entity načíst. Tyto dotazy jsou velmi efektivně a jejich použití je to možné.  

###### <a name="partition-queries"></a>Oddíl dotazy
Dotaz oddílu je dotaz, který načte sadu dat, který sdílí společný klíč oddílu. Dotaz určuje obvykle rozsahu hodnot řádku klíče nebo rozsah hodnot pro vlastnost některé entity kromě klíč oddílu. Tyto jsou míň efektivní než bodu dotazy a měli šetřit.  

###### <a name="table-queries"></a>Tabulku dotazů
Dotaz na tabulku je dotaz, který načte sada entit, které nesdílí běžné klíč oddílu. Tyto dotazy nejsou efektivní a neměli byste je-li to možné.  

##### <a name="subheading31"></a>Hustotu dotazu
Dalším klíčovým faktorem efektivitu dotazu je počet entit, vrátila ve srovnání se počet entit, zkontrolovat najít sadu vrácená. Pokud vaše aplikace provede dotaz na tabulku s filtrem pro hodnotu vlastnosti, pouze 1 % dat sdílené složky, dotaz vyhledá 100 entit pro každý jedna entita, kterou vrátí. Cíle škálovatelnosti tabulky popisovaný dříve všechny se týkají počet entit, zkontrolovat a ne počet entit, vrátila: hustotu nízkou dotaz může způsobit snadno služby table pro omezení aplikace, protože se musí kontrolovat mnoho entit k načtení entity, které hledáte.  Projděte si část níže na [denormalization](#subheading34) Další informace o tom, jak zamezit to.  

##### <a name="limiting-the-amount-of-data-returned"></a>Omezení množství dat vrácených
###### <a name="subheading32"></a>Filtrování
Pokud jste si jisti, že dotaz vrátí entit, které nepotřebujete v aplikaci klienta, vzít v úvahu pomocí filtru snížit velikost vrácený sady. Při entity není vrácen do klienta se stále započítává k omezení škálovatelnosti, bude z důvodu velikosti datové části snížené sítě a menší počet entit, které je nutné zpracovat klientské aplikace zlepšit výkon aplikace.  Viz výše Poznámka na [dotazu hustotu](#subheading31), ale – cíle škálovatelnosti souvisí počet entit, zkontrolovat, takže dotaz, který filtruje mnoho entit může stále mít za následek omezení, i když jsou vráceny několik entit.  

###### <a name="subheading33"></a>Projekce
Pokud klientské aplikace potřebuje pouze omezenou sadu vlastnosti z entit v tabulce, můžete k omezení velikosti sady vrácená data projekce. Stejně jako u filtrování, to pomáhá snížit zatížení sítě a zpracování na straně klienta.  

##### <a name="subheading34"></a>Denormalization
Na rozdíl od práce relačních databází, osvědčené postupy pro efektivní dotazování na data tabulky vést k denormalizing vaše data. To znamená duplikování stejná data ve více entit (jeden pro každý klíč, který můžete použít k vyhledání dat) Chcete-li minimalizovat počet entit, které musí dotaz prohledat najít data klienta musí namísto nutnosti kontrolovat velký počet entit najít data aplikace musí.  Například v webem elektronického obchodu může chcete najít řazení obou podle ID zákazníka (mě objednávky tohoto zákazníka) a k datu (mě objednávky na datum).  Ve službě Table Storage, je nejvhodnější pro ukládání entit (nebo odkaz na jeho) dvakrát – jednou s názvem tabulky Primárníklíč a pracovní usnadňuje hledání podle zákazníka ID, jednou pro usnadnění hledání k datu.  

#### <a name="insertupdatedelete"></a>Insert/Update/Delete
Tato část popisuje osvědčené postupy pro úpravy entit, které jsou uložené ve službě table.  

##### <a name="subheading35"></a>Dávkování
Dávkové transakce jsou známé jako Entity skupiny transakce (ETG) ve službě Azure Storage; všechny operace v rámci ETG musí být na jeden oddíl v jediné tabulce. Pokud je to možné, použijte ETGs provést vložení, aktualizace a odstranění v dávkách. To snižuje počet zpátečních cest z klientské aplikace na server, sníží počet fakturovatelný transakce (ETG počítá jako jediná transakce pro účely fakturace a může obsahovat až 100 operace úložiště) a umožňuje atomic aktualizace (všechny operace úspěšné nebo všechny selhání v rámci ETG). Prostředí s vysokou latencí, jako jsou například mobilní zařízení budou využívat výrazně ETGs.  

##### <a name="subheading36"></a>Upsert
Pomocí tabulky **Upsert** operations kdykoli je to možné. Existují dva typy **Upsert**, které může být efektivnější než tradiční **vložit** a **aktualizace** operace:  

* **InsertOrMerge**: to použijte, pokud chcete nahrát podmnožinu vlastností entity, ale nejste si jisti, zda entita již existuje. Pokud entita existuje, toto volání aktualizuje vlastnosti, které jsou součástí **Upsert** operaci nebo je opustí všechny existující vlastnosti protože se jedná, pokud entita neexistuje, vloží novou entitu. Toto je podobný používání projekce v dotazu, v tom, že potřebujete nahrát vlastnosti, které mění.
* **InsertOrReplace**: to použijte, pokud chcete nahrát zcela nové entity, ale nejste si jisti, zda již existuje. Tento postup byste měli používat jenom v, když víte, nově nahraném entity je zcela správné, protože úplně přepíše původní entita. Například chcete aktualizovat typ entity, která ukládá aktuální umístění uživatele bez ohledu na to, zda aplikace dříve uložil data o umístění pro uživatele. Nová entita umístění je dokončena a není nutné žádné informace z předchozích entitu.

##### <a name="subheading37"></a>Ukládání datových řad v jedné Entity
V některých případech aplikace ukládá řadu data, která je často nutné načíst všechny najednou: například aplikace může sledovat využití procesoru v čase k vykreslení grafu postupného dat z posledních 24 hodin. Jeden z přístupů je tak, aby měl jeden entitu tabulky za hodinu, s každé entity představující konkrétní hodinu a ukládání využití procesoru pro danou hodinu. K vykreslení tato data, aplikace musí pro načtení entit, který obsahuje data z za 24 hodin nejnovější.  

Alternativně může vaše aplikace ukládat využití procesoru pro každou hodinu jako samostatné vlastnost jedné entity: aktualizace každou hodinu, může aplikace pomocí jednoho **InsertOrMerge Upsert** volání aktualizujte hodnotu za poslední hodinu. K vykreslení data, aplikace potřebuje pouze k načtení jedné entity místo 24, díky velmi efektivní dotazu (viz výše diskuzi na [dotaz oboru](#subheading30)).

##### <a name="subheading38"></a>Ukládání strukturovaných dat do objektů BLOB
Někdy strukturovaných dat funguje stejně, jako by měl přejděte v tabulkách, ale rozsahy entit se vždy načítají společně a lze vložit batch.  Dobrým příkladem toho je soubor protokolu.  V takovém případě může několik minut protokolů služby batch, vložte je a pak se vždy načítání najednou i několik minut protokolů.  V takovém případě pro výkon, je lepší použít objekty BLOB místo tabulky, protože může výrazně snížit počet objektů, které jsou zapsány nebo vrátí, a také obvykle počet požadavků, které je třeba vytvořit.  

## <a name="queues"></a>Fronty
Kromě osvědčené postupy pro [všechny služby](#allservices) popsané, následující osvědčené postupy, jako platí konkrétně pro služby front.  

### <a name="subheading39"></a>Omezení škálovatelnosti
Jednou frontou může zpracovat přibližně 2 000 zprávy (1KB každé) za sekundu (každý AddMessage, GetMessage a DeleteMessage počet jako zprávu sem). Pokud to není dostatečné pro vaši aplikaci, musí používat více fronty a zprávy rozloženy je.  

Zobrazit aktuální cíle škálovatelnosti v [a cíle výkonnosti služby Azure Storage Scalability](storage-scalability-targets.md).  

### <a name="subheading40"></a>Nagle vypnuto
Projděte část o konfigurace tabulky, který popisuje algoritmus Nagle – algoritmus Nagle je obecně chybný pro výkon fronty požadavků, a zakažte je.  

### <a name="subheading41"></a>Velikost zprávy
Fronty neklesne jako zvyšuje velikost zprávy výkon a škálovatelnost. Měli byste umístit pouze informace nutné k příjemce ve zprávě.  

### <a name="subheading42"></a>Načtení batch
Až 32 zprávy můžete načíst z fronty v rámci jedné operace. To může snížit počet výměn dat z klientské aplikace, který je obzvláště užitečná v prostředích, jako jsou například mobilní zařízení, s vysokou latencí.  

### <a name="subheading43"></a>Interval dotazování fronty
Většina aplikací dotazování na zprávy z fronty, která může být jedna z největší zdrojů transakcí pro tuto aplikaci. Vyberte vaše interval dotazování dobře: dotazování příliš často může způsobit, že aplikace k přístupu cíle škálovatelnosti pro frontu. Ale v 200 000 transakce $0,01 (v době psaní), jeden procesor dotazování po méně než 15 centů tak náklady a náklady za sekundu dobu jednoho měsíce není obvykle faktor, který má vliv na vaši volbu interval dotazování.  

Náklady na aktuální informace najdete v tématu [Azure Storage – ceny](https://azure.microsoft.com/pricing/details/storage/).  

### <a name="subheading44"></a>UpdateMessage
Můžete použít **UpdateMessage** zvýšit časový limit neviditelnosti nebo aktualizovat informace o stavu zprávy. To je výkonný, mějte na paměti, aby se každý **UpdateMessage** operace, se započítává cíle škálovatelnosti. To však může být mnohem efektivnější přístup, než má pracovní postup, který předá úlohu z jedné fronty na další po dokončení každého kroku úlohy. Pomocí **UpdateMessage** operace umožňuje vaší aplikace, uložte stav úlohy na zprávu a poté pokračovat v práci, namísto znovu služby Řízení front zpráv pro další krok úlohy pokaždé, když se krok je dokončen.  

Další informace najdete v článku [postupy: Změna obsahu zpráv zařazených ve frontě](../queues/storage-dotnet-how-to-use-queues.md#change-the-contents-of-a-queued-message).  

### <a name="subheading45"></a>Architektura aplikace
Měli byste použít fronty aby škálovatelná architektura vaší aplikace. V následujícím seznamu uvedeny některé možnosti použití fronty aby vaše aplikace větší škálovatelnost:  

* Fronty můžete vytvořit nevyřízené položky práce pro zpracování a vyhlazení úlohy v aplikaci. Například může fronty požadavků ze uživatelé provádět náročné práce procesoru jako je například změna velikosti obrázků nahraný.
* Fronty můžete oddělit části aplikace, takže je možné škálovat nezávisle. Například by mohlo způsobit webového front-endu výsledky zjišťování od uživatelů do fronty pro pozdější analýzu a úložiště. Můžete přidat další instance rolí pracovního procesu pro zpracování dat fronty podle potřeby.  

## <a name="conclusion"></a>Závěr
Tento článek popsané, některé z nejběžnějších, osvědčené postupy pro optimalizaci výkonu při použití služby Azure Storage. Všem vývojářům aplikací doporučujeme, aby svoje aplikace vyhodnotili ve světle výše uvedených postupů a zvážili jednotlivá doporučení, která aplikacím využívajícím Azure Storage zajišťují skvělý výkon.