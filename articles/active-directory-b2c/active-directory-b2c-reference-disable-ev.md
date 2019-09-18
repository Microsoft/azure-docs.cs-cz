---
title: Zakázat ověřování e-mailů při registraci uživatele v Azure Active Directory B2C | Microsoft Docs
description: Téma demonstrující, jak zakázat ověřování e-mailů během registrace uživatele v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d827843bdbaff1ad95dce3318f39867abe8b2d0f
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065626"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Zakázat ověřování e-mailů při registraci uživatele v Azure Active Directory B2C
Pokud je povoleno, Azure Active Directory B2C (Azure AD B2C) poskytne spotřebiteli možnost Registrovat se k aplikacím zadáním e-mailové adresy a vytvořením místního účtu. Azure AD B2C zajišťuje platné e-mailové adresy tím, že je vyžaduje, aby je uživatelé během procesu registrace ověřili. Zároveň zabrání škodlivému automatizovanému procesu generování falešných účtů pro aplikace.

Někteří vývojáři aplikací preferují během procesu registrace ověřování e-mailu a místo toho můžou e-mailovou adresu ověřit později. V případě potřeby je Azure AD B2C možné nakonfigurovat pro zákaz ověřování e-mailů. Tím dojde k vytvoření plynulého procesu registrace a vývojářům poskytuje flexibilitu v tom, aby vylišili uživatele, kteří ověřili jejich e-mailovou adresu od zákazníků, kteří ne.

Ve výchozím nastavení má přihlášení uživatelských toků zapnuté ověřování e-mailů. Pro vypnutí použijte následující postup:

1. Klikněte na **toky uživatelů**.
2. Kliknutím na svůj uživatelský tok (například "B2C_1_SiUp") ho otevřete.
3. Klikněte na **rozložení stránky**.
4. Klikněte na **přihlašovací stránku místního účtu**.
5. Klikněte na **e-mailová adresa** ve sloupci **název** v části **atributy uživatele** .
6. V části **vyžaduje ověření**vyberte **ne**.
7. V horní části okna klikněte na **Uložit**. Už jste hotovi!

> [!NOTE]
> Vypnutí ověřování e-mailu v procesu registrace může vést k spamu. Pokud výchozí nastavení zakážete, doporučujeme přidat vlastní ověřovací systém.
>
>
