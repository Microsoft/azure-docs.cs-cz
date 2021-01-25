---
title: Rozšiřování Sentinel Azure mezi pracovními prostory a klienty | Microsoft Docs
description: Jak používat službu Azure Sentinel k dotazování a analýze dat napříč pracovními prostory a klienty.
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
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: 4312a819f8fd41805dca095556efdc6189f23af9
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98757115"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>Rozšíření Azure Sentinelu napříč pracovními prostory a tenanty

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>Nutnost použít několik pracovních prostorů služby Azure Sentinel

Azure Sentinel je postavená na Log Analytics pracovním prostoru. Všimněte si, že prvním krokem při připojování služby Azure Sentinel je výběr pracovního prostoru Log Analytics, který chcete pro tento účel použít.

Všechny výhody Azure Sentinelu můžete plně využít při použití jednoho pracovního prostoru. I tak existují některé okolnosti, které mohou vyžadovat, abyste měli více pracovních prostorů. V následující tabulce jsou uvedeny některé z těchto situací a pokud je to možné, navrhuje, jak může být požadavek splněn s jedním pracovním prostorem:

| Požadavek | Popis | Způsoby snížení počtu pracovních prostorů |
|-------------|-------------|--------------------------------|
| Suverenita a dodržování předpisů | Pracovní prostor je vázaný na konkrétní oblast. Pokud se data musí uchovávat v různých [zeměpisných oblastech Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , aby splňovaly zákonné požadavky, musí být rozdělené do samostatných pracovních prostorů. |  |
| Vlastnictví dat | Hranice vlastnictví dat, například dceřinými společnostmi nebo přidruženými společnostmi, jsou lépe vymezeny pomocí samostatných pracovních prostorů. |  |
| Několik tenantů Azure | Služba Azure Sentinel podporuje shromažďování dat z prostředků Microsoft a Azure SaaS jenom v rámci své vlastní hranice tenanta Azure Active Directory (Azure AD). Každý tenant služby Azure AD proto vyžaduje samostatný pracovní prostor. |  |
| Odstupňované řízení přístupu k datům | Organizace může pro přístup k některým datům shromažďovaných službou Azure Sentinel vyžadovat v rámci organizace nebo mimo ni jiné skupiny. Například:<br><ul><li>Vlastníci prostředků mají přístup k datům, která se týkají jejich prostředků.</li><li>Regionální nebo dceřiné Socy – přístup k datům relevantním pro jejich části organizace</li></ul> | Používání [prostředků](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) Azure RBAC nebo [úrovně tabulky Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| Podrobné nastavení uchovávání informací | Historicky bylo několik pracovních prostorů jediným způsobem, jak nastavit různá období uchovávání pro různé datové typy. Díky zavedení nastavení uchování na úrovni tabulky už to v mnoha případech nepotřebujeme. | Použití [Nastavení uchování na úrovni tabulky](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) nebo automatizace [odstranění dat](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| Rozdělená fakturace | Když umístíte pracovní prostory do samostatných předplatných, můžou se fakturovat různým stranám. | Použijte vykazování a přeúčtování. |
| Zastaralá architektura | Použití několika pracovních prostorů může vyrazit z historických návrhů, které vzaly v úvahu omezení nebo osvědčené postupy, které už nedrží hodnotu true. Může se také jednat o volbu návrhu, který lze upravit tak, aby lépe vyhovoval Azure Sentinelu.<br><br>Mezi příklady patří:<br><ul><li>Použití výchozího pracovního prostoru pro každé předplatné při nasazení Azure Security Center</li><li>Nutnost podrobnějšího řízení přístupu nebo nastavení uchovávání, řešení, pro která jsou relativně nová</li></ul> | Změňte architekturu pracovních prostorů. |

### <a name="managed-security-service-provider-mssp"></a>Spravovaný poskytovatel služby zabezpečení (MSSP)

Konkrétní případ použití, který používá několik pracovních prostorů, je MSSP služba Azure Sentinel. V tomto případě platí, že mnoho, pokud ne všechny výše uvedené požadavky platí, představuje několik pracovních prostorů v rámci klientů, což je nejlepší postup. MSSP může použít [Azure Lighthouse](../lighthouse/overview.md) k rozšiřování funkcí mezi pracovními prostory Azure Sentinel napříč klienty.

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Architektura s více pracovními prostory Azure Sentinelu

Vzhledem k výše uvedeným požadavkům jsou k dispozici případy, kdy je nutné centrálně monitorovat a spravovat více pracovních prostorů Azure Sentinel, potenciálně v rámci klientů Azure Active Directory (Azure AD), a to v jednom SOC.

- MSSP služba Sentinel Azure.

- Globální SOC obsluhující několik poboček, z nichž každá má vlastní místní SOC.

- SOC monitorování více tenantů Azure AD v rámci organizace.

Pro vyřešení tohoto požadavku Azure Sentinel nabízí možnosti více pracovních prostorů, které umožňují centrální monitorování, konfiguraci a správu, a poskytuje jedno podokno skla v rámci všeho, co pokrývá SOC, jak je znázorněno v následujícím diagramu.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Architektura mezi jednotlivými pracovními prostory":::

Tento model nabízí významné výhody oproti plně centralizovanému modelu, ve kterém se všechna data zkopírují do jednoho pracovního prostoru:

- Flexibilní přiřazení role k globálním a místnímu SOC nebo MSSP svým zákazníkům.

- Méně výzev týkajících se vlastnictví dat, ochrany osobních údajů a dodržování legislativních předpisů.

- Minimální latence sítě a poplatky

- Snadné zprovoznění a odregistrace nových poboček nebo zákazníků.

V následujících částech budeme vysvětlit, jak tento model provozovat, a zejména postup:

- Centrálně monitorujte více pracovních prostorů, potenciálně mezi klienty, a poskytněte SOC s jedním podoknem skla.

- Centrálně konfigurujte a spravujte víc pracovních prostorů, které jsou potenciálně mezi klienty, a to pomocí automatizace.

## <a name="cross-workspace-monitoring"></a>Monitorování mezi pracovními prostory

### <a name="manage-incidents-on-multiple-workspaces"></a>Správa incidentů ve více pracovních prostorech

Azure Sentinel podporuje [zobrazení na více pracovních prostorů](./multiple-workspace-view.md) , které usnadňuje monitorování a správu událostí v rámci několika pracovních prostorů. Centralizované zobrazení incidentu umožňuje přímo spravovat incidenty nebo procházet hierarchii transparentně na podrobnosti o incidentech v souvislosti s původním pracovním prostorem.

### <a name="cross-workspace-querying"></a>Dotazování mezi pracovními prostory

Sentinel Azure podporuje dotazování na [více pracovních prostorů v jednom dotazu](../azure-monitor/log-query/cross-workspace-query.md), což vám umožní vyhledávat a korelovat data z několika pracovních prostorů v jednom dotazu. 

- Pomocí [výrazu pracovní prostor ()](../azure-monitor/log-query/workspace-expression.md) můžete odkazovat na tabulku v jiném pracovním prostoru. 
- Použijte [operátor UNION](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) vedle výrazu pracovní prostor () a použijte dotaz napříč tabulkami ve více pracovních prostorech.

Uložené [funkce](../azure-monitor/log-query/functions.md) můžete použít k zjednodušení dotazů mezi jednotlivými pracovními prostory. Například pokud je odkaz na pracovní prostor dlouhý, může být vhodné uložit výraz `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` jako funkci s názvem `SecurityEventCustomerA` . Pak můžete napsat dotazy jako `SecurityEventCustomerA | where ...` .

Funkce může také zjednodušit běžně používané sjednocení. Můžete například uložit následující výraz jako funkci s názvem `unionSecurityEvent` :

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

Dotaz můžete v obou pracovních prostorech napsat tak, že začnete s `unionSecurityEvent | where ...` .

#### <a name="cross-workspace-analytics-rules"></a>Pravidla analýzy mezi pracovními prostory<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Dotazy na více pracovních prostorů teď můžou být zahrnuté do pravidel plánovaných analýz, a to v souladu s těmito omezeními:

- V jednom dotazu může být zahrnuto až 15 pracovních prostorů.
- U každého pracovního prostoru, na který se odkazuje v dotazu, musí být nasazená Azure Sentinel.

> [!NOTE] 
> Dotazování na více pracovních prostorů ve stejném dotazu může mít vliv na výkon, a proto je doporučeno pouze v případě, že tato funkce vyžaduje tuto funkci.

#### <a name="cross-workspace-workbooks"></a>Sešity mezi pracovními prostory<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Pracovní [panely poskytují řídicí](./overview.md#workbooks) panely a aplikace pro službu Azure Sentinel. Když pracujete s několika pracovními prostory, poskytují monitorování a akce napříč pracovními prostory.

Sešity můžou obsahovat dotazy pro více pracovních prostorů v jedné ze tří metod, z nichž každý má stejný způsob, jakým má platforma pro koncové uživatele různé úrovně znalostí:

| Metoda  | Popis | Kdy mám použít? |
|---------|-------------|--------------------|
| Zápis dotazů mezi pracovními prostory | Tvůrce sešitu může v sešitu zapsat dotazy mezi pracovními prostory (popsané výše). | Tato možnost umožňuje autorům sešitu chránit uživatele ze struktury pracovního prostoru. |
| Přidat selektor pracovního prostoru do sešitu | Tvůrce sešitu může jako součást sešitu implementovat selektor pracovního prostoru, jak je popsáno [zde](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357). | Tato možnost poskytuje uživateli kontrolu nad pracovními prostory, které jsou uvedeny v sešitu, prostřednictvím snadno použitelného rozevíracího pole. |
| Interaktivní úprava sešitu | Pokročilý uživatel, který upravuje existující sešit, může upravit dotazy v něm, vybrat cílové pracovní prostory pomocí výběru pracovního prostoru v editoru. | Tato možnost umožňuje uživateli snadno upravit existující sešity pro práci s více pracovními prostory. |
|

#### <a name="cross-workspace-hunting"></a>Lov mezi pracovními prostory

Azure Sentinel poskytuje předem načtené Ukázky dotazů, které vám pomohou začít a seznámit s tabulkami a dotazovacím jazykem. Tyto integrované lovecké dotazy jsou vyvíjené výzkumnými pracovníky Microsoftu, a to tak, že se přidávají nové dotazy a doladí existující dotazy. získáte tak vstupní bod, který bude hledat nové detekce a identifikovat známky vniknutí, které se v nástrojích zabezpečení už nerozpoznaly.  

Lovecké možnosti mezi pracovními prostory umožňují, aby se vaše hrozba Hunters vytvářet nové lovecké dotazy nebo přizpůsobily stávajícím, aby pokryly několik pracovních prostorů, a to pomocí operátoru Union a výrazu pracovního prostoru (), jak je uvedeno

## <a name="cross-workspace-management-using-automation"></a>Správa mezi jednotlivými pracovními prostory pomocí automatizace

Pokud chcete nakonfigurovat a spravovat víc pracovních prostorů služby Azure Sentinel, budete muset automatizovat používání rozhraní API pro správu služby Azure Sentinel. Další informace o tom, jak automatizovat nasazení prostředků Sentinel Azure, včetně pravidel výstrah, loveckých dotazů, sešitů a playbooky, najdete v tématu [rozšíření Azure Sentinel: API, Integration and Management Automation](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885).

Viz také [nasazení a Správa služby Azure Sentinel jako kódu](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) a [kombinování Azure Lighthouse s možnostmi DevOps Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) pro konsolidovanou metodologii pro správu Azure Sentinel jako kódu a pro nasazení a konfiguraci prostředků z privátního úložiště GitHub. 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Správa pracovních prostorů napříč klienty pomocí Azure Lighthouse

Jak je uvedeno výše, v mnoha scénářích se můžou různé pracovní prostory Azure Sentinel nacházet v různých klientech Azure AD. Pomocí [Azure Lighthouse](../lighthouse/overview.md) můžete v rámci hranic klientů rozšíříte všechny aktivity mezi jednotlivými pracovními prostory a umožníte uživatelům v tenantovi pro správu pracovat v pracovních prostorech Azure Sentinel ve všech klientech. Po [zprovoznění](../lighthouse/how-to/onboard-customer.md)služby Azure Lighthouse můžete pomocí [voliče adresář a odběr](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants) v Azure Portal vybrat všechna předplatná obsahující pracovní prostory, které chcete spravovat, aby se zajistilo, že budou všechny dostupné v různých selektorech pracovního prostoru na portálu.

Při používání Azure Lighthouse se doporučuje vytvořit skupinu pro každou roli Sentinel Azure a delegovat oprávnění z každého tenanta na tyto skupiny.

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste zjistili, jak můžete rozšířit možnosti služby Azure Sentinel napříč několika pracovními prostory a klienty. Praktické pokyny k implementaci architektury Azure Sentinel pro různé pracovní prostory v následujících článcích:

- Naučte [se pracovat s více klienty](./multiple-tenants-service-providers.md) v Azure Sentinel pomocí Azure Lighthouse.
- Naučte se hladce [Zobrazit a spravovat incidenty ve více pracovních prostorech](./multiple-workspace-view.md) .