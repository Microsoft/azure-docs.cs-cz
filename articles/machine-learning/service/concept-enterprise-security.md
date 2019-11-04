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
ms.date: 11/04/2019
ms.openlocfilehash: e834c55ec35195ff627176603c7611abbf6adf1c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497513"
---
# <a name="enterprise-security-for-azure-machine-learning"></a>Podnikové zabezpečení pro Azure Machine Learning

V tomto článku se dozvíte o funkcích zabezpečení dostupných pro Azure Machine Learning.

Když použijete cloudovou službu, osvědčeným postupem je omezit přístup jenom na uživatele, kteří ho potřebují. Začněte tím, že rozumíte modelu ověřování a autorizace používaném službou. Můžete taky chtít omezit přístup k síti nebo bezpečně připojit prostředky v místní síti ke cloudu. Šifrování dat je také důležité v klidovém režimu i při přesunu dat mezi službami. Nakonec musíte být schopni sledovat službu a vystavit protokol auditu pro všechny aktivity.

## <a name="authentication"></a>Ověření

Služba Multi-Factor Authentication je podporovaná, pokud je služba Azure Active Directory (Azure AD) nakonfigurovaná tak, aby ji používala. Toto je proces ověřování:

1. Klient se přihlásí do služby Azure AD a získá token Azure Resource Manager.  Uživatelé a instanční objekty jsou plně podporované.
1. Klient prezentuje token pro Azure Resource Manager a všem Azure Machine Learning.
1. Služba Machine Learning poskytuje Machine Learning token služby pro výpočetní cíl pro uživatele (například Výpočetní prostředky služby Machine Learning). Tento token používá cílový výpočetní cíl pro zpětné volání do služby Machine Learning po dokončení spuštění. Rozsah je omezen na pracovní prostor.

[Ověřování ![v Azure Machine Learning](./media/enterprise-readiness/authentication.png)](./media/enterprise-readiness/authentication-expanded.png)

### <a name="authentication-for-web-service-deployment"></a>Ověřování pro nasazení webové služby

Azure Machine Learning podporuje dvě formy ověřování pro webové služby: klíč a token. Každá webová služba může současně povolit jenom jednu formu ověřování.

|Metoda ověřování|Azure Container Instances|AKS|
|---|---|---|
|Klíč|Zakázáno ve výchozím nastavení| Ve výchozím nastavení povoleno|
|Podpisový| Není k dispozici| Zakázáno ve výchozím nastavení |

#### <a name="authentication-with-keys"></a>Ověřování pomocí klíčů

Pokud povolíte ověřování klíčů pro nasazení, automaticky se vytvoří ověřovací klíče.

* Ověřování je ve výchozím nastavení povolené při nasazení do služby Azure Kubernetes Service (AKS).
* Ověřování je ve výchozím nastavení zakázáno při nasazení do Azure Container Instances.

Pokud chcete povolit ověřování pomocí klíče, použijte při vytváření nebo aktualizaci nasazení parametr `auth_enabled`.

Pokud je povolené klíčové ověřování, můžete k načtení primárního a sekundárního ověřovacího klíče použít metodu `get_keys`:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Pokud potřebujete znovu vygenerovat klíč, použijte [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

#### <a name="authentication-with-tokens"></a>Ověřování pomocí tokenů

Pokud povolíte ověřování pomocí tokenu pro webovou službu, uživatelé musí pro přístup k této webové službě předložit Azure Machine Learning JSON Web Token.

* Ověřování tokenu je ve výchozím nastavení při nasazení do služby Azure Kubernetes zakázané.
* Ověřování tokenu se při nasazení do Azure Container Instances nepodporuje.

K řízení ověřování pomocí tokenu použijte parametr `token_auth_enabled` při vytváření nebo aktualizaci nasazení.

Pokud je povoleno ověřování tokenu, můžete použít metodu `get_token` k načtení JSON Web Token (JWT) a času vypršení platnosti tohoto tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po `refresh_by`ovém čase tokenu budete muset požádat o nový token.
>
> Důrazně doporučujeme vytvořit pracovní prostor Azure Machine Learning ve stejné oblasti jako cluster služby Azure Kubernetes. 
>
> K ověřování pomocí tokenu webová služba provede volání do oblasti, ve které je vytvořen Azure Machine Learning pracovní prostor. Pokud oblast pracovního prostoru není k dispozici, nebudete moci načíst token pro webovou službu, i když je váš cluster v jiné oblasti než pracovní prostor. Výsledkem je, že ověřování Azure AD není k dispozici, dokud nebude oblast pracovního prostoru znovu dostupná. 
>
> Čím větší vzdálenost mezi oblastí vašeho clusteru a oblastí vašeho pracovního prostoru, tím déle bude trvat Načtení tokenu.

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
| Upgrade pracovního prostoru na Enterprise Edition | ✓ | |
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

Azure Machine Learning vytvoří další aplikaci (název začíná na `aml-` nebo `Microsoft-AzureML-Support-App-`) s přístupem na úrovni přispěvatele ve vašem předplatném pro každou oblast pracovního prostoru. Pokud máte například jeden pracovní prostor v Východní USA a jiný pracovní prostor v Severní Evropa ve stejném předplatném, uvidíte dvě z těchto aplikací. Tyto aplikace umožňují Azure Machine Learning, které vám pomůžou se správou výpočetních prostředků.

## <a name="network-security"></a>Zabezpečení sítě

Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky. Výpočetní prostředky (cíle výpočtů) se používají ke školení a nasazení modelů. Tyto výpočetní cíle můžete vytvořit ve virtuální síti. Můžete například použít Azure Data Science Virtual Machine k vytvoření výukového modelu a nasazení modelu do AKS.  

Další informace najdete v tématu [Jak spustit experimenty a odvozování ve virtuální síti](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Šifrování dat

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Machine Learning ukládá snímky, výstup a protokoly v účtu služby Azure Blob Storage, který je svázán s pracovním prostorem Azure Machine Learning a vaším předplatným. Všechna data uložená v úložišti objektů BLOB v Azure jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

Informace o tom, jak používat vlastní klíče pro data uložená v úložišti objektů BLOB v Azure, najdete [v tématu Azure Storage šifrování pomocí klíčů spravovaných zákazníkem v Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Data školení se většinou ukládají také v úložišti objektů BLOB v Azure, aby byla dostupná pro školení výpočetních cílů. Toto úložiště není spravované nástrojem Azure Machine Learning, ale je připojené k výpočetním cílům jako vzdálený systém souborů.

Informace o opětovném generování přístupových klíčů pro účty úložiště Azure, které se používají v pracovním prostoru, najdete v tématu [obnovení přístupových klíčů k úložišti](how-to-change-storage-access-key.md).

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning ukládá metriky a metadata v instanci Azure Cosmos DB přidružené k předplatnému Microsoftu spravovanému pomocí Azure Machine Learning. Všechna data uložená v Azure Cosmos DB jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

#### <a name="azure-container-registry"></a>Azure Container Registry

Všechny Image kontejneru v registru (Azure Container Registry) jsou v klidovém stavu šifrované. Azure tento obrázek před uložením automaticky zašifruje a když ho Azure Machine Learning načte, bude ho dešifrovat.

#### <a name="machine-learning-compute"></a>Výpočetní prostředky služby Machine Learning

Disk s operačním systémem pro každý výpočetní uzel, který je uložený v Azure Storage, je zašifrovaný pomocí klíčů spravovaných Microsoftem v Azure Machine Learning účty úložiště. Tento cílový výpočetní výkon je dočasný a clustery se obvykle škálují, když nejsou žádné běhy ve frontě. V podkladovém virtuálním počítači se zruší zřízení a disk s operačním systémem se odstraní. Pro disk s operačním systémem se Azure Disk Encryption nepodporuje.

Každý virtuální počítač má také místní dočasný disk pro operace s operačním systémem. Pokud chcete, můžete použít disk pro přípravu školicích dat. Disk není zašifrovaný.
Další informace o tom, jak šifrování v klidovém umístění funguje v Azure, najdete v tématu [šifrování dat Azure v klidovém umístění](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Šifrování při přenosu

Protokol SSL můžete použít k zabezpečení interní komunikace mezi Azure Machine Learning mikroslužby a k zabezpečení externích volání do bodového koncového bodu. K přístupu k Azure Storage dojde taky přes zabezpečený kanál.

Další informace najdete v tématu [použití protokolu SSL k zabezpečení webové služby prostřednictvím Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Použití Azure Key Vault

Azure Machine Learning používá instanci Azure Key Vault přidruženou k pracovnímu prostoru k ukládání přihlašovacích údajů různých typů:

* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Hesla a klíče SSH k výpočetním cílům, jako je Azure HDInsight a virtuální počítače, jsou uložené v jiném trezoru klíčů, který je přidružený k předplatnému Microsoft. Azure Machine Learning neukládají žádná hesla ani klíče poskytované uživateli. Místo toho generuje, autorizuje a ukládá vlastní klíče SSH pro připojení k virtuálním počítačům a HDInsight pro spuštění experimentů.

Každý pracovní prostor má přidruženou spravovanou identitu přiřazenou systémem, která má stejný název jako pracovní prostor. Tato spravovaná identita má přístup ke všem klíčům, tajným klíčům a certifikátům v trezoru klíčů.

## <a name="monitoring"></a>Sledování

### <a name="metrics"></a>Metriky

Metriky Azure Monitor můžete použít k zobrazení a monitorování metrik pro pracovní prostor Azure Machine Learning. V [Azure Portal](https://portal.azure.com)vyberte svůj pracovní prostor a pak vyberte **metriky**:

[![snímek obrazovky ukazující ukázkovou metriku pro pracovní prostor](./media/enterprise-readiness/workspace-metrics.png)](./media/enterprise-readiness/workspace-metrics-expanded.png)

Metriky obsahují informace o spuštění, nasazení a registracích.

Další informace najdete v tématu [metriky v Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Protokol aktivit

Můžete zobrazit protokol aktivit pracovního prostoru a zobrazit různé operace, které se provádějí v pracovním prostoru. Protokol obsahuje základní informace, jako je název operace, iniciátor události a časové razítko.

Na tomto snímku obrazovky vidíte protokol aktivity pracovního prostoru:

[![snímek obrazovky ukazující protokol aktivity pracovního prostoru](./media/enterprise-readiness/workspace-activity-log.png)](./media/enterprise-readiness/workspace-activity-log-expanded.png)

Podrobnosti žádosti o vyhodnocování jsou uložené v Application Insights. Při vytváření pracovního prostoru se ve vašem předplatném vytvoří Application Insights. Protokolované informace obsahují pole jako HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId a Duration.

> [!IMPORTANT]
> Některé akce v pracovním prostoru Azure Machine Learning neprotokolují informace do protokolu aktivit. Například spuštění školicího programu a registrace modelu se nezaprotokolují.
>
> Některé z těchto akcí se zobrazí v oblasti **aktivity** pracovního prostoru, ale tato oznámení nenaznačují, kdo aktivitu inicioval.

## <a name="data-flow-diagrams"></a>Diagramy toku dat

### <a name="create-workspace"></a>Vytvoření pracovního prostoru

Následující diagram znázorňuje pracovní postup vytvoření pracovního prostoru.

* Uživatel se přihlásí ke službě Azure AD z jednoho z podporovaných klientů Azure Machine Learning (Azure CLI, Python SDK, Azure Portal) a vyžádá příslušný Azure Resource Manager token.
* Uživatel volá Azure Resource Manager k vytvoření pracovního prostoru. 
* Azure Resource Manager kontaktuje poskytovatele prostředků Azure Machine Learning a zřídí pracovní prostor.

V předplatném uživatele se vytvoří další prostředky během vytváření pracovního prostoru:

* Key Vault (pro ukládání tajných klíčů)
* Účet služby Azure Storage (včetně objektů BLOB a sdílených souborů)
* Azure Container Registry (pro ukládání imagí Docker pro odvození/bodování a experimentování)
* Application Insights (pro uložení telemetrie)

Uživatel může také zřídit jiné výpočetní cíle, které jsou připojeny k pracovnímu prostoru (například službě Azure Kubernetes nebo virtuálním počítačům) podle potřeby.

[pracovní postup vytvoření ![pracovního prostoru](./media/enterprise-readiness/create-workspace.png)](./media/enterprise-readiness/create-workspace-expanded.png)

### <a name="save-source-code-training-scripts"></a>Uložit zdrojový kód (školicí skripty)

Následující diagram znázorňuje pracovní postup snímku kódu.

Přidruženo k pracovnímu prostoru Azure Machine Learning jsou adresáře (experimenty), které obsahují zdrojový kód (školicí skripty). Tyto skripty se ukládají na vašem místním počítači a v cloudu (ve službě Azure Blob Storage pro vaše předplatné). Snímky kódu se používají ke spuštění nebo kontrole historických auditů.

[pracovní postup snímku kódu ![](./media/enterprise-readiness/code-snapshot.png)](./media/enterprise-readiness/code-snapshot-expanded.png)

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

[pracovní postup školení ![](./media/enterprise-readiness/training-and-metrics.png)](./media/enterprise-readiness/training-and-metrics-expanded.png)

### <a name="creating-web-services"></a>Vytváření webových služeb

Následující diagram znázorňuje odvozený pracovní postup. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data.

Podrobnosti najdete tady:

* Uživatel zaregistruje model pomocí klienta, jako je Azure Machine Learning SDK.
* Uživatel vytvoří obrázek pomocí modelu, souboru skóre a dalších závislostí modelu.
* Image Docker se vytvoří a uloží v Azure Container Registry.
* Webová služba je nasazena do cíle služby COMPUTE (Container Instances/AKS) pomocí image vytvořené v předchozím kroku.
* Podrobnosti žádosti o vyhodnocování jsou uložené v Application Insights, který je v předplatném uživatele.
* Telemetrii se taky vloží do předplatného Microsoft/Azure.

[pracovní postup odvození ![](./media/enterprise-readiness/inferencing.png)](./media/enterprise-readiness/inferencing-expanded.png)

## <a name="next-steps"></a>Další kroky

* [Zabezpečené Azure Machine Learning webové služby pomocí protokolu SSL](how-to-secure-web-service.md)
* [Využití modelu Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md)
* [Jak spustit Batch předpovědi](how-to-run-batch-predictions.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Sada Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Použití Azure Machine Learning s využitím Azure Virtual Network](how-to-enable-virtual-network.md)
* [Osvědčené postupy pro sestavování doporučení pro systémy](https://github.com/Microsoft/Recommenders)
* [Sestavení rozhraní API pro doporučení v reálném čase v Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
