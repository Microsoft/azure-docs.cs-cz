---
title: Přehled funkcí Microsoft Threat Modeling Tool – Azure
description: Přečtěte si o všech funkcích, které jsou k dispozici v Threat Modeling Tool, jako je například zobrazení analýzy a sestavy.
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 65a88f309602462e77336181316c0d5bf19a8a1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90980674"
---
# <a name="threat-modeling-tool-feature-overview"></a>Přehled funkcí Threat Modeling Tool

Threat Modeling Tool vám může pomáhat s potřebou modelování hrozeb. Základní informace o nástroji najdete v tématu [Začínáme s Threat Modeling Tool](threat-modeling-tool-getting-started.md).

> [!NOTE]
>Threat Modeling Tool se často aktualizují, proto si přečtěte v této příručce naše nejnovější funkce a vylepšení.

Chcete-li otevřít prázdnou stránku, vyberte možnost **vytvořit model**.

![Prázdná stránka](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Chcete-li zobrazit funkce, které jsou aktuálně k dispozici v nástroji, použijte model hrozby vytvořený naším týmem v příkladu [Začínáme](threat-modeling-tool-getting-started.md) .

![Model Basic Threat](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigace

Než se podíváme na předdefinované funkce, Podívejme se na hlavní komponenty, které jsme našli v nástroji.

### <a name="menu-items"></a>Položky nabídky

Prostředí je podobné ostatním produktům společnosti Microsoft. Pojďme si prohlédnout položky nabídky nejvyšší úrovně.

![Položky nabídky](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Popisek                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Soubor** | <ul><li>Otevření, uložení a zavření souborů</li><li>Přihlaste se a odhlaste se z účtů OneDrive.</li><li>Sdílet odkazy (Zobrazit a upravit).</li><li>Zobrazit informace o souboru.</li><li>Použijte novou šablonu pro existující modely.</li></ul> |
| **Upravit** | Akce zpět a znovu a také kopírovat, vložit a odstranit. |
| **Zobrazení** | <ul><li>Přepínání mezi zobrazeními pro **analýzu** a **Návrh**</li><li>Otevřete uzavřená okna (například vzorníky, vlastnosti prvku a zprávy).</li><li>Obnovit rozložení na výchozí nastavení.</li></ul> |
| **Diagram** | Přidejte a odstraňte diagramy a přejděte na karty diagramů. |
| **Sestavy** | Vytváření sestav HTML pro sdílení s ostatními. |
| **Nápověda** | Najděte si příručky, které vám pomůžou nástroj používat. |

Symboly jsou zkratky pro nabídky nejvyšší úrovně:

| Symbol                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Otevřít** | Otevře nový soubor. |
| **Uložit** | Uloží aktuální soubor. |
| **Návrh** | Otevře zobrazení **Návrh** , kde můžete vytvářet modely. |
| **Analýza** | Zobrazuje vygenerované hrozby a jejich vlastnosti. |
| **Přidat diagram** | Přidá nový diagram (podobně jako nové karty v Excelu). |
| **Odstranit diagram** | Odstraní aktuální diagram. |
| **Kopírování, vyjmutí a vložení** | Kopie, řezy a minulé prvky. |
| **Vrátit zpět/znovu** | Vrátí akce a provede akci znovu. |
| **Přiblížení/oddálení** | Přiblížení a oddálení diagramu pro lepší zobrazení. |
| **Zpětná vazba** | Otevře Fórum MSDN. |

### <a name="canvas"></a>Plátno

Plátno je místo, kam přetahujete prvky. Přetahování je nejrychlejší a nejúčinnější způsob sestavování modelů. Můžete také kliknout pravým tlačítkem a vybrat položky z nabídky, chcete-li přidat obecné verze prvků, jak je znázorněno níže:

#### <a name="drop-the-stencil-on-the-canvas"></a>Vyřazení vzorníku na plátně

![Přetažení plátna](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Výběr vzorníku

![Vlastnosti elementu](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Vzorníky

Na základě vámi vybrané šablony můžete najít všechny vzorníky, které jsou k dispozici pro použití. Pokud nemůžete najít správné prvky, použijte jinou šablonu. Případně můžete šablonu upravit tak, aby vyhovovala vašim potřebám. Obecně můžete najít kombinaci kategorií, jako jsou tyto:

| Název vzorníku                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Proces** | Aplikace, moduly plug-in prohlížeče, vlákna, virtuální počítače |
| **Externí interakce** | Poskytovatelé ověřování, prohlížeče, uživatelé, webové aplikace |
| **Úložiště dat** | Mezipaměť, úložiště, konfigurační soubory, databáze, Registry |
| **Tok dat** | Binární, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, pojmenovaný kanál, RPC/DCOM, SMB, UDP |
| **Čára vztahu důvěryhodnosti/hranice ohraničení** | Podnikové sítě, Internet, počítač, izolovaný režim, uživatel nebo režim jádra |

### <a name="notesmessages"></a>Poznámky a zprávy

| Součást                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Zprávy** | Interní logika nástrojů, která upozorní uživatele vždy, když dojde k chybě, například bez toků dat mezi prvky. |
| **Poznámky** | V rámci procesu návrhu a kontroly jsou do souboru přidány ruční poznámky. |

### <a name="element-properties"></a>Vlastnosti elementu

Vlastnosti elementu se liší podle vybraných prvků. Kromě hranic vztahů důvěryhodnosti všechny ostatní prvky obsahují tři obecné výběry:

| Vlastnost elementu                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Název** | Užitečné pro pojmenovávání procesů, obchodů, interakcí a toků, aby byly snadno rozpoznané. |
| **Mimo rozsah** | Pokud je tato možnost vybrána, bude prvek vybraný z matice generace hrozeb (nedoporučuje se). |
| **Důvod mimo rozsah** | Pole odůvodnění, aby uživatelé věděli, proč je vybraný rozsah. |

Vlastnosti se mění v rámci každé kategorie elementu. Vyberte jednotlivé prvky a zkontrolujte dostupné možnosti. Případně můžete otevřít šablonu a získat další informace. Pojďme se podívat na funkce.

## <a name="welcome-screen"></a>Obrazovka Vítejte

Když aplikaci otevřete, zobrazí se **uvítací** obrazovka.

### <a name="open-a-model"></a>Otevření modelu

Najeďte myší na **Otevřít model** a odhalte dvě možnosti: **otevřít z tohoto počítače** a **otevřít z OneDrivu**. První možnost otevře obrazovku **otevřít soubor** . Druhá možnost vás provede procesem přihlášení pro OneDrive. Po úspěšném ověření můžete vybrat složky a soubory.

![Otevřít model](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Otevřít z počítače nebo OneDrivu](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Názory, návrhy a problémy

Když vyberete **zpětnou vazbu, návrhy a problémy**, přejdete na Fórum MSDN pro nástroje SDL. Můžete si přečíst, co o nástroji říká další lidé, včetně řešení a nových nápadů.

![Snímek obrazovky zobrazující tlačítko s zpětnou vazbou textu, návrhy a problémy.](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>zobrazení Návrh

Když otevřete nebo vytvoříte nový model, otevře se **návrhové** zobrazení.

### <a name="add-elements"></a>Přidání prvků

Prvky mřížky můžete přidat dvěma způsoby:

- **Přetažení: přetáhněte**požadovaný prvek do mřížky. Pak použijte vlastnosti prvku k poskytnutí dalších informací.
- Klikněte **pravým tlačítkem**myši na libovolné místo v mřížce a vyberte položku z rozevírací nabídky. Obecná reprezentace prvku, který vyberete, se zobrazí na obrazovce.

### <a name="connect-elements"></a>Propojit prvky

Prvky lze spojit dvěma způsoby:

- **Přetažení**: přetáhněte požadovaný tok dat do mřížky a oba elementy end připojte k odpovídajícím prvkům.
- **Klikněte na + Shift**: klikněte na první prvek (odesílá data), stiskněte a podržte klávesu SHIFT a potom vyberte druhý prvek (příjem dat). Klikněte pravým tlačítkem a vyberte **připojit**. Pokud používáte obousměrný tok dat, pořadí není důležité.

### <a name="properties"></a>Vlastnosti

 Chcete-li zobrazit vlastnosti, které lze upravovat ve vzornících, vyberte Vzorník a příslušné informace naplní odpovídajícím způsobem. Následující příklad ukazuje, že před a po přetažení vzorníku **databáze** do diagramu:

#### <a name="before"></a>Před

![Před](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Po

![Po](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Zprávy

Pokud vytvoříte model hrozeb a zapomenete připojit toky dat k prvkům, dostanete oznámení. Tuto zprávu můžete ignorovat, nebo můžete podle pokynů tento problém vyřešit. 

![Snímek obrazovky ukazuje konektor modelu hrozeb, který není připojený k elementům, a zprávu, že tento problém způsobuje.](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Poznámky

Chcete-li přidat poznámky do diagramu, přepněte z karty **zprávy** na kartu **poznámky** .

## <a name="analysis-view"></a>Zobrazení analýzy

Po sestavení diagramu vyberte symbol **analýzy** (Lupa) na panelu nástrojů zástupci a přepněte se do zobrazení **analýzy** .

![Zobrazení analýzy](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Výběr vygenerované hrozby

Když vyberete hrozbu, můžete použít tři různé funkce:

| Příznak                               | Informace      |
| --------------------------------------- | ------------ |
| **Přečíst ukazatel** | <p>Hrozba je označena jako přečtená, která pomáhá sledovat položky, které jste zkontrolovali.</p><p>![Indikátor čtení a nepřečtené](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Fokus interakce** | <p>Je zvýrazněna interakce v diagramu, která patří k hrozbě.</p><p>![Fokus interakce](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Vlastnosti hrozby** | <p>Další informace o hrozbě se zobrazí v okně **vlastnosti hrozby** .</p><p>![Vlastnosti hrozby](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Změna priority

Úroveň priority každé vygenerované hrozby můžete změnit. Různé barvy usnadňují identifikaci vysoce, střední a nízké priority hrozeb.

![Změna priority](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Vlastnosti hrozby – upravitelné pole

Jak je vidět na předchozím obrázku, můžete změnit informace vygenerované nástrojem. Můžete také přidat informace do určitých polí, jako je například odůvodnění. Tato pole jsou generována šablonou. Pokud pro každou hrozbu potřebujete další informace, můžete provést úpravy.

![Vlastnosti hrozby](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Sestavy

Až dokončíte měnící se priority a aktualizujete stav každé vygenerované hrozby, můžete soubor uložit nebo vytisknout sestavu. Přejít na **sestavu**  >  **vytvořit úplnou sestavu**. Pojmenujte sestavu a měli byste vidět něco podobného jako na následujícím obrázku:

![Snímek obrazovky ukazuje ukázkovou sestavu modelování hrozeb, včetně souhrnu, diagramů a dalších informací.](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Další kroky

- Pošlete své dotazy, komentáře a obavy do tmtextsupport@microsoft.com . Začněte **[stažením](https://aka.ms/threatmodelingtool)** Threat Modeling Tool.
- Pokud chcete přispívat šablonu pro komunitu, podívejte se na naši stránku [GitHub](https://github.com/Microsoft/threat-modeling-templates) .