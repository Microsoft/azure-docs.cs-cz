---
title: Rozhraní API Azure FarmBeats
description: Přečtěte si o rozhraních API Azure FarmBeats, která poskytují zemědělským firmám standardizované rozhraní RESTful s odpověďmi založenými na JSON.
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: f15bee7e802b04d04a3c87d7f84fc975b88bf260
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86536568"
---
# <a name="azure-farmbeats-apis"></a>Rozhraní API Azure FarmBeats

Tento článek popisuje rozhraní API služby Azure FarmBeats. Rozhraní API Azure FarmBeats poskytují zemědělským firmám standardizované rozhraní RESTful s odpověďmi založenými na standardu JSON, které vám pomůžou využít možnosti Azure FarmBeats, například:

- Rozhraní API pro získání senzorů, fotoaparátů, pomocí dronů, počasí, satelitních a spravovaných pozemních dat.
- Normalizace a kontext dat napříč společnými poskytovateli dat.
- Schematized přístup a možnosti dotazování pro všechna ingestovaná data.
- Automatické generování metadat, na které lze dotazovat, na základě agronomických funkcí.
- Automaticky generované agregace časových sekvencí pro rychlé vytváření modelů.
- Integrovaný modul Azure Data Factory, který umožňuje snadno vytvářet vlastní kanály zpracování dat.

## <a name="application-development"></a>Vývoj aplikací

Rozhraní API FarmBeats obsahují technickou dokumentaci k Swagger. Informace o všech rozhraních API a jejich odpovídajících požadavcích nebo odpovědích najdete v části [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

V následující tabulce najdete souhrn všech objektů a prostředků v FarmBeats DataHub:

| Objekty a prostředky | Description
--- | ---|
Sdílených | Farma odpovídá fyzickému umístění zájmu v rámci systému FarmBeats. Každá farma má název farmy a jedinečné ID farmy. |
Zařízení  | Zařízení odpovídá fyzickému zařízení, které je ve farmě přítomné. Každé zařízení má jedinečné ID zařízení. Zařízení je obvykle zřízené pro farmu s ID farmy.
DeviceModel  | DeviceModel odpovídá metadatům zařízení, jako je například výrobce a typ zařízení, který je buď bránou, nebo uzlem.
Elektrické  | Senzor odpovídá fyzickému senzoru, který zaznamenává hodnoty. Senzor se obvykle připojuje k zařízení s ID zařízení.
SensorModel  | SensorModel odpovídá metadatům snímače, jako je výrobce, typ snímače, který je buď analogový, nebo digitální, a měření snímače, například okolní teplota a tlak.
Telemetrie  | Telemetrie nabízí možnost číst zprávy telemetrie pro určitý senzor a časový rozsah.
Úloha  | Úloha odpovídá jakémukoli pracovnímu postupu aktivit, které jsou spuštěny v systému FarmBeats, aby získala požadovaný výstup. Každá úloha je přidružená k ID úlohy a typu úlohy.
JobType  | JobType odpovídá různým typům úloh, které systém podporuje. Jsou zahrnuty typy úloh definovaných systémem a uživatelem definovaných typů.
ExtendedType  | ExtendedType odpovídá seznamu typů systémem a uživatelem definovaných v systému. ExtendedType pomáhá nastavit nový senzor, scén nebo typ souboru scény v systému FarmBeats.
Partner  | Partner odpovídá partnerovi pro integraci senzorů a snímků pro FarmBeats.
Scény  | Scény odpovídá jakémukoli vygenerovanému výstupu v kontextu farmy. Každá scéna má ID scény, zdroj scény, typ scény a ID farmy, která je k němu přidružená. K jednotlivým ID scény může být přidruženo více souborů scény.
SceneFile |SceneFile odpovídá všem souborům, které jsou generovány pro jednu scénu. K jednomu ID scény může být přidruženo několik ID SceneFile.
Pravidlo  |Pravidlo odpovídá podmínkám pro data související s farmou, která aktivuje výstrahu. Každé pravidlo je v kontextu dat farmy.
Výstrahy  | Výstraha odpovídá oznámení, které se generuje při splnění podmínky pravidla. Každé upozornění je v kontextu pravidla.
Rutiny roledefinition  | Rutiny roledefinition definuje povolené a zakázané akce pro roli.
RoleAssignment  |RoleAssignment odpovídá přiřazování rolí uživateli nebo instančnímu objektu.

### <a name="data-format"></a>Formát dat

JSON je běžný jazyk nezávislý na jazyce, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace najdete na [webu JSON](https://www.json.org/).

## <a name="authentication-and-authorization"></a>Ověřování a autorizace

Požadavky HTTP na REST API jsou chráněny pomocí Azure Active Directory (Azure AD).
Aby bylo možné provést ověřený požadavek na rozhraní REST API, kód klienta vyžaduje ověření pomocí platných přihlašovacích údajů před voláním rozhraní API. Ověřování je sladěné mezi různými aktéry Azure AD. Poskytne klientovi přístupový token jako ověření platnosti ověřování. Token se pak pošle v hlavičce autorizace HTTP REST API požadavků. Další informace o ověřování Azure AD najdete v tématu [Azure Active Directory](https://portal.azure.com) pro vývojáře.

Přístupový token se musí poslat v následných požadavcích rozhraní API v části záhlaví jako:

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>Hlavičky požadavku HTTP

Tady jsou nejběžnější hlavičky požadavků, které je potřeba zadat při volání rozhraní API do Azure FarmBeats DataHub.


**Hlaviček** | **Popis a příklad**
--- | ---
Typ obsahu  | Formát požadavku (Content-Type: Application/ <format> ). Pro rozhraní API Azure FarmBeats DataHub je formát JSON. Content-Type: Application/JSON
Autorizace  | Určuje přístupový token potřebný k vytvoření volání rozhraní API. Autorizace: nosič <Access-Token>
Přijmout | Formát odpovědi. Pro rozhraní API Azure FarmBeats DataHub je formát JSON. Přijmout: Application/JSON

### <a name="api-requests"></a>Požadavky rozhraní API

Chcete-li vytvořit žádost o REST API, kombinujete metodu HTTP (GET, POST, PUT nebo DELETE), adresu URL služby API, identifikátor URI k prostředku, který se má dotazovat, odeslat data do, aktualizovat nebo odstranit a pak přidat jednu nebo více hlaviček požadavku HTTP.

Adresa URL služby API je vaše adresa URL DataHub, například https:// \<yourdatahub-website-name> . azurewebsites.NET.

Volitelně můžete zahrnout parametry dotazu pro volání funkce GET k filtrování, omezení velikosti a řazení dat v odpovědích.

Následující vzorová žádost slouží k získání seznamu zařízení:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Většina volání GET, POST a PUT vyžaduje tělo požadavku JSON.

Následující vzorový požadavek vytvoří zařízení. Tato žádost obsahuje JSON Input s textem žádosti.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parametry dotazů

V případě volání funkce REST můžete filtrovat, omezit velikost a seřadit data v odpovědi rozhraní API zahrnutím jednoho nebo více parametrů dotazu do identifikátoru URI požadavku. Parametry dotazu najdete v dokumentaci k rozhraní API a v jednotlivých voláních GET.
Například při dotazování na seznam zařízení (získat hovor v/Device) lze zadat následující parametry dotazu:

![Seznam zařízení](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Zpracování chyb

Rozhraní API služby Azure FarmBeats DataHub vrací standardní chyby protokolu HTTP. Nejběžnější kódy chyb jsou následující:

 |Kód chyby             | Description |
 |---                    | --- |
 |200                    | Success |
 |201                    | Vytvoření (post) úspěšné |
 |400                    | Chybný požadavek. V žádosti došlo k chybě. |
 |401                    | Přístupu. Volající rozhraní API nemá autorizaci pro přístup k prostředku. |
 |404                    | Prostředek se nenašel. |
 |5XX                    | Došlo k vnitřní chybě serveru. Kódy chyb začínající na 5XX znamená, že na serveru došlo k nějaké chybě. Další podrobnosti najdete v protokolech serveru a v následující části. |


Kromě standardních chyb HTTP vrátí rozhraní API služby Azure FarmBeats DataHub také interní chyby v následujícím formátu:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

V tomto příkladu se při vytvoření farmy ve vstupní datové části nezadalo povinné pole "název". Výsledná chybová zpráva by byla:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Přidat uživatele nebo registrace aplikací do Azure Active Directory

K rozhraním API služby Azure FarmBeats se dá přistup uživatel nebo registrace aplikace v Azure Active Directory. Pokud chcete vytvořit registraci aplikace v Azure Active Directory, postupujte takto:

1. Přejít na [Azure Portal](https://portal.azure.com)a vyberte **Azure Active Directory**  >  **Registrace aplikací**  >  **nové registrace**. Případně můžete použít existující účet.
2. Pro nový účet udělejte toto:

    - Zadejte název.
    - Vyberte **účty jenom v tomto organizačním adresáři (jeden tenant)**.
    - Použijte výchozí hodnoty ve zbývajících polích.
    - Vyberte **Zaregistrovat**.

3. V podokně s **přehledem** nové a existující registrace aplikace proveďte tento postup:

    - Zachyťte **ID klienta** a **ID tenanta**.
    - Pokud chcete vygenerovat nový tajný klíč klienta a zachytit si **tajný klíč klienta**, přečtěte si **certifikáty a tajné** kódy.
    - Vraťte se zpět na **Přehled**a vyberte odkaz vedle **možnosti spravovat aplikaci v místním adresáři**.
    - Přejít na **vlastnosti** a zachytit **ID objektu**.

4. Přejít do DataHub Swagger (https:// <yourdatahub> . azurewebsites.net/swagger/index.html) a proveďte následující kroky:
    - Přejít na **rozhraní RoleAssignment API**.
    - Proveďte příspěvek pro vytvoření objektu **RoleAssignment** pro **ID objektu** , který jste právě vytvořili.
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > Další informace o tom, jak přidat uživatele a registraci služby Active Directory, najdete v tématu [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Po dokončení předchozích kroků může registrace vaší aplikace (klienta) volat rozhraní API Azure FarmBeats pomocí přístupového tokenu prostřednictvím ověření identity.

Pomocí přístupového tokenu ho odešlete v následujících požadavcích rozhraní API v části záhlaví jako:

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
