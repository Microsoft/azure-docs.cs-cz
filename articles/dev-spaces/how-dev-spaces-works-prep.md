---
title: Jak funguje příprava projektu pro Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje, jak funguje příprava projektu pomocí Azure Dev Spaces.
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 24a54fffdc8e94493d2a4a9aeb1c5f02dcd192b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241631"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Jak funguje příprava projektu pro Azure Dev Spaces

Azure Dev Spaces vám nabízí několik způsobů, jak rychle itetovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Služby Azure Kubernetes (AKS). Dev Spaces můžete generovat Dockerfiles a Helm grafy pro váš projekt. Dev Spaces také vytvoří a používá konfigurační soubor pro nasazení, spuštění a ladění aplikací Kubernetes v AKS. Všechny tyto soubory jsou umístěny s kódem aplikace a mohou být přidány do systému správy verzí.

Tento článek popisuje, co se stane připravit projekt pro spuštění v AKS s Dev Spaces.

## <a name="prepare-your-code"></a>Příprava kódu

Chcete-li spustit aplikaci v prostoru pro vývoj, je třeba kontejnerizovat a je třeba definovat, jak by měla být nasazena do Kubernetes. Chcete-li kontejnerizovat aplikace, budete potřebovat Dockerfile. Chcete-li definovat, jak je aplikace nasazena do Kubernetes, potřebujete [graf Helm](https://docs.helm.sh/). Chcete-li pomoci při vytváření dockerfile a helm graf pro vaši `prep` aplikaci, nástroje na straně klienta poskytují příkaz:

```cmd
azds prep --enable-ingress
```

Příkaz `prep` se podíváme na soubory v projektu a pokuste se vytvořit Dockerfile a Helm graf pro spuštění aplikace v Kubernetes. V současné `prep` době příkaz vygeneruje graf Dockerfile a Helm s následujícími jazyky:

* Java
* Node.js
* .NET Core

Příkaz *je nutné* spustit z adresáře, který obsahuje zdrojový kód. `prep` Spuštění `prep` příkazu ze správného adresáře umožňuje nástrojům na straně klienta identifikovat jazyk a vytvořit vhodný soubor Dockerfile pro kontejnerizaci aplikace. `prep` Příkaz můžete spustit také z adresáře, který obsahuje soubor *pom.xml* pro projekty jazyka Java.

Pokud spustíte `prep` příkaz z adresáře, který neobsahuje zdrojový kód, nástroje na straně klienta nebudou generovat soubor Dockerfile. Zobrazí se také chyba, která říká: *Dockerfile nelze vygenerovat z důvodu nepodporovaného jazyka*. K této chybě dochází také v případě, že nástroje na straně klienta nerozpozná typ projektu.

Při spuštění `prep` příkazu máte možnost zadat `--enable-ingress` příznak. Tento příznak říká kontroleru vytvořit koncový bod přístupný internetu pro tuto službu. Pokud tento příznak nezadáte, služba je přístupná pouze z clusteru nebo pomocí tunelu localhost vytvořeného nástroji na straně klienta. Toto chování můžete povolit `prep` nebo zakázat po spuštění příkazu aktualizací generovaného grafu helmu.

Příkaz `prep` nenahradí žádné existující Dockerfiles nebo Helm grafy, které máte v projektu. Pokud existující dockerfile nebo helm graf používá stejnou konvenci pojmenování jako soubory generované příkazem, `prep` `prep` příkaz přeskočí generování těchto souborů. V opačném `prep` případě příkaz vygeneruje svůj vlastní Dockerfile nebo Helm graf spolu s existujícísoubory.

> [!IMPORTANT]
> Azure Dev Spaces používá Dockerfile a Helm graf pro váš projekt k sestavení a spuštění kódu, ale můžete upravit tyto soubory, pokud chcete změnit způsob sestavení a spuštění projektu.

Příkaz `prep` také vygeneruje `azds.yaml` soubor v kořenovém adresáři projektu. Azure Dev Spaces používá tento soubor k sestavení, instalaci, konfiguraci a spuštění aplikace. Tento konfigurační soubor uvádí umístění dockerfile a helm grafu a také poskytuje další konfiguraci nad tyto artefakty.

Zde je příklad souboru azds.yaml vytvořeného pomocí [ukázkové aplikace .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

Soubor `azds.yaml` generovaný `prep` příkazem je určen pro jednoduchý, jeden scénář vývoje projektu. Pokud váš konkrétní projekt zvýšil složitost, bude pravděpodobně nutné `prep` aktualizovat tento soubor po spuštění příkazu. Projekt může například vyžadovat některé změny procesu sestavení nebo spuštění na základě vašich potřeb vývoje nebo ladění. Můžete mít také více aplikací v projektu, které vyžadují více procesů sestavení nebo jiný obsah sestavení.

## <a name="next-steps"></a>Další kroky

Další informace o spuštění kódu v prostoru pro spuštění najdete v tématu [Jak funguje spuštění kódu s Azure Dev Spaces][how-it-works-up].

Pokud chcete začít používat Azure Dev Spaces k přípravě projektu pro Azure Dev Space, podívejte se na následující rychlé starty:

* [Rychlé itereje a ladění pomocí kódu Visual Studio a Javy][quickstart-java]
* [Rychlé itered a ladění pomocí kódu Sady Visual Studio a rozhraní .NET][quickstart-netcore]
* [Rychlé itered a ladění pomocí kódu sady Visual Studio a souboru Node.js][quickstart-node]
* [Rychlé itered a ladění pomocí sady Visual Studio a .NET Core][quickstart-vs]
* [Použití CLI k vývoji aplikace na Kubernetes][quickstart-cli]

[how-it-works-up]: how-dev-spaces-works-up.md
[quickstart-cli]: quickstart-cli.md
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md