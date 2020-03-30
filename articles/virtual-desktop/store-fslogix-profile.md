---
title: Kontejner profilu úložiště FSLogix Windows Virtual Desktop – Azure
description: Možnosti ukládání profilu Windows Virtual Desktop FSLogix ve službě Azure Storage.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 71ba24784dee7771acbe19bf0261c7dc02478b24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127514"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Možnosti úložiště pro kontejnery profilů FSLogix ve Windows Virtual Desktop

Azure nabízí několik řešení úložiště, která můžete použít k uložení kontejneru profilu FSLogix. Tento článek porovnává řešení úložiště, která Azure nabízí pro kontejnery profilů profilu uživatele Windows Virtual Desktop FSLogix.

Windows Virtual Desktop nabízí kontejnery profilu FSLogix jako doporučené řešení profilu uživatele. FSLogix je určen k přechytování profilů ve vzdálených výpočetních prostředích, jako je například virtuální plocha systému Windows. Při přihlášení je tento kontejner dynamicky připojen k výpočetnímu prostředí pomocí nativně podporovaného virtuálního pevného disku (VHD) a virtuálního pevného disku Hyper-V (VHDX). Profil uživatele je okamžitě k dispozici a zobrazí se v systému přesně jako nativní profil uživatele.

Následující tabulky porovnávají úložiště řešení, která Azure Storage nabízí pro profily kontejneru profilů profilů Windows Virtual Desktop FSLogix.

## <a name="azure-platform-details"></a>Podrobnosti o platformě Azure

|Funkce|Soubory Azure|Azure NetApp Files|Prostory úložiště – přímé|
|--------|-----------|------------------|---------------------|
|Případ použití|Obecné účely|Ultra výkon nebo migrace z netappu v místním prostředí|Různé platformy|
|Služba platformy|Ano, nativní řešení Azure|Ano, nativní řešení Azure|Ne, samoobslužná|
|Regionální dostupnost|Všechny regiony|[Vybrat oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Všechny regiony|
|Redundance|Místně redundantní/zónově redundantní/geograficky redundantní|Místně redundantní|Místně redundantní/zónově redundantní/geograficky redundantní|
|Úrovně a výkon|Standard<br>Premium<br>Až maximálně 100 kS Na akcii s 5 BPBps na akcii při latenci asi 3 ms|Standard<br>Premium<br>Ultra<br>Až 320 k (16 k) IOPS s 4,5 BPS na svazek při latenci přibližně 1 ms|Standardní hdd: až 500 IOPS na disk limity<br>Standardní SSD: až 4k IOPS na disk limity<br>Premium SSD: až 20k SOPS na disk limity<br>Doporučujeme disky Premium pro prostory úložiště Direct|
|Kapacita|100 TiB na akcii|100 TiB na svazek, až 12,5 PiB na předplatné|Maximálně 32 TiB na disk|
|Požadovaná infrastruktura|Minimální velikost sdílené složky 1 GiB|Bazén s minimální kapacitou 4 TiB, min objem 100 GiB|Dva virtuální počítače na Azure IaaS (+ Cloud Witness) nebo alespoň tři virtuální počítače bez a náklady na disky|
|Protokoly|SMB 2.1/3. a REST|NFSv3, NFSv4.1 (náhled), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Podrobnosti o správě Azure

|Funkce|Soubory Azure|Azure NetApp Files|Prostory úložiště – přímé|
|--------|-----------|------------------|---------------------|
|Access|Cloud, místní a hybridní (synchronizace souborů Azure)|Cloud, místní (přes ExpressRoute)|Cloud, místní|
|Zálohování|Integrace snímků zálohování Azure|Snímky souborů Azure NetApp|Integrace snímků zálohování Azure|
|Zabezpečení a dodržování předpisů|[Všechny podporované certifikáty Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO dokončeno|[Všechny podporované certifikáty Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integrace Azure Active Directory|[Nativní služba Active Directory a služba Azure Active Directory Domain Services](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Služby Azure Active Directory Domain Services a nativní služba Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Nativní podpora služby Active Directory nebo služby Azure Active Directory Domain Services|

Jakmile si vyberete metodu úložiště, podívejte se na [ceny Windows Virtual Desktop,](https://azure.microsoft.com/pricing/details/virtual-desktop/) kde najdete informace o našich cenových plánech.

## <a name="next-steps"></a>Další kroky

Další informace o kontejnerech profilů FSLogix, discích profilu uživatele a dalších technologiích profilů uživatelů najdete v tabulce v [kontejnerech profilů FSLogix a v souborech Azure](fslogix-containers-azure-files.md).

Pokud jste připraveni vytvořit si vlastní kontejnery profilu FSLogix, můžete začít s jedním z těchto kurzů:

- [Začínáme s kontejnery profilů FSLogix v souborech Azure ve Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Vytvoření kontejneru profilu FSLogix pro fond hostitelů pomocí souborů Azure NetApp](create-fslogix-profile-container.md)
- Pokyny v [nasazení dvounohého úložného prostoru Přímé škálovatkapacity souborový server pro úložiště UPD v Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) platí také při použití kontejneru profilu FSLogix namísto disku profilu uživatele

Můžete také začít od samého začátku a nastavit vlastní řešení Windows Virtual Desktop na [Vytvoření klienta v aplikaci Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
