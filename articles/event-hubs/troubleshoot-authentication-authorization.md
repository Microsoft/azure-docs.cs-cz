---
title: Řešení potíží s ověřováním a autorizací – Azure Event Hubs
description: Tento článek poskytuje informace o řešení potíží s ověřováním a autorizací pomocí Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 368fd8efda1b828f99bc41da0743768989c1a601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329606"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Řešení potíží s ověřováním a autorizací – Azure Event Hubs
Článek [Poradce při](troubleshooting-guide.md) potížích s připojením poskytuje tipy pro řešení potíží s připojením pomocí Azure Event Hubs. Tento článek poskytuje tipy a doporučení pro řešení problémů s ověřováním a autorizací pomocí Azure Event Hubs. 

## <a name="if-you-are-using-azure-active-directory"></a>Pokud používáte Azure Active Directory
Pokud používáte Azure Active Directory (Azure AD) k ověřování a autorizaci se službou Azure Event Hubs, ověřte, že identita, která přistupuje k centru událostí, je členem správné **role Azure** v pravém **oboru prostředků** (skupina příjemců, centrum událostí, obor názvů, skupina prostředků nebo předplatné).

### <a name="azure-roles"></a>Role Azure
- [Vlastník dat Event Hubs Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) pro úplný přístup k prostředkům Event Hubs.
- [Odesilatel dat Azure Event Hubs](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) pro přístup pro odesílání.
- [Příjemce dat Event Hubs Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) pro přístup pro příjem.

Předdefinované role registru schématu najdete v tématu [role registru schématu](schema-registry-overview.md#azure-role-based-access-control).

### <a name="resource-scopes"></a>Obory prostředků
- **Skupina příjemců**: v tomto oboru se přiřazení role vztahuje pouze na tuto entitu. V současné době Azure Portal nepodporuje přiřazení role Azure k objektu zabezpečení na této úrovni. 
- **Centrum událostí**: přiřazení role se vztahuje k entitě centra událostí a skupině příjemců.
- **Obor názvů**: přiřazování rolí zahrnuje celou topologii Event Hubs pod oborem názvů a do skupiny uživatelů, které jsou k ní přidružené.
- **Skupina prostředků**: přiřazení role se vztahuje na všechny prostředky Event Hubs v rámci skupiny prostředků.
- **Předplatné**: přiřazení role se vztahuje na všechny prostředky Event Hubs ve všech skupinách prostředků v rámci předplatného.

Další informace najdete v následujících článcích:

- [Ověření aplikace s Azure Active Directory pro přístup k prostředkům Event Hubs](authenticate-application.md)
- [Autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Pokud používáte sdílené přístupové podpisy (SAS)
Pokud používáte [SAS](authenticate-shared-access-signature.md), postupujte takto: 

- Ujistěte se, že je klíč SAS, který používáte, správný. V takovém případě použijte správný klíč SAS.
- Ověřte, zda má klíč správná oprávnění (odeslání, přijetí nebo Správa). V takovém případě použijte klíč, který má oprávnění, které potřebujete. 
- Ověřte, zda platnost klíče vypršela. Doporučujeme, abyste před vypršením platnosti obnovili SAS. Pokud mezi klientem a Event Hubsmi uzly služby dojde k časovému zkosení, může platnost ověřovacího tokenu vypršet, než ho klient bude realizovat. Aktuální účty implementace jsou po hodinách nakloněné po dobu 5 minut. to znamená, že klient obnoví token 5 minut, než vyprší jeho platnost. Proto, pokud je časový posun větší než 5 minut, může klient sledovat občasné chyby ověřování.
- Pokud je **čas spuštění SAS** nastavený na **nyní**, může se při prvních několika minutách zobrazit občasné selhání kvůli posunu hodin (rozdíly v aktuálním čase na různých počítačích). Nastavte čas spuštění aspoň na 15 minut v minulosti nebo ho nenastavujte vůbec. Totéž platí i pro čas vypršení platnosti. 

Další informace najdete v následujících článcích: 

- [Ověřte pomocí sdílených přístupových podpisů (SAS)](authenticate-shared-access-signature.md). 
- [Autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Další kroky

Viz následující články:

* [Řešení potíží s připojením](troubleshooting-guide.md)
