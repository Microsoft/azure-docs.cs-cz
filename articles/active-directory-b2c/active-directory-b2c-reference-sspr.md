---
title: Resetování hesla pomocí samoobslužné služby | Microsoft Docs
description: Ukazuje, jak nastavit samoobslužné resetování hesla pro vaše zákazníky v Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/17/2018
ms.author: davidmu
ms.openlocfilehash: 5b75455ad604b594a5f85fea8299d35a7d02c848
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
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

