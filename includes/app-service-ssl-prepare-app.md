---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4c5c0197ab6cecbba4bac4c0bff5ef76de24b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300994"
---
## <a name="prepare-your-web-app"></a>Příprava webové aplikace

Chcete-li vytvořit vlastní vazby zabezpečení nebo povolit klientské certifikáty pro aplikaci App Service, váš [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) musí být na úrovni **Basic**, **Standard**, **Premium**nebo **Isolated.** V tomto kroku se ujistíte, že je vaše webová aplikace na podporované cenové úrovni.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete [portál Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Přechod do webové aplikace

Vyhledejte a vyberte **služby App Services**.

![Vybrat služby aplikace](./media/app-service-ssl-prepare-app/app-services.png)

Na stránce **Služby aplikací** vyberte název webové aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-ssl-prepare-app/select-app.png)

Přistáli jste na stránce správy webové aplikace.  

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

V levém navigačním panelu na stránce webové aplikace se posuňte do části **Nastavení** a vyberte **Vertikálně navýšit kapacitu (plán služby App Service)**.

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Zkontrolujte, že vaše webová aplikace není na úrovni **F1** nebo **D1**. Aktuální úroveň webové aplikace je zvýrazněná tmavě modrým rámečkem.

![Kontrola cenové úrovně](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Na úrovni **F1** a **D1** se nepodporuje vlastní SSL. Pokud potřebujete vertikálně navýšit kapacitu, postupujte podle kroků v další části. V opačném případě zavřete stránku **Vertika nahoru** a přeskočte oddíl [Škálování na výšinou stránku plánu služby App Service.](#scale-up-your-app-service-plan)

### <a name="scale-up-your-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některou z placených úrovní (**B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na **Použít**.

![Výběr cenové úrovně](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Oznámení vertikálního navýšení kapacity](./media/app-service-ssl-prepare-app/scale-notification.png)

