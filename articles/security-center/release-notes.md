---
title: Poznámky k verzi pro Azure Security Center
description: Popis toho, co je nového a co se změnilo v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/07/2020
ms.author: memildin
ms.openlocfilehash: 3272212a857f24916acce7da5a4ee31561ec0c5a
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922922"
---
# <a name="whats-new-in-azure-security-center"></a>Co je nového v Azure Security Center?

Security Center je v aktivním vývoji a průběžně přijímá vylepšení. Tato stránka vám poskytne informace o nových funkcích, opravách chyb a zazastaralých funkcích, abyste měli přehled o nejnovějším vývoji.

Tato stránka se často aktualizuje, takže ji můžete často znovu navštěvovat. 

Další informace o *plánovaných* změnách, které už brzy Security Center, najdete v tématu [důležité nadcházející změny Azure Security Center](upcoming-changes.md). 

> [!TIP]
> Pokud hledáte položky starší než šest měsíců, najdete je v archivu, kde najdete novinky [v Azure Security Center](release-notes-archive.md).


## <a name="december-2020"></a>Prosinec 2020

Aktualizace v prosinci zahrnují:

- [Azure Defender pro servery SQL na počítačích je všeobecně dostupný.](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Podpora Azure Defenderu pro Azure synapse Analytics vyhrazený fond SQL je všeobecně dostupná.](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Globální správci teď můžou udělovat oprávnění na úrovni tenanta.](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Dva nové plány Azure Defenderu: Azure Defender pro DNS a Azure Defender pro Správce prostředků (ve verzi Preview)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)

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

Pokud chcete přiřadit oprávnění na úrovni tenanta, postupujte podle pokynů v tématu [udělení oprávnění pro všechny klienty](security-center-management-groups.md#grant-tenant-wide-permissions-to-yourself).


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Dva nové plány Azure Defenderu: Azure Defender pro DNS a Azure Defender pro Správce prostředků (ve verzi Preview)

Přidali jsme dvě nové možnosti ochrany před hrozbami v cloudu pro vaše prostředí Azure.

Tyto nové ochrany významně zvyšují odolnost proti útokům z aktérů hrozeb a významně zvyšují počet prostředků Azure chráněných pomocí Azure Defenderu.

- **Azure Defender pro správce prostředků** – automaticky monitoruje všechny operace správy prostředků provedené ve vaší organizaci. Další informace najdete tady:
    - [Seznámení s Azure Defenderem pro Správce prostředků](defender-for-resource-manager-introduction.md)
    - [Reakce na upozornění Azure Defenderu pro Resource Manager](defender-for-resource-manager-usage.md)
    - [Seznam výstrah, které poskytuje Azure Defender pro Správce prostředků](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender pro DNS** – nepřetržitě monitoruje všechny dotazy DNS z vašich prostředků Azure. Další informace najdete tady:
    - [Seznámení s Azure Defenderem pro DNS](defender-for-dns-introduction.md)
    - [Reakce na upozornění Azure Defenderu pro DNS](defender-for-dns-usage.md)
    - [Seznam upozornění poskytovaných službou Azure Defender pro DNS](alerts-reference.md#alerts-dns)


## <a name="november-2020"></a>Listopad 2020

Aktualizace v listopadu zahrnují:

- [29 doporučení verze Preview pro zvýšení pokrytí srovnávacích testů zabezpečení Azure](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 přidáno do řídicího panelu dodržování předpisů pro Security Center](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Seznam doporučení nyní obsahuje filtry](#recommendations-list-now-includes-filters)
- [Vylepšené a rozšířené prostředí pro Automatické zřizování](#auto-provisioning-experience-improved-and-expanded)
- [Pro průběžný export je teď dostupné zabezpečené skóre (Preview).](#secure-score-is-now-available-in-continuous-export-preview)
- ["Na vaše počítače by se měly nainstalovat aktualizace systému." teď obsahuje doporučení pro dílčí doporučení](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)
- [Stránka správy zásad v Azure Portal nyní zobrazuje stav přiřazení výchozích zásad](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>29 doporučení verze Preview pro zvýšení pokrytí srovnávacích testů zabezpečení Azure

Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy na základě běžných architektur dodržování předpisů, které jsou specifické pro Azure. [Další informace o srovnávacím testu zabezpečení Azure](../security/benchmarks/introduction.md)

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


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>"Na vaše počítače by se měly nainstalovat aktualizace systému." teď obsahuje doporučení pro dílčí doporučení

**Aktualizace systému by se měly nainstalovat na vaše doporučení vašich počítačů** . Nová verze zahrnuje dílčí doporučení pro všechny chybějící aktualizace a přináší následující vylepšení:

- Přepracované prostředí na Azure Security Center stránkách Azure Portal. Stránka s podrobnostmi o doporučení pro **aktualizace systému by měla být nainstalovaná na vašich počítačích** obsahuje seznam zjištění, jak je uvedeno níže. Když vyberete jedno hledání, otevře se podokno podrobností s odkazem na informace o opravách a seznam ovlivněných prostředků.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Otevře se jedno z dílčích doporučení v prostředí portálu pro aktualizované doporučení.":::

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

Řídicí panel obsahuje výchozí sadu regulativních standardů. Pokud některé z uvedených standardů nejsou pro vaši organizaci důležité, je teď jednoduchý proces, který je jednoduše odebral z uživatelského rozhraní pro předplatné. Standardy je možné odebrat jenom na úrovni *předplatného* . Nejedná se o obor skupiny pro správu.

Další informace najdete v [Odebrání standardu z řídicího panelu](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabulka Microsoft. Security/securityStatuses se odebrala z Azure Resource graphu (ARG).

Azure Resource Graph je služba v Azure, která je navržená tak, aby poskytovala efektivní průzkum prostředků s možností škálování v rámci dané sady předplatných, abyste mohli efektivně řídit vaše prostředí. 

Pro Azure Security Center můžete použít ARG a [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/) k dotazování široké škály dat stav zabezpečení. Příklad:

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


## <a name="september-2020"></a>Září 2020

Aktualizace v září zahrnují:
- [Security Center získá nový vzhled.](#security-center-gets-a-new-look)
- [Vydaný Azure Defender](#azure-defender-released)
- [Azure Defender pro Key Vault je všeobecně dostupný](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender pro ochranu úložiště souborů a ADLS Gen2 je všeobecně dostupný](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Nástroje inventáře prostředků jsou teď všeobecně dostupné.](#asset-inventory-tools-are-now-generally-available)
- [Zakázání konkrétní chyby zabezpečení při hledání registrů kontejnerů a virtuálních počítačů](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Vyloučení prostředku z doporučení](#exempt-a-resource-from-a-recommendation)
- [Konektory AWS a GCP v Security Center přinášejí prostředí pro více cloudů](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Sada doporučení pro ochranu úloh Kubernetes](#kubernetes-workload-protection-recommendation-bundle)
- [Výsledky posouzení ohrožení zabezpečení jsou nyní k dispozici při průběžném exportu](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Zabránit neoprávněným konfiguracím zabezpečení vynucováním doporučení při vytváření nových prostředků](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Vylepšení doporučení skupiny zabezpečení sítě](#network-security-group-recommendations-improved)
- [Doporučení AKS inverze Preview "pod" zásady zabezpečení by měly být definované v Kubernetes Services "](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [Vylepšená e-mailová oznámení z Azure Security Center](#email-notifications-from-azure-security-center-improved)
- [Zabezpečené skóre nezahrnuje doporučení pro verzi Preview.](#secure-score-doesnt-include-preview-recommendations)
- [Doporučení nyní obsahují indikátor závažnosti a interval aktuálnosti.](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center získá nový vzhled.

Vydali jsme aktualizované uživatelské rozhraní pro stránky portálu Security Center. Nové stránky obsahují novou stránku Přehled a také řídicí panely pro bezpečné skóre, inventář prostředků a Azure Defender.

Stránka s přehledem přepracování teď obsahuje dlaždici pro přístup k zabezpečenému skóre, inventáři prostředků a řídicím panelům Azure Defenderu. Obsahuje také dlaždici, která odkazuje na řídicí panel dodržování předpisů.

Přečtěte si další informace o [stránce Přehled](overview-page.md).


### <a name="azure-defender-released"></a>Vydaný Azure Defender

**Azure Defender** je platforma ochrany zatížení cloudu (CWPP) integrovaná v rámci Security Center pro pokročilou, inteligentní a chráněnou službu Azure a hybridní úlohy. Nahradí možnost standardní cenové úrovně v Security Center. 

Pokud povolíte Azure Defender z oblasti **ceny a nastavení** Azure Security Center, jsou všechny tyto plány Defenderu povolené současně a poskytují komplexní ochranu pro výpočetní, datovou a provozní vrstvu vašeho prostředí:

- [Azure Defender pro servery](defender-for-servers-introduction.md)
- [Azure Defender pro App Service](defender-for-app-service-introduction.md)
- [Azure Defender pro úložiště](defender-for-storage-introduction.md)
- [Azure Defender pro SQL](defender-for-sql-introduction.md)
- [Azure Defender pro trezor klíčů](defender-for-key-vault-introduction.md)
- [Azure Defender pro Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender pro registry kontejnerů](defender-for-container-registries-introduction.md)

Každý z těchto plánů je vysvětlen samostatně v dokumentaci Security Center.

Díky jeho vyhrazenému řídicímu panelu poskytuje Azure Defender výstrahy zabezpečení a pokročilou ochranu před internetovými útoky pro virtuální počítače, databáze SQL, kontejnery, webové aplikace, síť a další.

[Další informace o Azure Defenderu](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender pro Key Vault je všeobecně dostupný

Azure Key Vault je cloudová služba, která chrání šifrovací klíče a tajné kódy, jako jsou certifikáty, připojovací řetězce a hesla. 

**Azure Defender pro Key Vault** poskytuje rozšířenou ochranu před internetovými útoky azure pro Azure Key Vault a poskytuje další vrstvu funkcí Security Intelligence. V důsledku rozšíření používá Azure Defender pro Key Vault ochranu mnoha prostředků závislých na vašich Key Vault účtech.

Volitelný plán je teď GA. Tato funkce je ve verzi Preview jako rozšířená ochrana před internetovými útoky pro Azure Key Vault.

Key Vault stránky v Azure Portal nyní obsahují vyhrazenou **bezpečnostní** stránku pro **Security Center** doporučení a upozornění.

Další informace najdete v tomto [Key Vault v Azure Defenderu](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender pro ochranu úložiště souborů a ADLS Gen2 je všeobecně dostupný 

**Azure Defender pro úložiště** detekuje na vašich Azure Storage účtech potenciálně škodlivé aktivity. Data je možné chránit bez ohledu na to, jestli jsou uložená jako kontejnery objektů blob, sdílené složky nebo datová jezera.

Podpora pro [soubory](../storage/files/storage-files-introduction.md) a [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) Azure je teď všeobecně dostupná.

Od 1. října 2020 začneme účtovat za ochranu prostředků u těchto služeb.

Další informace najdete v [Azure Defenderu pro úložiště](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Nástroje inventáře prostředků jsou teď všeobecně dostupné.

Stránka inventáře assetů Azure Security Center poskytuje jednu stránku pro zobrazení stav zabezpečení prostředků, ke kterým jste se připojili Security Center.

Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby se identifikovaly potenciální ohrožení zabezpečení. Pak vám poskytne doporučení k nápravě těchto chyb zabezpečení.

Pokud některý z prostředků obsahuje nevyřízená doporučení, zobrazí se v inventáři.

Další informace najdete v informacích o [prozkoumání a správě vašich prostředků pomocí inventáře a nástrojů pro správu](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Zakázání konkrétní chyby zabezpečení při hledání registrů kontejnerů a virtuálních počítačů

Azure Defender obsahuje skenery ohrožení zabezpečení pro skenování imagí ve vašich Azure Container Registry a virtuálních počítačích.

Pokud máte organizaci, kterou je třeba ignorovat, místo toho, aby ji bylo možné opravit, můžete ji případně zakázat. Zakázané závěry neovlivňují vaše zabezpečené skóre nebo generují nežádoucí hluk.

Pokud hledání odpovídá kritériím, která jste definovali v pravidlech vypnutí, nezobrazí se v seznamu zjištění.

Tato možnost je k dispozici na stránkách s podrobnostmi doporučení pro:

- **Ohrožení zabezpečení v Azure Container Registrych imagí by mělo být opraveno**
- **Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.**

Další informace najdete v [zakažte konkrétní zjištění imagí kontejneru](defender-for-container-registries-usage.md#disable-specific-findings-preview) a [zakažte konkrétní zjištění pro vaše virtuální počítače](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Vyloučení prostředku z doporučení

V některých případech se prostředek bude zobrazovat jako špatný v souvislosti s konkrétním doporučením (a tím se sníží vaše zabezpečené skóre), i když byste to ještě neměli. Mohl by být opraven procesem, který není sledován nástrojem Security Center. Nebo možná vaše organizace rozhodla přijmout riziko pro konkrétní prostředek. 

V takových případech můžete vytvořit pravidlo výjimky a zajistit, aby prostředek nebyl v budoucnu uvedený v seznamu prostředků, které nejsou v pořádku. Tato pravidla mohou zahrnovat popsaná odůvodnění, jak je popsáno níže.

Další informace najdete v informacích [o vyloučení prostředků z doporučení a zabezpečeného skóre](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>Konektory AWS a GCP v Security Center přinášejí prostředí pro více cloudů

Cloudové úlohy běžně pokrývá několik cloudových platforem, ale cloudové služby zabezpečení musí provádět stejné.

Azure Security Center nyní chrání úlohy v Azure, Amazon Web Services (AWS) a Google Cloud Platform (GCP).

Při připojování účtů AWS a GCP do Security Center se integruje AWS Security hub, GCP Security a Azure Security Center. 

Další informace najdete v informacích o [připojení účtů AWS k Azure Security Center](quickstart-onboard-aws.md) a [připojení účtů GCP k Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Sada doporučení pro ochranu úloh Kubernetes

Aby se zajistilo, že Kubernetes úlohy jsou zabezpečené ve výchozím nastavení, Security Center přidávají doporučení pro posílení úrovně Kubernetes, včetně možností vynucení s řízením přístupu Kubernetes.

Když jste nainstalovali doplněk Azure Policy pro Kubernetes do clusteru AKS, všechny požadavky na server rozhraní Kubernetes API se budou monitorovat proti předdefinované sadě osvědčených postupů předtím, než se trvale uloží do clusteru. Pak můžete nakonfigurovat, aby vynutil osvědčené postupy a nakonfigurovaly je pro budoucí úlohy.

Můžete například stanovit, že by se neměly vytvářet privilegované kontejnery a všechny budoucí požadavky tak budou zablokovány.

Další informace najdete v článku [osvědčené postupy ochrany úloh pomocí řízení přístupu Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Výsledky posouzení ohrožení zabezpečení jsou nyní k dispozici při průběžném exportu

Používejte průběžný export ke streamování výstrah a doporučení v reálném čase do služby Azure Event Hubs, Log Analytics pracovních prostorů nebo Azure Monitor. Odtud můžete tato data integrovat s systémů Siem (například Azure Sentinel, Power BI, Azure Průzkumník dat a dalšími.

Nástroje pro posouzení ohrožení zabezpečení Security Center v rámci "nadřazených" doporučení vracet informace o vašich prostředcích jako užitečná doporučení, jako je třeba ohrožení zabezpečení ve virtuálních počítačích. 

Výsledky zabezpečení jsou nyní k dispozici pro export prostřednictvím průběžného exportu, když vyberete možnost doporučení a povolíte možnost **Zahrnout zjištění zabezpečení** .

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Zahrnout zjištění zabezpečení – přepnout do konfigurace průběžného exportu" :::

Související stránky:

- [Integrované řešení posouzení ohrožení zabezpečení Security Center pro virtuální počítače Azure](deploy-vulnerability-assessment-vm.md)
- [Integrované řešení posouzení ohrožení zabezpečení Security Center pro bitové kopie Azure Container Registry](defender-for-container-registries-usage.md)
- [Průběžný export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Zabránit neoprávněným konfiguracím zabezpečení vynucováním doporučení při vytváření nových prostředků

Nezabezpečené konfigurace zabezpečení představují hlavní příčinu incidentů zabezpečení. Security Center nyní mají schopnost *zabránit* v neúspěšně konfiguracích nových prostředků s ohledem na konkrétní doporučení. 

Tato funkce vám může přispět k zabezpečení vašich úloh a ke stabilizaci zabezpečeného skóre.

Vynucování zabezpečené konfigurace na základě konkrétního doporučení se nabízí ve dvou režimech:

- Pomocí efektu **odepření** Azure Policy můžete zastavit vytváření špatných prostředků.

- Pomocí možnosti **Vynutilit** můžete využít **DeployIfNotExist** efektu zásad Azure a automaticky opravovat prostředky, které nedodržují předpisy při tvorbě.
 
Tato možnost je k dispozici pro vybraná doporučení zabezpečení a nachází se v horní části stránky s podrobnostmi o prostředku.

Další informace o tom, jak [zabránit v neoprávněných konfiguracích s doporučeními pro vymáhání](prevent-misconfigurations.md)

###  <a name="network-security-group-recommendations-improved"></a>Vylepšení doporučení skupiny zabezpečení sítě

Byla vylepšena následující doporučení zabezpečení související se skupinami zabezpečení sítě, aby se snížily některé výskyty falešně pozitivních hodnot.

- Všechny síťové porty by se měly omezit na NSG přidružených k vašemu VIRTUÁLNÍmu počítači.
- Porty pro správu by měly být uzavřeny na virtuálních počítačích
- Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě
- Podsítě by měly být přidružené ke skupině zabezpečení sítě.


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Doporučení AKS inverze Preview "pod" zásady zabezpečení by měly být definované v Kubernetes Services "

Doporučení pro verzi Preview "pod zásadou zabezpečení by se měly definovat na Kubernetes Services", jak je popsáno v dokumentaci ke [službě Azure Kubernetes](../aks/use-pod-security-policies.md) .

Funkce zásady zabezpečení pod (Preview) je nastavena pro vyřazení a nebude již k dispozici po 15. říjnu 2020 ve prospěch Azure Policy pro AKS.

Po použití zásady zabezpečení (Preview) je zastaralá. tuto funkci je třeba zakázat na všech stávajících clusterech pomocí zastaralé funkce, aby se prováděly budoucí upgrady clusteru a zůstaly v rámci podpory Azure.


### <a name="email-notifications-from-azure-security-center-improved"></a>Vylepšená e-mailová oznámení z Azure Security Center

Vylepšili jsme následující oblasti e-mailů týkajících se výstrah zabezpečení: 

- Přidání možnosti odesílání e-mailových oznámení o výstrahách pro všechny úrovně závažnosti
- Přidali jsme možnost informovat uživatele o různých rolích Azure v rámci předplatného.
- Ve výchozím nastavení oznamujeme, že vlastníci předplatného budou upozorňováni na výstrahy s vysokou závažností (s vysokou pravděpodobností, že se jedná o pravé porušení předpisů).
- Odebrali jsme pole telefonní číslo ze stránky Konfigurace e-mailových oznámení.

Další informace najdete v [Nastavení e-mailových oznámení pro výstrahy zabezpečení](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Zabezpečené skóre nezahrnuje doporučení pro verzi Preview. 

Security Center průběžně vyhodnocuje vaše prostředky, odběry a organizace pro problémy se zabezpečením. Pak agreguje všechna zjištění do jediného skóre, abyste na první pohled mohli sdělit aktuální situaci zabezpečení: čím vyšší je skóre, tím se sníží zjištěná úroveň rizika.

Nově zjištěné hrozby jsou v Security Center k dispozici nové Rady zabezpečení prostřednictvím nových doporučení. Pokud se chcete vyhnout nedarovaným změnám v zabezpečeném skóre a poskytnout období odkladu, ve kterém můžete prozkoumat nová doporučení předtím, než budou mít dopad na vaše skóre, doporučení označená jako **verze Preview** už nejsou zahrnutá do výpočtů zabezpečeného skóre. Měly by se pořád opravit všude, kde je to možné, takže po skončení období Preview budou přispět ke svému skóre.

Doporučení **verze Preview** navíc nevykreslují prostředek "není v pořádku".

Příklad doporučení verze Preview:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Doporučení s příznakem Preview":::

[Přečtěte si další informace o zabezpečeném skóre](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Doporučení nyní obsahují indikátor závažnosti a interval aktuálnosti.

Stránka s podrobnostmi pro doporučení nyní obsahuje indikátor intervalu aktuálnosti (kdykoli je to relevantní) a jasné zobrazení závažnosti doporučení.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Stránka s doporučeními ukazující aktuálnost a závažnost":::



## <a name="august-2020"></a>Srpen 2020

Aktualizace v srpnu zahrnují:

- [Inventář assetů – výkonné nové zobrazení stav zabezpečení vašich prostředků](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Přidání podpory pro výchozí nastavení zabezpečení Azure Active Directory (pro Multi-Factor Authentication)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Přidalo se doporučení instančních objektů.](#service-principals-recommendation-added)
- [Posouzení ohrožení zabezpečení na virtuálních počítačích – konsolidovaná doporučení a zásady](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nové zásady zabezpečení AKS přidané do ASC_default iniciativy – jenom pro zákazníky s privátní verzí Preview](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventář assetů – výkonné nové zobrazení stav zabezpečení vašich prostředků

Inventář assetů Security Center (aktuálně ve verzi Preview) poskytuje způsob, jak zobrazit stav zabezpečení prostředků, ke kterým jste se připojili Security Center.

Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure, aby se identifikovaly potenciální ohrožení zabezpečení. Pak vám poskytne doporučení k nápravě těchto chyb zabezpečení. Pokud některý z prostředků obsahuje nevyřízená doporučení, zobrazí se v inventáři.

Můžete použít zobrazení a jeho filtry k prozkoumání dat stav zabezpečení a provádět další akce na základě vašich výsledků.

Přečtěte si další informace o [inventáři prostředků](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Přidání podpory pro výchozí nastavení zabezpečení Azure Active Directory (pro Multi-Factor Authentication)

Security Center přidaná plná podpora pro [výchozí nastavení zabezpečení](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), ochrana zabezpečení identit od Microsoftu je bezplatná.

Výchozí hodnoty zabezpečení poskytují předem nakonfigurovaná nastavení zabezpečení identity, která chrání vaši organizaci před běžnými útoky souvisejícími s identitou. Výchozí hodnoty zabezpečení již chrání více než 5 000 000 klientů. klienti 50 000 jsou chráněni také pomocí Security Center.

Security Center teď poskytuje doporučení zabezpečení vždy, když identifikuje předplatné Azure bez povolených výchozích hodnot zabezpečení. Až do této chvíle Security Center doporučeno povolit vícefaktorové ověřování pomocí podmíněného přístupu, který je součástí licence Azure Active Directory (AD) Premium. Pro zákazníky, kteří používají Azure AD Free, teď doporučujeme povolit výchozí nastavení zabezpečení. 

Naším cílem je povzbudit větší počet zákazníků k zabezpečení svých cloudových prostředí pomocí vícefaktorového ověřování a zmírnit jedno z nejvyšších rizik, která jsou také největší dopadem na vaše [zabezpečené skóre](secure-score-security-controls.md).

Přečtěte si další informace o [výchozích hodnotách zabezpečení](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Přidalo se doporučení instančních objektů.

Bylo přidáno nové doporučení, které Security Center zákazníky, kteří používají certifikáty pro správu ke správě předplatných, přepínat na instanční objekty.

K **ochraně vašich předplatných, namísto certifikátů pro správu** , doporučujeme použít instanční objekty nebo Azure Resource Manager pro bezpečnější správu předplatných. 

Další informace o [aplikacích a instančních objektech služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Posouzení ohrožení zabezpečení na virtuálních počítačích – konsolidovaná doporučení a zásady

Security Center zkontroluje vaše virtuální počítače a zjistí, jestli neběží na řešení pro posouzení ohrožení zabezpečení. Pokud se nenajde žádné řešení posouzení ohrožení zabezpečení, Security Center poskytne doporučení ke zjednodušení nasazení.

Po nalezení ohrožení zabezpečení Security Center poskytuje doporučení k tomu, abyste zjistili, co je potřeba k prozkoumání a nápravě v případě potřeby.

Abychom zajistili konzistentní prostředí pro všechny uživatele, bez ohledu na typ skeneru, který používají, jsme sjednoceni čtyřmi doporučeními v následujících dvou příkladech:

|Sjednocené doporučení|Popis změny|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.**|Nahrazuje následující dvě doporučení:<br> **•** Povolit integrované řešení posouzení ohrožení zabezpečení na virtuálních počítačích (s využitím Qualys (teď zastaralé) (zahrnuté do úrovně Standard)<br> **•** Řešení posouzení ohrožení zabezpečení by se mělo nainstalovat na vaše virtuální počítače (teď zastaralé) (úrovně Standard a Free).|
|**Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.**|Nahrazuje následující dvě doporučení:<br>**•** Oprava ohrožení zabezpečení zjištěných na virtuálních počítačích (s technologií Qualys) (nyní zastaralé)<br>**•** Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení (teď zastaralé).|
|||

Nyní použijete stejné doporučení k nasazení rozšíření posouzení ohrožení zabezpečení Security Center nebo soukromě Licencovanému řešení ("BYOL") od partnera, jako je Qualys nebo Rapid7.

V případě, že dojde k nalezení a nahlášení ohrožení zabezpečení Security Center, se vám zobrazí jedno doporučení bez ohledu na řešení posouzení ohrožení zabezpečení, které je identifikovalo.

#### <a name="updating-dependencies"></a>Aktualizace závislostí

Pokud máte skripty, dotazy nebo automatizace odkazující na předchozí doporučení nebo klíče zásad nebo názvy, použijte následující tabulky k aktualizaci odkazů:

##### <a name="before-august-2020"></a>Před srpna 2020

|Doporučení|Rozsah|
|----|:----|
|**Povolení integrovaného řešení posouzení ohrožení zabezpečení na virtuálních počítačích (používá se Qualys)**<br>Klíč: 550e890b-e652-4d22-8274-60b3bdb24c63|Integrované|
|**Náprava ohrožení zabezpečení zjištěná na vašich virtuálních počítačích (používá se Qualys)**<br>Klíč: 1195afff-c881-495E-9bc5-1486211ae03f|Integrované|
|**Řešení posouzení ohrožení zabezpečení by se mělo nainstalovat na virtuální počítače.**<br>Klíč: 01b1ed4c-B733-4FEE-b145-f23236e70cf3|BYOL|
|**Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.**<br>Klíč: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Zásada|Rozsah|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené posouzení ohrožení zabezpečení**<br>ID zásady: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Integrované|
|**Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.**<br>ID zásady: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Od srpna 2020

|Doporučení|Rozsah|
|----|:----|
|**Na virtuálních počítačích by mělo být povolené řešení posouzení ohrožení zabezpečení.**<br>Klíč: ffff0522-1e88-47fc-8382-2a80ba848f5d|Předdefinované + BYOL|
|**Ohrožení zabezpečení ve vašich virtuálních počítačích by se mělo opravit.**<br>Klíč: 1195afff-c881-495E-9bc5-1486211ae03f|Předdefinované + BYOL|
||||

|Zásada|Rozsah|
|----|:----|
|[**Na virtuálních počítačích by mělo být povolené posouzení ohrožení zabezpečení**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>ID zásady: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Předdefinované + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nové zásady zabezpečení AKS přidané do ASC_default iniciativy – jenom pro zákazníky s privátní verzí Preview

Aby se zajistilo, že Kubernetes úlohy jsou zabezpečené ve výchozím nastavení, Security Center přidávají zásady Kubernetes úrovně a posílení doporučení, včetně možností vynucení pomocí řízení přístupu Kubernetes.

Úvodní fáze tohoto projektu zahrnuje soukromou verzi Preview a přidání nových (zakázaných ve výchozím) zásadách pro ASC_default iniciativu.

Tyto zásady můžete bezpečně ignorovat a nebude to mít žádný vliv na vaše prostředí. Pokud je chcete povolit, zaregistrujte si verzi Preview na adrese https://aka.ms/SecurityPrP a vyberte si z těchto možností:

1. **Single Preview** – pro připojení pouze k této privátní verzi Preview. Jako náhled, ke kterému se chcete připojit, výslovně uveďte "ASC průběžné prověřování".
1. **Probíhající program** – přidaný do tohoto a budoucího privátního náhledu. Budete muset dokončit profil a smlouvu o ochraně osobních údajů.


## <a name="july-2020"></a>Červenec 2020

Aktualizace v červenci zahrnují:
- [Pro Image mimo Marketplace je teď k dispozici posouzení ohrožení zabezpečení pro virtuální počítače.](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Ochrana před hrozbami pro Azure Storage rozšířena tak, aby zahrnovala soubory Azure a Azure Data Lake Storage Gen2 (Preview)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Osm nových doporučení pro povolení funkcí ochrany před hrozbami](#eight-new-recommendations-to-enable-threat-protection-features)
- [Vylepšení zabezpečení kontejnerů – rychlejší prohledávání registru a aktualizace dokumentace](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Adaptivní řízení aplikací je aktualizované o nové doporučení a podporu zástupných znaků v pravidlech cesty.](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Šest zásad pro zabezpečení rozšířených dat SQL zastaralé](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Pro Image mimo Marketplace je teď k dispozici posouzení ohrožení zabezpečení pro virtuální počítače.

Při nasazení řešení posouzení ohrožení zabezpečení Security Center před nasazením dříve provedla kontrolu ověření. Kontrola měla potvrdit SKLADOVOU položku Marketplace cílového virtuálního počítače. 

Z této aktualizace jste odebrali kontrolu a teď můžete nasadit nástroje pro posouzení ohrožení zabezpečení do vlastních počítačů se systémem Windows a Linux. Vlastní image jsou ty, které jste změnili ve výchozím nastavení webu Marketplace.

I když teď můžete nasadit integrované rozšíření posouzení ohrožení zabezpečení (s technologií Qualys) na mnoho dalších počítačů, podpora je dostupná jenom v případě, že používáte operační systém, který je uvedený v [části nasazení integrovaného skeneru zabezpečení na virtuální počítače úrovně Standard](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) .

Přečtěte si další informace o [integrovaném skeneru ohrožení zabezpečení pro virtuální počítače (vyžaduje Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Přečtěte si další informace o používání vlastního řešení posouzení ohrožení zabezpečení v soukromém prostředí z Qualys nebo Rapid7 v [nasazení řešení pro kontrolu ohrožení zabezpečení partnera](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Ochrana před hrozbami pro Azure Storage rozšířena tak, aby zahrnovala soubory Azure a Azure Data Lake Storage Gen2 (Preview)

Ochrana před hrozbami pro Azure Storage detekuje na vašich Azure Storage účtech potenciálně škodlivé aktivity. Security Center zobrazuje výstrahy, když detekuje pokusy o přístup k účtům úložiště nebo jejich zneužití. 

Data je možné chránit bez ohledu na to, jestli jsou uložená jako kontejnery objektů blob, sdílené složky nebo datová jezera.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Osm nových doporučení pro povolení funkcí ochrany před hrozbami

Přidali jsme osm nových doporučení, která poskytují jednoduchý způsob, jak povolit funkce ochrany před internetovými útoky v Azure Security Center pro následující typy prostředků: virtuální počítače, App Service plány, Azure SQL Database servery, SQL servery na počítačích, účty Azure Storage, clustery služby Azure Kubernetes, Azure Container Registry Registry a Azure Key Vault trezory.

Nová doporučení:

- **V Azure SQL Databasech serverech by mělo být povolené rozšířené zabezpečení dat.**
- **Rozšířené zabezpečení dat by mělo být povoleno na serverech SQL na počítačích**
- **V plánech Azure App Service by měla být povolena rozšířená ochrana před internetovými útoky.**
- **V Azure Container Registry Registry by měla být povolena rozšířená ochrana před internetovými útoky.**
- **V trezorech Azure Key Vault by měla být povolena rozšířená ochrana před internetovými útoky.**
- **V clusterech služby Azure Kubernetes by měla být povolena rozšířená ochrana před internetovými útoky.**
- **Pro účty Azure Storage by měla být povolena rozšířená ochrana před internetovými útoky.**
- **Na virtuálních počítačích by měla být povolena rozšířená ochrana před internetovými útoky.**

Tato nová doporučení patří do nastavení Povolit řízení zabezpečení v **Azure Defenderu** .

Doporučení také zahrnují funkci rychlé opravy. 

> [!IMPORTANT]
> Oprava některé z těchto doporučení bude mít za následek poplatky za ochranu relevantních prostředků. Tyto poplatky začnou platit okamžitě, pokud máte související prostředky v aktuálním předplatném. Případně můžete v budoucnu přidat data později.
> 
> Například pokud ve vašem předplatném nemáte žádné clustery služby Azure Kubernetes a povolíte ochranu před hrozbami, neúčtují se žádné poplatky. Pokud v budoucnu přidáte cluster do stejného předplatného, bude automaticky chráněn a v tomto čase začnou platit poplatky.

Přečtěte si další informace o každé z těchto informací na [referenční stránce doporučení zabezpečení](recommendations-reference.md).

Přečtěte si další informace o [ochraně před hrozbami v Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Vylepšení zabezpečení kontejnerů – rychlejší prohledávání registru a aktualizace dokumentace

V rámci kontinuálního investování v doméně zabezpečení kontejneru máme na začátku sdílení významného zlepšení výkonu Security Center dynamické kontroly imagí kontejnerů uložených v Azure Container Registry. Kontroly se teď většinou dokončí přibližně po dvou minutách. V některých případech může trvat až 15 minut.

Abychom vylepšili přehlednost a pokyny týkající se schopností zabezpečení kontejneru Azure Security Center, Aktualizovali jsme také stránky dokumentace k zabezpečení kontejnerů. 

Další informace o zabezpečení kontejneru Security Center najdete v následujících článcích:

- [Přehled funkcí zabezpečení kontejneru Security Center](container-security.md)
- [Podrobnosti o integraci s Azure Container Registry](defender-for-container-registries-introduction.md)
- [Podrobnosti o integraci se službou Azure Kubernetes](defender-for-kubernetes-introduction.md)
- [Jak kontrolovat registry a posílit svoje hostitele Docker](container-security.md)
- [Výstrahy zabezpečení z funkcí ochrany před hrozbami pro clustery služby Azure Kubernetes](alerts-reference.md#alerts-akscluster)
- [Výstrahy zabezpečení z funkcí ochrany před hrozbami pro hostitele služeb Azure Kubernetes](alerts-reference.md#alerts-containerhost)
- [Doporučení zabezpečení pro kontejnery](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptivní řízení aplikací je aktualizované o nové doporučení a podporu zástupných znaků v pravidlech cesty.

Funkce Adaptivní řízení aplikací obdržela dvě důležité aktualizace:

* Nové doporučení identifikuje potenciálně legitimní chování, které nebylo dříve povoleno. Nové doporučení, **pravidla povolených v zásadách adaptivního řízení aplikací by se měla aktualizovat**, vyzve vás k přidání nových pravidel do stávajících zásad, aby se snížil počet falešně pozitivních výstrah v rámci adaptivních upozornění na porušení řízení aplikací.

* Pravidla cesty teď podporují zástupné znaky. Z této aktualizace můžete nakonfigurovat pravidla povolených cest pomocí zástupných znaků. Existují dva podporované scénáře:

    * Použití zástupného znaku na konci cesty pro povolení všech spustitelných souborů v této složce a podsložkách

    * Použití zástupných znaků uprostřed cesty k povolení známého názvu spustitelného souboru s názvem složky (například osobní složky uživatele se známým spustitelným souborem, automaticky generované názvy složek atd.).


[Přečtěte si další informace o adaptivních řízeních aplikací](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Šest zásad pro zabezpečení rozšířených dat SQL zastaralé

Šest zásad souvisejících s pokročilým zabezpečením dat pro počítače SQL je zastaralé:

- V rozšířených nastaveních zabezpečení dat spravované instance SQL by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.
- V rozšířených nastaveních zabezpečení dat SQL serveru by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.
- Pokročilá nastavení zabezpečení dat pro spravovanou instanci SQL by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- Pokročilá nastavení zabezpečení dat pro SQL Server by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- E-mailová oznámení správcům a vlastníkům předplatného by se měla povolit v rozšířených nastaveních zabezpečení dat spravované instance SQL
- E-mailová oznámení správcům a vlastníkům předplatného by měla být povolená v nastavení SQL Server Advanced Data Security

Přečtěte si další informace o [předdefinovaných zásadách](./policy-reference.md).
