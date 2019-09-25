---
title: Neměnné úložiště pro objekty blob Azure Storage | Microsoft Docs
description: Azure Storage nabízí pro úložiště objektů BLOB (Object) možnost WORM (napsat jednou, číst mnoho), která umožňuje uživatelům ukládat data v neerasablem stavu, který se v zadaném intervalu nedají měnit.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/01/2019
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: fcc5c4008c0fdef3b77e436761d8958fe31458d8
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257375"
---
# <a name="store-business-critical-data-in-azure-blob-storage-immutably"></a>Ukládání důležitých podnikových dat do služby Azure Blob Storage immutably 

Neměnné úložiště pro úložiště objektů BLOB v Azure umožňuje uživatelům ukládat datové objekty kritické pro podnikání do ČERVa (psát jednou, číst mnoho). Tento stav zpřístupňuje data, která nejsou Erasable a není upravitelná pro interval zadaný uživatelem. Pro dobu trvání intervalu uchování lze vytvořit a číst objekty objektů blob, ale nikoli upravit ani odstranit. Pro Pro obecné účely v2 a účty Blob Storage ve všech oblastech Azure je povolené neměnné úložiště.

## <a name="overview"></a>Přehled

Neměnné úložiště pomáhá organizacím v oblasti zdravotní péče, finančním institucím a souvisejícím odvětvím – zejména k bezpečnému ukládání dat v organizacích pro dealery. Dá se taky využít v jakémkoli scénáři k ochraně důležitých dat proti úpravám nebo odstranění. 

Mezi typické případy použití patří:

- **Dodržování předpisů**: Neměnné úložiště pro Azure Blob Storage pomáhá organizacím s výjimkou 17a-4 (f), CFTC 1.31 (d), FINRA a dalšími předpisy. Technický dokument White Paper od Cohasset přidruží k podrobnostem o tom, jak neproměnlivé úložiště řeší tyto zákonné požadavky, ke stažení prostřednictvím [portálu Microsoft Trust Service](https://aka.ms/AzureWormStorage). [Centrum zabezpečení Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) obsahuje podrobné informace o našich certifikátech dodržování předpisů.

- **Zabezpečené uchovávání dokumentů**: Neměnné úložiště pro úložiště objektů BLOB v Azure zajišťuje, že data nemůžete upravovat ani odstraňovat žádný uživatel, včetně uživatelů s oprávněními pro správu účtu.

- **Právní blokování**: Neměnné úložiště pro úložiště objektů BLOB v Azure umožňuje uživatelům ukládat citlivé informace, které jsou pro soudní spory nebo obchodní použití ve stavu manipulace po manipulaci, až po odebrání blokování. Tato funkce není omezená jenom na případy právního použití, ale dá se taky představit jako blok založený na událostech nebo na podnikovém zámku, kde je potřeba chránit data na základě triggerů událostí nebo podnikových zásad.

Neměnné úložiště podporuje následující:

- **[Podpora zásad uchovávání na základě času](#time-based-retention)** : Uživatelé můžou nastavit zásady pro ukládání dat v zadaném intervalu. Když se nastaví zásady uchovávání informací založené na čase, můžou se objekty blob vytvářet a číst, ale ne upravovat ani odstraňovat. Po vypršení doby uchování je možné objekty blob odstranit, ale nebudou přepsány.

- **[Podpora zásad právního blokování](#legal-holds)** : Pokud není interval uchovávání znám, uživatelé můžou nastavit právní blokování na ukládání dat immutably, dokud se neodstraní právní blokování.  Pokud je nastavena zásada právního blokování, lze objekty blob vytvořit a číst, ale nikoli upravovat ani odstraňovat. Každé právní blokování je přidruženo k uživatelsky definované alfanumerické značce (například ID případu, název události atd.), které se používají jako řetězec identifikátoru. 

- **Podpora pro všechny úrovně objektů BLOB**: Zásady ČERVa jsou nezávislé na úrovni úložiště objektů BLOB v Azure a platí pro všechny úrovně: horká, studená a archivní. Uživatelé můžou při zachování dat neměnnosti data do nejvyšší úrovně optimalizované pro své úlohy na nejvyšší náklady.

- **Konfigurace na úrovni kontejneru**: Uživatelé můžou na úrovni kontejneru nakonfigurovat zásady uchovávání informací založené na čase a značky právního blokování. Díky použití jednoduchých nastavení na úrovni kontejneru můžou uživatelé vytvářet a zamykat zásady uchovávání informací založené na čase, nastavovat a zablokovat, nastavovat a mazat právní omezení. Tyto zásady se vztahují na všechny objekty BLOB v kontejneru, a to stávající i nové.

- **Podpora protokolování auditu**: Každý kontejner obsahuje protokol auditu zásad. Zobrazuje až sedm příkazů pro uchovávání informací na základě času pro uzamčené zásady uchovávání informací podle času a obsahuje ID uživatele, typ příkazu, časová razítka a interval uchovávání. V případě právního blokování obsahuje protokol ID uživatele, typ příkazu, časová razítka a značky právního blokování. Tento protokol se zachovává po dobu života zásady, v souladu s pravidly pro legislativní SEK – 17a (f). [Protokol aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md) zobrazuje komplexnější protokol všech aktivit řídicích rovin; zatímco povolení [diagnostických protokolů Azure](../../azure-monitor/platform/resource-logs-overview.md) zachovává a zobrazuje operace roviny dat. Je zodpovědností uživatele ukládat tyto protokoly trvale, jako by se vyžadovalo pro regulativní nebo jiné účely.

## <a name="how-it-works"></a>Jak to funguje

Neměnné úložiště pro úložiště objektů BLOB v Azure podporuje dva typy ČERVů nebo neproměnlivých zásad: uchování založené na čase a právní blokování. Pokud je na kontejneru použita zásada uchovávání informací založená na čase nebo právní blokování, všechny existující objekty BLOB se přesunou do neměnného stavu ČERVa za méně než 30 sekund. Všechny nové objekty blob nahrané do tohoto kontejneru se také přesunou do neměnného stavu. Po přesunutí všech objektů blob do neměnných stavů se potvrdí neměnné zásady a všechny operace přepsání nebo odstranění pro existující a nové objekty v neměnném kontejneru nejsou povolené.

Odstranění kontejneru a účtu se taky nepovoluje, pokud jsou nějaké objekty blob chráněné neproměnlivou zásadou. Operace odstranění kontejneru selže, pokud alespoň jeden objekt BLOB existuje se zamčenými zásadami uchovávání informací založenými na čase nebo s právním blokováním. Odstranění účtu úložiště selže, pokud obsahuje alespoň jeden kontejner WORM s blokováním z právních důvodů nebo objektem blob s aktivním intervalem uchovávání informací. 

### <a name="time-based-retention"></a>Uchovávání na základě času

> [!IMPORTANT]
> Zásady uchovávání informací založené na čase musí být *uzamčené* , aby objekt BLOB byl ve stavu kompatibilním s neproměnlivým stavem (Write and Delete Protected) pro SEK-4 (f) a další zákonné dodržování předpisů. Doporučujeme zásady uzamknout v rozumné době, většinou méně než 24 hodin. Počáteční stav použitých zásad uchovávání dat je *odemčený*, což vám umožní otestovat funkci a provést změny zásad před jejich uzamknutím. I když *odemčený* stav poskytuje ochranu neměnnosti, nedoporučujeme používat *odemčený* stav pro jiné účely, než krátkodobé testy funkcí. 

Pokud jsou na kontejneru aplikovány zásady uchovávání informací založené na čase, všechny objekty BLOB v kontejneru zůstanou v neměnném stavu po dobu trvání *efektivní* doby uchování. Efektivní doba uchovávání informací pro stávající objekty blob se rovná rozdílu mezi časem vytvoření objektu blob a uživatelem zadaným intervalem uchovávání informací.

Pro nové objekty blob se efektivní doba uchovávání informací rovná uživatelem zadanému intervalu uchovávání informací. Vzhledem k tomu, že uživatelé můžou roztáhnout interval uchovávání dat, neměnné úložiště používá k výpočtu efektivní doby uchování nejnovější hodnotu intervalu pro uchování zadaného uživatelem.

> [!TIP]
> **Příklad:** Uživatel vytvoří zásady uchovávání informací založené na čase s intervalem uchování pět let.
>
> Stávající objekt BLOB v tomto kontejneru, _testblob1_, byl vytvořen před rokem. Efektivní doba uchování pro _testblob1_ je 4 roky.
>
> Nový objekt blob, _testblob2_, se teď nahraje do kontejneru. Efektivní doba uchování tohoto nového objektu BLOB je pět let.

Odemčené zásady uchovávání informací založené na čase se doporučují jenom pro testování funkcí a zásady musí být uzamčené, aby byly kompatibilní s SEK-4 (f) a dalšími předpisy pro dodržování předpisů. Když jsou zásady uchovávání informací založené na čase uzamčené, zásada se nedá odebrat a je povolená maximálně 5 zvýšení na efektivní dobu uchování. Další informace o tom, jak nastavit a uzamknout zásady uchovávání informací podle časových údajů, najdete v části [Začínáme](#getting-started) .

### <a name="legal-holds"></a>Blokování z právních důvodů

Když nastavíte právní blokování, všechny existující a nové objekty blob zůstanou v neměnném stavu, dokud se neodstraní právní blokování. Další informace o tom, jak nastavit a vymazat právní blokování, najdete v části [Začínáme](#getting-started) .

Kontejner může současně obsahovat i právní blokování i zásady uchovávání informací podle času. Všechny objekty BLOB v tomto kontejneru zůstávají v neměnném stavu, dokud se nevymaže všechna zákonná blokování, a to i v případě, že vypršela doba uchování platnosti. V opačném případě objekt BLOB zůstane v neměnném stavu, dokud nevyprší doba uchování, a to i v případě, že všechna zákonná blokování byla vymazána.

V následující tabulce jsou uvedeny typy operací objektů blob, které jsou zakázané pro různé neměnné scénáře. Další informace najdete v dokumentaci k [rozhraní API služby Azure Blob Service](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) .

|Scénář  |Stav objektu BLOB  |Operace objektu BLOB nejsou povoleny.  |
|---------|---------|---------|
|Efektivní interval uchovávání informací pro objekt blob ještě nevypršel a/nebo je nastavené blokování z právních důvodů     |Neměnné: chráněné proti odstranění i zápisu         | Vložte objekt BLOB<sup>1</sup>, PUT blok<sup>1</sup>, PUT list list<sup>1</sup>, odstranit kontejner, odstranit objekt blob, nastavte metadata objektu blob, vložte stránku, nastavte vlastnosti objektů blob, objekt BLOB snímku, přírůstkové kopírování objektu blob, připojit blok.         |
|Vypršel efektivní interval uchovávání informací pro objekt blob     |Chráněné pouze proti zápisu (operace odstranění jsou povolené)         |Vložte objekt BLOB<sup>1</sup>, PUT blok<sup>1</sup>, PUT seznam blokování<sup>1</sup>, nastavte metadata objektu blob, PUT, nastavte vlastnosti objektů blob, objekt BLOB snímku, objekt BLOB přírůstkového kopírování, doplňovací blok.         |
|Všechny právní předpisy jsou nezaškrtnuté a v kontejneru nejsou nastavené žádné zásady uchovávání informací podle času.     |Měnitelné         |Žádné         |
|Nevytvořily se žádné zásady ČERVů (uchovávání na základě času nebo právní blokování).     |Měnitelné         |Žádné         |

<sup>1</sup> aplikace umožňuje těmto operacím vytvořit nový objekt BLOB jednou. Všechny následné operace přepsání na stávající cestě objektu BLOB v neměnitelném kontejneru nejsou povoleny.

## <a name="supported-values"></a>Podporované hodnoty

### <a name="time-based-retention"></a>Uchovávání na základě času
- V případě účtu úložiště je maximální počet kontejnerů, které mají uzamčené zásady s neproměnlivým časem, 1 000.
- Minimální interval uchovávání dat je 1 den. Maximální počet je 146 000 dní (400 let).
- V případě kontejneru je maximální počet úprav pro prodloužení intervalu uchování pro neměnné zásady na základě času 5.
- V případě kontejneru se pro zamčené zásady uchovávají maximálně 7 protokolů auditu zásad uchovávání času.

### <a name="legal-hold"></a>Právní důvody
- V případě účtu úložiště je maximální počet kontejnerů s nastavením právního blokování 1 000.
- V případě kontejneru je maximální počet platných značek blokování 10.
- Minimální délka značky právního blokování je 3 alfanumerické znaky. Maximální délka je 23 alfanumerických znaků.
- V případě kontejneru se pro dobu trvání zásady uchovávají maximálně 10 protokolů auditu zásad uchovávání předpisů.

## <a name="pricing"></a>Ceny

Za použití této funkce se neúčtují žádné další poplatky. Neproměnlivá data se účtují stejným způsobem jako běžná a proměnlivá data. Podrobnosti o cenách na Azure Blob Storage najdete na [stránce s cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="getting-started"></a>Začínáme
Neměnné úložiště je k dispozici pouze pro účty Pro obecné účely v2 a Blob Storage. Tyto účty musí být spravované prostřednictvím [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). Informace o tom, jak upgradovat existující účet úložiště Pro obecné účely V1, najdete v tématu [upgrade účtu úložiště](../common/storage-account-upgrade.md).

Nejaktuálnější verze [Azure Portal](https://portal.azure.com), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)a [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) podporují neměnné úložiště pro úložiště objektů BLOB v Azure. K dispozici je také [Podpora klientské knihovny](#client-libraries) .

### <a name="azure-portal"></a>portál Azure

1. Vytvořte nový kontejner nebo vyberte stávající kontejner pro uložení objektů blob, které je potřeba zachovat v neměnném stavu.
 Kontejner musí být v účtu GPv2 nebo BLOB Storage.
2. V nastavení kontejneru vyberte **zásady přístupu** . Pak v části **neměnné úložiště objektů BLOB**vyberte **+ Přidat zásadu** .

    ![Nastavení kontejneru na portálu](media/storage-blob-immutable-storage/portal-image-1.png)

3. Pokud chcete povolit uchovávání na základě času, v rozevírací nabídce vyberte **časovou dobu uchování** .

    !["Doba uchovávání na základě času" vybraná v části "typ zásad"](media/storage-blob-immutable-storage/portal-image-2.png)

4. Zadejte interval uchování ve dnech (přijatelné hodnoty 1 až 146000 dní).

    ![Pole aktualizovat dobu uchování na](media/storage-blob-immutable-storage/portal-image-5-retention-interval.png)

    Počáteční stav zásady je odemčený, což vám umožní otestovat tuto funkci a před tím, než ji uzamknete, provést změny zásad. Uzamčení zásad je nezbytné pro dodržování předpisů s předpisy, jako je například 17a – 4.

5. Zásady uzamknout. Klikněte pravým tlačítkem myši na tři tečky ( **...** ) a zobrazí se následující nabídka s dalšími akcemi:

    !["Zásada zámku" v nabídce](media/storage-blob-immutable-storage/portal-image-4-lock-policy.png)

6. Vyberte **zásady uzamčení** a potvrďte zámek. Zásada je nyní uzamčena a nelze ji odstranit, bude povolena pouze přípona intervalu uchování. Odstranění a přepsání objektu BLOB nejsou povoleny. 

    ![V nabídce potvrďte zásady uzamčení.](media/storage-blob-immutable-storage/portal-image-5-lock-policy.png)

7. Pokud chcete povolit právní blokování, vyberte **+ Přidat zásadu**. Z rozevírací nabídky vyberte **právní blokování** .

    !["Právní blokování" v nabídce v části "typ zásad"](media/storage-blob-immutable-storage/portal-image-legal-hold-selection-7.png)

8. Vytvořte právní blokování s jednou nebo více značkami.

    ![Pole "název značky" pod položkou Typ zásady](media/storage-blob-immutable-storage/portal-image-set-legal-hold-tags.png)

9. Pokud chcete odstranit právní blokování, jednoduše odeberte použitou značku identifikátoru blokování.

### <a name="azure-cli"></a>Azure CLI

Tato funkce je zahrnutá v následujících skupinách příkazů: `az storage container immutability-policy` a `az storage container legal-hold`. Spusťte `-h` na nich, aby se zobrazily příkazy.

### <a name="powershell"></a>PowerShell

Modul AZ. Storage podporuje neměnné úložiště.  Pokud chcete tuto funkci povolit, postupujte takto:

1. Ujistěte se, že máte nainstalovanou nejnovější verzi PowerShellGet: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Odeberte všechny předchozí instalace Azure PowerShell.
3. Nainstalovat Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

[Ukázkový kód PowerShellu](#sample-powershell-code) dále v tomto článku ukazuje použití funkcí.

## <a name="client-libraries"></a>Klientské knihovny

Následující klientské knihovny podporují pro úložiště objektů BLOB v Azure neměnné úložiště:

- [Klientská knihovna .NET verze 7.2.0 – Preview a novější](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/7.2.0-preview)
- [Klientská knihovna Node. js verze 4.0.0 a novější](https://www.npmjs.com/package/azure-arm-storage)
- [Klientská knihovna Python verze 2.0.0 verze Candidate 2 a novější](https://pypi.org/project/azure-mgmt-storage/2.0.0rc2/)
- [Klientská knihovna Java](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/storage/resource-manager/Microsoft.Storage/preview/2018-03-01-preview)

## <a name="faq"></a>Nejčastější dotazy

**Máte k dispozici dokumentaci týkající se dodržování předpisů WORM?**

Ano. Aby bylo možné dokumentovat dodržování předpisů, společnost Microsoft si zachovala přední nezávislý podnik hodnocení, který se specializuje na správu záznamů a zásady správného řízení pro informace, Cohasset přidruží k vyhodnocení neměnného Blob Storagee Azure a dodržování požadavků. do odvětví finančních služeb. Cohasset ověřil, že služba Azure unmutable Blob Storage, pokud se používá k uchování časových objektů blob na základě času, splňuje příslušné požadavky na úložiště CFTC pravidla 1.31 (c) – (d), FINRA Rule 4511 a SEK pravidlo 17a-4. Microsoft cílí na tuto sadu pravidel, protože představují nejdůležitější Doporučené postupy globálně pro uchovávání záznamů pro finanční instituce. Sestava Cohasset je k dispozici v [Centru zabezpečení služby společnosti Microsoft](https://aka.ms/AzureWormStorage). Pokud chcete požádat o ověření od Microsoftu ohledně dodržování předpisů WORM, obraťte se prosím na podporu Azure.

**Vztahuje se tato funkce jenom na objekty blob bloku nebo na stránky a doplňovací objekty blob?**

Neměnné úložiště lze použít s libovolným typem objektu blob, protože je nastaveno na úrovni kontejneru, ale doporučujeme použít ČERVa pro kontejnery, které hlavně ukládají objekty blob bloku. Na rozdíl od objektů blob bloku musí být všechny nové objekty blob stránky a doplňovací objekty blob vytvořené vně kontejneru WORM a pak se zkopírují do. Po zkopírování těchto objektů blob do kontejneru ČERVa nejsou povoleny žádné další *připojení* k doplňovacímu objektu BLOB nebo změnám objektu blob stránky. Proto nastavení zásad ČERVa na kontejneru, který ukládá virtuální pevné disky (objekty blob stránky) pro všechny aktivní Virtual Machines, se důrazně nedoporučuje, protože se zamkne disk virtuálního počítače.

**Potřebuji vytvořit nový účet úložiště, abyste mohli tuto funkci používat?**

Ne, můžete použít neměnné úložiště s existujícími nebo nově vytvořenými účty Pro obecné účely v2 nebo BLOB Storage. Tato funkce je určená pro použití s objekty blob bloku v účtech GPv2 a Blob Storage. Účty úložiště Pro obecné účely V1 se nepodporují, ale dají se snadno upgradovat na Pro obecné účely v2. Informace o tom, jak upgradovat existující účet úložiště Pro obecné účely V1, najdete v tématu [upgrade účtu úložiště](../common/storage-account-upgrade.md).

**Můžu použít právní zásady uchovávání informací i na základě času?**

Ano, kontejner může současně obsahovat i právní blokování i zásady uchovávání informací podle času. Všechny objekty BLOB v tomto kontejneru zůstávají v neměnném stavu, dokud se nevymaže všechna zákonná blokování, a to i v případě, že vypršela doba uchování platnosti. V opačném případě objekt BLOB zůstane v neměnném stavu, dokud nevyprší doba uchování, a to i v případě, že všechna zákonná blokování byla vymazána.

**Jsou právní zásady uchovávání jenom pro právní jednání nebo existují jiné scénáře použití?**

Ne, právní blokování je jenom obecný pojem, který se používá pro zásady uchovávání nezaložené na čase. Nemusí se používat jenom pro řízení v souvislosti s řízením sporů. Zásady právního blokování jsou užitečné pro zakázání přepsání a odstranění pro ochranu důležitých podnikových dat virů, kde doba uchování není známá. Můžete ji použít jako podnikovou zásadu k ochraně vašich důležitých úloh WORM a jejich použití jako pracovní zásady před tím, než vlastní Trigger událostí vyžaduje použití zásady uchovávání informací podle času. 

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

Ano. Když se poprvé vytvoří zásady uchovávání informací založené na čase, je v *odemčeném* stavu. V tomto stavu můžete provést jakoukoli požadovanou změnu intervalu uchování, jako je například zvýšení nebo snížení, a dokonce zásadu odstranit. Jakmile je zásada uzamčená, zůstane zamčená, dokud neuplyne interval uchovávání. Tyto uzamčené zásady zabrání odstranění a úpravám do intervalu uchování. Důrazně doporučujeme použít stav odemčeno pouze pro účely zkušební verze a uzamknout zásadu během 24 hodin. Tyto postupy vám pomůžou v dodržování předpisů s výjimkou 17a – 4 (f) a dalšími předpisy.

**Můžu použít obnovitelné odstranění společně se zásadami neproměnlivého objektu BLOB?**

Ano. [Obnovitelné odstranění pro úložiště objektů BLOB v Azure](storage-blob-soft-delete.md) platí pro všechny kontejnery v rámci účtu úložiště bez ohledu na to, jakou dobu zablokují nebo zásady uchovávání informací podle času. Před použitím a potvrzením jakýchkoli neměnitelných zásad WORM doporučujeme povolit obnovitelné odstranění pro další ochranu. 

**Kde je funkce k dispozici?**

Neměnné úložiště je dostupné v oblastech veřejné, čínské a státní správy Azure. Pokud neproměnlivé úložiště není ve vaší oblasti k dispozici, obraťte se prosím azurestoragefeedback@microsoft.comna podporu a e-mail.

## <a name="sample-powershell-code"></a>Ukázkový kód PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Následující vzorový skript PowerShellu je určen pro referenci. Tento skript vytvoří nový účet úložiště a kontejner. Pak ukazuje, jak nastavit a vymazat právní blokování, vytvořit a uzamknout zásady uchovávání informací založené na čase (označované také jako zásady neměnnosti) a rozšíří interval uchovávání informací.

Nastavte a otestujte účet Azure Storage:

```powershell
$ResourceGroup = "<Enter your resource group>”
$StorageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$container2 = "<Enter another container name>”
$location = "<Enter the storage account location>"

# Log in to the Azure Resource Manager account
Login-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $ResourceGroup -Location $location

# Create your Azure storage account
New-AzStorageAccount -ResourceGroupName $ResourceGroup -StorageAccountName `
    $StorageAccount -SkuName Standard_LRS -Location $location -Kind StorageV2

# Create a new container
New-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Create Container 2 with a storage account object
$accountObject = Get-AzStorageAccount -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount
New-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Get a container
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container

# Get a container with an account object
$containerObject = Get-AzStorageContainer -StorageAccount $accountObject -Name $container

# List containers
Get-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount

# Remove a container (add -Force to dismiss the prompt)
Remove-AzStorageContainer -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container2

# Remove a container with an account object
Remove-AzStorageContainer -StorageAccount $accountObject -Name $container2

# Remove a container with a container object
$containerObject2 = Get-AzStorageContainer -StorageAccount $accountObject -Name $container2
Remove-AzStorageContainer -InputObject $containerObject2
```

Nastavit a vymazat právní blokování:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag1>,<tag2>,...

# with an account object
Add-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>

# with a container object
Add-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>,<tag5>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -Name $container -Tag <tag2>

# with an account object
Remove-AzRmStorageContainerLegalHold -StorageAccount $accountObject -Name $container -Tag <tag3>,<tag5>

# with a container object
Remove-AzRmStorageContainerLegalHold -Container $containerObject -Tag <tag4>
```

Vytvořit nebo aktualizovat zásady neměnnosti:
```powershell
# with an account name or container name
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container -ImmutabilityPeriod 10

# with an account object
Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -ImmutabilityPeriod 1 -Etag $policy.Etag

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 7

# with an immutability policy object
Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -ImmutabilityPeriod 5
```

Načíst zásady neměnnosti:
```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $ResourceGroup `
    -StorageAccountName $StorageAccount -ContainerName $container

# with an account object
Get-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container

# with a container object
Get-AzRmStorageContainerImmutabilityPolicy -Container $containerObject
```

Uzamknout zásady neměnnosti (přidání-Force pro zavření výzvy):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy -force

# with an account name or container name
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -Etag $policy.Etag

# with a container object
$policy = Lock-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -Etag $policy.Etag -force
```

Rozšířené zásady neměnnosti:
```powershell

# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container

$policy = Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy

# with an account name or container name
$policy = Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -ImmutabilityPeriod 11 -Etag $policy.Etag -ExtendPolicy

# with an account object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -StorageAccount `
    $accountObject -ContainerName $container -ImmutabilityPeriod 12 -Etag `
    $policy.Etag -ExtendPolicy

# with a container object
$policy = Set-AzRmStorageContainerImmutabilityPolicy -Container `
    $containerObject -ImmutabilityPeriod 13 -Etag $policy.Etag -ExtendPolicy
```

Odebrat odemčenou zásadu neměnnosti (Add-Force pro zavření výzvy):
```powershell
# with an immutability policy object
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container
Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy

# with an account name or container name
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $ResourceGroup -StorageAccountName $StorageAccount -ContainerName $container `
    -Etag $policy.Etag

# with an account object
Remove-AzRmStorageContainerImmutabilityPolicy -StorageAccount $accountObject `
    -ContainerName $container -Etag $policy.Etag

# with a container object
Remove-AzRmStorageContainerImmutabilityPolicy -Container $containerObject `
    -Etag $policy.Etag

```
