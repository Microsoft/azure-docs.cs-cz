---
title: Začínáme – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Naučte se, jak začít používat Threat Modeling Tool. Vytvořte diagram, identifikujte hrozby, zmírňujte hrozby a ověřte jednotlivá rizika.
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
ms.openlocfilehash: 322f5f6a79bdce23706b2211ccc04ef2451675d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94515723"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Začínáme s Threat Modeling Tool

Microsoft Threat Modeling Tool 2018 byla vydána jako GA v září 2018 jako bezplatný soubor **[ke stažení](https://aka.ms/threatmodelingtool)**. Mechanismus změny v doručení nám umožňuje nabízet nejnovější vylepšení a opravy chyb zákazníkům pokaždé, když tento nástroj otevřou, což usnadňuje údržbu a používání.
Tento článek vás provede procesem Začínáme s řešením Microsoft SDL Threat Modeling a ukazuje, jak používat nástroj k vývoji skvělých modelů hrozeb jako páteře procesu zabezpečení.

Tento článek se sestavuje s existujícím vědomím přístupu k modelování hrozeb v SDL. V případě rychlé kontroly se podívejte na **[webové aplikace modelování hrozeb](/previous-versions/msp-n-p/ff648006(v=pandp.10))** a Archivovaná verze řešení chyb **[zabezpečení na základě přístupu k](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** článku věnovaném webu MSDN publikovaném v 2006.

K rychlému sumarizaci je potřeba, abyste vytvořili diagram, identifikovali hrozby, zmírnili je a vyhodnotili případné zmírnění. Zde je diagram, který zvýrazní tento proces:

![Proces SDL](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Spouští se proces modelování hrozeb.

Když spustíte Threat Modeling Tool, všimnete si pár věcí, jak je vidět na obrázku:

![Prázdná úvodní stránka](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Oddíl modelu hrozeb

| Součást                                   | Podrobnosti                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Názory, tlačítko návrhy a problémy** | Přejdete na **[Fórum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** pro všechny věci SDL. Nabízí vám možnost si přečíst si, co dělají jiní uživatelé, spolu s alternativními řešeními a doporučeními. Pokud stále nemůžete najít, co hledáte, pošlete tmtextsupport@microsoft.com nám e-mail pro náš tým podpory, který vám může pomoci                                                                                                                            |
| **Vytvoření modelu**                          | Otevře prázdné plátno pro vykreslení diagramu. Ujistěte se, že jste vybrali šablonu, kterou chcete použít pro váš model.                                                                                                                                                                                                                                                                                                                                                                       |
| **Šablona pro nové modely**                 | Před vytvořením modelu je nutné vybrat šablonu, kterou chcete použít. Naší hlavní šablonou je šablona modelu Azure Threat, která obsahuje vzorníky specifické pro Azure, hrozby a omezení rizik. V případě obecných modelů vyberte v rozevírací nabídce možnost znalostní báze SDL TM. Chcete vytvořit vlastní šablonu nebo Odeslat novou pro všechny uživatele? Další informace najdete na naší stránce GitHubu **[úložiště šablon](https://github.com/Microsoft/threat-modeling-templates)** .                              |
| **Otevření modelu**                            | <p>Otevře dříve uložené modely hrozeb. Funkce nedávno otevřených modelů je skvělé, pokud potřebujete otevřít nejaktuálnější soubory. Když najedete myší na výběr, najdete dva způsoby otevření modelů:</p><p><ul><li>Otevřít z tohoto počítače – klasický způsob otevření souboru pomocí místního úložiště</li><li>Otevřené z OneDrivu – týmy můžou používat složky na OneDrivu k ukládání a sdílení všech jejich modelů hrozeb na jednom místě, které vám pomůžou zvýšit produktivitu a spolupráci.</li></ul></p> |
| **Průvodce Začínáme**                   | Otevře hlavní stránku **[Microsoft Threat Modeling Tool](threat-modeling-tool.md)** .                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Oddíl Template

| Součást               | Podrobnosti                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Vytvořit novou šablonu** | Otevře prázdnou šablonu, na kterou se má vytvořit. Pokud nemáte rozsáhlé znalosti při vytváření šablon od začátku, doporučujeme, abyste si vytvořili z existujících. |
| **Otevřít šablonu**       | Otevře existující šablony, abyste mohli provádět změny.                                                                                                              |

Tým Threat Modeling Tool stále pracuje na vylepšení funkcí a zkušeností nástrojů. V průběhu roku může probíhat několik menších změn, ale všechny významné změny vyžadují přepis v průvodci. Přečtěte si často, abyste měli jistotu, že získáte nejnovější oznámení.

## <a name="building-a-model"></a>Vytvoření modelu

V této části budeme postupovat takto:

- Cristina (vývojář)
- Ricardo (Správce programů) a
- Ashish (tester)

Procházejí procesem vývoje prvního modelu hrozeb.

> Ricardo: Dobrý den Cristina, jsem pracoval na diagramu modelu hrozeb, a chtěli byste se ujistit, že jsme získali oprávnění details. Můžu se mi lépe podívat na to?
> Cristina: naprosto. Pojďme se na to podívat.
> Ricardo otevře nástroj a nasdílí jeho obrazovku s Cristina.

![Model Basic Threat](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: OK, vypadá to jasné, ale můžete s ním vyzkoušet?
> Ricardo: Ujistěte se, že! Toto je rozpis:
> - Náš lidský uživatel je vykreslen jako externí entita – čtverec
> - Odesílají příkazy na náš webový server – kruh
> - Webový server je v konzultační databázi (dva paralelní čáry).

Jaký Ricardo se právě ukázal jako Cristina, je **[diagram toku dat](https://en.wikipedia.org/wiki/Data_flow_diagram)** krátký. Threat Modeling Tool umožňuje uživatelům zadat hranice důvěry, které označují červeně tečkované řádky, k zobrazení, kde jsou různé entity v ovládacím prvku. Správci IT například vyžadují pro účely ověřování systém Active Directory, takže služba Active Directory je mimo svůj ovládací prvek.

> Cristina: vypadá vpravo. Co jsou hrozby?
> Ricardo: nyní vám ukážeme.

## <a name="analyzing-threats"></a>Analýza hrozeb

Jakmile kliknete na zobrazení analýzy z výběru nabídky ikony (soubor se zvětšováním skla), převezme se seznam generovaných hrozeb Threat Modeling Tool nalezen na základě výchozí šablony, která používá přístup SDL s názvem **[Rozteč (falšování identity, manipulace, zpřístupnění informací, odmítnutí služby a zvýšení oprávnění)](https://en.wikipedia.org/wiki/STRIDE_(security))**. Účelem je, aby se software objevil v předvídatelné sadě hrozeb, které se dají najít pomocí těchto 6 kategorií.

Tento přístup se podobá zabezpečení vaší domu tím, že před přidáním poplašného systému nebo dohledávání po moci zloděj zajistíte, aby jednotlivé dveře a okna byly na svém místě blokovacího mechanismu.

![Základní hrozby](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo začíná výběrem první položky v seznamu. Co se stane:

Za prvé je vylepšená interakce mezi těmito dvěma vzorníky.

![Snímek obrazovky se dvěma předlohami a zakřivenou šipkou, která je propojuje s těžší tloušťkou čáry.](./media/threat-modeling-tool-getting-started/interaction.png)

Za druhé se zobrazí další informace týkající se hrozby okno Vlastnosti

![Snímek obrazovky zobrazuje okno Vlastnosti hrozeb, která obsahuje název, kategorii, popis, interakci a prioritu.](./media/threat-modeling-tool-getting-started/interactioninfo.png)

Vygenerovaná hrozba pomáhá pochopit možné nedostatky v návrhu. Kategorizace v ROZTEČi poskytuje představu o potenciálních způsobech útoku, zatímco další popis přesně informuje o tom, co je špatně, a také potenciální způsoby, jak to zmírnit. Může použít upravitelná pole k zápisu poznámek v podrobnostech odůvodnění nebo změnu hodnocení priority v závislosti na panelu chyb ve své organizaci.

Šablony Azure obsahují další podrobnosti, které uživatelům pomohou pochopit nejen to, co je špatně, ale také jak je opravit přidáním popisů, příkladů a hypertextových odkazů do dokumentace ke službě Azure specifické pro Azure.

Popis má za to důležitost přidání mechanismu ověřování, který uživatelům brání v falšování identity, což odhalí první hrozbu, na které se má pracovat. Několik minut do diskuze s Cristina se rozumí důležitosti při implementaci řízení přístupu a rolí. Ricardo vyplnili některé rychlé poznámky, abyste se ujistili, že byly implementovány.

Protože Ricardo došlo k hrozbám v oblasti zpřístupnění informací, vytvořil plán řízení přístupu pro audit a generování sestav některé účty jen pro čtení. Přemýšleli, jestli by se jednalo o novou hrozbu, ale jejich zmírnění bylo stejné, a proto si ji poznamenali.
Mysleli jsme také o zpřístupnění informací o větším a realizovaném, že záložní pásky budou potřebovat šifrování, což je úloha pro provozní tým.

V rozevíracím seznamu stav můžete změnit hrozby, které se nevztahují na návrh z důvodu stávajících rizik nebo záruk zabezpečení. Existují tři další možnosti: Nezahájeno – výchozí výběr, nutné šetření – používá se k tomu, aby se sledovaly položky a zmírnily – Jakmile je plně fungovalo.

## <a name="reports--sharing"></a>Sestavy & sdílení

Jakmile Ricardo projde seznamem pomocí Cristina a přidá důležité poznámky, zmírnění a odůvodnění, priority a změny stavu, vybere sestavy – > vytvořit úplnou sestavu > Uložit sestavu, která pro ni vytiskne zprávu, která bude mít k dispozici, aby bylo zajištěno správné fungování zabezpečení.

![Snímek obrazovky se zobrazí jako zástupce sestavy modelování hrozeb.](./media/threat-modeling-tool-feature-overview/report.png)

Pokud místo toho chce Ricardo sdílet soubor, může to udělat tak, že se uloží na účet OneDrive jeho organizace. Jakmile to provede, může zkopírovat odkaz na dokument a sdílet ho s kolegy. 

## <a name="threat-modeling-meetings"></a>Schůzky modelování hrozeb

Když Ricardo poslal svůj model hrozeb svému kolegovi přes OneDrive, Ashish, Tester underwhelmed. Zdálo se, že Ricardo a Cristina neúplně ještě několik důležitých rohových případů, které by bylo možné snadno ohrozit. Jeho skepticismus je doplňkem k modelům hrozeb.

V tomto scénáři poté, co Ashish převzala přes model hrozeb, zavolat na dvě schůzky modelování hrozeb: jednu schůzku pro synchronizaci na procesu a Projděte si diagramy a pak druhou schůzku pro kontrolu a odhlášení hrozby.

V první schůzce Ashish vyčerpáno 10 minut, aby všichni prošli procesem modelování hrozeb v SDL. Pak sestaví diagram modelu hrozeb a začne ho vysvětlit podrobněji. Během pěti minut bylo zjištěno důležité chybějící součásti.

Několik minut později, Ashish a Ricardo, dostali se do rozšířené diskuze o tom, jak byl webový server sestaven. Nejednalo se o ideální způsob, jak pokračovat v konání schůzky, ale všichni nakonec souhlasili s tím, že v budoucnu budou ušetřit čas v budoucnosti.

V druhé schůzce tým vás provedl přes hrozby, projednali si některé způsoby, jak je vyřešit, a odhlásili se v modelu hrozeb. Kontrolovaly dokument do správy zdrojového kódu a pokračuje ve vývoji.

## <a name="thinking-about-assets"></a>Uvažujete o prostředcích

Někteří čtenáři, kteří mají model hrozeb, si můžou všimnout, že jsme ještě nemluvilii o prostředcích. Zjistili jsme, že velký počet softwarových inženýrů rozumí jejich softwaru lépe než porozumění konceptu prostředků a k jakým prostředkům by útočník mohl zajímat.

Pokud se chystáte o hrozby, můžete začít myslet o své rodině, nenahraditelných fotografiích nebo cenném kresbě. Možná budete chtít začít tím, kdo může přerušit a aktuální systém zabezpečení. Nebo můžete začít zvážením fyzických funkcí, jako je fond nebo front-Porch. Jedná se o analogické informace o prostředcích, útočníkech nebo návrhu softwaru. Každý z těchto tří přístupů funguje.

Přístup k modelování hrozeb, který jsme tady provedli, je podstatně jednodušší než to, co Microsoft v minulosti udělal. Zjistili jsme, že přístup k návrhu softwaru funguje dobře pro mnoho týmů. Doufáme, že to bude obsahovat.

## <a name="next-steps"></a>Další kroky

Pošlete své dotazy, komentáře a obavy do tmtextsupport@microsoft.com . Začněte **[stažením](https://aka.ms/threatmodelingtool)** Threat Modeling Tool.