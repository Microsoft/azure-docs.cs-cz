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
ms.openlocfilehash: 70e5c7e5fa428d5a71ca1a7468bbaab2fc94078e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101643832"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Hybridní identita: Porovnání nástrojů pro integraci adresáře
V průběhu let se nástroje pro integraci adresáře rozšířily a vyvinuly.  


- [FIM](/previous-versions/windows/desktop/forefront-2010/ff182370(v=vs.100)) a [MIM](/microsoft-identity-manager/microsoft-identity-manager-2016) jsou pořád podporované a primárně umožňují synchronizaci mezi místními systémy.   [Konektor FIM Windows Azure AD](/previous-versions/mim/dn511001(v=ws.10)) se podporuje v zařízeních FIM a MIM, ale nedoporučuje se pro nová nasazení – zákazníci s místními zdroji, jako jsou poznámky nebo SAP HCM, by měli použít MIM k naplnění Active Directory Domain Services (služba AD DS) a pak taky použít buď Azure AD Connect synchronizace nebo Azure AD Connect zřízení cloudu pro synchronizaci z služba AD DS do Azure AD.
- [Azure AD Connect synchronizace](how-to-connect-sync-whatis.md) zahrnuje součásti a funkce dříve vydané v DirSync a Azure AD Sync pro synchronizaci mezi služba AD DS doménovou strukturou a službou Azure AD.  
- [Azure AD Connect zřizování cloudu](../cloud-sync/what-is-cloud-sync.md) je novým agentem Microsoftu pro synchronizaci z služba AD DS do Azure AD, který je vhodný pro scénáře, jako je například fúze a akvizice, kde jsou doménové struktury služby AD získanou společností izolované od nadřazených doménových struktur služby AD.

Další informace o rozdílech mezi Azure AD Connect synchronizace a Azure AD Connect zřizování cloudu najdete v článku [co je Azure AD Connect zřizování cloudu?](../cloud-sync/what-is-cloud-sync.md)

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).