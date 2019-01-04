---
title: App Service v Azure stacku – přehled | Dokumentace Microsoftu
description: Přehled služby App Service v Azure stacku
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: sethm
ms.reviewer: anwestg
ms.openlocfilehash: b1b4ea5775a661712838c1495ef0025a83b8c518
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53607939"
---
# <a name="app-service-on-azure-stack-overview"></a>Přehled App Service v Azure Stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure App Service ve službě Azure Stack je nabídka platform-as-a-service (PaaS) Microsoft Azure k dispozici pro Azure Stack. Tato služba umožňuje vaší interní nebo externí zákazníkům vytvářet webové rozhraní API a Azure Functions aplikací pro jakoukoliv platformu nebo zařízení. Vaše aplikace můžou integrovat s místními aplikacemi a automatizovat své obchodní procesy. Operátoři cloudu Azure Stack můžou spouštět zákaznické aplikace na plně spravovaných virtuálních počítačích se sdílenými prostředky podle vlastního výběru nebo na vyhrazených virtuálních počítačích.

Azure App Service umožňuje automatizaci obchodních procesů a hostování cloudových rozhraní API. Jako jediná integrovaná služba Azure App Service umožňuje kombinovat různé součásti, jako je například weby, rozhraní REST API a obchodních procesů v rámci jednoho řešení.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Proč nabízí Azure App Service ve službě Azure Stack?

Toto jsou některé klíčové funkce a možnosti služby App Service:

- **Více jazyků a architektur**: App Service zahrnuje prvotřídní podporu jazyků ASP.NET, Node.js, Java, PHP nebo Python. Na virtuálních počítačích App Service můžete také spustit prostředí Windows PowerShell a další skripty nebo spustitelné soubory.
- **Optimalizace DevOps**: Nastavte průběžnou integraci a nasazování pomocí Githubu, místního Gitu nebo BitBucket. Můžete aktualizace lze podporovat prostřednictvím testovacího a přípravného prostředí a spravovat vaše aplikace ve službě App Service pomocí Azure Powershellu nebo pomocí multiplatformního rozhraní příkazového řádku (CLI).
- **Integrace se sadou Visual Studio**: Vyhrazené nástroje v sadě Visual Studio zjednodušují práci při vytváření a nasazování aplikací.

## <a name="app-types-in-app-service"></a>Typy aplikací v App Service

App Service nabízí několik typů aplikací, z nichž každá je určena k hostování konkrétního typu úlohy:

- [Webové aplikace](../app-service/overview.md) pro hostování webů a webových aplikací.
- [API Apps](../app-service/overview.md) pro hostování rozhraní REST API.
- Azure Functions pro hostování řízené událostmi, provádění úloh bez serveru.

Slovo *aplikace* odkazuje na prostředky hostování vyhrazené pro spuštěnou úlohu. Pořízení *webovou aplikaci* jako příklad, pravděpodobně jste zvyklí na dala webovou aplikaci jako výpočetní prostředky i aplikace, které tuto funkci společně doručovat do prohlížeče kód. Webové aplikace ve službě App Service je výpočetní prostředek, který poskytuje Azure Stack pro hostování kódu aplikace.

Vaše aplikace může skládat z více aplikací App Service různého druhu. Například pokud vaše aplikace se skládá z webového front-endu a rozhraní REST API zpět ukončit, můžete:

- Nasadit obojí (front-end i rozhraní API) do jedné webové aplikace.
- Nasadit kód front-endu do webové aplikace a kód back-endu do aplikace API.

   [ ![Přehled služby App Service se data monitorování](media/azure-stack-app-service-overview/image01.png "Přehled služby App Service s dat monitorování") ](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Co je plán služby App Service?

Poskytovatele prostředků App Service používá stejný kód, který používá služba Azure App Service a proto sdílí některé obvyklé koncepty. Ve službě App Service, se nazývá cenové kontejneru pro aplikace *plán služby App Service*. Představuje sadu vyhrazených virtuálních počítačů sloužící k uchování vašich aplikací. V rámci daného předplatného můžete mít více plánů služby App Service.

V Azure jsou sdílené a vyhrazené pracovní procesy. Sdílených pracovních serverů podporoval hostování s vysokou hustotou aplikace s více tenanty, a pouze jednu sadu sdílených pracovních procesů. Vyhrazené servery se používá jenom k jednomu tenantovi a vytvářet ve třech velikostech: malé, střední a velké. Potřebám zákazníků v místním nemůže vždy najdete pomocí těchto podmínek. Ve službě App Service ve službě Azure Stack můžete definovat správci poskytovatele prostředků vrstvy pracovních procesů, které chtějí zpřístupnit. Na základě vašich jedinečných hostování potřeb, můžete definovat více sad sdílených pracovních procesů nebo jinou sadu vyhrazené pracovní procesy. Pomocí těchto definic vrstvy pracovního procesu, můžete pak definovat svou vlastní ceny skladových položek.

## <a name="portal-features"></a>Funkce portálu

Služba App Service ve službě Azure Stack používá stejné uživatelské rozhraní, která používá službu Azure App Service. Platí to i s back-endu. Ale některé funkce jsou zakázány ve službě Azure Stack. Očekávání týkající se Azure nebo služby, které vyžadují tyto funkce nejsou aktuálně k dispozici ve službě Azure Stack.

## <a name="next-steps"></a>Další postup

- [Před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Instalace poskytovatele prostředků App Service](azure-stack-app-service-deploy.md)

Můžete také vyzkoušet ostatní [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md), například [poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md) a [poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md).
