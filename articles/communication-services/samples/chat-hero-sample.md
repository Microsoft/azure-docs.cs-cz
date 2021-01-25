---
title: Ukázka Hero v konverzaci skupin
titleSuffix: An Azure Communication Services sample overview
description: Přehled služby chat Hero Sample pomocí komunikačních služeb Azure, které vývojářům umožňují získat další informace o vnitřních pracovních způsobech ukázky a o tom, jak je upravit.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 80fdedb29f70c961431c987558a42d5fa46dfa7d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98760804"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>Začínáme s příkladem skupiny Hero chat

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!----
> [!WARNING]
> links to our Hero Sample repo need to be updated when the sample is publicly available.
---->

> [!IMPORTANT]
> [Tato ukázka je k dispozici na GitHubu.](https://github.com/Azure-Samples/communication-services-web-chat-hero)


**Ukázka konverzace Hero skupiny** služby Azure Communication Services ukazuje, jak lze použít knihovnu webového klienta chat komunikační služby k vytvoření skupinového volání.

V tomto rychlém startu se dozvíte, jak ukázka funguje před spuštěním ukázky na místním počítači. Pak nasadíme ukázku do Azure s využitím vlastních prostředků služby Azure Communication Services.


## <a name="overview"></a>Přehled

Ukázka obsahuje aplikaci na straně klienta i aplikaci na straně serveru. **Aplikace na straně klienta** je webová aplikace reagující na Redux, která používá architekturu uživatelského rozhraní Fluent společnosti Microsoft. Tato aplikace posílá požadavky na ASP.NET Core **aplikace na straně serveru** , která pomáhá aplikaci na straně klienta připojit se k Azure. 

Ukázka vypadá takto:

:::image type="content" source="./media/chat/landing-page.png" alt-text="Snímek obrazovky s úvodní stránkou ukázkové aplikace":::

Po stisknutí tlačítka Spustit chat načte webová aplikace token přístupu uživatele z aplikace na straně serveru. Tento token se pak použije k připojení klientské aplikace ke komunikačním službám Azure. Po načtení tokenu se zobrazí výzva k zadání vašeho jména a Emoji, která bude představovat chat. 

:::image type="content" source="./media/chat/pre-chat.png" alt-text="Snímek obrazovky s obrazovkou předběžného chatu aplikace":::

Jakmile nakonfigurujete své zobrazované jméno a Emoji, můžete se připojit k relaci chatu. Teď se zobrazí hlavní plátno pro chat, kde se nachází v základním prostředí chatu.

:::image type="content" source="./media/chat/main-app.png" alt-text="Snímek obrazovky znázorňující hlavní obrazovku ukázkové aplikace":::

Součásti hlavní obrazovky chatu:

- **Hlavní oblast chatu**: Toto je základní prostředí pro chat, kde mohou uživatelé odesílat a přijímat zprávy. Chcete-li odesílat zprávy, můžete použít vstupní oblast a stisknout klávesu ENTER (nebo použít tlačítko Odeslat). Přijaté zprávy chatu jsou zařazeny do kategorie odesílatelem se správným názvem a emoji. V oblasti chatu se zobrazí dva typy oznámení: 1) psaní oznámení, když uživatel zadá a 2) odesílá a čte oznámení pro zprávy.
- **Záhlaví**: uživateli se zobrazí název konverzačního vlákna a ovládací prvky pro přepnutí účastníků a nastavení a tlačítko opustit k ukončení relace chatu.
- **Boční panel**: v tomto případě se zobrazí účastníci a informace o nastavení při přepínání pomocí ovládacích prvků v záhlaví. Boční panel účastníci obsahuje seznam účastníků v chatu a odkaz pro pozvání účastníků do relace chatu. Postranní panel nastavení vám umožní nakonfigurovat název vlákna chatu. 

Níže najdete další informace o požadavcích a krocích pro nastavení ukázky.

## <a name="prerequisites"></a>Požadavky

- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (8.11.2 a vyšší)](https://nodejs.org/en/download/)
- [Visual Studio (2017 a novější)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Ujistěte se, že je nainstalována verze, která odpovídá vaší instanci sady Visual studio, 32 vs 64 bitů)
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../quickstarts/create-communication-resource.md). Pro tento rychlý Start budete muset zaznamenat **připojovací řetězec** prostředků.

## <a name="locally-deploying-the-service--client-app"></a>Místní nasazení klientské aplikace & služby

Ukázka konverzace s jedním vláknem je v podstatě dvě aplikace klienta a serveru.

Otevřete Visual Studio na chatu. csproj a spusťte v režimu ladění. tím se spustí front-end služba chatu. Když se serverová aplikace navštíví z prohlížeče, přesměruje provoz směrem do místně nasazené služby front-end chatu.

Ukázku můžete otestovat místně tak, že otevřete několik relací prohlížeče s adresou URL chatu pro simulaci konverzace s více uživateli.

## <a name="before-running-the-sample-for-the-first-time"></a>Před prvním spuštěním ukázky

1. Otevřete instanci PowerShellu, Windows Terminal, Command Prompt nebo ekvivalentní a přejděte do adresáře, do kterého chcete ukázku naklonovat.
2. `git clone https://github.com/Azure-Samples/communication-services-web-chat-hero.git`
3. Získat `Connection String` z Azure Portal. Další informace o připojovacích řetězcích najdete v tématu [vytvoření prostředků komunikace Azure](../quickstarts/create-communication-resource.md) .
4. Jakmile získáte `Connection String` připojovací řetězec, přidejte ho do souboru **chat nebo appsettings.js** , který se nachází ve složce chat. Zadejte připojovací řetězec do proměnné: `ResourceConnectionString` .

### <a name="local-run"></a>Místní spuštění

1. Přejít do složky chat a otevřít `Chat.csproj` řešení v aplikaci Visual Studio
2. Spusťte projekt. Prohlížeč se otevře v localhost: 5000.

#### <a name="troubleshooting"></a>Odstraňování potíží

- Řešení se nevytvoří, vyvolá chyby během instalace/buildu NPM.

   Vyčistit/znovu sestavit řešení C#

## <a name="publish-the-sample-to-azure"></a>Publikování ukázky do Azure

1. Klikněte pravým tlačítkem na `Chat` projekt a vyberte publikovat.
2. Vytvořte nový publikační profil a vyberte své předplatné Azure.
3. Před publikováním přidejte připojovací řetězec pomocí `Edit App Service Settings` a `ResourceConnectionString` jako hodnotu zadejte připojovací řetězec (zkopírovaný z appsettings.json).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Další kroky

>[!div class="nextstepaction"] 
>[Stažení ukázky z GitHubu](https://github.com/Azure-Samples/communication-services-web-chat-hero)

Další informace najdete v následujících článcích:

- Další informace o [konceptech chatu](../concepts/chat/concepts.md)
- Seznamte se s naší [klientskou knihovnou chatu](../concepts/chat/sdk-features.md)
- Kontrola ukázky [aplikace Contoso med](https://github.com/Azure-Samples/communication-services-contoso-med-app)

## <a name="additional-reading"></a>Další materiály ke čtení

- [GitHub pro komunikaci Azure](https://github.com/Azure/communication) – další příklady a informace najdete na oficiální stránce GitHubu.
- [Redux](https://redux.js.org/) – Správa stavu na straně klienta
- [FluentUI](https://aka.ms/fluent-ui) – knihovna rozhraní založená na Microsoftu
- [Reagovat](https://reactjs.org/) – knihovna pro sestavování uživatelských rozhraní
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) – rozhraní pro vytváření webových aplikací