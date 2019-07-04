---
title: Připojení k koncové body REST z Azure Logic Apps
description: Monitorování koncových bodů REST v automatizovaných úloh, procesy a pracovní postupy s využitím Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: f0410ed7a98e4838e41407868cf26b5254811ae3
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541600"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Volají koncové body REST s využitím Azure Logic Apps

S [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrované HTTP + Swagger konektor, můžete automatizovat pracovní postupy, které pravidelně volání jakékoli koncového bodu REST prostřednictvím [soubor Swagger](https://swagger.io) díky vytváření aplikací logiky. HTTP + Swagger aktivovat a akce, které fungují stejně jako [triggeru HTTP a akce](connectors-native-http.md) ale poskytovat lepší prostředí v návrháři aplikace logiky zveřejněním strukturu rozhraní API a výstupy popisuje soubor Swagger. K implementaci cyklického dotazování aktivační událost, mají tvar dotazování, který je popsaný v [vytvoření vlastních rozhraní API volat jiné rozhraní API, služby a systémy z aplikací logiky](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL pro soubor Swagger popisující cílový koncový bod REST

  Koncový bod REST, obvykle musí splňovat tato kritéria pro fungování konektoru:

  * Soubor Swagger musí být hostovaný na adresu URL HTTPS, který je veřejně dostupná.

  * Soubor Swagger musí mít [sdílení prostředků mezi zdroji (CORS)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) povolena.

  Chcete-li odkazovat na soubor Swagger, který není hostitelem nebo, který nesplňuje požadavky na nepůvodního zdroje a zabezpečení, můžete [nahrát soubor Swagger na kontejner objektů blob v účtu služby Azure storage](#host-swagger)a proto povolení CORS na tomto účtu úložiště můžete odkazovat na soubor.

  V příkladech v tomto tématu [Cognitive Services Face API](https://docs.microsoft.com/azure/cognitive-services/face/overview), což vyžaduje [služeb Cognitive Services účtu a přístupový klíč](../cognitive-services/cognitive-services-apis-create-account.md).

* Základní znalosti o [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikace logiky, ze kterého má volat cílový koncový bod. Začít s HTTP + Swagger aktivovat, [vytvoření prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Použití HTTP + Swagger akce začínat všechny aktivační události, které chcete aplikaci logiky. Tento příklad používá HTTP + Swagger trigger jako první krok.

## <a name="add-an-http--swagger-trigger"></a>Přidat HTTP + Swagger aktivační událost

Tato aktivační událost integrované odešle požadavek HTTP na adresu URL pro soubor Swagger popisující rozhraní REST API a vrátí odpověď, která obsahuje obsah tohoto souboru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevřete váš prázdné aplikace logiky v návrháři aplikace logiky.

1. V návrháři, do vyhledávacího pole zadejte jako filtr "swagger". Z **triggery** seznamu, vyberte **HTTP + Swagger** aktivační události.

   ![Vyberte HTTP + Swagger aktivovat](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. V **adresa URL koncového bodu SWAGGERU** , zadejte adresu URL pro soubor Swagger a vyberte **Další**.

   V tomto příkladu adresa URL Swaggeru, která se nachází v oblasti západní USA pro [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Zadejte adresu URL pro koncový bod Swaggeru](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Když je v Návrháři znázorněn operací popsaných v souboru Swagger, vyberte operaci, kterou chcete použít.

   ![Operace v souboru Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Zadejte hodnoty pro aktivační událost parametry, které se liší v závislosti na vybranou operaci, kterou chcete zahrnout do volání koncového bodu. Nastavení opakování pro jak často chcete, aby se aktivační událost pro volání koncového bodu.

   Tento příklad přejmenuje aktivační události "HTTP + Swagger aktivace: Rozpoznávání tváře – zjišťovat"tak, aby měl více popisný název kroku.

   ![Podrobnosti o operaci](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Chcete-li přidat další dostupné parametry, otevřete **přidat nový parametr** seznam a vyberte požadované parametry.

   Další informace o dostupných typech ověřování pro HTTP + Swagger, naleznete v tématu [ověřování protokolu HTTP triggery a akce](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Pokračujte v sestavování pracovního postupu aplikace logiky s akcemi, které běží na aktivaci triggeru.

1. Jakmile budete hotovi, nezapomeňte si uložit aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

## <a name="add-an-http--swagger-action"></a>Přidat HTTP + Swagger akce

Toto integrované akce provede požadavek HTTP na adresu URL pro soubor Swagger popisující rozhraní REST API a vrátí odpověď, která obsahuje obsah tohoto souboru.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Otevření aplikace logiky v návrháři aplikace logiky.

1. V části krok, ve které chcete přidat HTTP + Swagger akci, vyberte **nový krok**.

   Přidání akce mezi kroky, přesuňte ukazatel nad šipku mezi kroky. Vyberte znaménko plus ( **+** ), který se zobrazí a pak vyberte **přidat akci**.

1. V návrháři, do vyhledávacího pole zadejte jako filtr "swagger". Z **akce** seznamu, vyberte **HTTP + Swagger** akce.

    ![Vyberte HTTP + Swagger akce](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. V **adresa URL koncového bodu SWAGGERU** , zadejte adresu URL pro soubor Swagger a vyberte **Další**.

   V tomto příkladu adresa URL Swaggeru, která se nachází v oblasti západní USA pro [Cognitive Services Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Zadejte adresu URL pro koncový bod Swaggeru](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Když je v Návrháři znázorněn operací popsaných v souboru Swagger, vyberte operaci, kterou chcete použít.

   ![Operace v souboru Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Zadejte hodnoty parametrů akce, které se liší podle vybraného operace, které chcete zahrnout do volání koncového bodu.

   Tento příklad nemá žádné parametry, ale přejmenuje akci, která se "HTTP + Swagger akce: Rozpoznávání tváře – identifikace"tak, aby měl více popisný název kroku.

   ![Podrobnosti o operaci](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Chcete-li přidat další dostupné parametry, otevřete **přidat nový parametr** seznam a vyberte požadované parametry.

   Další informace o dostupných typech ověřování pro HTTP + Swagger, naleznete v tématu [ověřování protokolu HTTP triggery a akce](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Jakmile budete hotovi, nezapomeňte si uložit aplikaci logiky. Na panelu nástrojů návrháře zvolte **Uložit**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Hostitel Swagger ve službě Azure Storage

Můžete odkazovat na soubor Swagger, který není hostitelem nebo, který nesplňuje požadavky mezi zdroji na zabezpečení a nahrání souboru do kontejneru objektů blob v účtu služby Azure storage a povolením CORS na tomto účtu úložiště. K vytvoření, nastavení a ukládání souborů Swagger ve službě Azure Storage, postupujte podle těchto kroků:

1. [Vytvořte si účet Azure Storage](../storage/common/storage-create-storage-account.md).

1. Nyní povolte CORS pro objekt blob. V nabídce účtu úložiště vyberte **CORS**. Na **službu Blob service** kartu, zadejte tyto hodnoty a pak vyberte **Uložit**.

   | Vlastnost | Hodnota |
   |----------|-------|
   | **Povolené zdroje** | `*` |
   | **Povolené metody** | `GET`, `HEAD`, `PUT` |
   | **Povolené hlavičky** | `*` |
   | **Zveřejněné hlavičky** | `*` |
   | **Maximální stáří** (v sekundách) | `200` |
   |||

   I když v tomto příkladu [webu Azure portal](https://portal.azure.com), můžete použít nástroj, jako [Průzkumníka služby Azure Storage](https://storageexplorer.com/), nebo automaticky konfigurovat toto nastavení s použitím této ukázky [skript prostředí PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Vytvořte kontejner objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md). Ke kontejneru **přehled** vyberte **změnit úroveň přístupu**. Z **úroveň veřejného přístupu** seznamu vyberte **objektů Blob (anonymní přístup pro čtení pro objekty BLOB pouze)** a vyberte **OK**.

1. [Nahrát soubor Swagger do kontejneru objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob)– buď prostřednictvím [webu Azure portal](https://portal.azure.com) nebo [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

1. Chcete-li odkazovat na soubor v kontejneru objektů blob, použijte odkazu HTTPS, který následuje tento formát, který je velká a malá písmena:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Referenční informace ke konektorům

Zde jsou další informace o výstup z HTTP + Swagger triggeru nebo akce. HTTP + Swagger volání vrátí tyto informace:

| Název vlastnosti | Type | Popis |
|---------------|------|-------------|
| Záhlaví | objekt | Hlavičky požadavku |
| Text | objekt | JSON – objekt | Objekt s obsah textu požadavku |
| Stavový kód | int | Stavový kód z požadavku |
|||

| Kód stavu | Popis |
|-------------|-------------|
| 200 | OK |
| 202 | Přijato |
| 400 | Nesprávná žádost |
| 401 | Neautorizováno |
| 403 | Zakázáno |
| 404 | Nebyl nalezen |
| 500 | Vnitřní chyba serveru. Došlo k neznámé chybě. |
|||

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)