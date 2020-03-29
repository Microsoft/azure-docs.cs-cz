---
title: Začínáme – Nástroj pro modelování hrozeb Microsoft – Azure | Dokumenty společnosti Microsoft
description: Toto je hlubší přehled, který zvýrazňuje nástroj pro modelování hrozeb v akci.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 1454826095bcced9b20935405c0befd5a1ed1ddd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728302"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Začínáme s nástrojem pro modelování hrozeb

Microsoft Threat Modeling Tool 2018 byl vydán jako GA v září 2018 jako bezplatný **[click-to-download](https://aka.ms/threatmodelingtool)**. Změna mechanismu doručování nám umožňuje tlačit nejnovější vylepšení a opravy chyb zákazníkům při každém otevření nástroje, což usnadňuje údržbu a používání.
Tento článek vás provede procesem začínáme s přístupem modelování hrozeb Microsoft SDL a ukazuje, jak pomocí nástroje vyvíjet velké modely hrozeb jako páteř procesu zabezpečení.

Tento článek vychází z existující znalosti přístupu modelování hrozeb SDL. Stručný přehled naleznete v **[článku Threat Modeling Web Applications](https://msdn.microsoft.com/library/ms978516.aspx)** a archivované verzi aplikace **[Uncover Security Flaws Using the STRIDE Approach](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** MSDN article published in 2006.

Chcete-li rychle shrnout, přístup zahrnuje vytvoření diagramu, identifikaci hrozeb, jejich zmírnění a ověření každé zmírnění. Zde je diagram, který zvýrazňuje tento proces:

![Proces SDL](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Spuštění procesu modelování hrozeb

Když spustíte nástroj pro modelování hrozeb, všimnete si několika věcí, jak je vidět na obrázku:

![Prázdná úvodní stránka](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Oddíl modelu hrozby

| Komponenta                                   | Podrobnosti                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Tlačítko Zpětná vazba, návrhy a problémy** | Vezme si **[MSDN fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** pro všechny věci SDL. To vám dává možnost přečíst si, co ostatní uživatelé dělají, spolu s řešeními a doporučeními. Pokud stále nemůžete najít to, co hledáte, pošlete e-mail tmtextsupport@microsoft.com našemu týmu podpory, který vám pomůže                                                                                                                            |
| **Vytvoření modelu**                          | Otevře prázdné plátno, ve které můžete nakreslit diagram. Ujistěte se, že jste vybrali šablonu, kterou chcete pro model použít.                                                                                                                                                                                                                                                                                                                                                                       |
| **Šablona pro nové modely**                 | Před vytvořením modelu je nutné vybrat šablonu, která se má použít. Naší hlavní šablonou je šablona modelu hrozeb Azure, která obsahuje vzorníky, hrozby a skutečnosti snižující závažnost rizika specifické pro Azure. U obecných modelů vyberte znalostní bázi SDL TM z rozevírací nabídky. Chcete si vytvořit vlastní šablonu nebo odeslat novou šablonu pro všechny uživatele? Další informace najdete na stránce GitHub, kde se můžete dozvědět více **[o úložišti šablon](https://github.com/Microsoft/threat-modeling-templates)**                              |
| **Otevření modelu**                            | <p>Otevře dříve uložené modely hrozeb. Funkce Naposledy otevřené modely je skvělá, pokud potřebujete otevřít nejnovější soubory. Když na výběr najedete, najdete dva způsoby, jak modely otevřít:</p><p><ul><li>Otevřít z tohoto počítače – klasický způsob otevírání souboru pomocí místního úložiště</li><li>Otevření z OneDrivu – týmy můžou pomocí složek na OneDrivu ukládat a sdílet všechny modely hrozeb na jednom místě, aby zvýšily produktivitu a spolupráci.</li></ul></p> |
| **Příručka Začínáme**                   | Otevře hlavní stránku **[nástroje Microsoft Threat Modeling Tool.](threat-modeling-tool.md)**                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Oddíl Šablona

| Komponenta               | Podrobnosti                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Vytvořit novou šablonu** | Otevře prázdnou šablonu, na které můžete stavět. Pokud nemáte rozsáhlé znalosti v oblasti vytváření šablon od nuly, doporučujeme stavět ze stávajících |
| **Otevřít šablonu**       | Otevře existující šablony, které můžete provést změny                                                                                                              |

Tým nástrojů pro modelování hrozeb neustále pracuje na zlepšení funkčnosti a zkušeností nástrojů. Několik drobných změn může dojít v průběhu roku, ale všechny hlavní změny vyžadují přepsání v příručce. Často na něj překažte, abyste se ujistili, že dostanete nejnovější oznámení.

## <a name="building-a-model"></a>Vytvoření modelu

V této sekci sledujeme:

- Cristina (vývojář)
- Ricardo (programový manažer) a
- Ashish (tester)

Procházejí procesem vývoje svého prvního modelu hrozeb.

> Ricardo: Ahoj Cristina, pracoval jsem na diagramu modelu hrozby a chtěl jsem se ujistit, že máme správné detaily. Pomůžeš mi to pohlédnout?
> Cristina: Rozhodně. Pojďme se na to podívat.
> Ricardo otevře nástroj a sdílí svou obrazovku s Cristinou.

![Základní model ohrožení](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Ok, vypadá přímočaře, ale můžete mi to projít?
> Ricardo: Jistě! Zde je rozpis:
> - Náš lidský uživatel je vykreslen jako vnější entita – čtverec
> - Posílají příkazy na náš webový server – kruh
> - Webový server konzultuje databázi (dvě paralelní čáry)

Co Ricardo právě ukázal Cristina je DFD, **[zkratka](https://en.wikipedia.org/wiki/Data_flow_diagram)** pro data Flow Diagram . Nástroj pro modelování hrozeb umožňuje uživatelům určit hranice důvěryhodnosti označené červenými tečkovanými čarami, aby ukázali, kde mají kontrolu různé entity. Správci IT například vyžadují pro účely ověřování systém služby Active Directory, takže služba Active Directory je mimo jejich kontrolu.

> Cristina: Mně to připadá správné. A co ty výhružky?
> Dovolte mi, abych vám to ukázal.

## <a name="analyzing-threats"></a>Analýza hrozeb

Jakmile klikne na pohled analýzy z výběru nabídky ikon (soubor s lupou), je převelen do seznamu generovaných hrozeb, které nástroj pro modelování hrozeb nachází na základě výchozí šablony, která používá přístup SDL s názvem **[STRIDE (Falzení, Falkování, Zveřejnění informací, Odmítnutí, Odmítnutí služby a Zvýšení oprávnění)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Myšlenka spočívá v tom, že software spadá pod předvídatelnou sadu hrozeb, které lze nalézt pomocí těchto 6 kategorií.

Tento přístup je jako zajištění vašeho domu tím, že zajistí, že každé dveře a okno má blokovací mechanismus na místě před přidáním poplašného systému nebo honí za zlodějem.

![Základní hrozby](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo začíná výběrem první položky v seznamu. Zde je to, co se stane:

Za prvé, interakce mezi těmito dvěma vzorníky je posílena

![Interakce](./media/threat-modeling-tool-getting-started/interaction.png)

Za druhé, další informace o hrozbě se zobrazí v okně Vlastnosti hrozby

![Informace o interakci](./media/threat-modeling-tool-getting-started/interactioninfo.png)

Vytvořená hrozba mu pomáhá pochopit potenciální konstrukční nedostatky. Kategorizace STRIDE mu dává představu o potenciálních útočných vektorech, zatímco další popis mu přesně říká, co je špatně, spolu s možnými způsoby, jak to zmírnit. Pomocí upravitelných polí může psát poznámky do podrobností odůvodnění nebo měnit hodnocení priorit v závislosti na panelu chyb jeho organizace.

Šablony Azure obsahují další podrobnosti, které uživatelům pomáhají pochopit nejen to, co je špatně, ale také jak to opravit přidáním popisů, příkladů a hypertextových odkazů do dokumentace specifické pro Azure.

Popis ho přiměl uvědomit si, že je důležité přidat ověřovací mechanismus, který zabrání tomu, aby uživatelé byli podivíni, a odhalil i první hrozbu, na které se pracuje. Několik minut po diskusi s Cristinou pochopili, jak je důležité implementovat řízení přístupu a role. Ricardo vyplnil několik rychlých poznámek, aby se ujistil, že byly implementovány.

Když Ricardo vstoupil do hrozeb v rámci informací o zpřístupnění, uvědomil si, že plán kontroly přístupu vyžaduje některé účty jen pro čtení pro audit a generování zpráv. Přemýšlel, zda by to měla být nová hrozba, ale zmírnění bylo stejné, a tak si všiml hrozby odpovídajícím způsobem.
Také přemýšlel o zpřístupnění informací trochu víc a uvědomil si, že záložní pásky budou potřebovat šifrování, práci pro operační tým.

Hrozby, které se nevztahují na návrh z důvodu stávajících zmírnění rizik nebo záruk zabezpečení, lze z rozevíracího přehledu Stav změnit na "Nepoužít". Existují tři další možnosti: Nezahájeno – výchozí výběr, potřeby šetření – slouží k sledování položek a zmírnění – jakmile je plně zpracována.

## <a name="reports--sharing"></a>Sestavy & sdílení

Jakmile Ricardo projde seznam s Cristinou a přidá důležité poznámky, zmírnění / odůvodnění, priority a změny stavu, vybere zprávy -> Vytvořit úplnou zprávu -> Uložit report, který vytiskne pěknou zprávu, kterou má projít s kolegy, aby zajistil správnou bezpečnostní práci.

![Informace o interakci](./media/threat-modeling-tool-feature-overview/report.png)

Pokud chce Ricardo soubor místo toho sdílet, může to snadno provést uložením do účtu OneDrive své organizace. Jakmile to udělá, může zkopírovat odkaz na dokument a sdílet jej se svými kolegy. 

## <a name="threat-modeling-meetings"></a>Schůzky modelování hrozeb

Když Ricardo poslal svůj model hrozby svému kolegovi pomocí OneDrive, Ashish, tester, byl ohromen. Zdálo se, že Ricardo a Cristina vynechali několik důležitých rohových případů, které by mohly být snadno ohroženy. Jeho skepse je doplňkem modelů hrozeb.

V tomto scénáři poté, co Ashish převzal model hrozeb, vyzval ke dvěma schůzkám modelování hrozeb: jedné schůzce, která se synchronizuje v procesu a prochází diagramy a pak druhá schůzka pro kontrolu a odhlášení hrozeb.

Na prvním setkání, Ashish strávil 10 minut procházky všichni přes proces modelování hrozeb SDL. Poté vytáhl diagram modelu hrozby a začal ho podrobně vysvětlovat. Během pěti minut byla identifikována důležitá chybějící součástka.

O několik minut později, Ashish a Ricardo dostal do rozšířené diskuse o tom, jak byl vytvořen webový server. Nebylo to ideální způsob, jak pokračovat v jednání, ale všichni se nakonec shodli na tom, že včasné odhalení nesrovnalosti jim ušetří čas v budoucnu.

Na druhém setkání tým prošel hrozbami, projednal některé způsoby, jak je řešit, a podepsal model hrozeb. Zkontrolovali dokument do správy zdrojového kódu a pokračovali ve vývoji.

## <a name="thinking-about-assets"></a>Přemýšlení o aktivech

Někteří čtenáři, kteří mají hrozbu modelován si mohou všimnout, že jsme nemluvili o majetku vůbec. Zjistili jsme, že mnoho softwarových inženýrů rozumí svému softwaru lépe než konceptu datových zdrojů a o to, jaký majetek by útočník mohl zajímat.

Pokud se chystáte vyhrožovat model domu, můžete začít tím, že přemýšlíte o své rodině, nenahraditelných fotografiích nebo cenných uměleckých dílech. Možná byste mohl začít tím, že přemýšlíte o tom, kdo by se mohl vloupat dovnitř a současný bezpečnostní systém. Nebo můžete začít tím, že zvážífyzické rysy, jako je bazén nebo přední veranda. Ty jsou obdobou přemýšlení o datových zdrojech, útočnících nebo návrhu softwaru. Některý z těchto tří přístupů funguje.

Přístup k modelování hrozeb, který jsme zde prezentovali, je podstatně jednodušší než to, co společnost Microsoft udělala v minulosti. Zjistili jsme, že přístup k návrhu softwaru funguje dobře pro mnoho týmů. Doufáme, že to bude zahrnovat i to vaše.

## <a name="next-steps"></a>Další kroky

Své dotazy, připomínky tmtextsupport@microsoft.coma připomínky zasílejte společnosti . **[Stáhněte si](https://aka.ms/threatmodelingtool)** nástroj pro modelování hrozeb a můžete začít.
