---
title: Atributy zabezpečení pro služby Azure
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení služeb Azure
services: security
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: security
ms.subservice: security-fundamentals
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: mbaldwin
ms.openlocfilehash: be39a623dc5dac09dcfe47de67e025191b5c7de3
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727497"
---
# <a name="security-attributes-for-azure-services"></a>Atributy zabezpečení pro služby Azure

Tento článek shromažďuje atributy zabezpečení pro vybrané služby Azure. Atribut zabezpečení je kvalita nebo funkce služby Azure. Přispívá k schopnostem služby zabránit, zjišťovat a reagovat na ohrožení zabezpečení.

Atributy zabezpečení jsou kategorizované jako:
* Preventivní
* Segmentace sítě
* Detekce
* Podpora správy identit a přístupu
* Záznam pro audit
* Řízení přístupu (Pokud se používá)
* Správa konfigurace (Pokud se používá)

V každé kategorii zobrazujeme "Ano" nebo "ne" k označení toho, zda je atribut použit. U některých služeb se pro nepoužitý atribut zobrazuje "N/A". Můžete také zadat poznámku nebo odkaz na Další informace o atributu.

## <a name="api-managementazureapi-managementapi-management-security-attributes"></a>[API Management](/azure/api-management/api-management-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Citlivá data, jako jsou certifikáty, klíče a hodnoty tajného klíče, jsou šifrovány pomocí klíčů pro instanci služby spravované službou. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | Připojení k [rychlé trase](/azure/expressroute/index) a síti VNet zajišťuje [síť Azure](/azure/virtual-network/index). |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne | Všechny šifrovací klíče jsou na instanci služby a jsou spravované službou. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Volání roviny správy se provádí prostřednictvím [Azure Resource Manager](/azure/azure-resource-manager/index) přes TLS. Vyžaduje se platný webový token JSON (JWT).  Volání roviny dat je možné zabezpečit pomocí protokolu TLS a jednoho z podporovaných mechanismů ověřování (například klientský certifikát nebo token JWT).
 |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Podpora vkládání virtuální sítě| Ano | |
| Izolace sítě a podpora brány firewall| Ano | Používání skupin zabezpečení sítě (NSG) a Azure Application Gateway (nebo jiného softwarového zařízení). |
| Podpora vynuceného tunelování| Ano | Sítě Azure poskytují vynucené tunelové propojení. |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | |
| Authorization| Ano | |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | [Protokoly aktivit Azure Monitor](/azure/azure-monitor/platform/activity-logs-overview) |
| Protokolování a audit roviny dat| Ano | [Azure monitor diagnostické protokoly](/azure/azure-monitor/platform/diagnostic-logs-overview) a (volitelně) [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview).|

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Používání [sady Resource Kit pro Azure API Management DevOps](https://aka.ms/apimdevops) |

### <a name="vulnerability-scans-false-positives"></a>Ohrožení zabezpečení kontroluje falešně pozitivní výsledky.

Tato část se týká běžných chyb zabezpečení, které neovlivňují Azure API Management.

| Chyba zabezpečení               | Popis                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | Ticketbleed je zranitelnost při implementaci rozšíření TLS SessionTicket, které najdete v některých produktech F5. Umožňuje únik ("vykrvení") až 31 bajtů dat z neinicializované paměti. To je způsobeno tím, že přeplňování zásobníku TLS předává ID relace předané z klienta s daty, aby byla 32 bitů dlouhá. |


## <a name="app-serviceazureapp-serviceapp-service-security-attributes"></a>[App Service](/azure/app-service/app-service-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Obsah souboru webu je uložen v Azure Storage, který automaticky zašifruje obsah v klidovém formátu. V části [Azure Storage šifrování pro](/azure/storage/common/storage-service-encryption)neaktivní neaktivní data.<br><br>Tajné kódy poskytnuté zákazníkem jsou zašifrované v klidovém stavu. Tajné klíče jsou v klidovém stavu zašifrované a ukládají se do konfiguračních databází App Service.<br><br>Místně připojené disky je možné volitelně použít jako dočasné úložiště na webech (D:\Local a% TMP%). Místně připojené disky nejsou v klidovém stavu šifrované. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | Zákazníci mohou nakonfigurovat webové servery tak, aby vyžadovaly a používaly protokol HTTPS pro příchozí provoz. Informace o tom, [jak nastavit jenom Azure App Service https](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/), najdete v blogovém příspěvku. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ano | Zákazníci se můžou rozhodnout ukládat tajná klíč aplikace do Key Vault a načíst je za běhu. Viz [použití Key Vault odkazů pro App Service a Azure Functions (Preview)](/azure/app-service/app-service-key-vault-references).|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Volání správy ke konfiguraci App Service nastávají prostřednictvím volání [Azure Resource Manager](/azure/azure-resource-manager/index) prostřednictvím protokolu HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Aktuálně k dispozici ve verzi Preview pro App Service. Viz [omezení přístupu Azure App Service](/azure/app-service/app-service-ip-restrictions). |
| Podpora vkládání virtuální sítě| Ano | App Service prostředí jsou soukromá implementace App Service vyhrazená pro jednoho zákazníka, který je vložený do virtuální sítě zákazníka. Viz [Úvod do prostředí App Service](/azure/app-service/environment/intro). |
| Izolace sítě a podpora brány firewall| Ano | Pro účely App Service veřejné varianty pro více tenantů můžou zákazníci nakonfigurovat seznamy ACL sítě (omezení IP adres), aby se mohl zamknout povolený příchozí provoz.  Viz [omezení přístupu Azure App Service](/azure/app-service/app-service-ip-restrictions).  Prostředí App Service se nasazují přímo do virtuálních sítí, takže je můžete zabezpečit pomocí skupin zabezpečení sítě. |
| Podpora vynuceného tunelování| Ano | Prostředí App Service můžete nasadit do virtuální sítě zákazníka, ve které je nakonfigurované vynucené tunelování. Zákazníci musí postupovat podle pokynů v části [konfigurace App Service Environment s vynuceným tunelovým propojením](/azure/app-service/environment/forced-tunnel-support). |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | App Service se integruje s Application Insights pro jazyky, které podporují Application Insights (kompletní .NET Framework, .NET Core, Java a Node. JS).  Viz [monitorování výkonu Azure App Service](/azure/azure-monitor/app/azure-web-apps). App Service také odesílá metriky aplikace do Azure Monitor. Viz [monitorování aplikací v Azure App Service](/azure/app-service/web-sites-monitor). |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Zákazníci můžou vytvářet aplikace na App Service, které se automaticky integrují s [Azure Active Directory (Azure AD)](/azure/active-directory/index) , i s dalšími kompatibilními zprostředkovateli identity OAuth. viz [ověřování a autorizace v Azure App Service](/azure/app-service/overview-authentication-authorization). Pro přístup pro správu k prostředkům App Service se veškerý přístup řídí kombinací ověřeného objektu zabezpečení Azure AD a Azure Resource Manager rolí RBAC. |
| Authorization| Ano | Pro přístup pro správu k prostředkům App Service se veškerý přístup řídí kombinací ověřeného objektu zabezpečení Azure AD a Azure Resource Manager rolí RBAC.  |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Všechny operace správy prováděné s App Service objekty nastávají přes [Azure Resource Manager](/azure/azure-resource-manager/index). Historické protokoly těchto operací jsou k dispozici na portálu i prostřednictvím rozhraní příkazového řádku. viz [Azure Resource Manager operace poskytovatele prostředků](/azure/role-based-access-control/resource-provider-operations#microsoftweb) a [AZ monitor Activity-log](/cli/azure/monitor/activity-log). |
| Protokolování a audit roviny dat | Ne | Rovina dat pro App Service je vzdálená sdílená složka, která obsahuje obsah webu nasazeného zákazníka.  Neexistuje žádné auditování sdílené složky vzdáleného souboru. |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | V případě operací správy lze stav konfigurace App Service exportovat jako šablonu Azure Resource Manager a v průběhu času. Pro běhové operace můžou zákazníci spravovat několik různých živých verzí aplikace pomocí funkce App Servicech slotů nasazení. | 



## <a name="azure-resource-managerazureazure-resource-managerazure-resource-manager-security-attributes"></a>[Azure Resource Manager](/azure/azure-resource-manager/azure-resource-manager-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | HTTPS/TLS. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Není k dispozici | Azure Resource Manager neukládá žádný zákaznický obsah, řídí se jenom data. |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | |
| Zašifrovaná volání rozhraní API| Ano | |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne | |
| Podpora vkládání virtuální sítě| Ano | |
| Izolace sítě a podpora brány firewall| Ne |  |
| Podpora vynuceného tunelování| Ne |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ne | |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Na základě [Azure Active Directory](/azure/active-directory) .|
| Authorization| Ano | |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Protokoly aktivit zpřístupňují všechny operace zápisu (PUT, POST, DELETE) provedené na vašich prostředcích. v tématu [zobrazení protokolů aktivit můžete auditovat akce u prostředků](/azure/azure-resource-manager/resource-group-audit). |
| Protokolování a audit roviny dat| Není k dispozici | |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  |


## <a name="azure-backupazurebackupbackup-security-attributes"></a>[Azure Backup](/azure/backup/backup-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování)| Ano | Používá se šifrování služby Storage pro účty úložiště. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ne | Pomocí protokolu HTTPS. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ne |  |
| Zašifrovaná volání rozhraní API| Ano |  |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne |  |
| Podpora vkládání virtuální sítě| Ne |  |
| Izolace sítě a podpora brány firewall| Ano | Pro zálohování virtuálního počítače se podporuje vynucené tunelování. Pro úlohy běžící uvnitř virtuálních počítačů se vynucené tunelování nepodporuje. |
| Podpora vynuceného tunelování| Ne |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Log Analytics se podporují prostřednictvím diagnostických protokolů. Další informace najdete v tématu [monitorování Azure Backup chráněných úlohami pomocí Log Analytics](https://azure.microsoft.com/blog/monitor-all-azure-backup-protected-workloads-using-log-analytics/) . |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |
| Authorization| Ano | Používají se vytvořené a předdefinované role RBAC. Další informace najdete v tématu [použití Access Control založeného na rolích ke správě Azure Backup bodů obnovení](/azure/backup/backup-rbac-rs-vault) . |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Všechny akce aktivované zákazníkem z Azure Portal jsou protokolovány do protokolů aktivit. |
| Protokolování a audit roviny dat| Ne | Azure Backup rovině dat nelze přímo získat.  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano|  |

## <a name="cosmos-dbazurecosmos-dbcosmos-db-security-attributes"></a>[Databáze Cosmos](/azure/cosmos-db/cosmos-db-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Všechny databáze Cosmos DB a zálohy jsou standardně šifrované. viz [šifrování dat v Azure Cosmos DB](/azure/cosmos-db/database-encryption-at-rest). Šifrování na straně serveru pomocí klíčů spravovaných zákazníkem se nepodporuje. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Všechna Azure Cosmos DBová data jsou při přenosu šifrovaná. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Ano | Pouze v rozhraních API tabulky Premium. Tato funkce nepodporuje všechna rozhraní API. Viz [Úvod do Azure Cosmos DB: Rozhraní API pro tabulky](/azure/cosmos-db/table-introduction). |
| Zašifrovaná volání rozhraní API| Ano | Všechna připojení k Azure Cosmos DB podporují protokol HTTPS. Azure Cosmos DB podporuje také připojení TLS 1,2, ale toto není ještě vynutilo. Pokud zákazníci na svém konci vypnou nižší úroveň TLS, můžou se připojit k Cosmos DB.  |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Ano | Pomocí koncového bodu služby virtuální sítě můžete nakonfigurovat účet Azure Cosmos DB, aby povoloval přístup jenom z konkrétní podsítě virtuální sítě (VNet). Můžete také kombinovat přístup k virtuální síti pomocí pravidel brány firewall.  Viz [Azure Cosmos DB přístupu z virtuálních sítí](/azure/cosmos-db/vnet-service-endpoint). |
| Izolace sítě a podpora brány firewall| Ano | Díky podpoře brány firewall můžete účet Azure Cosmos nakonfigurovat tak, aby povoloval přístup jenom ze schválené sady IP adres, rozsahu IP adres a/nebo cloudových služeb. Viz [Konfigurace brány firewall protokolu IP v Azure Cosmos DB](/azure/cosmos-db/how-to-configure-firewall).|
| Podpora vynuceného tunelování| Ano | Dá se nakonfigurovat na straně klienta na virtuální síti, kde jsou umístěné virtuální počítače.   |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Všechny požadavky, které se odesílají do Azure Cosmos DB, se zaznamenávají do protokolu. [Azure Monitoring](/azure/azure-monitor/overview), metriky Azure, protokolování Azure audit se podporují.  Můžete protokolovat informace, které odpovídají požadavkům na rovinu dat, statistiky dotazů za běhu, text dotazu a MongoDB požadavky. Můžete také nastavit výstrahy. |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ano na úrovni databázového účtu; na úrovni roviny dat Cosmos DB používá tokeny prostředků a přístup k klíčům. |
| Authorization| Ano | Podporováno v účtu Azure Cosmos s hlavními klíči (primárními a sekundárními) a tokeny prostředků. Přístup k datům můžete získat jen pro čtení a zápis nebo pro čtení pomocí hlavních klíčů. Tokeny prostředků umožňují omezený časový přístup k prostředkům, jako jsou dokumenty a kontejnery. |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Protokol aktivit Azure pro operace na úrovni účtu, jako jsou brány firewall, virtuální sítě, přístup k klíčům a IAM. |
| Protokolování a audit roviny dat | Ano | Protokolování monitorování diagnostiky pro operace na úrovni kontejneru, jako je vytvoření kontejneru, zajištění propustnosti, zásady indexování a operace CRUD v dokumentech. |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ne  | | 

### <a name="additional-security-attributes-for-cosmos-db"></a>Další atributy zabezpečení pro Cosmos DB

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Sdílení prostředků mezi zdroji (CORS) | Ano | Viz [Konfigurace sdílení prostředků mezi zdroji (CORS)](/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing). |


## <a name="event-hubsazureevent-hubsevent-hubs-security-attributes"></a>[Event Hubs](/azure/event-hubs/event-hubs-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) |  Ano | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano |  |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Ne | |
| Izolace sítě a podpora brány firewall| Ano |  |
| Podpora vynuceného tunelování| Ne |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | |
| Authorization|  Ano | |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat| Ano |   |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | |


## <a name="expressrouteazureexpressrouteexpressroute-security-attributes"></a>[ExpressRoute](/azure/expressroute/expressroute-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) |  Není k dispozici | ExpressRoute neukládá zákaznická data. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ne | |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Není k dispozici |  |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](/azure/azure-resource-manager/index) a HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici |  |
| Podpora vkládání virtuální sítě| Není k dispozici | |
| Izolace sítě a podpora brány firewall| Ano | Každý zákazník je obsažen ve své vlastní doméně směrování a je připojen k jeho vlastní virtuální síti. |
| Podpora vynuceného tunelování| Není k dispozici | Přes Border Gateway Protocol (BGP). |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Viz [ExpressRoute monitoring, metriky a výstrahy](/azure/expressroute/expressroute-monitoring-metrics-alerts).|

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |
| Authorization|  Ano |Účet služby pro bránu Microsoft (GWM) (Controller); Přístup JIT (just in time) pro vývoj a OP. |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky| 
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat| Ne |   |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Přes poskytovatele síťových prostředků (NRP). |


## <a name="key-vaultkey-vaultkey-vault-security-attributesmd"></a>[Key Vault](../../key-vault/key-vault-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Všechny objekty jsou zašifrovány. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | Veškerá komunikace probíhá prostřednictvím šifrovaných volání rozhraní API. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ano | Zákazník řídí všechny klíče v jejich Key Vault. Když jsou zadané klíče v modulu hardwarového zabezpečení (HSM), chrání klíč, certifikát nebo tajný kód úrovně FIPS úrovně 2. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici |  |
| Zašifrovaná volání rozhraní API| Ano | Pomocí protokolu HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Použití koncových bodů služby Virtual Network (VNet). |
| Podpora vkládání virtuální sítě| Ne |  |
| Izolace sítě a podpora brány firewall| Ano | Používají se pravidla brány firewall virtuální sítě. |
| Podpora vynuceného tunelování| Ne |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Použití Log Analytics. |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |
| Authorization| Ano | Používají se zásady přístupu Key Vault. |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Použití Log Analytics. |
| Protokolování a audit roviny dat| Ano | Použití Log Analytics. |

### <a name="access-controls"></a>Ovládací prvky přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvky pro řízení přístupu roviny řízení/správy | Ano | Řízení přístupu podle role (RBAC) služby Azure Resource Manager |
| Řízení přístupu roviny dat (na každé úrovni služby) | Ano | Zásada přístupu Key Vault |

## <a name="load-balancerload-balancerload-balancer-security-attributesmd"></a>[Load Balancer](../../load-balancer/load-balancer-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Není k dispozici | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Není k dispozici | |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Není k dispozici | |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](/azure/azure-resource-manager/index). |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici | |
| Podpora vkládání virtuální sítě| Není k dispozici | . |
| Izolace sítě a podpora brány firewall| Není k dispozici |  |
| Podpora vynuceného tunelování| Není k dispozici | |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | [Load Balancer veřejné základní](/azure/load-balancer/load-balancer-monitor-log)najdete v tématu protokoly Azure monitor. |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Není k dispozici |  |
| Authorization| Není k dispozici |  |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | [Load Balancer veřejné základní](/azure/load-balancer/load-balancer-monitor-log)najdete v tématu protokoly Azure monitor. |
| Protokolování a audit roviny dat | Není k dispozici |  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Není k dispozici |  | 

## <a name="service-bus-messagingazureservice-bus-messagingservice-bus-messaging-security-attributes"></a>[Service Bus zasílání zpráv](/azure/service-bus-messaging/service-bus-messaging-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) |  Ano, pokud je ve výchozím nastavení zapnuté šifrování na straně serveru. | Spravované klíče zákazníka a BYOK ještě nejsou podporované. Šifrování na straně klienta je zodpovědností klienta. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | Podporuje standardní mechanismus HTTPS/TLS. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne |   |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Volání rozhraní API se provádí prostřednictvím [Azure Resource Manager](/azure/azure-resource-manager/index) a HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano (jenom úroveň Premium) | Koncové body služby virtuální sítě se podporují jenom pro [Service Bus úrovně Premium](/azure/service-bus-messaging/service-bus-premium-messaging) . |
| Podpora vkládání virtuální sítě| Ne | |
| Izolace sítě a podpora brány firewall| Ano (jenom úroveň Premium) |  |
| Podpora vynuceného tunelování| Ne |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Podporováno prostřednictvím [Azure monitor a výstrah](/azure/service-bus-messaging/service-bus-metrics-azure-monitor). |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Spravované prostřednictvím [Azure Active Directory identita spravované služby](/azure/service-bus-messaging/service-bus-managed-service-identity); viz [Service Bus ověřování a autorizace](/azure/service-bus-messaging/service-bus-authentication-and-authorization).|
| Authorization| Ano | Podporuje autorizaci pomocí [RBAC](/azure/service-bus-messaging/service-bus-role-based-access-control) (Preview) a tokenu SAS; viz [Service Bus ověřování a autorizace](/azure/service-bus-messaging/service-bus-authentication-and-authorization). |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Jsou k dispozici protokoly operací. viz [protokoly diagnostiky Service Bus](/azure/service-bus-messaging/service-bus-diagnostic-logs).  |
| Protokolování a audit roviny dat| Ne |  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Podporuje správu verzí poskytovatele prostředků prostřednictvím [rozhraní Azure Resource Manager API](/rest/api/resources/).|


## <a name="service-bus-relayazureservice-bus-relayservice-bus-relay-security-attributes"></a>[Service Bus Relay](/azure/service-bus-relay/service-bus-relay-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) |  Není k dispozici | Relay je webový soket a neuchovává data. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | Služba vyžaduje protokol TLS. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne | Používá pouze certifikáty Microsoft TLS.  |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | HTTPS. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ne |  |
| Izolace sítě a podpora brány firewall| Ne |  |
| Podpora vynuceného tunelování| Není k dispozici | Relay je tunelové propojení TLS  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Via SAS. |
| Authorization|  Ano | Via SAS. |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Prostřednictvím [Azure Resource Manager](/azure/azure-resource-manager/index). |
| Protokolování a audit roviny dat| Ano | Úspěch nebo neúspěch připojení a chyby a protokolování  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Prostřednictvím [Azure Resource Manager](/azure/azure-resource-manager/index).|

## <a name="service-fabricservice-fabricservice-fabric-security-attributesmd"></a>[Service Fabric](../../service-fabric/service-fabric-security-attributes.md)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Zákazník vlastní cluster a sadu škálování virtuálního počítače, na kterých je cluster sestaven. V sadě škálování virtuálního počítače můžete povolit službu Azure Disk Encryption. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | Zákazník vlastní cluster a sadu škálování virtuálního počítače, na kterých je cluster sestaven. V sadě škálování virtuálního počítače můžete povolit službu Azure Disk Encryption. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici |  |
| Zašifrovaná volání rozhraní API| Ano | Service Fabric volání rozhraní API se provádí prostřednictvím Azure Resource Manager. Vyžaduje se platný webový token JSON (JWT). |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Ano |  |
| Izolace sítě a podpora brány firewall| Ano | Používání skupin zabezpečení sítě (NSG). |
| Podpora vynuceného tunelování| Ano | Sítě Azure poskytují vynucené tunelové propojení. |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Používání Azure Monitoring support a podpory třetích stran. |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Ověřování probíhá prostřednictvím Azure Active Directory. |
| Authorization| Ano | Správa identit a přístupu (IAM) pro volání prostřednictvím SFRP. Volání přímo do koncového bodu clusteru podporují dvě role: Uživatel a správce. Zákazník může namapovat rozhraní API na jednu roli. |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Všechny operace roviny ovládacího prvku se spouštějí prostřednictvím procesů pro auditování a schválení. |
| Protokolování a audit roviny dat| Není k dispozici | Zákazník vlastní cluster.  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | |

## <a name="sql-databasesql-databasesql-database-security-attributesmd"></a>[SQL Database](../../sql-database/sql-database-security-attributes.md)

SQL Database zahrnuje [jednu databázi](/azure/sql-database/sql-database-single-index) i [spravovanou instanci](/azure/sql-database/sql-database-managed-instance). Následující položky platí pro obě nabídky kromě případů, kdy je uvedeno jinak.

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Označuje se jako "použití šifrování", jak je popsáno v článku [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). Šifrování na straně serveru používá [transparentní šifrování dat](/azure/sql-database/transparent-data-encryption-azure-sql).|
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | Pomocí protokolu HTTPS. |
| Zpracování šifrovacího klíče, jako je například CMK nebo BYOK| Ano | Jsou nabízeny zpracování klíčů spravovaných službou i zákazníkem. Druhá z nich je nabízena prostřednictvím [Azure Key Vault](/azure/key-vault/index). |
| Šifrování na úrovni sloupce poskytované datovými službami Azure| Ano | Prostřednictvím [Always Encrypted](/azure/sql-database/sql-database-always-encrypted). |
| Šifrovaná volání rozhraní API| Ano | Pomocí protokolu HTTPS/SSL. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Platí pouze pro [jedinou databázi](/azure/sql-database/sql-database-single-index) . |
| Podpora injektáže v Azure Virtual Network| Ano | Platí jenom pro [spravovanou instanci](/azure/sql-database/sql-database-managed-instance) . |
| Izolace sítě a podpora brány firewall| Ano | Brána firewall na úrovni databáze i serveru. Izolace sítě je určena pouze pro [spravovanou instanci](/azure/sql-database/sql-database-managed-instance) . |
| Podpora vynuceného tunelování| Ano | [Spravovaná instance](/azure/sql-database/sql-database-managed-instance) prostřednictvím sítě VPN [ExpressRoute](/azure/expressroute/index.yml) |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure, například Log Analytics nebo Application Insights| Ano | SecureSphere řešení SIEM z Imperva je také podporováno prostřednictvím integrace služby [Azure Event Hubs](/azure/event-hubs/index) prostřednictvím [auditování SQL](/azure/sql-database/sql-database-auditing). |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Azure Active Directory (Azure AD) |
| Authorization| Ano | Žádné |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Řízení-rovina a Správa – protokolování a audit roviny| Ano | Ano jenom pro některé události |
| Protokolování a audit roviny dat | Ano | Via [SQL audit](/azure/sql-database/sql-database-auditing) |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace, jako je například Správa verzí konfigurace| Ne  | Žádný |

### <a name="additional-security-attributes-for-sql-database"></a>Další atributy zabezpečení pro SQL Database

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Preventivní: posouzení ohrožení zabezpečení | Ano | Další informace najdete v tématu [Služba posouzení ohrožení zabezpečení SQL vám pomůže identifikovat slabá místa databáze](/azure/sql-database/sql-vulnerability-assessment). |
| Preventivní: zjišťování a klasifikace dat  | Ano | Viz [Azure SQL Database a SQL Data Warehouse klasifikace & zjišťování dat](/azure/sql-database/sql-database-data-discovery-and-classification). |
| Detekce: detekce hrozeb | Ano | Viz [Rozšířená ochrana před internetovými útoky pro Azure SQL Database](/azure/sql-database/sql-database-threat-detection-overview). |


## <a name="storageazurestoragecommonstorage-security-attributes"></a>[Storage](/azure/storage/common/storage-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano |  |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě) | Ano | Podporuje standardní mechanismy protokolu HTTPS/TLS.  Uživatelé mohou také šifrovat data před jejich přenosem do služby. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ano | Viz [šifrování služby Storage použití klíčů spravovaných zákazníkem v Azure Key Vault](/azure/storage/common/storage-service-encryption-customer-managed-keys?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici |  |
| Zašifrovaná volání rozhraní API| Ano |  |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano |  |
| Podpora vkládání virtuální sítě| Není k dispozici |  |
| Izolace sítě a podpora brány firewall| Ano | |
| Podpora vynuceného tunelování| Není k dispozici |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Azure Monitor metriky, které jsou teď dostupné, se zaprotokolují verze Preview. |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Azure Active Directory, sdílený klíč, sdílený přístupový token. |
| Authorization| Ano | Podpora autorizace přes RBAC, seznamy ACL v POSIX a tokeny SAS |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy | Ano | Protokol aktivit Azure Resource Manager |
| Protokolování a audit roviny dat| Ano | Protokoly diagnostiky služby a Azure Monitor protokolování počáteční verze Preview  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | Podpora správy verzí poskytovatele prostředků prostřednictvím rozhraní API Azure Resource Manager |

## <a name="virtual-machines-and-virtual-machine-scale-sets"></a>Virtual Machines a Virtual Machine Scale Sets

[Virtuální počítače](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes)se systémem Linux virtuální počítače s[Windows](/azure/virtual-machines/windows/virtual-machines-windows-security-attributes) | VM[Scale Sets](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-security-attributes)  | 


### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Podívejte [se, jak zašifrovat virtuální počítač se systémem Linux v Azure](/azure/virtual-machines/linux/encrypt-disks) a [Šifrovat virtuální disky na](/azure/virtual-machines/windows/encrypt-disks)virtuálním počítači s Windows. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Azure Virtual Machines podporuje šifrování [ExpressRoute](/azure/expressroute) a virtuální sítě. Viz [šifrování v přenosech na virtuálních počítačích](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ano | Klíče spravované zákazníkem jsou podporovaným scénářem šifrování Azure; viz [Přehled šifrování Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Přes HTTPS a SSL. |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | |
| Podpora vkládání virtuální sítě| Ano | . |
| Izolace sítě a podpora brány firewall| Ano |  |
| Podpora vynuceného tunelování| Ano | Další informace najdete v tématu [Konfigurace vynuceného tunelování pomocí modelu nasazení Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Podívejte se na téma [monitorování a aktualizace virtuálního počítače se systémem Linux v Azure](/azure/virtual-machines/linux/tutorial-monitoring) a [monitorování a aktualizace virtuálního počítače s Windows v Azure](/azure/virtual-machines/windows/tutorial-monitoring). |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano |  |
| Authorization| Ano |  |


### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano |  |
| Protokolování a audit roviny dat | Ne |  |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano |  | 


## <a name="vpn-gatewayazurevpn-gatewayvpn-gateway-security-attributes"></a>[VPN Gateway](/azure/vpn-gateway/vpn-gateway-security-attributes)

### <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Není k dispozici | Zákaznická data služby VPN Gateway neukládají zákaznická data |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Brána VPN Gateway zašifruje pakety zákazníků mezi bránami Azure VPN a místními zařízeními VPN (P2S) zákazníků. Brány VPN také podporují šifrování VNet-to-VNet. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ne | Předem sdílené klíče zadané zákazníkem jsou zašifrované v klidovém stavu. ale zatím není integrovaná do CMK. |
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Prostřednictvím [Azure Resource Manager](/azure/azure-resource-manager/index) a https  |

### <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Není k dispozici | |
| Podpora vkládání virtuální sítě| Není k dispozici | . |
| Izolace sítě a podpora brány firewall| Ano | Brány VPN jsou vyhrazené instance virtuálních počítačů pro jednotlivé zákazníky Virtual Network  |
| Podpora vynuceného tunelování| Ano |  |

### <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | Viz [Azure monitor diagnostické protokoly/výstrahy](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log) & [Azure monitor metriky/výstrahy](/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric).  |

### <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) pro správu služby a konfiguraci služby Azure VPN Gateway. |
| Authorization| Ano | Umožňuje autorizaci pomocí [RBAC](/azure/role-based-access-control/overview). |

### <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Azure Resource Manager protokolu aktivit. |
| Protokolování a audit roviny dat | Ano | [Protokoly diagnostiky Azure monitor](/azure/azure-resource-manager/resource-group-audit) pro protokolování a AUDITOVÁNÍ připojení VPN. |

### <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | V případě operací správy se stav konfigurace služby Azure VPN Gateway dá exportovat jako šablona Azure Resource Manager a v průběhu času se pořídí verze. | 

