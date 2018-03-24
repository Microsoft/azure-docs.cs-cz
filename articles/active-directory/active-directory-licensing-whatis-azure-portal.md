---
title: Co je na základě skupin licencování v Azure Active Directory? | Dokumenty Microsoft
description: Popis služby Azure Active Directory na základě skupiny licencí, jak to funguje a doporučené postupy
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2018
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: 1d4151d6c00da618cc008257bcdad4607f8fec49
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Na základě skupiny licencování základy v Azure Active Directory

Použití Microsoft placené cloudové služby, jako je například Office 365, Enterprise Mobility + Security, Dynamics CRM a další podobné produkty, vyžaduje licence. Tyto licence jsou přiřazeny každý uživatel, který potřebuje přístup k těmto službám. Pokud chcete spravovat licence, správci pomocí jedné z portály pro správu (Office nebo Azure) a rutin prostředí PowerShell. Azure Active Directory (Azure AD) je základní infrastruktura, která podporuje správu identit pro všechny cloudové služby Microsoftu. Azure AD ukládá informace o stavu přiřazení licence pro uživatele.

Dosud licence lze přiřadit pouze na úrovni jednotlivých uživatelů, což mohou ztížit správu ve velkém měřítku. Například pokud chcete přidat nebo odebrat uživatelských licencí podle změn v organizaci, jako jsou uživatelé propojení nebo bez organizace nebo oddělení, správce často musíte napsat skript prostředí PowerShell komplexní. Tento skript volá jednotlivé cloudové služby.

Chcete-li tyto problémy vyřešit, obsahuje teď Azure AD na základě skupin licencí. Jeden nebo více licencí produktu můžete přiřadit ke skupině. Azure AD zajistí, že licence jsou přiřazené k všechny členy skupiny. Žádné nové členy, kteří připojit ke skupině jsou přiřazeni příslušné licence. Po skončení skupiny, tyto licence budou odebrány. Tím se eliminuje potřeba pro automatizaci správy licencí pomocí prostředí PowerShell, aby se projevily změny v organizaci a oddělení struktura na jednotlivé uživatele.

## <a name="features"></a>Funkce

Zde jsou hlavní funkce na základě skupiny licencí:

- Licence může být přiřazeny do žádné skupiny zabezpečení ve službě Azure AD. Skupiny zabezpečení, může být synchronizovaná místně pomocí Azure AD Connect. Skupiny zabezpečení můžete taky vytvořit přímo ve službě Azure AD (také nazývané skupiny jenom pro cloud) nebo automaticky pomocí funkce Dynamická skupina Azure AD.

- Pokud je licence produktu přiřazen do skupiny, můžete zakázat správce jeden nebo více plánů služby v rámci produktu. Obvykle to se provádí při organizace ještě není připravený k použití služby zahrnuté v produktu. Například správce může přiřadit oddělení služeb Office 365, ale dočasně zakázat službu Yammer.

- Jsou podporovány všem cloudovým službám Microsoftu, které vyžadují individuální licencování. To zahrnuje všechny produkty, Enterprise Mobility + Security a Dynamics CRM v Office 365.

- Na základě skupiny licencí je aktuálně k dispozici pouze prostřednictvím [portálu Azure](https://portal.azure.com). Pokud používáte jiné portály pro správu především pro uživatele a skupiny správy, jako je například portál Office 365, můžete to udělat. Ale byste měli používat portál Azure ke správě licencí na úrovni skupiny.

- Azure AD automaticky spravuje úpravy licencí, které jsou výsledkem změn členství ve skupinách. Změny licencí jsou obvykle efektivní minut změny členství.

- Uživatel může být členem více skupin se zásadami licencí zadaná. Uživatel může mít také některé licence, které byly přímo přiřazeny, mimo žádné skupiny. Výsledný stav uživatele je kombinací všech přiřazené produktu a licence služby.

- V některých případech nelze přiřadit licence pro uživatele. Například v klientovi nemusí být dostatek dostupných licencí, nebo ve stejnou dobu může byly přiřazeny konfliktní služby. Správci mají přístup k informacím o uživatelích, které Azure AD nebylo možné plně zpracovat skupiny licencí. Poté můžete začít opravné akce na základě těchto informací.

- Během verzi public preview zkušební nebo placené předplatné pro edice Azure AD Basic nebo Premium k použití správy na základě skupiny licencí vyžaduje v klientovi.

## <a name="your-feedback-is-welcome"></a>Vaše zpětná vazba je Vítejte!

Pokud máte zpětnou vazbu nebo funkce požadavky, prosím sdílet je s ním pomocí [Toto fórum](https://feedback.azure.com/forums/169401-azure-active-directory/category/317677-group-based-licensing).

## <a name="next-steps"></a>Další postup

Další informace o scénáře pro správu licencí prostřednictvím na základě skupin licencí najdete v tématu:

* [Začínáme s licencí v Azure Active Directory](active-directory-licensing-get-started-azure-portal.md)
* [Přiřazování licencí pro skupinu v Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identifikace a řešení potíží s licencí pro skupinu v Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Postup migrace na základě skupiny licencí v Azure Active Directory pro jednotlivé licencovaní uživatelé](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory na základě skupin licencí další scénáře](active-directory-licensing-group-advanced.md)
