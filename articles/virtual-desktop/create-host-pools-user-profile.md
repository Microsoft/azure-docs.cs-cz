---
title: Windows Virtual Desktop FSLogix kontejner kontejneru – Azure
description: Jak nastavit kontejner profilu FSLogix pro fond hostitelů virtuální plochy systému Windows pomocí sdílené složky založené na virtuálním počítači.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 96b593f544aa4bbf126c06747a01902581f5ffb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250917"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Vytvoření kontejneru profilů pro fond hostitelů s využitím sdílené složky

Služba Windows Virtual Desktop nabízí kontejnery profilů FSLogix jako doporučené řešení profilu uživatele. Nedoporučujeme používat řešení Disk profilu uživatele (UPD), které bude v budoucích verzích virtuální plochy windows zastaralé.

Tento článek vám řekne, jak nastavit sdílenou složku kontejneru profilu FSLogix pro fond hostitelů pomocí sdílené složky založené na virtuálním počítači. Další dokumentaci fslogix naleznete na [webu FSLogix](https://docs.fslogix.com/).

>[!NOTE]
>Pokud hledáte srovnávací materiál o různých možnostech úložiště kontejneru profilů FSLogix v Azure, [přečtěte si informace o možnostech úložiště pro kontejnery profilů FSLogix](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Vytvoření nového virtuálního počítače, který bude fungovat jako sdílená složka

Při vytváření virtuálního počítače jej nezapomeňte umístit do stejné virtuální sítě jako virtuální počítače fondu hostitelů nebo do virtuální sítě, která má připojení k virtuálním počítačům fondu hostitelů. Virtuální počítač můžete vytvořit několika způsoby:

- [Vytvoření virtuálního počítače z bitové kopie Galerie Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače ze spravované bitové kopie](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Vytvoření virtuálního počítače z nespravované bitové kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Po vytvoření virtuálního počítače se připojte k doméně tak, že uděláte následující věci:

1. [Připojte se k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) s přihlašovacími údaji, které jste zadali při vytváření virtuálního počítače.
2. Na virtuálním stroji spusťte **Ovládací panely** a vyberte **Systém**.
3. Vyberte **Název počítače**, vyberte **Změnit nastavení**a pak **vyberte Změnit...**
4. Vyberte **Doména** a zadejte doménu služby Active Directory ve virtuální síti.
5. Ověřte pomocí účtu domény, který má oprávnění k počítačům s připojením k doméně.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Příprava virtuálního počítače na funkci sdílené složky pro profily uživatelů

Následují obecné pokyny, jak připravit virtuální počítač tak, aby působil jako sdílená složka pro profily uživatelů:

1. Přidejte uživatele služby Active Directory služby Windows Virtual Desktop do [skupiny zabezpečení služby Active Directory](/windows/security/identity-protection/access-control/active-directory-security-groups/). Tato skupina zabezpečení bude použita k ověření uživatelů virtuální plochy systému Windows na virtuální mstovitá položku, kterou jste právě vytvořili.
2. [Připojte se k virtuálnímu počítači pro sdílení souborů](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Na virtuálním počítači pro sdílení souborů vytvořte složku na **jednotce C,** která bude použita jako sdílená složka profilu.
4. Klepněte pravým tlačítkem myši na novou složku, vyberte **Vlastnosti**, vyberte **Sdílení**a vyberte **možnost Rozšířené sdílení...**.
5. Vyberte **Sdílet tuto složku**, vyberte **Oprávnění...** a pak vyberte **Přidat...**.
6. Vyhledejte skupinu zabezpečení, do které jste přidali uživatele virtuální plochy systému Windows, a ujistěte se, že tato skupina má **úplné řízení**.
7. Po přidání skupiny zabezpečení klepněte pravým tlačítkem myši na složku, vyberte **Vlastnosti**, vyberte **sdílení**a zkopírujte **síťovou cestu,** kterou chcete použít na později.

Další informace o oprávněních naleznete v [dokumentaci fslogix](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurace kontejneru profilu FSLogix

Chcete-li nakonfigurovat virtuální počítače pomocí softwaru FSLogix, postupujte takto na každém počítači registrovaném do fondu hostitelů:

1. [Připojte se k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) s přihlašovacími údaji, které jste zadali při vytváření virtuálního počítače.
2. Spusťte internetový prohlížeč a přejděte na [tento odkaz](https://go.microsoft.com/fwlink/?linkid=2084562) a stáhněte si agenta FSLogix.
3. Přejděte \\ \\do souboru \\ \\ZIP na\\win32\\nebo na verzi X64 a spusťte **aplikaci FSLogixAppsSetup** a nainstalujte agenta FSLogix.  Další informace o instalaci fslogixu najdete v [tématu Stažení a instalace fslogixu](/fslogix/install-ht/).
4. Přejděte na **programové soubory** > **FSLogix** > **Apps** a potvrďte nainstalovaného agenta.
5. V nabídce Start spusťte **RegEdit** jako správce. Přejděte na **software\\\\\\computer HKEY_LOCAL_MACHINE FSLogix**.
6. Vytvořte klíč s názvem **Profily**.
7. Vytvořte následující hodnoty pro klíč Profily:

| Name (Název)                | Typ               | Data/hodnota                        |
|---------------------|--------------------|-----------------------------------|
| Povoleno             | DWORD              | 1                                 |
| Umístění virtuálních pevných disků        | Hodnota více řetězců | "Síťová cesta pro sdílenou složku"     |

>[!IMPORTANT]
>Chcete-li zabezpečit prostředí Virtuální desktopwindows v Azure, doporučujeme neotevírat příchozí port 3389 na virtuálních počítačích. Virtuální plocha Windows nevyžaduje otevřený vstupní port 3389 pro přístup uživatelů k virtuálním počítačům hostitelského fondu. Pokud musíte otevřít port 3389 pro účely řešení potíží, doporučujeme použít [přístup k virtuálním ms just-in-time](../security-center/security-center-just-in-time.md).
