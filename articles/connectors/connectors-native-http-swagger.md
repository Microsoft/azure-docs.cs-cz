---
title: Připojení k koncovým bodům REST z Azure Logic Apps
description: Monitorování koncových bodů REST v automatizovaných úlohách, procesech a pracovních postupech pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: 8bd46f013680d5cbc9e75a312206fdb2fdcac7ec
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109352"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Volání koncových bodů REST pomocí Azure Logic Apps

Díky [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrovanému konektoru http + Swagger můžete automatizovat pracovní postupy, které pravidelně volají libovolný koncový bod REST prostřednictvím [souboru Swagger](https://swagger.io) vytvořením Logic Apps. Aktivační událost HTTP + Swagger a akce fungují stejně jako [Trigger http a akci](connectors-native-http.md) , ale poskytují lepší zkušenosti v návrháři aplikace logiky tím, že zpřístupňují strukturu rozhraní API a výstupy popsané v souboru Swagger. K implementaci triggeru cyklického dotazování použijte vzor cyklického dotazování, který je popsaný v tématu [Vytvoření vlastních rozhraní API a volání dalších rozhraní API, služeb a systémů z Logic Apps](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL souboru Swagger (ne OpenAPI), který popisuje cílový koncový bod REST

  Koncový bod REST musí obvykle splňovat tato kritéria, aby mohl konektor fungovat:

  * Soubor Swagger musí být hostovaný na adrese URL HTTPS, která je veřejně přístupná.
  
  * Soubor Swagger musí `operationID` pro každou operaci v definici obsahovat. V takovém případě konektor zobrazuje pouze poslední operaci v souboru Swagger. 

  * Soubor Swagger musí mít povolený [sdílení prostředků mezi zdroji (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .

  Chcete-li vytvořit odkaz na soubor Swagger, který není hostovaný nebo který nesplňuje požadavky na zabezpečení a více zdrojů, můžete [nahrát soubor Swagger do kontejneru objektů BLOB v účtu úložiště Azure](#host-swagger)a povolit pro tento účet úložiště CORS, abyste mohli soubor odkázat.

  V příkladech v tomto tématu se používá [Cognitive Services rozhraní API pro rozpoznávání tváře](../cognitive-services/face/overview.md), která vyžaduje [účet Cognitive Services a přístupový klíč](../cognitive-services/cognitive-services-apis-create-account.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md) Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikace logiky, ze které chcete zavolat cílový koncový bod. Pokud chcete začít s triggerem HTTP + Swagger, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci HTTP + Swagger, spusťte aplikaci logiky s libovolným triggerem, který chcete. V tomto příkladu se jako první krok používá Trigger HTTP + Swagger.

## <a name="add-an-http--swagger-trigger"></a>Přidání triggeru HTTP + Swagger

Tato integrovaná aktivační událost pošle požadavek HTTP na adresu URL pro soubor Swagger, který popisuje REST API a vrátí odpověď obsahující obsah tohoto souboru.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. V návrháři do vyhledávacího pole zadejte "Swagger" jako filtr. V seznamu **triggery** vyberte aktivační událost **http + Swagger** .

   ![Vybrat aktivační událost HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Do pole **Adresa URL koncového bodu Swagger** zadejte adresu URL souboru Swagger a vyberte **Další**.

   V tomto příkladu se používá adresa URL Swagger, která je umístěná v Západní USA oblasti [Cognitive Services rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky s triggerem "H T T P + Swagger" a vlastností "koncový bod" koncového bodu U R L nastavenou na hodnotu U R L.](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Když Návrhář zobrazí operace popsané souborem Swagger, vyberte operaci, kterou chcete použít.

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky s triggerem "H T T P + Swagger" a seznamem, který zobrazuje operace Swagger.](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Zadejte hodnoty pro parametry triggeru, které se liší v závislosti na vybrané operaci, kterou chcete zahrnout do volání koncového bodu. Nastavte opakování pro to, jak často chcete, aby Trigger volal koncový bod.

   Tento příklad přejmenuje aktivační událost na "aktivační událost HTTP + Swagger: Face-Detect", aby měl krok výstižnější název.

   ![Snímek obrazovky, který zobrazuje návrháře aplikace logiky s triggerem "H T T P + Swagger", který zobrazuje operaci "obličeje-rozpoznávání".](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

   Další informace o typech ověřování dostupných pro HTTP + Swagger najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Pokračujte v vytváření pracovního postupu aplikace logiky s akcemi, které se spustí, když se Trigger aktivuje.

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="add-an-http--swagger-action"></a>Přidat akci HTTP + Swagger

Tato Vestavěná akce vytvoří požadavek HTTP na adresu URL pro soubor Swagger, který popisuje REST API a vrátí odpověď obsahující obsah tohoto souboru.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Otevřete aplikaci logiky v návrháři aplikace logiky.

1. V kroku, kam chcete přidat akci HTTP + Swagger, vyberte **Nový krok**.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.

1. V návrháři do vyhledávacího pole zadejte "Swagger" jako filtr. V seznamu **Akce** vyberte akci **http + Swagger** .

    ![Vybrat akci HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Do pole **Adresa URL koncového bodu Swagger** zadejte adresu URL souboru Swagger a vyberte **Další**.

   V tomto příkladu se používá adresa URL Swagger, která je umístěná v Západní USA oblasti [Cognitive Services rozhraní API pro rozpoznávání tváře](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Zadejte adresu URL koncového bodu Swagger.](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Když Návrhář zobrazí operace popsané souborem Swagger, vyberte operaci, kterou chcete použít.

   ![Operace v souboru Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Zadejte hodnoty pro parametry akce, které se liší v závislosti na vybrané operaci, kterou chcete zahrnout do volání koncového bodu.

   Tento příklad nemá žádné parametry, ale přejmenuje akci na HTTP + Swagger Action: Face-Identifikujte, aby měl krok výstižnější název.

   ![Podrobnosti o operaci](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

   Další informace o typech ověřování dostupných pro HTTP + Swagger najdete v tématu [Přidání ověřování do odchozích volání](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>V Azure Storage hostitele Swagger

Můžete odkazovat na soubor Swagger, který není hostovaný nebo který nesplňuje požadavky na zabezpečení a více zdrojů, odesláním tohoto souboru do kontejneru objektů BLOB v účtu úložiště Azure a povolením CORS v tomto účtu úložiště. Pokud chcete vytvářet, nastavovat a ukládat soubory Swagger v Azure Storage, postupujte podle následujících kroků:

1. [Vytvořte účet úložiště Azure](../storage/common/storage-account-create.md).

1. Nyní povolte CORS pro objekt BLOB. V nabídce účtu úložiště vyberte **CORS**. Na kartě **BLOB Service** zadejte tyto hodnoty a pak vyberte **Uložit**.

   | Vlastnost | Hodnota |
   |----------|-------|
   | **Povolené zdroje** | `*` |
   | **Povolené metody** | `GET`, `HEAD`, `PUT` |
   | **Povolené hlavičky** | `*` |
   | **Zveřejněné hlavičky** | `*` |
   | **Maximální stáří** (v sekundách) | `200` |
   |||

   I když tento příklad používá [Azure Portal](https://portal.azure.com), můžete použít nástroj jako [Průzkumník služby Azure Storage](https://storageexplorer.com/)nebo automaticky nakonfigurovat toto nastavení pomocí tohoto ukázkového [skriptu PowerShellu](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Vytvořte kontejner objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). V podokně s **přehledem** kontejneru vyberte **změnit úroveň přístupu**. V seznamu **úroveň veřejného přístupu** vyberte možnost **objekt BLOB (anonymní přístup pro čtení jenom pro objekty BLOB)** a vyberte **OK**.

1. [Nahrajte soubor Swagger do kontejneru objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), a to buď pomocí [Azure Portal](https://portal.azure.com) , nebo [Průzkumník služby Azure Storage](https://storageexplorer.com/).

1. Pokud chcete odkazovat na soubor v kontejneru objektů blob, Získejte adresu URL HTTPS, která následuje tento formát, což rozlišuje velká a malá písmena, od Průzkumník služby Azure Storage:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<complete-swagger-file-name>?<query-parameters>`

## <a name="connector-reference"></a>Referenční informace ke konektorům

Zde jsou další informace o výstupech z triggeru HTTP + Swagger nebo akce. Volání HTTP + Swagger vrátí tyto informace:

| Název vlastnosti | Typ | Description |
|---------------|------|-------------|
| záhlaví | object | Hlavičky z požadavku |
| text | object | Objekt s obsahem textu z požadavku |
| stavový kód | int | Stavový kód z požadavku |
||||

| Stavový kód | Description |
|-------------|-------------|
| 200 | OK |
| 202 | Přijato |
| 400 | Chybný požadavek |
| 401 | Neautorizováno |
| 403 | Forbidden |
| 404 | Nenalezeno |
| 500 | Vnitřní chyba serveru. Došlo k neznámé chybě. |
|||

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
