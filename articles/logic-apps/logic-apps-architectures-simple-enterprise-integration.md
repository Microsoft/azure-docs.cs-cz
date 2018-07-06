---
title: Integrační služby Azure – jednoduché podniková integrace
description: Referenční architektura ukazuje, jak implementovat vzor jednoduché podnikové integrace s Azure Logic Apps a Azure API Management
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: df86ca5aed405ab5eda05c1dd207f0b6656bfd96
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860193"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Jednoduchá integrace Enterprise – referenční architektury

## <a name="overview"></a>Přehled

Tato referenční architektura představuje sadu osvědčených postupů pro integraci aplikace, která využívá integrace služeb Azure. Tato architektura může sloužit jako tento základ mnoha vzory jinou aplikaci vyžadující rozhraní API HTTP, pracovních postupů a Orchestrace.

*Existuje mnoho možných aplikací integrace technologie z bodu aplikace jednoduchý point-to na úplné podnikové služby Service bus. Tato architektura řada stanoví opakovaně použitelné součásti, které může použít pro sestavení aplikace obecný integrace – architekty zvažte, jaké konkrétní komponenty, bude nutné implementovat pro svoje aplikace a infrastrukturu.*

   ![Diagram architektury – jednoduché podniková integrace](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Architektura

Tato architektura se skládá z následujících komponent:

- Skupina prostředků. [Skupina prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) je logický kontejner prostředků Azure.
- Azure API Management. [Azure API Management](https://docs.microsoft.com/azure/api-management/) je plně spravovaná platforma pro publikování, zabezpečení a transformace rozhraní HTTP API.
- Portál pro vývojáře Azure API Management. Každá instance Azure API Management obsahuje [portál pro vývojáře](https://docs.microsoft.com/azure/api-management/api-management-customize-styles), poskytne přístup k dokumentaci, ukázky kódu a možnost otestovat rozhraní API.
- Azure Logic Apps. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) je platforma bez serveru pro tvorbu podnikových pracovního postupu a integrace.
- Konektory. [Konektory](https://docs.microsoft.com/azure/connectors/apis-list) se používají v Logic Apps pro připojení k nejčastěji používané služby. Už má stovky různých konektory Logic Apps, ale mohou být také vytvořeny pomocí vlastního konektoru.
- IP adresa. Služba Azure API Management má pevné veřejné [IP adresu](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) a názvu domény. Název domény je subdoména azure-api.net, jako je například contoso.azure-api.net. Logic Apps a služby Service Bus taky mít veřejnou IP adresu. ale v této architektuře Omezujeme přístup k volání koncových bodů aplikace logiky pro pouze správy IP adres z rozhraní API (pro zabezpečení). Volání služby Service Bus jsou zabezpečená pomocí sdíleného přístupového podpisu.
- Azure DNS. [Azure DNS](https://docs.microsoft.com/azure/dns/) je hostitelská služba určená pro domény DNS a zajišťuje překlad názvů s využitím infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. Chcete-li použít vlastní název domény (například contoso.com) vytvořte záznamy DNS, které mapují vlastní název domény na IP adresu. Další informace najdete v části Konfigurace vlastního názvu domény ve službě Azure API Management.
- Azure Active Directory (Azure AD). Použití [Azure AD](https://docs.microsoft.com/azure/active-directory/) nebo jiného zprostředkovatele identit pro ověřování. Azure AD poskytuje ověřování ke koncovým bodům přístup k rozhraní API (předáním [webového tokenu JSON pro službu API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) ověřit) a může zabezpečit přístup k portálu pro vývojáře pro správu rozhraní API (jenom úrovně Standard a Premium).

Tato architektura se některých základních vzorů pro své činnosti:

2. Složený rozhraní API se vytvářejí pomocí Logic Apps; Orchestrace volání k systémům SAAS, služby Azure a libovolné rozhraní API publikované do API managementu. [Logic Apps se také publikují](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) prostřednictvím portálu pro vývojáře rozhraní API pro správu.
3. Aplikace [získání tokenu zabezpečení OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) nezbytné pro získání přístupu k rozhraní API pomocí Azure Active Directory.
4. Azure API Management [ověří token zabezpečení](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)a předá požadavek back-endové aplikace API a logiky.

## <a name="recommendations"></a>Doporučení

Vašim konkrétním požadavkům mohou lišit od obecné architektuře zde popsané. Jako výchozí bod použijte doporučení uvedená v této části.

### <a name="azure-api-management-tier"></a>Azure API Management úrovně

Použít na úrovni Basic, Standard nebo Premium úrovní, protože nabízí produkční smlouvy SLA a podporují horizontální navýšení kapacity v rámci oblasti Azure (počet jednotek se liší podle úrovně). Úroveň Premium také podporuje škálování ve víc oblastech Azure. Základní úroveň, kterou jste zvolili na úroveň propustnosti a funkce. Další informace najdete v tématu [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).

Pro všechny instance API Management se účtují, když jsou spuštěné. Pokud máte vertikálně navýšit a nemusíte tuto úroveň výkonu celou dobu, zvažte využití výhod hodinové fakturace a škálování rozhraní API správy.

### <a name="logic-apps-pricing"></a>Ceny Logic Apps

Logic Apps funguje jako [bez serveru](logic-apps-serverless-overview.md) model fakturace se vypočítá podle provádění akcí a konektorů. Zobrazit [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) Další informace. Aktuálně neexistují žádné požadavky na úroveň pro Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps pro asynchronní volání rozhraní API

Logic Apps optimální konfigurace ve scénářích, které nevyžadují s nízkou latencí – například asynchronní nebo běžící středníkem dlouhé volání rozhraní API. Pokud je potřeba nízkou latenci (například volání, které blokuje uživatelské rozhraní) doporučujeme implementace tohoto rozhraní API nebo operace pomocí různých technologií, např. Azure Functions nebo webové rozhraní API nasazené pomocí služby App Service. Doporučujeme, aby se toto rozhraní API přední stěnou pomocí služby API Management k rozhraní API příjemců.

### <a name="region"></a>Oblast

Zřízení API Management a Logic Apps ve stejné oblasti kvůli minimalizaci latence sítě. Vyberte oblast, která je vašim uživatelům nejbližší.

Skupina prostředků má také oblast, která určuje, kde se ukládají metadata nasazení, kde se spouštějí šablonu nasazení z. Skupinu prostředků a její prostředky umístěte do stejné oblasti. Zlepšíte tím dostupnost během nasazení.

## <a name="scalability-considerations"></a>Aspekty zabezpečení

API Management správcům přidala [zásady ukládání do mezipaměti](../api-management/api-management-howto-cache.md) případně snížit zatížení jejich back-endové služby a zvýšení škálovatelnosti služby.

V oblasti Azure nabízí větší kapacitu je lze s škálovat úrovně Azure API Management Basic, Standard a Premium. Správce můžete použít metriky kapacity v rámci nabídky metrik pro analýzu využití svých služeb a vertikální navýšení kapacity nebo vertikálně snížit kapacitu podle potřeby.

Doporučení pro škálování služby API Management:

- Škálování je potřeba vzít v úvahu vzory provozu – zákazníci s více těkavých vzory provozu bude mít větší potřebovat větší kapacitu.
- Konzistentní kapacity nad rámec 66 % může znamenat potřeba vertikálně navýšit kapacitu.
- Konzistentní kapacitu menší než 20 % může znamenat příležitost můžete vertikálně snížit kapacitu.
- Vždy doporučujeme načíst testovací služby API Management se reprezentativní zatížení před povolením v produkčním prostředí.

Úroveň služeb Premium můžete škálovat napříč několika oblastmi Azure. Zákazníci, kteří nasazují tímto způsobem získají vyšší úroveň SLA (99,9 % na rozdíl od 99,95 %) a můžete zřizovat služby blízko uživatelům v několika oblastech.

Aplikace logiky bez serveru modelu znamená, že správce není nutné provést další pozornost pro škálovatelnost služby; Služba se automaticky škáluje podle potřeby.

## <a name="availability-considerations"></a>Aspekty dostupnosti

V době psaní smlouvu o úrovni služeb (SLA) pro službu Azure API Management je 99,9 % pro úrovně Basic, Standard a Premium. Konfigurace úrovně Premium s nasazení nejméně jedné jednotky ve dvou nebo více oblastech mají smlouvou SLA zajišťující 99,95 %.

V době psaní smlouvu o úrovni služeb (SLA) pro Azure Logic Apps je 99,9 %.

### <a name="backups"></a>Zálohování

Konfigurace služby Azure API Management by měla být [pravidelně zálohovány](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (odpovídajícím způsobem na základě pravidelnost změny) a že záložní soubory uložené v umístění nebo oblasti Azure v různých ve kterém se služba nachází. Zákazníci pak můžou zvolit jednu ze dvou možností pro své strategie zotavení po Havárii:

1. V případě zotavení po Havárii zřídit nové instance API managementu, obnovení zálohy do ní a záznamy DNS jsou repointed.
2. Udržování zákazníků jsou pravidelně obnovit pasivní kopii své služby v jiné oblasti Azure (bez dalších nákladů) zálohování. V případě zotavení po Havárii musí být pouze záznamy DNS repointed obnovení služby.

Logic Apps můžete znovu vytvořit velmi rychle a jsou bez serveru, jsou zálohované serverem uložení kopie přidružené šablony Azure Resource Manageru. Ty se můžou ukládat do zdrojového ovládacího prvku/integrované do procesu průběžné integrace a nasazování (CI/CD) zákazníky.

Logic Apps, které jsou zveřejněné prostřednictvím služby API Management potřebovat jejich umístění aktualizace by měla při přechodu jiné datové centrum. Můžete to provést prostřednictvím jednoduchý skript prostředí PowerShell, který umožňuje aktualizovat vlastnost back-Endového rozhraní API.

## <a name="manageability-considerations"></a>Aspekty správy

Vytvořit samostatné skupiny prostředků pro produkční prostředí, vývoje a testovacích prostředí. Usnadníte tak správu nasazení, odstranění nasazení v testovacím prostředí a přiřazení přístupových práv.
Při přiřazování prostředků skupinám prostředků zvažte následující:

- Životní cyklus. Prostředky se stejným životním cyklem umístěte do stejné skupiny prostředků.
- Přístup. Můžete použít [řízení přístupu na základě Role](../role-based-access-control/overview.md) (RBAC) k uplatnění zásad přístupu k prostředkům ve skupině.
- Fakturace. Můžete zobrazit souhrnné náklady na skupinu prostředků.
- Cenová úroveň pro službu API Management – doporučujeme použít úroveň Developer pro vývojová a testovací prostředí. Pro předprodukční režim doporučujeme nasadit repliku produkčního prostředí, spouštění testů a vypínání pro minimalizaci nákladů.

Další informace najdete v tématu [skupiny prostředků](../azure-resource-manager/resource-group-overview.md) Přehled.

### <a name="deployment"></a>Nasazení

Doporučujeme, abyste použili [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) nasazení Azure API Management a Azure Logic Apps. Šablony usnadňují automatizaci nasazení prostřednictvím Powershellu nebo rozhraní příkazového řádku Azure (CLI).

Doporučujeme, abyste uvedení Azure API Management a všechny jednotlivé Logic Apps do své vlastní samostatný šablon Resource Manageru. To vám umožní ukládání v systémy správy zdrojového kódu. Tyto šablony můžete nasadit pak společně nebo samostatně jako součást procesu nasazení s nepřetržitou integrací (CI/CD).

### <a name="versions"></a>Verze

Pokaždé, když provedete konfiguraci změnit na aplikaci logiky (nebo nasazení aktualizaci pomocí šablony Resource Manageru), kopii této verze se ukládají pro usnadnění práce (všechny verze, které mají historii spuštění zůstanou zachovaná). Tyto verze můžete použít ke sledování historické změny a také podporu verze na aktuální konfiguraci aplikace logiky. To znamená je můžete efektivně vrácení zpět aplikaci logiky, například.

Služba API Management má dva různé (ale praktického) [koncepty správy verzí](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Verze použitý pro vaše zákazníky řadu rozhraní API může používat podle svých potřeb (např. v1, v2 nebo beta, produkčním prostředí).
- Revize umožňuje správcům bezpečně provádět změny rozhraní API a nasadit je uživatelům s volitelné komentáře rozhraní API.

V rámci nasazení – API Management revize by měly být považovány způsob, jak provádět změny bez obav, zachovat historii změn a informovat zákazníky provedené změny. Revizi lze vytvořit v prostředí pro vývoj a nasazení mezi další prostředí pomocí šablon Resource Manageru.

Zatímco revize můžete použít k testování rozhraní API, než ho tvoří "aktuální" a přístupné pro uživatele, nedoporučujeme používat tento mechanismus pro zatížení nebo testování integrace – samostatný test nebo předprovozní prostředí by měl použít místo toho.

### <a name="configuration"></a>Konfigurace

Nikdy nezapisujte hesla, přístupové klíče nebo připojovací řetězce v do správy zdrojového kódu. Pokud nejsou potřeba, použijte odpovídající postup pro nasazení a zabezpečit tyto hodnoty. 

Ve službě Logic Apps by žádné citlivé hodnoty potřebné v rámci aplikace logiky (která nelze vytvořit v podobě připojení) uložené ve službě Azure Key Vault a uvedené ze šablony Resource Manageru. Doporučujeme také použití parametrů šablony nasazení společně s soubory parametrů pro každé prostředí. Další pokyny k [zabezpečení parametry a vstupy v pracovním postupu](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Ve službě API Management se spravují tajných kódů pomocí objektů, jako s názvem hodnoty nebo vlastnosti. Tyto zásady služby API Management bezpečně uložte hodnoty, které mohou být přístupné. V tématu Jak [spravovat tajné kódy ve službě API Management](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostika a monitorování

Obě [API Management](../api-management/api-management-howto-use-azure-monitor.md) a [Logic Apps](logic-apps-monitor-your-logic-apps.md) podporují provozní monitorování prostřednictvím [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure Monitor je ve výchozím nastavení povolené a bude poskytovat informace na základě různých metrik nakonfigurované pro každou službu.

Kromě toho existují další možnosti pro každou službu:

- Je možné odeslat protokoly aplikace logiky [Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) pro hlubší analýzu a mnoha.
- API Management podporuje konfiguraci doplňku Application Insights pro Devops monitorování.
- API Management podporuje [šablona řešení Power BI pro vlastní rozhraní API analýzy](http://aka.ms/apimpbi). Tato šablona řešení umožňuje zákazníkům vytvářet své vlastní řešení vlastní analýzy s sestav dostupných v Power BI pro obchodní uživatele.

## <a name="security-considerations"></a>Aspekty zabezpečení

Tato část uvádí informace o zabezpečení, které jsou specifické pro služby Azure popisované v tomto článku, jak je popsáno nasazení v architektuře. Nejedná se o úplný seznam osvědčených postupů zabezpečení.

- Pomocí řízení přístupu na základě role (RBAC) zajistit odpovídající úrovně přístupu pro uživatele.
- Zabezpečení ve službě API Management pomocí OAuth nebo Open IDConnect veřejné koncové body rozhraní API. K tomu konfigurace zprostředkovatele identity a přidáním zásady ověřování tokenů JWT.
- Připojení k back-endové služby ze služby API Management pomocí vzájemných certifikátů
- Zabezpečení aplikací logiky na základě aktivační události HTTP tak, že vytvoříte seznam povolených adres IP adresu odkazující na IP adresu rozhraní API Management. Díky tomu volání aplikace logiky z veřejného Internetu bez nutnosti první prostřednictvím služby API Management.

Tato referenční architektura vám ukázal, jak vytvořit jednoduchý podnikové integrace platformu pomocí integrace služby Azure.

## <a name="next-steps"></a>Další postup

- [Podniková integrace pomocí front a události](logic-apps-architectures-enterprise-integration-with-queues-events.md)
