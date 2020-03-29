---
title: Poradce při potížích s rozšířením přístupového panelu Azure pro aplikaci IE | Dokumenty společnosti Microsoft
description: Jak použít zásady skupiny k nasazení doplňku aplikace Internet Explorer pro portál Moje aplikace.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0269c87572e2a9242a54491103ae0fcc3637518
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67723917"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Poradce při potížích s rozšířením přístupového panelu pro aplikaci Internet Explorer

Tento článek vám pomůže vyřešit následující problémy:

* Při používání Internet Exploreru nemůžete přistupovat k aplikacím prostřednictvím portálu Moje aplikace.
* Zobrazí se zpráva Instalovat software, i když jste software již nainstalovali.

Pokud jste správce, přečtěte si informace [o tom, jak nasadit rozšíření přístupového panelu pro aplikaci Internet Explorer pomocí zásad skupiny](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Spuštění diagnostického nástroje

Problémy s instalací rozšíření přístupového panelu můžete diagnostikovat stažením a spuštěním diagnostického nástroje přístupového panelu. 

Stažení a instalace diagnostického nástroje:

1. [Vyberte tento odkaz a stáhněte diagnostický nástroj.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Otevřete soubor a extrahujte obsah do počítače.
1. Chcete-li nástroj spustit, klepněte pravým tlačítkem myši na soubor s názvem *AccessPanelExtensionDiagnosticTool.js* a vyberte příkaz **Otevřít pomocí** > **hostitele skriptů založených na systému Microsoft Windows .**

    ![Otevřít s > windows založené ho script host](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Zkontrolujte výsledky diagnostiky, které se zobrazí, a vyberte **Ano,** abyste problémy opravili. Zobrazí se dialogové okno **Zkontrolovat výsledky** s informacemi o tom, co dělat, když rozšíření nefunguje.  
1. Přečtěte si zprávu a vyberte **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Kontrola, zda je povoleno rozšíření přístupového panelu

Ověření, zda jste v aplikaci Internet Explorer povolili rozšíření přístupového panelu:

1. V aplikaci Internet Explorer vyberte **ikonu Ozubená zařízení** v pravém horním rohu okna a vyberte **možnosti Internetu**.
1. Přejděte na kartu **Programy** a vyberte **Spravovat doplňky**.
1. V části Microsoft **Corporation** vyberte **Možnost Rozšíření přístupového panelu** a vyberte **Povolit**.
1. Chcete-li změny uložit, zavřete všechna otevřená okna prohlížeče Internet Explorer. Změna se projeví při příštím spuštění aplikace Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Povolení rozšíření pro procházení inprivate

Povolení rozšíření pro procházení inprivate:

1. V aplikaci Internet Explorer vyberte **ikonu Ozubená zařízení** v pravém horním rohu okna a vyberte **možnosti Internetu**.
1. Přejděte na kartu **Ochrana osobních údajů** a ověřte, zda je zaškrtnutí políčka **Zakázat panely nástrojů a rozšíření při spuštění procházení inprivate.**
1. Chcete-li změny uložit, zavřete všechna otevřená okna prohlížeče Internet Explorer. Změna se projeví při příštím spuštění aplikace Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Odinstalace rozšíření přístupového panelu

Jak odinstalovat rozšíření přístupového panelu z počítače:

1. V Ovládacích panelech vyhledejte možnost *Odinstalovat*.
1. Ve výsledcích hledání vyberte **Odinstalovat program**.

    ![V ovládacím panelu vyberte možnost Odinstalovat program.](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. V seznamu vyberte **Rozšíření přístupového panelu** a vyberte **Odinstalovat**.

    ![Odinstalace rozšíření přístupového panelu](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Potom můžete zkusit nainstalovat rozšíření znovu a zjistěte, zda byl problém vyřešen.

Pokud narazíte na problémy s odinstalací rozšíření, můžete ho také odebrat pomocí nástroje [Microsoft Fix It.](https://go.microsoft.com/?linkid=9779673)

## <a name="related-articles"></a>Související články

* [Přístup k aplikacím a jednotné přihlašování pomocí služby Azure Active Directory](what-is-single-sign-on.md)
* [Jak nasadit rozšíření přístupového panelu pro aplikaci Internet Explorer pomocí zásad skupiny](deploy-access-panel-browser-extension.md)
