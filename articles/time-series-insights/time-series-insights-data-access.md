---
title: Konfigurace zabezpečení pro přístup a správu Azure Time Series Insights Preview | Microsoft Docs
description: Tento článek popisuje, jak nakonfigurovat zabezpečení a oprávnění jako zásady přístupu pro správu a zásady přístupu k datům pro zabezpečení Azure Time Series Insights ve verzi Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 670615980b7fd78441a08ba987073dc139b3792a
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274434"
---
# <a name="grant-data-access-to-an-environment"></a>Udělení přístupu k datům do prostředí

Tento článek pojednává o dvou typech zásad přístupu ke službě Azure Time Series Insights Preview.

## <a name="sign-in-to-time-series-insights"></a>Přihlášení k Time Series Insights

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
1. Najděte své Time Series Insights prostředí. Do **vyhledávacího** pole zadejte `Time Series`. Ve výsledcích hledání vyberte **prostředí Time Series** .
1. Ze seznamu vyberte prostředí Time Series Insights.

## <a name="grant-data-access"></a>Udělení přístupu k datům

Pomocí těchto kroků udělíte přístup k datům pro objekt zabezpečení uživatele.

1. Vyberte **zásady přístupu k datům**a pak vybrat **+ Přidat**.

    [![Data-Access-One](media/data-access/data-access-one.png)](media/data-access/data-access-one.png#lightbox)

1. Zvolte **Vybrat uživatele**. Vyhledejte uživatelské jméno nebo e-mailovou adresu a vyhledejte uživatele, kterého chcete přidat. Vyberte **možnost vybrat** a potvrďte výběr.

    [@no__t – 1Data – přístup – dvě](media/data-access/data-access-two.png)](media/data-access/data-access-two.png#lightbox)

1. Zvolte **Vybrat roli**. Vyberte příslušnou roli přístupu pro uživatele:

    * Vyberte **Přispěvatel** , pokud chcete, aby uživatel mohl měnit referenční data a sdílet uložené dotazy a perspektivy s ostatními uživateli tohoto prostředí.

    * V opačném případě vyberte **Čtenář** , který uživateli umožní dotazovat se na data v prostředí a ukládat osobní, nesdílené a dotazy v prostředí.

   Výběrem **OK** potvrďte volbu role.

    [@no__t – 1Data – přístup – tři](media/data-access/data-access-three.png)](media/data-access/data-access-three.png#lightbox)

1. Na stránce **Vybrat roli uživatele** vyberte **OK** .

    [@no__t – 1Data – přístup – čtyři](media/data-access/data-access-four.png)](media/data-access/data-access-four.png#lightbox)

1. Potvrďte, že na stránce **zásady přístupu k datům** jsou uvedeni uživatelé a role pro každého uživatele.

    [@no__t – 1Data – přístup – pět](media/data-access/data-access-five.png)](media/data-access/data-access-five.png#lightbox)

## <a name="provide-guest-access-from-another-aad-tenant"></a>Poskytnutí přístupu hosta z jiného tenanta AAD

`Guest` není role správy. Jedná se o termín, který se používá pro účet, který je pozván z jednoho klienta na jiný. Po pozvání účtu hosta do adresáře klienta může být pro něj použito stejné řízení přístupu jako u jakéhokoli jiného účtu. Přístup pro správu můžete Time Series Insights prostředí udělit pomocí okna Access Control (IAM). Nebo můžete přístup k datům v prostředí udělit prostřednictvím okna zásady přístupu k datům. Pokud chcete získat další informace o přístupu klienta Azure Active Directory (Azure AD) k hostům, přečtěte si téma [přidání Azure Active Directory uživatelů pro spolupráci B2B v Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Pomocí těchto kroků udělíte uživateli služby Azure AD přístup hosta k Time Series Insights prostředí z jiného tenanta.

1. Vyberte **zásady přístupu k datům**a pak vybrat **+ pozvat**.

    [@no__t – 1Data – přístup – šest](media/data-access/data-access-six.png)](media/data-access/data-access-six.png#lightbox)

1. Zadejte e-mailovou adresu uživatele, kterého chcete pozvat. Tato e-mailová adresa musí být přidružená k Azure AD. Volitelně můžete přidat osobní zprávu s pozvánkou.

    [@no__t – 1Data – přístup – sedm](media/data-access/data-access-seven.png)](media/data-access/data-access-seven.png#lightbox)

1. Vyhledejte bublinu s potvrzením, která se zobrazí na obrazovce.

    [@no__t – 1Data – přístup – osm](media/data-access/data-access-eight.png)](media/data-access/data-access-eight.png#lightbox)

1. Zvolte **Vybrat uživatele**. Vyhledejte e-mailovou adresu uživatele typu Host, kterého jste pozvali, a vyhledejte uživatele, kterého chcete přidat. Pak **výběrem** potvrďte výběr.

    [@no__t – 1Data – přístup – devět](media/data-access/data-access-nine.png)](media/data-access/data-access-nine.png#lightbox)

1. Zvolte **Vybrat roli**. Vyberte příslušnou roli přístupu pro uživatele typu Host:

    * Vyberte **Přispěvatel** , pokud chcete, aby uživatel mohl měnit referenční data a sdílet uložené dotazy a perspektivy s ostatními uživateli tohoto prostředí.

    * V opačném případě vyberte **Čtenář** , který uživateli umožní dotazovat se na data v prostředí a ukládat osobní, nesdílené a dotazy v prostředí.

   Výběrem **OK** potvrďte volbu role.

    [![Data-Access-deset](media/data-access/data-access-ten.png)](media/data-access/data-access-ten.png#lightbox)

1. Na stránce **Vybrat roli uživatele** vyberte **OK** .

1. Potvrďte, že na stránce **zásady přístupu k datům** se zobrazuje uživatel typu Host a role pro každého uživatele typu Host.

    [@no__t – 1Data – přístup-jedenáct](media/data-access/data-access-eleven.png)](media/data-access/data-access-eleven.png#lightbox)

1. Uživatel typu Host musí nyní postupovat podle kroků pro přístup k prostředí nacházející se v tenantovi Azure, do kterého jste je pozvali. Nejdřív Přijměte pozvánku, kterou jste jim poslali. Tato pozvánka se pošle e-mailem na e-mailovou adresu, kterou jste použili v kroku 5. Vyberou možnost **začít** přijímat.

    [@no__t – 1Data – přístup – 12](media/data-access/data-access-twelve.png)](media/data-access/data-access-twelve.png#lightbox)

1. V dalším kroku uživatel typu Host přijme oprávnění přidružená k organizaci správce.

    [@no__t – 1Data-přístup-třináct](media/data-access/data-access-thirteen.png)](media/data-access/data-access-thirteen.png#lightbox)

1. Jakmile se uživatel typu Host přihlásí k e-mailové adrese, kterou jste použili k jejich pozvání, a pozvánku přijme, přejde na insights.azure.com. Jakmile tam dojde, v pravém horním rohu obrazovky vyberte miniaturu vedle své e-mailové adresy.

    [![Data-Access-14](media/data-access/data-access-fourteen.png)](media/data-access/data-access-fourteen.png#lightbox)

1. V dalším kroku uživatel typu host vybere vašeho tenanta Azure z rozevírací nabídky adresář. Tento tenant je ten, kterému jste pozvaní.

    [![Data-přístup-15](media/data-access/data-access-fifteen.png)](media/data-access/data-access-fifteen.png#lightbox)

Jakmile uživatel typu host vybere vašeho tenanta, uvidí Time Series Insights prostředí, do kterého jste jim poskytli přístup. Nyní mají všechny možnosti spojené s rolí, které jste jim poskytli v **kroku 5**.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak do prostředí Time Series Insights [Přidat zdroj událostí Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) .

* Odešle [události do zdroje událostí](./time-series-insights-send-events.md).

* Zobrazte [si prostředí v Průzkumníkovi služby Time Series Insights Preview](./time-series-insights-update-explorer.md).
