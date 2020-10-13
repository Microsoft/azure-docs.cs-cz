---
title: Ovládací prvky zabezpečení pro Azure App Service
description: Vyhledejte kontrolní seznam bezpečnostních ovládacích prvků pro vyhodnocení Azure App Service vaší organizace.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 93e5123e5b61c9013177f7f3c908578b68da52d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962923"
---
# <a name="security-controls-for-azure-app-service"></a>Ovládací prvky zabezpečení pro Azure App Service

Tento článek popisuje ovládací prvky zabezpečení integrované do Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Síť

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Podpora koncového bodu služby| Yes | K dispozici pro App Service.| [Omezení přístupu Azure App Service](app-service-ip-restrictions.md)
| Podpora vkládání virtuální sítě| Yes | App Service prostředí jsou soukromá implementace App Service vyhrazená pro jednoho zákazníka, který je vložený do virtuální sítě zákazníka. | [Úvod do služby App Service Environment](environment/intro.md)
| Izolace sítě a podpora brány firewall| Yes | Pro účely App Service veřejné varianty pro více tenantů můžou zákazníci nakonfigurovat seznamy ACL sítě (omezení IP adres), aby se mohl zamknout povolený příchozí provoz.  Prostředí App Service se nasazují přímo do virtuálních sítí, takže je můžete zabezpečit pomocí skupin zabezpečení sítě. | [Omezení přístupu Azure App Service](app-service-ip-restrictions.md)
| Podpora vynuceného tunelování| Yes | Prostředí App Service můžete nasadit do virtuální sítě zákazníka, ve které je nakonfigurované vynucené tunelování. | [Konfigurace vynuceného tunelového propojení ve službě App Service Environment](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Monitorování protokolování &

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Yes | App Service se integruje s Application Insights pro jazyky, které podporují Application Insights (kompletní .NET Framework, .NET Core, Java a Node.JS).  Viz [monitorování výkonu Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service také odesílá metriky aplikace do Azure Monitor. | [Monitorování aplikací v Azure App Service](web-sites-monitor.md)
| Protokolování a audit roviny řízení a správy| Yes | Všechny operace správy prováděné s App Service objekty nastávají přes [Azure Resource Manager](../azure-resource-manager/index.yml). Historické protokoly těchto operací jsou k dispozici na portálu i prostřednictvím rozhraní příkazového řádku. | [Azure Resource Manager operací poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftweb), [AZ monitor Activity-log](/cli/azure/monitor/activity-log)
| Protokolování a audit roviny dat | No | Rovina dat pro App Service je vzdálená sdílená složka, která obsahuje obsah webu nasazeného zákazníka.  Neexistuje žádné auditování sdílené složky vzdáleného souboru. |

## <a name="identity"></a>Identita

| Řízení zabezpečení | Ano/Ne | Poznámky |  Dokumentace
|---|---|--|
| Authentication| Yes | Zákazníci můžou sestavovat aplikace na App Service, které se automaticky integrují s [Azure Active Directory (Azure AD)](../active-directory/index.yml) , i s dalšími kompatibilními zprostředkovateli identity OAuth pro přístup pro správu App Service assetů. veškerý přístup se řídí kombinací ověřeného objektu zabezpečení Azure AD a Azure RBAC. | [Ověřování a autorizace v prostředí Azure App Service](overview-authentication-authorization.md)
| Autorizace| Yes | Pro přístup pro správu k App Service prostředkům se veškerý přístup řídí kombinací ověřeného objektu zabezpečení Azure AD a Azure RBAC.  | [Ověřování a autorizace v prostředí Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Ochrana dat

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Šifrování na straně serveru v klidovém umístění: klíče spravované společností Microsoft | Yes | Obsah souboru webu je uložen v Azure Storage, který automaticky zašifruje obsah v klidovém formátu. <br><br>Tajné kódy poskytnuté zákazníkem jsou zašifrované v klidovém stavu. Tajné klíče jsou v klidovém stavu zašifrované a ukládají se do konfiguračních databází App Service.<br><br>Místně připojené disky je možné volitelně použít jako dočasné úložiště na webech (D:\Local a% TMP%). Místně připojené disky nejsou v klidovém stavu šifrované. | [Šifrování služby Azure Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md)
| Šifrování na straně serveru v klidovém umístění: klíče spravované zákazníkem (BYOK) | Yes | Zákazníci se můžou rozhodnout ukládat tajná klíč aplikace do Key Vault a načíst je za běhu. | [Použití Key Vault odkazů pro App Service a Azure Functions (Preview)](app-service-key-vault-references.md)
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování VNet-VNet)| Yes | Zákazníci mohou nakonfigurovat webové servery tak, aby vyžadovaly a používaly protokol HTTPS pro příchozí provoz.  | [Jak nastavit jenom Azure App Service https](/archive/blogs/benjaminperkins/how-to-make-an-azure-app-service-https-only) (Blogový příspěvek)
| Zašifrovaná volání rozhraní API| Yes | Volání správy ke konfiguraci App Service nastávají prostřednictvím volání [Azure Resource Manager](../azure-resource-manager/index.yml) prostřednictvím protokolu HTTPS. |

## <a name="configuration-management"></a>Správa konfigurace

| Řízení zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Yes | V případě operací správy lze stav konfigurace App Service exportovat jako šablonu Azure Resource Manager a v průběhu času. Pro běhové operace můžou zákazníci spravovat několik různých živých verzí aplikace pomocí funkce App Servicech slotů nasazení. | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných kontrolních prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).