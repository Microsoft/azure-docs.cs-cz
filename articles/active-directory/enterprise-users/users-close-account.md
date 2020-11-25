---
title: Uzavření pracovního nebo školního účtu v nespravované organizaci Azure AD
description: Jak zavřít svůj pracovní nebo školní účet v nespravovaném Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17a5042df0c2ebe9f1216ded1354c7beba28c911
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95996776"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>Zavřete svůj pracovní nebo školní účet v nespravované organizaci Azure AD.

Pokud jste uživatelem v nespravovaném Azure Active Directory (Azure AD) a už nepotřebujete používat aplikace z této organizace ani nemusíte spravovat žádné přidružení, můžete účet kdykoli zavřít. Nespravovaná organizace nemá globálního správce. Uživatelé v nespravované organizaci můžou svoje účty zavřít sami, aniž by museli kontaktovat správce.

Uživatelé v nespravované organizaci se často vytvářejí při registraci samoobslužných služeb. Příkladem může být informační pracovník v organizaci, který si zaregistruje bezplatnou službu. Další informace o samoobslužném přihlášení najdete v tématu [co je samoobslužná registrace pro Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Než začnete

Než budete moct účet zavřít, zkontrolujte, že máte následující položky:

* Ujistěte se, že jste uživatelem nespravované organizace Azure AD. Pokud patříte do spravované organizace, účet nemůžete zavřít. Pokud patříte do spravované organizace a chcete účet zavřít, musíte se obrátit na správce. Informace o tom, jak určit, zda patříte do nespravované organizace, najdete v tématu [odstranění uživatele z nespravovaného tenanta](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Uložte všechna data, která chcete zachovat. Informace o tom, jak odeslat žádost o export, najdete v tématu [přístup k systémem generovaným protokolům a jejich export pro nespravované klienty](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Zavření účtu je nevratné. Po zavření účtu se odeberou všechny osobní údaje. Už nebudete mít přístup k vašemu účtu a datům přidruženým k vašemu účtu.

## <a name="close-your-account"></a>Zavření vašeho účtu

Pokud chcete zavřít nespravovaný pracovní nebo školní účet, postupujte podle těchto kroků:

1. Přihlaste se, abyste mohli [účet zavřít](https://go.microsoft.com/fwlink/?linkid=873123), a to pomocí účtu, který chcete zavřít.

1. V **okně žádosti o data** vyberte **Zavřít účet**.

    ![Moje požadavky na data – zavřít účet](./media/users-close-account/close-account.png)

1. Zkontrolujte potvrzovací zprávu a pak vyberte **Ano**.

    ![Moje žádosti o data – potvrdit zavření](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Další kroky

- [Co je samoobslužná registrace do služby Azure Active Directory?](directory-self-service-signup.md)
- [Odstranění uživatele z nespravovaného tenanta](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Přístup k systémem generovaným protokolům pro nespravované tenanty a jejich export](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)