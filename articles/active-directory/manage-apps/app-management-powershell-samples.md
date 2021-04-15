---
title: Ukázky PowerShellu pro správu aplikací Azure Active Directory
description: Tyto ukázky PowerShellu se používají pro aplikace, které spravujete ve vašem tenantovi Azure Active Directory. Pomocí těchto ukázkových skriptů můžete najít informace o vypršení platnosti tajných kódů a certifikátů.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 02/18/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: 6b6314935bfafc2fe6288c30619e1d01242a991d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378818"
---
# <a name="azure-active-directory-powershell-examples-for-application-management"></a>Příklady Azure Active Directory PowerShellu pro správu aplikací

Následující tabulka obsahuje odkazy na příklady skriptu PowerShellu pro správu aplikací Azure AD. Tyto ukázky vyžadují jednu z následujících akcí:
- [Modul AzureAD v2 PowerShell pro graf](/powershell/azure/active-directory/install-adv2) nebo,
- [AzureAD v2 PowerShell pro verzi Preview modulu graphu](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true), pokud není uvedeno jinak.

Další informace o rutinách použitých v těchto ukázkách najdete v tématu [aplikace](/powershell/module/azuread/#applications).

| Odkaz | Popis |
|---|---|
|**Skripty správy aplikací**||
| [Export tajných klíčů a certifikátů (registrace aplikací)](scripts/powershell-export-all-app-registrations-secrets-and-certs.md) | Exportujte tajné klíče a certifikáty pro registrace aplikací v tenantovi Azure Active Directory. |
| [Export tajných klíčů a certifikátů (podnikové aplikace)](scripts/powershell-export-all-enterprise-apps-secrets-and-certs.md) | Exportujte tajná klíčová a certifikát pro podnikové aplikace v tenantovi Azure Active Directory. |
| [Export tajných kódů a certifikátů](scripts/powershell-export-apps-with-expriring-secrets.md) | Exportujte registrace aplikací s platností tajných klíčů a certifikátů a jejich vlastníků v Azure Active Directory tenant. |
| [Export tajných klíčů a certifikátů po uplynutí požadovaného data](scripts/powershell-export-apps-with-secrets-beyond-required.md) | Exportujte registrace aplikací pomocí tajných klíčů a certifikátů, jejichž platnost překračuje požadované datum v Azure Active Directory tenant. Tento postup používá neinteraktivní Client_Credentials tok OAuth. |
