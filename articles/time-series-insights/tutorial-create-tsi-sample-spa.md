---
title: 'Kurz: Vytvoření webové aplikace Azure Time Series Insights jednostránkového stránkování | Microsoft Docs'
description: Naučte se vytvořit jednostránkovou webovou aplikaci, která se dotazuje a vykresluje data z Azure Time Series Insightsho prostředí.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 031e8074585426584d7ef63a103c9c2b4d90e6c3
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194209"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Kurz: Vytvoření jednostránkové webové aplikace Azure Time Series Insights

Tento kurz vás provede procesem vytvoření webové aplikace s jednou stránkou (SPA) pro přístup k datům Azure Time Series Insights.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Návrh aplikace
> * Postup registrace aplikace pomocí Azure Active Directory (Azure AD)
> * Postup pro sestavení, publikování a testování webové aplikace

> [!NOTE]
> * Zdrojový kód pro tento kurz je k dispozici [](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial)na GitHubu.
> * Ukázková [aplikace Time Series Insights klienta](https://insights.timeseries.azure.com/clientsample) je hostována k zobrazení dokončené aplikace používané v tomto kurzu.

Zaregistrujte si [bezplatné předplatné Azure](https://azure.microsoft.com/free/) , pokud ho ještě nemáte.

## <a name="prerequisites"></a>Požadavky

* Bezplatnou kopii sady Visual Studio. Začněte tím, že si stáhnete [verze komunity 2017 nebo 2019](https://www.visualstudio.com/downloads/) .

* Komponenty nástrojů IIS Express, Nasazení webu a Azure Cloud Services Core Tools pro Visual Studio. Přidejte komponenty úpravou instalace sady Visual Studio.

## <a name="understand-application-design"></a>Pochopení návrhu aplikace

Ukázka Time Series Insights SPA je základem pro návrh a kód použitý v tomto kurzu. Kód používá klientskou knihovnu Time Series Insights JavaScript. Klientská knihovna Time Series Insights poskytuje abstrakci pro dvě hlavní kategorie rozhraní API:

- **Metody obálky pro volání rozhraní API pro Time Series Insights dotazy**: Rozhraní REST API můžete použít k dotazování na data Time Series Insights pomocí výrazů založených na JSON. Metody jsou uspořádány pod oborem názvů TsiClient. Server knihovny.

- **Metody pro vytvoření a naplnění několika typů ovládacích prvků pro vytváření grafů**: Metody, které můžete použít k vizualizaci Time Series Insights dat na webové stránce. Metody jsou uspořádány pod oborem názvů TsiClient. ux knihovny.

Tento kurz používá také data z Time Series Insightsho prostředí ukázkové aplikace. Podrobnosti o struktuře ukázkové aplikace Time Series Insights a způsobu použití klientské knihovny Time Series Insights naleznete v kurzu [zkoumání Azure Time Series Insights klientské knihovny JavaScriptu](tutorial-explore-js-client-lib.md).

## <a name="register-with-azure-ad"></a>Registrace ve službě Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>Sestavování a publikování

1. Vytvořte adresář pro uložení souborů projektu vaší aplikace. Pak použijte následující adresy URL. Klikněte pravým tlačítkem myši na nezpracovaný odkaz v pravém horním rohu stránky a pak vyberte **Uložit jako** a uložte soubory do adresáře projektu.

   - [*index. html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML a JavaScript pro stránku
   - [*sampleStyles. CSS*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): Šablona stylů CSS

   > [!NOTE]
   > V závislosti na prohlížeči může být nutné před uložením souboru změnit přípony souborů na. html nebo. CSS.

1. Ověřte, zda jsou požadované součásti nainstalovány v aplikaci Visual Studio. Musí být nainstalované komponenty nástrojů IIS Express, Nasazení webu a Azure Cloud Services Core Tools pro Visual Studio.

    [![Visual Studio – změna nainstalovaných komponent](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Vaše prostředí sady Visual Studio se může mírně lišit od zobrazených příkladů v závislosti na vaší verzi a nastavení konfigurace.

1. Otevřete Visual Studio a přihlaste se. Chcete-li vytvořit projekt pro webovou aplikaci, vyberte v nabídce **soubor** možnost **otevřít** > **Web**.

    [![Visual Studio – vytvoření nového řešení](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. V podokně **Otevřít web** vyberte pracovní adresář, kam jste ULOŽILI soubory HTML a CSS, a pak vyberte **otevřít**.

   [![Visual Studio – nabídka soubor s možnostmi otevřít a Web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. V nabídce **zobrazení** v aplikaci Visual Studio vyberte možnost **Průzkumník řešení**. Otevře se nové řešení. Obsahuje projekt webu (ikonu zeměkoule), který obsahuje soubory HTML a CSS.

   [![Visual Studio – nové řešení v Průzkumník řešení](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Před publikováním aplikace je třeba změnit nastavení konfigurace v souboru *index. html*.

   1. Odkomentujte tři řádky v komentáři `"PROD RESOURCE LINKS"` a přepněte závislosti z vývoje do produkčního prostředí. Odkomentujte tři řádky v komentáři `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Vaše závislosti by měly být uvedené v komentáři jako v následujícím příkladu:

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Pokud chcete aplikaci nakonfigurovat tak, aby používala ID registrace aplikace Azure AD, `clientID` změňte hodnotu tak, aby používala **ID aplikace** , které jste zkopírovali v **kroku 3** při [registraci aplikace pro používání služby Azure AD](#register-with-azure-ad). Pokud jste v Azure AD vytvořili odhlašovací **adresu URL** , nastavte tuto hodnotu jako `postLogoutRedirectUri` hodnotu.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Příklad:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Až skončíte s úpravami, uložte *index. html*.

1. Publikujte webovou aplikaci v předplatném Azure jako Azure App Service.  

   > [!NOTE]
   > Několik možností na snímcích obrazovky, které se zobrazují v následujících krocích, se automaticky vyplní daty z vašeho předplatného Azure. Načtení každého podokna zcela může trvat několik sekund.  

   1. V Průzkumník řešení klikněte pravým tlačítkem myši na uzel projektu webu a pak vyberte **publikovat webovou aplikaci**.  

      [![Visual Studio – vyberte možnost Průzkumník řešení publikovat webovou aplikaci.](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Pokud chcete zahájit publikování aplikace, vyberte **začít** .

      [![Visual Studio – podokno profil publikování](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Tuto možnost vyberte, pokud chcete publikovat novou instanci Azure App Service nebo použít existující.

      [![Vyberte instanci Azure App Service.](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-select-target.png#lightbox)

   1. Vyberte předplatné, které chcete použít k publikování aplikace. Vyberte projekt **TsiSpaApp** . Pak vyberte **OK**.

      [![Visual Studio – podokno App Service profil publikování](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Vyberte **publikovat** a nasaďte webovou aplikaci.

      [![Visual Studio – možnost publikování a výstup protokolu publikování](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. V podokně **výstup** sady Visual Studio se zobrazí úspěšný protokol publikování. Po dokončení nasazení aplikace Visual Studio otevře webovou aplikaci na kartě prohlížeče a zobrazí výzvu k přihlášení. Po úspěšném přihlášení se ovládací prvky Time Series Insights naplní daty.

   1. Přejděte do své webové aplikace a přihlaste se, abyste zobrazili vykreslená Time Series Insights vizuální data.

      [![Kontrola hostované webové aplikace](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png)](media/tutorial-create-tsi-sample-spa/vs-publish-hosted-app.png#lightbox)

## <a name="troubleshoot"></a>Řešení potíží  

Kód chyby nebo chybová podmínka | Popis
---------------------| -----------
*AADSTS50011: Pro aplikaci není zaregistrována žádná adresa pro odpověď.* | V registraci služby Azure AD chybí vlastnost **URI pro přesměrování** . Pro registraci aplikace služby Azure AD přejít na**identifikátory URI přesměrování** **ověřování** > . Ověřte, jestli existuje **identifikátor URI pro přesměrování** , který jste zadali v **kroku 2** nebo **4** , pokud jste [zaregistrovali aplikaci pro používání služby Azure AD](#register-with-azure-ad) .
*AADSTS50011: Adresa URL odpovědi zadaná v požadavku neodpovídá adresám URL odpovědí nakonfigurovaným pro aplikaci: '\<Identifikátor GUID ID aplikace > '.* |  >  [](#build-and-publish) Zadaný v kroku 6. b v sestavách a publikování webové aplikace se musí shodovat s hodnotou zadanou v části ověřování identifikátorů URI přesměrování v registraci aplikace služby Azure AD. `postLogoutRedirectUri` |
Webová aplikace se načte, ale má nestylované přihlašovací stránky jenom pro text s bílým pozadím. | Ověřte, zda jsou cesty popsané v **kroku 6** v tématu [sestavení a publikování webové aplikace](#build-and-publish) správné. Pokud webová aplikace nemůže najít soubory .css, stránka nebude používat správné styly.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto kurzu se vytvoří několik spuštěných služeb Azure. Pokud neplánujete dokončit tuto řadu kurzů, doporučujeme odstranit všechny prostředky, abyste se vyhnuli zbytečným nákladům.

V nabídce Azure Portal vlevo:

1. Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste vytvořili pro prostředí Time Series Insights. V horní části stránky vyberte **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a pak vyberte **Odstranit**.
1. Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou vytvořil akcelerátor řešení pro simulaci zařízení. V horní části stránky vyberte **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a pak vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Návrh aplikace
> * Jak zaregistrovat aplikaci v Azure AD
> * Postup pro sestavení, publikování a testování webové aplikace

Tento kurz se integruje s Azure AD a používá identitu uživatele, který je přihlášený k získání přístupového tokenu. Informace o tom, jak získat přístup k rozhraní Time Series Insights API pomocí identity aplikace služby nebo démona, najdete v tomto článku:

> [!div class="nextstepaction"]
> [Ověřování a autorizace pro rozhraní API služby Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
