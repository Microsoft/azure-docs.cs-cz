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
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992025"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Podnikové zabezpečení a zásady správného řízení pro Azure Machine Learning

V tomto článku se dozvíte o funkcích zabezpečení a zásad správného řízení dostupných pro Azure Machine Learning. Tyto funkce jsou užitečné pro správce, DevOps a MLOps, kteří chtějí vytvořit zabezpečenou konfiguraci kompatibilní se zásadami vaší společnosti. Díky Azure Machine Learning a platformě Azure můžete:

* Omezení přístupu k prostředkům a operacím pomocí uživatelského účtu nebo skupin
* Omezení příchozí a odchozí síťové komunikace
* Šifrování dat při přenosu a v klidovém provozu
* Vyhledat ohrožení zabezpečení
* Použít a auditovat zásady konfigurace

## <a name="restrict-access-to-resources-and-operations"></a>Omezení přístupu k prostředkům a operacím

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) je poskytovatel služby identit pro Azure Machine Learning. Umožňuje vytvářet a spravovat objekty zabezpečení (uživatele, skupinu, instanční objekt a spravovanou identitu), které se používají k _ověřování_ prostředků Azure. Multi-Factor Authentication se podporuje, pokud je služba Azure AD nakonfigurovaná tak, aby ji používala.

Tady je proces ověřování pro Azure Machine Learning používání služby Multi-Factor Authentication ve službě Azure AD:

1. Klient se přihlásí do služby Azure AD a získá token Azure Resource Manager.
1. Klient prezentuje token pro Azure Resource Manager a všem Azure Machine Learning.
1. Azure Machine Learning poskytuje Machine Learning token služby pro výpočetní cíl pro uživatele (například Azure Machine Learning výpočetní cluster). Tento token používá cílový výpočetní cíl pro zpětné volání do služby Machine Learning po dokončení spuštění. Rozsah je omezen na pracovní prostor.

[![Ověřování v Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Každý pracovní prostor má přidruženou [spravovanou identitu](../active-directory/managed-identities-azure-resources/overview.md) přiřazenou systémem, která má stejný název jako pracovní prostor. Tato spravovaná identita se používá k zabezpečenému přístupu k prostředkům používaným pracovním prostorem. Má následující oprávnění Azure RBAC pro připojené prostředky:

| Prostředek | Oprávnění |
| ----- | ----- |
| Pracovní prostor | Přispěvatel |
| Účet úložiště | Přispěvatel dat v objektech blob služby Storage |
| Trezor klíčů | Přístup ke všem klíčům, tajným klíčům, certifikátům |
| Azure Container Registry | Přispěvatel |
| Skupina prostředků, která obsahuje pracovní prostor | Přispěvatel |
| Skupina prostředků, která obsahuje Trezor klíčů (Pokud se liší od tu, která obsahuje pracovní prostor) | Přispěvatel |

Nedoporučujeme, aby správci odvolali přístup ke spravované identitě k prostředkům uvedeným v předchozí tabulce. Přístup můžete obnovit pomocí [operace opětovné synchronizace klíčů](how-to-change-storage-access-key.md).

Azure Machine Learning také vytvoří další aplikaci (název začíná na `aml-` nebo `Microsoft-AzureML-Support-App-` ) s přístupem na úrovni přispěvatele v rámci vašeho předplatného pro každou oblast pracovního prostoru. Například pokud máte jeden pracovní prostor v Východní USA a druhý v Severní Evropa ve stejném předplatném, uvidíte dvě z těchto aplikací. Tyto aplikace umožňují Azure Machine Learning, které vám pomůžou se správou výpočetních prostředků.

Můžete taky nakonfigurovat vlastní spravované identity pro použití s Azure Virtual Machines a Azure Machine Learning výpočetním clusterem. Pomocí virtuálního počítače se spravovaná identita dá použít pro přístup k vašemu pracovnímu prostoru ze sady SDK namísto účtu Azure AD jednotlivých uživatelů. Pomocí výpočetního clusteru se spravovaná identita používá pro přístup k prostředkům, jako je zabezpečené úložiště dat, ke kterému uživatel spouštějící školicí úlohu nemusí mít přístup. Další informace najdete v tématu [ověřování pro Azure Machine Learning pracovní prostor](how-to-setup-authentication.md).

> [!TIP]
> Existují výjimky pro použití Azure AD a Azure RBAC v rámci Azure Machine Learning:
> * Volitelně můžete povolit přístup přes __SSH__ k výpočetním prostředkům, jako je Azure Machine Learning výpočetní instance a výpočetní cluster. Přístup přes SSH je založený na páru veřejného a privátního klíče, nikoli v Azure AD. Přístup SSH se neřídí službou Azure RBAC.
> * Můžete provést ověření pro modely nasazené jako webové služby (koncové body odvození) pomocí ověřování na základě __klíče__ nebo __tokenu__. Klíče jsou statické řetězce, zatímco tokeny se načítají pomocí objektu zabezpečení Azure AD. Další informace najdete v tématu [Konfigurace ověřování pro modely nasazené jako webové služby](how-to-authenticate-web-service.md).

Další informace najdete v následujících článcích:
* [Ověřování pro Azure Machine Learning pracovní prostor](how-to-setup-authentication.md)
* [Správa přístupu k Azure Machine Learning](how-to-assign-roles.md)
* [Připojení ke službám úložiště](how-to-access-data.md)
* [Při výuce použít Azure Key Vault pro tajné klíče](how-to-use-secrets-in-runs.md)
* [Použití spravované identity Azure AD s Azure Machine Learning](how-to-use-managed-identities.md)
* [Použití spravované identity Azure AD s vaší webovou službou](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Zabezpečení a izolace sítě

K omezení síťového přístupu k prostředkům Azure Machine Learning můžete použít [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md). Virtuální sítě umožňují vytvářet síťová prostředí, která jsou částečně nebo plně izolovaná od veřejného Internetu. Tím se sníží plocha pro útoky na vaše řešení a také šance na exfiltrace dat.

Bránu virtuální privátní sítě (VPN) můžete použít k propojení jednotlivých klientů nebo vaší vlastní sítě k virtuální síti.

Pracovní prostor Azure Machine Learning může pomocí [privátního propojení Azure](../private-link/private-link-overview.md) vytvořit privátní koncový bod za virtuální sítí. To poskytuje sadu privátních IP adres, které se dají použít pro přístup k pracovnímu prostoru z virtuální sítě. Některé ze služeb, které Azure Machine Learning spoléhají, můžou také používat privátní propojení Azure, ale některé jsou závislé na skupinách zabezpečení sítě nebo na uživatelem definovaném směrování.

Další informace najdete v následujících dokumentech:

* [Přehled izolace a ochrany osobních údajů virtuální sítě](how-to-network-security-overview.md)
* [Zabezpečené prostředky pracovního prostoru](how-to-secure-workspace-vnet.md)
* [Zabezpečené prostředí pro trénování](how-to-secure-training-vnet.md)
* [Zabezpečené odvozené prostředí](how-to-secure-inferencing-vnet.md)
* [Použití studia v zabezpečené virtuální síti](how-to-enable-studio-virtual-network.md)
* [Použití vlastní služby DNS](how-to-custom-dns.md)
* [Konfigurace brány firewall](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Šifrování dat

Azure Machine Learning využívá řadu výpočetních prostředků a úložišť dat na platformě Azure. Další informace o tom, jak každý z těchto způsobů podporuje šifrování dat v klidovém umístění a při přenosu, najdete v tématu [šifrování dat pomocí Azure Machine Learning](concept-data-encryption.md).

Když nasazujete modely jako webové služby, můžete povolit TLS (Transport Layer Security) k šifrování dat při přenosu. Další informace najdete v tématu [Konfigurace zabezpečené webové služby](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Kontrola ohrožení zabezpečení

[Azure Security Center](../security-center/security-center-introduction.md) zajišťuje jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami. Pro Azure Machine Learning byste měli povolit kontrolu [Azure Container Registry](../container-registry/container-registry-intro.md) prostředků a prostředků služby Azure Kubernetes. Další informace najdete v tématu [Azure Container Registry skenování imagí Security Center](../security-center/defender-for-container-registries-introduction.md) a [integrace služby Azure Kubernetes Services s Security Center](../security-center/defender-for-kubernetes-introduction.md).

## <a name="audit-and-manage-compliance"></a>Auditování a správa dodržování předpisů

[Azure Policy](../governance/policy/index.yml) je nástroj zásad správného řízení, který vám umožní zajistit, aby prostředky Azure vyhovovaly vašim zásadám. Zásady můžete nastavit tak, aby povolovaly nebo vynutily konkrétní konfigurace, například to, jestli váš pracovní prostor Azure Machine Learning používá privátní koncový bod. Další informace o Azure Policy najdete v dokumentaci k [Azure Policy](../governance/policy/overview.md). Další informace o zásadách specifických pro Azure Machine Learning najdete v tématu [auditování a Správa dodržování předpisů pomocí Azure Policy](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Další kroky

* [Zabezpečení Azure Machine Learning webové služby pomocí protokolu TLS](how-to-secure-web-service.md)
* [Využití modelu Machine Learning nasazeného jako webové služby](how-to-consume-web-service.md)
* [Použití Azure Machine Learning s Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Použití Azure Machine Learning s využitím Azure Virtual Network](how-to-network-security-overview.md)
* [Šifrování dat v klidovém umístění a při přenosu](concept-data-encryption.md)
* [Sestavení rozhraní API pro doporučení v reálném čase v Azure](/azure/architecture/reference-architectures/ai/real-time-recommendation)
