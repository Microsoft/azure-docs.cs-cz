---
title: Přehled funkcí Nástroje pro modelování hrozeb Microsoft – Azure
description: Informace o všech funkcích dostupných v nástroji pro modelování hrozeb
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 88eea0eb2bceb0f6322381a94d4339cbdfa662c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552113"
---
# <a name="threat-modeling-tool-feature-overview"></a>Přehled funkcí nástroje modelování hrozeb

Nástroj pro modelování hrozeb vám může pomoci s potřebami modelování hrozeb. Základní úvod k nástroji najdete v tématu [Začínáme s nástrojem pro modelování hrozeb](threat-modeling-tool-getting-started.md).

> [!NOTE]
>Nástroj pro modelování hrozeb je často aktualizován, proto často kontrolujte tuto příručku, abyste viděli naše nejnovější funkce a vylepšení.

Chcete-li otevřít prázdnou stránku, vyberte **Vytvořit model**.

![Prázdná stránka](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Chcete-li zobrazit funkce, které jsou v nástroji aktuálně k dispozici, použijte model hrozeb vytvořený naším týmem v příkladu [Začínáme.](threat-modeling-tool-getting-started.md)

![Základní model hrozeb](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigace

Než budeme diskutovat o vestavěných funkcích, podívejme se na hlavní součásti, které se nacházejí v nástroji.

### <a name="menu-items"></a>Položky nabídky

Prostředí je podobné jako u jiných produktů společnosti Microsoft. Podívejme se na položky nabídky nejvyšší úrovně.

![Položky nabídky](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Popisek                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Soubor** | <ul><li>Otevírání, ukládání a zavírání souborů</li><li>Přihlaste se a odhlaste se z účtů OneDrivu.</li><li>Sdílení odkazů (zobrazení a úpravy).</li><li>Zobrazení informací o souboru.</li><li>Použijte novou šablonu u existujících modelů.</li></ul> |
| **Upravit** | Vrátit a znovu provést akce, stejně jako kopírovat, vkládat a odstraňovat. |
| **Zobrazit** | <ul><li>Přepínání mezi **pohledy analýzy** a **návrhu**</li><li>Otevřete zavřená okna (například vzorníky, vlastnosti elementu a zprávy).</li><li>Obnovit výchozí nastavení rozložení.</li></ul> |
| **Diagram** | Přidejte a odstraňte diagramy a procházeněte kartami diagramů. |
| **Sestavy** | Vytvořte sestavy HTML, které chcete sdílet s ostatními. |
| **Nápověda** | Najděte vodítka, která vám pomohou s použitím nástroje. |

Symboly jsou zkratky pro nabídky nejvyšší úrovně:

| Symbol                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Otevřít** | Otevře nový soubor. |
| **Uložit** | Uloží aktuální soubor. |
| **Návrh** | Otevře **návrhové** zobrazení, ve kterém můžete vytvářet modely. |
| **Analýza** | Zobrazuje generované hrozby a jejich vlastnosti. |
| **Přidat diagram** | Přidá nový diagram (podobně jako nové karty v aplikaci Excel). |
| **Odstranit diagram** | Odstraní aktuální diagram. |
| **Kopírovat/vyjmout/vložit** | Kopíruje, vyjme a vkládá prvky. |
| **Vrátit a vrátit znovu** | Znovu se odvádí a znovu provádí akce. |
| **Přiblížení/oddálení** | Přiblíží a oddálí diagram pro lepší zobrazení. |
| **Váš názor** | Otevře fórum MSDN. |

### <a name="canvas"></a>Plátno

Plátno je prostor, kde přetáhnete prvky. Přetažení je nejrychlejší a nejefektivnější způsob vytváření modelů. Můžete také klepnout pravým tlačítkem myši a vybrat položky z nabídky a přidat obecné verze prvků, jak je znázorněno:

#### <a name="drop-the-stencil-on-the-canvas"></a>Drop šablonu na plátně

![Přetažení plátna](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Výběr vzorníku

![Vlastnosti prvku](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Vzorníky

Na základě vybrané šablony najdete všechny vzorníky, které jsou k dispozici. Pokud nemůžete najít správné prvky, použijte jinou šablonu. Nebo můžete upravit šablonu tak, aby vyhovovala vašim potřebám. Obecně můžete najít kombinaci kategorií, jako jsou tyto:

| Název vzorníku                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Proces** | Aplikace, pluginy prohlížeče, vlákna, virtuální počítače |
| **Externí interagátor** | Poskytovatelé ověřování, prohlížeče, uživatelé, webové aplikace |
| **Úložiště dat** | Cache, úložiště, konfigurační soubory, databáze, registr |
| **Tok dat** | Binární, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, pojmenovaný kanál, RPC/DCOM, SMB, UDP |
| **Čára důvěryhodnosti/hranice ohraničení** | Podnikové sítě, internet, stroj, pískoviště, režim user/kernel |

### <a name="notesmessages"></a>Poznámky/zprávy

| Komponenta                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Zprávy** | Interní logika nástroje, která upozorňuje uživatele, kdykoli dojde k chybě, jako je například žádné toky dat mezi prvky. |
| **Poznámky** | Ruční poznámky jsou přidány do souboru inženýrskými týmy v průběhu procesu návrhu a revize. |

### <a name="element-properties"></a>Vlastnosti prvku

Vlastnosti prvku se liší podle vybraných prvků. Kromě hranic důvěryhodnosti obsahují všechny ostatní prvky tři obecné výběry:

| Vlastnost elementu                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Název** | Užitečné pro pojmenování procesů, obchodů, mezihera a toků tak, aby byly snadno rozpoznatelné. |
| **Mimo rozsah** | Pokud je tato možnost vybrána, prvek je odebrán z matice generování hrozeb (nedoporučuje se). |
| **Důvod nedoslýchavosti** | Pole Zarovnání, které uživatelům umožní zjistit, proč byla vybrána možnost Mimo rozsah. |

Vlastnosti jsou změněny v rámci každé kategorie prvku. Vyberte každý prvek a zkontrolujte dostupné možnosti. Nebo můžete otevřít šablonu a dozvědět se více. Podívejme se na funkce.

## <a name="welcome-screen"></a>Obrazovka Vítejte

Když otevřete aplikaci, zobrazí se **úvodní** obrazovka.

### <a name="open-a-model"></a>Otevření modelu

Najeďte přes **Otevřít model a** zobrazte dvě možnosti: Otevřít z tohoto **počítače** a Otevřít **z OneDrivu**. První možnost otevře obrazovku **Otevřít soubor.** Druhá možnost vás provede procesem přihlášení pro OneDrive. Po úspěšném ověření můžete vybrat složky a soubory.

![Otevřít model](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Otevření z počítače nebo OneDrivu](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Zpětná vazba, návrhy a problémy

Když vyberete **zpětnou vazbu, návrhy a problémy**, přejdete na Fórum MSDN pro nástroje SDL. Můžete si přečíst, co ostatní lidé říkají o nástroji, včetně řešení a nových nápadů.

![Váš názor](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Návrhové zobrazení

Když otevřete nebo vytvoříte nový model, otevře se **návrhové** zobrazení.

### <a name="add-elements"></a>Přidání prvků

Prvky můžete do mřížky přidat dvěma způsoby:

- **Přetažení**: Přetáhněte požadovaný prvek do mřížky. Potom použijte vlastnosti prvku poskytnout další informace.
- **Klikněte pravým tlačítkem myši**: Klikněte pravým tlačítkem myši na libovolné místo v mřížce a v rozevírací nabídce vyberte položky. Na obrazovce se zobrazí obecná reprezentace vybraného prvku.

### <a name="connect-elements"></a>Připojení prvků

Prvky můžete propojit dvěma způsoby:

- **Přetažení**: Přetáhněte požadovaný tok dat do mřížky a připojte oba konce k příslušným prvkům.
- **Klepněte na + Shift**: Klikněte na první prvek (odesílání dat), stiskněte a podržte klávesu Shift a pak vyberte druhý prvek (příjem dat). Klepněte pravým tlačítkem myši a vyberte **připojit**. Pokud používáte obousměrný tok dat, pořadí není tak důležité.

### <a name="properties"></a>Vlastnosti

 Chcete-li zobrazit vlastnosti, které lze upravit na vzorníky, vyberte vzorník a informace naplní odpovídajícím způsobem. Následující příklad ukazuje před a po **databázi** vzorník je přetažen do diagramu:

#### <a name="before"></a>Před

![Před](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Po

![Po](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Zprávy

Pokud vytvoříte model hrozeb a zapomenete připojit toky dat k prvkům, dostanete oznámení. Zprávu můžete ignorovat nebo můžete problém vyřešit podle pokynů. 

![Zprávy](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Poznámky

Pokud chcete do diagramu přidat poznámky, přepněte z karty **Zprávy** na kartu **Poznámky.**

## <a name="analysis-view"></a>Pohled analýzy

Po vytvoření diagramu vyberte symbol **analýzy** (lupa) na panelu nástrojů zástupců a přepněte do **pohledu Analýza.**

![Pohled analýzy](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Výběr generované hrozby

Když vyberete hrozbu, můžete použít tři odlišné funkce:

| Funkce                               | Informace      |
| --------------------------------------- | ------------ |
| **Indikátor čtení** | <p>Hrozba je označena jako přečtená, což vám pomůže sledovat položky, které jste zkontrolovali.</p><p>![Indikátor čtení/nepřečteného](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Zaměření interakce** | <p>Interakce v diagramu, který patří k hrozbě je zvýrazněna.</p><p>![Zaměření interakce](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Vlastnosti hrozby** | <p>Další informace o hrozbě se zobrazí v okně **Vlastnosti hrozby.**</p><p>![Vlastnosti hrozby](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Změna priority

Můžete změnit úroveň priority každé generované hrozby. Různé barvy usnadňují identifikaci hrozeb s vysokou, střední a nízkou prioritou.

![Změna priority](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Upravitelná pole vlastností hrozby

Jak je vidět na předchozím obrázku, můžete změnit informace generované nástrojem. Do určitých polí můžete také přidat informace, například zarovnání. Tato pole jsou generována šablonou. Pokud potřebujete více informací pro každou hrozbu, můžete provést změny.

![Vlastnosti hrozby](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Sestavy

Po dokončení změny priorit a aktualizaci stavu každé generované hrozby můžete soubor uložit nebo vytisknout sestavu. Přejděte na **Vytvořit** > **úplnou sestavu**. Pojmenujte sestavu a měli byste vidět něco podobného následujícímu obrázku:

![Sestava](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Další kroky

- Své dotazy, připomínky tmtextsupport@microsoft.coma připomínky zasílejte společnosti . **[Stáhněte si](https://aka.ms/threatmodelingtool)** nástroj pro modelování hrozeb a můžete začít.
- Pokud chcete přispět šablonou pro komunitu, přejděte na naši stránku [GitHub.](https://github.com/Microsoft/threat-modeling-templates)