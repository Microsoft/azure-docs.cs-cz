---
title: Shromažďování dat
description: Přečtěte si, jaká ukázková data se mají shromažďovat při vývoji vaší aplikace.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: c901d4024292ad03a9195ad0cbd226f473917ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98738080"
---
# <a name="data-collection-for-your-app"></a>Shromažďování dat pro vaši aplikaci

Aplikace Language Understanding (LUIS) potřebuje data jako součást vývoje aplikací.

## <a name="data-used-in-luis"></a>Data používaná v LUIS

LUIS používá text jako data ke školení a testování vaší LUIS aplikace pro účely klasifikace [záměrů](luis-concept-intent.md) a pro extrakci [entit](luis-concept-entity-types.md). Potřebujete dostatečně velkou datovou sadu, pro kterou máte dostatečná data k vytvoření samostatných datových sad pro školení a testování, které mají různou a distribuční hodnotu vyvolanou přímo níže.  Data v každé z těchto sad by se neměla překrývat.

## <a name="training-data-selection-for-example-utterances"></a>Výběr dat školení pro například projevy

Vyberte projevy pro sadu školení na základě následujících kritérií:

* **Skutečná data jsou nejlepší**:
    * **Skutečná data z klientské aplikace**: vyberte projevy, která jsou skutečná data z klientské aplikace.  Pokud zákazník odešle webový formulář s dotazem ještě dnes a vytváříte robota, můžete začít používat data webového formuláře.
    * **Data se zaplněnými zdroji**: Pokud nemáte žádná existující data, zvažte přeplnění projevy zdrojů.  Vyzkoušejte si přeplnění zdroje projevy z vlastního uživatelského naplnění vašeho scénáře, abyste získali nejlepší odhad skutečných dat, která vaše aplikace uvidí. Naplnění lidských projevy je lepší než projevy generované počítačem.  Když sestavíte datovou sadu syntetických projevy vygenerovaných v určitých vzorcích, nebudete mít velkou variantu, která se vám bude zobrazovat s lidmi, které vytváří projevy, a neukončí generalizaci v produkčním prostředí.
* **Rozmanitost dat**:
    * **Různorodost oblastí**: Ujistěte se, že data pro každý záměr jsou co nejvíce odlišná, včetně _formulace_ (volba Wordu) a _gramatiky_.  Pokud prezentujete záměr o personální politice o dnech dovolené, ujistěte se, že máte projevy, který představuje výrazy používané pro všechny oblasti, které spravujete.  V Evropě se například může zeptat, co `taking a holiday` se v nich můžou zeptat `taking vacation days` .
    * **Jazykové rozmanitost**: Pokud máte uživatele s různými nativními jazyky, které komunikují v druhém jazyce, ujistěte se, že máte projevy, který představuje jiné než nativní reproduktory.
    * **Vstupní různorodost**: zvažte zadání vaší cesty pro zadávání dat. Pokud shromažďujete data od jedné osoby, oddělení nebo vstupního zařízení (mikrofon), pravděpodobně nebudete mít k dispozici různorodost, která bude pro vaši aplikaci důležitá vědět o všech vstupních cestách.
    * **Typografie interpunkce**: Zvažte, že lidé používají různé úrovně interpunkce v textových aplikacích a že mají různou část, jak se používá interpunkce. Pokud používáte data, která pocházejí z rozpoznávání řeči, nebude mít žádná interpunkční znaménka, takže by vaše data neměla být.
* **Distribuce dat**: Zajistěte, aby data rozložená mezi záměry představovaly stejnou část dat, jakou klientská aplikace přijímá. Pokud vaše aplikace LUIS klasifikuje projevy, které jsou požadavky na naplánování ponechání (50%), ale uvidí také projevy o dotazování na dny ukončení, zbývající (20%) a schválení opouští (20%) a nejedná se o chat z oboru a funkce CHITEST (10%) Vaše datová sada musí mít ukázková procenta každého typu utterance.
* **Použít všechny datové formuláře**: Pokud aplikace Luis převezme data ve více formulářích, nezapomeňte tyto formuláře zahrnout do školicích projevy. Například pokud vaše klientská aplikace přebírá zadání řeči i psaní textového vstupu, budete potřebovat projevy vygenerovaný převod řeči na text a také typ projevy.  Ve způsobu, jakým uživatelé mluví při rozpoznávání řeči a překlepech, se zobrazí různé odchylky.  Veškerá tato variace by měla být reprezentovaná ve vašich školicích datech.
* **Pozitivní a negativní příklady**: pro výuku aplikace Luis se musí zjistit, co je záměr (pozitivní) a co není (negativní). V LUIS může být projevy pro jediný záměr pouze kladný. Když se do záměru přidá utterance, LUIS automaticky vytvoří stejný příklad utterance negativní příklad pro všechny ostatní záměry.
* **Data mimo rozsah aplikace**: Pokud vaše aplikace uvidí projevy, která patří mimo vaše definované záměry, nezapomeňte je poskytnout. Příklady, které nejsou přiřazeny konkrétnímu definovanému záměru, budou označeny záměrem **none** .  Je důležité mít reálné příklady pro záměr **none** pro správnou předpověď projevy, které jsou mimo rozsah definovaných záměrů.

    Například pokud vytváříte robota na HR, který se zaměřuje na dobu opuštění a máte tři záměry:
    * Naplánování nebo úprava ponechání
    * Dotázat se na dny volna k dispozici
    * schválit nebo neschválit opustit

    Chcete se ujistit, že máte projevyy, které se vztahují na oba tyto záměry, ale také na to, aby pokryly potenciální projevyy mimo tento obor, na které by měla aplikace sloužit:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Vzácné příklady**: vaše aplikace bude potřebovat výjimečné příklady a běžné příklady.  Pokud vaše aplikace nikdy neviděla vzácné příklady, nebude ji moct identifikovat v produkčním prostředí. Pokud používáte skutečná data, budete moct přesněji odhadnout, jak bude vaše aplikace LUIS pracovat v produkčním prostředí.

### <a name="quality-instead-of-quantity"></a>Kvalita namísto množství

Před přidáním dalších dat zvažte kvalitu stávajících dat.  S LUIS používáte učebnu počítače.  Vaše aplikace LUIS používá kombinaci štítků a funkcí strojového učení, které definujete.  Nespoléhá se jenom na množství popisků, aby se zajistila nejlepší předpověď.  Nejdůležitější část je nejdůležitější část příkladů a jejich reprezentace toho, co vaše aplikace LUIS uvidí v produkčním prostředí.

### <a name="preprocessing-data"></a>Předzpracování dat

Následující kroky předběžného zpracování vám pomůžou sestavit lepší LUIS aplikaci:

* **Odebrat duplicity**: duplicitní projevy nebude snížit, ale neposkytne jim žádné z nich, takže při jejich odebrání se ušetří čas označování.
* **Použít stejný předzpracování klientské aplikace**: Pokud vaše klientská aplikace, která volá koncový bod předpovědi Luis, aplikuje zpracování dat za běhu před odesláním textu do Luis, měli byste aplikaci Luis na data, která se zpracovávají stejným způsobem. Například pokud vaše klientská aplikace používá [Kontrola pravopisu Bingu](../bing-spell-check/overview.md) k opravě pravopisu vstupů do Luis, opravte projevy pro školení a testování před přidáním do Luis.
* Nepoužívejte **nové procesy čištění, které klientská aplikace nepoužívá**: Pokud klientská aplikace akceptuje text generovaný hlasem přímo bez jakéhokoli vyčištění, jako je třeba gramatika nebo interpunkční znaménka, musí projevy odpovídat stejnému, včetně chybějících interpunkčních znamének a dalších chybných rozpoznávání, pro které budete potřebovat účet.
* **Nevyčistit data**: Neodstraňujte poškozený vstup, který může být od nepoškozeného rozpoznávání řeči, nechtěného stisknutí kláves nebo chybně napsaného textu. Pokud vaše aplikace uvidí vstupy jako ty, je důležité, aby ji bylo možné vyškolet a testovat. Pokud byste neočekávali, že vaše aplikace bude rozumět, přidejte _poškozený vstupní_ záměr. Označte tato data, aby vaše aplikace LUIS mohla předpovídat správnou odezvu za běhu. Klientská aplikace může zvolit vhodnou reakci na nesrozumitelnou projevy, jako je například `Please try again` .

### <a name="labeling-data"></a>Popisování dat

* **Text popisku, jako kdyby byl správný**: vzorový projevy by měl mít všechny formuláře označené entity. To zahrnuje text, který je špatně napsaný, nesprávně typový a špatně přeložený.

### <a name="data-review-after-luis-app-is-in-production"></a>Kontrola dat po LUIS aplikace v produkčním prostředí

Pokud jste nasadili aplikaci do produkčního prostředí, [Projděte si službu Endpoint projevy](luis-concept-review-endpoint-utterances.md) a sledujte skutečný utterance provoz.  To vám umožní aktualizovat školicí projevy se skutečnými daty, což vylepšuje vaši aplikaci. Veškerá aplikace vytvořená s přeplněnými daty scénářů nebo nereálného scénáře se bude muset zlepšit na základě skutečného využití.

## <a name="test-data-selection-for-batch-testing"></a>Výběr testovacích dat pro dávkové testování

Všechny zásady uvedené výše pro školicí projevy se vztahují na projevy, které byste měli použít pro [sadu testů](./luis-how-to-batch-test.md). Ujistěte se, že rozdělení mezi záměry a entitami zrcadlí skutečnou distribuci co nejpřesněji.

Nepoužívejte znovu projevy ze sady školení v sadě testů. Tím dojde k nesprávnému posunutí vašich výsledků a neposkytneme vám správné informace o tom, jak bude vaše aplikace LUIS pracovat v produkčním prostředí.

Po publikování první verze vaší aplikace byste měli aktualizovat sadu testů pomocí projevy z reálného provozu, abyste zajistili, že vaše testovací sada odráží vaši produkční distribuci a můžete monitorovat reálný výkon v průběhu času.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přečtěte si, jak LUIS mění data před předpovědi.](luis-concept-data-alteration.md)