---
title: Odkazy
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 318b3e3f774a4381434fd56154f4c0d95c28c7a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479511"
---
# <a name="references"></a>Odkazy

Tento článek popisuje Azure FarmBeats API.

## <a name="rest-api"></a>REST API

Rozhraní API Azure FarmBeats poskytují zemědělským podnikům standardizované rozhraní RESTful s odpověďmi založenými na JSON, které vám pomohou využít výhod azure farmbeats, jako jsou:

- API pro získání senzorů, kamer, dronů, počasí, satelitů a kurátorských pozemních dat.
- Normalizace a kontextualizace dat napříč běžnými poskytovateli dat.
- Schematized přístup a dotaz možnosti na všechna ingemovaná data.
- Automatické generování metadat, které lze dotazovat na základě agronomických funkcí.
- Automaticky generované časové pořadí agreguje pro rychlé vytváření modelů.
- Integrovaný modul Azure Data Factory pro snadné vytváření vlastních kanálů pro zpracování dat.

## <a name="application-development"></a>Vývoj aplikací

FarmBeats API obsahují Swagger technickou dokumentaci. Informace o všech api a jejich odpovídající požadavky nebo odpovědi, naleznete v tématu [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

Následující tabulka shrnuje všechny objekty a zdroje v FarmBeats Datahub:

| Objekty a zdroje | Popis
--- | ---|
Farmě | Farma odpovídá fyzickému umístění zájmu v rámci systému FarmBeats. Každá farma má název farmy a jedinečné ID farmy. |
Zařízení  | Zařízení odpovídá fyzickému zařízení přítomnému na farmě. Každé zařízení má jedinečné ID zařízení. Zařízení se obvykle zřizováno do farmy s ID farmy.
DeviceModel  | DeviceModel odpovídá metadatům zařízení, jako je například výrobce a typ zařízení, což je brána nebo uzel.
Senzor  | Senzor odpovídá fyzickému senzoru, který zaznamenává hodnoty. Senzor je obvykle připojen k zařízení s ID zařízení.
SenzorModel  | SensorModel odpovídá metadatům senzoru, jako je výrobce, typ senzoru, který je analogový nebo digitální, a měření senzoru, jako je okolní teplota a tlak.
Telemetrie  | Telemetrie poskytuje možnost číst telemetrické zprávy pro konkrétní snímač a časový rozsah.
Úloha  | Úloha odpovídá všem pracovním postupům aktivit, které jsou prováděny v systému FarmBeats získat požadovaný výstup. Každá úloha je přidružena k ID úlohy a typu úlohy.
Typ úlohy  | JobType odpovídá různým typům úloh podporovaných systémem. Jsou zahrnuty typy úloh definované systémem a uživatelem.
Rozšířený typ  | ExtendedType odpovídá seznamu systémem a uživatelem definovaných typů v systému. ExtendedType pomáhá nastavit nový typ souboru senzoru, scény nebo scény v systému FarmBeats.
Partner  | Partner odpovídá partnerovi pro integraci senzorů a snímků pro FarmBeats.
Scény  | Scéna odpovídá jakémukoli generovanému výstupu v kontextu farmy. Každá scéna má ID scény, zdroj scény, typ scény a ID farmy s ním spojené. Ke každému ID scény může být přidruženo více souborů scény.
Soubor scény |SceneFile odpovídá všem souborům, které jsou generovány pro jednu scénu. K jednomu ID scény může být přidruženo více ID souboru scény.
Pravidlo  |Pravidlo odpovídá podmínce pro data související s farmou pro aktivaci výstrahy. Každé pravidlo je v kontextu dat farmy.
Výstrahy  | Výstraha odpovídá oznámení, které se vygeneruje při splnění podmínky pravidla. Každá výstraha je v kontextu pravidla.
Definice role  | RoleDefinition definuje povolené a nepovolené akce pro roli.
Přiřazení role  |RoleAssignment odpovídá přiřazení role uživateli nebo instančnímu objektu.

### <a name="data-format"></a>Formát dat

JSON je společný jazykově nezávislý formát dat, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace naleznete na [webových stránkách společnosti JSON](https://www.json.org/).

## <a name="authentication-and-authorization"></a>Ověřování a autorizace

Http požadavky na rozhraní REST API jsou chráněné pomocí Azure Active Directory (Azure AD).
Chcete-li provést ověřený požadavek na rozhraní REST API, kód klienta vyžaduje před voláním rozhraní API ověření s platnými přihlašovacími údaji. Ověřování je koordinováno mezi různými aktéry podle Azure AD. Poskytuje klientovi přístupový token jako důkaz ověřování. Token je pak odeslán v hlavičce autorizace HTTP požadavků rozhraní REST API. Další informace o ověřování Azure AD najdete v [tématu Azure Active Directory](https://portal.azure.com) pro vývojáře.

Přístupový token musí být odeslán v následujících požadavcích rozhraní API v části záhlaví, jako:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>Hlavičky požadavků HTTP

Tady jsou nejběžnější hlavičky požadavků, které je nutné zadat při volání rozhraní API azure FarmBeats Datahub.


**Záhlaví** | **Popis a příklad**
--- | ---
Typ obsahu  | Formát požadavku (Content-Type:<format>application/ ). Pro Azure FarmBeats Datahub API formát je JSON. Typ obsahu: aplikace/json
Autorizace  | Určuje přístupový token potřebný k volání rozhraní API. Autorizace:> přístupového tokenu <nosiče
Accept | Formát odpovědi. Pro Azure FarmBeats Datahub API formát je JSON. Přijmout: aplikace/json

### <a name="api-requests"></a>Požadavky rozhraní API

Chcete-li vytvořit požadavek rozhraní REST API, zkombinujte metodu HTTP (GET, POST, PUT nebo DELETE), adresu URL služby rozhraní API, identifikátor URI do prostředku k dotazování, odeslání dat, aktualizaci nebo odstranění a potom přidejte jednu nebo více hlaviček požadavků HTTP.

Adresa URL služby ROZHRANÍ API je vaše adresa\<URL Datahubu, například https:// název vašeho datahubu-web>.azurewebsites.net.

Volitelně můžete zahrnout parametry dotazu na volání GET pro filtrování, omezení velikosti a řazení dat v odpovědích.

Následující ukázkový požadavek se používá k získání seznamu zařízení:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Většina get, post a PUT volání vyžadují tělo požadavku JSON.

Následující ukázkový požadavek vytvoří zařízení. Tento požadavek má vstupní JSON s tělo požadavku.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parametry dotazu

Pro volání REST GET můžete filtrovat, omezit velikost a seřadit data v odpovědi rozhraní API zahrnutím jednoho nebo více parametrů dotazu na identifikátor URI požadavku požadavku. Parametry dotazu naleznete v dokumentaci k rozhraní API a jednotlivé volání GET.
Například při dotazování na seznam zařízení (GET call on /Device) lze zadat následující parametry dotazu:

![Seznam zařízení](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Zpracování chyb

Azure FarmBeats Datahub API vrátit standardní chyby HTTP. Nejběžnější kódy chyb jsou následující:

 |Kód chyby             | Popis |
 |---                    | --- |
 |200                    | Úspěch |
 |201                    | Vytvořit (odeslat) úspěch |
 |400                    | Špatná žádost. V požadavku je chyba. |
 |401                    | Neoprávněným. Volající rozhraní API není oprávněn k přístupu k prostředku. |
 |404                    | Zdroj nebyl nalezen. |
 |5XX                    | Vnitřní chyba serveru. Chybové kódy začínající 5XX znamená, že na serveru je nějaká chyba. Další podrobnosti naleznete v protokolech serveru a v následující části. |


Kromě standardních chyb HTTP azure farmbeats datahub api také vrátit interní chyby v následujícím formátu:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

V tomto příkladu při vytvoření farmy nebylo povinné pole "Název" zadáno ve vstupní datové části. Výsledná chybová zpráva by byla:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Přidání uživatelů nebo registrací aplikací do služby Azure Active Directory

K rozhraním API Azure FarmBeats má přístup uživatel nebo registrace aplikace ve službě Azure Active Directory. Pokud chcete vytvořit registraci aplikace ve službě Azure Active Directory, postupujte takto:

1. Přejděte na [portál Azure a](https://portal.azure.com)vyberte registrace > **App registrations** > aplikací **Azure Active Directory**Nová**registrace**. Případně můžete použít existující účet.
2. U nového účtu postupujte takto:

    - Zadejte název.
    - Vyberte **účty v tomto organizačním adresáři pouze (jeden tenant)**.
    - Použijte výchozí hodnoty ve zbývajících polích.
    - Vyberte **Zaregistrovat**.

3. V podokně Nový a existující **přehled** registrace aplikace postupujte takto:

    - **Zachyťte ID klienta** a **ID klienta**.
    - Přejděte na **certifikáty a tajné klíče,** chcete-li vygenerovat nový tajný klíč klienta a zachytit **tajný klíč klienta**.
    - Přejděte zpět na **přehled**a vyberte odkaz vedle **položky Spravovat aplikaci v místním adresáři**.
    - Chcete-li zachytit **ID objektu,** přejděte na **příkaz Vlastnosti** .

4. Přejděte na svůj [Datahub Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) a udělejte toto:
    - Přejděte na **rozhraní API přiřazení rolí**.
    - Proveďte POST a vytvořte objekt **RoleAssignment** pro **ID objektu,** který jste právě vytvořili.

  > [!NOTE]
  > Další informace o přidání uživatelů a registraci služby Active Directory naleznete v [tématu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Po dokončení předchozích kroků může registrace aplikace (klienta) volat azure farmbeats API pomocí přístupového tokenu prostřednictvím ověřování nosiče.

Pomocí přístupového tokenu jej odešlete v následujících požadavcích rozhraní API v části záhlaví jako:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
