---
title: Začínáme – modelování nástroj Microsoft Threat – Azure | Dokumentace Microsoftu
description: Toto je hlubší přehled zvýraznění nástroj pro modelování hrozeb v akci.
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 340c92d2830069a9d957f4ece79416a707062629
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47096770"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Začínáme s nástroj pro modelování hrozeb

Cloudové a podnikové zabezpečení nástroje team vydání Preview nástroj pro modelování hrozeb počátkem tohoto roku jako bezplatná  **[klikněte na tlačítko Stáhnout](https://aka.ms/tmtpreview)**. Změnu v hodnotě mechanismus doručení umožňuje nabízet nejnovější vylepšení a opravy chyb pro zákazníky pokaždé, když otevřou nástroj, což usnadňuje údržbě a používání.
Tento článek vás provede procesem Začínáme se službou Microsoft SDL hrozeb modelování přístup a ukazuje, jak pomocí nástroje pro vývoj modely skvělé hrozeb jako páteřní zabezpečení procesu.

Tento článek vychází stávajících znalostí hrozeb SDL modelování přístup. Stručné shrnutí, najdete v tématu **[modelování ohrožení webových aplikací](https://msdn.microsoft.com/library/ms978516.aspx)** a archivované verze **[odkrýt zabezpečení chyby pomocí přístupu STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** Publikování článku MSDN v 2006.

Rychle Souhrnně řečeno, tento přístup zahrnuje vytvoření diagramu, identifikaci hrozeb, snížení rizik souvisejících s nimi a ověřování jednotlivá zmírnění. Zde je diagram, který zvýrazňuje tento proces:

![Proces SDL](./media/azure-security-threat-modeling-tool-feature-overview/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Spouští se proces modelování ohrožení

Když spustíte nástroj pro modelování hrozeb, můžete si všimnout pár věcí, jak je vidět na obrázku:

![Prázdné úvodní stránky](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Část model hrozeb

| Komponenta                                   | Podrobnosti                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Zpětná vazba, návrhy a tlačítko problémy** | Přejdete **[fórum na webu MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** pro všechno kolem SDL. To vám dává možnost číst prostřednictvím jiné činnosti uživatelů, spolu s jejich řešení a doporučení. Pokud stále nemůžete najít co hledáte, e-mailu tmtextsupport@microsoft.com pro náš tým podpory vám pomůže                                                                                                                            |
| **Vytvoření modelu**                          | Otevře se prázdné plátno nakreslení diagramu. Je nutné vybrat šablonu, kterou chcete použít pro váš model                                                                                                                                                                                                                                                                                                                                                                       |
| **Šablony pro nové modely**                 | Musíte vybrat šablonu lze použít, než vytváření modelu. Naší hlavní šablony je šablony modelu před internetovými útoky Azure, která obsahuje vzorníků týkající se Azure, hrozby a způsoby zmírnění rizik. Pro obecný modely vyberte z rozevírací nabídky SDL TM Knowledge Base. Chcete si vytvořit vlastní šablonu, nebo odešlete nový pro všechny uživatele? Podívejte se na naše **[úložiště šablon](https://github.com/Microsoft/threat-modeling-templates)** stránku Githubu Další informace                              |
| **Otevření modelu**                            | <p>Otevře se dříve uložený modely hrozeb. Naposledy otevřené modely funkce je skvělé, pokud je potřeba otevřít nejnovější soubory. Když najedete myší výběr, zjistíte 2 způsoby, jak otevřít modely:</p><p><ul><li>Otevřít z tohoto počítače – klasické způsobem, jak otevřít soubor pomocí místního úložiště</li><li>Otevřít z Onedrivu – mohou týmy používat složky ve Onedrivu pro ukládání a sdílení svých modely hrozeb a pomáhá tak zvýšit produktivitu a spolupráci na jednom místě</li></ul></p> |
| **Příručka Začínáme**                   | Otevře **[nástroj pro modelování hrozeb Microsoftu](./azure-security-threat-modeling-tool.md)** hlavní stránky                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Části šablony

| Komponenta               | Podrobnosti                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Vytvoření nové šablony** | Otevře se prázdné šablony vytváření. Pokud máte rozsáhlé znalosti při vytváření šablony ze začátku, doporučujeme vám umožní vytvářet z již existujících |
| **Otevřít šablonu**       | Otevře existující šablony můžete provést změny                                                                                                              |

Nástroj pro modelování hrozeb týmu soustavně pracujeme na zlepšení funkčnosti nástroje a prostředí. Několik menší změny může trvat místo v průběhu tohoto roku, ale všechny důležité změny vyžadují přepisů v průvodci. Přečtěte si k němu často Ujistěte se, že získáte nejnovější oznámení.

## <a name="building-a-model"></a>Sestavení modelu

V této části budeme postupovat podle:

- Cristina (Vývojář)
- Ricardo (programový manažer) a
- Ashish (tester)

Probíhá proces vývoje jejich první model hrozeb.

> Ricardo: Dobrý den Cristina, jsem pracoval v diagramu model hrozeb a chtěli jsme se ujistit, že nám dává podrobnosti správné. Můžete mi může pomoci Projděte část?
> Cristina: vůbec. Podívejme se na to.
> Ricardo otevře nástroj a sdílet svou obrazovku s Cristina.

![Základní rizik](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Ok, vyhledá jasné, ale můžete si projdete mi to?
> Ricardo: zda! Tady je výčet:
> - Naše lidské uživatele se vykreslí jako externí entity – čtverec
> - Při posílání příkazů do našich webového serveru – kruhu
> - Webový server je consulting databáze (paralelní dva řádky)

Co Ricardo chvilkou ukázal Cristina je diagramu toku dat, krátká pro  **[Diagram toku dat](https://en.wikipedia.org/wiki/Data_flow_diagram)**. Nástroj pro modelování hrozeb umožní uživatelům zadat hranicemi vztahů důvěryhodnosti označená červenou tečkované čáry, chcete-li zobrazit, kde jsou různé entity v ovládacím prvku. Například správci IT vyžadují systému Active Directory pro účely ověřování, proto je služba Active Directory nemá pod kontrolou.

> Cristina: Vypadá dobře mně. Jaké informace o hrozbách?
> Ricardo: Let me můžete zobrazit.

## <a name="analyzing-threats"></a>Analýza hrozeb

Jakmile se klikne na zobrazení analýzy z výběru nabídky ikonu (soubor s ikonou lupy), byl přijat do seznamu generovaných hrozeb najít nástroj pro modelování hrozeb na základě výchozí šablony, která používá přístupu SDL říká  **[ STRIDE (falšování identity, úmyslné poškozování, zpřístupnění informací, popírání odpovědnosti, odepření služby (DOS) a neautorizované zvýšení oprávnění)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Cílem je, že software pochází podle předvídatelné sadu hrozeb, které lze nalézt pomocí těchto 6 kategorií.

Tento přístup se podobá zabezpečení vaší organizace tím, že zajišťuje každý dveře a okno na místě před přidáním poplašný systém nebo dohledávání po bude moci zloděj má mechanismus zamykání.

![Základní hrozby](./media/azure-security-threat-modeling-tool-feature-overview/basicthreats.png)

Ricardo začíná tak, že vyberete první položku v seznamu. Stane se toto:

Nejprve je vylepšená interakce mezi dvěma vzorníků.

![Interakce](./media/azure-security-threat-modeling-tool-feature-overview/interaction.png)

Druhý, další informace o dané hrozbě se zobrazí v okně Vlastnosti před internetovými útoky

![Informace o interakce](./media/azure-security-threat-modeling-tool-feature-overview/interactioninfo.png)

Vygenerovaný hrozeb pomáhá mu pochopit potenciální chyby v návrhu. Kategorizace STRIDE mu poskytuje nápad na potenciální útoky, zatímco další popis říká mu přesně čem je problém, spolu s potenciální způsoby, jak ji zmírnit. Upravitelné pole může použít k psát poznámky v podrobnostech o zarovnání do bloku nebo změnit prioritu hodnocení v závislosti na panelu chyby ve své organizaci.

Šablony Azure mají další podrobnosti, které pomůžou uživatelům pomoct pochopit nejen čem je problém, ale také jak ten problém vyřešit tak, že přidáte popisy a příklady hypertextové odkazy na dokumentaci týkající se Azure.

Popis provedené ho realizovat důležitost Přidání ověřovacího mechanismu zabráníte uživatelům, aby se maskování odhalení první ohrožení mají zpracovat. Za několik minut do diskuse s Cristina, jejich chápali důležitost implementace řízení přístupu a rolí. Ricardo vyplnili některé rychlých poznámek, abyste měli jistotu, že tyto byly implementovány.

Jak Ricardo byly přidány do hrozby pod zpřístupnění informací, realizované mu, že plán řízení přístupu je potřeba některé účty jen pro čtení pro audit a generování sestav. Má zajímá, zda je třeba nové hrozby, ale zmírnění byly stejné, tak, že jste si poznamenali hrozby odpovídajícím způsobem.
Má také mluvit o zpřístupnění informací trochu více a díky, že byly záložní pásky bude potřebovat šifrování, úlohu pro provozní tým.

Z rozevíracího seznamu Stav zaručuje není k dispozici v návrhu z důvodu zabezpečení nebo existující zmírnění hrozby můžete změnit na "Není k dispozici". Existují tři jiné možnosti: Nezahájeno – výchozí výběr, potřebuje šetření – používají ke zpracování položek a Mitigated – Jakmile plně pracovali.

## <a name="reports--sharing"></a>Sestavy a sdílení

Jakmile Ricardo prochází seznam s Cristina a přidává důležité poznámky, zmírnění/důkazy, prioritu a stav se změní, vybere he sestavy -> vytvořit úplnou sestavu -> Uložit sestavu, která vytiskne přehledné sestavě ze absolvovat s kolegy zajistit správné zabezpečení práce je implementováno.

![Informace o interakce](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

Pokud chce mít Ricardo místo toho můžete soubor sdílet, mohl snadno to provedete kliknutím ukládání ve své organizaci účtu OneDrive. Po, který dělá, mohl zkopírovat odkaz na dokument a sdílet ho s jeho kolegové. 

## <a name="threat-modeling-meetings"></a>Schůzky modelování hrozeb

Ricardo odesílá jeho model hrozeb jeho kolega použití Onedrivu, Ashish, tester, byla underwhelmed. Vypadal jako jako Ricardo a Cristina vynechalo několik důležitých krajní případy, které může být snadno k ohrožení. Své strategie je doplněk k modely hrozeb.

V tomto scénáři po Ashish převzal model hrozeb volal pro dva schůzky modelování hrozeb: schůzek synchronizovat v procesu a podrobné vysvětlení diagramy druhý schůzku hrozeb kontrola a schválení.

První schůzce Ashish strávil 10 minut walking všem uživatelům prostřednictvím modelování procesu SDL hrozby. Potom dali diagramu modelu před internetovými útoky a spustit ji s vysvětlením podrobně. Do pěti minut byla určena důležité chybějící komponentu.

Za několik minut později Ashish a Richard dostal do rozšířené informace o tom, jak byla sestavena webový server. Nejednalo se o ideální způsob, jak schůzky, aby bylo možné pokračovat, ale všichni souhlasili nakonec již v rané fázi zjišťování nesrovnalosti můru, je ušetřit čas v budoucnosti.

Druhý schůzky v týmu provedl hrozby, popsané některé způsoby, jak je řešit a schválil model hrozeb. Se změnami dokumentu do správy zdrojového kódu a pokračovat s vývojem.

## <a name="thinking-about-assets"></a>Uvažujete o prostředcích

Některé čtenáře, kteří mají hrozeb modelovat si všimnout, že nebyly mluvili jsme o prostředky ve všech. Zjistili jsme, že mnoho softwarové inženýry pochopit jejich software lépe, než se rozumí koncept prostředky a jaké prostředky útočníka mohly zajímat.

Pokud se chystáte model hrozeb domu, můžete začít tím přemýšlení o řadu, nenahraditelných fotografie nebo cenné kresbu. Může být třeba začněte přemýšlet o tom, kdo může proniknout a aktuální zabezpečení systému. Nebo můžete začít v úvahu fyzické funkce, jako je fondu nebo předními porch. Toto jsou obdobou přemýšlení o prostředky, útočníci nebo návrh softwaru. Některé z těchto tří přístupů fungovat.

Přístup k modelování, který jsme jste okomentovat ohrožení je výrazně jednodušší, než co společnost Microsoft provedla v minulosti. Zjistili jsme, že přístup návrh softwaru funguje dobře pro mnoho týmů. Doufáme, že váš obsahující.

## <a name="next-steps"></a>Další kroky

Odesílat dotazy, komentáře a aspekty do tmtextsupport@microsoft.com. **[Stáhněte si](https://aka.ms/tmtpreview)**  nástroj pro modelování hrozeb začít pracovat.
