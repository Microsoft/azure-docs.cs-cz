---
title: Jak vyžadovat vícefaktorové ověřování | Dokumentace Microsoftu
description: Zjistěte, jak vyžadovat vícefaktorové ověřování (MFA) pro privilegované identity pomocí rozšíření Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 8e1c0fa212b31c05fcc4559f9f8d42b627f0da0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622843"
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Jak vyžadovat vícefaktorové ověřování ve službě Azure AD Privileged Identity Management
Doporučujeme vyžadovat vícefaktorové ověřování (MFA) pro všechny vaše správce. Tím se snižuje riziko útoků kvůli ohrožení zabezpečení hesla.

Může vyžadovat, aby uživatelé dokončit ověřovacím testem MFA při přihlášení. Tento blogový příspěvek [vícefaktorové ověřování pro Office 365 a Azure MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) porovná, co je součástí předplatných Office a Azure, pomocí funkce obsažené v nabídce Microsoft Azure Multi-Factor Authentication.

Můžete také vyžadovat, aby uživatelé dokončit ověřovacím testem MFA při aktivaci role v Azure AD PIM. Tímto způsobem, pokud uživatel nedokončil výzvu vícefaktorové ověřování, pokud jsou přihlášení, se výzva k tomu pomocí PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Vyžadování vícefaktorového ověřování ve službě Azure AD Privileged Identity Management
Při správě identit v PIM jako správce privilegovaných rolí, může se zobrazit upozornění, které doporučujeme MFA pro privilegované účty. Klikněte na výstrahu zabezpečení na řídicím panelu PIM a otevře se nové okno se seznamem z účtů správce, které by měla vyžadovat vícefaktorové ověřování.  Vícefaktorové ověřování můžete vyžádat výběrem víc rolí a pak kliknutím na **opravit** tlačítko, nebo můžete kliknout na tři tečky vedle jednotlivých rolí a pak klikněte na tlačítko **opravit** tlačítko.

> [!IMPORTANT]
> Klikněte pravým tlačítkem myši Přejít k pokladně, Azure MFA funguje jenom v pracovní nebo školní účty, ne na účty Microsoft (obvykle pomocí osobního účtu, který se používá k přihlášení ke službám Microsoftu, jako je Skype, Xbox, Outlook.com atd.). Z tohoto důvodu nikdo jiný nemohl pomocí účtu Microsoft nemůže být oprávněného správce, protože nemohou používat vícefaktorové ověřování pro aktivovaly jejich role. Pokud tito uživatelé muset pokračovat ve správě úloh pomocí účtu Microsoft, jejich povýšení na trvalí správci teď.
> 
> 

Kromě toho můžete změnit požadavek vícefaktorového ověřování pro konkrétní role kliknutím v části role PIM řídicího panelu. Potom klikněte na **nastavení** v okně roli a pak vyberte **povolit** v části ověřování Multi-Factor Authentication.

## <a name="how-azure-ad-pim-validates-mfa"></a>Jak Azure AD PIM ověří MFA
Existují dvě možnosti pro ověřování MFA, když uživatel aktivuje roli.

Nejjednodušší možnost je využívají Azure MFA pro uživatele, kteří jsou aktivace privilegovaných rolí. Chcete-li to provést, nejprve zkontrolujte, že tito uživatelé mají licenci, v případě potřeby a jste se zaregistrovali pro Azure MFA. Další informace o tom, jak to provést, je v [Začínáme se službou Azure Multi-Factor Authentication v cloudu](../authentication/howto-mfa-getstarted.md). Je doporučeno, ale není nutné nakonfigurovat služby Azure AD k vynucení vícefaktorového ověřování pro tyto uživatele při přihlášení. Je to proto, že se pošle kontroly vícefaktorové ověřování pomocí Azure AD PIM, samotného.

Případně pokud se uživatelé ověřují místně může mít zprostředkovatele identity zodpovídat za MFA. Pokud jste nakonfigurovali federační služby AD vyžadují ověřování pomocí čipové karty před přístupem k Azure AD, například [zabezpečení cloudových prostředků pomocí ověřování Azure Multi-Factor Authentication a AD FS](../authentication/howto-mfa-adfs.md) obsahuje pokyny pro konfiguraci služby AD FS k posílání deklarací identit do služby Azure AD. Když se uživatel pokusí aktivovat roli, Azure AD PIM přijme, že vícefaktorové ověřování již byl ověřen pro uživatele, jakmile obdrží odpovídající deklarace identity.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../../includes/active-directory-privileged-identity-management-toc.md)]

