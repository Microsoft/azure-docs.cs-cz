---
title: Podnikové zabezpečení
titleSuffix: Azure Machine Learning service
description: 'Bezpečně používejte Azure Machine Learning službu: ověřování, autorizaci, zabezpečení sítě, šifrování dat a monitorování.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/07/2019
ms.openlocfilehash: d1ad89943f6acfec6e42199ef399643be12e2b8b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856216"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Enterprise Security for Azure Machine Learning Service

V tomto článku se dozvíte o funkcích zabezpečení dostupných ve službě Azure Machine Learning.

Při použití cloudové služby je osvědčeným postupem omezit přístup jenom na uživatele, kteří ho potřebují. Tím se zahájí porozumění modelu ověřování a autorizace používaného službou. Můžete taky chtít omezit přístup k síti nebo bezpečně spojit prostředky v místní síti s nástroji v cloudu. Šifrování dat je také důležité v klidovém režimu i při přesunu dat mezi službami. Nakonec musíte být schopni sledovat službu a vystavit protokol auditu pro všechny aktivity.

## <a name="authentication"></a>Ověřování

Multi-Factor Authentication se podporuje, pokud je Azure Active Directory (Azure AD) nakonfigurované pro stejné.

* Klient se do Azure AD přihlásí a získá token Azure Resource Manager.  Uživatelé a instanční objekty jsou plně podporované.
* Klient prezentuje token Azure Resource Manager & všechny Azure Machine Learning služby.
* Služba Azure Machine Learning poskytuje výpočetnímu prostředí pro uživatele Azure Machine Learning token. Například Výpočetní prostředky služby Machine Learning. Tento Azure Machine Learning token používá výpočetní výkon pro volání zpět do služby Azure Machine Learning (omezení oboru do pracovního prostoru) po dokončení spuštění.

![Snímek obrazovky ukazující, jak ověřování funguje ve službě Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-for-web-service-deployment"></a>Ověřování pro nasazení webové služby

Azure Machine Learning podporuje dvě formy ověřování pro webové služby, klíč a token. Každá webová služba může současně povolit jenom jednu formu ověřování.

|Metoda ověření|ACI|AKS|
|---|---|---|
|Klíč|Zakázáno ve výchozím nastavení| Ve výchozím nastavení povoleno|
|Podpisový| Není dostupné| Zakázáno ve výchozím nastavení |

#### <a name="authentication-with-keys"></a>Ověřování pomocí klíčů

Pokud povolíte ověřování klíčů pro nasazení, automaticky se vytvoří ověřovací klíče.

* Ověřování je ve výchozím nastavení povolené při nasazení do služby Azure Kubernetes.
* Ověřování je ve výchozím nastavení zakázáno při nasazení do Azure Container Instances.

Pokud chcete povolit ověřování pomocí klíče, `auth_enabled` použijte při vytváření nebo aktualizaci nasazení parametr.

Pokud je povolené klíčové ověřování, můžete k načtení primárního `get_keys` a sekundárního ověřovacího klíče použít metodu:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Pokud potřebujete znovu vygenerovat klíč, použijte[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

#### <a name="authentication-with-tokens"></a>Ověřování pomocí tokenů

Pokud povolíte ověřování pomocí tokenu pro webovou službu, musí uživatel poskytnout Azure Machine Learning JSON Web Token webové službě, aby k ní měl přístup.

* Ověřování tokenu je ve výchozím nastavení zakázáno při nasazení do služby Azure Kubernetes.
* Ověřování tokenu není při nasazení do Azure Container Instances podporováno.

K řízení ověřování tokenu použijte `token_auth_enabled` parametr při vytváření nebo aktualizaci nasazení.

Pokud je povoleno ověřování tokenu, můžete použít `get_token` metodu k načtení tokenu JWT a času vypršení platnosti tokenu:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Po `refresh_by` čase tokenu budete muset požádat o nový token.
>
> Důrazně doporučujeme vytvořit pracovní prostor Azure Machine Learning ve stejné oblasti jako cluster služby Azure Kubernetes. K ověřování pomocí tokenu webová služba provede volání do oblasti, ve které je vytvořen Azure Machine Learning pracovní prostor. Pokud oblast pracovního prostoru není k dispozici, nebudete moci načíst token pro vaši webovou službu, a to i v případě, že se váš cluster nachází v jiné oblasti než v pracovním prostoru. To efektivně vede k nedostupnosti ověřování Azure AD, dokud nebude oblast pracovního prostoru znovu dostupná. Navíc čím větší je vzdálenost mezi oblastí vašeho clusteru a oblastí vašeho pracovního prostoru, tím déle bude trvat Načtení tokenu.

## <a name="authorization"></a>Authorization

Můžete vytvořit víc pracovních prostorů a každý pracovní prostor může být sdílen více lidí. Když sdílíte pracovní prostor, můžete k němu řídit přístup přiřazením následujících rolí uživatelům:

* Owner
* Přispěvatel
* Čtenář

V následující tabulce jsou uvedené některé hlavní operace Azure Machine Learning služby a role, které je můžou provádět:

| Operace Azure Machine Learning služby | Owner | Přispěvatel | Čtenář |
| ---- |:----:|:----:|:----:|
| Vytvořit pracovní prostor | ✓ | ✓ | |
| Sdílet pracovní prostor | ✓ | |  |
| Vytvořit výpočetní prostředky | ✓ | ✓ | |
| Připojit výpočetní prostředky | ✓ | ✓ | |
| Připojit úložiště dat | ✓ | ✓ | |
| Spuštění experimentu | ✓ | ✓ | |
| Zobrazit běhy/metriky | ✓ | ✓ | ✓ |
| Registrace modelu | ✓ | ✓ | |
| Vytvořit image | ✓ | ✓ | |
| Nasazení webové služby | ✓ | ✓ | |
| Zobrazení modelů a imagí | ✓ | ✓ | ✓ |
| Volání webové služby | ✓ | ✓ | ✓ |

Pokud předdefinované role nestačí pro vaše potřeby, můžete také vytvořit vlastní role. Jedinými vlastními rolemi, které podporujeme, jsou operace v pracovním prostoru a Výpočetní prostředky služby Machine Learning. Vlastní role mohou mít oprávnění číst, zapisovat nebo odstranit v pracovním prostoru a výpočetní prostředky v tomto pracovním prostoru. Role může být dostupná na konkrétní úrovni pracovního prostoru, na konkrétní úrovni skupiny prostředků nebo na konkrétní úrovni předplatného. Další informace najdete v tématu [Správa uživatelů a rolí v pracovním prostoru Azure Machine Learning](how-to-assign-roles.md) .

### <a name="securing-compute-and-data"></a>Zabezpečení výpočetních prostředků a dat

Vlastníci a přispěvatelé můžou používat všechny výpočetní cíle a úložiště dat, která jsou připojená k pracovnímu prostoru.  
Každý pracovní prostor má také přidruženou spravovanou identitu přiřazenou systémem (se stejným názvem jako má pracovní prostor) s následujícími oprávněními k připojeným prostředkům použitým v pracovním prostoru:

Další informace o spravovaných identitách najdete v tématu [spravované identity pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) .

| Resource | Oprávnění |
| ----- | ----- |
| Pracovní prostor | Přispěvatel |
| Účet úložiště | Přispěvatel dat v objektech blob služby Storage |
| Key Vault | Přístup ke všem klíčům, tajným klíčům, certifikátům |
| Registr kontejneru Azure | Přispěvatel |
| Skupina prostředků, která obsahuje pracovní prostor | Přispěvatel |
| Skupina prostředků obsahující Key Vault (Pokud se liší od ta, která obsahuje pracovní prostor) | Přispěvatel |

Doporučujeme, aby správci nezrušili přístup ke spravované identitě k výše uvedeným prostředkům. Přístup se dá obnovit pomocí operace opětovné synchronizace klíčů.

Služba Azure Machine Learning vytvoří další aplikaci (název začíná `aml-`) s přístupem na úrovni přispěvatele v rámci vašeho předplatného pro každou oblast pracovního prostoru. Pro ex. Pokud máte pracovní prostor v Východní USA a jiný pracovní prostor v Severní Evropa ve stejném předplatném, zobrazí se dvě takové aplikace. To je potřeba, aby služba Azure Machine Learning mohla pomáhat při správě výpočetních prostředků.

## <a name="network-security"></a>Zabezpečení sítě

Služba Azure Machine Learning spoléhá na další služby Azure pro výpočetní prostředky. Výpočetní prostředky (cíle výpočtů) se používají ke školení a nasazení modelů. Tyto výpočetní cíle je možné vytvořit ve virtuální síti. Například můžete použít Microsoft Data Science Virtual Machine pro výuku modelu a pak model nasadit do služby Azure Kubernetes Service (AKS).  

Další informace najdete v tématu [Jak spustit experimenty a odvozování ve virtuální síti](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Šifrování dat

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

#### <a name="azure-blob-storage"></a>Azure Blob Storage

Služba Azure Machine Learning ukládá snímky, výstupy a protokoly v účtu Azure Blob Storage, který se váže k pracovnímu prostoru služby Azure Machine Learning a žije v předplatném uživatele. Všechna data uložená v Azure Blob Storage jsou v klidovém stavu šifrovaná pomocí klíčů spravovaných Microsoftem.

Další informace o tom, jak přenést vlastní klíče pro data uložená v Azure Blob Storage, najdete v tématu [šifrování služby Storage použití klíčů spravovaných zákazníkem v Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Data školení se většinou ukládají také v úložišti objektů BLOB v Azure, aby byla dostupná pro školení výpočtů. Toto úložiště nespravuje Azure Machine Learning, ale je připojené k výpočetnímu systému jako vzdálený systém souborů.

Informace o opětovném generování přístupových klíčů pro účty úložiště Azure, které se používají v pracovním prostoru, najdete v článku o opětovném vygenerování přístupových klíčů k [úložišti](how-to-change-storage-access-key.md) .

#### <a name="cosmos-db"></a>Databáze Cosmos

Služba Azure Machine Learning ukládá metriky a metadata do Cosmos DB, která bydlí v předplatném Microsoftu spravovaném službou Azure Machine Learning. Všechna data uložená v Cosmos DB jsou v klidovém stavu zašifrovaná pomocí spravovaných klíčů Microsoftu.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)

Všechny Image kontejneru v registru (ACR) jsou v klidovém stavu šifrované. Azure tento obrázek automaticky šifruje před uložením a dešifruje ho, když Azure Machine Learning služba napustí image.

#### <a name="machine-learning-compute"></a>Výpočetní prostředky služby Machine Learning

Disk s operačním systémem pro každý výpočetní uzel je uložený v Azure Storage je zašifrovaný pomocí spravovaných klíčů Microsoftu v Azure Machine Learning účty úložiště služby. Toto výpočetní prostředí je dočasné a clustery se obvykle škálují, když nejsou ve frontě žádné běhy. Základní virtuální počítač je nezřízený a disk s operačním systémem se odstranil. Pro disk s operačním systémem se nepodporuje Azure Disk Encryption.
Každý virtuální počítač má také místní dočasný disk pro operace s operačním systémem. Tento disk můžete také volitelně použít k přípravě školicích dat. Tento disk není zašifrovaný.
Další informace o tom, jak šifrování v klidovém umístění funguje v Azure, najdete v tématu [šifrování dat Azure v klidovém umístění](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest).

### <a name="encryption-in-transit"></a>Šifrování při přenosu

Interní komunikace mezi různými Azure Machine Learning mikroslužeb a externí komunikací volání koncového bodu je podporovaná pomocí protokolu SSL. Veškerý přístup k Azure Storage je taky přes zabezpečený kanál.
Další informace najdete v tématu [zabezpečené Azure Machine Learning webové služby pomocí protokolu SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Použití Azure Key Vault

Služba Azure Machine Learning používá k ukládání přihlašovacích údajů různých typů Key Vault instanci přidruženou k pracovnímu prostoru:

* Přidružený připojovací řetězec účtu úložiště
* Hesla k instancím služby Azure Container úložiště
* Připojovací řetězce k úložištím dat

Hesla a klíče SSH k výpočetním cílům, jako je HDI HDInsight a VM, se ukládají v samostatné Key Vault, která je přidružená k předplatnému Microsoft. Služba Azure Machine Learning neukládá žádná hesla ani klíče poskytnuté uživatelem, ale generuje, autorizuje a ukládá vlastní klíče SSH pro připojení k virtuálnímu počítači/HDInsight za účelem spuštění experimentů.
Každý pracovní prostor má přidruženou spravovanou identitu přiřazenou systémem (se stejným názvem jako má pracovní prostor), který má přístup ke všem klíčům, tajným klíčům a certifikátům v Key Vault.

## <a name="monitoring"></a>Monitorování

### <a name="metrics"></a>Metriky

Metriky Azure Monitor lze použít k zobrazení a monitorování metrik pro pracovní prostor služby Azure Machine Learning. V [Azure Portal](https://portal.azure.com)vyberte svůj pracovní prostor a pak použijte odkaz __metriky__ .

![Snímek obrazovky znázorňující ukázkovou metriku pro pracovní prostor](./media/enterprise-readiness/workspace-metrics.png)

Metriky obsahují informace o spuštění, nasazení a registracích.

Další informace najdete v tématu [metriky v Azure monitor](/azure/azure-monitor/platform/data-platform-metrics).

### <a name="activity-log"></a>Protokol aktivit

Pomocí protokolu aktivit v pracovním prostoru můžete zobrazit různé operace provedené v pracovním prostoru a získat základní informace, jako je název operace, událost iniciovaná nástrojem, časové razítko atd.

Na následujícím snímku obrazovky vidíte protokol aktivit pro pracovní prostor:

![Snímek obrazovky zobrazující protokol aktivit v pracovním prostoru](./media/enterprise-readiness/workspace-activity-log.png)

Podrobnosti žádosti o bodování se ukládají do AppInsights, který se vytvoří v předplatném uživatele při vytváření pracovního prostoru. To zahrnuje pole jako HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, Duration atd.

## <a name="data-flow-diagram"></a>Diagram toku dat

### <a name="create-workspace"></a>Vytvoření pracovního prostoru

Následující diagram znázorňuje pracovní postup vytvoření pracovního prostoru.
Uživatel se do Azure AD přihlásí z libovolného podporovaného klienta služby Azure Machine Learning (rozhraní příkazového řádku, sady Python SDK a Azure Portal) a vyžádá příslušný Azure Resource Manager token. Uživatel pak zavolá Azure Resource Manager k vytvoření pracovního prostoru.  Azure Resource Manager kontaktuje poskytovatele prostředků služby Azure Machine Learning a zřídí pracovní prostor.  Při vytváření pracovního prostoru se v předplatném zákazníka vytvoří další prostředky:

* Trezor klíčů (pro ukládání tajných klíčů)
* Účet Azure Storage (včetně sdílení souborů BLOB &)
* Azure Container Registry (pro ukládání imagí Docker pro odvození/bodování a experimentování)
* Application Insights (pro uložení telemetrie)

Další výpočetní prostředky připojené k pracovnímu prostoru (služba Azure Kubernetes, virtuální počítač atd.) můžou zákazníci zřídit taky podle potřeby.

![Snímek obrazovky znázorňující pracovní postup vytvoření pracovního prostoru](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Uložit zdrojový kód (školicí skripty)

Následující diagram znázorňuje pracovní postup snímku kódu.
Přidruženo k pracovnímu prostoru služby Azure Machine Learning jsou adresáře (experimenty), které obsahují zdrojový kód (školicí skripty).  Jsou uložené v místním počítači zákazníka a v cloudu (v Azure Blob Storage v rámci předplatného zákazníka). Tyto snímky kódu se používají ke spuštění nebo kontrole historických auditů.

![Snímek obrazovky znázorňující pracovní postup vytvoření pracovního prostoru](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Školení

Následující diagram znázorňuje pracovní postup školení.

* Služba Azure Machine Learning je volána s ID snímku pro snímek kódu, který byl uložen výše.
* Služba Azure Machine Learning vytváří ID běhu (volitelné) & Azure Machine Learning služby, které jsou později používány výpočetními cíli, jako je Výpočetní prostředky služby Machine Learning/VM, ke komunikaci se službou Azure Machine Learning
* Můžete vybrat buď spravovaný výpočetní výkon (např. Výpočetní prostředky služby Machine Learning) nebo nespravovaný výpočetní výkon (např. VM) pro spuštění školicích úloh. Tok dat je vysvětlen pro následující scénáře:
* (Virtuální počítač/HDInsight – přístup pomocí přihlašovacích údajů SSH v Key Vault v předplatném Microsoftu) Služba Azure Machine Learning spouští kód pro správu na výpočetním cíli, který:

   1. Připraví prostředí. (Docker je také možnost pro virtuální počítače a místní. Pokud chcete zjistit, jak funguje experiment na kontejneru Docker, přečtěte si následující postup Výpočetní prostředky služby Machine Learning.)
   1. Stáhne kód.
   1. Nastaví proměnné prostředí a konfigurace.
   1. Spustí skript uživatele (výše uvedený snímek kódu).

* (Výpočetní prostředky služby Machine Learning – k němuž se přistupovalo pomocí spravované identity v pracovním prostoru) Vzhledem k tomu, že Výpočetní prostředky služby Machine Learning je spravovaný výpočetní výkon, je spravován Microsoftem v důsledku toho, že se spustí v rámci předplatného společnosti Microsoft.

   1. V případě potřeby se spustí konstrukce vzdáleného Docker.
   1. Zapisuje kód správy do uživatelské sdílené složky Azure.
   1. Spustí kontejner s počátečním příkazem, tj. kód správy, jak je popsáno v předchozím kroku.

#### <a name="querying-runs-and-metrics"></a>Dotazování na běhy a metriky

Tento krok se zobrazuje v toku, ve kterém služba Training COMPUTE zapisuje *metriky spuštění* zpátky do služby Azure Machine Learning, ze které se uloží do Cosmos DB. Klienti mohou volat službu Azure Machine Learning, která bude z Cosmos DB znovu aktivovat metriky a vrátit ji zpět do klienta.

![Snímek obrazovky znázorňující pracovní postup vytvoření pracovního prostoru](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Vytváření webových služeb

Následující diagram znázorňuje odvozený pracovní postup. Odvození modelu nebo Bodové hodnocení je fáze, ve které se nasazený model používá pro předpověď, nejčastěji pro produkční data.
Podívejte se na podrobnosti níže:

* Uživatel zaregistruje model pomocí klienta, jako je Azure ML SDK.
* Uživatel vytvoří obrázek pomocí modelu, souboru skóre a dalších závislostí modelu.
* Image Docker se vytvoří a uloží v ACR.
* Služba WebService je nasazená do cíle služby COMPUTE (ACI/AKS) pomocí image vytvořené výše.
* Podrobnosti žádosti o vyhodnocování se ukládají do AppInsights, který je v předplatném uživatele.
* Telemetrie se taky vloží do předplatného Microsoft/Azure.

![Snímek obrazovky znázorňující pracovní postup vytvoření pracovního prostoru](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>Další postup

* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)
* [Jak spustit predikcí služby batch](how-to-run-batch-predictions.md)
* [Monitorování modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat pro modely v produkčním prostředí](how-to-enable-data-collection.md)
* [Sada SDK služby Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Použití Azure Machine Learning služby s virtuálními sítěmi Azure](how-to-enable-virtual-network.md)
* [Osvědčené postupy pro sestavování doporučení pro systémy](https://github.com/Microsoft/Recommenders)
* [Sestavení rozhraní API pro doporučení v reálném čase v Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
