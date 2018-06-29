---
title: 'Přehled služby App Service: Azure zásobníku | Microsoft Docs'
description: Přehled služby App Service v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 42258a352b05d97341f20c13bf837e1398dd8979
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099958"
---
# <a name="app-service-on-azure-stack-overview"></a>Přehled App Service v Azure Stacku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Azure App Service v zásobníku Azure je nabídku platformy jako služba (PaaS) Microsoft Azure k dispozici pro Azure zásobníku. Služba umožní vašim zákazníkům - interních nebo externích – vytvoření webové rozhraní API a funkce Azure aplikací pro všechny platformy a zařízení. Mohou své aplikace můžete integrovat s místními aplikacemi a automatizovat podnikové procesy. Operátoři cloudu Azure zásobníku můžou spouštět aplikace pro zákazníka na plně spravovaných virtuálních počítačích (VM) s jejich výběr sdílené prostředky virtuálních počítačů nebo vyhrazených virtuálních počítačích.

Aplikační služba Azure umožňuje automatizovat obchodní procesy a hostování cloudových rozhraní API. Jako jediná integrovaná služba Azure App Service umožňuje kombinovat různé komponenty – weby, rozhraní RESTful API a firemní procesy – v jediném řešení.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Proč nabízejí služby Azure App Service v zásobníku Azure?

Toto jsou některé klíčové funkce a možnosti služby App Service:

- **Více jazyků a rozhraní**: služby App Service zahrnuje prvotřídní podporu pro ASP.NET, Node.js, Java, PHP a Python. Na virtuálních počítačích App Service můžete také spustit prostředí Windows PowerShell a další skripty či spustitelné soubory.
- **Optimalizace DevOps**: nastavte průběžnou integraci a nasazení v Githubu, místní Git nebo BitBucket. Aktualizace lze podporovat prostřednictvím testovacích a přípravných prostředí. Spravujte aplikace v App Service pomocí prostředí Azure PowerShell nebo rozhraní příkazového řádku (CLI) napříč platformami.
- **Integrace aplikace Visual Studio**: vyhrazené nástroje v sadě Visual Studio zjednodušují práci při vytváření a nasazování aplikací.

## <a name="app-types-in-app-service"></a>Typy aplikací v App Service

Služby App Service nabízí několik typů aplikací, z nichž každá je určena k hostování konkrétní úlohu:

- [Webové aplikace](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) pro hostování webů a webových aplikací.
- [Aplikace API](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) pro hostování rozhraní RESTful API.
- Azure Functions pro hostování událostí řízené bez serveru úloh.

Aplikace word odkazuje na prostředky hostování vyhrazené na běžící zatížení. Pokud zde například zmiňujeme „webovou aplikaci“, pravděpodobně jste zvyklí chápat webovou aplikaci jako výpočetní prostředky i kód aplikace, které společně poskytují v prohlížeči příslušné funkce. Ale ve službě App Service je webová aplikace výpočetní prostředky, které zásobník Azure poskytuje k hostování kódu aplikace.

Aplikace může být tvořen více aplikací App Service různého druhu. Například pokud vaše aplikace se skládá z webového uživatelského rozhraní a ukončení rozhraní RESTful API zpět, můžete:

- Nasadit obojí (front-end i rozhraní API) do jedné webové aplikace.
- Nasadit kód front-endu do webové aplikace a kód back-endu do aplikace API.

   ![Přehled aplikace služby s daty monitorování](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>Co je plán služby App Service?

Poskytovatel prostředku služby App Service používá stejný kód, který používá Azure App Service. V důsledku toho jsou některé běžné koncepty vhodné popisující. Ve službě App Service se nazývá cenovou kontejneru pro aplikace plán služby App Service. Reprezentuje sadu vyhrazené virtuální počítače používané pro udržení vaší aplikace. V rámci konkrétního předplatného můžete mít víc plány služby App Service.

V Azure jsou sdílené a vyhrazených pracovních procesů. Sdílených pracovních podporuje hostování s vysokou hustotou víceklientské aplikace a je jen jednu sadu sdílených pracovních procesů. Vyhrazené servery se používají pouze jednoho klienta a pocházet na třech úrovních: malé, střední a velké. Potřebám zákazníků místní nelze popsané vždy pomocí tyto podmínky. Ve službě App Service v Azure zásobníku správci zprostředkovatele prostředků můžete definovat pracovní vrstvy, které se mají být k dispozici. Podle potřeb jedinečný hostování, můžete definovat více sad sdílených pracovních procesů nebo různé sady vyhrazených pracovních procesů. Pomocí těchto pracovních vrstvě definice se můžete pak definovat vlastní ceny SKU.

## <a name="portal-features"></a>Funkce portálu

V zásobníku Azure App Service používá stejné uživatelské rozhraní, která používá Azure App Service, platí také s back-end. Některé funkce, ale jsou zakázány a nejsou funkční v zásobníku Azure. Očekávání specifické pro Azure nebo služby, které vyžadují tyto funkce nejsou aktuálně k dispozici v zásobníku Azure.

## <a name="next-steps"></a>Další postup

- [Před zahájením práce s App Service v Azure zásobníku](azure-stack-app-service-before-you-get-started.md)
- [Nainstalujte zprostředkovatele prostředků služby App Service](azure-stack-app-service-deploy.md)

Můžete také zkusit dalších [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md), jako [poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md) a [poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md).
