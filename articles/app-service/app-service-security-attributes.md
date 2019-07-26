---
title: Atributy zabezpečení pro Azure App Service
description: Kontrolní seznam atributů zabezpečení pro vyhodnocení Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 299262610c027529749840720f46d6dc97a07b21
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442256"
---
# <a name="security-attributes-for-azure-app-service"></a>Atributy zabezpečení pro Azure App Service

Tento článek popisuje atributy zabezpečení integrované do Azure App Service.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém formátu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Obsah souboru webu je uložen v Azure Storage, který automaticky zašifruje obsah v klidovém formátu. V části [Azure Storage šifrování pro](../storage/common/storage-service-encryption.md)neaktivní neaktivní data.<br><br>Tajné kódy poskytnuté zákazníkem jsou zašifrované v klidovém stavu. Tajné klíče jsou v klidovém stavu zašifrované a ukládají se do konfiguračních databází App Service.<br><br>Místně připojené disky je možné volitelně použít jako dočasné úložiště na webech (D:\Local a% TMP%). Místně připojené disky nejsou v klidovém stavu šifrované. |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování virtuální sítě a šifrování virtuální sítě)| Ano | Zákazníci mohou nakonfigurovat webové servery tak, aby vyžadovaly a používaly protokol HTTPS pro příchozí provoz. Informace o tom, [jak nastavit jenom Azure App Service https](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/), najdete v blogovém příspěvku. |
| Zpracování šifrovacích klíčů (CMK, BYOK atd.)| Ano | Zákazníci se můžou rozhodnout ukládat tajná klíč aplikace do Key Vault a načíst je za běhu. Viz [použití Key Vault odkazů pro App Service a Azure Functions (Preview)](app-service-key-vault-references.md).|
| Šifrování na úrovni sloupce (Azure Data Services)| Není k dispozici | |
| Zašifrovaná volání rozhraní API| Ano | Volání správy ke konfiguraci App Service nastávají prostřednictvím volání [Azure Resource Manager](../azure-resource-manager/index.yml) prostřednictvím protokolu HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | Aktuálně k dispozici ve verzi Preview pro App Service. Viz [omezení přístupu Azure App Service](app-service-ip-restrictions.md). |
| Podpora vkládání virtuální sítě| Ano | App Service prostředí jsou soukromá implementace App Service vyhrazená pro jednoho zákazníka, který je vložený do virtuální sítě zákazníka. Viz [Úvod do prostředí App Service](environment/intro.md). |
| Izolace sítě a podpora brány firewall| Ano | Pro účely App Service veřejné varianty pro více tenantů můžou zákazníci nakonfigurovat seznamy ACL sítě (omezení IP adres), aby se mohl zamknout povolený příchozí provoz.  Viz [omezení přístupu Azure App Service](app-service-ip-restrictions.md).  Prostředí App Service se nasazují přímo do virtuálních sítí, takže je můžete zabezpečit pomocí skupin zabezpečení sítě. |
| Podpora vynuceného tunelování| Ano | Prostředí App Service můžete nasadit do virtuální sítě zákazníka, ve které je nakonfigurované vynucené tunelování. Zákazníci musí postupovat podle pokynů v části [konfigurace App Service Environment s vynuceným tunelovým propojením](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Detekce

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora monitorování Azure (Log Analytics, App Insights atd.)| Ano | App Service se integruje s Application Insights pro jazyky, které podporují Application Insights (kompletní .NET Framework, .NET Core, Java a Node. JS).  Viz [monitorování výkonu Azure App Service](../azure-monitor/app/azure-web-apps.md). App Service také odesílá metriky aplikace do Azure Monitor. Viz [monitorování aplikací v Azure App Service](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ověřování| Ano | Zákazníci můžou vytvářet aplikace na App Service, které se automaticky integrují s [Azure Active Directory (Azure AD)](../active-directory/index.yml) , i s dalšími kompatibilními zprostředkovateli identity OAuth. viz [ověřování a autorizace v Azure App Service](overview-authentication-authorization.md). Pro přístup pro správu k prostředkům App Service se veškerý přístup řídí kombinací ověřeného objektu zabezpečení Azure AD a Azure Resource Manager rolí RBAC. |
| Authorization| Ano | Pro přístup pro správu k prostředkům App Service se veškerý přístup řídí kombinací ověřeného objektu zabezpečení Azure AD a Azure Resource Manager rolí RBAC.  |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Protokolování a audit roviny řízení a správy| Ano | Všechny operace správy prováděné s App Service objekty nastávají přes [Azure Resource Manager](../azure-resource-manager/index.yml). Historické protokoly těchto operací jsou k dispozici na portálu i prostřednictvím rozhraní příkazového řádku. viz [Azure Resource Manager operace poskytovatele prostředků](../role-based-access-control/resource-provider-operations.md#microsoftweb) a [AZ monitor Activity-log](/cli/azure/monitor/activity-log). |
| Protokolování a audit roviny dat | Ne | Rovina dat pro App Service je vzdálená sdílená složka, která obsahuje obsah webu nasazeného zákazníka.  Neexistuje žádné auditování sdílené složky vzdáleného souboru. |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (Správa verzí konfigurace atd.)| Ano | V případě operací správy lze stav konfigurace App Service exportovat jako šablonu Azure Resource Manager a v průběhu času. Pro běhové operace můžou zákazníci spravovat několik různých živých verzí aplikace pomocí funkce App Servicech slotů nasazení. | 

