---
title: Publikování nabídky spravované služby pro Azure Marketplace
description: Naučte se publikovat nabídku spravované služby, která zákazníkům zařadí do Azure Lighthouse.
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: b906ba9961ae4f77ca16890fd0a9036123343111
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092155"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publikování nabídky spravované služby pro Azure Marketplace

V tomto článku se dozvíte, jak publikovat veřejnou nebo soukromou nabídku spravované služby, která se [Azure Marketplace](https://azuremarketplace.microsoft.com) pomocí programu [komerčního obchodu](../../marketplace/overview.md) v partnerském centru. Zákazníci, kteří nabídku kupují, pak budou delegovat předplatná nebo skupiny prostředků, což vám umožní je spravovat prostřednictvím [Azure Lighthouse](../overview.md).

## <a name="publishing-requirements"></a>Požadavky na publikování

Aby bylo možné vytvořit a publikovat nabídky, musíte mít platný [účet v partnerském centru](../../marketplace/partner-center-portal/create-account.md) . Pokud účet ještě nemáte, [proces registrace](https://aka.ms/joinmarketplace) vás provede kroky pro vytvoření účtu v partnerském centru a registraci v programu komerčního tržiště.

Aby bylo možné publikovat nabídku spravované služby podle [spravované služby](/legal/marketplace/certification-policies#700-managed-services), musíte mít [úroveň kompetence cloudové platformy stříbrné nebo Gold](/partner-center/learn-about-competencies) nebo být [odborníkem na Azure Experts MSP](https://partner.microsoft.com/membership/azure-expert-msp) . Je také nutné [zadat cíl zájemce, který vytvoří záznam v systému CRM](../../marketplace/plan-managed-service-offer.md#customer-leads) pokaždé, když zákazník nasadí vaši nabídku.

Pokud nechcete publikovat nabídku Azure Marketplace nebo nesplňujete všechny požadavky, můžete zákazníky připojit ručně pomocí Azure Resource Manager šablon. Další informace najdete v tématu [zprovoznění zákazníka do Azure Lighthouse](onboard-customer.md).

Následující tabulka vám pomůže určit, jestli se zákazníci mají připojit, publikováním nabídky spravované služby nebo pomocí Azure Resource Manager šablon.

|**Aspekty**  |**Nabídka spravované služby**  |**Šablony ARM**  |
|---------|---------|---------|
|Vyžaduje [účet partnerského centra](../../marketplace/partner-center-portal/create-account.md)   |Yes         |No        |
|Vyžaduje [úroveň kompetence cloudové platformy stříbrného nebo Gold](/partner-center/learn-about-competencies) nebo [Azure expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) .      |Yes         |No         |
|K dispozici novým zákazníkům prostřednictvím Azure Marketplace     |Yes     |No       |
|Může omezit nabídku na konkrétní zákazníky.     |Ano (jenom s privátními nabídkami, které se nedají použít s odběry vytvořenými prostřednictvím prodejce v programu Cloud Solution Provider (CSP))         |Yes         |
|Vyžaduje přijetí zákazníkem v Azure Portal     |Yes     |No   |
|Může používat automatizaci k připojování více předplatných, skupin prostředků nebo zákazníků. |No     |Yes    |
|Okamžitý přístup k novým vestavěným rolím a funkcím Azure Lighthouse     |Ne vždy (všeobecně dostupné po nějaké prodlevě)         |Yes         |

> [!NOTE]
> Nabídky spravované služby nemusí být k dispozici v Azure Government a dalších národních cloudech.

## <a name="create-your-offer"></a>Vytvoření nabídky

Podrobné pokyny k vytvoření nabídky, včetně všech informací a prostředků, které budete potřebovat, najdete v tématu [Vytvoření nabídky spravované služby](../../marketplace/create-managed-service-offer.md).

Další informace o obecném procesu publikování najdete v [dokumentaci k komerčnímu webu Marketplace](../../marketplace/overview.md). Měli byste si také projít [zásady certifikace komerčního tržiště](/legal/marketplace/certification-policies), zejména část [spravované služby](/legal/marketplace/certification-policies#700-managed-services) .

Jakmile zákazník vaši nabídku přidá, bude moct delegovat jedno nebo víc předplatných nebo skupin prostředků, které se pak připojí [do Azure Lighthouse](#the-customer-onboarding-process).

> [!IMPORTANT]
> Každý plán v nabídce spravované služby obsahuje oddíl s **podrobnostmi o manifestu** , ve kterém definujete entity Azure Active Directory (Azure AD) ve vašem tenantovi, které budou mít přístup k delegovaným skupinám prostředků nebo předplatným pro zákazníky, kteří si zakoupí tento plán. Je důležité si uvědomit, že jakákoli skupina (nebo uživatel nebo instanční objekt), kterou zahrnete, bude mít stejná oprávnění pro každého zákazníka, který plán koupí. Pro přiřazení různých skupin pro práci se všemi zákazníky můžete publikovat samostatný [soukromý plán](../../marketplace/private-offers.md) , který je exkluzivní pro každého zákazníka. Pamatujte, že privátní plány nejsou podporovány u předplatných vytvořených prostřednictvím prodejce programu Cloud Solution Provider (CSP).

## <a name="publish-your-offer"></a>Publikování nabídky

Po dokončení všech oddílů je dalším krokem publikování nabídky Azure Marketplace. Kliknutím na tlačítko **publikovat** zahajte proces provádění vaší nabídky v reálném čase. Další informace o tomto procesu najdete [tady](../../marketplace/review-publish-offer.md).

[Aktualizovanou verzi vaší nabídky můžete publikovat](../..//marketplace/partner-center-portal/update-existing-offer.md) kdykoli. Například můžete chtít přidat novou definici role do dříve publikované nabídky. V takovém případě se zákazníkům, kteří si tuto nabídku už přidali, na Azure Portal stránce [**poskytovatelé služeb**](view-manage-service-providers.md) zobrazí ikona s informacemi o tom, že je k dispozici aktualizace. Každý zákazník bude moci [Zkontrolovat změny](view-manage-service-providers.md#update-service-provider-offers) a rozhodnout, jestli chtějí aktualizovat na novou verzi. 

## <a name="the-customer-onboarding-process"></a>Proces zprovoznění zákazníků

Jakmile zákazník přidá vaši nabídku, bude moct [delegovat jedno nebo několik konkrétních předplatných nebo skupin prostředků](view-manage-service-providers.md#delegate-resources), které se pak připojí do Azure Lighthouse. Pokud zákazník nabídku přijal, ale ještě nedelegoval žádné prostředky, uvidí v **horní části stránky** poskytovatelé [**služeb**](view-manage-service-providers.md) v Azure Portal poznámku.

> [!IMPORTANT]
> Delegování je nutné provést pomocí účtu, který není hostem v tenantovi zákazníka, který má roli s `Microsoft.Authorization/roleAssignments/write` oprávněním, jako je například [vlastník](../../role-based-access-control/built-in-roles.md#owner), pro odběr, který se připojuje (nebo který obsahuje skupiny prostředků, které jsou připojené). Pokud chcete najít uživatele, kteří můžou delegovat předplatné, uživatel v tenantovi zákazníka může vybrat předplatné ve Azure Portal, otevřít **řízení přístupu (IAM)** a [Zobrazit všechny uživatele s rolí vlastníka](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Jakmile zákazník deleguje předplatné (nebo jednu nebo více skupin prostředků v rámci předplatného), zaregistruje se pro toto předplatné poskytovatele prostředků **Microsoft. ManagedServices** a uživatelé ve vašem tenantovi budou mít přístup k delegovaným prostředkům v závislosti na autorizacích vaší nabídky.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [komerčním tržišti](../../marketplace/overview.md).
- [Propojte své ID partnera](partner-earned-credit.md) a sledujte svůj dopad na zapojení zákazníků.
- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
- V **Azure Portal můžete** [Zobrazit a spravovat zákazníky](view-manage-customers.md) .
