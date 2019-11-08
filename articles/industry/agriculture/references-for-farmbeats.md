---
title: Odkazy na FarmBeats
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8e8e4524034f0a296045691309b065f8547bdaa0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797696"
---
# <a name="references"></a>Odkazy

Níže jsou uvedeny poznámky a pokyny, které popisují rozhraní API služby Azure FarmBeats.

## <a name="rest-api"></a>Rozhraní REST API

Rozhraní API Azure FarmBeats poskytují zemědělský podnik standardizovaným rozhraním RESTful s odpověďmi založenými na standardu JSON, které vám pomůžou využít možnosti Azure FarmBeats:

- Rozhraní API pro získání senzorů, fotoaparátů, pomocí dronů, počasí, satelitních a spravovaných pozemních dat.
- Normalizace/kontextová data napříč společnými poskytovateli dat
- Schematized přístup a možnosti dotazování pro všechna ingestovaná data.
- Automatické generování metadat, na které lze dotazovat, na základě agronomických funkcí.  
- Automaticky generované agregace časových sekvencí pro rychlé vytváření modelů.
- Integrovaný modul Azure Data Factory (ADF), který umožňuje snadno vytvářet vlastní kanály zpracování dat.

## <a name="application-development"></a>Vývoj aplikací

Rozhraní API obsahují technickou dokumentaci k Swagger. Informace o všech rozhraních API a jejich odpovídajících požadavcích a odpovědích najdete v [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) .

Toto je souhrn všech objektů a prostředků v FarmBeats datových rozbočovačích:

Sdílených | Farma odpovídá fyzickému umístění zájmu v rámci systému FarmBeats. Každá farma má název farmy a jedinečné ID farmy.
--- | ---|
Zařízení  | Zařízení odpovídá fyzickému zařízení přítomnému ve farmě. Každé zařízení má jedinečné ID zařízení. Zařízení je obvykle zřízené pro farmu s ID farmy.
deviceModel  | DeviceModel odpovídá metadatům zařízení, jako je výrobce, typ zařízení buď bránu, nebo uzel.
Elektrické  | Senzor odpovídá fyzickému senzoru, který zaznamenává hodnoty. Senzor se obvykle připojuje k zařízení s ID zařízení.
SensorModel  | SensorModel odpovídá meta údajům snímačů, jako je výrobce, typ snímače buď analogového, nebo digitálního, měření senzorů, jako je okolní teplota, tlak atd.
Telemetrie  | Telemetrie nabízí možnost číst zprávy telemetrie pro určitý senzor a časový rozsah.
Úloha  | Úloha odpovídá jakémukoli pracovnímu postupu aktivity, který se spustí v systému FarmBeats, aby získal požadovaný výstup. Každá úloha je přidružená k ID úlohy a typu úlohy.
JobType  | JobType odpovídá různým typům úloh, které systém podporuje. To zahrnuje systémy definované & uživatelsky definované typy úloh.
ExtendedType  | ExtendedType odpovídá seznamu systémových & uživatelsky definované typy v systému. To pomáhá nastavit nový senzor nebo scén nebo Scenefile typ v systému FarmBeats.
Partner  | Partner odpovídá partnerovi pro integraci senzorů/snímků pro FarmBeats.
Scény  | Scény odpovídá jakémukoli vygenerovanému výstupu v kontextu farmy. Každá scéna má ID scény, zdroj scény, typ scény a ID farmy, která je k němu přidružená. K jednotlivým ID scény může být přidruženo více souborů scény.
SceneFile |SceneFile odpovídá všem souborům, které jsou generovány pro jednu scénu. K jednomu ID scény může být přidruženo několik ID SceneFile.
Pravidlo  |Pravidlo odpovídá podmínkám pro data související s farmou, která aktivuje výstrahu. Každé pravidlo bude v kontextu dat farmy.
Výstrahy  | Výstraha odpovídá oznámení, které se generuje při splnění podmínky pravidla. Každá výstraha bude v kontextu pravidla.
Rutiny roledefinition  | Rutiny roledefinition definuje povolené a zakázané akce pro roli.
RoleAssignment  |RoleAssignment odpovídá přiřazování rolí uživateli nebo instančnímu objektu.

**Formát dat**

JSON (JavaScript Object Notation) je běžný jazyk nezávislý na jazyce, který poskytuje jednoduchou textovou reprezentaci libovolných datových struktur. Další informace najdete v tématu json.org.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace

Požadavky HTTP na REST API jsou chráněny pomocí Azure Active Directory (Azure AD).
Aby bylo možné provést ověřený požadavek na rozhraní REST API, kód klienta vyžaduje ověření pomocí platných přihlašovacích údajů před voláním rozhraní API. Ověřování je sladěné mezi různými aktéry Azure AD a poskytuje vašemu klientovi přístupový token jako ověření platnosti. Token se pak pošle v hlavičce autorizace HTTP REST API požadavků. Další informace o ověřování Azure AD najdete v tématu [Azure Active Directory](https://portal.azure.com) pro vývojáře.

Přístupový token se musí odeslat v následujících žádostech o rozhraní API v oddílu záhlaví jako:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**Hlavičky požadavku HTTP**

Tady jsou nejčastější hlavičky požadavků, které je potřeba zadat při volání rozhraní API do služby Azure FarmBeats data hub:


**Hlavička** | **Popis a příklad**
--- | ---
Typ obsahu  | Formát požadavku (Content-Type: Application/<format>) pro formát rozhraní API centra dat Azure FarmBeats je JSON. Content-Type: Application/JSON
Autorizace  | Určuje přístupový token potřebný k vytvoření volání rozhraní API. **Autorizace: nosný < přístup-token >**
vyjádřit | Formát odpovědi. Pro rozhraní API centra dat Azure FarmBeats formát je **přijmout JSON: Application/JSON.**

**Požadavky rozhraní API**

Chcete-li vytvořit požadavek REST API, zkombinujete metodu HTTP (GET, POST, PUT nebo DELETE), adresu URL služby API, identifikátor URI k prostředku, který se má dotazovat, odeslat data do, aktualizovat nebo odstranit a jednu nebo více hlaviček požadavku HTTP.

Adresa URL služby API je vaše adresa URL centra dat https://\<yourdatahub-website-název >. azurewebsites. NET můžete zahrnout parametry dotazu pro volání funkce GET pro filtrování, omezení velikosti a řazení dat v odpovědích.

Níže uvedená ukázková žádost slouží k získání seznamu zařízení:

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Většina volání GET, POST a PUT vyžaduje tělo požadavku JSON.

Níže uvedená ukázková žádost slouží k vytvoření zařízení (obsahuje vstupní kód JSON s textem žádosti).

```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Parametry dotazu**

V případě **volání funkce** REST můžete filtrovat, omezit velikost a seřadit data v odpovědi rozhraní API zahrnutím jednoho nebo více parametrů dotazu do identifikátoru URI požadavku. Parametry dotazu najdete v dokumentaci k rozhraní API a na jednotlivých voláních GET.
Například při dotazování na seznam zařízení (volání GET na/Device) je možné zadat následující parametry dotazu:  

![Beats farmy projektu](./media/for-references/query-parameters-device-1.png)

**Zpracování chyb**

Rozhraní API služby Azure FarmBeats data hub vrací standardní chyby protokolu HTTP. Nejběžnější kódy chyb jsou následující:


 |Kód chyby             | Popis |
 |---                    | --- |
 |200                    | Úspěch |
 |201                    | Vytvoření (post) úspěšné |
 |400                    | Chybný požadavek. V žádosti došlo k chybě. |
 |401                    | Přístupu. Volající rozhraní API nemá autorizaci pro přístup k prostředku. |
 |404                    | Prostředek se nenašel. |
 |5XX                    | Došlo k vnitřní chybě serveru. Kódy chyb začínající na 5XX znamená, že na serveru došlo k nějaké chybě. Další podrobnosti najdete v protokolech serveru a v následující části. |


Kromě standardních chyb HTTP vrátí rozhraní API služby Azure FarmBeats data hub také interní chyby v následujícím formátu:

```
{
  "message": "<More information on the error>",
  "status": "<error code>”,
  "code": "<InternalErrorCode>",
  "moreInfo": "<Details of the error>"
}
```

Příklad: při vytváření farmy nebyla ve vstupní datové části zadána povinná pole "Name". Výsledná chybová zpráva by byla:

```
{
  "message": "Model validation failed",
  "status": 400,
  "code": "ModelValidationFailed",
  "moreInfo": "[\"The Name field is required.\"]"
}
```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Přidávání uživatelů nebo registrace aplikací do Azure Active Directory

 K rozhraním API služby Azure FarmBeats se dá přistup uživatel nebo registrace aplikace v Azure Active Directory. Pokud chcete na svém Azure Active Directory vytvořit registraci aplikace, postupujte podle následujících kroků:  

1. Přejít na [Azure Portal](https://portal.azure.com) **Azure Active Directory, registrace aplikací**> **nové registrace**. Případně můžete použít existující účet.
2. Pro nový účet se ujistěte, že jste dokončili následující kroky:

    - Zadejte název.
    - Vybrat **účty pouze v tomto organizačním adresáři (jeden tenant)**
    - Výchozí hodnoty ve zbývajících polích
    - Vybrat **registraci**

3. V části Nová/existující registrace aplikace **– Přehled**proveďte následující:

    - Zachyťte **ID klienta** a **ID tenanta**.
    - Pokud chcete vygenerovat nový tajný klíč klienta a zachytit si **tajný klíč klienta**, přečtěte si **certifikáty a tajné** kódy.
    - Vraťte se na přehled a klikněte na odkaz vedle **možnosti spravovat aplikaci v místním adresáři** .
    - Přejít na **vlastnosti** pro zachycení **ID objektu**

4. Přečtěte si [Swagger centra dat](https://<yourdatahub>.azurewebsites.net/swagger/index.html) a proveďte následující kroky:
    - Přejít na **rozhraní RoleAssignment API**
    - Proveďte **příspěvek** k vytvoření ROLEASSIGNMENT pro **ID objektu** , který jste právě vytvořili. – Vstupní JSON je:

  > [!NOTE]
  > Další informace o přidávání uživatelů a registraci AD najdete v tématu [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) .

Po dokončení výše uvedeného postupu může registrace vaší aplikace (klienta) volat rozhraní API Azure FarmBeats pomocí přístupového tokenu prostřednictvím nosiče ověřování.  

Pomocí přístupového tokenu ho odešlete v následujících požadavcích rozhraní API v části záhlaví jako:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
