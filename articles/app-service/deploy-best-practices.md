---
title: Doporučené postupy pro nasazení
description: Přečtěte si o klíčových mechanismech nasazení na Azure App Service. Vyhledání doporučení specifických pro jazyk a dalších aspektů.
keywords: Azure App Service, Webová aplikace, nasazení, nasazení, kanály, sestavení
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: d3959b9a86ccc2d42cbf7bd188ce86bf4b7a2e63
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670094"
---
# <a name="deployment-best-practices"></a>Osvědčené postupy nasazení

Každý vývojový tým má jedinečné požadavky, které můžou zajistit obtížné implementaci kanálu efektivního nasazení v jakékoli cloudové službě. Tento článek představuje tři hlavní komponenty nasazení do App Service: zdroje nasazení, kanály sestavení a mechanismy nasazení. Tento článek také popisuje některé osvědčené postupy a tipy pro konkrétní jazykové zásobníky.

## <a name="deployment-components"></a>Součásti nasazení

### <a name="deployment-source"></a>Zdroj nasazení

Zdroj nasazení je umístění vašeho kódu aplikace. U produkčních aplikací je zdrojem nasazení obvykle úložiště hostované softwarem pro řízení verzí, jako je [GitHub, Bitbucket nebo Azure Repos](deploy-continuous-deployment.md). Ve scénářích vývoje a testování může být zdrojem nasazení [projekt na vašem místním počítači](deploy-local-git.md). App Service také podporuje [OneDrive a složky Dropboxu](deploy-content-sync.md) jako zdroje nasazení. I když cloudové složky můžou snadno začít pracovat s App Service, obvykle se tento zdroj nedoporučuje používat pro produkční aplikace na podnikové úrovni. 

### <a name="build-pipeline"></a>Kanál sestavení

Jakmile se rozhodnete pro zdroj nasazení, je dalším krokem výběr kanálu sestavení. Kanál sestavení přečte zdrojový kód ze zdroje nasazení a provede řadu kroků (například kompilování kódu, minifikace HTML a JavaScriptu, spuštění testů a balení komponent) k získání aplikace ve stavu spustitelný. Konkrétní příkazy spouštěné kanálem sestavení závisí na vašem jazykovém zásobníku. Tyto operace lze spustit na serveru sestavení, například Azure Pipelines, nebo v místním prostředí.

### <a name="deployment-mechanism"></a>Mechanismus nasazení

Mechanismus nasazení je akce, která se používá k umístění vaší sestavené aplikace do adresáře */Home/site/wwwroot* vaší webové aplikace. Adresář */wwwroot* je připojené umístění úložiště sdílené všemi instancemi vaší webové aplikace. Když mechanismus nasazení umístí aplikaci do tohoto adresáře, obdrží vaše instance oznámení o synchronizaci nových souborů. App Service podporuje následující mechanismy nasazení:

- Kudu koncové body: [Kudu](https://github.com/projectkudu/kudu/wiki) je open source nástroj pro zvýšení produktivity vývojářů, který běží jako samostatný proces ve Windows App Service a jako druhý kontejner v App Service systému Linux. Kudu zpracovává nepřetržitá nasazení a poskytuje koncové body HTTP pro nasazení, jako je například zipdeploy.
- FTP a WebDeploy: pomocí svého [webového serveru nebo přihlašovacích údajů uživatele](deploy-configure-credentials.md)můžete soubory nahrávat [přes FTP](deploy-ftp.md) nebo WebDeploy. Tyto mechanismy nejdou přes Kudu.  

Nástroje pro nasazení, jako jsou Azure Pipelines, Jenkinse a moduly plug-in, používají jeden z těchto mechanismů nasazení.

## <a name="language-specific-considerations"></a>Doporučení pro konkrétní jazyk

### <a name="java"></a>Java

Použijte Kudu [zipdeploy/](deploy-zip.md) rozhraní API pro nasazení aplikací jar a [wardeploy/](deploy-zip.md#deploy-war-file) pro aplikace War. Pokud používáte Jenkinse, můžete tato rozhraní API používat přímo ve fázi nasazení. Další informace najdete v [tomto článku](../jenkins/execute-cli-jenkins-pipeline.md).

### <a name="node"></a>Uzel

Ve výchozím nastavení spustí Kudu kroky sestavení pro vaši aplikaci uzlu (`npm install`). Pokud používáte sestavovací službu, jako je například Azure DevOps, sestavení Kudu není nutné. Chcete-li zakázat sestavení Kudu, vytvořte nastavení aplikace `SCM_DO_BUILD_DURING_DEPLOYMENT`s hodnotou `false`.

### <a name="net"></a>.NET 

Ve výchozím nastavení Kudu provádí kroky sestavení pro vaši aplikaci .NET (`dotnet build`). Pokud používáte sestavovací službu, jako je například Azure DevOps, sestavení Kudu není nutné. Chcete-li zakázat sestavení Kudu, vytvořte nastavení aplikace `SCM_DO_BUILD_DURING_DEPLOYMENT`s hodnotou `false`.

## <a name="other-deployment-considerations"></a>Další požadavky na nasazení

### <a name="use-deployment-slots"></a>Použití slotů nasazení

Kdykoli je to možné, použijte [nasazovací sloty](deploy-staging-slots.md) při nasazení nového výrobního buildu. Pokud používáte úroveň plánu úrovně Standard App Service nebo lepší, můžete aplikaci nasadit do přípravného prostředí, ověřit své změny a provádět testy kouře. Až budete připraveni, můžete vyměnit pracovní a produkční sloty. Operace prohození zahřívá potřebné pracovní instance, aby odpovídaly vašemu produkčnímu měřítku, a tím eliminuje prostoje. 

### <a name="local-cache"></a>Místní mezipaměť

Obsah Azure App Service je uložený v Azure Storage a je vytvořen jako trvalý způsob sdílení obsahu. Některé aplikace ale potřebují jenom vysoce výkonné úložiště obsahu jen pro čtení, které můžou běžet s vysokou dostupností. Tyto aplikace můžou využívat používání [místní mezipaměti](overview-local-cache.md). Pro weby správy obsahu, jako je WordPress, se nedoporučuje místní mezipaměť.

V kombinaci s [sloty nasazení](deploy-staging-slots.md) vždy používejte místní mezipaměť, aby nedocházelo k výpadkům. V [této části](overview-local-cache.md#best-practices-for-using-app-service-local-cache) najdete informace o tom, jak tyto funkce používat dohromady.

### <a name="high-cpu-or-memory"></a>Vysoký procesor nebo paměť

Pokud váš App Service plán používá více než 90% dostupného procesoru nebo paměti, může mít základní virtuální počítač potíže se zpracováním nasazení. Pokud k tomu dojde, dočasná velikost počtu instancí proveďte v případě nasazení. Po dokončení nasazení můžete počet instancí vrátit do předchozí hodnoty.
