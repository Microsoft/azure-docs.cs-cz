---
title: Sdílená složka kontejneru profilu FSLogix virtuálního počítače s Windows – Azure
description: Postup nastavení kontejneru profilu FSLogix pro fond hostitelů virtuálních klientů s Windows pomocí sdílené složky na základě virtuálního počítače.
author: Heidilohr
ms.topic: how-to
ms.date: 08/20/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 35b692033cc16f276b48bc6d973b27d994c1082a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88002584"
---
# <a name="create-a-profile-container-for-a-host-pool-using-a-file-share"></a>Vytvoření kontejneru profilů pro fond hostitelů s využitím sdílené složky

Služba Virtual Desktop systému Windows nabízí kontejnery profilů FSLogix jako doporučené řešení profilu uživatele. Nedoporučujeme používat řešení diskového profilu uživatele (UPD), které se v budoucích verzích virtuálního klienta Windows už nepoužívá.

V tomto článku se dozvíte, jak nastavit sdílení kontejneru profilu FSLogix pro fond hostitelů pomocí sdílené složky založené na virtuálním počítači. Důrazně doporučujeme místo sdílených složek používat soubory Azure. Další dokumentaci k FSLogix najdete na [webu FSLogix](https://docs.fslogix.com/).

>[!NOTE]
>Pokud hledáte srovnávací materiál o různých možnostech úložiště kontejneru FSLogix v Azure, přečtěte si téma [Možnosti úložiště pro kontejnery profilů FSLogix](store-fslogix-profile.md).

## <a name="create-a-new-virtual-machine-that-will-act-as-a-file-share"></a>Vytvořit nový virtuální počítač, který bude fungovat jako sdílená složka

Při vytváření virtuálního počítače ho Nezapomeňte umístit do stejné virtuální sítě jako virtuální počítače fondu hostitelů nebo do virtuální sítě, která má připojení k virtuálním počítačům fondu hostitelů. Virtuální počítač můžete vytvořit několika způsoby:

- [Vytvoření virtuálního počítače z Image Galerie Azure](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Vytvoření virtuálního počítače ze spravované image](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Vytvoření virtuálního počítače z nespravované image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Po vytvoření virtuálního počítače ho připojte k doméně tím, že provedete následující akce:

1. [Připojte se k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
2. Na virtuálním počítači spusťte **Ovládací panely** a vyberte možnost **systém**.
3. Vyberte **název počítače**, vyberte **změnit nastavení** a pak zvolte **změnit...**
4. Vyberte **doména** a pak zadejte doménu služby Active Directory ve virtuální síti.
5. Proveďte ověření pomocí doménového účtu, který má oprávnění k počítačům připojeným k doméně.

## <a name="prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles"></a>Příprava virtuálního počítače, který bude fungovat jako sdílená složka pro profily uživatelů

Níže najdete obecné pokyny k přípravě virtuálního počítače, který bude fungovat jako sdílená složka pro profily uživatelů:

1. Přidejte uživatele služby Active Directory systému Windows do [skupiny zabezpečení služby Active Directory](/windows/security/identity-protection/access-control/active-directory-security-groups/). Tato skupina zabezpečení se použije k ověření uživatelů virtuální plochy Windows na virtuálním počítači sdílené složky, který jste právě vytvořili.
2. [Připojte se k virtuálnímu počítači sdílené složky](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine).
3. Na virtuálním počítači sdílené složky vytvořte složku na **jednotce C** , která bude použita jako sdílená složka profilu.
4. Pravým tlačítkem myši klikněte na novou složku, vyberte **vlastnosti**, vyberte **sdílení** a pak vyberte **Rozšířené sdílení...**.
5. Vyberte **sdílet tuto složku**, vyberte **oprávnění...** a pak vyberte **Přidat...**.
6. Vyhledejte skupinu zabezpečení, do které jste přidali uživatele virtuální plochy Windows, a ujistěte se, že má skupina **oprávnění Úplné řízení**.
7. Po přidání skupiny zabezpečení klikněte pravým tlačítkem na složku, vyberte **vlastnosti**, vyberte **sdílení** a pak zkopírujte **síťovou cestu** , která se má použít pro pozdější použití.

Další informace o oprávněních najdete v [dokumentaci k FSLogix](/fslogix/fslogix-storage-config-ht/).

## <a name="configure-the-fslogix-profile-container"></a>Konfigurace kontejneru profilu FSLogix

Pokud chcete nakonfigurovat virtuální počítače s FSLogix softwarem, udělejte na každém počítači zaregistrovaném do fondu hostitelů následující:

1. [Připojte se k virtuálnímu počítači](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) pomocí přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače.
2. Spusťte internetový prohlížeč a přejděte na [Tento odkaz](https://go.microsoft.com/fwlink/?linkid=2084562) a Stáhněte si agenta FSLogix.
3. Přejděte na \\ \\ verzi Win32 \\ nebo \\ \\ x64 \\ v souboru. zip a spuštěním **FSLogixAppsSetup** nainstalujte agenta FSLogix.  Další informace o tom, jak nainstalovat FSLogix, najdete v tématu [Stažení a instalace FSLogix](/fslogix/install-ht/).
4. Přejděte na **Program Files**  >  **FSLogix**  >  **Apps** a potvrďte, že je agent nainstalovaný.
5. V nabídce Start spusťte program **Regedit** jako správce. Přejděte na **počítač \\ HKEY_LOCAL_MACHINE \\ software \\ FSLogix**.
6. Vytvořte klíč s názvem **Profiles**.
7. Pro klíč profilů vytvořte následující hodnoty:

| Název                | Typ               | Data/hodnota                        |
|---------------------|--------------------|-----------------------------------|
| Povoleno             | DWORD              | 1                                 |
| VHDLocations        | Hodnota s více řetězci | "Síťová cesta pro sdílenou složku"     |

>[!IMPORTANT]
>Pro lepší zabezpečení prostředí virtuálních počítačů s Windows v Azure doporučujeme na svých virtuálních počítačích neotevírat port 3389 pro příchozí spojení. Virtuální počítač s Windows nevyžaduje pro přístup k virtuálním počítačům fondu hostitelů otevřený příchozí port 3389. Pokud musíte pro účely řešení potíží otevřít port 3389, doporučujeme použít [přístup k virtuálnímu počítači za běhu](../security-center/security-center-just-in-time.md).
