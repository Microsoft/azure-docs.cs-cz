---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90946964"
---
## <a name="object-model"></a>Objektový model

Tokeny přístupu uživatele umožňují autorizovat uživatele vaší aplikace, aby se připojovali přímo k vašemu prostředku služby Azure Communication Services. Tyto tokeny vygenerujete na serveru, předáte je zpátky do klientské aplikace a pak je použijete k inicializaci klientských knihoven komunikačních služeb. Abyste to dosáhli, měli byste vytvořit koncový bod důvěryhodné služby, který dokáže ověřit uživatele, a vydávat tokeny přístupu uživatele. Tato služba by měla udržovat trvalé mapování mezi identitami uživatelů vaší aplikace a uživateli komunikačních služeb Azure.

Třída CommunicationIdentityClient poskytuje všechny funkce související s uživatelem a přístupovým tokenem. Vytvoří se jeho instance s připojovacím řetězcem a pak ho použije ke správě uživatelů a k vydávání přístupových tokenů.

> [!WARNING]
> Tokeny jsou citlivá data, protože udělují přístup k prostředkům uživatele. Proto je důležité chránit tokeny před ohrožením. Měli byste vytvořit koncový bod důvěryhodné služby, který u autorizovaných uživatelů vaší aplikace vydá jenom přístupové tokeny. Pokud přiřadíte tokeny přístupu uživatele do mezipaměti pro záložní úložiště, důrazně doporučujeme použít šifrování.

### <a name="access-token-scopes"></a>Obory přístupového tokenu

Obory umožňují určit přesnou funkci služby Azure Communications Services, kterou bude token přístupu uživatele moci autorizovat. Obory se aplikují na jednotlivé tokeny přístupu uživatelů. Komunikační služby Azure podporují následující obory pro tokeny přístupu uživatele:

| Název   | Popis                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | Udělí možnost zúčastnit se chatu.                                         |
| `VoIP` | Uděluje možnost vytvářet a přijímat volání VOIP pomocí volání klientské knihovny * |
| `Pstn` | Udělí možnost vytvořit volání do veřejné sítě pomocí volání klientské knihovny *           |

\* Funkce ještě není podporována a bude brzy k dispozici.

Chcete-li odebrat možnost uživatele pro přístup k určitým konkrétním funkcím, měli byste nejprve [odvolat všechny existující přístupové tokeny](#revoke-user-access-tokens) , které mohou obsahovat nepotřebné obory, před vydáním nového tokenu s více omezenými množinami oborů.
