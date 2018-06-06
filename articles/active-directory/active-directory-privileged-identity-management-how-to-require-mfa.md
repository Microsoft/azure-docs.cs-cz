---
title: Jak vyžadovat vícefaktorové ověřování | Microsoft Docs
description: Zjistěte, jak vyžadovat vícefaktorové ověřování (MFA) pro privilegované identity pomocí rozšíření Azure Active Directory Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 06/06/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 6e773c0609a0d6a4c7ebf20ddfdcfcdd08d9a786
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698369"
---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>Jak vyžadovat vícefaktorové ověřování v Azure AD Privileged Identity Management
Doporučujeme vyžadovat vícefaktorové ověřování (MFA) pro všechny správce. Tím se snižuje riziko útoků z důvodu ohroženými heslo.

Může vyžadovat, aby uživatelé dokončit výzvu MFA při přihlášení. V příspěvku blogu [MFA pro Office 365 a Azure MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) porovná předplatných Office a Azure, co je součástí pomocí funkce obsažené v nabídce Microsoft Azure Multi-Factor Authentication.

Může také vyžadovat, aby uživatelé dokončit výzvu MFA při aktivují role v Azure AD PIM. Tímto způsobem, pokud uživatel nebyla dokončena výzvu vícefaktorového ověřování, když se přihlášení, se zobrazí výzva k tomu podle PIM.

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>Vyžadování vícefaktorového ověřování v Azure AD Privileged Identity Management
Když budete spravovat identity v PIM jako správce privilegovaných rolí, může se zobrazit výstrahy, které doporučujeme vícefaktorového ověřování pro privilegované účty. Klikněte na výstrahu zabezpečení v řídicím panelu PIM a otevře se nové okno s seznam účtů správce, které by vyžadovalo.  Můžete požadovat MFA výběrem víc rolí a pak kliknutím na **opravte** můžete kliknutím na tři tečky vedle jednotlivých rolí a pak klikněte na tlačítko, nebo můžete **opravte** tlačítko.

> [!IMPORTANT]
> Pravým nyní Azure MFA pracuje pouze s pracovní nebo školní účty, není účty Microsoft (obvykle osobní účet, který se používá k přihlášení ke službám Microsoft Skype, Xbox, Outlook.com, atd.). Z toho důvodu ostatní uživatele používající účet Microsoft nemůže být oprávněné správce, protože nemohou používat MFA k aktivaci jejich rolí. Pokud tito uživatelé potřebovat dál spravovat úlohy pomocí účtu Microsoft, zvýšení oprávnění je trvalé správcům nyní.
> 
> 

Kromě toho můžete změnit požadavek vícefaktorového ověřování pro určité role kliknutím v části role PIM řídicího panelu. Potom klikněte na **nastavení** v okně role a potom výběrem **povolit** v rámci služby Multi-Factor authentication.

## <a name="how-azure-ad-pim-validates-mfa"></a>Jak Azure AD PIM ověří vícefaktorového ověřování
Existují dvě možnosti pro ověřování MFA, když uživatel aktivuje roli.

Nejjednodušší možnost je moct spolehnout na Azure MFA pro uživatele, kteří jsou aktivací privilegované role. Uděláte to tak, nejdřív zkontrolujte, zda tito uživatelé mají licenci, pokud je to nutné a zaregistrovali pro Azure MFA. Další informace o tom, jak to udělat najdete v [Začínáme s Azure Multi-Factor Authentication v cloudu](authentication/howto-mfa-getstarted.md). Je doporučeno, ale není vyžadována, že nakonfigurujete Azure AD k vynucení vícefaktorového ověřování pro tyto uživatele při přihlášení. Je to proto, že budou provedeny kontroly vícefaktorového ověřování pomocí Azure AD PIM sám sebe.

Případně pokud se uživatelé ověřují místně může mít zprostředkovatele identity je zodpovědná za vícefaktorového ověřování. Pokud jste nakonfigurovali federační služby AD do vyžadují ověřování pomocí čipové karty před přístupem k Azure AD, například [zabezpečení cloudových prostředků s Azure Multi-Factor Authentication a AD FS](authentication/howto-mfa-adfs.md) obsahuje pokyny pro konfiguraci služby AD FS k posílání deklarací identit do služby Azure AD. Když uživatel pokusí o aktivaci role, Azure AD PIM přijme, že MFA již byl ověřen pro uživatele, jakmile obdrží odpovídající deklarace identity.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Další postup
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

