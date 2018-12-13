---
title: Nasadit vlastní image, více kontejnerů nebo integrované image - službě Azure App Service | Dokumentace Microsoftu
description: Jak se rozhodnout mezi vlastní nasazení kontejneru Dockeru, více kontejnerů a architektura integrované aplikace pro App Service v Linuxu
keywords: služby Azure app service, webové aplikace, linux a oss
services: app-service
documentationCenter: ''
authors: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: d53fc8b3971a1003b4f5d9b9e52f86ee73829cc2
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315793"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Vlastní image, více kontejnerů nebo image integrované platformy?

[App Service v Linuxu](app-service-linux-intro.md) nabízí tři různé cesty k zařazení vaší aplikace publikována na webu:

- **Nasazení vlastní image**: "Ukotvovat" vaší aplikace do image Dockeru, který obsahuje všechny soubory a závislosti v balíčku připravené ke spuštění.
- **Nasazení vícekontejnerových**: "Ukotvovat" vaší aplikace napříč více kontejnery pomocí Docker Compose nebo konfigurační soubor Kubernetes. Další informace najdete v tématu [vícekontejnerová aplikace](#multi-container-apps-supportability).
- **Nasazení aplikace s integrovanou platformu imagí**: Naše integrované platformy Image obsahují běžných modulů runtime webové aplikace a závislosti, jako je například Node a PHP. Použít libovolný z [metody nasazení služby Azure App Service](../app-service-deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) k nasazení aplikace do webové aplikace úložiště a pak ji spustit pomocí image integrované platformy.

## <a name="which-method-is-right-for-your-app"></a>Jakou metodu je nejvhodnější pro vaši aplikaci? 

Primárními faktory vzít v úvahu, jsou:

- **Dostupnost dockeru v pracovním postupu vývoje**: Vývoj vlastní image vyžaduje základní znalost Dockeru pracovního postupu vývoje. Nasazení vlastní image do služby web app vyžaduje zveřejnění vlastní image do úložiště hostitele, jako je Docker Hubu. Pokud jste obeznámeni s prostředím Docker a může přidat úkoly Dockeru do pracovního postupu sestavení nebo pokud jsou již publikování vaší aplikace jako image Dockeru, vlastní image je téměř jistě nejlepší volbou.
- **Víceúrovňová architektura**: Nasazení několika kontejnerů, jako jsou webové aplikační vrstvu a vrstvu rozhraní API k oddělení možností s využitím více kontejnerů. 
- **Výkon aplikace**: Zvýšení výkonu vícekontejnerové aplikace pomocí mezipaměti vrstvy, jako jsou Redis. Vyberte více kontejnerů toho dosáhnout.
- **Požadavky na modul runtime jedinečný**: Integrovaná platforma Image jsou určeny k uspokojení potřeb většiny webových aplikací, ale mají omezenou jejich přizpůsobitelnost. Vaše aplikace může mít jedinečné závislosti nebo jiné požadavky na modul runtime, které překračují, co jsou schopny integrované Image.
- **Požadavky na sestavení**: S [průběžné nasazování](../app-service-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), můžete získat vaší aplikace do provozu v Azure přímo ze zdrojového kódu. Vyžaduje se bez externího procesu sestavení nebo publikace. Existuje ale omezení přizpůsobitelnost a dostupnost nástroje sestavení v rámci [Kudu](https://github.com/projectkudu/kudu/wiki) stroj pro nasazení. Vaše aplikace může velký růst Kudu pro možnosti, roste v jeho závislostí nebo požadavků pro logiku vlastního sestavení.
- **Požadavky na čtení/zápis disku**: Všechny webové aplikace se přidělují svazek úložiště pro webový obsah. Tento svazek se opírá o Azure Storage, je připojený k `/home` v systému souborů aplikace. Na rozdíl od souborů v systému souborů kontejneru soubory obsahu hromadně jsou přístupné napříč všemi instancemi škálování aplikace a změny se zachová napříč restartování aplikace. Ale je vyšší latence obsahu svazku disku a další proměnnou než latence místního kontejneru systému souborů a přístupu k nim může mít vliv platformy upgrady, neplánované výpadky a problémy se síťovým připojením. Aplikace, které vyžadují náročné přístup jen pro čtení souborů obsahu můžou mít užitek z nasazení vlastní image, které umístí soubory bitové kopie systému souborů namísto na obsahu svazku.
- **Sestavení využití prostředků**: Když je aplikace nasazená ze zdroje, skripty nasazení spustit pomocí Kudu stejného plánu služby App Service úložnou a výpočetní prostředky jako spuštěné aplikaci. Nasazení velkých aplikací může využívat další prostředky nebo dobu, než požadovaný. Konkrétně se mnoho pracovní postupy nasazení generovat aktivitu náročné disku na svazku obsahu aplikace, které není optimalizovaná pro takové činnosti. Vlastní image všechny soubory a závislosti vaší aplikace přináší do Azure v jediném balíčku bez nutnosti další soubor či akce související s nasazením.
- **Pro rychlé iterace potřebovat**: Dockerizing aplikace vyžaduje další kroky sestavení. Změny se projeví musí nahrání nové image do úložiště při každé z nich. Tyto aktualizace jsou pak dali do prostředí Azure. Pokud jeden z předdefinovaných kontejnerů splňuje potřeby vašich aplikací, nasazení ze zdroje můžou nabízet rychlejší pracovního postupu vývoje.

## <a name="multi-container-apps-supportability"></a>Možnosti podpory vícekontejnerových aplikací

### <a name="supported-docker-compose-configuration-options"></a>Podporované možnosti konfigurace Docker Compose
- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

### <a name="unsupported-docker-compose-configuration-options"></a>Nepodporované možnosti konfigurace Docker Compose
- build (nepovoleno)
- depends_on (ignorováno)
- networks (ignorováno)
- secrets (ignorováno)
- jiné porty než 80 a 8080 (ignorováno)

> [!NOTE]
> Jakékoli jiné možnosti, které nejsou explicitně volané, se ve veřejné verzi Preview také ignorují.

### <a name="supported-kubernetes-configuration-options"></a>Podporované možnosti konfigurace Kubernetes
- args
- command
- containers
- image
- jméno
- ports
- spec

> [!NOTE]
>Jakékoli jiné možnosti Kubernetes, které nejsou explicitně volané, se ve veřejné verzi Preview nepodporují.
>
