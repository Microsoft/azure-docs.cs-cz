---
title: Přístup k datům na základě identity ke službám úložiště v Azure
titleSuffix: Machine Learning
description: Naučte se používat přístup k datům na základě identity k připojení ke službám úložiště v Azure s Azure Machine Learning úložiště dat a Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 02/22/2021
ms.custom: how-to, contperf-fy21q1, devx-track-python, data4ml
ms.openlocfilehash: a46f54bd037dcf8d71ba3fbafb2ba0fd961a32cc
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210648"
---
# <a name="connect-to-storage-by-using-identity-based-data-access-preview"></a>Připojení k úložišti pomocí přístupu k datům založeným na identitě (Preview)

>[!IMPORTANT]
> Funkce prezentované v tomto článku jsou ve verzi Preview. Měly by se považovat za [experimentální](/python/api/overview/azure/ml/#stable-vs-experimental) funkce ve verzi Preview, které se můžou kdykoli změnit.

V tomto článku se dozvíte, jak se připojit ke službám úložiště v Azure pomocí přístupu k datům na základě identity a Azure Machine Learning úložiště dat pomocí [Azure Machine Learning SDK pro Python](/python/api/overview/azure/ml/intro).  

Úložiště dat obvykle používá přístup k datům na základě přihlašovacích údajů k potvrzení, že máte oprávnění pro přístup ke službě úložiště. V [trezoru klíčů](https://azure.microsoft.com/services/key-vault/) , který je přidružený k pracovnímu prostoru, udržují informace o připojení, jako je ID předplatného a autorizace tokenu. Když vytvoříte úložiště dat, které používá přístup k datům na základě identity, účet Azure ([Azure Active Directory token](../active-directory/fundamentals/active-directory-whatis.md)) se používá k potvrzení, že máte oprávnění pro přístup ke službě úložiště. V tomto scénáři nejsou uloženy žádné přihlašovací údaje pro ověřování. V úložišti dat se ukládají jenom informace o účtu úložiště. 

Pokud chcete vytvářet úložiště dat, která používají ověřování založené na přihlašovacích údajích, jako jsou přístupové klíče nebo instanční objekty, přečtěte si téma [připojení ke službám úložiště v Azure](how-to-access-data.md).

## <a name="identity-based-data-access-in-azure-machine-learning"></a>Přístup k datům na základě identity v Azure Machine Learning

Existují dva scénáře, ve kterých můžete použít přístup k datům na základě identity v Azure Machine Learning. Tyto scénáře jsou vhodné pro přístup na základě identity, když pracujete s důvěrnými daty a potřebujete podrobnější správu přístupu k datům:

- Přístup ke službám úložiště
- Školení modelů strojového učení s privátními daty

### <a name="accessing-storage-services"></a>Přístup ke službám úložiště

Ke službám úložiště se můžete připojit prostřednictvím přístupu k datům založeným na identitě s Azure Machine Learning úložiště dat nebo [Azure Machine Learning datových sad](how-to-create-register-datasets.md). 

Přihlašovací údaje pro ověřování jsou obvykle uchovávány v úložišti dat, které slouží k zajištění oprávnění pro přístup ke službě úložiště. Pokud jsou tato pověření registrována prostřednictvím úložiště dat, může je načíst libovolný uživatel s rolí čtenáře pracovního prostoru. Tato škála přístupu může být v některých organizacích obavou zabezpečení. [Přečtěte si další informace o roli čtenáře pracovního prostoru.](how-to-assign-roles.md#default-roles) 

Když použijete přístup k datům založeným na identitě, Azure Machine Learning vás vyzve k zadání tokenu Azure Active Directory pro ověřování přístupu k datům namísto udržování přihlašovacích údajů v úložišti dat. Tento přístup umožňuje správu přístupu k datům na úrovni úložiště a zachovává důvěrné přihlašovací údaje. 

Stejné chování platí i v případě, že:

* [Vytvořte datovou sadu přímo z adres URL úložiště](#use-data-in-storage). 
* Pracujte s daty interaktivně prostřednictvím Jupyter Notebook v místním počítači nebo [instanci služby COMPUTE](concept-compute-instance.md).

> [!NOTE]
> Přihlašovací údaje uložené prostřednictvím ověřování založeného na přihlašovacích údajích zahrnují ID předplatných, tokeny sdíleného přístupového podpisu (SAS) a přístupový klíč k úložišti a informace o instančním objektu, jako jsou ID klientů a ID klientů

### <a name="model-training-on-private-data"></a>Školení modelů u privátních dat

Některé scénáře strojového učení zahrnují školicí modely s privátními daty. V takových případech musí odborníci na data spouštět školicí pracovní postupy, aniž by museli být vystaveni důvěrné vstupní údaje. V tomto scénáři se pro ověřování přístupu k datům používá spravovaná identita výpočetní služby. Tento přístup umožňuje správcům úložiště udělit přístup ke službě úložiště dat objektů BLOB úložiště spravované identitě, kterou výpočetní služba používá ke spuštění úlohy školení. Jednotlivým pracovníkům dat není potřeba udělit přístup. Další informace najdete v tématu [nastavení spravované identity ve výpočetním clusteru](how-to-create-attach-compute-cluster.md#managed-identity).


## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed bezplatný účet. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).

- Účet úložiště Azure s podporovaným typem úložiště. Tyto typy úložišť jsou podporované ve verzi Preview: 
    - [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md)
    - [Azure Data Lake Storage Gen1](../data-lake-store/index.yml)
    - [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
    - [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)

- [Sada SDK Azure Machine Learning pro Python](/python/api/overview/azure/ml/install).

- Pracovní prostor služby Azure Machine Learning.
  
  Buď [Vytvořte pracovní prostor Azure Machine Learning](how-to-manage-workspace.md) nebo použijte [existující sadu pomocí sady Python SDK](how-to-manage-workspace.md#connect-to-a-workspace). 

## <a name="storage-access-permissions"></a>Přístupová oprávnění k úložišti

Aby bylo zajištěno, že se budete zabezpečeně připojovat k vaší službě úložiště v Azure, Azure Machine Learning vyžaduje, abyste měli oprávnění pro přístup k odpovídajícímu úložišti dat.

Přístup k datům na základě identity podporuje připojení jenom na následující služby úložiště:

* Azure Blob Storage
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

Chcete-li získat přístup k těmto službám úložiště, je nutné mít k dispozici alespoň přístup ke [čtečce dat objektů BLOB úložiště](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . [Úroveň přístupu můžou změnit](../storage/common/storage-auth-aad-rbac-portal.md)jenom vlastníci účtu úložiště pomocí Azure Portal.

Pokud provádíte školení modelu na vzdáleném cílovém výpočetním cíli, musí být identitě služby Storage udělena alespoň role čtečky dat objektů BLOB úložiště. Přečtěte si, jak [nastavit spravovanou identitu na výpočetním clusteru](how-to-create-attach-compute-cluster.md#managed-identity).

## <a name="work-with-virtual-networks"></a>Práce s virtuálními sítěmi

Ve výchozím nastavení Azure Machine Learning nemůže komunikovat s účtem úložiště, který je za bránou firewall nebo ve virtuální síti.

Účty úložiště můžete nakonfigurovat tak, aby povolovaly přístup jenom v rámci konkrétních virtuálních sítí. Tato konfigurace vyžaduje další kroky, aby se zajistilo, že nedojde k úniku dat mimo síť. Toto chování je stejné pro přístup k datům na základě přihlašovacích údajů. Další informace najdete v tématu [Konfigurace scénářů virtuální sítě](how-to-access-data.md#virtual-network). 

## <a name="create-and-register-datastores"></a>Vytvoření a registrace úložišť dat

Když zaregistrujete službu úložiště v Azure jako úložiště dat, automaticky vytvoříte a zaregistrujete toto úložiště dat do konkrétního pracovního prostoru. Pokyny k požadovaným typům oprávnění najdete v tématu [oprávnění k přístupu k úložišti](#storage-access-permissions) . Podrobnosti o tom, jak se připojit k úložišti dat za virtuálními sítěmi, najdete v tématu [práce s virtuálními sítěmi](#work-with-virtual-networks) .

V následujícím kódu si všimněte neexistence parametrů ověřování, jako je `sas_token` , `account_key` , `subscription_id` a instančního objektu `client_id` . Toto vynechání indikuje, že Azure Machine Learning bude k ověřování používat přístup k datům na základě identity. Vytváření úložiště dat obvykle probíhá interaktivně v poznámkovém bloku nebo prostřednictvím studia. Proto se váš Azure Active Directory token používá pro ověřování přístupu k datům.

> [!NOTE]
> Názvy úložišť dat by se měly skládat jenom z malých písmen, číslic a podtržítek. 

### <a name="azure-blob-container"></a>Kontejner objektů blob Azure

Pokud chcete zaregistrovat kontejner objektů blob Azure jako úložiště dat, použijte [`register_azure_blob_container()`](/python/api/azureml-core/azureml.core.datastore%28class%29#register-azure-blob-container-workspace--datastore-name--container-name--account-name--sas-token-none--account-key-none--protocol-none--endpoint-none--overwrite-false--create-if-not-exists-false--skip-validation-false--blob-cache-timeout-none--grant-workspace-access-false--subscription-id-none--resource-group-none-) .

Následující kód vytvoří `credentialless_blob` úložiště dat, zaregistruje ho do `ws` pracovního prostoru a přiřadí ho k `blob_datastore` proměnné. Toto úložiště dat přistupuje k `my_container_name` kontejneru objektů BLOB v `my-account-name` účtu úložiště.

```Python
# Create blob datastore without credentials.
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,
                                                      datastore_name='credentialless_blob',
                                                      container_name='my_container_name',
                                                      account_name='my_account_name')
```

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

K registraci úložiště dat, které se připojuje k Azure Data Lake Storage Gen1, použijte [register_azure_data_lake ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-workspace--datastore-name--store-name--tenant-id-none--client-id-none--client-secret-none--resource-url-none--authority-url-none--subscription-id-none--resource-group-none--overwrite-false--grant-workspace-access-false-) .

Následující kód vytvoří `credentialless_adls1` úložiště dat, zaregistruje ho do `workspace` pracovního prostoru a přiřadí ho k `adls_dstore` proměnné. Toto úložiště dat přistupuje k `adls_storage` účtu Azure Data Lake Storage.

```Python
# Create Azure Data Lake Storage Gen1 datastore without credentials.
adls_dstore = Datastore.register_azure_data_lake(workspace = workspace,
                                                 datastore_name='credentialless_adls1',
                                                 store_name='adls_storage')

```

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

K registraci úložiště dat, které se připojuje k Azure Data Lake Storage Gen2, použijte [register_azure_data_lake_gen2 ()](/python/api/azureml-core/azureml.core.datastore.datastore#register-azure-data-lake-gen2-workspace--datastore-name--filesystem--account-name--tenant-id--client-id--client-secret--resource-url-none--authority-url-none--protocol-none--endpoint-none--overwrite-false-) .

Následující kód vytvoří `credentialless_adls2` úložiště dat, zaregistruje ho do `ws` pracovního prostoru a přiřadí ho k `adls2_dstore` proměnné. Toto úložiště dat přistupuje k systému souborů `tabular` v `myadls2` účtu úložiště.  

```python
# Create Azure Data Lake Storage Gen2 datastore without credentials.
adls2_dstore = Datastore.register_azure_data_lake_gen2(workspace=ws, 
                                                       datastore_name='credentialless_adls2', 
                                                       filesystem='tabular', 
                                                       account_name='myadls2')
```

## <a name="use-data-in-storage"></a>Použití dat v úložišti

Při práci s daty v úložišti s Azure Machine Learning doporučujeme použít [Azure Machine Learning datové sady](how-to-create-register-datasets.md) . 

Datové sady zabalí vaše data do laxně vytvářená vyhodnoceného spotřebního objektu pro úlohy strojového učení, jako je například školení. Díky datovým sadám můžete také [stahovat nebo připojovat](how-to-train-with-datasets.md#mount-vs-download) soubory libovolného formátu ze služeb Azure Storage, jako je Azure Blob Storage, a Azure Data Lake Storage do cíle výpočetní služby.


Chcete-li vytvořit datové sady s přístupem k datům založeným na identitě, máte k dispozici následující možnosti. Tento typ vytvoření datové sady používá token Azure Active Directory pro ověřování přístupu k datům. 

*  Cesty odkazů z úložišť dat, které také používají přístup k datům na základě identity. 
<br>V následujícím příkladu `blob_datastore` již existuje a používá přístup k datům na základě identity.   

    ```python
    blob_dataset = Dataset.Tabular.from_delimited_files(blob_datastore,'test.csv') 
    ```

* Přeskočte vytváření úložiště dat a vytvořte datové sady přímo z adres URL úložiště. Tato funkce aktuálně podporuje jenom objekty blob Azure a Azure Data Lake Storage Gen1 a Gen2.

    ```python
    blob_dset = Dataset.File.from_files('https://myblob.blob.core.windows.net/may/keras-mnist-fashion/')
    ```

Při odeslání školicí úlohy, která spotřebovává datovou sadu vytvořenou pomocí přístupu k datům založeným na identitě, se pro ověřování přístupu k datům používá spravovaná identita výpočetní služby. Váš Azure Active Directory token se nepoužívá. V tomto scénáři se ujistěte, že se spravované identitě COMPUTE uděluje alespoň role čtečky dat objektů BLOB úložiště ze služby úložiště. Další informace najdete v tématu [nastavení spravované identity na výpočetních clusterech](how-to-create-attach-compute-cluster.md#managed-identity). 

## <a name="next-steps"></a>Další kroky

* [Vytvoření datové sady Azure Machine Learning](how-to-create-register-datasets.md)
* [Trénování s datovými sadami](how-to-train-with-datasets.md)
* [Vytvoření úložiště dat s přístupem k datům na základě klíčů](how-to-access-data.md)
