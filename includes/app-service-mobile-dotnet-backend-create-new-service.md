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
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325791"
---
1. Přihlaste se k webu [Azure Portal].

2. Klikněte na **Vytvořit prostředek**.

3. Do vyhledávacího pole zadejte **webovou aplikaci**.
    
4. V seznamu výsledků vyberte **webovou aplikaci** z webu Marketplace.

5. Vyberte vaše **předplatné** a **skupiny prostředků** (vyberte existující skupinu prostředků _nebo_ vytvořit novou (použijte stejný název jako vaše aplikace)).

6. Zvolte jedinečný **název** vaší webové aplikace.

7. Zvolte výchozí **publikovat** možnost jako **kód**.

8. V **zásobník modulu Runtime**, je nutné vybrat verzi v **ASP.NET** nebo **uzel**. Pokud vytváříte back-endu .NET, vyberte verzi v rámci technologie ASP.NET. Jinak pokud cílíte na uzel na základě aplikace, vyberte jednu z verze z uzlu.

9. Vybrat vhodnou **operačního systému**, Linux nebo Windows. 

10. Vyberte **oblasti** kde chcete tuto aplikaci nasadit. 

11. Vyberte příslušné **plán služby App Service** a přístupů **revize a vytvořit**. 

12. V části **Skupina prostředků** vyberte existující skupinu prostředků _nebo_ vytvořte novou (použijte stejný název, jaký má aplikace).

13. Klikněte na možnost **Vytvořit**. Počkejte několik minut na úspěšné nasazení služby a teprve potom pokračujte. Sledujte ikonu oznámení (zvonek) v hlavičce portálu upozorňující na aktualizace stavu.

14. Po dokončení nasazení klikněte na **podrobnosti o nasazení** a potom klikněte na typ prostředku **Microsoft.Web/sites**. To bude Navigovat k webové aplikaci služby App Service, kterou jste právě vytvořili. 

15. Klikněte na **konfigurace** okně v části **nastavení** a **nastavení aplikace**, klikněte na **nové nastavení aplikace** tlačítko.

16. V **nastavení aplikace, přidat či upravit** zadejte **název** jako **MobileAppsManagement_EXTENSION_VERSION** a hodnotu jako **nejnovější** a Klikněte na OK.

Vše je nastaveno na tuto nově vytvořenou aplikaci App Service Web jako mobilní aplikace použít.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/