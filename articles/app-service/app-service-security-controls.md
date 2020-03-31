---
title: Ovládací prvky zabezpečení
description: Najděte kontrolní seznam ovládacích prvků zabezpečení pro vyhodnocení služby Azure App Service pro vaši organizaci.
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74671456"
---
# <a name="security-controls-for-azure-app-service"></a>Ovládací prvky zabezpečení pro službu Azure App Service

Tento článek dokumentuje ovládací prvky zabezpečení integrované do služby Azure App Service.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Network (Síť)

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Podpora koncového bodu služby| Ano | K dispozici pro službu App Service.| [Omezení přístupu k službě Azure App Service](app-service-ip-restrictions.md)
| Podpora vstřikování virtuální sítě| Ano | Prostředí služby App Service jsou privátní implementace služby App Service určené pro jednoho zákazníka vložené do virtuální sítě zákazníka. | [Úvod do služby App Service Environment](environment/intro.md)
| Podpora izolace sítě a brány firewall| Ano | Pro veřejné víceklientské varianty app service, zákazníci mohou nakonfigurovat síťové aklů (OMEZENÍ IP) uzamknout povolené příchozí provoz.  Prostředí služby App Service se nasazují přímo do virtuálních sítí, a proto je lze zabezpečit pomocí cílů sítě. | [Omezení přístupu k službě Azure App Service](app-service-ip-restrictions.md)
| Podpora vynuceného tunelování| Ano | Prostředí služby App Service lze nasadit do virtuální sítě zákazníka, kde je nakonfigurováno vynucené tunelové propojení. | [Konfigurace vynuceného tunelového propojení ve službě App Service Environment](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>Sledování & protokolování

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Podpora monitorování Azure (analýza protokolů, přehledy aplikací atd.)| Ano | Služba App Service se integruje s Application Insights pro jazyky, které podporují Application Insights (Full .NET Framework, .NET Core, Java a Node.JS).  Viz [Monitorování výkonu služby Azure App Service](../azure-monitor/app/azure-web-apps.md). Služba App Service také odesílá metriky aplikací do Azure Monitoru. | [Monitorování aplikací ve službě Azure App Service](web-sites-monitor.md)
| Protokolování a audit roviny řízení a správy| Ano | Všechny operace správy prováděné na objektech služby App Service probíhají prostřednictvím [Správce prostředků Azure](../azure-resource-manager/index.yml). Historické protokoly těchto operací jsou k dispozici jak na portálu, tak prostřednictvím rozhraní příkazového příkazu. | [Operace zprostředkovatele prostředků Azure Resource Manager](../role-based-access-control/resource-provider-operations.md#microsoftweb), [az monitor protokol aktivit](/cli/azure/monitor/activity-log)
| Protokolování a audit roviny dat | Ne | Rovina dat pro službu App Service je vzdálená sdílená složka obsahující obsah nasazeného webu zákazníkem.  Neexistuje žádný audit vzdálené sdílené složky. |

## <a name="identity"></a>Identita

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky |  Dokumentace
|---|---|--|
| Ověřování| Ano | Zákazníci můžou vytvářet aplikace ve službě App Service, které se automaticky integrují s [Azure Active Directory (Azure AD)](../active-directory/index.yml) a s dalšími poskytovateli identit kompatibilními s OAuth Pro správu přístupu k prostředkům služby App Service je veškerý přístup řízen kombinací ověřených primárních a azure resource managerů RBAC rolí. | [Ověřování a autorizace v prostředí Azure App Service](overview-authentication-authorization.md)
| Autorizace| Ano | Pro přístup ke správě prostředků služby App Service je veškerý přístup řízen kombinací ověřených primárních objektů Azure AD a rolí RBAC Azure Resource Manager.  | [Ověřování a autorizace v prostředí Azure App Service](overview-authentication-authorization.md)

## <a name="data-protection"></a>Ochrana dat

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Šifrování na straně serveru v klidovém stavu: Klíče spravované společností Microsoft | Ano | Obsah souboru webu se ukládá ve službě Azure Storage, která automaticky šifruje obsah v klidovém stavu. <br><br>Tajné informace zadané zákazníkem jsou v klidovém stavu zašifrovány. Tajné klíče jsou šifrovány v klidovém stavu při ukládání v konfiguračních databázích služby App Service.<br><br>Místně připojené disky mohou být volitelně použity jako dočasné úložiště weby (D:\local a %TMP%). Místně připojené disky nejsou šifrovány v klidovém stavu. | [Šifrování Azure Storage pro data v klidovém stavu](../storage/common/storage-service-encryption.md)
| Šifrování na straně serveru v klidovém stavu: klíče spravované zákazníkem (BYOK) | Ano | Zákazníci si mohou uložit tajné klíče aplikací v trezoru klíčů a načíst je za běhu. | [Použití odkazů trezoru klíčů pro službu App Service a funkce Azure (preview)](app-service-key-vault-references.md)
| Šifrování na úrovni sloupců (Azure Data Services)| Není dostupné. | |
| Šifrování při přenosu (například šifrování ExpressRoute, šifrování ve virtuální síti a šifrování virtuální sítě)| Ano | Zákazníci mohou nakonfigurovat webové servery tak, aby vyžadovaly a používaly protokol HTTPS pro příchozí provoz.  | [Jak vytvořit jenom azure app service HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) (blog post)
| Zašifrovaná volání rozhraní API| Ano | Volání správy ke konfiguraci služby App Service probíhají prostřednictvím volání [Azure Resource Manager](../azure-resource-manager/index.yml) přes protokol HTTPS. |

## <a name="configuration-management"></a>Správa konfigurace

| Ovládací prvek zabezpečení | Ano/Ne | Poznámky | Dokumentace
|---|---|--|
| Podpora správy konfigurace (správa verzí konfigurace atd.)| Ano | Pro operace správy lze stav konfigurace služby App Service exportovat jako šablonu Azure Resource Manager a v průběhu času se bude verzí. Pro operace s provozem runtime mohou zákazníci udržovat více různých živých verzí aplikace pomocí funkce slotů pro nasazení služby App Service. | 

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [integrovaných ovládacích prvcích zabezpečení napříč službami Azure](../security/fundamentals/security-controls.md).
