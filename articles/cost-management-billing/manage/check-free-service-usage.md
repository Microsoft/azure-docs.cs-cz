---
title: Monitorování a sledování využití bezplatných služeb Azure
description: Zjistěte, jak zkontrolovat využití bezplatných služeb na webu Azure Portal. Za služby, které jsou součástí bezplatného účtu, se neúčtují žádné poplatky, pokud nepřekročíte jejich limity.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: banders
ms.openlocfilehash: c7c28e64822a6aefa17e8baa4ef42a3b3fea8adb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589772"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Kontrola využití bezplatných služeb, které jsou součástí bezplatného účtu Azure

Za služby zahrnuté bezplatně v rámci bezplatného účtu Azure se neúčtují žádné poplatky, pokud nepřekročíte limity těchto služeb. Pokud chcete zůstat v mezích těchto limitů, můžete sledovat využití bezplatných služeb pomocí webu Azure Portal.

## <a name="check-usage-in-the-azure-portal"></a>Kontrola využití na webu Azure Portal

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1.  Vyhledejte **Předplatná**.  
    ![Snímek obrazovky, který zobrazuje hledání předplatných na portálu](./media/check-free-service-usage/billing-search-subscriptions.png)
1.  Vyberte předplatné, které se vytvořilo při vaší registraci bezplatného účtu Azure.
1.  Posuňte se dolů, kde najdete tabulku s využitím bezplatných služeb.  
    ![Snímek obrazovky ukazující využití bezplatných služeb](./media/check-free-service-usage/subscription-usage-free-services.png)

Tabulka obsahuje následující sloupce:

* **Měřič:** Určuje měrnou jednotku spotřebovávané služby.
* **Využití/limit:** Využití za aktuální měsíc a limit daného měřiče.
* **Stav:** Stav využití dané služby. V závislosti na vašem využití se může zobrazit jeden z následujících stavů:
  * **Nepoužívá se:** Měřič nepoužíváte nebo fakturační systém ještě nepřijal informace o využití z měřiče.
  * **Překročeno dne \<Date>:** Dne \<Date> jste překročili limit měřiče.
  * **Pravděpodobně se nepřekročí:** Pravděpodobně nepřekročíte limit měřiče.
  * **Překročí se dne \<Date>:** Dne \<Date> pravděpodobně překročíte limit měřiče.

> [!IMPORTANT]
>
> Bezplatné služby jsou k dispozici pouze pro předplatné, které se vytvořilo, když jste si zaregistrovali bezplatný účet Azure. Pokud se na stránce s přehledem předplatného nezobrazí tabulka bezplatných služeb, znamená to, že pro dané předplatné nejsou bezplatné služby k dispozici.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- [Upgrade bezplatného účtu Azure](upgrade-azure-subscription.md)
