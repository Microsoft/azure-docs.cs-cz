---
title: 'Přehled služby App Service: Azure Stack | Dokumentace Microsoftu'
description: Přehled služby App Service v Azure stacku
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: cf2d65e7e2927aee99e533ea0bca0818f3ab98f6
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079153"
---
# <a name="app-service-on-azure-stack-overview"></a>Přehled App Service v Azure Stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure App Service ve službě Azure Stack je nabídka platform-as-a-service (PaaS) Microsoft Azure k dispozici pro Azure Stack. Služba dá vašim zákazníkům - interní nebo externí – vytvoření webové rozhraní API a Azure Functions aplikací pro jakoukoliv platformu nebo zařízení. Mohou integrovat aplikace s místními aplikacemi a automatizovat podnikové procesy. Operátoři Azure stacku cloudu můžete spustit zákaznických aplikací na plně spravovaných virtuálních počítačích (VM) s jejich zúčastnit slosování o sdílené prostředky virtuálních počítačů nebo vyhrazených virtuálních počítačích.

Azure App Service umožňuje automatizovat obchodní procesy a hostování cloudových rozhraní API. Jako jediná integrovaná služba Azure App Service umožňuje kombinovat různé komponenty – weby, rozhraní RESTful API a firemní procesy – v rámci jednoho řešení.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Proč nabízí Azure App Service ve službě Azure Stack?

Toto jsou některé klíčové funkce a možnosti služby App Service:

- **Více jazyků a architektur**: služby App Service zahrnuje prvotřídní podporu jazyků ASP.NET, Node.js, Java, PHP nebo Python. Na virtuálních počítačích App Service můžete také spustit prostředí Windows PowerShell a další skripty nebo spustitelné soubory.
- **Optimalizace DevOps**: nastavit průběžnou integraci a nasazování pomocí Githubu, místního Gitu nebo BitBucket. Aktualizace lze podporovat prostřednictvím testovacího a přípravného prostředí. Správa aplikací ve službě App Service pomocí Azure Powershellu nebo pomocí multiplatformního rozhraní příkazového řádku (CLI).
- **Integrace se sadou Visual Studio**: vyhrazené nástroje v sadě Visual Studio zjednodušují práci při vytváření a nasazování aplikací.

## <a name="app-types-in-app-service"></a>Typy aplikací v App Service

App Service nabízí několik typů aplikací, z nichž každá je určena k hostování konkrétního typu úlohy:

- [Webové aplikace](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) pro hostování webů a webových aplikací.
- [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) pro hostování rozhraní RESTful API.
- Azure Functions pro hostování řízené událostmi, provádění úloh bez serveru.

Aplikace word se tady odkazuje na prostředky hostování vyhrazené pro spuštěnou úlohu. Pokud zde například zmiňujeme „webovou aplikaci“, pravděpodobně jste zvyklí chápat webovou aplikaci jako výpočetní prostředky i kód aplikace, které společně poskytují v prohlížeči příslušné funkce. Ale ve službě App Service je webová aplikace výpočetní prostředky, které poskytuje Azure Stack pro hostování kódu aplikace.

Vaše aplikace může skládat z více aplikací App Service různého druhu. Například pokud vaše aplikace se skládá z webového front-endu a end zpět o rozhraní RESTful API, můžete:

- Nasadit obojí (front-end i rozhraní API) do jedné webové aplikace.
- Nasadit kód front-endu do webové aplikace a kód back-endu do aplikace API.

   ![Přehled služby App Service s dat monitorování](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Co je plán služby App Service?

Poskytovatele prostředků App Service používá stejný kód, který používá službu Azure App Service. V důsledku toho některé obvyklé koncepty stojí za to s popisem. Cenové kontejner pro aplikace ve službě App Service má název plánu služby App Service. Představuje sadu vyhrazených virtuálních počítačů sloužící k uchování vašich aplikací. V rámci daného předplatného můžete mít více plánů služby App Service.

V Azure jsou sdílené a vyhrazené pracovní procesy. Sdílených pracovních serverů podporoval hostování s vysokou hustotou aplikace s více tenanty, a pouze jednu sadu sdílených pracovních procesů. Vyhrazené servery se používá jenom k jednomu tenantovi a vytvářet ve třech velikostech: malé, střední a velké. Potřebám zákazníků v místním nemůže vždy najdete pomocí těchto podmínek. Ve službě App Service ve službě Azure Stack můžete definovat správci poskytovatele prostředků vrstvy pracovních procesů, které chtějí zpřístupnit. Na základě vašich jedinečných hostování potřeb, můžete definovat více sad sdílených pracovních procesů nebo jinou sadu vyhrazené pracovní procesy. Pomocí těchto definic vrstvy pracovního procesu, můžete pak definovat svou vlastní ceny skladových položek.

## <a name="portal-features"></a>Funkce portálu

Služba App Service ve službě Azure Stack používá stejné uživatelské rozhraní, která používá službu Azure App Service, platí s back-endu. Ale některé funkce jsou zakázány a nejsou funkční v Azure stacku. Očekávání týkající se Azure nebo služby, které vyžadují tyto funkce nejsou aktuálně k dispozici ve službě Azure Stack.

## <a name="next-steps"></a>Další postup

- [Před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Instalace poskytovatele prostředků App Service](azure-stack-app-service-deploy.md)

Můžete také vyzkoušet ostatní [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md), například [poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md) a [poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md).
