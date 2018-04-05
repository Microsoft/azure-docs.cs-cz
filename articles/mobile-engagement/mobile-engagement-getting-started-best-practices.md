---
title: Příručka Začínáme k Azure Mobile Engagement s osvědčenými postupy
description: Příručka Začínáme k Azure Mobile Engagement s osvědčenými postupy k zahájení práce
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: dfce1183-6398-466e-aa7e-ed702fb52818
ms.service: mobile-engagement
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: cf9dc5febeae27473d2adb8a024536b945bb9d13
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Azure Mobile Engagement – příručka Začínáme s osvědčenými postupy
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

## <a name="overview"></a>Přehled
**Mobilní obrazovka je velmi frekventované místo:** Studie z roku 2013 zjistila, že na průměrném mobilním zařízení je nainstalováno 27 aplikací. Uživatelé obvykle stráví používáním svých aplikací asi 30 hodin měsíčně. Většina této doby připadá na používání sociálních sítí a hraní (asi 20 hodin). V roce 2014 si mohli uživatelé na trhu aplikací pro Android vybírat z více než 1,5 milionu produktů. V Apple App Storu pak bylo k dispozici asi 1,2 milionu aplikací. Popularita mobilních aplikací stále roste a vývojáři neustále uvádějí na trh nové mobilní aplikace. 

Průměrný mobilní uživatel velmi často instaluje nové aplikace a odinstalovává aplikace, které už nechce, v závislosti na proměnách svých zájmů a spokojenosti s aplikacemi. K určení úspěšnosti aplikace již nestačí vědět pouze to, kolik uživatelů si aplikaci nainstalovalo. Je důležité vědět, do jaké míry je aplikace užitečná, a znát trendy jejího používání. Je potřeba znát odpovědi na tyto otázky:

* Přestává být aplikace pro uživatele zajímavá nebo jim připadá zastaralá? 
* Kolik uživatelů úplně přestalo aplikaci používat? 
* Mají nákupy z aplikace rostoucí nebo klesající tendenci?
* Nedokončují uživatelé pracovní postupy v aplikaci z důvodu jejích chyb nebo proto, že v jejich průběhu ztrácejí zájem? 
* Je možné odesíláním aktuálního obsahu uživatelům aplikace zachovávat užitečnost a vhodnost aplikace ke konkrétnímu účelu? 
* Má být tento obsah stejný pro všechny uživatele nebo se má pro různé segmenty uživatelů lišit v závislosti na tom, jak aplikaci používají? 

Odpovědi na podobné otázky pomáhají prodloužit životnost aplikace a zvýšit výnosy, které z ní plynou. Mohou vám také pomoci zjistit, kdo aplikaci používá, a její uživatele si udržet. 

Jedním z typů aplikací, které uživatelé nejméně odinstalovávají, jsou aplikace související s médii. Jedním z důvodů je, že uživatelům neustále nabízejí čerstvý obsah. Uchování aplikací také výrazně zvyšuje časná implementace užitečných nabízených oznámení odesílaných cílovému segmentu uživatelů. 

Program Azure Mobile Engagement nabízí metody shromažďování a analyzování informací o používání vaší aplikace, a pomáhá vám tak prodlužovat její životnost a zvyšovat míru jejího uchování uživateli. Pomůže vám klasifikovat uživatele aplikace podle jejich chování a vytvářet cílené kampaně doručování nabízených oznámení a zpráv v aplikacích různým segmentům uživatelů. Klíčové ukazatele výkonu měří aktivitu uživatelů vaší aplikace s ohledem na její různé aspekty. Azure Mobile Engagement poskytuje metody, které potřebujete k určení těchto klíčových ukazatelů výkonu. Nabízí vám infrastrukturu potřebnou k většímu zapojení uživatelů vaší aplikace a zvyšuje tak návratnost vašich investic. 

Aby pro vás byl program Azure Mobile Engagement co nejužitečnější, je nutné začít s dobře navrženým plánem zapojení uživatelů. Váš plán pomůže identifikovat podrobná data, která budete potřebovat, abyste mohli rozdělit uživatele své aplikace do segmentů. Lze toho docílit na základě chování uživatelů a způsobu, jakým aplikaci používají. Osvědčeným postupem k vytvoření úspěšného plánu je přesně definovat klíčové ukazatele výkonu, které budou měřit cíle vaší aplikace. Pokud budete mít přesně definované klíčové ukazatele výkonu, budete do aplikace moci snadno vložit potřebné funkce pro shromažďování podrobných dat, která využijete k jejich analýze a hodnocení. Toto téma je příručkou s osvědčenými postupy definování klíčových ukazatelů výkonu, které budete používat ve svých plánech zapojení. 

## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Krok 1: Definování klíčových ukazatelů výkonu podle modelu OZT
Správné definování klíčových ukazatelů výkonu nemusí být snadné. Aplikace navržené pro různá odvětví mají svá vlastní specifika a účely. To vás může při vývoji vaší koncepce mást. Abyste se tomu vyhnuli, lze cíle a klíčové ukazatele výkonu rozdělit do tří kategorií: **Obchodní**, **Zapojení** a **Technické**. Tento model nazýváme **modelem OZT**.

Kvalitní plán má obecně vzato cíle s klíčovými ukazateli výkonu, které měří úspěch v každé z následujících kategorií modelu OZT.

#### <a name="business-kpis"></a>Obchodní klíčové ukazatele výkonu
Definice obchodních klíčových ukazatelů výkonu by měla být nejjednodušší. Tyto ukazatele jste již pravděpodobně v nějaké podobě definovali při plánování své mobilní aplikace. Obecně vzato pomáhají měřit výnosy z aplikace a návratnost investic do aplikace. Následující seznam uvádí některé ukázkové klíčové ukazatele výkonu, které vám mohou pomoci definovat vaše vlastní ukazatele výkonu:

* Obchodní klíčové ukazatele výkonu pro mediální obsah
  * počet kliknutí na reklamy
  * počet návštěv stránky na uživatele
  * počet souběžných odběrů
* Obchodní klíčové ukazatele výkonu pro herní obsah
  * počet nákupů z aplikace
  * průměrný výnos na uživatele
  * strávená doba na relaci
  * počet dnů hraní a aktuální úroveň ve hře
* Klíčové ukazatele výkonu pro elektronické obchodování
  * počet dnů používání aplikace
  * průměrný výnos na uživatele
  * průměrná cena zboží v košíku na pokladně
  * kategorie nejčastěji zobrazovaných a zakoupených produktů
* Obchodní ukazatele výkonu pro obsah z oblasti bankovnictví a pojišťovnictví
  * počet účtů
  * aktivované funkce
  * počet navštívených stránek s nabídkami
  * počet upozornění, která uživatel aktivuje nebo na ně klikne       

#### <a name="engagement-kpis"></a>Klíčové ukazatele zapojení
Klíčový ukazatel zapojení je ukazatel výkonu, který umožňuje měřit zapojení uživatelů. Trendy v této oblasti pomáhají určit míru uchování aplikace. Zde je několik ukázkových klíčových ukazatelů výkonu tohoto typu:

* počet aktivních uživatelů během posledních 7 dnů
* počet neaktivních uživatelů během posledních 7 dnů
* počet uživatelů, kteří aplikaci nepoužili během posledních 30 dnů  

Ukazatele v této oblasti mohou ovlivnit některé zřejmé externí faktory. Například můžete předpokládat, že má uživatel mobilní zařízení stále u sebe. To může, ale také nemusí být pravda. Herní aplikace mívají vyšší míru využití ve volných dnech, kdy hráči nejsou ve škole nebo v zaměstnání a mají na hraní více času.   

Dobře definované klíčové ukazatele výkonu v této kategorii pomáhají měřit vztah zákazníků k aplikaci.

#### <a name="technical-kpis"></a>Technické klíčové ukazatele výkonu
Ukazatele výkonu v této kategorii pomáhají určit, zda se aplikace správně chová, zda nepřestává reagovat či zda v ní nedochází k chybám. Tyto ukazatele mohou měřit kvalitu aplikace a určovat problémy, které mohou uživatelům bránit v jejím používání. Informace shromážděné pro tuto kategorii mohou obsahovat také informace o výkonu, které jsou relevantní pro marketingové týmy. Získaná také mohou být užitečná pro IT týmy a týmy podpory a pomáhat jim při řešení potíží a identifikaci neohlášených chyb. 

Zde jsou některé příklady technických klíčových ukazatelů výkonu:

* informace o neošetřených a ošetřených výjimkách a jejich počtu 
* časové razítko poslední chyby
* tlačítko, na které uživatel naposledy kliknul, nebo stránka, kterou naposledy navštívil
* využití paměti aplikací
* obnovovací frekvence aplikace
* verze operačního systému, ve kterém je aplikace spuštěná
* verze aplikace

Definování těchto klíčových ukazatelů výkonu vám pomůže měřit výkon aplikace a identifikovat potenciální chyby. Tyto indikátory by měly pomáhat zkracovat dobu potřebnou k doručení oprav zákazníkům. Mohou vám také pomoci určit segment uživatelů, kteří se setkali s konkrétními potížemi. Na základě segmentace uživatelů můžete vytvářet kampaně doručování oznámení týkajících se dostupných oprav a potenciální propagační oznámení, které vám pomohu obnovit spokojenost zákazníků. 

#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Cvičení se scénářem 1: Vytvoření řídicího panelu klíčových ukazatelů výkonu
Při definování marketingové strategie by klíčové ukazatele výkonu měli reprezentovat pohled na každý z vašich hlavních cílů. Mělo by se jednat o jasně definované datové body, které vám umožní shromažďovat důležité informace potřebné k monitorování aplikace a chování koncových uživatelů.

Sestavení řídicího panelu klíčových ukazatelů výkonu, které obsahují níže uvedené informace

1. K čemu slouží klíčové ukazatele výkonu aplikace?
2. Jaké datové body použiji k reprezentaci jednotlivých klíčových ukazatelů výkonu?
3. Kde tato data ve své aplikaci najdu (na obrazovce, v nastavení, v systému…)?
4. Mohu pro tento klíčový ukazatel výkonu spustit sekvenci zapojení?

Příklady a nápovědu najdete na listu **KPI Builder** (Tvůrce klíčových ukazatelů výkonu) v sešitu [Media Playbook Template][Media Playbook link] (Šablona scénáře pro média).

## <a name="step-2-your-engagement-program"></a>Krok 2: Váš program zapojení
Kvalitní program zapojení mobilních zařízení je klíčovou komponentou vaší aplikace. V každém případě by měl obsahovat špičkový uvítací program, který se věnuje uživateli během prvních dnů používání vaší aplikace. Má velmi pozitivní vliv na zapojení uživatele a uchování aplikace. Studie prokázaly, že většina uživatelů přestává aplikaci používat během prvních několika dnů po instalaci. Proto byste se měli v tomto období snažit splnit či ještě lépe překonat očekávání uživatele a co nejvíce podporovat jeho zájem o aplikaci. Vždy zákazníkům prezentujte klíčové přínosy a výhody aplikace. 

![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Nejlepší metodou k dosažení zapojení uživatelů mobilních zařízení během prvních dnů používání aplikace jsou nabízená oznámení. Při používání nabízených oznámení je však nutné věnovat důkladnou pozornost rozdělení uživatelů do segmentů. Jakmile uživatel získá dojem, že je mu odesílán spam nebo nezajímavá oznámení, může to mít velmi neblahý vliv. Uživatel může několika kliknutími aplikaci odstranit a nikdy se k ní už nevrátí. Uživatel by měl v aplikaci dostávat vysoce individuální hodnotný obsah, a ne obecný spam.

Když jsou uživatelé aktivně zapojení, můžete program zapojení použít k rozvoji dalších aspektů aplikace.

Můžete například nastavit kampaň, která aktivní uživatele žádá o hodnocení aplikace. Protože uživatelé aplikaci nejaktivněji používají a mají s ní největší zkušenosti, je pravděpodobné, že vám poskytnou  nejpřesnější hodnocení. Jakmile získáte vysoké hodnocení aplikace, může to pomoci přirozenému růstu počtu stažení aplikace a snížit náklady na získávání nových zákazníků.

#### <a name="engagement-sequence"></a>Sekvence zapojení
Globální program zapojení zahrnuje různé sekvence zapojení. Cílem každé sekvence je dosažení několika cílů.

###### <a name="life-push-sequence"></a>Sekvence nabízených oznámení životního cyklu
Cíle sekvence nabízených oznámení životního cyklu se liší v závislosti na zapojení uživatele aplikace. Uživatel může být nový, neaktivní nebo velmi aktivní. V různých fázích životního cyklu mohou mít uživatelé užitek z čerstvého obsahu, který jim poskytujete ve formě tipů nebo odkazů na dokumentaci. 

Nový uživatel například může ocenit pomoc se zorientováním se v aplikaci nebo může mít užitek z nové pobídkové odměny podobné pobídkovým odměnám, které mu byly nabízeny při prvním spuštění aplikace...

*„Vážíme si toho, že jste si vybrali právě nás. Nezapomeňte se přihlásit a získáte první měsíc zdarma!“*

###### <a name="behavioral-push-sequence"></a>Sekvence nabízených oznámení chování
Cílem sekvence nabízených oznámení chování je zvýšit míru používání aplikace na základě dat o chování uživatele shromážděných pro danou aplikaci.  

Velmi aktivní uživatel aplikace fantasy fotbalu například může mít užitek z následujícího nabízeného oznámení...

*„Honzo, jste pravý fanoušek fotbalu! Přihlaste se k naší fotbalové sekci a vyhrajte lístky na finále poháru!“*

###### <a name="alerting-push-sequence"></a>Sekvence nabízených oznámení upozornění
Uživatelé ocení relevantní novinky týkající se jejich zájmů. Sekvence nabízených oznámení upozornění zvyšuje zapojení odesíláním upozornění na základě zřetelně projevených zájmů uživatele. Tyto zájmy lze odvodit přímo, například když je uživatel v aplikaci přímo vybere. Lze je také odvodit z dat shromážděných během interakce uživatele s aplikací.

Uživatel aplikace pro elektronické obchodování například může pravidelně nakupovat zboží od jedné značky kávy, což zaznamenáte pomocí obchodního klíčového ukazatele výkonu. Zapojení tohoto uživatele do používání aplikace může zvýšit následující upozornění.

*„Dobrý den, Petře, jedna z vašich oblíbených značek kávy se bude první týden v září 2015 prodávat s 25% slevou. Vážíme si toho, že jste naším zákazníkem, a chtěli jsme se ujistit, že vám tato nabídka neunikne.“*

###### <a name="rentention-push-sequence"></a>Sekvence nabízených oznámení uchování
Cílem této sekvence je udržet si uživatele pomocí kampaní opakovaných nabízených oznámení, která podporují zapojení uživatelů do používání aplikace. Pokud se uživateli příslušné interakce líbí, může to pomoci zvýšit míru uchování aplikace. 

Uživateli aplikace související se sportem například můžete týdně odesílat následující nabízené oznámení založené na jeho oblíbených týmech:

*„Hlasujte o tom, jestli Sparta o víkendu porazí Slávii, a vyhrajte 200 bodů!“*

#### <a name="the-3w-approach"></a>Koncepce KCK
Ovládnutí koncepcí různých sekvencí nabízených oznámení vám pomůže zapojit koncové uživatele. Stále však budete potřebovat také koncepci KCK, která vám je pomůže přizpůsobit. Koncepce KCK definuje Komu se jednotlivá oznámení odesílají, Co se v nich odesílá a Kdy se odesílají. Když získáte jasné odpovědi na tyto tři otázky, vaše oznámení by měla být správně zaměřena na podporu zapojení.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)

###### <a name="who-the-user-segment-that-will-receive-messages"></a>Kdo: Segment uživatelů, kteří budou zprávy dostávat
Odesílání nabízených oznámení uživatelům je nutné považovat za velmi citlivý komunikační kanál. Ujistěte se, že jsou oznámení, která chcete segmentu uživatelů odesílat, odpovídajícím způsobem zaměřená na zájmy uživatelů v daném segmentu. Nesprávně směrované oznámení bude mít velmi pravděpodobně na uživatele negativní vliv. Mohou ho považovat za spam a může je to vést k tomu, že vaši aplikaci odinstalují. 

Při definování segmentu uživatelů, kteří budou dostávat oznámení, použijte kombinaci specifických technických kritérií a kritérií chování. Jednoduchý příklad určení segmentu uživatelů představuje následující definice:

„Všichni uživatelé, kteří spustili aplikaci poprvé před třemi dny a dvakrát navštívili přihlašovací obrazovku, aniž by se skutečně přihlásili.“

Tato definice pomáhá identifikovat data, která by bylo potřeba shromáždit k podpoře určitého scénáře.

###### <a name="what-the-message-that-you-will-send"></a>Co: Zpráva, kterou odešlete
**Styl podání**

V oznámeních podporujících zapojení uživatelů používejte styl podání, který je vhodný pro vás i pro uživatele v segmentech. Jedná se o výborný prostředek pro navázání kontaktu s uživateli a podpoře jejich zájmu o aplikaci. 

**Přesměrování**

Nabízené oznámení lze použít k více účelům, než k pouhému spuštění aplikace. Pokud zpráva oznámení poskytuje kontext, například čerstvé zprávy nebo propagaci produktu, oznámení lze přímo propojit s příslušným obsahem aplikace. Za tímto účelem je nutné vytvořit schéma adres URL, které umožní aplikaci přesměrování spravovat. Při práci na sekvencích zapojení je to důležitý krok, na který nesmíte zapomenout.

Přesměrování lze spravovat i pro jiné systémy. Pomocí adresy URL lze přesměrovat koncové uživatele do množství dalších systému, včetně následujících:

* web
* poštovní schránka s již připraveným e-mailem
* schránka na zprávy SMS
* služba pro vytáčení telefonních čísel
* stránka hodnocení aplikace v obchodu s aplikacemi 

Tyto možnosti vám poskytují množství příležitostí k zapojení koncových uživatelů a vytváření automatických pravidel ke zlepšení výkonu.

**Formát a obsah**

Různé typy formátování nabízených oznámení:

1. **Oznámení**: Umožňují uživatelům v různých situacích odesílat reklamní zprávy (mimo aplikaci, v aplikaci nebo kdykoliv).
2. **Hlasování**: Umožňují od koncových uživatelů sbírat informace ve formě odpovědí na otázky. Tyto odpovědi jsou poté dostupné při vytváření kritérií k cílení na koncové uživatele.
3. **Datová oznámení**: Umožňují odesílat binární datové soubory a datové soubory ve formátu base64 aktualizací aplikace. Informace obsažené v datovém oznámení se odešlou do aplikace k přizpůsobení funkcí aplikace potřebám uživatele. Aplikace musí být navržena k podpoře dat v datových oznámeních.
4. **Dlaždice (pouze Windows Phone)**: Umožňuje používat  Microsoft Službu nabízených oznámení (MPNS) a odesílat její pomocí nativní nabízená oznámení obsahující data XML (podporováno od verze sady SDK 0.9.0; velikost datové části souborů nesmí překročit 32 kilobajtů). Zpráva se zobrazí přímo v dlaždici na ploše.
5. **Webové zobrazení**: Webové zobrazení je automaticky otevírané okno s webovým obsahem. Toto automaticky otevírané okno se zobrazí, když uživatel klikne na nabízené oznámení. Webové zobrazení umožňuje dosáhnout větší interakce s koncovým uživatelem.

> [!NOTE]
> Ujistěte se, že je obsah odesílaný jako nabízené oznámení kompatibilní s pravidly příslušné platformy (iOS, Android, Windows) pro vývoj aplikací a odesílání nabízených oznámení.
> 
> 

###### <a name="when-the-timing-of-your-campaign"></a>Kdy: Načasování vaší kampaně
Kdy je nejlepší čas aktivovat kampaň spouštějící nabízená oznámení? Má se aktivovat ručně nebo automaticky? Má se opakovat? K dosažení nejlepších výsledků zapojení uživatelů je velmi důležité určit správnou chvíli a frekvenci. Pro každou sekvenci a scénář zapojení je nutné určit nejlepší chvíli k odesílání nabízených oznámení. Zde je několik možných příkladů:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Pokud odesíláte nabízená oznámení denně, musíte důkladně zvážit, zda uživatelé vaše zprávy nebudou považovat za spam. 

Azure Mobile Engagement nabízí dvě metody jak zabránit tomu, aby uživatelé vaše zprávy považovali za spam. První z nich spočívá v jemné segmentaci, která zajistí, že nebudete cílit na stejné uživatele. Kromě toho Azure Mobile Engagement poskytuje tzv. „kvóty“. Pomocí této funkce lze omezit počet oznámení odeslaných v rámci kampaně. Nastavení kvóty 5 oznámení za týden, například zajistí, že uživatel zahrnutý do segmentu uživatelů kampaně v daném týdnu nedostane více než 5 oznámení.

#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Cvičení se scénářem 2: Vytvoření programu podpory zapojení
Věnujte nějaký čas shrnutí svých cílů a definování kampaní, které chcete uskutečňovat pomocí specifických sekvencí. Při definování oznámení kampaní vždy používejte koncepci KCK. 

Příklady a nápovědu najdete na listu **Engagement Program** (Program zapojení) v sešitu [Media Playbook Template][Media Playbook link] (Šablona scénáře pro média).

## <a name="step-3-app-integration"></a>Krok 3: Integrace aplikací
#### <a name="create-a-tag-plan"></a>Vytvoření plánu značek
K integraci Azure Mobile Engagementu do vaší aplikace budete potřebovat vytvořit plán značek. Plán značek je základním kamenem projektu. Definuje vztahy mezi marketingovými specifikacemi, pracovním postupem aplikace a skutečnými daty značek shromážděnými v aplikaci k měření klíčových ukazatelů výkonu. Určuje, jaké analytické údaje uvidíte na portálu. Pomáhá vám také definovat segmenty uživatelů a odesílat cílená nabízená oznámení k zapojení uživatelů. Jakmile je plán značek definovaný, přidání kódu k jeho integraci do aplikace pomocí sady Azure Mobile Engagement SDK je již snadné.

Plán značek by neměl značkami označovat úplně vše v aplikaci. Měl by zahrnovat pouze data značek, která jsou součástí vaší strategie pro zapojení mobilních zařízení. V různých aplikacích se bude pravděpodobně lišit. Sešit [Media Playbook Template][Media Playbook link] (Šablona scénáře pro média) poskytovaný platformou Azure Mobile Engagement vám pomocí dané metody pomůže sestavit plán značek. Jako vodítko k sestavení plánu značek použijte list **Tag Plan** (Plán značek).

Při definování sekce značek na listu buďte velmi konkrétní. Je to velmi důležité k zamezení nejasnostem. Podrobně popište jednotlivé scénáře ve kterých se budou odesílat jednotlivé značky. Přidejte také názvy aktivit, ve kterých jsou jednotlivé značky vložené. Všechny tyto informace by měly být v sekci **Informative** (Informativní) listu. List plánu značek by měl být hlavní referencí pro ověřování testů. 

V sekci **Data to collect** (Shromažďovaná data) by měl váš vývojový tým nalézt typy, názvy, hodnoty a páry klíč–hodnota dalších informací všech značek, které budou vloženy do aplikace.

Plán značek doporučujeme posoudit ve spolupráci se všemi týmy podílejícími se na projektu. Proveďte potřebné úpravy a ujistěte se, že je členům marketingových a vývojových týmů vše jasné.

List **Statement of work** (Popis práce) Lze použít jako návod pro všechny účastníky projektu.

#### <a name="data-types"></a>Typy dat
Níže jsou uvedeny běžné typy dat podporovaných Azure Mobile Engagementem.

###### <a name="devices-and-users"></a>Zařízení a uživatelé
Azure Mobile Engagement rozpoznává uživatele tak, že pro každé zařízení generuje jedinečný identifikátor. Tento identifikátor se nazývá identifikátor zařízení (neboli deviceid). Generuje se tak, že všechny aplikace spuštěné na stejném zařízení sdílejí stejný identifikátor.

###### <a name="sessions-and-activities"></a>Relace a aktivity
Relace je jedna instance aplikace spuštěná uživatelem. Relace začíná časem spuštění aplikace a končí časem ukončení aplikace.

Aktivita je logické seskupení sady různých akcí, které může aplikace provádět během relace. Obvykle se jedná o konkrétní obrazovku v aplikaci, ale může se jednat o cokoliv, co je definováno logikou aplikace. Minimem by mělo být označení jednotlivých obrazovek aplikace. Umožní vám to porozumět způsobu, jakým uživatelé s aplikací pracují.

###### <a name="events"></a>Události
Události se používají k hlášení interakcí uživatelů s aplikací. Může se jednat o okamžité akce, například sdílení obsahu nebo spuštění videa. Označení událostí značkami vám poskytne kolekce dat ukazujích způsob interakce uživatelů s aplikací. 

###### <a name="jobs"></a>Úlohy
Úlohy se používají k hlášení akcí s dobou trvání. Níže je několik příkladů:

* provádění volání rozhraní API
* doba zobrazení reklam
* doba provádění úloh na pozadí 
* doba trvání procesu nákupu
* přehrávání videa

###### <a name="errors"></a>Chyby
Chyby se používají k hlášení problémů zjištěných aplikací. Může se například jednat o chybné akce uživatelů nebo chyby volání rozhraní API.

###### <a name="application-information"></a>Informace o aplikaci
Značky informací o aplikaci (App-Info) se používají k označování dat souvisejících s činností koncových uživatelů. Je generována interakcí uživatele s aplikací. 

Pro daný klíč Azure Mobile Engagement sleduje pouze nejnovější hodnotu (bez historie). Značky informací o aplikaci poskytují informace o stavu aplikace a jejích koncových uživatelů. Například může jít o stav přihlášení nebo oblíbenou skupinu produktů uživatele.

###### <a name="crash-data"></a>Data o chybách
Data o chybách automaticky shromážděná sadou Mobile Engagement SDK poskytují informace o chybách aplikace, které aplikace nedokáže ošetřit. Může se jednat například o neošetřené výjimky.

###### <a name="extra-data"></a>Doplňující data
Informace o událostech, chybách, aktivitách a úlohách lze rozšířit o další parametry. Vývojáři mohou jako tyto doplňující informace poskytovat konkrétní data získaná z aplikace. Je to důležité pro jemné rozdělení uživatelů do segmentů. 

Hodnota značky „article“ (článek) vám například umožňuje zařadit koncové uživatele do konkrétního segmentu na základě toho, kdo zobrazil konkrétní článek. To však nemusí stačit. Lepší by bylo, kdyby stejná značka „article“ v aktivitě obsahovala také doplňující informace, například „news-category“ (kategorie zpráv). Bylo by to užitečné k dynamickému zjištění oblíbených kategorií uživatele. 

Další informace jsou poskytovány jako páry klíč–hodnota. V příkladu této mediální aplikace by byla hodnotou této kategorie doplňující informace „news–category“. Například „sports“ (sport), „economy“ (ekonomika) nebo „politics“ (politika).

#### <a name="tag-and-sdk-integration"></a>Značky a integrace sady SDK
Podrobné pokyny k integraci sady Azure Mobile Engagement SDK do své aplikace naleznete v dokumentaci k [integraci sady Engagement SDK](mobile-engagement-windows-store-integrate-engagement.md) na webu Azure. V odkazech nahoře na této stránce zvolte svou cílovou platformu.

Doporučujeme vytvářet projekty pro dvě aplikace založené na Azure Mobile Engagement. Jednu pro fáze vývoje a testování a druhou pro fáze produkce. Po úspěšném otestování přijetí uživateli poté váš IT tým může přejít z fáze testování do fáze produkce.

#### <a name="user-acceptance-testing-uat"></a>Testování přijetí uživateli
Účelem testování přijetí uživateli je ujistit se, že vše funguje tak, jak má. Lze dokončit pracovní postupy a na základě vašeho plánu značek získat všechna požadovaná data:

* Měly by být vestavěny značky informací podle zdokumentovaných koncepcí AZME.
* Měly by být shromážděny všechny potřebné informace (včetně doplňkových informací a informací o aplikaci).
* Terminologie by měla odpovídat plánu značek.
* Neměly by se odesílat žádné duplicitní značky.

Mělo by být důkladně otestováno chování všech typů oznámení vložených do aplikace:

* oznámení, hlasování, datová oznámení mimo aplikaci a v aplikaci
* textová a webová zobrazení
* aktualizace oznámení, kategorie

#### <a name="setup"></a>Nastavení
Nastavení Azure Mobile Engagementu je velmi snadné. Veškerá dokumentace související s uživatelským rozhraním je dostupná na webu Azure Mobile Engagementu [Jak navigovat v uživatelském rozhraní](mobile-engagement-user-interface-home.md).

Doporučuje se, abyste začali nastavením správných rolí a členství v rolích pro uživatele svého projektu.  Pomůže vám to spravovat přístup všech uživatelů k platformě. Můžete použít následující role:

* Správci
* Vývojáři
* Prohlížející 

Dále:

* Registrujte své deviceID, abyste mohli aplikaci testovat ve svém vlastním zařízení.
* Přejděte do nastavení účtu a nastavte časové pásmo, aby čas grafů a odesílání oznámení odpovídal vašemu časovému pásmu.
* Přejděte do nastavení aplikace a zaregistrujte informace o aplikaci, které potřebujete k cílení na koncové uživatele v Reachi.

Další informace o způsobu puštění své první kampaně nabízených oznámení najdete v článku [Začínáme s používáním a správou nabízených oznámení k oslovení koncových uživatelů](mobile-engagement-how-tos.md).

## <a name="conclusion"></a>Závěr
Programy zapojení se neustále opakují, a proto byste měli na základě nových poznatků o své aplikaci svůj program stále vylepšovat. 

Při získávání zkušeností se strategiemi zapojení se nepokoušejte rovnou sestavit úplnou globální strategii zapojení. Při identifikování svých klíčových ukazatelů výkonu a možnostech jejich využití postupujte krok za krokem. Strategie zapojení bude pro každou aplikaci jedinečná.

Až získáte dostatek zkušeností, můžete zvážit rozšíření svých programů zapojení o následující komponenty:

* Sledování: Získáte uživatele a pravděpodobně se vám podaří definovat zdroje sběru dat. Azure Mobile Engagement lze se zdroji sběru dat propojit. Umožní vám to sledovat přínos jednotlivých zdrojů. Tyto informace budou užitečné k maximalizaci vaší investice do pořízení. 
* Testování A/B: Jedná se o základní součást programu zapojení. Každá aplikace má svá vlastní specifika. Testování A/B vám umožní program zapojení vylepšit.
* Zeměpisná poloha: Funkce určování zeměpisné polohy mají obrovský potenciál pro značky. Díky těmto funkcím můžete uživatele oslovit ve správnou chvíli na správném místě. Před zahájením používání funkcí určování zeměpisné polohy doporučujeme ověřit, že jste získali dostatek informací o chování koncových uživatelů.
* Datová oznámení: Datová oznámení jsou neviditelná oznámení. Datová oznámení umožňují přizpůsobit aplikaci na základě chování koncových uživatelů. Pokud se například uživatelé v některém segmentu často zajímají o špičkové technologické produkty, lze jim odeslat datové oznámení, které přizpůsobí domovskou stránku jejich aplikace a přidá na ni obsah související se špičkovými technologickými produkty. 

## <a name="next-steps"></a>Další kroky
* [Vytvoření účtu Azure Mobile Engagementu](mobile-engagement-create.md).
* Další informace o definování strategie Mobile Engagementu najdete v článku [Definování strategie Mobile Engagementu](mobile-engagement-define-your-mobile-engagement-strategy.md).

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
