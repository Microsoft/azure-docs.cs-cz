---
title: Principy Azure AD Connect 1.4.xx.x a zmizení zařízení | Dokumenty společnosti Microsoft
description: Tento dokument popisuje problém, který vzniká s verzí 1.4.xx.x Služby Azure AD Connect
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176021"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Principy Azure AD Connect 1.4.xx.x a zmizení zařízení
S verzí 1.4.xx.x Azure AD Connect, někteří zákazníci mohou vidět některé nebo všechny jejich zařízení s Windows zmizí z Azure AD. To není důvodkem k obavám, protože tyto identity zařízení nejsou používány službou Azure AD během autorizace podmíněného přístupu. Tato změna neodstraní všechna zařízení s Windows, která byla správně zaregistrována pomocí azure ad pro hybridní připojení Azure AD.

Pokud se zobrazí odstranění objektů zařízení ve službě Azure AD přesahující prahovou hodnotu odstranění exportu, doporučuje se, aby zákazník povolil odstranění projít. [Jak: povolit odstranění toku, když překročí prahovou hodnotu odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Pozadí
Zařízení s Windows registrovaná jako hybridní připojení Azure AD jsou reprezentovaná ve službě Azure AD jako objekty zařízení. Tyto objekty zařízení lze použít pro podmíněný přístup. Zařízení s Windows 10 se synchronizují do cloudu přes Azure AD Connect, zařízení s Windows na nižší úrovni se zaevidují přímo pomocí služby AD FS nebo bezproblémového jednotného přihlašování.

## <a name="windows-10-devices"></a>Zařízení s Windows 10
Jenom zařízení s Windows 10 s konkrétní hodnotou atributu userCertificate nakonfigurované pomocí hybridního připojení Azure AD mají být synchronizována do cloudu pomocí Azure AD Connect. V předchozích verzích Azure AD Connect tento požadavek nebyl důsledně vynuceno, výsledkem jsou zbytečné objekty zařízení ve službě Azure AD. Taková zařízení ve službě Azure AD vždy zůstala ve stavu čekající na vyřízení, protože tato zařízení nebyla určena k registraci ve službě Azure AD. 

Tato verze Azure AD Connect bude synchronizovat jenom zařízení s Windows 10, která jsou správně nakonfigurovaná jako hybridní Azure AD připojená. Objekty zařízení s Windows 10 bez připojení Azure AD konkrétní userCertificate se odeberou z Azure AD.

## <a name="down-level-windows-devices"></a>Zařízení windows nižší úrovně
Azure AD Connect by nikdy neměla synchronizovat [zařízení s Windows nižší úrovně](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Všechna zařízení ve službě Azure AD dříve synchronizované nesprávně se teď odstraní z Azure AD. Pokud Azure AD Connect se pokouší odstranit [zařízení windows nižší úrovně](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), pak zařízení není ten, který byl vytvořen Microsoft Workplace Join pro počítače bez Windows [10 MSI](https://www.microsoft.com/download/details.aspx?id=53554) a není možné spotřebovávat žádné jiné funkce Azure AD.

Někteří zákazníci možná budou muset znovu navštívit [how to: naplánovat hybridní azure active directory připojit k implementaci](../devices/hybrid-azuread-join-plan.md) získat jejich zařízení se systémem Windows správně zaregistrovat a ujistěte se, že tato zařízení mohou plně účastnit zařízení podmíněného přístupu. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Jak mohu ověřit, která zařízení jsou touto aktualizací odstraněna?

Chcete-li ověřit, která zařízení jsou odstraněna, můžete použít tento skript prostředí PowerShell:https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Tento skript generuje sestavu o certifikátech uložených v objektech počítače služby Active Directory, konkrétně o certifikátech vydaných funkcí připojení hybridní ho služby Azure AD.
Zkontroluje certifikáty přítomné ve vlastnosti UserCertificate objektu Computer ve službě AD a pro každý certifikát, jehož platnost nevypršela, ověří, zda byl certifikát vydán pro funkci připojení hybridního azure ad (tj. název subjektu odpovídá CN={ObjectGUID}).
Než by azure ad připojení synchronizovat do Azure AD libovolný počítač, který obsahoval alespoň jeden platný certifikát, ale počínaje Azure AD Connect verze 1.4, modul synchronizace můžete identifikovat hybridní certifikáty připojení Azure AD a bude 'cloudfilter' objekt počítače ze synchronizace do Azure AD, pokud neexistuje platný certifikát připojení hybridní azure ad.
Zařízení Azure AD, která už byla synchronizována do služby AD, ale nemají platný certifikát připojení hybridní ho služby Azure AD, se synchronizační modul odstraní (CloudFiltered=TRUE).

## <a name="next-steps"></a>Další kroky
- [Historie verzí služby Azure AD Connect](reference-connect-version-history.md)
