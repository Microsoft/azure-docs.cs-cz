---
title: Řešení potíží | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 09/11/2018
ms.topic: article
description: Rychlý vývoj na platformě Kubernetes s využitím kontejnerů a mikroslužeb v Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
manager: douge
ms.openlocfilehash: c6ca3003c1338f3e057c76d9e04d8b0cbd2210c7
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721190"
---
# <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Tato příručka obsahuje informace o běžných problémů, možná bude při používání Azure Dev mezery.

## <a name="enabling-detailed-logging"></a>Povolení podrobného protokolování

Aby bylo možné řešení problémů s efektivněji, může být užitečné vytvořit podrobnější protokoly ke kontrole.

Pro rozšíření sady Visual Studio, nastavte `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` proměnné prostředí na hodnotu 1. Je potřeba restartovat Visual Studio pro proměnné prostředí se projeví. Po povolení podrobných protokolů se zapíšou do vaší `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` adresáře.

V rozhraní příkazového řádku, můžete pomocí výstupní informace během provádění příkazu `--verbose` přepnout.

## <a name="debugging-services-with-multiple-instances"></a>Ladění služeb s více instancemi

V současné době podporuje Azure Dev prostory ladění pouze na jednu instanci (pod). Soubor azds.yaml obsahuje nastavení, replicaCount, která určuje počet instancí, které se spustí pro vaši službu. Pokud změníte replicaCount nakonfigurovat svoji aplikaci spouštět více instancí pro určitou službu, nemusí být chování ladicí program podle očekávání.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Chyba "nepovedlo se vytvořit Azure Dev prostory kontroleru.

Tato chyba může zobrazit, když dojde k chybě při vytváření kontroleru. Pokud se jedná o přechodnou chybu, odstranění a opětovné vytvoření kontroleru opraví ho.

### <a name="try"></a>Zkuste:

Pokud chcete odstranit kontroleru, použijte rozhraní příkazového řádku Azure Dev mezery. Není možné provést v sadě Visual Studio nebo službě Cloud Shell. Pokud chcete nainstalovat rozhraní příkazového řádku AZDS, nejprve nainstalovat rozhraní příkazového řádku Azure a pak spusťte tento příkaz:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

A pak spuštěním následujícího příkazu odstraňte kontroleru:

```cmd
azds remove -g <resource group name> -n <cluster name>
```

Opětovné vytvoření kontroleru můžete udělat v sadě Visual Studio nebo rozhraní příkazového řádku. Postupujte podle pokynů v kurzech, jako kdyby spouští poprvé.


## <a name="error-service-cannot-be-started"></a>Chyba "službu nelze spustit."

Tato chyba může zobrazit, když kódu služby se nepodaří spustit. Příčinou je často v uživatelském kódu. Pokud chcete získat další diagnostické informace, proveďte následující změny k příkazům a nastavení:

### <a name="try"></a>Zkuste:

Na příkazovém řádku:

Při použití _azds.exe_, použijte--verbose možnost příkazového řádku a použijte možnost příkazového řádku--output k určení formátu výstupního.
 
    ```cmd
    azds up --verbose --output json
    ```

V sadě Visual Studio:

1. Otevřít **nástroje > Možnosti** a v části **projekty a řešení**, zvolte **sestavíte a spustíte**.
2. Změnit nastavení pro **podrobnosti výstupu sestavení projektu nástroje MSBuild** k **podrobné** nebo **diagnostických**.

    ![Možnosti nástrojů – snímek obrazovky dialogového okna](media/common/VerbositySetting.PNG)
    
## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>Překlad názvů DNS pro veřejnou adresu URL související se službou Dev prostory nezdaří

Při překladu názvů DNS selže, může se zobrazit "Nelze zobrazit stránku" nebo "Tento web je nedostupné" Chyba ve webovém prohlížeči při pokusu o připojení k veřejné adrese URL přidružené k Dev prostory služby.

### <a name="try"></a>Zkuste:

Můžete použít následující příkaz, který seznam všech adres URL související s vašimi službami Dev mezery:

```cmd
azds list-uris
```

Pokud je adresa URL v *čekající* stavu, která znamená, že Dev prostory stále čeká registraci DNS pro dokončení. V některých případech trvá několik minut, než registraci dokončit. Vývoj prostory otevře také tunel localhost pro každou službu, kterou můžete použít při čekání na registraci DNS.

Pokud adresa URL zůstane v *čekající* stavu po dobu více než 5 minut, může to znamenat problém s externí pod DNS, který vytvoří veřejný koncový bod a/nebo pod kontroleru příchozího přenosu dat nginx, která získá veřejný koncový bod. Pokud chcete odstranit tyto pody můžete použít následující příkazy. Že se znovu vytvoří automaticky.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Chyba "Vyžaduje nástroje a konfigurace nebyly nalezeny"

K této chybě může dojít při spuštění VS Code: "[Azure Dev prostory] nástroje a konfigurací pro sestavení a ladění. [název projektu]' chybí požadované."
Chyba znamená, že tento azds.exe není v proměnné prostředí PATH, jak je vidět ve VS Code.

### <a name="try"></a>Zkuste:

Spusťte VS Code z příkazového řádku, kde je správně nastavit proměnné prostředí PATH.

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Chyba 'azds' nebyl rozpoznán jako vnitřního ani vnějšího příkazu, spustitelného programu nebo dávkového souboru
 
Pokud azds.exe není nainstalovaná nebo správně nakonfigurovaný, může se zobrazit tato chyba.

### <a name="try"></a>Zkuste:

1. Zkontrolujte umístění %ProgramFiles%/Microsoft SDKs\Azure\Azure Dev prostory CLI (Preview) pro azds.exe. Pokud existuje, přidejte do proměnné prostředí PATH v tomto umístění.
2. Pokud není nainstalovaná azds.exe, spusťte následující příkaz:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Upozornění "soubor Dockerfile se nepodařilo vygenerovat z důvodu nepodporovaný jazyk.
Azure Dev prostory poskytuje nativní podporu pro C# nebo Node.js. Při spuštění *azds prep* do adresáře, který obsahuje kód napsaný v jednom z těchto jazyků, prostory vývoj Azure automaticky vytvoří odpovídající soubor Dockerfile za vás.

Stále můžete Azure Dev prostory pomocí kódu napsaného v jiných jazycích, ale budete muset vytvořit soubor Dockerfile před spuštěním příkazu *azds nahoru* poprvé.

### <a name="try"></a>Zkuste:
Pokud vaše aplikace je napsán v jazyce, že Azure Dev prostory nenabízí nativní podporu, bude nutné zadat příslušný soubor Dockerfile pro sestavení image kontejneru, spouštění kódu. Docker nabízí [seznam osvědčených postupů pro psaní soubory Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) a také [odkaz na soubor Dockerfile](https://docs.docker.com/engine/reference/builder/) , které vám umožňují se píše soubor Dockerfile, který vyhovuje vašim potřebám.

Jakmile budete mít odpovídající soubor Dockerfile v místě, abyste mohli pokračovat spuštění *azds nahoru* ke spuštění aplikace v Azure Dev mezery.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Chyba "upstream Chyba připojení nebo odpojení/reset před záhlaví"
Při pokusu o přístup ke službě, může se zobrazit tato chyba. Například když přejdete na adresu URL služby v prohlížeči. 

### <a name="reason"></a>Důvod 
Port kontejneru není k dispozici. Tomuto problému může dojít, protože: 
* Kontejner se stále ještě probíhá sestavíte a nasadíte. Tento problém může nastat, pokud spustíte `azds up` nebo spuštění ladicího programu a pak zkuste přístup ke kontejneru předtím, než byl úspěšně nasazen.
* Konfigurace portu není konzistentní napříč vaší _soubor Dockerfile_, diagram helmu a libovolný kód serveru, které se otevře port.

### <a name="try"></a>Zkuste:
1. Jestli je kontejner právě vytvořená/nasazuje, můžete počkejte 2-3 sekund a zkuste to znovu přístupu ke službě. 
1. Zkontrolujte konfiguraci portů. Zadaný port čísla by měla být **identické** ve níže prostředky:
    * **Soubor Dockerfile:** určené `EXPOSE` instrukce.
    * **[Diagram helmu](https://docs.helm.sh):** určené `externalPort` a `internalPort` hodnoty pro službu (nacházejí se často ve `values.yml` souboru),
    * Žádné porty se otevřely v kódu aplikace, například v Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Nebyl nalezen konfigurační soubor
Spuštění `azds up` a zobrazí následující chyba: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Důvod
Je nutné spustit `azds up` z kořenového adresáře kódu, které chcete spustit, a je třeba inicializovat složce kód ke spuštění Azure Dev mezer.

### <a name="try"></a>Zkuste:
1. Do kořenové složky, která obsahuje kód služby změňte aktuální adresář. 
1. Pokud nemáte _azds.yaml_ souboru ve složce kód spustit `azds prep` ke generování Docker, Kubernetes a Azure Dev prostory prostředky.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Chyba: "cílového programu se neočekávaně ukončil s kódem 126 azds."
Spouští se ladicí program VS Code může někdy vést k této chybě.

### <a name="try"></a>Zkuste:
1. Zavřete a znovu spusťte VS Code.
2. Znovu, stiskněte F5.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Chyba: Nepodařilo se najít rozšíření ladicího programu pro typ: coreclr"ladění
Spuštění ladicího programu VS Code zaznamená chybu: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Důvod
Rozšíření VS Codu pro jazyk C# nainstalována na vývojovém počítači nemáte. Rozšíření jazyka C# obsahuje podporu ladění pro.Net Core (CoreCLR).

### <a name="try"></a>Zkuste:
Nainstalujte [rozšíření VS Codu pro jazyk C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Chyba ladění není podporován typ ladění nakonfigurováno "coreclr.
Spuštění ladicího programu VS Code zaznamená chybu: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Důvod
Rozšíření VS Codu pro Azure Dev prostory nainstalována na vývojovém počítači nemáte.

### <a name="try"></a>Zkuste:
Nainstalujte [rozšíření VS Codu pro Azure Dev prostory](get-started-netcore.md).

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Název typu nebo oboru názvů 'Moje knihovna' nebyl nalezen

### <a name="reason"></a>Důvod 
Kontext sestavení na úrovni projektu nebo služby ve výchozím nastavení se proto projekt knihovny, které používáte nebude nalezena.

### <a name="try"></a>Zkuste:
Co je potřeba udělat:
1. Upravit _azds.yaml_ souboru nastavit kontext sestavení na úrovni řešení.
2. Upravit _soubor Dockerfile_ a _Dockerfile.develop_ soubory k odkazování na projekt (_.csproj_) soubory správně, vzhledem k nové sestavení kontextu.
3. Místo _.dockerignore_ souboru vedle souboru .sln a podle potřeby upravte.

Můžete najít na příklad https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>Chyba autorizace "Microsoft.DevSpaces/register/action.
Když spravujete prostorem Azure Dev a práci v rámci předplatného Azure, pro který nemáte přístup přispěvatele nebo vlastníka, může se zobrazit následující chyba.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Důvod
Vybrané předplatné Azure nebyla zaregistrována `Microsoft.DevSpaces` oboru názvů.

### <a name="try"></a>Zkuste:
Někdo s přístupem k roli vlastníka nebo přispěvatele k předplatnému Azure spuštěním následujícího příkazu rozhraní příkazového řádku Azure k ruční registraci `Microsoft.DevSpaces` obor názvů:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev prostory vypadá, že nepodporuje použití Můj existující soubor Dockerfile k vytvoření kontejneru 

### <a name="reason"></a>Důvod
Azure Dev prostory můžete nakonfigurovat tak, aby odkazoval na konkrétní _soubor Dockerfile_ ve vašem projektu. Pokud se zdá, že nepoužívá prostory vývoj Azure _soubor Dockerfile_ očekáváte, že k vytvoření kontejnerů, možná budete muset říct Azure Dev prostory tam, kde je explicitně. 

### <a name="try"></a>Zkuste:
Otevřít _azds.yaml_ soubor, který byl vygenerován mezerami vývoj Azure ve vašem projektu. Použít `configurations->develop->build->dockerfile` směrnice tak, aby odkazoval na soubor Dockerfile, který chcete použít:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```
