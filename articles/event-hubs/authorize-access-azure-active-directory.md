---
title: Autorizace přístupu pomocí Azure Active Directory
description: Tento článek poskytuje informace o autorizaci přístupu k prostředkům Event Hubs pomocí Azure Active Directory.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: d26838831964b66beea4ed1e3c6e9873cfce247e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270028"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory
Azure Event Hubs podporuje použití Azure Active Directory (Azure AD) k autorizaci požadavků na Event Hubs prostředky. Pomocí Azure AD můžete použít řízení přístupu na základě role (RBAC) k udělení oprávnění objektu zabezpečení, který může být uživatel nebo instanční objekt služby. Další informace o rolích a přiřazení rolí najdete v tématu [Principy různých rolí](../role-based-access-control/overview.md).

## <a name="overview"></a>Přehled
Když se objekt zabezpečení (uživatel nebo aplikace) pokusí o přístup k prostředku Event Hubs, musí být žádost autorizována. S Azure AD je přístup k prostředku v procesu se dvěma kroky. 

 1. Nejprve je ověřená identita objektu zabezpečení a je vrácen token OAuth 2,0. Název prostředku pro vyžádání tokenu `https://eventhubs.azure.net/` . Pro klienty Kafka je prostředek pro vyžádání tokenu `https://<namespace>.servicebus.windows.net` .
 1. V dalším kroku se token předává jako součást požadavku služby Event Hubs k autorizaci přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby žádost o aplikaci obsahovala přístupový token OAuth 2,0 za běhu. Pokud je aplikace spuštěná v rámci entity Azure, jako je třeba virtuální počítač Azure, sada škálování virtuálního počítače nebo aplikace funkce Azure, může pro přístup k prostředkům použít spravovanou identitu. Informace o tom, jak ověřit požadavky prováděné spravovanou identitou pro Event Hubs službu, najdete v tématu [ověření přístupu k prostředkům azure Event Hubs pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](authenticate-managed-identity.md). 

Autorizační krok vyžaduje, aby se k objektu zabezpečení přiřadila jedna nebo více rolí Azure. Azure Event Hubs poskytuje role Azure, které zahrnují sady oprávnění pro prostředky Event Hubs. Role, které jsou přiřazeny objektu zabezpečení, určují oprávnění, která bude mít objekt zabezpečení. Další informace o rolích Azure najdete v tématu [předdefinované role Azure pro azure Event Hubs](#azure-built-in-roles-for-azure-event-hubs). 

Nativní aplikace a webové aplikace, které vytvářejí požadavky na Event Hubs, mohou být také autorizovány pomocí Azure AD. Informace o tom, jak požádat o přístupový token a použít ho k autorizaci žádostí o Event Hubs prostředky, najdete v tématu [ověření přístupu k azure Event Hubs pomocí Azure AD z aplikace](authenticate-application.md). 

## <a name="assign-azure-roles-for-access-rights"></a>Přiřazení rolí Azure pro přístupová práva
Azure Active Directory (Azure AD) autorizuje přístupová práva k zabezpečeným prostředkům prostřednictvím [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/overview.md). Azure Event Hubs definuje sadu předdefinovaných rolí Azure, které zahrnují společné sady oprávnění používaných pro přístup k datům centra událostí, a můžete také definovat vlastní role pro přístup k datům.

Když je role Azure přiřazená k objektu zabezpečení Azure AD, poskytuje Azure přístup k těmto prostředkům pro daný objekt zabezpečení. Přístup může být vymezen na úrovni předplatného, skupiny prostředků, oboru názvů Event Hubs nebo jakéhokoli prostředku. Objekt zabezpečení služby Azure AD může být uživatel nebo instanční objekt nebo [spravovaná identita pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Předdefinované role Azure pro Azure Event Hubs
Azure poskytuje následující předdefinované role Azure pro autorizaci přístupu k Event Hubs datům pomocí Azure AD a OAuth:

| Role | Popis | 
| ---- | ----------- | 
[Vlastník dat Event Hubs Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | Pomocí této role získáte úplný přístup k Event Hubs prostředkům. |
| [Odesilatel dat Event Hubs Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | Pomocí této role udělíte přístup k prostředkům Event Hubs. |
| [Přijímač dat Event Hubs Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | Tuto roli použijte, pokud chcete, aby přístup k Event Hubs prostředkům poskytoval náročné a přijímaly. |
| Nástroj pro čtení registru schématu (Preview) | Čtení a výpis skupin registru a schémat registru. |
| Přispěvatel registru schématu (Preview) | Čtení, zápis a odstraňování skupin registru schémat a schémat. |

## <a name="resource-scope"></a>Obor prostředku 
Než do objektu zabezpečení přiřadíte roli Azure, určete rozsah přístupu, který má objekt zabezpečení mít. Osvědčené postupy určují, že vždy nejlépe přidělíte jenom nejužšímu možnému rozsahu.

Následující seznam popisuje úrovně, na jejichž základě můžete nastavit rozsah přístupu k prostředkům Event Hubs, počínaje nejužším rozsahem:

- **Skupina příjemců**: v tomto oboru se přiřazení role vztahuje pouze na tuto entitu. V současné době Azure Portal nepodporuje přiřazení role Azure k objektu zabezpečení na této úrovni. 
- **Centrum událostí**: přiřazení role se vztahuje k entitě centra událostí a skupině příjemců.
- **Obor názvů**: přiřazování rolí zahrnuje celou topologii Event Hubs pod oborem názvů a do skupiny uživatelů, které jsou k ní přidružené.
- **Skupina prostředků**: přiřazení role se vztahuje na všechny prostředky Event Hubs v rámci skupiny prostředků.
- **Předplatné**: přiřazení role se vztahuje na všechny prostředky Event Hubs ve všech skupinách prostředků v rámci předplatného.

> [!NOTE]
> - Mějte na paměti, že rozšíření přiřazení rolí Azure může trvat až pět minut. 
> - Tento obsah se týká Event Hubs i Event Hubs pro Apache Kafka. Další informace o Event Hubs podpoře pro Kafka najdete v článku [Event Hubs pro Kafka-zabezpečení a ověřování](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Další informace o tom, jak jsou předdefinované role definované, najdete v tématu [vysvětlení definic rolí](../role-based-access-control/role-definitions.md#management-and-data-operations). Informace o vytváření vlastních rolí Azure najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Ukázky
- [Ukázky Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
    
    Tyto ukázky používají starou knihovnu **Microsoft. Azure. EventHubs** , ale můžete ji snadno aktualizovat tak, aby používala nejnovější knihovnu **Azure. Messaging. EventHubs** . Postup přesunutí ukázky z použití staré knihovny do nové verze najdete v [Průvodci migrací z Microsoft. Azure. EventHubs do Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Ukázky pro Azure. Messaging. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Tato ukázka se aktualizovala tak, aby používala nejnovější knihovnu **Azure. Messaging. EventHubs** .
- [Event Hubs pro ukázky Kafka-OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Další kroky
- Informace o tom, jak přiřadit předdefinovanou roli Azure k objektu zabezpečení, najdete v tématu [ověření přístupu k Event Hubs prostředkům pomocí Azure Active Directory](authenticate-application.md).
- Naučte [se vytvářet vlastní role pomocí RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Naučte [se používat Azure Active Directory s eh](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK) .

Podívejte se na následující související články:

- [Ověřování požadavků do Azure Event Hubs z aplikace pomocí Azure Active Directory](authenticate-application.md)
- [Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs](authenticate-managed-identity.md)
- [Ověřování požadavků na Azure Event Hubs pomocí sdílených přístupových podpisů](authenticate-shared-access-signature.md)
- [Autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)
