---
title: Použití entit pro klasifikaci a analýzu dat v ověřovacích datech Azure | Microsoft Docs
description: Přiřaďte klasifikace entit (uživatele, názvy hostitelů, IP adresy) k datovým položkám v Azure Sentinel a použijte je k porovnání, analýze a koreluji dat z více zdrojů.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 43da1af7a3001d7f8e000a878948428a3d63aa4e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456248"
---
# <a name="classify-and-analyze-data-using-entities-in-azure-sentinel"></a>Klasifikace a analýza dat pomocí entit v Azure Sentinel

## <a name="what-are-entities"></a>Co jsou entity?

Pokud jsou výstrahy odesílány nebo generovány službou Azure Sentinel, obsahují datové položky, které může Sentinel rozpoznat a klasifikovat do kategorií jako **entity**. Když Azure Sentinel rozumí, jaký druh entity konkrétní datová položka představuje, ví se o tom, co se o ní bude zeptat, a pak může porovnat informace o této položce v celé škále zdrojů dat a snadno je sledovat a odkazovat na ně v celém prostředí Sentinel – analýze, vyšetřování, nápravě, lovu atd. Mezi běžné příklady entit patří uživatelé, hostitelé, soubory, procesy, IP adresy a adresy URL.

### <a name="entity-identifiers"></a>Identifikátory entit

Sentinel Azure podporuje širokou škálu typů entit. Každý typ má své vlastní jedinečné atributy, včetně těch, které lze použít k identifikaci konkrétní entity. Tyto atributy jsou reprezentovány jako pole v entitě a nazývají se **identifikátory**. Úplný seznam podporovaných entit a jejich identifikátorů najdete v části.

#### <a name="strong-and-weak-identifiers"></a>Silné a slabé identifikátory

Jak bylo uvedeno výše, pro každý typ entity existují pole nebo sady polí, které ji mohou identifikovat. Tato pole nebo sady polí můžou být označovány jako **silné identifikátory** , pokud můžou jedinečně identifikovat entitu bez nejednoznačnosti, nebo jako **slabé identifikátory** , pokud můžou identifikovat entitu za určitých okolností, ale nemají zaručenou jedinečnou identifikaci entity ve všech případech. V mnoha případech je však možné kombinovat výběr slabých identifikátorů a vytvořit tak silný identifikátor.

Například uživatelské účty je možné identifikovat jako entity **účtu** s více než jedním způsobem: pomocí jednoho **silného identifikátoremu** , jako je číselný identifikátor účtu Azure AD (pole **GUID** ), nebo jeho hodnoty **hlavní název uživatele (UPN)** , případně pomocí kombinace **slabých identifikátorů** , jako je jeho **název** a pole **NTDomain** . Různé zdroje dat mohou identifikovat stejného uživatele různými způsoby. Kdykoli Azure Sentinel zjistí dvě entity, které může rozpoznat jako stejnou entitu na základě jejich identifikátorů, sloučí tyto dvě entity do jedné entity, aby mohla být zpracována správně a konzistentně.

Pokud ale jeden z vašich poskytovatelů prostředků vytvoří výstrahu, ve které není entita dostatečně identifikovaná – například jenom jeden **slabý identifikátor** , jako je uživatelské jméno bez kontextu názvu domény, nejde entitu uživatele sloučit s ostatními instancemi stejného uživatelského účtu. Tyto další instance by se identifikovaly jako samostatná entita. Tyto dvě entity by zůstaly oddělené místo sjednocení.

Abyste minimalizovali riziko tohoto problému, měli byste ověřit, že všichni poskytovatelé výstrah správně identifikují entity v upozorněních, které vydávají. Synchronizace entit uživatelských účtů pomocí Azure Active Directory může navíc vytvořit sjednocený adresář, který bude moci sloučit entity uživatelských účtů.

#### <a name="supported-entities"></a>Podporované entity

V systému Azure Sentinel jsou v tuto chvíli identifikované následující typy entit:

- Uživatelský účet
- Hostitel
- IP adresa
- Malware
- Soubor
- Proces
- Cloudová aplikace
- Název domény
- Prostředek Azure
- Hodnota hash souboru
- Klíč registru
- Hodnota registru
- Skupina zabezpečení
- URL
- Zařízení IoT
- Mailbox
- Poštovní cluster
- Poštovní zpráva
- Odeslaná pošta

Identifikátory těchto entit a další relevantní informace můžete zobrazit v referenčních informacích k [entitám](entities-reference.md).

## <a name="entity-mapping"></a>Mapování entit

Jak funkce Azure Sentinel v upozornění rozpozná určitou část dat jako identifikaci entity?

Pojďme se podívat, jak se zpracování dat provádí v Azure Sentinel. Data se ingestují z různých zdrojů přes [konektory](connect-data-sources.md), ať už jde o službu, službu založenou na agentech nebo pomocí služby syslog a předávání protokolů. Data jsou uložena v tabulkách v pracovním prostoru Log Analytics. Tyto tabulky se pak dotazují v pravidelných naplánovaných intervalech pomocí pravidel pro analýzu, která jste definovali a povolili. Jednou z mnoha akcí pořízených těmito pravidly analýzy je mapování datových polí v tabulkách na entity rozpoznané pro Azure Sentinel. Podle mapování definovaných ve vašich pravidlech analýz služba Azure Sentinel vezme pole z výsledků vrácených dotazem, rozpoznává je pomocí identifikátorů, které jste zadali pro každý typ entity, a aplikujte je na typ entity identifikovaný pomocí těchto identifikátorů.

Co je to vše?

Když je Azure Sentinel schopný identifikovat entity v výstrahách z různých typů zdrojů dat, a zejména v případě, že to může udělat pomocí silných identifikátorů společných pro každý zdroj dat nebo do třetího schématu, může se potom snadno korelovat mezi všemi těmito výstrahami a zdroji dat. Tyto korelace vám pomůžou vytvořit bohatou část informací a přehled o entitách, což vám poskytne Solid Foundation za vaše operace zabezpečení.

Naučte se [mapovat datová pole na entity](map-data-fields-to-entities.md).

Zjistěte [, které identifikátory silně identifikují entitu](entities-reference.md).

## <a name="entity-pages"></a>Stránky entit

Když narazíte na libovolnou entitu (v současné době omezené na uživatele a hostitele), můžete vybrat entitu a provést ji na **stránce entity**, datový list, který je plný z užitečných informací o této entitě. Typy informací, které na této stránce najdete, zahrnují základní fakta o entitě, časovou osu důležitých událostí souvisejících s touto entitou a přehled o chování entity.

Stránky entit se skládají ze tří částí:

- Panel na levé straně obsahuje identifikační informace entity shromážděné ze zdrojů dat, jako jsou Azure Active Directory, Azure Monitor, Azure Security Center a Microsoft Defender.

- Středový panel zobrazuje grafickou a textovou časovou osu důležitých událostí souvisejících s entitou, jako jsou například výstrahy, záložky a aktivity. Aktivity jsou agregace důležitých událostí z Log Analytics. Dotazy, které zjišťují tyto aktivity, jsou vyvíjeny týmy Microsoft Security Research.

- Na panelu na pravé straně se zobrazují přehledy chování entity. Tyto přehledy vám pomůžou rychle identifikovat anomálie a bezpečnostní hrozby. Přehledy se vyvinuly v rámci Microsoft Security Research teams a jsou založené na modelech detekce anomálií.

### <a name="the-timeline"></a>Časová osa

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="Časová osa stránek entit":::

Časová osa je hlavní součástí příspěvku na stránce entity k analýze chování v Azure Sentinel. Prezentuje v souvislosti s událostmi souvisejícími s entitami, které vám pomůžou pochopit aktivitu entity v určitém časovém rámci.

Můžete zvolit **časový rozsah** mezi několika možnostmi přednastavených (například *posledních 24 hodin*) nebo ho nastavit na libovolný vlastní časový rámec. Kromě toho můžete nastavit filtry, které omezují informace na časové ose na konkrétní typy událostí nebo výstrah.

Časová osa obsahuje následující typy položek:

- Výstrahy – všechny výstrahy, ve kterých je entita definovaná jako **mapovaná entita**. Všimněte si, že pokud vaše organizace vytvořila [vlastní výstrahy pomocí analytických pravidel](./tutorial-detect-threats-custom.md), měli byste se ujistit, že mapování entit pravidel se provádí správně.

- Záložky – jakékoli záložky, které obsahují konkrétní entitu zobrazenou na stránce.

- Aktivity – agregace důležitých událostí souvisejících s entitou.

### <a name="entity-insights"></a>Přehledy entit

Entity Insights jsou dotazy definované výzkumníky zabezpečení Microsoftu, které vašim analytikům pomůžou efektivněji a efektivně prozkoumat. Přehledy se zobrazují jako součást stránky entity a poskytují cenné informace o zabezpečení pro hostitele a uživatele ve formě tabulkových dat a grafů. Tady jsou informace, které vám to znamená, že nemusíte Log Analytics. Přehledy zahrnují data týkající se přihlášení, přidání skupin, události neobvyklé a další a zahrnují pokročilé algoritmy ML pro detekci chování neobvyklé.

Přehledy jsou založené na následujících zdrojích dat:

- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Azure Sentinel UEBA)
- Prezenční signál (agent Azure Monitor)
- CommonSecurityLog (Azure Sentinel)

### <a name="how-to-use-entity-pages"></a>Jak používat stránky entit

Stránky entit jsou navržené tak, aby byly součástí více scénářů použití, a je možné k nim získat přístup ze správy incidentů, grafu šetření, záložek nebo přímo na stránce vyhledávání entit v části **Analýza chování entit** v hlavní nabídce Azure Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Případy použití stránky entity":::

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se dozvěděli o práci s entitami v Azure Sentinel. Praktické pokyny k implementaci a používání přehledů, které jste získali, najdete v následujících článcích:

- [Povolí analýzy chování entit](./enable-entity-behavior-analytics.md) v Azure Sentinel.
- [Pro bezpečnostní hrozby](./hunting.md).
