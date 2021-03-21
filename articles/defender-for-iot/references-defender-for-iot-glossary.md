---
title: Glosář Defenderu for IoT
description: V tomto glosáři najdete stručný popis důležitých Defenderů pro pojmy a koncepty platforem IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: f26cea9442aa3fbbe7f475cc5d16bea792b83fb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493981"
---
# <a name="defender-for-iot-glossary"></a>Glosář Defenderu for IoT

V tomto glosáři najdete stručný popis důležitých pojmů a konceptů pro platformu Azure Defender pro IoT. Kliknutím na odkaz další **informace** můžete přejít na související výrazy v glosáři. To vám pomůže rychleji se naučit a používat nástroje produktu.

> [!Note]
> Jakákoli podmínka, která je `(DB)` uvedena v názvu, je termínem tvůrce zařízení založený na agentovi. 

<a name="glossary-a"></a>

## <a name="a"></a>A

| Období | Description | Další informace |
|--|--|--|
| **Přístup ke skupině** | Podpora požadavků uživatelů na přístup pro velké organizace vytvořením pravidel skupin pro přístup<br /><br />Pravidla umožňují řídit zobrazení a konfiguraci přístupu k místní konzole pro správu služby Defender pro IoT pro konkrétní role uživatelů v příslušných obchodních jednotkách, oblastech, lokalitách a zónách.<br /><br />Například umožňuje analytikům zabezpečení ze skupiny služby Active Directory přístup k datům západní Evropa, ale nebrání přístup k datům v Africe. | **[Místní Konzola pro správu](#o)** <br /><br />**[Obchodní jednotka](#b)** |
| **Přístupové tokeny** | Vygenerujte přístupové tokeny pro přístup k programu Defender pro IoT REST API. | **[Rozhraní API](#glossary-a)** |
| **Potvrdit událost výstrahy** | Dejte programu Defender pro IoT, aby výstrahu pro zjištěnou událost jednou skryl. Po opětovném zjištění události bude výstraha znovu aktivována. | **[Výstraha](#glossary-a) <br /> <br /> [Zobrazit událost výstrahy](#l) <br /> <br /> [Ztlumení události výstrahy](#m)** |
| **Výstraha** | Zpráva, kterou Defender pro modul IoT spustí v souvislosti s odchylkami od autorizovaného chování sítě, anomálií sítě nebo podezřelé síťové aktivity a provozu. | **[Pravidlo předávání](#f) <br /> <br /> [Pravidlo vyloučení](#e) <br /> <br /> [Systémová oznámení](#s)** |
| **Komentář k výstraze** | Komentáře, které analytici a správci zabezpečení vytvářejí v varovných zprávách. Komentář k výstraze může například poskytnout pokyny týkající se zmírňující akce, které se mají provést, nebo jména jednotlivců, kteří se na událost vztahují.<br /><br />Uživatelé, kteří kontrolují výstrahy, mohou zvolit komentář nebo komentáře, které nejlépe odpovídají stavu události, nebo kroky podniknuté k prošetření výstrahy. | **[Výstrahy](#glossary-a)** |
| **Modul anomálií** | Defender pro modul IoT, který detekuje neobvyklou komunikaci a chování typu počítač-počítač (M2M). Modul může například detekovat nadměrné pokusy o přihlášení SMB. Výstrahy anomálií se spouštějí při zjištění těchto událostí. | **[Defender pro moduly IoT](#d)** |
| **Rozhraní API** | Umožňuje externím systémům přístup k datům zjištěným pomocí programu Defender pro IoT a provádění akcí pomocí externích REST API přes připojení SSL. | **[Přístupové tokeny](#glossary-a)** |
| **Sestava vektoru útoku** | Grafická reprezentace řetězů ohrožení zabezpečení v reálném čase.<br /><br />Sestavy umožňují vyhodnotit účinek zmírňující aktivity v sekvenci útoků, které vám pomůžou určit. Můžete například vyhodnotit, jestli upgrade systému narušuje cestu útočníka tím, že se přeruší řetězec útoku, nebo jestli zůstane alternativní cesta k útoku. Tato akce má přednost při nápravných a zmírňních aktivitách. | **[Sestava posouzení rizik](#r)** |

## <a name="b"></a>B

| Období | Description | Další informace |
|--|--|--|
| **Obchodní jednotka** | Logická organizace vaší firmy podle konkrétního odvětví.<br /><br />Například globální společnost, která obsahuje skleněné továrny a plastové továrny, může být spravovaná jako dvě různé obchodní jednotky. Můžete řídit přístup programu Defender pro uživatele IoT na konkrétní obchodní jednotky. | **[Místní konzola](#o) <br /> <br /> pro správu [Přístup ke skupině](#glossary-a) <br /> <br /> [Webový server](#s) <br /> <br /> [Zóna](#z)** |
| **Standardní hodnoty** | Schválený síťový provoz, protokoly, příkazy a zařízení. Defender pro IoT identifikuje odchylky od směrného plánu sítě. Vygenerování sestav dolování dat vám zobrazí schválený základní provoz. | **[Dolování dat](#d) <br /> <br /> [Výukový režim](#l)** |

## <a name="c"></a>C

| Období | Description | Další informace |
|--|--|--|
| **Příkazy rozhraní příkazového řádku** | Možnosti rozhraní příkazového řádku (CLI) pro uživatele v programu Defender pro správce služby IoT. Příkazy rozhraní příkazového řádku jsou k dispozici pro funkce, ke kterým nelze získat přístup z konzoly Defender pro služby IoT. | - |


## <a name="d"></a>D

| Období | Description | Další informace |
|--|--|--|
| **Dolování dat** | Generování komplexních a podrobných sestav o síťových zařízeních:<br /><br />- **Odpověď na incident SOC**: sestavy v reálném čase, které vám pomůžou s okamžitou reakcí na incidenty. Sestava může například vypsat zařízení, která by mohla vyžadovat opravy.<br /><br />- **Forenzní**: sestavy založené na historických datech pro vyšetřovací sestavy.<br /><br />- **Integrita sítě IT**: sestavy, které pomůžou zlepšit celkové zabezpečení sítě. Sestava může například vypsat zařízení se slabými přihlašovacími údaji pro ověřování.<br /><br />- **visibility**: sestavy, které pokrývají všechny položky dotazu pro zobrazení všech parametrů standardních hodnot vaší sítě.<br /><br />Ukládat sestavy dolování dat pro uživatele, kteří jsou jen pro čtení, k zobrazení. | **[Standardní hodnoty](#b) <br /> <br /> [Sestavy](#r)** |
| **Defender pro moduly IoT** | Analytické moduly pro samoobslužné učení v programu Defender pro IoT eliminují nutnost aktualizace podpisů nebo definování pravidel. Moduly využívají analýzy chování specifické pro ICS a datové vědy k nepřetržité analýze síťového provozu pro anomálie, malware, provozní problémy, porušení protokolu a odchylky od aktivity základní sítě.<br /><br />Pokud modul zjistí odchylku, aktivuje se výstraha. Výstrahy můžete zobrazit a spravovat z obrazovky **výstrahy** nebo z Siem. | **[Výstrahy](#glossary-a)** |
| **Defender pro platformu IoT** | Řešení Defender for IoT nainstalované v programu Defender pro senzory IoT a místní konzolu pro správu. | **[](#s) <br /> Senzor <br /> [Místní Konzola pro správu](#o)** |
| **Mapa zařízení** | Grafická reprezentace síťových zařízení, která Defender pro IoT detekuje Zobrazuje připojení mezi zařízeními a informacemi o jednotlivých zařízeních. Použijte mapování na:<br /><br />– Načtení a řízení důležitých informací o zařízení.<br /><br />– Analyzujte síťové řezy.<br /><br />– Exportovat podrobnosti a souhrny zařízení | **[Purdue – skupina vrstev](#p)** |
| **Inventář zařízení – senzor** | Inventář zařízení zobrazuje rozsáhlou škálu atributů zařízení zjištěných v programu Defender pro IoT. Možnosti jsou k dispozici pro:<br /><br />-Filtrovat zobrazené informace.<br /><br />– Exportujte tyto informace do souboru CSV.<br /><br />– Importujte podrobnosti registru Windows. | **[Group (Skupina)](#g)** <br /><br />**[Inventář zařízení – místní Konzola pro správu](#d)** |
| **Inventář zařízení – místní Konzola pro správu** | Informace o zařízení od připojených senzorů můžete zobrazit z místní konzoly pro správu v inventáři zařízení. To uživatelům místní konzole pro správu poskytuje ucelený přehled o všech síťových informacích. | **[Inventář zařízení – senzor](#d) <br /> <br /> [Inventář zařízení – integrátor dat](#d)** |
| **Inventář zařízení – integrátor dat** | Možnosti integrace dat v místní konzole pro správu umožňují vylepšit data v inventáři zařízení s informacemi z dalších podnikových prostředků. Příkladem prostředků jsou CMDBs, DNS, firewall a webová rozhraní API. | **[Inventář zařízení – místní Konzola pro správu](#d)** |
| **Vlákna zařízení**`(DB)` | Vlákna zařízení jsou dokumenty JSON, které ukládají informace o stavu zařízení včetně metadat, konfigurací a podmínek. | [Modul je nevlákenný](#m) <br /> <br />[Defender-IoT-Micro-agent nevláken](#s) |

## <a name="e"></a>E

| Období | Description | Další informace |
|--|--|--|
| **Podnikové zobrazení** | Globální mapa, která prezentuje obchodní jednotky, lokality a zóny, kde jsou nainstalovány nástroje pro obranu pro IoT snímače. Zobrazit geografická umístění škodlivých výstrah, provozní výstrahy a další. | **[Organizační jednotka](#b) <br /> <br /> [Webový server](#s) <br /> <br /> [Zóna](#z)** |
| **Časová osa události** | Časová osa aktivity zjištěná ve vaší síti, včetně:<br /><br />– Aktivované výstrahy.<br /><br />– Události sítě (informativní).<br /><br />– Uživatelské operace jako přihlašování, odstraňování uživatelů a vytváření uživatelů a operace správy výstrah, jako je ztlumení, učení a potvrzení. K dispozici v konzolách snímačů. | - |
| **Pravidlo vyloučení** | Dejte programu Defender pro IoT, aby ignoroval triggery výstrah na základě časového období, adresy zařízení a názvu výstrahy nebo konkrétního senzoru.<br /><br />Pokud například víte, že všechna zařízení, která monitoruje určitý senzor, procházejí postupem údržby mezi 6:30 a 10:15 v ráno, můžete nastavit pravidlo vyloučení, které uvádí, že by tento senzor neměl v předdefinovaném období odesílat výstrahy. | **[Výstraha](#glossary-a) <br /> <br /> [Ztlumení události výstrahy](#m)** |

## <a name="f"></a>F

| Období | Description | Další informace |
|--|--|--|
| **Pravidlo předávání** | Pravidla předávání instruují program IoT, aby odesílal informace o výstrahách dodavatelům nebo systémům partnerů.<br /><br />Můžete například odesílat informace o výstrahách na server Splunk nebo na server syslog. | **[Výstrahy](#glossary-a)** |

## <a name="g"></a>G

| Období | Description | Další informace |
|--|--|--|
| **Skupina** | Předdefinované nebo vlastní skupiny zařízení, které obsahují konkrétní atributy, jako jsou například zařízení, která provedla činnost programování nebo zařízení umístěná v konkrétní podsíti. Použijte skupiny, které vám pomůžou zobrazit zařízení a analyzovat zařízení v programu Defender pro IoT.<br /><br />Skupiny se dají zobrazit v a vytvořit z mapy zařízení a inventáře zařízení. | **[Mapa zařízení](#d) <br /> <br /> [Inventář zařízení](#d)** |

## <a name="h"></a>H

| Období | Description | Další informace |
|--|--|--|
| **Vývojové prostředí pro otevřený Horizont** | Zabezpečená zařízení IoT a ICS spouštějící proprietární a vlastní protokoly nebo protokoly, které se odchylují od všech standardních. K vývoji modulů plug-in, které dekódují síťový provoz na základě definovaných protokolů, použijte sadu SDK pro vývoj a vývojové prostředí pomocí nástroje horizont. Obrana pro služby IoT Services analyzují provoz, aby poskytovaly kompletní monitorování, upozorňování a vytváření sestav.<br /><br />Použít horizont:<br /><br />- **Rozšiřte** viditelnost a řízení bez nutnosti upgradovat Defender pro verze platformy IoT.<br /><br />- **Zabezpečte** proprietární informace vývojem na pracovišti jako externí modul plug-in.<br /><br />- **Lokalizace** textu pro výstrahy, události a parametry protokolu.<br /><br />Podrobnosti vám poskytne zástupce úspěšnosti zákazníka. | **[Podpora protokolů](#p) <br /> <br /> [Lokalizace](#l)** |
| **Vlastní výstraha horizontu** | Vylepšete správu výstrah v podniku tím, že aktivujete vlastní výstrahy pro libovolný protokol (na základě přenosů dat v architektuře Horizon).<br /><br />Tyto výstrahy se dají použít ke sdělování informací:<br /><br />– Informace o detekcích provozu na základě protokolů a základních protokolů v modulu plug-in proprietárního horizontu.<br /><br />– Informace o kombinaci polí protokolu ze všech vrstev protokolu. | **[Podpora protokolu](#p)** |

## <a name="i"></a>I

| Období | Description | Další informace |
|--|--|--|
| **IoT Hub** `(DB)` | Spravovaná služba hostovaná v cloudu, která funguje jako centrální Centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje.  |   |
| **Integrace** | Rozbalte Defender pro možnosti IoT sdílením informací o zařízení s partnerskými systémy. Organizace můžou Přemostit dříve vytvářená řešení zabezpečení, NAC, správy incidentů a správy zařízení a urychlit tak reakce na systém a rychleji zmírnit rizika. | **[Pravidlo předávání](#f)** |
| **Interní podsíť** | Konfigurace podsítí definované v programu Defender pro IoT V některých případech, jako jsou například prostředí, která používají veřejné rozsahy jako interní rozsahy, můžete programu Defender for IoT vyřešit všechny podsítě jako interní podsítě. Podsítě se zobrazují v mapě a v různých Defenderech sestav IoT. | **[Podsítě](#s)** |

## <a name="l"></a>L

| Období | Description | Další informace |
|--|--|--|
| **Zobrazit událost výstrahy** | Řekněte programu Defender, aby IoT schvaloval provoz zjištěný v události výstrahy. | **[Výstraha](#glossary-a) <br /> <br /> [Potvrdit událost výstrahy](#glossary-a) <br /> <br /> [Ztlumení události výstrahy](#m)** |
| **Výukový režim** | Režim použitý v případě, kdy se program Defender pro IoT učí síťové aktivity. Tato aktivita se bude nacházet na vaše síťové základny. Defender pro IoT zůstane v režimu pro předdefinované období po instalaci. Aktivita, která se po uplynutí této doby odchyluje od zjištěné aktivity, aktivuje Defender pro výstrahy IoT. | **[Inteligentní učení IT](#s) <br /> <br /> [Standardní hodnoty](#b)** |
| **Lokalizace** | Lokalizace textu pro výstrahy, události a parametry protokolu pro nesektorové moduly plug-in vyvinuté nástrojem Horizont | **[Vývojové prostředí pro otevřený Horizont](#h)** |

## <a name="m"></a>M


| Období | Description | Další informace |
|--|--|--|
| **Micro agent**`(DB)` | Poskytuje hloubkové možnosti zabezpečení pro zařízení IoT, včetně stav zabezpečení a detekce hrozeb. | |
| **Modul je nevlákenný**`(DB)` | Dvojčata modulů jsou dokumenty JSON, které obsahují informace o stavu modulu, včetně metadat, konfigurací a podmínek. | [Dvojče zařízení](#d) <br /> <br />[Defender-IoT-Micro-agent nevláken](#s) |
| **Ztlumení události výstrahy** | Dejte programu Defender, aby IoT neustále ignoroval činnost se stejnými zařízeními a srovnatelnými přenosy. | **[Výstraha](#glossary-a) <br /> <br /> [Pravidlo vyloučení](#e) <br /> <br /> [Potvrdit událost výstrahy](#glossary-a) <br /> <br /> [Zobrazit událost výstrahy](#l)** |

## <a name="n"></a>N

| Období | Description | Další informace |
|--|--|--|
| **Oznámení** | Informace o změnách sítě nebo nevyřešených vlastnostech zařízení. K dispozici jsou možnosti aktualizace informací o zařízení a síti s zjištěnými novými daty. Reakce na oznámení vylepšuje inventář zařízení, mapu a různé sestavy. K dispozici na konzolách senzorů. | **[Výstraha](#glossary-a) <br /> <br /> [Systémová oznámení](#s)** |

## <a name="o"></a>O

| Období | Description | Další informace |
|--|--|--|
| **Místní Konzola pro správu** | Místní Konzola pro správu poskytuje centralizované zobrazení a správu zařízení a hrozeb, které se ve vaší organizaci zjišťují při nasazeních senzorů IoT. | **[Defender pro platformu IoT](#d) <br /> <br /> [Senzor](#s)** |
| **Provozní upozornění** | Výstrahy týkající se problémů s provozní sítí, jako je například zařízení, u kterého se očekává odpojení od sítě. | **[Výstraha](#glossary-a) <br /> <br /> [Výstraha zabezpečení](#s)** |

## <a name="p"></a>P

| Období | Description | Další informace |
|--|--|--|
| **Purdue vrstva** | Zobrazuje propojení a vzájemné závislosti hlavních komponent typických funkcí ICS na mapě. |  |
| **Podpora protokolu** | Kromě podpory vloženého protokolu můžete zabezpečit zařízení IoT a ICS s využitím vlastních a vlastních protokolů nebo protokolů, které se odchylují od jakéhokoli standardu, pomocí sady SDK pro vývojové prostředí Open. | **[Vývojové prostředí pro otevřený Horizont](#h)** |

## <a name="r"></a>R

| Období | Description | Další informace |
|--|--|--|
| **Oblast** | Logické rozdělení globální organizace do geografických oblastí. Příklady jsou Severní Amerika, západní Evropa a východní Evropa.<br /><br />Severní Amerika mohou mít továrny z různých obchodních jednotek. | **[Přístup ke skupině](#glossary-a) <br /> <br /> [Organizační jednotka](#b) <br /> <br /> [Místní konzola](#o) <br /> <br /> pro správu [Webový server](#s) <br /> <br /> [Zóna](#z)** |
| **Sestavy** | Sestavy odrážejí informace vygenerované výsledky dotazu dolování dat. To zahrnuje výchozí výsledky dolování dat, které jsou k dispozici v zobrazení **sestavy** . Správci a analytici zabezpečení mohou také generovat vlastní dotazy dolování dat a uložit je jako sestavy. Tyto sestavy budou také k dispozici pro uživatele jen pro čtení. | **[Dolování dat](#d)** |
| **Sestava posouzení rizik** | Vytváření sestav hodnocení rizik umožňuje vygenerovat bezpečnostní skóre pro každé síťové zařízení spolu s celkovým skóre zabezpečení sítě. Celkové skóre představuje procento z 100 procent zabezpečení. Tato sestava poskytuje doporučení pro zmírnění rizik, která vám pomůžou vylepšit aktuální skóre zabezpečení. | - |

## <a name="s"></a>S

| Období | Description | Další informace |
|--|--|--|
| **Výstraha zabezpečení** | Výstrahy, které řeší problémy se zabezpečením, například nadměrné pokusy o přihlášení SMB nebo zjištění malwaru. | **[Výstraha](#glossary-a) <br /> <br /> [Provozní upozornění](#o)** |
| **Defender-IoT-Micro-agent nevláken**`(DB)` | V programu Defender-IoT-Micro-Agent se zobrazí všechny informace, které jsou důležité pro zabezpečení zařízení, pro každé konkrétní zařízení ve vašem řešení. | [Dvojče zařízení](#d) <br /> <br />[Modul je nevlákenný](#m)  |
| **Selektivní zjišťování** | Defender pro IoT prověří IT a za provozu a detekuje relevantní informace o zařízeních, jejich atributech, jejich chování a dalších. V některých případech nemusí být některé informace v pasivních síťových analýzách viditelné.<br /><br />V takovém případě můžete pomocí bezpečných a podrobných nástrojů pro zjišťování v programu Defender pro IoT zjistit důležité informace o dříve nedosažitelných zařízeních. | - |
| **Elektrické** | Fyzický nebo virtuální počítač, na kterém je nainstalována platforma Defender for IoT. | **[Místní Konzola pro správu](#o)** |
| **Web** | Umístění, které továrna nebo jiná entita. Lokalita by měla obsahovat zónu nebo několik zón, ve kterých je nainstalován senzor. | **[Zóna](#z)** |
| **Správa lokality** | Možnost místní konzoly pro správu, která umožňuje správu podnikových senzorů. | - |
| **Inteligentní učení IT** | Po dokončení období učení a výukového režimu je program Defender pro IoT možné detekovat neobvykle vysokou úroveň změn standardních hodnot, které jsou výsledkem normální aktivity IT, jako jsou požadavky na DNS a HTTP. Tento provoz může aktivovat zbytečné výstrahy narušení zásad a systémová oznámení. Pokud chcete tyto výstrahy a oznámení snížit, můžete povolit inteligentní učení IT. | **[Výukový režim](#l) <br /> <br /> [Standardní hodnoty](#b)** |
| **Podsítě** | Pokud chcete povolit zaměření na zařízeních, zařízení se automaticky agregují podle podsítě na mapě zařízení. Každá podsíť je prezentována jako jediná entita na mapě, včetně interaktivní sbalení nebo rozbalování možností pro zaměření na podsíť IT a zpět. | **[Mapa zařízení](#d)** |
| **Systémová oznámení** | Oznámení z přegradování místní konzoly pro správu:<br /><br />-Stav připojení senzoru.<br /><br />– Selhání vzdálené zálohy. | **[](#n) <br /> Oznámení <br /> [Výstraha](#glossary-a)** |

## <a name="z"></a>Z

| Období | Description | Další informace |
|--|--|--|
| **Zóna** | Oblast v rámci lokality, ve které je nainstalovaný senzor nebo senzory. | **[Webový server](#s) <br /> <br /> [Organizační jednotka](#b) <br /> <br /> [Oblast](#r)** |
