---
title: Šifrování dat pomocí Azure Machine Learningu
titleSuffix: Azure Machine Learning
description: Seznamte se s tím, jak Azure Machine Learning výpočetní prostředí a úložiště dat poskytují šifrování a přenos dat v klidovém prostředí.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: a133cdb72e304a254305833b9ae6e8a7ebe30ab3
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540004"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Šifrování dat pomocí Azure Machine Learning

Azure Machine Learning využívá celou řadu služeb Azure Data Storage a výpočetních prostředků při výuce modelů a provádění odvození. Každé z nich má vlastní příběh, jak poskytují šifrování pro neaktivní a tranzitní data. V tomto článku se dozvíte o každé z nich, která je pro váš scénář nejvhodnější.

> [!IMPORTANT]
> Pro šifrování produkčních prostředků během __školení__ doporučuje Microsoft používat výpočetní cluster Azure Machine Learning. V případě šifrování produkčních stupňů při __odvozování__ doporučuje Microsoft používat službu Azure Kubernetes.
>
> Azure Machine Learning výpočetní instance je prostředí pro vývoj a testování. Při použití doporučujeme ukládat do sdílené složky soubory, jako jsou třeba poznámkové bloky a skripty. Data by měla být uložená v úložišti dat.

## <a name="encryption-at-rest"></a>Šifrování neaktivních uložených dat

> [!IMPORTANT]
> Pokud váš pracovní prostor obsahuje citlivá data, doporučujeme při vytváření pracovního prostoru nastavit [příznak hbi_workspace](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) . `hbi_workspace`Příznak lze nastavit pouze v případě, že je vytvořen pracovní prostor. Nedá se změnit pro existující pracovní prostor.

`hbi_workspace`Příznak řídí množství dat, která [Microsoft shromažďuje pro účely diagnostiky](#microsoft-collected-data) , a umožňuje [Další šifrování v prostředích spravovaných Microsoftem](../security/fundamentals/encryption-atrest.md). Kromě toho umožňují následující akce:

* Spustí šifrování místního pomocného disku ve vašem Azure Machine Learning výpočetním clusteru, protože jste v tomto předplatném nevytvořili žádné předchozí clustery. V opačném případě je potřeba vyvolat lístek podpory, který umožní šifrování pomocného disku vašich výpočetních clusterů. 
* Čištění místního pomocného disku mezi jednotlivými spuštěními
* Zabezpečeně předává přihlašovací údaje pro účet úložiště, registr kontejnerů a účet SSH z vrstvy spouštění do výpočetních clusterů pomocí vašeho trezoru klíčů.
* Povolí filtrování protokolu IP, aby se zajistilo, že se nadřazené fondy dávek nedají volat v jiných externích službách než AzureMachineLearningService.
* Počítejte s tím, že výpočetní instance nejsou v pracovním prostoru HBI podporované.

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning ukládá snímky, výstup a protokoly v účtu služby Azure Blob Storage, který je svázán s pracovním prostorem Azure Machine Learning a vaším předplatným. Všechna data uložená v úložišti objektů BLOB v Azure jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

Informace o tom, jak používat vlastní klíče pro data uložená v úložišti objektů BLOB v Azure, najdete [v tématu Azure Storage šifrování pomocí klíčů spravovaných zákazníkem v Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md).

Data školení se většinou ukládají také v úložišti objektů BLOB v Azure, aby byla dostupná pro školení výpočetních cílů. Toto úložiště není spravované nástrojem Azure Machine Learning, ale je připojené k výpočetním cílům jako vzdálený systém souborů.

Pokud potřebujete svůj klíč __otočit nebo odvolat__ , můžete to udělat kdykoli. Při otáčení klíče začne účet úložiště používat nový klíč (nejnovější verzi) k šifrování dat v klidovém formátu. Při odvolávání (zakázání) klíče účet úložiště postará o neúspěšné požadavky. To obvykle trvá hodinu, než se rotace nebo odvolání projeví.

Informace o opětovném generování přístupových klíčů najdete v tématu [opětovné vygenerování přístupových klíčů k úložišti](how-to-change-storage-access-key.md).

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning ukládá metadata v instanci Azure Cosmos DB. Tato instance je přidružená k předplatnému Microsoftu spravovanému pomocí Azure Machine Learning. Všechna data uložená v Azure Cosmos DB jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

Pokud chcete k šifrování instance Azure Cosmos DB použít vlastní klíče (spravované zákazníkem), můžete vytvořit vyhrazenou instanci Cosmos DB pro použití s vaším pracovním prostorem. Tento postup doporučujeme, pokud chcete ukládat data, jako jsou informace o historii spuštění, mimo instanci víceklientské Cosmos DB hostované v předplatném Microsoftu. 

Pokud chcete ve svém předplatném povolit zřizování Cosmos DB instance pomocí klíčů spravovaných zákazníkem, proveďte následující akce:

* Zaregistrujte poskytovatele prostředků Microsoft. MachineLearning a Microsoft.DocumentDB v rámci vašeho předplatného, pokud to ještě neudělalo.

* Při vytváření pracovního prostoru Azure Machine Learning použijte následující parametry. Oba parametry jsou povinné a podporují se v šablonách SDK, CLI, REST API a Správce prostředků.

    * `resource_cmk_uri`: Tento parametr je úplným identifikátorem URI spravovaného klíče zákazníka ve vašem trezoru klíčů, včetně [informací o verzi klíče](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Tento parametr je ID prostředku pro Trezor klíčů v rámci vašeho předplatného. Tento Trezor klíčů musí být ve stejné oblasti a předplatném, které budete používat pro Azure Machine Learning pracovní prostor. 
    
        > [!NOTE]
        > Tato instance trezoru klíčů se může lišit od trezoru klíčů, který je vytvořený Azure Machine Learning při zřizování pracovního prostoru. Pokud chcete pro pracovní prostor použít stejnou instanci trezoru klíčů, předejte stejný Trezor klíčů při zřizování pracovního prostoru pomocí [parametru key_vault](/python/api/azureml-core/azureml.core.workspace%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Pokud potřebujete svůj klíč __otočit nebo odvolat__ , můžete to udělat kdykoli. Při otočení klíče Cosmos DB začne používat nový klíč (nejnovější verzi) k šifrování neaktivních dat. Při odvolání (zakázání) klíče se Cosmos DB postará o neúspěšné požadavky. To obvykle trvá hodinu, než se rotace nebo odvolání projeví.

Další informace o klíčích spravovaných zákazníkem pomocí Cosmos DB najdete v tématu [konfigurace klíčů spravovaných zákazníkem pro účet Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

### <a name="azure-container-registry"></a>Azure Container Registry

Všechny Image kontejneru v registru (Azure Container Registry) jsou v klidovém stavu šifrované. Azure tento obrázek před uložením automaticky zašifruje a dešifruje, když Azure Machine Learning načte image.

Pokud chcete své Azure Container Registry šifrovat pomocí vlastních klíčů (spravovaných zákazníkem), musíte si vytvořit vlastní ACR a připojit ho při zřizování pracovního prostoru nebo zašifrování výchozí instance, která se vytvoří v době zřizování pracovního prostoru.

> [!IMPORTANT]
> Azure Machine Learning vyžaduje, aby byl na svém Azure Container Registry povolen účet správce. Ve výchozím nastavení je toto nastavení zakázáno při vytváření registru kontejnerů. Informace o povolení účtu správce najdete v tématu [účet správce](../container-registry/container-registry-authentication.md#admin-account).
>
> Po vytvoření Azure Container Registry pro pracovní prostor ji neodstraňujte. Tím dojde k přerušení pracovního prostoru Azure Machine Learning.

Příklad vytvoření pracovního prostoru pomocí existující Azure Container Registry najdete v následujících článcích:

* [Vytvořte pracovní prostor pro Azure Machine Learning pomocí Azure CLI](how-to-manage-workspace-cli.md).
* [Vytvořte pracovní prostor pomocí sady Python SDK](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Instance kontejneru Azure

Nasazený prostředek Azure Container instance (ACI) můžete šifrovat pomocí klíčů spravovaných zákazníkem. Klíč spravovaný zákazníkem, který se používá pro ACI, může být uložený v Azure Key Vault pro váš pracovní prostor. Informace o vygenerování klíče najdete v tématu [šifrování dat pomocí klíče spravovaného zákazníkem](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Pokud chcete klíč použít při nasazování modelu do služby Azure Container instance, vytvořte novou konfiguraci nasazení pomocí `AciWebservice.deploy_configuration()` . Zadejte klíčové informace pomocí následujících parametrů:

* `cmk_vault_base_url`: Adresa URL trezoru klíčů, který obsahuje klíč.
* `cmk_key_name`: Název klíče.
* `cmk_key_version`: Verze klíče.

Další informace o vytváření a používání konfigurace nasazení najdete v následujících článcích:

* Odkaz na [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Kde a jak nasadit](how-to-deploy-and-where.md)
* [Nasazení modelu do služby Azure Container Instances](how-to-deploy-azure-container-instance.md)

Další informace o používání klíče spravovaného zákazníkem v ACI najdete v tématu [šifrování dat pomocí klíče spravovaného zákazníkem](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Nasazený prostředek služby Azure Kubernetes můžete šifrovat kdykoli pomocí klíčů spravovaných zákazníkem. Další informace najdete v tématu [Přineste si vlastní klíče ke službě Azure Kubernetes](../aks/azure-disk-customer-managed-keys.md). 

Tento proces umožňuje šifrovat data i disk s operačním systémem nasazených virtuálních počítačů v clusteru Kubernetes.

> [!IMPORTANT]
> Tento proces funguje jenom s AKS K8s verze 1,17 nebo vyšší. Azure Machine Learning přidat podporu pro AKS 1,17 na 13. ledna 2020.

### <a name="machine-learning-compute"></a>Výpočetní prostředky služby Machine Learning

Disk s operačním systémem pro každý výpočetní uzel, který je uložený v Azure Storage, je zašifrovaný pomocí klíčů spravovaných Microsoftem v Azure Machine Learning účty úložiště. Tento cílový výpočetní výkon je dočasný a clustery se obvykle škálují, když nejsou žádné běhy ve frontě. V podkladovém virtuálním počítači se zruší zřízení a disk s operačním systémem se odstraní. Pro disk s operačním systémem se Azure Disk Encryption nepodporuje.

Každý virtuální počítač má také místní dočasný disk pro operace s operačním systémem. Pokud chcete, můžete použít disk pro přípravu školicích dat. Disk je ve výchozím nastavení zašifrovaný pro pracovní prostory s `hbi_workspace` parametrem nastaveným na `TRUE` . Toto prostředí je krátkodobé jenom po dobu trvání běhu a podpora šifrování je omezená jenom na klíče spravované systémem.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks lze použít v kanálech Azure Machine Learning. Ve výchozím nastavení je systém souborů datacihly (DBFS) používaný Azure Databricks zašifrovaný pomocí klíče spravovaného společností Microsoft. Pokud chcete nakonfigurovat Azure Databricks pro použití klíčů spravovaných zákazníkem, přečtěte si téma [konfigurace klíčů spravovaných zákazníkem ve výchozím nastavení (root) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

## <a name="encryption-in-transit"></a>Šifrování během přenosu

Azure Machine Learning používá protokol TLS k zabezpečení interní komunikace mezi různými Azure Machine Learning mikroslužby. K přístupu k Azure Storage dojde taky přes zabezpečený kanál.

Aby bylo možné zabezpečit externí volání u bodování koncového bodu, Azure Machine Learning používá protokol TLS. Další informace najdete v tématu [použití protokolu TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](./how-to-secure-web-service.md).

## <a name="data-collection-and-handling"></a>Shromažďování a zpracování dat

### <a name="microsoft-collected-data"></a>Shromážděná data společnosti Microsoft

Společnost Microsoft může shromažďovat neuživatelem identifikovatelné informace, jako jsou názvy prostředků (například název datové sady nebo název experimentu Machine Learning), nebo proměnné prostředí úloh pro účely diagnostiky. Všechna taková data se ukládají pomocí klíčů spravovaných Microsoftem v úložišti hostovaném v předplatných vlastněných společností Microsoft a [na základě standardních zásad ochrany osobních údajů společnosti Microsoft a standardů pro zpracování dat](https://privacy.microsoft.com/privacystatement).

Microsoft také doporučuje do proměnných prostředí ukládat citlivé informace (třeba klíčová tajná klíče účtu). Proměnné prostředí jsou protokolovány, šifrovány a uloženy v USA. Podobně při pojmenování [run_id](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)Vyhněte zahrnutí citlivých informací, jako jsou uživatelská jména nebo tajné názvy projektů. Tyto informace se mohou zobrazit v protokolech telemetrie, které jsou přístupné pro podpora Microsoftu inženýry.

Shromážděná diagnostická data můžete odhlásit tím, že `hbi_workspace` `TRUE` při zřizování pracovního prostoru nastavíte parametr na. Tato funkce se podporuje při použití šablon aplikace AzureML Python SDK, CLI, REST API nebo Azure Resource Manager.

## <a name="using-azure-key-vault"></a>Použití Azure Key Vault

Azure Machine Learning používá instanci Azure Key Vault přidruženou k pracovnímu prostoru k ukládání přihlašovacích údajů různých typů:

* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Hesla a klíče SSH k výpočetním cílům, jako je Azure HDInsight a virtuální počítače, jsou uložené v jiném trezoru klíčů, který je přidružený k předplatnému Microsoft. Azure Machine Learning neukládají žádná hesla ani klíče poskytované uživateli. Místo toho generuje, autorizuje a ukládá vlastní klíče SSH pro připojení k virtuálním počítačům a HDInsight pro spuštění experimentů.

Každý pracovní prostor má přidruženou spravovanou identitu přiřazenou systémem, která má stejný název jako pracovní prostor. Tato spravovaná identita má přístup ke všem klíčům, tajným klíčům a certifikátům v trezoru klíčů.

## <a name="next-steps"></a>Další kroky

* [Připojení k Azure Storage](how-to-access-data.md)
* [Získání dat z úložiště dat](how-to-create-register-datasets.md)
* [Připojení k datům](how-to-connect-data-ui.md)
* [Trénování s datovými sadami](how-to-train-with-datasets.md)