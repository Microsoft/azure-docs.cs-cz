---
title: Identita zařízení a virtualizace plochy – Azure Active Directory
description: Přečtěte si, jak se dají identity zařízení infrastruktury VDI a Azure AD používat společně.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0dde9d8b50233c3c4033daf618e0e626c0174b0c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87903149"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identita zařízení a virtualizace plochy

Správci běžně nasazují platformy infrastruktury virtuálních klientských počítačů (VDI), které hostují operační systémy Windows ve svých organizacích. Správci nasazují VDI na:

- Zjednodušte správu.
- Snižte náklady prostřednictvím konsolidace a centralizovaného využití prostředků.
- Dodávejte koncovým uživatelům mobilitu a volnost přístupu k virtuálním plochám odkudkoli a na jakémkoli zařízení.

Existují dva hlavní typy virtuálních ploch:

- Trvalý
- Bez trvalého přihlášení

Trvalé verze používají pro každého uživatele nebo fond uživatelů jedinečnou image plochy. Tyto jedinečné plochy je možné přizpůsobit a uložit pro budoucí použití. 

Netrvalé verze používají kolekci ploch, ke kterým mají uživatelé přístup podle potřeby. Po odhlášení uživatele se tyto netrvalé plochy vrátí do původního stavu.

Tento článek pokryje správcům Microsoftu informace o podpoře identity zařízení a VDI. Další informace o identitě zařízení najdete v článku [co je identita zařízení](overview.md).

## <a name="supported-scenarios"></a>Podporované scénáře

Než nakonfigurujete identity zařízení ve službě Azure AD pro vaše prostředí VDI, Seznamte se s podporovanými scénáři. Následující tabulka ukazuje, které scénáře zřizování jsou podporované. Zřizování v tomto kontextu předpokládá, že správce může konfigurovat identitu zařízení ve velkém měřítku bez nutnosti zásahu koncového uživatele.

| Typ identity zařízení | Infrastruktura identity | Zařízení s Windows | Verze platformy VDI | Podporováno |
| --- | --- | --- | --- | --- |
| k hybridní službě Azure AD. | Federované | Aktuální Windows * * * * a Windows nižší úrovně * * * * | Trvalý | Ano |
|   |   | Aktuální Windows | Bez trvalého přihlášení | Ne |
|   |   | Windows nižší úrovně | Bez trvalého přihlášení | Ano |
|   | Spravované * * | Aktuální Windows a Windows nižší úrovně | Trvalý | Ano |
|   |   | Aktuální Windows | Bez trvalého přihlášení | Ne |
|   |   | Windows nižší úrovně | Bez trvalého přihlášení | Ano |
| Připojené k Azure AD | Federovaní | Aktuální Windows | Trvalý | Ne |
|   |   |   | Bez trvalého přihlášení | Ne |
|   | Spravované | Aktuální Windows | Trvalý | Ne |
|   |   |   | Bez trvalého přihlášení | Ne |
| Registrováno v Azure AD | Federovaní | Aktuální Windows | Trvalý | Neuvedeno |
|   |   |   | Bez trvalého přihlášení | Neuvedeno |
|   | Spravované | Aktuální Windows | Trvalý | Neuvedeno |
|   |   |   | Bez trvalého přihlášení | Neuvedeno |

\***Federované** prostředí infrastruktury identit představuje prostředí s poskytovatelem identity, jako je AD FS nebo jiné IDP třetí strany.

\*\***Spravované** prostředí infrastruktury identit představuje prostředí se službou Azure AD jako zprostředkovatele identity nasazeného s použitím [synchronizace hodnot hash hesel (kosmetice)](../hybrid/whatis-phs.md) nebo [předávacího ověřování (PTA)](../hybrid/how-to-connect-pta.md) s [bezproblémovém jednotným přihlašováním](../hybrid/how-to-connect-sso.md).

\*\*\***Aktuální zařízení Windows** jsou Windows 10, windows Server 2016 a windows server 2019.

\*\*\*\*Zařízení **nižší úrovně Windows** reprezentují Windows 7, Windows 8.1, windows Server 2008 R2, windows Server 2012 a windows Server 2012 R2. Informace o podpoře ve Windows 7 najdete v článku [Podpora pro Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Informace o podpoře na Windows serveru 2008 R2 najdete v článku [Příprava pro Windows server 2008 na konci podpory](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Doprovodné materiály Microsoftu

Správci by měli na základě infrastruktury identit odkazovat na následující články, abyste se seznámili s postupem konfigurace hybridního připojení ke službě Azure AD.

- [Konfigurace služby Hybrid Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md)
- [Konfigurace hybridního Azure Active Directory JOIN pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

Pokud se spoléháte na nástroj pro přípravu systému (sysprep.exe) a pokud pro instalaci používáte image starší než Windows 10 1809, ujistěte se, že image není ze zařízení, které už je zaregistrované ve službě Azure AD, jako připojené k hybridní službě Azure AD.

Pokud při vytváření dalších virtuálních počítačů spoléháte na snímek virtuálního počítače, ujistěte se, že snímek není z virtuálního počítače, který je už zaregistrovaný ve službě Azure AD, jako připojení k hybridní službě Azure AD.

V současných verzích Windows není podporováno hybridní připojení k Azure AD pro netrvalou infrastrukturu virtuálních počítačů. Při nasazování netrvalé infrastruktury virtuálních počítačů (VDI) pro Windows na nižší úrovni by správci IT měli věnovat velkou pozornost správě zastaralých zařízení v Azure AD. Společnost Microsoft doporučuje, aby správci IT implementovali následující pokyny. V takovém případě bude mít váš adresář spoustu zastaralých zařízení připojených k hybridní službě Azure AD, která byla zaregistrována z vaší netrvalé platformy VDI.

- Vytvořte a použijte předponu pro zobrazované jméno počítače, který označuje plochu jako na bázi VDI.
- Jako součást skriptu pro odhlášení implementujte následující příkaz. Tento příkaz aktivuje nejlepší úsilí ve službě Azure AD za účelem odstranění tohoto zařízení.
   - Zařízení Windows nižší úrovně – autoworkplace.exe/Leave
- Definujte a implementujte proces pro [správu zastaralých zařízení](manage-stale-devices.md).
   - Jakmile budete mít strategii, jak identifikovat netrvalá hybridní zařízení připojená k Azure AD, můžete být více agresivní na vyčištění těchto zařízení, abyste se ujistili, že se váš adresář nespotřebovává s velkým počtem zastaralých zařízení.
 
## <a name="next-steps"></a>Další kroky

[Konfigurace služby Hybrid Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md)
