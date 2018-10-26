---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134415"
---
Tento příklad zahrnuje použití [Twilio](https://www.twilio.com/) služby umožňující odesílání zpráv serveru SMS na mobilní telefon. Služba Azure Functions již obsahuje podporu pro Twilio prostřednictvím [Twilio vazby](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio), a ukázka používá tuto funkci.

První věc, co potřebujete, je účtu Twilio. Můžete si ho vytvořit zdarma na https://www.twilio.com/try-twilio. Jakmile budete mít účet, přidejte následující tři **nastavení aplikace** aplikaci function App.

| Název nastavení aplikace | Hodnota Popis |
| - | - |
| **TwilioAccountSid**  | Identifikátor SID účtu Twilio |
| **TwilioAuthToken**   | Ověřovací token účtu Twilio |
| **TwilioPhoneNumber** | Telefonní číslo přidružené k účtu Twilio. To se používá k odeslání serveru SMS zprávy. |