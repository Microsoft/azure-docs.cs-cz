---
title: Samoobslužné resetování hesla ve Azure Active Directory B2C | Dokumentace Microsoftu
description: Ukazuje, jak nastavit samoobslužné resetování hesla pro vaše zákazníky v Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c9d6a66a9d54c75fbb20775f0ae4de4d22a7d599
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840858"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Nastavení samoobslužného resetování hesla pro vaše zákazníky
Díky funkci resetování hesla pomocí samoobslužné služby mohou vaši zákazníci, kteří si zaregistrovali pro místní účty resetování hesel na své vlastní. To významně snižuje zatížení pracovníkům podpory, zejména v případě, že má vaše aplikace miliony zákazníci, kteří používají v pravidelných intervalech. V současné době pomocí ověřený e-mailová adresa je metoda pouze podporované obnovení.

> [!NOTE]
> Tento článek se týká hesla pomocí samoobslužné služby použít v kontextu rozhraní V1 resetování **přihlášení** tok uživatele, který používá **přihlášení místním účtem** jako zprostředkovatele identity. Pokud potřebujete toky uživatelů pro resetování plně přizpůsobitelná hesla vyvolané z vaší aplikace, přečtěte si [v tomto článku](active-directory-b2c-reference-policies.md#create-a-password-reset-user-flow).
> 
> 

Ve výchozím adresáři nemá hesla pomocí samoobslužné služby resetování zapnuté. Ho Pokud chcete zapnout, postupujte následovně:

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako správce předplatného. Toto je stejný pracovní nebo školní účet nebo stejný účet Microsoft, který jste použili k vytvoření adresáře.
2. Otevřít **Azure Active Directory** (na navigačním panelu na levé straně).
4. Nastavte **samoobslužné resetování hesla povoleno** k **všechny**. 
5. Klikněte na **Uložit** v horní části stránky. Je to!

Pokud chcete otestovat, pomocí funkce "Spustit nyní" na přihlášení uživatele tok, který má místní účty jako zprostředkovatele identity. Na přihlášení místním účtem stránce (Pokud zadáte e-mailovou adresu a heslo, nebo uživatelské jméno a heslo), klikněte na **nemá přístup k účtu?** ověřit uživatelské prostředí.

> [!NOTE]
> Na stránkách resetování hesla pomocí samoobslužné služby lze přizpůsobit pomocí [firemní branding funkce](../active-directory/fundamentals/customize-branding.md).
> 
> 

