---
title: Správa verzí aplikací blockchain – Azure blockchain Workbench
description: Jak používat verze aplikací v Azure blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 2a70112fd0ab6e2f664ca48265c121936b01e58b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85209874"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Správa verzí aplikace ve verzi Preview služby Azure blockchain Workbench

Můžete vytvářet a používat víc verzí aplikace Azure blockchain Workbench Preview. Pokud je nahráno více verzí stejné aplikace, je k dispozici historie verzí a uživatelé si mohou vybrat, kterou verzi chtějí používat.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Nasazení aplikace blockchain Workbench Další informace najdete v tématu [nasazení Azure blockchain Workbench](deploy.md) pro podrobnosti o nasazení.
* Nasazená aplikace blockchain v blockchain Workbench. Další informace najdete [v tématu Vytvoření aplikace v blockchain v Azure blockchain Workbench](create-app.md) .

## <a name="add-an-app-version"></a>Přidání verze aplikace

Pokud chcete přidat novou verzi, nahrajte do aplikace blockchain Workbench nové soubory konfigurace a inteligentních smluv.

1. Ve webovém prohlížeči přejděte na webovou adresu blockchain Workbench. Například informace o `https://{workbench URL}.azurewebsites.net/` tom, jak najít webovou adresu blockchain Workbench, najdete v tématu [Webová adresa URL služby blockchain Workbench](deploy.md#blockchain-workbench-web-url) .
2. Přihlaste se jako [správce aplikace blockchain Workbench](manage-users.md#manage-blockchain-workbench-administrators).
3. Vyberte aplikaci blockchain, kterou chcete aktualizovat, s jinou verzí.
4. Vyberte **Přidat verzi**. Zobrazí se podokno **Přidat verzi** .
5. Vyberte nové konfigurace kontraktu verze a soubory kódu kontraktu, které se mají nahrát. Konfigurační soubor je automaticky ověřen. Před nasazením aplikace opravte všechny chyby ověřování.
6. Pokud chcete přidat novou verzi aplikace blockchain, vyberte **Přidat verzi** .

    ![Přidání nové verze](media/version-app/add-version.png)

Nasazení aplikace blockchain může trvat několik minut. Po dokončení nasazení aktualizujte stránku aplikace. Výběrem aplikace a kliknutím na tlačítko **Historie verzí** zobrazíte historii verzí aplikace.

> [!IMPORTANT]
> Předchozí verze aplikace jsou zakázané. Můžete jednotlivě znovu povolit starší verze.
>
> Pokud byly provedeny změny v rolích aplikace v nové verzi, bude pravděpodobně nutné znovu přidat členy do aplikačních rolí.

## <a name="using-app-versions"></a>Používání verzí aplikace

Ve výchozím nastavení se v blockchain Workbench používá nejnovější povolená verze aplikace. Pokud chcete použít předchozí verzi aplikace, musíte nejdřív zvolit verzi ze stránky aplikace.

1. V části aplikace blockchain Workbench zaškrtněte políčko aplikace obsahující kontrakt, který chcete použít. Pokud jsou povoleny předchozí verze, je k dispozici tlačítko Historie verzí.
2. Vyberte tlačítko **Historie verzí** .
3. V podokně Historie verzí zvolte verzi aplikace výběrem odkazu ve sloupci *Datum změny* .

    ![Zvolit předchozí verzi](media/version-app/use-version.png)

    Můžete vytvářet nové smlouvy nebo provádět akce s předchozími smlouvami o verzi. Verze aplikace se zobrazí za názvem aplikace a zobrazí se informace o starší verzi.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s Azure blockchain Workbench](troubleshooting.md)
