---
title: Stažení seznamu uživatelů na portálu Azure Active Directory | Microsoft Docs
description: Hromadné stažení záznamů uživatelů v centru pro správu Azure v Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042139a39c28ee4944a7f3f766fc61b163629843
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574390"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Stažení seznamu uživatelů na portálu Azure Active Directory

Azure Active Directory (Azure AD) podporuje operace hromadného importu uživatelů (Create).

## <a name="required-permissions"></a>Požadovaná oprávnění

Pokud si chcete stáhnout seznam uživatelů z centra pro správu Azure AD, musíte být přihlášeni pomocí uživatele přiřazeného k jedné nebo více rolím Správce na úrovni organizace ve službě Azure AD (Správce uživatelů je minimální požadovaná role). Pozvání hostů a vývojář aplikací se nepovažují za role správců.

## <a name="to-download-a-list-of-users"></a>Stažení seznamu uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu správce uživatele v organizaci.
2. Přejděte na Azure Active Directory > uživatelé. Pak vyberte uživatele, které chcete zahrnout do stahování, a to tak, že zaškrtnete políčko v levém sloupci vedle každého uživatele. Poznámka: v současné době neexistuje žádný způsob, jak vybrat všechny uživatele k exportu. Každé z nich musí být vybráno jednotlivě.
3. V Azure AD vyberte **Uživatelé**  >  **stahovat uživatele**.
4. Na stránce **Stáhnout uživatele** vyberte možnost **Spustit** , pokud chcete zobrazit soubor CSV s výpisem vlastností profilu uživatele. Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s výsledky hromadné operace. Soubor obsahuje důvod každé chyby.

   ![Vyberte, kde chcete seznam uživatelů, které chcete stáhnout.](./media/users-bulk-download/bulk-download.png)

   Soubor ke stažení bude obsahovat filtrovaný seznam uživatelů.

   Jsou zahrnuty následující atributy uživatele:

   - userPrincipalName (Hlavní název uživatele)
   - displayName
   - surname
   - pošta
   - givenName
   - Objektu
   - userType
   - jobTitle
   - Oddělení
   - accountEnabled
   - usageLocation
   - streetAddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - Ovládacím
   - telephoneNumber
   - mobil
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Zkontrolování stavu

Stav vašich nevyřízených hromadných požadavků můžete zobrazit na stránce **výsledků hromadných operací** .

[![Ověřte stav na stránce výsledků hromadných operací.](./media/users-bulk-download/bulk-center.png)](./media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Omezení služby hromadného stahování

Každá Hromadná aktivita pro vytvoření seznamu uživatelů může běžet až po dobu jedné hodiny. To umožňuje vytvoření a stažení seznamu minimálně 500 000 uživatelů.

## <a name="next-steps"></a>Další kroky

- [Hromadné přidání uživatelů](users-bulk-add.md)
- [Hromadné odstraňování uživatelů](users-bulk-delete.md)
- [Hromadné obnovování uživatelů](users-bulk-restore.md)
