---
title: Identifikace pokročilých hrozeb pomocí analýzy chování uživatelů a entit (UEBA) v Azure Sentinel | Microsoft Docs
description: Vytvářejte směrné plány chování pro entity (uživatele, názvy hostitelů, IP adresy) a využijte je k detekci chování neobvyklé a identifikaci nenulových pokročilých trvalých hrozeb (APT).
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
ms.date: 01/25/2021
ms.author: yelevin
ms.openlocfilehash: 7944ea48feaca1eb8312e01d876bf9627af429d1
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784791"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Identifikace pokročilých hrozeb pomocí analýzy chování uživatelů a entit (UEBA) v Azure Sentinel

> [!IMPORTANT]
>
> - Funkce stránek UEBA a entity jsou teď **obecně dostupné** ve *_všech_* geografických oblastech a oblastech Sentinel Azure.

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>Co je analýza chování uživatelů a entit (UEBA)?

### <a name="the-concept"></a>Koncept

Identifikace hrozeb v rámci vaší organizace a jejich potenciálního dopadu – bez ohledu na to, jestli je neoprávněná entita nebo škodlivý program Insider, se vždycky stal časově náročným procesem náročným na práci. Procházíme výstrahami, propojením teček a aktivním prostředím, které se přidává až do obrovských objemů a úsilí vynaložených s minimálními návratnostmi a možností sofistikovaných hrozeb jednoduše obejít zjišťování. Obzvláště profesionální hrozby, jako je například nula, cílené a pokročilé trvalé hrozby, mohou být pro vaši organizaci nejbezpečnější, takže jejich zjišťování je vše důležité.

Funkce UEBA v Azure Sentinel eliminuje drudgery z úloh vašich analytiků a nejistotu vyplývající z jejich úsilí a poskytuje vysokou věrnou inteligentní analýzu, aby se mohla soustředit na šetření a nápravu.

Když je Azure Sentinel shromažďuje protokoly a výstrahy ze všech připojených zdrojů dat, analyzuje je a sestavuje profily chování entit vaší organizace (uživatelů, hostitelů, IP adres, aplikací atd.) napříč časem a horizontem partnerské skupiny. Díky nejrůznějším technikám a možnostem strojového učení může Sentinel identifikovat aktivitu neobvyklé a pomůže vám určit, jestli došlo k ohrožení bezpečnosti majetku. Nejen to, ale může také zjistit relativní citlivost konkrétních assetů, identifikovat partnerské skupiny assetů a vyhodnotit potenciální dopad kteréhokoli daného ohroženého prostředku (jeho "" "vysokého" poloměru "). S těmito informacemi můžete efektivně určit prioritu šetření a zpracování incidentů. 

### <a name="architecture-overview"></a>Přehled architektury

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Architektura analýzy chování entit":::

### <a name="security-driven-analytics"></a>Analýzy řízené zabezpečením

Nechte inspirovat podle Gartner 's paradigma pro UEBA řešení poskytuje Azure Sentinel přístup "mimo rámec", který je založený na třech rámcích reference:

- _ *Případy použití:** stanovením priorit pro relevantní vektory útoku a scénáře založené na výzkumu zabezpečení zarovnaném na MITRE ATT&CK Framework of taktiku, techniky a dílčí techniky, které do dezaktivačního řetězu umísťují různé entity jako oběti, pachatele nebo body pivotu; Azure Sentinel se zaměřuje konkrétně na nejcennější protokoly, které může každý zdroj dat poskytnout.

- **Zdroje dat:** Při prvním a nejpřednější podpoře zdrojů dat Azure si Azure Sentinel Thoughtfully vybírá zdroje dat třetích stran, které poskytují data odpovídající scénářům hrozeb.

- **Analýza:** Pomocí různých algoritmů strojového učení (ML) Azure Sentinel identifikuje aktivity neobvyklé a prezentuje důkaz jasně a stručně ve formě kontextových rozšíření, které jsou uvedené níže.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Analýza chování mimo přístup":::

Azure Sentinel prezentuje artefakty, které pomůžou vašim analytikům zabezpečení jasně pochopit aktivity neobvyklé v kontextu a porovnat s profilem standardních hodnot uživatele. Akce prováděné uživatelem (nebo hostitelem nebo adresou) jsou vyhodnocovány v kontextu, kde "true" výsledek indikuje zjištěnou anomálii:
- v různých geografických umístěních, zařízeních a prostředích.
- v rámci časových a frekvenčních horizontů (ve srovnání s vlastní historií uživatele).
- ve srovnání s chováním partnerských vztahů.
- ve srovnání s chováním organizace.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Kontext entity":::


### <a name="scoring"></a>Vyhodnocování

Každá aktivita je hodnocena jako "skóre priority šetření" – což určuje pravděpodobnost konkrétního uživatele, který provádí určitou činnost na základě způsobu učení uživatele a jejich partnerských uzlů. Aktivity identifikované jako nejabnormálních výsledků získají nejvyšší skóre (na škále 0-10).

Příklad toho, jak to funguje, najdete v tématu Jak se používá analýza chování v [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) .

## <a name="entities-in-azure-sentinel"></a>Entity v Azure Sentinel

### <a name="entity-identifiers"></a>Identifikátory entit

Když se výstrahy odesílají do Azure Sentinel, obsahují datové prvky, které Azure Sentinel identifikuje a klasifikuje jako entity, například uživatelské účty, hostitele, IP adresy a další. V některých případech může být tato identifikace výzvou, pokud výstraha neobsahuje dostatečné informace o entitě.

Uživatelské účty je například možné identifikovat více než jedním způsobem: pomocí číselného identifikátoru (GUID) účtu Azure AD nebo jeho hodnoty hlavní název uživatele (UPN) nebo případně pomocí kombinace uživatelského jména a názvu domény NT. Různé zdroje dat mohou identifikovat stejného uživatele různými způsoby. Proto pokud je to možné, Azure Sentinel tyto identifikátory sloučí do jedné entity, aby mohla být správně identifikována.

Může k tomu dojít, když některý z poskytovatelů prostředků vytvoří výstrahu, ve které není entita dostatečně identifikovaná – například uživatelské jméno bez kontextu názvu domény. V takovém případě nemůže být entita uživatele sloučena s jinými instancemi stejného uživatelského účtu, který by byl identifikován jako samostatná entita, a tyto dvě entity by zůstaly oddělené místo sjednocení.

Abyste minimalizovali riziko tohoto problému, měli byste ověřit, že všichni poskytovatelé výstrah správně identifikují entity v upozorněních, které vydávají. Synchronizace entit uživatelských účtů pomocí Azure Active Directory může navíc vytvořit sjednocený adresář, který bude moci sloučit entity uživatelských účtů.

V systému Azure Sentinel jsou v tuto chvíli identifikované následující typy entit:

- Uživatelský účet (účet)
- Hostitel
- IP adresa (IP)
- Malware
- Soubor
- Proces
- Cloudová aplikace (CloudApplication)
- Název domény (DNS)
- Prostředek Azure
- File (hash)
- Klíč registru
- Hodnota registru
- Skupina zabezpečení
- URL
- Zařízení IoT
- Mailbox
- Poštovní cluster
- Poštovní zpráva
- Odeslaná pošta

### <a name="entity-pages"></a>Stránky entit

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
 
Entity Insights jsou dotazy definované výzkumníky zabezpečení Microsoftu, které vašim analytikům pomůžou efektivněji a efektivně prozkoumat. Přehledy se zobrazují jako součást stránky entity a poskytují cenné informace o zabezpečení pro hostitele a uživatele ve formě tabulkových dat a grafů. Tady jsou informace, které vám to znamená, že nemusíte Log Analytics. Přehledy zahrnují data týkající se přihlášení, přidání skupin, události neobvyklé a další a zahrnují pokročilé algoritmy ML pro detekci chování neobvyklé. Přehledy jsou založené na následujících datových typech:
- Syslog
- SecurityEvent
- Protokoly auditu
- Protokoly přihlášení
- Aktivita Office
- BehaviorAnalytics (UEBA) 
 
### <a name="how-to-use-entity-pages"></a>Jak používat stránky entit

Stránky entit jsou navržené tak, aby byly součástí více scénářů použití, a je možné k nim získat přístup ze správy incidentů, grafu šetření, záložek nebo přímo na stránce vyhledávání entit v části **Analýza chování entit** v hlavní nabídce Azure Sentinel.

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="Případy použití stránky entity":::


## <a name="data-schema"></a>Schéma dat

### <a name="behavior-analytics-table"></a>Tabulka analýzy chování

| Pole                     | Popis                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | jedinečné ID pro tenanta                                      |
| SourceRecordId            | jedinečné číslo ID události EBA                                   |
| TimeGenerated             | časové razítko výskytu aktivity                              |
| TimeProcessed             | časové razítko zpracování aktivity modulem EBA            |
| ActivityType              | kategorie nejvyšší úrovně aktivity                                 |
| ActionType                | normalizovaný název aktivity                                     |
| Uživatelské jméno                  | uživatelské jméno uživatele, který spustil aktivitu                    |
| UserPrincipalName         | úplné uživatelské jméno uživatele, který spustil aktivitu               |
| EventSource               | zdroj dat, který poskytl původní událost                        |
| SourceIPAddress           | IP adresa, ze které se iniciovala aktivita                        |
| SourceIPLocation          | země, ze které byla aktivita zahájena, obohacena z IP adresy |
| SourceDevice              | název hostitele zařízení, které spustilo aktivitu                  |
| DestinationIPAddress      | IP adresa cíle aktivity                            |
| DestinationIPLocation     | země cíle aktivity, obohaceno z IP adresy     |
| DestinationDevice         | název cílového zařízení                                           |
| **UsersInsights**         | kontextové obohacení zúčastněných uživatelů                            |
| **DevicesInsights**       | Kontextová obohacení zúčastněných zařízení                          |
| **ActivityInsights**      | Kontextová analýza aktivity založená na naší profilaci              |
| **InvestigationPriority** | skóre anomálií, mezi 0-10 (0 = neškodné, 10 = vysoce neobvyklé)         |
|

V [referenčním dokumentu pro obohacení UEBA](ueba-enrichments.md)můžete zobrazit úplnou sadu kontextových rozšíření, která jsou odkazována v **UsersInsights**, **DevicesInsights** a **ActivityInsights** .

### <a name="querying-behavior-analytics-data"></a>Dotazování na data analýzy chování

Pomocí [KQL](/azure/data-explorer/kusto/query/)se můžeme dotazovat na tabulku chování při analýze.

Například pokud chceme najít všechny případy uživatele, kterému se nepovedlo přihlásit k prostředku Azure, kde se jednalo o první pokus o připojení z dané země, a připojení z této země jsou neobvyklá i pro rovnocenné uživatele, můžeme použít následující dotaz:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>Metadata partnerských vztahů uživatelů – tabulka a Poznámkový blok

Metadata uživatelských partnerů poskytují důležitý kontext při detekci hrozeb, při vyšetřování incidentu a v lovu potenciální hrozby. Analytiky zabezpečení můžou sledovat normální aktivity partnerských uzlů uživatele, aby zjistili, jestli jsou aktivity uživatele neobvyklé v porovnání se svými partnery.

Služba Azure Sentinel vypočítá a rozhodne partnerské vztahy uživatele na základě členství ve skupině zabezpečení Azure AD, seznamu adresátů, et zajistila a ukládá partnerské vztahy seřazené 1-20 v tabulce **UserPeerAnalytics** . Níže uvedený snímek obrazovky ukazuje schéma tabulky UserPeerAnalytics a zobrazuje prvních osm partnerských uzlů uživatele Kendall Collins. Azure Sentinel používá algoritmus *četnosti inverzního dokumentu* (TF-IDF) k normalizování vážení pro výpočet pořadí: čím menší je, tím vyšší je váha. 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="Snímek obrazovky tabulky metadat partnerských vztahů uživatelů":::

K vizualizaci metadat partnerských vztahů uživatelů můžete použít [Poznámkový blok Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) , který je k dispozici v úložišti GitHub Azure Sentinel. Podrobné pokyny k používání poznámkového bloku najdete v poznámkovém bloku [metadata zabezpečení uživatele s asistencí](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="permission-analytics---table-and-notebook"></a>Analýzy oprávnění – tabulka a Poznámkový blok

Analýza oprávnění pomáhá určit potenciální dopad na narušení organizačního prostředku útočníkem. Tento dopad se označuje také jako "vysoké poloměr assetu". Analytici zabezpečení můžou tyto informace použít k určení priorit šetření a zpracování incidentů.

Azure Sentinel Určuje práva k přímým a přenosnému přístupu držené daným uživatelem a prostředky Azure vyhodnocením předplatných Azure, ke kterým může uživatel přistupovat přímo nebo prostřednictvím skupin nebo instančních objektů. Tyto informace, stejně jako úplný seznam členství uživatele ve skupině zabezpečení Azure AD, se pak ukládají do tabulky **UserAccessAnalytics** . Níže uvedený snímek obrazovky ukazuje vzorový řádek v tabulce UserAccessAnalytics pro uživatele Alex Johnsonem. **Zdrojová entita** je uživatel nebo hlavní účet služby a **Cílová entita** je prostředek, ke kterému má zdrojová entita přístup. Hodnoty **úrovně přístupu** a **typu přístupu** závisí na modelu řízení přístupu cílové entity. Můžete vidět, že Alex má přispěvatele přístup k Tenantovi Azure s předplatným *Contoso*. Model řízení přístupu předplatného je Azure RBAC.   

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="Snímek obrazovky tabulky analýzy přístupu uživatele":::

Pomocí [poznámkového bloku Jupyter](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (výše zmíněného poznámkového bloku) z úložiště GitHub Azure Sentinel můžete vizualizovat data analýzy oprávnění. Podrobné pokyny k používání poznámkového bloku najdete v poznámkovém bloku [metadata zabezpečení uživatele s asistencí](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb) .

### <a name="hunting-queries-and-exploration-queries"></a>Lovecké dotazy a dotazy průzkumu

Azure Sentinel poskytuje předem připravenou sadu pro lovecké dotazy, průzkumové dotazy a sešity založené na tabulce BehaviorAnalytics. Tyto nástroje prezentují obohacená data zaměřené na konkrétní případy použití, které označují chování neobvyklé. 

Přečtěte si další informace o [lovu a grafu šetření](./hunting.md) ve službě Azure Sentinel.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli o schopnostech analýzy chování entit v Azure Sentinel. Praktické pokyny k implementaci a používání přehledů, které jste získali, najdete v následujících článcích:

- [Povolí analýzy chování entit](./enable-entity-behavior-analytics.md) v Azure Sentinel.
- [Pro bezpečnostní hrozby](./hunting.md).