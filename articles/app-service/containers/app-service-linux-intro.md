---
title: Spuštění kódu ve výchozích kontejnerech Linuxu
description: Azure App Service můžete spustit váš kód na předem sestavené linuxové kontejnery. Zjistěte, jak můžete v Azure spouštět linuxové webové aplikace.
keywords: azure app service, linux, oss
author: msangapu-msft
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 27f085543869b1a77db9c97ca2e7ae7d3d3b7b88
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80046421"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Seznámení se službou Azure App Service v Linuxu

[Azure App Service](../overview.md) je plně spravovaná výpočetní platforma, která je optimalizovaná pro hostování webů a webových aplikací. V případě podporovaných zásobníků aplikací můžou zákazníci službu App Service v Linuxu použít k nativnímu hostování webových aplikací v Linuxu.

## <a name="languages"></a>Jazyky

App Service v Linuxu podporuje řadu integrovaných imagí za účelem zvýšení produktivity vývojářů. Mezi jazyky patří: Node.js, Java (JRE 8 & JRE 11), PHP, Python, .NET Core a Ruby. Spuštěním [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) zobrazíte nejnovější jazyky a podporované verze. Pokud modul runtime, který vaše aplikace vyžaduje, není v integrovaných imagích podporovaný, tady najdete pokyny k [sestavení vlastní image Dockeru](tutorial-custom-docker-image.md) pro nasazení do služby Web App for Containers.

## <a name="deployments"></a>Nasazení

* FTP
* Místní Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Přípravná prostředí
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) a DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Konzola, publikování a ladění

* Prostředí
* Nasazení
* Základní konzola
* SSH

## <a name="scaling"></a>Škálování

* Zákazníci můžou vertikálně navyšovat nebo snižovat kapacitu aplikací změnou úrovně [plánu služby App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Umístění

Podívejte se na [řídicí panel stavu Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Omezení

Na webu Azure Portal se zobrazují pouze funkce, které jsou aktuálně funkční pro službu Web App for Containers. Další funkce se na portálu budou zobrazovat s tím, jak je budeme povolovat.

Služba App Service na Linuxu je podporovaná jenom s plány [bezplatné, základní, standardní a prémiové](https://azure.microsoft.com/pricing/details/app-service/plans/) služby aplikace a nemá [sdílenou](https://azure.microsoft.com/pricing/details/app-service/plans/) úroveň. Linux Web App nelze vytvořit v plánu služby App Service, který již hostuje jiné než Linux Web Apps.  

Na základě aktuálního omezení pro stejnou skupinu prostředků nelze kombinovat windows a linuxové aplikace ve stejné oblasti.

## <a name="troubleshooting"></a>Řešení potíží

> [!NOTE]
> Je tu nová integrovaná funkce protokolování s [Azure Monitoring (preview)](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs#send-logs-to-azure-monitor-preview) . 
>
>

Pokud se aplikaci nepodaří spustit nebo pokud chcete zkontrolovat protokolování z aplikace, zkontrolujte protokoly Dockeru v adresáři LogFiles. Přístup k tomuto adresáři získáte prostřednictvím webu SCM nebo přes protokol FTP. Chcete-li `stdout` `stderr` protokolovat a z kontejneru, musíte povolit **protokolování aplikací** v **protokolech služby App Service**. Nastavení se projeví okamžitě. Služba App Service zjistí změnu a automaticky restartuje kontejner.

Na web SCM můžete přejít přes možnost **Rozšířené nástroje** v nabídce **Vývojové nástroje**.

![Zobrazení protokolů Dockeru pomocí Kudu][1]

## <a name="next-steps"></a>Další kroky

Tyto články vám pomůžou začít používat službu App Service v Linuxu pomocí webových aplikací napsaných v různých jazycích:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Přejít](quickstart-docker-go.md)
* [Vícekontejnerové aplikace](quickstart-multi-container.md)

Další informace o službě App Service na Linuxu najdete v tématu:

* [Nejčastější dotazy k App Service pro Linux](app-service-linux-faq.md)
* [Podpora SSH pro službu App Service v Linuxu](app-service-linux-ssh-support.md)
* [Nastavení přípravných prostředí ve službě App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Průběžné nasazování pomocí Centra Dockeru](app-service-linux-ci-cd.md)

Otázky a připomínky můžete publikovat na [našem fóru](https://docs.microsoft.com/answers/topics/azure-webapps.html).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
