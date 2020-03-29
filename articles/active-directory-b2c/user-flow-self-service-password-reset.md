---
title: Samoobslužné resetování hesla ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Ukazuje, jak nastavit samoobslužné resetování hesla pro zákazníky ve službě Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d6dad52c8a3e63c64bb8e0e0030e8c50b5bab42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183104"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Nastavení samoobslužného resetování hesla pro zákazníky

Díky funkci samoobslužného resetování hesla mohou vaši zákazníci, kteří se zaregistrovali k místním účtům, resetovat svá hesla sami. To výrazně snižuje zátěž pro pracovníky podpory, zejména pokud vaše aplikace má miliony zákazníků, kteří ji pravidelně používají. V současné době je použití ověřené e-mailové adresy jedinou podporovanou metodou obnovení.

> [!NOTE]
> Tento článek se vztahuje na samoobslužné resetování hesla používané v kontextu toku uživatele **přihlášení** V1, který používá **místní účet Přihlásit** jako zprostředkovatele identity. Pokud potřebujete plně přizpůsobitelné toky uživatelů pro resetování hesla vyvolané z vaší aplikace, přečtěte si [tento článek](user-flow-overview.md).
>
>

Ve výchozím nastavení není v adresáři zapnuto samoobslužné resetování hesla. Pomocí následujících kroků jej zapněte:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako správce předplatného. Jedná se o stejný pracovní nebo školní účet nebo stejný účet Microsoft, který jste použili k vytvoření adresáře.
2. Otevřete **Službu Azure Active Directory** (na navigačním panelu na levé straně).
3. Posuňte se dolů na okno voleb a vyberte **Resetování hesla**.
4. Nastavte **samoobslužné resetování hesla povoleno** na **všechny**.
5. Klikněte na **Uložit** v horní části stránky. A je to hotové!

Chcete-li otestovat, použijte funkci "Spustit nyní" na všech přihlašování uživatele tok, který má místní účty jako zprostředkovatel identity. Na přihlašovací stránce místního účtu (kde zadáte e-mailovou adresu a heslo nebo uživatelské jméno a heslo) ověřte zákazníka kliknutím na možnost Nelze **získat přístup ke svému účtu?**

> [!NOTE]
> Samoobslužné stránky pro resetování hesla lze přizpůsobit pomocí [funkce firemníznačky](../active-directory/fundamentals/customize-branding.md).
>
>

