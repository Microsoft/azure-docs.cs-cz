---
title: Autorizace přístupu ke službě Azure Event Hubs
description: Tento článek poskytuje informace o různých možnostech pro autorizaci přístupu k prostředkům Azure Event Hubs.
ms.topic: conceptual
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 5fc35387f4ac28ad4dd28bea0013bcdf1e1e9f02
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92332378"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorizace přístupu ke službě Azure Event Hubs
Pokaždé, když publikujete nebo spotřebujete události a data z centra událostí, se klient snaží získat přístup k prostředkům Event Hubs. Každý požadavek na zabezpečený prostředek musí být autorizovaný, aby služba mohla zajistit, aby měl klient potřebná oprávnění k publikování/využívání dat. 

Azure Event Hubs nabízí následující možnosti pro autorizaci přístupu k zabezpečeným prostředkům:

- Azure Active Directory
- Sdílený přístupový podpis

> [!NOTE]
> Tento článek se týká Event Hubs i [Apache Kafkach](event-hubs-for-kafka-ecosystem-overview.md) scénářů. 

## <a name="azure-active-directory"></a>Azure Active Directory
Integrace služby Azure Active Directory (Azure AD) pro prostředky Event Hubs poskytuje řízení přístupu na základě role Azure (Azure RBAC) pro detailní kontrolu nad přístupem klienta k prostředkům. Službu Azure RBAC můžete použít k udělení oprávnění objektu zabezpečení, který může být uživatel, skupina nebo instanční objekt aplikace. Služba Azure AD ověřuje objekt zabezpečení, aby vrátil token OAuth 2,0. Token se dá použít k autorizaci žádosti o přístup k prostředku Event Hubs.

Další informace o ověřování ve službě Azure AD najdete v následujících článcích:

- [Ověřování požadavků do Azure Event Hubs pomocí Azure Active Directory](authenticate-application.md)
- [Autorizujte přístup k prostředkům Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy 
Sdílené přístupové podpisy (SAS) pro prostředky Event Hubs poskytují omezený delegovaný přístup k prostředkům Event Hubs. Přidávání omezení v časovém intervalu, pro který je podpis platný, nebo na oprávnění, která uděluje, poskytují flexibilitu při správě prostředků. Další informace najdete v tématu [ověřování pomocí sdílených přístupových podpisů (SAS)](authenticate-shared-access-signature.md). 

Autorizaci uživatelů nebo aplikací pomocí tokenu OAuth 2,0 vráceného službou Azure AD poskytuje prvotřídní zabezpečení a usnadňuje použití přes sdílené přístupové podpisy (SAS). S Azure AD není nutné ukládat přístupové tokeny s vaším kódem a rizikovým potenciálním ohrožením zabezpečení. I když můžete nadále používat sdílené přístupové podpisy (SAS) pro udělení jemně odstupňovaného přístupu k Event Hubs prostředkům, Azure AD nabízí podobné možnosti, aniž by bylo nutné spravovat tokeny SAS nebo se starat o odvolání ohroženého SAS. 

Ve výchozím nastavení jsou všechny prostředky Event Hubs zabezpečené a jsou k dispozici pouze vlastníkovi účtu. I když můžete použít kteroukoli strategii autorizace uvedenou výše a udělit klientům přístup k prostředkům centra událostí. Microsoft doporučuje používat Azure AD, pokud je to možné, pro zajištění maximálního zabezpečení a snadného použití.

Další informace o autorizaci pomocí SAS najdete v tématu [autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Další kroky
- Podívejte se na [ukázky Azure RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) publikované v našem úložišti GitHubu. 
- Viz následující články:
    - [Ověřování požadavků do Azure Event Hubs z aplikace pomocí Azure Active Directory](authenticate-application.md)
    - [Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs](authenticate-managed-identity.md)
    - [Ověřování požadavků na Azure Event Hubs pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
    - [Autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)

