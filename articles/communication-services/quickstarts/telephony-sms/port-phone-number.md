---
title: Rychlý Start – port a telefonní číslo do komunikačních služeb Azure
description: Informace o tom, jak přenést telefonní číslo do prostředku služby Communication Services
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/20/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: b4357b8d4fe1d7184fc2dcfab2008dc6e0f334fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729919"
---
# <a name="quickstart-port-a-phone-number"></a>Rychlý Start: port a telefonní číslo

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Začněte s komunikačními službami Azure prostřednictvím přenosu telefonního čísla do svého prostředku služby Azure Communication Services. Bezplatná a geografická čísla založená na USA mají nárok na přenos. Další informace o typech telefonních čísel najdete v [Koncepční dokumentaci k telefonnímu](../../concepts/telephony-sms/plan-solution.md)číslu.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Aktivní prostředek služby Communication Services.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Shromážděte podrobnosti o prostředcích Azure.

Před zahájením žádosti o port přejděte na Azure Portal a vyberte svůj prostředek služby Communications. Po `Overview` výběru podokna klikněte na `JSON View` odkaz v pravém horním rohu:

:::image type="content" source="./media/number-port/json-view.png" alt-text="Snímek obrazovky znázorňující výběr zobrazení JSON":::

Poznamenejte si **ID Azure** prostředku a **ID neměnného prostředku**:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Snímek obrazovky znázorňující výběr vlastností JSON":::

## <a name="initiate-the-port-request"></a>Iniciování žádosti o port

Bezplatná a geografická čísla založená na USA mají nárok na přenos. K odeslání žádosti o port použijte jednu z následujících forem:

- Pro čísla bez poplatků: [požadavek na číslo bezplatného čísla portu](https://aka.ms/acs-port-form-tollfree)
- Geografické čísla na základě [požadavku na číslo portu USA: geografické číslo](https://aka.ms/acs-port-form-geographic)

Po dokončení odešlete dokončený formulář žádosti o port do acsporting@microsoft.com . Ujistěte se prosím, že řádek předmětu e-mailu začíná textem "ACS Port-In Request".

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak:

> [!div class="checklist"]
> * Získání metadat prostředků komunikačních služeb
> * Odeslat žádost o port vašeho telefonního čísla

> [!div class="nextstepaction"]
> [Odeslat SMS](../telephony-sms/send.md) 
>  [Začínáme s voláním](../voice-video-calling/getting-started-with-calling.md)
