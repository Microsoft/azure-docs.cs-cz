---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174808"
---
Tato ukázka zahrnuje použití služby [Twilio](https://www.twilio.com/) k odesílání sms zpráv na mobilní telefon. Azure Functions už má podporu pro Twilio prostřednictvím [vazby Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)a ukázka používá tuto funkci.

První věc, kterou potřebujete, je účet Twilio. Můžete vytvořit jeden https://www.twilio.com/try-twiliozdarma na . Až budete mít účet, přidejte do aplikace funkce následující tři **nastavení aplikace.**

| Název nastavení aplikace | Popis hodnoty |
| - | - |
| **TwilioAccountSid**  | SID pro váš účet Twilio |
| **Token TwilioAuth**   | Auth token pro váš účet Twilio |
| **Číslo twiliotelefonu** | Telefonní číslo spojené s vaším účtem Twilio. Používá se k odesílání SMS zpráv. |