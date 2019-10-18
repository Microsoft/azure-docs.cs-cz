---
title: Stažení seznamu uživatelů (Preview) na portálu Azure Active Directory | Microsoft Docs
description: Hromadné stažení záznamů uživatelů v centru pro správu Azure v Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 07/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd0829afca05058892d3a0ceeb50c9955d792dc3
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517061"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Stažení seznamu uživatelů (Preview) na portálu Azure Active Directory

Azure Active Directory (Azure AD) podporuje operace hromadného importu uživatelů (Create).

## <a name="required-permissions"></a>Požadovaná oprávnění

Pokud si chcete stáhnout seznam uživatelů z centra pro správu Azure AD, musíte být přihlášeni pomocí uživatele, který je přiřazený k jedné nebo více rolím Správce na úrovni organizace ve službě Azure AD. Pozvání hostů a vývojář aplikací se nepovažují za role správců.

## <a name="to-download-a-list-of-users"></a>Stažení seznamu uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu správce uživatele v organizaci.
1. V Azure AD vyberte **uživatelé** > **Stáhnout uživatele**.
1. Na stránce **Stáhnout uživatele** vyberte možnost **Spustit** , pokud chcete zobrazit soubor CSV s výpisem vlastností profilu uživatele. Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s výsledky hromadné operace. Soubor obsahuje důvod každé chyby.

   ![Vyberte, kde chcete seznam uživatelů, které chcete stáhnout.](./media/users-bulk-download/bulk-download.png)

   Soubor ke stažení bude obsahovat filtrovaný seznam uživatelů.

   Budou zahrnuty následující atributy uživatele: 

   - userPrincipalName (Hlavní název uživatele)
   - DisplayName
   - Příjmení
   - Modul
   - GivenName
   - Objektu
   - userType
   - JobTitle
   - Ministerstvo
   - Programu
   - AccountEnabled
   - usageLocation
   - streetAddress
   - state
   - Krajin
   - physicalDeliveryOfficeName
   - city
   - Ovládacím
   - TelephoneNumber
   - telefon
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
