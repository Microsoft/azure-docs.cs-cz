---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026804"
---
Tato ukázka zahrnuje použití služby [Twilio](https://www.twilio.com/) k posílání zpráv SMS na mobilní telefon. Azure Functions již podporuje Twilio prostřednictvím [vazby Twilio](../articles/azure-functions/functions-bindings-twilio.md)a ukázka tuto funkci používá.

První věc, kterou potřebujete, je účet Twilio. Můžete si ho vytvořit na adrese https://www.twilio.com/try-twilio . Jakmile budete mít účet, přidejte do aplikace Function App následující tři **nastavení aplikace** .

| Název nastavení aplikace | Popis hodnoty |
| - | - |
| **TwilioAccountSid**  | Identifikátor SID účtu Twilio |
| **TwilioAuthToken**   | Token ověřování pro účet Twilio |
| **TwilioPhoneNumber** | Telefonní číslo přidružené k vašemu účtu Twilio. Slouží k posílání zpráv SMS. |