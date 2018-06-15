---
title: Microsoft Threat modelování nástroj – Azure | Microsoft Docs
description: Další informace o všech funkcí, které jsou k dispozici v nástroji pro modelování hrozeb
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
ms.openlocfilehash: 76e49fc0e680acbc2b7b7c62b69fbf72d6690acf
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2017
ms.locfileid: "26754741"
---
# <a name="threat-modeling-tool-feature-overview"></a>Přehled funkcí nástroje modelování hrozeb

Nástroj modelování hrozeb vám může pomoct s vaší threat modelování potřebám. Základní Úvod k nástroji naleznete v tématu [začít pracovat s nástrojem modelování hrozeb](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>Nástroj modelování hrozeb se často aktualizuje, proto tento často najdete v příručce pro naše nejnovější funkce a vylepšení.

Otevření prázdné stránky, vyberte **vytvoření modelu A**.

![Prázdná stránka](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Pokud chcete zobrazit funkce, které jsou aktuálně k dispozici v nástroji, použijte model hrozeb, které jsou vytvořené náš tým v [Začínáme](./azure-security-threat-modeling-tool-getting-started.md) příklad.

![Model základní hrozeb](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigace

Před probereme integrované funkce, budeme zkontrolujte hlavními součástmi najít v nástroji.

### <a name="menu-items"></a>Položky nabídky

Prostředí je podobná další produkty společnosti Microsoft. Pojďme si prohlédněte položky, nabídek nejvyšší úrovně.

![Položky nabídky](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Štítek                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **File** | <ul><li>Otevřít, uložte a zavřete soubory</li><li>Přihlaste se a odhlásit z Onedrivu účty.</li><li>Sdílet odkazy (zobrazení a úpravy).</li><li>Zobrazení informací o souboru.</li><li>Použijte novou šablonu pro stávající modely.</li></ul> |
| **Upravit** | Vrátit zpět a znovu proveďte akce, jakož i kopírování, vložení a odstraňte. |
| **Zobrazení** | <ul><li>Přepínání mezi **Analysis** a **návrhu** zobrazení.</li><li>Otevřete uzavřené windows (například vzorníky, vlastností elementů a zprávy).</li><li>Obnovíte výchozí nastavení rozložení.</li></ul> |
| **Diagram** | Přidat a odstranit diagramy a přechod na kartu diagramů. |
| **Sestavy** | Vytváření sestav HTML sdílet s ostatními uživateli. |
| **Pomoc** | Najít příručky můžete použít nástroj. |

Symboly jsou zkratky pro nejvyšší úrovně nabídky:

| Symbol                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Otevřete** | Otevře se nový soubor. |
| **Uložit** | Uloží aktuální soubor. |
| **Návrh** | Otevře se **návrhu** zobrazení, kde můžete vytvořit modely. |
| **Analýza** | Ukazuje generuje hrozby a jejich vlastnosti. |
| **Přidat diagram** | Přidá nový diagram (podobně jako nové karty v aplikaci Excel). |
| **Odstranit diagram** | Odstraní aktuální diagram. |
| **Vyjmutí/kopírování/vkládání** | Zkopíruje, vyjme a vloží elementy. |
| **Vrátit/opakovat** | Vrátí zpět a znovu provede akce. |
| **Přiblížení / oddálení** | Zvětší směřující diagram pro lepší zobrazení. |
| **Váš názor** | Otevře na fóru MSDN. |

### <a name="canvas"></a>Plátno

Na plátně je místo, kde můžete přetáhnout myší elementy. Přetažení je nejrychlejší a nejúčinnější způsob, jak vytvářet modely. Můžete také klikněte pravým tlačítkem a vyberte položky v nabídce Přidat obecné verze elementů, jak je znázorněno:

#### <a name="drop-the-stencil-on-the-canvas"></a>Vyřaďte vzorníku na plátno

![Vyřaďte plátno](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Vyberte vzorník

![Vlastnosti elementů](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Vzorníky

Na základě šablony, kterou vyberete, můžete najít všechny vzorníky k dispozici pro použití. Pokud nemůžete najít správné elementy, použijte jinou šablonu. Nebo můžete upravit šablonu podle vašich potřeb. Obecně platí můžete najít kombinaci kategorie, jako jsou tyto:

| Název vzorníku                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Proces** | Aplikace, moduly plug-in prohlížeče, vláken, virtuální počítače |
| **Externí interakce** | Zprostředkovatelé ověřování, prohlížečů, uživatelé, webových aplikací |
| **Úložiště dat** | Mezipaměť, úložiště, konfigurační soubory, databáze, registru |
| **Tok dat** | Binární ALPC, HTTP, HTTPS, TLS/SSL, IOCTL, IPSec, pojmenovaný kanál, RPC/DCOM, protokol SMB, UDP |
| **Vztah důvěryhodnosti hranic čáry či ohraničení** | Podnikové sítě, internet, počítače, izolovaného prostoru, režimu uživatele nebo jádra |

### <a name="notesmessages"></a>Poznámky a zprávy

| Komponenta                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Zprávy** | Interní nástroj pro logiku, která upozorní uživatele, vždy, když dojde k chybě, jako je například žádná data proudí mezi elementy. |
| **Poznámky k** | Ruční poznámky se přidají do souboru týmy technického návrhu a proces zkontrolovat. |

### <a name="element-properties"></a>Vlastnosti elementů

Vlastnosti elementů se liší podle prvky, které vyberete. Kromě hranice vztahů důvěryhodnosti všechny ostatní elementy obsahovat tři obecné možnosti:

| Vlastnost element                               | Podrobnosti      |
| --------------------------------------- | ------------ |
| **Název** | Užitečné u názvů běžných procesů, úložiště, interactors a toky tak, aby se snadno rozpoznat. |
| **Mimo rozsah** | Pokud vyberete, je element dostala mimo matici hrozbu generace (nedoporučuje se). |
| **Důvod mimo rozsah** | Při zarovnání do bloku pole umožníte uživatelům vědět, proč mimo rozsah byla vybrána. |

Vlastnosti jsou změnit v rámci každé kategorie elementu. Vyberte každý prvek zkontrolovat dostupné možnosti. Nebo můžete otevřít šablonu, kterou chcete získat další informace. Pojďme si funkce.

## <a name="welcome-screen"></a>Úvodní obrazovka

Při otevření aplikace se zobrazí **úvodní** obrazovky.

### <a name="open-a-model"></a>Otevřete model

Pozastavte ukazatel myši nad **otevřete modelu A** na nich dvě možnosti: **otevřete z tento počítač** a **OneDrive otevřete z**. První možnost otevře **otevřít soubor** obrazovky. Druhá možnost vás provede proces přihlášení pro OneDrive. Po úspěšném ověření můžete vybrat soubory a složky.

![Otevřete modelu](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Otevřete v počítači nebo OneDrive](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Zpětná vazba, návrhy a problémy

Když vyberete **zpětnou vazbu, návrhy a problémy**, můžete přejít na fóru MSDN nástroje SDL. Může číst jiní lidé názory týkající se nástroje, včetně řešení a nových nápadů.

![Váš názor](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Návrhové zobrazení

Při otevírání nebo vytvořit nový model, **návrhu** zobrazení otevře.

### <a name="add-elements"></a>Přidat elementy

Elementy lze přidat na mřížky dvěma způsoby:

- **Přetáhnout myší**: přetáhněte požadované elementu k mřížce. Pak pomocí vlastností element můžete poskytnout dodatečné informace.
- **Klikněte pravým tlačítkem na**: klepněte pravým tlačítkem myši na mřížky a vyberte položky z rozevírací nabídky. Obecná reprezentace elementu, který vyberete, zobrazí se na obrazovce.

### <a name="connect-elements"></a>Prvky propojit

Můžete připojit elementy dvěma způsoby:

- **Přetáhnout myší**: Přetáhněte požadovaného toku dat do mřížky a připojte se k příslušné elementy oba elementy end.
- **Klikněte na tlačítko + Shift**: klikněte na první prvek (odesílání dat), stiskněte a podržte klávesu Shift a vyberte druhý prvkem (přijetí dat). Klikněte pravým tlačítkem a vyberte **Connect**. Pokud používáte obousměrný datový tok, není pořadí jako důležité.

### <a name="properties"></a>Vlastnosti

 Chcete-li zobrazit vlastnosti, které lze upravit v vzornících, vyberte vzorník a informace o naplní odpovídajícím způsobem. Následující příklad ukazuje před a po **databáze** vzorníku je přetáhli diagramu:

#### <a name="before"></a>Před

![Před](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Po

![Po](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Zprávy

Pokud chcete vytvořit model hrozeb a nezapomněli připojení k prvkům datové toky, dostanete oznámení. Zprávu můžete ignorovat, nebo můžete postupujte podle pokynů a vyřešte problém. 

![Zprávy](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Poznámky

Přidání poznámky do diagramu, přejít ze **zprávy** kartu k **poznámky** kartě.

## <a name="analysis-view"></a>Zobrazení analýzy

Po sestavení diagramu, vyberte **Analysis** symbol (Lupa) na panelu nástrojů zástupce přepnout **Analysis** zobrazení.

![Zobrazení analýzy](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Výběr generovaného hrozeb

Když vyberete hrozbu, můžete provádět tři odlišné funkce:

| Funkce                               | Informace      |
| --------------------------------------- | ------------ |
| **Indikátor pro čtení** | <p>Riziko, že je označena jako pro čtení, který pomáhá udržovat přehled o položky, které jste prošli.</p><p>![Pro čtení nebo nepřečtená indikátoru](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interakce fokusu** | <p>Interakce na obrázku, který patří do hrozbu zvýrazní.</p><p>![Interakce fokusu](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Vlastnosti hrozeb** | <p>Další informace o riziko, že se zobrazí v **Threat vlastnosti** okno.</p><p>![Vlastnosti hrozeb](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Priorita změny

Můžete změnit úroveň priority jednotlivé generovaného hrozby. Různé barvy usnadňují identifikaci vysokou – střední- a hrozeb nízkou prioritu.

![Priorita změny](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Upravitelné pole vlastnosti hrozeb

Jak je vidět na předchozím obrázku, můžete změnit informace generovaný nástrojem. Můžete také přidat informace do určitá pole, jako je například zarovnání do bloku. Tato pole jsou generovány šablonou. Pokud potřebujete další informace na jednotlivé hrozby, můžete provést úpravy.

![Vlastnosti hrozeb](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Reports

Po dokončení změn priority a při aktualizaci stavu jednotlivé generovaného hrozby, můžete soubor uložit nebo vytisknout sestavu. Přejděte na **sestavy** > **vytvoření úplné sestavy**. Název sestavy a měli byste vidět něco podobného jako na následujícím obrázku:

![Sestava](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Další kroky

* Můžete přispívat šablonu pro komunity, přejděte na našem [Githubu](https://github.com/Microsoft/threat-modeling-templates) stránky. 
* Chcete-li začít pracovat s nástrojem, přejděte na [Stáhnout](https://aka.ms/tmtpreview) stránky.
