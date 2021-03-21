---
title: Stažení seznamu uživatelů na portálu Azure Active Directory | Microsoft Docs
description: Hromadné stažení záznamů uživatelů v centru pro správu Azure v Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/04/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57e3a059a5dd846250ba162513ef118e084c4b87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861589"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Stažení seznamu uživatelů na portálu Azure Active Directory

Azure Active Directory (Azure AD) podporuje operace hromadného importu uživatelů (Create).

## <a name="required-permissions"></a>Požadovaná oprávnění

Pokud si chcete z centra pro správu Azure AD stáhnout seznam uživatelů, musíte být přihlášeni pomocí uživatele přiřazeného k jedné nebo více rolím správce na úrovni organizace ve službě Azure AD (správce uživatelů je minimální požadovaná role). Role odesílatele pozvánek hostů a vývojáře aplikací nejsou považované za role správce.

## <a name="to-download-a-list-of-users"></a>Stažení seznamu uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu správce uživatele v organizaci.
2. Přejděte na Azure Active Directory > Uživatelé. Potom vyberte uživatele, které chcete zahrnout do staženého souboru, a to tak, že zaškrtnete políčko v levém sloupci vedle každého požadovaného uživatele. Poznámka: V současné době neexistuje způsob, jak k exportu vybrat všechny uživatele. Každého uživatele je potřeba vybrat individuálně.
3. V Azure AD vyberte **Uživatelé**  >  **stahovat uživatele**.
4. Na stránce **Stáhnout uživatele** vyberte možnost **Spustit** , pokud chcete zobrazit soubor CSV s výpisem vlastností profilu uživatele. Pokud dojde k chybám, můžete si soubor výsledků stáhnout a zobrazit na stránce Výsledky hromadné operace. Soubor obsahuje důvody jednotlivých chyb.

   ![Vyberte, kde chcete seznam uživatelů, které chcete stáhnout.](./media/users-bulk-download/bulk-download.png)

   Stažený soubor bude obsahovat filtrovaný seznam uživatelů.

   Součástí budou následující atributy uživatelů:

   - userPrincipalName (Hlavní název uživatele)
   - displayName
   - surname
   - pošta
   - givenName
   - objectId
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
   - postalCode
   - telephoneNumber
   - mobil
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
