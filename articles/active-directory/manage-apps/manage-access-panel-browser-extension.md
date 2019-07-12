---
title: Řešení potíží s rozšířením panelu přístup ke službě Azure pro Internet Exploreru | Dokumentace Microsoftu
description: Jak používat zásady skupiny nasadit doplněk aplikace Internet Explorer pro Moje aplikace z portálu.
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
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723917"
---
# <a name="troubleshoot-the-access-panel-extension-for-internet-explorer"></a>Řešení potíží s rozšířením Panel přístupu pro aplikaci Internet Explorer

Tento článek pomáhá řešit následující problémy:

* Budete moci přistupovat k vaší aplikace na portálu Moje aplikace při použití aplikace Internet Explorer.
* Zobrazí se zpráva "Instalace softwaru" i když jste již nainstalovali software.

Pokud jste správce, přečtěte si téma [jak pro aplikaci Internet Explorer pomocí zásad skupiny nasadit rozšíření přístupového panelu](deploy-access-panel-browser-extension.md).

## <a name="run-the-diagnostic-tool"></a>Spusťte nástroj pro diagnostiku

Stažením a instalací diagnostický nástroj přístupového panelu můžete diagnostikovat potíže s instalací s rozšíření přístupového panelu. 

Stáhnout a nainstalovat nástroj pro diagnostiku:

1. [Vyberte tento odkaz ke stažení nástroj pro diagnostiku.](https://account.activedirectory.windowsazure.com/applications/AccessPanelExtensionDiagnosticTool/AccessPanelExtensionDiagnosticTool.zip)
1. Otevřete soubor a extrahujte obsah do počítače.
1. Chcete-li spustit nástroj, klikněte pravým tlačítkem na soubor s názvem *AccessPanelExtensionDiagnosticTool.js* a vyberte **otevřít v programu** > **Microsoft Windows na základě Script Host** .

    ![Otevřít v programu > skriptu hostitele se systémem Microsoft Windows](./media/manage-access-panel-browser-extension/open-access-panel-extension-diagnostic-tool.png)

1. Zkontrolujte výsledky diagnostiky, které se zobrazí a vyberte **Ano** opravit problémy. **Zkontrolujte výsledky** dialogové okno s informacemi o tom, co dělat, když rozšíření nefunguje.  
1. Přečtěte si zprávu a vyberte **OK**.

## <a name="check-that-the-access-panel-extension-is-enabled"></a>Zkontrolujte, že je povolené rozšíření přístupového panelu

Pokud chcete ověřit, že jste povolili rozšíření přístupového panelu v aplikaci Internet Explorer:

1. V aplikaci Internet Explorer, vyberte **ikonu ozubeného kola** v pravém horním rohu okna a vyberte **Možnosti Internetu**.
1. Přejděte **programy** kartě a vyberte **spravovat doplňky**.
1. Vyberte **rozšíření přístupového panelu** v **Microsoft Corporation** a vyberte **povolit**.
1. Uložte změny, zavřete všechna okna prohlížeče Internet Explorer je otevřena. Tato změna se projeví při příštím otevření aplikace Internet Explorer.

## <a name="enable-extensions-for-inprivate-browsing"></a>Povolit rozšíření pro procházení InPrivate

Postup povolení rozšíření pro procházení InPrivate:

1. V aplikaci Internet Explorer, vyberte **ikonu ozubeného kola** v pravém horním rohu okna a vyberte **Možnosti Internetu**.
1. Přejděte na **ochrany osobních údajů** kartě a ověřte, zda **zakázat panely nástrojů a rozšíření při spuštění procházení InPrivate** políčko není zaškrtnuté.
1. Uložte změny, zavřete všechna okna prohlížeče Internet Explorer je otevřena. Tato změna se projeví při příštím otevření aplikace Internet Explorer.

## <a name="uninstall-the-access-panel-extension"></a>Odinstalace rozšíření přístupového panelu

Chcete-li z počítače odinstalovat rozšíření přístupového panelu:

1. Hledat v Ovládacích panelech *odinstalovat*.
1. Ve výsledcích hledání vyberte **odinstalovat program**.

    ![Vyberte odinstalovat program možnost z ovládacích panelů](./media/manage-access-panel-browser-extension/uninstall-program-control-panel.png)

1. V seznamu vyberte **rozšíření přístupového panelu** a vyberte **odinstalovat**.

    ![Odinstalace rozšíření přístupového panelu](./media/manage-access-panel-browser-extension/uninstall-access-panel-extension.png)

1. Potom můžete nainstalovat rozšíření znovu, abyste viděli, pokud byl problém vyřešen.

Pokud narazíte na problémy odinstalovat rozšíření, můžete také odebrat pomocí [Microsoft Fix It](https://go.microsoft.com/?linkid=9779673) nástroj.

## <a name="related-articles"></a>Související články

* [Přístup k aplikaci a jednotné přihlašování s Azure Active Directory](what-is-single-sign-on.md)
* [Jak nasadit rozšíření přístupového panelu pro aplikaci Internet Explorer pomocí zásad skupiny](deploy-access-panel-browser-extension.md)
