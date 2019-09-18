---
title: Podpora pro Azure Active Directory B2C | Microsoft Docs
description: Jak zaAzure Active Directory B2Covat požadavky na podporu pro.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f7f2217677b81c6f3e87eaa2612880adf3b499c2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064944"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Žádosti o podporu souborů
Žádosti o podporu pro Azure Active Directory B2C (Azure AD B2C) na Azure Portal můžete vymezit pomocí následujících kroků:

1. Přepněte ze svého tenanta B2C do jiného tenanta, ke kterému je přidružené předplatné Azure. Obvykle se jedná o vašeho tenanta zaměstnance nebo výchozího tenanta vytvořeného při registraci předplatného Azure. Další informace najdete v tématu [jak předplatné Azure souvisí se službou Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![Azure Portal se zvýrazněným výběrem tenanta](./media/active-directory-b2c-support/support-switch-dir.png)

1. Po přepnutí klientů klikněte na tlačítko **help + podpora**.

    ![Dlaždice pomoc a podpora zvýrazněná v Azure Portal](./media/active-directory-b2c-support/support-support.png)

1. Klikněte na **Nová žádost o podporu**.

    ![V Azure Portal se zvýrazní nová dlaždice žádosti o podporu.](./media/active-directory-b2c-support/support-new.png)

1. V okně **základy** použijte tyto podrobnosti a klikněte na **Další**.

    * **Typ problému** je **technický**.
    * Vyberte odpovídající **předplatné**.
    * **Služba** je **Active Directory**.
    * Vyberte příslušný **plán podpory**. Pokud ho ještě nemáte, můžete si ho zaregistrovat [tady](https://azure.microsoft.com/support/plans/).

     ![Stránka základy s zvýrazněným tlačítkem Další v Azure Portal](./media/active-directory-b2c-support/support-basics.png)

1. V okně **problém** použijte tyto podrobnosti a klikněte na **Další**.

    * Vyberte příslušnou úroveň **závažnosti** .
    * **Typ problému** je **B2C**.
    * Vyberte příslušnou **kategorii**.
    * Popište svůj problém v poli **podrobností** . Zadejte podrobnosti, jako je název tenanta B2C, popis problému, chybové zprávy, ID korelace (Pokud je k dispozici) a tak dále.
    * V poli **časový rámec** zadejte datum a čas (včetně časového pásma), k němuž došlo k problému.
    * V části **nahrávání souborů**Nahrajte všechny snímky obrazovky a soubory, které vám pomohou při řešení problému.

     ![Stránka s problémem s zvýrazněným tlačítkem Další v Azure Portal](./media/active-directory-b2c-support/support-problem.png)

1. V okně **kontaktní informace** přidejte své kontaktní informace. Klikněte na možnost **Vytvořit**.

    ![Stránka kontaktní informace s zvýrazněným tlačítkem vytvořit na portálu](./media/active-directory-b2c-support/support-contact.png)

1. Po odeslání žádosti o podporu ji můžete monitorovat kliknutím na tlačítko **help + podpora** na úvodní panel a potom **Spravovat žádosti o podporu**.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Známý problém: Archivace žádosti o podporu v kontextu tenanta B2C

Pokud jste si vynechali krok 2 popsaný výše a pokusíte se vytvořit žádost o podporu v kontextu vašeho tenanta B2C, zobrazí se následující chyba.

> [!IMPORTANT]
> Nepokoušejte se zaregistrovat nové předplatné Azure v tenantovi B2C.

![V Azure Portal není zobrazená chyba předplatného.](./media/active-directory-b2c-support/support-no-sub.png)
