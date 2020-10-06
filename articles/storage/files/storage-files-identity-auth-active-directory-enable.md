---
title: Přehled – místní ověřování služba AD DS do sdílených složek Azure
description: Přečtěte si o ověřování Active Directory Domain Services (služba AD DS) do sdílených složek Azure. Tento článek se zabývá scénáři podpory, dostupností a vysvětluje, jak oprávnění fungují mezi služba AD DS a službou Azure Active Directory.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: bb408c762c33e4d146a2f0ef36f32e525b3859bd
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91758264"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Přehled – místní Active Directory Domain Services ověřování pomocí protokolu SMB pro sdílené složky Azure

[Soubory Azure](storage-files-introduction.md)   podporuje ověřování na základě identity přes protokol SMB (Server Message Block) prostřednictvím dvou typů doménových služeb: místní Active Directory Domain Services (služba AD DS) a Azure Active Directory Domain Services (Azure služba AD DS). Důrazně doporučujeme, abyste si v [části jak to funguje](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works) , abyste si vybrali správnou doménovou službu pro ověřování. Nastavení se liší v závislosti na zvolené doménové službě. Tato série článků se zaměřuje na povolování a konfigurování místních služba AD DS pro ověřování pomocí sdílených složek Azure.

Pokud se sdílenými složkami Azure teprve začínáte, doporučujeme si přečíst si náš [Průvodce plánováním](storage-files-planning.md) , abyste si přečetli následující řadu článků.

## <a name="supported-scenarios-and-restrictions"></a>Podporované scénáře a omezení

- Služba AD DS identit, které se používají pro místní služba AD DS ověřování souborů Azure, musí být synchronizované do Azure AD. Synchronizace hodnot hash hesel je volitelná. 
- Podporuje sdílené složky Azure spravované pomocí Azure File Sync.
- Podporuje ověřování pomocí protokolu Kerberos se službou AD s šifrováním RC4-HMAC a [AES 256](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). Šifrování Kerberos 128 se ještě nepodporuje.
- Podporuje prostředí s jednotným přihlašováním.
- Podporuje se jenom na klientech, na kterých běží novější verze operačních systémů než Windows 7 nebo Windows Server 2008 R2.
- Podporuje se jenom s doménovou strukturou služby AD, na kterou je účet úložiště zaregistrovaný. Ve výchozím nastavení můžete přistupovat ke sdíleným složkám Azure jenom s přihlašovacími údaji služba AD DS z jedné doménové struktury. Pokud potřebujete mít přístup ke sdílené složce Azure z jiné doménové struktury, ujistěte se, že máte nakonfigurovanou správnou důvěryhodnost doménové struktury. Podrobnosti najdete v [nejčastějších dotazech](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) .
- Nepodporuje ověřování u účtů počítačů vytvořených v služba AD DS.
- Nepodporuje ověřování proti sdíleným složkám souborů NFS (Network File System).

Když povolíte služba AD DS pro sdílené složky Azure přes protokol SMB, počítače připojené služba AD DS můžou připojit sdílené složky Azure pomocí svých stávajících přihlašovacích údajů služba AD DS. Tato funkce se dá povolit u prostředí služba AD DS hostovaného buď v premch počítačích, nebo hostovaných v Azure.

> [!NOTE]
> Abychom vám pomohli nastavit ověřování Azure Files AD pro některé běžné případy použití, publikovali jsme dvě videa s podrobnými pokyny pro následující scénáře:
> - [Výměna místních souborových serverů se soubory Azure (včetně nastavení na privátním odkazu pro soubory a ověřování AD)](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [Používání služby soubory Azure jako kontejneru profilů pro virtuální počítače s Windows (včetně nastavení ověřování AD a konfigurace FsLogix)](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>Předpoklady 

Než povolíte služba AD DS ověřování sdílených složek Azure, ujistěte se, že jste dokončili následující požadavky: 

- Vyberte nebo vytvořte [prostředí služba AD DS](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) a [synchronizujte ho s Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) s Azure AD Connect. 

    Tuto funkci můžete povolit na novém nebo existujícím místním služba AD DS prostředí. Identity používané pro přístup se musí synchronizovat do Azure AD. Tenant služby Azure AD a sdílená složka, ke kterým přistupujete, musí být přidruženy ke stejnému předplatnému.

- Připojte se k místnímu počítači nebo VIRTUÁLNÍmu počítači Azure k místnímu služba AD DS. Informace o tom, jak připojit k doméně, najdete v tématu [připojení počítače k doméně](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

    Pokud počítač není připojený k doméně služba AD DS, může být pro ověřování stále možné využít přihlašovací údaje služby AD, pokud má váš počítač dohled nad řadičem domény služby AD.

- Vyberte nebo vytvořte účet úložiště Azure.  Pro zajištění optimálního výkonu doporučujeme nasadit účet úložiště ve stejné oblasti jako klient, ze kterého plánujete přístup ke sdílené složce. Pak [připojte sdílenou složku Azure](storage-how-to-use-files-windows.md) k vašemu klíči účtu úložiště. Připojením k klíči účtu úložiště ověříte připojení.

    Ujistěte se, že účet úložiště obsahující vaše sdílené složky ještě není nakonfigurovaný pro ověřování Azure služba AD DS. Pokud je v účtu úložiště povolené Azure Files Azure služba AD DS Authentication, musí být před změnou na použití v místním služba AD DS zakázané. To znamená, že existující seznamy ACL, které jsou nakonfigurované v prostředí Azure služba AD DS, bude nutné překonfigurovat pro správné vynucení oprávnění.


    Pokud dochází k potížím s připojením k souborům Azure, přečtěte si [Nástroj pro řešení potíží, který jsme publikovali pro chyby připojení k souborům Azure ve Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Poskytujeme také [pokyny](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) pro řešení scénářů při zablokování portu 445. 


- Před povolením a konfigurací služba AD DS ověřování ke sdíleným složkám souborů Azure proveďte veškeré relevantní síťové konfigurace. Další informace najdete v tématu věnovaném [důležitým informacím o sítích Azure Files](storage-files-networking-overview.md) .

## <a name="regional-availability"></a>Regionální dostupnost

Ověřování pomocí služby soubory Azure s služba AD DS je dostupné ve [všech oblastech veřejné a gov Azure](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Přehled

Pokud máte v plánu povolit pro sdílenou složku všechny síťové konfigurace, doporučujeme, abyste si přečetli článek s [důležitými informacemi o sítích](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) a dokončili související konfiguraci, než povolíte služba AD DS ověřování.

Povolením ověřování služba AD DS pro sdílené složky Azure vám umožníte ověřit sdílené složky Azure pomocí vašich přihlašovacích údajů služba AD DS on-Prem. Dále vám umožní lépe spravovat vaše oprávnění, aby bylo možné podrobnější řízení přístupu. To vyžaduje synchronizaci identit z Prem služba AD DS do služby Azure AD pomocí služby AD Connect. Přístup na úrovni sdílené složky s identitami synchronizovanými v Azure AD se řídí při správě přístupu na úrovni souborů nebo sdílení pomocí Prem služba AD DS přihlašovacích údajů.

Potom postupujte podle následujících kroků a nastavte soubory Azure pro ověřování služba AD DS: 

1. [Část 1: povolení ověřování služba AD DS v účtu úložiště](storage-files-identity-ad-ds-enable.md)

1. [Druhá část: přiřazení oprávnění k přístupu ke sdílené složce identitě služby Azure AD (uživatel, skupina nebo instanční objekt), který je synchronizovaný s cílovou identitou AD](storage-files-identity-ad-ds-assign-permissions.md)

1. [Část 3: konfigurace seznamů řízení přístupu (ACL) systému Windows přes protokol SMB pro adresáře a soubory](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [4. část: připojení sdílené složky Azure k VIRTUÁLNÍmu počítači připojenému k vašemu služba AD DS](storage-files-identity-ad-ds-mount-file-share.md)

1. [Aktualizujte heslo identity účtu úložiště v služba AD DS](storage-files-identity-ad-ds-update-password.md)

Následující diagram znázorňuje kompletní pracovní postup pro povolení ověřování Azure AD přes protokol SMB pro sdílené složky Azure. 

![Diagram souborů pracovního postupu služby AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Identity používané pro přístup ke sdíleným složkám Azure musí být synchronizované do Azure AD, aby bylo možné vyhodnotit oprávnění k souborům na úrovni sdílené složky prostřednictvím modelu [řízení přístupu založeného na rolích Azure (Azure RBAC)](../../role-based-access-control/overview.md) . Pro soubory nebo adresáře přenesené z existujících souborových serverů se zachovají a vynutily [volitelné seznamy stylů Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) . To nabízí bezproblémovou integraci s podnikovým služba AD DSm prostředím. Při nahrazení Prem souborové servery pomocí sdílených složek Azure mají stávající uživatelé přístup ke sdíleným složkám Azure ze svých současných klientů s jednotným přihlašováním bez jakýchkoli změn v přihlašovacích údajích, které se používají.  

## <a name="next-steps"></a>Další kroky

Pokud chcete pro sdílenou složku Azure povolit místní služba AD DS ověřování, pokračujte k dalšímu článku:

[Část 1: povolení služba AD DS ověřování pro váš účet](storage-files-identity-ad-ds-enable.md)
