---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174808"
---
Tato ukázka zahrnuje použití služby [Twilio](https://www.twilio.com/) k posílání zpráv SMS na mobilní telefon. Azure Functions již podporuje Twilio prostřednictvím [vazby Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio)a ukázka tuto funkci používá.

První věc, kterou potřebujete, je účet Twilio. Můžete si ho vytvořit na adrese https://www.twilio.com/try-twilio . Jakmile budete mít účet, přidejte do aplikace Function App následující tři **nastavení aplikace** .

| Název nastavení aplikace | Popis hodnoty |
| - | - |
| **TwilioAccountSid**  | Identifikátor SID účtu Twilio |
| **TwilioAuthToken**   | Token ověřování pro účet Twilio |
| **TwilioPhoneNumber** | Telefonní číslo přidružené k vašemu účtu Twilio. Slouží k posílání zpráv SMS. |