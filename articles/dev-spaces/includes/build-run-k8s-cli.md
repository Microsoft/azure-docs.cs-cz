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
ms.openlocfilehash: 1cf301ab3b4430c7bb79f6fdd72243f9f2ad1d9b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
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
- Zobrazí se informace o koncové kontejneru. V našem případě jsme se očekává veřejnou adresu URL HTTPS.
- Za předpokladu, že výše fázích úspěšně dokončit, měli byste začít zobrazíte `stdout` (a `stderr`) výstup jako kontejner spuštění.

> [!Note]
> Tyto kroky bude trvat déle poprvé `up` příkaz spustí, ale při dalším spuštění by měl být rychlejší.

## <a name="test-the-web-app"></a>Test webové aplikace
Kontrola bude výstup konzoly informace o veřejnou adresu URL, který byl vytvořen `up` příkaz. Bude ve tvaru: 

`Running at public URL: https://<servicename>-<environmentname>.<guid>.<region>.aksapp.io` 

V okně prohlížeče otevřete tuto adresu URL a měli byste vidět webové aplikace načíst. Jako kontejner provede, `stdout` a `stderr` prostřednictvím výstupního datového proudu do okna terminálu.
