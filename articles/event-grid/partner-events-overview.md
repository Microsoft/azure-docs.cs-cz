---
title: Azure Event Grid – partnerské události
description: Posílání událostí od partnerů Event Grid SaaS a PaaS od jiných výrobců přímo ke službám Azure pomocí Azure Event Grid.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 87d1d40b3696229344b0b5c20d06d9d993a514a4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93102882"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Partnerské události v Azure Event Grid (Preview)
Funkce **partnerských událostí** umožňuje poskytovateli SaaS třetí strany publikovat události ze svých služeb, aby byly dostupné pro uživatele, kteří se můžou přihlásit k odběru těchto událostí. Nabízí prostředí jako první strana pro zdroje událostí třetích stran tím, že zveřejňuje typ [tématu](concepts.md#topics) , **Partnerská část** , kterou předplatitelé používají ke využívání událostí. Nabízí také čistý model Pub-sub, který odděluje obavy a vlastnictví prostředků, které používají vydavatelé a předplatitelé událostí.

> [!NOTE]
> Pokud s použitím Event Grid začínáte, přečtěte si téma [Přehled](overview.md), [Koncepty](concepts.md)a [obslužné rutiny událostí](event-handlers.md).

## <a name="what-is-partner-events-to-a-publisher"></a>Co jsou partnerské události pro vydavatele?
Pro vydavatele událostí umožňuje vydavatelům provádět následující úlohy:

- Zaregistrujte své zdroje událostí do Event Grid
- Vytvoření oboru názvů (koncového bodu), do kterého mohou publikovat události
- Vytváření partnerských témat v Azure, které můžou předplatitelé využít ke zpracování událostí

## <a name="what-is-partner-events-to-a-subscriber"></a>Co jsou partnerské události pro předplatitele?
Pro předplatitele funkce partnerských událostí umožňuje vytvořit Partnerská témata v Azure a využívat tak události ze zdrojů událostí třetích stran. Spotřeba události je realizována vytvořením odběrů událostí, které odesílají události (push) do obslužné rutiny události odběratele.

## <a name="why-should-i-use-partner-events"></a>Proč mám používat partnerské události?
Pokud máte jeden nebo více z následujících požadavků, je vhodné použít partnerské události.

### <a name="for-publishers"></a>Vydavatelům

- Chcete, aby byly vaše události v Azure k dispozici. Uživatelé můžou tyto události filtrovat a směrovat pomocí partnerských témat a odběrů událostí, které vlastní a spravují. Můžete použít další integrační přístupy, jako jsou [témata](custom-topics.md) a [domény](event-domains.md). Ale neumožňují vyčistit oddělení prostředků (témat partnerů), správu a fakturace mezi vydavateli a předplatiteli. Tento přístup taky poskytuje intuitivnější uživatelské prostředí, které usnadňuje vyhledávání a používání témat partnerů.
- Chcete publikovat události do jednoho koncového bodu, koncového bodu oboru názvů. A chcete mít možnost tyto události filtrovat, aby byly k dispozici pouze podmnožina. 
- Chcete mít přehled o metrikách souvisejících s publikovanými událostmi.
- Pro vaše události chcete použít schéma [Cloud Events 1,0](https://cloudevents.io/) .

### <a name="for-subscribers"></a>Pro předplatitele

- Chcete se přihlásit k odběru událostí od [vydavatelů třetích stran](#available-third-party-event-publishers) a zpracovávat události pomocí obslužných rutin událostí, které jsou v Azure nebo jinde.
- Chcete využívat bohatou sadu funkcí Směrování a jejich [cílů/obslužných rutin událostí](overview.md#event-handlers) pro zpracování událostí ze zdrojů třetích stran. 
- Chcete implementovat volně spojené architektury, kde vaše předplatitel nebo obslužná rutina události nevědí o existenci používaného zprostředkovatele zpráv. 
- Budete potřebovat odolný mechanismus pro doručování nabízených oznámení s podporou Send-Retry a alespoň jednou sémantikou.
- Pro vaše události chcete použít schéma [Cloud Events 1,0](https://cloudevents.io/) . 


## <a name="available-third-party-event-publishers"></a>K dispozici vydavatelé událostí třetích stran
Vydavatel události třetí strany musí projít [procesem](partner-onboarding-overview.md) registrace dřív, než odběratel může začít spotřebovávat své události. 

Pokud jste předplatitelem a chcete, aby služba třetí strany zveřejnila své události prostřednictvím Event Grid, 

### <a name="auth0"></a>Auth0
**Auth0** je první dostupný Vydavatel partnera. Můžete vytvořit [téma Auth0 partner](auth0-overview.md) pro připojení účtů Auth0 a Azure. Tato integrace umožňuje reagovat na události Auth0 v reálném čase, zaznamenávat je a monitorovat. Pokud ho chcete vyzkoušet, přečtěte si téma [integrace Azure Event Grid s Auto0](auth0-how-to.md)

Chcete-li, aby služba třetí strany zveřejnila své události prostřednictvím Event Grid, pošlete nápad na [portál User Voice](https://feedback.azure.com/forums/909934-azure-event-grid).
 
## <a name="resources-managed-by-event-publishers"></a>Prostředky spravované vydavateli událostí
Vydavatelé událostí vytvářejí a spravují následující zdroje:

### <a name="partner-registration"></a>Registrace partnera
Registrace obsahuje obecné informace související s vydavatelem. Definuje typ partnerského tématu, které se zobrazí v Azure Portal jako možnost, když se uživatelé pokusí vytvořit téma. Vydavatel může vystavit více než jeden nebo více typů partnerských témat, aby vyhovoval potřebám svých předplatitelů. To znamená, že Vydavatel může vytvořit samostatné registrace (typy partnerských témat) pro události z různých služeb. Například u služby lidských zdrojů (HR) může vydavatel definovat téma partnera pro události, jako jsou například připojené zaměstnance, zaměstnanec zvýšil (a zaměstnanec) opustil společnost. 

Mějte na paměti následující skutečnosti:

- Jsou viditelné jenom registrace partnerů schválené pro Azure. 
- Registrace jsou globální. To znamená, že nejsou přidruženy ke konkrétní oblasti Azure.
- Registrace je volitelný prostředek. Doporučujeme, abyste vy (jako vydavatel) vytvořili registraci. Umožňuje uživatelům zjišťovat témata na stránce **vytvořit téma partnera** v [Azure Portal](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic). Uživatel pak může při vytváření odběrů událostí vybrat typy událostí (například připojeného zaměstnance, zbývající zaměstnanec atd.).

### <a name="namespace"></a>Obor názvů
Podobně jako u [vlastních témat](custom-topics.md) a [domén](event-domains.md)je partnerský obor názvů místní koncový bod pro publikování událostí. Je to přes obory názvů, které vydavatelé vytvářejí a spravují kanály událostí. Obor názvů také funguje jako prostředek kontejneru pro kanály událostí.

### <a name="event-channels"></a>Kanály událostí
Kanál událostí je zrcadlený prostředek k tématu partnera. Když Vydavatel vytvoří kanál událostí v předplatném Azure vydavatele, vytvoří také téma partner v rámci předplatného Azure předplatitele. Operace prováděné s kanálem událostí (s výjimkou GET) se použijí na příslušné téma partnerského partnera, dokonce i odstranění. Jenom témata o partnerech jsou ale druhy prostředků, na kterých se dá nakonfigurovat odběry a doručování událostí.

## <a name="resources-managed-by-subscribers"></a>Prostředky spravované předplatiteli 
Předplatitelé můžou používat témata partnerů definovaná vydavatelem a jedná se o jediný typ prostředku, který vidí a spravuje. Po vytvoření partnerského tématu může uživatel předplatitele vytvořit odběry událostí definující pravidla filtru na [cíle nebo obslužné rutiny událostí](overview.md#event-handlers). Pro předplatitele téma Partnerská a přidružená předplatná událostí poskytují stejné možnosti jako [vlastní témata](custom-topics.md) a jejich související odběry s jedním významným rozdílem: Partnerská témata podporují jenom [schéma Cloud Events 1,0](cloudevents-schema.md), které poskytuje bohatší sadu funkcí než jiná podporovaná schémata.

## <a name="pricing"></a>Ceny
Témata týkající se partnerů se účtují podle počtu operací, které se provádí při použití Event Grid. Další informace o všech typech operací, které se používají jako základ pro fakturaci a podrobné informace o cenách, najdete v článku [Event Grid ceny](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="limits"></a>Omezení
Podrobné informace o omezeních, která jsou na místě pro partnerských témat, najdete v tématu [Event Grid služby omezení](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) .


## <a name="next-steps"></a>Další kroky

- [Téma Auth0 partner](auth0-overview.md)
- [Jak používat téma Auth0 partner](auth0-how-to.md)
- [Staňte se Event Grid partnerem](partner-onboarding-overview.md)