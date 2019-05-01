---
title: SaaS aplikace nabídka technické konfigurace Azure | Azure Marketplace
description: Technické informace o konfiguraci pro SaaS aplikace nabídky na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 46dcf4aeb7ddb67028eb23dde9236f2b7709f86d
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64941724"
---
# <a name="saas-application-technical-info-tab"></a>Karta technické informace o SaaS aplikace

Technické informace o kartě poskytuje technické konfiguračním formuláři. Tento formulář slouží k výběru typu SaaS aplikace (aplikace), kterou vytváříte a nakonfigurujte, jak vaše aplikace je k dispozici zákazníkům.

![Technické konfigurace formuláře](./media/saas-techinfo-techconfig.png)


## <a name="technical-configuration-form"></a>Technické konfigurace formuláře

Tento formulář má pole 2: Produkt a výzvu k akci.


### <a name="product-field"></a>Pole produktu

Můžete zadat aplikace SaaS pro obě následující prodejní místa:
- Podnikový uživatel tak, že vyberete **výpis** možnost.
- Pro IT správce, tak, že vyberete **prodávat prostřednictvím Microsoft**.
Při rozhodování, jaký typ aplikace SaaS, kterou vytváříte, přečtěte si téma [pochopit prezentace výběr](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection).


#### <a name="sell-through-microsoft"></a>Jejím prostřednictvím – Microsoft
K vytvoření tohoto prostředí budete muset nakonfigurovat následující:

- Připojte svůj web služby SaaS s rozhraními API SaaS společnosti Microsoft. [Zákazník SaaS pomocí Azure – rozhraní API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) článek vysvětluje, jak vytvořit toto připojení.
- Povolit dá prodávat prostřednictvím Azure na portál partnerů cloudu v podobě technických konfigurace a zadejte požadované informace. Další informace o tomto modelu fakturace a jak je implementován najdete v tématu [SaaS – prodávat prostřednictvím Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions).

  ![Prodej přes Microsoft formuláře](./media/saas-techinfo-sellthrough-ms.png)

Následující tabulka popisuje pole povinná pro všechny **prodávat prostřednictvím Microsoft**.  Povinná pole jsou uvést hvězdičkou (*).

|  **Název pole**   |  **Popis**  |
|  ---------------  |  ---------------  |
|  **ID předplatných ve verzi Preview\***   |  Všechny předplatné Azure identifikátory použitými k otestování vaší nabídky ve verzi preview, předtím, než je veřejně dostupná.  |
|  **Ve verzi Preview účty AAD/MSA\***   |  Účty Azure AD/MSA, oddělených čárkami, které je udělen přístup k verzi preview. |
|  **Začínáme se službou pokyny** |  Pokyny ke sdílení se zákazníky k usnadnění připojení k aplikaci SaaS. Jsou povolené základní značky HTML, například: &lt;p&gt;, &lt;h1&gt;, &lt;li&gt;atd.    |
|  **Cílová adresa URL stránky\***           |  Adresu URL vašeho webu, který bude směrovat vaši zákazníci objevil na po získání z webu Azure portal. Tato adresa URL bude také koncového bodu, který bude přijímat připojení rozhraní API pro usnadnění obchodování s Microsoftem.   |
| **Připojení Webhooku\***            |  Pro všechny asynchronní události, které je potřeba poslat jménem zákazníka Microsoftu (Příklad: Předplatné Azure náramků RFID neplatný), potřebujeme, abyste nám webhooku připojení. Pokud ještě nemáte systému webhooku v místě, je nejjednodušší konfiguraci, aby aplikace logiky koncový bod HTTP, který bude naslouchat žádné události odeslání do něj a potom je odpovídajícím způsobem zpracovat. Další informace najdete v tématu <a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">volání triggeru, nebo vnořené pracovní postupy s koncovými body HTTP ve službě logic apps</a>    |
|  **ID klienta Azure AD\***  a **ID aplikace\***      |   Uvnitř webu Azure portal, vyžadujeme, vytvoření aplikace Active Directory tak, že abychom mohli ověřit připojení mezi dvěma naše služby je za ověřené komunikaci. U těchto polí, vytvořit aplikaci AD a vložte odpovídající Id Tenanta a Id aplikace. Všimněte si, že je id aplikace přidružené k vaší publisherID. Ujistěte se proto, že stejné ID aplikace jako všechny nabídky.   |
|   |   |

Nakonec Pokud vyberete **prodávat prostřednictvím Microsoft**, existuje další nová nabídka kartu s názvem **plány**. 

[Kartu plans](./cpp-plans-tab.md) jsou uvedeny konkrétní plány a jejich odpovídající cenu, které vaše aplikace SaaS podporuje. Od dnešního dne umožňujeme měsíční ceny, možnost povolit pro 1 - nebo 3měsíční bezplatný přístup. Tyto plány a ceny by měl odpovídat přesně plány a ceny, které mají na svůj vlastní web aplikace SaaS.

>[!NOTE] 
>Plány jsou potřeba pouze v případě zvolte prodej prostřednictvím společnosti Microsoft.

### <a name="call-to-action-field"></a>Volání za účelem pole Akce

Volání pole akce umožňuje vybrat zprávu, která se zobrazí na tlačítku pro získání vaší nabídky. Jsou k dispozici následující možnosti:

- Free – Pokud vyberete tuto možnost, budete vyzváni k zadejte adresu URL zkušební verze, kde Zákazníci mohou získat přístup k aplikaci SaaS. Příklad: https://contoso.com/trial
- Bezplatná zkušební verze – Pokud vyberete tuto možnost, zobrazí výzva zadat adresu URL zkušební verze, kde Zákazníci mohou získat přístup k aplikaci SaaS. Příklad: https://contoso.com/trial
- Kontaktujte mě

Další informace o volání akce možnosti najdete v článku vybrat možnost publikování.

>[!Note]
>Kanál partnera cloudových poskytovatelů řešení (CSP) vyjádřit výslovný souhlas nyní k dispozici.  Podrobnosti najdete na [poskytovatele Cloud Solution Provider](../../cloud-solution-providers.md) Další informace o marketingu vaší nabídky prostřednictvím Microsoft CSP partner kanály.

## <a name="next-steps"></a>Další postup

- [Kartu plans (volitelné)](./cpp-plans-tab.md)
- [Karta informací o kanálech](./cpp-channel-info-tab.md)
