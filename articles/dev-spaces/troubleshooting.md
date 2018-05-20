---
title: Řešení potíží s | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: article
description: Rychlý vývoj Kubernetes s kontejnery a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Kubernetes služby Azure, kontejnery
manager: douge
ms.openlocfilehash: 371bb9195266f3511d115de2532e6b64f49ef26f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Tato příručka obsahuje informace o běžných problémech může mít při používání Azure Dev prostory.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Chyba "proti proudu Chyba připojení nebo odpojení nebo zrušit před hlavičky"
Tato chyba se může zobrazit při pokusu o přístup ke službě. Například když přejdete na adresu URL služby v prohlížeči. 

### <a name="reason"></a>Důvod 
Port kontejneru není k dispozici. Možné příčiny: 
* Kontejner se stále ještě probíhá vytvořené a nasazené. To může být případě, pokud spustíte `azds up` nebo spuštění ladicího programu a opakujte přístup kontejneru před úspěšně nasadil.
* Konfigurace portu není konzistentní napříč váš soubor Docker, Helm graf a serverový kód, které se otevře port.

### <a name="try"></a>Vyzkoušejte:
1. Pokud kontejner právě vytvořené nebo nasazení, můžete počkejte 2 – 3 sekund a zkuste znovu přistoupit ke službě. 
1. Zkontrolujte konfiguraci portů. Zadaný port čísla by měla mít **identické** v všechny prostředky níže:
    * **Soubor Docker:** určeného `EXPOSE` instrukcí.
    * **[Graf Helm](https://docs.helm.sh):** určeného `externalPort` a `internalPort` hodnoty pro službu (často umístěný v `values.yml` soubor),
    * Všechny porty se otevřely v kódu aplikace, například v Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Konfigurační soubor nebyl nalezen.
Při spuštění `azds up` a zobrazí následující chyba: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Důvod
Je nutné spustit `azds up` z kořenového adresáře kódu, kterou chcete spustit, a je třeba inicializovat složce kód ke spuštění s Azure Dev prostory.

### <a name="try"></a>Vyzkoušejte:
1. Změňte aktuální adresář ke kořenové složce obsahující kódu služby. 
1. Pokud nemáte azds.yaml soubor ve složce kódu, spusťte `azds prep` ke generování Docker, Kubernetes a prostory Dev Azure prostředky.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Chyba: 'kanálu program byl neočekávaně ukončen s kódem 126 azds."
Spuštění ladicího programu VS Code může v některých případech vést k této chybě. Jedná se o známý problém.

### <a name="try"></a>Vyzkoušejte:
1. Zavřete a znovu otevřete VS Code.
2. Znovu stiskněte F5.


## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Ladění – chyba "konfigurovaná ladění typ 'coreclr' není podporován.
Chyba spuštění ladicího programu VS Code sestavy: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Důvod
Nemáte rozšíření VS Code prostory Dev Azure nainstalovaná na počítači pro vývoj.

### <a name="try"></a>Vyzkoušejte:
Nainstalujte [VS Code rozšíření Azure Dev prostory](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Typ nebo obor názvů 'Moje knihovna' nebyl nalezen.

### <a name="reason"></a>Důvod 
Kontext sestavení je na úrovni projektu a službám ve výchozím nastavení, proto projekt knihovny, kterou používáte nebude nalezen.

### <a name="try"></a>Vyzkoušejte:
Co je potřeba udělat:
1. Upravte soubor azds.yaml nastavit kontext sestavení na úrovni řešení.
2. Upravte soubor Docker a Dockerfile.develop soubory k odkazování na soubory csproj správně, relativně k nový kontext sestavení.
3. Umístěte soubor .dockerignore vedle soubor .sln a podle potřeby upravte.

Můžete najít příklad v https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftconnectedenvironmentregisteraction-authorization-error"></a>Chyba autorizace 'Microsoft.ConnectedEnvironment/register/action.
Když spravujete Dev místa Azure a práci v předplatné Azure, pro který jste vlastníkem nebo přispěvatelem přístup, může se zobrazit následující chyby.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.ConnectedEnvironment/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Důvod
Vybrané předplatné není registrován Microsoft.ConnectedEnvironment obor názvů.

### <a name="try"></a>Vyzkoušejte:
Někdo s vlastníkem nebo přispěvatelem přístup k předplatnému Azure můžete spusťte následující příkaz rozhraní příkazového řádku Azure k ruční registraci Microsoft.ConnectedEnvironment obor názvů:

```cmd
az provider register --namespace Microsoft.ConnectedEnvironment
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Asi není používat Můj stávající soubor Docker vytvořit kontejner Azure prostory vývojářů 

### <a name="reason"></a>Důvod
Azure Dev prostory lze nakonfigurovat tak, aby odkazoval na konkrétní soubor Docker ve vašem projektu. Pokud se zdá, že Azure Dev prostory nepoužívá soubor Docker plánujete vytvoření kontejnerů, možná budete muset explicitně říct prostory Dev Azure tam, kde je. 

### <a name="try"></a>Vyzkoušejte:
Otevřete `azds.yaml` soubor, který byl vygenerován mezerami Dev Azure ve vašem projektu. Použít `configurations->develop->build->dockerfile` direktiva tak, aby odkazoval na soubor Docker, kterou chcete použít:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```