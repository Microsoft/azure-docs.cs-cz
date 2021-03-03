---
title: Přístup k datům na základě identity ke službám úložiště v Azure
titleSuffix: Azure Machine Learning
description: Naučte se používat přístup k datům na základě identity k připojení ke službám úložiště v Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: dbfb4ea729b8360c7065d75cb3efbaf42b82c0da
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662458"
---
# <a name="connect-to-storage-with-identity-based-data-access-preview"></a>Připojení k úložišti pomocí přístupu k datům založeným na identitě (Preview)

>[!IMPORTANT]
> Funkce uvedené v tomto článku jsou ve verzi Preview a měly by se považovat za [experimentální](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) funkce ve verzi Preview, které se můžou kdykoli změnit.

V tomto článku se dozvíte, jak se připojit ke službám úložiště v Azure s přístupem k datům založeným na identitě a Azure Machine Learning úložišti dat pomocí [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).  

Úložiště dat obvykle používá přístup k datům na základě přihlašovacích údajů k potvrzení, že máte oprávnění pro přístup ke službě úložiště. Udržují informace o připojení, například ID předplatného a autorizaci tokenu, ve vašem [Key Vault](https://azure.microsoft.com/services/key-vault/) , který je přidružený k pracovnímu prostoru. Když vytvoříte úložiště dat, které používá přístup k datům založeným na identitě, k potvrzení, že máte oprávnění pro přístup ke službě úložiště, se použije vaše přihlášení k Azure ([Azure Active Directory token](../active-directory/fundamentals/active-directory-whatis.md)). V tomto scénáři se neukládají žádné přihlašovací údaje pro ověřování a v úložišti dat jsou uložené jenom informace o účtu úložiště. 

Pokud chcete vytvářet úložiště dat, která používají ověřování založené na přihlašovacích údajích, jako jsou přístupové klíče nebo instanční objekty, přečtěte si téma [připojení ke službám úložiště v Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Přístup k datům na základě identity v Azure Machine Learning

Existují dvě oblasti, jak použít přístup k datům na základě identity v Azure Machine Learning. Obzvláště při práci s důvěrnými daty a potřebější správy přístupu k datům. 

1. Přístup ke službám úložiště
1. Školení modelů strojového učení s privátními daty.

### <a name="accessing-storage-services"></a>Přístup ke službám úložiště

Ke službám úložiště se můžete připojit prostřednictvím přístupu k datům založeným na identitě s Azure Machine Learning úložiště dat nebo [Azure Machine Learning datových sad](how-to-create-register-datasets.md). 

Přihlašovací údaje pro ověřování se obvykle uchovávají v úložišti dat, které se používá k zajištění, že máte oprávnění pro přístup ke službě úložiště. Pokud jsou tyto přihlašovací údaje registrovány s úložištěm dat, může je každý uživatel s rolí *čtenáře* pracovního prostoru načíst, což může být v některých organizacích obavou zabezpečení. [Přečtěte si další informace o roli *čtenáře* pracovního prostoru](how-to-assign-roles.md#default-roles). 

Když použijete přístup k datům založeným na identitě, Azure Machine Learning vás vyzve k zadání tokenu Azure Active Directory pro ověřování přístupu k datům místo udržování přihlašovacích údajů v úložišti dat. Což umožňuje správu přístupu k datům na úrovni úložiště a zachovává důvěrné přihlašovací údaje. 

Stejné chování platí v případě, že

* [Vytvořte datovou sadu přímo z adres URL úložiště](#use-data-in-storage). 
* Pracujte s daty interaktivně prostřednictvím poznámkového bloku Jupyter na místním počítači nebo [instanci služby COMPUTE](concept-compute-instance.md).

> [!NOTE]
> Přihlašovací údaje uložené pomocí ověřování založeného na přihlašovacích údajích zahrnují: ID předplatného, tokeny sdíleného přístupového podpisu (SAS), přístupové klíče úložiště a informace instančního objektu, jako je ID klienta a ID tenanta.

### <a name="model-training-on-private-data"></a>Školení modelů u privátních dat

Některé scénáře strojového učení zahrnují školicí modely s privátními daty. V takových případech odborníci na data potřebují spustit školicí pracovní postupy bez ozáření s důvěrnými vstupními daty. V tomto scénáři se pro ověřování přístupu k datům používá spravovaná identita výpočetní služby. Správci úložiště tak můžou udělit přístup ke službě **úložiště dat objektů BLOB úložiště** spravované identitě, kterou výpočetní služba používá ke spuštění školicí úlohy a nikoli jednotlivým odborníkům na data. Přečtěte si, jak [nastavit spravovanou identitu na výpočetním prostředí](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

- Účet úložiště Azure s podporovaným typem úložiště. Ve verzi Preview jsou podporovány následující typy úložišť. 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Gen 1](../data-lake-store/index.yml)
    - [Azure Data Lake Gen 2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- [Sada SDK Azure Machine Learning pro Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

- Pracovní prostor služby Azure Machine Learning.
  
  Buď [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) nebo použijte [existující sadu pomocí sady Python SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Přístupová oprávnění k úložišti

Aby bylo zajištěno zabezpečené připojení k vaší službě úložiště v Azure, Azure Machine Learning vyžaduje, abyste měli oprávnění pro přístup k odpovídajícímu úložišti dat.

Přístup k datům na základě identity podporuje jenom připojení k následujícím službám úložiště:

* Azure Blob Storage
* Azure Data Lake generace 1
* Azure Data Lake generace 2
* Databáze Azure SQL

Chcete-li získat přístup k těmto službám úložiště, je nutné mít k dispozici minimální přístup ke **čtečce dat objektů BLOB úložiště** . Přečtěte si další informace o [čtečce dat objektů BLOB úložiště](../role-based-access-control/built-in-roles.md#storage-blob-data-reader). [Úroveň přístupu můžou změnit](../storage/common/storage-auth-aad-rbac-portal.md)jenom vlastníci účtu úložiště pomocí Azure Portal.

Pokud provádíte školení modelu na vzdáleném cílovém výpočetním cíli, musí být identita COMPUTE udělena s alespoň rolí **úložiště dat objektů BLOB úložiště** ve službě úložiště. Přečtěte si, jak [nastavit spravovanou identitu na výpočetní](how-to-create-attach-compute-cluster.md#managed-identity)výkon.

## <a name="work-with-virtual-networks"></a>Práce s virtuálními sítěmi

Ve výchozím nastavení Azure Machine Learning nemůže komunikovat s účtem úložiště, který je za bránou firewall nebo v rámci virtuální sítě.

Účty úložiště se dají nakonfigurovat tak, aby povolovaly přístup jenom v rámci konkrétních virtuálních sítí, které vyžadují další konfigurace, aby se data neunikla mimo síť. Toto chování je stejné jako pro přístup k datům založeným na přihlašovacích údajích, informace o tom, [Jaké konfigurace jsou potřeba a jak je použít pro scénáře virtuální sítě](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Vytvoření a registrace úložišť dat

Když zaregistrujete službu úložiště v Azure jako úložiště dat, automaticky vytvoříte a zaregistrujete toto úložiště dat do konkrétního pracovního prostoru. Přečtěte si tyto oddíly: [oprávnění k přístupu k úložišti](#storage-access-permissions) pro doprovodné materiály k požadovaným typům oprávnění a informace o tom, jak se připojit k úložišti dat za virtuálními sítěmi, najdete v článku [spolupráce s virtuální sítí](#work-with-virtual-networks) .

V následujícím kódu si všimněte nepřítomnosti parametrů ověřování, například, `sas_token` , `account_key` `subscription_id` nebo instančního objektu `client_id` . Tato opomenutí znamená, že Azure Machine Learning použít k ověřování přístup k datům na základě identity. Vzhledem k tomu, že vytváření úložišť dat obvykle probíhá interaktivně v poznámkovém bloku nebo prostřednictvím studia, používá se pro ověřování přístupu k datům váš Azure Active Directory token.

> [!NOTE]
> Názvy úložišť dat by se měly skládat jenom z malých písmen, číslic a podtržítek. 

### <a name="azure-blob-container"></a>Kontejner objektů blob Azure

Pokud chcete zaregistrovat kontejner objektů blob Azure jako úložiště dat, použijte [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Následující kód vytvoří a zaregistruje `credentialless_blob` úložiště dat do `ws` pracovního prostoru a přiřadí ho proměnné, `blob_datastore` . Toto úložiště dat přistupuje k `my_container_name` kontejneru objektů BLOB v `my-account-name` účtu úložiště.

```Python
# create blob datastore without credentials
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-generation-1"></a>Azure Data Lake Storage generace 1

Pro úložiště dat Azure Data Lake Storage generace 1 (ADLS fin 1) použijte [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) k registraci úložiště dat, které se připojuje k úložišti Azure datalake generace 1.

Následující kód vytvoří a zaregistruje `credentialless_adls1` úložiště dat do `workspace` pracovního prostoru a přiřadí ho proměnné, `adls_dstore` . Toto úložiště dat přistupuje k `adls_storage` účtu úložiště Azure Data Lake Store.

```Python
# create adls gen1 without credentials
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-generation-2"></a>Azure Data Lake Storage generace 2

Pro úložiště dat Azure Data Lake Storage generace 2 (ADLS Gen 2) použijte [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) k registraci úložiště dat, které se připojuje k úložišti Azure datalake Gen 2.

Následující kód vytvoří a zaregistruje `credentialless_adls2` úložiště dat do `ws` pracovního prostoru a přiřadí ho proměnné, `adls2_dstore` . Toto úložiště dat přistupuje k systému souborů `tabular` v `myadls2` účtu úložiště.  

```python
# createn adls2 datastore without credentials
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Použití dat v úložišti

[Azure Machine Learning datové sady](how-to-create-register-datasets.md) jsou doporučeným způsobem, jak pracovat s daty ve službě storage pomocí Azure Machine Learning. 

Datové sady zabalí vaše data do laxně vytvářená vyhodnoceného spotřebního objektu pro úlohy strojového učení, jako je například školení. Díky datovým sadám můžete také [stahovat nebo připojovat](how-to-train-with-datasets.md#mount-vs-download) soubory libovolného formátu ze služeb Azure Storage, jako jsou Azure Blob Storage a Azure Data laků, do cíle výpočetní služby.


**Chcete-li vytvořit datové sady s přístupem k datům založeným na identitě**, máte k dispozici následující možnosti. Tento typ vytvoření datové sady využívá token Azure Active Directory pro ověřování přístupu k datům. 

*  Cesty odkazů z úložišť dat, které také používají přístup k datům na základě identity. 
<br>V následujícím příkladu `blob_datastore` byl dříve vytvořen pomocí přístupu k datům založeným na identitě.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Přeskočte vytváření úložiště dat a vytvořte datové sady přímo z adres URL úložiště. V současné době tato funkce podporuje jenom objekty blob Azure a Azure Data Lake Storage generace 1 a 2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

**Když ale odešlete školicí úlohu, která využívá datovou sadu vytvořenou s přístupem k datům založeným na identitě**, místo Azure Active Directory tokenu se pro ověřování přístupu k datům používá spravovaná identita výpočetní služby. V tomto scénáři zajistěte, aby byla spravovaná identita COMPUTE udělena s alespoň rolí **úložiště dat objektů BLOB úložiště** ve službě úložiště. Přečtěte si, jak [nastavit spravovanou identitu na výpočetní](how-to-create-attach-compute-cluster.md#managed-identity)výkon. 

## <a name="next-steps"></a>Další kroky

* [Vytvořte datovou sadu Azure Machine Learning](how-to-create-register-datasets.md).
* [Výuka s datovými sadami](how-to-train-with-datasets.md)
* [Vytvořte úložiště dat s přístupem k datům na základě klíčů](how-to-access-data.md).
