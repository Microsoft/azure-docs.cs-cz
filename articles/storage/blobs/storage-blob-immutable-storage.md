---
title: Neměnné úložiště objektů blob – Azure Storage
description: Azure Storage nabízí worm (write once, read many) podporu pro úložiště objektů Blob (objekt), který umožňuje uživatelům ukládat data v nesmazatelném, neupravitelném stavu po zadaný interval.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: a980c7bd068a463956191eece43ec1be233e7890
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367614"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Ukládání důležitých dat objektů blob s neměnným úložištěm

Neměnné úložiště pro úložiště objektů blob Azure umožňuje uživatelům ukládat důležité podnikové datové objekty ve stavu WORM (Write Once, Read Many). Tento stav umožňuje data nesympatizatelné a neupravitelné pro interval zadaný uživatelem. Po dobu trvání intervalu uchování objekty BLOB lze vytvořit a číst, ale nelze je změnit ani odstranit. Neměnné úložiště je k dispozici pro účty pro obecné účely v1, pro obecné účely v2, BlobStorage a BlockBlobStorage ve všech oblastech Azure.

Informace o tom, jak nastavit a vymazat právní blokování nebo vytvořit zásady uchovávání informací na základě času pomocí portálu Azure, PowerShellu nebo Azure CLI, najdete v [tématu Nastavení a správa zásad neměnnosti pro úložiště objektů Blob](storage-blob-immutability-policies-manage.md).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="about-immutable-blob-storage"></a>Neměnné úložiště objektů Blob

Neměnné úložiště pomáhá zdravotnické organizaci, finančním&mdash;institucím a&mdash;souvisejícím odvětvím, zejména organizacím makléřů a prodejcům bezpečně ukládat data. Neměnné úložiště lze také využít v libovolném scénáři k ochraně důležitých dat před úpravou nebo odstraněním.

Mezi typické případy použití patří:

- **Dodržování předpisů**: Neměnné úložiště pro úložiště objektů blob Azure pomáhá organizacím řešit SEC 17a-4(f), CFTC 1.31(d), FINRA a další předpisy. Technická bílá kniha společnosti Cohasset Associates podrobně popisuje, jak neměnné úložiště řeší tyto regulační požadavky, lze tyto regulační požadavky stáhnout prostřednictvím [portálu Microsoft Service Trust Portal](https://aka.ms/AzureWormStorage). [Centrum zabezpečení Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) obsahuje podrobné informace o našich certifikacích dodržování předpisů.

- **Zabezpečené uchovávání dokumentů**: Neměnné úložiště pro úložiště objektů blob Azure zajišťuje, že data nelze upravit nebo odstranit žádný uživatel, včetně uživatelů s oprávněními správce účtu.

- **Blokování z právního hlediska**: Neměnné úložiště pro úložiště objektů blob Azure umožňuje uživatelům ukládat citlivé informace, které jsou důležité pro soudní spory nebo obchodní použití ve stavu odolném proti neoprávněné manipulaci po požadovanou dobu, dokud není blokování odebráno. Tato funkce není omezena pouze na případy použití z právního systému, ale lze ji také považovat za blokování na základě událostí nebo podnikový zámek, kde je vyžadována potřeba chránit data na základě aktivačních událostí nebo podnikových zásad.

Neměnné úložiště podporuje následující funkce:

- **[Podpora zásad uchovávání informací podle času](#time-based-retention-policies)**: Uživatelé mohou nastavit zásady pro ukládání dat po zadaný interval. Pokud je nastavena zásada uchovávání informací založená na čase, objekty BLOB lze vytvořit a číst, ale není změněnnebo odstraněn. Po uplynutí doby uchovávání lze objekty BLOB odstranit, ale ne přepsat.

- **[Podpora zásad blokování z právních předpisů](#legal-holds)**: Pokud není znám interval uchovávání informací, mohou uživatelé nastavit blokování pro ukládání neměnných dat, dokud nebude blokování nastaveno, dokud nebude blokování nastaveno.  Pokud je nastavena zásada blokování právních předpisů, objekty BLOB lze vytvořit a číst, ale není změněnnebo odstraněn. Každé právní blokování je přidruženo k uživatelem definované alfanumerické značce (například ID případu, názvu události atd.), která se používá jako řetězec identifikátoru. 

- **Podpora pro všechny vrstvy objektů blob**: Zásady WORM jsou nezávislé na úrovni úložiště objektů Blob Azure a platí pro všechny úrovně: horké, studené a archivní. Uživatelé mohou přesouvat data na úroveň, která je optimální z hlediska nákladů, a přitom zachovat jejich neměnnost.

- **Konfigurace na úrovni kontejneru**: Uživatelé mohou konfigurovat zásady uchovávání informací podle času a značky blokování právní na úrovni kontejneru. Uživatelé mohou s využitím jednoduchých nastavení na úrovni kontejneru vytvořit a uzamknout zásady uchovávání informací podle času, prodloužit intervaly uchovávání informací, nastavit nebo zrušit blokování z právních důvodů atd. Tyto zásady platí pro všechny stávající i nové objekty blob v kontejneru.

- **Podpora protokolování auditu**: Každý kontejner obsahuje protokol auditu zásad. Zobrazuje až sedm příkazů uchovávání informací založených na čase pro uzamčené zásady uchovávání informací založené na čase a obsahuje ID uživatele, typ příkazu, časová razítka a interval uchovávání informací. V případě blokování z právních důvodů obsahuje protokol ID uživatele, typ příkazu, časová razítka a značky blokování z právních důvodů. Tento protokol je zachován po celou dobu trvání zásad v souladu s regulačními pokyny SEC 17a-4(f). [Protokol aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md) zobrazuje komplexnější protokol všech aktivit roviny ovládacího prvku; při povolení [Azure Diagnostické protokoly](../../azure-monitor/platform/platform-logs-overview.md) zachovává a zobrazuje operace roviny dat. Uživatel je zodpovědný za trvalé uchování těchto protokolů, protože se mohou vyžadovat pro legislativní nebo jiné účely.

## <a name="how-it-works"></a>Jak to funguje

Funkce Immutable Storage pro úložiště Azure Blob Storage podporuje dva typy zásad WORM neboli zásad neměnnosti: uchovávání informací podle času a blokování z právních důvodů. Při použití zásad y uchovávání informací na základě času nebo právní blokování na kontejneru, všechny existující objekty BLOB přesunout do neměnného stavu WORM za méně než 30 sekund. Všechny nové objekty BLOB, které jsou odeslány do tohoto kontejneru chráněného zásadami se také přesunou tzv. Jakmile jsou všechny objekty BLOB v neměnném stavu, je neměnná zásada potvrzena a nejsou povoleny žádné operace přepsání nebo odstranění v neměnném kontejneru.

Odstranění účtu kontejneru a úložiště také není povoleno, pokud existují nějaké objekty BLOB v kontejneru, které jsou chráněné právní blokování nebo uzamčené zásady založené na čase. Zásady blokování právních předpisů budou chráněny před odstraněním účtu objektů blob, kontejneru a úložiště. Odemčené i uzamčené zásady založené na čase budou chráněny proti odstranění objektů blob po zadanou dobu. Odemčené i uzamčené zásady založené na čase budou chráněny před odstraněním kontejneru pouze v případě, že v kontejneru existuje alespoň jeden objekt blob. Pouze kontejner s *uzamčenými* zásadami založenými na čase bude chránit před odstraněním účtu úložiště; kontejnery s odemčenými zásadami založenými na čase nenabízejí ochranu proti odstranění účtu úložiště ani dodržování předpisů.

Další informace o nastavení a uzamčení zásad uchovávání informací na základě času najdete v tématu [Nastavení a správa zásad neměnnosti pro úložiště objektů Blob](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Zásady uchovávání informací podle času

> [!IMPORTANT]
> Zásady uchovávání informací založené na čase musí být *uzamčeny* pro objekt blob být v kompatibilní matný (zápis a odstranění chráněné) stavu pro SEC 17a-4(f) a další dodržování předpisů. Doporučujeme zamknout zásady v přiměřené min. čas, obvykle méně než 24 hodin. Počáteční stav použitých zásad uchovávání informací podle času je *odemčen ,* což umožňuje otestovat funkci a provést změny zásad před uzamčením. Zatímco *odemčený* stav poskytuje ochranu proti neměnnosti, nedoporučujeme používat *odemčený* stav k jinému účelu než ke krátkodobým zkušebním verzím funkcí. 

Při použití zásady uchovávání informací na základě času na kontejneru, všechny objekty BLOB v kontejneru zůstane v neměnné stavu po dobu *trvání efektivní* retenční období. Efektivní doba uchovávání objektů BLOB se rovná rozdílu mezi **časem vytvoření** objektu blob a uživatelem zadaným intervalem uchovávání informací. Vzhledem k tomu, že uživatelé mohou interval uchovávání informací prodloužit, používá funkce Immutable Storage při výpočtu účinné doby uchovávání informací poslední hodnotu uživatelem zadaného intervalu.

Předpokládejme například, že uživatel vytvoří zásady uchovávání informací založené na čase s intervalem uchovávání informací pět let. Existující objekt blob v tomto _kontejneru, testblob1_, byl vytvořen před rokem; takže, efektivní retenční doba pro _testblob1_ je čtyři roky. Při nové objekt blob, _testblob2_, je nahrán do kontejneru, efektivní retenční doba pro _testblob2_ je pět let od okamžiku jeho vytvoření.

Odemčené zásady uchovávání informací podle času se doporučuje pouze pro testování funkcí a zásady musí být uzamčeny, aby byly v souladu s SEC 17a-4(f) a další dodržování předpisů. Jakmile jsou zásady uchovávání informací založené na čase uzamčeny, nelze zásadu odebrat a je povoleno maximálně pět zvýšení skutečné doby uchovávání informací.

Následující omezení platí pro zásady uchovávání informací:

- Pro účet úložiště je maximální počet kontejnerů s uzamčenými zásadami neměnných časových zásad 10 000.
- Minimální retenční interval je 1 den. Maximální hodnota je 146 000 dní (400 let).
- Pro kontejner maximální počet úprav prodloužit interval uchování pro uzamčené zásady na základě času je 5.
- Pro kontejner maximálně sedm protokolů auditu zásad uchovávání informací na základě času jsou zachovány pro uzamčené zásady.

### <a name="allow-protected-append-blobs-writes"></a>Povolit zápisy chráněných objektů BLOB

Připojit objekty BLOB se skládají z datových bloků a optimalizované pro operace připojení dat vyžadované auditováním a protokolováním scénářů. Podle návrhu připojit objekty BLOB pouze povolit přidání nových bloků na konec objektu blob. Bez ohledu na neměnnost, úpravy nebo odstranění existujících bloků v objektu blob připojit zásadně není povoleno. Další informace o přípojcích objektů BLOB najdete v tématu [O přípojcích objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Pouze zásady uchovávání informací `allowProtectedAppendWrites` založené na čase mají nastavení, které umožňuje zápis nových bloků do objektu blob připojit při zachování ochrany neměnnosti a dodržování předpisů. Pokud je tato možnost povolena, můžete vytvořit objekt blob pro připojení přímo v kontejneru chráněném zásadami a pokračovat v přidávání nových bloků dat na konec existujících objektů BLOB připojit pomocí rozhraní *API AppendBlock.* Lze přidat pouze nové bloky a všechny existující bloky nelze změnit ani odstranit. Ochrana proti neměnnosti uchovávání času stále platí, brání odstranění objektu blob připojit, dokud neuplyne efektivní doba uchovávání. Povolení tohoto nastavení nemá vliv na chování neměnnosti objektů BLOB bloku nebo objektů BLOB stránky.

Vzhledem k tomu, že toto nastavení je součástí zásad uchovávání informací na základě času, objekty BLOB připojení stále zůstávají v neměnnéstavu po dobu trvání *efektivní* retenční období. Vzhledem k tomu, že nová data lze připojit nad rámec počáteční vytvoření objektu blob připojit, je mírný rozdíl v tom, jak je určena doba uchovávání. Efektivní uchovávání informací je rozdíl mezi **časem poslední změny** objektu blob a uživatelem zadaným intervalem uchovávání informací. Podobně při prodloužení intervalu uchování neměnné úložiště používá nejnovější hodnotu uživatelem zadaný interval uchování k výpočtu efektivní retenční období.

Předpokládejme například, že uživatel vytvoří zásady `allowProtectedAppendWrites` uchovávání informací založené na čase s povolenou a interval uchování 90 dnů. Přidávací objekt blob _logblob1_, je vytvořen v kontejneru dnes, nové protokoly nadále přidány do objektu blob připojení pro dalších 10 dnů; takže efektivní doba uchovávání _pro logblob1_ je 100 dní ode dneška (čas jeho posledního připojení + 90 dní).

Odemčené zásady uchovávání `allowProtectedAppendWrites` informací podle času umožňují nastavení, které mají být povoleny a zakázány kdykoli. Jakmile jsou zásady uchovávání informací `allowProtectedAppendWrites` založené na čase uzamčeny, nelze nastavení změnit.

Právní blokování zásady nelze povolit `allowProtectedAppendWrites` a žádné právní blokování zruší 'allowProtectedAppendWrites' vlastnost. Pokud je právní blokování použito na zásady `allowProtectedAppendWrites` uchovávání informací založené na čase s povolenou, rozhraní *API AppendBlock* se nezdaří, dokud není zrušeno právní blokování.

## <a name="legal-holds"></a>Blokování z právních důvodů

Právní blokování jsou dočasné blokování, které lze použít pro účely právního vyšetřování nebo obecné politiky ochrany. Každá zásada blokování z právních předpisů musí být přidružena k jedné nebo více značkám. Značky se používají jako pojmenovaný identifikátor, například ID případu nebo událost, ke kategorizaci a popisu účelu blokování.

Kontejner může mít současně právní blokování i zásady uchovávání informací založené na čase. Všechny objekty blob v kontejneru zůstanou v neměnném stavu, dokud se nezruší všechna blokování z právních důvodů, a to i poté, co u nich uplyne účinná doba uchovávání informací. A naopak, objekt blob zůstane v neměnném stavu, dokud neuplyne účinná doba uchovávání informací, a to i po zrušení všech blokování z právních důvodů.

Na zákonné blokování platí následující omezení:

- Pro účet úložiště je maximální počet kontejnerů s nastavením blokování 10 000.
- Pro kontejner je maximální počet značek blokování z právních důvodů 10.
- Minimální délka zákonné značky blokování je tři alfanumerické znaky. Maximální délka je 23 alfanumerických znaků.
- Pro kontejner maximálně 10 protokoly auditu zásad blokování právní zásady jsou uchovávány po dobu trvání zásady.

## <a name="scenarios"></a>Scénáře
V následující tabulce jsou uvedeny typy operací úložiště objektů Blob, které jsou zakázány pro různé neměnné scénáře. Další informace najdete v dokumentaci k [rozhraní REST rozhraní AZURE blob Service.](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

|Scénář  |Stav objektu blob  |Operace objektů blob byly odepřeny.  |Ochrana kontejnerů a účtů
|---------|---------|---------|---------|
|Efektivní interval uchovávání informací pro objekt blob ještě nevypršel a/nebo je nastavené blokování z právních důvodů     |Neměnné: chráněné proti odstranění i zápisu         | Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Odstranit kontejner, Odstranit objekt blob, Nastavit metadata objektu blob, Umístit stránku, Nastavit vlastnosti objektu blob, Objekt blob snímku, Přírůstkový objekt blob pro kopírování, připojit blok<sup>2</sup>         |Odstranění kontejneru bylo odmítnuto. Odstranění účtu úložiště bylo odepřeno.         |
|Platnost intervalu uchovávání informací na objektu blob vypršela a není nastaveno žádné blokování    |Chráněné pouze proti zápisu (operace odstranění jsou povolené)         |Put Blob<sup>1</sup>, Put Block<sup>1</sup>, Put Block List<sup>1</sup>, Nastavit metadata objektu blob, Umístit stránku, Nastavit vlastnosti objektu blob, objekt blob snímku, objekt blob přírůstkové kopie, blok připojení<sup>2</sup>         |Odstranění kontejneru bylo odmítnuto, pokud v chráněném kontejneru existuje alespoň 1 objekt blob. Odstranění účtu úložiště bylo odepřeno pouze pro *uzamčené* zásady založené na čase.         |
|Žádné zásady WORM použity (žádné uchovávání na základě času a žádná značka blokování z právního nastavení)     |Měnitelné         |Žádný         |Žádný         |

<sup>1</sup> Služba objektu blob umožňuje tyto operace vytvořit nový objekt blob jednou. Všechny následné operace přepsání na existující cestě objektu blob v neměnné matné kontejneru nejsou povoleny.

<sup>2</sup> Připojit blok je povoleno pouze pro zásady uchovávání informací podle času s povolenou vlastností. `allowProtectedAppendWrites` Další informace naleznete v části [Povolit zápisy objektů BLOBs chráněného připojení.](#allow-protected-append-blobs-writes)

## <a name="pricing"></a>Ceny

Za použití této funkce se neplatí žádné další poplatky. Neměnná data jsou oceněna stejným způsobem jako proměnlivá data. Podrobnosti o cenách v úložišti objektů Blob Azure najdete na [stránce s cenami služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Nejčastější dotazy

**Můžete poskytnout dokumentaci o dodržování předpisů WORM?**

Ano. Společnost Microsoft si k dokumentaci dodržování předpisů ponechala přední nezávislou společnost zabývající se hodnocením, která se specializuje na správu záznamů a řízení informací, Cohasset Associates, aby vyhodnotila neměnné úložiště objektů Blob a jeho soulad s požadavky specifickými pro odvětví finančních služeb. Cohasset ověřil, že neměnné úložiště objektů blob, při použití k zachování objektů BLOB založených na čase ve stavu WORM, splňuje příslušné požadavky na úložiště CFTC pravidlo 1.31(c)-(d), FINRA pravidlo 4511 a PRAVIDLO SEC 17a-4. Společnost Microsoft zacílila na tuto sadu pravidel, protože představují nejnormavější pokyny pro uchovávání záznamů pro finanční instituce. Sestava Cohasset je k dispozici v [Centru zabezpečení služeb společnosti Microsoft](https://aka.ms/AzureWormStorage). Chcete-li požádat společnost Microsoft o potvrzení žádosti o dodržování předpisů pro neměnnost wormu, obraťte se na podporu Azure.

**Vztahuje se tato funkce pouze na objekty BLOB a objekty BLOB pro blokování nebo také na objekty BLOB stránky?**

Neměnné úložiště lze použít s libovolným typem objektu blob, protože je nastavenna na úrovni kontejneru, ale doporučujeme použít ČERV pro kontejnery, které hlavně ukládají objekty BLOB bloku a připojují objekty BLOB. Existující objekty BLOB v kontejneru budou chráněny nově nastavenou zásadou WORM. Ale všechny nové objekty BLOB stránky je třeba vytvořit mimo kontejner WORM a pak zkopírovat. Po zkopírování do kontejneru WORM nejsou povoleny žádné další změny objektu blob stránky. Nastavení zásadworm na kontejneru, který ukládá Virtuální počítače (objekty BLOB stránky) pro všechny aktivní virtuální počítače se nedoporučuje, protože se uzamkne disk virtuálního počítače. Doporučujeme důkladně zkontrolovat dokumentaci a otestovat scénáře před uzamčením jakékoli zásady založené na čase.

**Musím si vytvořit nový účet úložiště, abych mohl tuto funkci používat?**

Ne, můžete použít neměnné úložiště s existujícími nebo nově vytvořenými účty pro obecné účely v1, pro obecné účely v2, BlobStorage nebo BlockBlobStorage. Účty úložiště pro obecné účely v1 jsou podporovány, ale doporučujeme upgradovat na obecné účely v2 tak, abyste mohli využít další funkce. Informace o upgradu existujícího účtu úložiště pro obecné účely v1 najdete v [tématu Upgrade účtu úložiště](../common/storage-account-upgrade.md).

**Mohu použít jak právní blokování, tak zásady uchovávání informací založené na čase?**

Ano, kontejner může mít současně právní blokování i zásady uchovávání informací založené na čase. nastavení allowProtectedAppendWrites však nebude platit, dokud nebude vymazáno blokování. Všechny objekty blob v kontejneru zůstanou v neměnném stavu, dokud se nezruší všechna blokování z právních důvodů, a to i poté, co u nich uplyne účinná doba uchovávání informací. A naopak, objekt blob zůstane v neměnném stavu, dokud neuplyne účinná doba uchovávání informací, a to i po zrušení všech blokování z právních důvodů. 

**Jsou právní zásady pouze pro soudní řízení nebo existují jiné scénáře použití?**

Ne, Blokování z právního hlediska je pouze obecný termín používaný pro zásady uchovávání informací, které nejsou založeny na čase. Nemusí být použita pouze pro soudní řízení. Právní hold zásady jsou užitečné pro zakázání přepsat a odstraní pro ochranu důležitých dat enterprise WORM, kde doba uchování není známa. Můžete ji použít jako zásadu organizace k ochraně úloh červa kritické nebo použít jako pracovní zásady před vlastní aktivační událost vyžaduje použití zásady uchovávání informací na základě času. 

**Mohu odebrat _uzamčenou_ zásadu uchovávání informací založenou na čase nebo blokování?**

Z kontejneru lze odebrat pouze odemčené zásady uchovávání informací podle času. Jakmile je zásada uchovávání informací založená na čase uzamčena, nelze ji odebrat. jsou povolena pouze účinná prodloužení retenční doby. Legální blokování lze odstranit. Po odstranění všech právních značek je blokování odebráno.

**Co se stane, když se pokusím odstranit kontejner se zásadami uchovávání informací podle času nebo s blokováním?**

Operace Odstranit kontejner se nezdaří, pokud alespoň jeden objekt blob existuje v kontejneru s uzamčenou nebo odemčenou zásadou uchovávání informací založenou na čase nebo pokud má kontejner právní blokování. Operace Odstranit kontejner bude úspěšná pouze v případě, že v kontejneru neexistují žádné objekty BLOB a neexistují žádné právní blokování. 

**Co se stane, když se pokusím odstranit účet úložiště s kontejnerem, který má zásady uchovávání informací založené na čase nebo blokování z právního nastavení?**

Odstranění účtu úložiště se nezdaří, pokud existuje alespoň jeden kontejner s nastaveným právním blokováním nebo **uzamčenou** zásadou založenou na čase. Kontejner s odemčenými zásadami založenými na čase nechrání před odstraněním účtu úložiště. Před odstraněním účtu úložiště je nutné odebrat všechny legální blokování a odstranit všechny **uzamčené** kontejnery. Informace o odstranění kontejneru naleznete v předchozí otázce. Můžete také použít další odstranění ochrany pro váš účet úložiště s [uzamčení Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).

**Můžu přesunout data přes různé vrstvy objektů blob (horké, cool, archiv), když je objekt blob v neměnném stavu?**

Ano, můžete použít nastavit úroveň objektu blob příkaz k přesunutí dat přes vrstvy objektů blob při zachování dat v kompatibilním neměnnéstavu. Neměnné úložiště je podporováno na horkých, chladných a archivních úrovních objektů blob.

**Co se stane, když zapomenu zaplatit a můj interval uchovávání informací ještě nevypršel?**

V případě nezaplacení platí běžné zásady uchovávání údajů, jak je stanoveno v podmínkách vaší smlouvy se společností Microsoft. Obecné informace naleznete v [tématu Správa dat společnosti Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Nabízíte zkušební období nebo období odkladu pouze na vyzkoušení této funkce?**

Ano. Při prvním vytvoření zásady uchovávání informací podle času je v *odemčený* stav. V tomto stavu můžete provést požadovanou změnu intervalu uchování, například zvýšit nebo snížit a dokonce odstranit zásady. Po uzamčení zásady zůstane uzamčena, dokud nevyprší interval uchování. Tato uzamčená zásada zabraňuje odstranění a úpravě intervalu uchovávání informací. Důrazně doporučujeme používat *odemčený* stav pouze pro zkušební účely a zamknout zásady v rámci 24 hodin. Tyto postupy vám pomohou dodržovat sektoru SEK 17a-4(f) a další předpisy.

**Můžu vedle zásad objektů blob imemitovat použít obnovitelné odstranění?**

Ano, pokud vaše požadavky na dodržování předpisů umožňují povolit obnovitelné odstranění. [Obnovitelné odstranění pro úložiště objektů blob Azure](storage-blob-soft-delete.md) platí pro všechny kontejnery v rámci účtu úložiště bez ohledu na právní blokování nebo zásady uchovávání informací na základě času. Doporučujeme povolit měkké odstranění pro další ochranu před použitím a potvrzením jakýchkoli neměnných zásad WORM.

## <a name="next-steps"></a>Další kroky

- [Nastavení a správa zásad neměnnosti pro úložiště objektů Blob](storage-blob-immutability-policies-manage.md)
- [Nastavení pravidel pro automatické vrstvení a odstranění dat objektů blob pomocí správy životního cyklu](storage-lifecycle-management-concepts.md)
- [Obnovitelné odstranění objektů blob služby Azure Storage](../blobs/storage-blob-soft-delete.md)
- [Chraňte předplatná, skupiny prostředků a prostředky pomocí zámků Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).
