---
title: Příprava projektu na Azure Dev Spaces funguje
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje, jak připravit projekt pomocí Azure Dev Spaces funguje.
keywords: azds. yaml, Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Containers
ms.openlocfilehash: d2da69dd8a8c2683ff584dfd0ffc61cb023f2ece
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91968153"
---
# <a name="how-preparing-a-project-for-azure-dev-spaces-works"></a>Příprava projektu na Azure Dev Spaces funguje

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces poskytuje několik způsobů, jak rychle iterovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Azure Kubernetes Service (AKS). Vývojové prostory mohou generovat fázemi a Helm grafy pro váš projekt. Vývojové prostory také vytváří a využívají konfigurační soubor pro nasazení, spuštění a ladění aplikací Kubernetes v AKS. Všechny tyto soubory jsou umístěny v kódu vaší aplikace a lze je přidat do systému správy verzí.

Tento článek popisuje, co se stane, když připravíte projekt pro běh v AKS s využitím vývojových prostorů.

## <a name="prepare-your-code"></a>Příprava kódu

Aby bylo možné aplikaci spustit ve vývojovém prostoru, je nutné ji zařadit do kontejneru a je třeba definovat, jak by měla být nasazena do Kubernetes. K kontejnerizaceí vaší aplikace potřebujete souboru Dockerfile. K definování způsobu nasazení aplikace na Kubernetes potřebujete [graf Helm](https://docs.helm.sh/). Pro pomoc při vytváření grafu souboru Dockerfile i Helm pro vaši aplikaci poskytují nástroje na straně klienta `prep` příkaz:

```cmd
azds prep --enable-ingress
```

`prep`Příkaz se podívá na soubory v projektu a pokusí se vytvořit graf souboru Dockerfile a Helm pro spuštění aplikace v Kubernetes. V současné době `prep` příkaz vytvoří graf souboru Dockerfile a Helm s následujícími jazyky:

* Java
* Node.js
* .NET Core

Příkaz *musíte* spustit `prep` z adresáře, který obsahuje zdrojový kód. Spuštění `prep` příkazu ze správného adresáře umožňuje nástrojům na straně klienta identifikovat jazyk a vytvořit odpovídající souboru Dockerfile pro kontejnerizace aplikace. Můžete také spustit `prep` příkaz z adresáře, který obsahuje soubor *pom.xml* pro projekty Java.

Pokud spustíte `prep` příkaz z adresáře, který neobsahuje zdrojový kód, nástroje na straně klienta nebudou generovat souboru Dockerfile. Zobrazí se také chyba s oznámením, že *souboru Dockerfile nebylo možné vygenerovat z důvodu nepodporovaného jazyka*. K této chybě dochází také v případě, že nástroje na straně klienta nerozpoznají typ projektu.

Při spuštění `prep` příkazu máte možnost zadat `--enable-ingress` příznak. Tento příznak oznamuje řadiči, aby vytvořil koncový bod přístupný z Internetu pro tuto službu. Pokud tento příznak nezadáte, služba je přístupná jenom v rámci clusteru nebo pomocí tunelového připojení localhost vytvořeného nástroji na straně klienta. Můžete povolit nebo zakázat toto chování po spuštění `prep` příkazu aktualizací generovaného grafu Helm.

`prep`Příkaz nenahradí žádné existující grafy fázemi nebo Helm, které máte v projektu. Pokud existující souboru Dockerfile nebo Helm graf používá stejné konvence vytváření názvů jako soubory generované `prep` příkazem, `prep` příkaz tyto soubory přeskočí. V opačném případě `prep` příkaz vytvoří svůj vlastní souboru Dockerfile nebo Helm graf vedle sebe stávající soubory.

> [!IMPORTANT]
> Azure Dev Spaces používá k sestavení a spuštění kódu graf souboru Dockerfile a Helm pro váš projekt, ale tyto soubory lze změnit, pokud chcete změnit způsob sestavení a spuštění projektu.

`prep`Příkaz také vygeneruje `azds.yaml` soubor v kořenu projektu. Azure Dev Spaces používá tento soubor k sestavení, instalaci, konfiguraci a spuštění vaší aplikace. Tento konfigurační soubor obsahuje seznam umístění vašeho grafu souboru Dockerfile a Helm a také poskytuje další konfiguraci nad těmito artefakty.

Tady je příklad souboru azds. yaml vytvořeného pomocí [ukázkové aplikace .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

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

`azds.yaml`Soubor vygenerovaný `prep` příkazem je určen pro použití jednoduchého a jednoduchého vývoje projektu. Pokud váš konkrétní projekt zvyšuje složitost, možná budete muset tento soubor po spuštění `prep` příkazu aktualizovat. Například váš projekt může vyžadovat některé změny v procesu sestavení nebo spuštění na základě potřeb vývoje nebo ladění. V projektu může být také více aplikací, které vyžadují více procesů sestavení nebo jiný obsah sestavení.

## <a name="next-steps"></a>Další kroky

Další informace o spuštění kódu ve vývojovém prostoru naleznete v tématu [jak běží váš kód s Azure dev Spaces pracuje][how-it-works-up].

[how-it-works-up]: how-dev-spaces-works-up.md