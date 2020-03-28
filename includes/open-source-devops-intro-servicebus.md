---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 04/22/2019
ms.author: tarcher
ms.openlocfilehash: eb96027351cf244e9cd4404f702544411130db5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "67175697"
---
[Azure Service Bus](/azure/service-bus-messaging/service-bus-messaging-overview) je zprostředkovatel [zpráv podnikové integrace.](https://azure.microsoft.com/product-categories/integration/) Service bus podporuje dva typy komunikace: fronty a témata. 

Fronty podporují asynchronní komunikaci mezi aplikacemi. Aplikace odesílá zprávy do fronty, která ukládá zprávy. Přijímající aplikace se pak připojí a přečte zprávy z fronty.

Témata podporují vzor publikování a odběru, který umožňuje vztah 1:N mezi původcem zprávy a příjemcem zprávy.