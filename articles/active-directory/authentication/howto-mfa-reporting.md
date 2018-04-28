---
title: Přístup a použití sestav pro Azure MFA | Microsoft Docs
description: Popisuje jak používat funkci Azure Multi-Factor Authentication - sestavy.
services: multi-factor-authentication
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: 3f6b33c4-04c8-47d4-aecb-aa39a61c4189
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.openlocfilehash: 3a29d1b17924b519167f362e6116f405e182bf75
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Sestavy v Azure Multi-Factor Authentication

Azure Multi-Factor Authentication nabízí několik sestav, které mohou být využívána vám a vaší organizaci, která je přístupná prostřednictvím portálu Azure. V následující tabulce jsou uvedeny dostupné sestavy:

| Sestava | Umístění | Popis |
|:--- |:--- |:--- |
| Historie blokovaného uživatele | Azure AD > MFA serveru > zablokovat nebo odblokovat uživatele | Zobrazuje historie žádostí o blokování nebo odblokování uživatelů. |
| Využití a podvod výstrahy | Azure AD > přihlášení | Poskytuje informace o celkové využití: uživatelský souhrn a podrobnosti o uživateli; stejně jako historie upozornění na podvod odeslaných během období zadán. |
| Využití pro místní komponenty | Azure AD > MFA serveru > Sestava aktivit | Poskytuje informace o celkové využití pro vícefaktorové ověřování prostřednictvím rozšíření serveru NPS, AD FS, tak i MFA server. |
| Historie uživatele s jednorázovým přihlášením | Azure AD > MFA serveru > jednorázové přihlášení | Poskytuje historie žádostí o obejití služby Multi-Factor Authentication pro uživatele. |
| Stav serveru | Azure AD > MFA serveru > Stav serveru | Zobrazí stav aplikace Multi-Factor Authentication Server spojené s vaším účtem. |

## <a name="view-reports"></a>Zobrazení sestav 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **MFA Server**.
3. Vyberte sestavu, kterou chcete zobrazit.

   <center>![Cloud](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>Vytváření sestav prostředí PowerShell

Identifikujte uživatele, kteří mají zaregistrovaný pro MFA pomocí prostředí PowerShell, který následuje.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifikujte uživatele, kteří se ještě nezaregistrovali pro MFA pomocí prostředí PowerShell, který následuje dále.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Další postup

* [Pro uživatele](../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md)
* [Pokud k nasazení](concept-mfa-whichversion.md)
