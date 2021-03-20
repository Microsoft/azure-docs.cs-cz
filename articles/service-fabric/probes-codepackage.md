---
title: Sondy Azure Service Fabric
description: Postup modelování sondy živého provozu v Azure Service Fabric pomocí souborů manifestu aplikace a služby.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "82137582"
---
# <a name="liveness-probe"></a>Test živého provozu
Počínaje verzí 7,1 podporuje Azure Service Fabric mechanismus sondy pro aplikace s podporou pro [kontejnery][containers-introduction-link] . Sonda živého provozu pomáhá nahlásit živý obsah kontejnerových aplikací, který se restartuje, pokud to neodpoví rychle.
Tento článek poskytuje přehled o tom, jak definovat sondu živého provozu pomocí souborů manifestu.

Předtím, než budete pokračovat v tomto článku, se seznamte s [Service Fabric aplikačním modelem][application-model-link] a s [Service Fabric hostujícím modelem][hosting-model-link].

> [!NOTE]
> Sonda živých funkcí je podporovaná jenom pro kontejnery v režimu sítě NAT.

## <a name="semantics"></a>Sémantika
Můžete určit pouze jeden test živého provozu na kontejner a může řídit jeho chování pomocí těchto polí:

* `initialDelaySeconds`: Počáteční zpoždění v sekundách, po kterém se spustí sonda po spuštění kontejneru. Podporovaná hodnota je **int**. Výchozí hodnota je 0 a minimální hodnota je 0.

* `timeoutSeconds`: Doba v sekundách, po jejímž uplynutí se test považuje za neúspěšný, pokud se neúspěšně dokončil. Podporovaná hodnota je **int**. Výchozí hodnota je 1 a minimální hodnota je 1.

* `periodSeconds`: Doba v sekundách, po kterou se má určit frekvence testu. Podporovaná hodnota je **int**. Výchozí hodnota je 10 a minimální hodnota je 1.

* `failureThreshold`: Když máme tuto hodnotu, kontejner se restartuje. Podporovaná hodnota je **int**. Výchozí hodnota je 3 a minimální hodnota je 1.

* `successThreshold`: V případě selhání se test testu bude považovat za úspěšný, musí se pro tuto hodnotu úspěšně spustit. Podporovaná hodnota je **int**. Výchozí hodnota je 1 a minimální hodnota je 1.

V každém okamžiku může existovat maximálně jedna sonda k jednomu kontejneru. Pokud se sonda nedokončí v čase nastaveném v **timeoutSeconds**, vyčkejte a spočítejte čas na **failureThreshold**. 

Service Fabric navíc vyvolají následující [sestavy o stavu][health-introduction-link] testu na **DeployedServicePackage**:

* `OK`: Sonda se úspěšně nastavila pro hodnotu nastavenou v **successThreshold**.

* `Error`: Test **failureCount**  ==   **failureThreshold** před restartováním kontejneru.

* `Warning`: 
    * Sonda se nezdařila a **failureCount**  <  **failureThreshold**. Tato sestava stavu zůstane, dokud **failureCount** nedosáhne hodnoty nastavené v **failureThreshold** nebo **successThreshold**.
    * Po úspěchu po selhání zůstane upozornění, ale s aktualizovanými po sobě jdoucími úspěchy.

## <a name="specifying-a-liveness-probe"></a>Určení testu živého provozu

Test paměti můžete zadat v souboru ApplicationManifest.xml pod položkou **ServiceManifestImport**.

Sonda může být pro některé z těchto možností:

* HTTP
* TCP
* Průměrná 

### <a name="http-probe"></a>Test protokolu HTTP

U testu HTTP Service Fabric odešle požadavek HTTP na port a cestu, kterou zadáte. Návratový kód, který je větší než nebo roven 200 a menší než 400, indikuje úspěch.

Tady je příklad, jak určit test paměti protokolu HTTP:

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

Test HTTP má další vlastnosti, které můžete nastavit:

* `path`: Cesta, která se má použít v požadavku HTTP.

* `port`: Port, který se má použít pro sondy. Tato vlastnost je povinná. Rozsah je 1 až 65535.

* `scheme`: Schéma, které se má použít pro připojení k balíčku kódu. Pokud je tato vlastnost nastavená na HTTPS, ověření certifikátu se přeskočí. Výchozí nastavení je HTTP.

* `httpHeader`: Hlavičky, které mají být nastaveny v požadavku. Můžete zadat více hlaviček.

* `host`: IP adresa hostitele, ke které se chcete připojit.

### <a name="tcp-probe"></a>Test protokolu TCP

V případě sondy TCP se Service Fabric pokusí otevřít soket na kontejneru pomocí zadaného portu. Pokud může navázat připojení, test se považuje za úspěšný. Tady je příklad, jak zadat sondu, která používá soket TCP:

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

### <a name="exec-probe"></a>Test exec

Tato sonda vydá příkaz **exec** do kontejneru a počká, až se příkaz dokončí.

> [!NOTE]
> Příkaz **exec** přijímá řetězec oddělený čárkami. Příkaz v následujícím příkladu bude fungovat pro kontejner Linux.
> Pokud se snažíte testovat kontejner Windows, použijte **cmd**.

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
Související informace najdete v následujícím článku:
* [Service Fabric a kontejnery][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

