---
title: Autorizace přístupu ke službě Azure Event Hubs
description: Tento článek obsahuje informace o různých možnostech autorizace přístupu k prostředkům Centra událostí Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: d4304abf0ca089fbbea86f12cd03dea836db612e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368349"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Autorizace přístupu ke službě Azure Event Hubs
Pokaždé, když publikujete nebo spotřebováváte události/data z centra událostí, váš klient se pokouší získat přístup k prostředkům Centra událostí. Každý požadavek na zabezpečený prostředek musí být autorizován, aby služba mohla zajistit, že klient má požadovaná oprávnění k publikování nebo využití dat. 

Azure Event Hubs nabízí následující možnosti pro autorizaci přístupu k zabezpečeným prostředkům:

- Azure Active Directory
- Sdílený přístupový podpis

> [!NOTE]
> Tento článek se vztahuje na scénáře Event Hubs a [Apache Kafka.](event-hubs-for-kafka-ecosystem-overview.md) 

## <a name="azure-active-directory"></a>Azure Active Directory
Integrace Azure Active Directory (Azure AD) pro prostředky centra událostí poskytuje řízení přístupu na základě rolí (RBAC) pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům. Pomocí řízení přístupu na základě rolí (RBAC) můžete udělit oprávnění k zaregistrovaný objekt zabezpečení, který může být uživatel, skupina nebo instanční objekt služby aplikace. Objekt zabezpečení je ověřen službou Azure AD, aby vrátil token OAuth 2.0. Token lze použít k autorizaci požadavku na přístup k prostředku centra událostí.

Další informace o ověřování pomocí služby Azure AD najdete v následujících článcích:

- [Ověřování požadavků na Centra událostí Azure pomocí Služby Azure Active Directory](authenticate-application.md)
- [Autorizujte přístup k prostředkům Centra událostí pomocí služby Azure Active Directory](authorize-access-azure-active-directory.md).

## <a name="shared-access-signatures"></a>Sdílené přístupové podpisy 
Sdílené přístupové podpisy (SAS) pro prostředky Centra událostí poskytují omezený delegovaný přístup k prostředkům Centra událostí. Přidání omezení časového intervalu, pro který je podpis platný, nebo oprávnění, která uděluje, poskytuje flexibilitu při správě prostředků. Další informace naleznete v [tématu Authenticate using shared access signatures (SAS)](authenticate-shared-access-signature.md). 

Autorizace uživatelů nebo aplikací pomocí tokenu OAuth 2.0 vráceného službou Azure AD poskytuje vynikající zabezpečení a snadné použití oproti sdíleným přístupovým podpisům (SAS). S Azure AD, není nutné ukládat přístupové tokeny s kódem a riskovat potenciální ohrožení zabezpečení. Zatímco můžete i nadále používat sdílené přístupové podpisy (SAS) k udělení jemně odstupňovaného přístupu k prostředkům Centra událostí, Azure AD nabízí podobné funkce bez nutnosti spravovat tokeny SAS nebo se obávat zrušení ohroženého SAS. 

Ve výchozím nastavení jsou všechny prostředky centra událostí zabezpečené a jsou k dispozici pouze pro vlastníka účtu. I když můžete použít některou z výše uvedených strategií autorizace k udělení přístupu klientům k prostředkům Centra událostí. Autorizaci Azure AD podporují jenom prostředky event hubů vytvořené pomocí modelu nasazení Azure Resource Manageru. Společnost Microsoft doporučuje používat Azure AD, pokud je to možné pro maximální zabezpečení a snadné použití.

Další informace o autorizaci pomocí sas, naleznete [v tématu Autorizace přístupu k prostředkům centra událostí pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>Další kroky
- Prohlédněte si [ukázky RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) publikované v našem úložišti GitHub. 
- Viz následující články:
    - [Ověřování požadavků na Centra událostí Azure z aplikace pomocí Služby Azure Active Directory](authenticate-application.md)
    - [Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům centra událostí](authenticate-managed-identity.md)
    - [Ověřování požadavků na Centra událostí Azure pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
    - [Autorizace přístupu k prostředkům Centra událostí pomocí Služby Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorizace přístupu k prostředkům Centra událostí pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)

