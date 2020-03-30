---
title: Osvědčené postupy pro nasazení
description: Přečtěte si o klíčových mechanismech nasazení do služby Azure App Service. Najděte doporučení specifická pro jazyk a další upozornění.
keywords: azure app service, webová aplikace, nasazení, nasazení, kanály, sestavení
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: 14946a05f021a9b155fd9a9621f73bde980970fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750461"
---
# <a name="deployment-best-practices"></a>Doporučené postupy nasazení

Každý vývojový tým má jedinečné požadavky, které mohou ztížit implementaci kanálu efektivního nasazení v jakékoli cloudové službě. Tento článek představuje tři hlavní součásti nasazení do služby App Service: zdroje nasazení, sestavení kanály a mechanismy nasazení. Tento článek také popisuje některé osvědčené postupy a tipy pro konkrétní jazykové zásobníky.

## <a name="deployment-components"></a>Součásti nasazení

### <a name="deployment-source"></a>Zdroj nasazení

Zdroj nasazení je umístění kódu aplikace. Pro produkční aplikace je zdroj nasazení obvykle úložiště hostované softwarem pro správu verzí, jako je [GitHub, BitBucket nebo Azure Repos](deploy-continuous-deployment.md). Pro scénáře vývoje a testování může být zdrojem nasazení [projekt v místním počítači](deploy-local-git.md). Služba App Service také podporuje [složky OneDrive a Dropbox](deploy-content-sync.md) jako zdroje nasazení. Zatímco cloudové složky mohou usnadnit zahájení používání služby App Service, obvykle se nedoporučuje používat tento zdroj pro produkční aplikace na podnikové úrovni. 

### <a name="build-pipeline"></a>Kanál buildu

Jakmile se rozhodnete pro zdroj nasazení, dalším krokem je výběr kanálu sestavení. Kanál sestavení přečte zdrojový kód ze zdroje nasazení a provede řadu kroků (například kompilaci kódu, minifying HTML a JavaScript, spuštěné testy a součásti balení), aby se aplikace dostala do spustitelného stavu. Konkrétní příkazy prováděné kanálem sestavení závisí na zásobníku jazyka. Tyto operace lze provést na serveru sestavení, jako je například Azure Pipelines, nebo spustit místně.

### <a name="deployment-mechanism"></a>Mechanismus nasazení

Mechanismus nasazení je akce, která se používá k vložíní vytvořené aplikace do adresáře */home/site/wwwroot* vaší webové aplikace. Adresář */wwwroot* je připojené úložiště sdílené všemi instancemi webové aplikace. Když mechanismus nasazení vloží vaši aplikaci do tohoto adresáře, vaše instance obdrží oznámení o synchronizaci nových souborů. Služba App Service podporuje následující mechanismy nasazení:

- Kudu koncové body: [Kudu](https://github.com/projectkudu/kudu/wiki) je open source vývojářský nástroj produktivity, který běží jako samostatný proces ve službě Windows App Service a jako druhý kontejner v Linux App Service. Kudu zpracovává průběžné nasazení a poskytuje koncové body HTTP pro nasazení, jako je například zipdeploy.
- FTP a WebDeploy: Pomocí [vašeho webu nebo pověření uživatele](deploy-configure-credentials.md)můžete nahrávat soubory přes [FTP](deploy-ftp.md) nebo WebDeploy. Tyto mechanismy neprocházejí Kudu.  

Nástroje pro nasazení, jako jsou moduly plug-in Azure Pipelines, Jenkins a editor, používají jeden z těchto mechanismů nasazení.

## <a name="language-specific-considerations"></a>Důležité informace týkající se jazyka

### <a name="java"></a>Java

Použijte Kudu [zipdeploy/](deploy-zip.md) API pro nasazení aplikací JAR a [wardeploy/](deploy-zip.md#deploy-war-file) pro aplikace WAR. Pokud používáte Jenkins, můžete použít tato api přímo ve fázi nasazení. Další informace najdete v [tomto článku](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Node

Ve výchozím nastavení Kudu provede kroky sestavení pro`npm install`aplikaci uzlu ( ). Pokud používáte službu sestavení, jako je Azure DevOps, pak sestavení Kudu je zbytečné. Chcete-li zakázat sestavení Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`vytvořte nastavení `false`aplikace , s hodnotou .

### <a name="net"></a>.NET 

Ve výchozím nastavení Kudu provede kroky sestavení pro`dotnet build`vaši aplikaci .Net ( ). Pokud používáte službu sestavení, jako je Azure DevOps, pak sestavení Kudu je zbytečné. Chcete-li zakázat sestavení Kudu, `SCM_DO_BUILD_DURING_DEPLOYMENT`vytvořte nastavení `false`aplikace , s hodnotou .

## <a name="other-deployment-considerations"></a>Další důležité informace o nasazení

### <a name="use-deployment-slots"></a>Použití slotů pro nasazení

Kdykoli je to možné, použijte [sloty nasazení](deploy-staging-slots.md) při nasazování nového produkčního sestavení. Když používáte úroveň Standard App Service Plan nebo lepší, můžete nasadit aplikaci do pracovního prostředí, ověřit změny a provést kouřové testy. Až budete připraveni, můžete vyměnit pracovní a produkční sloty. Operace prohození zahřeje potřebné instance pracovníka tak, aby odpovídaly vašemu výrobnímu měřítku, čímž eliminuje prostoje. 

### <a name="local-cache"></a>Místní mezipaměť

Obsah služby Azure App Service se uložených ve službě Azure Storage a je vynořil a trvanlivým způsobem jako sdílené složky obsahu. Některé aplikace však potřebují pouze vysoce výkonné úložiště obsahu jen pro čtení, které mohou běžet s vysokou dostupností. Tyto aplikace mohou těžit z použití [místní mezipaměti](overview-local-cache.md). Místní mezipaměť se nedoporučuje pro weby pro správu obsahu, jako je WordPress.

Vždy používejte místní mezipaměť ve spojení s [sloty nasazení,](deploy-staging-slots.md) abyste zabránili prostojům. Informace o společném používání těchto funkcí naleznete v [této části.](overview-local-cache.md#best-practices-for-using-app-service-local-cache)

### <a name="high-cpu-or-memory"></a>Vysoký procesor nebo paměť

Pokud váš plán služby App Service používá více než 90 % dostupného procesoru nebo paměti, může mít základní virtuální počítač potíže se zpracováním vašeho nasazení. V takovém případě dočasně vertikálně navertit kapacitu počtu instancí k provedení nasazení. Po dokončení nasazení můžete vrátit počet instancí na jeho předchozí hodnotu.

Další informace o doporučených postupech najdete v centru [aplikace Diagnostika služby App Service](https://docs.microsoft.com/azure/app-service/overview-diagnostics) a vyhledejte užitečné postupy, které jsou specifické pro váš prostředek.

- Přejděte do webové aplikace na [webu Azure Portal](https://portal.azure.com).
- Klikněte na **Diagnostika a řešení problémů** v levém navigačním panelu, který otevře Diagnostika služby App Service.
- Zvolte dlaždice domovské stránky **doporučené postupy.**
- Chcete-li zobrazit aktuální stav aplikace s ohledem na tyto osvědčené postupy, klepněte na tlačítko **Doporučené postupy pro dostupnost & výkonu** nebo doporučené postupy pro optimální **konfiguraci.**

Pomocí tohoto odkazu můžete také přímo otevřít diagnostiku `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot`služby App Service pro váš prostředek: .
