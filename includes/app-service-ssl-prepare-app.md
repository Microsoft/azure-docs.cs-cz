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
ms.openlocfilehash: 858d098f18604d1360af8509eb8a0cec6fa7d0c7
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49354528"
---
## <a name="prepare-your-web-app"></a>Příprava webové aplikace

Pokud chcete vytvořit vazbu vlastního certifikátu SSL (certifikát třetí strany nebo certifikát App Service) ke své webové aplikaci, váš [plán služby App Service](https://azure.microsoft.com/pricing/details/app-service/) musí být na úrovni **Basic**, **Standard**, **Premium** nebo **Isolated**. V tomto kroku se ujistíte, že je vaše webová aplikace na podporované cenové úrovni.

### <a name="log-in-to-azure"></a>Přihlášení k Azure

Otevřete web [Azure Portal](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Přechod do webové aplikace

V levé nabídce klikněte na **App Services** a pak klikněte na název vaší webové aplikace.

![Výběr webové aplikace](./media/app-service-ssl-prepare-app/select-app.png)

Byli jste přesměrování na stránku pro správu vaší webové aplikace.  

### <a name="check-the-pricing-tier"></a>Kontrola cenové úrovně

V levém navigačním panelu na stránce webové aplikace se posuňte do části **Nastavení** a vyberte **Vertikálně navýšit kapacitu (plán služby App Service)**.

![Nabídka Vertikálně navýšit kapacitu](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Zkontrolujte, že vaše webová aplikace není na úrovni **F1** nebo **D1**. Aktuální úroveň webové aplikace je zvýrazněná tmavě modrým rámečkem.

![Kontrola cenové úrovně](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Na úrovni **F1** a **D1** se nepodporuje vlastní SSL. Pokud potřebujete vertikálně navýšit kapacitu, postupujte podle kroků v další části. Jinak zavřete stránku **Vertikálně navýšit kapacitu** a přejděte k části [Nahrání a vytvoření vazby certifikátu SSL](#upload).

### <a name="scale-up-your-app-service-plan"></a>Vertikální navýšení kapacity plánu služby App Service

Vyberte některou z placených úrovní (**B1**, **B2**, **B3** nebo kteroukoli úroveň v kategorii **Produkční**). Další možnosti se zobrazí po kliknutí na odkaz **Zobrazit další možnosti**.

Klikněte na tlačítko **Použít**.

![Výběr cenové úrovně](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Až se zobrazí následující oznámení, operace škálování je dokončená.

![Oznámení vertikálního navýšení kapacity](./media/app-service-ssl-prepare-app/scale-notification.png)

