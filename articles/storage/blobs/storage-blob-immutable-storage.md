---
title: Neměnné úložiště objektů blob – Azure Storage
description: Azure Storage nabízí pro úložiště objektů BLOB (Object) možnost WORM (napsat jednou, číst mnoho), která umožňuje uživatelům ukládat data v neerasablem stavu, který se v zadaném intervalu nedají měnit.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 54014a0d76130b82788a1ae432e42baec28df2c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87448334"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Ukládání důležitých podnikových dat objektů BLOB s neměnném úložištěm

Neměnné úložiště pro úložiště objektů BLOB v Azure umožňuje uživatelům ukládat datové objekty kritické pro podnikání do ČERVa (psát jednou, číst mnoho). Tento stav zpřístupňuje data, která nejsou Erasable a není upravitelná pro interval zadaný uživatelem. Po dobu trvání intervalu uchování dat je možné objekty blob vytvořit a číst, ale nelze je upravit ani odstranit. K dispozici je neměnné úložiště pro účty pro obecné účely V1, obecné pro v2, BlobStorage a BlockBlobStorage ve všech oblastech Azure.

Informace o tom, jak pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure, nastavit a vymazat právní zásady uchovávání informací, najdete v tématu [nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](storage-blob-immutability-policies-manage.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>Neměnné úložiště objektů BLOB

Neměnné úložiště pomáhá organizacím v oblasti zdravotní péče, finančním institucím a souvisejícím oborům, což je &mdash; zvláštní zprostředkovatel – organizace pro dealery &mdash; ukládají data bezpečně. Neměnné úložiště je také možné využít v jakémkoli scénáři k ochraně důležitých dat proti úpravám nebo odstranění.

Mezi typické případy použití patří:

- **Dodržování**předpisů: neměnné úložiště pro úložiště objektů BLOB v Azure pomáhá organizacím s výjimkou 17a-4 (f), CFTC 1.31 (d), FINRA a dalšími předpisy. Technický dokument White Paper od Cohasset přidruží k podrobnostem o tom, jak neproměnlivé úložiště řeší tyto zákonné požadavky, ke stažení prostřednictvím [portálu Microsoft Trust Service](https://aka.ms/AzureWormStorage). [Centrum zabezpečení Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) obsahuje podrobné informace o našich certifikátech dodržování předpisů.

- **Zabezpečené uchovávání dokumentů**: neměnné úložiště pro úložiště objektů BLOB v Azure zajišťuje, že data nelze upravovat ani odstraňovat žádného uživatele, včetně uživatelů s oprávněními správce účtu.

- **Právní blokování**: neměnné úložiště pro úložiště objektů BLOB v Azure umožňuje uživatelům ukládat citlivé informace, které jsou zásadní pro soudní spory nebo obchodní použití v případě, že se blokování neodstraní. Tato funkce není omezená jenom na případy právního použití, ale dá se taky představit jako blok založený na událostech nebo na podnikovém zámku, kde je potřeba chránit data na základě triggerů událostí nebo podnikových zásad.

Neměnné úložiště podporuje následující funkce:

- **[Podpora zásad uchovávání na základě času](#time-based-retention-policies)**: uživatelé můžou nastavit zásady pro ukládání dat v zadaném intervalu. Když se nastaví zásady uchovávání informací založené na čase, můžou se objekty blob vytvářet a číst, ale ne upravovat ani odstraňovat. Po vypršení doby uchování je možné objekty blob odstranit, ale nebudou přepsány.

- **[Podpora zásad právního blokování](#legal-holds)**: Pokud není Interval uchování známý, můžou uživatelé nastavit právní blokování na ukládání neproměnlivých dat, dokud se neodstraní právní blokování.  Pokud je nastavena zásada právního blokování, lze objekty blob vytvořit a číst, ale nikoli upravovat ani odstraňovat. Každé právní blokování je přidruženo k uživatelsky definované alfanumerické značce (například ID případu, název události atd.), které se používají jako řetězec identifikátoru. 

- **Podpora pro všechny úrovně objektů BLOB**: zásady červa jsou nezávislé na úrovni úložiště objektů BLOB v Azure a platí pro všechny úrovně: horká, studená a archivní. Uživatelé mohou přesouvat data na úroveň, která je optimální z hlediska nákladů, a přitom zachovat jejich neměnnost.

- **Konfigurace na úrovni kontejneru**: uživatelé můžou na úrovni kontejneru nakonfigurovat zásady uchovávání informací založené na čase a značky právního blokování. Uživatelé mohou s využitím jednoduchých nastavení na úrovni kontejneru vytvořit a uzamknout zásady uchovávání informací podle času, prodloužit intervaly uchovávání informací, nastavit nebo zrušit blokování z právních důvodů atd. Tyto zásady platí pro všechny stávající i nové objekty blob v kontejneru.

- **Podpora protokolování auditu**: každý kontejner zahrnuje protokol auditu zásad. Zobrazuje až sedm příkazů pro uchovávání informací na základě času pro uzamčené zásady uchovávání informací podle času a obsahuje ID uživatele, typ příkazu, časová razítka a interval uchovávání. V případě blokování z právních důvodů obsahuje protokol ID uživatele, typ příkazu, časová razítka a značky blokování z právních důvodů. Tento protokol se zachovává po dobu života zásady, v souladu s pravidly pro legislativní SEK – 17a (f). [Protokol aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md) zobrazuje komplexnější protokol všech aktivit řídicích rovin; Při povolování [protokolů prostředků Azure](../../azure-monitor/platform/platform-logs-overview.md) se uchovávají a zobrazují operace roviny dat. Uživatel je zodpovědný za trvalé uchování těchto protokolů, protože se mohou vyžadovat pro legislativní nebo jiné účely.

## <a name="how-it-works"></a>Jak to funguje

Funkce Immutable Storage pro úložiště Azure Blob Storage podporuje dva typy zásad WORM neboli zásad neměnnosti: uchovávání informací podle času a blokování z právních důvodů. Pokud je na kontejneru použita zásada uchovávání informací založená na čase nebo právní blokování, všechny existující objekty BLOB se přesunou do neměnného stavu ČERVa za méně než 30 sekund. Všechny nové objekty blob nahrané do tohoto kontejneru chráněného zásadami se také přesunou do neměnného stavu. Jakmile jsou všechny objekty BLOB v neměnném stavu, jsou potvrzeny neměnné zásady a všechny operace přepsání nebo odstranění v neměnitelném kontejneru nejsou povoleny.

Odstranění kontejneru a účtu úložiště se taky nepovoluje, pokud v kontejneru existují objekty blob, které jsou chráněné právním blokováním nebo uzamčenou časovou zásadou. Zásady právního blokování budou chránit před odstraněním objektu blob, kontejneru a účtu úložiště. Odemčené i uzamčené zásady založené na čase budou chránit před odstraněním objektu BLOB po určenou dobu. Odemčené i uzamčené zásady založené na čase se budou chránit proti odstranění kontejneru jenom v případě, že v kontejneru existuje aspoň jeden objekt BLOB. Jenom kontejner s *uzamčenou* zásadou založenou na čase se bude chránit před odstraňováním účtů úložiště. kontejnery s odemknutými časovými zásadami nenabízejí ochranu před odstraněním účtu úložiště ani dodržování předpisů.

Další informace o tom, jak nastavit a uzamknout zásady uchovávání informací podle časových údajů, najdete v tématu [nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Zásady uchovávání informací podle času

> [!IMPORTANT]
> Zásady uchovávání informací založené na čase musí být *uzamčené* , aby objekt BLOB byl ve stavu kompatibilním s neproměnlivým stavem (Write and Delete Protected) pro SEK-4 (f) a další zákonné dodržování předpisů. Doporučujeme zásady uzamknout v rozumné době, většinou méně než 24 hodin. Počáteční stav použitých zásad uchovávání dat je *odemčený*, což vám umožní otestovat funkci a provést změny zásad před jejich uzamknutím. I když *odemčený* stav poskytuje ochranu neměnnosti, nedoporučujeme používat *odemčený* stav pro jiné účely, než krátkodobé testy funkcí. 

Pokud jsou na kontejneru aplikovány zásady uchovávání informací založené na čase, všechny objekty BLOB v kontejneru zůstanou v neměnném stavu po dobu trvání *efektivní* doby uchování. Efektivní doba uchovávání objektů BLOB se rovná rozdílu mezi **časem vytvoření** objektu BLOB a intervalem uchování zadaným uživatelem. Vzhledem k tomu, že uživatelé mohou interval uchovávání informací prodloužit, používá funkce Immutable Storage při výpočtu účinné doby uchovávání informací poslední hodnotu uživatelem zadaného intervalu.

Předpokládejme například, že uživatel vytvoří zásady uchovávání dat s intervalem uchování na pět let. Stávající objekt BLOB v tomto kontejneru, _testblob1_, byl vytvořen před rokem; Efektivní doba uchování pro _testblob1_ je tedy čtyři roky. Když se do kontejneru nahraje nový objekt blob, _testblob2_, efektivní doba uchování pro _testblob2_ je pět let od doby jeho vytvoření.

Odemčené zásady uchovávání informací založené na čase se doporučují jenom pro testování funkcí a zásady musí být uzamčené, aby byly kompatibilní s SEK-4 (f) a dalšími předpisy pro dodržování předpisů. Jakmile jsou zásady uchovávání informací založené na čase uzamčené, zásada se nedá odebrat a maximálně pět zvýšení k efektivnímu období uchování je povolené.

Následující omezení platí pro zásady uchovávání informací:

- V případě účtu úložiště je maximální počet kontejnerů, které mají uzamčené zásady s neproměnlivým časem, 10 000.
- Minimální interval uchovávání dat je 1 den. Maximální počet je 146 000 dní (400 let).
- V případě kontejneru je maximální počet úprav pro prodloužení intervalu uchování pro neměnné zásady na základě času 5.
- V případě kontejneru jsou pro uzamčenou zásadu uchovány maximálně sedm protokolů auditu zásad uchovávání času.

### <a name="allow-protected-append-blobs-writes"></a>Povolení zápisů chráněných objektů BLOB

Doplňovací objekty BLOB se skládají z bloků dat a jsou optimalizované pro operace připojení dat vyžadované scénáři auditování a protokolování. V rámci návrhu připojovat objekty blob umožňují pouze přidávání nových bloků na konec objektu BLOB. Bez ohledu na neměnnosti, úprava nebo odstranění existujících bloků v doplňovacím objektu BLOB není v podstatě povolená. Další informace o přidaných objektech blob najdete v tématu věnovaném [přidávání objektů BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Jenom zásady uchovávání informací mají `allowProtectedAppendWrites` nastavení, které umožňuje psát nové bloky do doplňovacího objektu BLOB při zachování ochrany neměnnosti a dodržování předpisů. Pokud je toto nastavení povolené, budete moct vytvořit doplňovací objekt BLOB přímo v kontejneru chráněném zásadami a dál přidávat nové bloky dat na konec stávajících objektů BLOB s použitím rozhraní *AppendBlock* API. Přidat lze pouze nové bloky a všechny existující bloky nelze upravovat ani odstraňovat. Ochrana neměnnosti s časovým limitem se pořád platí, takže je možné odstranit doplňovací objekt BLOB až do doby, kdy uplynula doba uchování. Povolení tohoto nastavení nemá vliv na chování neměnnosti objektů blob bloku nebo objektů blob stránky.

Vzhledem k tomu, že toto nastavení je součástí zásad uchovávání informací založených na čase, doplňovací objekty blob zůstanou v neměnném stavu po dobu trvání *efektivní* doby uchování. Vzhledem k tomu, že je možné přidat nová data mimo počáteční vytvoření doplňovacího objektu blob, dojde k mírnému rozdílu v tom, jak se určuje doba uchování. Efektivní doba uchování je rozdíl mezi přidaným **časem poslední změny** připojení objektu BLOB a intervalem uchování zadaného uživatelem. Podobně když je interval uchovávání rozšířený, neměnné úložiště používá k výpočtu efektivní doby uchování nejnovější hodnotu intervalu uchování zadaného uživatelem.

Předpokládejme například, že uživatel vytvoří zásady uchovávání informací s `allowProtectedAppendWrites` povoleným a intervalem uchování 90 dnů. V současné době se vytvoří doplňovací objekt blob, _logblob1_a v kontejneru se nové protokoly přidají do připojeného objektu BLOB po dobu příštích 10 dnů. Efektivní doba uchování pro _logblob1_ je tedy 100 dnů od dnešního dne (čas posledního připojení + 90 dní).

Odemčené zásady uchovávání informací umožňují, aby `allowProtectedAppendWrites` bylo nastavení povolené a zakázané. Jakmile budou zásady uchovávání informací na základě času uzamčené, `allowProtectedAppendWrites` nastavení se nedá změnit.

Zásady právního blokování nemohou povolit `allowProtectedAppendWrites` a žádné právní blokování budou nezruší vlastnost allowProtectedAppendWrites. Pokud se pro zásady uchovávání informací s povoleným časovým plánem používá právní blokování `allowProtectedAppendWrites` , rozhraní *AppendBlock* API selže, dokud se neobnoví jeho právní blokování.

## <a name="legal-holds"></a>Blokování z právních důvodů

Právní blokování jsou dočasná blokování, která se dají použít pro účely soudního vyšetřování nebo obecné zásady ochrany. Všechny zásady právního blokování musí být přidružené k jedné nebo více značkám. Značky se používají jako pojmenovaný identifikátor, jako je ID případu nebo událost, ke kategorizaci a popisu účelu blokování.

Kontejner může současně obsahovat i právní blokování i zásady uchovávání informací podle času. Všechny objekty blob v kontejneru zůstanou v neměnném stavu, dokud se nezruší všechna blokování z právních důvodů, a to i poté, co u nich uplyne účinná doba uchovávání informací. A naopak, objekt blob zůstane v neměnném stavu, dokud neuplyne účinná doba uchovávání informací, a to i po zrušení všech blokování z právních důvodů.

Následující omezení platí pro právní blokování:

- V případě účtu úložiště je maximální počet kontejnerů s nastavením právního blokování 10 000.
- V případě kontejneru je maximální počet platných značek blokování 10.
- Minimální délka značky právního blokování je tři alfanumerické znaky. Maximální délka je 23 alfanumerických znaků.
- V případě kontejneru se pro dobu trvání zásady uchovávají maximálně 10 protokolů auditu zásad uchovávání předpisů.

## <a name="scenarios"></a>Scénáře
V následující tabulce jsou uvedeny typy operací úložiště objektů blob, které jsou zakázané pro různé neměnné scénáře. Další informace najdete v dokumentaci ke [službě Azure Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) .

|Scénář  |Stav objektu BLOB  |Operace objektu BLOB se zamítly.  |Ochrana kontejneru a účtu
|---------|---------|---------|---------|
|Efektivní interval uchovávání informací pro objekt blob ještě nevypršel a/nebo je nastavené blokování z právních důvodů     |Neměnné: chráněné proti odstranění i zápisu         | Vložte objekt BLOB<sup>1</sup>, PUT blok<sup>1</sup>, PUT seznam blokování<sup>1</sup>, odstranit kontejner, odstranit objekt blob, nastavte metadata objektu blob, vložte stránku, nastavte vlastnosti objektů blob, objekt BLOB snímku, přírůstkový objekt BLOB kopírování, připojovací blok<sup>2</sup> .         |Odstranění kontejneru bylo odepřeno; Odstranění účtu úložiště se zamítlo.         |
|Platnost platnosti intervalu uchování u objektu BLOB vypršela a není nastavené žádné právní blokování.    |Chráněné pouze proti zápisu (operace odstranění jsou povolené)         |Vložte objekt BLOB<sup>1</sup>, PUT blok<sup>1</sup>, PUT seznam blokování<sup>1</sup>, nastavte metadata objektu blob, PUT, nastavte vlastnosti objektů blob, objekt BLOB snímku, objekt BLOB přírůstkového kopírování, připojovat blok<sup>2</sup> .         |Odstranění kontejneru bylo odepřeno, pokud v chráněném kontejneru existuje alespoň 1 objekt BLOB; Odstranění účtu úložiště bylo odepřeno jenom pro *uzamčené* zásady založené na čase.         |
|Neaplikují se žádné zásady ČERVů (žádné uchování založené na čase ani značka právního blokování).     |Měnitelné         |Žádné         |Žádné         |

<sup>1</sup> služba BLOB umožňuje těmto operacím vytvořit nový objekt BLOB jednou. Všechny následné operace přepsání na stávající cestě objektu BLOB v neměnitelném kontejneru nejsou povoleny.

<sup>2</sup> připojovací blok je povolený jenom pro zásady uchovávání informací s `allowProtectedAppendWrites` povolenou vlastností. Další informace najdete v části [Povolení chráněných objektů BLOB pro zápis](#allow-protected-append-blobs-writes) .

## <a name="pricing"></a>Ceny

Za použití této funkce se neúčtují žádné další poplatky. Neproměnlivá data se účtují stejným způsobem jako proměnlivá data. Podrobnosti o cenách služby Azure Blob Storage najdete na [stránce s cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Časté otázky

**Máte k dispozici dokumentaci týkající se dodržování předpisů WORM?**

Ano. Aby bylo možné dokumentovat dodržování předpisů, společnost Microsoft zachovala přední nezávislý podnik hodnocení, který se specializuje na správu záznamů a zásady správného řízení informací, Cohasset přidruží k vyhodnocení neměnného úložiště objektů BLOB a jeho dodržování požadavků specifických pro odvětví finančních služeb. Cohasset ověřil, že neměnné úložiště objektů blob, pokud se používá k uchování časových objektů BLOB ve stavu ČERVa, splňuje požadavky na úložiště CFTC pravidla 1.31 (c) – (d), FINRA Rule 4511 a SEK pravidlo 17a-4. Microsoft cílí na tuto sadu pravidel, protože představují nejdůležitější Doporučené postupy globálně pro uchovávání záznamů pro finanční instituce. Sestava Cohasset je k dispozici v [Centru zabezpečení služby společnosti Microsoft](https://aka.ms/AzureWormStorage). Pokud chcete požádat o ověření od Microsoftu ohledně dodržování předpisů proti neměnnosti WORM, obraťte se prosím na podporu Azure.

**Platí tato funkce jenom pro objekty blob bloku a doplňovací objekty blob, nebo i pro objekty blob stránky?**

Neměnné úložiště lze použít s libovolným typem objektu blob, protože je nastaveno na úrovni kontejneru, ale doporučujeme, abyste pro kontejnery, které hlavně ukládají objekty blob bloku a doplňovací objekty blob, používali ČERVa. Existující objekty BLOB v kontejneru budou chráněny nově nastavenými zásadami WORM. Ale všechny nové objekty blob stránky je potřeba vytvořit vně kontejneru ČERVa a pak je zkopírovat do. Po zkopírování do kontejneru WORM nejsou povoleny žádné další změny objektu blob stránky. Nastavení zásad ČERVa v kontejneru, který ukládá virtuální pevné disky (objekty blob stránky) pro všechny aktivní virtuální počítače, se nedoporučuje, protože se zamkne disk virtuálního počítače. Doporučujeme důkladně si projít dokumentaci a vyzkoušet si své scénáře, abyste mohli uzamknout jakékoli zásady založené na čase.

**Potřebuji vytvořit nový účet úložiště, abyste mohli tuto funkci používat?**

Ne, můžete použít neměnné úložiště s existujícími nebo nově vytvořenými BlockBlobStorage účty pro obecné účely V1, BlobStorage a. Jsou podporovány účty úložiště pro obecné účely V1, ale doporučujeme provést upgrade na obecné účely v2, aby bylo možné využít více funkcí. Informace o tom, jak upgradovat existující účet úložiště pro obecné účely V1, najdete v tématu [upgrade účtu úložiště](../common/storage-account-upgrade.md).

**Můžu použít právní zásady uchovávání informací i na základě času?**

Ano, kontejner může současně obsahovat i právní blokování a zásady uchovávání informací na základě času; nastavení "allowProtectedAppendWrites" ale nebude platit, dokud není nezaškrtnuté právní blokování. Všechny objekty blob v kontejneru zůstanou v neměnném stavu, dokud se nezruší všechna blokování z právních důvodů, a to i poté, co u nich uplyne účinná doba uchovávání informací. A naopak, objekt blob zůstane v neměnném stavu, dokud neuplyne účinná doba uchovávání informací, a to i po zrušení všech blokování z právních důvodů. 

**Jsou právní zásady uchovávání jenom pro právní jednání nebo existují jiné scénáře použití?**

Ne, právní blokování je jenom obecný pojem, který se používá pro zásady uchovávání informací, které nejsou založené na čase. Nemusí se používat jenom pro řízení v souvislosti s řízením sporů. Zásady právního blokování jsou užitečné pro zakázání přepsání a odstranění pro ochranu důležitých podnikových dat virů, kde doba uchování není známá. Můžete ji použít jako podnikovou zásadu k ochraně vašich důležitých úloh WORM a jejich použití jako pracovní zásady před tím, než vlastní Trigger událostí vyžaduje použití zásady uchovávání informací podle času. 

**Můžu odebrat _uzamčené_ zásady uchovávání informací podle času nebo právní blokování?**

Z kontejneru lze odebrat pouze odemčené zásady uchovávání informací podle času. Jakmile jsou zásady uchovávání informací na základě času uzamčené, nejde odebrat. povolena jsou pouze efektivní rozšíření doby uchování. Je možné odstranit značky právního blokování. Po odstranění všech právních značek se odebere právní blokování.

**Co se stane, když se pokusím odstranit kontejner se zásadami uchovávání informací na základě času nebo s právním blokováním?**

Operace odstranění kontejneru selže, pokud v kontejneru existuje alespoň jeden objekt BLOB se zamčenou nebo odemčenou zásadou uchovávání informací založenou na čase nebo pokud má kontejner právní blokování. Operace odstranění kontejneru bude úspěšná jenom v případě, že v kontejneru neexistují žádné objekty BLOB a neexistují žádné právní předpisy. 

**Co se stane, když se pokusím odstranit účet úložiště s kontejnerem, který má zásady uchovávání informací založené na čase nebo právní blokování?**

Odstranění účtu úložiště se nepovede, pokud je aspoň jeden kontejner s právním nebo **uzamčenou** zásadou založenou na čase. Kontejner s odemknutou zásadou založenou na čase nechrání před odstraněním účtu úložiště. Předtím, než budete moci odstranit účet úložiště, je nutné odebrat všechny zákonné blokování a odstranit všechny **uzamčené** kontejnery. Informace o odstranění kontejneru najdete na předchozí otázce. Pro svůj účet úložiště můžete taky použít ochranu pomocí [Azure Resource Manager zámků](../../azure-resource-manager/management/lock-resources.md).

**Můžu přesunout data napříč různými úrovněmi objektů BLOB (horká, studená, archivní), když je objekt BLOB v neměnném stavu?**

Ano, příkaz nastavit úroveň objektu blob můžete použít k přesunu dat napříč vrstvami objektů BLOB a zachování dat v neměnném stavu kompatibility. Neměnné úložiště je podporováno na horké, studené a archivní úrovni objektů BLOB.

**Co se stane, když zapomenu zaplatit a můj interval uchovávání informací ještě nevypršel?**

V případě nevýplaty se budou běžné zásady uchovávání dat vztahovat na vymezené podmínky a ujednání vaší smlouvy s Microsoftem. Obecné informace najdete v tématu [Správa dat v Microsoftu](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Nabízíte zkušební období nebo období odkladu pouze na vyzkoušení této funkce?**

Ano. Když se poprvé vytvoří zásady uchovávání informací založené na čase, je v *odemčeném* stavu. V tomto stavu můžete provést jakoukoli požadovanou změnu intervalu uchování, jako je například zvýšení nebo snížení, a dokonce zásadu odstranit. Jakmile je zásada uzamčená, zůstane zamčená, dokud neuplyne interval uchovávání. Tyto uzamčené zásady zabrání odstranění a úpravám do intervalu uchování. Důrazně doporučujeme použít stav *odemčeno* pouze pro účely zkušební verze a uzamknout zásadu během 24 hodin. Tyto postupy vám pomůžou v dodržování předpisů s výjimkou 17a – 4 (f) a dalšími předpisy.

**Můžu použít obnovitelné odstranění společně se zásadami neproměnlivého objektu BLOB?**

Ano, pokud vaše požadavky na dodržování předpisů umožňují povolit obnovitelné odstranění. [Obnovitelné odstranění pro úložiště objektů BLOB v Azure](storage-blob-soft-delete.md) platí pro všechny kontejnery v rámci účtu úložiště bez ohledu na to, jakou dobu zablokují nebo zásady uchovávání informací podle času. Před použitím a potvrzením jakýchkoli neměnitelných zásad WORM doporučujeme povolit obnovitelné odstranění pro další ochranu.

## <a name="next-steps"></a>Další kroky

- [Nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](storage-blob-immutability-policies-manage.md)
- [Nastavení pravidel pro automatické vytvoření vrstev a odstraňování dat objektů BLOB pomocí správy životního cyklu](storage-lifecycle-management-concepts.md)
- [Obnovitelné odstranění objektů blob služby Azure Storage](../blobs/storage-blob-soft-delete.md)
- [Chraňte předplatná, skupiny prostředků a prostředky pomocí Azure Resource Manager zámků](../../azure-resource-manager/management/lock-resources.md).
