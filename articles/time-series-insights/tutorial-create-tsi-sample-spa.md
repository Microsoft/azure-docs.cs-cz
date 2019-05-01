---
title: 'Kurz: Vytvoření jednostránkovou webovou aplikaci Azure Time Series Insights | Dokumentace Microsoftu'
description: Zjistěte, jak vytvořit jednostránkovou webovou aplikaci, která dotazuje a vykresluje data z prostředí TSI.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 18f5c14a9427f4d7e34a802b2bcc0612a51a804a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573055"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Kurz: Vytvoření jednostránkové webové aplikace Azure Time Series Insights

Tento kurz vás provede procesem vytvoření vlastní jednostránkovou webovou aplikaci pro přístup k datům v Time Series Insights. Konkrétně se dozvíte o:

> [!div class="checklist"]
> * Návrh aplikace
> * Postup registrace aplikace v Azure Active Directory (AD)
> * Postup pro sestavení, publikování a testování webové aplikace

> [!NOTE]
> * Zdrojový kód pro účely tohoto kurzu je k dispozici na [Githubu](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Time Series Insights [ukázková aplikace klientské](https://insights.timeseries.azure.com/clientsample) hostována zobrazíte dokončená aplikace použité v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

* Zaregistrujte si [bezplatné předplatné Azure](https://azure.microsoft.com/free/), pokud ho ještě nemáte.

* Budete také potřebovat bezplatnou kopii sady Visual Studio. Stáhněte si [verze Community 2017 nebo 2019](https://www.visualstudio.com/downloads/) začít.

* Také budete muset **služby IIS Express**, **Webdeploy**, a **Azure Cloud Services core tools** components pro Visual Studio. Přidáte úpravou instalace sady Visual Studio.

## <a name="application-design-overview"></a>Přehled návrhu aplikace

Ukázkovou jednostránkovou aplikaci Time Series Insights poskytuje základ pro návrh a kód použitý v tomto kurzu. Kód zahrnuje použití javascriptové klientské knihovny pro TSI. Klientská knihovna pro TSI zajišťuje abstrakci dvou hlavních kategorií rozhraní API:

- **Metody obálky volání API pro dotazy služby TSI**: Rozhraní REST API, která umožňuje zadat dotaz pro TSI data pomocí výrazů založenými na JSON. Tyto metody jsou uspořádané v rámci oboru názvů `TsiClient.server` knihovny.

- **Metody vytvoření a naplnění několik typů grafů ovládací prvky**: Metody, které se používají pro vizualizace dat služby TSI na webové stránce. Tyto metody jsou uspořádané v rámci oboru názvů `TsiClient.ux` knihovny.

V tomto kurzu se také budou používat data z prostředí TSI ukázkové aplikace. Podrobnosti o struktuře ukázkové aplikace TSI a jejím použití klientské knihovny pro TSI najdete v kurzu [Zkoumání javascriptové klientské knihovny pro službu Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrace aplikace ve službě Azure AD

Před sestavením aplikace je potřeba ji zaregistrovat ve službě Azure AD. Registrace pro aplikaci zajistí konfiguraci identity a umožní jí tak využít podporu OAuth pro jednotné přihlašování. OAuth vyžaduje, aby jednostránkové aplikace používaly implicitní udělování autorizace, které aktualizujete v manifestu aplikace. Manifest aplikace představuje reprezentaci JSON konfigurace identity aplikace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu předplatného Azure.  
1. Vyberte **Azure Active Directory** prostředků v levém podokně, pak **registrace aplikací**, pak **+ registrace nové aplikace**.

   [![Registrace aplikace Azure AD webu Azure portal](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. Na **vytvořit** stránce, vyplňte požadované parametry.

   Parametr|Popis
   ---|---
   **Název** | Zadejte smysluplný název registrace.  
   **Typ aplikace** | Vzhledem k tomu, že vytváříte jednostránkovou webovou aplikaci, ponechte hodnotu Webová aplikace / rozhraní API.
   **Přihlašovací adresa URL** | Zadejte adresu URL domovské nebo přihlašovací stránky aplikace. Vzhledem k tomu, že aplikace bude hostovaná ve službě Azure App Service (vyšší), je nutné použít adresu URL v rámci "https:\//azurewebsites.net" domény. V tomto příkladu název vychází z názvu registrace.

   Jakmile budete hotovi, kliknutím na **Vytvořit** vytvořte novou registraci aplikace.

   [![Registrace aplikace Azure AD webu Azure portal – vytvoření](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. Aplikace prostředků poskytují rozhraní REST API, která můžou použít ostatní aplikace a která jsou také zaregistrovaná ve službě Azure AD. Rozhraní API poskytují podrobný a zabezpečený přístup ke klientským aplikacím tím, že zveřejňují obory. Vzhledem k tomu, že vaše aplikace bude volat rozhraní API služby Azure Time Series Insights, musíte zadat rozhraní API a obor, pro které se za běhu vyžádá a udělí oprávnění. Vyberte **nastavení**, pak **požadovaná oprávnění**, pak **+ přidat**.

   [![Azure portal, Azure AD přidat oprávnění](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. Na stránce **Přidat přístup přes rozhraní API** klikněte na **1 Výběr rozhraní API** a zadejte rozhraní API služby TSI. Na stránce **Výběr rozhraní API** do vyhledávacího pole zadejte azure time. Potom v seznamu výsledků vyberte "Azure Time Series Insights" rozhraní API a klikněte na tlačítko **vyberte**.

   [![Azure portal, Azure AD přidat oprávnění – rozhraní API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Teď zadáte obor rozhraní API. Opět na stránce **Přidat přístup přes rozhraní API** klikněte na **2 Výběr oprávnění**. Na stránce **Povolit přístup** vyberte obor Přístup ke službě Azure Time Series Insights. Klikněte na tlačítko **vyberte**, který se vrátíte do **přístup přes rozhraní API přidat** stránku, kde klikněte na **provádí**.

   [![Azure portal, Azure AD přidat oprávnění – obor](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. Po návratu na stránku **Požadovaná oprávnění** si všimněte, že teď obsahuje rozhraní API služby Azure Time Series Insights. Také musíte předem za všechny uživatele vyjádřit souhlas s tím, že aplikace bude mít oprávnění k přístupu k rozhraní API a oboru. Klikněte na tlačítko **udělit oprávnění** tlačítko v horní části a vyberte **Ano**.

   [![Azure portal, Azure AD požadovaná oprávnění – souhlas](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Jak již bylo zmíněno dříve, musíte také aktualizovat manifest aplikace. Kliknutím na název aplikace v popisu cesty se vraťte na stránku **Registrovaná aplikace**. Vyberte **Manifest**, změnit `oauth2AllowImplicitFlow` vlastnost `true`, pak klikněte na tlačítko **Uložit**.

   [![Manifest aktualizace webu Azure portal Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. Nakonec se kliknutím na popis cesty opět vraťte na stránku **Registrovaná aplikace** a zkopírujte vlastnosti adresy URL **domovské stránky** a **ID aplikace** pro vaši aplikaci. V pozdějším kroku budete pomocí těchto vlastností.

   [![Vlastnosti webu Azure portal Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Sestavení a publikování webové aplikace

1. Vytvořte adresář pro uložení souborů projektu vaší aplikace. Přejděte ke každému z následujících adres URL, klikněte pravým tlačítkem na odkaz "Raw" v horní pravé oblasti stránky a "Uložit jako" do adresáře vašeho projektu.

   > [!NOTE]
   > V závislosti na prohlížeči možná budete muset před uložením souboru opravit příponu souboru (na HTML nebo CSS).

   - [**index.html**](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML a JavaScript pro stránku.
   - [**sampleStyles.css**]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): Šablona stylů CSS.

1. Ověřte, zda má Visual Studio nainstalované potřebné součásti.

    [![VS - změnit nainstalované součásti](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    * Budete potřebovat **služby IIS Express**, **Webdeploy**, a **Azure Cloud Services core tools** components pro Visual Studio.

    > [!NOTE]
    > Prostředí sady Visual Studio se může mírně lišit od příkladů znázorněné v závislosti na verzi a nastavení konfigurace.

1. Spusťte sadu Visual Studio a přihlaste se, abyste mohli vytvořit projekt webové aplikace. V nabídce **Soubor** vyberte **Otevřít** a možnost **Web**.

    [![VS - vytvořit nové řešení](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. Na **otevřít webovou stránku** dialogového okna, vyberte pracovní adresář, kam jste uložili soubory HTML a CSS a pak klikněte na tlačítko **otevřít**.

   [![VS - otevřít web-file](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. Z nabídky **Zobrazení** v sadě Visual Studio otevřete **Průzkumníka řešení**. Měli byste vidět nové řešení, obsahuje webový projekt (Ikona zeměkoule), který obsahuje soubory HTML a CSS.

   [![VS - nové řešení řešení explorer](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Před publikováním aplikace, budete muset změnit nastavení konfigurace v **index.html**.

   a. Přepnout závislosti z VÝVOJOVÉHO do produkčního prostředí uncommenting tři řádky v části komentáře `"PROD RESOURCE LINKS"`. Odkomentujte tři řádky v části komentáře `"DEV RESOURCE LINKS"`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Proto by měl okomentován závislosti:

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

   b. V dalším kroku nakonfigurujte aplikaci, aby používala vašeho ID registrace aplikace Azure Active Directory. Změnit `clientID` a `postLogoutRedirectUri` pole použít **ID aplikace** a **adresa URL domovské stránky** jste si zkopírovali v **kroku 9** z [zaregistrovat aplikace s Azure AD](#register-the-application-with-azure-ad).

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Příklad:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   c. Uložit **index.html** až dokončíte tyto úpravy.

1. Nyní publikujte webovou aplikaci do vašeho předplatného Azure jako služby Azure App Service:  

   > [!NOTE]
   > Do několika polí v následujících dialogových oknech se vyplní data z vašeho předplatného Azure. Proto může úplné načtení jednotlivých dialogových oken několik sekund trvat a teprve pak můžete pokračovat.  

   a. Klikněte pravým tlačítkem na uzel projektu webové stránky v **Průzkumníka řešení**a vyberte **publikovat webovou aplikaci**.  

      [![VS - Průzkumník řešení publikování webové aplikace](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   b. Vyberte **Start** zahájíte publikování vaší aplikace.

      [![VS - profil publikování](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   c. Vyberte předplatné, které chcete použít pro publikování aplikace. Vyberte **TsiSpaApp** projektu. Potom **OK**:

      [![VS - Publikovat profil – služby app service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   d. Klikněte na tlačítko **publikovat** k nasazení webové aplikace.

      [![Sada Visual Studio – Publikovat webovou aplikaci – výstup protokolu publikování](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   e. V okně **Výstup** v sadě Visual Studio by se měl zobrazit protokol úspěšného publikování. Po dokončení nasazení sada Visual Studio také webovou aplikaci otevře na kartě prohlížeče a zobrazí výzvu k přihlášení. Po úspěšném přihlášení zobrazí se vám všechny prvky TSI naplněný daty.

## <a name="troubleshooting"></a>Řešení potíží  

Kód chyby nebo chybová podmínka | Popis
---------------------| -----------
*AADSTS50011: Žádná odpověď adresa je zaregistrovaný pro aplikaci.* | Registrace služby Azure AD se nenašel **adresy URL odpovědi** vlastnost. Přejděte na stránku **Nastavení** / **Adresy URL odpovědí** pro vaši registraci aplikace ve službě Azure AD. Ověřte, že **přihlašování** zadána adresa URL v **kroku 3** z [registrace aplikace v Azure AD](#register-the-application-with-azure-ad) je k dispozici.
*AADSTS50011: Odpověď url zadanou v požadavku se neshoduje s odpovědních adres URL nakonfigurované pro aplikaci: "\<Identifikátor GUID aplikace >'.* | Hodnota `postLogoutRedirectUri` zadaná v kroku 4 v části [Sestavení a publikování webové aplikace](#build-and-publish-the-web-application) musí odpovídat hodnotě zadané ve vlastnosti **Nastavení** / **Adresy URL odpovědí** ve vaší registraci aplikace ve službě Azure AD. Nezapomeňte také změnit **cílovou adresu URL** používat `https`, za **kroku 5** z [sestavení a publikování webových aplikací](#build-and-publish-the-web-application).
Webová aplikace se načte, ale zobrazí se přihlašovací stránka bez stylů pouze s textem na bílém pozadím. | Ověřte, že cest popsané v **kroku 4** z [sestavení a publikování webových aplikací](#build-and-publish-the-web-application) jsou správné. Pokud webová aplikace nemůže najít soubory .css, stránka nebude používat správné styly.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto kurzu se vytvoří několik spuštěných služeb Azure. Pokud se nechystáte tuto sérii kurzů dokončit, doporučujeme odstranit všechny prostředky, aby se vám neúčtovaly zbytečné poplatky.

V nabídce vlevo na webu Azure Portal:

1. Vyberte **skupiny prostředků**, pak vyberte skupinu prostředků, kterou jste vytvořili pro prostředí TSI. V horní části stránky klikněte na **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a potom klikněte na **Odstranit**.
1. Vyberte **skupiny prostředků**, pak vyberte skupinu prostředků, který byl vytvořen akcelerátor řešení simulaci zařízení. V horní části stránky klikněte na **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a potom klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Návrh aplikace
> * Postup registrace aplikace v Azure Active Directory (AD)
> * Postup pro sestavení, publikování a testování webové aplikace

V tomto kurzu se provádí integrace se službou Azure AD a pomocí identity přihlášeného uživatele se získá přístupový token. Informace o přístupu k rozhraní API služby TSI pomocí identity služby nebo aplikace démona najdete tady:

> [!div class="nextstepaction"]
> [Ověřování a autorizace pro rozhraní API služby Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)