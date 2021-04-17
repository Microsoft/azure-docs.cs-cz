---
title: Identita zařízení a virtualizace plochy – Azure Active Directory
description: Přečtěte si, jak se dají identity zařízení infrastruktury VDI a Azure AD používat společně.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1d78094effe6919587f24c2262612e4fab347d
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575373"
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

Netrvalé verze používají kolekci ploch, ke kterým mají uživatelé přístup podle potřeby. Tyto netrvalé pracovní plochy se vrátí do původního stavu, v případě Windows aktuální<sup>1</sup> k tomu dojde, když virtuální počítač projde procesem obnovení vypnutí/RESTARTOVÁNÍ/OS a v případě Windows nižší úrovně<sup>2</sup> to nastane, když se uživatel odhlásí.

Došlo ke zvýšení netrvalého nasazení VDI, protože vzdálená práce bude i nadále novou normou. Když zákazníci nasadí netrvalou infrastrukturu virtuálních počítačů, je důležité zajistit, aby bylo možné spravovat změny zařízení, které by mohly způsobovat kvůli časté registraci zařízení, aniž by museli mít správnou strategii pro správu životního cyklu zařízení.

> [!IMPORTANT]
> Nepovedlo se spravovat změny zařízení. může vést k nárůstu využití kvóty vašeho tenanta a potenciálního rizika přerušení služby, Pokud vyčerpáte kvótu tenanta. Při nasazování netrvalých prostředí VDI byste měli postupovat podle pokynů uvedených níže, abyste se vyhnuli této situaci.

Tento článek pokryje správcům Microsoftu informace o podpoře identity zařízení a VDI. Další informace o identitě zařízení najdete v článku [co je identita zařízení](overview.md).

## <a name="supported-scenarios"></a>Podporované scénáře

Než nakonfigurujete identity zařízení ve službě Azure AD pro vaše prostředí VDI, Seznamte se s podporovanými scénáři. Následující tabulka ukazuje, které scénáře zřizování jsou podporované. Zřizování v tomto kontextu předpokládá, že správce může konfigurovat identitu zařízení ve velkém měřítku bez nutnosti zásahu koncového uživatele.

| Typ identity zařízení | Infrastruktura identity | Zařízení s Windows | Verze platformy VDI | Podporováno |
| --- | --- | --- | --- | --- |
| k hybridní službě Azure AD. | Federované<sup>3</sup> | Aktuální Windows a Windows nižší úrovně | Trvalý | Yes |
|   |   | Aktuální Windows | Bez trvalého přihlášení | Ano<sup>5</sup> |
|   |   | Windows nižší úrovně | Bez trvalého přihlášení | Ano<sup>6</sup> |
|   | Spravované<sup>4</sup> | Aktuální Windows a Windows nižší úrovně | Trvalý | Yes |
|   |   | Aktuální Windows | Bez trvalého přihlášení | No |
|   |   | Windows nižší úrovně | Bez trvalého přihlášení | Ano<sup>6</sup> |
| Připojené k Azure AD | Federovaní | Aktuální Windows | Trvalý | No |
|   |   |   | Bez trvalého přihlášení | No |
|   | Spravované | Aktuální Windows | Trvalý | No |
|   |   |   | Bez trvalého přihlášení | No |
| Registrováno v Azure AD | Federované/spravované | Windows Current/Windows nižší úrovně | Trvalá/netrvalá | Neuvedeno |

<sup>1</sup> **Aktuální zařízení Windows** reprezentují Windows 10, Windows Server 2016 V1803 nebo novější a Windows Server 2019.
<sup>2</sup> zařízení **nižší úrovně Windows** reprezentují Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 a Windows Server 2012 R2. Informace o podpoře ve Windows 7 najdete v článku [Podpora pro Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Informace o podpoře na Windows serveru 2008 R2 najdete v článku [Příprava pro Windows server 2008 na konci podpory](https://www.microsoft.com/cloud-platform/windows-server-2008).

<sup>3</sup> **federované** prostředí infrastruktury identit představuje prostředí s poskytovatelem identity, jako je AD FS nebo jiné IDP třetí strany.

<sup>4</sup> **spravované** prostředí infrastruktury identit představuje prostředí se službou Azure AD jako zprostředkovatele identity nasazeného s použitím [synchronizace hodnot hash hesel (kosmetice)](../hybrid/whatis-phs.md) nebo [předávacího ověřování (PTA)](../hybrid/how-to-connect-pta.md) pomocí [bezproblémového jednotného přihlašování](../hybrid/how-to-connect-sso.md).

<sup>5</sup> **Podpora nestálosti systému Windows** vyžaduje další pozornost, jak je uvedeno níže v části s pokyny. Tento scénář vyžaduje systém Windows 10 1803, Windows Server 2019 nebo Windows Server (půlroční kanál) počínaje verzí 1803.

<sup>6</sup> **Podpora nestálosti Windows na nižší úrovni** vyžaduje další pozornost, jak je uvedeno níže v části s pokyny.


## <a name="microsofts-guidance"></a>Doprovodné materiály Microsoftu

Správci by měli na základě infrastruktury identit odkazovat na následující články, abyste se seznámili s postupem konfigurace hybridního připojení ke službě Azure AD.

- [Konfigurace služby Hybrid Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md)
- [Konfigurace hybridního Azure Active Directory JOIN pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>Netrvalá Infrastruktura virtuálních počítačů

Při nasazení trvalé infrastruktury virtuálních počítačů (VDI) Společnost Microsoft doporučuje, aby správci IT implementovali následující pokyny. V takovém případě bude mít váš adresář spoustu zastaralých hybridních zařízení připojených k Azure AD, která byla zaregistrovaná z vaší netrvalé platformy VDI, což má za následek vyšší tlak na vaši kvótu tenanta a riziko přerušení služby kvůli vyřazení kvóty tenanta.

- Pokud se spoléháte na nástroj pro přípravu systému (sysprep.exe) a pokud pro instalaci používáte image starší než Windows 10 1809, ujistěte se, že image není ze zařízení, které už je zaregistrované ve službě Azure AD, jako připojené k hybridní službě Azure AD.
- Pokud při vytváření dalších virtuálních počítačů spoléháte na snímek virtuálního počítače, ujistěte se, že snímek není z virtuálního počítače, který je už zaregistrovaný ve službě Azure AD, jako připojení k hybridní službě Azure AD.
- Vytvořte a použijte předponu pro zobrazované jméno (např. NPVDI-) počítače, který označuje plochu jako netrvalou bázi VDI.
- Pro Windows nižší úrovně:
   - Implementuje příkaz **autoworkplacejoin/Leave** jako součást skriptu pro odhlášení. Tento příkaz by měl být aktivován v kontextu uživatele a měl by být proveden před úplným odhlášením uživatele a v případě, že je stále k dispozici připojení k síti.
- Pro Windows Current ve federovaném prostředí (např. AD FS):
   - Implementujte **dsregcmd/JOIN** jako součást pořadí spouštění virtuálních počítačů nebo objednávky a předtím, než se uživatel přihlásí.
   - V rámci procesu vypnutí nebo restartování virtuálního počítače **nespouštějte dsregcmd** /Leave.
- Definujte a implementujte proces pro [správu zastaralých zařízení](manage-stale-devices.md).
   - Jakmile budete mít strategii pro identifikaci netrvalých hybridních zařízení připojených k Azure AD (například pomocí předpony zobrazovaného názvu počítače), měli byste být na vyčištění těchto zařízení spolehlivější, abyste se ujistili, že se váš adresář nespotřebovává s velkým počtem zastaralých zařízení.
   - Pro netrvalá nasazení VDI ve Windows aktuální a nižší úrovni byste měli odstranit zařízení, která mají **ApproximateLastLogonTimestamp** starší než 15 dní.

> [!NOTE]
> Pokud chcete zabránit stavu připojení zařízení, zajistěte, aby se používala netrvalá Infrastruktura virtuálních počítačů, a ověřte, že je nastaven následující klíč registru:  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> Ujistěte se, že používáte Windows 10 verze 1803 nebo vyšší.  
>
> Roaming libovolných dat v cestě není `%localappdata%` podporován. Pokud se rozhodnete přesunout obsah v rámci, ujistěte se `%localappdata%` , že obsah následujících složek a klíčů registru **nikdy** neopouští zařízení v žádné podmínce. Například: nástroje pro migraci profilu musí přeskočit následující složky a klíče:
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
>


### <a name="persistent-vdi"></a>Trvalá Infrastruktura virtuálních počítačů

Po nasazení trvalé infrastruktury virtuálních počítačů Microsoft doporučuje, aby správci IT implementovali následující pokyny. V důsledku tohoto selhání dojde k problémům s nasazením a ověřením. 

- Pokud se spoléháte na nástroj pro přípravu systému (sysprep.exe) a pokud pro instalaci používáte image starší než Windows 10 1809, ujistěte se, že image není ze zařízení, které už je zaregistrované ve službě Azure AD, jako připojené k hybridní službě Azure AD.
- Pokud při vytváření dalších virtuálních počítačů spoléháte na snímek virtuálního počítače, ujistěte se, že snímek není z virtuálního počítače, který je už zaregistrovaný ve službě Azure AD, jako připojení k hybridní službě Azure AD.

Kromě toho doporučujeme, abyste implementovali proces pro [správu zastaralých zařízení](manage-stale-devices.md). Tím se zajistí, že se váš adresář nespotřebovává s velkým počtem zastaralých zařízení, pokud vaše virtuální počítače pravidelně resetujete.
 
## <a name="next-steps"></a>Další kroky

[Konfigurace služby Hybrid Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md)
