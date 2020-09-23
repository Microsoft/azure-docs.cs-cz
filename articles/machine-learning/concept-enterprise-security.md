---
title: Podnikové zabezpečení
titleSuffix: Azure Machine Learning
description: 'Bezpečně používejte Azure Machine Learning: ověřování, autorizace, zabezpečení sítě, šifrování dat a monitorování.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/09/2020
ms.openlocfilehash: a3cd250e53fb30e07795b184b5c949505e3b20ae
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905749"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Podnikové zabezpečení pro Azure Machine Learning

V tomto článku se dozvíte o funkcích zabezpečení dostupných pro Azure Machine Learning.

Když použijete cloudovou službu, osvědčeným postupem je omezit přístup jenom na uživatele, kteří ho potřebují. Začněte tím, že rozumíte modelu ověřování a autorizace používaném službou. Můžete taky chtít omezit přístup k síti nebo bezpečně připojit prostředky v místní síti ke cloudu. Šifrování dat je také důležité v klidovém režimu i při přesunu dat mezi službami. Nakonec musíte být schopni sledovat službu a vystavit protokol auditu pro všechny aktivity.

> [!NOTE]
> Informace v tomto článku jsou v sadě Azure Machine Learning Python SDK verze 1.0.83.1 nebo vyšší.

## <a name="authentication"></a>Authentication

Služba Multi-Factor Authentication je podporovaná, pokud je služba Azure Active Directory (Azure AD) nakonfigurovaná tak, aby ji používala. Toto je proces ověřování:

1. Klient se přihlásí do služby Azure AD a získá token Azure Resource Manager.  Uživatelé a instanční objekty jsou plně podporované.
1. Klient prezentuje token pro Azure Resource Manager a všem Azure Machine Learning.
1. Služba Machine Learning poskytuje Machine Learning token služby pro výpočetní cíl pro uživatele (například Výpočetní prostředky služby Machine Learning). Tento token používá cílový výpočetní cíl pro zpětné volání do služby Machine Learning po dokončení spuštění. Rozsah je omezen na pracovní prostor.

[![Ověřování v Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Další informace najdete v tématu [nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy](how-to-setup-authentication.md). Tento článek obsahuje informace a příklady ověřování, včetně použití instančních objektů a automatizovaných pracovních postupů.

### <a name="authentication-for-web-service-deployment"></a>Ověřování pro nasazení webové služby

Azure Machine Learning podporuje dvě formy ověřování pro webové služby: klíč a token. Každá webová služba může současně povolit jenom jednu formu ověřování.

|Metoda ověřování|Description|Azure Container Instances|AKS|
|---|---|---|---|
|Klíč|Klíče jsou statické a není nutné je aktualizovat. Klíče je možné znovu vygenerovat ručně.|Zakázáno ve výchozím nastavení| Ve výchozím nastavení povolená|
|Token|Po zadaném časovém období vyprší platnost tokenů a je nutné ji aktualizovat.| Není k dispozici| Zakázáno ve výchozím nastavení |

Příklady kódu naleznete v [části ověřování webové služby](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorizace

Můžete vytvořit několik pracovních prostorů a každý pracovní prostor může sdílet více lidí. Když sdílíte pracovní prostor, můžete k němu řídit přístup přiřazením těchto rolí uživatelům:

* Vlastník
* Přispěvatel
* Čtenář

V následující tabulce jsou uvedené některé hlavní operace Azure Machine Learning a role, které je můžou provádět:

| Operace Azure Machine Learning | Vlastník | Přispěvatel | Čtenář |
| ---- |:----:|:----:|:----:|
| Vytvoření pracovního prostoru | ✓ | ✓ | |
| Sdílet pracovní prostor | ✓ | |  |
| Vytvořit cíl výpočtů | ✓ | ✓ | |
| Připojit cíl výpočtů | ✓ | ✓ | |
| Připojení úložišť dat | ✓ | ✓ | |
| Spustit experiment | ✓ | ✓ | |
| Zobrazit běhy/metriky | ✓ | ✓ | ✓ |
| Registrace modelu | ✓ | ✓ | |
| Vytvořit bitovou kopii | ✓ | ✓ | |
| Nasazení webové služby | ✓ | ✓ | |
| Zobrazení modelů a imagí | ✓ | ✓ | ✓ |
| Volání webové služby | ✓ | ✓ | ✓ |

Pokud předdefinované role nevyhovují vašim potřebám, můžete vytvořit vlastní role. Vlastní role se podporují pro řízení všech operací v pracovním prostoru, jako je vytváření výpočetních prostředků, odeslání běhu, registrace úložiště dat nebo nasazení modelu. Vlastní role můžou mít oprávnění ke čtení, zápisu a odstraňování různých prostředků pracovního prostoru, jako jsou clustery, úložiště dat, modely a koncové body. Role může být dostupná na konkrétní úrovni pracovního prostoru, na konkrétní úrovni skupiny prostředků nebo na konkrétní úrovni předplatného. Další informace najdete v tématu [Správa uživatelů a rolí v pracovním prostoru Azure Machine Learning](how-to-assign-roles.md).

> [!WARNING]
> Azure Machine Learning se podporuje při spolupráci Azure Active Directory Business-to-Business, ale v současné době se nepodporují u Azure Active Directory spolupráce od firmy po spotřebitele.

### <a name="securing-compute-targets-and-data"></a>Zabezpečení výpočetních cílů a dat

Vlastníci a přispěvatelé můžou používat všechny výpočetní cíle a úložiště dat, která jsou připojená k pracovnímu prostoru.  

Každý pracovní prostor má také přidruženou spravovanou identitu přiřazenou systémem, která má stejný název jako pracovní prostor. Spravovaná identita má následující oprávnění k připojeným prostředkům použitým v pracovním prostoru.

Další informace o spravovaných identitách najdete v tématu [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Prostředek | Oprávnění |
| ----- | ----- |
| Pracovní prostor | Přispěvatel |
| Účet úložiště | Přispěvatel dat v objektech blob služby Storage |
| Trezor klíčů | Přístup ke všem klíčům, tajným klíčům, certifikátům |
| Azure Container Registry | Přispěvatel |
| Skupina prostředků, která obsahuje pracovní prostor | Přispěvatel |
| Skupina prostředků, která obsahuje Trezor klíčů (Pokud se liší od tu, která obsahuje pracovní prostor) | Přispěvatel |

Nedoporučujeme, aby správci odvolali přístup ke spravované identitě k prostředkům uvedeným v předchozí tabulce. Přístup můžete obnovit pomocí operace opětovné synchronizace klíčů.

Azure Machine Learning vytvoří další aplikaci (název začíná `aml-` nebo `Microsoft-AzureML-Support-App-` ) s přístupem na úrovni přispěvatele v rámci vašeho předplatného pro každou oblast pracovního prostoru. Například pokud máte jeden pracovní prostor v Východní USA a druhý v Severní Evropa ve stejném předplatném, uvidíte dvě z těchto aplikací. Tyto aplikace umožňují Azure Machine Learning, které vám pomůžou se správou výpočetních prostředků.

## <a name="network-security"></a>Zabezpečení sítě

Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky. Výpočetní prostředky (cíle výpočtů) se používají ke školení a nasazení modelů. Tyto výpočetní cíle můžete vytvořit ve virtuální síti. Můžete například použít Azure Data Science Virtual Machine k vytvoření výukového modelu a nasazení modelu do AKS.  

Další informace najdete v tématu [Přehled izolace a ochrany osobních údajů ve virtuální síti](how-to-network-security-overview.md).

Můžete také povolit privátní propojení Azure pro váš pracovní prostor. Privátní odkaz vám umožní omezit komunikaci do svého pracovního prostoru z Virtual Network Azure. Další informace najdete v tématu [Postup konfigurace privátního propojení](how-to-configure-private-link.md).

## <a name="data-encryption"></a>Šifrování dat

> [!IMPORTANT]
> Pro šifrování produkčních prostředků během __školení__doporučuje Microsoft používat výpočetní cluster Azure Machine Learning. V případě šifrování produkčních stupňů při __odvozování__doporučuje Microsoft používat službu Azure Kubernetes.
>
> Azure Machine Learning výpočetní instance je prostředí pro vývoj a testování. Při použití doporučujeme ukládat do sdílené složky soubory, jako jsou třeba poznámkové bloky a skripty. Data by měla být uložená v úložišti dat.

### <a name="encryption-at-rest"></a>Šifrování neaktivních uložených dat

> [!IMPORTANT]
> Pokud váš pracovní prostor obsahuje citlivá data, doporučujeme při vytváření pracovního prostoru nastavit [příznak hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) . `hbi_workspace`Příznak lze nastavit pouze v případě, že je vytvořen pracovní prostor. Nedá se změnit pro existující pracovní prostor.

`hbi_workspace`Příznak řídí množství dat, která [Microsoft shromažďuje pro účely diagnostiky](#microsoft-collected-data) , a umožňuje [Další šifrování v prostředích spravovaných Microsoftem](../security/fundamentals/encryption-atrest.md). Kromě toho umožňují následující akce:

* Spustí šifrování místního pomocného disku ve vašem Azure Machine Learning výpočetním clusteru, protože jste v tomto předplatném nevytvořili žádné předchozí clustery. V opačném případě je potřeba vyvolat lístek podpory, který umožní šifrování pomocného disku vašich výpočetních clusterů. 
* Čištění místního pomocného disku mezi jednotlivými spuštěními
* Zabezpečeně předává přihlašovací údaje pro účet úložiště, registr kontejnerů a účet SSH z vrstvy spouštění do výpočetních clusterů pomocí vašeho trezoru klíčů.
* Povolí filtrování protokolu IP, aby se zajistilo, že se nadřazené fondy dávek nedají volat v jiných externích službách než AzureMachineLearningService.

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning ukládá snímky, výstup a protokoly v účtu služby Azure Blob Storage, který je svázán s pracovním prostorem Azure Machine Learning a vaším předplatným. Všechna data uložená v úložišti objektů BLOB v Azure jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

Informace o tom, jak používat vlastní klíče pro data uložená v úložišti objektů BLOB v Azure, najdete [v tématu Azure Storage šifrování pomocí klíčů spravovaných zákazníkem v Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Data školení se většinou ukládají také v úložišti objektů BLOB v Azure, aby byla dostupná pro školení výpočetních cílů. Toto úložiště není spravované nástrojem Azure Machine Learning, ale je připojené k výpočetním cílům jako vzdálený systém souborů.

Pokud potřebujete svůj klíč __otočit nebo odvolat__ , můžete to udělat kdykoli. Při otáčení klíče začne účet úložiště používat nový klíč (nejnovější verzi) k šifrování dat v klidovém formátu. Při odvolávání (zakázání) klíče účet úložiště postará o neúspěšné požadavky. To obvykle trvá hodinu, než se rotace nebo odvolání projeví.

Informace o opětovném generování přístupových klíčů najdete v tématu [opětovné vygenerování přístupových klíčů k úložišti](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning ukládá metriky a metadata v instanci Azure Cosmos DB. Tato instance je přidružená k předplatnému Microsoftu spravovanému pomocí Azure Machine Learning. Všechna data uložená v Azure Cosmos DB jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

Pokud chcete k šifrování instance Azure Cosmos DB použít vlastní klíče (spravované zákazníkem), můžete vytvořit vyhrazenou instanci Cosmos DB pro použití s vaším pracovním prostorem. Tento postup doporučujeme, pokud chcete ukládat data, jako jsou informace o historii spuštění, mimo instanci víceklientské Cosmos DB hostované v předplatném Microsoftu. 

Pokud chcete ve svém předplatném povolit zřizování Cosmos DB instance pomocí klíčů spravovaných zákazníkem, proveďte následující akce:

* Zaregistrujte poskytovatele prostředků Microsoft. MachineLearning a Microsoft.DocumentDB v rámci vašeho předplatného, pokud to ještě neudělalo.

* Při vytváření pracovního prostoru Azure Machine Learning použijte následující parametry. Oba parametry jsou povinné a podporují se v šablonách SDK, CLI, REST API a Správce prostředků.

    * `resource_cmk_uri`: Tento parametr je úplným identifikátorem URI spravovaného klíče zákazníka ve vašem trezoru klíčů, včetně [informací o verzi klíče](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Tento parametr je ID prostředku pro Trezor klíčů v rámci vašeho předplatného. Tento Trezor klíčů musí být ve stejné oblasti a předplatném, které budete používat pro Azure Machine Learning pracovní prostor. 
    
        > [!NOTE]
        > Tato instance trezoru klíčů se může lišit od trezoru klíčů, který je vytvořený Azure Machine Learning při zřizování pracovního prostoru. Pokud chcete pro pracovní prostor použít stejnou instanci trezoru klíčů, předejte stejný Trezor klíčů při zřizování pracovního prostoru pomocí [parametru key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#&preserve-view=truecreate-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Tato instance Cosmos DB je vytvořená ve skupině prostředků spravované Microsoftem ve vašem předplatném, spolu s případnými prostředky, které potřebuje. Spravovaná skupina prostředků je pojmenována ve formátu `<AML Workspace Resource Group Name><GUID>` . Pokud váš pracovní prostor Azure Machine Learning používá privátní koncový bod, vytvoří se pro instanci Cosmos DB taky virtuální síť. Tato virtuální síť slouží k zabezpečení komunikace mezi Cosmos DB a Azure Machine Learning.

> [!IMPORTANT]
> * Neodstraňujte skupinu prostředků, která obsahuje tuto instanci Cosmos DB, nebo žádný z prostředků, které se automaticky vytvořily v této skupině. Pokud potřebujete odstranit skupinu prostředků, Cosmos DB instanci atd., je nutné odstranit Azure Machine Learning pracovní prostor, který ho používá. Skupina prostředků, instance Cosmos DB a další automaticky vytvořené prostředky se odstraní při odstranění přidruženého pracovního prostoru.
> * Výchozí [__jednotky žádostí__](../cosmos-db/request-units.md) pro tento účet Cosmos DB jsou nastavené na __8000__. Změna této hodnoty není podporována.
> * Pro použití s vytvořenou instancí Cosmos DB nemůžete zadat vlastní virtuální síť. Nemůžete také upravovat virtuální síť. Nemůžete například změnit rozsah IP adres, který používá.

Pokud potřebujete svůj klíč __otočit nebo odvolat__ , můžete to udělat kdykoli. Při otočení klíče Cosmos DB začne používat nový klíč (nejnovější verzi) k šifrování neaktivních dat. Při odvolání (zakázání) klíče se Cosmos DB postará o neúspěšné požadavky. To obvykle trvá hodinu, než se rotace nebo odvolání projeví.

Další informace o klíčích spravovaných zákazníkem pomocí Cosmos DB najdete v tématu [konfigurace klíčů spravovaných zákazníkem pro účet Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

Všechny Image kontejneru v registru (Azure Container Registry) jsou v klidovém stavu šifrované. Azure tento obrázek před uložením automaticky zašifruje a dešifruje, když Azure Machine Learning načte image.

Pokud chcete své Azure Container Registry šifrovat pomocí vlastních klíčů (spravovaných zákazníkem), musíte si vytvořit vlastní ACR a připojit ho při zřizování pracovního prostoru nebo zašifrování výchozí instance, která se vytvoří v době zřizování pracovního prostoru.

> [!IMPORTANT]
> Azure Machine Learning vyžaduje, aby byl na svém Azure Container Registry povolen účet správce. Ve výchozím nastavení je toto nastavení zakázáno při vytváření registru kontejnerů. Informace o povolení účtu správce najdete v tématu [účet správce](/azure/container-registry/container-registry-authentication#admin-account).
>
> Po vytvoření Azure Container Registry pro pracovní prostor ji neodstraňujte. Tím dojde k přerušení pracovního prostoru Azure Machine Learning.

Příklad vytvoření pracovního prostoru pomocí existující Azure Container Registry najdete v následujících článcích:

* [Vytvořte pracovní prostor pro Azure Machine Learning pomocí Azure CLI](how-to-manage-workspace-cli.md).
* [Použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Instance kontejneru Azure

Nasazený prostředek Azure Container instance (ACI) můžete šifrovat pomocí klíčů spravovaných zákazníkem. Klíč spravovaný zákazníkem, který se používá pro ACI, může být uložený v Azure Key Vault pro váš pracovní prostor. Informace o vygenerování klíče najdete v tématu [šifrování dat pomocí klíče spravovaného zákazníkem](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Pokud chcete klíč použít při nasazování modelu do služby Azure Container instance, vytvořte novou konfiguraci nasazení pomocí `AciWebservice.deploy_configuration()` . Zadejte klíčové informace pomocí následujících parametrů:

* `cmk_vault_base_url`: Adresa URL trezoru klíčů, který obsahuje klíč.
* `cmk_key_name`: Název klíče.
* `cmk_key_version`: Verze klíče.

Další informace o vytváření a používání konfigurace nasazení najdete v následujících článcích:

* Odkaz na [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Kde a jak nasadit](how-to-deploy-and-where.md)
* [Nasazení modelu do služby Azure Container Instances](how-to-deploy-azure-container-instance.md)

Další informace o používání klíče spravovaného zákazníkem v ACI najdete v tématu [šifrování dat pomocí klíče spravovaného zákazníkem](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Nasazený prostředek služby Azure Kubernetes můžete šifrovat kdykoli pomocí klíčů spravovaných zákazníkem. Další informace najdete v tématu [Přineste si vlastní klíče ke službě Azure Kubernetes](../aks/azure-disk-customer-managed-keys.md). 

Tento proces umožňuje šifrovat data i disk s operačním systémem nasazených virtuálních počítačů v clusteru Kubernetes.

> [!IMPORTANT]
> Tento proces funguje jenom s AKS K8s verze 1,17 nebo vyšší. Azure Machine Learning přidat podporu pro AKS 1,17 na 13. ledna 2020.

#### <a name="machine-learning-compute"></a>Výpočetní prostředky služby Machine Learning

Disk s operačním systémem pro každý výpočetní uzel, který je uložený v Azure Storage, je zašifrovaný pomocí klíčů spravovaných Microsoftem v Azure Machine Learning účty úložiště. Tento cílový výpočetní výkon je dočasný a clustery se obvykle škálují, když nejsou žádné běhy ve frontě. V podkladovém virtuálním počítači se zruší zřízení a disk s operačním systémem se odstraní. Pro disk s operačním systémem se Azure Disk Encryption nepodporuje.

Každý virtuální počítač má také místní dočasný disk pro operace s operačním systémem. Pokud chcete, můžete použít disk pro přípravu školicích dat. Disk je ve výchozím nastavení zašifrovaný pro pracovní prostory s `hbi_workspace` parametrem nastaveným na `TRUE` . Toto prostředí je krátkodobé jenom po dobu trvání běhu a podpora šifrování je omezená jenom na klíče spravované systémem.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks lze použít v kanálech Azure Machine Learning. Ve výchozím nastavení je systém souborů datacihly (DBFS) používaný Azure Databricks zašifrovaný pomocí klíče spravovaného společností Microsoft. Pokud chcete nakonfigurovat Azure Databricks pro použití klíčů spravovaných zákazníkem, přečtěte si téma [konfigurace klíčů spravovaných zákazníkem ve výchozím nastavení (root) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Šifrování během přenosu

Azure Machine Learning používá protokol TLS k zabezpečení interní komunikace mezi různými Azure Machine Learning mikroslužby. K přístupu k Azure Storage dojde taky přes zabezpečený kanál.

Aby bylo možné zabezpečit externí volání u bodování koncového bodu, Azure Machine Learning používá protokol TLS. Další informace najdete v tématu [použití protokolu TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Použití Azure Key Vault

Azure Machine Learning používá instanci Azure Key Vault přidruženou k pracovnímu prostoru k ukládání přihlašovacích údajů různých typů:

* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Hesla a klíče SSH k výpočetním cílům, jako je Azure HDInsight a virtuální počítače, jsou uložené v jiném trezoru klíčů, který je přidružený k předplatnému Microsoft. Azure Machine Learning neukládají žádná hesla ani klíče poskytované uživateli. Místo toho generuje, autorizuje a ukládá vlastní klíče SSH pro připojení k virtuálním počítačům a HDInsight pro spuštění experimentů.

Každý pracovní prostor má přidruženou spravovanou identitu přiřazenou systémem, která má stejný název jako pracovní prostor. Tato spravovaná identita má přístup ke všem klíčům, tajným klíčům a certifikátům v trezoru klíčů.

## <a name="data-collection-and-handling"></a>Shromažďování a zpracování dat

### <a name="microsoft-collected-data"></a>Shromážděná data společnosti Microsoft

Společnost Microsoft může shromažďovat neuživatelem identifikovatelné informace, jako jsou názvy prostředků (například název datové sady nebo název experimentu Machine Learning), nebo proměnné prostředí úloh pro účely diagnostiky. Všechna taková data se ukládají pomocí klíčů spravovaných Microsoftem v úložišti hostovaném v předplatných vlastněných společností Microsoft a [na základě standardních zásad ochrany osobních údajů společnosti Microsoft a standardů pro zpracování dat](https://privacy.microsoft.com/privacystatement).

Microsoft také doporučuje do proměnných prostředí ukládat citlivé informace (třeba klíčová tajná klíče účtu). Proměnné prostředí jsou protokolovány, šifrovány a uloženy v USA. Podobně při pojmenování [run_id](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py&preserve-view=true)Vyhněte zahrnutí citlivých informací, jako jsou uživatelská jména nebo tajné názvy projektů. Tyto informace se mohou zobrazit v protokolech telemetrie, které jsou přístupné pro podpora Microsoftu inženýry.

Shromážděná diagnostická data můžete odhlásit tím, že `hbi_workspace` `TRUE` při zřizování pracovního prostoru nastavíte parametr na. Tato funkce se podporuje při použití šablon aplikace AzureML Python SDK, CLI, REST API nebo Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Data generovaná společností Microsoft

Při používání služeb, jako jsou například automatizované Machine Learning, může společnost Microsoft vygenerovat přechodná a předem zpracovaná data pro školení více modelů. Tato data jsou uložená v úložišti dat ve vašem pracovním prostoru, což vám umožní patřičně vymáhat řízení přístupu a šifrování.

Můžete také chtít šifrovat [diagnostické informace zaznamenané z nasazeného koncového bodu](how-to-enable-app-insights.md) do instance služby Azure Application Insights.

## <a name="monitoring"></a>Monitorování

### <a name="metrics"></a>Metriky

Metriky Azure Monitor můžete použít k zobrazení a monitorování metrik pro pracovní prostor Azure Machine Learning. V [Azure Portal](https://portal.azure.com)vyberte svůj pracovní prostor a pak vyberte **metriky**:

[![Snímek obrazovky znázorňující ukázkovou metriku pro pracovní prostor](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Metriky obsahují informace o spuštění, nasazení a registracích.

Další informace najdete v tématu [metriky v Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Protokol aktivit

Můžete zobrazit protokol aktivit pracovního prostoru a zobrazit různé operace, které se provádějí v pracovním prostoru. Protokol obsahuje základní informace, jako je název operace, iniciátor události a časové razítko.

Na tomto snímku obrazovky vidíte protokol aktivity pracovního prostoru:

[![Snímek obrazovky zobrazující protokol aktivity pracovního prostoru](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Podrobnosti žádosti o vyhodnocování jsou uložené v Application Insights. Při vytváření pracovního prostoru se ve vašem předplatném vytvoří Application Insights. Protokolované informace obsahují pole jako:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* Identifikátor
* Doba trvání

> [!IMPORTANT]
> Některé akce v pracovním prostoru Azure Machine Learning neprotokolují informace do protokolu aktivit. Například spuštění školicího programu a registrace modelu se nezaprotokolují.
>
> Některé z těchto akcí se zobrazí v oblasti **aktivity** pracovního prostoru, ale tato oznámení nenaznačují, kdo aktivitu inicioval.

## <a name="data-flow-diagrams"></a>Diagramy toku dat

### <a name="create-workspace"></a>Vytvoření pracovního prostoru

Následující diagram znázorňuje pracovní postup vytvoření pracovního prostoru.

* Přihlašujete se ke službě Azure AD z některého z podporovaných klientů Azure Machine Learning (Azure CLI, Python SDK, Azure Portal) a vyžádejte si příslušný Azure Resource Manager token.
* Zavoláte Azure Resource Manager k vytvoření pracovního prostoru. 
* Azure Resource Manager kontaktuje poskytovatele prostředků Azure Machine Learning a zřídí pracovní prostor.

V předplatném uživatele se vytvoří další prostředky během vytváření pracovního prostoru:

* Key Vault (pro ukládání tajných klíčů)
* Účet služby Azure Storage (včetně objektů BLOB a sdílených souborů)
* Azure Container Registry (pro ukládání imagí Docker pro odvození/bodování a experimentování)
* Application Insights (pro uložení telemetrie)

Uživatel může také zřídit jiné výpočetní cíle, které jsou připojeny k pracovnímu prostoru (například službě Azure Kubernetes nebo virtuálním počítačům) podle potřeby.

[![Vytvořit pracovní postup pracovního postupu](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Uložit zdrojový kód (školicí skripty)

Následující diagram znázorňuje pracovní postup snímku kódu.

Přidruženo k pracovnímu prostoru Azure Machine Learning jsou adresáře (experimenty), které obsahují zdrojový kód (školicí skripty). Tyto skripty se ukládají na vašem místním počítači a v cloudu (ve službě Azure Blob Storage pro vaše předplatné). Snímky kódu se používají ke spuštění nebo kontrole historických auditů.

[![Pracovní postup snímku kódu](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot.png#lightbox)

### <a name="training"></a>Školení

Následující diagram znázorňuje pracovní postup školení.

* Azure Machine Learning se volá s ID snímku pro snímek kódu uložený v předchozí části.
* Azure Machine Learning vytvoří ID spuštění (volitelné) a token služby Machine Learning, který je později využíván výpočetními cíli, jako je Výpočetní prostředky služby Machine Learning/virtuální počítače ke komunikaci se službou Machine Learning.
* Ke spuštění školicích úloh můžete vybrat buď spravovaný cíl služby COMPUTE (například Výpočetní prostředky služby Machine Learning), nebo nespravovaný cíl služby COMPUTE (například virtuální počítače). Zde jsou datové toky pro oba scénáře:
   * Virtuální počítače/HDInsight, ke kterým mají přístup přihlašovací údaje SSH v trezoru klíčů v předplatném Microsoftu. Azure Machine Learning spouští kód pro správu na výpočetním cíli, který:

   1. Připraví prostředí. (Docker je možnost pro virtuální počítače a místní počítače. Pokud chcete zjistit, jak fungují experimenty na kontejnerech Docker, Projděte si následující postup Výpočetní prostředky služby Machine Learning.)
   1. Stáhne kód.
   1. Nastaví proměnné prostředí a konfigurace.
   1. Spustí uživatelské skripty (snímek kódu uvedený v předchozí části).

   * K Výpočetní prostředky služby Machine Learning přistupované prostřednictvím identity spravované v pracovním prostoru.
Vzhledem k tomu, že Výpočetní prostředky služby Machine Learning je spravovaný výpočetní cíl (to znamená, že ho spravuje Microsoft), běží pod vaším předplatným Microsoft.

   1. V případě potřeby se spustí konstrukce vzdáleného Docker.
   1. Kód správy je zapsán do sdílené složky souborů Azure uživatele.
   1. Kontejner je spuštěn s počátečním příkazem. To znamená kód správy, jak je popsáno v předchozím kroku.

#### <a name="querying-runs-and-metrics"></a>Dotazování na běhy a metriky

V níže uvedeném diagramu se tento krok stane, když výpočetní cíl školení zapíše metriky Run zpátky do Azure Machine Learning z úložiště v databázi Cosmos DB. Klienti můžou volat Azure Machine Learning. Machine Learning bude z databáze Cosmos DB znovu aktivovat metriky a vracet je zpět klientovi.

[![Pracovní postup školení](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics.png#lightbox)

### <a name="creating-web-services"></a>Vytváření webových služeb

Následující diagram znázorňuje odvozený pracovní postup. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data.

Podrobnosti najdete tady:

* Uživatel zaregistruje model pomocí klienta, jako je Azure Machine Learning SDK.
* Uživatel vytvoří obrázek pomocí modelu, souboru skóre a dalších závislostí modelu.
* Image Docker se vytvoří a uloží v Azure Container Registry.
* Webová služba je nasazena do cíle služby COMPUTE (Container Instances/AKS) pomocí image vytvořené v předchozím kroku.
* Podrobnosti žádosti o vyhodnocování jsou uložené v Application Insights, který je v předplatném uživatele.
* Telemetrii se taky vloží do předplatného Microsoft/Azure.

[![Pracovní postup odvození](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing.png#lightbox)

## <a name="next-steps"></a>Další kroky

* [Zabezpečení Azure Machine Learning webové služby pomocí protokolu TLS](how-to-secure-web-service.md)
* [Využití modelu Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md)
* [Použití Azure Machine Learning s Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Použití Azure Machine Learning s využitím Azure Virtual Network](how-to-network-security-overview.md)
* [Osvědčené postupy pro sestavování doporučení pro systémy](https://github.com/Microsoft/Recommenders)
* [Sestavení rozhraní API pro doporučení v reálném čase v Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
