---
title: 'Kurz: použití rozhraní REST API'
description: V tomto kurzu se dozvíte, jak používat rozhraní REST API pro Azure dosah k přístupu k obsahu vašeho katalogu.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951239"
---
# <a name="tutorial-use-the-rest-apis"></a>Kurz: použití rozhraní REST API

V tomto kurzu se naučíte používat rozhraní REST API pro Azure dosah. Každý, kdo chce odesílat data do katalogu Azure dosah, zahrnout katalog jako součást automatizovaného procesu nebo vytvořit vlastní uživatelské prostředí v katalogu, můžete k tomu použít rozhraní REST API.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvoření instančního objektu (aplikace).
> * Nakonfigurujte svůj katalog tak, aby důvěřoval instančnímu objektu (aplikaci).
> * Podívejte se na dokumentaci rozhraní REST API.
> * Shromážděte potřebné hodnoty pro použití rozhraní REST API.
> * Použijte tohoto klienta pro volání rozhraní REST API.
> * Vygenerujte klienta .NET pro volání rozhraní REST API.

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="prerequisites"></a>Požadavky

* Abyste mohli začít, musíte mít existující účet Azure dosah. Pokud nemáte katalog, přečtěte si [rychlý Start pro vytvoření účtu Azure dosah](create-catalog-portal.md).

* Pokud potřebujete do svého katalogu přidat data, přečtěte si [kurz spuštění Starter Kit a data skenování](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="create-a-service-principal-application"></a>Vytvoření instančního objektu (aplikace)

Aby klient služby REST API mohl získat přístup ke katalogu, klient musí mít instanční objekt (aplikace) a identitu, kterou katalog rozpozná a je nakonfigurován tak, aby důvěřoval. Když v katalogu provedete REST API volání, použijí identitu instančního objektu.

Zákazníci, kteří použili existující instanční objekty (ID aplikací), měli vysokou míru selhání. Proto doporučujeme vytvořit nový instanční objekt pro volání rozhraní API.

Vytvoření nového instančního objektu:

1. Z [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Azure Active Directory**.
1. Na stránce **Azure Active Directory** v levém podokně vyberte **Registrace aplikací** .
1. Vyberte **Nová registrace**.
1. Na stránce **zaregistrovat aplikaci** :
    1. Zadejte **název** aplikace (hlavní název služby).
    1. Vyberte **účty jenom v tomto organizačním adresáři _&lt; &gt;_ (jenom u jednoho tenanta)**.
    1. Pro **identifikátor URI přesměrování (volitelné)** vyberte **Web** a zadejte hodnotu. Tato hodnota nemusí být platný identifikátor URI.
    1. Vyberte **Zaregistrovat**.
1. Na stránce nový instanční objekt zkopírujte hodnoty **zobrazovaného názvu** a **ID aplikace (klienta)** , které chcete uložit pro pozdější použití.

   ID aplikace je `client_id` hodnota v ukázkovém kódu.

Chcete-li použít instanční objekt (aplikace), musíte získat jeho heslo. Jak na to:

1. Z Azure Portal vyhledejte a vyberte **Azure Active Directory** a v levém podokně vyberte **Registrace aplikací** .
1. V seznamu vyberte objekt služby (aplikace).
1. V levém podokně vyberte **certifikáty & tajných** kódů.
1. Vyberte **Nový tajný klíč klienta**.
1. Na stránce **Přidat tajný klíč klienta** zadejte **Popis**, vyberte časový limit vypršení **platnosti a** potom vyberte **Přidat**.

   Na stránce **tajné klíče klienta** je řetězec ve sloupci **hodnota** nového tajného klíče vaším heslem. Uložte tuto hodnotu.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Snímek obrazovky zobrazující tajný klíč klienta":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Konfigurace služby Catalog pro důvěřování instančnímu objektu (aplikace)

Konfigurace Azure dosah, aby důvěřovala vašemu novému instančnímu objektu:

1. Přejděte k účtu dosah.

1. Na stránce **dosah účet** vyberte možnost **řízení přístupu (IAM)** na kartě.

1. Klikněte na **+ Přidat** .

1. Vyberte **Přidat přiřazení role** .

1. Pro typ role v **dosah data kurátor**

    > [!Note]
    > Další informace o oprávněních Data Catalog Azure dosah najdete v tématu [oprávnění katalogu](catalog-permissions.md). Například pokud potřebujete oprávnění ke spuštění kontroly, musí být typ role **dosah správce zdrojů dat**.

1. Pro **přiřazení přístupu k** ponechání výchozího, **uživatelského, skupinového nebo instančního objektu**

1. V poli **Vyberte** zadejte název Previosly vytvořeného objektu služby, který chcete přiřadit, a potom v podokně výsledků klikněte na jeho název.

1. Klikněte na **Uložit**.

Nyní jste nakonfigurovali instanční objekt jako správce aplikace, který umožňuje odesílat obsah do katalogu.

## <a name="view-the-rest-apis-documentation"></a>Zobrazit dokumentaci rozhraní REST API

Pokud chcete zobrazit dokumentaci k Swagger API, Stáhněte si [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip), extrahujte své soubory a otevřete index.html.

Pokud se chcete dozvědět víc o rozhraní API pro rozšířené vyhledávání a návrhy, které poskytuje Azure dosah, přečtěte si dokumentaci k vyhledávacímu filtru REST API. Klient vygenerovaný AutoRest v současné době nepodporuje přizpůsobené parametry hledání. Pro alternativní řešení použijte k definování tříd filtru v kódu jako parametrů volání rozhraní API dokument vyhledávacího filtru. V dokumentu index.html jsou příklady těchto rozhraní API.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>Shromažďování potřebných hodnot pro použití rozhraní REST API

Vyhledejte a uložte následující hodnoty:

* ID klienta:
  * V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Azure Active Directory**.
  * V levém podokně vyberte v části **Spravovat** možnost **vlastnosti**, najděte **ID tenanta** a potom vyberte ikonu **Kopírovat do schránky** a uložte její hodnotu.
* Koncový bod aplikace Atlas:
  * Na [stránce účty Azure dosah](https://aka.ms/purviewportal) v Azure Portal vyhledejte a vyberte svůj účet Azure dosah v seznamu.
  * Vyberte **vlastnosti**, najděte **koncový bod aplikace Atlas** a potom kliknutím na ikonu **Kopírovat do schránky** uložte její hodnotu. Odeberte část *https://* řetězce při pozdějším použití.
* Název účtu:
  * Extrahujte název vašeho katalogu z řetězce koncového bodu Atlas. Pokud je například koncový bod aplikace Atlas `https://ThisIsMyCatalog.catalog.purview.azure.com` , je název vašeho účtu `ThisIsMyCatalog` .

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>K volání rozhraní REST API použijte klienta pro odesílání

1. Nainstalujte [klienta post](https://www.getpostman.com/).
1. Z klienta vyberte **importovat** a použijte [Test.postman_collection.jsna](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Vyberte **kolekce** a pak vyberte **test**.
1. Vyberte **získat token**:
    1. V adrese URL vedle příspěvku nahraďte identifikátor *&lt; -tenant-ID &gt;* ID tenanta, které jste zkopírovali v předchozí části.
    1. Vyberte kartu **tělo** a nahraďte zástupné symboly v cestě a textu z předchozího kroku.

       Po výběru **Odeslat** text odpovědi obsahuje strukturu JSON, včetně názvu *access_token* a hodnoty řetězce v uvozovkách. 
    1. Zkopírujte hodnotu tokenu nosiče (bez uvozovek), která se použije v dalším kroku.

1. Vyberte **/v2/Types/Typedefs**:
    1. Zástupný symbol v cestě nahraďte hodnotou koncového bodu Atlas. Tuto hodnotu můžete získat tak, že přejdete na instanci katalogu na portálu Ibiza a kliknete na přehled. 

       Nosný token se nastavuje v prvním kroku (nebo ho můžete zkopírovat ručně na kartě autorizace).

    1. Pro získání odpovědi vyberte **Odeslat** .

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>Vygenerování klienta .NET pro volání rozhraní REST API

### <a name="install-autorest"></a>Nainstalovat AutoRest



1. [Nainstalujte Node.js](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md).
1. Otevřete PowerShell a spusťte následující příkaz:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>Stažení rest-api-specs.zip a vytvoření klienta

1. Stáhněte si [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) a extrahujte jeho soubory.
1. Spusťte následující příkaz v PowerShellu z extrahované složky REST-API-SPECS:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   Výstup tohoto procesu vytvoří složku PurviewCatalogClient a podsložce CSharp ve složce REST-API-SPECS.

### <a name="create-a-sample-net-console-application"></a>Vytvoření ukázkové konzolové aplikace .NET

1. Otevřete Visual Studio 2019. Tyto pokyny byly testovány pomocí bezplatné edice Community.
1. Na stránce **vytvořit nový projekt** vytvořte projekt **Konzolová aplikace (.NET Core)** v jazyce C#.
1. Zkopírujte a vložte poskytnutý [vzorový kód](#sample-code-for-the-console-application).
1. Hodnoty *account*. *servicePrincipalId*, *servicePrincipalKey* a *tenantId* nahraďte hodnotami, které jste dříve shromáždili.
1. Pomocí **Průzkumník řešení** přidejte složku s názvem **vygenerovanou** v projektu sady Visual Studio.
1. Zkopírujte složku rest-api-specs\PurviewCatalogClient\CSharp, kterou jste vytvořili dříve, do složky \Generated. Použijte Průzkumníka souborů nebo příkazový řádek pro operaci kopírování, která spustí Visual Studio k automatickému přidání souborů do projektu.
1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. Vyberte kartu **Procházet** . Vyhledejte a vyberte **Microsoft. REST. ClientRuntime**.
1. Ujistěte se, že je verze minimálně 2.3.21, a pak vyberte **nainstalovat**.
1. Sestavte a spusťte aplikaci.

Vzorový kód vrátí počet definice typedef v katalogu a ukazuje, jak zpracovávat přiřazení rolí. Podrobnosti naleznete v části `DoRoleAssignmentOperations()` v ukázkovém kódu. Další informace o projektu naleznete v tématu [nastavení projektu](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md).

### <a name="sample-code-for-the-console-application"></a>Vzorový kód pro konzolovou aplikaci

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa zdrojů dat](manage-data-sources.md)
