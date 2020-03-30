---
title: Příklady manifestu aplikace Azure Service Fabric
description: Přečtěte si, jak nakonfigurovat nastavení manifestu aplikací a služeb pro aplikaci Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451641"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Příklady manifestu aplikace service fabric a služby
Tato část obsahuje příklady manifestů aplikací a služeb. Tyto příklady nejsou určeny k zobrazení důležitých scénářů, ale k zobrazení různých nastavení, které jsou k dispozici a jak je používat. 

Následuje index zobrazených funkcí a ukázkových manifestů, kterých jsou součástí.

|Funkce|Manifest|
|---|---|
|[Zásady správného řízení prostředků](service-fabric-resource-governance.md)|[Manifest aplikace spolehlivých služeb](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Spuštění služby jako místního účtu správce](service-fabric-application-runas-security.md)|[Manifest aplikace spolehlivých služeb](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Použití výchozí zásady pro všechny balíčky kódu služby](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Manifest aplikace spolehlivých služeb](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Vytvořit objekty uživatelů a skupin](service-fabric-application-runas-security.md)|[Manifest aplikace spolehlivých služeb](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|sdílet datový balíček mezi instancemi služby|[Manifest aplikace spolehlivých služeb](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Přepsat koncové body služby](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Manifest aplikace spolehlivých služeb](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Spuštění skriptu při spuštění služby](service-fabric-run-script-at-service-startup.md)|[Manifest služby VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definování koncového bodu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifest služby VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Deklarovat balíček config](service-fabric-application-and-service-manifests.md)|[Manifest služby VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklarovat datový balíček](service-fabric-application-and-service-manifests.md)|[Manifest služby VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Přepsat proměnné prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurace mapování portu kontejneru na hostitele](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurace ověřování registru kontejneru](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Nastavení izolovaného režimu](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Určení iontů kontejnerů specifických pro sestavení operačního režimu](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Nastavení proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest služby FrontEndService kontejneru](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest) [, manifest služby BackEndService kontejneru](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Konfigurace koncového bodu](service-fabric-get-started-containers.md#configure-communication)|[Manifest služby FrontEndService kontejneru](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifest služby Container BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [Manifest služby VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|předat příkazy do kontejneru|[Manifest služby FrontEndService kontejneru](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Import certifikátu do kontejneru](service-fabric-securing-containers.md)|[Manifest služby FrontEndService kontejneru](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Konfigurace ovladače svazku](service-fabric-containers-volume-logging-drivers.md)|[Manifest služby BackEndService kontejneru](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

