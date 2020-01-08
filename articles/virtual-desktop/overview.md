---
title: Co je Windows Virtual Desktop? – Azure
description: Přehled virtuálního klienta Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 12/17/2019
ms.author: helohr
ms.openlocfilehash: dd5167af5f45ebae0529e16f224065627085e9b0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75348809"
---
# <a name="what-is-windows-virtual-desktop"></a>Co je Windows Virtual Desktop? 

Virtuální plocha Windows je služba virtualizace plochy a aplikací, která běží v cloudu.

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
* Služba Windows Server Active Directory je synchronizovaná s Azure Active Directory. Můžete ji nakonfigurovat jedním z následujících způsobů:
  * Azure AD Connect (pro hybridní organizace)
  * Azure AD Domain Services (pro hybridní nebo cloudové organizace)
* Předplatné Azure, které obsahuje virtuální síť, která buď obsahuje nebo je připojená k Windows serveru Active Directory
  
Virtuální počítače Azure, které vytvoříte pro virtuální počítače s Windows, musí být:

* [Standardní](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) služba AD připojená k doméně nebo k ní [připojená](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Virtuální počítače nemůžou být připojené k Azure AD.
* Spouští se jedna z následujících [podporovaných bitových kopií operačního systému](#supported-virtual-machine-os-images).

>[!NOTE]
>Pokud potřebujete předplatné Azure, můžete si [zaregistrovat měsíční bezplatnou zkušební verzi](https://azure.microsoft.com/free/). Pokud používáte bezplatnou zkušební verzi Azure, měli byste použít službu Azure AD Domain Services k udržování synchronizace služby Windows Server Active Directory s Azure Active Directory.

Virtuální počítače Azure, které vytvoříte pro virtuální počítače s Windows, musí mít odchozí přístup TCP 443 k následujícím adresám URL:

* *. wvd.microsoft.com
* *.blob.core.windows.net
* *.core.windows.net
* *.servicebus.windows.net
* prod.warmpath.msftcloudes.com
* catalogartifact.azureedge.net

>[!NOTE]
>Otevírání těchto adres URL je nezbytné pro spolehlivé nasazení virtuálních počítačů s Windows. Blokování přístupu k těmto adresám URL není podporováno a bude mít vliv na funkčnost služby. Tyto adresy URL odpovídají pouze webům a prostředkům virtuálních ploch systému Windows a neobsahují adresy URL pro jiné služby, jako je Azure AD.

Windows Virtual Desktop obsahuje stolní počítače a aplikace Windows, které dodáváte uživatelům a řešení pro správu, které je hostované jako služba v Azure od Microsoftu. Stolní počítače a aplikace můžou být nasazené na virtuálních počítačích v libovolné oblasti Azure a řešení pro správu a data pro tyto virtuální počítače se budou nacházet v USA. To může vést k přenosu dat do USA.

Pro zajištění optimálního výkonu zajistěte, aby vaše síť splňovala následující požadavky:

* Latence operace Round-Trip (RTT) ze sítě klienta do oblasti Azure, ve které byly nasazené fondy hostitelů, musí být nižší než 150 ms.
* Síťový provoz se může směrovat mimo hranice země/oblasti, když se virtuální počítače, které hostují desktopy a aplikace, připojí ke službě správy.
* Pro optimalizaci výkonu sítě doporučujeme, aby se virtuální počítače hostitele relace společně umístěného ve stejné oblasti Azure jako služba pro správu.

## <a name="supported-remote-desktop-clients"></a>Podporovaní klienti vzdálené plochy

Virtuální plocha Windows podporuje následující klienti vzdálené plochy:

* [Windows](connect-windows-7-and-10.md)
* [Web](connect-web.md)
* [Mac](connect-macos.md)
* [iOS](connect-ios.md)
* [Android (Preview)](connect-android.md)

## <a name="supported-virtual-machine-os-images"></a>Podporované image operačních systémů virtuálních počítačů

Virtuální počítač s Windows podporuje následující image operačních systémů x64:

* Windows 10 Enterprise s více relacemi verze 1809 nebo novější
* Windows 10 Enterprise, verze 1809 nebo novější
* Windows 7 Enterprise
* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2

Virtuální počítač s Windows nepodporuje image operačního systému x86 (32-bit), Windows 10 Enterprise N nebo Windows 10 Enterprise KN.

Dostupné možnosti automatizace a nasazení závisí na tom, který operační systém a jakou verzi si zvolíte, jak je znázorněno v následující tabulce: 

|Operační systém|Galerie imagí Azure|Ruční nasazení virtuálního počítače|Integrace šablon Azure Resource Manager|Zřizování fondů hostitelů na Azure Marketplace|Aktualizace agenta virtuálního počítače s Windows|
|--------------------------------------|:------:|:------:|:------:|:------:|:------:|
|Více relací Windows 10, verze 1903|Ano|Ano|Ano|Ano|Automaticky|
|Více relací Windows 10, verze 1809|Ano|Ano|Ne|Ne|Automaticky|
|Windows 10 Enterprise, verze 1903|Ano|Ano|Ano|Ano|Automaticky|
|Windows 10 Enterprise, verze 1809|Ano|Ano|Ne|Ne|Automaticky|
|Windows 7 Enterprise|Ano|Ano|Ne|Ne|Manual|
|Windows Server 2019|Ano|Ano|Ne|Ne|Automaticky|
|Windows Server 2016|Ano|Ano|Ano|Ano|Automaticky|
|Windows Server 2012 R2|Ano|Ano|Ne|Ne|Automaticky|

## <a name="next-steps"></a>Další kroky

Chcete-li začít, budete muset vytvořit tenanta. Pokud se chcete dozvědět víc o tom, jak vytvořit tenanta, přejděte k kurzu Vytvoření tenanta.

> [!div class="nextstepaction"]
> [Vytvoření klienta v Windows Virtual Desktop](tenant-setup-azure-active-directory.md)
