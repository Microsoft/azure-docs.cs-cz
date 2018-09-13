---
title: Azure Service Fabric container aplikace manifestu příklady | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat aplikace a služby manifestu nastavení pro aplikaci Service Fabric.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: xml
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/11/2018
ms.author: ryanwi
ms.openlocfilehash: 8336f0a63f74aa0db176adbb3baf462c903ba2ec
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643464"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Příklady manifest služby a aplikace Service Fabric
Tato část obsahuje příklady manifestů aplikace a služby. Tyto příklady nepoužívejte jako zobrazit důležité scénáře, ale zobrazit různá nastavení, které jsou k dispozici a jak je používat. 

Toto je index zobrazené funkce a příklad manifest(s) jsou součástí.

|Funkce|Manifest|
|---|---|
|[Zásady správného řízení prostředků](service-fabric-resource-governance.md)|[Spolehlivé manifestu aplikace služby](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [manifest aplikace typu kontejner](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Spuštění služby jako účet místního správce](service-fabric-application-runas-security.md)|[Spolehlivé manifestu aplikace služby](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Použít výchozí zásady na všechny balíčky kódu služby](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Spolehlivé manifestu aplikace služby](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Vytvoření uživatelů a skupin objektů zabezpečení](service-fabric-application-runas-security.md)|[Spolehlivé manifestu aplikace služby](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|sdílené složky balíčku dat mezi instancemi služeb|[Spolehlivé manifestu aplikace služby](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Přepsat koncové body služby](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Spolehlivé manifestu aplikace služby](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Spuštění skriptu při spuštění služby](service-fabric-run-script-at-service-startup.md)|[Manifest služby VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Definovat koncový bod HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[Manifest služby VotingWeb](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Deklarovat konfigurační balíček](service-fabric-application-and-service-manifests.md)|[Manifest služby VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Deklarovat balíček dat](service-fabric-application-and-service-manifests.md)|[Manifest služby VotingData](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Přepsání proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Konfigurace mapování portu kontejneru na hostitele](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Nakonfigurujte ověřování registru kontejneru](service-fabric-get-started-containers.md#configure-container-registry-authentication)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Nastavit režim izolace](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Určete konkrétní sestavení kontejnerové Image operačního systému](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifest aplikace kontejneru](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Nastavení proměnných prostředí](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest služby kontejneru FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [kontejneru BackEndService manifestu služby](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Konfigurace koncového bodu](service-fabric-get-started-containers.md#configure-communication)|[Manifest služby kontejneru FrontEndService](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [manifestu služby kontejneru BackEndService](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData manifestu služby](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|Předejte příkazy ke kontejneru|[Manifest služby FrontEndService kontejneru](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Import certifikátu do kontejneru](service-fabric-securing-containers.md)|[Manifest služby FrontEndService kontejneru](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Konfigurace svazku ovladače](service-fabric-containers-volume-logging-drivers.md)|[Manifest služby BackEndService kontejneru](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

