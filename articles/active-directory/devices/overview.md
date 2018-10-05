---
title: Co je správa zařízení v Azure Active Directory? | Microsoft Docs
description: Zjistěte, jak vám správa zařízení může pomoct získat kontrolu nad zařízeními, která přistupují k prostředkům ve vašem prostředí.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/25/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 3baa48113022bed1bdc7caa5854a231334c975af
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224626"
---
# <a name="what-is-device-management-in-azure-active-directory"></a>Co je správa zařízení v Azure Active Directory?

Ve světě orientovaném na mobilní zařízení a cloud umožňuje Azure Active Directory (Azure AD) jednotné přihlašování odkudkoli k zařízením, aplikacím a službám. S čím dál větším rozšířením zařízení (včetně používání vlastních zařízení) stojí před odborníky na IT dva protikladné cíle:

- Umožnit koncovým uživatelům být produktivní kdykoli a kdekoli
- Zajistit nepřetržitou ochranu podnikových prostředků

Prostřednictvím zařízení uživatelé získávají přístup k vašim firemním prostředkům. Pokud jako správce IT chcete chránit firemní prostředky, chcete mít nad těmito zařízeními kontrolu. To vám umožní zajistit, aby uživatelé přistupovali k vašim prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. 

Správa zařízení je také základem [podmíněného přístupu na základě zařízení](../conditional-access/require-managed-devices.md). Pomocí podmíněného přístupu na základě zařízení můžete zajistit, aby byl přístup k prostředkům ve vašem prostředí možný pouze se spravovanými zařízeními.   

Tento článek vysvětluje, jak funguje správa zařízení v Azure Active Directory.

## <a name="getting-devices-under-the-control-of-azure-ad"></a>Získání kontroly nad zařízeními prostřednictvím Azure AD

Pokud chcete získat kontrolu nad zařízeními prostřednictvím Azure AD, máte dvě možnosti:

- Registrace 
- Připojení

**Registrace** zařízení do Azure AD vám umožní spravovat identitu zařízení. Po registraci zařízení mu registrace zařízení služby Azure AD poskytne identitu, která se používá k ověření zařízení při přihlášení uživatele k Azure AD. Pomocí identity můžete zařízení povolit nebo zakázat.

Při kombinaci s řešením správy mobilních zařízení (MDM), jako je například Microsoft Intune, se atributy zařízení v Azure AD aktualizují o další informace o zařízení. To vám umožňuje vytvořit pravidla podmíněného přístupu, která vynucují, aby přístup měla pouze taková zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů.  Další informace o registraci zařízení v Microsoft Intune najdete v tématu Registrace zařízení pro správu v Intune.

**Připojení** zařízení představuje rozšíření registrace zařízení. To znamená, že vám poskytne všechny výhody registrace zařízení a navíc také změní místní stav zařízení. Změna místního stavu umožní vašim uživatelům přihlašovat se k zařízení pomocí pracovního nebo školního účtu organizace, a ne osobního účtu.

## <a name="azure-ad-registered-devices"></a>Zařízení zaregistrovaná v Azure AD   

Cílem registrovaných zařízení v Azure AD je poskytnout vám podporu pro scénáře **používání vlastních zařízení**. V tomto scénáři může uživatel přistupovat k prostředkům vaší organizace řízených službou Azure Active Directory pomocí osobního zařízení.  

![Zařízení zaregistrovaná v Azure AD](./media/overview/03.png)

Tento přístup je založený na pracovním nebo školním účtu zadaném na zařízení.  
Například Windows 10 umožňuje uživatelům přidat pracovní nebo školní účet na osobní počítač, tablet nebo telefon.  
Jakmile uživatel přidá pracovní nebo školní účet, zařízení se zaregistruje v Azure AD a volitelně i v systému správy mobilních zařízení (MDM) nakonfigurovaném ve vaší organizaci. Uživatelé vaší organizace můžou přidat pracovní nebo školní účet na osobní zařízení pohodlně:

- Při prvním přístupu k pracovní aplikaci
- V případě Windows 10 ručně přes nabídku **Nastavení** 

Registraci zařízení v Azure AD můžete nakonfigurovat pro Windows 10, iOS, Android a macOS.

## <a name="azure-ad-joined-devices"></a>Zařízení připojená k Azure AD

Cílem zařízení připojených k Azure AD je zjednodušit:

- Nasazení Windows na pracovní zařízení 
- Přístup k aplikacím a prostředkům organizace z jakéhokoli zařízení s Windows
- Cloudovou správu pracovních zařízení

![Zařízení zaregistrovaná v Azure AD](./media/overview/02.png)

K nasazení služby Azure AD Join je možné použít jakoukoli z následujících metod: 
 - [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
 - [Hromadné nasazení](https://docs.microsoft.com/intune/windows-bulk-enroll)
 - [Samoobslužné prostředí](azuread-joined-devices-frx.md) 

Služba **Azure AD Join** je určená pro organizace, které se chtějí zaměřit na cloud (tj. primárně používat cloudová zařízení s cílem omezit využívání místní infrastruktury) nebo chtějí fungovat pouze v cloudu (bez místní infrastruktury). Pro velikost ani typ organizací, které můžou službu Azure AD Join nasadit, neplatí žádná omezení. Azure AD Join funguje dobře dokonce i v hybridním prostředí, kde umožňuje přístup ke cloudovým i místním aplikacím a prostředkům.

Implementace zařízení připojených k Azure AD vám poskytne následující výhody:

- **Jednotné přihlašování** k vašim službám a aplikacím SaaS spravovaným v Azure. Vašim uživatelům se při přístupu k pracovním prostředkům nebudou zobrazovat další výzvy k ověření. Jednotné přihlašování funguje dokonce i v případě, že nejsou připojeni k dostupné doménové síti.

- **Předpisům organizace odpovídající přesun** uživatelských nastavení mezi připojenými zařízeními. Uživatelé kvůli zobrazení nastavení na různých zařízeních nemusí připojovat účet Microsoft (například Hotmail).

- **Přístup k Windows Storu pro firmy** pomocí účtu Azure AD. Vaši uživatelé mají na výběr z inventáře aplikací předem vybraných organizací.

- Podpora **Windows Hello** pro zajištění zabezpečeného a pohodlného přístupu k pracovním prostředkům.

- **Omezení přístupu** k aplikacím pouze na zařízení, která splňují zásady dodržování předpisů.

- **Bezproblémový přístup k místním prostředkům** v případě, že je zařízení v dohledu místního řadiče domény. 


Přestože je služba Azure AD Join primárně určená pro organizace, které nemají místní infrastrukturu Windows Server Active Directory, můžete ji využít i v následujících scénářích:

- Chcete přejít na cloudovou infrastrukturu s využitím Azure AD a MDM, jako je například Intune.

- Nemůžete použít připojení k místní doméně například v případě, že potřebujete získat kontrolu nad mobilními zařízeními, jako jsou tablety a telefony.

- Vaši uživatelé primárně potřebují přístup k Office 365 nebo dalším aplikacím SaaS integrovaným s Azure AD.

- Chcete spravovat skupinu uživatelů v Azure AD, a ne v Active Directory. Příkladem můžou být sezónní zaměstnanci, dodavatelé nebo studenti.

- Chcete poskytnout možnosti připojení pracovníkům ve vzdálených pobočkách s omezenou místní infrastrukturou.

Připojení zařízení k Azure AD můžete nakonfigurovat pro zařízení s Windows 10.


## <a name="hybrid-azure-ad-joined-devices"></a>Hybridní zařízení připojená k Azure AD

Už více než deset let využívá řada organizací připojení k doméně místní služby Active Directory k následujícím účelům:

- Umožnit IT oddělením spravovat pracovní zařízení z centrálního umístění.

- Umožnit uživatelům přihlašovat se ke svým zařízením pomocí svých pracovních nebo školních účtů Active Directory. 

Organizace využívající místní prostředí se při zřizování zařízení obvykle spoléhají na metody vytváření imagí a ke správě těchto zařízení často využívají **System Center Configuration Manager (SCCM)** nebo **zásady skupiny**.

Pokud se ve vašem prostředí využívá AD a také chcete využít možnosti, které poskytuje Azure Active Directory, můžete implementovat hybridní zařízení připojená k Azure AD. Jedná se o zařízení připojená k místní službě Active Directory a zaregistrovaná ve službě Azure Active Directory.

![Zařízení zaregistrovaná v Azure AD](./media/overview/01.png)


Hybridní zařízení připojená k Azure AD byste měli použít v případě, že:

- Máte na těchto zařízeních nasazené aplikace Win32, které se spoléhají na strojové ověřování v Active Directory.

- Pro správu zařízení vyžadujete zásady skupiny.

- Chcete i nadále ke konfiguraci zařízení pro zaměstnance používat řešení vytváření imagí.

Připojení hybridních zařízení k Azure AD můžete nakonfigurovat pro zařízení s Windows 10 nebo starším systémem, jako je Windows 8 a Windows 7.

## <a name="summary"></a>Souhrn

Správa zařízení v Azure AD vám umožní: 

- Zjednodušit proces získání kontroly nad zařízeními prostřednictvím Azure AD

- Poskytnout uživatelům snadný přístup ke cloudovým prostředkům vaší organizace

Obecně platí, že byste měli použít:

- Zařízení zaregistrovaná v Azure AD:

    - Pro osobní zařízení 

    - K ruční registraci zařízení v Azure AD

- Zařízení připojená k Azure AD: 

    - Pro zařízení ve vlastnictví vaší organizace

    - Pro zařízení, která **nejsou** připojená k místní službě AD

    - K ruční registraci zařízení v Azure AD

    - Ke změně místního stavu zařízení

- Hybridní zařízení připojená k Azure AD pro zařízení připojená k místní službě AD     

    - Pro zařízení ve vlastnictví vaší organizace

    - Pro zařízení připojená k místní službě AD

    - K automatické registraci zařízení v Azure AD

    - Ke změně místního stavu zařízení



## <a name="next-steps"></a>Další kroky

- Přehled správy zařízení na webu Azure Portal najdete v tématu věnovaném [správě zařízení pomocí webu Azure Portal](device-management-azure-portal.md).

- Další informace o podmíněném přístupu na základě zařízení najdete v tématu věnovaném [konfiguraci zásad podmíněného přístupu na základě zařízení v Azure Active Directory](../conditional-access/require-managed-devices.md).

- Nastavení:
    - Zařízení s Windows 10 zaregistrovaná v Azure Active Directory: Viz [konfigurace zařízení s Windows 10 zaregistrovaných v Azure Active Directory](../user-help/device-management-azuread-registered-devices-windows10-setup.md).
    - Zařízení připojená k Azure Active Directory: Viz [konfigurace zařízení připojených k Azure Active Directory](../user-help/device-management-azuread-joined-devices-setup.md).
    - Hybridní zařízení připojená k Azure AD: Viz [Plánování implementace hybridního připojení Azure Active Directory](hybrid-azuread-join-plan.md).


