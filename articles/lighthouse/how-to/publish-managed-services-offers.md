---
title: Publikování nabídky spravované služby pro Azure Marketplace
description: Naučte se publikovat nabídku spravované služby, která zákazníkům zařadí správu delegovaných prostředků Azure.
ms.date: 05/04/2020
ms.topic: how-to
ms.openlocfilehash: 214a71faca59072660f1e1f413cb107d8e8f6fc9
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85920901"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publikování nabídky spravované služby pro Azure Marketplace

V tomto článku se dozvíte, jak publikovat veřejnou nebo soukromou nabídku spravované služby, která se [Azure Marketplace](https://azuremarketplace.microsoft.com) pomocí programu [komerčního obchodu](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) v partnerském centru. Zákazníci, kteří si nabídku kupují, pak mohou připojit předplatné a skupiny prostředků pro [správu delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md).

## <a name="publishing-requirements"></a>Požadavky na publikování

Aby bylo možné vytvořit a publikovat nabídky, musíte mít platný [účet v partnerském centru](../../marketplace/partner-center-portal/create-account.md) . Pokud účet ještě nemáte, [proces registrace](https://aka.ms/joinmarketplace) vás provede kroky pro vytvoření účtu v partnerském centru a registraci v programu komerčního tržiště.

Aby bylo možné publikovat nabídku spravované služby podle [spravované služby](/legal/marketplace/certification-policies#7004-business-requirements), musíte mít [úroveň kompetence cloudové platformy stříbrné nebo Gold](/partner-center/learn-about-competencies) nebo být [odborníkem na Azure Experts MSP](https://partner.microsoft.com/membership/azure-expert-msp) .

Vaše ID Microsoft Partner Network (MPN) se [automaticky přidruží](../../billing/billing-partner-admin-link-started.md) k nabídkám, které publikujete, abyste mohli sledovat svůj dopad na zapojení zákazníků.

> [!NOTE]
> Pokud nechcete publikovat nabídku Azure Marketplace, můžete zákazníky připojit ručně pomocí Azure Resource Manager šablon. Další informace najdete v tématu připojení [zákazníka k delegované správě prostředků Azure](onboard-customer.md).

## <a name="create-your-offer"></a>Vytvoření nabídky

Podrobné pokyny k vytvoření nabídky, včetně všech informací a prostředků, které budete potřebovat, najdete v tématu [Vytvoření nabídky spravované služby](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Další informace o obecném procesu publikování najdete v tématu [Průvodce publikováním Azure Marketplace a AppSource](../../marketplace/marketplace-publishers-guide.md). Měli byste si také projít [zásady certifikace komerčního tržiště](/legal/marketplace/certification-policies), zejména část [spravované služby](/legal/marketplace/certification-policies#700-managed-services) .

Jakmile zákazník vaši nabídku přidá, bude moct delegovat jedno nebo víc předplatných nebo skupin prostředků, které se pak připojí [k správě delegovaných prostředků Azure](#the-customer-onboarding-process).

> [!IMPORTANT]
> Každý plán v nabídce spravované služby obsahuje oddíl s **podrobnostmi o manifestu** , ve kterém definujete entity Azure Active Directory (Azure AD) ve vašem tenantovi, které budou mít přístup k delegovaným skupinám prostředků nebo předplatným pro zákazníky, kteří si zakoupí tento plán. Je důležité si uvědomit, že jakákoli skupina (nebo uživatel nebo instanční objekt), kterou zahrnete, bude mít stejná oprávnění pro každého zákazníka, který plán koupí. Pokud chcete přiřadit různé skupiny pro práci s každým zákazníkem, budete muset publikovat samostatný [soukromý plán](../../marketplace/private-offers.md) , který je exkluzivní pro každého zákazníka.

## <a name="publish-your-offer"></a>Publikování nabídky

Po dokončení všech oddílů je dalším krokem publikování nabídky Azure Marketplace. Kliknutím na tlačítko **publikovat** zahajte proces provádění vaší nabídky v reálném čase. Další informace o tomto procesu najdete [tady](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish). 

[Aktualizovanou verzi vaší nabídky můžete publikovat](../..//marketplace/partner-center-portal/update-existing-offer.md) kdykoli. Například můžete chtít přidat novou definici role do dříve publikované nabídky. V takovém případě se zákazníkům, kteří si tuto nabídku už přidali, na Azure Portal stránce [**poskytovatelé služeb**](view-manage-service-providers.md) zobrazí ikona s informacemi o tom, že je k dispozici aktualizace. Každý zákazník bude moci [Zkontrolovat změny](view-manage-service-providers.md#update-service-provider-offers) a rozhodnout, jestli chtějí aktualizovat na novou verzi. 

## <a name="the-customer-onboarding-process"></a>Proces zprovoznění zákazníků

Jakmile zákazník vaši nabídku přidá, bude moct [delegovat jedno nebo několik konkrétních předplatných nebo skupin prostředků](view-manage-service-providers.md#delegate-resources), které se pak budou zakládat pro správu delegovaných prostředků Azure. Pokud zákazník nabídku přijal, ale ještě nedelegoval žádné prostředky, uvidí v **horní části stránky** poskytovatelé [**služeb**](view-manage-service-providers.md) v Azure Portal poznámku.

> [!IMPORTANT]
> Delegování musí provést účet bez hosta v tenantovi zákazníka, který má [předdefinovanou roli](../../role-based-access-control/built-in-roles.md#owner) předplatného pro odběr (nebo který obsahuje skupiny prostředků, které jsou připojené). Pokud chcete zobrazit všechny uživatele, kteří můžou delegovat předplatné, uživatel v tenantovi zákazníka může vybrat předplatné ve Azure Portal, otevřít **řízení přístupu (IAM)** a [Zobrazit všechny uživatele s rolí vlastníka](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Jakmile zákazník deleguje předplatné (nebo jednu nebo více skupin prostředků v rámci předplatného), zaregistruje se pro toto předplatné poskytovatele prostředků **Microsoft. ManagedServices** a uživatelé ve vašem tenantovi budou mít přístup k delegovaným prostředkům v závislosti na autorizacích vaší nabídky.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [komerčním tržišti](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Přečtěte si o [prostředích pro správu mezi klienty](../concepts/cross-tenant-management-experience.md).
- V **Azure Portal můžete** [Zobrazit a spravovat zákazníky](view-manage-customers.md) .
