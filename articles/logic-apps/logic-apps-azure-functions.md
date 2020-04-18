---
title: Přidání a volání funkcí Azure z aplikací Azure Logic Apps
description: Volání a spouštění vlastního kódu ve funkcích Azure z automatizovaných úloh a pracovních postupů v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.openlocfilehash: 29713622be90ea280bff3c002be746bf1615718f
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605898"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Volání funkcí Azure z Azure Logic Apps

Pokud chcete spustit kód, který provádí určitou úlohu ve vašich aplikacích logiky, můžete vytvořit vlastní funkci pomocí [Funkce Azure](../azure-functions/functions-overview.md). Tato služba vám pomůže vytvořit Funkce Node.js, C#, a F#, takže nemusíte vytvářet úplnou aplikaci nebo infrastrukturu pro spuštění kódu. Můžete také [volat aplikace logiky z funkce Azure](#call-logic-app). Funkce Azure poskytují v cloudu bezserverové výpočty a jsou užitečné pro provádění úloh, jako jsou tyto příklady:

* Rozšiřte chování aplikace logiky pomocí funkcí v souboru Node.js nebo C#.
* Provádějte výpočty v pracovním postupu aplikace logiky.
* Použijte rozšířené formátování nebo výpočetní pole v aplikacích logiky.

Chcete-li spustit fragmenty kódu bez vytváření funkcí Azure, přečtěte si, jak [přidat a spustit vložkový kód](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Integrace mezi logic ovými aplikacemi a funkcemi Azure momentálně nefunguje s povolenými sloty.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace pro funkce Azure, což je kontejner pro funkce Azure, spolu s vaší funkcí Azure. Pokud nemáte aplikaci funkcí, [vytvořte nejprve aplikaci funkcí](../azure-functions/functions-create-first-azure-function.md). Potom můžete vytvořit funkci buď mimo aplikaci logiky na webu Azure Portal, nebo [z aplikace logiky](#create-function-designer) v Návrháři aplikace logiky.

* Při práci s aplikacemi logiky platí stejné požadavky pro funkční aplikace a funkce, ať už jsou existující nebo nové:

  * Vaše aplikace funkcí a aplikace logiky musí používat stejné předplatné Azure.

  * Nové aplikace funkcí musí jako zásobník runtime používat .NET nebo JavaScript. Když přidáte novou funkci do existujících aplikací funkcí, můžete vybrat c# nebo JavaScript.

  * Funkce používá šablonu **aktivační události HTTP.**

    Šablona aktivační události HTTP `application/json` může přijímat obsah, který má typ z aplikace logiky. Když přidáte funkci Azure do aplikace logiky, Návrhář aplikace logiky zobrazí vlastní funkce, které se vytvoří z této šablony v rámci předplatného Azure.

  * Vaše funkce nepoužívá vlastní trasy, pokud jste nedefinovali [definici OpenAPI](../azure-functions/functions-openapi-definition.md) (dříve známou jako [soubor Swagger).](https://swagger.io/)

  * Pokud máte definici OpenAPI pro vaši funkci, Návrhář logických aplikací poskytuje bohatší prostředí při práci s parametry funkce. Než vaše aplikace logiky může najít a přístup k funkcím, které mají definice OpenAPI, [nastavte aplikaci funkce podle následujících kroků](#function-swagger).

* Aplikace logiky, kde chcete přidat funkci, včetně [aktivační události](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako první krok v aplikaci logiky

  Před přidáním akcí, které spouštějí funkce, musí aplikace logiky začínat aktivační událostí. Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a úvodní [příručka: Vytvořte si první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Najít funkce, které mají popisy OpenAPI

Pro bohatší prostředí při práci s parametry funkce v Logic Apps Designer, [generovat openapi definice](../azure-functions/functions-openapi-definition.md), dříve známé jako [soubor Swagger](https://swagger.io/), pro vaši funkci. Chcete-li nastavit aplikaci funkcí tak, aby vaše aplikace logiky mohla najít a používat funkce s popisy Swagger, postupujte takto:

1. Ujistěte se, že vaše aplikace funkce je aktivně spuštěna.

1. Ve své funkční aplikaci nastavte [sdílení prostředků napříč zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) tak, aby všechny původy byly povoleny pomocí následujících kroků:

   1. V seznamu **Aplikace funkcí** vyberte aplikaci funkcí. V pravém podokně vyberte **možnost Platformfeatures** > **CORS**.

      ![Vyberte aplikaci funkce > "Funkce platformy" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. V **části CORS**přidejte**`*`** zástupný znak hvězdičky ( ) ale odeberte všechny ostatní počátky v seznamu a vyberte **Uložit**.

      ![Nastavit "CORS* na zástupný znak *"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Přístup k hodnotám vlastností v požadavcích HTTP

Funkce webhooku mohou přijímat požadavky HTTP jako vstupy a předat tyto požadavky jiným funkcím. Například i když Logic Apps má [funkce, které převádějí DateTime hodnoty](../logic-apps/workflow-definition-language-functions-reference.md), tato základní ukázková funkce JavaScript ukazuje, jak můžete přistupovat k vlastnosti uvnitř objektu požadavku, který je předán funkci a provádět operace s touto hodnotou vlastnosti. Pro přístup k vlastnostem uvnitř objektů tento příklad používá [operátor tečka (.):](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Zde je to, co se děje uvnitř této funkce:

1. Funkce vytvoří `data` proměnnou a `body` přiřadí `request` objekt uvnitř objektu této proměnné. Funkce používá operátor tečka (.) `body` k odkazování na objekt uvnitř objektu: `request`

   ```javascript
   var data = request.body;
   ```

1. Funkce nyní může `date` přistupovat `data` k vlastnosti prostřednictvím proměnné a převést tuto hodnotu vlastnosti z typu DateTime na typ DateString voláním `ToDateString()` funkce. Funkce také vrátí výsledek `body` prostřednictvím vlastnosti v odpovědi funkce:

   ```javascript
   body: data.date.ToDateString();
   ```

Teď, když jste vytvořili funkci Azure, postupujte podle pokynů, jak [přidat funkce do aplikací logiky](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Vytváření funkcí uvnitř aplikací logiky

Funkce Azure můžete vytvářet přímo z pracovního postupu aplikace logiky pomocí integrované akce Funkce Azure v Návrháři aplikací logiky, ale tuto metodu můžete použít jenom pro funkce Azure napsané v JavaScriptu. Pro ostatní jazyky můžete vytvářet funkce Azure prostřednictvím prostředí Azure Functions na webu Azure Portal. Další informace najdete [v tématu Vytvoření první funkce na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md).

Ale než můžete vytvořit libovolnou funkci Azure, musíte už mít aplikaci funkce Azure, což je kontejner pro vaše funkce. Pokud nemáte aplikaci funkcí, vytvořte tuto aplikaci funkce jako první. Viz [Vytvoření první funkce na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md).

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Chcete-li vytvořit a přidat funkci, postupujte podle kroku, který se vztahuje na váš scénář:

   * V posledním kroku pracovního postupu aplikace logiky vyberte **Nový krok**.

   * Mezi existujícími kroky v pracovním postupu aplikace logiky přesuňte ukazatel myši na šipku, vyberte znaménko plus (+) a pak vyberte **Přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "azure functions". Ze seznamu akcí vyberte akci **Zvolit funkci Azure,** například:

   ![Najít "funkce Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Ze seznamu aplikací funkcí vyberte aplikaci funkcí. Po otevření seznamu akcí vyberte tuto akci: **Vytvořit novou funkci.**

   ![Výběr aplikace pro funkci](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. V editoru definic funkcí definujte svou funkci:

   1. V poli **Název funkce** zadejte název funkce.

   1. V poli **Kód** přidejte kód do šablony funkce, včetně odpovědi a datové části, které chcete vrátit do aplikace logiky po dokončení spuštění funkce. Až to budete mít, vyberte **Vytvořit**.

   Příklad:

   ![Definujte svou funkci](./media/logic-apps-azure-functions/add-code-function-definition.png)

   V kódu šablony * `context` objekt* odkazuje na zprávu, kterou aplikace logiky odešle prostřednictvím pole **Tělo požadavku** v pozdějším kroku. Chcete-li `context` získat přístup k vlastnostem objektu z evnitř funkce, použijte tuto syntaxi:

   `context.body.<property-name>`

   Chcete-li například `content` odkazovat `context` na vlastnost uvnitř objektu, použijte tuto syntaxi:

   `context.body.content`

   Kód šablony také `input` obsahuje proměnnou, která `data` ukládá hodnotu z parametru, takže vaše funkce může provádět operace s tou hodnotou. Uvnitř funkcí JavaScriptu `data` je proměnná `context.body`také zkratkou pro .

   > [!NOTE]
   > Vlastnost `body` zde platí pro `context` objekt a není stejný jako **body** token z výstupu akce, které můžete také předat vaší funkci.

1. V poli **Text požadavku** zadejte vstup funkce, který musí být formátován jako objekt Zápisu objektu JavaScriptu (JSON).

   Tento vstup je *objekt kontextu* nebo zprávy, které aplikace logiky odešle do vaší funkce. Po klepnutí do pole **Tělo požadavku** se zobrazí seznam dynamického obsahu, abyste mohli vybrat tokeny pro výstupy z předchozích kroků. Tento příklad určuje, že datová část `content` kontextu obsahuje vlastnost s názvem, která má hodnotu **tokenu Od** z aktivační události e-mailu.

   ![Příklad "Tělo požadavku" – datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   Zde objekt kontextu není přetypován jako řetězec, takže obsah objektu se přidá přímo do datové části JSON. Pokud však objekt kontextu není tokenEm JSON, který předá řetězec, objekt JSON nebo pole JSON, zobrazí se chyba. Takže pokud tento příklad místo toho použil token **přijatý čas,** můžete přetypovat objekt kontextu jako řetězec přidáním dvojitých uvozovek.

   ![Přetypování objektu jako řetězce](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Chcete-li zadat další podrobnosti, například metodu, která má být používána, záhlaví požadavku nebo parametry dotazu nebo ověřování, otevřete seznam **Přidat nový parametr** a vyberte požadované možnosti. Pro ověřování se možnosti liší v závislosti na vybrané funkci. Viz [Povolení ověřování pro funkce Azure](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Přidání existujících funkcí do aplikací logiky

Chcete-li volat existující funkce Azure z vašich aplikací logiky, můžete přidat funkce Azure jako každá jiná akce v Návrháři aplikací logiky.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Pod krokem, kam chcete přidat funkci, vyberte **Nový krok**.

1. V části **Zvolte akci**zadejte do vyhledávacího pole jako filtr "azure functions". Ze seznamu akcí vyberte akci **Zvolit funkci Azure.**

   ![Najít "funkce Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. Ze seznamu aplikací funkcí vyberte aplikaci funkcí. Po zobrazení seznamu funkcí vyberte funkci.

   ![Výběr aplikace funkcí a funkce Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Pro funkce, které mají definice rozhraní API (Swagger popisy) a jsou [nastaveny tak, aby vaše aplikace logiky můžete najít a přístup k těmto funkcím](#function-swagger), můžete vybrat **Swagger akce**.

   ![Vyberte aplikaci funkcí, "Akce swaggeru" a funkci Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. V poli **Text požadavku** zadejte vstup funkce, který musí být formátován jako objekt Zápisu objektu JavaScriptu (JSON).

   Tento vstup je *objekt kontextu* nebo zprávy, které aplikace logiky odešle do vaší funkce. Po klepnutí do pole **Tělo požadavku** se zobrazí seznam dynamického obsahu, abyste mohli vybrat tokeny pro výstupy z předchozích kroků. Tento příklad určuje, že datová část `content` kontextu obsahuje vlastnost s názvem, která má hodnotu **tokenu Od** z aktivační události e-mailu.

   ![Příklad "Tělo požadavku" – datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   Zde objekt kontextu není přetypován jako řetězec, takže obsah objektu se přidá přímo do datové části JSON. Pokud však objekt kontextu není tokenEm JSON, který předá řetězec, objekt JSON nebo pole JSON, zobrazí se chyba. Pokud tedy tento příklad místo toho použil token **přijatý čas,** můžete přetypovat objekt kontextu jako řetězec přidáním dvojitých uvozovek:

   ![Přetypování objektu jako řetězce](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Chcete-li zadat další podrobnosti, například metodu, která má být používána, záhlaví požadavků, parametry dotazu nebo ověřování, otevřete seznam **Přidat nový parametr** a vyberte požadované možnosti. Pro ověřování se možnosti liší v závislosti na vybrané funkci. Viz [Povolení ověřování ve funkcích Azure](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Volání aplikací logiky z funkcí Azure

Pokud chcete aktivovat aplikaci logiky z funkce Azure, aplikace logiky musí začínat aktivační událostí, která poskytuje volatelný koncový bod. Například můžete spustit aplikaci logiky s **HTTP**, **Požadavek**, **Fronty Azure**nebo **Event Grid** aktivační událost aktivační události. Uvnitř funkce odešlete požadavek HTTP POST na adresu URL aktivační události a zahrňte datovou část, kterou má aplikace logiky zpracovat. Další informace najdete v tématu [Volání, aktivační událost nebo hnízdit aplikace logiky](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-azure-functions"></a>Povolení ověřování pro funkce Azure

Chcete-li ověřit přístup k prostředkům v jiných klientech Služby Azure Active Directory (Azure AD), aniž byste se museli přihlašovat a zajišťovat přihlašovací údaje nebo tajné klíče, může vaše aplikace logiky používat [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) (dříve označovanou jako identita spravované služby nebo MSI). Azure spravuje tuto identitu za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte poskytovat nebo otáčet tajné kódy. Další informace o [službách Azure, které podporují spravované identity pro ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Pokud nastavíte aplikaci logiky pro použití systémově přiřazené identity nebo ručně vytvořené uživatelem přiřazené identity, funkce Azure ve vaší aplikaci logiky můžete také použít stejnou identitu pro ověřování. Další informace o podpoře ověřování funkcí Azure v aplikacích logiky najdete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Pokud chcete nastavit a používat spravovanou identitu pomocí své funkce, postupujte takto:

1. Povolte spravovanou identitu v aplikaci logiky a nastavte přístup této identity k cílovému prostředku. Viz [Ověření přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Povolte ověřování ve své aplikaci Azure funkce a funkce pomocí následujících kroků:

   * [Nastavení anonymního ověřování ve funkci](#set-authentication-function-app)
   * [Nastavení ověřování Azure AD ve funkční aplikaci](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Nastavení anonymního ověřování ve funkci

Chcete-li použít spravovanou identitu aplikace logiky ve funkci Azure, nastavili jste úroveň ověřování vaší funkce na anonymní. V opačném případě aplikace logiky vyvolá chybu "BadRequest".

1. Na [webu Azure Portal](https://portal.azure.com)najděte a vyberte aplikaci funkcí. Tyto kroky používají "FabrikamFunctionApp" jako ukázkovou aplikaci funkce.

1. V podokně aplikací funkcí vyberte **funkce platformy**. V části **Vývojové nástroje**vyberte Rozšířené **nástroje (Kudu).**

   ![Otevřít pokročilé nástroje pro Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. Na záhlaví webu Kudu v yberte v nabídce **Konzola ladění** v yberte **CMD**.

   ![Z nabídky ladicí konzoly vyberte možnost "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Po zobrazení další stránky vyberte ze seznamu složek **web** > **wwwroot** > *your-function*. Tyto kroky používají "FabrikamAzureFunction" jako příklad funkce.

   ![Vyberte "site" > "wwwroot" > své funkce](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Otevřete `function.json` soubor pro úpravy.

   ![Klikněte na upravit soubor "function.json"](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. V `bindings` objektu zkontrolujte, zda `authLevel` vlastnost existuje. Pokud vlastnost existuje, nastavte hodnotu `anonymous`vlastnosti na . V opačném případě přidejte tuto vlastnost a nastavte hodnotu.

   ![Přidejte vlastnost authLevel a nastavte na "anonymní"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Až budete hotovi, uložte nastavení a pokračujte k další části.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Nastavení ověřování Azure AD pro vaši funkční aplikaci

Před zahájením tohoto úkolu najděte a odložte tyto hodnoty stranou pro pozdější použití:

* ID objektu, který je generován pro systémově přiřazenou identitu, která představuje vaši aplikaci logiky

  * Chcete-li generovat toto ID objektu, [povolte systémově přiřazenou identitu aplikace logiky](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * V opačném případě chcete-li najít toto ID objektu, otevřete aplikaci logiky v Návrháři aplikace logiky. V nabídce aplikace logiky vyberte v části **Nastavení** **položku Identity** > **System assigned**.

* ID adresáře pro vašeho tenanta ve službě Azure Active Directory (Azure AD)

  Chcete-li získat ID adresáře vašeho [`Get-AzureAccount`](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureaccount) tenanta, můžete spustit příkaz Powershellu. Nebo na webu Azure Portal postupujte takto:

  1. Na [webu Azure Portal](https://portal.azure.com)najděte a vyberte aplikaci funkcí.

  1. Najděte a vyberte svého klienta Azure AD. Tyto kroky používají "Fabrikam" jako ukázkový tenant.

  1. V nabídce klienta vyberte v části **Spravovat** **položku Vlastnosti**.

  1. Zkopírujte například ID adresáře klienta a toto ID uložte pro pozdější použití.

     ![Vyhledání a zkopírování ID adresáře klienta Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* ID prostředku pro cílový prostředek, ke kterému chcete získat přístup

  * Pokud chcete najít tato ID prostředků, projděte si [služby Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Toto ID prostředku musí přesně odpovídat hodnotě, kterou azure ad očekává, včetně všech požadovaných koncových lomítka.

  Toto ID prostředku je také stejná hodnota, kterou později použijete ve **vlastnosti Okruh uživatelů** při [nastavování akce funkce pro použití systémově přiřazené identity](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Teď jste připraveni nastavit ověřování Azure AD pro vaši funkční aplikaci.

1. Na [webu Azure Portal](https://portal.azure.com)najděte a vyberte aplikaci funkcí.

1. V podokně aplikací funkcí vyberte **funkce platformy**. V části **Networking**vyberte **Ověřování / Autorizace**.

   ![Zobrazení nastavení ověřování a autorizace](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Změňte nastavení **Ověřování služby App Service** na **Zapnuto**. V **akci, která má být v případě, že požadavek není ověřen,** vyberte **Přihlásit se pomocí služby Azure Active Directory**. V části **Zprostředkovatelé ověřování**vyberte **službu Azure Active Directory**.

   ![Zapnutí ověřování pomocí Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. V podokně **Nastavení služby Azure Active Directory** postupujte takto:

   1. Nastavte **režim správy** na **upřesnit**.

   1. Ve vlastnosti **ID klienta** zadejte ID objektu pro systémově přiřazenou identitu aplikace logiky.

   1. Ve vlastnosti Adresa URL `https://sts.windows.net/` **vystavitela** zadejte adresu URL a přidejte ID adresáře klienta Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Ve vlastnosti **Povolené cílové skupiny tokenů** zadejte ID prostředku cílového prostředku, ke kterému chcete získat přístup.

      Toto ID prostředku je stejná hodnota, kterou později použijete ve **vlastnosti Okruh uživatelů** při [nastavování akce funkce pro použití systémově přiřazené identity](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   V tomto okamžiku vaše verze vypadá podobně jako v tomto příkladu:

   ![Nastavení ověřování služby Azure Active Directory](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Až to bude hotové, vyberte **OK**.

1. Vraťte se do návrháře aplikace logiky a postupujte [podle pokynů k ověření přístupu pomocí spravované identity](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech Logic Apps](../connectors/apis-list.md)
