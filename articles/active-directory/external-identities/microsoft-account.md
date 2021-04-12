---
title: Poskytovatel identity účtu Microsoft (MSA) ve službě Azure AD
description: Pomocí Azure AD můžete externímu uživateli (Guest) přihlašovat se k aplikacím Azure AD pomocí jejich účet Microsoft (MSA).
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d3380277d0e653fd5cb03069b7d91cb363dd95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101693058"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities-preview"></a>Poskytovatel identity účtu Microsoft (MSA) pro externí identity (Preview)

> [!NOTE]
> Poskytovatel identity účtu Microsoft je funkce Azure Active Directory ve verzi Public Preview. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Uživatelé typu Host B2B můžou používat vlastní osobní účty Microsoft pro spolupráci B2B bez další konfigurace. Uživatelé typu Host můžou uplatnit pozvánky na spolupráci B2B nebo dokončit toky registrace uživatelů pomocí jejich osobního účet Microsoft.

Účty Microsoft nastaví uživatel, který získá přístup k zákaznickým produktům a cloudovým službám Microsoftu, jako je například Outlook, OneDrive, Xbox LIVE nebo Microsoft 365. Účet je vytvořený a uložený v systému účtu Microsoft Consumer identity, který je spuštěný Microsoftem.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Přihlášení hosta pomocí účtů Microsoft

Účet Microsoft je ve výchozím nastavení k dispozici v seznamu zprostředkovatelů identity externích identit. Žádná další konfigurace není nutná k tomu, aby se uživatelé typu Host mohli přihlašovat pomocí svého účet Microsoft pomocí toku pozvání nebo uživatelského toku samoobslužné registrace.

![Účet Microsoft v seznamu zprostředkovatelé identit](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>Účet Microsoft v toku pozvánky

Při [pozvání uživatele typu Host](add-users-administrator.md) ke spolupráci B2B můžete zadat jejich účet Microsoft jako e-mailovou adresu, kterou budou používat k přihlášení.

![Pozvání pomocí účet Microsoft](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>Účet Microsoft v uživatelských tocích samoobslužné registrace

Účet Microsoft je možnost poskytovatele identity pro uživatelské toky samoobslužné registrace. Uživatelé se můžou k vašim aplikacím zaregistrovat pomocí vlastních účtů Microsoft. Nejdřív budete muset [Povolit samoobslužnou registraci](self-service-sign-up-user-flow.md) pro vašeho tenanta. Potom můžete nastavit tok uživatele pro aplikaci a vybrat účet Microsoft jako jednu z možností přihlášení.

![účet Microsoft v uživatelském toku samoobslužné registrace](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="next-steps"></a>Další kroky

- [Přidat Azure Active Directory uživatelů spolupráce B2B](add-users-administrator.md)
- [Přidání samoobslužné registrace do aplikace](self-service-sign-up-user-flow.md)