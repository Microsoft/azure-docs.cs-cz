---
title: Úvod do služby App Service v Linuxu – Azure | Dokumentace Microsoftu
description: Seznamte se se službou Azure App Service v Linuxu.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 6bca1b067f5ec667e8b5da92a182a5618582b2f3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2019
ms.locfileid: "67617430"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Seznámení se službou Azure App Service v Linuxu

[Azure App Service](../overview.md) je plně spravovaná výpočetní platforma, která je optimalizována pro hostování webů a webových aplikací. V případě podporovaných zásobníků aplikací můžou zákazníci službu App Service v Linuxu použít k nativnímu hostování webových aplikací v Linuxu. [Jazyky](#languages) části je uveden seznam aktuálně podporovaných zásobníků aplikací.

## <a name="languages"></a>Languages

App Service v Linuxu podporuje řadu integrovaných imagí za účelem zvýšení produktivity vývojářů. Pokud modul runtime, který vaše aplikace vyžaduje, není v integrovaných imagích podporovaný, tady najdete pokyny k [sestavení vlastní image Dockeru](tutorial-custom-docker-image.md) pro nasazení do služby Web App for Containers.

| Jazyk | Podporované verze |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 8.12, 9.4, 10.1, 10.10, 10.14 |
| Java* | Tomcat 8.5, 9.0, Java SE WildFly 14 (všechny spuštěné prostředí JRE 8) |
| PHP | 5.6, 7.0, 7.2, 7.3 |
| Python | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1, 2.2 |
| Ruby | 2.3, 2.4, 2.5, 2.6 |

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

App Service v Linuxu se podporuje jen při [Free, Basic, Standard a Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) plány služby app service a nemá [Shared](https://azure.microsoft.com/pricing/details/app-service/plans/) vrstvy. Nelze vytvořit Linuxovou webovou aplikaci v plánu služby App Service, který už je hostitelem jiných Linuxové webové aplikace.  

Podle aktuální omezení pro stejnou skupinu prostředků nejde kombinovat aplikace Windows a Linuxu ve stejné oblasti.

## <a name="troubleshooting"></a>Řešení potíží

Pokud se aplikaci nepodaří spustit nebo pokud chcete zkontrolovat protokolování z aplikace, zkontrolujte protokoly Dockeru v adresáři LogFiles. Přístup k tomuto adresáři získáte prostřednictvím webu SCM nebo přes protokol FTP. Do protokolu `stdout` a `stderr` z vašeho kontejneru, je potřeba povolit **protokolování kontejneru Dockeru** pod **protokoly služby aplikace**. Nastavení se projeví okamžitě. Služby App Service změnu zjistí a automaticky restartuje kontejner.

Na web SCM můžete přejít přes možnost **Rozšířené nástroje** v nabídce **Vývojové nástroje**.

![Zobrazení protokolů Dockeru pomocí Kudu][1]

## <a name="next-steps"></a>Další postup

Tyto články vám pomůžou začít používat službu App Service v Linuxu pomocí webových aplikací napsaných v různých jazycích:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Vícekontejnerové aplikace](quickstart-multi-container.md)

Další informace o službě App Service v Linuxu najdete v tématu:

* [Nejčastější dotazy k App Service pro Linux](app-service-linux-faq.md)
* [Podpora SSH pro službu App Service v Linuxu](app-service-linux-ssh-support.md)
* [Nastavení přípravných prostředí ve službě App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Průběžné nasazování pomocí Centra Dockeru](app-service-linux-ci-cd.md)

Otázky a připomínky můžete publikovat na [našem fóru](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
