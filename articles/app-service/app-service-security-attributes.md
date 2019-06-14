---
title: Atributy zabezpečení pro službu Azure App Service
description: Kontrolní seznam zabezpečení atributy za vaše rozhodnutí vyzkoušet službu Azure App Service
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e38e1479c1e094f691e8f22f0a48f9342cae5cf3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475077"
---
# <a name="security-attributes-for-azure-app-service"></a>Atributy zabezpečení pro službu Azure App Service

Tento článek popisuje běžné atributy zabezpečení integrované do služby Azure App Service.

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Preventivní

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Šifrování v klidovém stavu (například šifrování na straně serveru, šifrování na straně serveru pomocí klíčů spravovaných zákazníkem a další funkce šifrování) | Ano | Obsah souboru webu je uložený ve službě Azure Storage automaticky šifruje obsah v úložišti. Zobrazit [šifrování služby Azure Storage pro neaktivní uložená data](../storage/common/storage-service-encryption.md).<br><br>Tajné kódy zákazníka se šifrují při nečinnosti. Tajné klíče jsou šifrují při nečinnosti při uložení konfigurační databáze služby App Service.<br><br>Místně připojených disků můžete volitelně použít jako dočasné úložiště službou websites (D:\local a % TMP %). Místně připojené disky nejsou šifrovány v klidovém stavu. |
| Šifrování během přenosu (například šifrování ExpressRoute ve virtuální síti a šifrováním virtuálními sítěmi)| Ano | Zákazníci můžou nakonfigurovat webových stránek a vyžadovat a používat protokol HTTPS pro příchozí provoz. Najdete v blogovém příspěvku [jak Azure App Service pouze HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/). |
| Šifrování klíče zpracování (CMK, BYOK, atd.)| Ano | Zákazníci si mohou vybrat ukládání tajných klíčů aplikací ve službě Key Vault a načítat je za běhu. Zobrazit [pro App Service a Azure Functions (preview) odkazuje na použití služby Key Vault](app-service-key-vault-references.md).|
| Šifrování na úrovni sloupce (Azure Data Services)| neuvedeno | |
| Šifrované volání rozhraní API| Ano | Dojde k volání správy konfigurace služby App Service prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml) volání přes protokol HTTPS. |

## <a name="network-segmentation"></a>Segmentace sítě

| Atribut zabezpečení | Ano/Ne | Poznámky |
|---|---|--|
| Podpora koncového bodu služby| Ano | V současnosti ve verzi preview pro službu App Service. Zobrazit [omezení přístupu službě Azure App Service](app-service-ip-restrictions.md). |
| Vkládání podpory virtuálních sítí| Ano | App Service Environment jsou privátní implementace služby App Service je vyhrazený pro jediného zákazníka vloženy do virtuální sítě zákazníka. Zobrazit [Úvod do služby App Service Environment](environment/intro.md). |
| Izolace sítě a Firewalling podpory| Ano | Za veřejné více tenanty služby App Service zákazníci můžou nakonfigurovat seznamy ACL (omezení IP adres) zamezit povoleného příchozího provozu sítě.  Zobrazit [omezení přístupu službě Azure App Service](app-service-ip-restrictions.md).  Služby App Service Environment se nasazuje přímo do virtuálních sítí a proto ji můžete zabezpečit pomocí skupin zabezpečení sítě. |
| Vynucené tunelování podpory| Ano | Služby App Service Environment je možné nasadit do virtuální sítě zákazníka, kde je nakonfigurované vynucené tunelování. Pokud si zákazníci musí postupovat podle pokynů v [konfigurace služby App Service Environment pomocí vynuceného tunelování](environment/forced-tunnel-support.md). |

## <a name="detection"></a>Detection (Detekce)

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora (Log analytics, App insights atd.) pro monitorování Azure| Ano | App Service integruje se službou Application Insights pro jazyky, které podporují Application Insights (úplné rozhraní .NET Framework, .NET Core, Java a Node.JS).  Zobrazit [výkonu monitorování Azure App Service](../azure-monitor/app/azure-web-apps.md). Metriky aplikací služby App Service také odešle do služby Azure Monitor. Zobrazit [monitorování aplikací ve službě Azure App Service](web-sites-monitor.md). |

## <a name="identity-and-access-management"></a>Správa identit a přístupu

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Authentication| Ano | Zákazníci mohou vytvářet aplikace ve službě App Service, které automaticky integrují s [Azure Active Directory (Azure AD)](../active-directory/index.yml) i jinými poskytovateli identity kompatibilní OAuth; viz [ověřování a autorizace Azure App Service](overview-authentication-authorization.md). Pro správu přístupu pro prostředky App Service veškerý přístup je řízen kombinaci instanční objekt Azure AD, ověření a role Azure RBAC Resource Manageru. |
| Autorizace| Ano | Pro správu přístupu pro prostředky App Service veškerý přístup je řízen kombinaci instanční objekt Azure AD, ověření a role Azure RBAC Resource Manageru.  |


## <a name="audit-trail"></a>Záznam pro audit

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Ovládací prvek a správu roviny protokolování a auditování| Ano | Všechny operace správy prováděné s objekty služby App Service dojde k prostřednictvím [Azure Resource Manageru](../azure-resource-manager/index.yml). Historické protokoly tyto operace jsou k dispozici na portálu a prostřednictvím rozhraní příkazového řádku; Zobrazit [operace poskytovatele prostředků Azure Resource Manageru](../role-based-access-control/resource-provider-operations.md#microsoftweb) a [az monitorování – protokol aktivit](/cli/azure/monitor/activity-log). |
| Protokolování roviny dat a auditu | Ne | Rovina dat pro službu App Service je vzdálené sdílené složce s obsahem nasazený web zákazníka.  Neexistuje žádný auditování vzdálené sdílené složce. |

## <a name="configuration-management"></a>Správa konfigurace

| Atribut zabezpečení | Ano/Ne | Poznámky|
|---|---|--|
| Podpora správy konfigurace (verze konfigurace atd.)| Ano | Pro operace správy stav konfigurace služby App Service je možné exportovat jako šablonu Azure Resource Manageru a systémovou správou verzí v čase. Pro operace modulu runtime zákazníci mohli pro více různé verze živé aplikace pomocí funkce sloty nasazení služby App Service. | 

