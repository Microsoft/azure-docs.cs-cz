---
title: zahrnout soubor
description: zahrnout soubor
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: f77a036d41ce551d9eab0250eaf4dc16444b24da
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
## <a name="build-and-run-code-in-kubernetes"></a>Sestavení a spuštění kódu v Kubernetes
Umožňuje spustit naše kód! V okně terminálu, spusťte tento příkaz z **kořenové složky kód**, webfrontend:

```cmd
azds up
```

Dohlížet na výstup příkazu, můžete si všimnout několik věcí průběhu:
- Zdrojový kód je synchronizované s vývojového prostředí v Azure.
- Bitovou kopii kontejneru je vytvořen v Azure, podle specifikace Docker prostředky ve složce kódu.
- Jsou vytvořeny Kubernetes objekty, které využívají bitovou kontejneru jako zadaný Helm grafu ve složce kódu.
- Zobrazí se informace o koncové kontejneru. V našem případě jsme se očekává veřejnou adresu URL protokolu HTTP.
- Za předpokladu, že výše fázích úspěšně dokončit, měli byste začít zobrazíte `stdout` (a `stderr`) výstup jako kontejner spuštění.

> [!Note]
> Tyto kroky bude trvat déle poprvé `up` příkaz spustí, ale při dalším spuštění by měl být rychlejší.

## <a name="test-the-web-app"></a>Test webové aplikace
Kontrola bude výstup konzoly informace o veřejnou adresu URL, který byl vytvořen `up` příkaz. Bude ve tvaru: 

`Running at public URL: http://<servicename>-<environmentname>.<guid>.<region>.aksapp.io` 

V okně prohlížeče otevřete tuto adresu URL a měli byste vidět webové aplikace načíst. Jako kontejner provede, `stdout` a `stderr` prostřednictvím výstupního datového proudu do okna terminálu.
