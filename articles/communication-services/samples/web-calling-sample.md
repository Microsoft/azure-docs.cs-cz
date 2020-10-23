---
title: Komunikační služby Azure – ukázka volání webu
titleSuffix: An Azure Communication Services sample
description: Seznamte se s ukázkami webového volání komunikačních služeb
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e6fc3441fac5fe037e9a268d26012761d1fece70
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92463249"
---
# <a name="get-started-with-the-web-calling-sample"></a>Začínáme s ukázkou webového volání

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [Tato ukázka je k dispozici na GitHubu.](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)..

**Ukázka webového volání** služby Azure Communications ukazuje, jak lze použít komunikační služby volající klientskou knihovnu k sestavení volání pomocí JavaScriptu.

V tomto rychlém startu se dozvíte, jak ukázka funguje před spuštěním ukázky na místním počítači. Pak nasadíme ukázku do Azure s využitím vlastních prostředků služby Azure Communication Services.

## <a name="overview"></a>Přehled

Ukázka webového volání je webová aplikace, která slouží jako podrobný návod k různým funkcím poskytovaným klientským knihovnou webového volání komunikačních služeb. 

Tato ukázka byla sestavena pro vývojáře a usnadňuje vám práci s komunikačními službami. Jeho uživatelské rozhraní je rozdělené do několika oddílů, z nichž každý obsahuje tlačítko "Zobrazit kód", které umožňuje zkopírovat kód přímo z prohlížeče do vlastní aplikace komunikační služby.

Když je na vašem počítači [Ukázková webová volání](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) , zobrazí se následující cílová stránka:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Kurz pro webové volání 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Kurz pro webové volání 1" lightbox="./media/web-calling-tutorial-page-2.png":::


## <a name="user-provisioning-and-sdk-initialization"></a>Zřizování uživatelů a inicializace sady SDK 

Chcete-li začít používat ukázku, zadejte připojovací řetězec z [prostředku komunikačních služeb](../quickstarts/create-communication-resource.md) do `config.json` . Tato možnost se použije ke zřízení [přístupového tokenu uživatele](../concepts/authentication.md) , aby bylo možné inicializovat volání sady SDK.

Do vstupu identity uživatele zadejte svůj vlastní osobní identifikátor. Pokud zde není nic uvedeno, bude vygenerována náhodná identita uživatele. 

Kliknutím na "zřizování uživatele a inicializaci sady SDK" můžete sadu SDK inicializovat pomocí tokenu zřízeného službou zřizování back-end tokenu. Tato služba back-end je v systému `/project/webpack.config.js` .

Klikněte na tlačítko Zobrazit kód, abyste viděli vzorový kód, který můžete použít ve vašem vlastním řešení.

Po inicializaci sady SDK by se měla zobrazit následující:

:::image type="content" source="./media/user-provisioning.png" alt-text="Kurz pro webové volání 1" lightbox="./media/user-provisioning.png":::

Teď jste připraveni začít umísťovat volání pomocí prostředku komunikačních služeb!

## <a name="placing-and-receiving-calls"></a>Umístění a příjem volání

Sada SDK pro volání webu komunikačních služeb umožňuje **1:1**, **1: N**a volání **skupiny** .

Pro 1:1 nebo 1: N odchozí volání můžete zadat více identit uživatelů komunikačních služeb pro volání pomocí hodnot oddělených čárkami. Můžete také zadat tradiční telefonní čísla (PSTN), která budou volána pomocí hodnot oddělených čárkami. 

Při volání telefonních čísel PSTN zadejte alternativní ID volajícího. Kliknutím na tlačítko "umístit volání" umístěte odchozí volání:

:::image type="content" source="./media/place-a-call.png" alt-text="Kurz pro webové volání 1" lightbox="./media/place-a-call.png":::

Pokud se chcete připojit k volání skupiny, zadejte identifikátor GUID, který identifikuje volání, a klikněte na tlačítko "připojit skupinu":

:::image type="content" source="./media/join-a-group-call.png" alt-text="Kurz pro webové volání 1" lightbox="./media/join-a-group-call.png":::

Klikněte na tlačítko Zobrazit kód, abyste viděli vzorový kód pro umístění volání, přijímání volání a spojování volání skupin.

Aktivní volání vypadá takto:

:::image type="content" source="./media/group-call.png" alt-text="Kurz pro webové volání 1" lightbox="./media/group-call.png":::

Tato ukázka také poskytuje fragmenty kódu pro následující funkce:

  - Kliknutím na ikonu videa můžete zapnout/vypnout videokameru.
  - Kliknutím na ikonu mikrofonu můžete zapnout/vypnout mikrofon.
  - Kliknutím na ikonu přehrávání podržení nebo odblokování volání
  - Kliknutím na ikonu obrazovky spustíte nebo zastavíte sdílení obrazovky.
  - Kliknutím na ikonu osoby můžete přidat účastníka do volání.
  - Kliknutím na odebrat účastníka v seznamu členů účastníka odeberete konkrétního účastníka z tohoto volání.


## <a name="next-steps"></a>Další kroky

>[!div class="nextstepaction"] 
>[Stažení ukázky z GitHubu](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Další informace najdete v následujících článcích:

- Seznamte se s [použitím volání klientské knihovny](../quickstarts/voice-video-calling/calling-client-samples.md)
- Další informace o [volání funkce](../concepts/voice-video-calling/about-call-types.md)
- Kontrola [referenčních dokumentů rozhraní API](https://docs.microsoft.com/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js)

## <a name="additional-reading"></a>Další materiály ke čtení

- [GitHub pro komunikaci Azure](https://github.com/Azure/communication) – další příklady a informace najdete na oficiální stránce GitHubu.
- [Redux](https://redux.js.org/) – Správa stavu na straně klienta
- [FluentUI](https://aka.ms/fluent-ui) – knihovna rozhraní založená na Microsoftu
- [Reagovat](https://reactjs.org/) – knihovna pro sestavování uživatelských rozhraní
- [ASP.NET Core](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core?view=aspnetcore-3.1&preserve-view=true) – rozhraní pro vytváření webových aplikací
