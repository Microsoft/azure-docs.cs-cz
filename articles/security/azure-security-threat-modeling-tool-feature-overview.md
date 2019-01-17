---
title: Microsoft Threat modelování nástroj – Azure | Dokumentace Microsoftu
description: Další informace o funkcích dostupných v nástroji pro modelování hrozeb
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 8bbc13aa77c50c6ae02ecc67869035e7ec85effa
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359336"
---
# <a name="threat-modeling-tool-feature-overview"></a>Přehled funkcí nástroj pro modelování hrozeb

Nástroj pro modelování hrozeb můžete s vaší hrozeb modelování potřebám. Základní informace o nástroji, naleznete v tématu [začít pracovat s nástroj pro modelování hrozeb](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>Nástroj pro modelování hrozeb se často aktualizuje, tak tento často najdete v příručce pro naše nejnovější funkce a vylepšení.

Chcete-li otevřít prázdné stránky, vyberte **vytvoření modelu A**.

![Prázdná stránka](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Pokud chcete zobrazit funkce, které jsou aktuálně dostupné v nástroji, použijte model hrozeb, které jsou vytvořené pro tým v [Začínáme](./azure-security-threat-modeling-tool-getting-started.md) příklad.

![Základní rizik](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigace

Než si probereme integrované funkce, Pojďme se podívat na hlavní součásti, které jsou součástí nástroje.

### <a name="menu-items"></a>Položky nabídky

Prostředí funguje podobně jako ostatní produkty Microsoftu. Pojďme se podívat na položky nabídek nejvyšší úrovně.

![Položky nabídky](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Štítek                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **File** | <ul><li>Otevřete, uložte a zavřete soubory</li><li>Přihlaste se a odhlásit se z účtů Onedrivu.</li><li>Sdílení odkazů (prohlížení a úpravy).</li><li>Zobrazení informací o souboru.</li><li>Použijte novou šablonu pro stávající modely.</li></ul> |
| **Upravit** | Vrátit zpět a opakovat akce, jakož i kopírování, vložení a odstranění. |
| **Zobrazení** | <ul><li>Přepínání mezi **analýzy** a **návrhu** zobrazení.</li><li>Otevřít uzavřené windows (například vzorníky vlastností elementů a zprávy).</li><li>Obnovíte výchozí nastavení rozložení.</li></ul> |
| **Diagram** | Přidání a odstranění diagramy a přechod na kartu stránky diagramů. |
| **Sestavy** | Vytváření sestav HTML sdílet s ostatními. |
| **Pomoc** | Najdete, kteří vám pomůžou s používáním nástroje. |

Symboly jsou zástupci pro nabídek nejvyšší úrovně:

| Symbol                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Otevřít** | Otevře se nový soubor. |
| **Uložit** | Uloží aktuální soubor. |
| **návrh** | Otevře **návrhu** zobrazení, kde můžete vytvářet modely. |
| **Analýza** | Ukazuje generované hrozeb a jejich vlastnosti. |
| **Přidání diagramu** | Přidá nový diagram (podobně jako nových karet v Excelu). |
| **Odstranit diagramu** | Odstraní aktuální diagram. |
| **Vyjmutí/kopírování/vkládání** | Zkopíruje, vyjme a vloží prvky. |
| **Zpět/znovu** | Vrátí zpět a znovu provede akce. |
| **Přiblížení / oddálení** | Zvětší zobrazení do a z diagramu pro lepší zobrazení. |
| **Váš názor** | Otevře se fórum na webu MSDN. |

### <a name="canvas"></a>Plátno

Na plátně je místo, kde můžete přetáhnout myší elementy. Přetažení je nejúčinnější a nejrychlejší způsob, jak sestavovat modely. Můžete také klikněte pravým tlačítkem a vyberte položky v nabídce Přidat obecné verze prvků, jak je znázorněno:

#### <a name="drop-the-stencil-on-the-canvas"></a>Přetáhněte na plátno vzorníku

![Přetažení plátna](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Vyberte vzorníku

![Element vlastnosti](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Vzorníků

Založený na šabloně, kterou vyberete, můžete najít všechny k dispozici pro použití vzorníků. Pokud nemůžete najít správné elementy, použijte jiné šablony. Nebo můžete upravit šablonu, která bude vyhovovat vašim potřebám. Obecně platí můžete najít kombinaci kategorie, jako jsou tyto:

| Název šablony hloubky                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Proces** | Aplikace, moduly plug-in prohlížeče, vlákna, virtuální počítače |
| **Externí interakce** | Zprostředkovatelé ověřování, prohlížeče, uživatelé, webové aplikace |
| **Úložiště dat** | Mezipaměť, úložiště, konfigurační soubory, databáze, registru |
| **Tok dat** | Binární soubor, ALPC, protokolu HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, pojmenovaného kanálu, RPC/DCOM, protokol SMB, UDP |
| **Ohraničení čáry nebo hranice vztahu důvěryhodnosti** | Podnikové sítě, internet, počítače, izolovaného prostoru, režimu uživatele/jádra |

### <a name="notesmessages"></a>Poznámky k/zprávy

| Komponenta                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Zprávy** | Interní nástroj pro logiku, která upozorňuje uživatele pokaždé, když dojde k chybě, jako je například žádná data proudí mezi prvky. |
| **Poznámky** | Ruční poznámky přidá do souboru technické týmy v rámci návrhu a proces kontroly. |

### <a name="element-properties"></a>Element vlastnosti

Element vlastnosti se liší podle prvků, které jste vybrali. Kromě hranicemi vztahů důvěryhodnosti všechny ostatní prvky obsahovat tři obecné možnosti:

| Vlastnost elementu                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Název** | Užitečné pro vytváření názvů procesů, úložiště, interactors a toky, tak, aby se snadno rozpoznat. |
| **Mimo rozsah** | Pokud je zaškrtnuto, elementu je vyřazen ze matice generování před internetovými útoky (nedoporučuje se). |
| **Důvod mimo rozsah** | Byl vybrán odůvodnění pole, abyste uživatelům sdělili důvod, proč mimo rozsah. |

Vlastnosti jsou změněny v rámci kategorie každého prvku. Vyberte každý prvek na zkontrolujte dostupné možnosti. Nebo můžete otevřít šablonu, kterou chcete získat další informace. Pojďme se podívat na funkce.

## <a name="welcome-screen"></a>Úvodní obrazovka

Když aplikaci spustíte, zobrazí **úvodní** obrazovky.

### <a name="open-a-model"></a>Otevření modelu

Najeďte myší na **otevřený Model A** zobrazí dvě možnosti: **Otevřít z tohoto počítače** a **otevřít z Onedrivu**. První možnost otevře **otevřít soubor** obrazovky. Druhá možnost vás provede proces přihlašování k Onedrivu. Po úspěšném ověření můžete vybrat soubory a složky.

![Otevřený model](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Otevřít z počítače nebo OneDrive](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Zpětnou vazbu, nabízení návrhů a problémů

Když vyberete **zpětnou vazbu, návrhy a problémy**, můžete přejít na fóra MSDN pro nástroje SDL. Si můžete přečíst, co jiní lidé říkají o nástroji, včetně alternativních řešení a nové nápady.

![Váš názor](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Návrhové zobrazení

Když otevřete nebo vytvořte nový model **návrhu** zobrazení se otevře.

### <a name="add-elements"></a>Přidání prvků

Přidat prvky mřížky dvěma způsoby:

- **Přetáhnout myší**: Požadovaný element přetáhněte do mřížky. Pak pomocí vlastnosti prvku poskytují další informace.
- **Klikněte pravým tlačítkem na**: Klikněte pravým tlačítkem na libovolné místo v tabulce a vybrat položky z rozevírací nabídky. Obecná reprezentace elementu, který vyberete, se zobrazí na obrazovce.

### <a name="connect-elements"></a>Připojte se elementy

Můžete připojit prvky dvěma způsoby:

- **Přetáhnout myší**: Přetáhněte požadovaný tok dat do mřížky a obou koncích připojení do příslušných elementů.
- **Klikněte na + Shift**: Klikněte na první prvek (odesílání dat), stiskněte a podržte klávesu Shift a vyberte druhý prvek (přijímání dat). Klikněte pravým tlačítkem a vyberte **připojit**. Pokud používáte obousměrný datový tok, pořadí není důležité.

### <a name="properties"></a>Vlastnosti

 Pokud chcete zobrazit vlastnosti, které lze upravit na vzorníků, vyberte vzorníku a informace o naplní odpovídajícím způsobem. Následující příklad ukazuje před a po **databáze** vzorníku je přetáhnout do diagramu:

#### <a name="before"></a>Před

![Před](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Po

![Po](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Zprávy

Pokud vytvoříte model hrozeb a nezapomeňte znovu připojit datové toky na elementy, dostanete oznámení. Zprávy můžete ignorovat, nebo můžete postupovat podle pokynů k vyřešení tohoto problému. 

![Zprávy](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Poznámky

Chcete-li přidat poznámky k diagramu, přepněte z **zprávy** záložku **poznámky** kartu.

## <a name="analysis-view"></a>Zobrazení analýzy

Po vytvoření diagramu, vyberte **analýzy** symbolu (Lupa) na panelu nástrojů klávesové zkratky pro přepnutí do **analýzy** zobrazení.

![Zobrazení analýzy](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Výběr generované před internetovými útoky

Když vyberete hrozbu, můžete použít tři různé funkce:

| Funkce                               | Informace      |
| --------------------------------------- | ------------ |
| **Ukazatel čtení** | <p>Hrozby je označena jako čtení, což pomáhá udržovat přehled o položky, které jste kontrolovali.</p><p>![Čtení/nepřečtené indikátor](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interakce fokus** | <p>Interakce v diagramu, který patří k ohrožení je zvýrazněn.</p><p>![Interakce fokus](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Vlastnosti před internetovými útoky** | <p>Další informace o dané hrozbě se zobrazí v **hrozeb vlastnosti** okna.</p><p>![Vlastnosti před internetovými útoky](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Změna priority

Můžete změnit prioritu každé vygenerované hrozeb. Různé barvy usnadňují identifikaci vysoce, středně- a hrozby s nízkou prioritou.

![Změna priority](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Upravitelné pole vlastnosti před internetovými útoky

Jak je vidět na předchozím obrázku, můžete změnit informace generovaný nástrojem. Můžete také přidat informace do určitá pole, jako je například zarovnání do bloku. Tato pole jsou generovány pomocí šablony. Pokud potřebujete další informace na jednotlivé hrozby, můžete provést změny.

![Vlastnosti před internetovými útoky](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Reports

Po změně priority a aktualizuje se stav každé vygenerované hrozeb můžete soubor uložit nebo vytisknout sestavu. Přejděte na **sestavy** > **vytvořit úplnou sestavu**. Název sestavy a by měl vypadat podobně jako na následujícím obrázku:

![Sestava](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Další postup

* Pokud chcete přispívat šablonu pro komunitu, přejděte na naše [Githubu](https://github.com/Microsoft/threat-modeling-templates) stránky. 
* Chcete-li začít pracovat s nástroji, přejděte [Stáhnout](https://aka.ms/tmtpreview) stránky.
