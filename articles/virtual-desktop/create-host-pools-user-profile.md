---
title: Nastavení sdílené složky profilu uživatele pro fond hostitele Windows Virtual Desktop Preview – Azure
description: Jak vytvořit kontejner FSLogix profil pro fond hostitele Windows virtuální plochy, ve verzi Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: c9c2ca2cc27c5fa757b8ff6846e0a6a8f7087875
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403710"
---
# <a name="set-up-a-user-profile-share-for-a-host-pool"></a>Nastavení sdílené složky profilu uživatele pro fond hostitele

Služba Windows virtuální plochy, ve verzi Preview nabízí FSLogix profilu kontejnery jako řešení doporučené uživatelských profilů. Nedoporučujeme ale používat řešení disku profilu uživatele (UPD) a bude vyřazena v budoucích verzích Windows virtuálního klienta.

V této části zjistíte, jak vytvořit sdílenou složku FSLogix profilu kontejner pro skupinu hostitelů.

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Vytvořit nový virtuální počítač, který bude sloužit jako sdílené složky

Při vytváření virtuálního počítače, je nutné ho umístit na buď stejné virtuální síti jako virtuální počítače fondu hostitele nebo virtuální síť, která se může připojit k hostování fondu virtuálních počítačů. Vytvoření virtuálního počítače v několika způsoby:

- [Vytvoření virtuálního počítače z image Galerie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Vytvoření virtuálního počítače ze spravované image](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Vytvoření virtuálního počítače z nespravované image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Po vytvoření virtuálního počítače, připojte ho k doméně provedením následujících akcí:

1. [Připojení k virtuálnímu počítači](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) s přihlašovacími údaji, které jste zadali při vytváření virtuálního počítače.
2. Na virtuálním počítači spusťte **ovládací panely** a vyberte **systému**.
3. Vyberte **název_počítače**vyberte **změnit nastavení**a pak vyberte **změn...**
4. Vyberte **domény** a pak zadejte doménu služby Active Directory ve virtuální síti.
5. Ověření pomocí účtu domény, který má oprávnění k připojení k doméně počítače.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Příprava virtuálního počítače tak, aby fungoval jako sdílené složky pro profily uživatelů

Následují obecné pokyny o tom, jak připravit virtuální počítač tak, aby fungoval jako sdílené složky pro uživatelské profily:

1. Připojte se k relaci hostitele virtuálních počítačů [skupiny zabezpečení Active Directory](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-security-groups). Tuto skupinu zabezpečení se použije k ověření relace hostitele virtuálních počítačů do souboru virtuálního počítače sdílenou složku, kterou jste právě vytvořili.
2. [Připojení k virtuálnímu počítači sdílené složky souboru](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine).
3. Na virtuálním počítači soubor sdílené složce vytvořte složku na **jednotce C** , který se použije jako sdílenou složku profilu.
4. Klikněte pravým tlačítkem na novou složku, vyberte **vlastnosti**vyberte **sdílení**a pak vyberte **rozšířené možnosti sdílení...** .
5. Vyberte **sdílet tuto složku**vyberte **oprávnění...** a pak vyberte **přidat...** .
6. Vyhledejte skupinu zabezpečení, ke které jste přidali relace hostování virtuálních počítačů a pak ověřte, zda má tuto skupinu **úplné řízení**.
7. Po přidání skupiny zabezpečení, klikněte pravým tlačítkem na složku, vyberte **vlastnosti**vyberte **sdílení**, zkopírujte dolů **síťová cesta** pro pozdější použití.

Doporučených postupech ohledně oprávnění naleznete na následujícím [FSLogix dokumentaci](https://support.fslogix.com/index.php/forum-main/faqs/84-best-practices#120).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurace kontejneru FSLogix profilu

Ke konfiguraci virtuálních počítačů se softwarem FSLogix, proveďte postup na každý počítač zaregistrovaný do fondu hostitele:

1. [Připojení k virtuálnímu počítači](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) s přihlašovacími údaji, které jste zadali při vytváření virtuálního počítače.
2. Spustit internetový prohlížeč a přejděte na následující [odkaz](https://go.microsoft.com/fwlink/?linkid=2084562) stažení FSLogix agenta. Jako součást verze preview pro veřejnost virtuální plochy Windows získáte licenční klíč k aktivaci FSLogix softwaru. Klíč je LicenseKey.txt soubor zahrnut do souboru .zip FSLogix agenta.
3. Nainstalujte agenta FSLogix.
4. Přejděte do **Program Files** > **FSLogix** > **aplikace** potvrďte instalaci agenta.
5. Z nabídky start spusťte **RegEdit** jako správce. Přejděte do **počítače\\HKEY_LOCAL_MACHINE\\softwaru\\FSLogix\\profily**
6. Vytvořte následující hodnoty:

| Název                | Type               | Hodnota/dat                        |
|---------------------|--------------------|-----------------------------------|
| Povoleno             | DWORD              | 1                                 |
| VHDLocations        | Víceřetězcová hodnota | "Síťovou cestu pro sdílenou složku" |
| VolumeType          | String             | VHDX                              |
| SizeInMBs           | DWORD              | "celočíselné velikosti profilu"     |
| IsDynamic           | DWORD              | 1                                 |
| LockedRetryCount    | DWORD              | 1                                 |
| LockedRetryInterval | DWORD              | 0                                 |
