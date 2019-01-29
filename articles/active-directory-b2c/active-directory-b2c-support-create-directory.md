---
title: Řešení potíží s vytváření tenantů ve službě Azure Active Directory B2C | Dokumentace Microsoftu
description: Problémy a řešení pro vytváření tenanta služby Azure Active Directory nebo Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b8075e084481a9951c13896fa4db41613adc388f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160353"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Řešení problémů s vytvořením tenanta služby Azure Active Directory nebo Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Vytvoření tenanta Azure AD
Pokud při prvním pokusu nelze vytvořit tenanta služby Azure Active Directory (Azure AD), zkuste to znovu. Pokud se problém nevyřeší, obraťte se na podporu Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Vytvoření tenanta Azure AD B2C
Pokud narazíte na problémy při vám [vytvoření Azure Active Directory B2C tenantovi (Azure AD B2C)](active-directory-b2c-get-started.md), vyzkoušejte následující možnosti:

* Pokud tenanta Azure AD B2C není uveden v seznamu tenantů, zkuste znovu vytvořit tenanta.
* Pokud tenanta Azure AD B2C zobrazí v seznamu tenantů a zobrazí se následující chybová zpráva, odstraněním tenanta a znovu ji vytvořit:

    "Nemohl dokončit vytváření tenanta B2C"contosob2c". Najdete to [odkaz](https://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) další pokyny. "
* Dochází ke známým problémům při odstranění existujícího tenanta Azure AD B2C a znovu ho vytvořte s využitím se stejným názvem domény. Když vytvoříte nového tenanta Azure AD B2C, musíte použít jiný název domény.
* Pokud tyto řešení nefungují, obraťte se na podporu Azure. Další informace najdete v tématu [soubor žádosti o podporu pro Azure AD B2C](active-directory-b2c-support.md).

