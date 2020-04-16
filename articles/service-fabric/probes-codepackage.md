---
title: Azure Service Fabric sondy
description: Jak modelovat liveness sonda v Azure Service Fabric pomocí souborů manifestu aplikací a služeb.
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431212"
---
# <a name="liveness-probe"></a>Liveness Sonda
Počínaje 7.1 Service Fabric podporuje liveness sonda mechanismus pro [kontejnerizované][containers-introduction-link] aplikace. Liveness Probe pomáhají oznámit živost kontejnerizované aplikace a když nereagují včas, bude to mít za následek restartování.
Tento článek obsahuje přehled o tom, jak definovat sondu Liveness prostřednictvím souborů manifestu.

Než budete pokračovat v tomto článku, doporučujeme seznámit se s [modelem aplikace Service Fabric][application-model-link] a [modelhostování service fabric][hosting-model-link].

> [!NOTE]
> Sonda liveness je podporována pouze pro kontejnery v síťovém režimu NAT.

## <a name="semantics"></a>Sémantika
Můžete zadat pouze 1 sondu živosti na kontejner a můžete řídit jeho chování pomocí těchto polí:

* `initialDelaySeconds`: Počáteční zpoždění v sekundách spustit provádění sondy po spuštění kontejneru. Podporovaná hodnota je int. Výchozí hodnota je 0. Minimum je 0.

* `timeoutSeconds`: Období v sekundách, po kterém považujeme sondu za neúspěšnou, pokud nebyla úspěšně dokončena. Podporovaná hodnota je int. Výchozí hodnota je 1. Minimum je 1.

* `periodSeconds`: Tečka v sekundách určit, jak často jsme sonda. Podporovaná hodnota je int. Výchozí hodnota je 10. Minimum je 1.

* `failureThreshold`: Jakmile narazíme na FailureThreshold, kontejner se restartuje. Podporovaná hodnota je int. Výchozí hodnota je 3. Minimum je 1.

* `successThreshold`: Při selhání, pro sondu, která má být považována za úspěch, musí úspěšně spustit pro SuccessThreshold. Podporovaná hodnota je int. Výchozí hodnota je 1. Minimum je 1.

V jednom okamžiku bude k dispozici maximálně 1 sonda do kontejneru. Pokud sonda nedokončí v **timeoutSeconds** budeme čekat a počítání do **failureThreshold**. 

Kromě toho ServiceFabric vyvolá následující [sestavy stavu][health-introduction-link] sondy na DeployedServicePackage:

* `Ok`: Pokud sonda uspěje pro **successThreshold** pak hlásíme stav jako Ok.

* `Error`: Pokud sonda failureCount == **failureThreshold**, před restartováním kontejneru hlásíme chybu.

* `Warning`: 
    1. Pokud sonda selže a failureCount < **failureThreshold** hlásíme Upozornění. Tato sestava stavu zůstane, dokud failureCount dosáhne **failureThreshold** nebo **successThreshold**.
    2. Na úspěch post selhání, stále hlásíme varování, ale s aktualizovaným po sobě jdoucí úspěch.

## <a name="specifying-liveness-probe"></a>Určení sondy živost

Sondu můžete zadat v applicationManifest.xml v části ServiceManifestImport:

Sonda může buď jeden z :

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>Sonda HTTP

Pro sondu HTTP service fabric odešle požadavek HTTP na port a zadanou cestu. Návratový kód větší nebo rovno 200 a menší než 400 označuje úspěch.

Zde je příklad, jak zadat httpget sonda:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HttpGet sonda má další vlastnosti, které můžete nastavit:

* `path`: Cesta k přístupu na požadavek HTTP.

* `port`: Port pro přístup pro sondy. Rozsah je 1 až 65535. Povinné.

* `scheme`: Schéma pro připojení k balíčku kódu. Pokud je nastavena na HTTPS, ověření certifikátu je přeskočeno. Výchozí hodnota http

* `httpHeader`: Záhlaví nastavit v požadavku. Můžete zadat více z nich.

* `host`: Hostitelská IP adresa pro připojení.

## <a name="tcp-probe"></a>Sonda TCP

Pro sondu TCP service fabric se pokusí otevřít soket na kontejneru se zadaným portem. Pokud může navázat spojení, sonda je považována za úspěch. Zde je příklad, jak určit sondu, která používá soket TCP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>Sonda Exec

Tato sonda vydá exec do kontejneru a čekat na dokončení příkazu.

> [!NOTE]
> Příkaz Exec přebírá řetězec osvojovaný čárkou. Následující příkaz v příkladu bude fungovat pro kontejner Linux.
> Pokud zkoušíte windows kontejner, použijte <Command>cmd</Command>

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Další kroky
Související informace naleznete v následujících článcích.
* [Service Fabric a kontejnery.][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

