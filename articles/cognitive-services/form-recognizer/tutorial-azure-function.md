---
title: 'Kurz: použití funkce Azure ke zpracování uložených dokumentů'
titleSuffix: Azure Cognitive Services
description: V této příručce se dozvíte, jak pomocí funkce Azure aktivovat zpracování dokumentů, které se nahrají do kontejneru úložiště objektů BLOB v Azure.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: lajanuar
ms.openlocfilehash: 8c72a018f03b5284d3fc53be02d9eb526cdfcf28
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722238"
---
# <a name="tutorial-use-an-azure-function-to-process-stored-documents"></a>Kurz: použití funkce Azure ke zpracování uložených dokumentů

Nástroj pro rozpoznávání formulářů můžete použít jako součást kanálu automatizovaného zpracování dat sestaveného s Azure Functions. V této příručce se dozvíte, jak používat funkci Azure ke zpracování dokumentů nahraných do kontejneru úložiště objektů BLOB v Azure. Tento pracovní postup extrahuje data tabulky z uložených dokumentů pomocí služby rozložení pro rozpoznávání formulářů a uloží data tabulky do souboru. CSV v Azure. Data pak můžete zobrazit pomocí Microsoft Power BI (zde není pokrytá).

> [!div class="mx-imgBorder"]
> ![Diagram pracovního postupu služby Azure](./media/tutorial-azure-function/workflow-diagram.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu Azure Storage
> * Vytvoření projektu služby Azure Functions
> * Extrakce dat rozložení z nahraných formulářů
> * Nahrát data rozložení do Azure Storage

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Vytvoření prostředku pro rozpoznávání formulářů "  target="_blank"> vytvoření prostředku pro rozpoznávání formulářů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal k získání klíče a koncového bodu pro rozpoznávání formuláře. Po nasazení klikněte na **Přejít k prostředku**.
  * K připojení aplikace k rozhraní API pro rozpoznávání formulářů budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
  * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Místní dokument PDF, který se má analyzovat Můžete si stáhnout tento [ukázkový dokument](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout.pdf) , který chcete použít.
* Je nainstalovaný [Python 3.8. x](https://www.python.org/downloads/) .
* [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) nainstalován.
* [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cbash#install-the-azure-functions-core-tools) nainstalován.
* Visual Studio Code s následujícími nainstalovanými rozšířeními:
  * [Rozšíření Azure Functions](https://docs.microsoft.com/azure/developer/python/tutorial-vs-code-serverless-python-01#visual-studio-code-python-and-the-azure-functions-extension)
  * [Rozšíření Pythonu](https://code.visualstudio.com/docs/python/python-tutorial#_install-visual-studio-code-and-the-python-extension)

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu Azure Storage

[Vytvořte účet Azure Storage](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) v Azure Portal. Jako druh účtu vyberte **StorageV2** .

V levém podokně vyberte kartu **CORS** a odeberte existující zásady CORS, pokud existují.

Po nasazení, vytvořte dva prázdné kontejnery úložiště objektů BLOB s názvem **test** a **Output**.

## <a name="create-an-azure-functions-project"></a>Vytvoření projektu služby Azure Functions

Otevřete Visual Studio Code. Pokud jste nainstalovali rozšíření Azure Functions, měli byste vidět logo Azure v levém navigačním podokně. Vyberte ji. Vytvořte nový projekt a po zobrazení výzvy vytvořte místní složku, **coa_new** má projekt obsahovat.

![Tlačítko pro vytvoření funkce VSCode](./media/tutorial-azure-function/vs-code-create-function.png)


Zobrazí se výzva ke konfiguraci řady nastavení:
* Na příkazovém řádku pro **Výběr jazyka** vyberte Python.
* V příkazovém řádku **Vybrat šablonu** vyberte aktivační událost Azure Blob Storage. Pak zadejte název výchozí triggeru.
* Na příkazovém řádku pro **nastavení vyberte možnost** vytvořit nové nastavení místní aplikace.
* Vyberte své předplatné Azure s účtem úložiště, který jste vytvořili. Pak musíte zadat název kontejneru úložiště (v tomto případě `test/{name}` ).
* Přihlaste se k otevření projektu v aktuálním okně. 

![Příklad výzvy k vytvoření výzvy VSCode](./media/tutorial-azure-function/vs-code-prompt.png)

Až provedete tyto kroky, VSCode přidá nový projekt Azure Function se skriptem *\_ \_ init \_ \_ . py* Python. Tento skript se aktivuje, když se do kontejneru úložiště **testu** pošle soubor, ale neprovede se žádné akce.

## <a name="test-the-function"></a>Testování funkce

Stisknutím klávesy F5 spusťte funkci Basic. VSCode vás vyzve k výběru účtu úložiště pro rozhraní. Vyberte účet úložiště, který jste vytvořili, a pokračujte.

Otevřete Průzkumník služby Azure Storage a nahrajte do kontejneru **testu** ukázkový dokument PDF. Pak zkontrolujte terminál VSCode. Skript by měl protokolovat, že se aktivoval při nahrávání PDF.

![VSCode Terminal test](./media/tutorial-azure-function/vs-code-terminal-test.png)


Před pokračováním zastavte skript.

## <a name="add-form-processing-code"></a>Přidat kód pro zpracování formuláře

Dále do skriptu Pythonu přidáte vlastní kód, který bude volat službu rozpoznávání formulářů a analyzovat nahrané dokumenty pomocí [rozhraní API pro rozložení](concept-layout.md)pro rozpoznávání formulářů.

V VSCode přejděte do souboru *requirements.txt* funkce. To definuje závislosti pro váš skript. Přidejte do souboru následující balíčky Pythonu:

```
cryptography
azure-functions
azure-storage-blob
azure-identity
requests
pandas
numpy
```

Pak otevřete skript *\_ \_ init \_ \_ . py* . Přidejte následující příkazy `import`:

```Python
import logging
from azure.storage.blob import BlobServiceClient
import azure.functions as func
import json
import time
from requests import get, post
import os
from collections import OrderedDict
import numpy as np
import pandas as pd
```

Vygenerovanou funkci můžete nechat vygenerovanou `main` . Do této funkce přidáte vlastní kód.

```python
# This part is automatically generated
def main(myblob: func.InputStream):
    logging.info(f"Python blob trigger function processed blob \n"
    f"Name: {myblob.name}\n"
    f"Blob Size: {myblob.length} bytes")
```

Následující blok kódu volá rozhraní API [analyzovat rozložení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) pro nahraný dokument. Vyplňte svůj koncový bod a hodnoty klíče. 


# <a name="version-20"></a>[verze 2,0](#tab/2-0)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```

# <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/2-1)

```Python
# This is the call to the Form Recognizer endpoint
    endpoint = r"Your Form Recognizer Endpoint"
    apim_key = "Your Form Recognizer Key"
    post_url = endpoint + "/formrecognizer/v2.1-preview.3/Layout/analyze"
    source = myblob.read()

    headers = {
    # Request headers
    'Content-Type': 'application/pdf',
    'Ocp-Apim-Subscription-Key': apim_key,
        }

    text1=os.path.basename(myblob.name)
```
---

> [!IMPORTANT]
> Přejděte na Azure Portal. Pokud se prostředek pro rozpoznávání formulářů, který jste vytvořili v části **předpoklady** , nasadil úspěšně, klikněte na tlačítko **Přejít k prostředku** v části **Další kroky**. Klíč a koncový bod můžete najít na stránce **klíč a koncový bod** prostředku v části **Správa prostředků**. 
>
> Nezapomeňte odebrat klíč z kódu, až budete hotovi, a nikdy ho zveřejnit. V případě produkčního prostředí zvažte použití zabezpečeného způsobu ukládání a přístupu k vašim přihlašovacím údajům. Další informace najdete v článku o [zabezpečení Cognitive Services](../cognitive-services-security.md) .

Dále přidejte kód pro dotazování služby a získání vrácených dat. 


```Python
resp = requests.post(url = post_url, data = source, headers = headers)
    if resp.status_code != 202:
        print("POST analyze failed:\n%s" % resp.text)
        quit()
    print("POST analyze succeeded:\n%s" % resp.headers)
    get_url = resp.headers["operation-location"]

    wait_sec = 25
    
    time.sleep(wait_sec)
    # The layout API is async therefore the wait statement
    
    resp =requests.get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
    
    resp_json = json.loads(resp.text)
    
    
    status = resp_json["status"]
    
    
    if status == "succeeded":
        print("Layout Analysis succeeded:\n%s")
        results=resp_json
    else:
        print("GET Layout results failed:\n%s")
        quit()

    results=resp_json
```

Pak přidejte následující kód pro připojení ke kontejneru **výstup** Azure Storage. Zadejte vlastní hodnoty pro název a klíč účtu úložiště. Klíč můžete získat na kartě **přístupové klíče** prostředku úložiště v Azure Portal.

```Python
# This is the connection to the blob storage, with the Azure Python SDK
    blob_service_client = BlobServiceClient.from_connection_string("DefaultEndpointsProtocol=https;AccountName="Storage Account Name";AccountKey="storage account key";EndpointSuffix=core.windows.net")
    container_client=blob_service_client.get_container_client("output")
```

Následující kód analyzuje vrácenou odpověď na rozpoznávání formuláře, vytvoří soubor. csv a nahraje ho do **výstupního** kontejneru. 


> [!IMPORTANT]
> Bude pravděpodobně nutné upravit tento kód tak, aby odpovídal struktuře vlastních dokumentů formuláře.

```Python
# The code below is how I extract the json format into tabular data 
    # Please note that you need to adjust the code below to your form structure
    # It probably won't work out-of-box for your specific form
    pages = results["analyzeResult"]["pageResults"]

    def make_page(p):
        res=[]
        res_table=[]
        y=0
        page = pages[p]
        for tab in page["tables"]:
            for cell in tab["cells"]:
                res.append(cell)
                res_table.append(y)
            y=y+1
    
        res_table=pd.DataFrame(res_table)
        res=pd.DataFrame(res)
        res["table_num"]=res_table[0]
        h=res.drop(columns=["boundingBox","elements"])
        h.loc[:,"rownum"]=range(0,len(h))
        num_table=max(h["table_num"])
        return h, num_table, p

    h, num_table, p= make_page(0)   

    for k in range(num_table+1):
        new_table=h[h.table_num==k]
        new_table.loc[:,"rownum"]=range(0,len(new_table))
        row_table=pages[p]["tables"][k]["rows"]
        col_table=pages[p]["tables"][k]["columns"]
        b=np.zeros((row_table,col_table))
        b=pd.DataFrame(b)
        s=0
        for i,j in zip(new_table["rowIndex"],new_table["columnIndex"]):
            b.loc[i,j]=new_table.loc[new_table.loc[s,"rownum"],"text"]
            s=s+1

```

Nakonec poslední blok kódu nahraje extrahovanou tabulku a textová data do vašeho elementu BLOB Storage.

```Python
    # Here is the upload to the blob storage
    tab1_csv=b.to_csv(header=False,index=False,mode='w')
    name1=(os.path.splitext(text1)[0]) +'.csv'
    container_client.upload_blob(name=name1,data=tab1_csv)
```

## <a name="run-the-function"></a>Spuštění funkce

Stisknutím klávesy F5 spusťte funkci znovu. Pomocí Průzkumník služby Azure Storage nahrajte vzorový formulář PDF do kontejneru úložiště **testu** . Tato akce by měla aktivovat skript, který se má spustit, a měli byste vidět výsledný soubor. CSV (zobrazený jako tabulka) ve **výstupním** kontejneru.

Tento kontejner můžete připojit k Power BI a vytvořit tak bohatou vizualizaci dat, která obsahuje.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat funkci Azure napsanou v Pythonu k automatickému zpracování nahraných dokumentů PDF a výstup jejich obsahu ve více uživatelsky přívětivém formátu. Dále se naučíte, jak použít Power BI k zobrazení dat.

> [!div class="nextstepaction"]
> [Microsoft Power BI](https://powerbi.microsoft.com/integrations/azure-table-storage/)

* [Co je služba Rozpoznávání formulářů?](overview.md)
* Další informace o [rozhraní API pro rozložení](concept-layout.md)
