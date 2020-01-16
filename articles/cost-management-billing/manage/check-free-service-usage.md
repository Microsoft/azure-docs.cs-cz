---
title: Monitorování a sledování využití bezplatných služeb Azure
description: Zjistěte, jak zkontrolovat využití bezplatných služeb na webu Azure Portal.
author: amberbhargava
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 83937e31d844ba0cc8efc17f1ecefa2ad6e7a7c1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992826"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Kontrola využití bezplatných služeb, které jsou součástí bezplatného účtu Azure

Za služby zahrnuté bezplatně v rámci bezplatného účtu Azure se neúčtují žádné poplatky, pokud nepřekročíte limity těchto služeb. Pokud chcete zůstat v mezích těchto limitů, můžete sledovat využití bezplatných služeb pomocí webu Azure Portal.

## <a name="check-usage-in-the-azure-portal"></a>Kontrola využití na webu Azure Portal

1.  Přihlaste se na web [Azure Portal](https://portal.azure.com).

2.  Vyhledejte **Předplatná**.

    ![Snímek obrazovky, který zobrazuje hledání předplatných na portálu](./media/check-free-service-usage/billing-search-subscriptions.png)

3.  Vyberte předplatné, které se vytvořilo při vaší registraci bezplatného účtu Azure.

4.  Posuňte se dolů, kde najdete tabulku s využitím bezplatných služeb.

    ![Snímek obrazovky ukazující využití bezplatných služeb](./media/check-free-service-usage/subscription-usage-free-services.png)

    Tabulka obsahuje následující sloupce:

* **Měřič:** Určuje měrnou jednotku spotřebované služby.
* **Využití/omezení:** Využití a omezení aktuálního měsíce pro měřič.
* **Stav:** Stav využití služby V závislosti na vašem využití se může zobrazit jeden z následujících stavů:
  * **Nepoužívá se:** Nepoužili jste měřič nebo využití pro měření nedosáhlo fakturačního systému.
  * **Překročení > data \<:** Překročili jste limit > data měřiče \<.
  * **Nepravděpodobné překročení:** U měřiče pravděpodobně nepřekračujete limit.
  * **Překračuje > datum \<:** Pravděpodobně budete překročili limit > data měřiče \<.

> [!IMPORTANT]
>
> Bezplatné služby jsou k dispozici pouze pro předplatné, které se vytvořilo, když jste si zaregistrovali bezplatný účet Azure. Pokud se na stránce s přehledem předplatného nezobrazí tabulka bezplatných služeb, znamená to, že pro dané předplatné nejsou bezplatné služby k dispozici.

## <a name="need-help-contact-us"></a>Potřebujete pomoct? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- [Upgrade bezplatného účtu Azure](upgrade-azure-subscription.md)
