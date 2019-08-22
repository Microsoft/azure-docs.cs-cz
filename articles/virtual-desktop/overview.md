---
title: Co je verze Preview pro virtuální počítače s Windows?  – Azure
description: Přehled služby Windows Virtual Desktop Preview
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 08/07/2019
ms.author: helohr
ms.openlocfilehash: 8827aa660d348ef9e42c617067c2837b8697ba6e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876783"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Co je verze Preview pro virtuální počítače s Windows? 

Ve verzi Public Preview je nyní k dispozici služba Windows Virtual Desktop Preview je služba virtualizace plochy a aplikací, která běží v cloudu.

Tady je seznam toho, co můžete udělat při spuštění virtuální plochy Windows v Azure:

* Nastavení nasazení s více relacemi s Windows 10, které poskytuje plnou sadu Windows 10 s škálovatelností
* Virtualizujte Office 365 ProPlus a optimalizujte pro spouštění ve virtuálních scénářích s více uživateli.
* Poskytněte virtuálním klientům Windows 7 bezplatné rozšířené aktualizace zabezpečení.
* Přenesení stávajících klientských počítačů a aplikací pro vzdálenou plochu (RDS) a Windows serveru do libovolného počítače
* Virtualizujte desktopy i aplikace
* Správa stolních počítačů a aplikací pro Windows 10, Windows Server a Windows 7 pomocí jednotného prostředí pro správu

## <a name="introductory-video"></a>Úvodní video

Seznamte se s virtuálními počítači s Windows, proč je jedinečný a co je nového v tomto videu:

<br></br><iframe src="https://www.youtube.com/embed/NQFtI3JLtaU" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

Další informace o virtuálním počítači s Windows najdete v [našem seznamu](https://www.youtube.com/watch?v=NQFtI3JLtaU&list=PLXtHYVsvn_b8KAKw44YUpghpD6lg-EHev)testů.

## <a name="key-capabilities"></a>Klíčové funkce

S virtuálním počítačem s Windows můžete nastavit škálovatelné a flexibilní prostředí:

* Vytvořte v předplatném Azure úplné prostředí virtualizace plochy, aniž byste museli spouštět žádné další servery brány.
* Publikujte tolik fondů hostitelů, kolik potřebujete pro nejrůznější úlohy.
* Využijte vlastní image pro produkční úlohy nebo testy z Galerie Azure.
* Snižte náklady pomocí fondu a prostředků s více relacemi. Díky nové možnosti více relací pro Windows 10 Enterprise výhradně pro role Virtual Desktop a Hostitel relace vzdálené plochy (autovzdálené plochy) na Windows serveru můžete významně snížit počet virtuálních počítačů a zatížení operačního systému (OS), ale pořád poskytování stejných prostředků vašim uživatelům.
* Poskytněte individuální vlastnictví prostřednictvím osobních (trvalých) ploch.

Můžete nasazovat a spravovat virtuální klienty:

* K nakonfigurování fondů hostitelů, vytváření skupin aplikací, přiřazování uživatelů a publikování prostředků použijte rozhraní PowerShell a rozhraní REST pro virtuální počítače s Windows.
* Můžete publikovat celou plochu nebo jednotlivé vzdálené aplikace z jednoho fondu hostitelů, vytvořit jednotlivé skupiny aplikací pro různé sady uživatelů nebo dokonce přiřadit uživatele k několika skupinám aplikací, abyste snížili počet imagí.
* Při správě prostředí použijte integrovaný delegovaný přístup k přiřazování rolí a shromažďování diagnostiky pro pochopení různých konfiguračních a uživatelských chyb.
* Použijte novou diagnostickou službu k řešení chyb.
* Jenom spravovat image a virtuální počítače, ne infrastrukturu. Nemusíte osobně spravovat role vzdálené plochy, jako je třeba Vzdálená plocha, a to jenom virtuální počítače ve vašem předplatném Azure.

Můžete také přiřadit uživatele k virtuálním plochám a propojit je:

* Po přiřazení můžou uživatelé spustit libovolného klienta virtuální plochy Windows a připojit uživatele k publikovaným plochám a aplikacím Windows. Připojte se z libovolného zařízení prostřednictvím nativní aplikace na zařízení nebo webového klienta pro HTML5 virtuálních počítačů s Windows.
* Bezpečně navažte uživatele prostřednictvím zpětného připojení ke službě, takže nikdy nemusíte mít otevřené žádné příchozí porty.

## <a name="requirements"></a>Požadavky

K dispozici je několik věcí, které potřebujete k nastavení virtuální plochy Windows, a k úspěšnému připojení uživatelů ke svým plochám a aplikacím v systému Windows.

Plánujeme přidat podporu pro následující operačních systémech, takže se ujistěte, že máte [příslušné licence](https://azure.microsoft.com/pricing/details/virtual-desktop/) pro uživatele na základě plochy a aplikací, které plánujete nasadit:

|OS|Požadovaná licence|
|---|---|
|Windows 10 Enterprise s více relacemi nebo Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, a3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, a3, A5|
|Windows Server 2012 R2, 2016, 2019|Licence CAL pro klientský přístup (CAL) se Software Assurance|

K podpoře virtuálního klienta Windows potřebuje vaše infrastruktura tyto věci:

* [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Služba Windows Server Active Directory je synchronizovaná s Azure Active Directory. Dá se povolit prostřednictvím:
  * Azure AD Connect
  * Azure AD Domain Services
* Předplatné Azure, které obsahuje virtuální síť, která buď obsahuje, nebo je připojená k Windows serveru Active Directory.
  
Virtuální počítače Azure, které vytvoříte pro virtuální počítače s Windows, musí být:

* [Standardní](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) [Služba AD](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)připojená k doméně nebo k ní připojená. Virtuální počítače nemůžou být připojené k Azure AD.
* Spouští se jedna z následujících [podporovaných bitových kopií operačního systému](#supported-virtual-machine-os-images).

>[!NOTE]
>Pokud potřebujete předplatné Azure, můžete si [zaregistrovat měsíční bezplatnou zkušební verzi](https://azure.microsoft.com/free/). Pokud používáte bezplatnou zkušební verzi Azure, měli byste použít službu Azure AD Domain Services k udržování synchronizace služby Windows Server Active Directory s Azure Active Directory.

Windows Virtual Desktop obsahuje stolní počítače a aplikace Windows, které dodáváte uživatelům a řešení pro správu, které je hostované jako služba v Azure od Microsoftu. Během veřejné verze Preview se můžou desktopy a aplikace nasadit na virtuální počítače v libovolné oblasti Azure a řešení pro správu a data pro tyto virtuální počítače se budou nacházet v USA (USA – východ 2 oblasti). To může vést k přenosu dat do USA při testování služby během veřejné verze Preview. Začneme škálovat řešení pro správu a lokalizaci dat do všech oblastí Azure počínaje všeobecnou dostupností.

Pro zajištění optimálního výkonu zajistěte, aby vaše síť splňovala následující požadavky:

* Latence operace Round-Trip (RTT) ze sítě klienta do oblasti Azure, ve které byly nasazené fondy hostitelů, musí být nižší než 150 ms.
* Síťový provoz se může směrovat mimo hranice země/oblasti, když se virtuální počítače, které hostují desktopy a aplikace, připojí ke službě správy.
* Pro optimalizaci výkonu sítě doporučujeme, aby se virtuální počítače hostitele relace společně umístěného ve stejné oblasti Azure jako služba pro správu.

## <a name="supported-remote-desktop-clients"></a>Podporovaní klienti vzdálené plochy

Virtuální plocha Windows podporuje následující klienti vzdálené plochy:

* [Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-windows-7-and-10)
* [HTML5](https://docs.microsoft.com/azure/virtual-desktop/connect-web)

## <a name="supported-virtual-machine-os-images"></a>Podporované image operačních systémů virtuálních počítačů

Virtuální počítač s Windows podporuje následující image operačních systémů:

* Více relací Windows 10 Enterprise
* Windows Server 2016

## <a name="next-steps"></a>Další postup

Chcete-li začít, budete muset vytvořit tenanta. Pokud se chcete dozvědět víc o tom, jak vytvořit tenanta, přejděte k kurzu Vytvoření tenanta.

> [!div class="nextstepaction"]
> [Vytvoření tenanta ve verzi Preview pro virtuální počítač s Windows](tenant-setup-azure-active-directory.md)
