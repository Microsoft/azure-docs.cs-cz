---
title: JavaScript a stránku smlouvy verze pro toky uživatelů v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak povolit JavaScript a verze kontraktu stránky použít k přizpůsobení toku uživatele v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 997babef5860488232f0b530c90b44aba6608ac5
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008234"
---
# <a name="about-using-javascript-and-page-contract-versions-in-a-user-flow"></a>O používání jazyka JavaScript a stránky verze kontraktu v toku uživatele

Azure AD B2C poskytuje sadu obsah balíčku obsahující HTML, CSS a JavaScript pro prvky uživatelského rozhraní ve svých tocích uživatelů. Pokud máte v úmyslu povolit [JavaScript](javascript-samples.md) toku kódu na straně klienta v uživatelském, je vhodné opravdu že JavaScript založenou na prvky jsou neměnné. V opačném případě změny může způsobit neočekávané chování pro vaše uživatele tok stránky. K těmto potížím předešli, můžete vynutit používání kontrakt stránky pro tok uživatele a určit verzi smlouvy stránky. Tím se zajistí, že obsahu definice, které jsme na základě JavaScript jsou neměnné. I v případě, že nemáte v úmyslu povolit JavaScript pro tok uživatele, můžete určit verzi smlouvy stránce pro stránky tok uživatele.

> [!NOTE]
> Tento článek popisuje jazyka JavaScript pro toky uživatelů, ale můžete také pomocí JavaScriptu a vybrat verze kontraktu stránek, při použití [vlastní zásady](page-contract.md).

## <a name="enable-javascript"></a>Povolit JavaScript

Ve vlastnosti toku uživatele můžete povolit JavaScript, který také vynutí použití kontraktu stránky. Potom můžete nastavit verzi smlouvy stránky, jak je popsáno v další části.

![Povolit nastavení jazyka JavaScript](media/user-flow-javascript-overview/javascript-settings.PNG)

## <a name="specify-a-page-contract-version"></a>Zadejte verzi smlouvy stránky

Jestli Povolit JavaScript v vlastnosti toku uživatele, můžete určit verzi smlouvy stránce pro stránky tok uživatele. Tok uživatele otevřete a vyberte **rozložení stránek**. V části **název rozložení**vyberte stránku toku uživatele a potom **verze kontraktu stránky**.

![Povolit nastavení jazyka JavaScript](media/user-flow-javascript-overview/page-contract-version.PNG)

## <a name="next-steps"></a>Další postup
Zobrazit [ukázky jazyka JavaScript pro použití v Azure Active Directory B2C](javascript-samples.md).
