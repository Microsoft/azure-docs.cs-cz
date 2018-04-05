---
title: Definování strategie Mobile Engagementu | Dokumentace Microsoftu
description: Naučte se ovládat a optimalizovat Mobile Engagement pomocí analytických funkcí a nabízených oznámení.
services: mobile-engagement
documentationcenter: Mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7533e318-81b9-4360-aace-b7be8225985b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 494dd27acfd59eb79da13dae9bbbe0f80ee41370
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="define-your-mobile-engagement-strategy"></a>Definování strategie Mobile Engagementu
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

*Aplikaci jste naprogramovali proto, aby ji vaši uživatelé používali.*

Věříme, že jste se maximálně snažili, aby se z ní stala skvělá aplikace, kterou budou uživatelé milovat. Také jste pravděpodobně investovali značnou částku do marketingového rozpočtu, abyste uživatele získali. Ale po úvodním nadšení možná uživatelé vaši aplikaci pomalu přestávají používat. *A přesně tohle je doména Azure Mobile Engagementu*: zaujmout uživatele a umožnit vám aplikaci neustále zlepšovat testováním a sledováním odezvy.

Náš přístup k vylepšování míry uchování a používání je založen na tom, že uživatele aplikace zaujmeme nabízenými oznámeními a zprávami v aplikaci. Děláme to ale dost neobvyklým způsobem – zprávami a komunikací, která je jim šitá přímo na míru podle toho, jak se ve vaší aplikaci chovají. Naším cílem je umožnit vám komunikovat s tou správnou cílovou skupinou ve správném čase a na správném místě.

K tomu ale nejprve potřebujete *své uživatele pochopit*, pak vytvořit skupiny založené na jejich aktivitách nebo charakteristikách (ty nazýváme segmenty) a konečně navázat s každým segmentem náležitou komunikaci.

## <a name="mobile-engagement-serves-your-objectives"></a>Mobile Engagement slouží vašim cílům
*Už jsme mluvili o uchovávání a používání, ale proč?*

Při budování strategie Mobile Engagementu je třeba nejprve sledovat cíle vaší aplikace a klíčové ukazatele výkonu.

Začněte definováním cílů a klíčových ukazatelů výkonu, které vám pomohou definovat případy použití Engagementu z toho správného hlediska.

Případy použití jsou jednoduchým seznamem kampaní, přes které byste chtěli komunikovat s uživateli – od jednoduchého pozdravu až po velmi pokročilý nástroj pro odesílání oznámení spouštěný z vašeho IT systému. Dobře sestavený případ použití musí zahrnovat alespoň trojici komponent *co-kdo-kdy*:

1. Velmi krátké označení (například „Uvítací kampaň“).
2. **Co**: Ukázková zpráva (například „Jsme rádi, že jste s námi! Nezapomeňte se přihlásit a získáte první měsíc zdarma!“) Tato zpráva rozhodně není definitivní a můžete ji změnit, kolikrát chcete. Obvykle ale pomáhá začít přemýšlet o tom, co chcete říci.
3. **Kdo**: Segment, který tuto zprávu obdrží (například „Všichni uživatelé, kteří aplikaci poprvé spustili před 3 dny, navštívili přihlašovací stránku, ale nepřihlásili se“).
   * Ano, s Azure Mobile Engagementem to můžete provést velmi snadno :)
   * Znovu opakujeme, že to nemusí být definitivní, protože segmenty můžete definovat kdykoliv. Je ale důležité hned na začátku definovat segmentační kriteria, abyste shromažďovali správná data.
4. **Kdy**: Načasování vaší kampaně. Může být nastaveno na určité datum, nebo po určité akci v závislosti na triggeru. Mobile Engagement nabízí spoustu možností, jak komunikaci správně načasovat.

Definování případů použití a segmentů dává určitý návod, jak definovat data, která je třeba v aplikaci sbírat. To je role *„plánu značek“*. Plán značek vám umožňuje zajistit, aby byl sběr dat specifikován pro vývojáře. Protože vývojáři dokážou do Mobile Engagementu vložit správné nastavení, díky kterému budete moci ve svých kampaních pracovat se správnými daty. Také bude velmi důležité spustit testy, abyste měli jistotu, že integrace je správná a že shromažďuje data, která potřebujete.

V závislosti na integraci budete jako obchodník moci sledovat analytické údaje v reálném čase, segmentovat cílovou skupinu a pak začít cíleně posílat inteligentní nabízená oznámení, abyste upoutali cílové uživatele v aplikaci nebo mimo ni.

### <a name="use-cases-to-get-started"></a>Případy použití pro začátek
1. Uvítací strategie: Vytvořte několik kampaní nabízených oznámení založených na chování koncových uživatelů při spuštění aplikace, abyste je znovu zaujali i 2, 5, 10 nebo 15 dnů po první relaci a zvýšili míru uchování po prvním spuštění.
2. Propagujte nový obsah (funkce, článek/video nebo produkt) podle chování koncových uživatelů tak, aby se odesílané informace dostaly pouze ke koncovým uživatelům, které nejspíše zaujmou.
3. Hodnocení aplikace: Zaměřte se na to méně než 1 % z vaší uživatelské základny, které s největší pravděpodobností v obchodě ohodnotí aplikaci 5 hvězdičkami.
4. Zvýšení odběrů: Propagujte cenný obsah u uživatelů, kteří jej ještě neviděli, a získávejte tak další odběratele.
5. Kurzy: Už žádné povinné kurzy pro každého. Proč si nesestavit skvělé kurzy přímo v aplikaci a pak je nespouštět jako zprávy v aplikaci jen tehdy, když se bude zdát, že uživatel má problém s použitím nějaké funkce?

## <a name="why-do-you-need-analytics-to-engage"></a>Na co potřebujete analýzu pro zaujmutí uživatelů?
V tomto bodě už je vám nejspíš jasné, že pouhé vysílání nabízených oznámení nestačí. Základními koncepty Mobile Engagementu je pomáhat obchodníkům a vývojářům nacházet ve správný čas a na správném místě správné koncové uživatele. Když znáte tyto tři hlavní koncepty, je základem získávání analytických dat z aplikace a jejich následné použití k rozdělení uživatelů do segmentů. To je ještě účinnější, pokud jsou segmenty chování doplněny daty z jiné vaší databáze, CRM nebo kanálu. Mobile Engagement umožňuje načíst data odkudkoli a využít je k zaměření na správnou cílovou skupinu.

Aby bylo možné při snaze o zapojení cílové skupiny co nejlépe pracovat s kontextem, je důležité znát chování koncových uživatelů a mít informace o jejich stavu v reálném čase. Sběr dat obchodníkům umožňuje, aby se mohli soustředit na to, na čem uživatelům v různých případech skutečně záleží, a dosahovat svých strategických cílů zapojení mobilních zařízení. Snaha o dosažení dříve stanovených cílů je také důvodem, proč nejlepší postup nespočívá ve shromažďování všech informací za všech okolností, ale pouze informací, které vám umožní zaměřit se na to, co chcete zjistit, a na vaše případy použití. To je dobrý způsob, jak zahájit, vyzkoušet, otestovat a naučit se používat řešení, adresovat inteligentní nabízená oznámení a zvyšovat míru uchování aplikace tak, aby se dostala na úroveň těch nejúspěšnějších.

> [!NOTE]
> Nezapomeňte: Příliš velký objem znehodnocuje kvalitu dat!
> 
> 

### <a name="use-cases-and-best-practices"></a>Případy použití a osvědčené postupy
Aby se vám lépe začínalo, stručně si v této části probereme některé klíčové případy použití, na které jsme narazili u našich zákazníků.

#### <a name="media"></a>Média
Shromažďujte údaje o typu obsahu, který cílový uživatel nejvíce využívá, podle tohoto chování segmentujte cílovou skupinu a potom směřujte určité typy obsahu pouze na ty, kteří je budou s největší pravděpodobností používat. Tím eliminujete odesílání spamu celé uživatelské základně a zajistíte lepší uchování.

#### <a name="m-commerce"></a>M-commerce
Shromažďujte údaje o produktových  kategoriích, které jsou v rámci aplikace nejnavštěvovanější, a na cílovou skupinu pak zaměřte propagaci slevy nebo nového produktu v kategorii, ve které tento koncový uživatel nejspíš nakoupí. Zaměřte se na zvyšování příjmů. Opakujeme, že cílem není spamovat!

#### <a name="gaming"></a>Hraní her
Shromažďujte data o úrovni koncového uživatele ve hře a o době, kterou v určitém intervalu strávil hraním. Můžete se pak zaměřit na cílovou skupinu, která má potíže s postupem na další úroveň a která na novou úroveň snáze postoupí s bonusovou nabídkou.

Komunikujte o určitých událostech a přitom se soustřeďte na uživatele, kteří už nějakou dobu nehráli, a snažte se je zvláštní nabídkou spojenou s událostí přesvědčit k návratu.

#### <a name="retail"></a>Maloobchod
Shromažďujte produkty nebo značky, které bude cílová skupina nejspíše používat. Vycházejte z toho, co má cílová skupina ráda nebo jak se chová, a snažte se ji přilákat do vašeho obchodu, abyste si zvýšili tržby.

#### <a name="banking"></a>Bankovnictví
Shromažďujte data od koncových uživatelů, kteří si vytvořili účet při prvním spuštění aplikace. Soustřeďte se na nasazení uvítací strategie s cílenými nabízenými oznámeními a zvyšujte počet odběratelských účtů.

### <a name="how-to-create-a-great-tag-plan"></a>Jak vytvořit co nejlepší plán značky?
Plán značky musí být koncipován jako popis uživatelské cesty nebo jako určitý pracovní postup aplikace. Určuje všechny potřebné značky (data), které je třeba shromažďovat k analýze chování uživatelů a správné segmentace uživatelské základny. Nejedná se o technický proces. Proto obchodníci dokáží sami na základě strategie Mobile Engagementu specifikovat data, která chtějí shromažďovat.

Minimálně je třeba označit alespoň všechny obrazovky aplikace (v Mobile Engagementu se jim říká *aktivity*). To pomáhá určit způsob, jakým se uživatel v aplikaci pohybuje.

Aktivita může obsahovat *události*, které shromažďují informace o akci, jako je například kliknutí na tlačítko. To umožňuje v rámci aplikace shromažďovat informace o interakcích. Takhle obchodníci dokáží zjistit, kterou obrazovku uživatelé navštěvují a co na ní dělají.

`Jobs`jsou akce s určitou dobou trvání. Díky nim obchodníci zjišťují, jak dlouho uživateli například trvá, než si vytvoří účet nebo než se přihlásí. Vývojáři zde zase mohou sledovat, jak dlouho trvá volání webové služby.

`Errors`je možné také monitorovat a mít tak přehled, zda uživatelé nemají s vaší aplikací problémy. Například může docházet k častým problémům s připojením.

Veškerá data tohoto typu se dají rozšířit o parametry (v Mobile Engagementu *další informace*), které vám umožní shromažďovat dynamická data z aplikace. To je důležité pro jemné rozdělení uživatelů do segmentů. Obchodníci mohou například segmentovat uživatele podle typu obsahu, který využívali. Typ obsahu budou dynamické informace o aktivitě nebo události.

*Informace o aplikaci* jsou data, která vám umožňují v reálném čase potvrdit stav aplikace nebo uživatele. Také vám pomáhají kategorizovat cílovou skupinu a rychle na ni cílit. Může se například jednat o informace o tom, zda se uživatel přihlašuje, či nikoliv, nebo o datu vypršení platnosti jeho předplatného.

#### <a name="example-of-tags"></a>Příklad značek
*Případ použití: Segmentování chování cílové skupiny, abyste na správného koncového uživatele mohli směřovat správná nabízená oznámení.*

1. Odeslání nabízeného oznámení propagujícího kategorii produktu: Shromažďujte data o chování k segmentování cílové skupiny podle kategorie produktu, kterou tito uživatelé v určitém období vícekrát navštívili, nebo určitého zboží, které vložili do košíku. Shromážděná data vám umožní segmentovat uživatele a následně rozeslat nabízená oznámení správné cílové skupině.
2. Hodnocení aplikace: Shromažďujte data založená na obsahu, který cílová skupina sdílí na sociální síti. Cílem je určit *ambasadory* vaší aplikace a podle nich segmentovat cílovou skupinu. Přes nabízená oznámení v aplikaci jsou ambasadoři tou nejlepší cílovou skupinou, kterou můžete požádat, aby vaši aplikaci v obchodě hodnotili 5 hvězdičkami.
   
   ![][1]

*Případ použití: Deklarativní data*

1. Segmentování výstražných zpráv: Shromažďujte deklarativní data, která segmentují cílovou skupinu podle jejích preferencí. To umožňuje odesílat nabízená oznámení pro určité téma, které určitou cílovou skupinu skutečně zajímá.
2. Segmentujte cílovou skupinu na základě stavu přihlášení. Shromažďujte data a zjišťujte, zda je uživatel připojen nebo jestli si vytvořil účet. Pomáhá se zaměřit na koncové uživatele, kteří se ještě nepřihlásili, a pomocí nabízených oznámení je povzbuzuje, aby se připojili.
   ![][2]

### <a name="next-steps"></a>Další kroky
* Další informace o základních konceptech Mobile Engagementu naleznete v článku [Koncepty Mobile Engagementu].
* Další informace o vytvoření nové kolekce aplikací Mobile Engagementu v Azure a řízení aplikace přes portál Mobile Engagementu najdete v článku [Vytvoření aplikace Mobile Engagementu](mobile-engagement-create.md).
* Podrobnější informace najdete v článku [Osvědčené postupy](mobile-engagement-getting-started-best-practices.md).
* Další informace o implementaci Mobile Engagementu a ukázkovou herní aplikaci najdete v článku [Scénář herní aplikace](mobile-engagement-gaming-scenario.md). 
* Další informace o implementaci Mobile Engagementu a ukázkovou mediální aplikaci najdete v článku [Scénář mediální aplikace](mobile-engagement-media-scenario.md). 
* Další informace o implementaci najdete v článku [Kurzy].

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Koncepty Mobile Engagementu]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Kurzy]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

