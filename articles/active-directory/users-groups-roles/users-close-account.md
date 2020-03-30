---
title: Uzavření pracovního nebo školního účtu v nespravovaném adresáři Azure AD
description: Jak zavřít pracovní nebo školní účet v nespravované službě Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815721"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>Zavření pracovního nebo školního účtu v nespravovaném adresáři

Pokud jste uživatelem v nespravované organizaci Azure Active Directory (Azure AD) a už nemusíte používat aplikace z této organizace ani s ní udržovat jakékoli přidružení, můžete svůj účet kdykoli zavřít. Nespravovaný adresář nemá globálního správce. Uživatelé v nespravovaném adresáři mohou své účty zavřít sami, aniž by museli kontaktovat správce.

Uživatelé v nespravovaném adresáři jsou často vytvářeny během samoobslužné registrace. Příkladem může být informační pracovník v organizaci, který se zaregistruje k bezplatné službě. Další informace o samoobslužné registraci najdete v tématu [Co je samoobslužná registrace pro Službu Azure Active Directory?](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>Než začnete

Před uzavřením účtu byste měli potvrdit následující položky:

* Ujistěte se, že jste uživatelem nespravovaného adresáře Azure AD. Pokud patříte do spravovaného adresáře, nemůžete svůj účet zavřít. Pokud patříte do spravovaného adresáře a chcete svůj účet zavřít, musíte se obrátit na správce. Informace o tom, jak určit, zda patříte do nespravovaného adresáře, naleznete [v tématu Odstranění uživatele z nespravovaného klienta](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant).

* Uložte všechna data, která chcete zachovat. Informace o odeslání žádosti o export naleznete [v tématu Přístup k protokolům generovaným systémem a jejich export pro nespravované klienty](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants).

> [!WARNING]
> Uzavření účtu je nevratné. Po uzavření účtu budou odstraněny všechny osobní údaje. Již nebudete mít přístup ke svému účtu a údajům spojeným s vaším účtem.

## <a name="close-your-account"></a>Zavření vašeho účtu

Chcete-li uzavřít nespravovaný pracovní nebo školní účet, postupujte takto:

1. Přihlaste se a [zavřete svůj účet](https://go.microsoft.com/fwlink/?linkid=873123)pomocí účtu, který chcete uzavřít.

1. V části **Moje požadavky na data**vyberte Zavřít **účet**.

    ![Moje žádosti o data - Zavřít účet](./media/users-close-account/close-account.png)

1. Zkontrolujte potvrzovací zprávu a pak vyberte **Ano**.

    ![Moje žádosti o data - Potvrdit zavření](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>Další kroky

- [Co je samoobslužná registrace pro Službu Azure Active Directory?](directory-self-service-signup.md)
- [Odstranění uživatele z nespravovaného tenanta](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [Přístup k systémem generovaným protokolům pro nespravované tenanty a jejich export](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
