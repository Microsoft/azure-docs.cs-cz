---
ms.openlocfilehash: 03aa3919e1da982bb8a8c235bb598f5b94df1ebf
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986649"
---

Pomocí klientské knihovny QnA Maker pro Python:

* Vytvoření znalostní báze
* Správa znalostní báze
* Publikování znalostní báze

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker?view=azure-python) | [Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-knowledge-qnamaker) | [knihovny Package (pypi)](https://pypi.org/project/azure-cognitiveservices-knowledge-qnamaker/) | [Python](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Nastavení

### <a name="create-a-qna-maker-azure-resource"></a>Vytvoření prostředku Azure pro QnA Maker

Azure Cognitive Services jsou reprezentované prostředky Azure, které si předplatíte. Vytvořte prostředek pro QnA Maker pomocí [portálu Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) nebo [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na místním počítači.

Po získání klíče z prostředku [vytvořte proměnné prostředí](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) `QNAMAKER_KEY` pro `QNAMAKER_HOST`prostředek s názvem a . Použijte hodnoty klíče a koncového bodu, které se nacházejí na webu Azure Portal.

### <a name="install-the-python-library-for-qna-maker"></a>Instalace knihovny pythonu pro QnA Maker

Po instalaci Pythonu můžete nainstalovat klientskou knihovnu pomocí:

```console
pip install azure-cognitiveservices-knowledge-qnamaker
```

## <a name="object-model"></a>Objektový model

Vytvořte objekt [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) s klíčem a použijte jej s koncovým bodem k vytvoření objektu [QnAMakerClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python)

Po vytvoření klienta použijte [znalostní bázi](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python) k vytvoření, správě a publikování znalostní báze.

Pro okamžité operace metoda obvykle vrátí objekt JSON označující stav. Pro dlouhotrvající operace je odpovědí ID operace. Zavolejte [klientovi. Operations.getDetails](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.operations%28class%29?view=azure-python#get-details-operation-id--custom-headers-none--raw-false----operation-config-) metoda s ID operace k určení [stavu požadavku](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-python).


## <a name="code-examples"></a>Příklady kódu

Tyto fragmenty kódu ukazují, jak provést následující akce v klientské knihovně QnA Maker pro python:

* [Vytvoření znalostní báze](#create-a-knowledge-base)
* [Aktualizace znalostní báze](#update-a-knowledge-base)
* [Publikování znalostní báze](#publish-a-knowledge-base)
* [Stažení znalostní báze](#download-a-knowledge-base)
* [Odstranění znalostní báze](#delete-a-knowledge-base)
* [Získání stavu operace](#get-status-of-an-operation)

## <a name="create-a-new-python-application"></a>Vytvoření nové aplikace pythonu

Vytvořte novou aplikaci Pythonu ve vašem preferovaném editoru nebo IDE. Pak importujte následující knihovny.

[!code-python[Dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=dependencies)]

Vytvořte proměnné pro koncový bod a klíč Azure vašeho prostředku. Pokud jste vytvořili proměnnou prostředí po spuštění aplikace, budete muset zavřít a znovu otevřít editor, IDE nebo prostředí, které ji spustilo, abyste měli přístup k proměnné.

|Proměnná prostředí|proměnná|Příklad|
|--|--|--|
|`QNAMAKER_KEY`|`subscription_key`|Klíč je řetězec 32 znaků a je k dispozici na portálu Azure, na prostředek QnA Maker, na stránce Rychlý start. To není stejné jako klíč koncového bodu předpověď.|
|`QNAMAKER_HOST`|`host`| Koncový bod pro vytváření ve `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com`formátu aplikace obsahuje **název prostředku**. Toto není stejná adresa URL, která se používá k dotazování koncového bodu předpověď.|

[!code-python[Azure resource variables](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=resourcekeys)]

## <a name="authenticate-the-client"></a>Ověření klienta

Dále vytvořte objekt CognitiveServicesCredentials s klíčem a použijte jej s koncovým bodem k vytvoření objektu [QnAMakerClient.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-python)


[!code-python[Authorization to resource key](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=authorization)]

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

 Pomocí objektu klienta získáte objekt [operací znalostní báze Knowledge Base.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python)

Znalostní báze ukládá dvojice otázek a odpovědí pro objekt [CreateKbDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-python) ze tří zdrojů:

* Pro **redakční obsah**použijte objekt [QnADTO.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-python)
* Pro **soubory**použijte objekt [FileDTO.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-python)
* Pro **adresy URL**použijte seznam řetězců.

Volání [create](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#create-create-kb-payload--custom-headers-none--raw-false----operation-config-) metoda pak předat ID vrácené operace [operations.getDetails](#get-status-of-an-operation) metoda dotazování stavu.

[!code-python[Create a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=createkb&highlight=15)]

Ujistěte se, [`_monitor_operation`](#get-status-of-an-operation) že zahrnout funkci, odkazuje ve výše uvedeném kódu, aby bylo možné úspěšně vytvořit znalostní báze.

## <a name="update-a-knowledge-base"></a>Aktualizace znalostní báze

Znalostní bázi můžete aktualizovat předáním ID znalostní b-boperačního [souboru](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-python) obsahujícího [objekty add](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-python), [update](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-python)a [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-python) DTO do metody [aktualizace.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#update-kb-id--update-kb--custom-headers-none--raw-false----operation-config-) Pomocí metody [Operation.getDetail](#get-status-of-an-operation) určete, zda byla aktualizace úspěšná.

[!code-python[Update a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=updatekb&highlight=2)]

Ujistěte se, [`_monitor_operation`](#get-status-of-an-operation) že zahrnout funkci, odkazuje ve výše uvedeném kódu, aby bylo možné úspěšně aktualizovat znalostní báze.

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikujte znalostní bázi pomocí metody [publikování.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#publish-kb-id--custom-headers-none--raw-false----operation-config-) To trvá aktuální uložený a trénovaný model, na který odkazuje ID znalostní báze a publikuje jej v koncovém bodě.

[!code-python[Publish a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=publishkb&highlight=2)]

## <a name="download-a-knowledge-base"></a>Stažení znalostní báze

Pomocí metody [download](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#download-kb-id--environment--custom-headers-none--raw-false----operation-config-) stáhněte databázi jako seznam [QnADocumentsDTO](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-python). To _není_ ekvivalentní exportu portálu QnA Maker ze stránky **Nastavení,** protože výsledkem této metody není soubor TSV.

[!code-python[Download a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=downloadkb&highlight=2)]

## <a name="delete-a-knowledge-base"></a>Odstranění znalostní báze

Odstraňte znalostní báze pomocí metody [delete](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.operations.knowledgebaseoperations?view=azure-python#delete-kb-id--custom-headers-none--raw-false----operation-config-) s parametrem ID znalostní báze.

[!code-python[Delete a knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=deletekb&highlight=2)]

## <a name="get-status-of-an-operation"></a>Získání stavu operace

Některé metody, například vytvořit a aktualizovat, může trvat tolik času, že namísto čekání na dokončení procesu je vrácena [operace.](https://docs.microsoft.com/python/api/azure-cognitiveservices-knowledge-qnamaker/azure.cognitiveservices.knowledge.qnamaker.models.operation%28class%29?view=azure-python) Pomocí ID operace z operace k dotazování (s logikou opakování) určete stav původní metody.

Volání _setTimeout_ v následujícím bloku kódu se používá k simulaci asynchronního kódu. Nahraďte ji logikou opakování.

[!code-python[Monitor an operation](~/samples-qnamaker-python/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py?name=monitorOperation&highlight=7)]

## <a name="run-the-application"></a>Spuštění aplikace

Spusťte `python knowledgebase_quickstart.py` aplikaci s příkazem z adresáře aplikace.

Všechny fragmenty kódu v tomto článku jsou [k dispozici](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py) a lze je spustit jako jeden soubor.

```console
python knowledgebase_quickstart.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)
