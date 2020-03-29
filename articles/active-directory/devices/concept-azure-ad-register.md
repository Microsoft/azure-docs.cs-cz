---
title: Co jsou zařízení registrovaná pro Azure AD?
description: Zjistěte, jak vám správa identit zařízení může pomoci spravovat zařízení, která přistupují k prostředkům ve vašem prostředí.
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462756"
---
# <a name="azure-ad-registered-devices"></a>Zařízení zaregistrovaná v Azure AD

Cílem registrovaných zařízení Azure AD je poskytnout uživatelům podporu pro scénáře Přineste si vlastní zařízení (BYOD) nebo mobilní zařízení. V těchto scénářích může uživatel přistupovat k prostředkům řízenéslužbou Azure Active Directory vaší organizace pomocí osobního zařízení.

|   | Registrace Azure AD |
| --- | --- |
| **Definice** | Registrace ve službě Azure AD bez nutnosti přihlášení k zařízení pomocí účtu organizace |
| **Primární publikum** | Platí pro všechny uživatele s následujícími kritérii: |
|   | Přineste si vlastní zařízení (BYOD) |
|   | Mobilní zařízení |
| **Vlastnictví zařízení** | Uživatel nebo organizace |
| **Operační systémy** | Windows 10, iOS, Android a MacOS |
| **Zřizování** | Windows 10 – nastavení |
|   | iOS/Android – Portál společnosti nebo aplikace Microsoft Authenticator |
|   | MacOS – Firemní portál |
| **Možnosti přihlášení k zařízení** | Místní pověření koncového uživatele |
|   | Heslo |
|   | Windows Hello |
|   | PIN |
|   | Biometrie nebo vzor pro jiná zařízení |
| **Správa zařízení** | Správa mobilních zařízení (příklad: Microsoft Intune) |
|   | Správa mobilních aplikací |
| **Klíčové funkce** | Připojenit cloudové prostředky |
|   | Podmíněný přístup při registraci do Intune |
|   | Podmíněný přístup prostřednictvím zásad ochrany aplikací |
|   | Povolí přihlášení telefonu pomocí aplikace Microsoft Authenticator |

![Zařízení zaregistrovaná v Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Zařízení registrovaná pro Azure AD jsou přihlášení k používání místního účtu, jako je účet Microsoft na zařízení s Windows 10, ale navíc mají účet Azure AD připojený pro přístup k prostředkům organizace. Přístup k prostředkům v organizaci může být dále omezen na základě toho, že účet Azure AD a zásady podmíněného přístupu použité pro identitu zařízení.

Správci můžou zabezpečit a dále řídit tato zařízení registrovaná službou Azure AD pomocí nástrojů pro správu mobilních zařízení (MDM), jako je Microsoft Intune. MDM poskytuje prostředky k vynucení konfigurace požadované organizací, jako je vyžadování úložiště, které mají být šifrovány, složitost hesla a bezpečnostní software průběžně aktualizován. 

Registrace Azure AD lze provést při prvním přístupu k pracovní aplikaci nebo ručně pomocí nabídky Nastavení Windows 10. 

## <a name="scenarios"></a>Scénáře

Uživatel ve vaší organizaci chce získat přístup k nástrojům pro e-mail, vykazování time-off a výhody registrace z domova PC. Vaše organizace má tyto nástroje za zásady podmíněného přístupu, které vyžadují přístup ze zařízení kompatibilního s Intune. Uživatel přidá svůj účet organizace a zaregistruje svůj domácí počítač pomocí Azure AD a požadované zásady Intune jsou vynucené, které uživateli poskytují přístup k jejich prostředkům.

Jiný uživatel chce získat přístup ke svým organizačním e-mailům na svém osobním telefonu Android, který byl zakořeněn. Vaše společnost vyžaduje kompatibilní zařízení a vytvořila zásady dodržování předpisů Intune, které blokují všechna zakořeněná zařízení. Zaměstnanci je zastaven přístup k organizačním prostředkům na tomto zařízení.

## <a name="next-steps"></a>Další kroky

- [Správa identit zařízení pomocí portálu Azure](device-management-azure-portal.md)
- [Správa zastaralých zařízení ve službě Azure AD](manage-stale-devices.md)
