---
title: Kontejner profilu úložiště FSLogix virtuální plocha Windows – Azure
description: Možnosti ukládání profilu FSLogix virtuálního počítače s Windows na Azure Storage.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 1ff8c645b1ad670f3824920d39aa0c6bf9783408
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445546"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Možnosti úložiště pro kontejnery profilů FSLogix ve virtuálním počítači s Windows

Azure nabízí několik řešení úložiště, která můžete použít k uložení kontejneru profilu FSLogix. Tento článek porovnává řešení úložiště, které Azure nabízí pro kontejnery profilů uživatelů FSLogix virtuálních počítačů s Windows. Pro většinu našich zákazníků doporučujeme ukládat do souborů Azure kontejnery profilů FSLogix.

Virtuální počítač s Windows nabízí kontejnery profilů FSLogix jako doporučené řešení profilace uživatele. Řešení FSLogix je určené k přesouvání profilů ve vzdálených výpočetních prostředích, jako je Windows Virtual Desktop. Při přihlašování se tento kontejner dynamicky připojuje k výpočetnímu prostředí pomocí nativně podporovaného virtuálního pevného disku (VHD) a virtuálního pevného disku Hyper-V (VHDX). Profil uživatele je hned k dispozici a objeví se v systému úplně stejně jako nativní profil uživatele.

V následujících tabulkách jsou porovnávána řešení úložiště Azure Storage nabídky pro profily uživatelů kontejnerů profilů FSLogix virtuálních počítačů s Windows.

## <a name="azure-platform-details"></a>Podrobnosti o platformě Azure

|Funkce|Azure Files|Azure NetApp Files|Prostory úložiště – přímé|
|--------|-----------|------------------|---------------------|
|Případ použití|Obecné účely|Ultra Performance nebo migrace z místního NetAppu|Různé platformy|
|Služba platformy|Ano, řešení Azure v nativním režimu|Ano, řešení Azure v nativním režimu|Ne, samostatně spravovaná|
|Regionální dostupnost|Všechny oblasti|[Vybrat oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Všechny oblasti|
|Redundance|Místně redundantní/Neredundantní/geograficky redundantní/Geografická zóna – redundantní|Místně redundantní|Místně redundantní/zóna – redundantní/geograficky redundantní|
|Úrovně a výkon| Standard (transakce optimalizované)<br>Premium<br>Až maximální počet 100 tisíc IOPS na sdílenou složku s 10 GB/s na sdílenou položku v rozmezí 3 MS|Standard<br>Premium<br>Ultra<br>Až 320k (16 KB) IOPS s 4,5 GB/s na jeden svazek při asi 1 MS latence|HDD úrovně Standard: omezení až 500 vstupně-výstupních operací za sekundu na disk<br>SSD úrovně Standard: omezení až 4k IOPS na disk<br>SSD úrovně Premium: omezení až 20 tisíc IOPS na disk<br>Pro Prostory úložiště s přímým přístupem doporučujeme použít prémiové disky.|
|Kapacita|100 TiB pro každou sdílenou složku až 5 PiB na účet pro obecné účely |100 TiB na svazek, až 12,5 PiB na předplatné|Maximální 32 TiB na disk|
|Požadovaná infrastruktura|Minimální velikost sdílené složky 1 GiB|Minimální kapacita fondu 4 TiB, minimální velikost svazku 100 GiB|Dva virtuální počítače v Azure IaaS (+ disk s kopií cloudu) nebo minimálně tři virtuální počítače bez a s náklady na disky|
|Protokoly|SMB 3.0/2.1, NFSv 4.1 (Preview), REST|NFSv3, NFSv 4.1 (Preview), SMB 3. x/2. x|NFSv3, NFSv 4.1, SMB 3,1|

## <a name="azure-management-details"></a>Podrobnosti o správě Azure

|Funkce|Azure Files|Azure NetApp Files|Prostory úložiště – přímé|
|--------|-----------|------------------|---------------------|
|Access|Cloud, místní a hybridní (Azure File Sync)|Cloud, místní (přes ExpressRoute)|Cloud, místní prostředí|
|Backup|Integrace snímků Azure Backup|Snímky Azure NetApp Files|Integrace snímků Azure Backup|
|Zabezpečení a dodržování předpisů|[Všechny podporované certifikáty Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|Dokončeno ISO|[Všechny podporované certifikáty Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integrace Azure Active Directory|[Nativní služba Active Directory a Azure Active Directory Domain Services](../storage/files/storage-files-active-directory-overview.md)|[Azure Active Directory Domain Services a nativní služba Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Nativní služba Active Directory nebo Azure Active Directory Domain Services pouze podpora|

Po výběru metody úložiště si Projděte [ceny pro virtuální počítače s Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/) , kde najdete informace o našich cenových plánech.

## <a name="azure-files-tiers"></a>Vrstvy souborů Azure

Soubory Azure nabízí dvě různé úrovně úložiště: Premium a Standard. Tyto úrovně vám umožní přizpůsobit výkon a náklady na sdílené složky, aby splňovaly požadavky vašeho scénáře.

- Soubory úrovně Premium jsou založené na jednotkách SSD (Solid-State Drive) a jsou nasazené v typu účtu úložiště souborů. Soubory úrovně Premium poskytují konzistentní vysoký výkon a nízkou latenci pro úlohy náročné na vstupní a výstupní (v/v). 

- Standardní sdílené složky se zálohují na jednotky pevného disku (HDD) a jsou nasazené v typu účtu úložiště pro obecné účely verze 2 (GPv2). Standardní sdílené složky poskytují spolehlivý výkon pro vstupně-výstupní úlohy, které jsou méně citlivé na variabilitu výkonu, jako jsou například sdílené složky pro obecné účely a prostředí pro vývoj a testování. Standardní sdílené složky jsou dostupné jenom v modelu fakturace s průběžnými platbami.

V následující tabulce jsou uvedená doporučení, která úroveň výkonu použít na základě vašich úloh. Tato doporučení vám pomůžou vybrat úroveň výkonu, která vyhovuje vašim cílům, rozpočtovým a regionálním hlediskům výkonu. Tato doporučení jsme na ukázkových scénářích z [typů úloh vzdálené plochy](/windows-server/remote/remote-desktop-services/remote-desktop-workloads)zavedli. 

| Typ úlohy | Doporučená vrstva souborů |
|--------|-----------|
| Světlá (méně než 200 uživatelů) | Standardní sdílené složky |
| Světlá (více než 200 uživatelů) | Soubory úrovně Premium nebo Standard s více sdílenými složkami |
|Střední|Soubory ke sdílení souborů úrovně Premium|
|Těžký|Soubory ke sdílení souborů úrovně Premium|
|Napájení|Soubory ke sdílení souborů úrovně Premium|

Další informace o výkonu služby soubory Azure najdete v tématu [sdílené složky a cíle pro škálování souborů](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets). Další informace o cenách najdete v tématu [ceny za Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Další kroky

Další informace o kontejnerech profilů FSLogix, discích profilů uživatelů a dalších technologiích profilů uživatelů najdete v tabulce v tématu [FSLogix Profiles Containers a Azure Files](fslogix-containers-azure-files.md).

Pokud jste připraveni vytvořit vlastní kontejnery profilu FSLogix, začněte s jedním z těchto kurzů:

- [Začínáme s kontejnery profilů FSLogix ve službě soubory Azure na virtuálním počítači s Windows](create-file-share.md)
- [Vytvoření kontejneru profilu FSLogix pro fond hostitelů pomocí souborů Azure NetApp](create-fslogix-profile-container.md)
- Pokyny v tématu [Nasazení souborového serveru se škálováním na více prostory úložiště s přímým přístupem uzlů pro UPD úložiště v Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) platí i v případě, že použijete kontejner profilu FSLogix místo disku profilu uživatele.

Můžete také začít od začátku a nastavit vlastní řešení pro virtuální počítače s Windows na stránce [vytvořit tenanta na virtuálním počítači s Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
