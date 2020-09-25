---
title: Režim učni – Přizpůsobte si
description: ''
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: f793535f04b36d231cec384b7acd66e38a7eb039
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91253526"
---
# <a name="use-apprentice-mode-to-train-personalizer-without-affecting-your-existing-application"></a>Použití režimu učně ke školení přizpůsobené aplikace bez ovlivnění vaší stávající aplikace

Vzhledem k povaze učení v **reálném čase** je možné model přizpůsobovat pouze v produkčním prostředí. Při nasazení nového případu použití není model přizpůsobování účinný, protože potřebuje čas potřebný k dostatečnému školení modelu.  **Režim učni** je vzdělávací chování, které tuto situaci usnadňuje a umožňuje v modelu získat důvěru, a to bez toho, aby vývojář měnil nějaký kód.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="what-is-apprentice-mode"></a>Co je to režim učni?

Podobně jako v případě, že se učni učí z hlavní větve, a díky tomu může docházet k lepšímu využití. Režim učni je _chování_ , které umožňuje přizpůsobovat informace, a to pozorováním výsledků získaných z existující logiky aplikace.

Přizpůsobené vlaky mimicking stejným výstupem jako aplikace. Podobně jako u většího toku událostí může Přizpůsob _zachytit_ existující aplikaci, aniž by to ovlivnilo existující logiku a výsledky. Metriky, které jsou dostupné z Azure Portal a rozhraní API, vám pomůžou pochopit výkon, jak se model učí.

Jakmile se přizpůsobené přizpůsobuje a dosáhne určité úrovně porozumění, může vývojář změnit chování z režimu učni na online režim. V tuto chvíli začne přizpůsobením vliv na akce v rozhraní API řazení.

## <a name="purpose-of-apprentice-mode"></a>Účel režimu učni

V režimu učni máte důvěru v rámci služby přizpůsobeného a funkce machine learningu a poskytujete kontrolu nad tím, že služba odesílá informace, které je možné zjistit z – bez rizika ohrožení online provozu.

K použití režimu učni mají dva hlavní důvody:

* Zmírnění **studeného**startu: režim učni pomáhá spravovat a vyhodnocovat náklady na "nový" školicí čas modelu – Pokud nevrací nejlepší akci a nedosáhli uspokojivé úrovně efektivity přibližně 60-80%.
* **Ověřování funkcí akcí a kontextu**: funkce odesílané v akcích a kontextu mohou být nedostatečné nebo nepřesné, příliš málo, jsou příliš velké, nesprávné nebo příliš specifické pro přizpůsobení výuky, aby dosáhli ideální míry účinnosti. Pomocí [hodnocení funkcí](concept-feature-evaluation.md) můžete vyhledat a opravit problémy s funkcemi.

## <a name="when-should-you-use-apprentice-mode"></a>Kdy byste měli použít režim učni?

Využijte režim učni ke školení přizpůsobeného nástroje, aby se zlepšila efektivita prostřednictvím následujících scénářů, a přitom předchází prostředí vašich uživatelů, na které neovlivnilo individuální nastavení:

* Implementujete přizpůsobování v novém případu použití.
* Výrazně jste změnili funkce, které odesíláte v kontextu nebo akcích.
* Významně jste změnili, kdy a jak počítáte ceny.

Režim učni není účinným způsobem měření přizpůsobeného vlivu na měnu. Chcete-li změřit, jak efektivní přizpůsobené je výběr nejlepší možné akce pro každé volání pořadí, použijte [offline vyhodnocení](concepts-offline-evaluation.md).

## <a name="who-should-use-apprentice-mode"></a>Kdo by měl používat režim učni?

Režim učni je užitečný pro vývojáře, odborníky na data a pracovníky pro rozhodování:

* **Vývojáři** můžou použít režim učni, aby se zajistilo, že rozhraní API pořadí a odměňování se v aplikaci používají správně a že funkce odesílané do přizpůsobené aplikace z aplikace neobsahují žádné chyby nebo nerelevantní funkce, jako je například časové razítko nebo prvek userid.

* Odborníci přes **data** mohou pomocí režimu učni ověřit, zda jsou funkce efektivní pro výuku modelů pro přizpůsobení, že doba čekání na odměnu není příliš dlouhá nebo krátká.

* **Tvůrci obchodních rozhodnutí** můžou pomocí režimu učni vyhodnotit potenciál přizpůsobeného přizpůsobování, aby se zlepšily výsledky (tj. ceny) ve srovnání se stávající obchodní logikou. To jim umožní učinit informace, které mají vliv na uživatelské prostředí, kdy skutečné tržby a spokojenosti uživatelů jsou v místě.

## <a name="comparing-behaviors---apprentice-mode-and-online-mode"></a>Porovnání chování a režimu učni v režimu učně

Naučíte se, když se v režimu učni liší od režimu online následujícími způsoby.

|Oblast|Začátečnický režim|Online režim|
|--|--|--|
|Dopad na činnost koncového uživatele|Stávající chování uživatelů můžete použít ke školení přizpůsobeného uživatele tím, že mu umožníte sledovat (neovlivňuje), co vaše **výchozí akce** bude, a to, jakou má získaná měna. To znamená, že budou mít vliv na uživatelské prostředí a jejich obchodní výsledky.|Zobrazit horní akci vrácenou z volání pořadí, která ovlivní chování uživatele|
|Rychlost učení|Přizpůsobování se v režimu učně pomaleji, než když se naučíte v online režimu. V režimu učni se můžete seznámit jenom s tím, že se vyhodnotí ceny získané **výchozí akcí**, což omezuje rychlost učení, protože není možné provádět průzkum.|Rychleji se naučí, protože může využít aktuální model a prozkoumat nové trendy.|
|Strop pro efektivitu studia|Přizpůsobování může být přibližná, velmi zřídka a nikdy nepřesahují výkon základní obchodní logiky (celková celková hodnota dosažená **výchozí akcí** každého volání pořadí).|Přizpůsobený modul by měl přesáhnout směrný plán aplikací a v čase, kdy se zablokuje, byste měli provádět testování v režimu offline a vyhodnocení funkcí, aby bylo možné dál získat vylepšení modelu. |
|Hodnota rozhraní API pro řazení pro rewardActionId|Činnost uživatelů se netýká, protože _rewardActionId_ je vždy první akcí, kterou jste odeslali v žádosti o zařazení. Jinými slovy, rozhraní API řazení není pro vaši aplikaci v režimu učni nic viditelné. Rozhraní API pro odměnu v aplikaci by se nemělo měnit způsob, jakým používá API pro odměnu mezi jedním a druhým režimem.|Uživatelské prostředí se změní podle _rewardActionId_ , který přizpůsobuje pro vaši aplikaci. |
|Vyhodnocení|Přizpůsobuje srovnání celkových odměna, které vaše výchozí obchodní logika načítá, a přizpůsobení součtu odměna by se v online režimu v tomto okamžiku dostal. V Azure Portal pro daný prostředek je k dispozici porovnání.|Vyhodnotit efektivitu přizpůsobeného přizpůsobeného [offline hodnocením](concepts-offline-evaluation.md), které vám umožní porovnat celkový přizpůsobený program na základě potenciálních odchylek v rámci směrného plánu aplikace.|

Poznámka o účinnosti režimu učni:

* Efektivita přizpůsobeného přizpůsobování v režimu učni bude v blízkosti 100% směrného plánu aplikace výjimečně dosaženo. a nikdy ho nepřekročí.
* Osvědčené postupy by se nemusely snažit získat 100% dosažení; Rozsah 60% až 80% by ale měl cílit v závislosti na případu použití.

## <a name="using-apprentice-mode-to-train-with-historical-data"></a>Použití režimu učni ke školení s historickými daty

Pokud máte významné množství historických dat, chcete je použít ke školení, můžete použít režim učni k přehrání dat prostřednictvím přizpůsobení.

Nastavte přidaný objekt v režimu učni a vytvořte skript, který bude začínat pomocí akcí a funkcí kontextu z historických dat. Vyvolejte rozhraní API pro odměnu na základě výpočtů záznamů v těchto datech. K zobrazení některých výsledků budete potřebovat přibližně 50 000 historických událostí, ale pro větší jistotu ve výsledcích se doporučuje 500 000.

Při výuce z historických dat doporučujeme, aby se data odesílaná v (funkce pro kontext a akce, jejich rozvržení ve formátu JSON používaného pro požadavky na řazení a výpočet odměny v této sadě školicích dat) shodovala s daty (funkcemi a výpočtem z odměna) dostupnými z existující aplikace.

Data v režimu offline a po faktech mají za následek více neúplných a Noisier a ve formátu se liší. Během školení z historických dat může být výsledek neprůkazný a nejedná se o dobrý odhad toho, jak se dobře přizpůsobuje, zejména v případě, že se funkce liší od minulých dat a ze stávající aplikace.

V případě přizpůsobování, ve srovnání s školením s historickými daty, se obvykle jedná o účinnější cestu k tomu, abyste měli účinný model s menšími náklady na práci, datovou technikovou a čisticí práci.

## <a name="using-apprentice-mode-versus-ab-tests"></a>Použití režimu učni oproti testům a/B

Je vhodné provést testy přizpůsobení a/B jenom po ověření a učení v online režimu. V režimu učni se používá pouze **výchozí akce** , což znamená, že všichni uživatelé budou efektivně vidět prostředí pro kontrolu.

I v případě, že je _přidaný přidaný,_ je při ověřování dat pro přizpůsobení školení k dispozici stejná výzva. Místo toho se dá použít režim učni, s 100% provozu a všemi uživateli, kteří získají řízení (neovlivněné) prostředí.

Jakmile budete mít případ použití pomocí přizpůsobeného a online učení, experimenty a/B vám umožní provádět řízené kohorty a vědecké porovnání výsledků, které mohou být složitější než signály využité k dispozici. Příkladem otázky, kterou může test A/B odpovídat: `In a retail website, Personalizer optimizes a layout and gets more users to _check out_ earlier, but does this reduce total revenue per transaction?`

## <a name="next-steps"></a>Další kroky

* Informace o [aktivních a neaktivních událostech](concept-active-inactive-events.md)
