---
title: Složitost hesla – Azure AD B2C | Microsoft Docs
description: Postup konfigurace požadavky na složitost hesel poskytl příjemce v Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/16/2017
ms.author: davidmu
ms.openlocfilehash: b89a404d583e9d495b65180e145295e6a106b3ab
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Konfigurujte požadavky na složitost hesel

> [!NOTE]
> **Tato funkce je ve verzi preview.**  Obraťte se na [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com) tak, aby měl váš klient testovací povolit tuto funkci.  Neprovádějte testování to na produkční klienty.

Azure Active Directory B2C (Azure AD B2C) podporuje změna požadavky na složitost hesla poskytl koncového uživatele při vytváření účtu.  Ve výchozím nastavení používá Azure AD B2C `Strong` hesla.  Azure AD B2C podporuje také konfigurační možnosti pro řízení složitosti hesla, které zákazníci mohou používat.

## <a name="when-password-rules-are-enforced"></a>Když se vynucují pravidla pro hesla

Během registrace nebo resetování hesla, koncový uživatel musí zadat heslo, které splňuje pravidla složitosti.  Podle zásad se vynucují pravidla složitosti hesla.  Je možné, že jeden zásad vyžadovat kód pin čtyřciferné během registrace chvíli vyžaduje jinou zásadou řetězec osm znaků během registrace.  Může například pomocí zásad se složitost jiné heslo pro dospělé než pro děti.

Složitost hesla se nikdy vynucují během přihlášení.  Uživatelé nikdy vyzváni během přihlášení změnit své heslo, protože nesplňuje požadavek na aktuální složitost.

Toto jsou typy zásad konfigurovaným složitost hesla:

* Zásady registrace nebo přihlášení
* Zásady resetování hesel
* Vlastní zásady ([nakonfigurujte složitost hesel ve vlastních zásadách pro](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Postup konfigurace složitost hesla

1. Postupujte podle těchto kroků [přejděte do nastavení Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
1. Otevřete **registrace nebo přihlášení zásady**.
1. Vyberte zásadu a klikněte na **upravit**.
1. Otevřete **složitost hesla**.
1. Změnit složitost hesla pro tuto zásadu **jednoduché**, **silným**, nebo **vlastní**.

### <a name="comparison-chart"></a>Porovnávací graf

| Složitost | Popis |
| --- | --- |
| Jednoduchý | Heslo, které je alespoň 8 až 64 znaků. |
| Silné | Heslo, které je alespoň 8 až 64 znaků. To vyžaduje 3 ze 4 malá písmena, velká písmena, číslice a symboly. |
| Vlastní | Tato možnost poskytuje maximální kontrolu nad pravidla složitosti hesla.  Umožňuje konfiguraci vlastní délka.  Umožňuje také přijetí hesla jenom číslo (PIN). |

## <a name="options-available-under-custom"></a>Možnosti, které jsou k dispozici v části vlastní

### <a name="character-set"></a>Znakové sady

Umožňuje přijímat pouze číslice (PIN) nebo celý znaková sada.

* **Pouze čísla** umožňuje číslic pouze (0-9) při zadávání hesla.
* **Všechny** umožňuje jakékoli písmeno, číslo nebo symbol.

### <a name="length"></a>Délka

Umožňuje řídit požadavky na délku hesla.

* **Minimální délka** musí být aspoň na 4.
* **Maximální délka** musí být větší nebo rovna minimální délku a může obsahovat maximálně 64 znaků.

### <a name="character-classes"></a>Třídy znaků

Umožňuje řídit typy různých znakových použít heslo.

* **2 4: malé písmeno, velké písmeno, číslici (0-9), Symbol** zajišťuje heslo obsahuje alespoň dva typy znaků. Například číslo a malé písmeno.
* **3 4: malé písmeno, velké písmeno, číslici (0-9), Symbol** zajišťuje heslo obsahuje alespoň dva typy znaků. Například: číslo, malé písmeno a velké písmeno.
* **4 4: malé písmeno, velké písmeno, číslici (0-9), Symbol** zajišťuje heslo obsahuje všechny pro znakové typy.

    > [!NOTE]
    > Vyžadování **4 4** může mít za následek před koncového uživatele. Některé ze studií vyplývá, že tento požadavek není zlepšit heslo šifrování. V tématu [pokynů NIST heslo](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
