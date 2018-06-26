---
title: Vytvoření jednostránkové webové aplikace Azure Time Series Insights
description: Zjistěte, jak vytvořit jednostránkovou webovou aplikaci, která dotazuje a vykresluje data z prostředí TSI.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: bryanla
ms.openlocfilehash: 4442a724cf3e37d5e7271d9c29f99138ab1faa5f
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295826"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Kurz: Vytvoření jednostránkové webové aplikace Azure Time Series Insights

Tento kurz vás provede procesem vytvoření vlastní jednostránkové webové aplikace (SPA) pro přístup k datům TSI, která vychází z [ukázkové aplikace Time Series Insights (TSI)](https://insights.timeseries.azure.com/clientsample). V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Návrh aplikace
> * Postup registrace aplikace v Azure Active Directory (AD)
> * Postup pro sestavení, publikování a testování webové aplikace 

## <a name="prerequisites"></a>Požadavky

Zaregistrujte si [bezplatné předplatné Azure](https://azure.microsoft.com/free/), pokud ho ještě nemáte. 

Budete si také muset nainstalovat sadu Visual Studio, pokud jste to ještě neudělali. Pro účely tohoto kurzu si můžete [stáhnout a nainstalovat bezplatnou verzi Community nebo bezplatnou zkušební verzi](https://www.visualstudio.com/downloads/).

## <a name="application-design-overview"></a>Přehled návrhu aplikace

Jak už bylo zmíněno, ukázková aplikace TSI poskytuje základ návrhu a kódu použitých v tomto kurzu. Kód zahrnuje použití javascriptové klientské knihovny pro TSI. Klientská knihovna pro TSI zajišťuje abstrakci dvou hlavních kategorií rozhraní API:

- **Obálkové metody pro volání dotazovacích rozhraní API pro TSI:** Rozhraní REST API umožňující dotazovat data TSI pomocí výrazů založených na JSON. Tyto metody jsou uspořádané v rámci oboru názvů `TsiClient.server` knihovny.
- **Metody pro vytváření a naplňování různých typů ovládacích prvků grafů:** Metody používané k vizualizaci dat TSI na webové stránce. Tyto metody jsou uspořádané v rámci oboru názvů `TsiClient.ux` knihovny.

V tomto kurzu se také budou používat data z prostředí TSI ukázkové aplikace. Podrobnosti o struktuře ukázkové aplikace TSI a jejím použití klientské knihovny pro TSI najdete v kurzu [Zkoumání javascriptové klientské knihovny pro službu Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Registrace aplikace ve službě Azure AD 

Před sestavením aplikace je potřeba ji zaregistrovat ve službě Azure AD. Registrace pro aplikaci zajistí konfiguraci identity a umožní jí tak využít podporu OAuth pro jednotné přihlašování. OAuth vyžaduje, aby jednostránkové aplikace používaly implicitní udělování autorizace, které aktualizujete v manifestu aplikace. Manifest aplikace představuje reprezentaci JSON konfigurace identity aplikace. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu předplatného Azure.  
2. V levém podokně vyberte prostředek **Azure Active Directory**, pak vyberte **Registrace aplikací** a pak **+ Nová registrace aplikace**:  
   
   ![Registrace aplikace ve službě Azure AD na webu Azure Portal](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

3. Na stránce **Vytvořit** vyplňte požadované parametry:
   
   Parametr|Popis
   ---|---
   **Název** | Zadejte smysluplný název registrace.  
   **Typ aplikace** | Vzhledem k tomu, že vytváříte jednostránkovou webovou aplikaci, ponechte hodnotu Webová aplikace / rozhraní API.
   **Přihlašovací adresa URL** | Zadejte adresu URL domovské nebo přihlašovací stránky aplikace. Vzhledem k tomu, že aplikace bude hostovaná v Azure App Service (později), musíte použít adresu URL v rámci domény https://azurewebsites.net. V tomto příkladu název vychází z názvu registrace.

   Jakmile budete hotovi, kliknutím na **Vytvořit** vytvořte novou registraci aplikace.

   ![Registrace aplikace ve službě Azure AD na webu Azure Portal – vytvoření](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

4. Aplikace prostředků poskytují rozhraní REST API, která můžou použít ostatní aplikace a která jsou také zaregistrovaná ve službě Azure AD. Rozhraní API poskytují podrobný a zabezpečený přístup ke klientským aplikacím tím, že zveřejňují obory. Vzhledem k tomu, že vaše aplikace bude volat rozhraní API služby Azure Time Series Insights, musíte zadat rozhraní API a obor, pro které se za běhu vyžádá a udělí oprávnění. Vyberte **Nastavení**, pak **Požadovaná oprávnění** a pak **+ Přidat**:

   ![Přidání oprávnění ve službě Azure AD na webu Azure Portal](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

5. Na stránce **Přidat přístup přes rozhraní API** klikněte na **1 Výběr rozhraní API** a zadejte rozhraní API služby TSI. Na stránce **Výběr rozhraní API** do vyhledávacího pole zadejte azure time. Pak v seznamu výsledků vyberte rozhraní API služby Azure Time Series Insights a klikněte na **Vybrat**: 

   ![Přidání oprávnění ve službě Azure AD na webu Azure Portal – rozhraní API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

6. Teď zadáte obor rozhraní API. Opět na stránce **Přidat přístup přes rozhraní API** klikněte na **2 Výběr oprávnění**. Na stránce **Povolit přístup** vyberte obor Přístup ke službě Azure Time Series Insights. Kliknutím na **Vybrat** se vraťte na stránku **Přidat přístup přes rozhraní API**, kde klikněte na **Hotovo**:

   ![Přidání oprávnění ve službě Azure AD na webu Azure Portal – obor](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

7. Po návratu na stránku **Požadovaná oprávnění** si všimněte, že teď obsahuje rozhraní API služby Azure Time Series Insights. Také musíte předem za všechny uživatele vyjádřit souhlas s tím, že aplikace bude mít oprávnění k přístupu k rozhraní API a oboru. V horní části klikněte na tlačítko **Udělit oprávnění** a vyberte **Ano**:

   ![Požadovaná oprávnění ve službě Azure AD na webu Azure Portal – vyjádření souhlasu](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

8. Jak již bylo zmíněno dříve, musíte také aktualizovat manifest aplikace. Kliknutím na název aplikace v popisu cesty se vraťte na stránku **Registrovaná aplikace**. Vyberte **Manifest**, změňte vlastnost `oauth2AllowImplicitFlow` na hodnotu `true` a pak klikněte na **Uložit**:

   ![Aktualizace manifestu ve službě Azure AD na webu Azure Portal](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

9. Nakonec se kliknutím na popis cesty opět vraťte na stránku **Registrovaná aplikace** a zkopírujte vlastnosti adresy URL **domovské stránky** a **ID aplikace** pro vaši aplikaci. Tyto vlastnosti použijete v pozdějším kroku:

   ![Vlastnosti ve službě Azure AD na webu Azure Portal](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>Sestavení a publikování webové aplikace

1. Vytvořte adresář pro uložení souborů projektu vaší aplikace. Pak přejděte na obě z následujících adres URL, v pravé horní části stránky klikněte pravým tlačítkem na odkaz Raw (Nezpracovaná data) a vyberte Uložit jako do adresáře vašeho projektu:

   > [!NOTE]
   > V závislosti na prohlížeči možná budete muset před uložením souboru opravit příponu souboru (na HTML nebo CSS).

   - **index.html:** HTML a JavaScript pro stránku – https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css:** Šablona stylů CSS – https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
2. Spusťte sadu Visual Studio a přihlaste se, abyste mohli vytvořit projekt webové aplikace. V nabídce **Soubor** vyberte **Otevřít** a možnost **Web**. V dialogovém okně **Otevřít web** vyberte pracovní adresář, do kterého jste uložili soubory HTML a CSS, a pak klikněte na **Otevřít**:

   ![Sada Visual Studio – Soubor – Otevřít web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

3. Z nabídky **Zobrazení** v sadě Visual Studio otevřete **Průzkumníka řešení**. Mělo by se zobrazit vaše nové řešení obsahující projekt webu (ikona zeměkoule), který obsahuje soubory HTML a CSS:

   ![Sada Visual Studio – nové řešení v Průzkumníku řešení](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

4. Než budete moct aplikaci publikovat, je potřeba aktualizovat části kódu JavaScriptu v souboru **index.html**: 

   a. Nejprve změňte cesty pro odkazy na soubory JavaScriptu a šablony stylů v elementu `<head>`. Otevřete soubor **index.html** ve vašem řešení sady Visual Studio a vyhledejte následující řádky kódu JavaScriptu. Zrušte komentář na třech řádcích pod textem PROD RESOURCE LINKS a okomentujte tři řádky pod textem DEV RESOURCE LINKS:
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Váš upravený kód by měl vypadat podobně jako v následujícím příkladu:

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. Dále změňte logiku přístupového tokenu tak, aby používala vaši novou registraci aplikace ve službě Azure AD. Změňte následující proměnné `clientID` a `postLogoutRedirectUri` na ID aplikace a adresu URL domovské stránky, které jste zkopírovali v kroku 9 v části [Registrace aplikace ve službě Azure AD](#register-the-application-with-azure-ad):

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Váš upravený kód by měl vypadat podobně jako v následujícím příkladu:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. Jakmile budete s úpravami hotovi, uložte soubor **index.html**.

5. Teď webovou aplikaci publikujte do svého předplatného Azure jako Azure App Service:  

   > [!NOTE]
   > Do několika polí v následujících dialogových oknech se vyplní data z vašeho předplatného Azure. Proto může úplné načtení jednotlivých dialogových oken několik sekund trvat a teprve pak můžete pokračovat.  

   a. V **Průzkumníku řešení** klikněte pravým tlačítkem na uzel projektu webu a vyberte **Publikovat webovou aplikaci**:  

      ![Sada Visual Studio – Průzkumník řešení – Publikovat webovou aplikaci](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. Vyberte **Microsoft Azure App Service** a vytvořte cíl publikování:  

      ![Sada Visual Studio – Publikovat profil](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. Vyberte předplatné, které chcete k publikování aplikace použít, a pak klikněte na Nový: 

      ![Sada Visual Studio – Publikovat profil – App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. Počkejte několik sekund, než se v dialogovém okně **Vytvořit službu App Service** načtou všechna pole, a pak upravte následující pole:
   
      Pole | Popis
      ---|---
      **Název aplikace** | Změňte tuto hodnotu na název registrace aplikace ve službě Azure AD, který jste použili v kroku 3 v části [Registrace aplikace ve službě Azure AD](#register-the-application-with-azure-ad). 
      **Skupina prostředků** | Pomocí tlačítka **Nová...** změňte hodnotu tak, aby odpovídala poli **Název aplikace**.
      **Plán služby App Service** | Pomocí tlačítka **Nová...** změňte hodnotu tak, aby odpovídala poli **Název aplikace**.

      Jakmile budete hotovi, klikněte na **Vytvořit**. Zatímco se vytvářejí prostředky Azure, tlačítko **Exportovat** v levé dolní části se na několik sekund nahradí za text Nasazování. Po vytvoření prostředků se vrátíte do předchozího dialogového okna. 

      ![Sada Visual Studio – Publikovat profil – přidání nové služby App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. Po návratu do dialogového okna **Publikovat** se ujistěte, že je **Metoda publikování** nastavená na Nasazení webu. V **cílové adrese URL** také změňte `http` na `https`, aby odpovídala registraci aplikace ve službě Azure AD. Pak kliknutím na Publikovat webovou aplikaci nasaďte:  

      ![Sada Visual Studio – Publikovat webovou aplikaci – publikování služby App Service](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. V okně **Výstup** v sadě Visual Studio by se měl zobrazit protokol úspěšného publikování. Po dokončení nasazení sada Visual Studio také webovou aplikaci otevře na kartě prohlížeče a zobrazí výzvu k přihlášení. Po úspěšném přihlášení se zobrazí ovládací prvky TSI naplněné daty:  

      [![Sada Visual Studio – Publikovat webovou aplikaci – výstup protokolu publikování](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![Jednostránková aplikace TSI – přihlášení](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>Řešení potíží  

Kód chyby nebo chybová podmínka | Popis
---------------------| -----------
*AADSTS50011: Pro aplikaci není zaregistrovaná žádná adresa pro odpovědi.* | V registraci ve službě Azure AD chybí vlastnosti Adresa URL odpovědi. Přejděte na stránku **Nastavení** / **Adresy URL odpovědí** pro vaši registraci aplikace ve službě Azure AD. Ověřte, že se zobrazuje **přihlašovací** adresa URL zadaná v kroku 3 v části [Registrace aplikace ve službě Azure AD](#register-the-application-with-azure-ad). 
*AADSTS50011: Adresa URL zadaná v požadavku neodpovídá adresám URL nakonfigurovaným pro aplikaci: <Application ID GUID>.* | Hodnota `postLogoutRedirectUri` zadaná v kroku 4 v části [Sestavení a publikování webové aplikace](#build-and-publish-the-web-application) musí odpovídat hodnotě zadané ve vlastnosti **Nastavení** / **Adresy URL odpovědí** ve vaší registraci aplikace ve službě Azure AD. Nezapomeňte změnit také **cílovou adresu URL** tak, aby používala `https`, jako v kroku 5.e v části [Sestavení a publikování webové aplikace](#build-and-publish-the-web-application).
Webová aplikace se načte, ale zobrazí se přihlašovací stránka bez stylů pouze s textem na bílém pozadím. | Ověřte správnost cest popsaných v kroku 4 v části [Sestavení a publikování webové aplikace](#build-and-publish-the-web-application). Pokud webová aplikace nemůže najít soubory .css, stránka nebude používat správné styly.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V tomto kurzu se vytvoří několik spuštěných služeb Azure. Pokud se nechystáte tuto sérii kurzů dokončit, doporučujeme odstranit všechny prostředky, aby se vám neúčtovaly zbytečné poplatky. 

V nabídce vlevo na webu Azure Portal:

1. Klikněte na ikonu **Skupiny prostředků** a vyberte skupinu prostředků, kterou jste vytvořili pro prostředí TSI. V horní části stránky klikněte na **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a potom klikněte na **Odstranit**. 
2. Klikněte na ikonu **Skupiny prostředků** a vyberte skupinu vytvořenou akcelerátorem řešení simulace zařízení. V horní části stránky klikněte na **Odstranit skupinu prostředků**, zadejte název skupiny prostředků a potom klikněte na **Odstranit**. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Návrh aplikace
> * Postup registrace aplikace v Azure Active Directory (AD)
> * Postup pro sestavení, publikování a testování webové aplikace 

V tomto kurzu se provádí integrace se službou Azure AD a pomocí identity přihlášeného uživatele se získá přístupový token. Informace o přístupu k rozhraní API služby TSI pomocí identity služby nebo aplikace démona najdete tady:

> [!div class="nextstepaction"]
> [Ověřování a autorizace pro rozhraní API služby Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
