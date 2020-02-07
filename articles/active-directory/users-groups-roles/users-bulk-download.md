---
title: Stažení seznamu uživatelů (Preview) na portálu Azure Active Directory | Microsoft Docs
description: Hromadné stažení záznamů uživatelů v centru pro správu Azure v Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 02/06/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4716ff9547f64dc6551b4d4adb0a8578da9fa83e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063828"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Stažení seznamu uživatelů (Preview) na portálu Azure Active Directory

Azure Active Directory (Azure AD) podporuje operace hromadného importu uživatelů (Create).

## <a name="required-permissions"></a>Požadovaná oprávnění

Pokud si chcete stáhnout seznam uživatelů z centra pro správu Azure AD, musíte být přihlášeni pomocí uživatele, který je přiřazený k jedné nebo více rolím Správce na úrovni organizace ve službě Azure AD. Pozvání hostů a vývojář aplikací se nepovažují za role správců.

## <a name="to-download-a-list-of-users"></a>Stažení seznamu uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu správce uživatele v organizaci.
2. Přejděte na Azure Active Directory > uživatelé. Pak vyberte uživatele, které chcete zahrnout do stahování, a to tak, že zaškrtnete políčko v levém sloupci vedle každého uživatele. Poznámka: v současné době neexistuje žádný způsob, jak vybrat všechny uživatele k exportu. Každé z nich musí být vybráno jednotlivě.
3. V Azure AD vyberte **uživatelé** > **Stáhnout uživatele**.
4. Na stránce **Stáhnout uživatele** vyberte možnost **Spustit** , pokud chcete zobrazit soubor CSV s výpisem vlastností profilu uživatele. Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s výsledky hromadné operace. Soubor obsahuje důvod každé chyby.

   ![Vyberte, kde chcete seznam uživatelů, které chcete stáhnout.](./media/users-bulk-download/bulk-download.png)

   Soubor ke stažení bude obsahovat filtrovaný seznam uživatelů.

   Jsou zahrnuty následující atributy uživatele:

   - userPrincipalName (Hlavní název uživatele)
   - displayName
   - Příjmení
   - e-mailu
   - givenName
   - ID objektu
   - userType
   - pracovní funkce
   - Oddělení
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - krajin
   - physicalDeliveryOfficeName
   - city
   - PSČ
   - telephoneNumber
   - Mobilní zařízení
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Zkontrolování stavu

Stav vašich nevyřízených hromadných požadavků můžete zobrazit na stránce **výsledky hromadných operací (Preview)** .

   ![Na stránce výsledků hromadných operací ověřte stav nahrávání.](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Omezení služby hromadného stahování

Každá Hromadná aktivita pro vytvoření seznamu uživatelů může běžet až po dobu jedné hodiny. To umožňuje vytvoření a stažení seznamu minimálně 500 000 uživatelů.

## <a name="next-steps"></a>Další kroky

- [Hromadné přidání uživatelů](users-bulk-add.md)
- [Hromadné odstranění uživatelů](users-bulk-delete.md)
- [Uživatelé hromadného obnovení](users-bulk-restore.md)
