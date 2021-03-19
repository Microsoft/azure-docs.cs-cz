---
title: Porozumění Azure AD Connect 1.4. xx. x a odzobrazení zařízení | Microsoft Docs
description: Tento dokument popisuje problém, který nastane s verzí 1.4. xx. x z Azure AD Connect
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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "73176021"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>Porozumění Azure AD Connect 1.4. xx. x a odobrazení zařízení
S verzí 1.4. xx. x z Azure AD Connect můžou někteří zákazníci vidět, že některá nebo všechna zařízení s Windows zmizí ze služby Azure AD. Nejedná se o příčinu obav, protože tyto identity zařízení služba Azure AD během autorizace podmíněného přístupu nepoužívá. Tato změna neodstraní žádná zařízení s Windows, která byla správně zaregistrovaná ve službě Azure AD pro připojení k hybridní službě Azure AD.

Pokud se vám v Azure AD zobrazuje odstranění objektů zařízení, než je prahová hodnota pro odstranění exportu, doporučuje se, aby zákazník procházel tím, že se odstraní. [Postupy: povolení přetékání mazání při překročení prahové hodnoty pro odstranění](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>Pozadí
Zařízení s Windows zaregistrovaná jako připojená k hybridní službě Azure AD jsou ve službě Azure AD zastoupena jako objekty zařízení. Tyto objekty zařízení lze použít pro podmíněný přístup. Zařízení s Windows 10 se synchronizují do cloudu prostřednictvím Azure AD Connect, zařízení s Windows na nižší úrovni se registrují přímo pomocí AD FS nebo bezproblémového jednotného přihlašování.

## <a name="windows-10-devices"></a>Zařízení s Windows 10
Pouze zařízení s Windows 10 s konkrétní hodnotou atributu userCertificate nakonfigurovaná hybridním připojením služby Azure AD by měla být synchronizovaná v cloudu Azure AD Connect. V předchozích verzích Azure AD Connect tento požadavek nebyl striktně vynutil, což vedlo k zbytečným objektům zařízení v Azure AD. Taková zařízení ve službě Azure AD se vždycky nechali ve stavu čekání na vyřízení, protože tato zařízení nebyla určená k registraci ve službě Azure AD. 

Tato verze Azure AD Connect bude synchronizovat jenom zařízení s Windows 10, která jsou správně nakonfigurovaná tak, aby byla připojená k hybridní službě Azure AD. Z Azure AD se odeberou objekty zařízení s Windows 10 bez konkrétního userCertificate Azure AD JOIN.

## <a name="down-level-windows-devices"></a>Down-Level zařízení s Windows
Azure AD Connect by nikdy neměl synchronizovat [zařízení s Windows nižší úrovně](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices). Všechna zařízení v Azure AD, která se dřív synchronizoval, se teď z Azure AD odstraní. Pokud se Azure AD Connect pokouší odstranit zařízení s [Windows na nižší úrovni](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices), nejedná se o takové zařízení, které vytvořila [Služba Microsoft Workplace JOIN pro počítače se systémem Windows 10 nebo MSI](https://www.microsoft.com/download/details.aspx?id=53554) , a nelze ji spotřebovat žádnou jinou funkcí služby Azure AD.

Někteří zákazníci si možná budou muset znovu navštívit, jak naplánovat, aby se Azure Active Directory vaše zařízení s Windows [naplánovala](../devices/hybrid-azuread-join-plan.md) správně a zajistila tak, že se tato zařízení můžou plně zúčastnit podmíněného přístupu na základě zařízení. 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>Jak zjistím, která zařízení se s touto aktualizací odstraňují?

Pokud chcete ověřit, která zařízení se odstraňují, můžete použít tento skript PowerShellu: https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

Tento skript vygeneruje sestavu o certifikátech uložených v objektech počítače služby Active Directory, konkrétně certifikáty vydaných funkcí připojení k hybridní službě Azure AD.
Kontroluje certifikáty přítomné ve vlastnosti UserCertificate objektu počítače ve službě AD a pro každý přítomný certifikát bez vypršení platnosti ověřuje, jestli se certifikát vystavil pro funkci připojení Hybrid Azure AD (tj. název subjektu odpovídá CN = {ObjectGUID}).
Předtím, než se Azure AD Connect do Azure AD synchronizuje každý počítač, který obsahoval aspoň jeden platný certifikát, ale začíná na Azure AD Connect verze 1,4, může modul synchronizace identifikovat hybridní certifikáty Azure AD a bude cloudfilterovat objekt počítače z synchronizace do Azure AD, pokud neexistuje platný certifikát pro připojení k hybridní službě Azure AD.
Zařízení Azure AD, která už byla synchronizovaná s AD, ale nemají platný certifikát hybridního připojení k Azure AD, se odstraní pomocí synchronizačního modulu (CloudFiltered = TRUE).

## <a name="next-steps"></a>Další kroky
- [Historie verze Azure AD Connect](reference-connect-version-history.md)
