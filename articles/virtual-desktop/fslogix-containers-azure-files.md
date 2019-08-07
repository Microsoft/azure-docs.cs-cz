---
title: Kontejnery profilů FSLogix a soubory Azure na virtuálním počítači s Windows – Azure
description: Tento článek popisuje kontejnery profilů FSLogix v rámci virtuálních počítačů s Windows a souborů Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: helohr
ms.openlocfilehash: c01e138c8afcdd59fcb0c87f189d98bec10e16d7
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828144"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Kontejnery profilů FSLogix a soubory Azure

Služba Windows Virtual Desktop Preview doporučuje kontejnery profilů FSLogix jako řešení uživatelských profilů. FSLogix je navržená pro roaming profilů ve vzdálených výpočetních prostředích, jako je například virtuální počítač s Windows. Ukládá kompletní profil uživatele do jediného kontejneru. Při přihlášení se tento kontejner dynamicky připojí k výpočetnímu prostředí pomocí nativně podporovaného virtuálního pevného disku (VHD) a virtuálního pevného disku Hyper-V (VHDX). Profil uživatele je hned dostupný a zobrazí se v systému přesně jako nativní profil uživatele.

V tomto článku popíšeme kontejnery profilů FSLogix používané se soubory Azure. Informace jsou v kontextu virtuálního klienta Windows, který byl [oznámen 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Profily uživatelů

Uživatelský profil obsahuje prvky dat o jednotlivých uživatelích, včetně informací o konfiguraci, jako je nastavení plochy, trvalá síťová připojení a nastavení aplikace. Ve výchozím nastavení vytvoří Windows místní profil uživatele, který je úzce integrovaný s operačním systémem.

Profil vzdáleného uživatele poskytuje oddíl mezi uživatelskými daty a operačním systémem. Umožňuje nahradit nebo změnit operační systém, aniž by to ovlivnilo uživatelská data. V Hostitel relace vzdálené plochy (VZDÁLENÉm počítači a v infrastruktuře virtuálních klientských počítačů (VDI) může být operační systém nahrazen z následujících důvodů:

- Upgrade operačního systému
- Nahrazení existujícího virtuálního počítače (VM)
- Uživatel je součástí prostředí vzdálené plochy (non-persistent) s fondem nebo VDI.

Produkty Microsoftu pracují s několika technologiemi pro vzdálené profily uživatelů, včetně těchto technologií:
- Cestovní profily uživatelů (RUP)
- Disky uživatelských profilů (UPD)
- Roaming podnikového stavu (ESR)

UPD a RUP jsou nejčastěji používané technologie pro profily uživatelů v prostředích Hostitel relace vzdálené plochy (autovzdáleném vzdáleném pevném disku) a virtuálních pevných discích (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Problémy s předchozími technologiemi profilů uživatelů

Stávající a starší řešení Microsoftu pro profily uživatelů se dodává s různými problémy. Žádné předchozí řešení nezpracovalo všechny potřeby uživatelských profilů, které jsou součástí prostředí vzdálené plochy (hostitele) nebo VDI. UPD například nemůže zpracovávat velké soubory OST a RUP neuchovává moderní nastavení.

#### <a name="functionality"></a>Funkce

V následující tabulce jsou uvedeny výhody a omezení pro předchozí technologie profilů uživatelů.

| Technologie | Moderní nastavení | Nastavení Win32 | Nastavení operačního systému | Uživatelská data | Podporováno na SKU serveru | Back-endové úložiště v Azure | Back-endové úložiště v místním prostředí | Podpora verzí | Další čas přihlášení |Poznámky|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Disky uživatelských profilů (UPD)** | Ano | Ano | Ano | Ano | Ano | Ne | Ano | Win 7 + | Ano | |
| **Cestovní profil uživatele (RUP), režim údržby** | Ne | Ano | Ano | Ano | Ano| Ne | Ano | Win 7 + | Ne | |
| **Enterprise State Roaming (ESR)** | Ano | Ne | Ano | Ne | Zobrazit poznámky | Ano | Ne | Win 10 | Ne | Funkce na SKU serveru, ale žádné podpůrné uživatelské rozhraní |
| **Virtualizace uživatelského prostředí (UE-V)** | Ano | Ano | Ano | Ne | Ano | Ne | Ano | Win 7 + | Ne |  |
| **Cloudové soubory OneDrive** | Ne | Ne | Ne | Ano | Zobrazit poznámky | Zobrazit poznámky  | Zobrazit poznámky | Win 10 RS3 | Ne | Není testováno na SKU serveru. Back-endové úložiště v Azure závisí na synchronizaci klienta. Back-endové úložiště on-Prem potřebuje synchronizačního klienta. |

#### <a name="performance"></a>Výkon

UPD vyžaduje [prostory úložiště s přímým přístupem (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) k řešení požadavků na výkon. UPD používá protokol SMB (Server Message Block). Zkopíruje profil do virtuálního počítače, ve kterém je uživatel zaznamenáván. UPD s S2D bylo řešení, které tým RDS doporučuje pro virtuální plochu Windows ve verzi Preview služby.  

#### <a name="cost"></a>Náklady

I když clustery S2D dosahují nezbytného výkonu, jsou pro podnikové zákazníky nákladné, ale zvláště nákladné pro zákazníky s malým a středním firmou (SMB). Pro toto řešení podniky platíte za disky úložiště společně s náklady na virtuální počítače, které používají disky pro sdílenou složku.

#### <a name="administrative-overhead"></a>Administrativní režijní náklady

Clustery S2D vyžadují operační systém, který se opraví, aktualizuje a udržuje v zabezpečeném stavu. Tyto procesy a složitost nastavení zotavení po havárii S2D umožňují, aby se S2D mohla uskutečnit jenom pro podniky s vyhrazeným personálem IT.

## <a name="fslogix-profile-containers"></a>Kontejnery profilů FSLogix

19. listopadu 2018 [Společnost Microsoft získala FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix řeší mnoho výzev k kontejneru profilů. Mezi ně patří klíč:

- **Předepsané** [Kontejnery profilu FSLogix](https://fslogix.com/products/profile-containers) mají vysoký výkon a řeší problémy s výkonem, které mají historicky blokovaný režim výměny do mezipaměti.
- **OneDrivem** OneDrive pro firmy se bez kontejnerů profilů FSLogix nepodporuje v prostředích netrvalých virtuálních počítačů a virtuálních klientských počítačů (VDI). [Nejlepší postupy pro OneDrive pro firmy a FSLogix](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) popisují jejich interakci. Další informace najdete v tématu [použití synchronizačního klienta na virtuálních plochách](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Další složky:** FSLogix poskytuje možnost rozšiřování uživatelských profilů, aby zahrnovaly další složky.

Od pořízení společnost Microsoft začala nahrazovat existující řešení uživatelských profilů, jako je UPD, pomocí kontejnerů profilů FSLogix.

## <a name="azure-files-integration-with-azure-active-directory"></a>Integrace souborů Azure s Azure Active Directory

Výkon a funkce kontejnerů profilů FSLogix využívají cloud. V září. 24 2018 Microsoft Azure soubory oznámily veřejnou verzi Preview [souborů Azure, které podporují Azure Active Directory ověřování](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). Díky zabezpečení nákladů i administrativních režijních nákladů je Azure Files s ověřováním v Azure Active Directory řešení Premium pro profily uživatelů v nové službě Virtual Desktop systému Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Osvědčené postupy pro virtuální počítače s Windows

Virtuální plocha Windows nabízí plnou kontrolu velikosti, typu a počtu virtuálních počítačů, které používají zákazníci. Další informace najdete v tématu [co je verze Preview pro virtuální počítače s Windows?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Aby se zajistilo, že prostředí virtuálních počítačů s Windows dodržuje osvědčené postupy:

- Účet úložiště Azure Files musí být ve stejné oblasti jako virtuální počítače hostitele relace.
- Oprávnění k souborům Azure by měla odpovídat oprávněním popsaným v [kontejnerech profil požadavků](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Každý fond hostitelů musí být sestaven se stejným typem a velikostí virtuálního počítače na základě stejné hlavní bitové kopie.
- Každý virtuální počítač fondu hostitelů musí být ve stejné skupině prostředků, aby se mohla spravovat podpora, škálování a aktualizace.
- Pro zajištění optimálního výkonu by se řešení úložiště a kontejner profilu FSLogix měly nacházet ve stejném umístění datového centra.
- Účet úložiště, který obsahuje hlavní bitovou kopii, musí být ve stejné oblasti a předplatném, ve kterém jsou virtuální počítače zřízené.

## <a name="next-steps"></a>Další kroky

Při nastavování prostředí virtuálních počítačů s Windows použijte následující pokyny.

- Pokud chcete začít sestavovat vaše řešení virtualizace plochy, přečtěte si téma [Vytvoření tenanta ve virtuální ploše Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- Pokud chcete vytvořit fond hostitelů v rámci tenanta virtuálních klientů Windows, přečtěte si téma [Vytvoření fondu hostitelů pomocí Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- Pokud chcete nastavit plně spravované sdílené složky v cloudu, přečtěte si téma [Nastavení sdílené složky Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- Pokud chcete nakonfigurovat kontejnery profilů FSLogix, přečtěte si téma [Nastavení sdílené složky profilu uživatele pro fond hostitelů](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Pokud chcete přiřadit uživatele k fondu hostitelů, přečtěte si téma [Správa skupin aplikací pro virtuální počítač s Windows](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- Informace o přístupu k prostředkům virtuálního počítače s Windows z webového prohlížeče najdete v tématu [připojení k virtuálnímu](https://docs.microsoft.com/azure/virtual-desktop/connect-web)počítači s Windows.
