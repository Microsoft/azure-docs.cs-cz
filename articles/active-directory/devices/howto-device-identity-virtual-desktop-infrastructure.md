---
title: Identita zařízení a virtualizace desktopů – Azure Active Directory
description: Zjistěte, jak lze identity zařízení VDI a Azure AD používat společně
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
ms.openlocfilehash: 7b431cee3b8e5fc168dec2766442d6f6b9869d1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900377"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identita zařízení a virtualizace plochy

Správci běžně nasazují platformy infrastruktury virtuálních desktopů (VDI), které jsou hostiteli operačních systémů Windows ve svých organizacích. Správci nasazují VDI do:

- Zjednodušte správu.
- Snižte náklady prostřednictvím konsolidace a centralizace zdrojů.
- Poskytujte mobilitu koncovým uživatelům a svobodu přístupu k virtuálním desktopům kdykoli, odkudkoli a na jakémkoli zařízení.

Existují dva primární typy virtuálních ploch:

- Trvalý
- Netrvalé

Trvalé verze používají jedinečnou bitovou kopii plochy pro každého uživatele nebo fond uživatelů. Tyto jedinečné plochy lze přizpůsobit a uložit pro budoucí použití. 

Netrvalé verze používají kolekci ploch, ke kterým mají uživatelé přístup podle potřeby. Tyto netrvalé plochy se po odhlásíní uživatele vrátí do původního stavu.

Tento článek se bude týkat pokynů společnosti Microsoft pro správce o podpoře identity zařízení a VDI. Další informace o identitě zařízení najdete v článku [Co je identita zařízení](overview.md).

## <a name="supported-scenarios"></a>Podporované scénáře

Než nakonfigurujete identity zařízení ve službě Azure AD pro vaše prostředí VDI, seznamte se s podporovanými scénáři. Níže uvedená tabulka ukazuje, které scénáře zřizování jsou podporovány. Zřizování v tomto kontextu znamená, že správce může konfigurovat identity zařízení ve velkém měřítku bez nutnosti jakékoli interakce s koncovým uživatelem.

| Typ identity zařízení | Infrastruktura identit | Zařízení s Windows | Verze platformy VDI | Podporuje se |
| --- | --- | --- | --- | --- |
| k hybridní službě Azure AD. | Federovaný* | Windows current*** a Windows down-level**** | Trvalý | Ano |
|   |   | Windows aktuální | Netrvalé | Ne |
|   |   | Windows nižší úrovně | Netrvalé | Ano |
|   | Spravováno** | Aktuální systém Windows a nižší úroveň systému Windows | Trvalý | Ano |
|   |   | Windows aktuální | Netrvalé | Ne |
|   |   | Windows nižší úrovně | Netrvalé | Ano |
| Připojené k Azure AD | Federovaní | Windows aktuální | Trvalý | Ne |
|   |   |   | Netrvalé | Ne |
|   | Spravovaní | Windows aktuální | Trvalý | Ne |
|   |   |   | Netrvalé | Ne |
| Registrováno v Azure AD | Federovaní | Windows aktuální | Trvalý | Ne |
|   |   |   | Netrvalé | Ne |
|   | Spravovaní | Windows aktuální | Trvalý | Ne |
|   |   |   | Netrvalé | Ne |

\*Prostředí infrastruktury **federovaných** identit představuje prostředí s poskytovatelem identity, jako je služba AD FS nebo jiné idp třetí strany.

\*\*Prostředí **Managed** spravované infrastruktury identit představuje prostředí s Azure AD jako zprostředkovatele identity nasazeným buď se [synchronizací hash hesel (PHS),](../hybrid/whatis-phs.md) nebo [předávacím ověřováním (PTA)](../hybrid/how-to-connect-pta.md) s [bezproblémovým jednotném přihlašování](../hybrid/how-to-connect-sso.md).

\*\*\***Aktuální** zařízení Windows představují Windows 10, Windows Server 2016 a Windows Server 2019.

\*\*\*\***Zařízení nižší úrovně systému Windows** představují windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2. Informace o podpoře v systému Windows 7 naleznete v [tématu Podpora systému Windows 7 končí](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Informace o podpoře systému Windows Server 2008 R2 naleznete v [tématu Příprava na ukončení podpory systému Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

## <a name="microsofts-guidance"></a>Pokyny společnosti Microsoft

Správci by měli odkazovat na následující články na základě jejich identity infrastruktury, naučit se konfigurovat hybridní připojení Azure AD.

- [Konfigurace hybridního připojení služby Azure Active Directory pro federované prostředí](hybrid-azuread-join-federated-domains.md)
- [Konfigurace hybridního připojení služby Azure Active Directory pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

Pokud se spoléháte na nástroj pro přípravu systému (sysprep.exe) a pokud používáte image před Windows 10 1809 pro instalaci, ujistěte se, že image není ze zařízení, které je již registrované s Azure AD jako hybridní Azure AD připojil.

Pokud se spoléháte na snímek virtuálního počítače (VM) k vytvoření dalších virtuálních počítačů, ujistěte se, že snímek není z virtuálního počítače, který je již registrována s Azure AD jako hybridní připojení Azure AD.

Při nasazování netrvalého VDI by správci IT měli věnovat velkou pozornost správě zastaralých zařízení ve službě Azure AD. Společnost Microsoft doporučuje, aby správci IT implementovali níže uvedené pokyny. Pokud tak neučiníte, bude mít váš adresář spoustu zastaralých hybridních zařízení Azure AD spojených, které byly zaregistrovány z vaší netrvalé platformy VDI.

- Vytvořte a použijte předponu pro zobrazovaný název počítače, který označuje plochu jako vdi.
- Implementujte následující příkaz jako součást odhlašovacího skriptu. Tento příkaz aktivuje volání nejlepší úsilí do Služby Azure AD k odstranění zařízení.
   - Pro windows down-level zařízení – autoworkplace.exe /leave
- Definujte a implementujte proces pro [správu zastaralých zařízení](manage-stale-devices.md).
   - Jakmile budete mít strategii k identifikaci netrvalé hybridní Azure AD připojená zařízení, můžete být agresivnější na vyčištění těchto zařízení zajistit, že váš adresář nezíská spotřebované s velkým množstvím zastaralých zařízení.
 
## <a name="next-steps"></a>Další kroky

[Konfigurace hybridního připojení služby Azure Active Directory pro federované prostředí](hybrid-azuread-join-federated-domains.md)
