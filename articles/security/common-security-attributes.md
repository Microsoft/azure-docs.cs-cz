---
title: Atributy zabezpečení pro služby Azure
description: Kontrolní seznam společné atributy zabezpečení za vaše rozhodnutí vyzkoušet Azure Service Fabric
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 460d8756c437a1212aef054cf069be2bccac8c8a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001363"
---
# <a name="security-attributes-for-azure-services"></a>Atributy zabezpečení pro služby Azure

Tento článek shromažďuje společné atributy zabezpečení pro vybrané služby Azure. 

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="azure-api-managementapi-managementapi-management-security-attributesmd"></a>[Azure API Management](../api-management/api-management-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano (pouze šifrování na straně služby) | Jsou zašifrované citlivá data, jako jsou certifikáty, klíče a hodnoty s názvem tajný klíč spravovaný službou, za klíčů instance služby. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | [Express Route](../expressroute/index.yml) a zajišťuje šifrování pro virtuální síť [sítě Azure](../virtual-network/index.yml). |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne | Všechny šifrovací klíče jsou za instanci služby a spravované služby. |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | Volání rovina správy se provádějí přes [Azure Resource Manageru](../azure-resource-manager/index.yml) přes protokol TLS. Vyžaduje se platný webový token JSON (JWT).  Volání roviny dat je možné svázat s TLS a jeden z podporovaných ověřovací mechanismy, které (např. klientský certifikát nebo tokenů JWT).
 |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Vkládání podpory virtuálních sítí| Ano | |
| Izolace sítě a podporu funkce brány firewall| Ano | Použití skupin zabezpečení sítě (NSG) a Azure Application Gateway (nebo jiné softwarové zařízení) v uvedeném pořadí. |
| Vynucené tunelování podpory| Ano | Sítě Azure poskytují vynucené tunelování. |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | |
| Autorizace| Ano | |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | [Protokoly aktivit Azure Monitor](../azure-monitor/platform/activity-logs-overview.md) |
| Protokolování roviny dat a auditu| Ano | [Diagnostické protokoly Azure monitoru](../azure-monitor/platform/diagnostic-logs-overview.md) a (volitelně) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Použití [Azure API Management DevOps Resource Kit](https://aka.ms/apimdevops) |


## <a name="azure-app-serviceapp-serviceapp-service-security-attributesmd"></a>[Azure App Service](../app-service/app-service-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Obsah souboru webu je uložený ve službě Azure Storage automaticky šifruje obsah v úložišti. Zobrazit [šifrování služby Azure Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md).<br><br>Tajné kódy zákazníka se šifrují při nečinnosti. Tajné klíče jsou šifrují při nečinnosti při uložení konfigurační databáze služby App Service.<br><br>Místně připojených disků můžete volitelně použít jako dočasné úložiště službou websites (D:\local a % TMP %). Místně připojené disky nejsou šifrovány v klidovém stavu. |
| Šifrování během přenosu (například šifrování ExpressRoute ve virtuální síti a šifrováním virtuálními sítěmi)| Ano | Zákazníci můžou nakonfigurovat webových stránek a vyžadovat a používat protokol HTTPS pro příchozí provoz. Najdete v blogovém příspěvku [jak Azure App Service pouze HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Zákazníci si mohou vybrat ukládání tajných klíčů aplikací ve službě Key Vault a načítat je za běhu. Zobrazit [pro App Service a Azure Functions (preview) odkazuje na použití služby Key Vault](../app-service/app-service-key-vault-references.md).|
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | Dojde k volání správy konfigurace služby App Service prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml) volání přes protokol HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | V současnosti ve verzi preview pro službu App Service. Zobrazit [omezení přístupu službě Azure App Service](../app-service/app-service-ip-restrictions.md). |
| Vkládání podpory virtuálních sítí| Ano | App Service Environment jsou privátní implementace služby App Service je vyhrazený pro jediného zákazníka vloženy do virtuální sítě zákazníka. Zobrazit [Úvod do služby App Service Environment](../app-service/environment/intro.md). |
| Izolace sítě a Firewalling podpory| Ano | Za veřejné více tenanty služby App Service zákazníci můžou nakonfigurovat seznamy ACL (omezení IP adres) zamezit povoleného příchozího provozu sítě.  Zobrazit [omezení přístupu službě Azure App Service](../app-service/app-service-ip-restrictions.md).  Služby App Service Environment se nasazuje přímo do virtuálních sítí a proto ji můžete zabezpečit pomocí skupin zabezpečení sítě. |
| Vynucené tunelování podpory| Ano | Služby App Service Environment je možné nasadit do virtuální sítě zákazníka, kde je nakonfigurované vynucené tunelování. Pokud si zákazníci musí postupovat podle pokynů v [konfigurace služby App Service Environment pomocí vynuceného tunelování](../app-service/environment/forced-tunnel-support.md). |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | App Service integruje se službou Application Insights pro jazyky, které podporují Application Insights (úplné rozhraní .NET Framework, .NET Core, Java a Node.JS).  Zobrazit [výkonu monitorování Azure App Service](../azure-monitor/app/azure-web-apps.md). Metriky aplikací služby App Service také odešle do služby Azure Monitor. Zobrazit [monitorování aplikací ve službě Azure App Service](../app-service/web-sites-monitor.md). |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Zákazníci mohou vytvářet aplikace ve službě App Service, které automaticky integrují s [Azure Active Directory (Azure AD)](../active-directory/index.md) i jinými poskytovateli identity kompatibilní OAuth; viz [ověřování a autorizace Azure App Service](../app-service/overview-authentication-authorization.md). Pro správu přístupu pro prostředky App Service veškerý přístup je řízen kombinaci instanční objekt Azure AD, ověření a role Azure RBAC Resource Manageru. |
| Autorizace| Ano | Pro správu přístupu pro prostředky App Service veškerý přístup je řízen kombinaci instanční objekt Azure AD, ověření a role Azure RBAC Resource Manageru.  |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Všechny operace správy prováděné s objekty služby App Service dojde k prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml). Historické protokoly tyto operace jsou k dispozici na portálu a prostřednictvím rozhraní příkazového řádku; Zobrazit [operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md#microsoftweb) a [az monitorování – protokol aktivit](/cli/azure/monitor/activity-log). |
| Protokolování roviny dat a auditu | Ne | Rovina dat pro službu App Service je vzdálené sdílené složce obsahující webovou stránku s nasazené zákazníka obsahu.  Neexistuje žádný auditování vzdálené sdílené složce. |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Pro operace správy stav konfigurace služby App Service je možné exportovat jako šablonu Azure Resource Manageru a systémovou správou verzí v čase. Pro operace modulu runtime zákazníci mohli pro více různé verze živé aplikace pomocí funkce sloty nasazení služby App Service. | 


## <a name="azure-resource-managerazure-resource-managerazure-resource-manager-security-attributesmd"></a>[Azure Resource Manager](../azure-resource-manager/azure-resource-manager-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano |  |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | HTTPS/TLS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| neuvedeno | Azure Resource Manager ukládá žádný obsah zákazníka, jenom data ovládacího prvku. |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | |
| Šifrované volání rozhraní API| Ano | |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Vkládání podpory virtuálních sítí| Ano | |
| Izolace sítě a podporu funkce brány firewall| Ne |  |
| Vynucené tunelování podpory| Ne |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ne | |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | [Azure Active Directory](/azure/active-directory) založené.|
| Autorizace| Ano | |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Všechny operace (PUT, POST, DELETE) prováděné s vašimi prostředky; zápisu zpřístupňují protokoly aktivit Zobrazit [zobrazení protokolů aktivit pro auditování akcí u prostředků](../azure-resource-manager/resource-group-audit.md). |
| Protokolování roviny dat a auditu| neuvedeno | |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano |  |


## <a name="azure-backupbackupbackup-security-attributesmd"></a>[Azure Backup](../backup/backup-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Pomocí šifrování služby storage pro účty úložiště. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ne | Pomocí protokolu HTTPS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ne |  |
| Šifrované volání rozhraní API| Ano |  |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne |  |
| Vkládání podpory virtuálních sítí| Ne |  |
| Izolace sítě a podporu funkce brány firewall| Ano | Vynucené tunelování se podporuje pro zálohování virtuálních počítačů. Vynucené tunelování se nepodporuje pro pracovní postupy spouštěné ve virtuálních počítačích. |
| Vynucené tunelování podpory| Ne |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Log Analytics je podporované prostřednictvím diagnostické protokoly. Naleznete v tématu monitorování Azure Backup chráněných pracovních vytížení používat službu Log Analytics (https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) Další informace. |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Autorizace| Ano | Zákazník byl vytvořen a předdefinované role RBAC se používají. Zobrazit Use Role-Based řízení přístupu ke správě Azure Backup bodů obnovení (/ azure / / backup-rbac-rs – trezoru služby backup) pro další informace. |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Všechny akce aktivuje zákazníků na webu Azure Portal se zaznamenávají do protokolů aktivit. |
| Protokolování roviny dat a auditu| Ne | Rovina dat služby Azure Backup je nedostupné přímo.  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano|  |


## <a name="azure-key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Azure Key Vault](../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Všechny objekty jsou šifrovaná. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Veškerá komunikace probíhá přes šifrované volání rozhraní API |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Zákazník řídí všechny klíče ve své službě Key Vault. Pokud jsou zadány klíče modulu (HSM) zajišťuje hardwarové zabezpečení, HSM FIPS úroveň 2 chrání klíče, certifikát nebo tajný klíč. |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano | Pomocí protokolu HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Pomocí koncových bodů služby Virtual Network (VNet). |
| Vkládání podpory virtuálních sítí| Ne |  |
| Izolace sítě a podporu funkce brány firewall| Ano | Pomocí pravidel brány firewall virtuální sítě. |
| Vynucené tunelování podpory| Ne |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Používat službu Log Analytics. |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Autorizace| Ano | Pomocí zásady přístupu trezoru klíčů. |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení a rovinou protokolování a auditování| Ano | Používat službu Log Analytics. |
| Protokolování roviny dat a auditu| Ano | Používat službu Log Analytics. |

### <a name="access-controls"></a>Ovládací prvky přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení přístupu roviny řízení a správy | Ano | Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Řízení přístupu roviny dat (na všech úrovních služby) | Ano | Zásady přístupu trezoru klíčů |

## <a name="azure-service-bus-messagingservice-bus-messagingservice-bus-messaging-security-attributesmd"></a>[Azure Service Bus pro zasílání zpráv](../service-bus-messaging/service-bus-messaging-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>|  Ano pro serverové šifrování neaktivních ve výchozím nastavení. | Spravovaná zákazníkem klíče a BYOK se zatím nepodporují. Šifrování na straně klienta je odpovědností klienta |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Podporuje standardní mechanismus HTTPS/TLS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne |   |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | Volání rozhraní API se provádějí přes [Azure Resource Manageru](../azure-resource-manager/index.yml) a HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano (pouze úroveň Premium) | Koncové body služby virtuální sítě jsou podporovány pro [úroveň služby Service Bus Premium](../service-bus-messaging/service-bus-premium-messaging.md) pouze. |
| Vkládání podpory virtuálních sítí| Ne | |
| Izolace sítě a podporu funkce brány firewall| Ano (pouze úroveň Premium) |  |
| Vynucené tunelování podpory| Ne |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Podporováno prostřednictvím [Azure Monitor a výstrahy](../service-bus-messaging/service-bus-metrics-azure-monitor.md). |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Spravovat prostřednictvím [Azure Active Directory identitu spravované služby](../service-bus-messaging/service-bus-managed-service-identity.md); viz [ověřování a autorizace Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md).|
| Autorizace| Ano | Podporuje ověřování prostřednictvím [RBAC](../service-bus-messaging/service-bus-role-based-access-control.md) (Preview) a tokenu SAS, viz [ověřování a autorizace Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md). |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Protokoly operací jsou k dispozici. Zobrazit [diagnostické protokoly služby Service Bus](../service-bus-messaging/service-bus-diagnostic-logs.md).  |
| Protokolování roviny dat a auditu| Ne |  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Podporuje správu verzí zprostředkovatele prostředků prostřednictvím [API Azure Resource Manageru](/rest/api/resources/).|


## <a name="azure-service-bus-relayservice-bus-relayservice-bus-relay-security-attributesmd"></a>[Azure Service Bus Relay](../service-bus-relay/service-bus-relay-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>|  neuvedeno | Předávání přes webové sokety je a není zachována data. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Služba vyžaduje protokol TLS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ne | Používá pouze certifikáty Microsoft TLS.  |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne |  |
| Izolace sítě a podporu funkce brány firewall| Ne |  |
| Vynucené tunelování podpory| neuvedeno | Relay je tunelu TLS  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Via SAS. |
| Autorizace|  Ano | Via SAS. |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml). |
| Protokolování roviny dat a auditu| Ano | Připojení úspěch nebo selhání a chyby a protokoluje.  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml).|


## <a name="azure-service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Azure Service Fabric](../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Zákazník je vlastníkem služby clusteru a škálovací sady virtuálních počítačů (VM), které clusteru je postavená na. Azure disk encryption je možné povolit na škálovací sadu virtuálních počítačů. |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano |  |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Zákazník je vlastníkem služby clusteru a škálovací sady virtuálních počítačů (VM), které clusteru je postavená na. Azure disk encryption je možné povolit na škálovací sadu virtuálních počítačů. |
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano | Volání rozhraní API Service Fabric provádí prostřednictvím Azure Resource Manageru. Vyžaduje se platný webový token JSON (JWT). |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Vkládání podpory virtuálních sítí| Ano |  |
| Izolace sítě a podporu funkce brány firewall| Ano | Použití skupin zabezpečení sítě (NSG). |
| Vynucené tunelování podpory| Ano | Sítě Azure poskytují vynucené tunelování. |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Pomocí Azure monitoring podporu a podporu třetí strany. |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Ověřování je prostřednictvím Azure Active Directory. |
| Autorizace| Ano | Správa identit a přístupu (IAM) pro volání prostřednictvím SFRP. Volání přímo na koncový bod clusteru podporuje dvě role: Uživatele a správce. Zákazník může mapování rozhraní API na kteroukoli z těchto rolí. |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Spustit přes procesy pro auditování a jejich schvalování, všechny operace s roviny řízení. |
| Protokolování roviny dat a auditu| neuvedeno | Označuje zákazníka vlastnícího clusteru.  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Konfigurace služby se systémovou správou verzí a nasazených pomocí nasazení Azure. Kód (aplikace a modul runtime) je označené verzí využitím sestavování služby Azure.
 |

## <a name="azure-sql-databasesql-databasesql-database-security-attributesmd"></a>[Azure SQL Database](../sql-database/sql-database-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano | Označuje jako "šifrování v ovládáním", jak je popsáno v článku [s funkcí Always Encrypted](../sql-database/sql-database-always-encrypted.md). Šifrování na straně služby používá [transparentní šifrování dat](../sql-database/transparent-data-encryption-azure-sql.md) (TDE).|
| Šifrování během přenosu:<ul><li>Šifrování ExpressRoute</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Pomocí protokolu HTTPS. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Nabízíme zpracování klíče spravované službou i spravované zákazníkem (druhá možnost prostřednictvím [Azure Key Vault](../key-vault/index.yml). |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | Prostřednictvím [s funkcí Always Encrypted](../sql-database/sql-database-always-encrypted.md). |
| Šifrované volání rozhraní API| Ano | Pomocí protokolu HTTPS a SSL. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Platí pro [izolované databáze](../sql-database/sql-database-single-index.yml) pouze. |
| Vkládání podpory virtuálních sítí| Ano | Platí pro [spravovanou instanci](../sql-database/sql-database-managed-instance.md) pouze. |
| Izolace sítě a podporu funkce brány firewall| Ano | Brány firewall na obě databáze a serveru úroveň; izolace pro sítě [spravovanou instanci](../sql-database/sql-database-managed-instance.md) pouze |
| Vynucené tunelování podpory| Ano | [spravovaná instance](../sql-database/sql-database-managed-instance.md) prostřednictvím [Azure ExpressRoute](../expressroute/index.yml) sítě VPN |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Řešení SIEM třetích stran od Impervy (SecureSphere) je také podporována, prostřednictvím [Azure Event Hubs](../event-hubs/index.yml) integraci přes [auditování SQL](../sql-database/sql-database-auditing.md). |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Azure Active Directory. |
| Autorizace| Ano |  |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Ano, pro pouze některé události. |
| Protokolování roviny dat a auditu | Ano | Prostřednictvím [auditování SQL](../sql-database/sql-database-auditing.md). |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ne  | | 

### <a name="additional-security-attributes-for-sql-database"></a>Atributy dodatečné zabezpečení pro službu SQL Database

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Preventivní: posouzení ohrožení zabezpečení | Ano | Zobrazit [posouzení ohrožení zabezpečení SQL služba pomáhá identifikovat ohrožení zabezpečení databáze](../sql-database/sql-vulnerability-assessment.md). |
| Preventivní: data zjišťování a klasifikace  | Ano | Zobrazit [Azure SQL Database a SQL Data Warehouse zjišťování a klasifikace dat](../sql-database/sql-database-data-discovery-and-classification.md). |
| Zjišťování: detekce hrozeb | Ano | Zobrazit [Rozšířená ochrana před internetovými útoky pro Azure SQL Database](../sql-database/sql-database-threat-detection-overview.md). |

## <a name="azure-storagestoragecommonstorage-security-attributesmd"></a>[Azure Storage](../storage/common/storage-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu:<ul><li>Šifrování na straně serveru</li><li>Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem</li><li>Další funkce šifrování (například klientů, funkce always encrypted, atd.)</ul>| Ano |  |
| Šifrování během přenosu:<ul><li>Express route šifrování</li><li>Ve virtuální síti šifrování</li><li>Šifrování virtuálními sítěmi</ul>| Ano | Podpora standardních mechanismů protokolu HTTPS/TLS.  Uživatelé můžou taky k šifrování dat před odesláním do služby. |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Zobrazit [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](../storage/common/storage-service-encryption-customer-managed-keys.md).|
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno |  |
| Šifrované volání rozhraní API| Ano |  |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Vkládání podpory virtuálních sítí| neuvedeno |  |
| Izolace sítě a podporu funkce brány firewall| Ano | |
| Vynucené tunelování podpory| neuvedeno |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | Metriky Azure Monitor dostupné nyní, protokoly počáteční verzi preview |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Azure Active Directory, sdílený klíč, sdílený přístupový token. |
| Autorizace| Ano | Podporu ověřování přes RBAC, seznamech ACL POSIX a tokeny SAS |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování | Ano | Protokol aktivit v Azure Resource Manageru |
| Protokolování roviny dat a auditu| Ano | Diagnostické protokoly služby a Azure Monitor protokolování počáteční verzi preview  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Podpora správy verzí zprostředkovatele prostředků prostřednictvím rozhraní API Azure Resource Manageru |