---
title: Podnikové zabezpečení
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning můžete bezpečně používat: ověřování, autorizace, zabezpečení sítě, šifrování dat a monitorování.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/13/2020
ms.openlocfilehash: 4fbb3e83692ec058c03b22654e82d4093fe3541d
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756567"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Podnikové zabezpečení pro Azure Machine Learning

V tomto článku se dozvíte o funkcích zabezpečení, které jsou k dispozici pro Azure Machine Learning.

Při použití cloudové služby je osvědčeným postupem omezit přístup pouze na uživatele, kteří ji potřebují. Začněte pochopením modelu ověřování a autorizace používaného službou. Můžete také omezit přístup k síti nebo bezpečně připojit prostředky v místní síti s cloudem. Šifrování dat je také důležité, a to jak v klidovém stavu, tak při pohybu dat mezi službami. Nakonec musíte být schopni sledovat službu a vytvořit protokol auditu všech aktivit.

> [!NOTE]
> Informace v tomto článku funguje s Azure Machine Learning Python SDK verze 1.0.83.1 nebo vyšší.

## <a name="authentication"></a>Authentication

Vícefaktorové ověřování je podporované, pokud je azure active directory (Azure AD) nakonfigurovaný na jeho použití. Zde je proces ověřování:

1. Klient se přihlásí ke službě Azure AD a získá token Azure Resource Manager.  Uživatelé a instanční objekty jsou plně podporovány.
1. Klient představuje token pro Azure Resource Manager a pro všechny Azure Machine Learning.
1. Služba Machine Learning poskytuje token služby Machine Learning pro cíl výpočetního výkonu uživatele (například Machine Learning Compute). Tento token používá cíl výpočetního výkonu uživatele k volání zpět do služby Machine Learning po dokončení spuštění. Obor je omezen na pracovní prostor.

[![Ověřování v Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Další informace najdete v tématu [Nastavení ověřování pro prostředky a pracovní postupy Azure Machine Learning](how-to-setup-authentication.md). Tento článek obsahuje informace a příklady ověřování, včetně použití instančních objektů a automatizovaných pracovních postupů.

### <a name="authentication-for-web-service-deployment"></a>Ověřování pro nasazení webové služby

Azure Machine Learning podporuje dvě formy ověřování pro webové služby: klíč a token. Každá webová služba může povolit pouze jednu formu ověřování najednou.

|Metoda ověřování|Popis|Azure Container Instances|AKS|
|---|---|---|---|
|Klíč|Klíče jsou statické a není nutné je aktualizovat. Klíče lze obnovit ručně.|Ve výchozím nastavení zakázáno| Ve výchozím nastavení povolená|
|Podpisový|Platnost tokenů vyprší po uplynutí zadaného časového období a je třeba je aktualizovat.| Není k dispozici.| Ve výchozím nastavení zakázáno |

Příklady kódu naleznete v [části ověřování webové služby](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorizace

Můžete vytvořit více pracovních prostorů a každý pracovní prostor může sdílet více osob. Když sdílíte pracovní prostor, můžete k němu řídit přístup přiřazením těchto rolí uživatelům:

* Vlastník
* Přispěvatel
* Čtenář

V následující tabulce jsou uvedeny některé hlavní operace Azure Machine Learning a role, které je můžou provádět:

| Operace Azure Machine Learning | Vlastník | Přispěvatel | Čtenář |
| ---- |:----:|:----:|:----:|
| Vytvoření pracovního prostoru | ✓ | ✓ | |
| Sdílení pracovního prostoru | ✓ | |  |
| Upgrade pracovního prostoru na edici Enterprise | ✓ | |
| Vytvořit výpočetní cíl | ✓ | ✓ | |
| Připojit výpočetní cíl | ✓ | ✓ | |
| Připojení úložišť dat | ✓ | ✓ | |
| Spustit experiment | ✓ | ✓ | |
| Zobrazení sběhů/metrik | ✓ | ✓ | ✓ |
| Registrace modelu | ✓ | ✓ | |
| Vytvořit obrázek | ✓ | ✓ | |
| Nasazení webové služby | ✓ | ✓ | |
| Zobrazit modely/obrázky | ✓ | ✓ | ✓ |
| Zavolat na webovou službu | ✓ | ✓ | ✓ |

Pokud předdefinované role neodpovídají vašim potřebám, můžete vytvořit vlastní role. Vlastní role jsou podporovány pouze pro operace v pracovním prostoru a výpočetních strojových učení. Vlastní role mohou mít oprávnění ke čtení, zápisu nebo odstranění v pracovním prostoru a na výpočetní prostředek v tomto pracovním prostoru. Roli můžete zpřístupnit na určité úrovni pracovního prostoru, na úrovni konkrétní skupiny prostředků nebo na konkrétní úrovni předplatného. Další informace najdete [v tématu Správa uživatelů a rolí v pracovním prostoru Azure Machine Learning](how-to-assign-roles.md).

> [!WARNING]
> Azure Machine Learning není momentálně podporovaný díky spolupráci mezi podniky azure active directory.

### <a name="securing-compute-targets-and-data"></a>Zabezpečení výpočetních cílů a dat

Vlastníci a přispěvatelé můžou používat všechny výpočetní cíle a úložiště dat, která jsou připojená k pracovnímu prostoru.  

Každý pracovní prostor má také přidruženou spravovanou identitu přiřazenou systémem, která má stejný název jako pracovní prostor. Spravovaná identita má následující oprávnění pro připojené prostředky používané v pracovním prostoru.

Další informace o spravovaných identitách najdete [v tématu Spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Prostředek | Oprávnění |
| ----- | ----- |
| Pracovní prostor | Přispěvatel |
| Účet úložiště | Přispěvatel dat objektu blob úložiště |
| Trezor klíčů | Přístup ke všem klíčům, tajným kódům, certifikátům |
| Azure Container Registry | Přispěvatel |
| Skupina prostředků obsahující pracovní prostor | Přispěvatel |
| Skupina prostředků, která obsahuje trezor klíčů (pokud se liší od té, která obsahuje pracovní prostor) | Přispěvatel |

Nedoporučujeme, aby správci odvolali přístup spravované identity k prostředkům uvedeným v předchozí tabulce. Přístup můžete obnovit pomocí operace resync klíčů.

Azure Machine Learning vytvoří další aplikaci `aml-` `Microsoft-AzureML-Support-App-`(název začíná nebo ) s přístupem na úrovni přispěvatele ve vašem předplatném pro každou oblast pracovního prostoru. Například pokud máte jeden pracovní prostor ve východním USA a jeden v severní Evropě ve stejném předplatném, uvidíte dvě z těchto aplikací. Tyto aplikace umožňují Azure Machine Learning, který vám pomůže spravovat výpočetní prostředky.

## <a name="network-security"></a>Zabezpečení sítě

Azure Machine Learning závisí na jiných službách Azure pro výpočetní prostředky. Výpočetní prostředky (výpočetní cíle) se používají k trénování a nasazování modelů. Tyto výpočetní cíle můžete vytvořit ve virtuální síti. Virtuální počítač Azure Data Science můžete například použít k trénování modelu a následném nasazení modelu do AKS.  

Další informace najdete v tématu [Jak spustit experimenty a odvození ve virtuální síti](how-to-enable-virtual-network.md).

Můžete také povolit Azure Private Link pro váš pracovní prostor. Private Link umožňuje omezit komunikaci do pracovního prostoru z virtuální sítě Azure. Další informace naleznete v tématu [Jak nakonfigurovat soukromé spojení](how-to-configure-private-link.md).

> [!TIP]
> Můžete kombinovat virtuální síť a privátní propojení dohromady a chránit tak komunikaci mezi pracovním prostorem a dalšími prostředky Azure. Některé kombinace však vyžadují pracovní prostor edice Enterprise. V následující tabulce můžete pochopit, jaké scénáře vyžadují edici Enterprise:
>
> | Scénář | Enterprise</br>Edition | Základní</br>Edition |
> | ----- |:-----:|:-----:| 
> | Žádná virtuální síť nebo privátní spojení | ✔ | ✔ |
> | Pracovní prostor bez soukromého odkazu. Další prostředky (kromě Azure Container Registry) ve virtuální síti | ✔ | ✔ |
> | Pracovní prostor bez soukromého odkazu. Další zdroje s privátním odkazem | ✔ | |
> | Pracovní prostor s privátním odkazem. Další prostředky (kromě Azure Container Registry) ve virtuální síti | ✔ | ✔ |
> | Pracovní prostor a jakýkoli jiný zdroj s privátním odkazem | ✔ | |
> | Pracovní prostor s privátním odkazem. Další prostředky bez privátního spojení nebo virtuální sítě | ✔ | ✔ |
> | Azure Container Registry ve virtuální síti | ✔ | |
> | Klientspravované klíče pro pracovní prostor | ✔ | |
> 

> [!WARNING]
> Azure Machine Learning výpočetní instance náhled není podporovánv pracovním prostoru, kde je povolena privátní odkaz.
> 
> Azure Machine Learning nepodporuje pomocí služby Azure Kubernetes, která má povolenou privátní vazbu. Místo toho můžete použít službu Azure Kubernetes Service ve virtuální síti. Další informace najdete [v tématu Zabezpečené úlohy experimentování azure ml a odvození v rámci virtuální sítě Azure](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Šifrování dat

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

> [!IMPORTANT]
> Pokud pracovní prostor obsahuje citlivá data, doporučujeme nastavit [příznak hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) při vytváření pracovního prostoru. 

Příznak `hbi_workspace` řídí množství dat, která společnost Microsoft shromažďuje pro diagnostické účely, a umožňuje další šifrování ve spravovaných prostředích společnosti Microsoft. Kromě toho umožňuje následující:

* Spustí šifrování místního odkládací hodisku v clusteru Amlcompute za předpokladu, že jste v tomto předplatném nevytvořili žádné předchozí clustery. Jinak je třeba zvýšit lístek podpory povolit šifrování odkládací disk výpočetních clusterů 
* Vyčistí místní odkládací disk mezi spuštěními
* Bezpečně předává pověření pro váš účet úložiště, registr kontejnerů a účet SSH z vrstvy spuštění do výpočetních clusterů pomocí trezoru klíčů
* Umožňuje filtrování IP adres, aby bylo zajištěno, že základní dávkové fondy nemohou být volány žádnými externími službami jinými než AzureMachineLearningService.


Další informace o tom, jak šifrování v klidovém stavu funguje, najdete v [tématu šifrování dat Azure v klidovém stavu](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning ukládá snímky, výstup y a protokoly v účtu úložiště objektů blob Azure, který je vázaný na pracovní prostor Azure Machine Learning a vaše předplatné. Všechna data uložená v úložišti objektů Blob Azure se šifrují v klidovém stavu pomocí klíčů spravovaných Microsoftem.

Informace o tom, jak používat vlastní klíče pro data uložená v úložišti objektů Blob Azure, najdete v [tématu šifrování azure storage s klíči spravovanými zákazníky v Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Trénovací data se obvykle taky ukládají v úložišti objektů blob Azure, takže jsou přístupná trénovacím výpočetním cílům. Toto úložiště není spravované Azure Machine Learning, ale připojené k výpočetním cílům jako vzdálený souborový systém.

Pokud potřebujete klíč __otočit nebo odvolat,__ můžete tak učinit kdykoli. Při otáčení klíče začne účet úložiště používat nový klíč (nejnovější verze) k šifrování dat v klidovém stavu. Při zrušení (zakázání) klíče se účet úložiště postará o neúspěšné požadavky. Obvykle trvá hodinu, než rotace nebo odvolání budou účinné.

Informace o obnovení přístupových klíčů naleznete v tématu [Regenerate storage access keys](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning ukládá metriky a metadata v instanci Azure Cosmos DB. Tato instance je přidružena k předplatnému Microsoftu spravovanému Azure Machine Learning. Všechna data uložená v Azure Cosmos DB se šifrují v klidovém stavu pomocí klíčů spravovaných microsoftem.

Chcete-li k šifrování instance Azure Cosmos DB použít vlastní klíče (spravované zákazníkem), můžete vytvořit vyhrazenou instanci Cosmos DB pro použití s pracovním prostorem. Tento přístup doporučujeme, pokud chcete ukládat data, jako jsou informace o historii spuštění, mimo víceklientské instance Cosmos DB hostované v našem předplatném Microsoftu. 

Chcete-li povolit zřizování instance Cosmos DB v předplatném pomocí klíčů spravovaných zákazníkem, proveďte následující akce:

* Povolte klíčové funkce spravované zákazníkem pro Cosmos DB. V tomto okamžiku je nutné požádat o přístup k použití této funkce. Chcete-li tak [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com)učinit, obraťte se na společnost .

* Zaregistrujte zprostředkovatele prostředků Azure Machine Learning a Azure Cosmos DB ve svém předplatném, pokud to už není hotové.

* Autorizujte aplikaci Machine Learning (ve správě identit a přístupu) s oprávněními přispěvatele k vašemu předplatnému.

    ![Autorizace aplikace Azure Machine Learning App ve správě identit a přístupu na portálu](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Při vytváření pracovního prostoru Azure Machine Learning použijte následující parametry. Oba parametry jsou povinné a podporované v šablonách Sady SDK, CLI, REST API a Resource Manager.

    * `resource_cmk_uri`: Tento parametr je úplný identifikátor URI prostředku klienta spravovaného klíče v trezoru klíčů, včetně [informací o verzi klíče](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Tento parametr je ID prostředku trezoru klíčů v předplatném. Tento trezor klíčů musí být ve stejné oblasti a předplatné, které budete používat pro pracovní prostor Azure Machine Learning. 
    
        > [!NOTE]
        > Tato instance trezoru klíčů se může lišit od trezoru klíčů vytvořeného Azure Machine Learning při zřizování pracovního prostoru. Pokud chcete pro pracovní prostor použít stejnou instanci trezoru klíčů, předejte stejný trezor klíčů při zřizování pracovního prostoru pomocí [parametru key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Tato instance Cosmos DB se vytvoří ve skupině prostředků spravované společností Microsoft ve vašem předplatném. 

> [!IMPORTANT]
> * Pokud potřebujete odstranit tuto instanci Cosmos DB, musíte odstranit pracovní prostor Azure Machine Learning, který ji používá. 
> * Výchozí [__jednotky požadavků__](../cosmos-db/request-units.md) pro tento účet Cosmos DB jsou nastaveny na __hodnotu 8000__. Změna této hodnoty není podporována. 

Pokud potřebujete klíč __otočit nebo odvolat,__ můžete tak učinit kdykoli. Při otáčení klíče cosmos DB začne používat nový klíč (nejnovější verze) k šifrování dat v klidovém stavu. Při zrušení (zakázání) klíče cosmos DB se postará o selhání požadavků. Obvykle trvá hodinu, než rotace nebo odvolání budou účinné.

Další informace o klíčích spravovaných zákazníky pomocí služby Cosmos DB najdete v [tématu Konfigurace klíčů spravovaných zákazníky pro váš účet Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

Všechny image kontejneru ve vašem registru (Azure Container Registry) jsou šifrované v klidovém stavu. Azure automaticky zašifruje image před uložením a dešifruje ji, když Azure Machine Learning vytáhne image.

Chcete-li k šifrování registru kontejnerů Azure použít vlastní klíče (spravované zákazníkem), musíte vytvořit vlastní acr a připojit ho při zřizování pracovního prostoru nebo šifrovat výchozí instanci, která se vytvoří v době zřizování pracovního prostoru.

Příklad vytvoření pracovního prostoru pomocí existujícího registru kontejnerů Azure najdete v následujících článcích:

* [Vytvořte pracovní prostor pro Azure Machine Learning pomocí Azure CLI](how-to-manage-workspace-cli.md).
* [Vytvoření pracovního prostoru pro Azure Machine Learning pomocí šablony Azure Resource Manageru](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Instance kontejneru Azure

Nasazený prostředek instance kontejneru Azure (ACI) můžete zašifrovat pomocí klíčů spravovaných zákazníky. Klíč spravovaný zákazníkem použitý pro ACI lze uložit do trezoru klíčů Azure pro váš pracovní prostor. Informace o generování klíče naleznete v tématu [Šifrování dat pomocí klíče spravovaného zákazníkem](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Chcete-li použít klíč při nasazování modelu do instance `AciWebservice.deploy_configuration()`kontejneru Azure, vytvořte novou konfiguraci nasazení pomocí . Poskytněte klíčové informace pomocí následujících parametrů:

* `cmk_vault_base_url`: Adresa URL trezoru klíčů, který obsahuje klíč.
* `cmk_key_name`: Název klíče.
* `cmk_key_version`: Verze klíče.

Další informace o vytváření a používání konfigurace nasazení naleznete v následujících článcích:

* [Odkaz Na AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Kde a jak nasadit](how-to-deploy-and-where.md)
* [Nasazení modelu do instancí kontejnerů Azure](how-to-deploy-azure-container-instance.md)

Další informace o použití klíče spravovaného zákazníkem s ACI naleznete v [tématu Šifrování dat pomocí klíče spravovaného zákazníkem](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Nasazený prostředek služby Azure Kubernetes můžete kdykoli zašifrovat pomocí klíčů spravovaných zákazníky. Další informace najdete [v tématu Bring your own keys with Azure Kubernetes Service](../aks/azure-disk-customer-managed-keys.md). 

Tento proces umožňuje šifrovat data i disk operačního systému nasazených virtuálních počítačů v clusteru Kubernetes.

> [!IMPORTANT]
> Tento proces funguje pouze s AKS K8s verze 1.17 nebo vyšší. Azure Machine Learning přidal podporu pro AKS 1.17 na 13 ledna 2020.

#### <a name="machine-learning-compute"></a>Výpočetní výkon strojového učení

Disk operačního systému pro každý výpočetní uzel uložený ve službě Azure Storage je šifrovaný pomocí klíčů spravovaných Microsoftem v účtech úložiště Azure Machine Learning. Tento výpočetní cíl je dočasný a clustery jsou obvykle zmenšeny, když nejsou zařazeny do fronty žádné spuštění. Základní virtuální počítač je de-zřídit a disk operačního systému se odstraní. Azure Disk Encryption není pro disk operačního systému podporován.

Každý virtuální počítač má také místní dočasný disk pro operace operačního systému. Pokud chcete, můžete použít disk k fázi trénovací data. Disk je ve výchozím nastavení zašifrován pro pracovní prostory s parametrem `hbi_workspace` nastaveným na `TRUE`. Toto prostředí je krátkodobé pouze po dobu trvání vašeho spuštění a podpora šifrování je omezena pouze na klíče spravované systémem.

#### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks se dá použít v kanálech Azure Machine Learning. Ve výchozím nastavení je systém souborů Databricks (DBFS) používaný společností Azure Databricks šifrován pomocí klíče spravovaného společností Microsoft. Pokud chcete nakonfigurovat Azure Databricks tak, aby používaly klíče spravované zákazníkem, přečtěte si informace [o konfiguraci klíčů spravovaných zákazníky ve výchozím (kořenovém) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="encryption-in-transit"></a>Šifrování během přenosu

TLS můžete použít k zabezpečení interní komunikace mezi mikroslužbami Azure Machine Learning a k zabezpečení externích volání do koncového bodu vyhodnocování. Veškerý přístup k úložišti Azure storage se také vyskytuje přes zabezpečený kanál.

Další informace najdete [v tématu Použití TLS k zabezpečení webové služby prostřednictvím Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Použití trezoru klíčů Azure

Azure Machine Learning používá instanci Azure Key Vault přidruženou k pracovnímu prostoru k ukládání přihlašovacích údajů různých druhů:

* Přidružený připojovací řetězec účtu úložiště
* Hesla do instancí úložiště kontejnerů Azure
* Připojovací řetězce k úložištím dat

Hesla a klíče SSH pro výpočetní cíle, jako je Azure HDInsight a virtuální počítače, se ukládají v samostatném trezoru klíčů, který je přidružený k předplatnému Microsoftu. Azure Machine Learning neukládá žádná hesla ani klíče poskytované uživateli. Místo toho generuje, autorizuje a ukládá vlastní klíče SSH pro připojení k virtuálním zařízením a HDInsight ke spuštění experimentů.

Každý pracovní prostor má přidruženou spravovanou identitu přiřazenou systémem, která má stejný název jako pracovní prostor. Tato spravovaná identita má přístup ke všem klíčům, tajným klíčům a certifikátům v trezoru klíčů.

## <a name="data-collection-and-handling"></a>Shromažďování a zpracování dat

### <a name="microsoft-collected-data"></a>Společnost Microsoft shromáždila data

Společnost Microsoft může shromažďovat informace, které nejsou uživatelem, jako jsou názvy prostředků (například název datové sady nebo název experimentu strojového učení) nebo proměnné prostředí úloh y pro diagnostické účely. Všechna tato data jsou uložena pomocí klíčů spravovaných společností Microsoft v úložišti hostovaném v předplatných vlastněných společností Microsoft a řídí se [standardními zásadami ochrany osobních údajů a standardy zpracování dat společnosti Microsoft](https://privacy.microsoft.com/privacystatement).

Společnost Microsoft také doporučuje neukládat citlivé informace (například tajné klíče klíče účtu) v proměnných prostředí. Proměnné prostředí jsou protokolovány, šifrovány a ukládány námi. Podobně při pojmenování [runid](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py), vyhnout se zahrnutí citlivých informací, jako jsou uživatelská jména nebo tajné názvy projektů. Tyto informace se mohou zobrazit v protokolech telemetrie, které jsou přístupné technikům podpory společnosti Microsoft.

Můžete odhlásit z diagnostických dat `hbi_workspace` shromažďovaných nastavením parametru při `TRUE` zřizování pracovního prostoru. Tato funkce je podporovaná při použití šablon AzureML Python SDK, CLI, REST API nebo Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Data generovaná společností Microsoft

Při použití služeb, jako je například automatizované strojové učení, může společnost Microsoft generovat přechodná, předem zpracovaná data pro školení více modelů. Tato data jsou uložena v úložišti dat ve vašem pracovním prostoru, což umožňuje vhodně vynutit ovládací prvky přístupu a šifrování.

Můžete také chtít šifrovat [diagnostické informace zaznamenané z nasazeného koncového bodu](how-to-enable-app-insights.md) do instance Azure Application Insights.

## <a name="monitoring"></a>Monitorování

### <a name="metrics"></a>Metriky

Metriky Azure Monitoru můžete použít k zobrazení a monitorování metrik pro váš pracovní prostor Azure Machine Learning. Na [webu Azure Portal](https://portal.azure.com)vyberte pracovní prostor a pak vyberte **Metriky**:

[![Snímek obrazovky s ukázkovou metrikou pracovního prostoru](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Metriky zahrnují informace o spuštění, nasazení a registrace.

Další informace najdete [v tématu Metriky v Azure Monitoru](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Protokol aktivit

Můžete zobrazit protokol aktivit pracovního prostoru a zobrazit různé operace, které jsou prováděny v pracovním prostoru. Protokol obsahuje základní informace, jako je název operace, iniciátor události a časové razítko.

Tento snímek obrazovky ukazuje protokol aktivit pracovního prostoru:

[![Snímek obrazovky s protokolem aktivit pracovního prostoru](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Podrobnosti požadavku na vyhodnocování jsou uloženy v Application Insights. Application Insights se vytvoří ve vašem předplatném při vytváření pracovního prostoru. Protokolované informace zahrnují pole, jako jsou:

* Metoda HTTP
* Useragent
* ComputeType
* Adresa RequestUrl
* Statuscode
* Requestid
* Doba trvání

> [!IMPORTANT]
> Některé akce v pracovním prostoru Azure Machine Learning nezaznamenávají informace do protokolu aktivit. Například spuštění školení a registrace modelu nejsou zaznamenány.
>
> Některé z těchto akcí se zobrazí v oblasti **Aktivity** v pracovním prostoru, ale tato oznámení neoznačují, kdo aktivitu inicioval.

## <a name="data-flow-diagrams"></a>Diagramy toku dat

### <a name="create-workspace"></a>Vytvoření pracovního prostoru

Následující diagram znázorňuje pracovní postup vytvoření pracovního prostoru.

* K Azure AD se přihlásíte z jednoho z podporovaných klientů Azure Machine Learning (Azure CLI, Python SDK, portál Azure) a požádáte o příslušný token Azure Resource Manager.
* Zavoláte Správce prostředků Azure k vytvoření pracovního prostoru. 
* Azure Resource Manager kontaktuje poskytovatele prostředků Azure Machine Learning a zřídí pracovní prostor.

Další prostředky jsou vytvořeny v předplatném uživatele během vytváření pracovního prostoru:

* Trezor klíčů (pro uložení tajných klíčů)
* Účet úložiště Azure (včetně objektu blob a sdílené složky)
* Azure Container Registry (pro ukládání ibi Dockeru pro odvození/vyhodnocování a experimentování)
* Application Insights (pro ukládání telemetrie)

Uživatel může také zřídit další výpočetní cíle, které jsou připojené k pracovnímu prostoru (jako je služba Azure Kubernetes service nebo virtuální počítače) podle potřeby.

[![Vytvoření pracovního postupu pracovního prostoru](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Uložení zdrojového kódu (školicí skripty)

Následující diagram znázorňuje pracovní postup snímek kódu.

Přidružené k pracovnímu prostoru Azure Machine Learning jsou adresáře (experimenty), které obsahují zdrojový kód (školicí skripty). Tyto skripty se ukládají ve vašem místním počítači a v cloudu (v úložišti objektů Blob Azure pro vaše předplatné). Snímky kódu se používají pro provádění nebo kontrolu pro historické auditování.

[![Pracovní postup snímek kódu](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

### <a name="training"></a>Školení

Následující diagram znázorňuje pracovní postup školení.

* Azure Machine Learning se volá s ID snímku pro snímek kódu uložené v předchozí části.
* Azure Machine Learning vytvoří run ID (volitelné) a token služby Machine Learning, který se později používá výpočetní cíle, jako je Machine Learning Compute/VMke komunikaci se službou Machine Learning.
* Můžete si vybrat buď spravovaný výpočetní cíl (jako je Machine Learning Compute) nebo nespravovaný výpočetní cíl (jako virtuální počítače) pro spuštění trénovacích úloh. Tady jsou toky dat pro oba scénáře:
   * Virtuální ms/HDInsight, přístup k pověření SSH v trezoru klíčů v předplatném Microsoftu. Azure Machine Learning spouští kód pro správu na výpočetním cíli, který:

   1. Připravuje životní prostředí. (Docker je možnost pro virtuální počítače a místní počítače. V následujících krocích pro Machine Learning Compute zjistěte, jak funguje spuštění experimentů na kontejnerech Dockeru.)
   1. Stáhne kód.
   1. Nastaví proměnné a konfigurace prostředí.
   1. Spustí uživatelské skripty (snímek kódu uvedený v předchozí části).

   * Machine Learning Compute, přístupná prostřednictvím identity spravované pracovním prostorem.
Vzhledem k tomu, že Machine Learning Compute je cíle spravovaného výpočetního výkonu (to znamená, že je spravovaný společností Microsoft), běží pod vaším předplatným Microsoftu.

   1. V případě potřeby je zahájena výstavba vzdáleného Dockeru.
   1. Kód správy se zapisuje do sdílené složky Souborů Azure uživatele.
   1. Kontejner je spuštěn s počátečním příkazem. To znamená kód správy, jak je popsáno v předchozím kroku.

#### <a name="querying-runs-and-metrics"></a>Dotazování běhů a metrik

V diagramu toku níže k tomuto kroku dochází, když cíl výpočetní výpočetní výpočetní zapíše metriky spuštění zpět do Azure Machine Learning z úložiště v databázi Cosmos DB. Klienti můžou volat Azure Machine Learning. Machine Learning bude zase vyžádat metriky z databáze Cosmos DB a vrátit je zpět klientovi.

[![Tréninkový pracovní postup](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Vytváření webových služeb

Následující diagram znázorňuje pracovní postup odvození. Odvození nebo vyhodnocování modelu je fáze, ve které se nasazený model používá pro předpověď, nejčastěji na produkčních datech.

Zde jsou podrobnosti:

* Uživatel zaregistruje model pomocí klienta, jako je Azure Machine Learning SDK.
* Uživatel vytvoří bitovou kopii pomocí modelu, souboru skóre a dalších závislostí modelu.
* Image Dockeru se vytvoří a uloží v registru kontejnerů Azure.
* Webová služba se nasadí na výpočetní cíl (Instance kontejneru/AKS) pomocí bitové kopie vytvořené v předchozím kroku.
* Podrobnosti požadavku na vyhodnocování jsou uloženy v Application Insights, který je v předplatném uživatele.
* Telemetrie se také přenese do předplatného Microsoft/Azure.

[![Pracovní postup odvození](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Další kroky

* [Zabezpečené webové služby Azure Machine Learning s TLS](how-to-secure-web-service.md)
* [Využití modelu Machine Learning usazeného jako webová služba](how-to-consume-web-service.md)
* [Jak spustit predikce dávky](how-to-use-parallel-run-step.md)
* [Monitorování modelů Azure Machine Learning pomocí přehledů aplikací](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely ve výrobě](how-to-enable-data-collection.md)
* [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Použití Azure Machine Learning u virtuální sítě Azure](how-to-enable-virtual-network.md)
* [Osvědčené postupy pro vytváření doporučených systémů](https://github.com/Microsoft/Recommenders)
* [Vytvoření rozhraní API doporučení v reálném čase v Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
