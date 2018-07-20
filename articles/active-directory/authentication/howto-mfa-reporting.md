---
title: Sestavy o přístupech a použití pro Azure MFA | Dokumentace Microsoftu
description: Popisuje způsob použití funkce ověřování Azure Multi-Factor Authentication – sestavy.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: ead1c5a899057bb26154b45c75251e7d9e481147
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160888"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Sestavy v Azure Multi-Factor Authentication

Azure Multi-Factor Authentication nabízí několik sestav, které mohou využívat vás a vaši organizaci, která je přístupná prostřednictvím webu Azure portal. V následující tabulce jsou uvedeny dostupné sestavy:

| Sestava | Umístění | Popis |
|:--- |:--- |:--- |
| Historie blokovaného uživatele | Azure AD > MFA Server > blokování a odblokování uživatelů | Zobrazuje historie žádostí o blokování nebo odblokování uživatelů. |
| Využití a odhalování výstrahy | Azure AD > přihlášení | Poskytuje informace o celkové využití: uživatelský souhrn a podrobnosti o uživateli; také historie upozornění na podvod odeslaných během období zadán. |
| Využití pro místní komponenty | Azure AD > MFA Server > Sestava aktivit | Pomocí rozšíření serveru NPS, AD FS, poskytuje informace o celkové využití pro MFA a MFA serveru. |
| Historie uživatele s jednorázovým přihlášením | Azure AD > MFA Server > jednorázové přihlášení | Poskytuje historii žádostí o obejít ověřování Multi-Factor Authentication pro uživatele. |
| Stav serveru | Azure AD > MFA Server > Stav serveru | Zobrazí stav služby Multi-Factor Authentication Server spojenou s vaším účtem. |

## <a name="view-reports"></a>Zobrazení sestav 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **MFA Server**.
3. Vyberte sestavu, kterou chcete zobrazit.

   <center>![Cloud](./media/howto-mfa-reporting/report.png)</center>

## <a name="powershell-reporting"></a>Vytváření sestav pomocí Powershellu

Identifikujte uživatele, kterým jste se zaregistrovali pro vícefaktorové ověřování pomocí Powershellu, který následuje.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifikujte uživatele, kteří se ještě nezaregistrovali pro vícefaktorové ověřování pomocí Powershellu, který následuje.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>Další postup

* [Pro uživatele](../user-help/multi-factor-authentication-end-user.md)
* [Pokud chcete nasadit](concept-mfa-whichversion.md)
