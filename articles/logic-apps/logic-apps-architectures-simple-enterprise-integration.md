---
title: Jednoduché podnikové integrace architektury vzor – integrační služby Azure
description: Tato referenční architektura ukazuje, jak můžete implementovat vzor jednoduché podnikové integrace pomocí Azure Logic Apps a Azure API Management
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 7081c9e4f6e6deee196255f04180a8f2cc792876
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122491"
---
# <a name="simple-enterprise-integration-architecture"></a>Jednoduché podnikové integrace architektury

Tento článek popisuje architekturu podnikové integrace, který používá osvědčených postupů, které lze použít k integraci aplikace při používání integrace služby Azure. Tato architektura může použít jako základ pro mnoho různých aplikačních vzory, které vyžadují rozhraní API HTTP, pracovních postupů a Orchestrace.

![Diagram architektury – jednoduché podniková integrace](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

Tato série popisuje opakovaně použitelné součásti, které může použít k sestavení aplikace obecný integrace. Protože integrace technologie má mnoho možných aplikací, od jednoduchého typu point-to-point aplikace do úplné podnikové aplikace Azure Service Bus, vezměte v úvahu komponenty, které je nutné implementovat pro vaše aplikace a infrastrukturu.

## <a name="architecture-components"></a>Komponenty architektury

Tato architektura integrace enterprise zahrnuje tyto komponenty:

- **Skupina prostředků**: A [skupiny prostředků](../azure-resource-manager/resource-group-overview.md) je logický kontejner prostředků Azure.

- **Azure API Management**: [API Management](https://docs.microsoft.com/azure/api-management/) service je plně spravovaná platforma pro publikování, zabezpečení a transformace rozhraní HTTP API.

- **Portál Azure API Management Developer**: Každá instance Azure API Management poskytuje přístup k [portál pro vývojáře](../api-management/api-management-customize-styles.md). Tento portál poskytuje přístup k dokumentaci a ukázky kódu. Můžete také otestovat rozhraní API na portálu pro vývojáře.

- **Služba Azure Logic Apps**: [Logic Apps](../logic-apps/logic-apps-overview.md) je platforma pro vytváření pracovních postupů enterprise a integrace bez serveru.

- **Konektory**: Logic Apps používá [konektory](../connectors/apis-list.md) pro připojení k nejčastěji používané služby. Logic Apps nabízí stovky konektorů, ale můžete také vytvořit vlastní konektor.

- **IP adresa**: Služba Azure API Management má pevnou veřejnou [IP adresu](../virtual-network/virtual-network-ip-addresses-overview-arm.md) a názvu domény. Výchozí název domény je subdoména azure-api.net, například contoso.azure-api.net, ale můžete taky nakonfigurovat [vlastních domén](../api-management/configure-custom-domain.md). Logic Apps a služby Service Bus taky mít veřejnou IP adresu. Pro zabezpečení, ale tato architektura omezuje přístup pro volání koncových bodů aplikace logiky na IP adresu služby API Management. Volání služby Service Bus jsou zabezpečená pomocí sdíleného přístupového podpisu (SAS).

- **Azure DNS**: [Azure DNS](https://docs.microsoft.com/azure/dns/) je hostitelská služba určená pro domény DNS. Azure DNS poskytuje překlad názvů s využitím infrastruktury Microsoft Azure. Hostovat v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API, nástrojů a fakturační účely ostatních služeb Azure. Pokud chcete používat vlastní název domény, třeba contoso.com, vytvořte záznamy DNS, které mapují vlastní název domény na IP adresu. Další informace najdete v tématu [konfigurace vlastního názvu domény ve službě API Management](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**: můžete použít [Azure AD](https://docs.microsoft.com/azure/active-directory/) nebo jiného zprostředkovatele identit pro ověřování. Azure AD poskytuje ověřování pro přístup k koncových bodů rozhraní API pomocí předání [webového tokenu JSON pro službu API Management](../api-management/policies/authorize-request-based-on-jwt-claims.md) k ověření. Úrovně Standard a Premium Azure AD můžete zabezpečit přístup k portálu pro vývojáře rozhraní API Management.

## <a name="patterns"></a>Vzory 

Tato architektura používá některé vzory, které jsou základem pro operaci:

* Složený rozhraní API jsou sestaveny pomocí logic apps, které Orchestrace volání software jako služba (SaaS) systémů, služeb Azure a libovolné rozhraní API, které jsou publikovány do API managementu. Aplikace logiky se také [publikované prostřednictvím portálu pro vývojáře rozhraní API Management](../api-management/import-logic-app-as-api.md).

* Aplikace použít Azure AD pro [získání tokenu zabezpečení OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md) , který je potřeba k získání přístupu k rozhraní API.

* Azure API Management [ověří token zabezpečení](../api-management/api-management-howto-protect-backend-with-aad.md) a pak předá požadavek back endové rozhraní API nebo logiku aplikace.

## <a name="recommendations"></a>Doporučení

Vašim konkrétním požadavkům mohou lišit od obecné architektuře, která je popsána v tomto článku. Jako výchozí bod použijte doporučení uvedená v této části.

### <a name="azure-api-management-tier"></a>Azure API Management úrovně

Použití úrovně rozhraní API Management Basic, Standard nebo Premium. Tyto vrstvy představují produkční smlouvy o úrovni služeb (SLA) a podporují horizontálním navýšením kapacity v rámci oblasti Azure. Počet jednotek se liší podle úrovně. Na úrovni Premium také podporuje škálování ve víc oblastech Azure. Volba úrovně na základě sady funkcí a úroveň požadované propustnosti. Další informace najdete v tématu [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).

Pro všechny instance API Management se účtují, když jsou spuštěné. Pokud máte vertikálně navýšit a nemusíte tuto úroveň výkonu celou dobu, zvažte, s využitím služby API Management, fakturací po hodinách a vertikálně snížit kapacitu.

### <a name="logic-apps-pricing"></a>Ceny Logic Apps

Logic Apps používá [bez serveru](../logic-apps/logic-apps-serverless-overview.md) modelu. Fakturace se vypočítá podle provádění akcí a konektorů. Další informace najdete v tématu [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/). V současné době nejsou žádné požadavky na úroveň pro Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Logic Apps pro asynchronní volání rozhraní API

Logic Apps funguje nejlépe ve scénářích, které nevyžadují s nízkou latencí. Například Logic Apps je nejvhodnější pro asynchronní nebo volání částečně dlouho běžící rozhraní API. Pokud je potřeba s nízkou latencí, například volání, které blokuje uživatelské rozhraní implementovat rozhraní API nebo operace pomocí různých technologií. Například pomocí Azure Functions nebo webové rozhraní API, které nasazujete pomocí služby Azure App Service. API Management můžete použijte k front-rozhraní API pro vaše zákazníky.

### <a name="region"></a>Oblast

Kvůli minimalizaci latence sítě, vyberte stejnou oblast pro API Management a Logic Apps, Service Bus. Obecně platí vyberte oblast co nejblíže vašim uživatelům.

Skupina prostředků má také oblast. Tuto oblast určuje, kam se ukládají metadata nasazení a kde provést nasazení šablony. Chcete-li zlepšit dostupnost během nasazení, umístěte skupinu prostředků a prostředky ve stejné oblasti.

## <a name="scalability"></a>Škálovatelnost

Pokud chcete zvýšit škálovatelnost při správě služby API Management, přidejte [zásady ukládání do mezipaměti](../api-management/api-management-howto-cache.md) spouštějte podle potřeby. Ukládání do mezipaměti také pomáhá snížit zatížení na back endové služby.

Nabízí větší kapacitu, můžete horizontální navýšení kapacity vrstvy Azure API Management na úrovni Basic, Standard a Premium v oblasti Azure. K analýze využití pro vaši službu na **metriky** nabídku, vyberte **metriku kapacity** možnost a potom vertikálně navýšit kapacitu nebo vertikálně snížit kapacitu podle potřeby.

Doporučení pro škálování služby API Management:

- Při horizontálním škálování vezměte v úvahu vzory provozu. Zákazníci s více těkavých vzory provozu potřebujete víc kapacity.

- Konzistentní kapacity, který je větší než 66 % může znamenat potřeba vertikálně navýšit kapacitu.

- Konzistentní kapacity, který je v části 20 % může znamenat příležitost můžete vertikálně snížit kapacitu.

- Před povolením zatížení v produkčním prostředí vždy zátěžového testu služby API Management se reprezentativní zatížení.

Úroveň služeb Premium můžete škálovat napříč několika oblastmi Azure. Pokud provádíte nasazení díky škálování služby ve víc oblastech Azure, můžete získat vyšší úroveň SLA (99,95 % oproti 99,9 %) a služby zřizování blízko uživatelům v několika oblastech.

Aplikace logiky bez serveru modelu znamená, že správci nemusíte plánovat škálovatelnost služby. Služby se automaticky škáluje podle potřeby.

## <a name="availability"></a>Dostupnost

* U úrovní Basic, Standard a Premium smlouvu o úrovni služeb (SLA) pro službu Azure API Management je aktuálně 99,9 %. Smlouva SLA pro konfigurace úrovně premium s nasazením, který má alespoň jednu jednotku ve dvou nebo více oblastech, dostupnost 99,95 %.

* Smlouva SLA pro Azure Logic Apps je aktuálně 99,9 %.

### <a name="backups"></a>Zálohování

Podle pravidelnost změny, [pravidelně zálohovat](../api-management/api-management-howto-disaster-recovery-backup-restore.md) konfiguraci Azure API Management. Umístění nebo oblasti Azure, která se liší od ve které se nachází vaši službu Store záložní soubory. Pak můžete vybrat jednu z možností jako strategii zotavení po havárii:

* V události zotavení po havárii zřizovat nové instance API managementu, obnovení zálohy do nové instance a směrování záznamů DNS.

* Zachovat pasivní kopii vaší služby v jiné oblasti Azure, který se účtuje poplatek. Pravidelně OBNOVUJTE zálohy do tuto kopii. Chcete-li obnovit službu během události zotavení po havárii, potřebujete pouze repointu záznamů DNS.

Vzhledem k tomu, že můžete rychle znovu vytvořit aplikace logiky, které jsou bez serveru, zálohování je uložením kopii přidružené šablony Azure Resource Manageru. Šablony lze uložit do správy zdrojového kódu a šablon můžete integrovat s vaším procesem průběžné integrace a nasazování (CI/CD).

Pokud jste publikovali aplikaci logiky prostřednictvím služby Azure API Management a aplikaci logiky přesune do jiného datového centra, aktualizujte umístění aplikace. Můžete aktualizovat vaše rozhraní API **back-endu** vlastnost s použitím základní skript prostředí PowerShell.

## <a name="manageability"></a>Možnosti správy

Vytvořit samostatné skupiny prostředků pro produkční prostředí, vývoje a testovacích prostředí. Samostatné skupiny prostředků usnadňují správu nasazení, odstranění nasazení v testovacím a přiřadit přístupová práva.

Při přiřazování prostředků skupinám prostředků zvažte tyto faktory:

* **Životní cyklus**: cyklem umístěte prostředky, které mají stejný životní cyklus ve stejné skupině prostředků.

* **Přístup**: Chcete-li použít zásady přístupu pro prostředky ve skupině, můžete použít [řízení přístupu na základě role (RBAC)](../role-based-access-control/overview.md).

* **Fakturace**: můžete zobrazit souhrnné náklady pro skupinu prostředků.

* **Cenová úroveň pro službu API Management**: použití vrstvy pro vývojáře pro vývojová a testovací prostředí. Minimalizovat náklady během předprodukční kolekci, nasadit repliku produkčního prostředí, spuštění testů a poté vypněte.

Další informace naleznete v tématu [Přehled Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Nasazení

* Chcete-li nasadit API Management a Logic Apps, použijte [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md). Šablony usnadňují automatizace nasazení pomocí Powershellu nebo rozhraní příkazového řádku Azure.

* API Management a všechny jednotlivé logic apps do své vlastní samostatné šablony Resource Manageru. Když použijete samostatné šablony, můžete ukládat prostředky v systémy správy zdrojového kódu. Tyto šablony můžete nasadit společně nebo samostatně jako součást procesu průběžné integrace a nasazování (CI/CD).

### <a name="versions"></a>Verze

Pokaždé, když změníte konfiguraci vaší aplikace logiky nebo nasazení aktualizaci pomocí šablony Resource Manageru, Azure uchovává kopii této verze pro vaše pohodlí a uchovává všechny verze, které mají historie spuštění. Tyto verze můžete použít pro sledování historických změn nebo zvýšení úrovně na verzi jako aktuální konfiguraci aplikace logiky. Například můžete efektivně se vrátit zpět aplikaci logiky.

Azure API Management má tyto odlišné ale doplňkové [koncepty správy verzí](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

* Verze, které vaše rozhraní API příjemců poskytují možnost zvolit, verze rozhraní API, například podle svých potřeb, v1, v2, beta nebo produkčního prostředí

* Revize, které umožňují správcům rozhraní API bez obav proveďte změny v rozhraní API a poté nasadit tyto změny uživatelům s volitelné komentáře

Pro nasazení vezměte v úvahu revize rozhraní API Management jako způsob, jak bezpečně provést změny, zachovat historii změn a sdělovat tyto změny vašeho rozhraní API příjemců. Můžete provést revizi v prostředí pro vývoj a nasazení pomocí šablon Resource Manageru tuto změnu v jiných prostředích.

I když revize můžete použít pro testování rozhraní API před provedením těchto změn "aktuální" a jejich zpřístupnění uživatelům, tato metoda se nedoporučuje pro zatížení nebo testování integrace. Místo toho použijte samostatný test nebo předprovozním prostředí.

### <a name="configuration-and-sensitive-information"></a>Konfigurace a citlivých informací

Nikdy nezapisujte hesla, přístupové klíče nebo připojovací řetězce do správy zdrojového kódu. Pokud tyto hodnoty jsou povinné, zabezpečit a nasadit tyto hodnoty pomocí vhodných metod. 

Ve službě Logic Apps Pokud aplikace logiky vyžaduje citlivých hodnoty, které nelze vytvořit v rámci určitého připojení, uložte tyto hodnoty ve službě Azure Key Vault a odkazovat z šablony Resource Manageru. Pomocí parametrů šablony nasazení a soubory parametrů pro každé prostředí. Další informace najdete v tématu [zabezpečení parametry a vstupy v pracovním postupu](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

API Management spravuje tajných kódů pomocí objektů nazývaných *pojmenované hodnoty* nebo *vlastnosti*. Tyto objekty bezpečně ukládat hodnoty, které můžete přistupovat prostřednictvím zásad služby API Management. Další informace najdete v tématu [spravovat tajné kódy ve službě API Management](../api-management/api-management-howto-properties.md).

## <a name="diagnostics-and-monitoring"></a>Diagnostika a monitorování

Můžete použít [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) pro provozní monitorování v obou [API Management](../api-management/api-management-howto-use-azure-monitor.md) a [Logic Apps](../logic-apps/logic-apps-monitor-your-logic-apps.md). Azure Monitor poskytuje informace na základě metrik pro každou službu nakonfigurovat a je ve výchozím nastavení povolené.

Každá služba má také tyto možnosti:

* Odeslat protokoly s Logic Apps pro hlubší analýzu a mnoha [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md).

* U monitorování na DevOps, můžete nakonfigurovat Azure Application Insights pro službu API Management.

* API Management podporuje [šablona řešení Power BI pro vlastní rozhraní API analýzy](http://aka.ms/apimpbi). Tato šablona řešení můžete použít k vytváření vlastní analytické řešení. Pro obchodní uživatele Power BI zpřístupňuje sestavy.

## <a name="security"></a>Zabezpečení

I když tento seznam nepopisuje úplně všechny osvědčené postupy zabezpečení, tady jsou některé důležité informace o zabezpečení, které platí konkrétně pro Azure services nasazené v architektuře, která je popsána v tomto článku:

* Chcete-li mít jistotu, že uživatelé mají odpovídající přístup úrovně, použijte řízení přístupu na základě role (RBAC).

* Zabezpečte veřejné koncové body rozhraní API ve službě API Management s použitím účtu OAuth nebo OpenID Connect. Pro zabezpečení veřejné koncové body rozhraní API, konfigurace zprostředkovatele identity a přidat zásadu ověřování JSON Web Token (JWT).

* Připojení k back endové služby ze služby API Management s použitím vzájemnými certifikáty.

* Zabezpečení aplikací logiky založené na aktivační událost HTTP tak, že vytvoříte seznam povolených IP adres adres, který odkazuje na rozhraní API správy IP adres. Přidat na seznam povolených IP adresu brání volání aplikace logiky z veřejného Internetu bez nutnosti první prostřednictvím služby API Management.

## <a name="next-steps"></a>Další postup

* Další informace o [podniková integrace pomocí front a události](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md)
