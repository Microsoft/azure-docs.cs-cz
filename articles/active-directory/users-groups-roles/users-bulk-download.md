---
title: Stažení seznamu uživatelů (preview) na portálu Azure Active Directory | Dokumenty společnosti Microsoft
description: V Centru pro správu Azure ve Službě Azure Active Directory si hromadně stahujte záznamy uživatelů.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063828"
---
# <a name="download-a-list-of-users-preview-in-azure-active-directory-portal"></a>Stažení seznamu uživatelů (preview) na portálu Azure Active Directory

Azure Active Directory (Azure AD) podporuje operace hromadného importu (vytvoření) uživatelů.

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li stáhnout seznam uživatelů z Centra pro správu Azure AD, musíte být přihlášeni s uživatelem přiřazeným k jedné nebo více rolím správce na úrovni organizace ve službě Azure AD. Pozvaní hosté a vývojářaplikací nejsou považovány za role správce.

## <a name="to-download-a-list-of-users"></a>Stažení seznamu uživatelů

1. [Přihlaste se ke své organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu správce uživatele v organizaci.
2. Přejděte do služby Azure Active Directory > uživatelé. Poté vyberte uživatele, které chcete zahrnout do stahování, zaškrtnutím políčka v levém sloupci vedle každého uživatele. Poznámka: V tuto chvíli neexistuje žádný způsob, jak vybrat všechny uživatele pro export. Každý z nich musí být vybrán individuálně.
3. Ve službě Azure AD vyberte **uživatelé** > **stáhnout uživatele**.
4. Na stránce **Stáhnout uživatele** vyberte **Možnost Start,** chcete-li získat vlastnosti profilu uživatele se seznamem souborů CSV. Pokud dojde k chybám, můžete soubor výsledků stáhnout a zobrazit na stránce Výsledky hromadné operace. Soubor obsahuje důvod každé chyby.

   ![Vyberte, kde chcete seznam stáhnout uživatele.](./media/users-bulk-download/bulk-download.png)

   Soubor ke stažení bude obsahovat filtrovaný seznam uživatelů.

   Jsou zahrnuty následující uživatelské atributy:

   - userPrincipalName (Hlavní název uživatele)
   - displayName
   - surname
   - pošta
   - givenName
   - Objectid
   - userType
   - název úlohy
   - Oddělení
   - účetPovolený
   - usageUmístění
   - Streetaddress
   - state
   - country
   - physicalDeliveryOfficeName
   - city
   - Postalcode
   - phoneČíslo
   - mobil
   - authenticationPhoneNumber
   - authenticationAlternativePhoneNumber
   - authenticationEmail
   - alternateEmailAddress
   - ageGroup
   - consentProvidedForMinor
   - legalAgeGroupClassification

## <a name="check-status"></a>Zkontrolování stavu

Stav nevyřízených hromadných požadavků můžete zobrazit na stránce **Výsledky hromadné operace (preview).**

   ![Kontrola stavu nahrávání na stránce Výsledky hromadných operací](./media/users-bulk-download/bulk-center.png)

## <a name="bulk-download-service-limits"></a>Limity služby hromadného stahování

Každá hromadná aktivita pro vytvoření seznamu uživatelů může běžet až jednu hodinu. To umožňuje vytvoření a stažení seznamu nejméně 500 000 uživatelů.

## <a name="next-steps"></a>Další kroky

- [Hromadné přidání uživatelů](users-bulk-add.md)
- [Hromadné odstranění uživatelů](users-bulk-delete.md)
- [Uživatelé hromadného obnovení](users-bulk-restore.md)
