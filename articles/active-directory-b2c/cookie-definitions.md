---
title: Definice souborů cookie
titleSuffix: Azure AD B2C
description: Poskytuje definice pro soubory cookie používané v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 399b63cab2594610260997f8e5ecef9c3c05318f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712819"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definice souborů cookie pro Azure AD B2C

Následující části obsahují informace o souborech cookie používaných v Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSite

Služba Microsoft Azure AD B2C je kompatibilní s konfiguracemi prohlížeče SameSite, včetně podpory `SameSite=None` s atributem `Secure`.

Aby bylo možné chránit přístup k webům, budou webové prohlížeče zavádět nový zabezpečený model, který předpokládá, že všechny soubory cookie mají být chráněny z externího přístupu, pokud není uvedeno jinak. V prohlížeči Chrome je první implementace této změny od [Chrome 80 v únoru 2020](https://www.chromium.org/updates/same-site). Další informace o přípravě na změnu v Chrome najdete v tématu [vývojáři: Příprava na nové SameSite = None; Nastavení zabezpečeného souboru cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) na blogu Chromu

Vývojáři musí použít nové nastavení souborů cookie `SameSite=None`k určení souborů cookie pro přístup mezi weby. Pokud je přítomen atribut `SameSite=None`, musí být použit další atribut `Secure`, aby soubory cookie mezi weby byly dostupné pouze prostřednictvím připojení HTTPS. Ověří a otestuje všechny vaše aplikace, včetně aplikací, které používají Azure AD B2C.

Další informace najdete v tématu [vliv na zákaznické weby a služby a produkty Microsoftu v Chrome verze 80 nebo novější](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome).

## <a name="cookies"></a>Soubory cookie

V následující tabulce jsou uvedeny soubory cookie používané v Azure AD B2C.

| Name (Název) | Domain (Doména) | Vypršení platnosti | Účel |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Konec [relace prohlížeče](session-behavior.md) | Uchovává data o členství uživatelů napříč klienty. Klienti, na kterých je uživatel členem, a úroveň členství (správce nebo uživatel). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Slouží ke směrování požadavků do příslušné provozní instance. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Používá se ke sledování transakcí (počet požadavků na ověření Azure AD B2C) a aktuální transakce. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Používá se k údržbě relace jednotného přihlašování. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, značka domény | Konec [relace prohlížeče](session-behavior.md), úspěšné ověření | Slouží k údržbě stavu požadavku. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Token pro padělání žádostí mezi weby používaný pro CRSF ochranu. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Používá se pro Azure AD B2C síťové směrování. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Kontext |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Slouží k ukládání dat členství pro tenanta poskytovatele prostředků. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, značka domény | Konec [relace prohlížeče](session-behavior.md) | Slouží k uložení souboru cookie přenosu. |
