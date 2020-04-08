---
title: 'Hybridní identita: Porovnání nástrojů pro integraci adresáře | Dokumentace Microsoftu'
description: Tato stránka nabízí komplexní tabulku porovnávající různé nástroje integrace adresáře, které lze použít pro integraci adresáře.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811094"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hybridní identita: Porovnání nástrojů pro integraci adresáře
V průběhu let se nástroje pro integraci adresáře rozšířily a vyvinuly.  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) a [MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) jsou stále podporovány a primárně umožňují synchronizaci mezi místními systémy.   [Konektor FIM Windows Azure AD je](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) podporovaný ve FIM i MIM, ale nedoporučuje se pro nová nasazení – zákazníci s místními zdroji, jako jsou Poznámky nebo SAP HCM, by měli používat MIM k naplnění služby Active Directory Domain Services (AD DS) a pak také použít synchronizaci Azure AD Connect nebo azure ad connect cloud zřizování k synchronizaci ze služby AD DS do Služby Azure AD.
- [Azure AD Connect Synchronizace](how-to-connect-sync-whatis.md) zahrnuje součásti a funkce dříve vydané v DirSync a Azure AD Sync, pro synchronizaci mezi doménovými strukturami služby AD DS a Azure AD.  
- [Azure AD Connect cloud zřizování](../cloud-provisioning/what-is-cloud-provisioning.md) je nový agent Microsoft pro synchronizaci ze služby AD DS do Azure AD, užitečné pro scénáře, jako je fúze a akvizice, kde jsou doménové struktury ad získané společnosti izolované od doménové struktury ad mateřské společnosti.

Další informace o rozdílech mezi synchronizací Azure AD Connect a zřizováním cloudu Azure AD Connect najdete v článku [Co je zřizování cloudu Azure AD Connect?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).

