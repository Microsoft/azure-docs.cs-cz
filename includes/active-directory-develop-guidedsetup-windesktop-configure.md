---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: fb521fc9d4927a953cdd66948101969dfc102de4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843569"
---
## <a name="register-your-application"></a>Registrace vaší aplikace
Registrace vaší aplikace v jednom ze dvou způsobů.

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim
Můžete rychle registrace vaší aplikace následujícím způsobem:
1. Přejděte na portál [Microsoft Application Registration Portal](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Vyberte **přidat aplikaci**.

3. Do pole **Application Name** (Název aplikace) zadejte název vaší aplikace.

4. Ujistěte se, že **instalační program s asistencí** zaškrtávací políčko je vybrané a pak vyberte **vytvořit**.

5. Postupujte podle pokynů pro získání ID aplikace a vložte ho do svého kódu.

### <a name="option-2-advanced-mode"></a>Možnost 2: Rozšířeném režimu
Postup při registraci aplikace a přidání informací o registraci aplikace k řešení:
1. Pokud jste ještě nezaregistrovali vaši aplikaci, přejděte [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app).

2. Vyberte **přidat aplikaci**.

3. Do pole **Application Name** (Název aplikace) zadejte název vaší aplikace. 

4. Zkontrolujte, že není zaškrtnuté políčko **Guided Setup** (Instalační program s asistencí) a vyberte **Create** (Vytvořit).

5. Vyberte **Add Platform** (Přidat platformu), **Native Application** (Nativní aplikace) a pak **Save** (Uložit).

6. V **ID aplikace** pole, zkopírovat identifikátor GUID.

7. Přejděte do sady Visual Studio, otevřete *App.xaml.cs* souboru a potom nahraďte `your_client_id_here` s ID aplikace, které jste právě zaregistrovali a zkopírovat.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
