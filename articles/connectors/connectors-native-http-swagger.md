---
title: Připojení ke koncovým bodům REST z aplikací Azure Logic Apps
description: Monitorování koncových bodů REST v automatizovaných úlohách, procesech a pracovních postupech pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/01/2019
tags: connectors
ms.openlocfilehash: b34fdc36bd0b1ce294a92b2ae8fa5da01568e5a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787365"
---
# <a name="call-rest-endpoints-by-using-azure-logic-apps"></a>Volání koncových bodů REST pomocí aplikací Azure Logic Apps

S [Azure Logic Apps](../logic-apps/logic-apps-overview.md) a integrovaným konektorem HTTP + Swagger můžete automatizovat pracovní postupy, které pravidelně volají libovolný koncový bod REST prostřednictvím [souboru Swagger](https://swagger.io) vytvářením aplikací logiky. Aktivační událost a akce HTTP + Swagger fungují stejně jako [aktivační událost a akce PROTOKOLU HTTP,](connectors-native-http.md) ale poskytují lepší prostředí v návrháři aplikací logiky tím, že vystaví strukturu rozhraní API a výstupy popsané souborem Swagger. Chcete-li implementovat aktivační událost dotazování, postupujte podle způsobu dotazování, který je popsán v [části Vytvoření vlastních api pro volání jiných api, služeb a systémů z aplikací logiky](../logic-apps/logic-apps-create-api-app.md#polling-triggers).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Adresa URL souboru Swagger (nikoli OpenAPI), který popisuje cílový koncový bod REST

  Koncový bod REST musí obvykle splňovat tato kritéria pro konektor pracovat:

  * Soubor Swagger musí být hostován na adrese URL HTTPS, která je veřejně přístupná.

  * Soubor Swagger musí mít povoleno [sdílení prostředků mezi zdroji (CORS).](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)

  Chcete-li odkazovat na soubor Swagger, který není hostovaný nebo který nesplňuje požadavky na zabezpečení a křížový původ, můžete [nahrát soubor Swagger do kontejneru s objektem blob v účtu úložiště Azure](#host-swagger)a povolit CORS na tomto účtu úložiště, abyste mohli odkazovat na soubor.

  Příklady v tomto tématu používají [rozhraní API pro rozpoznávání tváře cognitive services](https://docs.microsoft.com/azure/cognitive-services/face/overview), které vyžaduje účet služeb Cognitive Services a [přístupový klíč](../cognitive-services/cognitive-services-apis-create-account.md).

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* Aplikace logiky, odkud chcete volat cílový koncový bod. Chcete-li začít s aktivační událostí HTTP + Swagger, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci HTTP + Swagger, spusťte aplikaci logiky s libovolnou aktivační událostí, kterou chcete. Tento příklad používá aktivační událost HTTP + Swagger jako první krok.

## <a name="add-an-http--swagger-trigger"></a>Přidání aktivační události HTTP + Swagger

Tato integrovaná aktivační událost odešle požadavek HTTP na adresu URL souboru Swagger, který popisuje rozhraní REST API a vrátí odpověď, která obsahuje obsah tohoto souboru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Otevřete prázdnou aplikaci logiky v Návrháři aplikací logiky.

1. Na návrháři, do vyhledávacího pole, zadejte "chvástání" jako filtr. Ze seznamu **Aktivační události** vyberte aktivační událost HTTP **+ Swagger.**

   ![Vyberte aktivační událost HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-trigger.png)

1. Do pole **Adresa URL swaggerendpointu** zadejte adresu URL souboru Swagger a vyberte **Další**.

   Tento příklad používá adresu URL Swagger, která se nachází v oblasti Západní USA pro [rozhraní API pro rozpoznávání tváře cognitive services](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Zadejte adresu URL koncového bodu Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-parameters.png)

1. Když návrhář zobrazí operace popsané souborem Swagger, vyberte operaci, kterou chcete použít.

   ![Operace v souboru Swagger](./media/connectors-native-http-swagger/http-swagger-trigger-operations.png)

1. Zadejte hodnoty pro parametry aktivační události, které se liší v závislosti na vybrané operaci, které chcete zahrnout do volání koncového bodu. Nastavte opakování pro to, jak často má aktivační událost volat koncový bod.

   Tento příklad přejmenuje aktivační událost na "HTTP + Swagger trigger: Face - Detect" tak, aby krok měl popisnější název.

   ![Podrobnosti o operaci](./media/connectors-native-http-swagger/http-swagger-trigger-operation-details.png)

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

   Další informace o typech ověřování dostupných pro protokol HTTP + Swagger naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Pokračujte v vytváření pracovního postupu aplikace logiky pomocí akcí, které se spustí při aktivaci aktivační události.

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

## <a name="add-an-http--swagger-action"></a>Přidání akce HTTP + Swagger

Tato předdefinovaná akce vytvoří požadavek HTTP na adresu URL souboru Swagger, který popisuje rozhraní REST API a vrátí odpověď, která obsahuje obsah tohoto souboru.

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Pod krokem, kam chcete přidat akci HTTP + Swagger, vyberte **Nový krok**.

   Chcete-li přidat akci mezi kroky, přesuňte ukazatel myši na šipku mezi kroky. Vyberte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.

1. Na návrháři, do vyhledávacího pole, zadejte "chvástání" jako filtr. Ze seznamu **Akce** vyberte akci **HTTP + Swagger.**

    ![Vyberte akci HTTP + Swagger](./media/connectors-native-http-swagger/select-http-swagger-action.png)

1. Do pole **Adresa URL swaggerendpointu** zadejte adresu URL souboru Swagger a vyberte **Další**.

   Tento příklad používá adresu URL Swagger, která se nachází v oblasti Západní USA pro [rozhraní API pro rozpoznávání tváře cognitive services](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236):

   `https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/export?DocumentFormat=Swagger&ApiName=Face%20API%20-%20V1.0`

   ![Zadejte adresu URL koncového bodu Swagger](./media/connectors-native-http-swagger/http-swagger-action-parameters.png)

1. Když návrhář zobrazí operace popsané souborem Swagger, vyberte operaci, kterou chcete použít.

   ![Operace v souboru Swagger](./media/connectors-native-http-swagger/http-swagger-action-operations.png)

1. Zadejte hodnoty parametrů akce, které se liší v závislosti na vybrané operaci, které chcete zahrnout do volání koncového bodu.

   Tento příklad nemá žádné parametry, ale přejmenuje akci na "HTTP + Swagger akce: Face - Identifikovat" tak, aby krok má více popisný název.

   ![Podrobnosti o operaci](./media/connectors-native-http-swagger/http-swagger-action-operation-details.png)

1. Chcete-li přidat další dostupné parametry, otevřete seznam **Přidat nový parametr** a vyberte požadované parametry.

   Další informace o typech ověřování dostupných pro protokol HTTP + Swagger naleznete v tématu [Přidání ověřování k odchozím voláním](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Až budete hotovi, nezapomeňte uložit aplikaci logiky. Na panelu nástrojů návrháře vyberte **Uložit**.

<a name="host-swagger"></a>

## <a name="host-swagger-in-azure-storage"></a>Host Swagger v Azure Storage

Můžete odkazovat na soubor Swagger, který není hostovaný nebo který nesplňuje požadavky na zabezpečení a cross-origin nahráním tohoto souboru do kontejneru blob v účtu úložiště Azure a povolením CORS v tomto účtu úložiště. Pokud chcete vytvářet, nastavovat a ukládat soubory Swagger ve Službě Azure, postupujte takto:

1. [Vytvořte účet úložiště Azure](../storage/common/storage-create-storage-account.md).

1. Teď povolte CORS pro objekt blob. V nabídce účtu úložiště vyberte **cors**. Na kartě **Služba objektu Blob** zadejte tyto hodnoty a pak vyberte **Uložit**.

   | Vlastnost | Hodnota |
   |----------|-------|
   | **Povolené zdroje** | `*` |
   | **Povolené metody** | `GET`, `HEAD`, `PUT` |
   | **Povolené hlavičky** | `*` |
   | **Zveřejněné hlavičky** | `*` |
   | **Maximální věk** (v sekundách) | `200` |
   |||

   I když tento příklad používá [portál Azure](https://portal.azure.com), můžete použít nástroj, jako je [Například Průzkumník a ukládání dat Azure](https://storageexplorer.com/), nebo toto nastavení automaticky nakonfigurovat pomocí tohoto ukázkového [skriptu prostředí PowerShell](https://github.com/logicappsio/EnableCORSAzureBlob/blob/master/EnableCORSAzureBlob.ps1).

1. [Vytvořte kontejner objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md). V podokně **Přehled** kontejneru vyberte **Změnit úroveň přístupu**. V seznamu **Úroveň veřejného přístupu** vyberte **objekt Blob (anonymní přístup pro čtení pouze pro objekty BLOB)** a vyberte **OK**.

1. [Nahrajte soubor Swagger do kontejneru objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), a to buď prostřednictvím [portálu Azure nebo](https://portal.azure.com) [Průzkumníka úložiště Azure](https://storageexplorer.com/).

1. Chcete-li odkazovat na soubor v kontejneru objektů blob, použijte odkaz HTTPS, který následuje za tímto formátem, který rozlišuje malá a velká písmena:

   `https://<storage-account-name>.blob.core.windows.net/<blob-container-name>/<swagger-file-name>`

## <a name="connector-reference"></a>Referenční informace ke konektorům

Zde je více informací o výstupech z aktivační události HTTP + Swagger nebo akce. Volání HTTP + Swagger vrátí tyto informace:

| Název vlastnosti | Typ | Popis |
|---------------|------|-------------|
| Záhlaví | objekt | Záhlaví z požadavku |
| text | objekt | Objekt JSON | Objekt s obsahem těla z požadavku |
| stavový kód | int | Stavový kód z požadavku |
|||

| Kód stavu | Popis |
|-------------|-------------|
| 200 | OK |
| 202 | Accepted |
| 400 | Chybný požadavek |
| 401 | Neautorizováno |
| 403 | Forbidden |
| 404 | Nebyl nalezen. |
| 500 | Vnitřní chyba serveru. Došlo k neznámé chybě. |
|||

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)
