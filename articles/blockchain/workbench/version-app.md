---
title: Správa verzí aplikací Blockchain v Azure Blockchain Workbench
description: Jak používat verze aplikace v Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 1/8/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: c4c5dcf9c83b79158e3459e79db6dd066d982fc8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54108255"
---
# <a name="azure-blockchain-workbench-application-versioning"></a>Azure Blockchain Workbench správy verzí aplikací

Můžete vytvořit a používat více verzí aplikace Azure Blockchain Workbench. Pokud se nahrají více verzí stejného aplikace, historie verzí je k dispozici a uživatelé si můžou vybrat verze, které chtějí používat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Blockchain Workbench umožňuje nasazení. Další informace najdete v tématu [Azure Blockchain Workbench umožňuje nasazení](deploy.md) podrobnosti o nasazení
* Nasazené blockchainové aplikace v Blockchain Workbench. Zobrazit [vytvoření blockchainové aplikace v Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Přidat verzi aplikace

K přidání nové verze, nahrajte novou konfiguraci a soubory inteligentního smlouvy do Blockchain Workbench.

1. Ve webovém prohlížeči přejděte na webovou adresu Blockchain Workbench. Například `https://{workbench URL}.azurewebsites.net/` informace o tom, jak najít webovou adresu Blockchain Workbench najdete v tématu [adresy URL webového Blockchain Workbench](deploy.md#blockchain-workbench-web-url)
2. Přihlaste se jako [Blockchain Workbench správce](manage-users.md#manage-blockchain-workbench-administrators).
3. Vyberte blockchainové aplikace, které chcete aktualizovat s jinou verzí.
4. Vyberte **verzi přidat**. **Verzi přidat** zobrazí podokno.
5. Vyberte novou konfiguraci verze smlouvy a smlouvy kódu soubory k nahrání. Konfigurační soubor se automaticky ověří. Opravte všechny chyby ověření před nasazením aplikace.
6. Vyberte **verzi přidat** pro přidání nové verze blockchainové aplikace.

    ![Přidání nové verze](media/version-app/add-version.png)

Nasazení blockchainových aplikací může trvat několik minut. Až se nasazení dokončí, aktualizujte stránku aplikace. Výběr aplikace a vyberete **historie verzí** tlačítko, zobrazí se historie verzí aplikace.

> [!IMPORTANT]
> Předchozí verze této aplikace jsou zakázané. Můžete jednotlivě znovu povolit starší verze.
>
> Budete muset znovu přidat členy do aplikační role, pokud byly provedeny změny aplikační role v nové verzi.

## <a name="using-app-versions"></a>Pomocí verze aplikace

Ve výchozím nastavení se používá nejnovější verze aplikace, která je povolená v Blockchain Workbench. Pokud chcete použít předchozí verzi aplikace, musíte nejprve zvolit verzi ze stránky aplikace.

1. V části aplikace Blockchain Workbench zaškrtněte políčko aplikaci, která obsahuje smlouvu, kterou chcete použít. Pokud jsou povolené předchozí verze, je k dispozici tlačítko historie verzí.
2. Vyberte **historie verzí** tlačítko.
3. V podokně historie verzí zvolte verzi aplikace tak, že vyberete odkaz v *datum změny* sloupce.

    ![Vyberte předchozí verzi](media/version-app/use-version.png)

    Můžete vytvořit nové smlouvy nebo provádět akce na předchozí verze smlouvy. Verze aplikace se zobrazí následující název aplikace a zobrazí se upozornění o starší verze.

## <a name="next-steps"></a>Další postup

* [Řešení potíží s Azure Blockchain Workbench](troubleshooting.md)
