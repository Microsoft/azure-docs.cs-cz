---
title: 'Kurz: Vytvoření jednostránkovou webovou aplikaci Azure Time Series Insights | Dokumentace Microsoftu'
description: Zjistěte, jak vytvořit jednostránkovou webovou aplikaci, která vyhledá a vykreslí data z prostředí Azure Time Series Insights.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 5ee6e9aefb235feb28468798c3bd6b107f8c7c49
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244040"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Kurz: Vytvoření jednostránkové webové aplikace Azure Time Series Insights

Tento kurz vás provede procesem vytvoření vlastní webové jednostránkové aplikace (SPA) pro přístup k datům Azure Time Series Insights. 

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Návrh aplikace
> * Postup registrace vaší aplikace pomocí Azure Active Directory (Azure AD)
> * Postup pro sestavení, publikování a testování webové aplikace

> [!NOTE]
> * Zdrojový kód pro účely tohoto kurzu je k dispozici na [Githubu](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Time Series Insights [ukázková aplikace klientské](https://insights.timeseries.azure.com/clientsample) hostována zobrazíte dokončená aplikace použité v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

* Zaregistrovat [bezplatné předplatné Azure](https://azure.microsoft.com/free/) pokud ho ještě nemáte.

* Bezplatnou kopii sady Visual Studio. Stáhněte si [verze Community 2017 nebo 2019](https://www.visualstudio.com/downloads/) začít.

* Služby IIS Express, Web Deploy a Azure Cloud Services core tools komponenty pro Visual Studio. Přidáte součásti úpravou instalace sady Visual Studio.

## <a name="application-design"></a>Návrh aplikací

Time Series Insights ukázková jednostránková aplikace slouží jako základ pro návrh a kód použitý v tomto kurzu. Tento kód použije klientskou knihovnu čas Series Insights JavaScript. Klientská knihovna služby Time Series Insights poskytuje abstrakci pro dvou hlavních kategorií rozhraní API:

- **Metody obálky volání služby Time Series Insights dotazování rozhraní API**: Rozhraní REST API, můžete pro Time Series Insights dotazovat data pomocí výrazů založenými na JSON. Metody jsou uspořádány v rámci oboru názvů TsiClient.server knihovny.

- **Metody vytvoření a naplnění několik typů grafů ovládací prvky**: Metody, které lze použít k vizualizaci dat Time Series Insights na webové stránce. Metody jsou uspořádány v rámci oboru názvů TsiClient.ux knihovny.

Tento kurz používá také data z prostředí Time Series Insights ukázkovou aplikaci. Další informace o struktuře Time Series Insights ukázkové aplikace a jak ji používá Klientská knihovna služby Time Series Insights, najdete v kurzu [prozkoumat klientské knihovně Azure čas Series Insights JavaScript](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrace aplikace ve službě Azure AD

Před sestavením aplikace, zaregistrujte se službou Azure AD. Registrace poskytuje konfigurace identity aplikace díky podpoře OAuth pro jednotné přihlašování. OAuth vyžaduje SPA určený typ udělení implicitní autorizace. Aktualizujete autorizace v manifestu aplikace. Manifest aplikace představuje reprezentaci JSON konfigurace identity aplikace.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí svého účtu předplatného Azure.  
1. Vyberte položky **Azure Active Directory** > **Registrace aplikací** > **Registrace nové aplikace**.

   [![Portál Azure – registrace aplikace Begin Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. V **vytvořit** podokno, vyplňte požadované parametry.

   Parametr|Popis
   ---|---
   **Název** | Zadejte název smysluplné registrace.  
   **Typ aplikace** | Nechte **webové aplikace nebo rozhraní API**.
   **Přihlašovací adresa URL** | Zadejte adresu URL pro přihlášení (domů) stránky aplikace. Vzhledem k tomu, že aplikace bude později hostované ve službě Azure App Service, musí používat adresu URL v https:\//azurewebsites.net domény. V tomto příkladu název vychází z názvu registrace.

   Vyberte **vytvořit** vytvoření registrace nové aplikace.

   [![Azure portal – možnosti vytvořit v podokně registrace aplikace Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. Prostředek aplikace poskytují rozhraní REST API, můžete použít jiné aplikace. Rozhraní API jsou zaregistrovaná taky v Azure AD. Rozhraní API poskytují podrobné, zabezpečený přístup pro klientské aplikace zveřejněním *obory*. Vzhledem k tomu, že vaše aplikace volá rozhraní API Azure čas Series Insights, je nutné zadat rozhraní API a oboru. Udělení oprávnění rozhraní API a oboru za běhu. Vyberte **nastavení** > **požadovaná oprávnění** > **přidat**.

   [![Azure portal – přidání možností pro přidání oprávnění Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. V **přístup přes rozhraní API přidat** vyberte **1 Vyberte rozhraní API** k určení rozhraní API Azure čas Series Insights. V **vyberte rozhraní API** podokno, do vyhledávacího pole zadejte **azure čas**. Vyberte **Azure Time Series Insights** v seznamu výsledků. Zvolte **Vybrat**.

   [![Azure portal – možnosti vyhledávání pro přidání oprávnění Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Můžete vybrat v oboru pro rozhraní API **přístup přes rozhraní API přidat** vyberte **2 oprávnění Select**. V **povolit přístup z** podokně, vyberte **service přístup k Azure Time Series Insights** oboru. Zvolte **Vybrat**. Se vrátíte **přístup přes rozhraní API přidat** podokně. Vyberte **Done** (Hotovo).

   [![Azure portal – nastavení oboru pro přidání oprávnění Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. V **požadovaná oprávnění** podokně rozhraní API Azure čas Series Insights se teď zobrazují. Také je třeba zadat oprávnění předběžné souhlasu pro aplikaci pro přístup k rozhraní API a oboru pro všechny uživatele. Vyberte **udělit oprávnění**a pak vyberte **Ano**.

   [![Azure portal – možnosti udělení oprávnění pro přidání služby Azure AD požadovaná oprávnění](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Jak jsme probírali výše, je také nutné aktualizovat manifest aplikace. Na vodorovné nabídce v horní části podokna ("popis cesty"), vyberte název aplikace se vraťte do **registrovaná aplikace** podokně. Vyberte **Manifest**, změnit `oauth2AllowImplicitFlow` vlastnost `true`a pak vyberte **Uložit**.

   [![Azure portal – manifest aktualizace služby Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. V navigace s popisem cesty, vyberte název aplikace se vrátíte **registrovaná aplikace** podokně. Zkopírujte hodnoty **domovskou stránku** a **ID aplikace** pro vaši aplikaci. Později v tomto kurzu použijete tyto vlastnosti.

   [![Azure portal – kopírování ID aplikace i adresa URL domovské stránky hodnoty pro vaši aplikaci](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Sestavení a publikování webové aplikace

1. Vytvořte adresář pro uložení souborů projektu vaší aplikace. Přejděte ke každé z následujících adres URL. Klikněte pravým tlačítkem myši **Raw** odkaz v pravém horním rohu stránky a pak vyberte **uložit jako** uložit soubory v adresáři projektu.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML a JavaScript pro danou stránku
   - [*sampleStyles.css*]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): Šablony stylů CSS

   > [!NOTE]
   > V závislosti na prohlížeči můžete potřebovat změnit přípony souborů HTML a CSS, než soubor uložíte.

1. Ověřte, že požadované součásti jsou nainstalovány v sadě Visual Studio. Musí být nainstalovány součásti nástroje služby IIS Express, Web Deploy a Azure Cloud Services core pro sadu Visual Studio.

    [![Visual Studio – změnit nainstalované součásti](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Prostředí sady Visual Studio se mohou mírně lišit od znázorněné příklady v závislosti na vaší verzi a nastavení konfigurace.

1. Otevřít Visual Studio a přihlaste se. Vytvoření projektu webové aplikace na **souboru** nabídce vyberte možnost **otevřít** > **webu**.

    [![Visual Studio – vytvořit nové řešení](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. V **otevřít webovou stránku** podokně, vyberte pracovní adresář, kam jste uložili soubory HTML a CSS a pak vyberte **otevřít**.

   [![Visual Studio – nabídky soubor, s možností Otevřít a webové stránky](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. V sadě Visual Studio **zobrazení** nabídce vyberte možnost **Průzkumníka řešení**. Otevře se nové řešení. Obsahuje projektu webu (Ikona zeměkoule), který obsahuje soubory HTML a CSS.

   [![Visual Studio – nová řešení v Průzkumníku řešení](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Před publikováním aplikace je nutné změnit nastavení konfigurace v *index.html*.

   1. Zrušením komentáře u tři řádky v části komentáře `"PROD RESOURCE LINKS"` přepnout závislosti z VÝVOJOVÉHO do produkčního prostředí. Odkomentujte tři řádky v části komentáře `"DEV RESOURCE LINKS"`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Závislosti měli okomentován jako v následujícím příkladu:

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

   1. Chcete-li nakonfigurovat aplikaci, aby používala registraci ID aplikace Azure AD, změňte `clientID` a `postLogoutRedirectUri` hodnot pro použití hodnoty **ID aplikace** a **domovskou stránku** , který jste zkopírovali v kroku 9 v [ Registrace aplikace v Azure AD](#register-the-application-with-azure-ad).

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Příklad:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Jakmile budete hotovi, úpravy, uložte *index.html*.

1. Publikování webových aplikací ve vašem předplatném Azure jako služby Azure App Service.  

   > [!NOTE]
   > Několik možností, jak na snímcích obrazovky, které jsou uvedeny v následujících krocích se automaticky vyplní data z vašeho předplatného Azure. Může trvat několik sekund pro každé podokno kompletně načtou.  

   1. V Průzkumníku řešení klikněte pravým tlačítkem myši na uzel projektu webu a pak vyberte **publikovat webovou aplikaci**.  

      [![Visual Studio – vyberte možnost řešení Explorer publikování webové aplikace](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Vyberte **Start** zahájíte publikování vaší aplikace.

      [![Visual Studio – podokno profil publikování](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Vyberte předplatné, pro kterou chcete použít k publikování aplikace. Vyberte **TsiSpaApp** projektu. Pak vyberte **OK**.

      [![Visual Studio – profil publikování podokně služby App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Vyberte **publikovat** k nasazení webové aplikace.

      [![Visual Studio – možnost publikovat a výstup protokolu publikování](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Úspěšné publikování protokol se zobrazí v sadě Visual Studio **výstup** podokně. Až se nasazení dokončí, sada Visual Studio spustí webovou aplikaci na kartě prohlížeče a zobrazí výzvu k přihlášení. Po úspěšném přihlášení ovládací prvky služby Time Series Insights jsou naplněný daty.

## <a name="troubleshoot"></a>Řešení potíží  

Kód chyby nebo chybová podmínka | Popis
---------------------| -----------
*AADSTS50011: Žádná odpověď adresa je zaregistrovaný pro aplikaci.* | Registrace služby Azure AD se nenašel **adresy URL odpovědi** vlastnost. Přejděte na **nastavení** > **adresy URL odpovědí** pro registraci aplikace Azure AD. Ověřte, že **přihlašování** adresa URL zadaná v kroku 3 v [registrace aplikace v Azure AD](#register-the-application-with-azure-ad) je k dispozici.
*AADSTS50011: Odpověď url zadanou v požadavku se neshoduje s odpovědních adres URL nakonfigurované pro aplikaci: "\<Identifikátor GUID aplikace >'.* | `postLogoutRedirectUri` Zadané v kroku 6 v [sestavení a publikování webových aplikací](#build-and-publish-the-web-application) musí odpovídat hodnotě zadané v položce **nastavení** > **adresy URL odpovědí** v registrace aplikace Azure AD. Nezapomeňte také změnit hodnotu **cílovou adresu URL** používat *https* za krok 5 v [sestavení a publikování webových aplikací](#build-and-publish-the-web-application).
Načtení webové aplikace, ale nemá míru, pouze text přihlašovací stránky, s bílým pozadím. | Ověřte, že cest popsané v kroku 4 v [sestavení a publikování webových aplikací](#build-and-publish-the-web-application) jsou správné. Pokud webová aplikace nemůže najít soubory .css, stránka nebude používat správné styly.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto kurzu se vytvoří několik spuštěných služeb Azure. Pokud nechcete v této sérii kurzů dokončit, doporučujeme vám odstranit všechny prostředky, aby vám neúčtovaly zbytečné poplatky.

V portálu levou nabídkou Azure:

1. Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, kterou jste vytvořili pro prostředí Time Series Insights. V horní části stránky vyberte **odstranit skupinu prostředků**, zadejte název skupiny prostředků a pak vyberte **odstranit**.
1. Vyberte **skupiny prostředků**a pak vyberte skupinu prostředků, který byl vytvořen akcelerátor řešení simulaci zařízení. V horní části stránky vyberte **odstranit skupinu prostředků**, zadejte název skupiny prostředků a pak vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o:

> [!div class="checklist"]
> * Návrh aplikace
> * Postup registrace vaší aplikace pomocí Azure AD
> * Postup pro sestavení, publikování a testování webové aplikace

V tomto kurzu se integruje s Azure AD a použije identitu uživatele, který je přihlášen k získání přístupového tokenu. Zjistěte, jak získat přístup k rozhraní API čas Series Insights pomocí identitu aplikace služby nebo démona, najdete v tomto článku:

> [!div class="nextstepaction"]
> [Ověřování a autorizace pro rozhraní API služby Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
