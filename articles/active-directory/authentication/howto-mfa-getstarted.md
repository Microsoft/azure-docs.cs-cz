---
title: Začínáme s ověřováním Azure Multi-Factor Authentication v cloudu | Dokumentace Microsoftu
description: Toto je stránka ověřování Azure Multi-Factor Authentication, která popisuje, jak začít s Azure MFA v cloudu.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: get-started-article
ms.date: 06/24/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 0a822d55e8d7bd0d503eb7d77f96dc9e60e1a4ba
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33882864"
---
# <a name="getting-started-with-azure-multi-factor-authentication-in-the-cloud"></a>Začínáme s ověřováním Azure Multi-Factor Authenticatin v cloudu
V tomto článku se dozvíte, jak začít používat Azure Multi-Factor Authentication v cloudu.

> [!NOTE]
> V následující dokumentaci najdete informace o tom, jak uživatelům povolit používání webu **Azure Portal**. Pokud hledáte informace o nastavení ověřování Azure Multi-Factor Authentication pro uživatele O365, najdete je v tématu [Nastavení ověřování Multi-Factor Authentication pro Office 365.](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=en-US&rs=en-US&ad=US)

![MFA v cloudu](./media/howto-mfa-getstarted/mfa_in_cloud.png)

## <a name="prerequisite"></a>Požadavek
[Zaregistrujte si předplatné Azure](https://azure.microsoft.com/pricing/free-trial/) – pokud ještě nemáte předplatné Azure, musíte se zaregistrovat. Pokud právě začínáte a používáte Azure MFA, můžete použít zkušební verzi předplatného

## <a name="enable-azure-multi-factor-authentication"></a>Povolení služby Azure Multi-Factor Authentication
Pokud vaši uživatelé mají licence, které zahrnují službu Azure Multi-Factor Authentication, pro zapnutí Azure MFA není potřeba nic dělat. Můžete začít s vyžadováním dvoustupňového ověřování u jednotlivých uživatelů. Licence, které umožňují Azure MFA, jsou:
- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

Pokud nemáte některou z těchto tří licencí nebo nemáte dostatek licencí pro pokrytí všech uživatelů, nic se neděje. Stačí jenom přidat další krok a v adresáři [vytvořit poskytovatele Multi-Factor Auth](concept-mfa-authprovider.md).

## <a name="turn-on-two-step-verification-for-users"></a>Zapnutí dvoustupňového ověřování pro uživatele

Pokud chcete začít používat Azure MFA, postupujte podle některého z postupů uvedených v tématu [Vyžadování dvoustupňového ověřování pro uživatele nebo skupinu](howto-mfa-userstates.md). Můžete se rozhodnout vynucovat dvoustupňové ověřování pro všechna přihlášení nebo můžete vytvořit zásady podmíněného přístupu a vyžadovat dvoustupňové ověřování, jenom když potřebujete.

## <a name="next-steps"></a>Další kroky
Teď, když máte nastavené Azure Multi-Factor Authentication v cloudu, můžete konfigurovat a nastavit nasazení. Další podrobnosti najdete v části [Konfigurace Azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

