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
ms.date: 01/09/2020
ms.openlocfilehash: 277b22498066542deaa080845cb816df493d7e13
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122351"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Podnikové zabezpečení pro Azure Machine Learning

V tomto článku se dozvíte o funkcích zabezpečení dostupných pro Azure Machine Learning.

Když použijete cloudovou službu, osvědčeným postupem je omezit přístup jenom na uživatele, kteří ho potřebují. Začněte tím, že rozumíte modelu ověřování a autorizace používaném službou. Můžete taky chtít omezit přístup k síti nebo bezpečně připojit prostředky v místní síti ke cloudu. Šifrování dat je také důležité v klidovém režimu i při přesunu dat mezi službami. Nakonec musíte být schopni sledovat službu a vystavit protokol auditu pro všechny aktivity.

> [!NOTE]
> Informace v tomto článku jsou v sadě Azure Machine Learning Python SDK verze 1.0.83.1 nebo vyšší.

## <a name="authentication"></a>Ověření

Služba Multi-Factor Authentication je podporovaná, pokud je služba Azure Active Directory (Azure AD) nakonfigurovaná tak, aby ji používala. Toto je proces ověřování:

1. Klient se přihlásí do služby Azure AD a získá token Azure Resource Manager.  Uživatelé a instanční objekty jsou plně podporované.
1. Klient prezentuje token pro Azure Resource Manager a všem Azure Machine Learning.
1. Služba Machine Learning poskytuje Machine Learning token služby pro výpočetní cíl pro uživatele (například Výpočetní prostředky služby Machine Learning). Tento token používá cílový výpočetní cíl pro zpětné volání do služby Machine Learning po dokončení spuštění. Rozsah je omezen na pracovní prostor.

[Ověřování ![v Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication-expanded.png#lightbox)

Další informace najdete v tématu [nastavení ověřování pro Azure Machine Learning prostředky a pracovní postupy](how-to-setup-authentication.md). Tento článek obsahuje informace a příklady ověřování, včetně použití instančních objektů a automatizovaných pracovních postupů.


### <a name="authentication-for-web-service-deployment"></a>Ověřování pro nasazení webové služby

Azure Machine Learning podporuje dvě formy ověřování pro webové služby: klíč a token. Každá webová služba může současně povolit jenom jednu formu ověřování.

|Metoda ověřování|Popis|Azure Container Instances|AKS|
|---|---|---|---|
|Klíč|Klíče jsou statické a není nutné je aktualizovat. Klíče je možné znovu vygenerovat ručně.|Zakázáno ve výchozím nastavení| Ve výchozím nastavení povolena|
|Podpisový|Po zadaném časovém období vyprší platnost tokenů a je nutné ji aktualizovat.| Není k dispozici| Zakázáno ve výchozím nastavení |

Příklady kódu naleznete v [části ověřování webové služby](how-to-setup-authentication.md#web-service-authentication).

## <a name="authorization"></a>Autorizace

Můžete vytvořit víc pracovních prostorů a každý pracovní prostor může být sdílen více lidí. Když sdílíte pracovní prostor, můžete k němu řídit přístup přiřazením těchto rolí uživatelům:

* Vlastník
* Přispěvatel
* Čtenář

V následující tabulce jsou uvedené některé hlavní operace Azure Machine Learning a role, které je můžou provádět:

| Operace Azure Machine Learning | Vlastník | Přispěvatel | Čtenář |
| ---- |:----:|:----:|:----:|
| Vytvoření pracovního prostoru | ✓ | ✓ | |
| Sdílet pracovní prostor | ✓ | |  |
| Upgrade pracovního prostoru na Enterprise Edition | ✓ | |
| Vytvořit cíl výpočtů | ✓ | ✓ | |
| Připojit cíl výpočtů | ✓ | ✓ | |
| Připojení úložišť dat | ✓ | ✓ | |
| Spusťte experiment | ✓ | ✓ | |
| Zobrazit běhy/metriky | ✓ | ✓ | ✓ |
| Registrace modelu | ✓ | ✓ | |
| Vytvořit image | ✓ | ✓ | |
| Nasazení webové služby | ✓ | ✓ | |
| Zobrazení modelů a imagí | ✓ | ✓ | ✓ |
| Volání webové služby | ✓ | ✓ | ✓ |

Pokud předdefinované role nevyhovují vašim potřebám, můžete vytvořit vlastní role. Vlastní role se podporují jenom pro operace v pracovním prostoru a Výpočetní prostředky služby Machine Learning. Vlastní role mohou mít oprávnění číst, zapisovat nebo odstranit v pracovním prostoru a výpočetní prostředky v daném pracovním prostoru. Role může být dostupná na konkrétní úrovni pracovního prostoru, na konkrétní úrovni skupiny prostředků nebo na konkrétní úrovni předplatného. Další informace najdete v tématu [Správa uživatelů a rolí v pracovním prostoru Azure Machine Learning](how-to-assign-roles.md).

### <a name="securing-compute-targets-and-data"></a>Zabezpečení výpočetních cílů a dat

Vlastníci a přispěvatelé můžou používat všechny výpočetní cíle a úložiště dat, která jsou připojená k pracovnímu prostoru.  

Každý pracovní prostor má také přidruženou spravovanou identitu přiřazenou systémem, která má stejný název jako pracovní prostor. Spravovaná identita má následující oprávnění k připojeným prostředkům použitým v pracovním prostoru.

Další informace o spravovaných identitách najdete v tématu [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

| Prostředek | Oprávnění |
| ----- | ----- |
| Pracovní prostor | Přispěvatel |
| Účet úložiště | Přispěvatel dat objektu BLOB služby Storage |
| Key Vault | Přístup ke všem klíčům, tajným klíčům, certifikátům |
| Azure Container Registry | Přispěvatel |
| Skupina prostředků, která obsahuje pracovní prostor | Přispěvatel |
| Skupina prostředků, která obsahuje Trezor klíčů (Pokud se liší od tu, která obsahuje pracovní prostor) | Přispěvatel |

Nedoporučujeme, aby správci odvolali přístup ke spravované identitě k prostředkům uvedeným v předchozí tabulce. Přístup můžete obnovit pomocí operace opětovné synchronizace klíčů.

Azure Machine Learning vytvoří další aplikaci (název začíná na `aml-` nebo `Microsoft-AzureML-Support-App-`) s přístupem na úrovni přispěvatele ve vašem předplatném pro každou oblast pracovního prostoru. Například pokud máte jeden pracovní prostor v Východní USA a druhý v Severní Evropa ve stejném předplatném, uvidíte dvě z těchto aplikací. Tyto aplikace umožňují Azure Machine Learning, které vám pomůžou se správou výpočetních prostředků.

## <a name="network-security"></a>Zabezpečení sítě

Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky. Výpočetní prostředky (cíle výpočtů) se používají ke školení a nasazení modelů. Tyto výpočetní cíle můžete vytvořit ve virtuální síti. Můžete například použít Azure Data Science Virtual Machine k vytvoření výukového modelu a nasazení modelu do AKS.  

Další informace najdete v tématu [Jak spustit experimenty a odvozování ve virtuální síti](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Šifrování dat

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

> [!IMPORTANT]
> Pokud váš pracovní prostor obsahuje citlivá data, doporučujeme při vytváření pracovního prostoru nastavit [příznak hbi_workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) . Tím se řídí množství dat, která Microsoft shromažďuje pro účely diagnostiky, a umožňuje další šifrování ve spravovaných prostředích Microsoftu.


#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning ukládá snímky, výstup a protokoly v účtu služby Azure Blob Storage, který je svázán s pracovním prostorem Azure Machine Learning a vaším předplatným. Všechna data uložená v úložišti objektů BLOB v Azure jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

Informace o tom, jak používat vlastní klíče pro data uložená v úložišti objektů BLOB v Azure, najdete [v tématu Azure Storage šifrování pomocí klíčů spravovaných zákazníkem v Azure Key Vault](../storage/common/storage-encryption-keys-portal.md).

Data školení se většinou ukládají také v úložišti objektů BLOB v Azure, aby byla dostupná pro školení výpočetních cílů. Toto úložiště není spravované nástrojem Azure Machine Learning, ale je připojené k výpočetním cílům jako vzdálený systém souborů.

Informace o opětovném generování přístupových klíčů najdete v tématu [opětovné vygenerování přístupových klíčů k úložišti](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning ukládá metriky a metadata v instanci Azure Cosmos DB. Tato instance je přidružená k předplatnému Microsoftu spravovanému pomocí Azure Machine Learning. Všechna data uložená v Azure Cosmos DB jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

Pokud chcete k šifrování instance Azure Cosmos DB použít vlastní klíče (spravované zákazníkem), můžete vytvořit vyhrazenou instanci Cosmos DB pro použití s vaším pracovním prostorem. Tento postup doporučujeme, pokud chcete ukládat data, jako jsou informace o historii spuštění, mimo instanci víceklientské Cosmos DB hostované v předplatném Microsoftu. 

> [!NOTE]
> Tato funkce je aktuálně dostupná pouze v USA – východ, USA – západ 2 USA (střed) – jih.

Pokud chcete ve svém předplatném povolit zřizování Cosmos DB instance pomocí klíčů spravovaných zákazníkem, proveďte následující akce:

* Povolte klíčové funkce spravované zákazníkem pro Cosmos DB. V tuto chvíli musíte požádat o přístup k používání této možnosti. Pokud to chcete udělat, kontaktujte prosím [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

* Pokud jste to ještě neudělali, zaregistrujte Azure Machine Learning a poskytovatele prostředků Azure Cosmos DB v předplatném.

* Autorizaci aplikace Machine Learning (v části Správa identit a přístupu) s oprávněními přispěvatele v předplatném.

    ![Autorizovat Azure Machine Learning App na portálu pro správu identit a přístupu](./media/concept-enterprise-security/authorize-azure-machine-learning.png)

* Při vytváření pracovního prostoru Azure Machine Learning použijte následující parametry. Oba parametry jsou povinné a podporují se v šablonách SDK, CLI, REST API a Správce prostředků.

    * `resource_cmk_uri`: Tento parametr je úplným identifikátorem URI spravovaného klíče zákazníka ve vašem trezoru klíčů, včetně [informací o verzi klíče](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning). 

    * `cmk_keyvault`: Tento parametr je ID prostředku pro Trezor klíčů v rámci vašeho předplatného. Tento Trezor klíčů musí být ve stejné oblasti a předplatném, které budete používat pro Azure Machine Learning pracovní prostor. 
    
        > [!NOTE]
        > Tato instance trezoru klíčů se může lišit od trezoru klíčů, který je vytvořený Azure Machine Learning při zřizování pracovního prostoru. Pokud chcete pro pracovní prostor použít stejnou instanci trezoru klíčů, předejte stejný Trezor klíčů při zřizování pracovního prostoru pomocí [parametru key_vault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-). 

Tato instance Cosmos DB se vytvoří ve skupině prostředků spravovaných Microsoftem v rámci vašeho předplatného. 

> [!IMPORTANT]
> * Pokud potřebujete tuto instanci Cosmos DB odstranit, je nutné odstranit Azure Machine Learning pracovní prostor, který ji používá. 
> * Výchozí [__jednotky žádostí__](../cosmos-db/request-units.md) pro tento účet Cosmos DB jsou nastavené na __8000__. Změna této hodnoty není podporována. 

Další informace o klíčích spravovaných zákazníkem pomocí Cosmos DB najdete v tématu [konfigurace klíčů spravovaných zákazníkem pro účet Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md).

#### <a name="azure-container-registry"></a>Azure Container Registry

Všechny Image kontejneru v registru (Azure Container Registry) jsou v klidovém stavu šifrované. Azure tento obrázek před uložením automaticky zašifruje a dešifruje, když Azure Machine Learning načte image.

Pokud chcete své Azure Container Registry šifrovat pomocí vlastních klíčů (spravovaných zákazníkem), musíte si vytvořit vlastní ACR a připojit ho při zřizování pracovního prostoru nebo zašifrování výchozí instance, která se vytvoří v době zřizování pracovního prostoru.

Příklad vytvoření pracovního prostoru pomocí existující Azure Container Registry najdete v následujících článcích:

* [Vytvořte pracovní prostor pro Azure Machine Learning pomocí Azure CLI](how-to-manage-workspace-cli.md).
* [Použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro Azure Machine Learning](how-to-create-workspace-template.md)

#### <a name="azure-container-instance"></a>Instance kontejneru Azure

Instance kontejneru Azure nepodporuje šifrování disku. Pokud potřebujete šifrování disku, doporučujeme místo toho [nasadit instanci služby Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) . V takovém případě můžete také použít podporu Azure Machine Learning pro řízení přístupu na základě rolí, abyste zabránili nasazením instance kontejneru Azure v rámci vašeho předplatného.

#### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Nasazený prostředek služby Azure Kubernetes můžete šifrovat kdykoli pomocí klíčů spravovaných zákazníkem. Další informace najdete na adrese [https://aka.ms/aks/byok](https://aka.ms/aks/byok). 

Tento proces umožňuje šifrovat data i disk s operačním systémem nasazených virtuálních počítačů v clusteru Kubernetes.

> [!IMPORTANT]
> Tento proces funguje jenom s AKS K8s verze 1,16 nebo vyšší. Azure Machine Learning přidat podporu pro AKS 1,16 na 13. ledna 2020.

#### <a name="machine-learning-compute"></a>Výpočetní prostředky služby Machine Learning

Disk s operačním systémem pro každý výpočetní uzel, který je uložený v Azure Storage, je zašifrovaný pomocí klíčů spravovaných Microsoftem v Azure Machine Learning účty úložiště. Tento cílový výpočetní výkon je dočasný a clustery se obvykle škálují, když nejsou žádné běhy ve frontě. V podkladovém virtuálním počítači se zruší zřízení a disk s operačním systémem se odstraní. Pro disk s operačním systémem se Azure Disk Encryption nepodporuje.

Každý virtuální počítač má také místní dočasný disk pro operace s operačním systémem. Pokud chcete, můžete použít disk pro přípravu školicích dat. Disk je ve výchozím nastavení zašifrovaný pro pracovní prostory s parametrem `hbi_workspace` nastaveným na hodnotu `TRUE`. Toto prostředí je krátkodobé jenom po dobu trvání běhu a podpora šifrování je omezená jenom na klíče spravované systémem.

Další informace o tom, jak šifrování v klidovém umístění funguje v Azure, najdete v tématu [šifrování dat Azure v klidovém umístění](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Šifrování během přenosu

Protokol SSL můžete použít k zabezpečení interní komunikace mezi Azure Machine Learning mikroslužby a k zabezpečení externích volání do bodového koncového bodu. K přístupu k Azure Storage dojde taky přes zabezpečený kanál.

Další informace najdete v tématu [použití protokolu SSL k zabezpečení webové služby prostřednictvím Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-secure-web-service).

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

Microsoft také doporučuje do proměnných prostředí ukládat citlivé informace (třeba klíčová tajná klíče účtu). Proměnné prostředí jsou protokolovány, šifrovány a uloženy v USA.

Shromážděná diagnostická data můžete odhlásit tím, že nastavíte parametr `hbi_workspace`, který `TRUE` při zřizování pracovního prostoru. Tato funkce se podporuje při použití šablon aplikace AzureML Python SDK, CLI, REST API nebo Azure Resource Manager.

### <a name="microsoft-generated-data"></a>Data generovaná společností Microsoft

Při používání služeb, jako jsou například automatizované Machine Learning, může společnost Microsoft vygenerovat přechodná a předem zpracovaná data pro školení více modelů. Tato data jsou uložená v úložišti dat ve vašem pracovním prostoru, což vám umožní patřičně vymáhat řízení přístupu a šifrování.

Můžete také chtít šifrovat [diagnostické informace zaznamenané z nasazeného koncového bodu](how-to-enable-app-insights.md) do instance služby Azure Application Insights.

## <a name="monitoring"></a>Sledování

### <a name="metrics"></a>Metriky

Metriky Azure Monitor můžete použít k zobrazení a monitorování metrik pro pracovní prostor Azure Machine Learning. V [Azure Portal](https://portal.azure.com)vyberte svůj pracovní prostor a pak vyberte **metriky**:

[![snímek obrazovky ukazující ukázkovou metriku pro pracovní prostor](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Metriky obsahují informace o spuštění, nasazení a registracích.

Další informace najdete v tématu [metriky v Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Protokol aktivit

Můžete zobrazit protokol aktivit pracovního prostoru a zobrazit různé operace, které se provádějí v pracovním prostoru. Protokol obsahuje základní informace, jako je název operace, iniciátor události a časové razítko.

Na tomto snímku obrazovky vidíte protokol aktivity pracovního prostoru:

[![snímek obrazovky ukazující protokol aktivity pracovního prostoru](media/concept-enterprise-security/workspace-activity-log.png)](media/concept-enterprise-security/workspace-activity-log-expanded.png#lightbox)

Podrobnosti žádosti o vyhodnocování jsou uložené v Application Insights. Při vytváření pracovního prostoru se ve vašem předplatném vytvoří Application Insights. Protokolované informace obsahují pole jako:

* HTTPMethod
* UserAgent
* ComputeType
* RequestUrl
* StatusCode
* RequestId
* Délka

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

[pracovní postup vytvoření ![pracovního prostoru](media/concept-enterprise-security/create-workspace.png)](media/concept-enterprise-security/create-workspace-expanded.png#lightbox)

### <a name="save-source-code-training-scripts"></a>Uložit zdrojový kód (školicí skripty)

Následující diagram znázorňuje pracovní postup snímku kódu.

Přidruženo k pracovnímu prostoru Azure Machine Learning jsou adresáře (experimenty), které obsahují zdrojový kód (školicí skripty). Tyto skripty se ukládají na vašem místním počítači a v cloudu (ve službě Azure Blob Storage pro vaše předplatné). Snímky kódu se používají ke spuštění nebo kontrole historických auditů.

[pracovní postup snímku kódu ![](media/concept-enterprise-security/code-snapshot.png)](media/concept-enterprise-security/code-snapshot-expanded.png#lightbox)

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

[pracovní postup školení ![](media/concept-enterprise-security/training-and-metrics.png)](media/concept-enterprise-security/training-and-metrics-expanded.png#lightbox)

### <a name="creating-web-services"></a>Vytváření webových služeb

Následující diagram znázorňuje odvozený pracovní postup. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data.

Podrobnosti najdete tady:

* Uživatel zaregistruje model pomocí klienta, jako je Azure Machine Learning SDK.
* Uživatel vytvoří obrázek pomocí modelu, souboru skóre a dalších závislostí modelu.
* Image Docker se vytvoří a uloží v Azure Container Registry.
* Webová služba je nasazena do cíle služby COMPUTE (Container Instances/AKS) pomocí image vytvořené v předchozím kroku.
* Podrobnosti žádosti o vyhodnocování jsou uložené v Application Insights, který je v předplatném uživatele.
* Telemetrii se taky vloží do předplatného Microsoft/Azure.

[pracovní postup odvození ![](media/concept-enterprise-security/inferencing.png)](media/concept-enterprise-security/inferencing-expanded.png#lightbox)

## <a name="next-steps"></a>Další kroky

* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Využití modelu Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md)
* [Jak spustit predikcí služby batch](how-to-use-parallel-run-step.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Použití Azure Machine Learning s využitím Azure Virtual Network](how-to-enable-virtual-network.md)
* [Osvědčené postupy pro sestavování doporučení pro systémy](https://github.com/Microsoft/Recommenders)
* [Sestavení rozhraní API pro doporučení v reálném čase v Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
