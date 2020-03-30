---
title: Správa verzí blockchainových aplikací - Azure Blockchain Workbench
description: Jak používat verze aplikací ve verzi Azure Blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74323911"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Správa verzí aplikací Azure Blockchain Workbench Preview

Můžete vytvořit a používat více verzí aplikace Azure Blockchain Workbench Preview. Pokud je odesláno více verzí stejné aplikace, je k dispozici historie verzí a uživatelé si mohou vybrat, kterou verzi chtějí použít.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nasazení blockchainworkbench. Další informace najdete v [tématu Nasazení Azure Blockchain Workbench](deploy.md) podrobnosti o nasazení
* Nasazená blockchainová aplikace v Blockchain Workbench. Viz [Vytvoření blockchainové aplikace v Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Přidání verze aplikace

Chcete-li přidat novou verzi, nahrajte novou konfiguraci a inteligentní soubory smluv do Blockchain Workbench.

1. Ve webovém prohlížeči přejděte na webovou adresu Blockchain Workbench. Informace o `https://{workbench URL}.azurewebsites.net/` tom, jak najít webovou adresu Blockchain Workbench, najdete například v [tématu Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. Přihlaste se jako [správce Blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Vyberte blockchainovou aplikaci, kterou chcete aktualizovat s jinou verzí.
4. Vyberte **Přidat verzi**. Zobrazí se podokno **Přidat verzi.**
5. Zvolte novou konfiguraci smlouvy verze a soubory kódu smlouvy, které chcete nahrát. Konfigurační soubor je automaticky ověřen. Před nasazením aplikace opravte všechny chyby ověření.
6. Vyberte **Přidat verzi** a přidejte novou verzi blockchainové aplikace.

    ![Přidání nové verze](media/version-app/add-version.png)

Nasazení blockchainové aplikace může trvat několik minut. Po dokončení nasazení aktualizujte stránku aplikace. Výběr aplikace a výběr tlačítka **Historie verzí** zobrazí historii verzí aplikace.

> [!IMPORTANT]
> Předchozí verze aplikace jsou zakázány. Můžete jednotlivě znovu povolit minulé verze.
>
> Pokud byly v nové verzi provedeny změny rolí aplikace, bude pravděpodobně nutné znovu přidat členy k rolím aplikace.

## <a name="using-app-versions"></a>Používání verzí aplikací

Ve výchozím nastavení se v Blockchain Workbench používá nejnovější povolená verze aplikace. Pokud chcete použít předchozí verzi aplikace, musíte nejprve vybrat verzi ze stránky aplikace.

1. V části aplikace Blockchain Workbench zaškrtněte políčko aplikace obsahující smlouvu, kterou chcete použít. Pokud jsou povoleny předchozí verze, je k dispozici tlačítko historie verzí.
2. Vyberte tlačítko **Historie verzí.**
3. V podokně Historie verzí zvolte verzi aplikace výběrem odkazu ve sloupci *Datum změny.*

    ![Výběr předchozí verze](media/version-app/use-version.png)

    Můžete vytvořit nové smlouvy nebo provést akce u předchozích smluv verze. Verze aplikace se zobrazí za názvem aplikace a zobrazí se upozornění o starší verzi.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s pracovní mitinou Azure Blockchain](troubleshooting.md)
