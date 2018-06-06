---
title: Hesla pomocí samoobslužné služby resetování v Azure Active Directory B2C | Microsoft Docs
description: Ukazuje, jak nastavit samoobslužné resetování hesla pro vaše zákazníky v Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ea8b23618b382f557340643afd62e56932bbfb2d
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712092"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Nastavení samoobslužného resetování hesla pro vaše zákazníky
Funkce resetování hesla pomocí samoobslužné služby s svým zákazníkům, kteří zaregistrovali pro místní účty mohou resetovat svá hesla na své vlastní. To významně snižuje zátěž vašim zaměstnancům technické podpory, zvlášť pokud vaše aplikace nemá miliony zákazníky používající v pravidelných intervalech. V současné době používá ověřenou e-mailovou adresu je metoda pouze podporované obnovení.

> [!NOTE]
> Tento článek se týká hesla pomocí samoobslužné služby resetování použít v kontextu zásady přihlášení. Pokud potřebujete zásady resetování plně přizpůsobitelná hesel volána z vaší aplikace, najdete v části [v tomto článku](active-directory-b2c-reference-policies.md#create-a-password-reset-policy).
> 
> 

Ve výchozím adresáři nemá hesla pomocí samoobslužné služby resetování zapnutý. Chcete-li pomocí následujících kroků:

1. Přihlaste se k [portál Azure](https://portal.azure.com/) jako správce předplatného. Toto je stejný pracovní nebo školní účet nebo stejný účet Microsoft, který jste použili k vytvoření adresáře.
2. Otevřete **Azure Active Directory** (v navigačním panelu na levé straně).
4. Nastavit **samoobslužném resetování hesla služby povolena** k **všechny**. 
5. Klikněte na tlačítko **Uložit** v horní části stránky. Hotovo!

K testování, pomocí funkce "Spustit nyní" na všechny zásady přihlášení, který má místní účty jako zprostředkovatele identity. Na přihlášení místní účet stránka (kde zadáte e-mailovou adresu a heslo, nebo uživatelské jméno a heslo), klikněte na tlačítko **nelze získat přístup k účtu?** ověření zkušeností zákazníků.

> [!NOTE]
> Na stránkách resetování hesla pomocí samoobslužné služby lze přizpůsobit pomocí [firemního brandingu funkce](../active-directory/customize-branding.md).
> 
> 

