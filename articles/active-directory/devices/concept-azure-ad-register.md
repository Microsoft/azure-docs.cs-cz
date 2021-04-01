---
title: Co jsou zařízení registrovaná v Azure AD?
description: Přečtěte si, jak zařízení registrovaná v Azure AD poskytují vašim uživatelům podporu pro scénáře Přineste si vlastní zařízení (BYOD) nebo mobilní zařízení.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c40b7962090492f8b4b85e555a947130c3628d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91256434"
---
# <a name="azure-ad-registered-devices"></a>Zařízení zaregistrovaná v Azure AD

Cílem zařízení registrovaných v Azure AD je poskytnout vašim uživatelům podporu pro scénáře Přineste si vlastní zařízení (BYOD) nebo mobilní zařízení. V těchto scénářích může uživatel získat přístup k prostředkům spravovaným Azure Active Directory vaší organizace pomocí osobního zařízení.

| Registrace Azure AD | Description |
| --- | --- |
| **Definition** | Registrováno ve službě Azure AD bez vyžadování účtu organizace pro přihlášení k zařízení |
| **Primární cílová skupina** | Platí pro všechny uživatele s následujícími kritérii: |
|   | Přineste si vlastní zařízení (BYOD) |
|   | Mobilní zařízení |
| **Vlastnictví zařízení** | Uživatel nebo organizace |
| **Operační systémy** | Windows 10, iOS, Android a MacOS |
| **Zřizování** | Windows 10 – nastavení |
|   | iOS/Android – Portál společnosti nebo Microsoft Authenticator aplikace |
|   | MacOS – Portál společnosti |
| **Možnosti přihlášení zařízení** | Místní přihlašovací údaje pro koncové uživatele |
|   | Heslo |
|   | Windows Hello |
|   | PIN |
|   | Biometrika nebo vzor pro jiná zařízení |
| **Správa zařízení** | Správa mobilních zařízení (příklad: Microsoft Intune) |
|   | Správa mobilních aplikací |
| **Klíčové funkce** | Jednotné přihlašování ke cloudovým prostředkům |
|   | Podmíněný přístup při registraci do Intune |
|   | Podmíněný přístup prostřednictvím zásad ochrany aplikací |
|   | Povolí telefonní přihlášení pomocí aplikace Microsoft Authenticator. |

![Zařízení zaregistrovaná v Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Zařízení registrovaná v Azure AD jsou přihlášená k používání místního účtu, jako je účet Microsoft na zařízení s Windows 10, ale navíc mají k dispozici účet Azure AD, který by měl mít přístup k prostředkům organizace. Přístup k prostředkům v organizaci je možné dál omezit na základě účtu Azure AD a zásad podmíněného přístupu, které se používají pro identitu zařízení.

Správci můžou pomocí nástrojů pro správu mobilních zařízení (MDM), jako je Microsoft Intune, zabezpečit a dále řídit tato zařízení registrovaná v Azure AD. MDM nabízí způsob, jak vyhovět konfiguracím vyžadovaným v organizaci, jako je třeba šifrování úložiště, složitost hesla a bezpečnostní software se aktualizoval. 

Registrace Azure AD se dá provést při prvním přístupu k pracovní aplikaci nebo k ručnímu použití v nabídce nastavení Windows 10. 

## <a name="scenarios"></a>Scénáře

Uživatel ve vaší organizaci chce získat přístup k nástrojům pro e-mail, odhlášením času a výhodám registrace z domácího počítače. Vaše organizace má tyto nástroje za zásadou podmíněného přístupu, které vyžadují přístup ze zařízení kompatibilního s Intune. Uživatel přidá svůj účet organizace a zaregistruje jeho domácí počítač s Azure AD a vynutily se požadované zásady Intune, které uživatelům umožní přístup k jejich prostředkům.

Jiný uživatel chce získat přístup k e-mailu organizace na svém osobním telefonu s Androidem, který je rootem. Vaše společnost vyžaduje vyhovující zařízení a vytvořila zásady dodržování předpisů Intune k blokování všech zařízení, která jsou rootem. Zaměstnanci se zastavilo přístup k prostředkům organizace v tomto zařízení.

## <a name="next-steps"></a>Další kroky

- [Správa identit zařízení přes Azure Portal](device-management-azure-portal.md)
- [Správa zastaralých zařízení v Azure AD](manage-stale-devices.md)
