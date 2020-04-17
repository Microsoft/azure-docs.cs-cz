---
title: Stažení seznamu uživatelů na portálu Azure Active Directory | Dokumenty společnosti Microsoft
description: V Centru pro správu Azure ve Službě Azure Active Directory si hromadně stahujte záznamy uživatelů.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b35163387ed4ce71f7a2019835a1d9fdbff3051
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532631"
---
# <a name="download-a-list-of-users-in-azure-active-directory-portal"></a>Stažení seznamu uživatelů na portálu Azure Active Directory

Azure Active Directory (Azure AD) podporuje operace hromadného importu (vytvoření) uživatelů.

## <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li stáhnout seznam uživatelů z Centra pro správu Azure AD, musíte být přihlášeni s uživatelem přiřazeným k jedné nebo více rolísprávce na úrovni organizace ve službě Azure AD (Správce uživatelů je minimální požadovaná role). Pozvaní hosté a vývojářaplikací nejsou považovány za role správce.

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

Stav nevyřízených hromadných požadavků můžete zobrazit na stránce **S výsledky hromadné operace.**

[![](media/users-bulk-download/bulk-center.png "Check status in the Bulk Operations Results page")](media/users-bulk-download/bulk-center.png#lightbox)

## <a name="bulk-download-service-limits"></a>Limity služby hromadného stahování

Každá hromadná aktivita pro vytvoření seznamu uživatelů může běžet až jednu hodinu. To umožňuje vytvoření a stažení seznamu nejméně 500 000 uživatelů.

## <a name="next-steps"></a>Další kroky

- [Hromadné přidání uživatelů](users-bulk-add.md)
- [Hromadné odstranění uživatelů](users-bulk-delete.md)
- [Uživatelé hromadného obnovení](users-bulk-restore.md)
