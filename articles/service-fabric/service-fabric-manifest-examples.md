---
title: Příklady manifestu aplikace Azure Service Fabric
description: Naučte se konfigurovat nastavení manifestu aplikace a služby pro Service Fabric aplikaci.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "75451641"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Příklady manifestu aplikace a služby Service Fabric
Tato část obsahuje příklady manifestů aplikace a služby. Tyto příklady nejsou určeny k zobrazení důležitých scénářů, ale k zobrazení různých nastavení, která jsou k dispozici a jak je používat. 

Následuje index zobrazených funkcí a ukázkových manifestů, které jsou součástí.

|Funkce|Manifest|
|---|---|
|[Zásady správného řízení prostředků](service-fabric-resource-governance.md)|[Reliable Services manifestu aplikace](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [manifestu aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Spusťte službu jako účet místního správce.](service-fabric-application-runas-security.md)|[Reliable Services manifest aplikace](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Použít výchozí zásadu na všechny balíčky kódu služby](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services manifest aplikace](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Vytváření objektů zabezpečení uživatelů a skupin](service-fabric-application-runas-security.md)|[Reliable Services manifest aplikace](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|sdílení balíčku dat mezi instancemi služby|[Reliable Services manifest aplikace](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Přepsat koncové body služby](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services manifest aplikace](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Spustit skript při spuštění služby](service-fabric-run-script-at-service-startup.md)|[Manifest služby VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definování koncového bodu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifest služby VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Deklarace konfiguračního balíčku](service-fabric-application-and-service-manifests.md)|[Manifest služby VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklarace balíčku dat](service-fabric-application-and-service-manifests.md)|[Manifest služby VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Přepsat proměnné prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurace mapování portů kontejneru na hostitele](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurace ověřování registru kontejneru](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Nastavit režim izolace](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Zadat image kontejneru specifické pro sestavení operačního systému](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Nastavení proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|Manifest [služby kontejneru FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifest služby kontejneru BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Konfigurace koncového bodu](service-fabric-get-started-containers.md#configure-communication)|Manifest služby [kontejnerů FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), manifest služby [kontejneru BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), manifest [služby VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|předání příkazů do kontejneru|[Manifest služby kontejneru FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Import certifikátu do kontejneru](service-fabric-securing-containers.md)|[Manifest služby kontejneru FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Konfigurace ovladače svazku](service-fabric-containers-volume-logging-drivers.md)|[Manifest služby kontejneru BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

