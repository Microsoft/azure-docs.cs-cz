---
title: Co je Windows Virtual Desktop Preview?  – Azure
description: Přehled Windows virtuální plochy, ve verzi Preview.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: overview
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0d606a489a069c4265088d8e07301693dc2f1c83
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694275"
---
# <a name="what-is-windows-virtual-desktop-preview"></a>Co je Windows Virtual Desktop Preview? 

Teď ve verzi public preview, Windows virtuální plochy, ve verzi Preview je služba virtualizace plochy a aplikace, která běží v cloudu.

Zde je, co můžete dělat, když spustíte virtuální plochy Windows na Azure:

* Nastavení nasazení více relací Windows 10, který poskytuje úplný Windows 10 škálovatelnost
* Virtualizovat Office 365 ProPlus a optimalizovat jeho spuštění ve scénářích virtuální více uživatelů
* Zadejte virtuální plochy Windows 7 s bezplatné rozšířené aktualizace zabezpečení
* Vzneste svoje existující služby Vzdálená plocha (RDS) a stolní počítače Windows serveru a aplikace na libovolném počítači
* Virtualizace plochy a aplikace
* Spravovat stolní počítače Windows 10, Windows Server a Windows 7 a aplikace s jedinečné prostředí pro správu

## <a name="key-capabilities"></a>Klíčové funkce

Virtuální plochy Windows můžete nastavit, škálovatelnou, flexibilní prostředí:

* Vytvoření plně virtualizované prostředí ve svém předplatném Azure bez nutnosti spuštění veškeré servery další brány.
* Publikujte, protože mnoho hostují fondy podle potřeby tak, aby vyhovovaly různých úloh.
* Používání vlastní image pro produkční úlohy nebo testů z Galerie Azure.
* Snížení nákladů s prostředky ve fondu, více relací. S Windows 10 Enterprise více relací možnost výhradně pro virtuální plochy Windows a hostitele relace vzdálené plochy (RDSH) role v systému Windows Server můžete výrazně snížit počet virtuálních počítačů a operační systém (OS) režie zatímco jste pořád poskytuje stejné prostředky pro vaše uživatele.
* Zadejte jednotlivé vlastnictví prostřednictvím osobní plochy (trvalý).

Můžete nasadit a spravovat virtuální klienty:

* Nakonfigurujte fondy hostitele, vytvoření skupiny aplikací, přiřazení uživatelů a publikování prostředků pomocí rozhraní Windows virtuální plochy Powershellu a REST.
* Publikovat zcela klasické pracovní plochy nebo jednotlivé vzdálené aplikace z jednoho hostitele fondu, vytvoření skupin pro jednotlivé aplikace pro různé skupiny uživatelů nebo dokonce přiřadit uživatele do několika skupin aplikací ke snížení počtu imagí.
* Při správě prostředí pomocí předdefinovaných Delegovaný přístup přiřazení rolí a shromažďování diagnostiky pochopit různé chyby konfigurace nebo uživatele.
* Pomocí nové služby Diagnostika potíží s chybami.
* Spravovat jenom na obrázku a virtuální počítače, ne na infrastrukturu. Nemusíte sami spravovat role vzdálené plochy, stejně jako u služby Vzdálená plocha, jenom virtuální počítače ve vašem předplatném Azure.

Můžete také přiřadit a připojení uživatelů k virtuálním klientům:

* Po přiřazení, uživatelé mohou spouštět libovolného klienta virtuální plochy Windows k připojení uživatelů ke své publikované stolní počítače Windows a aplikací. Připojení z libovolného zařízení pomocí obou nativní aplikaci na vašem zařízení nebo webový klient HTML5 virtuální plochy Windows.
* Bezpečně navázat, že otevřete uživatele prostřednictvím reverzního připojení ke službě, abyste si už nikdy nemusíte ponechte žádné příchozí porty.

## <a name="requirements"></a>Požadavky

Existuje několik věcí, které je potřeba nastavit virtuální plochy Windows a úspěšně připojit vaše uživatele pro své stolní počítače Windows a aplikací.

Virtuální Desktop Windows aktuálně podporuje pouze následující dva operační systémy:

* Windows 10 Enterprise více relací
* Windows Server 2016

Plánujeme přidat podporu pro následující operační systémy, takže se ujistěte, že máte [odpovídající licence](https://azure.microsoft.com/pricing/details/virtual-desktop/) pro vaše uživatele, založené na stolní počítače a aplikace, máte v úmyslu nasadit:

|Operační systém|Požadovaná licence|
|---|---|
|Více relací Windows 10 Enterprise nebo Windows 10 Enterprise|Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows 7 Enterprise |Microsoft 365 E3, E5, A3, A5, F1, Business<br>Windows E3, E5, A3, A5|
|Windows Server 2012 R2, 2016, 2019|Licence VP licence pro klientský přístup (CAL) s programem Software Assurance|

Vaše infrastruktura potřebuje následující akce pro podporu virtuální plochy Windows:

* [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)
* Windows Server Active Directory synchronizované se službou Azure Active Directory. To se dá nastavit pomocí:
  * Azure AD Connect
  * Azure AD Domain Services
* Předplatné Azure, který obsahuje virtuální síť, která obsahuje nebo je připojený ke službě Windows Server Active Directory
  
Virtuální počítače Azure, které vytvoříte pro virtuální plochy Windows musí být:

* [Standard připojených k doméně](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-comparison) nebo [AD připojená k hybridní službě](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan). Virtuální počítače nemůže být připojená k AD Azure.
* S některým z následujících podporovaných imagí operačního systému:
  * Windows 10 Enterprise více relací
  * Windows Server 2016

>[!NOTE]
>Pokud budete potřebovat předplatné Azure, můžete si [zaregistrovat k měsíční bezplatné zkušební verzi](https://azure.microsoft.com/free/). Pokud používáte bezplatnou zkušební verzi Azure, měli byste používat služby Azure AD Domain pro synchronizaci vašeho systému Windows Server Active Directory s Azure Active Directory.

Virtuální plochy Windows se skládá z plochy Windows a aplikací, které poskytujete uživatelům a do řešení pro správu, který je hostovaný jako služba v Azure od Microsoftu. Ve verzi public preview stolní počítače a aplikace se dají nasadit na virtuální počítače (VM) v libovolné oblasti Azure a řešení pro správu a data pro tyto virtuální počítače se nachází ve Spojených státech amerických (oblasti USA – východ 2). Výsledkem může přenos dat do USA při testování služby ve verzi public preview. Začneme pro horizontální navýšení kapacity správu řešení a data lokalizace do všech oblastí Azure od ve fázi obecné dostupnosti.

Pro optimální výkon Ujistěte se, že vaše síť splňuje následující požadavky:

* Latence zpátečního převodu (požadavku) ze sítě klienta do oblasti Azure, kde byly nasazeny fondy hostitele by měl být kratší než 150 ms.
* Síťový provoz může tok mimo hranice země, připojíte ke službě správy virtuálních počítačů, které hostování desktopů a aplikací.
* Pokud chcete optimalizovat výkon sítě, doporučujeme, že jsou seřazena relace hostování virtuálních počítačů ve stejné oblasti Azure jako službu pro správu.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Přejděte [technické komunitě virtuální plochy Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) fattica virtuální plochy Windows service s produktovým týmem a aktivní komunitě členy. Můžeme aktuálně nejsou trvá případy podpory virtuální plochy Windows je ve verzi preview.

## <a name="next-steps"></a>Další postup

Abyste mohli začít, musíte tenanta vytvořit. Další informace o tom, jak vytvořit tenanta, pokračujte ke kurzu vytváření tenanta.

> [!div class="nextstepaction"]
> [Vytvořit tenanta v náhledu virtuální plochy Windows](tenant-setup-azure-active-directory.md)
