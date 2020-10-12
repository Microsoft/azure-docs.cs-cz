---
title: Ověřování doručování událostí do obslužných rutin událostí (Azure Event Grid)
description: Tento článek popisuje různé způsoby ověřování doručování do obslužných rutin událostí v Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: abe16c9598c8c10caa832150aafac997dd7f1624
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87460639"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Ověřování doručování událostí do obslužných rutin událostí (Azure Event Grid)
Tento článek poskytuje informace o ověřování doručování událostí do obslužných rutin událostí. Také ukazuje, jak zabezpečit koncové body Webhooku, které se používají k přijímání událostí z Event Grid pomocí Azure Active Directory (Azure AD) nebo sdíleného tajného klíče.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Použití identit přiřazených systémem pro doručování událostí
Pro téma nebo doménu můžete povolit spravovanou identitu přiřazenou systémem a tuto identitu použít k přeposílání událostí do podporovaných cílů, jako jsou Service Bus fronty a témata, centra událostí a účty úložiště.

Postupujte takto: 

1. Vytvořte téma nebo doménu s identitou přiřazenou systémem, nebo aktualizujte existující téma nebo doménu, aby bylo možné identitu povolit. 
1. Přidejte identitu do příslušné role (například Service Bus odesílatel dat) v cíli (například Service Bus Queue).
1. Při vytváření odběrů událostí povolte použití identity k doručování událostí do cíle. 

Podrobné pokyny najdete v tématu [doručování událostí se spravovanou identitou](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Ověřování doručování událostí do koncových bodů Webhooku
Následující části popisují, jak ověřit doručování událostí do koncových bodů Webhooku. Je nutné použít ověřovací mechanismus handshake bez ohledu na použitou metodu. Podrobnosti najdete v tématu věnovaném [doručování událostí Webhooku](webhook-event-delivery.md) . 


### <a name="using-azure-active-directory-azure-ad"></a>Použití Azure Active Directory (Azure AD)
Koncový bod Webhooku, který se používá k přijímání událostí z Event Grid, můžete zabezpečit pomocí Azure AD. Budete muset vytvořit aplikaci Azure AD, vytvořit roli a instanční objekt v aplikaci s autorizací Event Grid a nakonfigurovat odběr událostí tak, aby používal aplikaci Azure AD. Naučte se, jak [nakonfigurovat Azure Active Directory s](secure-webhook-delivery.md)využitím Event Grid.

### <a name="using-client-secret-as-a-query-parameter"></a>Použití tajného klíče klienta jako parametru dotazu
Koncový bod Webhooku můžete také zabezpečit přidáním parametrů dotazu do cílové adresy URL Webhooku zadané jako součást vytvoření odběru události. Nastavte jeden z parametrů dotazu na tajný klíč klienta, jako je [přístupový token](https://en.wikipedia.org/wiki/Access_token) nebo sdílený tajný klíč. Služba Event Grid zahrnuje všechny parametry dotazu v každé žádosti o doručení události Webhooku. Služba Webhooku může načíst a ověřit tajný klíč. Pokud se tajný klíč klienta aktualizuje, musí se taky aktualizovat předplatné událostí. Aby nedocházelo k chybám při obnově v tajnosti, zajistěte, aby Webhook přijal staré i nové tajné klíče po omezené době před aktualizací odběru událostí novým tajným klíčem. 

Vzhledem k tomu, že parametry dotazu můžou obsahovat klientské tajné klíče, jsou zpracovávány s mimořádnou opatrností. Jsou uložené jako šifrované a nejsou k dispozici pro obsluhu služeb. Neprotokolují se jako součást protokolů/trasování služby. Při načítání vlastností odběru událostí nejsou ve výchozím nastavení vráceny cílové parametry dotazu. Příklad: parametr [--include-Full-Endpoint-URL](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) se používá v rozhraní příkazového [řádku Azure CLI](/cli/azure?view=azure-cli-latest).

Další informace o doručování událostí do webhooků najdete v tématu [doručování událostí Webhooku](webhook-event-delivery.md) .

> [!IMPORTANT]
Azure Event Grid podporuje pouze koncové body Webhooku **protokolu HTTPS** . 


## <a name="next-steps"></a>Další kroky
Další informace o ověřování klientů, kteří publikují události do témat nebo domén, najdete v tématu [ověřování klientů publikování](security-authenticate-publishing-clients.md) . 
