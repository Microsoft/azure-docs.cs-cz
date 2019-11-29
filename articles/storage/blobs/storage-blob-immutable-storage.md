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
ms.openlocfilehash: 92bfa4f13467763fd88b9ae993554aef69355d75
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555237"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Ukládání důležitých podnikových dat objektů BLOB s neměnném úložištěm

Neměnné úložiště pro úložiště objektů BLOB v Azure umožňuje uživatelům ukládat datové objekty kritické pro podnikání do ČERVa (psát jednou, číst mnoho). Tento stav zpřístupňuje data, která nejsou Erasable a není upravitelná pro interval zadaný uživatelem. Po dobu trvání intervalu uchování dat je možné objekty blob vytvořit a číst, ale nelze je upravit ani odstranit. K dispozici je neměnné úložiště pro účty pro obecné účely v2 a BLOB Storage ve všech oblastech Azure.

Informace o tom, jak pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure, nastavit a vymazat právní zásady uchovávání informací, najdete v tématu [nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](storage-blob-immutability-policies-manage.md).

## <a name="about-immutable-blob-storage"></a>Neměnné úložiště objektů BLOB

Neměnné úložiště pomáhá organizacím v oblasti zdravotní péče, finančním institucím a souvisejícím odvětvím&mdash;, což je zvláštní zprostředkovatel – organizace pro dealery&mdash;ukládat data bezpečně. Neměnné úložiště je také možné využít v jakémkoli scénáři k ochraně důležitých dat proti úpravám nebo odstranění.

Mezi typické případy použití patří:

- **Dodržování**předpisů: neměnné úložiště pro úložiště objektů BLOB v Azure pomáhá organizacím s výjimkou 17a-4 (f), CFTC 1.31 (d), FINRA a dalšími předpisy. Technický dokument White Paper od Cohasset přidruží k podrobnostem o tom, jak neproměnlivé úložiště řeší tyto zákonné požadavky, ke stažení prostřednictvím [portálu Microsoft Trust Service](https://aka.ms/AzureWormStorage). [Centrum zabezpečení Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) obsahuje podrobné informace o našich certifikátech dodržování předpisů.

- **Zabezpečené uchovávání dokumentů**: neměnné úložiště pro úložiště objektů BLOB v Azure zajišťuje, že data nelze upravovat ani odstraňovat žádného uživatele, včetně uživatelů s oprávněními správce účtu.

- **Právní blokování**: neměnné úložiště pro úložiště objektů BLOB v Azure umožňuje uživatelům ukládat citlivé informace, které jsou zásadní pro soudní spory nebo obchodní použití v případě, že se blokování neodstraní. Tato funkce není omezená jenom na případy právního použití, ale dá se taky představit jako blok založený na událostech nebo na podnikovém zámku, kde je potřeba chránit data na základě triggerů událostí nebo podnikových zásad.

Neměnné úložiště podporuje následující funkce:

- **[Podpora zásad uchovávání na základě času](#time-based-retention-policies)** : uživatelé můžou nastavit zásady pro ukládání dat v zadaném intervalu. Když se nastaví zásady uchovávání informací založené na čase, můžou se objekty blob vytvářet a číst, ale ne upravovat ani odstraňovat. Po vypršení doby uchování je možné objekty blob odstranit, ale nebudou přepsány.

- **[Podpora zásad právního blokování](#legal-holds)** : Pokud není Interval uchování známý, můžou uživatelé nastavit právní blokování na ukládání neproměnlivých dat, dokud se neodstraní právní blokování.  Pokud je nastavena zásada právního blokování, lze objekty blob vytvořit a číst, ale nikoli upravovat ani odstraňovat. Každé právní blokování je přidruženo k uživatelsky definované alfanumerické značce (například ID případu, název události atd.), které se používají jako řetězec identifikátoru. 

- **Podpora pro všechny úrovně objektů BLOB**: zásady červa jsou nezávislé na úrovni úložiště objektů BLOB v Azure a platí pro všechny úrovně: horká, studená a archivní. Uživatelé můžou při zachování dat neměnnosti data do nejvyšší úrovně optimalizované pro své úlohy na nejvyšší náklady.

- **Konfigurace na úrovni kontejneru**: uživatelé můžou na úrovni kontejneru nakonfigurovat zásady uchovávání informací založené na čase a značky právního blokování. Díky použití jednoduchých nastavení na úrovni kontejneru můžou uživatelé vytvářet a zamykat zásady uchovávání informací založené na čase, nastavovat a zablokovat, nastavovat a mazat právní omezení. Tyto zásady se vztahují na všechny objekty BLOB v kontejneru, a to stávající i nové.

- **Podpora protokolování auditu**: každý kontejner zahrnuje protokol auditu zásad. Zobrazuje až sedm příkazů pro uchovávání informací na základě času pro uzamčené zásady uchovávání informací podle času a obsahuje ID uživatele, typ příkazu, časová razítka a interval uchovávání. V případě právního blokování obsahuje protokol ID uživatele, typ příkazu, časová razítka a značky právního blokování. Tento protokol se zachovává po dobu života zásady, v souladu s pravidly pro legislativní SEK – 17a (f). [Protokol aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md) zobrazuje komplexnější protokol všech aktivit řídicích rovin; zatímco povolení [diagnostických protokolů Azure](../../azure-monitor/platform/resource-logs-overview.md) zachovává a zobrazuje operace roviny dat. Je zodpovědností uživatele ukládat tyto protokoly trvale, jako by se vyžadovalo pro regulativní nebo jiné účely.

## <a name="how-it-works"></a>Jak to funguje

Neměnné úložiště pro úložiště objektů BLOB v Azure podporuje dva typy ČERVů nebo neproměnlivých zásad: uchování založené na čase a právní blokování. Pokud je na kontejneru použita zásada uchovávání informací založená na čase nebo právní blokování, všechny existující objekty BLOB se přesunou do neměnného stavu ČERVa za méně než 30 sekund. Všechny nové objekty blob nahrané do tohoto kontejneru se také přesunou do neměnného stavu. Po přesunutí všech objektů blob do neměnných stavů se potvrdí neměnné zásady a všechny operace přepsání nebo odstranění pro existující a nové objekty v neměnném kontejneru nejsou povolené.

Odstranění kontejneru a účtu úložiště není povolené, pokud existují objekty BLOB v kontejneru nebo účtu úložiště, které jsou chráněné neproměnlivými zásadami. Operace odstranění kontejneru selže, pokud alespoň jeden objekt BLOB existuje se zamčenými zásadami uchovávání informací založenými na čase nebo s právním blokováním. Operace odstranění účtu úložiště selže, pokud je k dispozici alespoň jeden kontejner ČERVa s právním blokováním nebo objekt BLOB s aktivním intervalem uchování.

### <a name="time-based-retention-policies"></a>Zásady uchovávání informací podle času

> [!IMPORTANT]
> Zásady uchovávání informací založené na čase musí být *uzamčené* , aby objekt BLOB byl ve stavu kompatibilním s neproměnlivým stavem (Write and Delete Protected) pro SEK-4 (f) a další zákonné dodržování předpisů. Doporučujeme zásady uzamknout v rozumné době, většinou méně než 24 hodin. Počáteční stav použitých zásad uchovávání dat je *odemčený*, což vám umožní otestovat funkci a provést změny zásad před jejich uzamknutím. I když *odemčený* stav poskytuje ochranu neměnnosti, nedoporučujeme používat *odemčený* stav pro jiné účely, než krátkodobé testy funkcí. 

Pokud jsou na kontejneru aplikovány zásady uchovávání informací založené na čase, všechny objekty BLOB v kontejneru zůstanou v neměnném stavu po dobu trvání *efektivní* doby uchování. Efektivní doba uchovávání informací pro stávající objekty blob se rovná rozdílu mezi časem vytvoření objektu blob a uživatelem zadaným intervalem uchovávání informací.

Pro nové objekty blob se efektivní doba uchovávání informací rovná uživatelem zadanému intervalu uchovávání informací. Vzhledem k tomu, že uživatelé můžou roztáhnout interval uchovávání dat, neměnné úložiště používá k výpočtu efektivní doby uchování nejnovější hodnotu intervalu pro uchování zadaného uživatelem.

Předpokládejme například, že uživatel vytvoří zásady uchovávání dat s intervalem uchování na pět let. Stávající objekt BLOB v tomto kontejneru, _testblob1_, byl vytvořen před rokem. Efektivní doba uchování pro _testblob1_ je 4 roky. Když se do kontejneru nahraje nový objekt blob, _testblob2_, efektivní doba uchování nového objektu BLOB je pět let.

Odemčené zásady uchovávání informací založené na čase se doporučují jenom pro testování funkcí a zásady musí být uzamčené, aby byly kompatibilní s SEK-4 (f) a dalšími předpisy pro dodržování předpisů. Jakmile jsou zásady uchovávání informací založené na čase uzamčené, zásada se nedá odebrat a maximálně pět zvýšení k efektivnímu období uchování je povolené. Další informace o tom, jak nastavit a uzamknout zásady uchovávání informací podle časových údajů, najdete v tématu [nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](storage-blob-immutability-policies-manage.md).

Následující omezení platí pro zásady uchovávání informací:

- V případě účtu úložiště je maximální počet kontejnerů, které mají uzamčené zásady s neproměnlivým časem, 1 000.
- Minimální interval uchovávání dat je jeden den. Maximální počet je 146 000 dní (400 let).
- V případě kontejneru je maximální počet úprav pro prodloužení intervalu uchování pro neměnné zásady na základě času 5.
- V případě kontejneru jsou pro uzamčenou zásadu uchovány maximálně sedm protokolů auditu zásad uchovávání času.

### <a name="legal-holds"></a>Blokování z právních důvodů

Když nastavíte právní blokování, všechny existující a nové objekty blob zůstanou v neměnném stavu, dokud se neodstraní právní blokování. Další informace o tom, jak nastavit a vymazat právní blokování, najdete v tématu [nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](storage-blob-immutability-policies-manage.md).

Kontejner může současně obsahovat i právní blokování i zásady uchovávání informací podle času. Všechny objekty BLOB v tomto kontejneru zůstávají v neměnném stavu, dokud se nevymaže všechna zákonná blokování, a to i v případě, že vypršela doba uchování platnosti. V opačném případě objekt BLOB zůstane v neměnném stavu, dokud nevyprší doba uchování, a to i v případě, že všechna zákonná blokování byla vymazána.

V následující tabulce jsou uvedeny typy operací úložiště objektů blob, které jsou zakázané pro různé neměnné scénáře. Další informace najdete v dokumentaci ke [službě Azure Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) .

|Scénář  |Stav objektu BLOB  |Operace objektu BLOB nejsou povoleny.  |
|---------|---------|---------|
|Efektivní interval uchovávání informací pro objekt blob ještě nevypršel a/nebo je nastavené blokování z právních důvodů     |Neměnné: chráněné proti odstranění i zápisu         | Vložte objekt BLOB<sup>1</sup>, PUT blok<sup>1</sup>, PUT list list<sup>1</sup>, odstranit kontejner, odstranit objekt blob, nastavte metadata objektu blob, vložte stránku, nastavte vlastnosti objektů blob, objekt BLOB snímku, přírůstkové kopírování objektu blob, připojit blok.         |
|Vypršel efektivní interval uchovávání informací pro objekt blob     |Chráněné pouze proti zápisu (operace odstranění jsou povolené)         |Vložte objekt BLOB<sup>1</sup>, PUT blok<sup>1</sup>, PUT seznam blokování<sup>1</sup>, nastavte metadata objektu blob, PUT, nastavte vlastnosti objektů blob, objekt BLOB snímku, objekt BLOB přírůstkového kopírování, doplňovací blok.         |
|Všechny právní předpisy jsou nezaškrtnuté a v kontejneru nejsou nastavené žádné zásady uchovávání informací podle času.     |Měnitelné         |Žádné         |
|Nevytvořily se žádné zásady ČERVů (uchovávání na základě času nebo právní blokování).     |Měnitelné         |Žádné         |

<sup>1</sup> aplikace umožňuje těmto operacím vytvořit nový objekt BLOB jednou. Všechny následné operace přepsání na stávající cestě objektu BLOB v neměnitelném kontejneru nejsou povoleny.

Následující omezení platí pro právní blokování:

- V případě účtu úložiště je maximální počet kontejnerů s nastavením právního blokování 1 000.
- V případě kontejneru je maximální počet platných značek blokování 10.
- Minimální délka značky právního blokování je tři alfanumerické znaky. Maximální délka je 23 alfanumerických znaků.
- V případě kontejneru se pro dobu trvání zásady uchovávají maximálně 10 protokolů auditu zásad uchovávání předpisů.

## <a name="pricing"></a>Ceny

Za použití této funkce se neúčtují žádné další poplatky. Neproměnlivá data se účtují stejným způsobem jako proměnlivá data. Podrobnosti o cenách služby Azure Blob Storage najdete na [stránce s cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Časté otázky

**Máte k dispozici dokumentaci týkající se dodržování předpisů WORM?**

Ano. Aby bylo možné dokumentovat dodržování předpisů, společnost Microsoft si zachovala přední nezávislý podnik hodnocení, který se specializuje na správu záznamů a zásady správného řízení informací, Cohasset přidruží k vyhodnocení neměnného úložiště objektů BLOB a dodržování požadavků specifických pro odvětví finančních služeb. Cohasset ověřil, že neměnné úložiště objektů blob, pokud se používá k uchování časových objektů BLOB ve stavu ČERVa, splňuje požadavky na úložiště CFTC pravidla 1.31 (c) – (d), FINRA Rule 4511 a SEK pravidlo 17a-4. Microsoft cílí na tuto sadu pravidel, protože představují nejdůležitější Doporučené postupy globálně pro uchovávání záznamů pro finanční instituce. Sestava Cohasset je k dispozici v [Centru zabezpečení služby společnosti Microsoft](https://aka.ms/AzureWormStorage). Pokud chcete požádat o ověření od Microsoftu ohledně dodržování předpisů WORM, obraťte se prosím na podporu Azure.

**Vztahuje se tato funkce jenom na objekty blob bloku nebo na stránky a doplňovací objekty blob?**

Neměnné úložiště lze použít s libovolným typem objektu blob, protože je nastaveno na úrovni kontejneru, ale doporučujeme použít ČERVa pro kontejnery, které hlavně ukládají objekty blob bloku. Na rozdíl od objektů blob bloku musí být všechny nové objekty blob stránky a doplňovací objekty blob vytvořené vně kontejneru WORM a pak se zkopírují do. Po zkopírování těchto objektů blob do kontejneru ČERVa nejsou povoleny žádné další *připojení* k doplňovacímu objektu BLOB nebo změnám objektu blob stránky. Nastavení zásad ČERVa v kontejneru, který ukládá virtuální pevné disky (objekty blob stránky) pro všechny aktivní virtuální počítače, se nedoporučuje, protože se zamkne disk virtuálního počítače.

**Potřebuji vytvořit nový účet úložiště, abyste mohli tuto funkci používat?**

Ne, můžete použít neměnné úložiště s existujícími nebo nově vytvořenými účty úložiště pro obecné účely verze 2 nebo BLOB. Tato funkce je určená pro použití s objekty blob bloku v účtech GPv2 a BLOB Storage. Účty úložiště pro obecné účely V1 se nepodporují, ale dají se snadno upgradovat na obecné účely v2. Informace o tom, jak upgradovat existující účet úložiště pro obecné účely V1, najdete v tématu [upgrade účtu úložiště](../common/storage-account-upgrade.md).

**Můžu použít právní zásady uchovávání informací i na základě času?**

Ano, kontejner může současně obsahovat i právní blokování i zásady uchovávání informací podle času. Všechny objekty BLOB v tomto kontejneru zůstávají v neměnném stavu, dokud se nevymaže všechna zákonná blokování, a to i v případě, že vypršela doba uchování platnosti. V opačném případě objekt BLOB zůstane v neměnném stavu, dokud nevyprší doba uchování, a to i v případě, že všechna zákonná blokování byla vymazána.

**Jsou právní zásady uchovávání jenom pro právní jednání nebo existují jiné scénáře použití?**

Ne, právní blokování je jenom obecný pojem, který se používá pro zásady uchovávání informací, které nejsou založené na čase. Nemusí se používat jenom pro řízení v souvislosti s řízením sporů. Zásady právního blokování jsou užitečné pro zakázání přepsání a odstranění pro ochranu důležitých podnikových dat virů, kde doba uchování není známá. Můžete ji použít jako podnikovou zásadu k ochraně vašich důležitých úloh WORM a jejich použití jako pracovní zásady před tím, než vlastní Trigger událostí vyžaduje použití zásady uchovávání informací podle času. 

**Můžu odebrat _uzamčené_ zásady uchovávání informací podle času nebo právní blokování?**

Z kontejneru lze odebrat pouze odemčené zásady uchovávání informací podle času. Jakmile jsou zásady uchovávání informací na základě času uzamčené, nejde odebrat. povolena jsou pouze efektivní rozšíření doby uchování. Je možné odstranit značky právního blokování. Po odstranění všech právních značek se odebere právní blokování.

**Co se stane, když se pokusím odstranit kontejner s *uzamknutou* zásadou uchovávání informací podle času nebo blokováním z právních důvodů?**

Operace odstranění kontejneru selže, pokud alespoň jeden objekt BLOB existuje se zamčenými zásadami uchovávání informací založenými na čase nebo s právním blokováním. Operace odstranění kontejneru bude úspěšná pouze v případě, že žádný objekt BLOB s aktivním intervalem uchování neexistuje a žádné právní předpisy nejsou uloženy. Aby bylo možné kontejner odstranit, je nutné objekty blob odstranit.

**Co se stane, když se pokusím odstranit účet úložiště obsahující kontejner WORM s *uzamknutou* zásadou uchovávání informací podle času nebo blokováním z právních důvodů?**

Odstranění účtu úložiště selže, pokud obsahuje alespoň jeden kontejner WORM s blokováním z právních důvodů nebo objektem blob s aktivním intervalem uchovávání informací. Než budete moct odstranit účet úložiště, musíte odstranit všechny kontejnery WORM. Informace o odstranění kontejneru najdete na předchozí otázce.

**Můžu přesouvat data mezi různými úrovněmi objektu blob (horká, studená, archivní), když je objekt blob v neměnném stavu?**

Ano, příkaz nastavit úroveň objektu blob můžete použít k přesunu dat napříč vrstvami objektů BLOB a zachování dat v neměnném stavu kompatibility. Neměnné úložiště je podporováno na horké, studené a archivní úrovni objektů BLOB.

**Co se stane, když zapomenu zaplatit a můj interval uchovávání informací ještě nevypršel?**

V případě nevýplaty se budou běžné zásady uchovávání dat vztahovat na vymezené podmínky a ujednání vaší smlouvy s Microsoftem.

**Nabízíte zkušební období nebo období odkladu pouze na vyzkoušení této funkce?**

Ano. Když se poprvé vytvoří zásady uchovávání informací založené na čase, je v *odemčeném* stavu. V tomto stavu můžete provést jakoukoli požadovanou změnu intervalu uchování, jako je například zvýšení nebo snížení, a dokonce zásadu odstranit. Jakmile je zásada uzamčená, zůstane zamčená, dokud neuplyne interval uchovávání. Tyto uzamčené zásady zabrání odstranění a úpravám do intervalu uchování. Důrazně doporučujeme použít stav *odemčeno* pouze pro účely zkušební verze a uzamknout zásadu během 24 hodin. Tyto postupy vám pomůžou v dodržování předpisů s výjimkou 17a – 4 (f) a dalšími předpisy.

**Můžu použít obnovitelné odstranění společně se zásadami neproměnlivého objektu BLOB?**

Ano. [Obnovitelné odstranění pro úložiště objektů BLOB v Azure](storage-blob-soft-delete.md) platí pro všechny kontejnery v rámci účtu úložiště bez ohledu na to, jakou dobu zablokují nebo zásady uchovávání informací podle času. Před použitím a potvrzením jakýchkoli neměnitelných zásad WORM doporučujeme povolit obnovitelné odstranění pro další ochranu.

**Kde je funkce k dispozici?**

Neměnné úložiště je dostupné v oblastech veřejné, čínské a státní správy Azure. Pokud v oblasti není k dispozici žádné neměnné úložiště, obraťte se prosím na podporu a azurestoragefeedback@microsoft.come-mailem.

## <a name="next-steps"></a>Další kroky

[Nastavení a Správa zásad neměnnosti pro úložiště objektů BLOB](storage-blob-immutability-policies-manage.md)