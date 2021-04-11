---
title: Co je nového v Azure Sentinel
description: Tento článek popisuje nové funkce služby Azure Sentinel v posledních měsících.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 74cd5ce5912e5a656342a43b7c2b165fdf3c32d4
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490286"
---
# <a name="whats-new-in-azure-sentinel"></a>Co je nového v Azure Sentinel

Tento článek obsahuje seznam posledních funkcí přidaných pro službu Azure Sentinel a nové funkce v souvisejících službách, které poskytují vylepšené uživatelské prostředí Azure Sentinel.

Informace o dřívějších funkcích, které jsou dodávány, najdete v našem [blogech pro technickou komunitu](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Vyznačené funkce jsou aktuálně ve verzi PREVIEW. [Doplňkové podmínky Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zahrnují další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.


> [!TIP]
> Naši týmy pro lovecké hrozby napříč Microsoft Contribute dotazy, playbooky, sešity a poznámkové bloky do [komunity Sentinel Azure](https://github.com/Azure/Azure-Sentinel), včetně specifických [loveckých dotazů](https://github.com/Azure/Azure-Sentinel) , které vaše týmy můžou přizpůsobovat a používat. 
>
> Můžete také přispět. Připojte se k nám ve [komunitě Azure Sentinel Threat Hunters GitHub](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="march-2021"></a>Březen 2021

- [Nastavit automatické aktualizace sešitů v režimu zobrazení](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Nové detekce pro Azure Firewall](#new-detections-for-azure-firewall)
- [Pravidla automatizace a playbooky aktivované incidentem](#automation-rules-and-incident-triggered-playbooks) (včetně všech nových dokumentace PlayBook)
- [Nové obohacení výstrah: vylepšené mapování entit a vlastní podrobnosti](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Tisk sešitů Sentinel Azure nebo uložení ve formátu PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Filtry incidentů a předvolby řazení jsou nyní uloženy ve vaší relaci (Public Preview).](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Integrace incidentů v programu Microsoft 365 Defender (Public Preview)](#microsoft-365-defender-incident-integration-public-preview)
- [Nové konektory Microsoft Service Connector pomocí Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Nastavit automatické aktualizace sešitů v režimu zobrazení

Uživatelé Sentinel Azure teď můžou používat novou [Azure monitor schopnost](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) automaticky aktualizovat data sešitu během relace zobrazení.

V každém sešitu nebo šabloně sešitu vyberte možnost :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **automaticky aktualizovat** a zobrazte možnosti intervalu. Vyberte možnost, kterou chcete použít pro aktuální relaci zobrazení, a vyberte **použít**.

- Podporované intervaly aktualizace jsou v rozsahu od **5 minut** do **1 dne**.
- Ve výchozím nastavení je automatická aktualizace vypnutá. Pro optimalizaci výkonu je automatická aktualizace také vypnuta při každém zavření sešitu a neběží na pozadí. Při příštím otevření sešitu znovu zapněte automatické obnovení podle potřeby.
- Automatická aktualizace je pozastavena při úpravách sešitu a intervaly automatické aktualizace se restartují pokaždé, když přepnete zpět do režimu zobrazení v režimu úprav.

    Intervaly se také restartují, pokud sešit ručně aktualizujete tak, že vyberete :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: tlačítko **aktualizovat** .

Další informace najdete v tématu [kurz: vizualizace a monitorování dat](tutorial-monitor-your-data.md) a dokumentace k [Azure monitor](../azure-monitor/visualize/workbooks-overview.md).

### <a name="new-detections-for-azure-firewall"></a>Nové detekce pro Azure Firewall

Do oblasti [Analytics](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) v Azure Sentinel se přidalo několik předem připravených detekcí pro Azure firewall. Tato nová zjišťování umožňují týmům zabezpečení získat výstrahy v případě, že se počítače v interní síti pokoušejí dotazovat nebo se připojit k názvům domén nebo IP adresám, které jsou přidruženy ke známým IOCs, jak jsou definovány v dotazu pravidla zjišťování.

Nové detekce zahrnují:

- [Solorigate síťovou signalizaci](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Známé domény GALLIUM a hodnoty hash](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Známá IRIDIUM IP adresa](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Známé domény skupin fosforu/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [THALLIUM domény zahrnuté do DCU Takedown](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Známá hodnota hash maldoc související s ZINKem](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Známé domény skupiny STRONCI](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [NOBELIUM-Domain a IP IOCs – březen 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Detekce pro brány firewall Azure se průběžně přidávají do předdefinované galerie šablon. Chcete-li získat nejnovější zjišťování pro Azure Firewall, v části **šablony pravidel** filtrujte **zdroje dat** podle **Azure firewall**:

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Nové detekce v sešitu analýzy efektivity":::

Další informace najdete v tématu [nové detekce pro Azure firewall ve službě Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958).

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Pravidla automatizace a playbooky aktivované incidenty

Pravidla automatizace představují nový koncept ve službě Azure Sentinel, který umožňuje centrálně spravovat automatizaci zpracování incidentů. Kromě toho, že vám umožní přiřazovat playbooky incidentům (ne jen k výstrahám jako dřív), pravidla automatizace také umožňují automatizovat odpovědi na více pravidel analýzy najednou, automaticky označovat, přiřazovat nebo uzavřít incidenty bez nutnosti playbooky a řídit pořadí akcí, které se spustí. Pravidla automatizace zjednodušují použití automatizace v Azure Sentinel a umožní vám zjednodušit složité pracovní postupy pro procesy orchestrace incidentů.

Seznamte se s [úplnými vysvětlení pravidel automatizace](automate-incident-handling-with-automation-rules.md).

Jak je uvedeno výše, playbooky se teď dá aktivovat pomocí triggeru incidentu vedle triggeru výstrahy. Trigger incidentu poskytuje playbooky větší sadu vstupů, se kterými se pracuje (protože incident zahrnuje všechna upozornění a data entit) a poskytuje ještě větší výkon a flexibilitu v pracovních postupech reakcí. Playbooky aktivované incidentem se aktivují z pravidel automatizace.

Přečtěte si další informace o [rozšířených možnostech playbooky](automate-responses-with-playbooks.md)a o tom, jak [vytvořit pracovní postup odpovědi](tutorial-respond-threats-playbook.md) pomocí playbooky společně s pravidly automatizace.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Nové obohacení výstrah: vylepšené mapování entit a vlastní podrobnosti

Rozšiřte své výstrahy dvěma novými způsoby, abyste je mohli snadněji použít a podrobnější.

Začněte tím, že převezmete mapování entit na další úroveň. Nyní můžete namapovat skoro 20 druhů entit, uživatelů, hostitelů a IP adres, souborů a procesů, poštovních schránek, prostředků Azure a zařízení IoT. Pro každou entitu můžete také použít více identifikátorů, abyste posílili jejich jedinečnou identifikaci. Díky tomu máte ve svých incidentech mnohem rozsáhlejší datovou sadu, která poskytuje širší korelaci a výkonnější šetření. [Naučte se nový způsob mapování entit](map-data-fields-to-entities.md) v upozorněních.

[Přečtěte si další informace o entitách](entities-in-azure-sentinel.md) a podívejte se na [úplný seznam dostupných entit a jejich identifikátorů](entities-reference.md).

Poskytněte možnosti pro zkoumání a odpovědi ještě většímu zvýšení úrovně přizpůsobením vašich upozornění na podrobnosti o povrchu z nezpracovaných událostí. Přeneste obsah události do svých incidentů, abyste měli stále větší výkon a flexibilitu v reakci na hrozby zabezpečení a šetření. [Naučte se, jak v výstrahách surfovat vlastní podrobnosti](surface-custom-details-in-alerts.md) .



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Tisk sešitů Sentinel Azure nebo uložení ve formátu PDF

Nyní můžete tisknout sešity Sentinel Azure, které vám také umožní exportovat je do souborů PDF a ukládat místně nebo sdílet.

V sešitu vyberte nabídku Možnosti > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Tisk obsahu**. Pak vyberte svoji tiskárnu nebo v případě potřeby vyberte **Uložit jako PDF** .

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Vytiskněte sešit nebo ho uložte jako PDF.":::

Další informace najdete v tématu [kurz: vizualizace a monitorování dat](tutorial-monitor-your-data.md).

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Filtry incidentů a předvolby řazení jsou nyní uloženy ve vaší relaci (Public Preview).

Filtry incidentů a řazení se teď ukládají v rámci relace Sentinel Azure, a to i při přechodu na jiné oblasti produktu.
Pokud stále pracujete ve stejné relaci, můžete přejít zpátky do oblasti [incidenty](tutorial-investigate-cases.md) v Azure Sentinel a zobrazit tak filtry a řazení, stejně jako jste to opustili.

> [!NOTE]
> Filtry incidentů a řazení se po ukončení služby Azure Sentinel nebo obnovení prohlížeče neuloží.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Integrace incidentů v programu Microsoft 365 Defender (Public Preview)

Integrace incidentů služby [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) v Azure Sentinel umožňuje streamování všech M365D incidentů do Azure Sentinel a jejich synchronizaci mezi oběma portály. Incidenty od M365D (dříve označované jako Microsoft Threat Protection nebo MTP) obsahují všechny přidružené výstrahy, entity a relevantní informace, které vám poskytnou dostatečný kontext pro provádění třídění a předběžné šetření v kontextu Azure Sentinel. Jednou v Sentinele budou incidenty stále synchronizované s M365D, což vám umožní využít výhody obou portálů při vyšetřování incidentů.

Použití služby Azure Sentinel a Microsoft 365 Defender společně vám dává nejlepší z obou světů. Získáte přehled o tom, že vám SIEM poskytuje celou škálu informačních prostředků vaší organizace, a také hloubku vlastního a přizpůsobeného vyšetřovacího výkonu, který nástroj XDR poskytuje k ochraně vašich Microsoft 365ch prostředků, a to jak z těchto koordinovaných, tak i z důvodu bezproblémové operace SOC.

Další informace najdete v tématu [integrace programu Microsoft 365 Defender s použitím Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nové konektory Microsoft Service Connector pomocí Azure Policy

[Azure Policy](../governance/policy/overview.md) je služba Azure, která umožňuje používat zásady k vymáhání a řízení vlastností prostředku. Použití zásad zajišťuje, aby prostředky zůstaly v souladu se standardy pro řízení IT.

Mezi vlastnosti prostředků, které mohou být řízeny zásadami, patří vytváření a zpracování protokolů diagnostiky a auditování. Azure Sentinel nyní používá Azure Policy k tomu, aby vám umožnila použít pro všechny (aktuální a budoucí) prostředky určitého typu, jejichž protokoly chcete ingestovat do Azure Sentinel, možnost společné sady diagnostických protokolů. Když Azure Policy, nebudete už muset nastavovat prostředky diagnostiky nastavení prostředky podle prostředku.

Konektory založené na Azure Policy jsou teď dostupné pro tyto služby Azure:
- [Azure Key Vault](connect-azure-key-vault.md) (Public Preview)
- [Služba Azure Kubernetes](connect-azure-kubernetes-service.md) (Public Preview)
- Databáze a servery Azure SQL (GA)

Zákazníci pořád budou moct protokoly odesílat ručně pro konkrétní instance a nemusí používat modul zásad.

## <a name="february-2021"></a>Únor 2021

- [Sešit certifikace kyberbezpečnosti (CMMC) modelu splatnosti](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Datové konektory třetích stran](#third-party-data-connectors)
- [UEBA Insights na stránce entity (Public Preview)](#ueba-insights-in-the-entity-page-public-preview)
- [Vylepšené vyhledávání incidentů (verze Public Preview)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>Sešit certifikace kyberbezpečnosti (CMMC) modelu splatnosti

Sešit Azure Sentinel CMMC poskytuje mechanismus pro zobrazení dotazů protokolu zarovnaných k CMMC ovládacím prvkům v portfoliu Microsoftu, včetně nabídek zabezpečení Microsoftu, Office 365, týmů, Intune, virtuálního klienta Windows a spousty dalších.

Sešit CMMC umožňuje architektům zabezpečení, inženýrům, analytikům v oblasti zabezpečení, manažerům a odborníkům na IT získat přehled o tom, jaké informace jsou pro zabezpečení cloudových úloh stav. K dispozici jsou také doporučení pro výběr, návrh, nasazení a konfiguraci nabídek Microsoftu pro sbližování s příslušnými požadavky a postupy CMMC.

I v případě, že nepotřebujete dodržovat CMMC, je sešit CMMC užitečný při vytváření Center pro provozní centra, vývoji výstrah, vizualizaci hrozeb a poskytování povědomí o úlohách v situaci.

Přístup k sešitu CMMC v oblasti **sešitů** Sentinel Azure Vyberte **šablonu** a pak vyhledejte **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Zapnout a vypnout průvodce sešitem CMMC" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Další informace naleznete v tématu:

- [Sešit certifikace modelu splatnosti Azure Sentinel kyberbezpečnosti (CMMC)](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Kurz: Vizualizace a monitorování dat](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Datové konektory třetích stran

Naše kolekce integrace třetích stran se pořád zvětšuje, ale během posledních dvou měsíců se přidaly třicet konektory. Tady je seznam:

- [Antivirová ochrana proti útokům phishing a ochrana značky](connect-agari-phishing-defense.md)
- [Akamai události zabezpečení](connect-akamai-security-events.md)
- [Alsid pro službu Active Directory](connect-alsid-active-directory.md)
- [Server Apache HTTP](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [CylancePROTECT BlackBerry](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco FirePOWER eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [UCS (Cisco Unified Computing System)](connect-cisco-ucs.md)
- [Společnost ESET Enterprise Inspector](connect-data-sources.md)
- [Vyesetit Centrum správy zabezpečení](connect-data-sources.md)
- [Pracovní prostor Google (dříve G sada)](connect-google-workspace.md)
- [Imperva WAF Gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog protokoly DNS](connect-nxlog-dns.md)
- [NXLog Linux – audit](connect-nxlog-linuxaudit.md)
- [Platforma Onapsis](connect-data-sources.md)
- [Zabezpečení e-mailů na vyžádání Proofpoint (POD)](connect-proofpoint-pod.md)
- [Znalostní báze správy ohrožení zabezpečení Qualys](connect-data-sources.md)
- [Cloud služeb Salesforce](connect-salesforce-service-cloud.md)
- [SonicWall firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Endpoint Protection Symantec](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>UEBA Insights na stránce entity (Public Preview)

Stránky s podrobnostmi o entitě Azure Sentinel poskytují [podokno přehledy](identify-threats-with-entity-behavior-analytics.md#entity-insights), ve kterém se zobrazují přehledy chování pro entitu a umožňují rychle identifikovat anomálie a bezpečnostní hrozby.

Pokud jste [povolili UEBA](ueba-enrichments.md)a vybrali jste časový rámec alespoň čtyři dny, bude mít teď v tomto podokně přehledy taky následující nové oddíly pro UEBA Insights:

|Sekce  |Description  |
|---------|---------|
|**UEBA přehledy**     | Shrnuje aktivity uživatelů neobvyklé: <br>– Napříč geografickými umístěními, zařízeními a prostředími<br>– Napříč časovými a frekvenčními horizonty ve srovnání s vlastní historií uživatele <br>– Porovnání s chováním partnerských vztahů <br>– Ve srovnání s chováním organizace     |
|**Partnerské vztahy uživatele na základě členství ve skupině zabezpečení**     |   Vypíše partnerské vztahy uživatele založené na členství ve skupinách zabezpečení služby Azure AD a poskytnou týmům operací zabezpečení seznam dalších uživatelů, kteří sdílejí podobná oprávnění.  |
|**Oprávnění pro přístup uživatelů k předplatnému Azure**     |     Zobrazuje přístupová oprávnění uživatele k předplatným Azure, která jsou k dispozici přímo nebo prostřednictvím skupin nebo objektů služby Azure AD.   |
|**Indikátory hrozeb vztahující se k uživateli**     |  Obsahuje seznam známých hrozeb souvisejících s IP adresami, které se zobrazují v činnostech uživatele. Hrozby jsou uvedené podle typu a rodiny hrozeb a jsou obohaceny službou Microsoft Threat Intelligence.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Vylepšené vyhledávání incidentů (verze Public Preview)

Vylepšili jsme možnosti vyhledávání incidentu v Azure Sentinel, což vám umožní rychleji procházet incidenty při zkoumání konkrétní hrozby.

Při hledání incidentů ve službě Azure Sentinel teď můžete vyhledávat podle následujících údajů o incidentu:

- ID
- Nadpis
- Produkt
- Vlastník
- Značka

## <a name="january-2021"></a>Leden 2021

- [Průvodce analytickým pravidlem: vylepšené možnosti úprav dotazů (verze Public Preview)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [AZ. SecurityInsights PowerShell Module (Public Preview)](#azsecurityinsights-powershell-module-public-preview)
- [Konektor SQL Database](#sql-database-connector)
- [Konektor Dynamics 365 (Public Preview)](#dynamics-365-connector-public-preview)
- [Vylepšené komentáře k incidentům](#improved-incident-comments)
- [Vyhrazené Log Analytics clustery](#dedicated-log-analytics-clusters)
- [Spravované identity Logic Apps](#logic-apps-managed-identities)
- [Vylepšené ladění pravidel pomocí grafů s náhledem analytického pravidla](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Průvodce analytickým pravidlem: vylepšené možnosti úprav dotazů (verze Public Preview)

Průvodce pravidlo naplánované analýzy Azure Sentinel teď poskytuje následující vylepšení pro psaní a úpravy dotazů:

-   Rozšiřitelné okno pro úpravy, které poskytuje více místa na obrazovce pro zobrazení dotazu.
-   Klíčové slovo zvýraznění v kódu dotazu.
-   Podpora rozšířeného automatického dokončování.
-   Ověřování dotazů v reálném čase. Chyby v dotazu se teď v posuvníku zobrazují jako červený blok a jako červená tečka v názvu karty **logika pravidla nastavení** . Kromě toho nelze uložit dotaz s chybami.

Další informace najdete v tématu [kurz: vytvoření vlastních pravidel analýzy pro detekci hrozeb](tutorial-detect-threats-custom.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>AZ. SecurityInsights PowerShell Module (Public Preview)

Azure Sentinel teď podporuje nový modul PowerShellu [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) .

Modul **AZ. SecurityInsights** podporuje běžné případy použití Sentinel v Azure, jako je například interakce s incidenty ke změně Statues, závažnosti, vlastníka a tak dále, přidávání komentářů a popisků k incidentům a vytváření záložek.

I když pro kanál CI/CD doporučujeme používat šablony [Azure Resource Manager (ARM)](../azure-resource-manager/templates/index.yml) , modul **AZ. SecurityInsights** je vhodný pro úlohy po nasazení a je zaměřený na automatizaci SOC.  Například automatizace SOC může zahrnovat kroky ke konfiguraci datových konektorů, vytváření pravidel analýzy nebo přidávání akcí automatizace do pravidel analýz.

Další informace, včetně úplného seznamu a popisu dostupných rutin, popisů parametrů a příkladů, najdete v [dokumentaci AZ. SecurityInsights PowerShellu](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>Konektor SQL Database

Azure Sentinel teď poskytuje konektor Azure SQL Database, který umožňuje streamovat auditování a diagnostické protokoly vašich databází do služby Azure Sentinel a průběžně monitorovat aktivity ve všech vašich instancích.

Azure SQL je plně spravovaný databázový stroj PaaS (platforma jako služba), který zpracovává většinu funkcí správy databází, jako je upgrade, opravy, zálohování a monitorování, bez zásahu uživatele.

Další informace najdete v tématu [připojení protokolů diagnostiky a auditu služby Azure SQL Database](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector-public-preview"></a>Konektor Dynamics 365 (Public Preview)

Azure Sentinel teď poskytuje konektor pro Microsoft Dynamics 365, který umožňuje shromažďovat protokoly aktivit uživatelů, správců a podpory aplikace Dynamics 365 do Azure Sentinel. Tato data můžete použít k tomu, abyste vám pomohli auditovat celé akce zpracování dat a analyzovat je, aby mohlo dojít k narušení zabezpečení.

Další informace najdete v tématu [připojení protokolů aktivit Dynamics 365 ke službě Azure Sentinel](connect-dynamics-365.md).

### <a name="improved-incident-comments"></a>Vylepšené komentáře k incidentům

Analytici používají komentáře k incidentům ke spolupráci na incidentech, postupy při dokumentaci a kroky ručně nebo jako součást PlayBook. 

Naše vylepšené možnosti přidávání komentářů k incidentům umožňují formátovat komentáře a upravovat nebo odstraňovat existující komentáře.

Další informace najdete v tématu [Automatické vytváření incidentů z výstrah zabezpečení společnosti Microsoft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Vyhrazené Log Analytics clustery

Funkce Azure Sentinel teď podporuje vyhrazené clustery Log Analytics jako možnost nasazení. Vyhrazený cluster doporučujeme zvážit v těchto případech:

- Ingestování **na 1 TB za den** do pracovního prostoru Sentinel Azure
- **Mít několik pracovních prostorů služby Azure Sentinel** v registraci Azure

Vyhrazené clustery umožňují používat funkce, jako jsou klíče spravované zákazníkem, bezpečnostní modul, dvojité šifrování a rychlejší dotazy mezi jednotlivými pracovními prostory, pokud máte ve stejném clusteru více pracovních prostorů.

Další informace najdete v tématu [Azure monitor protokolu vyhrazených clusterů](../azure-monitor/logs/logs-dedicated-clusters.md).

### <a name="logic-apps-managed-identities"></a>Spravované identity Logic Apps

Azure Sentinel teď podporuje spravované identity pro konektor Azure Sentinel Logic Apps a umožňuje udělit oprávnění přímo ke konkrétnímu playbooku, aby fungovala na Azure Sentinel místo vytváření dalších identit.

- **Bez spravované identity** konektor Logic Apps vyžaduje samostatnou identitu s rolí RBAC Sentinel Azure, aby bylo možné spustit službu Azure Sentinel. Samostatnou identitou může být uživatel služby Azure AD nebo instanční objekt, jako je například aplikace registrovaná v Azure AD.

- **Zapnutím podpory spravovaných identit ve vaší aplikaci logiky** se zaregistruje aplikace logiky pomocí Azure AD a poskytne ID objektu. Použijte ID objektu v Azure Sentinel k přiřazení aplikace logiky k roli Azure RBAC v pracovním prostoru Sentinel Azure. 

Další informace naleznete v tématu:

- [Ověřování pomocí spravované identity v Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [Dokumentace ke konektoru služby Azure Sentinel Logic Apps](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Vylepšené ladění pravidel pomocí grafů s náhledem analytického pravidla (Public Preview)

Azure Sentinel teď pomáhá lépe vyladit pravidla analýzy, což vám pomůže zvýšit přesnost a snížit šum.

Po úpravě pravidla analýzy na kartě **nastavit logiku pravidla** Najděte na pravé straně oblast **simulace výsledků** . 

Vyberte **test s aktuálními daty** , aby Azure Sentinel spouštěl simulaci posledních 50 spuštění pravidla analýzy. Vygeneruje se graf, který zobrazí průměrný počet výstrah, které se pravidlo vygenerovalo, na základě vyhodnocených nezpracovaných dat události. 

Další informace najdete v tématu [Definování logiky dotazu pravidla a konfigurace nastavení](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Prosinec 2020

- [80 nové integrované lovecké dotazy](#80-new-built-in-hunting-queries)
- [Vylepšení agenta Log Analytics](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nové integrované lovecké dotazy
 
Integrované lovecké dotazy služby Azure Sentinel umožňují analytikům SOC snižovat mezery v aktuálním pokrytí detekce a Ignite nové lovecké zájemce.

Tato aktualizace pro službu Azure Sentinel zahrnuje nové lovecké dotazy, které poskytují pokrytí v rámci matice MITRE ATT&CK Framework:

- **Kolekce**
- **Příkaz a ovládací prvek**
- **Přístup k přihlašovacím údajům**
- **Zjišťování**
- **Realizaci**
- **Exfiltrace**
- **Dopad**
- **Počáteční přístup**
- **Dočasné**
- **Elevace oprávnění**

Přidané lovecké dotazy jsou navržené tak, aby vám pomohly najít podezřelou aktivitu ve vašem prostředí. I když můžou vracet legitimní aktivity a potenciálně škodlivou aktivitu, můžou být užitečné při vyplňování vašeho lovu. 

Po spuštění těchto dotazů máte jistotu, že budete chtít výsledky převést na analytická pravidla nebo přidat lovecké výsledky existujícím nebo novým incidentům.

Všechny přidané dotazy jsou k dispozici prostřednictvím stránky pro lov v rámci služby Azure Sentinel. Další informace najdete v tématu věnovaném [Zalovenému pro hrozby pomocí služby Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Vylepšení agenta Log Analytics

Výhodou pro uživatele Azure Sentinel z následujících vylepšení agenta Log Analytics:

- **Podpora pro další operační systémy**, včetně CentOS 8, RedHat 8 a SUSE Linux 15.
- **Podpora Pythonu 3** kromě Pythonu 2

Azure Sentinel používá agenta Log Analytics k posílání událostí do vašeho pracovního prostoru, včetně událostí zabezpečení systému Windows, událostí syslog, protokolů CEF a dalších.

> [!NOTE]
> Agent Log Analytics se někdy označuje jako agent OMS nebo Microsoft Monitoring Agent (MMA). 
> 

Další informace najdete v dokumentaci k [Log Analytics](../azure-monitor/agents/log-analytics-agent.md) a v [poznámkách k verzi agenta Log Analytics](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>Listopad 2020

- [Monitorování stavu vašeho Playbookyu v Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Konektor programu Microsoft 365 Defender (Public Preview)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>Monitorování stavu vašeho Playbookyu v Azure Sentinel

Služba Azure Sentinel playbooky vychází z pracovních postupů vytvořených v [Azure log Apps](../logic-apps/index.yml), což je cloudová služba, která pomáhá plánovat, automatizovat a orchestrovat úkoly, obchodní procesy a pracovní postupy. Playbooky se dá automaticky vyvolat při vytvoření incidentu nebo při třídění a práci s incidenty. 

Abychom vám poskytli přehled o stavu, výkonu a využití vašich playbooky, Přidali jsme [sešit](../azure-monitor/visualize/workbooks-overview.md) s názvem **monitorování stavu playbooky**. 

Pomocí sešitu **sledování stavu playbooky** můžete monitorovat stav svého playbookyu, nebo se můžete podívat na anomálie v množství úspěšných nebo neúspěšných spuštění. 

Sešit **monitorování stavu playbooky** je teď dostupný v galerii šablon Azure Sentinel:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Ukázka sešitu monitorování stavu Playbooky":::

Další informace naleznete v tématu:

- [Dokumentace k Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Dokumentace k Azure Monitor](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Konektor programu Microsoft 365 Defender (Public Preview)
 
Konektor programu Microsoft 365 Defender pro Azure Sentinel umožňuje streamovat pokročilé protokoly o lovu (typ nezpracovaných dat událostí) z Microsoft 365 Defenderu do Azure Sentinel. 

Díky integraci programu [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) do bezpečnostního kolektoru v [Microsoft 365 Defenderu](/microsoft-365/security/mtp/microsoft-threat-protection) teď můžete shromáždit rozšířené lovecké události v programu Microsoft Defender pro koncové body pomocí konektoru Microsoft 365 Defenderu a streamovat je přímo do nových účelových tabulek v pracovním prostoru Sentinel Azure. 

Tabulky Sentinel Azure jsou postavené na stejném schématu, které se používá na portálu Microsoft 365 Defender, a poskytuje úplný přístup k kompletní sadě pokročilých protokolů pro lov. 

Další informace najdete v tématu [připojení dat z Microsoft 365 Defenderu k Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender byl dřív známý jako Microsoft Threat Protection nebo MTP. Microsoft Defender pro koncové body se dřív jmenoval jako rozšířená ochrana před internetovými útoky v programu Microsoft Defender nebo MDATP.
> 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[On-Board – Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Získání přehledu o upozorněních](quickstart-get-visibility.md)
