---
title: Poznámky k verzi pro Azure Security Center
description: Popis toho, co je nového a co se změnilo v Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 03/22/2021
ms.author: memildin
ms.openlocfilehash: f6ec14c577d1203b92085b791f89e4873a97c41a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786077"
---
# <a name="whats-new-in-azure-security-center"></a>Co je nového v Azure Security Center?

Security Center je v aktivním vývoji a průběžně přijímá vylepšení. Tato stránka vám poskytne informace o nových funkcích, opravách chyb a zazastaralých funkcích, abyste měli přehled o nejnovějším vývoji.

Tato stránka se často aktualizuje, takže ji můžete často znovu navštěvovat. 

Další informace o *plánovaných* změnách, které už brzy Security Center, najdete v tématu [důležité nadcházející změny Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Pokud hledáte položky starší než šest měsíců, najdete je v archivu, kde najdete novinky [v Azure Security Center](release-notes-archive.md).


## <a name="march-2021"></a>Březen 2021

Aktualizace v březnu zahrnují:

- [Správa Azure Firewall integrovaná do Security Center](#azure-firewall-management-integrated-into-security-center)
- [Posouzení ohrožení zabezpečení SQL teď zahrnuje možnosti "zakázat pravidlo" (Preview).](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor sešitů integrovaných do Security Center a tří dodaných šablon](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Řídicí panel dodržování předpisů teď zahrnuje sestavy služby Azure audit (Preview).](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Data doporučení se dají zobrazit v Azure Resource graphu s "prozkoumat v ARG".](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Aktualizace zásad pro nasazení automatizace pracovního postupu](#updates-to-the-policies-for-deploying-workflow-automation)
- [Dvě starší verze doporučení už Nezapisovat data přímo do protokolu aktivit Azure](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Vylepšení stránky doporučení](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Správa Azure Firewall integrovaná do Security Center

Když otevřete Azure Security Center, na první stránce se zobrazí stránka přehled. 

Tento interaktivní řídicí panel poskytuje jednotný pohled na stav zabezpečení vašich hybridních cloudových úloh. Kromě toho zobrazuje výstrahy zabezpečení, informace o pokrytí a další.

V rámci snazšího zobrazení stavu zabezpečení z centrálního prostředí jsme do tohoto řídicího panelu zavedli nástroj Azure Firewall Manager. Nyní můžete zkontrolovat stav pokrytí brány firewall ve všech sítích a centrálně spravovat Azure Firewall zásady od Security Center.

Další informace o tomto řídicím panelu najdete na [stránce s přehledem Azure Security Center](overview-page.md).

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Řídicí panel s přehledem Security Center s dlaždicí pro Azure Firewall":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>Posouzení ohrožení zabezpečení SQL teď zahrnuje možnosti "zakázat pravidlo" (Preview).

Security Center obsahuje integrovanou kontrolu ohrožení zabezpečení, která vám umožní zjišťovat, sledovat a opravovat potenciální ohrožení zabezpečení databáze. Výsledky prověřování hodnocení vám poskytnou přehled o stavu zabezpečení počítačů s SQL a podrobností o všech zjištěních zabezpečení.

Pokud máte organizaci, kterou je třeba ignorovat, místo toho, aby ji bylo možné opravit, můžete ji případně zakázat. Zakázané závěry neovlivňují vaše zabezpečené skóre nebo generují nežádoucí hluk.

Další informace najdete v [zakažte konkrétní zjištění](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview).



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor sešitů integrovaných do Security Center a tří dodaných šablon

V rámci Ignite pružiny 2021 jsme v Security Center oznámili integrované prostředí Azure Monitor sešitům.

Novou integraci můžete využít k zahájení použití předem připravených šablon z Galerie Security Center. Pomocí šablon sešitu můžete získat přístup k dynamickým a vizuálním sestavám, abyste mohli sledovat stav zabezpečení vaší organizace. Kromě toho můžete vytvářet nové sešity na základě Security Centerch dat nebo jiných podporovaných datových typů a rychle nasazovat sešity komunity ze Security Center komunity GitHubu.

K dispozici jsou tři sestavy šablon:

- **Zabezpečení skóre v čase** – sledování skóre předplatných a změn doporučení pro vaše prostředky
- **Aktualizace systému** – zobrazení chybějících aktualizací systému podle prostředků, operačního systému, závažnosti a dalších
- **Zjištění ohrožení zabezpečení** – zobrazení výsledků kontroly ohrožení zabezpečení vašich prostředků Azure

Seznamte se s používáním těchto sestav nebo vytvářením vlastních [interaktivních sestav Security Centerch dat](custom-dashboards-azure-workbooks.md)s možností vytváření.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Sestava zabezpečeného skóre v čase":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Řídicí panel dodržování předpisů teď zahrnuje sestavy služby Azure audit (Preview).

Na panelu nástrojů řídicího panelu pro dodržování předpisů si teď můžete stáhnout sestavy certifikace pro Azure a Dynamics. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Panel nástrojů řídicí panel dodržování předpisů v legislativě":::

Můžete vybrat kartu pro příslušné typy sestav (PCI, SOC, ISO a další) a pomocí filtrů najít konkrétní sestavy, které potřebujete.

Přečtěte si další informace o [správě standardů na řídicím panelu dodržování předpisů](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="Filtrování seznamu dostupných sestav služby Azure audit":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Data doporučení se dají zobrazit v Azure Resource graphu s "prozkoumat v ARG".

Stránky s podrobnostmi o doporučení nyní obsahují tlačítko prozkoumat v ARG. Pomocí tohoto tlačítka můžete otevřít dotaz grafu prostředků Azure a prozkoumat, exportovat a sdílet data doporučení.

Azure Resource Graph (ARG) poskytuje okamžitý přístup k informacím o prostředcích napříč vašimi cloudovým prostředím pomocí robustních funkcí filtrování, seskupování a řazení. Jedná se o rychlý a efektivní způsob dotazování informací v rámci předplatných Azure prostřednictvím Azure Portal.

Přečtěte si další informace o [Azure Resource graphu (ARG)](../governance/resource-graph/index.yml).

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Prozkoumejte data doporučení v Azure Resource graphu.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Aktualizace zásad pro nasazení automatizace pracovního postupu

Automatizace procesů monitorování a reakce na incidenty vaší organizace může výrazně zlepšit dobu potřebnou k prošetření a zmírnění incidentů zabezpečení.

Poskytujeme tři Azure Policy zásady DeployIfNotExist, které vytvoří a nakonfigurují postupy automatizace pracovních postupů, abyste mohli automatizovat své automatizace ve vaší organizaci:

|Cíl  |Zásady  |ID zásady  |
|---------|---------|---------|
|Automatizace pracovního postupu pro výstrahy zabezpečení|[Nasazení automatizace pracovních postupů pro upozornění služby Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Automatizace pracovního postupu pro doporučení zabezpečení|[Nasazení automatizace pracovních postupů pro doporučení služby Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Automatizace pracovního postupu pro změny dodržování předpisů v legislativě|[Nasazení automatizace pracovních postupů pro Azure Security Center dodržování předpisů](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

K funkcím těchto zásad jsou k dispozici dvě aktualizace:

- Po přiřazení zůstanou povolené vynucením.
- Nyní můžete tyto zásady přizpůsobit a aktualizovat libovolné parametry, i když již byly nasazeny. Pokud například chce uživatel přidat další klíč hodnocení nebo upravit existující klíč hodnocení, může to provést.

Začínáme se [šablonami automatizace pracovních postupů](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Přečtěte si další informace o tom, jak [automatizovat odpovědi na triggery Security Center](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Dvě starší verze doporučení už Nezapisovat data přímo do protokolu aktivit Azure 

Security Center předá data pro téměř všechna doporučení zabezpečení, aby je Azure Advisor, která zase zapisuje do [protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md).

V případě dvou doporučení se data zapisují současně přímo do protokolu aktivit Azure. Tato změna Security Center zastaví zápis dat těchto starších doporučení zabezpečení přímo do protokolu aktivit. Místo toho data exportujeme, abychom Azure Advisori jako u všech dalších doporučení.

Existují dvě starší verze doporučení:
- Na vašich počítačích by se měly vyřešit problémy stavu aplikace Endpoint Protection.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno

Pokud jste přistupovali k informacím o těchto dvou doporučeních v kategorii "doporučení typu TaskDiscovery" protokolu aktivit, nebude už k dispozici.


### <a name="recommendations-page-enhancements"></a>Vylepšení stránky doporučení 

Vypnuli jsme vylepšenou verzi seznamu doporučení, abychom mohli na první pohled zobrazit další informace.

Nyní na stránce uvidíte:

1. Maximální skóre a aktuální skóre pro jednotlivé ovládací prvky zabezpečení.
1. Ikony, které nahrazují značky, jako je **Rychlá oprava** a **verze Preview**
1. Nový sloupec, který ukazuje [iniciativu k zásadám](security-policy-concept.md) souvisejícím s jednotlivými doporučeními – viditelné, pokud je zakázaná možnost seskupit podle ovládacích prvků.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Vylepšení stránky s doporučeními pro Azure Security Center – březen 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Vylepšení Azure Security Centerch doporučení &quot;Flat&quot; list-březen 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Další informace najdete v [doporučeních pro zabezpečení v Azure Security Center](security-center-recommendations.md).


## <a name="february-2021"></a>Únor 2021

Aktualizace v únoru zahrnují:

- [Nová stránka výstrah zabezpečení v Azure Portal vydaná pro obecnou dostupnost (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Doporučení pro ochranu úloh Kubernetes vydaná pro obecnou dostupnost (GA)](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Microsoft Defender pro integraci koncových bodů s Azure Defenderem teď podporuje Windows Server 2019 a virtuální plochu Windows 10 (WVD) (ve verzi Preview).](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Přímá vazba na zásadu ze stránky s podrobnostmi o doporučení](#direct-link-to-policy-from-recommendation-details-page)
- [Doporučení klasifikace dat SQL už nemá vliv na vaše zabezpečené skóre.](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Automatizace pracovního postupu se můžou aktivovat změnami pro vyhodnocení dodržování předpisů v legislativě (ve verzi Preview).](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Vylepšení stránky inventáře prostředků](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Nová stránka výstrah zabezpečení v Azure Portal vydaná pro obecnou dostupnost (GA)

Přenavrhli jsme stránku výstrahy zabezpečení Azure Security Center, aby poskytovala tyto:

- **Vylepšené možnosti třídění výstrah** – pomáhají snížit únavu výstrah a soustředit se na nejrelevantnější hrozby, a to díky tomu, že seznam obsahuje přizpůsobitelné filtry a možnosti seskupení.
- **Další informace najdete v seznamu výstrah** , například MITRE ATT&ACK taktiku.
- **Tlačítko pro vytvoření ukázkových upozornění** – k vyhodnocení možností Azure Defenderu a otestování výstrah. konfigurace (pro integraci SIEM, e-mailová oznámení a automatizace pracovních postupů) můžete vytvářet ukázkové výstrahy ze všech plánů Azure Defenderu.
- **Sbližování s incidentem funkce Sentinel Azure** – pro zákazníky, kteří používají oba produkty, je teď mezi nimi přepínání mezi nimi jednodušší a je snadné se o jeden další.
- **Lepší výkon** u rozsáhlých seznamů výstrah.
- **Navigace pomocí klávesnice** v seznamu výstrah.
- **Výstrahy z Azure Resource graphu** – pro všechny vaše prostředky můžete zadávat dotazy na výstrahy v Azure Resource graphu, Kusto jako rozhraní API. To je užitečné také při vytváření vlastních řídicích panelů výstrah. [Přečtěte si další informace o Azure Resource graphu](../governance/resource-graph/index.yml).
- **Vytvoření ukázkové** výstrahy – Pokud chcete vytvořit ukázkové výstrahy z nového prostředí výstrah, přečtěte si téma [Generování ukázkových výstrah v Azure Defenderu](security-center-alert-validation.md#generate-sample-azure-defender-alerts).

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Seznam výstrah zabezpečení Azure Security Center":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Doporučení pro ochranu úloh Kubernetes vydaná pro obecnou dostupnost (GA)

S radostí oznamujeme obecnou dostupnost sady doporučení pro Kubernetes ochrany úloh.

Aby se zajistilo, že Kubernetes úlohy jsou zabezpečené ve výchozím nastavení, Security Center přidaná doporučení pro posílení úrovně Kubernetes, včetně možností vynucení s řízením přístupu Kubernetes.

Když je v clusteru AKS (Azure Kubernetes Service) nainstalovaná Azure Policy doplněk pro Kubernetes, všechny požadavky na server rozhraní Kubernetes API se budou monitorovat proti předdefinované sadě osvědčených postupů, které se zobrazují jako 13 doporučení zabezpečení – před trvalým pokračováním do clusteru. Pak můžete nakonfigurovat, aby vynutil osvědčené postupy a nakonfigurovaly je pro budoucí úlohy.

Můžete například stanovit, že by se neměly vytvářet privilegované kontejnery a všechny budoucí požadavky tak budou zablokovány.

Další informace najdete v článku [osvědčené postupy ochrany úloh pomocí řízení přístupu Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> I když se doporučení zobrazovala ve verzi Preview, nespustila není v pořádku, takže se nezahrnuly do výpočtů vašeho zabezpečeného skóre. s tímto oznámením GA budou součástí výpočtu skóre. Pokud jste to ještě neudělali, může to vést ke mírnému dopadu na vaše zabezpečené skóre. Opravte je všude, kde je to možné, jak je popsáno v tématu o [opravách doporučení v Azure Security Center](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Microsoft Defender pro integraci koncových bodů s Azure Defenderem teď podporuje Windows Server 2019 a virtuální plochu Windows 10 (WVD) (ve verzi Preview).

Microsoft Defender pro koncové body je holistický, cloudové řešení zabezpečení koncového bodu. Poskytuje řízení a hodnocení ohrožení zabezpečení založené na rizicích a také zjišťování a odpověď koncového bodu (EDR). Úplný seznam výhod používání programu Defender pro koncový bod spolu s Azure Security Center najdete v tématu [Ochrana koncových bodů pomocí integrovaného řešení EDR Security Center: Microsoft Defender pro koncový bod](security-center-wdatp.md).

Pokud povolíte Azure Defender pro servery na Windows serveru, součástí plánu je licence k programu Defender pro koncový bod. Pokud jste už Azure Defender povolili pro servery a máte ve svém předplatném servery Windows 2019, automaticky obdrží v rámci této aktualizace Defender pro koncový bod. Není vyžadována žádná ruční akce. 

Podpora se teď rozšířila tak, aby zahrnovala Windows Server 2019 a [virtuální plochu Windows (WVD)](../virtual-desktop/overview.md).

> [!NOTE]
> Pokud povolujete Defender pro koncový bod na počítači s Windows serverem 2019, ujistěte se, že splňuje požadavky popsané v tématu [Povolení integrace programu Microsoft Defender pro koncové body](security-center-wdatp.md#enabling-the-microsoft-defender-for-endpoint-integration).

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Přímá vazba na zásadu ze stránky s podrobnostmi o doporučení

Při prohlížení podrobností doporučení je často užitečné, abyste mohli zobrazit základní zásady. Pro každé doporučení, které zásady podporuje, je k dispozici nový odkaz na stránce s podrobnostmi o doporučení:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Odkaz na stránku Azure Policy pro konkrétní zásadu podporující doporučení":::

Pomocí tohoto odkazu můžete zobrazit definici zásady a zkontrolovat logiku hodnocení. 

Pokud si prohlédnete seznam doporučení v našich [doporučeních o zabezpečení](recommendations-reference.md), přečtěte si také odkazy na stránky definice zásad:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Přístup na stránku Azure Policy pro konkrétní zásadu přímo z referenční stránky Azure Security Center doporučení" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Doporučení klasifikace dat SQL už nemá vliv na vaše zabezpečené skóre.
Doporučení **citlivá data v databázích SQL by měla být klasifikována** už neovlivní vaše zabezpečené skóre. Toto je jediné doporučení v ovládacím prvku zabezpečení **klasifikace dat** , aby měl ovládací prvek nyní hodnotu zabezpečeného skóre 0.

Úplný seznam všech ovládacích prvků zabezpečení v Security Center spolu s jejich hodnocením a seznamem doporučení v jednotlivých tématech najdete v tématu [ovládací prvky zabezpečení a jejich doporučení](secure-score-security-controls.md#security-controls-and-their-recommendations).

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Automatizace pracovního postupu se můžou aktivovat změnami pro vyhodnocení dodržování předpisů v legislativě (ve verzi Preview).
Do možností triggeru pro vaše automatizované pracovní postupy jsme přidali třetí datový typ: změny v protestech dodržování předpisů v legislativních prostředích.

Naučte se používat nástroje pro automatizaci pracovních postupů v [automatizaci odpovědí na Security Center triggery](workflow-automation.md).

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Použití změn pro vyhodnocení dodržování předpisů v legislativě pro aktivaci automatizace pracovního postupu" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Vylepšení stránky inventáře prostředků
Security Center stránku inventáře assetů se vylepšili následujícími způsoby:

- Souhrny v horní části stránky teď zahrnují **neregistrované odběry**, které zobrazují počet předplatných bez povoleného Security Center.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Počet neregistrovaných předplatných v souhrnech v horní části stránky inventáře prostředků":::

- Filtry se rozšířily a vylepšily, aby zahrnovaly:
    - **Počty** – každý filtr prezentuje počet prostředků, které splňují kritéria každé kategorie.

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Počty ve filtrech na stránce inventáře assetů Azure Security Center":::

    - **Obsahuje filtr výjimek** (volitelné) – zúžení výsledků na prostředky s neobdrženými výjimkami. Tento filtr se ve výchozím nastavení nezobrazuje, ale je přístupný z tlačítka **Přidat filtr** .

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Přidání filtru &quot;obsahuje výjimku&quot; na stránce Azure Security Center inventáře prostředků prostředků":::

Přečtěte si další informace o tom [, jak prozkoumat a spravovat prostředky pomocí inventáře prostředků](asset-inventory.md).

## <a name="january-2021"></a>Leden 2021

Aktualizace v lednu zahrnují:

- [Test zabezpečení Azure je teď výchozí iniciativou zásad pro Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Posouzení ohrožení zabezpečení pro místní a více cloudových počítačů se uvolní pro obecnou dostupnost (GA).](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [Ve verzi Preview je teď k dispozici zabezpečené skóre pro skupiny pro správu.](#secure-score-for-management-groups-is-now-available-in-preview)
- [Pro obecnou dostupnost se vydává rozhraní API pro stanovení skóre zabezpečení (GA).](#secure-score-api-is-released-for-general-availability-ga)
- [Dangling ochrany DNS do Azure Defenderu pro App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Vícenásobné cloudové konektory se uvolňují pro obecnou dostupnost (GA).](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Vyloučit celá doporučení ze zabezpečeného skóre pro předplatná a skupiny pro správu](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Uživatelé teď můžou vyžádat viditelnost v rámci tenanta od svého globálního správce.](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [35 doporučení verze Preview pro zvýšení pokrytí srovnávacího testu zabezpečení Azure](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [Export filtrovaných seznamů doporučení CSV](#csv-export-of-filtered-list-of-recommendations)
- [Prostředky, které se nepoužívají, teď v Azure Policy posouzení hlásí jako "kompatibilní".](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Exportovat týdenní snímky bezpečných bodů a dat dodržování předpisů pomocí průběžného exportu (Preview)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Test zabezpečení Azure je teď výchozí iniciativou zásad pro Azure Security Center

Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy na základě běžných architektur dodržování předpisů, které jsou specifické pro Azure. Toto široce uznávané sestavování srovnávacích testů v ovládacích prvcích z [centra pro Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) a [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) se soustředí na zabezpečení zaměřené na Cloud.

V posledních měsících se Security Center seznamu předdefinovaných doporučení pro zabezpečení významně rozšířila naše pokrytí tohoto srovnávacího testu.

V této verzi je srovnávací test základem pro doporučení Security Center a plně integrovaný jako výchozí iniciativa zásad. 

Všechny služby Azure mají v dokumentaci stránku standardních hodnot zabezpečení. [Jedná se například o směrný plán Security Center](security-baseline.md). Tyto směrné plány jsou postavené na základě srovnávacího testu zabezpečení Azure.

Pokud používáte řídicí panel dodržování předpisů pro Security Center, uvidíte dvě instance srovnávacího testu během přechodného období:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Řídicí panel dodržování předpisů pro Azure Security Center, který ukazuje srovnávací test zabezpečení Azure":::

Stávající doporučení nejsou ovlivněná a při zvětšování srovnávacích testů se změny automaticky projeví v Security Center. 

Další informace najdete na následujících stránkách:

- [Další informace o testu zabezpečení Azure](../security/benchmarks/introduction.md)
- [Přizpůsobení sady standardů na řídicím panelu dodržování předpisů](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Posouzení ohrožení zabezpečení pro místní a více cloudových počítačů se uvolní pro obecnou dostupnost (GA).

V říjnu jsme oznámili verzi Preview pro kontrolu serverů s podporou ARC Azure pomocí [Azure Defenderu pro](defender-for-servers-introduction.md)integrovaného posuzování ohrožení zabezpečení na serverech (používá se v Qualys).

Nově je vydaná pro obecnou dostupnost (GA).

Pokud jste povolili Azure ARC na počítačích mimo Azure, Security Center se bude nabízet k nasazení integrovaného skeneru ohrožení zabezpečení ručně a v rámci škálování.

V této aktualizaci si můžete vysílat výkon **Azure Defenderu pro servery** a konsolidovat svůj program pro správu ohrožení zabezpečení ve všech vašich prostředcích Azure i mimo Azure.

Hlavní možnosti:

- Monitorování stavu zřizování skeneru VA (zranitelnost) na počítačích ARC Azure
- Zřizování integrovaného agenta s rozhraním VA pro nechráněné počítače s Windows a Linuxem na platformě Azure ARC (ručně a v rozsahu)
- Přijímání a analýza zjištěných slabých míst z nasazených agentů (ručně a v rozsahu)
- Jednotné prostředí pro virtuální počítače Azure a počítače s obloukem Azure ARC

[Přečtěte si další informace o nasazení integrovaného skeneru ohrožení zabezpečení do hybridních počítačů](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Přečtěte si další informace o serverech s podporou ARC Azure](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>Ve verzi Preview je teď k dispozici zabezpečené skóre pro skupiny pro správu.

Stránka bezpečné skóre nyní zobrazuje agregovaná zabezpečená skóre pro skupiny pro správu kromě úrovně předplatného. Takže teď můžete zobrazit seznam skupin pro správu ve vaší organizaci a skóre pro každou skupinu pro správu.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Zobrazení bezpečných skóre pro skupiny pro správu.":::

Přečtěte si další informace o [zabezpečeném řízení hodnocení a zabezpečení v Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Pro obecnou dostupnost se vydává rozhraní API pro stanovení skóre zabezpečení (GA).

K vašemu skóre teď můžete přistupovat prostřednictvím [rozhraní API pro zabezpečené skóre](/rest/api/securitycenter/securescores/). Metody rozhraní API poskytují flexibilitu pro dotazování na data a vytváření vlastních mechanismů generování sestav v rámci vašich zabezpečených výsledků v průběhu času. Například:

- použití rozhraní API pro **zabezpečení skóre** k získání skóre pro konkrétní předplatné
- použití rozhraní API pro **řízení zabezpečeného skóre** k vypsání ovládacích prvků zabezpečení a aktuálního skóre vašich předplatných

Přečtěte si o externích nástrojích, které jsou možné díky rozhraní API pro bezpečné skóre v [oblasti zabezpečeného hodnocení naší komunity GitHubu](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Přečtěte si další informace o [zabezpečeném řízení hodnocení a zabezpečení v Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Dangling ochrany DNS do Azure Defenderu pro App Service

Převzetí subdomény jsou společnou vysoce závažnou hrozbou pro organizace. K převzetí subdomény může dojít, když máte záznam DNS, který odkazuje na oddaný Web. Tyto záznamy DNS se také označují jako položky DNS dangling. Záznamy CNAME jsou pro tuto hrozbu obzvláště zranitelné. 

Převzetí subdomény umožňuje aktérům hrozeb přesměrovat provoz určený pro doménu organizace na lokalitu, která provádí škodlivou aktivitu.

Azure Defender pro App Service nyní detekuje položky DNS dangling při vyřazení webu z provozu App Service. To je okamžik, kdy položka DNS odkazuje na neexistující prostředek, a váš web je zranitelný při převzetí subdoménou. Tyto ochrany jsou dostupné bez ohledu na to, jestli jsou vaše domény spravované pomocí Azure DNS nebo externího registrátora domény a platí pro App Service ve Windows i App Service v systému Linux.

Další informace:

- [Tabulka referenčních informací o výstrahách App Service](alerts-reference.md#alerts-azureappserv) – obsahuje dvě nové výstrahy Azure Defenderu, které se aktivují, když se zjistí položka DNS dangling.
- [Zabránit záznamům DNS v dangling a vyhnout se převzetí subdomény](../security/fundamentals/subdomain-takeover.md) – Přečtěte si o hrozbách převzetí subdomény a aspektu DNS dangling.
- [Seznámení s Azure Defenderem pro App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Vícenásobné cloudové konektory se uvolňují pro obecnou dostupnost (GA).

Cloudové úlohy běžně pokrývá několik cloudových platforem, ale cloudové služby zabezpečení musí provádět stejné.

Azure Security Center chrání úlohy v Azure, Amazon Web Services (AWS) a Google Cloud Platform (GCP).

Připojení účtů AWS nebo GCP integruje své nativní nástroje zabezpečení jako AWS Security Center a GCP Security Center do Azure Security Center.

Tato možnost znamená, že Security Center zajišťuje viditelnost a ochranu ve všech hlavních cloudových prostředích. Některé z výhod této integrace:

- Automatické zřizování agentů – Security Center pomocí ARC Azure nasadit agenta Log Analytics do instancí AWS
- Správa zásad
- Správa ohrožení zabezpečení
- Rozpoznání a odpověď vloženého koncového bodu (EDR)
- Zjišťování nezabezpečených konfigurací zabezpečení
- Jedno zobrazení, které zobrazuje doporučení pro zabezpečení ze všech poskytovatelů cloudu
- Zahrňte všechny vaše prostředky do výpočtů zabezpečeného skóre Security Center.
- Posouzení dodržování předpisů v rámci vašich AWS a GCP prostředků

V nabídce Security Center vyberte **multi cloudové konektory** a uvidíte možnosti pro vytváření nových konektorů:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Tlačítko Přidat účet AWS na stránce s více síťovými konektory v Security Center":::

Další informace:
- [Připojení účtů AWS k Azure Security Center](quickstart-onboard-aws.md)
- [Připojení účtů GCP k Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Vyloučit celá doporučení ze zabezpečeného skóre pro předplatná a skupiny pro správu

Rozšiřujeme možnost výjimky tak, aby obsahovala celá doporučení. Poskytuje další možnosti, které vám pomohou vyladit doporučení zabezpečení, která Security Center provede pro vaše předplatná, skupinu pro správu nebo prostředky.

V některých případech bude prostředek uveden jako špatný, pokud víte, že problém byl vyřešen nástrojem třetí strany, který Security Center nebyl zjištěn. Případně se v oboru, ve kterém se domníváte, že nepatří, se zobrazí doporučení. Doporučení může být pro konkrétní předplatné nevhodné. Nebo možná vaše organizace rozhodla přijmout rizika související s konkrétním prostředkem nebo doporučením.

Pomocí této funkce Preview teď můžete vytvořit výjimku pro doporučení:

- Zajistěte **výjimku prostředku** , abyste se ujistili, že v budoucnu není v pořádku, a neovlivní vaše zabezpečené skóre. Prostředek bude uveden jako nepoužitý a důvod bude zobrazen jako vyloučený s konkrétním odůvodněním, které vyberete.

- **Vyloučení předplatného nebo skupiny pro správu** , aby se zajistilo, že doporučení neovlivní vaše zabezpečené skóre a v budoucnu se nebude zobrazovat pro předplatné nebo skupinu pro správu. To se týká existujících prostředků a všech, které v budoucnu vytvoříte. Doporučení bude označeno konkrétním odůvodněním, které jste vybrali pro vybraný obor.

Další informace získáte v [informacích o výjimkách prostředků a doporučení ze zabezpečeného skóre](exempt-resource.md).



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Uživatelé teď můžou vyžádat viditelnost v rámci tenanta od svého globálního správce.

Pokud uživatel nemá oprávnění k zobrazení Security Center dat, uvidí teď odkaz na žádost o oprávnění od globálního správce organizace. Požadavek zahrnuje roli, kterou chcete, a odůvodnění, proč je to nezbytné.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner s informující uživatele, který může požádat o oprávnění na úrovni tenanta.":::

Další informace najdete v [žádosti o oprávnění na úrovni tenanta, pokud to není dostačující](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient).


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>35 doporučení verze Preview pro zvýšení pokrytí srovnávacího testu zabezpečení Azure

Srovnávací test zabezpečení Azure je výchozí iniciativou zásad v Azure Security Center. 

Pro zvýšení pokrytí tohoto srovnávacího testu byly do Security Center přidány následující doporučení 35 Preview.

> [!TIP]
> Doporučení pro verzi Preview negenerují prostředek v pořádku a nejsou zahrnutá do výpočtů vašeho zabezpečeného skóre. Opravte je všude, kde je to možné, takže až do doby, kdy období Preview skončí, přispějete k vašemu skóre. Přečtěte si další informace o tom, jak na tato doporučení reagovat v tématu o [opravách doporučení v Azure Security Center](security-center-remediate-recommendations.md).

| Řízení zabezpečení                     | Nová doporučení                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Povolit šifrování v klidovém umístění            | -Azure Cosmos DB účty by měly používat klíče spravované zákazníkem k šifrování neaktivních dat<br>– Azure Machine Learning pracovní prostory by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK)<br>– Zajistěte, aby byla pro servery MySQL povolená ochrana dat klíčů.<br>– Zajistěte, aby byla pro servery PostgreSQL povolená vlastní ochrana dat klíčů.<br>-Cognitive Services účty by měly povolit šifrování dat pomocí klíče spravovaného zákazníkem (CMK).<br>-Registry kontejneru by měly být zašifrované pomocí klíče spravovaného zákazníkem (CMK)<br>– Spravované instance SQL by měly používat klíče spravované zákazníkem k šifrování neaktivních dat<br>– SQL servery by měly používat klíče spravované zákazníky k šifrování neaktivních dat.<br>– Účty úložiště by měly pro šifrování používat klíč spravovaný zákazníkem (CMK)                                                                                                                                                              |
| Implementace osvědčených postupů zabezpečení    | – Odběry by měly mít kontaktní e-mailovou adresu pro problémy se zabezpečením<br> – Automatické zřizování agenta Log Analytics by mělo být ve vašem předplatném povolené.<br> -E-mailové oznámení pro výstrahy s vysokou závažností by mělo být povolené.<br> -E-mailové oznámení vlastníkovi předplatného pro upozornění s vysokou závažností by mělo být povolené.<br> – Trezory klíčů by měly mít povolenou ochranu vyprázdnění<br> – Trezory klíčů by měly mít povolené obnovitelné odstranění. |
| Správa přístupu a oprávnění        | – Aplikace Function app by měly mít povolené klientské certifikáty (příchozí klientské certifikáty). |
| Ochrana aplikací před útoky DDoS | – Brána Firewall webových aplikací (WAF) by měla být povolená pro Application Gateway<br> – Služba brány firewall webových aplikací (WAF) by měla být povolená pro službu Azure front-dveří |
| Omezit neautorizovaný přístup k síti | – Brána firewall by měla být povolená na Key Vault<br> – Privátní koncový bod by měl být nakonfigurovaný pro Key Vault<br> -Konfigurace aplikace by měla používat privátní odkaz<br> – Mezipaměť Azure pro Redis by se měla nacházet v rámci virtuální sítě.<br> -Azure Event Grid domény by měly používat privátní odkaz<br> -Azure Event Grid témata by měla používat privátní odkaz<br> -Azure Machine Learning pracovní prostory by měly používat privátní odkaz<br> – Služba signalizace Azure by měla používat privátní odkaz<br> – Jarní cloud Azure by měl používat vkládání ze sítě<br> – Registry kontejneru by neměly umožňovat neomezený přístup k síti.<br> -Registry kontejneru by měly používat privátní odkaz<br> – Přístup k veřejné síti by měl být pro MariaDB servery zakázaný.<br> – Přístup k veřejné síti by měl být pro servery MySQL zakázaný.<br> – Přístup k veřejné síti by měl být pro PostgreSQL servery zakázaný.<br> – Účet úložiště by měl používat připojení privátního odkazu.<br> – Účty úložiště by měly omezovat přístup k síti pomocí pravidel virtuální sítě<br> – Šablony pro sestavovatele imagí virtuálních počítačů by měly používat privátní odkaz|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Související odkazy:

- [Další informace o testu zabezpečení Azure](../security/benchmarks/introduction.md)
- [Další informace o Azure Database for MariaDB](../mariadb/overview.md)
- [Další informace o Azure Database for MySQL](../mysql/overview.md)
- [Další informace o Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>Export filtrovaných seznamů doporučení CSV 

V listopadu 2020 jsme přidali filtry na stránku doporučení ([teď seznam doporučení obsahuje filtry](#recommendations-list-now-includes-filters)). V prosinci jsme tyto filtry rozšířili ([Stránka doporučení obsahuje nové filtry pro prostředí, závažnost a dostupné odpovědi](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

V tomto oznámení měníme chování tlačítka **stáhnout do souboru CSV** , aby export sdíleného svazku clusteru zahrnoval jenom doporučení aktuálně zobrazená v seznamu filtrovaných. 

Například na obrázku níže vidíte, že seznam byl filtrován na dvě doporučení. Vygenerovaný soubor CSV obsahuje podrobnosti o stavu všech prostředků ovlivněných těmito dvěma doporučeními.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Export filtrovaných doporučení do souboru CSV":::

Další informace najdete v [doporučeních pro zabezpečení v Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Prostředky, které se nepoužívají, teď v Azure Policy posouzení hlásí jako "kompatibilní".

Dříve byly vyhodnoceny prostředky, které byly vyhodnoceny pro doporučení a v Azure Policy jako "nekompatibilní", **se objevily** jako nevyhovující. Žádné akce uživatele nemohly změnit stav na "kompatibilní". S touto změnou jsou pro lepší přehlednost hlášeny jako "vyhovující".

Jediný dopad bude zobrazen v Azure Policy, kde se zvýší počet odpovídajících prostředků. Vaše zabezpečené skóre v Azure Security Center nebude nijak ovlivněno.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Exportovat týdenní snímky bezpečných bodů a dat dodržování předpisů pomocí průběžného exportu (Preview)

Přidali jsme novou funkci Preview do nástrojů [průběžného exportu](continuous-export.md) pro export týdenních snímků zabezpečených bodů a dat dodržování předpisů pro legislativní účely.

Při definování průběžného exportu nastavte četnost exportu:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="Volba frekvence průběžného exportu":::

- **Streamování** – posouzení se pošle v reálném čase, když se aktualizuje stav prostředku (Pokud nedojde k žádným aktualizacím, nebudou se posílat žádná data).
- **Snímky** – snímek aktuálního stavu všech vyhodnocení dodržování předpisů v legislativě se pošle každý týden (Tato funkce je ve verzi Preview pro týdenní snímky zabezpečených výsledků a dat dodržování předpisů v legislativě).

Přečtěte si další informace o všech možnostech této funkce při [nepřetržitém exportu Security Center dat](continuous-export.md).

## <a name="december-2020"></a>Prosinec 2020

Aktualizace v prosinci zahrnují:

- [Azure Defender pro servery SQL na počítačích je všeobecně dostupný.](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Podpora Azure Defenderu pro Azure synapse Analytics vyhrazený fond SQL je všeobecně dostupná.](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Globální správci teď můžou udělovat oprávnění na úrovni tenanta.](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Dva nové plány Azure Defenderu: Azure Defender pro DNS a Azure Defender pro Správce prostředků (ve verzi Preview)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Stránka nové výstrahy zabezpečení v Azure Portal (Preview)](#new-security-alerts-page-in-the-azure-portal-preview)
- [ReSecurity Center v Azure SQL Database & spravované instanci SQL](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Aktualizace nástrojů inventáře prostředků a filtrů](#asset-inventory-tools-and-filters-updated)
- [Doporučení pro webové aplikace požadující certifikáty SSL už nejsou součástí zabezpečeného skóre.](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [Stránka s doporučeními obsahuje nové filtry pro prostředí, závažnost a dostupné odpovědi.](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Průběžný export získá nové datové typy a zlepší zásady deployifnotexist.](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender pro servery SQL na počítačích je všeobecně dostupný.

Azure Security Center nabízí dva plány Azure Defenderu pro SQL Server:

- **Azure Defender pro servery Azure SQL Database** – chrání vaše NATIVNÍ servery SQL Azure 
- **Azure Defender pro SQL servery na počítačích** – rozšiřuje stejnou ochranu na SQL servery v hybridních, cloudových a místních prostředích.

V tomto oznámení vám **Azure Defender pro SQL** teď chrání vaše databáze a data kdekoli, kde se nacházejí.

Azure Defender pro SQL zahrnuje funkce posouzení ohrožení zabezpečení. Nástroj pro posouzení ohrožení zabezpečení zahrnuje tyto pokročilé funkce:

- **Základní konfigurace** (novinka!) k inteligentně upřesnění výsledků kontroly ohrožení zabezpečení na ty, které mohou představovat reálné problémy se zabezpečením. Po vytvoření základního stavu zabezpečení nástroj pro posouzení ohrožení zabezpečení hlásí pouze odchylky od daného stavu. Výsledky, které se shodují se směrným plánem, se považují za následné prověřování. To umožňuje vašim a analytikům soustředit pozornost na to, kde záleží.
- **Podrobné informace o srovnávacích testech** , které vám pomohou *pochopit* zjištěná zjištění a proč se vztahují k vašim prostředkům.
- **Skripty pro nápravu** , které vám pomůžou zmírnit zjištěná rizika.

Přečtěte si další informace o [Azure Defenderu pro SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Podpora Azure Defenderu pro Azure synapse Analytics vyhrazený fond SQL je všeobecně dostupná.

Azure synapse Analytics (dříve SQL DW) je analytická služba, která kombinuje podnikové datové sklady a analýzu velkých objemů dat. Vyhrazené fondy SQL jsou funkce Azure synapse v podnikových datových skladech. Přečtěte si víc o [tom, co je Azure synapse Analytics (dřív SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Azure Defender pro SQL chrání vyhrazené fondy SQL pomocí:

- **Rozšířená ochrana před internetovými** útoky pro detekci hrozeb a útoků 
- **Možnosti posouzení ohrožení** zabezpečení při identifikaci a napravení chybných konfigurací zabezpečení

Podpora Azure Defenderu pro fondy SQL služby Azure synapse Analytics je automaticky přidaná do sady Azure SQL databases v Azure Security Center. V Azure Portal najdete novou kartu Azure Defender for SQL na stránce pracovního prostoru synapse.

Přečtěte si další informace o [Azure Defenderu pro SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Globální správci teď můžou udělovat oprávnění na úrovni tenanta.

Uživatel s rolí Azure Active Directory **globálního správce** může mít odpovědnost v rámci tenanta, ale nemá oprávnění Azure zobrazovat informace o celé organizaci v Azure Security Center. 

Pokud chcete přiřadit oprávnění na úrovni tenanta, postupujte podle pokynů v tématu [udělení oprávnění pro všechny klienty](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself).


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Dva nové plány Azure Defenderu: Azure Defender pro DNS a Azure Defender pro Správce prostředků (ve verzi Preview)

Přidali jsme dvě nové možnosti ochrany před hrozbami v cloudu pro vaše prostředí Azure.

Tyto nové ochrany významně zvyšují odolnost proti útokům z aktérů hrozeb a významně zvyšují počet prostředků Azure chráněných pomocí Azure Defenderu.

- **Azure Defender pro správce prostředků** – automaticky monitoruje všechny operace správy prostředků provedené ve vaší organizaci. Další informace naleznete v tématu:
    - [Seznámení s Azure Defenderem pro Správce prostředků](defender-for-resource-manager-introduction.md)
    - [Reakce na upozornění Azure Defenderu pro Resource Manager](defender-for-resource-manager-usage.md)
    - [Seznam výstrah, které poskytuje Azure Defender pro Správce prostředků](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender pro DNS** – nepřetržitě monitoruje všechny dotazy DNS z vašich prostředků Azure. Další informace naleznete v tématu:
    - [Seznámení s Azure Defenderem pro DNS](defender-for-dns-introduction.md)
    - [Reakce na upozornění Azure Defenderu pro DNS](defender-for-dns-usage.md)
    - [Seznam upozornění poskytovaných službou Azure Defender pro DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Stránka nové výstrahy zabezpečení v Azure Portal (Preview)

Přenavrhli jsme stránku výstrahy zabezpečení Azure Security Center, aby poskytovala tyto:

- **Vylepšené možnosti třídění výstrah** – pomáhají snížit únavu výstrah a soustředit se na nejrelevantnější hrozby, a to díky tomu, že seznam obsahuje přizpůsobitelné filtry a možnosti seskupení.
- **Další informace najdete v seznamu výstrah** , například MITRE ATT&ACK taktiku
- **Tlačítko pro vytvoření ukázkových upozornění** – k vyhodnocení možností Azure Defenderu a otestování konfigurace výstrah (pro integraci Siem, e-mailová oznámení a automatizace pracovních postupů) můžete vytvářet ukázkové výstrahy ze všech plánů Azure Defenderu.
- **Přizpůsobení s incidentem funkce Sentinel Azure** – pro zákazníky, kteří používají oba produkty, je teď mezi nimi přepínání mezi nimi jednodušší a je snadné ho snadno učit.
- **Lepší výkon** pro velké seznamy výstrah
- **Navigace pomocí klávesnice** v seznamu výstrah
- **Výstrahy z Azure Resource graphu** – pro všechny vaše prostředky můžete zadávat dotazy na výstrahy v Azure Resource graphu, Kusto jako rozhraní API. To je užitečné také při vytváření vlastních řídicích panelů výstrah. [Přečtěte si další informace o Azure Resource graphu](../governance/resource-graph/index.yml).

Pro přístup k novému prostředí použijte odkaz vyzkoušet si ho z banneru v horní části stránky výstrahy zabezpečení.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banner s odkazem na nové prostředí upozornění ve verzi Preview":::

Pokud chcete vytvořit ukázkové výstrahy z nového prostředí výstrah, přečtěte si téma [Generování ukázkových výstrah v Azure Defenderu](security-center-alert-validation.md#generate-sample-azure-defender-alerts).


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>ReSecurity Center v Azure SQL Database & spravované instanci SQL 

Prostředí Security Center v rámci SQL poskytuje přístup k následujícím Security Center a Azure Defenderu pro funkce SQL:

- **Doporučení zabezpečení** – Security Center pravidelně analyzuje stav zabezpečení všech připojených prostředků Azure pro identifikaci potenciálních konfigurací zabezpečení. Pak poskytuje doporučení k nápravě těchto ohrožení zabezpečení a zlepšování zabezpečení stav organizací.
- **Výstrahy zabezpečení** – služba zjišťování, která nepřetržitě monitoruje aktivity Azure SQL pro hrozby, jako jsou například injektáže SQL, útoky hrubou silou a zneužití oprávnění. Tato služba aktivuje podrobné výstrahy zabezpečení a pro akce zaměřené na akce v Security Center a poskytuje možnosti pro pokračování v šetření s ověřováním Azure Sentinel a Azure-Native SIEM řešení Microsoftu.
- **Zjištění** – služba posouzení ohrožení zabezpečení, která nepřetržitě monitoruje konfigurace Azure SQL a pomáhá napravit ohrožení zabezpečení. Kontroly Hodnocení poskytují přehled o stavech zabezpečení Azure SQL společně s podrobnými poznatky o zabezpečení.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="Funkce zabezpečení Azure Security Center pro SQL jsou dostupné v rámci Azure SQL.":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Aktualizace nástrojů inventáře prostředků a filtrů

Stránka inventáře v Azure Security Center byla aktualizována pomocí následujících změn:

- **Příručky a zpětná vazba** přidaná na panel nástrojů. Otevře se podokno s odkazy na související informace a nástroje. 
- **Filtr předplatných** je přidaný k výchozím filtrům dostupným pro vaše prostředky.
- **Otevře** odkaz na dotaz pro otevření aktuálních možností filtru jako dotaz na graf prostředků Azure (dříve se nazýval zobrazení v Průzkumníku grafu prostředků).
- **Možnosti operátoru** pro každý filtr. Nyní můžete zvolit z více logických operátorů kromě ' = '. Můžete například chtít najít všechny prostředky s aktivními doporučeními, jejichž názvy obsahují řetězec "Encrypt". 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Ovládací prvky pro možnost operátora ve filtrech inventáře prostředků":::

Přečtěte si další informace o inventáři v [průzkumu a správě vašich prostředků pomocí inventáře assetů](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Doporučení pro webové aplikace požadující certifikáty SSL už nejsou součástí zabezpečeného skóre.

Doporučení "webové aplikace by měly požádat o certifikát SSL pro všechny příchozí požadavky", které byly přesunuty z řízení zabezpečení **spravovat přístup a oprávnění** (maximálně 4 PTS) do **implementace osvědčených postupů zabezpečení** (což znamená, že nejsou žádné body). 

Zajištění toho, aby webová aplikace požádala o certifikát, je bezpečnější. Pro veřejné webové aplikace však není důležité. Pokud k webu přistupujete přes HTTP a ne HTTPS, nebudete dostávat žádné klientské certifikáty. Takže pokud vaše aplikace vyžaduje klientské certifikáty, neměli byste žádosti do vaší aplikace povolovat přes HTTP. Další informace najdete v [konfiguraci vzájemného ověřování TLS pro Azure App Service](../app-service/app-service-web-configure-tls-mutual-auth.md).

V této změně se teď doporučuje doporučit doporučený postup, který nemá na vaše skóre vliv. 

Seznamte se s doporučeními v jednotlivých ovládacích [prvcích zabezpečení v ovládacích prvcích zabezpečení a jejich doporučeních](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>Stránka s doporučeními obsahuje nové filtry pro prostředí, závažnost a dostupné odpovědi.

Azure Security Center monitoruje všechny připojené prostředky a generuje doporučení zabezpečení. Tato doporučení použijte k posílení svého hybridního cloudového stav a sledování dodržování zásad a standardů, které jsou relevantní pro vaši organizaci, odvětví a zemi.

Jak Security Center nadále rozšiřují jeho pokrytí a funkce, seznam doporučení zabezpečení se každý měsíc zvětšuje. Podívejte se například [na 29 doporučených doporučení verze Preview a zvyšte pokrytí srovnávacího testu zabezpečení Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Díky rostoucímu seznamu je potřeba vyfiltrovat doporučení a najít ty z největších zájmů. V listopadu jsme přidali filtry na stránku doporučení (viz [seznam doporučení teď obsahuje filtry](#recommendations-list-now-includes-filters)).

Filtry přidané tento měsíc poskytují možnosti pro upřesnění seznamu doporučení podle:

- **Prostředí** – zobrazení doporučení pro AWS, GCP nebo prostředky Azure (nebo jakékoli kombinace)
- **Závažnost** – zobrazení doporučení podle klasifikace závažnosti nastavené nástrojem Security Center
- **Akce odpovědi** – zobrazit doporučení podle dostupnosti Security Center možností odpovědi: Rychlá oprava, zamítnutí a vysazení

    > [!TIP]
    > Filtr akcí odpovědí nahrazuje filtr **Rychlá oprava k dispozici (ano/ne)** . 
    > 
    > Přečtěte si další informace o každé z těchto možností odpovědi:
    > - [Rychlá oprava nápravy](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Jak zabránit chybným konfiguracím s využitím doporučení k vynucení nebo zamítnutí](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Doporučení seskupená podle řízení zabezpečení" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Průběžný export získá nové datové typy a zlepší zásady deployifnotexist.

Nástroje pro průběžné exporty Azure Security Center umožňují exportovat doporučení Security Center a výstrahy pro použití s dalšími nástroji pro monitorování ve vašem prostředí.

Průběžný export umožňuje plně přizpůsobit, co se bude exportovat a kde se bude přecházet. Úplné podrobnosti najdete v tématu  [průběžné exportování dat Security Center](continuous-export.md).

Tyto nástroje se vylepšily a rozšířily následujícími způsoby:

- **Vylepšené zásady Deployifnotexist průběžného exportu**. Zásady teď:

    - **Ověřte, zda je konfigurace povolena.** Pokud tomu tak není, zásada se zobrazí jako nedodržující předpisy a vytvoří vyhovující prostředek. Další informace o dodaných šablonách Azure Policy najdete v části nasazení se škálováním na kartě Azure Policy v tématu [Nastavení průběžného exportu](continuous-export.md#set-up-a-continuous-export).

    - **Podpora exportu zjištění zabezpečení.** Při použití šablon Azure Policy můžete nakonfigurovat průběžný export, aby obsahoval nálezy. To je relevantní při exportu doporučení, která mají doporučení "sub", jako jsou nálezy ze skenerů posouzení ohrožení zabezpečení nebo specifické aktualizace systému pro doporučení "nadřazená" doporučení "by měly být nainstalovány na počítačích".
    
    - **Podpora exportu zabezpečených dat skóre.**

- **Byla přidána data vyhodnocení dodržování předpisů pro úpravy (ve verzi Preview).** Nyní můžete průběžně exportovat aktualizace posouzení dodržování předpisů, včetně pro jakékoli vlastní iniciativy, do pracovního prostoru Log Analytics nebo centra událostí. Tato funkce není k dispozici pro národní cloudy/svrchované.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="Možnosti pro zahrnutí informací o hodnocení vyhovujících předpisům pro průběžný export dat.":::


## <a name="november-2020"></a>Listopad 2020

Aktualizace v listopadu zahrnují:

- [29 doporučení verze Preview pro zvýšení pokrytí srovnávacích testů zabezpečení Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 přidáno do řídicího panelu dodržování předpisů pro Security Center](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Seznam doporučení nyní obsahuje filtry](#recommendations-list-now-includes-filters)
- [Vylepšené a rozšířené prostředí pro Automatické zřizování](#auto-provisioning-experience-improved-and-expanded)
- [Pro průběžný export je teď dostupné zabezpečené skóre (Preview).](#secure-score-is-now-available-in-continuous-export-preview)
- ["Na vaše počítače by se měly nainstalovat aktualizace systému." teď obsahuje doporučení](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Stránka správy zásad v Azure Portal nyní zobrazuje stav přiřazení výchozích zásad](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 doporučení verze Preview pro zvýšení pokrytí srovnávacích testů zabezpečení Azure

Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy, které se týkají zabezpečení a dodržování předpisů v závislosti na běžných architekturách dodržování předpisů. [Další informace o srovnávacím testu zabezpečení Azure](../security/benchmarks/introduction.md)

Následující 29 doporučení pro verzi Preview jsme přidali do Security Center, abyste zvýšili pokrytí tohoto srovnávacího testu.

Doporučení pro verzi Preview negenerují prostředek v pořádku a nejsou zahrnutá do výpočtů vašeho zabezpečeného skóre. Opravte je všude, kde je to možné, takže až do doby, kdy období Preview skončí, přispějete k vašemu skóre. Přečtěte si další informace o tom, jak na tato doporučení reagovat v tématu o [opravách doporučení v Azure Security Center](security-center-remediate-recommendations.md).

| Řízení zabezpečení                     | Nová doporučení                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Šifrovat data při přenosu              | – Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL.<br>– Pro databázové servery MySQL by mělo být povoleno připojení SSL.<br>-TLS by se měl aktualizovat na nejnovější verzi vaší aplikace API<br>-TLS by se měl aktualizovat na nejnovější verzi vaší aplikace Function App<br>-Protokol TLS by se měl aktualizovat na nejnovější verzi vaší webové aplikace.<br>– FTPS by mělo být vyžadováno v aplikaci API<br>– FTPS by mělo být vyžadováno ve vaší aplikaci Function App<br>– FTPS by mělo být vyžadováno ve vaší webové aplikaci                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Správa přístupu a oprávnění        | – Webové aplikace by měly požádat o certifikát SSL pro všechny příchozí požadavky<br>-V aplikaci API by se měla použít spravovaná identita.<br>– Ve vaší aplikaci Function app by se měla použít spravovaná identita<br>– Ve vaší webové aplikaci by se měla použít spravovaná identita.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Omezit neautorizovaný přístup k síti | – Privátní koncový bod by měl být povolený pro PostgreSQL servery.<br>– Privátní koncový bod by měl být povolený pro MariaDB servery.<br>– Privátní koncový bod by měl být povolený pro servery MySQL.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Povolit auditování a protokolování          | -Protokoly diagnostiky v App Services by měly být povolené                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Implementace osvědčených postupů zabezpečení    | -Azure Backup by měly být povolené pro virtuální počítače<br>– Geograficky redundantní záloha by měla být povolená pro Azure Database for MariaDB<br>– Geograficky redundantní záloha by měla být povolená pro Azure Database for MySQL<br>– Geograficky redundantní záloha by měla být povolená pro Azure Database for PostgreSQL<br>-PHP by se mělo aktualizovat na nejnovější verzi vaší aplikace API<br>– PHP by se mělo aktualizovat na nejnovější verzi vaší webové aplikace<br>– Java by se měl aktualizovat na nejnovější verzi vaší aplikace API<br>– Java by se měl aktualizovat na nejnovější verzi aplikace Function App<br>– Java by se měl aktualizovat na nejnovější verzi vaší webové aplikace<br>– Python by se měl aktualizovat na nejnovější verzi vaší aplikace API<br>– Python by se měl aktualizovat na nejnovější verzi vaší aplikace Function App<br>– Python by se měl aktualizovat na nejnovější verzi vaší webové aplikace<br>– Uchovávání auditu pro SQL servery by mělo být nastavené aspoň na 90 dní. |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Související odkazy:

- [Další informace o testu zabezpečení Azure](../security/benchmarks/introduction.md)
- [Další informace o Azure API Apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Další informace o aplikacích Azure Function App](../azure-functions/functions-overview.md)
- [Další informace o službě Azure Web Apps](../app-service/overview.md)
- [Další informace o Azure Database for MariaDB](../mariadb/overview.md)
- [Další informace o Azure Database for MySQL](../mysql/overview.md)
- [Další informace o Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 přidáno do řídicího panelu dodržování předpisů pro Security Center

Standard NIST SP 800-171 R2 je teď k dispozici jako integrovaná iniciativa pro použití s řídicím panelem dodržování předpisů Azure Security Center. Mapování pro ovládací prvky jsou popsána v [podrobnostech o integrované iniciativě NIST SP 800-171 R2 dodržování předpisů](../governance/policy/samples/nist-sp-800-171-r2.md). 

Pokud chcete použít standard pro vaše předplatná a průběžně monitorovat svůj stav dodržování předpisů, postupujte podle pokynů v tématu [přizpůsobení sady standardů na řídicím panelu dodržování předpisů](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="NIST SP 800 171 R2 Standard ve Security Center řídicím panelu dodržování předpisů v legislativě":::

Další informace o tomto standardu dodržování předpisů najdete v tématu [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final).


### <a name="recommendations-list-now-includes-filters"></a>Seznam doporučení nyní obsahuje filtry

Seznam doporučení zabezpečení teď můžete filtrovat podle rozsahu kritérií. V následujícím příkladu je seznam doporučení filtrovaný tak, aby zobrazoval doporučení, která:

- jsou **všeobecně dostupné** (tj. není ve verzi Preview)
- jsou pro **účty úložiště**
- podpora **opravy rychlých oprav**

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filtry pro seznam doporučení":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>Vylepšené a rozšířené prostředí pro Automatické zřizování

Funkce automatického zřizování pomáhá snižovat režijní náklady díky instalaci požadovaných rozšíření na nové a stávající virtuální počítače Azure, aby mohla těžit z ochrany Security Center. 

Jak Azure Security Center roste, vyvinuly se další rozšíření a Security Center můžou monitorovat větší seznam typů prostředků. Nástroje pro Automatické zřizování se teď rozšířily tak, aby podporovaly další rozšíření a typy prostředků, a to díky využití možností Azure Policy.

Teď můžete nakonfigurovat automatické zřizování pro:

- Agent Log Analytics
- New Doplněk Azure Policy pro Kubernetes
- New Microsoft Dependency Agent

Další informace o [automatických zřizováních agentů a rozšíření od Azure Security Center](security-center-enable-data-collection.md).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Pro průběžný export je teď dostupné zabezpečené skóre (Preview).

Díky průběžnému exportu zabezpečeného skóre můžete streamovat změny skóre v reálném čase do Azure Event Hubs nebo do pracovního prostoru Log Analytics. Tuto možnost použijte k těmto akcím:

- sledování zabezpečeného skóre v průběhu času pomocí dynamických sestav
- Export zabezpečeného data skóre do Azure Sentinel (nebo jakékoli jiné SIEM)
- Integrujte tato data se všemi procesy, které už možná používáte k monitorování zabezpečeného skóre ve vaší organizaci.

Přečtěte si další informace o [průběžném exportu Security Center dat](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>"Na vaše počítače by se měly nainstalovat aktualizace systému." teď obsahuje doporučení

**Aktualizace systému by se měly nainstalovat na vaše doporučení vašich počítačů** . Nová verze zahrnuje poddoporučení pro každou chybějící aktualizaci a přináší následující vylepšení:

- Přepracované prostředí na Azure Security Center stránkách Azure Portal. Stránka s podrobnostmi o doporučení pro **aktualizace systému by měla být nainstalovaná na vašich počítačích** obsahuje seznam zjištění, jak je uvedeno níže. Když vyberete jedno hledání, otevře se podokno podrobností s odkazem na informace o opravách a seznam ovlivněných prostředků.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Pro aktualizované doporučení se otevře jedno z doporučení v prostředí portálu.":::

- Obohacená data pro doporučení z Azure Resource graphu (ARG). ARG je služba Azure, která je navržená tak, aby poskytovala efektivní průzkum prostředků. ARG můžete použít k dotazování škálování v rámci dané sady předplatných, abyste mohli efektivně řídit vaše prostředí. 

    Pro Azure Security Center můžete použít ARG a [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) k dotazování široké škály dat stav zabezpečení.

    Pokud jste dřív uvedli toto doporučení v ARG, jsou dostupné jenom informace o tom, že je potřeba na počítači opravit doporučení. Následující dotaz rozšířené verze vrátí všechny chybějící aktualizace systému seskupené podle počítače.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Stránka správy zásad v Azure Portal nyní zobrazuje stav přiřazení výchozích zásad

Teď můžete vidět, jestli mají vaše předplatné nastavené výchozí zásady Security Center přiřazené, na stránce **zásady zabezpečení** Security Center Azure Portal.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="Stránka Správa zásad Azure Security Center zobrazuje přiřazení výchozích zásad.":::

## <a name="october-2020"></a>Říjen 2020

Aktualizace v říjnu zahrnují:
- [Posouzení ohrožení zabezpečení pro místní a více cloudových počítačů (Preview)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Přidala se Azure Firewall doporučení (Preview).](#azure-firewall-recommendation-added-preview)
- [Pro doporučení služby Kubernetes Services, které se aktualizovaly pomocí rychlé opravy, by se měly definovat povolené rozsahy IP](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Řídicí panel dodržování předpisů teď obsahuje možnost odebrání standardů.](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Tabulka Microsoft. Security/securityStatuses se odebrala z Azure Resource graphu (ARG).](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Posouzení ohrožení zabezpečení pro místní a více cloudových počítačů (Preview)

Skener pro posouzení ohrožení zabezpečení v [Azure Defenderu pro servery](defender-for-servers-introduction.md)(s technologií Qualys) teď prohledává servery s podporou ARC Azure.

Pokud jste povolili Azure ARC na počítačích mimo Azure, Security Center se bude nabízet k nasazení integrovaného skeneru ohrožení zabezpečení ručně a v rámci škálování.

V této aktualizaci si můžete vysílat výkon **Azure Defenderu pro servery** a konsolidovat svůj program pro správu ohrožení zabezpečení ve všech vašich prostředcích Azure i mimo Azure.

Hlavní možnosti:

- Monitorování stavu zřizování skeneru VA (zranitelnost) na počítačích ARC Azure
- Zřizování integrovaného agenta s rozhraním VA pro nechráněné počítače s Windows a Linuxem na platformě Azure ARC (ručně a v rozsahu)
- Přijímání a analýza zjištěných slabých míst z nasazených agentů (ručně a v rozsahu)
- Jednotné prostředí pro virtuální počítače Azure a počítače s obloukem Azure ARC

[Přečtěte si další informace o nasazení integrovaného skeneru ohrožení zabezpečení do hybridních počítačů](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Přečtěte si další informace o serverech s podporou ARC Azure](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Přidala se Azure Firewall doporučení (Preview).

Bylo přidáno nové doporučení pro ochranu všech virtuálních sítí pomocí Azure Firewall.

Doporučujeme, aby se **virtuální sítě chránily pomocí Azure firewall** radit s cílem omezit přístup k virtuálním sítím a zabránit potenciálním hrozbám pomocí Azure firewall.

Přečtěte si další informace o [Azure firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>Pro doporučení služby Kubernetes Services, které se aktualizovaly pomocí rychlé opravy, by se měly definovat povolené rozsahy IP

**Povolené rozsahy IP adres pro doporučení by měly být definované v Kubernetes Services** nyní mají možnost Rychlá oprava.

Další informace o tomto doporučení a všech dalších Security Center doporučeních najdete v tématu [doporučení zabezpečení – referenční příručka](recommendations-reference.md).

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Pro doporučení služby Kubernetes Services s možností Rychlá oprava by se měly definovat rozsahy autorizovaných IP adres.":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Řídicí panel dodržování předpisů teď obsahuje možnost odebrání standardů.

Řídicí panel dodržování legislativních předpisů Security Center poskytuje přehledy o dodržování předpisů v závislosti na tom, jak splňujete konkrétní kontrolu a požadavky na dodržování předpisů.

Řídicí panel obsahuje výchozí sadu regulativních standardů. Pokud některé z uvedených standardů nejsou pro vaši organizaci důležité, je teď jednoduchý proces, který je odebere z uživatelského rozhraní pro předplatné. Standardy je možné odebrat jenom na úrovni *předplatného* . Nejedná se o obor skupiny pro správu.

Další informace najdete v [Odebrání standardu z řídicího panelu](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabulka Microsoft. Security/securityStatuses se odebrala z Azure Resource graphu (ARG).

Azure Resource Graph je služba v Azure, která je navržená tak, aby poskytovala efektivní průzkum prostředků s možností škálování v rámci dané sady předplatných, abyste mohli efektivně řídit vaše prostředí. 

Pro Azure Security Center můžete použít ARG a [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) k dotazování široké škály dat stav zabezpečení. Například:

- Využití inventáře prostředků (ARG)
- Popsali jsme vzorový ARGický dotaz pro [identifikaci účtů bez povoleného vícefaktorového ověřování (MFA)](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled) .

V ARG existují tabulky dat, které můžete použít ve svých dotazech.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Průzkumník Azure Resource Graph a dostupné tabulky":::

> [!TIP]
> V dokumentaci ARG najdete seznam všech dostupných tabulek v [tabulce Azure Resource Graph a odkaz na typ prostředku](../governance/resource-graph/reference/supported-tables-resources.md).

Z této aktualizace byla odstraněna tabulka **Microsoft. Security/securityStatuses** . Rozhraní securityStatuses API je stále k dispozici.

Nahrazení dat může být použito v tabulce Microsoft. Security/Assessments.

Hlavním rozdílem mezi Microsoft. Security/securityStatuses a Microsoft. Security/Assessments je, že při první zobrazení agregace posouzení obsahuje sekund jeden záznam pro každý.

Například Microsoft. Security/securityStatuses by vrátil výsledek s polem dvou policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
To znamená, že Microsoft. Security/Assessments bude obsahovat záznam pro každé vyhodnocení zásad, a to následujícím způsobem:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Příklad převodu existujícího dotazu ARG pomocí securityStatuses pro teď použití tabulky Assessments:**

Dotaz, který odkazuje na SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Náhradní dotaz pro tabulku posouzení:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Další informace najdete na následujících odkazech:
- [Jak vytvářet dotazy pomocí Průzkumníka Azure Resource Graphu](../governance/resource-graph/first-query-portal.md)
- [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/)