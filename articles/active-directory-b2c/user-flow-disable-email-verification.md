---
title: Zakázat ověřování e-mailů při registraci zákazníka
titleSuffix: Azure AD B2C
description: Naučte se zakázat ověřování e-mailů při registraci zákazníka v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c598664ef721103740716ad6215ddaea53bc635f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183070"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Zakázat ověřování e-mailů při registraci zákazníka v Azure Active Directory B2C

Ve výchozím nastavení Azure Active Directory B2C (Azure AD B2C) ověřuje e-mailovou adresu vašeho zákazníka u místních účtů (účty pro uživatele, kteří se registrují pomocí e-mailové adresy nebo uživatelského jména). Azure AD B2C zajišťuje platné e-mailové adresy tím, že je vyžaduje, aby je uživatelé během procesu registrace ověřili. Zabrání taky škodlivým objektům actor v použití automatizovaných procesů ke generování podvodných účtů ve vašich aplikacích.

Někteří vývojáři aplikací chtějí během procesu registrace přeskočit ověřování e-mailu a místo toho můžou e-mailové adresy ověřit později. V případě potřeby je Azure AD B2C možné nakonfigurovat pro zákaz ověřování e-mailů. Tím dojde k vytvoření plynulého procesu registrace a vývojářům umožníte odlišit zákazníky, kteří si ověřili jejich e-mailovou adresu od zákazníků, kteří ne.

Ověřování e-mailu zakážete pomocí těchto kroků:

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Pomocí filtru **adresář a odběr** v horní nabídce vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Vyberte tok uživatele, pro který chcete zakázat ověřování e-mailů. Například *B2C_1_signinsignup*.
1. Vyberte **rozložení stránky**.
1. Vyberte **stránku pro registraci místního účtu**.
1. V části **atributy uživatele**vyberte **e-mailová adresa**.
1. V rozevíracím seznamu **vyžaduje ověření** vyberte možnost **ne**.
1. Vyberte **Save** (Uložit). Ověřování e-mailu je teď pro tento tok uživatelů zakázané.

> [!WARNING]
> Vypnutí ověřování e-mailu v procesu registrace může vést k spamu. Pokud zakážete výchozí ověřování e-mailů poskytnutých Azure AD B2C, doporučujeme implementovat systém náhradního ověřování.
