---
title: Přidání a volání funkcí z Azure Logic Apps
description: Pomocí automatizovaných úloh a pracovních postupů v Azure Logic Apps volejte a spouštějte vlastní kód ve funkcích provedených v Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-js
ms.openlocfilehash: 7df9f7d072af7c5f6523fd1be0432ce51954fa10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791875"
---
# <a name="call-functions-from-azure-logic-apps"></a>Volat funkce z Azure Logic Apps

Pokud chcete spustit kód, který provádí určitou úlohu ve svých aplikacích logiky, můžete vytvořit vlastní funkci pomocí [Azure Functions](../azure-functions/functions-overview.md). Tato služba pomáhá vytvářet funkce Node.js, C# a F#, takže nemusíte vytvářet kompletní aplikaci nebo infrastrukturu pro spouštění kódu. Můžete také [volat Logic Apps z uvnitř funkcí](#call-logic-app). Azure Functions poskytuje výpočetní prostředí bez serveru v cloudu a je užitečné pro provádění úloh, jako jsou tyto příklady:

* Rozšíří chování aplikace logiky pomocí funkcí v Node.js nebo C#.
* V pracovním postupu aplikace logiky proveďte výpočty.
* Použijte Rozšířené formátování nebo výpočetní pole ve vašich aplikacích logiky.

Chcete-li spustit fragmenty kódu bez použití Azure Functions, přečtěte si, jak [Přidat a spustit vložený kód](../logic-apps/logic-apps-add-run-inline-code.md).

> [!NOTE]
> Integrace mezi Logic Apps a Azure Functions v současnosti nefunguje s povolenými sloty.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace Function App, která je kontejnerem pro funkci vytvořenou v Azure Functions společně s funkcí, kterou vytvoříte. Pokud nemáte aplikaci Function App, [vytvořte nejdřív aplikaci Function App](../azure-functions/functions-get-started.md). Pak můžete vytvořit svou funkci buď mimo vaši aplikaci logiky, v Azure Portal, nebo [z aplikace logiky](#create-function-designer) v návrháři aplikace logiky.

* Při práci s Logic Apps platí stejné požadavky i pro aplikace Function App a funkce bez ohledu na to, jestli jsou existující nebo nové:

  * Aplikace Function App a aplikace logiky musí používat stejné předplatné Azure.

  * Nové aplikace funkcí musí jako zásobník modulu runtime používat rozhraní .NET nebo JavaScript. Když přidáte novou funkci do existujících aplikací Function App, můžete vybrat buď C#, nebo JavaScript.

  * Vaše funkce používá šablonu **triggeru http** .

    Šablona triggeru HTTP může přijmout obsah, který je `application/json` typu z vaší aplikace logiky. Když do aplikace logiky přidáte funkci, návrhář aplikace logiky zobrazí vlastní funkce, které jsou vytvořeny z této šablony v rámci vašeho předplatného Azure.

  * Vaše funkce nepoužívá vlastní trasy, pokud jste nedefinovali [definici openapi](../azure-functions/functions-openapi-definition.md) (dříve označovanou jako [soubor Swagger](https://swagger.io/)).

  * Pokud máte definici OpenAPI pro vaši funkci, Návrhář Logic Apps poskytuje bohatší možnosti při práci s parametry funkcí. Aby mohla aplikace logiky najít a získat přístup k funkcím, které mají definice OpenAPI, [nastavte aplikaci Function App pomocí následujících kroků](#function-swagger).

* Aplikace logiky, do které chcete přidat funkci, včetně [triggeru](../logic-apps/logic-apps-overview.md#logic-app-concepts) jako prvního kroku v aplikaci logiky

  Než budete moct přidat akce, které spouštějí funkce, musí vaše aplikace logiky začínat triggerem. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>Najít funkce, které mají popisy OpenAPI

Pro rozsáhlejší prostředí při práci s parametry funkcí v Návrháři Logic Apps [vygenerujte definici openapi](../azure-functions/functions-openapi-definition.md), dříve označovanou jako [soubor Swagger](https://swagger.io/), pro vaši funkci. Chcete-li nastavit aplikaci Function App, aby mohla aplikace logiky najít a používat funkce, které obsahují popisy Swagger, postupujte podle následujících kroků:

1. Ujistěte se, že je vaše aplikace Function App aktivně spuštěná.

1. Ve vaší aplikaci Function App nastavte [sdílení prostředků mezi zdroji (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) , aby se všechny zdroje povolovaly pomocí následujících kroků:

   1. V seznamu **aplikace Function** App vyberte svou aplikaci Function App. V pravém podokně vyberte **funkce platformy**  >  **CORS**.

      ![Výběr aplikace Function App > "funkce platformy" > "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. V části **CORS** přidejte zástupný znak hvězdičky ( **`*`** ), ale odeberte všechny ostatní počáteky v seznamu a vyberte **Uložit**.

      ![Nastavte CORS * na zástupný znak *.](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>Přístup k hodnotám vlastností v rámci požadavků HTTP

Funkce Webhooku můžou přijímat požadavky HTTP jako vstupy a předávat tyto požadavky jiným funkcím. Například i když Logic Apps obsahuje [funkce, které převádějí hodnoty DateTime](../logic-apps/workflow-definition-language-functions-reference.md), tato základní ukázková funkce JavaScriptu ukazuje, jak můžete přistupovat k vlastnosti uvnitř objektu Request, který je předán funkci a provádět operace s hodnotou této vlastnosti. Chcete-li získat přístup k vlastnostem uvnitř objektů, v tomto příkladu se používá [operátor tečka (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors):

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Tady je to, co se děje uvnitř této funkce:

1. Funkce vytvoří `data` proměnnou a přiřadí `body` objekt uvnitř `request` objektu k této proměnné. Funkce používá operátor tečka (.) pro odkazování na `body` objekt uvnitř `request` objektu:

   ```javascript
   var data = request.body;
   ```

1. Funkce teď může přistupovat k `date` vlastnosti přes `data` proměnnou a převést tuto hodnotu vlastnosti z typu DateTime na typ DateString voláním `ToDateString()` funkce. Funkce také vrátí výsledek prostřednictvím `body` vlastnosti v odpovědi na funkci:

   ```javascript
   body: data.date.ToDateString();
   ```

Teď, když jste vytvořili funkci v Azure, postupujte podle pokynů pro [Přidání funkcí do Logic Apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Vytváření funkcí v Logic Apps

Můžete vytvořit funkce přímo z pracovního postupu aplikace logiky pomocí předdefinované Azure Functions akce v návrháři aplikace logiky, ale tuto metodu lze použít pouze pro funkce napsané v jazyce JavaScript. Pro jiné jazyky můžete vytvářet funkce prostřednictvím Azure Functionsho prostředí v Azure Portal. Další informace najdete v tématu [Vytvoření první funkce v Azure Portal](../azure-functions/functions-get-started.md).

Než ale budete moct vytvořit funkci v Azure, musíte už mít aplikaci funkcí, což je kontejner pro vaše funkce. Pokud nemáte aplikaci Function App, vytvořte nejprve tuto aplikaci Function App. Další informace najdete [v tématu Vytvoření první funkce v Azure Portal](../azure-functions/functions-get-started.md).

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. Pokud chcete vytvořit a přidat funkci, postupujte podle kroků, které se vztahují k vašemu scénáři:

   * V posledním kroku pracovního postupu aplikace logiky vyberte **Nový krok**.

   * Mezi stávajícími kroky v pracovním postupu vaší aplikace logiky přesuňte ukazatel myši na šipku, vyberte znaménko plus (+) a pak vyberte **přidat akci**.

1. Do vyhledávacího pole zadejte jako filtr "Azure Functions". V seznamu akce vyberte akci **Azure Function** , například:

   ![Najít funkce v Azure Portal.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. V seznamu aplikace Function App vyberte svou aplikaci Function App. Po otevření seznamu akcí vyberte tuto akci: **vytvořit novou funkci**.

   ![Výběr aplikace Function App](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. V editoru definic funkcí definujte funkci:

   1. Do pole **název funkce** zadejte název funkce.

   1. V poli **kód** přidejte svůj kód do šablony funkce, včetně odpovědi a datové části, kterou chcete vrátit do vaší aplikace logiky poté, co byla funkce dokončena. Po dokončení vyberte **Vytvořit**.

   Například:

   ![Definovat funkci](./media/logic-apps-azure-functions/add-code-function-definition.png)

   V kódu šablony *`context` objekt* odkazuje na zprávu, kterou aplikace logiky posílá v pozdějším kroku v poli **Text žádosti** . Chcete-li získat přístup k `context` vlastnostem objektu zevnitř vaší funkce, použijte tuto syntaxi:

   `context.body.<property-name>`

   Například pro odkazování na `content` vlastnost uvnitř `context` objektu použijte tuto syntaxi:

   `context.body.content`

   Kód šablony obsahuje také `input` proměnnou, která ukládá hodnotu z `data` parametru, aby vaše funkce mohla provádět operace s touto hodnotou. V rámci funkcí JavaScriptu `data` je proměnná také zástupcem pro `context.body` .

   > [!NOTE]
   > Tato `body` vlastnost se vztahuje na `context` objekt a není shodná s tokenem **těla** z výstupu akce, který může být také předána vaší funkci.

1. Do pole **Text žádosti** zadejte vstup funkce, který musí být naformátován jako objekt JavaScript Object Notation (JSON).

   Tento vstup je *kontextový objekt* nebo zpráva, kterou vaše aplikace logiky posílá do vaší funkce. Když kliknete na pole **Text žádosti** , zobrazí se seznam dynamického obsahu, ve kterém můžete vybrat tokeny pro výstupy z předchozích kroků. Tento příklad určuje, že datová část kontextu obsahuje vlastnost s názvem `content` , která má hodnotu **od** tokenu z triggeru e-mailu.

   !["Tělo žádosti" – příklad – datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   V tomto případě objekt kontextu není přetypování jako řetězec, takže obsah objektu se přidá přímo do datové části JSON. Nicméně, pokud objekt kontextu není token JSON, který předává řetězec, objekt JSON nebo pole JSON, zobrazí se chyba. Takže pokud v tomto příkladu jste místo toho použili token **přijatého času** , můžete objekt kontextu přetypovat jako řetězec přidáním dvojitých uvozovek.

   ![Přetypování objektu jako řetězce](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Chcete-li zadat další podrobnosti, jako je například metoda, která má být použita, hlavičky požadavku nebo parametry dotazu nebo ověřování, otevřete seznam **Přidat nový parametr** a vyberte požadované možnosti. Pro ověřování se vaše možnosti liší v závislosti na vybrané funkci. Viz [Povolení ověřování pro funkce](#enable-authentication-functions).

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Přidání existujících funkcí do Logic Apps

Chcete-li volat existující funkce z aplikací logiky, můžete v návrháři aplikace logiky přidat funkce jako jakoukoli jinou akci.

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. V kroku, kam chcete funkci přidat, vyberte **Nový krok**.

1. V části **Vybrat akci** do vyhledávacího pole zadejte jako filtr "Azure Functions". V seznamu akce vyberte akci **použít funkci Azure** .

   ![Najděte funkci v Azure.](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. V seznamu aplikace Function App vyberte svou aplikaci Function App. Jakmile se zobrazí seznam Functions, vyberte svoji funkci.

   ![Výběr aplikace Function App a funkce](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Pro funkce, které mají definice rozhraní API (popisy Swagger) a jsou [nastavené tak, aby vaše aplikace logiky mohla najít a přistupovat k těmto funkcím](#function-swagger), můžete vybrat **Akce Swagger**.

   ![Výběr aplikace Function App, akcí Swagger a funkce](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. Do pole **Text žádosti** zadejte vstup funkce, který musí být naformátován jako objekt JavaScript Object Notation (JSON).

   Tento vstup je *kontextový objekt* nebo zpráva, kterou vaše aplikace logiky posílá do vaší funkce. Když kliknete na pole **Text žádosti** , zobrazí se seznam dynamického obsahu, ve kterém můžete vybrat tokeny pro výstupy z předchozích kroků. Tento příklad určuje, že datová část kontextu obsahuje vlastnost s názvem `content` , která má hodnotu **od** tokenu z triggeru e-mailu.

   !["Tělo žádosti" – příklad – datová část objektu kontextu](./media/logic-apps-azure-functions/function-request-body-example.png)

   V tomto případě objekt kontextu není přetypování jako řetězec, takže obsah objektu se přidá přímo do datové části JSON. Nicméně, pokud objekt kontextu není token JSON, který předává řetězec, objekt JSON nebo pole JSON, zobrazí se chyba. Takže pokud v tomto příkladu jste místo toho použili token **přijatého času** , můžete objekt kontextu přetypovat jako řetězec přidáním dvojitých uvozovek:

   ![Přetypování objektu jako řetězce](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. Chcete-li zadat další podrobnosti, jako je například metoda, která má být použita, hlavičky požadavku, parametry dotazu nebo ověřování, otevřete seznam **Přidat nový parametr** a vyberte požadované možnosti. Pro ověřování se vaše možnosti liší v závislosti na vybrané funkci. Viz [Povolení ověřování ve funkcích](#enable-authentication-functions).

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Volání aplikací logiky z funkcí

Pokud chcete spustit aplikaci logiky zevnitř funkce, musí aplikace logiky začínat triggerem, který poskytuje k dispozici koncový bod. Můžete například spustit aplikaci logiky s triggerem **http**, **Request**, **front Azure** nebo aktivační proceduru **Event Grid** . V rámci funkce odešlete požadavek HTTP POST do adresy URL triggeru a zahrňte datovou část, kterou má aplikace logiky zpracovat. Další informace najdete v tématu [volání, Trigger nebo vnořování Logic Apps](../logic-apps/logic-apps-http-endpoint.md).

<a name="enable-authentication-functions"></a>

## <a name="enable-authentication-for-functions"></a>Povolení ověřování pro funkce

Abyste mohli snadno ověřit přístup k jiným prostředkům, které jsou chráněné službou Azure Active Directory (Azure AD), aniž byste se museli přihlašovat a poskytovat přihlašovací údaje nebo tajné kódy, může vaše aplikace logiky používat [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) (dřív označovanou jako identita spravované služby nebo MSI). Azure tuto identitu spravuje za vás a pomáhá zabezpečit vaše přihlašovací údaje, protože nemusíte zadávat ani vyměňovat tajné kódy. Přečtěte si další informace o [službách Azure, které podporují spravované identity pro ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Pokud nastavíte aplikaci logiky tak, aby používala identitu přiřazenou systémem, nebo ručně vytvořenou identitu přiřazenou uživatelem, funkce ve vaší aplikaci logiky může použít stejnou identitu pro ověřování. Další informace o podpoře ověřování funkcí v Logic Apps najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

Pokud chcete nastavit a používat spravovanou identitu s vaší funkcí, postupujte podle těchto kroků:

1. Povolte spravovanou identitu ve vaší aplikaci logiky a nastavte přístup identity k cílovému prostředku. Informace najdete [v tématu ověření přístupu k prostředkům Azure pomocí spravovaných identit v Azure Logic Apps](../logic-apps/create-managed-service-identity.md).

1. Pomocí následujících kroků povolte ověřování v aplikaci Function a Function App:

   * [Nastavení anonymního ověřování ve vaší funkci](#set-authentication-function-app)
   * [Nastavení ověřování Azure AD ve vaší aplikaci Function App](#set-azure-ad-authentication)

<a name="set-authentication-function-app"></a>

### <a name="set-up-anonymous-authentication-in-your-function"></a>Nastavení anonymního ověřování ve vaší funkci

Pokud chcete ve své funkci použít spravovanou identitu vaší aplikace logiky, musíte nastavit úroveň ověřování vaší funkce na anonymní. V opačném případě vaše aplikace logiky vyvolá chybu "důvodu chybného požadavku".

1. V [Azure Portal](https://portal.azure.com)Najděte aplikaci Function App a vyberte ji. Tyto kroky jako ukázkovou aplikaci funkcí používají "FabrikamFunctionApp".

1. V podokně aplikace funkcí vyberte **funkce platformy**. V části **vývojové nástroje** vyberte **Rozšířené nástroje (Kudu)**.

   ![Otevřít rozšířené nástroje pro Kudu](./media/logic-apps-azure-functions/open-advanced-tools-kudu.png)

1. V záhlaví webu Kudu v nabídce **Konzola ladění** vyberte **cmd**.

   ![V nabídce Konzola ladění vyberte možnost "CMD"](./media/logic-apps-azure-functions/open-debug-console-kudu.png)

1. Po zobrazení další stránky vyberte v seznamu složka možnost **lokalita**  >  **wwwroot**  >  *vaše funkce*. Tyto kroky jako ukázkovou funkci používají "FabrikamAzureFunction".

   ![Vyberte "Web" > "wwwroot" > funkci](./media/logic-apps-azure-functions/select-site-wwwroot-function-folder.png)

1. Otevřete `function.json` soubor pro úpravy.

   ![Pro soubor function.json klikněte na Upravit.](./media/logic-apps-azure-functions/edit-function-json-file.png)

1. V `bindings` objektu ověřte, zda `authLevel` vlastnost existuje. Pokud vlastnost existuje, nastavte hodnotu vlastnosti na `anonymous` . V opačném případě přidejte tuto vlastnost a nastavte hodnotu.

   ![Přidat vlastnost "authLevel" a nastavit na "anonymous"](./media/logic-apps-azure-functions/set-authentication-level-function-app.png)

1. Až budete hotovi, uložte nastavení a potom pokračujte k další části.

<a name="set-azure-ad-authentication"></a>

### <a name="set-up-azure-ad-authentication-for-your-function-app"></a>Nastavení ověřování Azure AD pro aplikaci Function App

Před zahájením této úlohy Najděte a uložte tyto hodnoty pro pozdější použití:

* ID objektu, které je vygenerováno pro identitu přiřazenou systémem, která představuje vaši aplikaci logiky

  * Pokud chcete vygenerovat toto ID objektu, [Povolte identitu přiřazenou systému vaší aplikace logiky](../logic-apps/create-managed-service-identity.md#azure-portal-system-logic-app).

  * V opačném případě pro vyhledání tohoto ID objektu otevřete aplikaci logiky v návrháři aplikace logiky. V nabídce aplikace logiky v části **Nastavení** vyberte   >  **přiřazený systém** identit.

* ID adresáře pro vašeho tenanta ve službě Azure Active Directory (Azure AD)

  Pokud chcete získat ID adresáře vašeho tenanta, můžete spustit [`Get-AzureAccount`](/powershell/module/servicemanagement/azure.service/get-azureaccount) příkaz prostředí PowerShell. Nebo v Azure Portal postupujte podle následujících kroků:

  1. V [Azure Portal](https://portal.azure.com)Najděte aplikaci Function App a vyberte ji.

  1. Vyhledejte a vyberte svého tenanta služby Azure AD. Tyto kroky používají jako ukázkového tenanta "fabrikam".

  1. V nabídce tenanta v části **Spravovat** vyberte **vlastnosti**.

  1. Zkopírujte ID adresáře vašeho tenanta, například a uložte toto ID pro pozdější použití.

     ![Vyhledání a zkopírování ID adresáře tenanta Azure AD](./media/logic-apps-azure-functions/azure-active-directory-tenant-id.png)

* ID prostředku pro cílový prostředek, ke kterému chcete získat přístup

  * Pokud chcete najít Tato ID prostředků, Projděte si [služby Azure, které podporují Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

  > [!IMPORTANT]
  > Toto ID prostředku musí přesně odpovídat hodnotě, kterou očekává služba Azure AD, včetně všech požadovaných koncových lomítek.

  Toto ID prostředku je zároveň stejná hodnota, kterou později použijete ve vlastnosti **cílové skupiny** při [nastavení akce funkce pro použití identity přiřazené systémem](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

Teď jste připraveni nastavit ověřování Azure AD pro aplikaci Function App.

1. V [Azure Portal](https://portal.azure.com)Najděte aplikaci Function App a vyberte ji.

1. V podokně aplikace funkcí vyberte **funkce platformy**. V části **síť** vyberte **ověřování/autorizace**.

   ![Zobrazit nastavení ověřování a autorizace](./media/logic-apps-azure-functions/view-authentication-authorization-settings.png)

1. Změňte nastavení **ověřování App Service** na **zapnuto**. V seznamu **akce, která se má provést, když není ověřená žádost** vyberte možnost **přihlásit se pomocí Azure Active Directory**. V části **Zprostředkovatelé ověřování** vyberte **Azure Active Directory**.

   ![Zapnutí ověřování pomocí Azure AD](./media/logic-apps-azure-functions/turn-on-authentication-azure-active-directory.png)

1. V podokně **nastavení Azure Active Directory** postupujte podle následujících kroků:

   1. Nastavte **režim správy** na **Upřesnit**.

   1. Do vlastnosti **ID klienta** zadejte ID objektu pro identitu přiřazenou systému vaší aplikace logiky.

   1. Do vlastnosti **Adresa URL vystavitele** zadejte `https://sts.windows.net/` adresu URL a přidejte ID adresáře vašeho tenanta Azure AD.

      `https://sts.windows.net/<Azure-AD-tenant-directory-ID>`

   1. Do vlastnosti **Povolit cílové skupiny tokenu** zadejte ID prostředku pro cílový prostředek, ke kterému chcete získat přístup.

      Toto ID prostředku je stejná jako hodnota, kterou později použijete ve vlastnosti **cílová skupina** při [nastavení akce funkce pro použití identity přiřazené systémem](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

   V tomto okamžiku vaše verze vypadá podobně jako v tomto příkladu:

   ![Nastavení ověřování Azure Active Directory](./media/logic-apps-azure-functions/azure-active-directory-authentication-settings.png)

1. Až to bude hotové, vyberte **OK**.

1. Vraťte se do návrháře aplikace logiky a postupujte podle [pokynů pro ověření přístupu ke spravované identitě](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity).

## <a name="next-steps"></a>Další kroky

* Další informace o [konektorech Logic Apps](../connectors/apis-list.md)