---
title: "Řešení potíží: 'Služby Active Directory, je položka chybí nebo není k dispozici | Microsoft Docs"
description: Co dělat, pokud položka nabídky služby Active Directory se nezobrazí na portálu správy Azure.
services: active-directory
documentationcenter: na
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
editor: ''
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.openlocfilehash: 8996eaf1d780f957445c63eac96cf9a343b9ef90
ms.sourcegitcommit: c306be629977f6bc22bb7aefd0522a30ed591ff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34181533"
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Řešení potíží: 'Služby Active Directory, je položka chybí nebo není k dispozici
Řadu pokynů pro použití funkce Azure Active Directory a služby začínat řetězcem "přejděte na portálu pro správu Azure a klikněte na tlačítko **služby Active Directory**." Ale co můžete udělat, pokud položka rozšíření nebo nabídky služby Active Directory se nezobrazí nebo pokud je označen **není k dispozici**? Toto téma je navržená tak, abyste. Popisuje podmínky, pod kterým **služby Active Directory** se nezobrazí nebo není k dispozici a vysvětluje, jak pokračovat.

## <a name="active-directory-is-missing"></a>Chybí služby Active Directory
Obvykle **služby Active Directory** položka je zobrazena v levé navigační nabídce. Pokyny v Azure Active Directory postupech předpokládají, že tato položka je v zobrazení.

![Snímek obrazovky: služby Active Directory v Azure](./media/active-directory-troubleshooting/typical-view.png)

Pokud platí některá z následujících podmínek, zobrazí se v levém navigační nabídce položky služby Active Directory. Položka, jinak hodnota nezobrazí.

* Aktuální uživatel ověřen pomocí účtu Microsoft (dříve označované jako účet Windows Live ID).
  
    NEBO
* Ke klientovi Azure má adresáře a aktuálního účtu je správce adresáře.
  
    NEBO
* Ke klientovi Azure má alespoň jeden obor názvů řízení přístupu Azure AD (ACS). Další informace najdete v tématu [Namespace řízení přístupu](https://msdn.microsoft.com/library/azure/gg185908.aspx).
  
    NEBO
* Ke klientovi Azure má aspoň jednoho poskytovatele ověřování Azure Multi-Factor Authentication. Další informace najdete v tématu [Správa poskytovatelé Azure Multi-Factor Authentication](authentication/howto-mfa-getstarted.md).

Chcete-li vytvořit na obor názvů řízení přístupu nebo poskytovatele služby Multi-Factor Authentication, klikněte na tlačítko **+ nový** > **App Services** > **služby Active Directory**.

Potřebujete práva správce k adresáři, požádejte správce, přiřadit role správce účtu. Podrobnosti najdete v tématu [přiřazení rolí správce](active-directory-assign-admin-roles-azure-portal.md).

## <a name="active-directory-is-not-available"></a>Služba Active Directory není k dispozici
Když kliknete na tlačítko **+ nový** > **App Services**, **služby Active Directory** položka je zobrazena. Konkrétně položky služby Active Directory se zobrazí, když žádnou z funkcí služby Active Directory, jako je například adresář, řízení přístupu nebo zprostředkovatel vícefaktorového ověřování, jsou k dispozici pro aktuálního uživatele.

Ale při načítání stránky, položka je neaktivní a je označen **není k dispozici**. Toto je dočasný stav. Pokud jste Počkejte několik sekund, položka bude k dispozici. Pokud se prodlužuje zpoždění, obnovení webové stránky často řeší problém.

![Snímek obrazovky: není k dispozici služba Active Directory](./media/active-directory-troubleshooting/not-available.png)

