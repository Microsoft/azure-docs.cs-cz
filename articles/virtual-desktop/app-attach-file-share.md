---
title: Virtuální plocha Windows – nastavit sdílenou složku MSIX připojit k aplikaci – Azure
description: Jak nastavit sdílenou složku pro připojení aplikace MSIX k virtuálnímu klientovi Windows
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a2d4ebee02d85d10d5db8ec2de0bb1be334770dc
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717652"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>Nastavení sdílení souborů pro připojení aplikace MSIX

Všechny Image MSIX musí být uložené ve sdílené síťové složce, ke kterým můžou získat přístup uživatelé ve fondu hostitelů s oprávněním jen pro čtení.

Připojení aplikace MSIX nemá žádné závislosti na typu prostředků infrastruktury úložiště, které sdílená složka používá. Požadavky na připojení ke sdílené složce aplikace MSIX jsou stejné jako u sdílené složky FSLogix. Další informace o požadavcích na úložiště najdete v tématu [Možnosti úložiště pro kontejnery profilů FSLogix ve virtuálním počítači s Windows](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Požadavky na výkon

Aplikace MSIX připojovat omezení velikosti obrázků pro váš systém závisí na typu úložiště, který používáte k ukládání souborů VHD nebo VHDx, a také omezení velikosti souborů VHD, VHSD nebo CIM a systému souborů.

V následující tabulce je uveden příklad toho, kolik prostředků jedna bitová kopie MSIX s jednou aplikací MSIX v ní musí být pro každý virtuální počítač:

| Prostředek             | Požadavky |
|----------------------|--------------|
| Ustálený stav IOPs    | 1 IOPs       |
| Přihlášení počítače při spuštění | 10 IOPs      |
| Latence              | 400 MS       |

Požadavky se můžou výrazně lišit v závislosti na tom, kolik MSIXch aplikací je uložených v imagi MSIX. U větších imagí MSIX budete muset přidělit větší šířku pásma.

### <a name="storage-recommendations"></a>Doporučení pro úložiště

Azure nabízí více možností úložiště, které se dají použít pro připojení aplikace MISX. Doporučujeme používat soubory Azure nebo Azure NetApp Files, protože tyto možnosti nabízejí nejlepší hodnotu mezi náklady a režijními náklady na správu. Článek [Možnosti úložiště pro kontejnery profilů FSLogix ve virtuálním počítači s Windows](store-fslogix-profile.md) porovnává různá spravovaná řešení úložiště Azure nabízí v kontextu virtuálního klienta Windows.

### <a name="optimize-msix-app-attach-performance"></a>Optimalizace výkonu připojení aplikace MSIX

Tady je několik dalších věcí, které doporučujeme pro optimalizaci výkonu připojení aplikace MSIX:

- Řešení úložiště, které používáte pro připojení aplikace MSIX, by mělo být ve stejném umístění datacentra jako hostitelé relace.
- Aby nedocházelo k problémům s výkonem, vylučte z kontroly antivirového programu následující soubory VHD, VHDX a CIM:
   
    - <MSIXAppAttachFileShare \> \* . VIRTUÁLNÍHO
    - <MSIXAppAttachFileShare \> \* . DISKU
    - \\\\storageaccount.file.core.windows.net \\ sdílená složka \* \* . VIRTUÁLNÍHO
    - \\\\storageaccount.file.core.windows.net \\ sdílená složka \* \* . DISKU
    - <MSIXAppAttachFileShare>. CIM
    - \\\\storageaccount.file.core.windows.net \\ sdílená složka \* \* . CIM

- Oddělte prostředky prostředků infrastruktury úložiště pro připojení aplikace MSIX z kontejnerů profilů FSLogix.
- Všechny účty systému virtuálních počítačů a uživatelské účty musí mít oprávnění jen pro čtení pro přístup ke sdílené složce.
- Všechny plány zotavení po havárii pro virtuální počítače s Windows musí zahrnovat replikaci sdílené složky aplikace MSIX v sekundárním umístění pro převzetí služeb při selhání. Další informace o zotavení po havárii najdete v tématu [nastavení provozní kontinuity a plán zotavení po havárii](disaster-recovery.md).

## <a name="how-to-set-up-the-file-share"></a>Postup nastavení sdílení souborů

Proces instalace aplikace MSIX připojit sdílenou složku je převážně stejný jako [proces instalace pro sdílené složky profilu FSLogix](create-host-pools-user-profile.md). Budete ale muset přiřadit uživatele různá oprávnění. Připojení aplikace MSIX vyžaduje oprávnění jen pro čtení pro přístup ke sdílené složce souborů.

Pokud vaše aplikace MSIX ukládáte do služby soubory Azure, budete muset pro hostitele vaší relace přiřadit všechny virtuální počítače hostitele relací jak řízení přístupu na základě role (RBAC), tak i sdílet soubory, které mají ve sdílené složce oprávnění systému souborů NTFS.

| Objekt Azure                      | Požadovaná role                                     | Funkce role                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Hostitel relace (objekty počítačů virtuálních počítačů)| Přispěvatel sdílené složky SMB dat souboru úložiště          | Číst a spouštět, číst, zobrazovat obsah složky  |
| Správci ve sdílené souborové složce              | Přispěvatel sdílené složky SMB dat souboru úložiště s vyššími oprávněními | Full control                                  |
| Uživatelé ve sdílené složce               | Přispěvatel sdílené složky SMB dat souboru úložiště          | Číst a spouštět, číst, zobrazovat obsah složky  |

Přiřazení oprávnění k virtuálním počítačům hostitele relace pro účet úložiště a sdílení souborů:

1. Vytvořte skupinu zabezpečení Active Directory Domain Services (služba AD DS).

2. Přidejte účty počítače pro všechny virtuální počítače hostitele relace jako členy skupiny.

3. Synchronizuje skupinu služba AD DS do Azure Active Directory (Azure AD).

4. Vytvoření účtu úložiště

5. Podle pokynů v části [Vytvoření sdílené složky Azure](../storage/files/storage-how-to-create-file-share.md#create-a-file-share)vytvořte v účtu úložiště sdílenou složku.

6. Připojte účet úložiště k služba AD DS podle pokynů v [části One: povolení služba AD DS ověřování sdílených složek Azure](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Přiřaďte synchronizovaný služba AD DS skupinu ke službě Azure AD a přiřaďte účet úložiště k roli Přispěvatel sdílené složky SMB pro data souborů úložiště.

8. Připojte sdílenou složku ke všem hostitelům relace podle pokynů uvedených v [části druhá část: přiřazení oprávnění na úrovni sdílení k identitě](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Udělte ke sdílené složce oprávnění NTFS pro skupinu služba AD DS.

10. Nastavte oprávnění systému souborů NTFS pro uživatelské účty. Budete potřebovat provozní jednotku (OU), která je zdrojem z služba AD DS, do které patří účty virtuálních počítačů.

Po přiřazení identity k úložišti postupujte podle pokynů v článcích v části [Další kroky](#next-steps) a udělte k identitě, kterou jste přiřadili k virtuálním počítačům, další požadovaná oprávnění.

Budete taky muset zajistit, aby virtuální počítače hostitele relace měly oprávnění k novému systému souborů NTFS (Technology File System). Musíte mít kontejner operační jednotky, který se nakonfiguruje z Active Directory Domain Services (služba AD DS), a vaši uživatelé musí být členy této provozní jednotky, aby mohli tato oprávnění použít.

## <a name="next-steps"></a>Další kroky

Tady jsou další věci, které budete muset udělat po nastavení sdílené složky:

- Přečtěte si, jak nastavit Azure Active Directory Domain Services (služba AD DS) v tématu [vytvoření kontejneru profilu se soubory Azure a služba AD DS](create-file-share.md).
- Naučte se, jak nastavit soubory Azure a Azure služba AD DS v tématu [vytvoření kontejneru profilu se soubory Azure a azure služba AD DS](create-profile-container-adds.md).
- Přečtěte si, jak nastavit Azure NetApp Files pro připojení aplikace MSIX v tématu [vytvoření kontejneru profilu s Azure NetApp Files a služba AD DS](create-fslogix-profile-container.md).
- Naučte se používat sdílenou složku založenou na virtuálním počítači v tématu [vytvoření kontejneru profilů pro fond hostitelů s použitím sdílené složky](create-host-pools-user-profile.md).

Až budete hotovi, můžete najít další zdroje informací, které mohou být užitečné:

- Zeptejte se naší komunity na tuto funkci na [virtuálním počítači s Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- Zpětnou vazbu k virtuálnímu počítači s Windows můžete také opustit v [centru pro zpětnou vazbu na virtuálním počítači s Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [Glosář připojení aplikace MSIX](app-attach-glossary.md)
- [DOTAZY k připojení aplikace MSIX](app-attach-faq.md)
