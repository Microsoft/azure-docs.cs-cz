---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: f3da6fd75ac485a3e22a428934928795f1feae28
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487911"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Skupina komunikačních služeb Azure, která **volá Hero Sample** , ukazuje, jak lze pomocí komunikačních služeb volání webové klientské knihovny vytvořit skupinu volání.

V tomto rychlém startu se dozvíte, jak ukázka funguje před spuštěním ukázky na místním počítači. Pak nasadíme ukázku do Azure s využitím vlastních prostředků služby Azure Communication Services.

## <a name="download-code"></a>Stažení kódu

Vyhledá finální kód pro tento rychlý Start na [GitHubu](https://github.com/Azure-Samples/communication-services-web-calling-hero).

## <a name="overview"></a>Přehled

Ukázka obsahuje aplikaci na straně klienta i aplikaci na straně serveru. **Aplikace na straně klienta** je webová aplikace reagující na Redux, která používá architekturu uživatelského rozhraní Fluent společnosti Microsoft. Tato aplikace posílá požadavky na ASP.NET Core **aplikace na straně serveru** , která pomáhá aplikaci na straně klienta připojit se k Azure.

Ukázka vypadá takto:

:::image type="content" source="../media/calling/landing-page.png" alt-text="Snímek obrazovky znázorňující cílovou stránku ukázkové aplikace":::

Po stisknutí tlačítka spustit volání načte webová aplikace token přístupu uživatele z aplikace na straně serveru. Tento token se pak použije k připojení klientské aplikace ke komunikačním službám Azure. Po načtení tokenu budete vyzváni k zadání fotoaparátu a mikrofonu, který chcete použít. Pomocí ovládacích prvků přepínací tlačítko budete moct zařízení zakázat nebo povolit.

:::image type="content" source="../media/calling/pre-call.png" alt-text="Snímek obrazovky znázorňující obrazovku před voláním ukázkové aplikace":::

Jakmile nakonfigurujete své zobrazované jméno a zařízení, můžete se připojit k relaci volání. Pak uvidíte hlavní plátno volání, kde se nachází základní volající prostředí.

:::image type="content" source="../media/calling/main-app.png" alt-text="Snímek obrazovky znázorňující hlavní obrazovku ukázkové aplikace":::

Součásti hlavní volající obrazovky:

- **Galerie médií**: hlavní fáze, kde se zobrazují účastníci. Pokud má účastník zapnutou kameru, zobrazí se zde jeho video kanál. Každý účastník má jednotlivou dlaždici, která zobrazuje zobrazované jméno a datový proud videa (když je nějaký).
- **Záhlaví**: Zde jsou umístěny primární ovládací prvky volání pro přepínání nastavení a bočního panelu účastníka, zapnutí a vypnutí videa, sdílení obrazovky a opuštění volání.
- **Boční panel**: tady jsou informace o účastnících se účastníků a nastavení při přepínání pomocí ovládacích prvků v záhlaví. Komponenta se může v pravém horním rohu vynechává pomocí ' X '. Na bočním panelu účastníci se zobrazí seznam účastníků a odkaz pro pozvání více uživatelů k chatu. Postranní panel nastavení umožňuje konfigurovat nastavení mikrofonu a kamery.

Níže najdete další informace o požadavcích a krocích pro nastavení ukázky.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. Podrobnosti najdete v tématu [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
- [Node.js (12.18.4 a vyšší)](https://nodejs.org/en/download/)
- [Visual Studio (2019 a novější)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Ujistěte se, že je nainstalována verze, která odpovídá vaší instanci sady Visual studio, 32 vs 64 bitů)
- Prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../quickstarts/create-communication-resource.md). Pro tento rychlý Start budete muset zaznamenat **připojovací řetězec** prostředků.

## <a name="locally-deploy-the-service--client-applications"></a>Lokálně nasazovat klientské aplikace & služby

Vzor volání skupiny je v podstatě dvě aplikace: ClientApp a aplikace Service.NET.

Když chceme místně nasadit, musíme spustit obě aplikace. Když se serverová aplikace navštíví z prohlížeče, použije se pro uživatelské prostředí místně nasazené ClientApp.

Ukázku můžete otestovat místně tak, že otevřete několik relací prohlížeče s adresou URL volání, abyste mohli simulovat volání více uživatelů.

### <a name="before-running-the-sample-for-the-first-time"></a>Před prvním spuštěním ukázky

1. Otevřete instanci PowerShellu, Windows Terminal, Command Prompt nebo ekvivalentní a přejděte do adresáře, do kterého chcete ukázku naklonovat.
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. Získat `Connection String` z Azure Portal. Další informace o připojovacích řetězcích najdete v tématu [vytvoření prostředků komunikace Azure](../../quickstarts/create-communication-resource.md).
4. Po získání přidejte do `Connection String` souboru **Called/appsetting.jsv** souboru, který se nachází ve složce služby .NET, připojovací řetězec. Zadejte připojovací řetězec do proměnné: `ResourceConnectionString` .

### <a name="local-run"></a>Místní spuštění

1. Přejít na volající složku a otevřít `Calling.csproj` řešení v aplikaci Visual Studio.
2. Spusťte `Calling` projekt. Otevře se prohlížeč v `localhost:5001` .

## <a name="publish-the-sample-to-azure"></a>Publikování ukázky do Azure

1. Klikněte pravým tlačítkem na `Calling` projekt a vyberte publikovat.
2. Vytvořte nový publikační profil a vyberte své předplatné Azure.
3. Před publikováním přidejte připojovací řetězec pomocí `Edit App Service Settings` a `ResourceConnectionString` jako hodnotu zadejte připojovací řetězec (zkopírovaný z appsettings.json).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

>[!div class="nextstepaction"]
>[Stažení ukázky z GitHubu](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Další informace najdete v následujících článcích:

- Seznamte se s [použitím volání klientské knihovny](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Další informace o [volání funkce](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Další materiály ke čtení

- [Ukázky](./../overview.md) – další ukázky a příklady najdete na stránce s přehledem ukázek.
- [Redux](https://redux.js.org/) – Správa stavu na straně klienta
- [FluentUI](https://aka.ms/fluent-ui) – knihovna rozhraní založená na Microsoftu
- [Reagovat](https://reactjs.org/) – knihovna pro sestavování uživatelských rozhraní
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) – rozhraní pro vytváření webových aplikací
