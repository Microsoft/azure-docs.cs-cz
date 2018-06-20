---
title: Integrace se službou Azure Services – integrace jednoduché Enterprise
description: Referenční architektura znázorňující způsob implementace vzoru jednoduché enterprise integrace s Azure Logic Apps a Azure API Management
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
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232289"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Jednoduchá integrace Enterprise - referenční architektura

## <a name="overview"></a>Přehled

Tuto referenční architekturu zobrazuje sadu osvědčené postupy pro integraci aplikace, která používá Azure integrační služby. Tato architektura může sloužit jako tento základ mnoha různých aplikací schémat vyžadující rozhraní API HTTP, pracovní postup a orchestration.

*Existuje mnoho možné aplikací integrace technologie z aplikace bodu jednoduché point-to k service bus úplné enterprise. Tato architektura řada nastaví limit opakovaně použitelné součásti pro vytváření všech integrace aplikací – architekty zvažte součásti, které budou potřebovat pro jejich aplikace a infrastrukturu.*

   ![Diagram architektury - jednoduché enterprise integrace](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Architektura

Tato architektura se skládá z následujících komponent:

- Skupina prostředků. Skupina prostředků je logický kontejner pro prostředky Azure.
- Azure API Management. Azure API Management je plně spravovaná platforma pro publikování, zabezpečení a transformace rozhraní API HTTP.
- Portál pro vývojáře správy rozhraní API Azure. Každá instance služby Azure API Management se dodává s portál pro vývojáře, udělíte přístup k dokumentaci, ukázky kódu a možnost otestovat rozhraní API.
- Azure Logic Apps. Služba Logic Apps je bez serveru platformu pro sestavování enterprise pracovního postupu a integrace.
- Konektory. Konektory jsou používány Logic Apps pro připojení k běžně používaných služeb. Služba Logic Apps již stovky různých konektory, ale mohou být také vytvořeny pomocí vlastní konektor.
- IP adresa. Služba Azure API Management má pevnou veřejnou IP adresu a název domény. Název domény je subdoménou api.net azure, jako je například contoso. Azure-api.net. Služba Logic Apps má také veřejnou IP adresu; ale v této architektuře jsme omezit přístup k volání koncové body aplikace logiky k jenom správy IP adres z rozhraní API (pro zabezpečení).
- Azure DNS. Azure DNS je hostitelská služba domén DNS poskytnutí překladu názvů pomocí infrastruktury Microsoft Azure. Pokud svoje domény hostujete v Azure, můžete spravovat svoje DNS záznamy pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů a za stejných fakturačních podmínek jako u ostatních služeb Azure. Chcete-li použít vlastní název domény (například contoso.com) vytvořte záznamy DNS, které mapují vlastní název domény na IP adresu. Další informace najdete v části Konfigurace vlastního názvu domény v Azure API Management.
- Azure Active Directory (Azure AD). Použijte službu Azure AD nebo jiného zprostředkovatele identit pro ověřování. Azure AD poskytuje ověřování pro přístup k rozhraní API koncových bodů (předáním webového tokenu JSON pro správu rozhraní API pro ověření) a můžete zabezpečit přístup k portálu pro vývojáře rozhraní API správy (pouze úrovně Standard a Premium).

Tato architektura má některé základní vzorů pro své činnosti:

1. Existující back-end rozhraní API HTTP jsou publikovány prostřednictvím rozhraní API správy portál pro vývojáře, což umožňuje vývojářům (buď interní vaší organizace, externí nebo obojí) k integraci volání tato rozhraní API do aplikací.
2. Složené rozhraní API jsou vytvořeny pomocí Logic Apps; Orchestrace volání do systémů SAAS, služby Azure a všechny rozhraní API publikovat na rozhraní API Management. Aplikace logiky jsou také publikovat prostřednictvím portálu pro vývojáře správy rozhraní API.
3. Aplikace získat token OAuth 2.0 zabezpečení potřebné pro získání přístupu k rozhraní API pomocí Azure Active Directory.
4. Azure API Management ověří token zabezpečení a předá požadavek back-end aplikace API nebo logiky.

## <a name="recommendations"></a>Doporučení

Vaše požadavky se mohou od popsané architektury lišit. Jako výchozí bod použijte doporučení uvedená v této části.

### <a name="azure-api-management-tier"></a>Azure API Management vrstvy

Použít Basic, Standard nebo Premium úrovně, protože nabízejí provozní SLA a podporovat škálování oblast Azure (počet jednotek se liší podle úrovně). Úroveň Premium podporuje také škálování nad několika oblastmi Azure. Základní vrstvy, které jste zvolili na odpovídající úrovni vašeho propustnosti vyžaduje a funkce. Další informace najdete v tématu [ceny služby API Management](https://azure.microsoft.com/pricing/details/api-management/).

Budou se účtovat pro všechny instance API Management při spuštění. Pokud máte škálovat a nepotřebujete tuto úroveň výkonu vždy, zvažte využívat výhod API Management hodinové fakturace a škálování směrem dolů.

### <a name="logic-apps-pricing"></a>Aplikace logiky ceny

Služba Logic Apps funguje jako [bez serveru](logic-apps-serverless-overview.md) modelu – fakturace se počítá na základě na provedení akce a konektor. V tématu [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/) Další informace. Aktuálně neexistují žádné vrstvy požadavky pro Logic Apps.

### <a name="logic-apps-for-asynchronous-api-calls"></a>Služba Logic Apps pro asynchronní volání rozhraní API

Služba Logic Apps funguje nejlépe scénáře, které nevyžadují nízkou latencí – například asynchronní nebo volání rozhraní API návěs dlouhodobý spuštěná. Pokud je potřeba s nízkou latencí (například volání blokující uživatelské rozhraní) doporučujeme implementace tohoto rozhraní API nebo operace pomocí různých technologií, např. Azure Functions nebo webového rozhraní API nasadit pomocí služby App Service. Přesto doporučujeme, aby se toto rozhraní API přední stěnou pomocí rozhraní API správy k příjemce rozhraní API.

### <a name="region"></a>Oblast

Zřídit správy rozhraní API a Logic Apps ve stejné oblasti k minimalizaci latence sítě. Vyberte oblast, která je vašim uživatelům nejbližší.

Skupina prostředků má také oblast, která určuje, kde se ukládají metadata nasazení, a kde se šablona nasazení se spustí z. Skupinu prostředků a její prostředky umístěte do stejné oblasti. Zlepšíte tím dostupnost během nasazení.

## <a name="scalability-considerations"></a>Aspekty zabezpečení

API Management správci měli přidat [zásady ukládání do mezipaměti](../api-management/api-management-howto-cache.md) podle potřeby zvýšit škálovatelnost služby a snížit zatížení služeb back-end.

Azure úrovně rozhraní API správy Basic, Standard a Premium můžete škálovat na více systémů s v oblasti Azure, které nabízejí větší kapacitu. Správci můžou pomocí metriky kapacity v rámci nabídky metriky analýza využití své služby a škálovat nahoru i podle potřeby.

Doporučení pro škálování služby API Management:

- Škálování musí vzít v úvahu vzory přenosů dat – zákazníky s více volatile vzory přenosů dat, bude mít větší potřebu zvýšené kapacity.
- Konzistentní kapacity výše 66 % může znamenat potřeba vertikální navýšení kapacity.
- Konzistentní kapacitou nižší než 20 % může znamenat příležitost škálování směrem dolů.
- Vždy doporučujeme načíst testování služby API Management s reprezentativní zatížení před povolením v produkčním prostředí.

Úroveň služeb Premium můžete škálovat nad několika oblastmi Azure. Zákazníky, kteří instalují tímto způsobem bude mít vyšší SLA (99,95 % oproti 99,9 %) a můžete zřizovat služby blízko uživatelé v několika oblastech.

Aplikace logiky bez serveru modelu znamená, že správci není nutné provést další aspekt škálovatelnosti služby; Služba škáluje automaticky potřeby.

## <a name="availability-considerations"></a>Aspekty dostupnosti

V době psaní smlouvu o úrovni služeb (SLA) pro Azure API Management je 99,9 % pro úrovně Basic, Standard a Premium. Konfigurace úrovně Premium s nasazením alespoň jednu jednotku ve dvou nebo více oblastech mít SLA 99,95 %.

V době psaní smlouvu o úrovni služeb (SLA) pro Azure Logic Apps je 99,9 %.

### <a name="backups"></a>Zálohování

Konfigurace služby Azure API Management by měl být [pravidelně zálohovány](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (odpovídajícím způsobem na základě správnost změn) a zálohování souborů uložených v místě nebo jiné kde se služba nachází oblast Azure. Zákazníci můžete pak vyberte jednu ze dvou možností pro své strategie zotavení po Havárii:

1. V případě zotavení po Havárii nové instance API Management je zřízený, obnovení zálohy do něj a záznamy DNS jsou repointed.
2. Udržování zákazníci jsou pravidelně obnoveny pasivní kopii své služby v jiné oblasti Azure (by docházelo k další náklady) zálohování. V případě zotavení po Havárii musí být pouze záznamy DNS repointed obnovit službu.

Aplikace logiky můžete znovu vytvořit velmi rychle a bez serveru jsou, zálohování uložením kopie přidružené šablony Azure Resource Manager. To se dá uložit zdroj ovládacího prvku nebo integrovat do nepřetržité integrace/nepřetržitý proces nasazení (CI/CD) zákazníků.

Služba Logic Apps, které byly publikovány přes správu rozhraní API bude nutné jejich umístění aktualizovat by se přesouvají do různých datových center. Můžete to provést prostřednictvím jednoduchého skript PowerShell pro aktualizaci vlastnost back-end rozhraní API.

## <a name="manageability-considerations"></a>Aspekty správy

Vytvořit samostatné skupiny prostředků pro produkční prostředí, vývoj a testování prostředí. Usnadníte tak správu nasazení, odstranění nasazení v testovacím prostředí a přiřazení přístupových práv.
Při přiřazování prostředků skupinám prostředků zvažte následující:

- Životní cyklus. Prostředky se stejným životním cyklem umístěte do stejné skupiny prostředků.
- Přístup. Můžete použít [řízení přístupu na základě Role](../role-based-access-control/overview.md) (RBAC) Chcete-li použít zásady přístupu k prostředkům ve skupině.
- Fakturace. Můžete zobrazit souhrnné náklady na skupinu prostředků.
- Cenovou úroveň pro API Management – doporučujeme použít úroveň Developer pro vývojové a testovací prostředí. Pro předprodukční doporučujeme nasazení repliku produkčního prostředí, spouštění testů a pak vypínání minimalizovat náklady.

Další informace najdete v tématu [skupiny prostředků](../azure-resource-manager/resource-group-overview.md) Přehled.

### <a name="deployment"></a>Nasazení

Doporučujeme vám, že používáte [šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) nasazení Azure API Management a Azure Logic Apps. Šablony usnadňují automatizaci nasazení přes PowerShell nebo rozhraní příkazového řádku Azure (CLI).

Doporučujeme, abyste uvedení v vlastní samostatné šablony správce prostředků Azure API Management a všechny jednotlivé aplikace logiky. To vám umožní jejich ukládání do systémů řízení zdrojů. Tyto šablony se dá nasadit pak společně nebo samostatně jako součást nepřetržité integrace/průběžné (CI/CD) procesu nasazení.

### <a name="versions"></a>Verze

Pokaždé, když provedete konfiguraci změnit na aplikace logiky (nebo nasazení aktualizace pomocí šablony Resource Manager), kopii této verze je udržováno pro usnadnění práce (všechny verze, které mají historie spouštění se zachová). Tyto verze můžete sledovat historická změny a také podporovat verze na aktuální konfiguraci aplikace logiky; Tato možnost znamená můžete můžete efektivně vrácení aplikace logiky, třeba.

API Management má dva odlišné (ale bezplatnou) [koncepty správy verzí](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/):

- Verze použitý k poskytnutí uživatele rozhraní API s volbou rozhraní API se může používat podle svých potřeb (např. v1, v2 nebo beta, produkční).
- Revize umožňuje správcům API bezpečně provést změny do rozhraní API a nasadit je uživatelům s volitelné komentáře.

V rámci nasazení – rozhraní API správy revize by měly být považovány způsob, jak bezpečně, provést změny uchovat historii změn a informovat příjemci rozhraní API z těchto změn. Revize se dají vytvořit ve vývojovém prostředí a nasadit mezi dalších prostředích s pomocí šablony Resource Manageru.

Zatímco revize můžete použít k testování rozhraní API, než se provádí, aktuální a přístupné pro uživatele, nedoporučujeme použití tento mechanismus pro načtení nebo testování integrace – samostatný testovacím nebo předprodukčním prostředí by měl být místo toho použít.

### <a name="configuration"></a>Konfigurace

Zkontrolujte nikdy hesla, přístupové klíče nebo připojovací řetězce v do správy zdrojového kódu. Pokud jsou potřeba, použijte odpovídající postup k nasazení a zabezpečit tyto hodnoty. 

V Logic Apps by měl být žádné citlivé hodnoty, které jsou potřeba v rámci aplikace logiky, (který nelze vytvořit ve formě připojení) uložené v Azure Key Vault a uvedené z šablony Resource Manageru. Doporučujeme také pomocí parametry šablony nasazení společně se soubory parametrů pro každé prostředí. Další pokyny k [zabezpečení parametry a vstupy v pracovním postupu](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

Ve službě API Management se spravují tajné klíče pomocí objekty volána s názvem hodnoty nebo vlastnosti. Tyto zásady služby API Management bezpečně uložit hodnoty, které jsou přístupné. V tématu Jak [spravovat tajné klíče ve službě API Management](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostika a monitorování

Obě [API Management](../api-management/api-management-howto-use-azure-monitor.md) a [Logic Apps](logic-apps-monitor-your-logic-apps.md) podporují provozní monitorování prostřednictvím [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Azure monitorování je ve výchozím nastavení povolené a poskytne informace, na základě různých metrik nakonfigurována pro každou službu.

Kromě toho existují další možnosti pro každou službu:

- Protokoly aplikací logiky lze odeslat buď do [analýzy protokolů](logic-apps-monitor-your-logic-apps-oms.md) pro hlubší analýzu a dashboarding.
- API Management podporuje konfiguraci Application Insights pro vývojáře Ops monitorování.
- Podporuje API Management [šablona řešení Power BI pro vlastní rozhraní API analytics](http://aka.ms/apimpbi). Tato šablona řešení umožňuje zákazníkům vytvářet své vlastní řešení vlastní analýzy se sestavami, které jsou k dispozici v Power BI podnikovým uživatelům.

## <a name="security-considerations"></a>Aspekty zabezpečení

Tato část obsahuje důležité informace o zabezpečení, které jsou specifické pro Azure služeb popsaných v tomto článku, nasazené v architektuře, jak je popsáno. Nejedná se o úplný seznam osvědčených postupů zabezpečení.

- Pomocí řízení přístupu na základě role (RBAC) zajistit odpovídající úrovně přístupu pro uživatele.
- Zabezpečte veřejné koncové body rozhraní API ve službě API Management pomocí OAuth nebo Open IDConnect. To lze proveďte tak, že konfigurace zprostředkovatele identity a přidáním zásady ověřování tokenů JWT.
- Připojení k back-end služby ze správy rozhraní API pomocí vzájemnými certifikáty
- Zabezpečení protokolu HTTP na základě aktivační událost Logic Apps vytvořit listinu (whitelist) IP adresy na IP adresu API Management. Tím se zabrání volání aplikace logiky z veřejného Internetu bez průchodu první přes API Management.

Tuto referenční architekturu vám ukázal, jak sestavit platforma pro integraci jednoduché enterprise prostřednictvím integračních služeb Azure.

## <a name="next-steps"></a>Další postup

* [Enterprise integrace s front a události](logic-apps-architectures-enterprise-integration-with-queues-events.md)
