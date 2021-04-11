---
title: Povolit kompletní přenosovou vrstvu zabezpečení
titleSuffix: Azure Spring Cloud
description: Jak povolit komplexní zabezpečení transportní vrstvy pro aplikaci.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227802"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>Povolení kompletního TLS pro aplikaci

V tomto tématu se dozvíte, jak povolit kompletní protokol SSL/TLS pro zabezpečení provozu z kontroleru příchozího přenosu dat do aplikací, které podporují protokol HTTPS. Po povolení kompletního protokolu TLS a načtení certifikátu z trezoru klíčů jsou veškerá komunikace v rámci Azure jaře cloudu zabezpečená pomocí protokolu TLS.

   ![Graf komunikace zabezpečený protokolem TLS.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Požadavky 

- Nasazená instance cloudu Azure pro jaře. Začněte podle našeho [rychlého startu v části nasazení prostřednictvím Azure CLI](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) .
- Pokud nejste obeznámeni s komplexním protokolem TLS, přečtěte si [kompletní ukázku protokolu TLS](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl).
- K bezpečnému načtení požadovaných certifikátů do aplikací pro jarní spouštění můžete použít [jaře Boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates).


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>Povolení kompletního protokolu TLS u stávající aplikace

`az spring-cloud app update --enable-end-to-end-tls`K povolení nebo zakázání komplexního TLS pro aplikaci použijte příkaz.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>Při vázání vlastní domény povolit kompletní protokol TLS

`az spring-cloud app custom-domain update --enable-end-to-end-tls` `az spring-cloud app custom-domain bind --enable-end-to-end-tls` K povolení nebo zakázání komplexního TLS pro aplikaci použijte příkaz.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>Ověřit stav kompletního protokolu TLS

Pomocí příkazu `az spring-cloud app show` ověřte hodnotu `enableEndToEndTls` .
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Další kroky
* [Přístup ke konfiguračnímu serveru a registru služby](how-to-access-data-plane-azure-ad-rbac.md)
