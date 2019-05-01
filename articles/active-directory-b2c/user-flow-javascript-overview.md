---
title: JavaScript a stránku smlouvy verze – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak povolit JavaScript a používání verze kontraktu stránek v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 91b4b621fc3dcedb52f88372fbfac222a744dbd1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570625"
---
# <a name="javascript-and-page-contract-versions-in-azure-active-directory-b2c"></a>Verze kontraktu jazyka JavaScript a stránky v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C poskytuje sadu obsah balíčku obsahující HTML, CSS a JavaScript pro prvky uživatelského rozhraní v toky uživatelů a vlastních zásad. Pokud chcete povolit JavaScript pro aplikace, je nutné přidat element, který má vaše [vlastní zásady](active-directory-b2c-overview-custom.md) nebo ji povolit na portálu pro toky uživatelů, vyberte smlouvy stránky a použijte [b2clogin.com](b2clogin.md) v požadavcích.

Pokud máte v úmyslu povolit [JavaScript](javascript-samples.md) kód na straně klienta, je vhodné opravdu že JavaScript založenou na prvky jsou neměnné. Všechny změny v opačném případě může způsobit neočekávané chování na stránkách uživatele. K těmto potížím předešli, můžete vynutit používání kontrakt stránky a určit verzi smlouvy stránky. To zajišťuje, že obsahu definice, které jsme na základě JavaScript jsou neměnné. I když nemáte v úmyslu povolit JavaScript, můžete určit verzi smlouvy stránky pro vaše stránky.

## <a name="user-flows"></a>Toky uživatele

Ve vlastnosti toku uživatele můžete povolit JavaScript, který také vynutí použití kontraktu stránky. Potom můžete nastavit verzi smlouvy stránky, jak je popsáno v další části.

![Povolit nastavení jazyka JavaScript](media/user-flow-javascript-overview/javascript-settings.png)

Jestli Povolit JavaScript v vlastnosti toku uživatele, můžete určit verzi smlouvy stránce pro stránky tok uživatele. Tok uživatele otevřete a vyberte **rozložení stránek**. V části **název rozložení**vyberte stránku toku uživatele a potom **verze kontraktu stránky**.

![Povolit nastavení jazyka JavaScript](media/user-flow-javascript-overview/page-contract-version.png)

## <a name="custom-policies"></a>Vlastní zásady

Chcete-li povolit JavaScript v vlastní zásady, je přidat **ScriptExecution** element **RelyingParty** element v souboru vlastních zásad. Další informace najdete v tématu [ukázky jazyka JavaScript pro použití v Azure Active Directory B2C](javascript-samples.md).

Jestli Povolit JavaScript v vlastní zásady, můžete určit verzi smlouvy stránky pro vaše stránky. Další informace o zadávání stránku smlouvy, naleznete v tématu [vyberte stránku smlouvu v Azure Active Directory B2C pomocí vlastních zásad](page-contract.md).

## <a name="next-steps"></a>Další postup

Zobrazit [ukázky jazyka JavaScript pro použití v Azure Active Directory B2C](javascript-samples.md).
