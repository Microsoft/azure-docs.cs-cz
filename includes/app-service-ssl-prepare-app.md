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
ms.openlocfilehash: 1c3f2009dc71df1a5496d585bdcba986a79ac0d0
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768451"
---
## <a name="prepare-your-web-app"></a>Příprava webové aplikace

Pokud chcete vytvořit vazbu vlastního certifikátu SSL (certifikát třetí strany nebo certifikát App Service) ke své webové aplikaci, váš [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) musí být na úrovni **Basic**, **Standard**, **Premium** nebo **Isolated**. V tomto kroku se ujistíte, že je vaše webová aplikace na podporované cenové úrovni.

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Otevřete [portál Azure](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Přechod do webové aplikace

Vyhledejte a vyberte **App Services**.

![Vyberte App Services](./media/app-service-ssl-prepare-app/app-services.png)

Na stránce **App Services** vyberte název vaší webové aplikace.

![Přechod do aplikace Azure na portálu](./media/app-service-ssl-prepare-app/select-app.png)

Vyložili jste na stránce pro správu vaší webové aplikace.  

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

V levém navigačním panelu na stránce webové aplikace se posuňte do části **Nastavení** a vyberte **Vertikálně navýšit kapacitu (plán služby App Service)** .

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Zkontrolujte, že vaše webová aplikace není na úrovni **F1** nebo **D1**. Aktuální úroveň webové aplikace je zvýrazněná tmavě modrým rámečkem.

![Kontrola cenové úrovně](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Na úrovni **F1** a **D1** se nepodporuje vlastní SSL. Pokud potřebujete vertikálně navýšit kapacitu, postupujte podle kroků v další části. V opačném případě zavřete stránku **horizontálního navýšení kapacity** a přeskočte část [navýšení kapacity schématu App Service](#scale-up-your-app-service-plan) .

### <a name="scale-up-your-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některou z placených úrovní (**B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na tlačítko **Použít**.

![Výběr cenové úrovně](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Oznámení vertikálního navýšení kapacity](./media/app-service-ssl-prepare-app/scale-notification.png)

