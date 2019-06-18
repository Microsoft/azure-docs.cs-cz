---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175697"
---
[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) je podniková [integrace](https://azure.microsoft.com/product-categories/integration/) zprostředkovatele zpráv. Service bus podporuje dva typy komunikace: fronty a témata. 

Fronty podporují asynchronní komunikaci mezi aplikacemi. Aplikace odešle zprávy do fronty, která ukládá zprávy. Přijímající aplikaci pak připojí k a přečte zprávy z fronty.

Témata podporují publikování – vzorec, který umožňuje vztah jeden mnoho mezi původcem zprávy a zpráv receiver(s) odběru.