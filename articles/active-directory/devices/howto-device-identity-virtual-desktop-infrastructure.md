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
ms.openlocfilehash: 1a1cba2c4572b2f898f631aefbbf316fae1195ac
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596361"
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
| Připojeno k hybridní službě Azure AD | Federované | Aktuální Windows * * * * a Windows nižší úrovně * * * * | Trvalý | Ano |
|   |   |   | Bez trvalého přihlášení | Ano |
|   | Spravované * * | Aktuální Windows a Windows nižší úrovně | Trvalý | Ano |
|   |   | Windows nižší úrovně | Bez trvalého přihlášení | Ano |
|   |   | Aktuální Windows | Bez trvalého přihlášení | Ne |
| Připojeno k Azure AD | Federované | Aktuální Windows | Trvalý | Ne |
|   |   |   | Bez trvalého přihlášení | Ne |
|   | Spravované | Aktuální Windows | Trvalý | Ne |
|   |   |   | Bez trvalého přihlášení | Ne |
| Registrace Azure AD | Federované | Aktuální Windows | Trvalý | Ne |
|   |   |   | Bez trvalého přihlášení | Ne |
|   | Spravované | Aktuální Windows | Trvalý | Ne |
|   |   |   | Bez trvalého přihlášení | Ne |

\* prostředí **federovaného** infrastruktury identit představuje prostředí s poskytovatelem identity, jako je například AD FS nebo jiné IDP třetí strany.

\* \* **spravované** prostředí infrastruktury identit představuje prostředí se službou Azure AD jako zprostředkovatele identity nasazeného pomocí [synchronizace hodnot hash hesel (kosmetice)](../hybrid/whatis-phs.md) nebo [předávacího ověřování (PTA)](../hybrid/how-to-connect-pta.md) s [ bezproblémové jednotné přihlašování](../hybrid/how-to-connect-sso.md).

\* \* \* **aktuální** zařízení s Windows představuje Windows 10, windows Server 2016 a Windows Server 2019.

\* \* \* \* zařízení se **systémem Windows nižší úrovně** představuje Windows 7, Windows 8.1, Windows Server 2008 R2, windows Server 2012 a Windows Server 2012 R2. Informace o podpoře ve Windows 7 najdete v článku [Podpora pro Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Informace o podpoře na Windows serveru 2008 R2 najdete v článku [Příprava pro Windows server 2008 na konci podpory](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Doprovodné materiály Microsoftu

Správci by měli na základě infrastruktury identit odkazovat na následující články, abyste se seznámili s postupem konfigurace hybridního připojení ke službě Azure AD.

- [Konfigurace služby Hybrid Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md)
- [Konfigurace hybridního Azure Active Directory JOIN pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

Pokud se spoléháte na nástroj pro přípravu systému (Sysprep. exe) a pokud pro instalaci používáte bitovou kopii Pre-Windows 10 1809, ujistěte se, že image nepochází ze zařízení, které už je zaregistrované ve službě Azure AD, jako připojené k hybridní službě Azure AD.

Pokud při vytváření dalších virtuálních počítačů spoléháte na snímek virtuálního počítače, ujistěte se, že snímek není z virtuálního počítače, který je už zaregistrovaný ve službě Azure AD, jako připojení k hybridní službě Azure AD.

Při nasazování netrvalé infrastruktury virtuálních počítačů by správci IT měli věnovat velkou pozornost při správě zastaralých zařízení v Azure AD. Společnost Microsoft doporučuje, aby správci IT implementovali následující pokyny. V takovém případě bude mít váš adresář spoustu zastaralých zařízení připojených k hybridní službě Azure AD, která byla zaregistrována z vaší netrvalé platformy VDI.

- Vytvořte a použijte předponu pro zobrazované jméno počítače, který označuje plochu jako na bázi VDI.
- V rámci skriptu pro odhlášení implementujte následující příkazy. Tyto příkazy aktivují optimální volání služby Azure AD za účelem odstranění tohoto zařízení.
   - Pro aktuální zařízení s Windows – dsregcmd. exe/Leave
   - Zařízení Windows nižší úrovně – autopracoviště. exe/Leave
- Definujte a implementujte proces pro [správu zastaralých zařízení](manage-stale-devices.md).
   - Jakmile budete mít strategii, jak identifikovat netrvalá hybridní zařízení připojená k Azure AD, můžete být více agresivní na vyčištění těchto zařízení, abyste se ujistili, že se váš adresář nespotřebovává s velkým počtem zastaralých zařízení.
 
## <a name="next-steps"></a>Další kroky

[Konfigurace služby Hybrid Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md)
