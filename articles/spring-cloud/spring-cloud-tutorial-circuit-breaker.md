---
title: 'Kurz: Použití řídicího panelu pro dělení na okruhy pomocí Azure jaře cloudu'
description: Naučte se používat řídicí panel pro přerušení okruhů se službou Azure jaře Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java
ms.openlocfilehash: c189411b13baf2497f0752c15550dd419f88f754
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088597"
---
# <a name="use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Použití řídicího panelu pro dělení na okruhy pomocí Azure jarního cloudu

**Tento článek se týká:** ✔️ Java

::: zone pivot="programming-language-java"
Jarní [cloudová Netflix turbína](https://github.com/Netflix/Turbine) se běžně používá k agregaci více datových proudů [hystrix](https://github.com/Netflix/Hystrix) metrik, aby bylo možné datové proudy sledovat v jednom zobrazení pomocí řídicího panelu hystrix. V tomto kurzu se dozvíte, jak je používat v Azure jarním cloudu.
> [!NOTE]
> Netflix hystrix se běžně používá v mnoha stávajících jarních cloudových aplikacích, ale už není v aktivním vývoji. Pokud vyvíjíte nový projekt, použijte místo toho implementace přerušení cloudového okruhu, jako je [resilience4j](https://github.com/resilience4j/resilience4j). V rámci tohoto kurzu se liší od turbíny, ale nová architektura pro vypínacích okruhů v cloudovém prostředí sjednocuje všechny implementace kanálu dat metrik do mikroměřiče. Pořád pracujeme na podpoře mikroměřiče v Azure jaře cloudu, takže se tento kurz nezabývá.

## <a name="prepare-your-sample-applications"></a>Příprava ukázkových aplikací
Ukázka je rozvětvená z tohoto [úložiště](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine).

Naklonujte ukázkové úložiště do vývojového prostředí:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Sestavte 3 aplikace, které se použijí v tomto kurzu:
* User-Service: jednoduchá služba REST s jedním koncovým bodem/Personalized/{ID}
* doporučení – služba: jednoduchá služba REST s jedním koncovým bodem/Recommendations, který bude volat služba User-Service.
* hystrix-turbína: služba hystrix Dashboard pro zobrazení datových proudů hystrix a službu turbíny, která agreguje datový proud hystrix metriky z jiných služeb.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Zřízení instance Azure jaře cloudu
Použijte postup a v [Azure CLI zřiďte instanci služby](./spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Nasazení vašich aplikací do jarního cloudu Azure
Tyto aplikace nepoužívají **konfigurační server**, takže není nutné nastavovat **konfigurační server** pro jarní cloud Azure.  Vytvořte a nasaďte následujícím způsobem:
```azurecli
az spring-cloud app create -n user-service --is-public
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --is-public

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Ověření aplikací
Až budou všechny aplikace spuštěné a zjistitelný, přístup k `user-service` cestě https:// <username> -User-Service.azuremicroservices.IO/Personalized/1 z prohlížeče. Pokud má uživatel-služba přístup `recommendation-service` , měli byste získat následující výstup. Aktualizujte webovou stránku několikrát, pokud nefunguje.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Přístup ke streamu hystrix a datovým proudem metrik
Ověřte použití veřejných koncových bodů nebo privátních koncových bodů testu.

### <a name="using-public-endpoints"></a>Použití veřejných koncových bodů
Přístup k hystrix-turbíně s cestou `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` z prohlížeče.  Následující obrázek ukazuje řídicí panel hystrix běžící v této aplikaci.

![Řídicí panel hystrix](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Do textového pole zkopírujte adresu URL datového proudu turbíny `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` a klikněte na **sledovat Stream**.  Tím se zobrazí řídicí panel. Pokud se v prohlížeči nic nezobrazuje, přihlaste se `user-service` ke koncovým bodům a vygenerujte streamy.

![Hystrix Stream ](media/spring-cloud-circuit-breaker/hystrix-stream.png) teď můžete experimentovat s řídicím panelem pro dělení na okruh.
> [!NOTE] 
> V produkčním prostředí by se řídicí panel hystrix a datový proud metrik neměl zveřejnit na internetu.

### <a name="using-private-test-endpoints"></a>Používání privátních koncových bodů testu
Datové proudy metrik hystrix jsou také přístupné z `test-endpoint` . Jako back-end služba jsme nepřiřadili veřejný koncový bod pro `recommendation-service` , ale můžeme zobrazit své metriky pomocí testovacího koncového bodu na adrese `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream`

![Datový proud hystrix testovacího koncového bodu](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Jako webová aplikace by měl řídicí panel hystrix pracovat `test-endpoint` . Pokud nefunguje správně, může dojít k dvěma důvodům: první, při použití `test-endpoint` změny základní adresy URL z `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` , nebo za sekundu používá webová aplikace absolutní cestu pro statický prostředek. Chcete-li ho využít `test-endpoint` , budete možná muset ručně upravit <base> v front-endové soubory.

## <a name="next-steps"></a>Další kroky
* [Zřízení instance služby v Azure CLI](./spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Příprava pružinové aplikace Java pro nasazení v jarním cloudu Azure](./spring-cloud-tutorial-prepare-app-deployment.md)
::: zone-end