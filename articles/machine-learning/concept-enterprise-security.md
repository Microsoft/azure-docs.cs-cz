---
title: Podnikové zabezpečení a zásady správného řízení
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
ms.openlocfilehash: 4e2bcb683c9d4c5248315549bf6d6ee26b2a51ac
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965030"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Podnikové zabezpečení a zásady správného řízení pro Azure Machine Learning

V tomto článku se dozvíte o funkcích zabezpečení dostupných pro Azure Machine Learning.

Když použijete cloudovou službu, osvědčeným postupem je omezit přístup jenom na uživatele, kteří ho potřebují. Začněte tím, že rozumíte modelu ověřování a autorizace používaném službou. Můžete taky chtít omezit přístup k síti nebo bezpečně připojit prostředky v místní síti ke cloudu. Šifrování dat je také důležité v klidovém režimu i při přesunu dat mezi službami. Můžete také vytvořit zásady pro vykonání určitých konfigurací nebo protokolování při vytváření nevyhovujících konfigurací. Nakonec musíte být schopni sledovat službu a vystavit protokol auditu pro všechny aktivity.

> [!NOTE]
> Informace v tomto článku jsou v sadě Azure Machine Learning Python SDK verze 1.0.83.1 nebo vyšší.

## <a name="authentication--authorization"></a>Ověřování & autorizaci

Většina ověřování pro Azure Machine Learning prostředky používá pro ověřování Azure Active Directory (Azure AD) a řízení přístupu na základě role (Azure RBAC) pro autorizaci. Výjimkou jsou tyto výjimky:

* __SSH__: můžete povolit přístup přes SSH k některým výpočetním prostředkům, jako je například Azure Machine Learning výpočetní instance. Přístup SSH používá ověřování na základě klíčů. Další informace o vytváření klíčů SSH najdete v tématu [vytváření a Správa klíčů ssh](../virtual-machines/linux/create-ssh-keys-detailed.md). Informace o povolení přístupu přes SSH najdete v tématu [Vytvoření a správa Azure Machine Learning výpočetní instance](how-to-create-manage-compute-instance.md).
* __Modely nasazené jako webové služby__: nasazení webové služby může používat __klíč__ nebo řízení přístupu na základě __tokenu__. Klíče jsou statické řetězce. Tokeny se načítají pomocí účtu Azure AD. Další informace najdete v tématu [Konfigurace ověřování pro modely nasazené jako webové služby](how-to-authenticate-web-service.md).

Konkrétní služby, které Azure Machine Learning spoléhá na službu Azure Data Storage, mají vlastní metody ověřování a autorizace. Další informace o ověřování služeb úložiště najdete v tématu [připojení ke službám úložiště](how-to-access-data.md).

### <a name="azure-ad-authentication"></a>Ověřování Azure AD

Služba Multi-Factor Authentication je podporovaná, pokud je služba Azure Active Directory (Azure AD) nakonfigurovaná tak, aby ji používala. Toto je proces ověřování:

1. Klient se přihlásí do služby Azure AD a získá token Azure Resource Manager.  Uživatelé a instanční objekty jsou plně podporované.
1. Klient prezentuje token pro Azure Resource Manager a všem Azure Machine Learning.
1. Služba Machine Learning poskytuje Machine Learning token služby pro výpočetní cíl pro uživatele (například Výpočetní prostředky služby Machine Learning). Tento token používá cílový výpočetní cíl pro zpětné volání do služby Machine Learning po dokončení spuštění. Rozsah je omezen na pracovní prostor.

[![Ověřování v Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Další informace najdete v tématu [ověřování pro Azure Machine Learning pracovní prostor](how-to-setup-authentication.md).

### <a name="azure-rbac"></a>Azure RBAC

Můžete vytvořit několik pracovních prostorů a každý pracovní prostor může sdílet více lidí. Můžete určit, k jakým funkcím nebo operacím uživatelů pracovního prostoru máte přístup, přiřazením svého účtu Azure AD k rolím Azure. Níže jsou uvedené předdefinované role:

* Vlastník
* Přispěvatel
* Čtenář

Vlastníci a přispěvatelé můžou používat všechny výpočetní cíle a úložiště dat, která jsou připojená k pracovnímu prostoru.  

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

Pokud předdefinované role nevyhovují vašim potřebám, můžete vytvořit vlastní role. Vlastní role řídí všechny operace v pracovním prostoru, například vytváření výpočetních prostředků, odesílání a registraci úložiště dat nebo nasazení modelu. Vlastní role můžou mít oprávnění ke čtení, zápisu a odstraňování různých prostředků pracovního prostoru, jako jsou clustery, úložiště dat, modely a koncové body. Role může být dostupná na konkrétní úrovni pracovního prostoru, na konkrétní úrovni skupiny prostředků nebo na konkrétní úrovni předplatného. Další informace najdete v tématu [Správa uživatelů a rolí v pracovním prostoru Azure Machine Learning](how-to-assign-roles.md).

Další informace o používání RBAC s Kubernetes najdete v tématu [Azure Role-Based Access Control pro autorizaci Kubernetes](../aks/manage-azure-rbac.md).

> [!IMPORTANT]
> Azure Machine Learning závisí na dalších službách Azure, jako je Azure Blob Storage a Azure Kubernetes Services. Každá služba Azure má vlastní konfigurace služby Azure RBAC. Abyste dosáhli požadované úrovně řízení přístupu, možná budete muset použít konfiguraci Azure RBAC pro Azure Machine Learning i pro služby, které se používají s Azure Machine Learning.

> [!WARNING]
> Azure Machine Learning se podporuje při spolupráci Azure Active Directory Business-to-Business, ale v současné době se nepodporují u Azure Active Directory spolupráce od firmy po spotřebitele.

### <a name="managed-identities"></a>Spravované identity

Každý pracovní prostor má také přidruženou [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) přiřazenou systémem, která má stejný název jako pracovní prostor. Spravovaná identita se používá k zabezpečenému přístupu k prostředkům používaným pracovním prostorem. Má následující oprávnění k připojeným prostředkům:

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

Volitelně můžete nakonfigurovat vlastní spravované identity pro použití s Azure Virtual Machines a Azure Machine Learning výpočetním clusterem. Pomocí virtuálního počítače se spravovaná identita dá použít pro přístup k vašemu pracovnímu prostoru ze sady SDK namísto účtu Azure AD jednotlivých uživatelů. Pomocí výpočetního clusteru se spravovaná identita používá pro přístup k prostředkům, jako je zabezpečené úložiště dat, ke kterému uživatel spouštějící školicí úlohu nemusí mít přístup. Další informace najdete v tématu [ověřování pro Azure Machine Learning pracovní prostor](how-to-setup-authentication.md).

## <a name="network-security-and-isolation"></a>Zabezpečení a izolace sítě

K omezení fyzického přístupu k prostředkům Azure Machine Learning můžete použít Azure Virtual Network (VNet). Virtuální sítě umožňují vytvářet síťová prostředí, která jsou částečně nebo plně izolovaná od veřejného Internetu. Tím se sníží plocha pro útoky na vaše řešení a také šance na exfiltrace dat.

Další informace najdete v následujících dokumentech:

* [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md)
* [Zabezpečené prostředky pracovního prostoru](how-to-secure-workspace-vnet.md)
* [Zabezpečené prostředí pro trénování](how-to-secure-training-vnet.md)
* [Zabezpečené odvozené prostředí](how-to-secure-inferencing-vnet.md)
* [Použití studia v zabezpečené virtuální síti](how-to-enable-studio-virtual-network.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Šifrování dat

Azure Machine Learning používá nejrůznější výpočetní prostředky a úložiště dat. Další informace o tom, jak každý z těchto způsobů podporuje šifrování dat v klidovém umístění a při přenosu, najdete v tématu [šifrování dat pomocí Azure Machine Learning](concept-data-encryption.md).

### <a name="microsoft-generated-data"></a>Data generovaná společností Microsoft

Při používání služeb, jako jsou například automatizované Machine Learning, může společnost Microsoft vygenerovat přechodná a předem zpracovaná data pro školení více modelů. Tato data jsou uložená v úložišti dat ve vašem pracovním prostoru, což vám umožní patřičně vymáhat řízení přístupu a šifrování.

Můžete také chtít šifrovat [diagnostické informace zaznamenané z nasazeného koncového bodu](how-to-enable-app-insights.md) do instance služby Azure Application Insights.

## <a name="monitoring"></a>Monitorování

K dispozici je několik scénářů monitorování, které se Azure Machine Learning v závislosti na roli a co se sleduje.

| Role | Monitorování, které se má použít | Popis |
| ---- | ----- | ----- |
| Správce, DevOps, MLOps | [Azure monitor metriky](#azure-monitor), [Protokol aktivit](#activity-log), [Kontrola ohrožení zabezpečení](#vulnerability-scanning) | Informace o úrovni služby |
| Data – vědecký a MLOps | [Monitorování spuštění](#monitor-runs) | Informace zaznamenané během školicích běhů |
| MLOps | [Shromažďovat data modelu](how-to-enable-data-collection.md), [monitorovat pomocí Application Insights](how-to-enable-app-insights.md) | Informace zaznamenávané modely nasazenými jako webové služby nebo moduly IoT Edge|

### <a name="monitor-runs"></a>Monitorování spuštění

Můžete sledovat experimenty spouštěné v Azure Machine Learning, včetně informací o protokolování z vašich školicích skriptů. Tyto informace si můžete prohlédnout v sadě SDK, Azure CLI a studiu. Další informace najdete v následujících článcích:

* [Spuštění, monitorování a zrušení školicích běhů](how-to-manage-runs.md)
* [Povolení protokolů](how-to-track-experiments.md)
* [Zobrazení protokolů](how-to-monitor-view-training-logs.md)
* [Vizualizace spuštění s využitím TensorBoardu](how-to-monitor-tensorboard.md)

### <a name="azure-monitor"></a>Azure Monitor

Metriky Azure Monitor můžete použít k zobrazení a monitorování metrik pro pracovní prostor Azure Machine Learning. V [Azure Portal](https://portal.azure.com)vyberte svůj pracovní prostor a pak vyberte **metriky**:

[![Snímek obrazovky znázorňující ukázkovou metriku pro pracovní prostor](media/concept-enterprise-security/workspace-metrics.png)](media/concept-enterprise-security/workspace-metrics-expanded.png#lightbox)

Metriky obsahují informace o spuštění, nasazení a registracích.

Další informace najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md).

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

### <a name="vulnerability-scanning"></a>Kontrola ohrožení zabezpečení

Azure Security Center zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Pro Azure Machine Learning byste měli povolit kontrolu Azure Container Registry prostředků a prostředků služby Azure Kubernetes. Přečtěte si téma [Azure Container Registry prohledávání imagí pomocí Security Center](../security-center/defender-for-container-registries-introduction.md) a [integrace služby Azure Kubernetes Services s Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Auditování a správa dodržování předpisů

[Azure Policy](../governance/policy/index.yml) je nástroj zásad správného řízení, který vám umožní zajistit, aby prostředky Azure vyhovovaly vašim zásadám. Pomocí Azure Machine Learning můžete přiřadit následující zásady:

* **Klíč spravovaný zákazníkem**: audit nebo vymáhání, jestli musí pracovní prostory používat klíč spravovaný zákazníkem.
* **Privátní odkaz**: Audituje, jestli pracovní prostory používají privátní koncový bod ke komunikaci s virtuální sítí.

Další informace o Azure Policy najdete v dokumentaci k [Azure Policy](../governance/policy/overview.md).

Další informace o zásadách specifických pro Azure Machine Learning najdete v tématu [auditování a Správa dodržování předpisů pomocí Azure Policy](how-to-integrate-azure-policy.md).

## <a name="resource-locks"></a>Zámky prostředků

[!INCLUDE [resource locks](../../includes/machine-learning-resource-lock.md)]

## <a name="next-steps"></a>Další kroky

* [Zabezpečení Azure Machine Learning webové služby pomocí protokolu TLS](how-to-secure-web-service.md)
* [Využití modelu Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md)
* [Použití Azure Machine Learning s Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Použití Azure Machine Learning s využitím Azure Virtual Network](how-to-network-security-overview.md)
* [Šifrování dat v klidovém umístění a při přenosu](concept-data-encryption.md)
* [Sestavení rozhraní API pro doporučení v reálném čase v Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
