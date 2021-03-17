---
title: Požadavky na rozhraní API – Azure Marketplace
description: Předpoklady pro používání rozhraní portál partnerů cloudu API
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: 374cc3398d1037b19ceddcbdaafd333f643e44ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91261154"
---
# <a name="api-prerequisites"></a>Požadavky na rozhraní API

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Pro existující produkty, které už jsou integrované před přechodem do partnerského centra, používejte jenom rozhraní API CPP. nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

K používání rozhraní portál partnerů cloudu API potřebujete dva požadované programové prostředky: instanční objekt a přístupový token služby Azure Active Directory (Azure AD).

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>Vytvoření instančního objektu ve službě Azure Active Directory tenant

Nejdřív je potřeba vytvořit instanční objekt v tenantovi Azure AD. Tomuto tenantovi se přiřadí vlastní sada oprávnění v portál partnerů cloudu. Váš kód bude volat rozhraní API pomocí tohoto tenanta místo vašich osobních přihlašovacích údajů. Úplné vysvětlení vytváření instančního objektu naleznete v tématu [How to: use the a Create a Application Azure AD a instanční objekt, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="add-service-principal-to-your-account"></a>Přidání instančního objektu k vašemu účtu

Teď, když jste ve svém tenantovi vytvořili instanční objekt, ho můžete přidat jako uživatele na účet vašeho portálu partnerského centra. Stejně jako uživatel může instanční objekt být vlastníkem nebo přispěvatelem portálu. Podrobnosti najdete v části **Další kroky** níže.

## <a name="next-steps"></a>Další kroky

Viz [Správa aplikací Azure AD](partner-center-portal/manage-account.md#manage-azure-ad-applications).
