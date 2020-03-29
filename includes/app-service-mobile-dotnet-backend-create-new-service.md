---
title: zahrnout soubor
description: zahrnout soubor
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67325791"
---
1. Přihlaste se k [portálu Azure].

2. Klikněte na **Vytvořit prostředek**.

3. Do vyhledávacího pole zadejte **Web App**.
    
4. V seznamu výsledků vyberte **webovou aplikaci** z webu Marketplace.

5. Vyberte **své předplatné** a **skupinu prostředků** (vyberte existující skupinu prostředků _nebo_ vytvořte novou (se stejným názvem jako vaše aplikace)).

6. Vyberte jedinečný **název** webové aplikace.

7. Zvolte výchozí možnost **Publikovat** jako **kód**.

8. V **zásobníku runtime**je třeba vybrat verzi v části **ASP.NET** nebo **Uzel**. Pokud vytváříte back-end .NET, vyberte verzi pod ASP.NET. V opačném případě, pokud cílíte na aplikaci založenou na uzlu, vyberte jednu z verzí z uzlu.

9. Vyberte správný **operační systém**, linux nebo windows. 

10. Vyberte **oblast,** kde chcete tuto aplikaci nasadit. 

11. Vyberte příslušný **plán služby App Service A** stiskněte tlačítko Revize a **vytvořte**. 

12. V části **Skupina prostředků** vyberte existující skupinu prostředků _nebo_ vytvořte novou (použijte stejný název, jaký má aplikace).

13. Klikněte na **Vytvořit**. Počkejte několik minut na úspěšné nasazení služby a teprve potom pokračujte. Sledujte ikonu oznámení (zvonek) v hlavičce portálu upozorňující na aktualizace stavu.

14. Po dokončení nasazení klikněte na část **Podrobnosti o nasazení** a potom klikněte na zdroj typu **Microsoft.Web/weby**. Přejdete na webovou aplikaci App Service, kterou jste právě vytvořili. 

15. Klikněte na okno **Konfigurace** v části **Nastavení** a v **nastavení aplikace**, klikněte na tlačítko Nastavení **nové aplikace.**

16. Na stránce **Nastavení přidat nebo upravit aplikaci** zadejte **Název** jako **MobileAppsManagement_EXTENSION_VERSION** a Hodnotu jako **nejnovější** a stiskněte OK.

Jste všichni připraveni používat tuto nově vytvořenou webovou aplikaci služby App Service jako mobilní aplikaci.

<!-- URLs. -->
[Portál Azure]: https://portal.azure.com/