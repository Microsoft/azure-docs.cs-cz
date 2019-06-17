---
title: Kontejnery FSLogix profilu a službou soubory Azure v virtuální plochy Windows – Azure
description: Tento článek popisuje FSLogix profilu kontejnery v rámci souborů virtuální plochy Windows a Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: b3032aa796b3c79572bbf8b2beb85efc252ff73b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66497537"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Kontejnery profilů FSLogix a soubory Azure

Služba Windows virtuální plochy, ve verzi Preview se doporučuje FSLogix profilu kontejnery jako řešení profilu uživatele. FSLogix slouží k roaming profilů ve vzdálené výpočetních prostředích, jako je například virtuální plochy Windows. Ukládá kompletní profil v jedním kontejnerem. Při přihlášení je tento kontejner dynamicky připojen k výpočetním prostředí pomocí nativně podporované virtuální pevný Disk (VHD) a technologie Hyper-V virtuálního pevného disku (VHDX). Profil uživatele je hned dostupný a zobrazí se v systému úplně stejně jako nativní profil.

V tomto článku popisujeme kontejnery FSLogix profil použít se soubory Azure. Informace jsou v rámci virtuální plochy Windows, která byla [zveřejněných na 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Profily uživatelů

Profil uživatele obsahuje údaje o jednotlivých uživatelů, včetně informací o konfiguraci, jako je nastavení pracovní plochy, trvalé síťové připojení a nastavení aplikace. Ve výchozím nastavení vytvoří místní profil uživatele, který je úzce integrovaná s operačním systémem Windows.

Na vzdálený profil uživatele obsahuje oddíl mezi operačního systému a dat uživatele. To umožňuje operačního systému, nahrazení nebo změnit, aniž by to ovlivnilo uživatelská data. Hostitel relace vzdálené plochy (RDSH) a infrastruktur virtuální plochy (VDI) může být nahrazen operačního systému z následujících důvodů:

- Upgrade operačního systému
- Nahrazení z existujícího virtuálního počítače (virtuálního počítače)
- Uživatel se zapojil ve fondu (dočasné) prostředí RDSH nebo infrastruktury virtuálních klientských počítačů

Produkty Microsoftu pracují s více technologiemi pro profily vzdáleného uživatele, včetně těchto technologií:
- Cestovní profily uživatelů (cestovními profily uživatelů)
- Disky profilu uživatele (UPD)
- Stav podnikový roaming (ESR)

UPD a cestovních profilů uživatelů jsou nejčastěji používané technologie pro uživatelské profily v prostředí hostitele relace vzdálené plochy (RDSH) a virtuální pevný Disk (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Problémy s předchozím technologiemi profilu uživatele

Existující a starší verze řešení Microsoftu pro profily uživatelů byly dodány s různé výzvy. Žádné předchozí řešení zpracovává všechny požadavky profilu uživatele, které jsou součástí prostředí RDSH nebo infrastruktury virtuálních klientských počítačů. Například UPD nemůže zpracovat velké soubory OST a cestovních profilů uživatelů není zachována moderní nastavení.

#### <a name="functionality"></a>Funkce

Následující tabulka uvádí výhody a omezení předchozí technologií profilu uživatele.

| Technologie | Moderní nastavení | Nastavení systému Win32 | Nastavení operačního systému | Uživatelská data | Podporuje se ve SKU serveru | Back-end v úložišti Azure | Back endové úložné prostory | Podpora verzí | Další znak v čase |Poznámky|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Disky profilu uživatele (UPD)** | Ano | Ano | Ano | Ano | Ano | Ne | Ano | Windows 7 + | Ano | |
| **Roaming uživatelského profilu (RUP), režim údržby** | Ne | Ano | Ano | Ano | Ano| Ne | Ano | Windows 7 + | Ne | |
| **Enterprise State Roaming (ESR)** | Ano | Ne | Ano | Ne | Viz poznámky | Ano | Ne | Windows 10 | Ne | Funkce na SKU serveru, ale bez podpory uživatelského rozhraní |
| **Virtualizace uživatelského prostředí (UE-V)** | Ano | Ano | Ano | Ne | Ano | Ne | Ano | Windows 7 + | Ne |  |
| **Soubory Onedrivu v cloudu** | Ne | Ne | Ne | Ano | Viz poznámky | Viz poznámky  | Viz poznámky | Win 10 RS3 | Ne | Netestováno na SKU serveru. Back-end v úložišti Azure závisí na synchronizačního klienta. Back endové úložiště v místním prostředí musí synchronizačního klienta. |

#### <a name="performance"></a>Výkon

Vyžaduje UPD [úložiště prostorů s přímým přístupem (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) zohledňování požadavků výkonu. UPD používá protokol zprávy bloku SMB (Server). Profil, který se zkopíruje do virtuálních počítačů, ve kterém uživatel přihlášen. UPD s S2D se tým vzdálené plochy se doporučuje pro virtuální plochy Windows ve verzi preview služby řešení.  

#### <a name="cost"></a>Náklady

Zatímco clustery S2D dosáhnout potřebné výkon, náklady jsou nákladné podnikových zákazníků, ale zvláště nákladné pro malé a střední firmy (SMB). Pro tohle řešení podniky platí za disky premium storage, spolu s náklady na virtuální počítače, které používají disky pro sdílené složky.

#### <a name="administrative-overhead"></a>Režijní náklady na správu

Clustery S2D vyžadují operační systém, který se opravit, aktualizují a zachována ve stavu zabezpečení. Tyto procesy a složité nastavování zotavení po havárii S2D je S2D vhodná pouze pro podniky s oddělením IT.

## <a name="fslogix-profile-containers"></a>Kontejnery FSLogix profilu

19\. listopadu 2018 [společnosti Microsoft získali FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix řeší řadu výzev kontejneru profilu. Klíče mezi nimi jsou:

- **Výkon:** [FSLogix profilu kontejnery](https://fslogix.com/products/profile-containers) vysoce a vyřešit problémy s výkonem, které jste v minulosti blokované uložili do mezipaměti v režimu serveru exchange.
- **OneDrive:** Bez FSLogix profilu kontejnery OneDrive pro firmy nepodporuje dočasné prostředí RDSH nebo infrastruktury virtuálních klientských počítačů. [OneDrive pro firmy a FSLogix osvědčené postupy](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) popisuje způsob, jakým interagují. Další informace najdete v tématu [použití synchronizačního klienta na virtuální plochy](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Další složky:** FSLogix umožňuje rozšířit uživatelské profily mají být zahrnout další složky.

Od pořízení začínal Microsoft nahradíte existující řešení profilu uživatele, jako jsou UPD, kontejnery FSLogix profilu.

## <a name="azure-files-integration-with-azure-active-directory"></a>Integrace soubory Azure pomocí Azure Active Directory

FSLogix profilu kontejnery výkon i funkce využít výhod cloudu. 24\. září 2018, Microsoft Azure Files oznámili verze public preview služby [soubory Azure podporuje ověřování Azure Active Directory](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). Adresování administrativní režii i náklady, soubory Azure pomocí ověřování Azure Active Directory je řešení premium pro uživatelské profily v této nové službě virtuální plochy Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Osvědčené postupy pro virtuální plochy Windows

Virtuální Desktop Windows nabízí plnou kontrolu nad velikost, typ a počet virtuálních počítačů, které jsou používány zákazníky. Další informace najdete v tématu [co je Windows Virtual Desktop Preview?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Aby vaše virtuální plochy Windows prostředí řídí doporučenými postupy:

- Účet služby Azure storage soubory musí být ve stejné oblasti jako hostitel relace virtuálních počítačů.
- Oprávnění u souborů Azure by měl odpovídat oprávnění popsaná v [požadavky - profilu kontejnery](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Každý fond hostitele musí být sestaveny stejného typu a velikosti virtuálních počítačů na základě stejný hlavní image.
- Každý fond hostitele virtuálního počítače musí být ve stejné skupině prostředků pro podporu správy, škálování a aktualizaci.
- Pro optimální výkon řešení úložiště a FSLogix kontejneru profil by měl být ve stejné data center umístění.
- Účet úložiště, který obsahuje hlavní bitová kopie musí být ve stejné oblasti a předplatné, ve kterém se zřizuje virtuální počítače.

## <a name="next-steps"></a>Další postup

Použijte následující pokyny k nastavení prostředí virtuálního klienta Windows.

- Začněte sestavovat si vaše řešení virtualizace plochy, najdete v článku [vytvořit tenanta v virtuální plochy Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- Vytvoření fondu hostiteli svého tenanta virtuální plochy Windows najdete v tématu [vytvoření fondu hostitele pomocí webu Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- K nastavení plně spravované sdílené složky v cloudu, přečtěte si téma [nastavit sdílenou složku služby soubory Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- Nakonfigurování FSLogix profilu kontejnerů najdete v tématu [nastavení sdílené složky profilu uživatele pro hostitele fond](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Přiřazení uživatelů k fondu hostitele, naleznete v tématu [Správa skupiny aplikací pro Windows virtuální plochy](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- Pro přístup k prostředkům Windows virtuální plochy z webového prohlížeče, naleznete v tématu [připojit k virtuálnímu klientovi Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-web).
