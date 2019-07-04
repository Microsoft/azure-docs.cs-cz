---
title: Co jsou Azure AD registrované zařízení?
description: Zjistěte, jak Správa identit zařízení vám mohou pomoci při správě zařízení, která mají přístup k prostředkům ve vašem prostředí.
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
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462756"
---
# <a name="azure-ad-registered-devices"></a>Zařízení zaregistrovaná v Azure AD

Cílem zařízení registrováno v Azure AD je uživatelům poskytnout podporu pro přineste si vlastní zařízení (BYOD) nebo scénáře mobilních zařízení. V těchto scénářích platí uživatel může přistupovat k prostředkům vaší organizace Azure Active Directory řídit použití osobních zařízení.

|   | Registrováno v Azure AD |
| --- | --- |
| **Definice** | Zaregistrované do služby Azure AD bez nutnosti organizační účet pro přihlášení k zařízení |
| **Primární cílovou skupinu** | Dají se použít pro všechny uživatele s následujícími kritérii: |
|   | Přineste si vlastní zařízení (BYOD) |
|   | Mobilní zařízení |
| **Vlastnictví zařízení** | Uživatele nebo organizace |
| **Operační systémy** | Windows 10, iOS, Android a MacOS |
| **Zřizování** | Windows 10 – nastavení |
|   | iOS/Android – firemní portál nebo Microsoft Authenticator aplikace |
|   | – Portál společnosti pro MacOS |
| **Možnosti přihlášení zařízení** | Místní přihlašovací údaje koncového uživatele |
|   | Heslo |
|   | Windows Hello |
|   | PIN |
|   | Biometrika nebo vzor pro jiná zařízení |
| **Správa zařízení** | Správa mobilních zařízení (Příklad: Microsoft Intune) |
|   | Správa mobilních aplikací |
| **Klíčové funkce** | Jednotné přihlašování ke cloudovým prostředkům |
|   | Podmíněný přístup v případě registrace do Intune |
|   | Podmíněný přístup prostřednictvím zásad ochrany aplikací |
|   | Umožňuje přihlášení telefonem pomocí aplikace Microsoft Authenticator |

![Zařízení zaregistrovaná v Azure AD](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Podpora k zařízením Azure AD registrované přihlášení na zařízení s Windows 10 pomocí místní účet jako účet Microsoft, ale navíc máte účet Azure AD připojené pro přístup k prostředkům organizace. Přístup k prostředkům v organizaci může být použitý pro identitu zařízení zásady podmíněného přístupu a další omezené na základě tohoto účtu Azure AD.

Správci můžou zabezpečení a další ovládací prvek tato zařízení registrováno v Azure AD pomocí správy mobilních zařízení (MDM) nástroje, jako je Microsoft Intune. MDM zajišťuje vynucovat organizace vyžaduje konfigurace, jako je vyžadování šifrování úložiště, složitost hesla a zabezpečení softwaru aktualizován. 

Registrace Azure AD můžete udělat, když přistupoval k pracovní aplikace pro první nebo ručně pomocí nabídky nastavení Windows 10. 

## <a name="scenarios"></a>Scénáře

Ve vaší organizaci chce uživatel přístup k nástrojům pro e-mailu, vytváření sestav volna a výhody registrace z jejich domácí počítače. Vaše organizace má tyto nástroje za zásady podmíněného přístupu, která vyžaduje přístup ze zařízení kompatibilní s Intune. Uživatel přidá účet své organizace a registruje svůj domácí počítač s Azure AD a požadované zásady Intune se vynucují poskytují uživateli přístup ke svým prostředkům.

Jiný uživatel chce, aby se pro přístup k jejich organizace e-mailu na jejich osobní telefon s Androidem, která nemá root. Vaše společnost vyžaduje vyhovující zařízení a vytvořil zásady dodržování předpisů Intune můžete blokovat všechny zařízením s rootem. Zaměstnanec zastavena v přístupu k prostředkům organizace na tomto zařízení.

## <a name="next-steps"></a>Další postup

- [Správa identit zařízení pomocí webu Azure portal](device-management-azure-portal.md)
- [Správa zastaralých zařízení ve službě Azure AD](manage-stale-devices.md)
