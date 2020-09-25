---
title: Zabezpečení pro řešení Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Seznamte se s osvědčenými postupy zabezpečení u digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 4dc768b92a9ffbeafc31ef81d065237f47fb645e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331875"
---
# <a name="secure-azure-digital-twins"></a>Zabezpečení digitálních vláken Azure

Z důvodu zabezpečení umožňuje digitální vlákna Azure přesné řízení přístupu ke konkrétním datům, prostředkům a akcím v nasazení. Provede to prostřednictvím podrobné strategie správy rolí a oprávnění označovaného jako **řízení přístupu na základě role (RBAC)**. [Tady](../role-based-access-control/overview.md)si můžete přečíst obecné principy RBAC pro Azure.

Digitální vlákna Azure také podporuje šifrování dat v klidovém umístění.

## <a name="granting-permissions-with-rbac"></a>Udělování oprávnění pomocí RBAC

RBAC je poskytována pro digitální vlákna Azure pomocí integrace s [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Pomocí RBAC můžete udělit oprávnění *objektu zabezpečení*, který může být uživatel, skupina nebo instanční objekt. Objekt zabezpečení je ověřený službou Azure AD a při návratu obdrží token OAuth 2,0. Tento token se dá použít k autorizaci žádosti o přístup k instanci digitálních vláken Azure.

### <a name="authentication-and-authorization"></a>Ověřování a autorizace

S Azure AD je přístup k procesu se dvěma kroky. Když se objekt zabezpečení (uživatel, skupina nebo aplikace) pokusí získat přístup k digitálním podmnožinám Azure, musí být požadavek *ověřený* a *autorizovaný*. 

1. Nejprve je *ověřená*identita objektu zabezpečení a je vrácen token OAuth 2,0.
2. V dalším kroku se token předává jako součást požadavku službě Azure Digital zdvojené služby k *autorizaci* přístupu k zadanému prostředku.

Krok ověřování vyžaduje, aby každá žádost aplikace při spuštění obsahovala přístupový token OAuth 2,0. Pokud je aplikace spuštěná v rámci entity Azure, jako je například aplikace [Azure Functions](../azure-functions/functions-overview.md) , může pro přístup k prostředkům použít **spravovanou identitu** . Další informace o spravovaných identitách najdete v další části.

Autorizační krok vyžaduje, aby byla role Azure přiřazená k objektu zabezpečení. Role, které jsou přiřazeny objektu zabezpečení, určují oprávnění, která bude mít objekt zabezpečení. Funkce digitálních vláken Azure poskytuje role Azure, které zahrnují sady oprávnění pro prostředky digitálních vláken Azure. Tyto role jsou popsány dále v tomto článku.

Další informace o rolích a přiřazení rolí podporovaných v Azure najdete v tématu [*pochopení různých rolí*](../role-based-access-control/rbac-and-directory-admin-roles.md) v dokumentaci k Azure RBAC.

#### <a name="authentication-with-managed-identities"></a>Ověřování pomocí spravovaných identit

[Spravované identity prostředků Azure](../active-directory/managed-identities-azure-resources/overview.md) je funkce mezi Azure, která umožňuje vytvořit zabezpečenou identitu přidruženou k nasazení, kde se spouští kód aplikace. Tuto identitu pak můžete přidružit k rolím řízení přístupu a udělit tak vlastní oprávnění pro přístup ke konkrétním prostředkům Azure, které vaše aplikace potřebuje.

Se spravovanými identitami spravuje platforma Azure tuto identitu modulu runtime. Nemusíte ukládat a chránit přístupové klíče v kódu nebo konfiguraci aplikace, a to buď pro samotnou identitu, nebo pro prostředky, ke kterým potřebujete přístup. Klientská aplikace pro digitální vlákna Azure běžící v aplikaci Azure App Service nepotřebuje zpracovávat pravidla a klíče SAS ani žádné jiné přístupové tokeny. Klientská aplikace potřebuje jenom adresu koncového bodu oboru názvů digitálních vláken Azure. Když se aplikace připojí, digitální vlákna Azure váže kontext spravované entity k klientovi. Po přidružení ke spravované identitě může klient Azure s digitálními podmnožinami provádět všechny autorizované operace. Autorizace se pak udělí přidružením spravované entity k roli Azure Digital-propojování Azure (popsané níže).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Autorizace: role Azure pro digitální vlákna Azure

Azure poskytuje níže vestavěné role Azure pro autorizaci přístupu k prostředku digitálních vláken Azure:
* *Vlastník digitálních vláken Azure (Preview)* – pomocí této role můžete poskytnout úplný přístup k prostředkům Azure Digital Resources.
* *Čtečka digitálních vláken Azure (Preview)* – tato role slouží k udělení přístupu k prostředkům digitálních vláken Azure, které jsou jen pro čtení.

> [!TIP]
> Role *Čtenář digitálních vláken Azure (Preview)* teď podporuje i vztahy procházení.

Další informace o tom, jak jsou předdefinované role definované, najdete v tématu [*vysvětlení definic rolí*](../role-based-access-control/role-definitions.md) v dokumentaci k Azure RBAC. Informace o vytváření vlastních rolí Azure najdete v tématu [*vlastní role Azure*](../role-based-access-control/custom-roles.md).

Role můžete přiřadit dvěma způsoby:
* prostřednictvím podokna řízení přístupu (IAM) pro digitální vlákna Azure v Azure Portal (viz [*Přidání nebo odebrání přiřazení rolí pomocí Azure RBAC a Azure Portal*](../role-based-access-control/role-assignments-portal.md))
* Přidání nebo odebrání role přes příkazy rozhraní příkazového řádku

Podrobnější pokyny k tomu, jak to provést, najdete v kurzu o digitálních Zdvojeních Azure [*: připojení kompletního řešení*](tutorial-end-to-end.md).

### <a name="permission-scopes"></a>Obory oprávnění

Než do objektu zabezpečení přiřadíte roli Azure, určete rozsah přístupu, který má objekt zabezpečení mít. Osvědčené postupy určují, že je nejvhodnější udělit jenom nejužšího možného rozsahu.

Následující seznam popisuje úrovně, na kterých můžete přistoupit k prostředkům v oblasti digitálních vláken Azure.
* Modely: akce pro tento prostředek určují kontrolu nad [modely](concepts-models.md) nahranými v digitálních činnostech Azure.
* Graf digitálního vyzdvojení dotazů: akce pro tento prostředek určují schopnost spouštět [operace dotazů](concepts-query-language.md) u digitálních vláken v grafu digitálních vláken Azure.
* Digitální vlákna: akce pro tento prostředek poskytují kontrolu nad operacemi CRUD u [digitálních vláken](concepts-twins-graph.md) ve dvojitých grafech.
* Digitální zdvojený vztah: akce pro tento prostředek definují kontrolu nad operacemi CRUD u [vztahů](concepts-twins-graph.md) mezi digitálními prvky v grafu s dvojitou závislostí.
* Trasa události: akce pro tento prostředek určují oprávnění k [Směrování událostí](concepts-route-events.md) z digitálních vláken Azure do služby koncového bodu, jako [je centrum událostí](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)nebo [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Řešení potíží s oprávněními

Pokud se uživatel pokusí provést akci, kterou role nepovoluje, může se zobrazit chyba z čtení žádosti o službu `403 (Forbidden)` . Další informace a postup pro řešení potíží najdete v tématu [*řešení potíží: žádost o digitální vlákna Azure se nezdařila se stavem: 403 (zakázáno)*](troubleshoot-error-403.md).

## <a name="encryption-of-data-at-rest"></a>Šifrování dat v klidovém umístění

Digitální vlákna Azure zajišťuje Šifrování neaktivních a přenosových dat, která se zapisují v našich datových centrech, a při přístupu k nim dešifruje. K tomuto šifrování dochází pomocí spravovaného šifrovacího klíče společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

* Podívejte se na tyto koncepty v tématu [*Postup: nastavení instance a ověřování*](how-to-set-up-instance-portal.md).

* Informace o tom, jak s těmito koncepty pracovat z klientského kódu aplikace v tématu [*Postupy: psaní ověřovacího kódu aplikace*](how-to-authenticate-client.md).

* Přečtěte si další informace o [RBAC pro Azure](../role-based-access-control/overview.md).
