---
title: Publikování nabídky spravovaných služeb na Azure Marketplace
description: Zjistěte, jak publikovat nabídku spravované služby, která zákazníkům zařazuje delegovanou správu prostředků Azure.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673935"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publikování nabídky spravovaných služeb na Azure Marketplace

V tomto článku se dozvíte, jak publikovat veřejnou nebo soukromou nabídku spravované služby na [Azure Marketplace](https://azuremarketplace.microsoft.com) pomocí programu [Commercial Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) v Centru [partnerů](https://partner.microsoft.com/). Zákazníci, kteří si zakoupí nabídku, pak mohou zavést předplatná a skupiny prostředků pro [správu delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md).

## <a name="publishing-requirements"></a>Požadavky na publikování

Chcete-li vytvářet a publikovat nabídky, musíte mít [v Partnerském centru](../../marketplace/partner-center-portal/create-account.md) platný účet. Pokud ještě nemáte účet, proces [registrace](https://aka.ms/joinmarketplace) vás provede kroky vytvoření účtu v Partnerském centru a registrace do programu Commercial Marketplace.

Podle požadavků na [certifikaci spravované služby](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)musíte mít [úroveň kompetence silver nebo gold cloudové platformy](https://docs.microsoft.com/partner-center/learn-about-competencies) nebo být [msp experta Azure,](https://partner.microsoft.com/membership/azure-expert-msp) abyste mohli publikovat nabídku spravované služby.

ID sítě Microsoft Partner Network (MPN) bude [automaticky přidruženo](../../billing/billing-partner-admin-link-started.md) k nabídkám, které publikujete, aby bylo možné sledovat váš dopad na příčky zákazníků.

> [!NOTE]
> Pokud nechcete publikovat nabídku na Azure Marketplace, můžete zákazníky zaoklézat ručně pomocí šablon Azure Resource Manager. Další informace najdete [v tématu Onboard zákazníka azure delegované správy prostředků](onboard-customer.md).

## <a name="create-your-offer"></a>Vytvořte si nabídku

Podrobné pokyny k vytvoření nabídky, včetně všech informací a prostředků, které budete potřebovat poskytnout, najdete v [tématu Vytvoření nové nabídky spravovaných služeb](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Informace o obecném procesu publikování najdete v [tématu Azure Marketplace a Průvodce publikováním AppSource](../../marketplace/marketplace-publishers-guide.md). Měli byste si také přečíst [zásady certifikace komerčního tržiště](https://docs.microsoft.com/legal/marketplace/certification-policies), zejména část [Spravované služby.](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)

Jakmile zákazník přidá vaši nabídku, bude moct delegovat jedno nebo více předplatných nebo skupin prostředků, které pak budou [na palubě pro správu delegovaných prostředků Azure](#the-customer-onboarding-process).

> [!IMPORTANT]
> Každý plán v nabídce spravované služby zahrnuje oddíl **Podrobnosti manifestu,** kde definujete entity Azure Active Directory (Azure AD) ve vašem tenantovi, které budou mít přístup k delegovaným skupinám prostředků nebo předplatným pro zákazníky, kteří si tento plán zakoupí. Je důležité si uvědomit, že každá skupina (nebo uživatel nebo instanční objekt), který zahrnete, bude mít stejná oprávnění pro každého zákazníka, který si plán zakoupí. Chcete-li přiřadit různé skupiny pro práci s každým zákazníkem, budete muset publikovat samostatný [soukromý plán,](../../marketplace/private-offers.md) který je výhradní pro každého zákazníka.

## <a name="publish-your-offer"></a>Publikování nabídky

Po dokončení všech částí je dalším krokem publikování nabídky na Azure Marketplace. Výběrem tlačítka **Publikovat** zahájíte proces vytváření nabídky živě. Více informací o tomto procesu naleznete [zde](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish).

[Aktualizovanou verzi nabídky](../../marketplace/partner-center-portal/update-existing-offer.md) můžete kdykoli publikovat. Můžete například přidat novou definici role do dříve publikované nabídky. Když tak učiníte, zákazníci, kteří už přidali nabídku, uvidí na stránce [**Poskytovatelé služeb**](view-manage-service-providers.md) na webu Azure Portal ikonu, která jim dá vědět, že je k dispozici aktualizace. Každý zákazník bude moci [zkontrolovat změny](view-manage-service-providers.md#update-service-provider-offers) a rozhodnout, zda chce aktualizovat na novou verzi. 

## <a name="the-customer-onboarding-process"></a>Proces registrace zákazníka

Poté, co zákazník přidá vaši nabídku, bude moct [delegovat jedno nebo více konkrétních předplatných nebo skupin prostředků](view-manage-service-providers.md#delegate-resources), které pak budou na palubě pro správu delegovaných prostředků Azure. Pokud zákazník přijal nabídku, ale ještě nedelegoval žádné prostředky, zobrazí se mu poznámka v horní části stránky **Poskytovatelé** [**služeb**](view-manage-service-providers.md) na webu Azure Portal.

> [!IMPORTANT]
> Delegování musí být provedeno nehostem v tenantovi zákazníka, který má [předdefinovanou roli vlastníka](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pro předplatné, které je na palubě (nebo který obsahuje skupiny prostředků, které jsou na palubě). Chcete-li zobrazit všechny uživatele, kteří mohou delegovat předplatné, uživatel v tenantovi zákazníka můžete vybrat předplatné na webu Azure Portal, otevřete **řízení přístupu (IAM)** a [zobrazit všechny uživatele s rolí Vlastník](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Jakmile zákazník deleguje předplatné (nebo jednu nebo více skupin prostředků v rámci předplatného), poskytovatel prostředků **Microsoft.ManagedServices** bude pro toto předplatné zaregistrován a uživatelé ve vašem tenantovi budou mít přístup k delegovaným prostředkům podle autorizací ve vaší nabídce.

## <a name="next-steps"></a>Další kroky

- Další informace o [komerčním tržišti](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Další informace o [prostředích správy mezi tenanty](../concepts/cross-tenant-management-experience.md).
- [Zobrazte a spravujte zákazníky](view-manage-customers.md) tak, že přejdete na můj **zákazník** na webu Azure Portal.
