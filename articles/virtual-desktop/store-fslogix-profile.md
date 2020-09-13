---
title: Kontejner profilu úložiště FSLogix virtuální plocha Windows – Azure
description: Možnosti ukládání profilu FSLogix virtuálního počítače s Windows na Azure Storage.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0b1a5e36232e74caa34037efbbb0da0c39051998
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89568689"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Možnosti úložiště pro kontejnery profilů FSLogix ve virtuálním počítači s Windows

Azure nabízí několik řešení úložiště, která můžete použít k uložení kontejneru profilu FSLogix. Tento článek porovnává řešení úložiště, které Azure nabízí pro kontejnery profilů uživatelů FSLogix virtuálních počítačů s Windows. Pro většinu našich zákazníků doporučujeme ukládat do souborů Azure kontejnery profilů FSLogix.

Virtuální počítač s Windows nabízí kontejnery profilů FSLogix jako doporučené řešení profilace uživatele. Řešení FSLogix je určené k přesouvání profilů ve vzdálených výpočetních prostředích, jako je Windows Virtual Desktop. Při přihlašování se tento kontejner dynamicky připojuje k výpočetnímu prostředí pomocí nativně podporovaného virtuálního pevného disku (VHD) a virtuálního pevného disku Hyper-V (VHDX). Profil uživatele je hned dostupný a zobrazí se v systému přesně jako nativní profil uživatele.

V následujících tabulkách jsou porovnávána řešení úložiště Azure Storage nabídky pro profily uživatelů kontejnerů profilů FSLogix virtuálních počítačů s Windows.

## <a name="azure-platform-details"></a>Podrobnosti o platformě Azure

|Funkce|Soubory Azure|Azure NetApp Files|Prostory úložiště – přímé|
|--------|-----------|------------------|---------------------|
|Případ použití|Obecné účely|Ultra Performance nebo migrace z místního NetAppu|Různé platformy|
|Služba platformy|Ano, řešení Azure v nativním režimu|Ano, řešení Azure v nativním režimu|Ne, samostatně spravovaná|
|Regionální dostupnost|Všechny oblasti|[Vybrat oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Všechny oblasti|
|Redundance|Místně redundantní/zóna – redundantní/geograficky redundantní|Místně redundantní|Místně redundantní/zóna – redundantní/geograficky redundantní|
|Úrovně a výkon|Standard<br>Premium<br>Až do maximálního počtu 100 tisíc IOPS na sdílenou složku s 5 GB/s na sdílenou jednotku v rozmezí 3 MS|Standard<br>Premium<br>Ultra<br>Až 320k (16 KB) IOPS s 4,5 GB/s na jeden svazek při asi 1 MS latence|HDD úrovně Standard: omezení až 500 vstupně-výstupních operací za sekundu na disk<br>SSD úrovně Standard: omezení až 4k IOPS na disk<br>SSD úrovně Premium: omezení až 20 tisíc IOPS na disk<br>Pro Prostory úložiště s přímým přístupem doporučujeme použít prémiové disky.|
|Kapacita|100 TiB na sdílenou složku|100 TiB na svazek, až 12,5 PiB na předplatné|Maximální 32 TiB na disk|
|Požadovaná infrastruktura|Minimální velikost sdílené složky 1 GiB|Minimální kapacita fondu 4 TiB, minimální velikost svazku 100 GiB|Dva virtuální počítače v Azure IaaS (+ disk s kopií cloudu) nebo minimálně tři virtuální počítače bez a s náklady na disky|
|Protokoly|SMB 2.1/3. a REST|NFSv3, NFSv 4.1 (Preview), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Podrobnosti o správě Azure

|Funkce|Soubory Azure|Azure NetApp Files|Prostory úložiště – přímé|
|--------|-----------|------------------|---------------------|
|Access|Cloud, místní a hybridní (Azure File Sync)|Cloud, místní (přes ExpressRoute)|Cloud, místní prostředí|
|Backup|Integrace snímků Azure Backup|Snímky Azure NetApp Files|Integrace snímků Azure Backup|
|Zabezpečení a dodržování předpisů|[Všechny podporované certifikáty Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|Dokončeno ISO|[Všechny podporované certifikáty Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integrace Azure Active Directory|[Nativní služba Active Directory a Azure Active Directory Domain Services](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Azure Active Directory Domain Services a nativní služba Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Nativní služba Active Directory nebo Azure Active Directory Domain Services pouze podpora|

Po výběru metody úložiště si Projděte [ceny pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/) , kde najdete informace o našich cenových plánech.

## <a name="next-steps"></a>Další kroky

Další informace o kontejnerech profilů FSLogix, discích profilů uživatelů a dalších technologiích profilů uživatelů najdete v tabulce v tématu [FSLogix Profiles Containers a Azure Files](fslogix-containers-azure-files.md).

Pokud jste připraveni vytvořit vlastní kontejnery profilu FSLogix, začněte s jedním z těchto kurzů:

- [Začínáme s kontejnery profilů FSLogix ve službě soubory Azure na virtuálním počítači s Windows](create-file-share.md)
- [Vytvoření kontejneru profilu FSLogix pro fond hostitelů pomocí souborů Azure NetApp](create-fslogix-profile-container.md)
- Pokyny v tématu [Nasazení souborového serveru se škálováním na více prostory úložiště s přímým přístupem uzlů pro UPD úložiště v Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) platí i v případě, že použijete kontejner profilu FSLogix místo disku profilu uživatele.

Můžete také začít od začátku a nastavit vlastní řešení pro virtuální počítače s Windows na stránce [vytvořit tenanta na virtuálním počítači s Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
