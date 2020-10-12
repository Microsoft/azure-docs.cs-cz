---
title: Soubory kontejnerů profilů FSLogix virtuálních počítačů s Windows – Azure
description: Tento článek popisuje kontejnery profilů FSLogix v rámci virtuálních počítačů s Windows a souborů Azure.
author: Heidilohr
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 669f4baa723b78b8933f3a75fc361c468f9e2df9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88002399"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Kontejnery profilů FSLogix a soubory Azure

Služba Virtual Desktop systému Windows doporučuje kontejnery profilů FSLogix jako řešení uživatelského profilu. Řešení FSLogix je určené k přesouvání profilů ve vzdálených výpočetních prostředích, jako je Windows Virtual Desktop. Kompletní profily uživatelů ukládá do jednotlivých kontejnerů. Při přihlášení se tento kontejner dynamicky připojí k výpočetnímu prostředí pomocí nativně podporovaného virtuálního pevného disku (VHD) a virtuálního pevného disku Hyper-V (VHDX). Profil uživatele je hned k dispozici a objeví se v systému úplně stejně jako nativní profil uživatele. Tento článek popisuje, jak se ve virtuálním počítači s Windows používají kontejnery profilu FSLogix se soubory Azure.

>[!NOTE]
>Pokud hledáte srovnávací materiál o různých možnostech úložiště kontejneru FSLogix v Azure, přečtěte si téma [Možnosti úložiště pro kontejnery profilů FSLogix](store-fslogix-profile.md).

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
| **Disky uživatelských profilů (UPD)** | Yes | Yes | Yes | Yes | Yes | No | Yes | Win 7 + | Yes | |
| **Cestovní profil uživatele (RUP), režim údržby** | No | Yes | Yes | Yes | Yes| No | Yes | Win 7 + | No | |
| **Enterprise State Roaming (ESR)** | Yes | No | Yes | No | Zobrazit poznámky | Yes | No | Win 10 | No | Funkce na SKU serveru, ale žádné podpůrné uživatelské rozhraní |
| **Virtualizace uživatelského prostředí (UE-V)** | Yes | Yes | Yes | No | Yes | No | Yes | Win 7 + | No |  |
| **Cloudové soubory OneDrive** | No | No | No | Yes | Zobrazit poznámky | Zobrazit poznámky  | Zobrazit poznámky | Win 10 RS3 | No | Není testováno na SKU serveru. Back-endové úložiště v Azure závisí na synchronizaci klienta. Back-endové úložiště on-Prem potřebuje synchronizačního klienta. |

#### <a name="performance"></a>Výkon

UPD vyžaduje [prostory úložiště s přímým přístupem (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) k řešení požadavků na výkon. UPD používá protokol SMB (Server Message Block). Zkopíruje profil do virtuálního počítače, ve kterém je uživatel zaznamenáván. UPD s S2D je řešení, které doporučujeme pro virtuální počítače s Windows.

#### <a name="cost"></a>Náklady

I když clustery S2D dosahují nezbytného výkonu, jsou pro podnikové zákazníky nákladné, ale zvláště nákladné pro zákazníky s malým a středním firmou (SMB). Pro toto řešení podniky platíte za disky úložiště společně s náklady na virtuální počítače, které používají disky pro sdílenou složku.

#### <a name="administrative-overhead"></a>Režijní náklady na správu

Clustery S2D vyžadují operační systém, který se opraví, aktualizuje a udržuje v zabezpečeném stavu. Tyto procesy a složitost nastavení zotavení po havárii S2D umožňují, aby se S2D mohla uskutečnit jenom pro podniky s vyhrazeným personálem IT.

## <a name="fslogix-profile-containers"></a>Kontejnery profilů FSLogix

19. listopadu 2018 [Společnost Microsoft získala FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix řeší mnoho výzev k kontejneru profilů. Mezi ně patří klíč:

- **Výkon:** [Kontejnery profilu FSLogix](/fslogix/configure-profile-container-tutorial/) mají vysoký výkon a řeší problémy s výkonem, které mají historicky blokovaný režim výměny do mezipaměti.
- **OneDrive:** OneDrive pro firmy se bez kontejnerů profilů FSLogix nepodporuje v prostředích netrvalých virtuálních počítačů a virtuálních klientských počítačů (VDI). [Nejlepší postupy pro OneDrive pro firmy a FSLogix](/fslogix/overview/) popisují jejich interakci. Další informace najdete v tématu [použití synchronizačního klienta na virtuálních plochách](/deployoffice/rds-onedrive-business-vdi/).
- **Další složky:** FSLogix poskytuje možnost rozšiřování uživatelských profilů, aby zahrnovaly další složky.

Od pořízení společnost Microsoft začala nahrazovat existující řešení uživatelských profilů, jako je UPD, pomocí kontejnerů profilů FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Integrace souborů Azure s doménovou službou Azure Active Directory

Výkon a funkce kontejnerů profilů FSLogix využívají cloud. Od 7. srpna 2019 Microsoft Azure soubory oznámily obecnou dostupnost [ověřování souborů Azure pomocí služby Azure Active Directory Domain Service (služba AD DS)](../storage/files/storage-files-active-directory-overview.md). Díky ověřování Azure se službou Azure služba AD DS ověřování je pro profily uživatelů ve službě Virtual Desktop systému Windows výhodnější řešení Azure Files.

## <a name="best-practices-for-windows-virtual-desktop"></a>Osvědčené postupy pro virtuální počítače s Windows

Virtuální plocha Windows nabízí plnou kontrolu velikosti, typu a počtu virtuálních počítačů, které používají zákazníci. Další informace najdete v tématu [co je to virtuální počítač s Windows?](overview.md).

Aby se zajistilo, že prostředí virtuálních počítačů s Windows dodržuje osvědčené postupy:

- Účet úložiště Azure Files musí být ve stejné oblasti jako virtuální počítače hostitele relace.
- Oprávnění k souborům Azure by měla odpovídat oprávněním popsaným v [kontejnerech profil požadavků](/fslogix/fslogix-storage-config-ht).
- Každý fond hostitelů musí být sestaven se stejným typem a velikostí virtuálního počítače na základě stejné hlavní bitové kopie.
- Každý virtuální počítač fondu hostitelů musí být ve stejné skupině prostředků, aby se mohla spravovat podpora, škálování a aktualizace.
- Pro zajištění optimálního výkonu by se řešení úložiště a kontejner profilu FSLogix měly nacházet ve stejném umístění datového centra.
- Účet úložiště, který obsahuje hlavní bitovou kopii, musí být ve stejné oblasti a předplatném, ve kterém jsou virtuální počítače zřízené.

## <a name="next-steps"></a>Další kroky

K nastavení prostředí virtuálních ploch Windows použijte následující příručky.

- Pokud chcete začít sestavovat vaše řešení virtualizace plochy, přečtěte si téma [Vytvoření tenanta ve virtuální ploše Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
- Pokud chcete vytvořit fond hostitelů v rámci tenanta virtuálních klientů Windows, přečtěte si téma [Vytvoření fondu hostitelů pomocí Azure Marketplace](create-host-pools-azure-marketplace.md).
- Pokud chcete nastavit plně spravované sdílené složky v cloudu, přečtěte si téma [Nastavení sdílené složky Azure Files](/azure/storage/files/storage-files-active-directory-enable/).
- Pokud chcete nakonfigurovat kontejnery profilů FSLogix, přečtěte si téma [vytvoření kontejneru profilu pro fond hostitelů pomocí sdílené složky](create-host-pools-user-profile.md).
- Pokud chcete přiřadit uživatele k fondu hostitelů, přečtěte si téma [Správa skupin aplikací pro virtuální počítač s Windows](manage-app-groups.md).
- Informace o přístupu k prostředkům virtuálního počítače s Windows z webového prohlížeče najdete v tématu [připojení k virtuálnímu](connect-web.md)počítači s Windows.
