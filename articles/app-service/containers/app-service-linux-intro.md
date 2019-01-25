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
ms.author: msangapu;yili
ms.custom: seodec18
ms.openlocfilehash: 2e75ff08acdda03c0080f49c6616274a4b031075
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54903719"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Seznámení se službou Azure App Service v Linuxu

[Azure App Service](../overview.md) je plně spravovaná výpočetní platforma, která je optimalizována pro hostování webů a webových aplikací. V případě podporovaných zásobníků aplikací můžou zákazníci službu App Service v Linuxu použít k nativnímu hostování webových aplikací v Linuxu. [Jazyky](#languages) části je uveden seznam aktuálně podporovaných zásobníků aplikací.

## <a name="languages"></a>Jazyky

App Service v Linuxu podporuje řadu integrovaných imagí za účelem zvýšení produktivity vývojářů. Pokud modul runtime, který vaše aplikace vyžaduje, není v integrovaných imagích podporovaný, tady najdete pokyny k [sestavení vlastní image Dockeru](tutorial-custom-docker-image.md) pro nasazení do služby Web App for Containers.

| Jazyk | Podporované verze |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 9.4, 10.1,10.10 |
| Java* | Tomcat 8.5, 9.0, Java SE WildFly 14 (všechny spuštěné prostředí JRE 8) |
| PHP | 5.6, 7.0, 7.2 |
| Python (Preview) | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1 |
| Ruby | 2.3 |

## <a name="deployments"></a>Nasazení

* FTP
* Místní Git
* GitHubu
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

Některé funkce, například integrace virtuálních sítí, ověřování pomocí Azure Active Directory nebo prostřednictvím třetí strany nebo rozšíření webu Kudu, ještě nejsou dostupné. Jakmile budou tyto funkce dostupné, aktualizujeme naši dokumentaci a blog, abychom vás informovali o změnách.

Služba App Service v Linuxu se podporuje pouze s plány služby úrovně [Basic, Standard a Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) a neobsahuje úroveň [Free ani Shared](https://azure.microsoft.com/pricing/details/app-service/plans/). Službu Web App for Containers nemůžete vytvořit v plánu služby App Service, který už je hostitelem služby Web Apps mimo Linux. 

Také založené na aktuálním omezením, Nekombinujte aplikace Windows a Linuxu ve stejné skupině prostředků.

## <a name="troubleshooting"></a>Řešení potíží

Pokud se aplikaci nepodaří spustit nebo pokud chcete zkontrolovat protokolování z aplikace, zkontrolujte protokoly Dockeru v adresáři LogFiles. Přístup k tomuto adresáři získáte prostřednictvím webu SCM nebo přes protokol FTP.
Pokud chcete protokolovat výstupy `stdout` a `stderr` z vašeho kontejneru, musíte povolit **Protokolování kontejneru Dockeru** v části **Diagnostické protokoly**.

![Povolení protokolování][2]

Nastavení se projeví okamžitě. App Service zjistí změnu nastavení a kontejneru automaticky restartuje za vás.

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
