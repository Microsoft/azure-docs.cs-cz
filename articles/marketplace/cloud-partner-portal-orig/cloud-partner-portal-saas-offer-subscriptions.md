---
title: SaaS - prodávat prostřednictvím Azure | Dokumentace Microsoftu
description: Popisuje model pro aplikace SaaS a způsobu jeho implementace fakturace za předplatné.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809375"
---
<a name="saas---sell-through-azure"></a>SaaS - prodávat prostřednictvím Azure
=========================

Tento článek popisuje model fakturace předplatného pro SaaS aplikace a jak implementovat v portál partnerů cloudu.


<a name="overview"></a>Přehled
--------

Azure marketplace umožňuje publikovat a finančně zhodnotit vaše nabídky SaaS prostřednictvím Azure marketplace. Pro zákazníky a chcete, můžete nyní se naúčtuje za období přímo na fakturaci Azure a stačí dostanou jeden účet z Azure pro všechny svoje prostředky a služby si aktivovali. Pro publikování, předplatná SaaS nabízí tyto výhody:

-   **Konzistentní prostředí pro publikování** – Pokud už publikování nabídky SaaS nebo žádnou jinou nabídkou v Azure Marketplace, možnosti publikování je prostřednictvím portálu pro publikování stejné.
-   **Nový prodejní místo pro zjišťování** – všechny nabídky publikované se nebude zobrazovat v externích prodejních míst Azure Marketplace, stejně jako rozšíření webu Azure Marketplace v rámci webu Azure portal.
-   **Integrované fakturaci** – teď můžete prodávat ve všech oblastech, kde Azure prodává a nechejte systém Azure se postará o fakturaci za vás.
-   **Integrované generování potenciálních zákazníků** – můžete nyní automaticky získávat potenciálních zákazníků z Azure do CRM podle vašeho výběru, pokaždé, když Azure uživatel přihlásí do vaší služby SaaS pomocí webu Azure marketplace.
-   **Společný prodej s prodejci Microsoftu** – můžete kvalifikovat pro obousměrné sdílení potenciálních zákazníků, prioritní zařazení do katalogů a příležitost zapojit se a uzavírat společné obchodů – souběžně s prodejci Microsoftu.

<a name="billing-models"></a>Modely fakturace
--------------

Jediný momentálně podporovaný model fakturace je fixní měsíční poplatek za předplatné vaší služby SaaS.

**Poznámka:** další modely fakturace může být k dispozici na budoucí dobu.

Každá nabídka SaaS může obsahovat jeden nebo více plánů (například Basic nebo Premium). Každý plán obsahuje některé základní metadata spojená s ním, spolu s cenou přidružené k plánu.

Máte plnou kontrolu nad definici plánu. Například co základní plán tvoří? Tento plán je definované uživatelem a poskytnete nezbytné text pro popis součástí publikování vašeho plánu.

Cena přidružené k plánu se účtuje paušální měsíční poplatek, který uživatelům systému Azure budou platit k používání služby.

### <a name="what-is-not-supported-today"></a>Co není podporováno dnes?

-   Fakturace na základě vlastní jednotky – například stanovenou cenu na základě počtu požadavků.
-   Fakturace podle licence přidělení – například uživatelům Azure koupit licence na základě počtu uživatelů.

<a name="end-to-end-flow"></a>Kompletního toku
---------------

Předplatné SaaS nabízejí tok z hlediska koncových uživatelů první

**Poznámka:** tento popis toku se předpokládá, že publikování vaší nabídky SaaS v Azure marketplace, které se nazývá "Contoso ukázkové SaaS".

![Tok uživatele předplatné SaaS](media/saas-offer-subscription/saas-subscription-user-flow.png)

Uživatel s Azure může mít následující interakce s vaší služby SaaS:

-   Zjistit vaší služby SaaS v Azure Marketplace
-   K odběru vaší služby SaaS v Azure
-   Přejděte do služby SaaS z portálu Azure portal
-   Vytvoření účtu ve službě SaaS a spravovat účet saas (plán měnit/odstraňovat)
-   Odhlásit odběr služby SaaS z webu Azure portal

<a name="discover-your-saas-service-in-azure-marketplace"></a>Zjistit vaší služby SaaS v Azure Marketplace
-----------------------------------------------

Když uživatelé spouštět Azure Marketplace na webu Azure Portal, zobrazí se kategorie s názvem "Software jako služba (SaaS)".

![Zjišťování pomocí kategorie nabídky SaaS](media/saas-offer-subscription/saas-category-menu.png)

Uživatele můžete také vyhledat ve službě SaaS.

![Zjistit SaaS pomocí služby search](media/saas-offer-subscription/saas-cpp-search.png)

Uživatelé mohou následně zobrazit podrobnosti o službě a potom klikněte na tlačítko **vytvořit**.

![Vytváří se předplatné SaaS](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>K odběru služby SaaS v Azure

Uživatel může potom přihlásit k odběru služby SaaS z Azure.

![K odběru služby SaaS](media/saas-offer-subscription/saas-subscribe-signup.png)

Pokud se uživatel přihlásí do vaší služby SaaS, uživatel zadá následující informace

-   Název – Název, aby uživatelé mohou zjistit nebo spravovat tuto instanci předplatné SaaS v Azure.
-   Předplatné – Kontext předplatného, které chtějí spojit fakturace služby SaaS.
-   Plán – Plán služby SaaS, který se chcete přihlásit k odběru.

Dokument podmínek použití poskytuje jako součást publikování nabídky se také zobrazí uživateli předtím, než uživatel přihlásí ke službě SaaS.

Uživatel můžete nyní přihlásit k odběru služby kliknutím **přihlásit k odběru**.
Po dokončení předplatné Azure odesílá oznámení na portálu.

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>Přejděte do služby SaaS z webu Azure Portal

Pak klikněte na uživatelé **přejít k prostředku** zobrazovat a spravovat jejich předplatné instance SaaS.

![Zobrazovat a spravovat vaše instance SaaS](media/saas-offer-subscription/saas-go-to-resource.png)

Uživatel je upozorněn, že se musí nakonfigurovat účet ve službě SaaS nejprve. Jejich fakturace začne běžet po upozorní Azure spustit fakturace, což je, pokud uživatel vytvoří účet na webu služby SaaS ve službě SaaS.

![Umožňuje nakonfigurovat instanci aplikace SaaS](media/saas-offer-subscription/saas-configure-account.png)

Když uživatel klikne na **nakonfigurovat účet**, bude přesměrován na váš koncový bod služby SaaS. Během této přesměrování token předají jako parametr dotazu. Příklad:

![SaaS účtu tokenu](media/saas-offer-subscription/saas-account-token.png)

Poznámka: Tato hodnota tokenu. Tento token je krátkodobé a jednorázové a musí být přeloženy na získat identifikátor předplatného v Azure.

<a name="creating-a-saas-offer-subscription"></a>Vytváří se předplatné nabídky SaaS
----------------------------------

Pokud chcete vytvořit toto prostředí, se dvěma pracovními, které jsou požadovány:

-   Připojení webu služby SaaS s Microsoftem\'s rozhraním API SaaS. Tento dokument [zákazník SaaS pomocí Azure – rozhraní API](./cloud-partner-portal-saas-subscription-apis.md) vysvětluje, jak vytvořit toto připojení.  
-   Povolit **prodávat prostřednictvím Azure** na portál Cloud Partner v **technické informace o** části a poskytují veškeré podrobnosti vaší konfigurace.

![Technické informace nové nabídky SaaS](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

Následuje vysvětlení všech požadovaných polí pro **technické informace o** části:

|  **Nabídka pole**                 |  **Popis**                                   |
|  ----------------                 |  -------------------------------------             |
| ID předplatných ve verzi Preview          | Všechny předplatné Azure identifikátory použitými k otestování vaší nabídky ve verzi preview, předtím, než je veřejně dostupná. |
| Začínáme se službou pokyny      | Pokyny ke sdílení se zákazníky k usnadnění připojení k aplikaci SaaS. Základní informace o využití ve formátu HTML je povolený, tyto značky jako `<p>`, `<h1>`, `<li>`atd.  |
| Cílová adresa URL stránky                  | Adresu URL vašeho webu, který bude směrovat vaši zákazníci objevil na po získání z webu Azure portal. Tato adresa URL bude také koncového bodu, který bude přijímat připojení rozhraní API pro usnadnění obchodování s Microsoftem.  |
| Připojení Webhooku                | Pro všechny asynchronní události, které je potřeba poslat jménem zákazníka Microsoftu (Příklad: předplatné Azure náramků RFID neplatný), potřebujeme, abyste nám webhooku připojení. Pokud ještě nemáte systému webhooku v místě, je nejjednodušší konfiguraci, aby aplikace logiky koncový bod HTTP, který bude naslouchat žádné události odeslání do něj a potom je odpovídajícím způsobem zpracovat.  Další informace najdete v tématu [volání triggeru, nebo vnořené pracovní postupy s koncovými body HTTP ve službě logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint). |
| Azure AD Tenanta, ID a ID aplikace     | Uvnitř webu Azure portal, vyžadujeme, vytvoření aplikace Active Directory tak, že abychom mohli ověřit připojení mezi dvěma naše služby je za ověřené komunikaci. U těchto polí, vytvořit aplikaci AD a vložte odpovídající Id Tenanta a Id aplikace. |
|  |  |


Nakonec Pokud vyberete **prodávat prostřednictvím Azure**, je přidání část s názvem **plány**. Plány jsou potřeba pouze v případě, že je vybrán zákazník prostřednictvím Azure. Tato část obsahuje konkrétní plány a jejich odpovídající cenu, které vaše aplikace SaaS podporuje. Od dnešního dne umožňujeme měsíční ceny, možnost povolit pro 1 - nebo 3měsíční bezplatný přístup. Tyto plány a ceny by měl odpovídat přesně plány a ceny, které mají na svůj vlastní web aplikace SaaS.
