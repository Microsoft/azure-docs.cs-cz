---
title: Windows Virtual Desktop FSLogix kontejnery profil soubory - Azure
description: Tento článek popisuje kontejnery profilů FSLogix v rámci Windows Virtual Desktop a Azure soubory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127880"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Kontejnery profilů FSLogix a soubory Azure

Služba Windows Virtual Desktop doporučuje kontejnery profilů FSLogix jako řešení profilu uživatele. FSLogix je určen k přechytování profilů ve vzdálených výpočetních prostředích, jako je například virtuální plocha systému Windows. Ukládá úplný uživatelský profil v jednom kontejneru. Při přihlášení je tento kontejner dynamicky připojen k výpočetnímu prostředí pomocí nativně podporovaného virtuálního pevného disku (VHD) a virtuálního pevného disku Hyper-V (VHDX). Profil uživatele je okamžitě k dispozici a zobrazí se v systému přesně jako nativní profil uživatele. Tento článek popisuje, jak kontejnery profilů FSLogix používané s Azure Files fungují ve Windows Virtual Desktop.

>[!NOTE]
>Pokud hledáte srovnávací materiál o různých možnostech úložiště kontejneru profilů FSLogix v Azure, [přečtěte si informace o možnostech úložiště pro kontejnery profilů FSLogix](store-fslogix-profile.md).

## <a name="user-profiles"></a>Profily uživatelů

Profil uživatele obsahuje datové prvky o jednotlivci, včetně informací o konfiguraci, jako je nastavení plochy, trvalá síťová připojení a nastavení aplikací. Ve výchozím nastavení systém Windows vytvoří místní uživatelský profil, který je úzce integrován s operačním systémem.

Vzdálený profil uživatele poskytuje oddíl mezi uživatelská data a operační systém. Umožňuje výměnu nebo změnu operačního systému bez ovlivnění uživatelských dat. V hostitelích relací vzdálené plochy (RDSH) a infrastrukturách virtuálních ploch (VDI) může být operační systém nahrazen z následujících důvodů:

- Upgrade operačního systému
- Nahrazení existujícího virtuálního počítače (VM)
- Uživatel, který je součástí sdruženého (netrvalého) prostředí RDSH nebo VDI

Produkty společnosti Microsoft pracují s několika technologiemi pro vzdálené uživatelské profily, včetně těchto technologií:
- Cestovní uživatelské profily (RUP)
- Disky s profilem uživatele (UPD)
- Roaming ve stavu organizace (ESR)

UPD a RUP jsou nejpoužívanější technologie pro profily uživatelů v prostředích RDSH (RSH) a Virtuální pevný disk (Virtual Hard Disk).

### <a name="challenges-with-previous-user-profile-technologies"></a>Výzvy s předchozími technologiemi uživatelských profilů

Stávající a starší řešení společnosti Microsoft pro uživatelské profily byly řešeny s různými problémy. Žádné předchozí řešení zpracovány všechny potřeby profilu uživatele, které jsou dodávány s rdsh nebo vDi prostředí. Například UPD nemůže zpracovávat velké soubory OST a RUP neuchovává moderní nastavení.

#### <a name="functionality"></a>Funkce

V následující tabulce jsou uvedeny výhody a omezení předchozích technologií profilu uživatele.

| Technologie | Moderní nastavení | Nastavení win32 | Nastavení operačního ses | Uživatelská data | Podporováno na skladové jednotce serveru | Back-endové úložiště v Azure | Back-end úložiště v místním prostředí | Podpora verzí | Následný čas přihlášení |Poznámky|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Disky profilu uživatele (UPD)** | Ano | Ano | Ano | Ano | Ano | Ne | Ano | Vyhrajte 7+ | Ano | |
| **Cestovní uživatelský profil (RUP), režim údržby** | Ne | Ano | Ano | Ano | Ano| Ne | Ano | Vyhrajte 7+ | Ne | |
| **Roaming podnikového státu (ESR)** | Ano | Ne | Ano | Ne | Zobrazit poznámky | Ano | Ne | Vyhrajte 10 | Ne | Funkce na skladové jednotce serveru, ale bez podpůrného uživatelského rozhraní |
| **Virtualizace uživatelského prostředí (UE-V)** | Ano | Ano | Ano | Ne | Ano | Ne | Ano | Vyhrajte 7+ | Ne |  |
| **Cloudové soubory OneDrivu** | Ne | Ne | Ne | Ano | Zobrazit poznámky | Zobrazit poznámky  | Viz Poznámky | Vyhrajte 10 RS3 | Ne | Není testováno na skladové jednotce serveru. Back-endové úložiště v Azure závisí na synchronizačním klientovi. Back-end úložiště on-prem potřebuje synchronizačního klienta. |

#### <a name="performance"></a>Výkon

UPD vyžaduje [storage spaces direct (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) k řešení požadavků na výkon. UPD používá protokol SMB (Server Message Block). Zkopíruje profil do virtuálního virtuálního uživatele, ve kterém je uživatel protokolován. UPD s S2D je řešení, které doporučujeme pro Windows Virtual Desktop.  

#### <a name="cost"></a>Náklady

Zatímco clustery S2D dosahují potřebného výkonu, náklady jsou pro podnikové zákazníky nákladné, ale obzvláště nákladné pro zákazníky malých a středních podniků (SMB). Pro toto řešení firmy platí za disky úložiště, spolu s náklady na virtuální počítače, které používají disky pro sdílenou složku.

#### <a name="administrative-overhead"></a>Režijní náklady na správu

Clustery S2D vyžadují operační systém, který je opraven, aktualizován a udržován v zabezpečeném stavu. Díky těmto procesům a složitosti nastavení zotavení po havárii S2D je S2D proveditelný pouze pro podniky se specializovaným pracovníkem IT.

## <a name="fslogix-profile-containers"></a>Kontejnery profilů FSLogix

19. listopadu 2018 [společnost Microsoft získala společnost FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). FSLogix řeší mnoho problémů kontejneru profilu. Klíčové z nich jsou:

- **Výkon:** [Kontejnery profilu FSLogix](/fslogix/configure-profile-container-tutorial/) jsou vysoce výkonné a řeší problémy s výkonem, které mají historicky blokovaný režim výměny v mezipaměti.
- **OneDrive:** Bez kontejnerů profilů FSLogix není OneDrive pro firmy podporován v netrvalých prostředích RDSH nebo VDI. [Doporučené postupy OneDrivu pro firmy a FSLogix](/fslogix/overview/) popisují, jak interagují. Další informace naleznete [v tématu Použití klienta synchronizace na virtuálních počítačích](/deployoffice/rds-onedrive-business-vdi/).
- **Další složky:** FSLogix poskytuje možnost rozšířit uživatelské profily tak, aby zahrnovaly další složky.

Od získání společnost Microsoft začala nahrazovat stávající řešení profilů uživatelů, jako je UPD, kontejnery profilů FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Integrace souborů Azure se službou Azure Active Directory Domain Service

Výkon a funkce kontejnerů profilů FSLogix využívají cloud. 7. srpna 2019 oznámily soubory Microsoft Azure obecnou dostupnost [ověřování souborů Azure pomocí služby Azure Active Directory Domain Service (AD DS).](../storage/files/storage-files-active-directory-overview.md) Tím, že řeší náklady i administrativní režie, Soubory Azure s Azure AD DS Ověřování je prémiové řešení pro uživatelské profily ve službě Windows Virtual Desktop.

## <a name="best-practices-for-windows-virtual-desktop"></a>Doporučené postupy pro virtuální plochu Windows

Windows Virtual Desktop nabízí plnou kontrolu nad velikostí, typem a počtem virtuálních počítačů, které používají zákazníci. Další informace naleznete v tématu [What is Windows Virtual Desktop?](overview.md).

Chcete-li zajistit, aby vaše prostředí Virtuální plocha systému Windows dodržovalo osvědčené postupy:

- Účet úložiště Souborů Azure musí být ve stejné oblasti jako virtuální počítače hostitele relace.
- Oprávnění K souborům Azure by měla odpovídat oprávněním popsaným v [části Požadavky – kontejnery profilů](/fslogix/fslogix-storage-config-ht).
- Každý fond hostitelů musí být sestaven stejného typu a velikosti virtuálního počítače na základě stejné hlavní image.
- Každý virtuální hostitelský virtuální ms fondu musí být ve stejné skupině prostředků pro správu, škálování a aktualizaci.
- Pro optimální výkon by řešení úložiště a kontejner profilu FSLogix měly být ve stejném umístění datového centra.
- Účet úložiště obsahující hlavní bitovou kopii musí být ve stejné oblasti a předplatné, kde se zřizovány virtuální chod.

## <a name="next-steps"></a>Další kroky

Pomocí následujících pokynů nastavte prostředí Virtuální plochy systému Windows.

- Informace o tom, že můžete začít vytvářet řešení virtualizace plochy, najdete [v tématu Vytvoření klienta ve Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
- Pokud chcete vytvořit fond hostitelů v tenantovi virtuální plochy Windows, přečtěte si informace [o vytvoření fondu hostitelů na Webu Azure Marketplace](create-host-pools-azure-marketplace.md).
- Pokud chcete nastavit plně spravované sdílené složky v cloudu, přečtěte si část [Nastavení sdílené složky Azure Files](/azure/storage/files/storage-files-active-directory-enable/).
- Pokud chcete nakonfigurovat kontejnery profilů FSLogix, přečtěte si část [Vytvoření kontejneru profilu pro fond hostitelů pomocí sdílené složky](create-host-pools-user-profile.md).
- Informace o přiřazení uživatelů do fondu hostitelů naleznete v [tématu Správa skupin aplikací pro windows virtual desktop](manage-app-groups.md).
- Informace o přístupu k prostředkům virtuální plochy systému Windows z webového prohlížeče naleznete v [tématu Připojení k virtuální ploše systému Windows](connect-web.md).
