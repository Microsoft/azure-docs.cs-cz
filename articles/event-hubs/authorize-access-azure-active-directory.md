---
title: Autorizace přístupu pomocí Azure Active Directory
description: Tento článek obsahuje informace o autorizaci přístupu k prostředkům Centra událostí pomocí služby Azure Active Directory.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 1fa8f7a48c03ead7e939185b23834b3049b3e21c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064860"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autorizace přístupu k prostředkům Centra událostí pomocí Služby Azure Active Directory
Azure Event Hubs podporuje použití Azure Active Directory (Azure AD) k autorizaci požadavků na prostředky Event Hubs. S Azure AD, můžete použít řízení přístupu na základě rolí (RBAC) udělit oprávnění k zaregistrovaný objekt zabezpečení, který může být uživatel nebo instanční objekt služby aplikace. Další informace o rolích a přiřazeních rolí naleznete v [tématu Principy různých rolí](../role-based-access-control/overview.md).

## <a name="overview"></a>Přehled
Když se objekt zabezpečení (uživatel nebo aplikace) pokusí o přístup k prostředku centra událostí, musí být požadavek autorizován. S Azure AD, přístup k prostředku je dvoustupňový proces. 

 1. Nejprve je ověřena identita objektu zabezpečení a je vrácen token OAuth 2.0. Název prostředku pro vyžádání `https://eventhubs.azure.net/`tokenu je . Pro klienty Kafka je `https://<namespace>.servicebus.windows.net`prostředek, který požaduje token .
 1. Dále je token předán jako součást požadavku na službu Event Hubs o autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby požadavek aplikace obsahoval přístupový token OAuth 2.0 za běhu. Pokud aplikace běží v rámci entity Azure, jako je například virtuální počítač Azure, škálovací sada virtuálního počítače nebo aplikace Azure Function, můžete použít spravovanou identitu pro přístup k prostředkům. Informace o tom, jak ověřovat požadavky spravované identity ve službě Event Hubs, najdete [v tématu Ověřování přístupu k prostředkům Centra událostí Azure pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](authenticate-managed-identity.md). 

Krok autorizace vyžaduje, aby byla k objektu zabezpečení přiřazena jedna nebo více rolí RBAC. Azure Event Hubs poskytuje role RBAC, které zahrnují sady oprávnění pro prostředky event hubů. Role, které jsou přiřazeny k objektu zabezpečení určit oprávnění, která bude mít objekt zabezpečení. Další informace o rolích RBAC najdete [v tématu předdefinované role RBAC pro centra událostí Azure](#built-in-rbac-roles-for-azure-event-hubs). 

Nativní aplikace a webové aplikace, které pořizují požadavky na centra událostí, můžou taky autorizovat pomocí Azure AD. Informace o tom, jak požádat o přístupový token a použít ho k autorizaci požadavků na prostředky Centra událostí, najdete [v tématu Ověřování přístupu k centru událostí Azure pomocí Azure AD z aplikace](authenticate-application.md). 

## <a name="assign-rbac-roles-for-access-rights"></a>Přiřazení rolí RBAC pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě rolí (RBAC).](../role-based-access-control/overview.md) Azure Event Hubs definuje sadu předdefinovaných rolí RBAC, které zahrnují společné sady oprávnění používaných pro přístup k datům centra událostí a můžete také definovat vlastní role pro přístup k datům.

Když je role RBAC přiřazena k objektu zabezpečení Azure AD, Azure uděluje přístup k těmto prostředkům pro tento objekt zabezpečení. Přístup může být vymezen na úroveň předplatného, skupiny prostředků, obor názvů Event Hubs nebo jakýkoli prostředek pod ním. Zaregistrovaný objekt zabezpečení Azure AD může být uživatel nebo instanční objekt služby aplikace nebo [spravovaná identita pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="built-in-rbac-roles-for-azure-event-hubs"></a>Integrované role RBAC pro Centra událostí Azure
Azure poskytuje následující integrované role RBAC pro autorizaci přístupu k datům centra událostí pomocí Azure AD a OAuth:

- [Vlastník dat centra událostí Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Pomocí této role můžete poskytnout úplný přístup k prostředkům Centra událostí.
- [Odesílatel dat centra událostí Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Pomocí této role můžete udělit přístup k odesílání prostředků centra událostí.
- [Přijímač dat centra událostí Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Tato role slouží k tomu, abyste spotřebovávají a přijímala přístup k prostředkům centra událostí.

## <a name="resource-scope"></a>Obor prostředků 
Před přiřazením role RBAC k objektu zabezpečení určete rozsah přístupu, který by měl mít zaregistrovaný objekt zabezpečení. Osvědčené postupy určují, že je vždy nejlepší udělit pouze nejužší možný rozsah.

Následující seznam popisuje úrovně, na kterých můžete obor přístup k prostředkům centra událostí, počínaje nejužším rozsahem:

- **Skupina příjemce**: V tomto oboru se přiřazení role vztahuje pouze na tuto entitu. V současné době portál Azure nepodporuje přiřazení role RBAC k objektu zabezpečení na této úrovni. 
- **Centrum událostí**: Přiřazení role se vztahuje na entitu Event Hub a skupinu spotřebitelů pod ní.
- **Obor názvů**: Přiřazení role zahrnuje celou topologii center událostí pod oborem názvů a do skupiny spotřebitelů, která je k němu přidružena.
- **Skupina prostředků**: Přiřazení role se vztahuje na všechny prostředky centra událostí v rámci skupiny prostředků.
- **Předplatné**: Přiřazení role se vztahuje na všechny prostředky Centra událostí ve všech skupinách prostředků v předplatném.

> [!NOTE]
> - Mějte na paměti, že přiřazení rolí RBAC může trvat až pět minut k šíření. 
> - Tento obsah se vztahuje na centra událostí i centra událostí pro Apache Kafka. Další informace o event hubech pro podporu Kafka najdete [v tématu Centra událostí pro Kafka – zabezpečení a ověřování](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Další informace o tom, jak jsou definovány předdefinované role, naleznete [v tématu Principy definic rolí](../role-based-access-control/role-definitions.md#management-and-data-operations). Informace o vytváření vlastních rolí RBAC najdete v [tématu Vytvoření vlastních rolí pro řízení přístupu na základě rolí Azure](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>ukázky
- [Ukázky Microsoft.Azure.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Tyto ukázky používají starou knihovnu **Microsoft.Azure.EventHubs,** ale můžete ji snadno aktualizovat na použití nejnovější **knihovny Azure.Messaging.EventHubs.** Pokud chcete vzorek přesunout z používání staré knihovny do nové, přečtěte si [příručku pro migraci z Microsoft.Azure.EventHubs na Azure.Messaging.EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md).
- [Ukázky Azure.Messaging.EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Tato ukázka byla aktualizována tak, aby používala nejnovější knihovnu **Azure.Messaging.EventHubs.**
- [Event Huby pro ukázky Kafka - OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Další kroky
- Zjistěte, jak přiřadit integrovanou roli RBAC k objektu zabezpečení, najdete v [tématu Ověření přístupu k prostředkům Centra událostí pomocí služby Azure Active Directory](authenticate-application.md).
- Naučte [se vytvářet vlastní role pomocí RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Přečtěte [si, jak používat Azure Active Directory s EH](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Podívejte se na následující související články:

- [Ověřování požadavků na Centra událostí Azure z aplikace pomocí Služby Azure Active Directory](authenticate-application.md)
- [Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům centra událostí](authenticate-managed-identity.md)
- [Ověřování požadavků na Centra událostí Azure pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
- [Autorizace přístupu k prostředkům Centra událostí pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)
