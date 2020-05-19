---
title: 'Rychlý Start: QnA Maker klientskou knihovnu pro Python'
description: V tomto rychlém startu se dozvíte, jak začít s knihovnou klienta QnA Maker pro Python.
ms.topic: include
ms.date: 04/27/2020
ms.openlocfilehash: 8a180096e21203dd45d806ceca14794c985d664a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82875990"
---
Použití klientské knihovny QnA Maker pro Python pro:

* Vytvoření znalostní báze
* Aktualizace znalostní báze
* Publikování znalostní báze
* Získat publikovaný klíč koncového bodu
* Počkat na dlouhodobě běžící úlohu
* Odstranit znalostní bázi

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python)  |  [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker)  |  [Balíček (PyPi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/)  |  [Ukázky Pythonu](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
* [Python 3.x](https://www.python.org/)
* Jakmile budete mít předplatné Azure, vytvořte v Azure Portal [prostředek QnA maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) , abyste získali svůj klíč a koncový bod pro vytváření obsahu. Po nasazení vyberte **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API služby QnA Maker budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku Azure QnA Maker

Azure Cognitive Services jsou představovány prostředky Azure, ke kterým jste se přihlásili. Vytvořte prostředek pro QnA Maker pomocí [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo rozhraní příkazového [řádku Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na vašem místním počítači.

Po získání klíče z prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) pro prostředek s názvem `QNAMAKER_KEY` a `QNAMAKER_HOST` . Použijte hodnoty klíč a koncový bod nalezené v Azure Portal.

### <a name="install-the-python-library-for-qna-maker"></a>Nainstalujte knihovnu Pythonu pro QnA Maker

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí nástroje:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

## <a name="object-model"></a>Objektový model

Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.qn_amaker_client?view=azure-python) .

Po vytvoření klienta použijte [znalostní bázi znalostní báze](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations?view=azure-python) k vytvoření, správě a publikování znalostní báze.

Pro okamžité operace metoda obvykle vrací objekt JSON indikující stav. V případě dlouhotrvajících operací je odpověď ID operace. Zavolejte [klientovi. Operations. GetDetails](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operationstatetype?view=azure-python) – metoda s ID operace, která určuje [stav žádosti](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operationstatetype?view=azure-python).


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak pomocí QnA Maker klientské knihovny pro Python provést následující akce:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Stáhnout znalostní bázi](#download-a-knowledge-base)
* [Odstranění znalostní báze](#delete-a-knowledge-base)
* [Získat stav operace](#get-status-of-an-operation)

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Vytvořte novou aplikaci v Pythonu v upřednostňovaném editoru nebo integrovaném vývojovém prostředí. Pak importujte následující knihovny.

[!code-python[Dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=dependencies)]

Vytvořte proměnné pro koncový bod a klíč Azure prostředku. Pokud jste po spuštění aplikace vytvořili proměnnou prostředí, budete muset zavřít a znovu otevřít Editor, rozhraní IDE nebo prostředí, na kterém je spuštěný, abyste měli přístup k této proměnné.

|Proměnná prostředí|proměnná|Příklad|
|--|--|--|
|`QNAMAKER_KEY`|`subscription_key`|Klíč je řetězec znaků 32 a je k dispozici v Azure Portal v prostředku QnA Maker na stránce rychlý Start. To není totéž jako klíč koncového bodu předpovědi.|
|`QNAMAKER_HOST`|`host`| Váš koncový bod pro vytváření obsahu ve formátu `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com` obsahuje název vašeho **prostředku**. Nejedná se o stejnou adresu URL, která se používá k dotazování koncového bodu předpovědi.|

[!code-python[Azure resource variables](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Ověření klienta

Potom vytvořte objekt CognitiveServicesCredentials s klíčem a použijte ho u svého koncového bodu k vytvoření objektu [QnAMakerClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.qnamakerclient?view=azure-python) .


[!code-python[Authorization to resource key](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=authorization)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

 Objekt klienta použijte k získání objektu [operací znalostní báze](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations?view=azure-python) .

Znalostní báze ukládá páry dotazů a odpovědí pro objekt [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.create_kb_dto?view=azure-python) ze tří zdrojů:

* Pro **redakční obsah**použijte objekt [QnADTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qn_adto?view=azure-python) .
* Pro **soubory**použijte objekt [FileDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.file_dto?view=azure-python) .
* Pro **adresy URL**použijte seznam řetězců.

Zavolejte metodu [Create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) a potom předejte vrácené ID operace do metody [Operations. GetDetails](#get-status-of-an-operation) pro dotazování na stav.

[!code-python[Create a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=createkb&highlight=15)]

Aby bylo možné úspěšně vytvořit znalostní bázi, ujistěte se, že je zahrnutá [`_monitor_operation`](#get-status-of-an-operation) funkce, na kterou se odkazuje v kódu výše.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat tak, že do metody [aktualizace](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) předáte ID znalostní báze a [UpdateKbOperationDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto?view=azure-python) obsahující objekty pro [Přidání](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_add?view=azure-python), [aktualizaci](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_update?view=azure-python)a [odstranění](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.update_kb_operation_dto_delete?view=azure-python) DTO. K určení, jestli se aktualizace zdařila, použijte metodu [Operational detail](#get-status-of-an-operation) .

[!code-python[Update a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=updatekb&highlight=2)]

Aby [`_monitor_operation`](#get-status-of-an-operation) bylo možné úspěšně aktualizovat znalostní bázi, ujistěte se, že obsahuje funkci, na kterou se odkazuje ve výše uvedeném kódu.

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [Publish](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) . Tím se převezme aktuální uložený a vycvičený model, na který odkazuje ID znalostní báze, a publikuje ho na koncovém bodu.

[!code-python[Publish a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=publishkb&highlight=2)]

## <a name="download-a-knowledge-base"></a>Stáhnout znalostní bázi

Použijte metodu [Download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) ke stažení databáze jako seznamu [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.qn_adocuments_dto?view=azure-python). Nejedná _se o ekvivalent exportu_ QnA Makerového portálu ze stránky **Nastavení** , protože výsledek této metody není soubor TSV.

[!code-python[Download a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=downloadkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní bázi pomocí metody [Delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.operations.knowledgebase_operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) s parametrem ID znalostní báze.

[!code-python[Delete a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=deletekb&highlight=2)]

## <a name="get-status-of-an-operation"></a>Získat stav operace

Některé metody, jako například vytváření a aktualizace, mohou trvat dostatek času, než čeká na dokončení procesu, a vrátí se [operace](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.authoring.models.operation.operation?view=azure-python) . K určení stavu původní metody použijte ID operace z operace k dotazování (s logikou opakování).

Volání _setTimeout_ v následujícím bloku kódu slouží k simulaci asynchronního kódu. Nahraďte logikou opakování.

[!code-python[Monitor an operation](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=monitorOperation&highlight=7)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte aplikaci pomocí `python knowledgebase_quickstart.py` příkazu z adresáře aplikace.

Všechny fragmenty kódu v tomto článku jsou [k dispozici](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) a lze je spustit jako jeden soubor.

```console
python knowledgebase_quickstart.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)
