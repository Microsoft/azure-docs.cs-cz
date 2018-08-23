---
title: Integrační služby jednoduché podnikové integrace referenční architektura Azure
description: Popisuje referenční architektury, který ukazuje, jak implementovat vzor jednoduché podnikové integrace s Azure Logic Apps a Azure API Management.
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
ms.openlocfilehash: f73a9e59c0add664128b506172182afe566ca670
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444506"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>Referenční architektura: jednoduché podniková integrace

Tato referenční architektura ukazuje sadu osvědčených postupů, které můžete použít k integraci aplikace, která využívá integrace služeb Azure. Tato architektura může sloužit jako základ pro mnoho různých aplikačních vzory, které vyžadují rozhraní API HTTP, pracovních postupů a Orchestrace.

![Diagram architektury – jednoduché podniková integrace](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

*Existuje mnoho možných aplikací pro integraci technologie. Jsou v rozsahu od jednoduché point-to-point aplikace k úplné podnikové aplikace Azure Service Bus. Architektura série popisuje opakovaně použitelné součásti, které může použít k sestavení aplikace obecný integrace. Architekti zvažte komponenty, které potřebují k implementaci pro své aplikace a infrastrukturu.*

## <a name="architecture"></a>Architektura

Tato architektura se skládá z následujících komponent:

- **Skupina prostředků**. [Skupina prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) je logický kontejner prostředků Azure.
- **Azure API Management**. [API Management](https://docs.microsoft.com/azure/api-management/) je plně spravovaná platforma, která se používá k publikování, zabezpečení a transformujte rozhraní API protokolu HTTP.
- **Portál Azure API Management Developer**. Každá instance Azure API Management obsahuje přístup k [portál pro vývojáře](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Portál pro vývojáře rozhraní API Management poskytuje přístup k dokumentaci a ukázky kódu. Testování rozhraní API na portálu pro vývojáře.
- **Azure Logic Apps**. [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) je platformy bez serveru, který se používá k vytvoření pracovního postupu enterprise a integrace.
- **Konektory**. Logic Apps používá [konektory](https://docs.microsoft.com/azure/connectors/apis-list) pro připojení k nejčastěji používají služby. Už má stovky různých konektory Logic Apps, ale můžete také vytvořit vlastní konektor.
- **IP adresa**. Služba Azure API Management má pevné veřejné [IP adresu](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) a názvu domény. Výchozí název domény je subdoména azure-api.net, jako je například contoso.azure-api.net, ale [vlastních domén](https://docs.microsoft.com/azure/api-management/configure-custom-domain) je také možné nakonfigurovat. Logic Apps a služby Service Bus taky mít veřejnou IP adresu. Ale v této architektuře Omezujeme přístup k volání koncových bodů aplikace logiky na IP adresu služby API Management (pro zabezpečení). Volání služby Service Bus jsou zabezpečená pomocí sdíleného přístupového podpisu (SAS).
- **Azure DNS**. [Azure DNS](https://docs.microsoft.com/azure/dns/) je hostitelská služba určená pro domény DNS. Azure DNS poskytuje překlad názvů s využitím infrastruktury Microsoft Azure. Hostovat v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturační účely ostatních služeb Azure. Pokud chcete použít vlastní název domény třeba contoso.com, vytvořte záznamy DNS, které mapují vlastní název domény na IP adresu. Další informace najdete v tématu [konfigurace vlastního názvu domény ve službě API Management](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD):** Použití [Azure AD](https://docs.microsoft.com/azure/active-directory/) nebo jiného zprostředkovatele identit pro ověřování. Azure AD poskytuje ověřování pro přístup k koncových bodů rozhraní API pomocí předání [webového tokenu JSON pro službu API Management](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) k ověření. Azure AD můžete zabezpečit přístup k portálu pro vývojáře rozhraní API Management (jenom úrovně Standard a Premium).

Tato architektura se některé vzory, které jsou zásadní pro její činnosti:

- Složený rozhraní API jsou vytvořena pomocí logic apps. Jejich Orchestrace volání software jako služba (SaaS) systémů, služeb Azure a libovolné rozhraní API, které jsou publikovány do API managementu. [Aplikace logiky se také publikují](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) prostřednictvím portálu pro vývojáře rozhraní API Management.
- Aplikace použít Azure AD, aby [získání tokenu zabezpečení OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) , který je potřeba k získání přístupu k rozhraní API.
- Azure API Management [ověří token zabezpečení](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) a pak předá požadavek back endové rozhraní API nebo logiku aplikace.

## <a name="recommendations"></a>Doporučení

Vašim konkrétním požadavkům mohou lišit od obecné architektuře popsané v tomto článku. Jako výchozí bod použijte doporučení uvedená v této části.

### <a name="azure-api-management-tier"></a>Azure API Management úrovně

Použití úrovně rozhraní API Management Basic, Standard nebo Premium. Vrstvy představují produkční smlouvy o úrovni služeb (SLA) a podporují horizontální navýšení kapacity v rámci oblasti Azure (počet jednotek se liší podle úrovně). Na úrovni Premium také podporuje škálování ve víc oblastech Azure. Základní úroveň, kterou zvolíte na úroveň propustnosti a vaši sadu funkcí. Další informace najdete v tématu [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).

Pro všechny instance API Management se účtují, když jsou spuštěné. Pokud máte vertikálně navýšit a nemusíte tuto úroveň výkonu celou dobu, zvažte, s využitím služby API Management, fakturací po hodinách a vertikálně snížit kapacitu.

### <a name="logic-apps-pricing"></a>Ceny Logic Apps

Logic Apps používá [bez serveru](logic-apps-serverless-overview.md) modelu. Fakturace se vypočítá podle provádění akcí a konektorů. Další informace najdete v tématu [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). V současné době nejsou žádné požadavky na úroveň pro Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps pro asynchronní volání rozhraní API

Logic Apps funguje nejlépe ve scénářích, které nevyžadují s nízkou latencí. Například je nejvhodnější pro asynchronní nebo volání částečně dlouho běžící rozhraní API. Pokud je povinný (například volání, které blokuje uživatelské rozhraní) s nízkou latencí, doporučujeme implementace tohoto rozhraní API nebo operace pomocí různých technologií. Například pomocí Azure Functions nebo webové rozhraní API, které nasazujete pomocí služby Azure App Service. Nadále doporučujeme přední příjemci rozhraní API pro rozhraní API pomocí služby API Management.

### <a name="region"></a>Oblast

Zřízení API Management a Logic Apps ve stejné oblasti kvůli minimalizaci latence sítě. Obecně volte oblast co nejblíže vašim uživatelům.

Skupina prostředků má také oblast. Oblast určuje, kde se ukládají metadata nasazení a kde šablonu nasazení spustí. Skupinu prostředků a její prostředky umístěte ve stejné oblasti vylepšit dostupnost během nasazení.

## <a name="scalability"></a>Škálovatelnost

API Management správcům přidala [zásady ukládání do mezipaměti](../api-management/api-management-howto-cache.md) případně pokud chcete zvýšit škálovatelnost služby. Ukládání do mezipaměti také pomáhá snížit zatížení na back endové služby.

Azure API Management Basic, Standard a Premium můžete škálovat v oblasti Azure nabízí větší kapacitu. Správci můžou použít **metriku kapacity** možnost **metriky** nabídky k analýze využití své služby a potom vertikálně navýšit kapacitu nebo vertikálně snížit kapacitu podle potřeby.

Doporučení pro škálování služby API Management:

- Škálování je potřeba vzít v úvahu vzory provozu. Zákazníci s více těkavých vzory provozu mají větší potřebovat větší kapacitu.
- Konzistentní kapacity nad rámec 66 % může znamenat potřeba vertikálně navýšit kapacitu.
- Konzistentní kapacitu menší než 20 % může znamenat příležitost můžete vertikálně snížit kapacitu.
- Vždy doporučujeme k zátěžovému testu služby API Management se reprezentativní zatížení před povolením zatížení v produkčním prostředí.

Úroveň služeb Premium můžete škálovat napříč několika oblastmi Azure. Zákazníci, kteří nasazují díky škálování služby ve víc oblastech Azure získat vyšší úroveň SLA (99,95 % oproti 99,9 %) a můžete zřizovat služby blízko uživatelům v několika oblastech.

Aplikace logiky bez serveru modelu znamená, že správci nemusíte plánovat škálovatelnost služby. Služby se automaticky škáluje podle potřeby.

## <a name="availability"></a>Dostupnost

Smlouva SLA pro službu Azure API Management v současné době je 99,9 % pro úrovně Basic, Standard a Premium. Konfigurace úrovně Premium s nasazení nejméně jedné jednotky ve dvou nebo více oblastech mají smlouvou SLA zajišťující 99,95 %.

Smlouva SLA pro Azure Logic Apps se v současné době 99,9 %.

### <a name="backups"></a>Zálohování

Konfigurace Azure API Management by měl být [pravidelně zálohovány](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (podle pravidelnost změny). Záložní soubory by měl být uložené v umístění nebo oblasti Azure, která se liší od ve kterém se služba nachází. Zákazníci pak můžou zvolit jednu ze dvou možností pro své strategie zotavení po havárii:

- V události zotavení po havárii se zřizuje nové instance API managementu, zálohování, budou obnoveny do nové instance a záznamy DNS jsou repointed.
- Zákazníci si uložte kopii pasivní své služby v jiné oblasti Azure (bez dalších nákladů). Zálohy jsou pravidelně obnovit kopii. V události zotavení po havárii musí být pouze záznamy DNS repointed obnovení služby.

Protože aplikace logiky můžete znovu vytvořit rychle a bez serveru, jsou zálohované serverem uložení kopie přidružené šablony Azure Resource Manageru. Šablony lze uložit do správy zdrojového kódu a je možné integrovat s procesem průběžné integrace a nasazování (CI/CD) na základě.

Pokud aplikace logiky, která byla publikována prostřednictvím služby API Management se přesune do jiného datového centra, aktualizujte umístění aplikace. Aktualizace umístění aplikace, použijte základní skript prostředí PowerShell k aktualizaci **back-endu** vlastnost rozhraní API.

## <a name="manageability"></a>Možnosti správy

Vytvořit samostatné skupiny prostředků pro produkční prostředí, vývoje a testovacích prostředí. Pomocí samostatných skupin prostředků usnadňuje správu nasazení, odstranění nasazení v testovacím a přiřadit přístupová práva.

Při přiřazování prostředků skupinám prostředků zvažte následující faktory:

- **Životní cyklus**. Obecně platí vložte prostředky, které mají stejný životní cyklus ve stejné skupině prostředků.
- **Přístup**. Můžete použít [řízení přístupu na základě rolí](../role-based-access-control/overview.md) (RBAC) k uplatnění zásad přístupu k prostředkům ve skupině.
- **Fakturace**. Můžete zobrazit souhrnné náklady pro skupinu prostředků.
- **Cenová úroveň pro službu API Management**. Doporučujeme používat úroveň Developer pro vývoj a testovací prostředí. Pro předprodukční prostředí doporučujeme nasadit repliku produkčního prostředí, spouštění testů a vypínání pro minimalizaci nákladů.

Další informace naleznete v tématu [Přehled Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Nasazení

Doporučujeme, abyste použili [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) nasazování API Management a Logic Apps. Šablony usnadňují automatizaci nasazení prostřednictvím Powershellu nebo rozhraní příkazového řádku Azure.

Doporučujeme, abyste uvedení Azure API Management a všechny jednotlivé logic apps do své vlastní, samostatné šablony Resource Manageru. Když použijete samostatné šablony, můžete ukládat prostředky v systémy správy zdrojového kódu. Můžete také nasadit prostředky společně nebo samostatně jako součást procesu nasazení CI/CD.

### <a name="versions"></a>Verze

Pokaždé, když provedete konfiguraci změnit na aplikaci logiky (nebo nasazení aktualizaci pomocí šablony Resource Manageru), kopii této verze se ukládají pro usnadnění práce (jsou zachovány všechny verze, které mají historii spuštění). Tyto verze můžete použít ke sledování historických změn a zvýšení úrovně verzi, aby si aktuální konfiguraci aplikace logiky. Například můžete efektivně se vrátit zpět aplikaci logiky.

Služba API Management má dva různé (ale praktického) [koncepty správy verzí](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Verze, které se používají k zajištění vašeho rozhraní API příjemců řadu rozhraní API můžete využívat podle svých potřeb (třeba v1, v2 nebo beta, produkčním prostředí).
- Revize, které správcům rozhraní API umožňují bezpečně provádět změny rozhraní API a pak nasadit změny na uživatele s volitelné komentáře.

V rámci nasazení je vhodné vzít v úvahu revize rozhraní API Management jako způsob, jak bezpečně provést změny, zachovat historii změn a informovat zákazníky provedené změny. Revize lze vytvořit v prostředí pro vývoj a nasazení mezi další prostředí pomocí šablon Resource Manageru.

Ačkoli použití revizí k otestování rozhraní API před prováděním "aktuální" a přístupný pro uživatele, nedoporučujeme používat tento mechanismus pro zatížení nebo testování integrace. Místo toho použijte samostatný test nebo předprovozním prostředí.

### <a name="configuration"></a>Konfigurace

Nikdy vrácení se změnami hesla, přístupové klíče nebo připojovací řetězce do správy zdrojového kódu. Pokud tyto hodnoty jsou povinné, použijte odpovídající postup k nasazení a zabezpečit tyto hodnoty. 

Ve službě Logic Apps by žádné citlivé hodnoty požadované aplikace logiky (která nelze vytvořit v podobě připojení) uložené ve službě Azure Key Vault a uvedené ze šablony Resource Manageru. Doporučujeme také, pro každé prostředí použijete parametry šablony nasazení a soubory parametrů. Další informace najdete v tématu [zabezpečení parametry a vstupy v pracovním postupu](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Ve službě API Management se spravují tajných kódů pomocí objektů nazývaných *pojmenované hodnoty* nebo *vlastnosti*. Objekty bezpečně ukládat hodnoty, které mohou být přístupné v zásady služby API Management. Další informace najdete v tématu [spravovat tajné kódy ve službě API Management](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostika a monitorování

[API Management](../api-management/api-management-howto-use-azure-monitor.md) a [Logic Apps](logic-apps-monitor-your-logic-apps.md) podporovaly provozní monitorování prostřednictvím [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Platforma Azure Monitor poskytuje informace na základě metrik, které jsou konfigurovány pro každou službu. Azure Monitor je standardně povolená.

Tyto možnosti jsou taky dostupné u každé služby:

- Je možné odeslat protokoly aplikace logiky [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) pro hlubší analýzu a mnoha.
- API Management podporuje konfiguraci služby Azure Application Insights pro DevOps monitorování.
- API Management podporuje [šablona řešení Power BI pro vlastní rozhraní API analýzy](http://aka.ms/apimpbi). Zákazníci slouží k vytvoření vlastních řešení analýzy vlastní šablonu řešení. Sestavy jsou k dispozici v Power BI pro obchodní uživatele.

## <a name="security"></a>Zabezpečení

Tato část uvádí informace o zabezpečení, které jsou specifické pro služby Azure, které jsou popsané v tomto článku a které jsou nasazené v architektuře, jak je popsáno. Nejedná se o úplný seznam osvědčených postupů zabezpečení.

- Pomocí řízení přístupu na základě role (RBAC) zajistit odpovídající úrovně přístupu pro uživatele.
- Zabezpečte veřejné koncové body rozhraní API ve službě API Management s použitím OAuth/OpenID Connect. K zabezpečení veřejné koncové body rozhraní API, nakonfigurujte zprostředkovatele identity a přidejte zásad ověřování JSON Web Token (JWT).
- Připojení k back endové služby ze služby API Management s použitím vzájemnými certifikáty.
- Zabezpečení aplikací logiky založené na aktivační událost HTTP tak, že vytvoříte seznam povolených IP adres adres, který odkazuje na rozhraní API správy IP adres. Přidat na seznam povolených IP adresu brání volání aplikace logiky z veřejného Internetu bez nutnosti první prostřednictvím služby API Management.

## <a name="next-steps"></a>Další postup

- Další informace o [podniková integrace pomocí front a události](logic-apps-architectures-enterprise-integration-with-queues-events.md).
