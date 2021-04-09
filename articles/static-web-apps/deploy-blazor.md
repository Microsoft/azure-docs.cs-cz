---
title: 'Kurz: Vytvoření statické webové aplikace pomocí Blazor ve statickém Web Apps Azure'
description: Naučte se vytvářet web Azure static Web Apps pomocí Blazor.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 0086f7f68fd05d6925d19c7ab457fbc125e36be4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96350224"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Kurz: Vytvoření statické webové aplikace pomocí Blazor ve statickém Web Apps Azure

Služba Azure static Web Apps publikuje web do produkčního prostředí tím, že vytváří aplikace z úložiště GitHub. V tomto kurzu nasadíte webovou aplikaci do statických webových aplikací Azure pomocí Azure Portal.

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Požadavky

- Účet [GitHub](https://github.com)
- Účet [Azure](https://portal.azure.com)

## <a name="application-overview"></a>Přehled aplikace

Služba Azure static Web Apps umožňuje vytváření statických webových aplikací podporovaných back-endu bez serveru. Následující kurz ukazuje, jak nasadit webovou aplikaci v jazyce C# Blazor, která vrací data o počasí.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="Dokončení aplikace Blazor":::

Aplikace vybraná v tomto kurzu se skládá ze tří různých projektů Visual studia:

- **API**: aplikace Azure Functions C#, která implementuje koncový bod rozhraní API, který poskytuje informace o počasí do statické aplikace. **WeatherForecastFunction** vrací pole `WeatherForecast` objektů.

- **Klient**: projekt webového sestavení front-endu Blazor. Je implementována [záložní trasa](#fallback-route) , která zajistí, že všechny trasy budou obsluhovat soubor _index.html_ .

- **Shared**: uchovává společné třídy, na které odkazuje rozhraní API, i klientské projekty, které umožňují tok dat z koncového bodu rozhraní API do front-endové webové aplikace. [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs)Třída je sdílena mezi oběma aplikacemi.

Dohromady tyto projekty tvoří součásti, které vyžadují vytvoření webové aplikace v Blazor, která běží v prohlížeči, který podporuje back-end rozhraní API.

## <a name="fallback-route"></a>Záložní trasa

Aplikace zveřejňuje adresy URL jako _/Counter_ a _/fetchdata_ , které se mapují na konkrétní trasy aplikace. Vzhledem k tomu, že je tato aplikace implementována jako jediná stránková aplikace, je pro každou trasu obsluhován soubor _index.html_ . Aby se zajistilo, že požadavek na jakoukoliv cestu vrátí _index.html_ , implementuje se [záložní trasa](./routes.md#fallback-routes) do _routes.jsv_ souboru, který se nachází ve složce _wwwroot_ klientského projektu.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Výše uvedená konfigurace zajišťuje, že požadavky na jakoukoli trasu v aplikaci vrátí stránku _index.html_ .

## <a name="create-a-repository"></a>Vytvořte úložiště

V tomto článku se používá úložiště šablon GitHubu, které vám usnadní začít. Šablona obsahuje úvodní aplikaci nasazenou do Azure static Web Apps.

1. Ujistěte se, že jste přihlášeni k GitHubu, a přejděte do následujícího umístění a vytvořte nové úložiště:
    - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Pojmenování úložiště **My-First-static-blazor-App**

## <a name="create-a-static-web-app"></a>Vytvoření statické webové aplikace

Teď, když je úložiště vytvořené, vytvořte ze Azure Portal statickou webovou aplikaci.

1. Přejít na [Azure Portal](https://portal.azure.com)
1. Vyberte **vytvořit prostředek** .
1. Vyhledejte **Static Web Apps**.
1. Vybrat **statické Web Apps (Náhled)**
1. Vyberte **Vytvořit**.

V části _základy_ začněte konfigurací nové aplikace a propojením s úložištěm GitHub.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Karta základy":::

1. Výběr vašeho _předplatného Azure_
1. Vyberte nebo vytvořte novou _skupinu prostředků_ .
1. Pojmenování aplikace **My-First-static-blazor-App**
    - Platné znaky jsou `a-z` (bez rozlišování malých a velkých písmen), `0-9` a `-`.
1. Vyberte _oblast_ , která je pro vás nejblíže
1. Výběr **bezplatné** _SKU_
1. Vyberte tlačítko **Přihlásit se pomocí GitHubu** a proveďte ověření pomocí GitHubu.

Až se přihlásíte pomocí GitHubu, zadejte informace o úložišti.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Podrobnosti úložiště":::

1. Vybrat upřednostňovanou _organizaci_
1. Z rozevíracího seznamu _úložiště_ vyberte **My-First-static-blazor-App** .
1. V rozevíracím seznamu _větev_ vyberte **Main (Hlavní** ).

    Pokud nevidíte žádná úložiště, možná budete muset autorizovat Azure static Web Apps v GitHubu. Přejděte do úložiště GitHub a přejděte na **nastavení > aplikace > autorizovaných aplikací OAuth**, vyberte **statické Web Apps Azure** a pak vyberte **udělit**. V případě úložišť organizace musíte být vlastníkem organizace, abyste udělili oprávnění.

1. V části _Podrobnosti o sestavení_ přidejte podrobnosti o konfiguraci specifické pro Blazor.

    - V rozevíracím seznamu _přednastavení sestavení_ vyberte **Blazor** a ponechte všechny výchozí hodnoty.

1. Vyberte **Zkontrolovat a vytvořit**.

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="Tlačítko pro vytvoření Revize":::

1. Vyberte **Vytvořit**.

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="Tlačítko Create (Vytvořit)":::

1. Vyberte **Přejít k prostředku**.

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Tlačítko Go to resource (Přejít k prostředku)":::

## <a name="view-the-website"></a>Zobrazit web

Existují dva aspekty nasazení statické aplikace. Při prvním se zřídí základní prostředky Azure, které tvoří vaši aplikaci. Druhým je právě pracovní postup GitHub Actions, který vytvoří a publikuje vaši aplikaci.

Předtím, než budete moci přejít na novou statickou lokalitu, musí být sestavení nasazení nejprve dokončeno.

V okně Přehled statického Web Apps se zobrazuje řada odkazů, které vám pomůžou s webovou aplikací pracovat.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Okno s přehledem":::

1. Kliknutím na banner, který říkáte, _klikněte sem, abyste zkontrolovali stav svých akcí GitHubu_ , které se budou spouštět na základě vašeho úložiště. Jakmile ověříte, že je úloha nasazení dokončená, můžete přejít na web prostřednictvím vygenerované adresy URL.

2. Po dokončení pracovního postupu akcí GitHubu můžete kliknutím na odkaz _URL_ otevřít web na nové kartě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, můžete instanci statického Web Apps Azure odstranit pomocí následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com).
1. Hledání **My-First-static-blazor-App** z horního panelu hledání
1. Vyberte název aplikace.
1. Vybrat na tlačítku **Odstranit**
1. Kliknutím na **Ano** potvrďte akci odstranit.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ověřování a autorizace](./authentication-authorization.md)
