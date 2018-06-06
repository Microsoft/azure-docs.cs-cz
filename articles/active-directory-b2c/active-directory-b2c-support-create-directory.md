---
title: Řešení potíží s vytváření klienty v Azure Active Directory B2C | Microsoft Docs
description: Problémy a řešení pro vytvoření klienta služby Azure Active Directory nebo Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 90d9d2fb80dfbd094754850b7d1270a5fafcdd96
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712500"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Řešení potíží s vytváření klienta služby Azure Active Directory nebo Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Vytvoření klienta Azure AD
Pokud při prvním pokusu nelze vytvořit klienta služby Azure Active Directory (Azure AD), zkuste to znovu. Pokud potíže potrvají, kontaktujte podporu Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C
Pokud narazíte na potíže při jste [vytvoření Azure Active Directory B2C klienta (Azure AD B2C)](active-directory-b2c-get-started.md), vyzkoušejte následující možnosti:

* Pokud klienta Azure AD B2C není zobrazena v seznamu klienty, zkuste to znovu vytvořit klienta.
* Pokud klienta Azure AD B2C objeví v seznamu klientů a zobrazí se následující chybová zpráva, odstraňte klienta a vytvořit znovu:

    "Nelze dokončit vytvoření klienta B2C 'contosob2c'. Navštivte toto [odkaz](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) další pokyny. "
* Existují známé problémy při odstranění existujícího klienta Azure AD B2C a znovu vytvořit pomocí stejného názvu domény. Při vytváření nového klienta Azure AD B2C, musíte použít jiný název domény.
* Pokud tato řešení nefungují, kontaktujte podporu Azure. Další informace najdete v tématu [soubor žádosti o podporu pro Azure AD B2C](active-directory-b2c-support.md).

